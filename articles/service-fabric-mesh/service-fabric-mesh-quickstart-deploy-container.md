---
title: 'Início Rápido: Implantar um aplicativo Olá, Mundo na Malha do Azure Service Fabric'
description: Este início rápido mostra como implantar um aplicativo da Malha do Service Fabric na Malha do Azure Service Fabric.
author: georgewallace
ms.author: gwallace
ms.date: 11/27/2018
ms.topic: quickstart
ms.openlocfilehash: c89cc1972a554cac85ce2a258873f6c810e45167
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102217713"
---
# <a name="quickstart-deploy-hello-world-to-service-fabric-mesh"></a>Início Rápido: Implantar Olá, Mundo na malha do Service Fabric

> [!IMPORTANT]
> A versão prévia da Malha do Azure Service Fabric foi desativada. Não serão mais permitidas novas implantações por meio da API da Malha do Service Fabric. O suporte para as implantações existentes continuará até 28 de abril de 2021.
> 
> Para obter detalhes, confira [Desativação da versão prévia da Malha do Azure Service Fabric](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

A [Malha do Service Fabric](service-fabric-mesh-overview.md) torna mais fácil criar e gerenciar aplicativos de microsserviços no Azure, sem a necessidade de provisionar máquinas virtuais. Neste início rápido, você criará um aplicativo Olá, Mundo no Azure e o exporá à internet. Essa operação é concluída com um único comando. Em apenas alguns minutos, você verá este modo de exibição em seu navegador:

![Aplicativo Olá, Mundo no navegador][sfm-app-browser]

[!INCLUDE [preview note](./includes/include-preview-note.md)]

Se você ainda não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="set-up-service-fabric-mesh-cli"></a>Configurar CLI da Malha do Service Fabric 
Você pode usar o Azure Cloud Shell ou uma instalação local da CLI do Azure para concluir esse guia de início rápido. Instale o módulo de extensão de CLI da Malha do Azure Service Fabric seguindo estas [instruções](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure
Entre no Azure e defina sua assinatura.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>Criar grupo de recursos
Crie um grupo de recursos para implantar o aplicativo. Você pode usar um grupo de recursos existente e ignorar esta etapa. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application"></a>Implantar o aplicativo

>[!NOTE]
> Desde 2 de novembro de 2020, [limites de taxa de download se aplicam](https://docs.docker.com/docker-hub/download-rate-limit/) a solicitações anônimas e autenticadas para o Docker Hub de contas do plano gratuito do Docker e são impostas por endereço IP. 
> 
> Esses modelos fazem uso de imagens públicas do Docker Hub. Observe que você pode ter uma taxa limitada. Para obter mais detalhes, confira [Autenticar-se com o Docker Hub](../container-registry/buffer-gate-public-content.md#authenticate-with-docker-hub).

Criar seu aplicativo no grupo de recursos usando o comando `az mesh deployment create`.  Execute o seguinte:

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.linux.json --parameters "{'location': {'value': 'eastus'}}" 
```

O comando anterior implanta um aplicativo Linux usando o [modelo linux.json](https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.linux.json). Se desejar implantar um aplicativo do Windows, use o [modelo windows.json](https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.windows.json). Imagens de contêiner do Windows são maiores do que as imagens de contêiner do Linux e podem levar mais tempo para implantar.

Este comando produzirá um snippet JSON mostrado abaixo. Na seção ```outputs``` da saída JSON, copie a propriedade ```publicIPAddress```.

```json
"outputs": {
    "publicIPAddress": {
    "type": "String",
    "value": "40.83.78.216"
    }
}
```

Essa informação vem da seção ```outputs``` no modelo ARM. Conforme mostrado abaixo, esta seção referencia o recurso de Gateway para buscar o endereço IP público. 

```json
  "outputs": {
    "publicIPAddress": {
      "value": "[reference('helloWorldGateway').ipAddress]",
      "type": "string"
    }
  }
```

## <a name="open-the-application"></a>Abrir o aplicativo
Depois que o aplicativo é implantado com êxito, copie o endereço IP público para o ponto de extremidade de serviço da saída da CLI. Abra o endereço IP em um navegador da Web. Uma página da Web com o logotipo da Malha do Azure Service Fabric é exibida.

## <a name="check-the-application-details"></a>Verifique os detalhes do aplicativo
Você pode verificar o status do aplicativo usando o comando `az mesh app show`. Esse comando fornece informações úteis que você pode acompanhar.

O nome do aplicativo para este início rápido é `helloWorldApp`, para coletar os detalhes sobre o aplicativo execute o seguinte comando:

```azurecli-interactive
az mesh app show --resource-group myResourceGroup --name helloWorldApp
```

## <a name="see-the-application-logs"></a>Ver os logs de aplicativo

Examine os logs para o aplicativo implantado usando o comando `az mesh code-package-log get`:

```azurecli-interactive
az mesh code-package-log get --resource-group myResourceGroup --application-name helloWorldApp --service-name helloWorldService --replica-name 0 --code-package-name helloWorldCode
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando estiver pronto para excluir o aplicativo, execute o comando [az group delete][az-group-delete] para remover o grupo de recursos e os recursos de rede e aplicativo que ele contém.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como criar e implantar aplicativos da Malha do Service Fabric, continue no tutorial.
> [!div class="nextstepaction"]
> [Criar e implantar um aplicativo Web com vários serviços](service-fabric-mesh-tutorial-create-dotnetcore.md)

<!-- Images -->
[sfm-app-browser]: ./media/service-fabric-mesh-quickstart-deploy-container/HelloWorld.png

<!-- Links / Internal -->
[az-group-delete]: /cli/azure/group
[azure-cli-install]: /cli/azure/install-azure-cli