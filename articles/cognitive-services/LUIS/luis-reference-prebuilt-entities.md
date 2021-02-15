---
title: Todas as entidades predefinidas-LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém listas das entidades predefinidas incluídas em LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/20/2020
ms.openlocfilehash: 7a4654b8f389ad18010bb4ad211103f2816a18ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541995"
---
# <a name="entities-per-culture-in-your-luis-model"></a>Entidades por cultura em seu modelo do LUIS

O LUIS (Serviço Inteligente de Reconhecimento Vocal) fornece entidades predefinidas.

## <a name="entity-resolution"></a>Resolução de entidade
Quando uma entidade predefinida é incluída em seu aplicativo, o LUIS inclui a resolução de entidade correspondente na resposta do ponto de extremidade. Todos os enunciados de exemplo também são rotulados com a entidade.

O comportamento de entidades predefinidas não pode ser modificado, mas você pode melhorar [a resolução adicionando a entidade predefinida como um recurso a uma entidade ou subentidade de aprendizado de máquina](luis-concept-entity-types.md#effective-prebuilt-entities).

## <a name="availability"></a>Disponibilidade
A menos que indicado de outra forma, entidades predefinidas estão disponíveis em todas as localidades (culturas) do aplicativo de LUIS. A tabela a seguir mostra as entidades predefinidas compatíveis com cada cultura.

|Cultura|Subculturas|Observações|
|--|--|--|
|Chinês|[zh-CN](#chinese-entity-support)||
|Holandês|[nl-NL](#dutch-entity-support)||
|Inglês|[en-US (Americano)](#english-american-entity-support)||
|Francês|[fr-CA (Canadá)](#french-canadian-entity-support), [fr-FR (França)](#french-france-entity-support), ||
|Alemão|[de-DE](#german-entity-support)||
|Italiano|[it-IT](#italian-entity-support)||
|Japonês|[ja-JP](#japanese-entity-support)||
|Coreano|[ko-KR](#korean-entity-support)||
|Português|[pt-BR (Brasil)](#portuguese-brazil-entity-support)||
|Espanhol|[es-ES (Espanha)](#spanish-spain-entity-support), [es-MX (México)](#spanish-mexico-entity-support)||
|Turco|[Turco](#turkish-entity-support)|Não há suporte para entidades predefinidas em Turco|

## <a name="prediction-endpoint-runtime"></a>Tempo de execução de ponto de extremidade de previsão

A disponibilidade de uma entidade predefinida em um idioma específico é determinada pela versão de tempo de execução de ponto de extremidade de previsão.

## <a name="chinese-entity-support"></a>Suporte de entidades chinesas

Há suporte para as seguintes entidades:

| Entidade predefinida | zh-CN |
| --------------- | :---: |
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>week<br>dia   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionária (ex: centavo)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    V2, V3   |
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milhas por hora)  |    V2, V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Número](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentual](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    V2, V3   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="dutch-entity-support"></a>Suporte de entidades holandesas

Há suporte para as seguintes entidades:

| Entidade predefinida | nl-NL |
| --------------- | :---: |
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>week<br>dia   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionária (ex: centavo)  |    V2, V3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milhas por hora)  |    V2, V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Número](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentual](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="english-american-entity-support"></a>Suporte a entidades inglesas (americanas)

Há suporte para as seguintes entidades:

| Entidade predefinida | pt-BR |
| --------------- | :---: |
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>week<br>dia   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionária (ex: centavo)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    V2, V3   |
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milhas por hora)  |    V2, V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    V2, V3   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Número](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    V2, V3   |
[Percentual](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    V2, V3   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-france-entity-support"></a>Suporte de entidade do francês (França)

Há suporte para as seguintes entidades:

| Entidade predefinida | fr-FR |
| --------------- | :---: |
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>week<br>dia   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionária (ex: centavo)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    V2, V3   |
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milhas por hora)  |    V2, V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Número](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentual](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |   -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-canadian-entity-support"></a>Suporte a entidades francesas (canadenses)

Há suporte para as seguintes entidades:

| Entidade predefinida | fr-CA |
| --------------- | :---: |
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>week<br>dia   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionária (ex: centavo)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    V2, V3   |
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milhas por hora)  |    V2, V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Número](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentual](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="german-entity-support"></a>Suporte da entidade alemã

Há suporte para as seguintes entidades:

|Entidade predefinida | de-DE |
| -------------- | :---: |
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>week<br>dia   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionária (ex: centavo)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    V2, V3   |
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milhas por hora)  |    V2, V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Número](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentual](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="italian-entity-support"></a>Suporte da entidade alemã

Idade predefinida italiano, moeda, dimensão, número, _resolução_ de porcentagem alterada da versão de visualização V2 e v3.

Há suporte para as seguintes entidades:

| Entidade predefinida | it-IT |
| --------------- | :---: |
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>week<br>dia   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionária (ex: centavo)  |    V2, V3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milhas por hora)  |    V2, V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Número](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentual](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="japanese-entity-support"></a>Suporte a entidade japonesa

Há suporte para as seguintes entidades:

|Entidade predefinida | ja-JP |
| -------------- | :---: |
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>week<br>dia   |    V2,-   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionária (ex: centavo)  |    V2,-   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milhas por hora)  |    V2,-   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Número](luis-reference-prebuilt-number.md)   |    V2,-   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2,-   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentual](luis-reference-prebuilt-percentage.md)   |    V2,-   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2,-   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="korean-entity-support"></a>Suporte da entidade alemã

Há suporte para as seguintes entidades:

| Entidade predefinida | ko-KR |
| --------------- | :---: |
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>week<br>dia   |    -   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionária (ex: centavo)  |    -   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milhas por hora)  |    -   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Número](luis-reference-prebuilt-number.md)   |    -   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentual](luis-reference-prebuilt-percentage.md)   |    -   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="portuguese-brazil-entity-support"></a>Suporte de entidade do português (Brasil)

