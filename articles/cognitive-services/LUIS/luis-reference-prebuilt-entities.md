---
title: Todas as entidades predefinidas
titleSuffix: Azure Cognitive Services
description: Este artigo contém listas das entidades predefinidas incluídas em LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/05/2019
ms.author: diberry
ms.openlocfilehash: 96157511c0255774a64c0f64a333fe88a37f80d6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60712681"
---
# <a name="entities-per-culture-in-your-luis-model"></a>Entidades por cultura em seu modelo do LUIS

O LUIS (Serviço Inteligente de Reconhecimento Vocal) fornece entidades predefinidas. Quando uma entidade predefinida é incluída em seu aplicativo, o LUIS inclui a previsão de entidade correspondente na resposta do ponto de extremidade. Todos os enunciados de exemplo também são rotulados com a entidade. O comportamento das entidades predefinidas **não pode** ser modificado. A menos que indicado de outra forma, entidades predefinidas estão disponíveis em todas as localidades (culturas) do aplicativo de LUIS. A tabela a seguir mostra as entidades predefinidas compatíveis com cada cultura.

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
|Turco|[turco](#turkish-entity-support)|Não há entidades predefinidas com suporte em turco|

## <a name="chinese-entity-support"></a>Suporte de entidades chinesas

Há suporte para as seguintes entidades:

|Entidade predefinida|```zh-CN``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>dia   |    ✔   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionária (ex: centavo)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    ✔   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milhas por hora)  |    ✔   | 
[Email](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   | 
[Número](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentual](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   | 
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="dutch-entity-support"></a>Suporte de entidades holandesas

Há suporte para as seguintes entidades:

|Entidade predefinida|```nl-NL``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>dia   |    ✔   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionária (ex: centavo)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    -   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milhas por hora)  |    ✔   | 
[Email](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Número](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentual](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="english-american-entity-support"></a>Suporte a entidades inglesas (americanas)

Há suporte para as seguintes entidades:

|Entidade predefinida|```en-US``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>dia   |    ✔   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionária (ex: centavo)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    ✔   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milhas por hora)  |    ✔   | 
[Email](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    ✔   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Número](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentual](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   | 
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="french-france-entity-support"></a>Suporte de entidade do francês (França)

Há suporte para as seguintes entidades:

|Entidade predefinida|```fr-FR``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>dia   |    ✔   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionária (ex: centavo)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    ✔   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milhas por hora)  |    ✔   | 
[Email](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Número](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentual](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |   -   | 
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="french-canadian-entity-support"></a>Suporte a entidades francesas (canadenses)

Há suporte para as seguintes entidades:

|Entidade predefinida|```fr-CA``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>dia   |    ✔   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionária (ex: centavo)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    ✔   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milhas por hora)  |    ✔   | 
[Email](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Número](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentual](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="german-entity-support"></a>Suporte da entidade alemã

Há suporte para as seguintes entidades:

|Entidade predefinida|```de-DE``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>dia   |    ✔   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionária (ex: centavo)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    ✔   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milhas por hora)  |    ✔   | 
[Email](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Número](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentual](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="italian-entity-support"></a>Suporte da entidade alemã

Há suporte para as seguintes entidades:

|Entidade predefinida|```it-IT``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>dia   |    ✔   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionária (ex: centavo)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    -   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milhas por hora)  |    ✔   | 
[Email](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Número](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentual](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="japanese-entity-support"></a>Suporte a entidade japonesa

Há suporte para as seguintes entidades:

|Entidade predefinida|```ja-JP``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>dia   |    ✔   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionária (ex: centavo)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    -   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milhas por hora)  |    ✔   | 
[Email](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Número](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentual](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="korean-entity-support"></a>Suporte da entidade alemã

Há suporte para as seguintes entidades:

|Entidade predefinida|```ko-KR``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>dia   |    -   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionária (ex: centavo)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    -   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milhas por hora)  |    -   | 
[Email](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Número](luis-reference-prebuilt-number.md)   |    -   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |  
[Percentual](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="portuguese-brazil-entity-support"></a>Suporte de entidade do português (Brasil)

Há suporte para as seguintes entidades:

|Entidade predefinida|```pt-BR``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>dia   |    ✔   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionária (ex: centavo)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    ✔   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milhas por hora)  |    ✔   | 
[Email](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Número](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentual](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="spanish-spain-entity-support"></a>Suporte de entidade de espanhol (Espanha)

Há suporte para as seguintes entidades:

|Entidade predefinida|```es-ES``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>dia   |    ✔   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionária (ex: centavo)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    ✔   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milhas por hora)  |    ✔   | 
[Email](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Número](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentual](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="spanish-mexico-entity-support"></a>Suporte de entidade espanhola (México)

Há suporte para as seguintes entidades:

|Entidade predefinida|```es-MX``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>dia   |    -   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionária (ex: centavo)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    -   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milhas por hora)  |    -   | 
[Email](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Número](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |  
[Percentual](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

Veja as observações sobre [Entidades predefinidas preteridas](luis-reference-prebuilt-deprecated.md)

A frase-chave não está disponível em todas as subculturas de Português (Brasil) - ```pt-BR```.

## <a name="turkish-entity-support"></a>Suporte de entidades turcas

**Não há nenhum as entidades predefinidas com suporte em turco.** 

<!--

|Prebuilt entity|```tr-tr``` |
------|:------:|
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

GeographyV2 e PersonName não estão incluídos no projeto reconhecedores de texto. Para problemas com essas entidades predefinidas, abra uma [solicitação de suporte](../../azure-supportability/how-to-create-azure-support-request.md). 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as entidades [número](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md) e [moeda](luis-reference-prebuilt-currency.md). 
