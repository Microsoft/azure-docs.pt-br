---
title: Gerenciar contas do Azure Media Services v2 | Microsoft Docs
description: Para iniciar o gerenciamento, a criptografia, a codificação, a análise e a transmissão de conteúdo de mídia no Azure, você precisa criar uma conta nos Serviços de Mídia. Este artigo explica como gerenciar contas do Azure Media Services v2.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: a097b186a2287ec13866c8a5ee9420641b44d131
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103015933"
---
# <a name="manage-azure-media-services-v2-accounts"></a>Gerenciar contas do Azure Media Services v2

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Para iniciar o gerenciamento, a criptografia, a codificação, a análise e a transmissão de conteúdo de mídia no Azure, você precisa criar uma conta nos Serviços de Mídia. Ao criar uma conta dos Serviços de Mídia, você precisa fornecer o nome de um recurso de conta de Armazenamento do Azure. A conta de armazenamento especificada está conectada à sua conta de Serviços de Mídia. A conta dos Serviços de Mídia e todas as contas de armazenamento associadas precisam estar na mesma assinatura do Azure.  

## <a name="moving-a-media-services-account-between-subscriptions"></a>Movendo uma conta dos serviços de mídia entre assinaturas

Se precisar mover uma conta dos serviços de mídia para uma nova assinatura, primeiro você precisará mover todo o grupo de recursos que contém a conta dos serviços de mídia para a nova assinatura. Você deve mover todos os recursos anexados: contas de armazenamento do Azure, perfis da CDN do Azure, etc. Para obter mais informações, consulte [mover recursos para um novo grupo de recursos ou assinatura](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Assim como ocorre com qualquer recurso no Azure, as movimentações do grupo de recursos podem levar algum tempo para serem concluídas.

Os serviços de mídia v2 não dão suporte ao modelo de multilocação. Se você precisar mover uma conta dos serviços de mídia para uma assinatura em um novo locatário, crie um novo aplicativo Azure Active Directory (Azure AD) no novo locatário. Em seguida, mova sua conta para a assinatura no novo locatário. Após a conclusão da movimentação do locatário, você pode começar a usar um aplicativo do Azure AD do novo locatário para acessar a conta dos serviços de mídia usando as APIs v2.

> [!IMPORTANT]
> Você precisa redefinir as informações de [autenticação do Azure ad](media-services-portal-get-started-with-aad.md) para acessar a API dos serviços de mídia v2.
  
### <a name="considerations"></a>Considerações

* Crie backups de todos os dados em sua conta antes de migrar para uma assinatura diferente.
* Você precisa interromper todos os pontos de extremidade de streaming e recursos de transmissão ao vivo. Os usuários não poderão acessar o conteúdo pela duração da movimentação do grupo de recursos.

> [!IMPORTANT]
> Não inicie o ponto de extremidade de streaming até que a movimentação seja concluída com êxito.

### <a name="troubleshoot"></a>Solucionar problemas

Se uma conta dos serviços de mídia ou uma conta de armazenamento do Azure associada se tornar "desconectada" após a movimentação do grupo de recursos, tente girar as chaves da conta de armazenamento. Se a rotação das chaves da conta de armazenamento não resolver o status "desconectado" da conta dos serviços de mídia, faça uma nova solicitação de suporte no menu "suporte + solução de problemas" na conta dos serviços de mídia.  

## <a name="next-steps"></a>Próximas etapas

[Criar uma conta](media-services-portal-create-account.md)
