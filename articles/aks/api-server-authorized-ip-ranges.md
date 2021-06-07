---
title: Intervalos de IP autorizados do servidor de API no serviço kubernetes do Azure (AKS)
description: Saiba como proteger seu cluster usando um intervalo de endereços IP para acessar o servidor de API no serviço kubernetes do Azure (AKS)
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.openlocfilehash: 20c3d46e9a19c66c727e5a21bccd6caa0dc5f0ba
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771916"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>Proteger o acesso ao servidor de API usando intervalos de endereços IP autorizados no serviço de kubernetes do Azure (AKS)

No kubernetes, o servidor de API recebe solicitações para executar ações no cluster, como para criar recursos ou dimensionar o número de nós. O servidor de API é a maneira central de interagir com e gerenciar um cluster. Para melhorar a segurança do cluster e minimizar os ataques, o servidor de API só deve ser acessível a partir de um conjunto limitado de intervalos de endereços IP.

Este artigo mostra como usar intervalos de endereços IP autorizados do servidor de API para limitar quais endereços IP e CIDR podem acessar o plano de controle.

## <a name="before-you-begin"></a>Antes de começar

Este artigo mostra como criar um cluster AKS usando o CLI do Azure.

Você precisará da CLI do Azure versão 2.0.76 ou posterior instalada e configurada. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][install-azure-cli].

### <a name="limitations"></a>Limitações

