<img src='img/banner.jpg'>

Este repositorio 🗂️ contiene una serie de pasos de como generar los insumos necesarios para realizar una visualización en 3D de cualquier parte del mundo usando MapLibre 
con el lenguaje de programación de `Js` junto con una estructura de `HTML` y algo de estilos con `CSS`. 

Para este video usaremos las siguientes herramientas:

- [rio-rgbify](https://github.com/mapbox/rio-rgbify): Módulo de Python que permite convertir datos raster en teselas codificadas en formato RGB.
- [mbutils](https://github.com/mapbox/mbutil): Herramienta basada en Python que permite convertir un MBTiles en teselas organizadas por carpetas o directorios según el niveles de zoom, filas y columnas, siguiendo el esquema Z/X/Y el cual es muy utilizado en la visualización web.
- [QGIS](https://www.qgis.org/): Software de Sistema de Información Geográfica libre y de código abierto que permite manipular, editar, analizar y visualizar datos espaciales.
  - Plugin: [JAXA Earth API Plugin](https://plugins.qgis.org/plugins/qgis-jaxa-earth-plugin-master/): API para QGIS que permite descargar datos de la Agencia Japonesa de Exploración Aeroespacial.
- [MapLibre GL JS](https://maplibre.org/maplibre-gl-js/docs/): Librería de TypeScript que utiliza WebGL para renderizar mapas interactivos a partir de mosaicos vectoriales en el navegador.

## 🔵 Pasos para generar una visualización en 3D con MapLibre

1. Primer paso: Instalación del plugin JAXA Earth API en QGIS para la descarga del modelo digital de superficie (DSM) del área de interés
2. Segundo paso: Generar el MBTiles del DSM descargado en QGIS.

    Para este procedimiento usaremos el siguiente extracto de código 👇

    ```bash
    rio rgbify -b -10000 -i 0.1 --min-z 0 --max-z 12 -j 24 --format png RUTA_DEL_DSM_AQUI.tif output.mbtiles
    ```

3. Tercer paso: Generar directorios o carpetas según el nivel de zoom, filas y columnas provenientes del MBTiles.

    ```bash
    python mb-util output.mbtiles tiles
    ```

4. Cuarto paso: Generar la visualización en 3D con MapLibre.
   Aquí se adjunta un template para la visualización 👇

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <title>3D Terrain</title>
        <meta property="og:description" content="Go beyond hillshade and show elevation in actual 3D." />
        <meta charset='utf-8'>
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <link rel='stylesheet' href='https://unpkg.com/maplibre-gl@5.0.0/dist/maplibre-gl.css' />
        <script src='https://unpkg.com/maplibre-gl@5.0.0/dist/maplibre-gl.js'></script>
        <style>
            body { margin: 0; padding: 0; }
            html, body, #map { height: 100%; }
        </style>
    </head>
    <body>
    <div id="map"></div>
    <script>
        const map = (window.map = new maplibregl.Map({
            container: 'map',
            zoom: 12,
            center: [11.39085, 47.27574],
            pitch: 70,
            hash: true,
            style: {
                version: 8,
                sources: {
                    osm: {
                        type: 'raster',
                        tiles: ['https://a.tile.openstreetmap.org/{z}/{x}/{y}.png'],
                        tileSize: 256,
                        attribution: '&copy; OpenStreetMap Contributors',
                        maxzoom: 19
                    },
                    // Use a different source for terrain and hillshade layers, to improve render quality
                    terrainSource: {
                        type: 'raster-dem',
                        url: '["./tiles/{z}/{x}/{y}.png"]',
                        tileSize: 256
                    },
                    hillshadeSource: {
                        type: 'raster-dem',
                        url: '["./tiles/{z}/{x}/{y}.png"]',
                        tileSize: 256
                    }
                },
                layers: [
                    {
                        id: 'osm',
                        type: 'raster',
                        source: 'osm'
                    },
                    {
                        id: 'hills',
                        type: 'hillshade',
                        source: 'hillshadeSource',
                        layout: {visibility: 'visible'},
                        paint: {'hillshade-shadow-color': '#473B24'}
                    }
                ],
                terrain: {
                    source: 'terrainSource',
                    exaggeration: 1
                },
                sky: {}
            },
            maxZoom: 18,
            maxPitch: 85
        }));

        map.addControl(
            new maplibregl.NavigationControl({
                visualizePitch: true,
                showZoom: true,
                showCompass: true
            })
        );

        map.addControl(
            new maplibregl.TerrainControl({
                source: 'terrainSource',
                exaggeration: 1
            })
        );
    </script>
    </body>
    </html>
    ```


Todo el proceso desarrollado esta explicado a detalle paso por paso en el siguiente video de Youtube 🎥.

[![Watch the video](https://img.youtube.com/vi//0.jpg)](https://youtu.be/aM93Jn1uD-I?si=tnOLDjmW-TkOb3U9)


¡Enteráte más y aprender conmigo! 🔍💡 Suscríbete, activa las notificaciones 🔔 y únete a la comunidad que ama el software libre de código abierto. 🌟🌍 👇
- <img src='https://raw.githubusercontent.com/geografope/recursos/d7be118ef25f46cb6f748d623012bcc9c8e76db6/youtube.svg' width=20 align='center'>https://www.youtube.com/@geografope

- <img src='https://raw.githubusercontent.com/geografope/recursos/d7be118ef25f46cb6f748d623012bcc9c8e76db6/tiktok.svg' width=15 align='center'>https://www.tiktok.com/@geografope

- <img src='https://raw.githubusercontent.com/geografope/recursos/d7be118ef25f46cb6f748d623012bcc9c8e76db6/linkedin.svg' width=15 align='center'>https://www.linkedin.com/in/antonybarja/

## 🔵 Referencias: 
 - *https://maplibre.org/maplibre-gl-js/docs/examples/3d-terrain/*
 - *https://plugins.qgis.org/plugins/qgis-jaxa-earth-plugin-master/*
 - *https://github.com/mapbox/mbutil*
 - *https://github.com/mapbox/rio-rgbify*