---
title: 'Tutorial: Habilitar o complemento Controlador de Entrada para o cluster do AKS existente com o Gateway de Aplicativo do Azure existente'
description: Usar este tutorial para habilitar o complemento Controlador de Entrada para o cluster do AKS existente com um Gateway de Aplicativo existente
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: 2fe615da256099c3135f607a7b6f8095bb93b442
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772809"
---
# <a name="tutorial-enable-application-gateway-ingress-controller-add-on-for-an-existing-aks-cluster-with-an-existing-application-gateway"></a>Tutorial: habilitar o complemento Controlador de Entrada do Gateway de Aplicativo para um cluster do AKS existente usando um Gateway de Aplicativo existente

É possível usar o portal ou a CLI do Azure a fim de habilitar o complemento [AGIC (Controlador de Entrada do Gateway de Aplicativo)](ingress-controller-overview.md) para um cluster do [AKS (Serviço de Kubernetes do Azure) existente](https://azure.microsoft.com/services/kubernetes-service/). Neste tutorial, você aprenderá a usar o complemento AGIC para expor seu aplicativo Kubernetes em um cluster do AKS existente por meio de um Gateway de Aplicativo existente implantado em redes virtuais separadas. Você começará criando um cluster do AKS em uma rede virtual e um Gateway de Aplicativo em uma rede virtual separada para simular os recursos existentes. Será necessário habilitar o complemento AGIC, emparelhar as duas redes virtuais e implantar um aplicativo de exemplo que será exposto por meio do Gateway de Aplicativo usando o complemento AGIC. Se você estiver habilitando o complemento AGIC para um Gateway de Aplicativo existente e um cluster do AKS existente na mesma rede virtual, então poderá ignorar a etapa de emparelhamento abaixo. O complemento fornece uma forma muito mais rápida de implantar o AGIC no cluster do AKS do que [a maneira anterior por meio do Helm](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) e também oferece uma experiência totalmente gerenciada.  

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um grupo de recursos 
> * Criar um cluster do AKS 
> * Criar um Gateway de Aplicativo 
> * Habilitar o complemento AGIC em um cluster do AKS existente por meio da CLI do Azure 
> * Habilitar o complemento AGIC em um cluster do AKS existente por meio do portal do Azure 
> * Emparelhar a rede virtual do Gateway de Aplicativo com a rede virtual do cluster do AKS
> * Implantar um aplicativo de exemplo usando o AGIC para entrada no cluster do AKS
> * Verificar se o aplicativo pode ser acessado por meio do Gateway de Aplicativo

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

No Azure, você pode alocar recursos relacionados a um grupo de recursos. Crie um grupo de recursos usando [az group create](/cli/azure/group#az_group_create). O exemplo a seguir cria um grupo de recursos denominado *myResourceGroup* no local (região) *canadacentral*. 

```azurecli-interactive
az group create --name myResourceGroup --location canadacentral
```

## <a name="deploy-a-new-aks-cluster"></a>Implantar um novo cluster do AKS

Agora você implantará um novo cluster do AKS, para simular ter um cluster do AKS existente para o qual deseja habilitar o complemento AGIC.  

No exemplo a seguir, você implantará um novo cluster do AKS denominado *myCluster* usando a [CNI do Azure](../aks/concepts-network.md#azure-cni-advanced-networking) e as [Identidades Gerenciadas](../aks/use-managed-identity.md) no grupo de recursos que você criou, *myResourceGroup*.

```azurecli-interactive
az aks create -n myCluster -g myResourceGroup --network-plugin azure --enable-managed-identity 
```

Para configurar parâmetros adicionais para o comando `az aks create`, visite as referências [aqui](/cli/azure/aks#az_aks_create). 

## <a name="deploy-a-new-application-gateway"></a>Implantar um novo Gateway de Aplicativo 

Agora você implantará um novo Gateway de Aplicativo, para simular ter um Gateway de Aplicativo existente que deseja usar para balancear carga de tráfego do cluster do AKS *myCluster*. O nome do Gateway de Aplicativo será *myApplicationGateway*, mas você precisará primeiro criar um recurso de IP público denominado *myPublicIp* e uma rede virtual denominada *myVnet* com o espaço de endereço 11.0.0.0/8 (e uma sub-rede com espaço de endereço 11.1.0.0/16 denominada *mySubnet*), depois implantar o Gateway de Aplicativo na *mySubnet* usando o *myPublicIp*. 

Ao usar um cluster do AKS e um Gateway de Aplicativo em redes virtuais separadas, os espaços de endereço das duas redes virtuais não devem se sobrepor. O espaço de endereço padrão em que um cluster do AKS é implantado é 10.0.0.0/8, portanto, definimos o prefixo de endereço da rede virtual do Gateway de Aplicativo como 11.0.0.0/8. 

```azurecli-interactive
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16 
az network application-gateway create -n myApplicationGateway -l canadacentral -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> O complemento AGIC (Controlador de Entrada do Gateway de Aplicativo) **só** é compatível com SKUs do Gateway de Aplicativo v2 (Standard e WAF) e **não** com SKUs do Gateway de Aplicativo v1. 

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-through-azure-cli"></a>Habilitar o complemento AGIC em um cluster do AKS existente por meio da CLI do Azure 

Caso queira continuar usando a CLI do Azure, será possível prosseguir habilitando o complemento AGIC no cluster do AKS criado anteriormente: *myCluster*. Além disso, será necessário especificar o complemento AGIC para usar o Gateway de Aplicativo existente criado anteriormente: *myApplicationGateway*.

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

## <a name="enable-the-agic-add-on-in-existing-aks-cluster-through-portal"></a>Habilitar o complemento AGIC em um cluster do AKS existente por meio do portal do Azure 

Caso queira usar o portal do Azure para habilitar o complemento AGIC, acesse [(https://aka.ms/azure/portal/aks/agic)](https://aka.ms/azure/portal/aks/agic) e navegue até o cluster do AKS por meio do link do portal. Nessa opção, acesse a guia Rede no cluster do AKS. Você verá uma seção do controlador de entrada do Gateway de Aplicativo. Ele permite habilitar/desabilitar o complemento controlador de entrada usando a interface do usuário do portal do Azure. Marque a caixa ao lado da opção "Habilitar controlador de entrada". No menu suspenso, selecione o Gateway de Aplicativo criado anteriormente: *myApplicationGateway*. 

![Portal do Controlador de Entrada do Gateway de Aplicativo](./media/tutorial-ingress-controller-add-on-existing/portal-ingress-controller-add-on.png)

## <a name="peer-the-two-virtual-networks-together&quot;></a>Emparelhar as duas redes virtuais

Como implantamos o cluster do AKS na própria rede virtual dele e o Gateway de Aplicativo em outra rede virtual, você precisará emparelhar as duas redes virtuais para que o tráfego flua do Gateway de Aplicativo para os pods do cluster. O emparelhamento das duas redes virtuais requer a execução do comando da CLI do Azure duas vezes, separadamente, para garantir que a conexão seja bidirecional. O primeiro comando criará uma conexão de emparelhamento da rede virtual do Gateway de Aplicativo para a rede virtual do AKS. O segundo comando criará uma conexão de emparelhamento na direção oposta.

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query &quot;nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```

## <a name="deploy-a-sample-application-using-agic"></a>Implantar um aplicativo de exemplo usando o AGIC 

Agora, você implantará um aplicativo de exemplo no cluster do AKS que você criou, que usará o complemento AGIC para entrada e conectará o Gateway de Aplicativo ao cluster do AKS. Primeiro, você receberá credenciais para o cluster do AKS implantado executando o comando `az aks get-credentials`. 

```azurecli-interactive
az aks get-credentials -n myCluster -g myResourceGroup
```

Após ter as credenciais para o cluster que você criou, execute o comando a seguir para configurar um aplicativo de exemplo que use o AGIC para entrada no cluster. O AGIC atualizará o Gateway de Aplicativo que você configurou anteriormente com as regras de roteamento correspondentes para o novo aplicativo de exemplo implantado.  

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml 
```

## <a name="check-that-the-application-is-reachable"></a>Verificar se o aplicativo está acessível

Agora que o Gateway de Aplicativo está configurado para fornecer tráfego ao cluster do AKS, vamos verificar se o aplicativo está acessível. Primeiro, você obterá o endereço IP da entrada. 

```azurecli-interactive
kubectl get ingress
```

Verifique se o aplicativo de exemplo que você criou está ativo e em execução visitando o endereço IP do Gateway de Aplicativo que você obteve ao executar o comando acima ou verifique com `curl`. Pode levar um minuto até que o Gateway de Aplicativo obtenha a atualização, portanto, se o Gateway de Aplicativo ainda estiver no estado "Atualizando" no portal, deixe que ele termine antes de tentar acessar o endereço IP. 

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, remova o grupo de recursos, o gateway de aplicativo e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup 
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aprender como desabilitar o complemento AGIC](./ingress-controller-disable-addon.md)
