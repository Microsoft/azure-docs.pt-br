---
title: Usar a CLI do Azure para criar um aplicativo do Azure AD e configurá-lo para acessar a API dos Serviços de Mídia do Azure | Microsoft Docs
description: Este tópico mostra como usar a CLI do Azure para criar um aplicativo do Azure AD e configurá-lo para acessar a API dos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 93a77c64e82c39f8f967a3549fafecfae59b194b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465722"
---
# <a name="use-azure-cli-to-create-an-azure-ad-app-and-configure-it-to-access-media-services-api"></a>Usar a CLI do Azure para criar um aplicativo do Azure AD e configurá-lo para acessar a API dos Serviços de Mídia do Azure 

Este tópico mostra como usar a CLI do Azure para criar um aplicativo do Azure AD (Azure Active Directory) e uma entidade de serviço para acessar os recursos dos Serviços de Mídia do Azure. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Para obter detalhes, confira [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Uma conta dos Serviços de Mídia. Para obter mais informações, consulte [Criar uma conta dos Serviços de Mídia do Azure usando o portal do Azure](media-services-portal-create-account.md).

## <a name="use-the-azure-cloud-shell"></a>Usar o Azure Cloud Shell

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Inicie o Cloud Shell no painel de navegação superior do portal.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

Para obter mais informações, consulte [Visão geral do Azure Cloud Shell](../../cloud-shell/overview.md).

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-azure-cli"></a>Criar um aplicativo do Azure AD e configurar o acesso à conta de mídia com a CLI do Azure
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> --password <strong password>
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

Para obter mais informações, confira [Criar uma entidade de serviço do Azure com a CLI do Azure](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)

Consulte também [Gerenciar o Controle de Acesso Baseado em Função com a interface de linha de comando do Azure](../../role-based-access-control/role-assignments-cli.md). 

## <a name="next-steps"></a>Próximas etapas

Comece a [carregar arquivos para sua conta](media-services-portal-upload-files.md).
