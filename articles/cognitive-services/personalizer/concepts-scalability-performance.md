---
title: Escalabilidade e desempenho-personalizador
titleSuffix: Azure Cognitive Services
description: 'Os aplicativos e os sites de alto desempenho e alto tráfego têm dois principais fatores a considerar com o Personalizador para desempenho e escalabilidade: taxa de transferência de treinamento e latência.'
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/24/2019
ms.openlocfilehash: 52d22fce64a26267e73cb9a1df8614129bb96c3e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87132680"
---
# <a name="scalability-and-performance"></a>Desempenho e escalabilidade

Os aplicativos e os sites de alto desempenho e de alto tráfego têm dois fatores principais a considerar com o Personalizador para desempenho e escalabilidade:

* Como manter a baixa latência ao fazer chamadas à API de Classificação
* Como garantir que a taxa de transferência de treinamento acompanhe eventos de entrada

A personalização pode retornar uma classificação rapidamente, com a maior parte da duração da chamada dedicada à comunicação por meio da API REST. O Azure fará o dimensionamento automático da capacidade de responder rapidamente às solicitações.

##  <a name="low-latency-scenarios"></a>Cenários de baixa latência

Alguns aplicativos exigem latências baixas ao retornar uma classificação. São necessárias latências baixas:

* Para evitar que usuário tenha que esperar um tempo considerável para exibir o conteúdo classificado.
* Para ajudar um servidor que esteja enfrentando tráfego extremo a evitar associar conexões de rede e tempo de computação escassos.


## <a name="scalability-and-training-throughput"></a>Taxa de transferência de treinamento e escalabilidade

O Personalizador funciona atualizando um modelo que é retreinado com base em mensagens enviados de forma assíncrona pelo Personalizador após as APIs de Classificação e Recompensa. Essas mensagens são enviadas usando um Hub de Eventos do Azure para o aplicativo.

 É improvável que a maioria dos aplicativos atinjam a taxa de transferência máxima de treinamento e de associação do Personalizador. Embora atingir este máximo não torne o aplicativo mais lento, isso significa que as filas no Hub de Eventos serão preenchidas com mais rapidez do que elas podem ser limpas.

## <a name="how-to-estimate-your-throughput-requirements"></a>Como estimar seus requisitos de taxa de transferência

* Estime o número médio de bytes por evento de classificação adicionando os comprimentos dos documentos JSON de contexto e de ação.
* Divida 20 MB/seg pela média estimada de bytes.

Por exemplo, se sua carga média tem 500 recursos e cada um tem em média 20 caracteres, então cada evento tem aproximadamente 10 kb. Com essas estimativas, 20.000.000/10.000 = 2.000 eventos/s, o que representa cerca de 173 milhões de eventos/dia. 

Se você está atingindo esses limites, entre em contato com nossa equipe de suporte para obter orientações sobre arquitetura.

## <a name="next-steps"></a>Próximas etapas

[Criar e configurar o personalizador](how-to-settings.md).