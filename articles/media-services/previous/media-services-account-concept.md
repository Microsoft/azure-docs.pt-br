---
title: Gerenciar contas do serviços de mídia do Azure v2 | Microsoft Docs
description: Para iniciar o gerenciamento, a criptografia, a codificação, a análise e a transmissão de conteúdo de mídia no Azure, você precisa criar uma conta nos Serviços de Mídia. Este artigo explica como gerenciar contas de v2 de serviços de mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: b4c19b1f502d079d7dfcc1edef4674d21f78ac3a
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67622037"
---
# <a name="manage-azure-media-services-v2-accounts"></a>Gerenciar contas de v2 de serviços de mídia do Azure

Para iniciar o gerenciamento, a criptografia, a codificação, a análise e a transmissão de conteúdo de mídia no Azure, você precisa criar uma conta nos Serviços de Mídia. Ao criar uma conta dos Serviços de Mídia, você precisa fornecer o nome de um recurso de conta de Armazenamento do Azure. A conta de armazenamento especificada está conectada à sua conta de Serviços de Mídia. A conta dos Serviços de Mídia e todas as contas de armazenamento associadas precisam estar na mesma assinatura do Azure.  

## <a name="moving-a-media-services-account-between-subscriptions"></a>Uma conta de serviços de mídia a movimentação entre assinaturas 

Se você precisar mover uma conta de serviços de mídia para uma nova assinatura, você precisa primeiro mova o grupo de recursos inteiro que contém a conta de serviços de mídia para a nova assinatura. Você deve mover todos os recursos anexados: Contas de armazenamento do Azure, perfis de CDN do Azure, etc. Para saber mais, confira [Mover recursos para um novo grupo de recursos ou assinatura](../../azure-resource-manager/resource-group-move-resources.md). Assim como acontece com todos os recursos no Azure, move de grupo de recursos pode levar algum tempo para ser concluída.

Os serviços de mídia v2 não oferece suporte a multilocação modelo. Se você precisar mover uma conta de serviços de mídia para uma assinatura em um novo locatário, crie um novo aplicativo do Azure Active Directory (Azure AD) no novo locatário. Mova sua conta para a assinatura no novo locatário. Após a mudança de locatário, você pode começar usando um aplicativo do Azure AD do novo locatário para acessar a conta de serviços de mídia usando as APIs de v2. 

> [!IMPORTANT]
> Você precisará redefinir as [autenticação do Azure AD](media-services-portal-get-started-with-aad.md) informações para acessar os serviços de mídia v2 API.  
### <a name="considerations"></a>Considerações

* Crie backups de todos os dados em sua conta antes de migrar para uma assinatura diferente.
* Você precisará parar todos o Streaming de pontos de extremidade e recursos de transmissão ao vivo. Os usuários não poderão acessar seu conteúdo durante a movimentação do grupo de recursos. 

> [!IMPORTANT]
> Não inicie o ponto de extremidade de Streaming até que a migração seja concluída com êxito.

### <a name="troubleshoot"></a>Solução de problemas 

Se uma conta de serviços de mídia ou uma conta de armazenamento do Azure associada tornam-se "desconectada" seguindo a movimentação do grupo de recursos, tente a rotação das chaves de conta de armazenamento. Se a rotação das chaves de conta de armazenamento não resolver o status "desconectado" da conta de serviços de mídia, uma nova solicitação de suporte do arquivo de "Suporte + solução de problemas" menu na conta de serviços de mídia.  
 
## <a name="next-steps"></a>Próximas etapas

[Criar uma conta](media-services-portal-create-account.md)
