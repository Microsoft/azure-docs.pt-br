---
title: Gerenciar contas dos serviços de mídia do Azure v3
description: Para iniciar o gerenciamento, a criptografia, a codificação, a análise e a transmissão de conteúdo de mídia no Azure, você precisa criar uma conta nos Serviços de Mídia. Este artigo explica como gerenciar contas dos serviços de mídia do Azure v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: inhenkel
ms.openlocfilehash: 49cdee15923123ced03c2c6bc750e1b98dd42887
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896369"
---
# <a name="manage-azure-media-services-v3-accounts"></a>Gerenciar contas dos serviços de mídia do Azure v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Para iniciar o gerenciamento, a criptografia, a codificação, a análise e a transmissão de conteúdo de mídia no Azure, você precisa criar uma conta nos Serviços de Mídia. Ao criar uma conta dos Serviços de Mídia, você precisa fornecer o nome de um recurso de conta de Armazenamento do Azure. A conta de armazenamento especificada está conectada à sua conta de Serviços de Mídia. A conta dos Serviços de Mídia e todas as contas de armazenamento associadas precisam estar na mesma assinatura do Azure. Para obter mais informações, consulte [contas de armazenamento](storage-account-concept.md).

[!INCLUDE [account creation note](./includes/note-2020-05-01-account-creation.md)]

## <a name="moving-a-media-services-account-between-subscriptions"></a>Movendo uma conta dos serviços de mídia entre assinaturas 

Se precisar mover uma conta dos serviços de mídia para uma nova assinatura, primeiro você precisará mover todo o grupo de recursos que contém a conta dos serviços de mídia para a nova assinatura. Você deve mover todos os recursos anexados: contas de armazenamento do Azure, perfis da CDN do Azure, etc. Para obter mais informações, consulte [mover recursos para um novo grupo de recursos ou assinatura](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Assim como ocorre com qualquer recurso no Azure, as movimentações do grupo de recursos podem levar algum tempo para serem concluídas.

> [!NOTE]
> Os serviços de mídia v3 oferecem suporte ao modelo de multilocação.

### <a name="considerations"></a>Considerações

* Crie backups de todos os dados em sua conta antes de migrar para uma assinatura diferente.
* Você precisa interromper todos os pontos de extremidade de streaming e recursos de transmissão ao vivo. Os usuários não poderão acessar o conteúdo pela duração da movimentação do grupo de recursos. 

> [!IMPORTANT]
> Não inicie o ponto de extremidade de streaming até que a movimentação seja concluída com êxito.

### <a name="troubleshoot"></a>Solucionar problemas

Se uma conta dos serviços de mídia ou uma conta de armazenamento do Azure associada se tornar "desconectada" após a movimentação do grupo de recursos, tente girar as chaves da conta de armazenamento. Se a rotação das chaves da conta de armazenamento não resolver o status "desconectado" da conta dos serviços de mídia, faça uma nova solicitação de suporte no menu "suporte + solução de problemas" na conta dos serviços de mídia.  

## <a name="next-steps"></a>Próximas etapas

[Criar uma conta](./create-account-howto.md)
