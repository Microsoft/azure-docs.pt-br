---
title: Sincronizar o conteúdo de uma pasta de nuvem
description: Saiba como implantar seu aplicativo no serviço de Azure App por meio da sincronização de conteúdo de uma pasta de nuvem, incluindo o OneDrive ou o dropbox.
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.topic: article
ms.date: 02/25/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: bfee320c7a8b4cbe8439c376350d1234b393bfb5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102051201"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Sincronizar o conteúdo de uma pasta de nuvem para o Serviço de Aplicativo do Azure
Este artigo mostra como sincronizar seu conteúdo do Dropbox e OneDrive com o [Serviço de Aplicativo do Azure](./overview.md). 

Com a abordagem de sincronização de conteúdo, seu trabalho com o código do aplicativo e o conteúdo em uma pasta de nuvem designada para certificar-se de que ele está em um estado pronto para implantar e, em seguida, sincronizar com o serviço de aplicativo com o clique de um botão. 

Devido a diferenças subjacentes nas APIs, o **OneDrive for Business** não tem suporte no momento.

> [!NOTE]
> A página do **centro de desenvolvimento (clássico)** na portal do Azure, que é a antiga experiência de implantação, será preterida em março de 2021. Essa alteração não afetará nenhuma configuração de implantação existente em seu aplicativo e você poderá continuar a gerenciar a implantação do aplicativo na página do **centro de implantação** .

## <a name="enable-content-sync-deployment"></a>Habilitar a implantação da sincronização de conteúdo

1. Na [portal do Azure](https://portal.azure.com), navegue até a página de gerenciamento do aplicativo do serviço de aplicativo.

1. No menu à esquerda, clique em configurações da **central de implantação**  >  . 

1. Em **origem**, selecione **onedrive** ou **Dropbox**.

1. Clique em **autorizar** e siga os prompts de autorização. 

    ![Mostra como autorizar o OneDrive ou o Dropbox no centro de implantação no portal do Azure.](media/app-service-deploy-content-sync/choose-source.png)

    Você só precisa autorizar com o OneDrive ou o Dropbox uma vez para sua conta do Azure. Para autorizar uma conta diferente do OneDrive ou do Dropbox para um aplicativo, clique em **alterar conta**.

1. Em **pasta**, selecione a pasta a ser sincronizada. Esta pasta é criada no seguinte caminho de conteúdo designado no OneDrive ou Dropbox. 
   
    * **Onedrive**: `Apps\Azure Web Apps`
    * **Dropbox**: `Apps\Azure`
    
1. Clique em **Save** (Salvar).

## <a name="synchronize-content"></a>Sincronizar o conteúdo

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. Na [portal do Azure](https://portal.azure.com), navegue até a página de gerenciamento do aplicativo do serviço de aplicativo.

1. No menu à esquerda, clique em  >  **reimplantar/sincronizar** da central de implantação. 

    ![Mostra como sincronizar sua pasta de nuvem com o serviço de aplicativo.](media/app-service-deploy-content-sync/synchronize.png)
   
1. Clique em **OK** para confirmar a sincronização.

# <a name="azure-cli"></a>[CLI do Azure](#tab/cli)

Inicie uma sincronização executando o seguinte comando e substituindo \<group-name> e \<app-name> :

```azurecli-interactive
az webapp deployment source sync –-resource-group <group-name> –-name <app-name>
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Inicie uma sincronização executando o seguinte comando e substituindo \<group-name> e \<app-name> :

```azurepowershell-interactive
Invoke-AzureRmResourceAction -ResourceGroupName <group-name> -ResourceType Microsoft.Web/sites -ResourceName <app-name> -Action sync -ApiVersion 2019-08-01 -Force –Verbose
```

-----

## <a name="disable-content-sync-deployment"></a>Desabilitar a implantação da sincronização de conteúdo

1. Na [portal do Azure](https://portal.azure.com), navegue até a página de gerenciamento do aplicativo do serviço de aplicativo.

1. No menu à esquerda, clique em configurações da **central de implantação**  >    >  **Desconectar**. 

    ![Mostra como desconectar a sincronização da pasta de nuvem com seu aplicativo do serviço de aplicativo no portal do Azure.](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Implantação por meio do repositório Git local](deploy-local-git.md)