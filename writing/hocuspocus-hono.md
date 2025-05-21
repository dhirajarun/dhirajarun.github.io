---
category: writing
title: How to setup hocospocus in Hono
publishDate: 2025-03-21
draft: false
archive: false
---

In [Hocuspocus](https://github.com/ueberdosis/hocuspocus) documentation, it has [examples](https://tiptap.dev/docs/hocuspocus/server/examples) on how to set it up with express and koa, but I couldn't find any example on integrating in with Hono js.

Hono has middleware and [helpers](https://hono.dev/docs/helpers/websocket) to work with WebSocket but I had to modify the [code](https://github.com/honojs/middleware/tree/main/packages/node-ws) a bit to make it work with Hocuspocus.

I removed `upgradeWebSocket` and added `websocket` method.

```ts
// hono-websocket.ts
import type { IncomingMessage } from "node:http";
import type { Server } from "node:http";
import type { Http2SecureServer, Http2Server } from "node:http2";
import type { Duplex } from "node:stream";
import type { Hono } from "hono";
import { createMiddleware } from "hono/factory";
import type { WebSocket } from "ws";
import { WebSocketServer } from "ws";

export interface NodeWebSocketInit {
  // eslint-disable-next-line @typescript-eslint/no-explicit-any
  app: Hono<any, any, any>;
  baseUrl?: string | URL;
}

/**
 * Create WebSockets for Node.js
 * @param init Options
 * @returns NodeWebSocket
 */
export const createNodeWebSocket = (init: NodeWebSocketInit) => {
  const wss = new WebSocketServer({ noServer: true });
  const waiterMap = new Map<
    IncomingMessage,
    { resolve: (ws: WebSocket) => void; response: Response }
  >();

  wss.on("connection", (ws, request) => {
    const waiter = waiterMap.get(request);
    if (waiter) {
      waiter.resolve(ws);
      waiterMap.delete(request);
    }
  });

  const nodeUpgradeWebSocket = (
    request: IncomingMessage,
    response: Response,
  ) => {
    return new Promise<WebSocket>((resolve) => {
      waiterMap.set(request, { resolve, response });
    });
  };

  return {
    injectWebSocket(server: Server | Http2Server | Http2SecureServer) {
      server.on("upgrade", async (request, socket: Duplex, head) => {
        const url = new URL(
          request.url ?? "/",
          init.baseUrl ?? "http://localhost",
        );

        const headers = new Headers();
        for (const key in request.headers) {
          const value = request.headers[key];
          if (!value) {
            continue;
          }
          headers.append(key, Array.isArray(value) ? value[0] : value);
        }

        const response = await init.app.request(
          url,
          { headers: headers },
          { incoming: request, outgoing: undefined },
        );

        const waiter = waiterMap.get(request);
        if (!waiter || waiter.response !== response) {
          socket.end(
            "HTTP/1.1 400 Bad Request\r\n" +
              "Connection: close\r\n" +
              "Content-Length: 0\r\n" +
              "\r\n",
          );
          waiterMap.delete(request);
          return;
        }

        wss.handleUpgrade(request, socket, head, (ws) => {
          wss.emit("connection", ws, request);
        });
      });
    },

    websocket: () =>
      createMiddleware<{
        Variables: {
          ws: ((response: Response) => Promise<WebSocket>) | null;
        };
      }>(async (c, next) => {
        if (c.req.header("upgrade")?.toLowerCase() !== "websocket") {
          // Not websocket
          await next();
          c.set("ws", null);
          return;
        }

        const request = (c.env as any).incoming;

        const ws = async (response: Response) => {
          const ws = await nodeUpgradeWebSocket(request, response);
          return ws;
        };

        c.set("ws", ws);
        return next();
      }),
  };
};
```

Here is how to integrate hocupocus

```ts
import { Server } from "@hocuspocus/server"
import { serve } from '@hono/node-server'
import { Hono } from 'hono'
import { createNodeWebSocket } from './hono-websocket'

const app = new Hono();

// Configure Hocuspocus
const hocuspocusServer = Server.configure({
  // …
});

const { injectWebSocket, websocket } = createNodeWebSocket({
  app,
});

app
  .get("/collaboration")
  .use(websocket())
  .use(async (c) => {
    const ws = c.get("ws");

    if (!ws) {
      throw new HTTPException(400, {
        message: "No websocket found",
      });
    }

    const appId = c.req.param("appId");

    // DO NOT use await here, it will block the response
    const response = new Response();
    ws(response)
      .then((websocket) => {
        if (!appId) {
          throw new HTTPException(400, {
            message: "No app ID provided",
          });
        }

        const request = (c.env as any).incoming;
        hocuspocusServer.handleConnection(websocket, request, {
          appId,
        });
      })
      .catch((error) => {
        console.error("Error handling connection", error);
      });

    return response;
  });

const server = serve(app)
injectWebSocket(server)
```


## Update May 8, 2025

Later I found out, I do not need to write or modify anything, this can be done by Hono [helpers](https://hono.dev/docs/helpers/websocket) @hono/node-ws. I found it in [blocknote example](https://github.com/TypeCellOS/BlockNote-demo-nextjs-hocuspocus/blob/main/hocuspocus-server/src/index.ts#L44-L51).

```ts
import { createNodeWebSocket } from "@hono/node-ws";

app.get(
  "/hocuspocus",
  upgradeWebSocket((c) => ({
    onOpen(_evt, ws) {
      hocuspocusServer.handleConnection(ws.raw, c.req.raw);
    },
  }))
);
```

simple as that.