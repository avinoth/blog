---
layout: post
title: How to Handle Gzipped Response in Nodejs
date: 2014-12-21 13:25:56.000000000 +05:30
---
Often web servers sends out response as HTTP compressed data while making requests either as JSOn or JS. Our browsers are smart enough to uncompress the data by the appropriate zipped method and show our web pages for us, but in an application, well we have to make it smarter.

Whenever I am searching for some solutions and I land on a Blog post, I don’t read the content but skip straight away to the code, so without further ado below is the code to unzip the response if it is gzipped. The code is using Node.JS’s request module and zlib.

This method also uses NodeJS’s stream api.

`zlib = require 'zlib'
request = require 'request'
unGzipResponse = (url) ->
    ungzip = new zlib.createGunzip()
    resp = request url
    try
        resp.pipe(ungzip)
        ungzip.on('data', (data) ->
            buffer = []
            buffer.push(data.toString())
            ).on 'end', ->
                console.log buffer.join("")
    catch e
        console.log "ERROR - #{e}"`

The code is written in my beloved coffeescript and if you want, you may convert it here
