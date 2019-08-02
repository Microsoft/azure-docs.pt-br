---
title: Solicitar limites – API de Tradução de Texto
titleSuffix: Azure Cognitive Services
description: Este artigo lista os limites de solicitação para a API de Tradução de Texto. Cobranças são incorridas com base na contagem de caracteres, não a frequência de solicitação com um limite de 5.000 caracteres por solicitação. Limites de caractere são assinatura com base com F0 limitado a 2 milhões de caracteres por hora.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: f9620cc5f135dd7b10da5528e5dec0f5baa70350
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226244"
---
# <a name="request-limits-for-translator-text"></a>Limites de solicitação para a Tradução de Texto

Este artigo fornece limites de limitação para a API de Tradução de Texto. Os serviços incluem tradução, transliteração, detecção de comprimento de frase, detecção de idioma e traduções alternativas.

## <a name="character-and-array-limits-per-request"></a>Limites de matriz e de caractere por solicitação

Cada solicitação de traduzir é limitada a 5.000 caracteres. Você é cobrado por personagem, não pelo número de solicitações. É recomendável para enviar solicitações mais curtas.

As seguinte tabela listas matriz elemento e o caractere de limites para cada operação de API de tradução de texto.

| Operação | Tamanho máximo do elemento de matriz |   Número máximo de elementos de matriz |  Tamanho do máximo de solicitação (caracteres) |
|:----|:----|:----|:----|
| Translate | 5\.000 | 100   | 5\.000 |
| Transliterate | 5\.000 | 10    | 5\.000 |
| Detect | 10.000 | 100 |   50.000 |
| BreakSentence | 10.000    | 100 | 5,0000 |
| Pesquisa no dicionário| 100 |  10  | 1\.000 |
| Exemplos de dicionário | 100 para texto e 100 para tradução (total de 200)| 10|   2\.000 |

## <a name="character-limits-per-hour"></a>Limites de caractere por hora

Seu limite de caractere por hora baseia-se em sua camada de assinatura de Tradução de Texto. 

A cota por hora deve ser consumida uniformemente ao longo da hora. Por exemplo, no limite da camada F0 2 milhões de caracteres por hora, caracteres devem ser consumidos não mais rápido do que aproximadamente 33,300 caracteres por minuto (2 milhões de caracteres divididos por 60 minutos) de janela deslizante.

Se você atinge ou ultrapassar esses limites ou envia muito grande de uma parte da cota em um curto período de tempo, você provavelmente receberá uma falta de resposta de cota. Não há nenhum limite nas solicitações simultâneas.

| Camada | Limite de caracteres |
|------|-----------------|
| F0 | 2 milhões de caracteres por hora |
| S1 | 40 milhões de caracteres por hora |
| S2 / C2 | 40 milhões de caracteres por hora |
| S3 / C3 | 120 milhões de caracteres por hora |
| S4 / C4 | 200 milhões de caracteres por hora |

Limites para [multi-Service assinaturas](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication) são o mesmo que a camada S1.

Esses limites são restritos aos modelos de tradução padrão da Microsoft. Modelos de conversão personalizada que usam o conversor personalizado são limitados a 1.800 caracteres por segundo.

## <a name="latency"></a>Latency

A API de tradução de texto tem uma latência máxima de 15 segundos usando os modelos padrão. Usando modelos personalizados de tradução tem uma latência máxima de 25 segundos. Nesse momento você vai ter recebido um resultado ou uma resposta de tempo limite. Normalmente, as respostas são retornadas em 150 milissegundos para 300 milissegundos. Tempos de resposta variará com base no tamanho do par de solicitação e de idioma. Se você não receber uma tradução ou um [resposta de erro](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors) nesse período de tempo, você deve verificar sua conexão de rede e tente novamente.

## <a name="sentence-length-limits"></a>Limites de duração de sentença

Ao usar a função [BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence), o comprimento da sentença é limitado a 275 caracteres. Existem exceções para esses idiomas:

| Idioma | Código | Limite de caracteres |
|----------|------|-----------------|
| Chinês | zh | 132 |
| Alemão | de | 290 |
| Italiano | it | 280 |
| Japonês | ja | 150 |
| Português | pt | 290 |
| Espanhol | es | 280 |
| Italiano | it | 280 |
| Tailandês | th | 258 |

> [!NOTE]
> Esse limite não se aplica a traduções.

## <a name="next-steps"></a>Próximas etapas

* [Preços](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Disponibilidade regional](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [referência de API de Tradução de Texto v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
