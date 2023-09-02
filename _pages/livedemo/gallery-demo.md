---
title: "Live demo"
permalink: /livedemo/
comment: jfiewofjaiweofjawoeifjawieofj
layout: gallery

tags:
  - [test]
---

<html lang="{{ site.locale | slice: 0,2 | default: "en" }}" class="no-js">
<head>
    <style>
        .gallery {
            display: flex;
            flex-wrap: wrap;
            justify-content: space-around;
        }

        .gallery-item {
            position: relative;
            margin: 10px;
            overflow: hidden;
            width: 300px;
            height: 200px;
        }

        .gallery-item img {
            width: 100%;
            height: 100%;
            object-fit: cover;
        }

        .gallery-item .hover-button {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background-color: rgba(255,255,255, 0.9);
            color: #FF0000;
            padding: 10px 20px;
            border: none;
            cursor: pointer;
            opacity: 0;
            transition: opacity 0.3s ease-in-out;
            text-decoration: none;
        }

        .gallery-item .title {
            position: absolute;
            bottom: 0;
            left: 0;
            width: 100%;
            background-color: rgba(0, 0, 0, 0.5);
            color: #fff;
            padding: 5px 10px;
        }


        .gallery-item:hover .hover-button {
            opacity: 1;
        }
    </style>
    </head>

<body>
   <div class="gallery">
        <div class="gallery-item">
            <img src='/assets/images/demo/jukebox.png' >
            <a href="https://onejae.github.io/demo/jukebox" class="hover-button" target='_blank'>Open</a>
            <div class="title">Jukebox(Three.js + React.js)</div>
        </div>
        <div class="gallery-item">
            <img src='/assets/images/demo/keymetronome.png' >
            <a href="https://yesjamstudio.com/" class="hover-button" target='_blank'>Open</a>
            <div class="title">Metronome(React Native)</div>
        </div>
        <!-- <div class="gallery-item">
            <img src='/assets/images/demo/jukebox.png' >
            <a href="https://onejae.github.io/demo/jukebox" class="hover-button" target='_blank'>Open</a>
            <div class="title">Game Streaming(k8s, C++)</div>
        </div> -->
    </div>
</body>
</html>
