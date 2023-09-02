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
            justify-content: space-between;
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
        </div>
        <!-- <div class="gallery-item">
            <img src="image2.jpg" alt="Image 2">
            <a href="link2.html" class="hover-button">View</a>
        </div>
        <div class="gallery-item">
            <img src="image3.jpg" alt="Image 3">
            <a href="link3.html" class="hover-button">View</a>
        </div> -->
        <!-- Add more gallery items as needed -->
    </div>

</body>
</html>
