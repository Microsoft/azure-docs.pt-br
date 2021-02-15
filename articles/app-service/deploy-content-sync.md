---
title: Sincronizar o conteúdo de uma pasta de nuvem
description: Saiba como implantar seu aplicativo no serviço de Azure App por meio da sincronização de conteúdo de uma pasta de nuvem, incluindo o OneDrive ou o dropbox.
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.topic: article
ms.date: 12/03/2018
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 880edff95bb548ec5328c543a542ea5dfcfc362f
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150292"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Sincronizar o conteúdo de uma pasta de nuvem para o Serviço de Aplicativo do Azure
Este artigo mostra como sincronizar seu conteúdo do Dropbox e OneDrive com o [Serviço de Aplicativo do Azure](./overview.md). 

A implantação de sincronização de conteúdo sob demanda é ativada pelo [mecanismo de implantação do Kudu](https://github.com/projectkudu/kudu/wiki) do Serviço de Aplicativo. Você pode trabalhar com o código e o conteúdo do aplicativo em uma pasta de nuvem designada e, em seguida, sincronizar com o serviço de aplicativo com o clique de um botão. Sincronização de conteúdo usa o servidor de build do Kudu. 

## <a name="enable-content-sync-deployment"></a>Habilitar a implantação da sincronização de conteúdo

Para habilitar a sincronização de conteúdo, navegue até a página do aplicativo do Serviço de Aplicativo no [portal do Azure](https://portal.azure.com).

No menu à esquerda, clique em **central de implantação**  >  **onedrive** ou **Dropbox**  >  **autorizar**. Siga as solicitações de autorização. 

![Mostra como autorizar o OneDrive ou o Dropbox no centro de implantação no portal do Azure.](media/app-service-deploy-content-sync/choose-source.png)

Você só precisa autorizar uma vez com o OneDrive ou Dropbox. Se você já tiver autorização, basta clicar em **Continuar**. Você pode alterar a conta autorizada do OneDrive ou Dropbox clicando em **Alterar conta**.

![Mostra como alterar a conta do OneDrive ou Dropbox autorizada no centro de implantação no portal do Azure.](media/app-service-deploy-content-sync/continue.png)

Na página **Configurar**, selecione a pasta que deseja sincronizar. Esta pasta é criada no seguinte caminho de conteúdo designado no OneDrive ou Dropbox. 
   
* **Onedrive**: `Apps\Azure Web Apps`
* **Dropbox**: `Apps\Azure`

Ao terminar, clique em **Continuar**.

Na página **Resumo**, verifique as opções e clique em **Concluir**.

## <a name="synchronize-content"></a>Sincronizar o conteúdo

Se você quiser sincronizar o conteúdo em sua pasta de nuvem com o Serviço de Aplicativo, volte para a página **Centro de Implantação** e clique em **Sincronizar**.

![Mostra como sincronizar sua pasta de nuvem com o serviço de aplicativo.](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > Devido a diferenças subjacentes nas APIs, o **OneDrive for Business** não tem suporte no momento. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>Desabilitar a implantação da sincronização de conteúdo

Para desabilitar a sincronização de conteúdo, navegue até sua página do aplicativo de Serviço de Aplicativo no [portal do Azure](https://portal.azure.com).

No menu à esquerda, clique **Deployment Center**em  >  **Desconectar**da central de implantação.

![Mostra como desconectar a sincronização da pasta de nuvem com seu aplicativo do serviço de aplicativo no portal do Azure.](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Implantação por meio do repositório Git local](deploy-local-git.md)