Há suporte para as seguintes entidades:

| Entidade predefinida | pt-BR |
| --------------- | :---: |
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>week<br>dia   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionária (ex: centavo)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    V2, V3   |
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milhas por hora)  |    V2, V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Número](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentual](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-spain-entity-support"></a>Suporte de entidade de espanhol (Espanha)

Há suporte para as seguintes entidades:

| Entidade predefinida | es-ES |
| --------------- | :---: |
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>week<br>dia   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionária (ex: centavo)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    V2, V3   |
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milhas por hora)  |    V2, V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Número](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentual](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-mexico-entity-support"></a>Suporte de entidade espanhola (México)

Há suporte para as seguintes entidades:

| Entidade predefinida | es-MX |
| --------------- | :---: |
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>week<br>dia   |    -   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionária (ex: centavo)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    -   |
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milhas por hora)  |    -   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Número](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentual](luis-reference-prebuilt-percentage.md)   |    -   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

Veja as observações sobre [Entidades predefinidas preteridas](luis-reference-prebuilt-deprecated.md)

A frase-chave não está disponível em todas as subculturas de Português (Brasil) - ```pt-BR```.

## <a name="turkish-entity-support"></a>Suporte de entidades turcas

**Não há entidades predefinidas com suporte em Turco.**

<!--

| Prebuilt entity | tr-tr |
| --------------- | :---: |
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    -   |
[Currency (money)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (ex: penny)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    -   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>volume<br>area<br>weight<br>information (ex: bit/byte)<br>length (ex: meter)<br>speed (ex: mile per hour)  |    -   |
[Email](luis-reference-prebuilt-email.md)   |    -   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Number](luis-reference-prebuilt-number.md)   |    -   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    -   |
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   |
[URL](luis-reference-prebuilt-url.md)   |    -   |

See notes on [Deprecated prebuilt entities](luis-reference-prebuilt-deprecated.md)


KeyPhrase is not available.
-->

## <a name="contribute-to-prebuilt-entity-cultures"></a>Contribuir para culturas de entidade predefinida
As entidades predefinidas são desenvolvidas no projeto de software livre Recognizers-Text. [Contribua com](https://github.com/Microsoft/Recognizers-Text) para o projeto. Esse projeto inclui exemplos de moeda por cultura.

GeographyV2 e PersonName não estão incluídos no projeto reconhecedores de texto. Para problemas com essas entidades predefinidas, abra uma [solicitação de suporte](../../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as entidades [número](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md) e [moeda](luis-reference-prebuilt-currency.md).
