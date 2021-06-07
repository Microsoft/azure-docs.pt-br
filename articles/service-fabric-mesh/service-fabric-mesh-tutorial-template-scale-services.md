---
title: Tutorial – Dimensionar um aplicativo em execução na Malha do Azure Service Fabric
description: Neste tutorial, você aprenderá como escalar os serviços em um aplicativo em execução na Malha do Service Fabric.
author: georgewallace
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: 02dc5d43a23c572d441da2bbb7386885bf66ece7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625376"
---
# <a name="tutorial-scale-an-application-running-in-service-fabric-mesh"></a>Tutorial: Dimensionar um aplicativo em execução na Malha do Service Fabric

> [!IMPORTANT]
> A versão prévia da Malha do Azure Service Fabric foi desativada. Não serão mais permitidas novas implantações por meio da API da Malha do Service Fabric. O suporte para as implantações existentes continuará até 28 de abril de 2021.
> 
> Para obter detalhes, confira [Desativação da versão prévia da Malha do Azure Service Fabric](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Este tutorial é a parte dois de uma série. Saiba como dimensionar manualmente o número de instâncias de serviço de um aplicativo que foi [implantado anteriormente na Malha do Service Fabric](service-fabric-mesh-tutorial-template-deploy-app.md). Quando tiver terminado, você terá um serviço de front-end com três instâncias em execução e um serviço de dados com duas instâncias em execução.

Na segunda parte da série, você aprenderá como:

> [!div class="checklist"]
> * Configurar o número desejado de instâncias de serviço
> * Executar uma atualização

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * [Implantar um aplicativo na Malha do Service Fabric usando um modelo](service-fabric-mesh-tutorial-template-deploy-app.md)
> * Dimensionar um aplicativo em execução na Malha do Service Fabric
> * [Atualizar um aplicativo em execução na Malha do Service Fabric](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * [Remover um aplicativo](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Prerequisites

Antes de começar este tutorial:

* Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* [Instale a CLI do Azure e a CLI da Malha do Service Fabric localmente](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli).

## <a name="manually-scale-your-services-in-or-out"></a>Dimensionar ou reduzir manualmente seus serviços

Uma das principais vantagens da implantação de aplicativos na Malha do Service Fabric é a capacidade de dimensionar e reduzir seus serviços com facilidade. Isso é usado para manipulação de volumes variáveis de carga nos seus serviços ou para melhorar a disponibilidade.

Este tutorial usa uma lista de tarefas como exemplo, que [já foi implantada](service-fabric-mesh-tutorial-template-deploy-app.md) e agora deve estar em execução. O aplicativo tem dois serviços: WebFrontEnd e ToDoService. Cada serviço foi implantado inicialmente com uma contagem de réplica de 1.  Para exibir o número de réplicas em execução para o serviço WebFrontEnd, execute o seguinte:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp --query "replicaCount"
```

Para exibir o número de réplicas em execução para o serviço ToDoService, execute o seguinte:

```azurecli
az mesh service show --resource-group myResourceGroup --name ToDoService --app-name todolistapp --query "replicaCount"
```

No modelo de implantação para o recurso de aplicativo, cada serviço tem uma propriedade *replicaCount* que pode ser usada para definir o número de vezes que você deseja que esse serviço seja implantado. Um aplicativo pode consistir em vários serviços, cada um deles com um número exclusivo de *replicaCount*, que são implantados e gerenciados em conjunto. Para dimensionar o número de réplicas de serviço, modifique o valor *replicaCount* para cada serviço que você deseja reduzir horizontalmente no modelo de implantação ou no arquivo de parâmetros.  Em seguida, atualize o aplicativo.

### <a name="modify-the-deployment-template-parameters"></a>Modificar os parâmetros do modelo de implantação

Quando há valores no modelo que você pretende alterar após a implantação do aplicativo ou se você deseja ter a opção de alterar a cada implantação (se você planeja reutilizar esse modelo para outras implantações), a prática recomendada é parametrizar os valores.

O aplicativo foi implantado previamente usando os arquivos [de modelo de implantação mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) e [de parâmetros mesh_rp.windows.parameter.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json).

Abra o arquivo [de parâmetros mesh_rp.windows.parameter.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) localmente e defina o valor *frontEndReplicaCount* para 3 e o valor *serviceReplicaCount* para 2:

```json
      "frontEndReplicaCount":{
        "value": "3"
      },
      "serviceReplicaCount":{
        "value": "2"
      }
```

Salve suas alterações no arquivo de parâmetros.  Os parâmetros *frontEndReplicaCount* e *serviceReplicaCount* são declarados na seção *parameters* do [modelo de implantação mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json):

```json
"frontEndReplicaCount":{
      "defaultValue": "1",
      "type": "string"
    },
    "serviceReplicaCount":{
      "defaultValue": "1",
      "type": "string"
    }
```

A propriedade do serviço WebFrontEnd *replicaCount* referencia o parâmetro *frontEndReplicaCount* e a propriedade *replicaCount* do serviço ToDoService referencia o parâmetro *serviceReplicaCount*:

```json
    "services": [
    {
    "name": "WebFrontEnd",
    "properties": {
        "description": "WebFrontEnd description.",
        "osType": "Windows",
        "codePackages": [
        {
            ...
        }
        ],
        "replicaCount": "[parameters('frontEndReplicaCount')]",
        "networkRefs": [
        {
            "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'todolistappNetwork')]"
        }
        ]
    }
    },
    {
    "name": "ToDoService",
    "properties": {
        "description": "ToDoService description.",
        "osType": "Windows",
        "codePackages": [
        {
            ...
        }
        ],
        "replicaCount": "[parameters('serviceReplicaCount')]",
        "networkRefs": [
        {
            "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'todolistappNetwork')]"
        }
        ]
    }
    }
],
```

Depois que seu modelo tiver sido modificado, atualize o aplicativo.

### <a name="upgrade-your-application"></a>Atualizar seu aplicativo

Enquanto o aplicativo estiver em execução, você poderá atualizá-lo reimplantando o modelo e o arquivo de parâmetros atualizado:

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

Isso iniciará uma atualização sem interrupção no aplicativo, e você verá como as instâncias de serviço aumentarão dentro de alguns minutos.  Para exibir o número de réplicas em execução para o serviço WebFrontEnd, execute o seguinte:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp --query "replicaCount"
```

Para exibir o número de réplicas em execução para o serviço ToDoService, execute o seguinte:

```azurecli
az mesh service show --resource-group myResourceGroup --name ToDoService --app-name todolistapp --query "replicaCount"
```

## <a name="next-steps"></a>Próximas etapas

Nesta parte do tutorial, você aprendeu a:

> [!div class="checklist"]
> * Configurar o número desejado de instâncias de serviço
> * Executar uma atualização

Prosseguir para o próximo tutorial:
> [!div class="nextstepaction"]
> [Atualizar um aplicativo em execução na Malha do Service Fabric](service-fabric-mesh-tutorial-template-upgrade-app.md)
