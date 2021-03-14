---
title: Introdução às funções geoespaciais do Azure Stream Analytics
description: Este artigo descreve funções geoespaciais que são usadas em trabalhos do Azure Stream Analytics.
ms.service: stream-analytics
author: enkrumah
ms.author: ebnkruma
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: a5e65ddacfd4c168d4b97816e448951bacc2b534
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102438533"
---
# <a name="introduction-to-stream-analytics-geospatial-functions"></a>Introdução às funções geoespaciais do Stream Analytics

As funções geoespaciais no Azure Stream Analytics permitem análises em tempo real em dados geoespaciais de streaming. Com apenas algumas linhas de código, é possível desenvolver uma solução de nível de produção para cenários complexos. Essas funções dão suporte a todos os tipos WKT e geojson ponto, polígono e LineString.

Exemplos de cenários que podem se beneficiar de funções geoespaciais incluem:

* Compartilhamento de percurso
* Gerenciamento de frota
* Acompanhamento de ativos
* Isolamento geográfico
* Rastreamento por telefone em sites de celular

A Linguagem de Consulta do Stream Analytics tem sete funções geoespaciais internas: **CreateLineString**, **CreatePoint**, **CreatePolygon**, **ST_DISTANCE**, **ST_OVERLAPS**, **ST_INTERSECTS** e **ST_WITHIN**.

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

Para saber mais, visite a referência [CreateLineString](/stream-analytics-query/createlinestring).

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

Para saber mais, visite a referência [CreatePoint](/stream-analytics-query/createpoint).

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

Para saber mais, visite a referência [CreatePolygon](/stream-analytics-query/createpolygon).


## <a name="st_distance"></a>ST_DISTANCE
A `ST_DISTANCE` função retorna a distância entre duas geometrias em metros. 

A consulta a seguir usa `ST_DISTANCE` para gerar um evento quando um posto de gasolina está a menos de 10 km do carro.

```SQL
SELECT Cars.Location, Station.Location 
FROM Cars c  
JOIN Station s ON ST_DISTANCE(c.Location, s.Location) < 10 * 1000
```

Para saber mais, visite a referência [ST_DISTANCE](/stream-analytics-query/st-distance).

## <a name="st_overlaps"></a>ST_OVERLAPS
A `ST_OVERLAPS` função compara duas geometrias. Se as geometrias se sobrepõem, a função retorna um 1. A função retornará 0 se as geometrias não se sobrepõem. 

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

Para saber mais, visite a referência [ST_OVERLAPS](/stream-analytics-query/st-overlaps).

## <a name="st_intersects"></a>ST_INTERSECTS
A `ST_INTERSECTS` função compara duas geometrias. Se as geometrias se interseccionarem, a função retornará 1. A função retornará 0 se as geometrias não se interseccionarem.

A consulta de exemplo a seguir usa `ST_INTERSECTS` para determinar se uma estrada pavimentada intersecciona uma estrada de terra.

```SQL 
SELECT  
     ST_INTERSECTS(input.pavedRoad, input.dirtRoad)  
FROM input  
```  

### <a name="input-example"></a>Exemplo de entrada  
  
|datacenterArea|stormArea|  
|--------------------|---------------|  
|{"tipo": "LineString", "coordenadas": [[-10,0, 0,0], [0,0, 0,0], [10,0, 0,0]]}|{"tipo": "LineString", "coordenadas": [[0,0, 10,0], [0,0, 0,0], [0,0,-10,0]]}|  
|{"tipo": "LineString", "coordenadas": [[-10,0, 0,0], [0,0, 0,0], [10,0, 0,0]]}|{"tipo": "LineString", "coordenadas": [[-10,0, 10,0], [0,0, 10,0], [10,0, 10,0]]}|  
  
### <a name="output-example"></a>Exemplo de saída  

 1  
  
 0  

Para saber mais, visite a referência[ST_INTERSECTS](/stream-analytics-query/st-intersects).

## <a name="st_within"></a>ST_WITHIN
A `ST_WITHIN` função determina se uma geometria está dentro de outra geometria. Se o primeiro estiver contido no último, a função retornará 1. A função retornará 0 se a primeira geometria não estiver localizada dentro da última.

A consulta de exemplo a seguir usa `ST_WITHIN` para determinar se o ponto de destino de entrega está dentro do polígono do depósito especificado.

```SQL 
SELECT  
     ST_WITHIN(input.deliveryDestination, input.warehouse)  
FROM input 
```  

### <a name="input-example"></a>Exemplo de entrada  
  
|deliveryDestination|depósito|  
|-------------------------|---------------|  
|{"Type": "ponto", "coordenadas": [76,6, 10,1]}|{"tipo": "Polygon", "coordenadas": [[0,0, 0,0], [10,0, 0,0], [10,0, 10,0], [0,0, 10,0], [0,0, 0,0]]}|  
|{"Type": "ponto", "coordenadas": [15,0, 15,0]}|{"tipo": "Polygon", "coordenadas": [[10,0, 10,0], [20,0, 10,0], [20,0, 20,0], [10,0, 20,0], [10,0, 10,0]]}|  
  
### <a name="output-example"></a>Exemplo de saída  

 0  
  
 1  

Para saber mais, visite a referência [ST_WITHIN](/stream-analytics-query/st-within).

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Stream Analytics do Azure](stream-analytics-introduction.md)
* [Introdução ao uso do Stream Analytics do Azure](stream-analytics-real-time-fraud-detection.md)
* [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
* [Referência de Linguagem de Consulta do Stream Analytics do Azure](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](/rest/api/streamanalytics/)
