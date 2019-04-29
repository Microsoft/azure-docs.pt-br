---
title: Análise de JSON e AVRO no Azure Stream Analytics
description: Este artigo descreve como operar em tipos de dados complexos, como matrizes, JSON, CSV dados formatados.
services: stream-analytics
ms.service: stream-analytics
author: lingliw
ms.author: v-lingwu
manager: digimobile
ms.topic: conceptual
origin.date: 08/03/2018
ms.date: 11/26/2018
ms.openlocfilehash: 3d4c1bfa8b376ec50efc7b3cddc8d22a40e70892
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478854"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Analisar dados JSON e Avro no Azure Stream Analytics

O Azure Stream Analytics dá suporte ao processamento de eventos em formatos de dados CSV, JSON e Avro. JSON e Avro podem conter tipos complexos, como objetos aninhados (registros) e matrizes. 

## <a name="array-data-types"></a>Tipos de dados de matrizes  
Tipos de dados de matriz são uma coleção ordenada de valores. Algumas operações típicas em valores da matriz são detalhadas abaixo. Estes exemplos supõem que os eventos de entrada tem uma propriedade chamada "arrayField" que é um tipo de dados de matriz.

Esses exemplos usam as funções [GetArrayElement](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelement-azure-stream-analytics), [GetArrayElements](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelements-azure-stream-analytics), [GetArrayLength](https://msdn.microsoft.com/azure/stream-analytics/reference/getarraylength-azure-stream-analytics)e o operador [Aplicar](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics).

## <a name="examples"></a>Exemplos  
 Selecione o elemento da matriz em um índice especificado (selecionando o primeiro elemento da matriz):  

```SQL 
SELECT   
    GetArrayElement(arrayField, 0) AS firstElement  
FROM input  
```  

 Selecionar o comprimento da matriz:  

```SQL  
SELECT   
    GetArrayLength(arrayField) AS arrayLength  
FROM input  
```  

Selecione todos os elementos de matriz como eventos individuais. O operador [APLICAR](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics) junto com a função [GetArrayElements](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelements-azure-stream-analytics) interna extrai todos os elementos da matriz como eventos individuais:  

```SQL  
SELECT   
    arrayElement.ArrayIndex,  
    arrayElement.ArrayValue  
FROM input as event  
CROSS APPLY GetArrayElements(event.arrayField) AS arrayElement  
```  

## <a name="record-data-types"></a>Tipos de dados do sistema  
Tipos de dados de registro são usados para representar matrizes JSON e Avro quando formatos correspondentes são usados em fluxos de dados de entrada. Esses exemplos demonstram um sensor de exemplo, o que está lendo os eventos de entrada no formato JSON. Aqui está o exemplo de um único evento:

```json  
{  
    "DeviceId" : "12345",  
    "Location" : 
    {
        "Lat": 47,
        "Long": 122 
    },  
    "SensorReadings" :  
    {  
        "Temperature" : 80,  
        "Humidity" : 70,  
        "CustomSensor01" : 5,  
        "CustomSensor02" : 99  
    }  
}  
```  

## <a name="examples"></a>Exemplos  
Use a notação de ponto (.) para acessar campos aninhados. Por exemplo, esta consulta seleciona o Lat e coordena Long sob a propriedade local nos dados JSON anteriores: 

```SQL  
SELECT  
    DeviceID,  
    Location.Lat,  
    Location.Long  
FROM input  
```  

Use a função [GetRecordPropertyValue](https://msdn.microsoft.com/azure/stream-analytics/reference/getrecordpropertyvalue-azure-stream-analytics) se o nome da propriedade for desconhecido. Por exemplo, imagine que um fluxo de dados de exemplo precisa ser unida com limites de recipiente de dados de referência para cada sensor do dispositivo:  

```json  
{  
    "DeviceId" : "12345",  
    "SensorName" :  "Temperature",
    "Value" : 75
}  
```  

```SQL  
SELECT  
    input.DeviceID,  
    thresholds.SensorName  
FROM input  
JOIN thresholds  
ON  
    input.DeviceId = thresholds.DeviceId  
WHERE  
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value  
```  

Para converter os campos de registro em eventos separados, use o operador [APLICAR](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics) junto com a função [GetRecordProperties](https://msdn.microsoft.com/azure/stream-analytics/reference/getrecordproperties-azure-stream-analytics). Por exemplo, para converter um fluxo de exemplo em um fluxo de eventos com leituras de sensor individuais, essa consulta pode ser usada:  

```SQL  
SELECT   
    event.DeviceID,  
    sensorReading.PropertyName,  
    sensorReading.PropertyValue  
FROM input as event  
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading  
```  

Você pode selecionar todas as propriedades de um registro aninhado usando o curinga'*'. Considere o exemplo a seguir:  

```SQL  
SELECT input.SensorReadings.*  
FROM input  
```  

O resultado é:  

```json  
{  
    "Temperature" : 80,  
    "Humidity" : 70,  
    "CustomSensor01" : 5,  
    "CustomSensor022" : 99  
}  
```  

## <a name="see-also"></a>Veja também  
 [Tipos de dados no Azure Stream Analytics](https://msdn.microsoft.com/azure/stream-analytics/reference/data-types-azure-stream-analytics)
 
<!-- Update_Description: new articles on stream analytics parsing json -->
<!--ms.date: 09/17/2018-->