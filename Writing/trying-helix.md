---
title: Trying Helix Editor
publishDate: 2025-05-13
draft: false
archive: false
---

I was scrolling twitter, There was a [tweet](https://x.com/peach2k2/status/1921888383858544872), saying "neovim killer, no lsp config or plugins needed", So I searched for [it](https://helix-editor.com/).

Read its docs, watched a [video](https://youtu.be/HcuDmSb-JBU?si=MyqZrZZZ_wuympRk), installed and tried it. In fact I am writing this blog on Helix itself.

The problem with neovim, is its ovewhelming plugins, which makes things slow. In order to switch I needed to make sure, it is faster and has all the most used functionality from neovim. Reading the doc and wiki I figured, it has everything from nvim. Just one I did not see was [flash](https://github.com/folke/flash.nvim), I felt little disapponting after seaching for a while then, in the video, I found, it has similar functionality called "jump to two character label"(gw).

VIM and NEOVIM is text editor instead of full fledged IDE, but Helix is an IDE. It comes with all the IDE features, it even has multiple cursor support(which I've not yet figured out how to use). Similar to vim it is modal editor, with some fundamental change in keybindings. Talking about speed, helix is considered to be the faster, will see.

I have setup `jk` for Escape. Tried setting up typescript, but was getting the error on tree-sitter. Asked about it on matrix server, after publishing this, checked the server and a memeber had already replied; he was able to help, The problem was I forgot to do the step 2 of [installation process](https://docs.helix-editor.com/install.html#pre-built-binaries) and started using the IDE after step 1.

So for now, I am glad, I found this, I'll be using it, will see if it still makes me glad after a week or a month.