O recurso de intervalos de IP autorizados do servidor de API tem as seguintes limitações:
- Em clusters criados após os intervalos de endereços IP autorizados do servidor de API movidos para fora da visualização em outubro de 2019, os intervalos de endereços IP autorizados do servidor de API só têm suporte no balanceador de carga SKU *padrão* . Os clusters existentes com o balanceador de carga de SKU *básico* e os intervalos de endereços IP autorizados do servidor de API configurados continuarão funcionando como estão, mas não poderão ser migrados para um balanceador de carga de SKU *padrão* . Esses clusters existentes também continuarão a funcionar se sua versão ou plano de controle do kubernetes forem atualizados. Os intervalos de endereços IP autorizados do servidor de API não têm suporte para clusters privados.
- Esse recurso não é compatível com clusters que usam [IP público por nó](use-multiple-node-pools.md#assign-a-public-ip-per-node-for-your-node-pools).

## <a name="overview-of-api-server-authorized-ip-ranges"></a>Visão geral dos intervalos de IP autorizados do servidor de API

O servidor de API do kubernetes é como as APIs subjacentes do kubernetes são expostas. Esse componente fornece a interação para ferramentas de gerenciamento, tais como `kubectl` ou o painel do Kubernetes. O AKS fornece um plano de controle de cluster de locatário único, com um servidor de API dedicado. Por padrão, o servidor de API recebe um endereço IP público e você deve controlar o acesso usando o controle de acesso baseado em função do kubernetes (kubernetes RBAC) ou o RBAC do Azure.

Para proteger o acesso ao servidor do plano de controle AKS/API publicamente acessível, você pode habilitar e usar intervalos de IP autorizados. Esses intervalos de IP autorizados permitem que os intervalos de endereços IP definidos se comuniquem com o servidor de API. Uma solicitação feita ao servidor de API de um endereço IP que não faz parte desses intervalos de IP autorizado é bloqueada. Continue a usar o RBAC do kubernetes ou o RBAC do Azure para autorizar usuários e as ações que eles solicitam.

Para obter mais informações sobre o servidor de API e outros componentes de cluster, consulte [kubernetes Core Concepts for AKs][concepts-clusters-workloads].

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>Criar um cluster AKS com intervalos de IP autorizados do servidor de API habilitado

Crie um cluster usando [AZ AKs Create][az-aks-create] e especifique o *`--api-server-authorized-ip-ranges`* parâmetro para fornecer uma lista de intervalos de endereços IP autorizados. Esses intervalos de endereços IP geralmente são intervalos de endereços usados por suas redes locais ou IPs públicos. Quando você especificar um intervalo CIDR, comece com o primeiro endereço IP no intervalo. Por exemplo, *137.117.106.90/29* é um intervalo válido, mas certifique-se de especificar o primeiro endereço IP no intervalo, como *137.117.106.88/29*.

> [!IMPORTANT]
> Por padrão, o cluster usa o [balanceador de carga SKU padrão][standard-sku-lb] que você pode usar para configurar o gateway de saída. Quando você habilita intervalos de IP autorizados do servidor de API durante a criação do cluster, o IP público do cluster também é permitido, por padrão, além dos intervalos que você especificar. Se você especificar *""* ou nenhum valor para *`--api-server-authorized-ip-ranges`* , os intervalos de IP autorizados do servidor de API serão desabilitados. Observe que, se você estiver usando o PowerShell, use *`--api-server-authorized-ip-ranges=""`* (com sinal de igual) para evitar quaisquer problemas de análise.

O exemplo a seguir cria um cluster de nó único chamado *myAKSCluster* no grupo de recursos chamado *MyResource* Group com intervalos de IP autorizados do servidor de API habilitados. Os intervalos de endereços IP permitidos são *73.140.245.0/24*:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --generate-ssh-keys
```

> [!NOTE]
> Você deve adicionar esses intervalos a uma lista de permissões:
> - O endereço IP público do firewall
> - Qualquer intervalo que represente as redes das quais você administrará o cluster
> - Se estiver usando Azure Dev Spaces em seu cluster AKS, você precisará permitir [intervalos adicionais com base em sua região][dev-spaces-ranges].
>
> O limite superior para o número de intervalos de IP que você pode especificar é 200.
>
> As regras podem levar até 2min para serem propagadas. Aguarde até esse momento ao testar a conexão.

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>Especificar os IPs de saída para o balanceador de carga SKU padrão

Ao criar um cluster AKS, se você especificar os endereços IP de saída ou prefixos para o cluster, esses endereços ou prefixos também serão permitidos. Por exemplo:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2> \
    --generate-ssh-keys
```

No exemplo acima, todos os IPs fornecidos no parâmetro *`--load-balancer-outbound-ip-prefixes`* são permitidos junto com os IPs no *`--api-server-authorized-ip-ranges`* parâmetro.

Em vez disso, você pode especificar o *`--load-balancer-outbound-ip-prefixes`* parâmetro para permitir prefixos de IP de balanceador de carga de saída.

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>Permitir somente o IP público de saída do balanceador de carga SKU padrão

Quando você habilita intervalos de IP autorizados do servidor de API durante a criação do cluster, o IP público de saída para o balanceador de carga SKU padrão para o cluster também é permitido por padrão, além dos intervalos que você especificar. Para permitir apenas o IP público de saída do balanceador de carga SKU padrão, use *0.0.0.0/32* ao especificar o *`--api-server-authorized-ip-ranges`* parâmetro.

No exemplo a seguir, somente o IP público de saída do balanceador de carga SKU padrão é permitido e você só pode acessar o servidor de API a partir dos nós no cluster.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 0.0.0.0/32 \
    --generate-ssh-keys
```

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>Atualizar intervalos de IP autorizados do servidor de API de um cluster

Para atualizar os intervalos de IP autorizados do servidor de API em um cluster existente, use o comando [AZ AKs Update][az-aks-update] e use os *`--api-server-authorized-ip-ranges`* parâmetros,--Load-Balancer-Outbound-IP-Prefixes *, *`--load-balancer-outbound-ips`* ou--Load-Balancer-IP-Prefixes* .

O exemplo a seguir atualiza os intervalos de IP autorizados do servidor de API no cluster chamado *myAKSCluster* no grupo de recursos chamado *MyResource* Group. O intervalo de endereços IP a ser autorizado é *73.140.245.0/24*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

Você também pode usar *0.0.0.0/32* ao especificar o *`--api-server-authorized-ip-ranges`* parâmetro para permitir apenas o IP público do balanceador de carga SKU padrão.

## <a name="disable-authorized-ip-ranges"></a>Desabilitar intervalos de IP autorizados

Para desabilitar intervalos IP autorizados, use [AZ AKs Update][az-aks-update] e especifique um intervalo vazio para desabilitar intervalos de IP autorizados do servidor de API. Por exemplo:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="find-existing-authorized-ip-ranges"></a>Localizar intervalos de IP autorizados existentes

Para localizar intervalos de IP que foram autorizados, use [AZ AKs show][az-aks-show] e especifique o nome do cluster e o grupo de recursos. Por exemplo:

```azurecli-interactive
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query apiServerAccessProfile.authorizedIpRanges'
```

## <a name="update-disable-and-find-authorized-ip-ranges-using-azure-portal"></a>Atualizar, desabilitar e localizar intervalos de IP autorizados usando o portal do Azure

As operações acima de adicionar, atualizar, localizar e desabilitar intervalos de IP autorizados também podem ser executadas no portal do Azure. Para acessar o, navegue até **rede** em **configurações** na folha do menu do recurso de cluster.

:::image type="content" source="media/api-server-authorized-ip-ranges/ip-ranges-specified.PNG" alt-text="Em um navegador, mostra a página de portal do Azure de configurações de rede do recurso de cluster. As opções ' definir intervalo de IP especificado ' e ' intervalos de IP especificados ' são realçadas.":::

## <a name="how-to-find-my-ip-to-include-in---api-server-authorized-ip-ranges"></a>Como encontrar meu IP para incluir em `--api-server-authorized-ip-ranges` ?

Você deve adicionar seus computadores de desenvolvimento, ferramentas ou endereços IP de automação à lista de clusters do AKS de intervalos de IP aprovados para acessar o servidor de API a partir daí. 

Outra opção é configurar um Jumpbox com as ferramentas necessárias dentro de uma sub-rede separada na rede virtual do firewall. Isso pressupõe que seu ambiente tenha um firewall com a respectiva rede e que você tenha adicionado os IPs de firewall a intervalos autorizados. Da mesma forma, se você tiver um túnel forçado da sub-rede AKS para a sub-rede de firewall, do que ter o Jumpbox na sub-rede do cluster também está bem.

Adicione outro endereço IP aos intervalos aprovados com o comando a seguir.

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)
# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32
```

>> [!NOTE]
> O exemplo acima acrescenta os intervalos de IP autorizados do servidor de API no cluster. Para desabilitar intervalos IP autorizados, use AZ AKs Update e especifique um intervalo vazio "". 

Outra opção é usar o comando abaixo em sistemas Windows para obter o endereço IPv4 público, ou você pode usar as etapas em [localizar seu endereço IP](https://support.microsoft.com/en-gb/help/4026518/windows-10-find-your-ip-address).

```azurepowershell-interactive
Invoke-RestMethod http://ipinfo.io/json | Select -exp ip
```

Você também pode encontrar esse endereço pesquisando "o que é meu endereço IP" em um navegador da Internet.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você habilitou intervalos de IP autorizados do servidor de API. Essa abordagem é uma parte de como você pode executar um cluster AKS seguro.

Para obter mais informações, consulte [conceitos de segurança para aplicativos e clusters no AKs][concepts-security] e [as práticas recomendadas para segurança de cluster e atualizações no AKs][operator-best-practices-cluster-security].

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: ../dev-spaces/configure-networking.md#aks-cluster-network-requirements
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[route-tables]: ../virtual-network/manage-route-table.md
[standard-sku-lb]: load-balancer-standard.md
