---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: 5ad589c4adb60369f81979e214935f73d9eb0755
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60309475"
---
<a name="definitions"></a>
## <a name="definitions"></a>Definições

<a name="point"></a>
### <a name="point"></a>Point

|NOME|DESCRIÇÃO|Esquema|
|---|---|---|
|**Timestamp**  <br>*opcional*|O carimbo de data/hora para o ponto de dados. Certifique-se de que ele se alinhe com a meia-noite e use uma cadeia de caracteres de data/hora em UTC, por exemplo, 2017-08-01T00:00:00Z.|cadeia de caracteres (data e hora)|
|**Valor**  <br>*opcional*|Um valor de medida de dados.|número (double)|


<a name="request"></a>
### <a name="request"></a>Solicitação

|NOME|DESCRIÇÃO|Esquema|
|---|---|---|
|**Período**  <br>*opcional*|O período dos pontos de dados. Se o valor for nulo ou se não está presente, a API determinará o período automaticamente.|número (double)|
|**Pontos**  <br>*opcional*|Os pontos de dados da série de tempo. Os dados devem ser classificados por ordem crescente de carimbo de data/hora para corresponder com o resultado de anomalias. Se os dados não forem classificados corretamente ou se houver um carimbo de data/hora, a API detectará os pontos de anomalia corretamente, mas você não poderia fazer a correspondência dos pontos retornados com a entrada. Nesse caso, uma mensagem de aviso será adicionada na resposta.|< [ponto](#point) > matriz|


<a name="response"></a>
### <a name="response"></a>Response

|NOME|DESCRIÇÃO|Esquema|
|---|---|---|
|**ExpectedValues**  <br>*opcional*|O valor previsto pelo modelo baseado em aprendizado. Se os pontos de dados de entrada forem classificados por carimbo de data/hora crescente, o índice da matriz poderá ser usado para mapear o valor esperado e o valor original.|< número (double) > matriz|
|**IsAnomaly**  <br>*opcional*|O resultado se os pontos de dados são anomalias ou não em ambas as direções (picos ou quedas). true significa que o ponto é uma anomalia, false significa que o ponto não é uma anomalia. Se os pontos de dados de entrada forem classificados por carimbo de data/hora crescente, o índice da matriz poderá ser usado para mapear o valor esperado e o valor original.|< booliano > matriz|
|**IsAnomaly_Neg**  <br>*opcional*|O resultado que diz se os pontos de dados são anomalias na direção negativa (quedas). verdadeiro significa que a direção das anomalias é negativa. Se os pontos de dados de entrada forem classificados por carimbo de data/hora crescente, o índice da matriz poderá ser usado para mapear o valor esperado e o valor original.|< booliano > matriz|
|**IsAnomaly_Pos**  <br>*opcional*|O resultado que diz se os pontos de dados são anomalias na direção positiva (picos). verdadeiro significa que a direção das anomalias é positiva. Se os pontos de dados de entrada forem classificados por carimbo de data/hora crescente, o índice da matriz poderá ser usado para mapear o valor esperado e o original.|< booliano > matriz|
|**LowerMargin**  <br>*opcional*|(ExpectedValue - LowerMargin) determina se o limite inferior do ponto de dados ainda é considerado como normal. Se os pontos de dados de entrada forem classificados por carimbo de data/hora crescente, o índice da matriz poderá ser usado para mapear o valor esperado e o valor original.|< número (double) > matriz|
|**Período**  <br>*opcional*|O período em que a API detectou os pontos de anomalias.|número (float)|
|**UpperMargin**  <br>*opcional*|A soma de ExpectedValue e UpperMargin determina o limite superior do ponto de dados para o qual ele ainda é considerado como normal. Se os pontos de dados de entrada forem classificados por carimbo de data/hora crescente, o índice da matriz poderá ser usado para mapear o valor esperado e o valor original.|< número (double) > matriz|
|**WarningText**  <br>*opcional*|Se os pontos de dados de entrada fornecidos não estiverem seguindo a regra que requer que a API e os dados ainda possam ser detectados pela API, a API analisará os dados e acrescentará as informações de advertência neste campo.|string|



