---
title: Usar a CLI do Azure para criar um aplicativo do Azure AD e configurá-lo para acessar a API dos Serviços de Mídia do Azure | Microsoft Docs
description: Este tópico mostra como usar a CLI do Azure para criar um aplicativo do Azure AD e configurá-lo para acessar a API dos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 6517cc8224a1a4e2e3319fde52fb621e4f7ab564
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103016926"
---
# <a name="use-azure-cli-to-create-an-azure-ad-app-and-configure-it-to-access-media-services-api"></a>Usar a CLI do Azure para criar um aplicativo do Azure AD e configurá-lo para acessar a API dos Serviços de Mídia do Azure

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Não estão sendo adicionados novos recursos ou funcionalidades aos Serviços de Mídia v2. <br/>Confira a versão mais recente, [Serviços de Mídia v3](../latest/index.yml). Além disso, consulte s [diretrizes de migração da v2 para a v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Este tópico mostra como usar a CLI do Azure para criar um aplicativo do Azure AD (Azure Active Directory) e uma entidade de serviço para acessar os recursos dos Serviços de Mídia do Azure. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Para obter detalhes, consulte [avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Uma conta dos Serviços de Mídia. Para obter mais informações, consulte [Criar uma conta dos Serviços de Mídia do Azure usando o portal do Azure](media-services-portal-create-account.md).

## <a name="use-the-azure-cloud-shell"></a>Usar o Azure Cloud Shell

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Inicie o Cloud Shell no painel de navegação superior do portal.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

Para obter mais informações, consulte [Visão geral do Azure Cloud Shell](../../cloud-shell/overview.md).

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-azure-cli"></a>Criar um aplicativo do Azure AD e configurar o acesso à conta de mídia com a CLI do Azure
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> 
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

Por exemplo:

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

Neste exemplo, o **escopo** é o caminho de recurso completo para a conta dos serviços de mídia. No entanto, o **escopo** pode estar em qualquer nível.

Por exemplo, ele pode estar em um dos seguintes níveis:
 
* O nível de **assinatura**.
* O nível de **grupo de recursos**.
* O nível de **recurso** (por exemplo, uma conta de Mídia).

Para obter mais informações, confira [Criar uma entidade de serviço do Azure com a CLI do Azure](/cli/azure/create-an-azure-service-principal-azure-cli)

Consulte também [Adicionar ou remover atribuições de função do Azure usando CLI do Azure](../../role-based-access-control/role-assignments-cli.md). 

## <a name="next-steps"></a>Próximas etapas

Comece a [carregar arquivos para sua conta](media-services-portal-upload-files.md).
