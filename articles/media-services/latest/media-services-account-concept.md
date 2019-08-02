---
title: Gerenciar contas do Azure Media Services v3 | Microsoft Docs
description: Para iniciar o gerenciamento, a criptografia, a codificação, a análise e a transmissão de conteúdo de mídia no Azure, você precisa criar uma conta nos Serviços de Mídia. Este artigo explica como gerenciar contas do Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: fa9720c2c29af184016d2903e60520e701b4cf79
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670677"
---
# <a name="manage-azure-media-services-v3-accounts"></a>Gerenciar contas do Azure Media Services v3

Para iniciar o gerenciamento, a criptografia, a codificação, a análise e a transmissão de conteúdo de mídia no Azure, você precisa criar uma conta nos Serviços de Mídia. Ao criar uma conta dos Serviços de Mídia, você precisa fornecer o nome de um recurso de conta de Armazenamento do Azure. A conta de armazenamento especificada está conectada à sua conta de Serviços de Mídia. A conta dos Serviços de Mídia e todas as contas de armazenamento associadas precisam estar na mesma assinatura do Azure. Para obter mais informações, consulte [contas de armazenamento](storage-account-concept.md).

## <a name="moving-a-media-services-account-between-subscriptions"></a>Uma conta de serviços de mídia a movimentação entre assinaturas 

Se você precisar mover uma conta de serviços de mídia para uma nova assinatura, você precisa primeiro mova o grupo de recursos inteiro que contém a conta de serviços de mídia para a nova assinatura. Você deve mover todos os recursos anexados: Contas de armazenamento do Azure, perfis de CDN do Azure, etc. Para saber mais, confira [Mover recursos para um novo grupo de recursos ou assinatura](../../azure-resource-manager/resource-group-move-resources.md). Assim como acontece com todos os recursos no Azure, move de grupo de recursos pode levar algum tempo para ser concluída.

> [!NOTE]
> Serviços de mídia v3 dá suporte ao modelo de multilocação.

### <a name="considerations"></a>Considerações

* Crie backups de todos os dados em sua conta antes de migrar para uma assinatura diferente.
* Você precisará parar todos o Streaming de pontos de extremidade e recursos de transmissão ao vivo. Os usuários não poderão acessar seu conteúdo durante a movimentação do grupo de recursos. 

> [!IMPORTANT]
> Não inicie o ponto de extremidade de Streaming até que a migração seja concluída com êxito.

### <a name="troubleshoot"></a>Solução de problemas 

Se uma conta de serviços de mídia ou uma conta de armazenamento do Azure associada tornam-se "desconectada" seguindo a movimentação do grupo de recursos, tente a rotação das chaves de conta de armazenamento. Se a rotação das chaves de conta de armazenamento não resolver o status "desconectado" da conta de serviços de mídia, uma nova solicitação de suporte do arquivo de "Suporte + solução de problemas" menu na conta de serviços de mídia.  

## <a name="next-steps"></a>Próximas etapas

[Criar uma conta](create-account-cli-quickstart.md)
