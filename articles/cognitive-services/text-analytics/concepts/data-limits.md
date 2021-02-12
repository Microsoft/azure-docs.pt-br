---
title: Limites de dados para o API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Limitações de dados para a API de Análise de Texto dos Serviços Cognitivos do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 11/19/2020
ms.author: aahi
ms.reviewer: chtufts
ms.openlocfilehash: 9ba9fe7ca73e874fb55c228e22b884a86de736cf
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661452"
---
# <a name="data-and-rate-limits-for-the-text-analytics-api"></a>Limites de dados e taxa para a API de Análise de Texto
<a name="data-limits"></a>

Use este artigo para localizar os limites para o tamanho e as tarifas para as quais você pode enviar dados para API de Análise de Texto. Observe que o preço não é afetado pelos limites de dados ou pelos limites de taxa. O preço está sujeito aos [detalhes de preço](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) do recurso Análise de Texto.

## <a name="data-limits"></a>Limites de dados

> [!NOTE]
> * Se você precisar analisar documentos maiores do que o limite permite, poderá dividir o texto em partes menores antes de enviá-los para a API. 
> * Um documento é uma cadeia de caracteres de texto.  

| Limite | Valor |
|------------------------|---------------|
| Tamanho máximo de um único documento | 5\.120 caracteres conforme medidos por [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). Também se aplica à Análise de Texto para integridade. |
| Tamanho máximo de um documento individual (ponto de extremidade `/analyze`)  | 125 mil caracteres, conforme calculado por [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements). Não se aplica à Análise de Texto para integridade. |
| Tamanho máximo de toda a solicitação | 1 MB. Também se aplica à Análise de Texto para integridade. |


Se um documento exceder o limite de caracteres, a API se comportará de maneira diferente, dependendo do ponto de extremidade que você estiver usando:

* Ponto de extremidade `/analyze`:
  * A API rejeitará toda a solicitação e retornará um erro `400 bad request` se qualquer documento dentro dela exceder o tamanho máximo.
* Todos os outros pontos de extremidade:  
  * A API não processará os documentos que excederem o tamanho máximo e retornará um erro de documento inválido para cada um deles. Se uma solicitação de API tiver vários documentos, a API continuará a processá-los se estiverem dentro do limite de caracteres.

O número máximo de documentos que você pode enviar em uma solicitação dependerá da versão da API e do recurso que você está usando, conforme descrito na tabela abaixo.

#### <a name="version-3"></a>[Versão 3](#tab/version-3)

Os limites a seguir referem-se à API v3 atual. Exceder os limites abaixo vai gerar um código de erro HTTP 400.


| Recurso | Máximo de documentos por solicitação | 
|----------|-----------|
| Detecção de Idioma | 1000 |
| Análise de Sentimento | 10 |
| Mineração de opiniões | 10 |
| Extração de Frases-Chave | 10 |
| Reconhecimento de Entidade Nomeada | 5 |
| Vinculação de Identidade | 5 |
| Análise de Texto para integridade  | 10 para a API baseada na Web, 1.000 para o contêiner. |
| Analisar ponto de extremidade | 25 para todas as operações. |

#### <a name="version-2"></a>[Versão 2](#tab/version-2)

| Recurso | Máximo de documentos por solicitação | 
|----------|-----------|
| Detecção de Idioma | 1000 |
| Análise de Sentimento | 1000 |
| Extração de Frases-Chave | 1000 |
| Reconhecimento de Entidade Nomeada | 1000 |
| Vinculação de Identidade | 1000 |

---

## <a name="rate-limits"></a>Limites de taxa

O limite de taxa varia de acordo com o [tipo de preço](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/). Esses limites são os mesmos para as duas versões da API. Esses limites de taxa não se aplicam à Análise de Texto do contêiner de integridade, que não tem um limite de taxa definido.

| Camada          | Solicitações por segundo | Solicitações por minuto |
|---------------|---------------------|---------------------|
| S/Vários serviços | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

As taxas de solicitações são calculadas separadamente para cada recurso da Análise de Texto. Você pode enviar o número máximo de solicitações do tipo de preço para cada recurso, ao mesmo tempo. Por exemplo, se você estiver na camada `S` e enviar 1.000 solicitações de uma vez, não poderá enviar outra solicitação por 59 segundos.


## <a name="see-also"></a>Confira também

* [O que é a API de Análise de Texto](../overview.md)
* [Detalhes de preço](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)
