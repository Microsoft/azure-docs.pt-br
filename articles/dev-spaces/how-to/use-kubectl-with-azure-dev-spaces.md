---
title: Como usar o kubectl com Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Saiba como usar comandos kubectl em um espaço de desenvolvimento em um cluster do serviço kubernetes do Azure com Azure Dev Spaces habilitado
keywords: 'Docker, Kubernetes, Azure, AKS, Serviço de Kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, k8s '
ms.openlocfilehash: e6f79d98cf209d1bc19753f19c9b17b06017c2b7
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91960146"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Usar kubectl com um Azure Dev Space

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

É possível acessar o cluster do Kubernetes em um Azure Dev Space e usar ferramentas do Kubernetes existentes, como `kubectl`.

Executar o comando `az aks use-dev-spaces` adicionará automaticamente um contexto de configuração `kubectl`, portanto, o kubectl já deverá estar conectado ao cluster do Kubernetes do Azure Dev Spaces. Exemplos:
- Confirme o contexto atual: `kubectl config current-context`
- Listar todos os contextos disponíveis: `kubectl config get-contexts`. 
- Alterar contexto: `kubectl config use-context <context-name>`
- Exiba o painel do Kubernetes: execute `kubectl proxy` e, em seguida, abra o navegador no endereço que este comando emitir (acrescente `/ui` à URL para navegar até o painel do Kubernetes).
- Liste os serviços em execução no espaço de Azure Dev Spaces padrão denominado *padrão*: `kubectl get services --namespace=default`

