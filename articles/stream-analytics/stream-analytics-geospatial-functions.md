---
title: Introdução às funções geoespaciais do Azure Stream Analytics
description: Este artigo descreve funções geoespaciais que são usadas em trabalhos do Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: ad789a597da759b9a2d58138c7ed441389a12adb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479976"
---
# <a name="introduction-to-stream-analytics-geospatial-functions"></a>Introdução às funções geoespaciais do Stream Analytics

As funções geoespaciais no Azure Stream Analytics permitem análises em tempo real em dados geoespaciais de streaming. Com apenas algumas linhas de código, é possível desenvolver uma solução de nível de produção para cenários complexos. 

Exemplos de cenários que podem se beneficiar de funções geoespaciais incluem:

* Compartilhamento de percurso
* Gerenciamento de frota
* Acompanhamento de ativos
* Isolamento geográfico
* Rastreamento por telefone em sites de celular

A linguagem de consulta do Stream Analytics tem sete funções geoespaciais internas: **CreateLineString**, **CreatePoint**, **CreatePolygon**, **ST_DISTANCE**, **ST_OVERLAPS**, **ST_INTERSECTS** e **ST_WITHIN**.

## <a name="createlinestring"></a>CreateLineString

A função `CreateLineString` aceita pontos e retorna um LineString GeoJSON, que pode ser plotado como uma linha em um mapa. É necessário ter pelo menos dois pontos para criar um LineString. Os pontos de LineString serão conectados em ordem.

A consulta a seguir usa `CreateLineString` para criar um LineString usando três pontos. O primeiro ponto é criado a partir dos dados de entrada de streaming, enquanto os outros dois são criados manualmente.

```SQL 
SELECT  
     CreateLineString(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5))  
FROM input  
```  

### <a name="input-example"></a>Exemplo de entrada  
  
|latitude|longitude|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Exemplo de saída  

 {"type" : "LineString", "coordinates" : [ [-10.2, 3.0], [10.0, 10.0], [10.5, 10.5] ]}

 {"type" : "LineString", "coordinates" : [ [20.2321, -87.33], [10.0, 10.0], [10.5, 10.5] ]}

