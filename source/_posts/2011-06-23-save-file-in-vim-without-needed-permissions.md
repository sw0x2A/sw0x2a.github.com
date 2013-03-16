---
title: Save file in vim without needed permissions
author: sw
layout: post
permalink: /2011/06/save-file-in-vim-without-needed-permissions/
categories:
  - Uncategorized
tags:
  - permissions
  - sudo
  - vi
  - vim
---
# 

It is annoying when you realize that all you have done to the file that you are currently editing in vim cannot be saved because you need root permissions. Thanks to the masters of [commandlinefu][1] I know how to do it anyway.

 [1]: http://www.commandlinefu.com/commands/view/1204/save-a-file-you-edited-in-vim-without-the-needed-permissions

    :w !sudo tee %