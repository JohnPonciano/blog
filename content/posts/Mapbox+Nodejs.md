+++
title = "Mapbox+Nodejs"
date = "2023-09-06T13:31:36-03:00"
author = ""
authorTwitter = "" #do not include @
cover = ""
tags = ["", ""]
keywords = ["", ""]
description = ""
showFullContent = false
readingTime = false
hideComments = false
color = "blue" #color from the theme settings
+++

# Desenferrujando meu NodeJS



###### Vamos lá, recentemente achei umas vulnerabilidades no serviço de Geo da minha cidade, (Onde já reportei eo o pessoal já fez FIX, obs: Abraço Rafão)

Então nessa brincadeirinha, fiz um dump dos marcos geodesicos de cameras da cidade, e assim criei um projetinho usando o MAPBOX pra organizar isso pra mim. 



Meu server foi feito em nodejs, mas nada muito fora do comum 

server.js

````js
const express = require('express');
const path = require('path');

const app = express();
const port = 3000;

app.use(express.static('public'));

app.get('/radares', (req, res) => {
  const filePath = path.join(__dirname, 'app/sample.geojson');
  res.sendFile(filePath);
});

app.get('/cameras', (req, res) => {
  const filePath = path.join(__dirname, 'app/dados_cameras.geojson');
  res.sendFile(filePath);
});

app.get('/reconhecimento', (req, res) => {
  const filePath = path.join(__dirname, 'app/template_reconhecimento.geojson');
  res.sendFile(filePath);
});

app.listen(port, () => {
  console.log(`Servidor rodando em http://localhost:${port}`);
});
````

Enquanto isso eu fiz um HTML, usando a API do maxbox, pra gerar um mapa legal.

public/index.html

````html
<!DOCTYPE html>
<html>

<head>
  <meta charset="utf-8">
  <title>Mapa com Mapbox</title>
  <script src='https://api.mapbox.com/mapbox-gl-js/v2.4.1/mapbox-gl.js'></script>
  <link href='https://api.mapbox.com/mapbox-gl-js/v2.4.1/mapbox-gl.css' rel='stylesheet' />
  <link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Roboto:wght@300&display=swap" rel="stylesheet">
  <style>
   body {
      margin: 0;
      padding: 0;
    }

    #map {
      position: absolute;
      top: 0;
      bottom: 0;
      width: 70%;
      
    }

    #sidebar {
      font-family: 'Roboto', sans-serif;
      position: absolute;
      top: 0;
      right: 0;
      bottom: 0;
      width: 30%;
      background-color: #3e3e42;
      padding: 20px;
      overflow-y: auto;
      color: whitesmoke;
    }
  </style>
</head>

<body>
  
  <div id='map'></div>
  <div id="sidebar"></div>

  <script>
    mapboxgl.accessToken = 'pk.eyJ1IjoiaGFra3UwMDEiLCJhIjoiY2ttb3Q0Z3FuMGV4cDJ1bGZ5ZTF3ZHB6YSJ9.xDnHMY98d1eRJTukvupWQg';

    // Inicializa o mapa
    var map = new mapboxgl.Map({
      container: 'map',
      style: 'mapbox://styles/mapbox/navigation-night-v1',
      center: [-47.2963245563055, -23.2607676472329], // Coordenadas de centro
      zoom: 9 // Nível de zoom inicial
    });

    function loadGeoJSONAndAddLayers(url, color) {
      fetch(url)
        .then(response => response.json())
        .then(data => {
          map.addSource(url, {
            type: 'geojson',
            data: data
          });

          // Estiliza o GeoJSON
          map.addLayer({
            id: url,
            type: 'circle',
            source: url,
            paint: {
              'circle-color': color,
              'circle-radius': 6,
              'circle-stroke-width': 2,
              'circle-stroke-color': '#FFFFFF'
            }
          });

          // Adiciona um evento de clique para exibir dados no menu lateral
          map.on('click', url, function (e) {
            const properties = e.features[0].properties;
            const geometry = e.features[0].geometry;
            const sidebar = document.getElementById('sidebar');
            sidebar.innerHTML = '<h3>Dados do Ponto</h3>';
            sidebar.innerHTML += `<p>Endereço: ${properties.endereco}</p>`;
            sidebar.innerHTML += `<p>Observações: ${properties.observacoe}</p>`;
            sidebar.innerHTML += `<p>Responsavel: ${properties.responsave} ${properties.responsavel}</p>`;
            sidebar.innerHTML += `<p>Tipo: ${properties.tipo}</p>`

            sidebar.innerHTML += `<p>Lat: ${geometry.coordinates[0]}</p>`
            sidebar.innerHTML += `<p>Log: ${geometry.coordinates[1]}</p>`
          });
        });
    }

    map.on('load', function () {
      // Carrega e adiciona camadas para /radares
      loadGeoJSONAndAddLayers('/radares', '#FF0000');

      // Carrega e adiciona camadas para /cameras
      loadGeoJSONAndAddLayers('/cameras', '#00FF00');

      loadGeoJSONAndAddLayers('/reconhecimento', '#4169E1');
    });
  </script>
</body>

</html>
````

E obvio, não vou soltar aqui os dados no post, mas tem um samples no meu Github, enfim é só isso mesmo time. 
Meu GitHub : https://github.com/JohnPonciano/ItuMapExploited/

É isso Pessoal, Valeu!