Para saber mais, visite a referência [CreateLineString](https://msdn.microsoft.com/azure/stream-analytics/reference/createlinestring).

## <a name="createpoint"></a>CreatePoint

A função `CreatePoint` aceita uma latitude e longitude e retorna um ponto GeoJSON, que pode ser plotado em um mapa. As latitudes e longitudes devem ser um tipo de dados **float**.

A consulta de exemplo a seguir usa `CreatePoint` para criar um ponto usando latitudes e longitudes dos dados de entrada de streaming.

```SQL 
SELECT  
     CreatePoint(input.latitude, input.longitude)  
FROM input 
```  

### <a name="input-example"></a>Exemplo de entrada  
  
|latitude|longitude|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Exemplo de saída
  
 {"type" : "Point", "coordinates" : [-10.2, 3.0]}  
  
 {"type" : "Point", "coordinates" : [20.2321, -87.33]}  

Para saber mais, visite a referência [CreatePoint](https://msdn.microsoft.com/azure/stream-analytics/reference/createpoint).

## <a name="createpolygon"></a>CreatePolygon

A função `CreatePolygon` aceita pontos e retorna um registro de polígono GeoJSON. A ordem dos pontos deve seguir a orientação do polígono direito ou sentido anti-horário. Imagine andar de um ponto a outro na ordem em que foram declarados. O centro do polígono estaria à esquerda o tempo todo.

A consulta de exemplo a seguir usa `CreatePolygon` para criar um polígono a partir de três pontos. Os dois primeiros pontos são criados manualmente e o último ponto é criado a partir dos dados de entrada.

```SQL 
SELECT  
     CreatePolygon(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5), CreatePoint(input.latitude, input.longitude))  
FROM input  
```  

### <a name="input-example"></a>Exemplo de entrada  
  
|latitude|longitude|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>Exemplo de saída  

 {"type" : "Polygon", "coordinates" : [[ [-10.2, 3.0], [10.0, 10.0], [10.5, 10.5], [-10.2, 3.0] ]]}
 
 {"type" : "Polygon", "coordinates" : [[ [20.2321, -87.33], [10.0, 10.0], [10.5, 10.5], [20.2321, -87.33] ]]}

Para saber mais, visite a referência [CreatePolygon](https://msdn.microsoft.com/azure/stream-analytics/reference/createpolygon).


## <a name="stdistance"></a>ST_DISTANCE
A função `ST_DISTANCE` retorna a distância entre dois pontos em metros. 

A consulta a seguir usa `ST_DISTANCE` para gerar um evento quando um posto de gasolina está a menos de 10 km do carro.

```SQL
SELECT Cars.Location, Station.Location 
FROM Cars c  
JOIN Station s ON ST_DISTANCE(c.Location, s.Location) < 10 * 1000
```

Para saber mais, visite a referência [ST_DISTANCE](https://msdn.microsoft.com/azure/stream-analytics/reference/st-distance).

## <a name="stoverlaps"></a>ST_OVERLAPS
A função `ST_OVERLAPS` compara dois polígonos. Se os polígonos se sobrepuserem, a função retornará um 1. A função retornará 0, se os polígonos não se sobrepuserem. 

A consulta a seguir usa `ST_OVERLAPS` para gerar um evento quando uma construção estiver dentro de uma possível zona de inundação.

```SQL
SELECT Building.Polygon, Building.Polygon 
FROM Building b 
JOIN Flooding f ON ST_OVERLAPS(b.Polygon, b.Polygon) 
```

A consulta de exemplo a seguir gera um evento quando uma tempestade indo em direção a um carro.

```SQL
SELECT Cars.Location, Storm.Course
FROM Cars c, Storm s
JOIN Storm s ON ST_OVERLAPS(c.Location, s.Course)
```

Para saber mais, visite a referência [ST_OVERLAPS](https://msdn.microsoft.com/azure/stream-analytics/reference/st-overlaps).

## <a name="stintersects"></a>ST_INTERSECTS
A função `ST_INTERSECTS` compara dois LineString. Se o LineString interseccionar, a função retornará 1. A função retornará 0, se a LineString não interseccionar.

A consulta de exemplo a seguir usa `ST_INTERSECTS` para determinar se uma estrada pavimentada intersecciona uma estrada de terra.

```SQL 
SELECT  
     ST_INTERSECTS(input.pavedRoad, input.dirtRoad)  
FROM input  
```  

### <a name="input-example"></a>Exemplo de entrada  
  
|datacenterArea|stormArea|  
|--------------------|---------------|  
|{“type”:”LineString”, “coordinates”: [ [-10.0, 0.0], [0.0, 0.0], [10.0, 0.0] ]}|{“type”:”LineString”, “coordinates”: [ [0.0, 10.0], [0.0, 0.0], [0.0, -10.0] ]}|  
|{“type”:”LineString”, “coordinates”: [ [-10.0, 0.0], [0.0, 0.0], [10.0, 0.0] ]}|{“type”:”LineString”, “coordinates”: [ [-10.0, 10.0], [0.0, 10.0], [10.0, 10.0] ]}|  
  
### <a name="output-example"></a>Exemplo de saída  

 1  
  
 0  

Para saber mais, visite a referência[ST_INTERSECTS](https://msdn.microsoft.com/azure/stream-analytics/reference/st-intersects).

## <a name="stwithin"></a>ST_WITHIN
A função `ST_WITHIN` determina se um ponto ou polígono está dentro de um polígono. Se o polígono contiver o ponto ou polígono, a função retornará 1. A função retornará 0, se o ponto ou polígono não estiver localizado no polígono declarado.

A consulta de exemplo a seguir usa `ST_WITHIN` para determinar se o ponto de destino de entrega está dentro do polígono do depósito especificado.

```SQL 
SELECT  
     ST_WITHIN(input.deliveryDestination, input.warehouse)  
FROM input 
```  

### <a name="input-example"></a>Exemplo de entrada  
  
|deliveryDestination|depósito|  
|-------------------------|---------------|  
|{“type”:”Point”, “coordinates”: [76.6, 10.1]}|{“type”:”Polygon”, “coordinates”: [ [0.0, 0.0], [10.0, 0.0], [10.0, 10.0], [0.0, 10.0], [0.0, 0.0] ]}|  
|{“type”:”Point”, “coordinates”: [15.0, 15.0]}|{“type”:”Polygon”, “coordinates”: [ [10.0, 10.0], [20.0, 10.0], [20.0, 20.0], [10.0, 20.0], [10.0, 10.0] ]}|  
  
### <a name="output-example"></a>Exemplo de saída  

 0  
  
 1  

Para saber mais, visite a referência [ST_WITHIN](https://msdn.microsoft.com/azure/stream-analytics/reference/st-within).

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)