---
title: Tutorial – Atualizar um aplicativo em execução na Malha do Azure Service Fabric
description: Neste tutorial, você aprenderá como atualizar um aplicativo do Service Fabric em execução na Malha do Service Fabric.
author: georgewallace
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: 8a71e854f03bee75b757e0a0aa02e7aa2c24469b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99626554"
---
# <a name="tutorial-upgrade-a-service-fabric-application-running-in-service-fabric-mesh"></a>Tutorial: Atualizar um aplicativo do Service Fabric em execução na Malha do Service Fabric

> [!IMPORTANT]
> A versão prévia da Malha do Azure Service Fabric foi desativada. Não serão mais permitidas novas implantações por meio da API da Malha do Service Fabric. O suporte para as implantações existentes continuará até 28 de abril de 2021.
> 
> Para obter detalhes, confira [Desativação da versão prévia da Malha do Azure Service Fabric](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Este tutorial é a parte três de uma série. Você aprenderá a atualizar um aplicativo do Service Fabric que estava [implantado anteriormente na Malha do Service Fabric](service-fabric-mesh-tutorial-template-deploy-app.md) aumentando os recursos de CPU alocados.  Quando terminar, você terá um serviço de front-end da Web funcionando com mais recursos de CPU.

Na terceira parte da série, você aprenderá a:

> [!div class="checklist"]
> * Alterar as configurações do aplicativo
> * Atualizar um aplicativo em execução na Malha do Service Fabric

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * [Implantar um aplicativo na Malha do Service Fabric usando um modelo](service-fabric-mesh-tutorial-template-deploy-app.md)
> * [Dimensionar um aplicativo em execução na Malha do Service Fabric](service-fabric-mesh-tutorial-template-scale-services.md)
> * Atualizar um aplicativo em execução na Malha do Service Fabric
> * [Remover um aplicativo](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Prerequisites

Antes de começar este tutorial:

* Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Abra o [Azure Cloud Shell](service-fabric-mesh-howto-setup-cli.md) ou [instale a CLI do Azure e a CLI da Malha do Service Fabric localmente](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli).

## <a name="upgrade-application-configurations"></a>Atualizar as configurações de aplicativo

Uma das principais vantagens da implantação de aplicativos na Malha do Service Fabric é a capacidade de atualizar facilmente a configuração de aplicativo.  Por exemplo, os recursos de CPU ou memória dos serviços.

Este tutorial usa uma lista de tarefas como exemplo, que [já foi implantada](service-fabric-mesh-tutorial-template-deploy-app.md) e agora deve estar em execução. O aplicativo tem dois serviços: WebFrontEnd e ToDoService. Cada serviço foi implantado inicialmente com recursos de CPU iguais a 0,5.  Para exibir os recursos de CPU do serviço WebFrontEnd, execute o seguinte:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp
```

No modelo de implantação do recurso de aplicativo, cada serviço tem uma propriedade *cpu* que pode ser usada para definir os recursos de CPU solicitados. Um aplicativo pode consistir em vários serviços, cada serviço com uma única configuração de *cpu*, que são implantados e gerenciados em conjunto. Para aumentar os recursos de CPU do serviço de front-end da Web, modifique o valor de *cpue* no modelo de implantação ou no arquivo de parâmetros.  Em seguida, atualize o aplicativo.

### <a name="modify-the-deployment-template-parameters"></a>Modificar os parâmetros do modelo de implantação

Quando há valores no modelo que você pretende alterar após a implantação do aplicativo ou se você deseja ter a opção de alterar a cada implantação (se você planeja reutilizar esse modelo para outras implantações), a prática recomendada é parametrizar os valores.

O aplicativo foi implantado previamente usando os arquivos [de modelo de implantação mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) e [de parâmetros mesh_rp.windows.parameter.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json).

Abra o arquivo [de parâmetros mesh_rp.windows.parameter.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) local e defina o valor de *frontEndCpu* como 1:

```json
      "frontEndCpu":{
        "value": "1"
      }
```

Salve suas alterações no arquivo de parâmetros.  

O parâmetro *frontEndCpu* é declarado na seção *parameters* do [modelo de implantação mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json):

```json
"frontEndCpu": {
    "defaultValue": "0.5",
    "type": "string",
    "metadata": {
        "description": "The CPU resources for the front end web service."
    }
}
```

A propriedade *codePackages->resources->requests->cpu* do serviço WebFrontEnd referencia o parâmetro *frontEndCpu*:

```json
    "services": [
          {
            "name": "WebFrontEnd",
            "properties": {
              "description": "WebFrontEnd description.",
              "osType": "Windows",
              "codePackages": [
                {
                  "name": "WebFrontEnd",
                  "image": "[parameters('frontEndImage')]",
                  ...
                  "resources": {
                    "requests": {
                      "cpu": "[parameters('frontEndCpu')]",
                      "memoryInGB": "[parameters('frontEndMemory')]"
                    }
                  },
                  "imageRegistryCredential": {
                    "server": "[parameters('registryServer')]",
                    "username": "[parameters('registryUserName')]",
                    "password": "[parameters('registryPassword')]"
                  }
                }
              ],
              ...
```

### <a name="upgrade-your-application"></a>Atualizar seu aplicativo

Enquanto o aplicativo estiver em execução, você poderá atualizá-lo reimplantando o modelo e o arquivo de parâmetros atualizado:

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

Isso iniciará uma atualização sem interrupção do aplicativo, e você verá como os recursos de CPU aumentarão em alguns minutos.  Para exibir os recursos de CPU do serviço WebFrontEnd, execute o seguinte:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp
```

## <a name="next-steps"></a>Próximas etapas

Nesta parte do tutorial, você aprendeu a:

> [!div class="checklist"]
> * Alterar as configurações do aplicativo
> * Atualizar um aplicativo em execução na Malha do Service Fabric

Prosseguir para o próximo tutorial:
> [!div class="nextstepaction"]
> [Remover um aplicativo da Malha do Service Fabric](service-fabric-mesh-tutorial-template-remove-app.md)
