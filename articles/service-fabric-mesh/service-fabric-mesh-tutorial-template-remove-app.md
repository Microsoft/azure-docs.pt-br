---
title: Tutorial – Remover um aplicativo em execução na Malha do Azure Service Fabric
description: Neste tutorial, saiba como remover um aplicativo em execução na Malha do Service Fabric e excluir recursos.
author: georgewallace
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: 4aa2fd08491616c1202cc19fb1a6b9bc8e89853c
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/30/2020
ms.locfileid: "97826065"
---
# <a name="tutorial-remove-an-application-and-resources"></a>Tutorial: remover um aplicativo e recursos

Este tutorial é a parte quatro de uma série. Você aprenderá como remover um aplicativo em execução que estava [implantado anteriormente na malha do Service Fabric](service-fabric-mesh-tutorial-template-deploy-app.md). 

Na primeira parte da série, você aprende a:

> [!div class="checklist"]
> * Excluir um aplicativo em execução na Malha do Service Fabric
> * Excluir os recursos do aplicativo

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * [Implantar um aplicativo na Malha do Service Fabric usando um modelo](service-fabric-mesh-tutorial-template-deploy-app.md)
> * [Dimensionar um aplicativo em execução na Malha do Service Fabric](service-fabric-mesh-tutorial-template-scale-services.md)
> * [Atualizar um aplicativo em execução na Malha do Service Fabric](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * Remover um aplicativo

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Prerequisites

Antes de começar este tutorial:

* Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Abra o [Azure Cloud Shell](service-fabric-mesh-howto-setup-cli.md) ou [instale a CLI do Azure e a CLI da Malha do Service Fabric localmente](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli).

## <a name="delete-the-resource-group-and-all-the-resources"></a>Excluir o grupo de recursos e todos os recursos

Quando não forem mais necessários, exclua todos os recursos criados. Anteriormente, você [criou um novo grupo de recursos](service-fabric-mesh-tutorial-template-deploy-app.md#create-a-container-registry) para hospedar a instância do Registro de Contêiner do Azure e os recursos de aplicativo da Malha do Service Fabric.  Você pode excluir esse grupo de recursos, o que excluirá todos os recursos associados.

```azurecli
az group delete --resource-group myResourceGroup
```

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="individually-delete-the-resources"></a>Excluir os recursos individualmente
Você também pode excluir a instância do ACR, o aplicativo da Malha do Service Fabric e os recursos de rede individualmente.

Para excluir a instância do ACR:

```azurecli
az acr delete --resource-group myResourceGroup --name myContainerRegistry
```

Para excluir o aplicativo de Malha do Service Fabric:

```azurecli
az mesh app delete --resource-group myResourceGroup --name todolistapp
```

Para excluir a rede:
```azurecli
az mesh network delete --resource-group myResourceGroup --name todolistappNetwork
```

## <a name="next-steps"></a>Próximas etapas

Nesta parte do tutorial, você aprendeu a:

> [!div class="checklist"]
> * Excluir um aplicativo em execução na Malha do Service Fabric
> * Excluir os recursos do aplicativo
