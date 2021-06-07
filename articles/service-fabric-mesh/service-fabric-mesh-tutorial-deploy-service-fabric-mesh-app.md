---
title: Tutorial – Implantar um aplicativo da Malha do Service Fabric
description: Saiba como usar o Visual Studio para publicar um aplicativo da Malha do Azure Service Fabric que consiste em um site do ASP.NET Core que se comunica com um serviço Web de back-end.
author: georgewallace
ms.topic: tutorial
ms.date: 09/18/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: 713f8dcb3d3b3d30fecbea4bb6b50cc4e47d451d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102216744"
---
# <a name="tutorial-deploy-a-service-fabric-mesh-application"></a>Tutorial: Implantar um aplicativo de Malha do Service Fabric

> [!IMPORTANT]
> A versão prévia da Malha do Azure Service Fabric foi desativada. Não serão mais permitidas novas implantações por meio da API da Malha do Service Fabric. O suporte para as implantações existentes continuará até 28 de abril de 2021.
> 
> Para obter detalhes, confira [Desativação da versão prévia da Malha do Azure Service Fabric](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Este tutorial é a terceira parte de uma série e mostra como publicar um aplicativo Web de Malha do Azure Service Fabric diretamente do Visual Studio.

Neste tutorial, você aprenderá a:
> [!div class="checklist"]
> * Publicar o aplicativo no Azure usando o Visual Studio.
> * Verificar o status da implantação do aplicativo
> * Ver todos os aplicativos atualmente implantados em sua assinatura

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * [Criar um aplicativo da Malha do Service Fabric no Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Depurar um aplicativo da Malha do Service Fabric em execução no cluster de desenvolvimento local](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * Implantar um aplicativo da Malha do Service Fabric
> * [Atualizar um aplicativo da Malha do Service Fabric](service-fabric-mesh-tutorial-upgrade.md)
> * [Limpar os recursos da Malha do Service Fabric](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* [Configure o ambiente de desenvolvimento](service-fabric-mesh-howto-setup-developer-environment-sdk.md), o que inclui a instalação do runtime do Service Fabric, do SDK, do Docker e do Visual Studio 2017.

## <a name="download-the-to-do-sample-application"></a>Faça o download do aplicativo de exemplo de tarefas pendentes

Se você não tiver compilado o aplicativo de exemplo de tarefas pendentes na [parte dois desta série de tutoriais](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md), faça o download do arquivo. Em uma janela de comando, execute o comando a seguir para clonar o repositório de aplicativos de exemplo no computador local.

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

O aplicativo está no diretório `src\todolistapp`.

## <a name="publish-to-azure"></a>Publicar no Azure

Para publicar o projeto da Malha do Service Fabric no Azure, clique com o botão direito do mouse em **todolistapp** no Visual Studio e selecione **Publicar...**

A seguir, você verá uma caixa de diálogo **Publicar aplicativo do Service Fabric**.

![Caixa de diálogo de publicação de Malha do Service Fabric do Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Escolha a conta e assinatura do Azure. Escolha um **Local**. Este artigo usa **Leste dos EUA**.

Em **Grupo de recursos**, selecione **\<Create New Resource Group...>** . Uma caixa de diálogo é exibida no local onde você irá criar um novo grupo de recursos. Este artigo usa o local **Leste dos EUA** e nomeia o grupo **sfmeshTutorial1RG** (se sua organização tiver várias pessoas usando a mesma assinatura, escolha um nome de grupo exclusivo).  Pressione **Criar** para criar o grupo de recursos e retornar à caixa de diálogo de publicação.

![Caixa de diálogo do novo grupo de recursos de Malha do Service Fabric do Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-resource-group-dialog.png)

De volta à caixa de diálogo **Publicar Aplicativo do Service Fabric**, em **Registro de Contêiner do Azure**, selecione **\<Create New Container Registry...>** . Na caixa de diálogo **Criar Registro de Contêiner**, use um nome exclusivo para o **Nome do registro de contêiner**. Especifique um **Local** (este tutorial usa **Leste dos EUA**). Escolha o **Grupo de recursos** que você criou na etapa anterior na lista suspensa, por exemplo, **sfmeshTutorial1RG**. Defina a **SKU** para **Básico** e pressione **Criar** para criar o Registro de Contêiner do Azure privado e retornar à caixa de diálogo Publicar.

![Caixa de diálogo do novo registro de contêiner da Malha do Service Fabric do Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-container-registry-dialog.png)

Se receber um erro informando que um provedor de recursos não foi registrado para a assinatura, você poderá registrá-lo. Primeiro, verifique se o provedor de recursos está disponível para a assinatura:

```Powershell
Get-AzureRmResourceProvider -ListAvailable
```

Se o provedor de registro do contêiner (`Microsoft.ContainerRegistry`) estiver disponível, registre-o no Powershell:

```Powershell
Connect-AzureRmAccount
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ContainerRegistry
```

Na caixa de diálogo de publicação, pressione o botão **Publicar** para implantar o aplicativo do Service Fabric no Azure.

Ao publicar no Azure pela primeira vez, a imagem do docker é enviada para o ACR (Registro de Contêiner do Azure) e isso demora, dependendo do tamanho da imagem. Publicações subsequentes do mesmo projeto serão mais rápidas. É possível monitorar o andamento da implantação escolhendo o painel **Ferramentas do Service Fabric** na janela **Saída** do Visual Studio. Quando a implantação for concluída, a saída **Ferramentas do Service Fabric** exibirá o endereço IP e a porta do aplicativo na forma de uma URL.

```
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMeshApp\ToDoService\bin\Any CPU\Release\netcoreapp2.0\ToDoService.dll
Uploading the images to Azure Container Registry...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Abra um navegador da Web e navegue até a URL para ver o site em execução no Azure.

## <a name="set-up-service-fabric-mesh-cli"></a>Configurar CLI da Malha do Service Fabric

Você pode usar o Azure Cloud Shell ou uma instalação local da CLI do Azure para concluir as etapas restantes. Instale o módulo de extensão de CLI da Malha do Azure Service Fabric seguindo estas [instruções](service-fabric-mesh-howto-setup-cli.md).

## <a name="check-application-deployment-status"></a>Verificar o status da implantação do aplicativo

Neste ponto, seu aplicativo já terá sido implantado. Você pode verificar o status usando o comando `app show`. 

O nome do aplicativo para o aplicativo do tutorial é `todolistapp`. Reúna os detalhes sobre o aplicativo com o seguinte comando:

```azurecli-interactive
az mesh app show --resource-group $rg --name todolistapp
```

## <a name="get-the-ip-address-of-your-deployment"></a>Obter o endereço IP da sua implantação

Se desejar obter o endereço IP do aplicativo, use o seguinte comando:
  
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --name todolistappGateway
```

## <a name="see-all-applications-currently-deployed-to-your-subscription"></a>Ver todos os aplicativos atualmente implantados em sua assinatura

É possível usar o comando "lista de aplicativos" para obter uma lista dos aplicativos implantados em sua assinatura.

```azurecli-interactive
az mesh app list --output table
```

## <a name="next-steps"></a>Próximas etapas

Nesta parte do tutorial, você aprendeu a:
> [!div class="checklist"]
> * Publicar o aplicativo no Azure.
> * Verificar o status da implantação do aplicativo
> * Ver todos os aplicativos atualmente implantados em sua assinatura

Prosseguir para o próximo tutorial:
> [!div class="nextstepaction"]
> [Atualizar um aplicativo da Malha do Service Fabric](service-fabric-mesh-tutorial-upgrade.md)

[azure-cli-install]: /cli/azure/install-azure-cli
