---
title: Instalar o Azure Dev Spaces no AKS e nas ferramentas do lado do cliente
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/24/2019
ms.topic: conceptual
description: Saiba como instalar Azure Dev Spaces em um cluster AKS e instalar as ferramentas do lado do cliente.
keywords: Docker, Kubernetes, Azure, AKS, Serviço de Kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, k8s
ms.openlocfilehash: 642d4406840f1748c3b6cbb4441227fff0c6d721
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494840"
---
# <a name="install-azure-dev-spaces-on-aks-and-the-client-side-tooling"></a>Instalar o Azure Dev Spaces no AKS e nas ferramentas do lado do cliente

Este artigo mostra várias maneiras de instalar Azure Dev Spaces em um cluster AKS, bem como instalar as ferramentas do lado do cliente.

## <a name="install-azure-dev-spaces-using-the-cli"></a>Instalar Azure Dev Spaces usando a CLI

Antes de poder instalar espaços de desenvolvimento usando a CLI, você precisa:
* Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, é possível criar uma [conta gratuita][az-portal-create-account].
* [O CLI do Azure instalado][install-cli].
* [Um cluster AKs][create-aks-cli] em uma [região com suporte][supported-regions].

Use o comando `use-dev-spaces` para habilitar o Dev Spaces no cluster do AKS e siga os prompts.

```cmd
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster
```

O comando acima habilita espaços de desenvolvimento no cluster *myAKSCluster* no grupo *MyResource* Group e cria um espaço de desenvolvimento *padrão* .

```cmd
$ az aks use-dev-spaces -g myResourceGroup -n myAKSCluster

'An Azure Dev Spaces Controller' will be created that targets resource 'myAKSCluster' in resource group 'myResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'myAKSCluster' in resource group 'myResourceGroup' that targets resource 'myAKSCluster' in resource group 'myResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'myAKSCluster' in resource group 'myResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

O `use-dev-spaces` comando também instala a CLI do Azure dev Spaces.

## <a name="install-azure-dev-spaces-using-the-azure-portal"></a>Instalar Azure Dev Spaces usando o portal do Azure

Antes de instalar espaços de desenvolvimento usando o portal do Azure, você precisa:
* Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, é possível criar uma [conta gratuita][az-portal-create-account].
* [Um cluster AKs][create-aks-portal] em uma [região com suporte][supported-regions].

Para instalar Azure Dev Spaces usando o portal do Azure:
1. Entre no [Portal do Azure][az-portal].
1. Navegue até o cluster AKS.
1. Clique em *espaços de desenvolvimento*.
1. Altere *Habilitar Dev Spaces* para *Sim* e clique em *Salvar*.

![Habilitar o Dev Spaces no portal do Azure](../media/how-to-setup-dev-spaces/enable-dev-spaces-portal.png)

A instalação do Azure Dev Spaces usando **o portal do Azure não** instala nenhuma ferramenta do lado do cliente para Azure dev Spaces.

## <a name="install-the-client-side-tooling"></a>Instalar as ferramentas do lado do cliente

Você pode usar o Azure Dev Spaces ferramentas do lado do cliente para interagir com espaços de desenvolvimento em um cluster AKS do computador local. Há várias maneiras de instalar as ferramentas do lado do cliente:

* No [Visual Studio Code][vscode], instale a [extensão de Azure dev Spaces][vscode-extension].
* No [Visual Studio 2019][visual-studio], instale a carga de trabalho de desenvolvimento do Azure.
* No Visual Studio 2017, instale a carga de trabalho de desenvolvimento Web e [Ferramentas do Visual Studio para kubernetes][visual-studio-k8s-tools].
* Baixe e instale a CLI do [Windows][cli-win], [Mac][cli-mac]ou [Linux][cli-linux] .

## <a name="next-steps"></a>Próximas etapas

Saiba como o Azure Dev Spaces ajuda você a desenvolver aplicativos mais complexos em vários contêineres e como você pode simplificar o desenvolvimento colaborativo trabalhando com versões diferentes ou branches do seu código em diferentes espaços.

> [!div class="nextstepaction"]
> [Desenvolvimento em equipe com o Azure Dev Spaces][team-development-qs]

[create-aks-cli]: ../../aks/kubernetes-walkthrough.md#create-a-resource-group
[create-aks-portal]: ../../aks/kubernetes-walkthrough-portal.md#create-an-aks-cluster
[install-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[supported-regions]: ../about.md#supported-regions-and-configurations
[team-development-qs]: ../quickstart-team-development.md

[az-portal]: https://portal.azure.com
[az-portal-create-account]: https://azure.microsoft.com/free
[cli-linux]: https://aka.ms/get-azds-linux
[cli-mac]: https://aka.ms/get-azds-mac
[cli-win]: https://aka.ms/get-azds-windows
[visual-studio]: https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs
[visual-studio-k8s-tools]: https://aka.ms/get-vsk8stools
[vscode]: https://code.visualstudio.com/download
[vscode-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
