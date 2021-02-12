---
title: 'Tutorial: Habilitar o complemento Controlador de Entrada para um novo cluster do AKS com uma nova instância do Gateway de Aplicativo do Azure'
description: Use este tutorial para aprender a usar a CLI do Azure para habilitar o complemento Controlador de Entrada do novo cluster do AKS com uma nova instância do Gateway de Aplicativo.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 09/24/2020
ms.author: caya
ms.openlocfilehash: 775dc2133473354a1e534275fb0d813f299217d1
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593801"
---
# <a name="tutorial-enable-the-ingress-controller-add-on-preview-for-a-new-aks-cluster-with-a-new-application-gateway-instance"></a>Tutorial: Habilitar o complemento Controlador de Entrada (versão prévia) para um novo cluster do AKS com uma nova instância do Gateway de Aplicativo

Você pode usar a CLI do Azure para habilitar o complemento [AGIC (Controlador de Entrada do Gateway de Aplicativo)](ingress-controller-overview.md) para um cluster do [AKS (Serviço de Kubernetes do Azure)](https://azure.microsoft.com/services/kubernetes-service/). No momento, o complemento está em versão prévia.

Neste tutorial, você criará um cluster do AKS com o complemento AGIC habilitado. A criação do cluster criará automaticamente uma instância do Gateway de Aplicativo do Azure a ser usada. Em seguida, você implantará um aplicativo de exemplo que usará o complemento para expor o aplicativo por meio do Gateway de Aplicativo. 

O complemento proporciona uma forma muito mais rápida de implantar o AGIC no cluster do AKS do que a forma usada [anteriormente, por meio do Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on). Ele também oferece uma experiência totalmente gerenciada.    

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Crie um grupo de recursos. 
> * Criar um cluster do AKS com o complemento AGIC habilitado. 
> * Implantar um aplicativo de exemplo usando o AGIC para entrada no cluster do AKS.
> * Verificar se o aplicativo pode ser acessado por meio do Gateway de Aplicativo.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial requer a versão 2.0.4 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada. Se você está usando a CLI do Azure, você precisa instalar a extensão preview na CLI usando o seguinte comando, se ainda não estiver instalada:
    ```azurecli-interactive
    az extension add --name aks-preview
    ```

 - Registre o sinalizador de recursos *AKS-IngressApplicationGatewayAddon* usando o comando [az feature register](/cli/azure/feature#az-feature-register), conforme mostrado no exemplo a seguir. Você precisará fazer isso apenas uma vez por assinatura enquanto o complemento ainda estiver na versão prévia.
    ```azurecli-interactive
    az feature register --name AKS-IngressApplicationGatewayAddon --namespace Microsoft.ContainerService
    ```

   Pode demorar alguns minutos para que o status `Registered` seja exibido. Você pode verificar o status de registro usando o comando [az feature list](/cli/azure/feature#az-feature-register):
    ```azurecli-interactive
    az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-IngressApplicationGatewayAddon')].{Name:name,State:properties.state}"
    ```

 - Quando estiver pronto, atualize o registro do provedor de recursos Microsoft.ContainerService usando o comando [az provider register](/cli/azure/provider#az-provider-register):
    ```azurecli-interactive
    az provider register --namespace Microsoft.ContainerService
    ```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

No Azure, você pode alocar recursos relacionados a um grupo de recursos. Crie um grupo de recursos usando [az group create](/cli/azure/group#az-group-create). O seguinte exemplo cria um grupo de recursos denominado *myResourceGroup* no local (região) *canadacentral*: 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-an-aks-cluster-with-the-add-on-enabled"></a>Implantar um cluster do AKS com o complemento habilitado

Agora, você vai implantar um novo cluster do AKS com o complemento AGIC habilitado. Se você não fornecer uma instância existente do Gateway de Aplicativo para uso nesse processo, criaremos e configuraremos automaticamente uma nova instância para levar o tráfego ao cluster do AKS.  

> [!NOTE]
> O complemento Controlador de Entrada do Gateway de Aplicativo dá suporte *somente* aos SKUs do Gateway de Aplicativo v2 (Standard e WAF), e *não* aos SKUs do Gateway de Aplicativo v1. Quando estiver implantando uma nova instância do Gateway de Aplicativo por meio do complemento AGIC, você poderá implantar somente um SKU do Gateway de Aplicativo Standard_v2 SKU. Se quiser habilitar o complemento para um SKU do Gateway de Aplicativo WAF_v2, use um destes métodos:
>
> - Habilitar o WAF no Gateway de Aplicativo por meio do portal. 
> - Primeiro, crie a instância do Gateway de Aplicativo WAF_v2 e, depois, siga as instruções sobre como [habilitar o complemento AGIC com um cluster do AKS existente e uma instância do Gateway de Aplicativo existente](tutorial-ingress-controller-add-on-existing.md). 

No exemplo a seguir, você implantará um novo cluster do AKS chamado *myCluster* usando a [CNI do Azure](../aks/concepts-network.md#azure-cni-advanced-networking) e as [identidades gerenciadas](../aks/use-managed-identity.md). O complemento AGIC será habilitado no grupo de recursos que você criou, *myResourceGroup*. 

Implantar um novo cluster do AKS com o complemento AGIC habilitado sem especificar uma instância existente do Gateway de Aplicativo levará à criação automática de uma instância do Gateway de Aplicativo no SKU Standard_v2. Sendo assim, você também especificará o nome e o espaço de endereço de sub-rede da instância do Gateway de Aplicativo. O nome da instância do Gateway de Aplicativo será *myApplicationGateway* e o espaço de endereço da sub-rede que usaremos será 10.2.0.0/16. Verifique se você adicionou ou atualizou a extensão aks-preview no início deste tutorial. 

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity -a ingress-appgw --appgw-name myApplicationGateway --appgw-subnet-prefix "10.2.0.0/16" --generate-ssh-keys
```

Para configurar parâmetros adicionais para o comando `az aks create`, confira [estas referências](/cli/azure/aks#az-aks-create). 

> [!NOTE]
> O cluster do AKS que você criou aparecerá no grupo de recursos criado, *myResourceGroup*. No entanto, a instância do Gateway de Aplicativo criada automaticamente estará no grupo de recursos do nó, onde os pools de agentes se encontram. O grupo de recursos do nó é denominado *MC_resource-group-name_cluster-name_location* por padrão, mas isso pode ser modificado. 

## <a name="deploy-a-sample-application-by-using-agic"></a>Implantar um aplicativo de exemplo usando o AGIC

Agora, você implantará um aplicativo de exemplo no cluster do AKS que foi criado. O aplicativo usará o complemento AGIC para entrada e conectará a instância do Gateway de Aplicativo ao cluster do AKS. 

Primeiro, obtenha as credenciais para o cluster do AKS executando o comando `az aks get-credentials`: 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

Agora que você tem as credenciais, execute o comando a seguir para configurar um aplicativo de exemplo que usa o AGIC para entrada no cluster. O AGIC atualizará a instância do Gateway de Aplicativo que você configurou anteriormente com as regras de roteamento correspondentes para o novo aplicativo de exemplo implantado.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Verificar se o aplicativo está acessível

Agora que a instância do Gateway de Aplicativo está configurada para fornecer tráfego ao cluster do AKS, vamos verificar se o aplicativo está acessível. Primeiro, obtenha o endereço IP da entrada: 

```azurecli-interactive
kubectl get ingress
```

Verifique se o aplicativo de exemplo criado está sendo executado de uma das seguintes maneiras:

- Visitando o endereço IP da instância do Gateway de Aplicativo que você obteve executando o comando anterior.
- Usando `curl`. 

O Gateway de Aplicativo pode levar um minuto para obter a atualização. Se o Gateway de Aplicativo ainda estiver no estado **atualizando** no portal, aguarde a conclusão da atualização antes de tentar acessar o endereço IP. 

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não precisar mais deles, remova o grupo de recursos, a instância do Gateway de Aplicativo e todos os recursos relacionados:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba como desabilitar o complemento AGIC](./ingress-controller-disable-addon.md)
