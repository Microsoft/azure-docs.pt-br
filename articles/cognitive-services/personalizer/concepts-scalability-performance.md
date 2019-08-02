---
title: Escalabilidade e desempenho-personalizador
titleSuffix: Azure Cognitive Services
description: 'Os aplicativos e os sites de alto desempenho e alto tráfego têm dois principais fatores a considerar com o Personalizador para desempenho e escalabilidade: taxa de transferência de treinamento e latência.'
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: diberry
ms.openlocfilehash: d116f6bd389b1404ea723c965111cd05880e6c30
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662811"
---
# <a name="scalability-and-performance"></a>Desempenho e escalabilidade

Os aplicativos e os sites de alto desempenho e de alto tráfego têm dois fatores principais a considerar com o Personalizador para desempenho e escalabilidade:

* Como manter a baixa latência ao fazer chamadas à API de Classificação
* Como garantir que a taxa de transferência de treinamento acompanhe eventos de entrada

A personalização pode retornar uma classificação muito rapidamente, com a maioria da duração da chamada dedicada à comunicação por meio da API REST. O Azure fará o dimensionamento automático da capacidade de responder rapidamente às solicitações.

##  <a name="low-latency-scenarios"></a>Cenários de baixa latência

Alguns aplicativos exigem latências baixas ao retornar uma classificação. Isto é necessário:

* Para evitar que usuário tenha que esperar um tempo considerável para exibir o conteúdo classificado.
* Para ajudar um servidor que esteja enfrentando tráfego extremo a evitar associar conexões de rede e tempo de computação escassos.

<!--

If your web site is scaled on your infrastructure, you can avoid making HTTP calls by hosting the Personalizer API in your own servers running a Docker container.

This change would be transparent to your application, other than using an endpoint URL referring to the running docker instances as opposed to an online service in the cloud.



### Extreme Low Latency Scenarios

If you require latencies under a millisecond, and have already tested using Personalizer via containers, please contact our support team so we can assess your scenario and provide guidance suited to your needs.

-->

## <a name="scalability-and-training-throughput"></a>Taxa de transferência de treinamento e escalabilidade

O Personalizador funciona atualizando um modelo que é retreinado com base em mensagens enviados de forma assíncrona pelo Personalizador após as APIs de Classificação e Recompensa. Essas mensagens são enviadas usando um Hub de Eventos do Azure para o aplicativo.

 É improvável que a maioria dos aplicativos atinjam a taxa de transferência máxima de treinamento e de associação do Personalizador. Embora atingir este máximo não torne o aplicativo mais lento, isso significa que as filas no Hub de Eventos serão preenchidas com mais rapidez do que elas podem ser limpas.

## <a name="how-to-estimate-your-throughput-requirements"></a>Como estimar seus requisitos de taxa de transferência

* Estime o número médio de bytes por evento de classificação adicionando os comprimentos dos documentos JSON de contexto e de ação.
* Divida 20 MB/seg pela média estimada de bytes.

Por exemplo, se sua carga média tem 500 recursos e cada um tem em média 20 caracteres, então cada evento tem aproximadamente 10 kb. Com essas estimativas, 20.000.000/10.000 = 2.000 eventos/s, o que representa cerca de 173 milhões de eventos/dia. 

Se você está atingindo esses limites, entre em contato com nossa equipe de suporte para obter orientações sobre arquitetura.

## <a name="next-steps"></a>Próximas etapas

[Criar e configurar o Personalizador](how-to-settings.md).