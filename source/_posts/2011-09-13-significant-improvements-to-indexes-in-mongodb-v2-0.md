---
title: Significant improvements to indexes in MongoDB v2.0
author: sw
layout: post
permalink: /2011/09/significant-improvements-to-indexes-in-mongodb-v2-0/
categories:
  - Uncategorized
tags:
  - mongodb
---
# 

I have upgraded my MongoDB installation to version 2.0 today and converted existing indexes to {v:1} format. According to the [release notes][1], *“Indexes are often 25% smaller and 25% faster (depends on the use case)”*.

 [1]: http://www.mongodb.org/display/DOCS/2.0 Release Notes

    > db.hashes.totalIndexSize()
    5932986112
    > db.hashes.runCommand( "compact" )
    { "ok" : 1 }
    > db.hashes.totalIndexSize()
    3527294016
    

In my case, a more than 10 GB database, the `totalIndexSize` of {v:1} format indexes is reduced by more than 40% to only 59.45% of the size of the old {v:0} format indexes. Well done, MongoDB developers!