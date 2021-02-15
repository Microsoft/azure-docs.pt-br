---
title: Limites de solicitação-Tradutor
titleSuffix: Azure Cognitive Services
description: Este artigo lista os limites de solicitação para o tradutor. Cobranças são incorridas com base na contagem de caracteres, não a frequência de solicitação com um limite de 5.000 caracteres por solicitação. Limites de caractere são assinatura com base com F0 limitado a 2 milhões de caracteres por hora.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: lajanuar
ms.openlocfilehash: 2bc2c1361c7d2f73ff8a67e906a6db725f669d52
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895400"
---
# <a name="request-limits-for-translator"></a>Limites de solicitação para o tradutor

Este artigo fornece limites de limitação para o tradutor. Os serviços incluem tradução, transliteração, detecção de comprimento de frase, detecção de idioma e traduções alternativas.

## <a name="character-and-array-limits-per-request"></a>Limites de caracteres e de matriz por solicitação

Cada solicitação de conversão é limitada a 10.000 caracteres, em todos os idiomas de destino para os quais você está convertendo. Por exemplo, o envio de uma solicitação de conversão de 3.000 caracteres para traduzir para três idiomas diferentes resulta em um tamanho de solicitação de 3000x3 = 9.000 caracteres, o que atende ao limite da solicitação. Você é cobrado por personagem, não pelo número de solicitações. É recomendável enviar solicitações mais curtas.

A tabela a seguir lista os limites de elemento e de caracteres de matriz para cada operação do tradutor.

| Operação | Tamanho máximo do elemento de matriz |    Número máximo de elementos da matriz |    Tamanho máximo da solicitação (caracteres) |
|:----|:----|:----|:----|
| Tradução | 10.000    | 100   | 10.000 |
| Transliterate | 5\.000 | 10    | 5\.000 |
| Detect | 50.000 | 100 |   50.000 |
| BreakSentence | 50.000    | 100 | 50.000 |
| Pesquisa no dicionário| 100 |  10  | 1,000 |
| Exemplos de dicionário | 100 para texto e 100 para conversão (total de 200)| 10|   2\.000 |

## <a name="character-limits-per-hour"></a>Limites de caractere por hora

O limite de caracteres por hora é baseado na camada de assinatura do tradutor. 

A cota por hora deve ser consumida uniformemente durante a hora. Por exemplo, no limite da camada F0 de 2 milhões caracteres por hora, os caracteres devem ser consumidos não mais rápido do que aproximadamente 33.300 caracteres por minuto janela deslizante (2 milhões caracteres divididos por 60 minutos).

Se você atingir ou ultrapassar esses limites, ou enviar um grande número de uma parte da cota em um curto período de tempo, provavelmente receberá uma resposta de cota insuficiente. Não há limites em solicitações simultâneas.

| Camada | Limite de caracteres |
|------|-----------------|
| F0 | 2 milhões de caracteres por hora |
| S1 | 40 milhões de caracteres por hora |
| S2/C2 | 40 milhões de caracteres por hora |
| S3/C3 | 120 milhões de caracteres por hora |
| S4/C4 | 200 milhões de caracteres por hora |

Os limites para [assinaturas de vários serviços](./reference/v3-0-reference.md#authentication) são os mesmos da camada S1.

Esses limites são restritos aos modelos de tradução padrão da Microsoft. Os modelos de tradução personalizados que usam o tradutor personalizado são limitados a 1.800 caracteres por segundo.

## <a name="latency"></a>Latency

O tradutor tem uma latência máxima de 15 segundos usando modelos padrão e 120 segundos ao usar modelos personalizados. Normalmente, as respostas *de texto dentro de 100 caracteres* são retornadas em 150 milissegundos a 300 milissegundos. Os modelos de Tradutor personalizados têm características de latência semelhantes na taxa de solicitação sustentada e podem ter uma latência mais alta quando a taxa de solicitação é intermitente. Os tempos de resposta variam de acordo com o tamanho da solicitação e do par de idiomas. Se você não receber uma conversão ou uma [resposta de erro](./reference/v3-0-reference.md#errors) dentro desse período, verifique seu código, sua conexão de rede e tente novamente. 

## <a name="sentence-length-limits"></a>Limites de duração de sentença

Ao usar a função [BreakSentence](./reference/v3-0-break-sentence.md), o comprimento da sentença é limitado a 275 caracteres. Existem exceções para esses idiomas:

| Idioma | Código | Limite de caracteres |
|----------|------|-----------------|
| Chinês | zh | 166 |
| Alemão | de | 800 |
| Italiano | it | 800 |
| Japonês | ja | 166 |
| Português | pt | 800 |
| Espanhol | es | 800 |
| Tailandês | th | 180 |

> [!NOTE]
> Esse limite não se aplica a traduções.

## <a name="next-steps"></a>Próximas etapas

* [Preços](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Disponibilidade regional](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [Referência do Tradutor v3](./reference/v3-0-reference.md)