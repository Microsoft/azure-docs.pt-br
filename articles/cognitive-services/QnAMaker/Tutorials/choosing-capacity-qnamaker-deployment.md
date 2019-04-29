---
title: Capacidade de recursos para implantação – QnA Maker
titleSuffix: Azure Cognitive Services
description: Antes de criar seu serviço QnA Maker, é preciso decidir qual camada dos serviços acima são adequadas para você.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/24/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: a332d263526bb6507e7394c205caa1c4d1f9e3e6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60613265"
---
# <a name="choosing-capacity-for-your-qna-maker-deployment"></a>Escolher a capacidade para sua implantação do QnA Maker

O serviço QnA Maker depende de três recursos do Azure:
1.  Serviço de Aplicativo (para o tempo de execução)
2.  Azure Search (para armazenar QnAs)
3.  App Insights (opcional, para armazenar telemetria e logs de chat)

Antes de criar seu serviço QnA Maker, é preciso decidir qual camada dos serviços acima são adequadas para você. 

Normalmente, há três parâmetros que você precisa considerar:

1. **A taxa de transferência que você precisa do serviço**: selecione o [Plano de aplicativo](https://azure.microsoft.com/pricing/details/app-service/plans/) apropriado para o serviço de aplicativo com base em suas necessidades. Você pode [escalar verticalmente](https://docs.microsoft.com/azure/app-service/web-sites-scale) ou reduzir verticalmente o Aplicativo. Isso também deve influenciar a seleção da SKU do Azure Search, veja mais detalhes [aqui](https://docs.microsoft.com/azure/search/search-sku-tier).

1. **Tamanho e número de bases de dados de conhecimento**: Escolha o [SKU do Azure Search](https://azure.microsoft.com/pricing/details/search/) apropriado para seu cenário. Você pode publicar as bases de dados de conhecimento N-1 em uma camada específica, onde N representa os índices máximos permitidos na camada. Verifique também o tamanho máximo e o número de documentos permitidas por camada.

    Por exemplo, se a camada tiver 15 índices permitidos, você poderá publicar 14 bases de dados de conhecimento (1 índice por base de dados de conhecimento publicada). O décimo quinto índice é usado para todas as bases de dados de conhecimento para criação e teste. 

1. **Número de documentos como fontes**: a SKU gratuita do serviço de gerenciamento do QnA Maker limita o número de documentos que você pode gerenciar por meio do portal e as APIs a três (cada uma com 1 MB de tamanho). O padrão de SKU não tem limites para o número de documentos que você pode gerenciar. Veja mais detalhes [aqui](https://aka.ms/qnamaker-pricing).

A tabela a seguir fornece algumas diretrizes de alto nível.

|                        | Gerenciamento do QnA Maker | Serviço de Aplicativo | Azure Search | Limitações                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Experimentação        | SKU gratuito             | Camada Gratuita   | Camada Gratuita    | Publicar até 2 KB/s, tamanho de 50 MB  |
| Ambiente de Desenvolvimento/Teste   | SKU Standard         | Compartilhado      | Basic        | Publicar até 14 KBs, com tamanho de 2 GB    |
| Ambiente de Produção | SKU Standard         | Basic       | Standard     | Publicar até 49 KBs, tamanho de 25 GB |

Para fazer upgrade de sua pilha do QnA Maker, veja [Fazer upgrade do seu serviço QnA Maker](../How-To/upgrade-qnamaker-service.md).

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Fazer upgrade do seu serviço QnA Maker](../How-To/upgrade-qnamaker-service.md)
