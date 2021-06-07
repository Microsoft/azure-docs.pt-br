---
title: Configurar IP de saída estático
description: Configurar o Firewall do Azure e as rotas definidas pelo usuário para as cargas de trabalho das instâncias de contêiner do Azure que usam o endereço IP público do firewall para entrada e saída
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: 497645b9fe7f908cc9b8b4d7ed0ba5e201570160
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89566561"
---
# <a name="configure-a-single-public-ip-address-for-outbound-and-inbound-traffic-to-a-container-group"></a>Configurar um único endereço IP público para tráfego de entrada e de saída para um grupo de contêineres

A configuração de um [grupo de contêineres](container-instances-container-groups.md) com um endereço IP voltado para o externo permite que clientes externos usem o endereço IP para acessar um contêiner no grupo. Por exemplo, um navegador pode acessar um aplicativo Web em execução em um contêiner. No entanto, atualmente um grupo de contêineres usa um endereço IP diferente para o tráfego de saída. Esse endereço IP de egresso não é exposto programaticamente, o que torna o monitoramento do grupo de contêineres e a configuração de regras de firewall do cliente mais complexas.

Este artigo fornece etapas para configurar um grupo de contêineres em uma [rede virtual](container-instances-virtual-network-concepts.md) integrada ao [Firewall do Azure](../firewall/overview.md). Ao configurar uma rota definida pelo usuário para o grupo de contêineres e as regras de firewall, você pode rotear e identificar o tráfego de e para o grupo de contêineres. Entrada e saída do grupo de contêineres usam o endereço IP público do firewall. Um único endereço IP de egresso pode ser usado por vários grupos de contêineres implantados na sub-rede da rede virtual delegada para instâncias de contêiner do Azure.

Neste artigo, você usa o CLI do Azure para criar os recursos para este cenário:

* Grupos de contêineres implantados em uma sub-rede delegada [na rede virtual](container-instances-vnet.md) 
* Um firewall do Azure implantado na rede com um endereço IP público estático
* Uma rota definida pelo usuário na sub-rede dos grupos de contêineres
* Uma regra NAT para entrada de firewall e uma regra de aplicativo para saída

Em seguida, você valida a entrada e a saída dos grupos de contêineres de exemplo por meio do firewall.

## <a name="deploy-aci-in-a-virtual-network"></a>Implantar ACI em uma rede virtual

Em um caso típico, talvez você já tenha uma rede virtual do Azure na qual implantar um grupo de contêineres. Para fins de demonstração, os comandos a seguir criam uma rede virtual e uma sub-rede quando o grupo de contêineres é criado. A sub-rede é delegada para instâncias de contêiner do Azure. 

O grupo de contêineres executa um pequeno aplicativo Web a partir da `aci-helloworld` imagem. Conforme mostrado em outros artigos da documentação, essa imagem empacota um pequeno aplicativo Web escrito em Node.js que atende a uma página HTML estática.

Se precisar de um, primeiro crie um grupo de recursos do Azure com o comando [AZ Group Create][az-group-create] . Por exemplo:

```azurecli
az group create --name myResourceGroup --location eastus
```

Para simplificar os exemplos de comando a seguir, use uma variável de ambiente para o nome do grupo de recursos:

```console
export RESOURCE_GROUP_NAME=myResourceGroup
```

Crie o grupo de contêineres com o comando [AZ container Create][az-container-create] :

```azurecli
az container create \
  --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet aci-vnet \
  --vnet-address-prefix 10.0.0.0/16 \
  --subnet aci-subnet \
  --subnet-address-prefix 10.0.0.0/24
```

> [!TIP]
> Ajuste o valor de `--subnet address-prefix` para o espaço de endereço IP que você precisa em sua sub-rede. A menor sub-rede com suporte é/29, que fornece oito endereços IP. Alguns endereços IP são reservados para uso pelo Azure.

Para usar em uma etapa posterior, obtenha o endereço IP privado do grupo de contêineres executando o comando [AZ container show] [AZ-contêiner-show]:

```azurecli
ACI_PRIVATE_IP="$(az container show --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress.ip --output tsv)"
```

## <a name="deploy-azure-firewall-in-network"></a>Implantar o Firewall do Azure na rede

Nas seções a seguir, use o CLI do Azure para implantar um firewall do Azure na rede virtual. Para obter informações, consulte [tutorial: implantar e configurar o Firewall do Azure usando o portal do Azure](../firewall/deploy-cli.md).

Primeiro, use [AZ Network vnet subnet Create][az-network-vnet-subnet-create] para adicionar uma sub-rede chamada AzureFirewallSubnet para o firewall. AzureFirewallSubnet é o nome *necessário* desta sub-rede.

```azurecli
az network vnet subnet create \
  --name AzureFirewallSubnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet   \
  --address-prefix 10.0.1.0/26
```

Use os comandos a seguir [CLI do Azure](../firewall/deploy-cli.md) para criar um firewall na sub-rede.

Se ainda não estiver instalado, adicione a extensão de firewall ao CLI do Azure usando o comando [AZ Extension Add][az-extension-add] :

```azurecli
az extension add --name azure-firewall
```

Crie os recursos de firewall:

```azurecli
az network firewall create \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus

az network public-ip create \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --allocation-method static \
  --sku standard
    
az network firewall ip-config create \
  --firewall-name myFirewall \
  --name FW-config \
  --public-ip-address fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet
```

Atualize a configuração do firewall usando o comando [AZ Network firewall Update][az-network-firewall-update] :

```azurecli
az network firewall update \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME
```

Obtenha o endereço IP privado do firewall usando o comando [AZ Network firewall IP-config List][az-network-firewall-ip-config-list] . Esse endereço IP privado é usado em um comando posterior.


```azurecli
FW_PRIVATE_IP="$(az network firewall ip-config list \
  --resource-group $RESOURCE_GROUP_NAME \
  --firewall-name myFirewall \
  --query "[].privateIpAddress" --output tsv)"
```
Obtenha o endereço IP público do firewall usando o comando [AZ Network Public-IP show][az-network-public-ip-show] . Esse endereço IP público é usado em um comando posterior.

```azurecli
FW_PUBLIC_IP="$(az network public-ip show \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress --output tsv)"
```

## <a name="define-user-defined-route-on-aci-subnet"></a>Definir a rota definida pelo usuário na sub-rede ACI

Defina uma rota definida pelo uso na sub-rede ACI para desviar o tráfego para o Firewall do Azure. Para obter mais informações, consulte [rotear tráfego de rede](../virtual-network/tutorial-create-route-table-cli.md). 

### <a name="create-route-table"></a>Criar tabela de rotas

Primeiro, execute o comando [AZ Network Route-Table Create][az-network-route-table-create] a seguir para criar a tabela de rotas. Crie a tabela de rotas na mesma região que a rede virtual.

```azurecli
az network route-table create \
  --name Firewall-rt-table \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --disable-bgp-route-propagation true
```

### <a name="create-route"></a>Criar rota

Execute [AZ Network-rota-tabela Route Create][az-network-route-table-route-create] para criar uma rota na tabela de rotas. Para rotear o tráfego para o firewall, defina o tipo do próximo salto como `VirtualAppliance` e passe o endereço IP privado do firewall como o endereço do próximo salto.

```azurecli
az network route-table route create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $FW_PRIVATE_IP
```

### <a name="associate-route-table-to-aci-subnet"></a>Associar a tabela de rotas à sub-rede ACI

Execute o comando [AZ Network vnet subnet Update][az-network-vnet-subnet-update] para associar a tabela de rotas à sub-rede delegada às instâncias de contêiner do Azure.

```azurecli
az network vnet subnet update \
  --name aci-subnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet \
  --address-prefixes 10.0.0.0/24 \
  --route-table Firewall-rt-table
```

## <a name="configure-rules-on-firewall"></a>Configurar regras no firewall

Por padrão, o Firewall do Azure nega (bloqueia) o tráfego de entrada e de saída. 

### <a name="configure-nat-rule-on-firewall-to-aci-subnet"></a>Configurar a regra NAT no firewall para a sub-rede ACI

Crie uma [regra NAT](../firewall/rule-processing.md) no firewall para converter e filtrar o tráfego de Internet de entrada para o contêiner de aplicativos que você iniciou anteriormente na rede. Para obter detalhes, consulte [Filtrar tráfego de Internet de entrada com o Firewall do Azure DNAT](../firewall/tutorial-firewall-dnat.md)

Crie uma regra NAT e uma coleção usando o comando [AZ Network firewall NAT-Rule Create][az-network-firewall-nat-rule-create] :

```azurecli
az network firewall nat-rule create \
  --firewall-name myFirewall \
  --collection-name myNATCollection \
  --action dnat \
  --name myRule \
  --protocols TCP \
  --source-addresses '*' \
  --destination-addresses $FW_PUBLIC_IP \
  --destination-ports 80 \
  --resource-group $RESOURCE_GROUP_NAME \
  --translated-address $ACI_PRIVATE_IP \
  --translated-port 80 \
  --priority 200
```

Adicione regras NAT conforme necessário para filtrar o tráfego para outros endereços IP na sub-rede. Por exemplo, outros grupos de contêineres na sub-rede podem expor endereços IP para o tráfego de entrada ou outros endereços IP internos podem ser atribuídos ao grupo de contêineres após uma reinicialização.

### <a name="create-outbound-application-rule-on-the-firewall"></a>Criar regra de aplicativo de saída no firewall

Execute o seguinte comando [AZ Network firewall Application-Rule Create][az-network-firewall-application-rule-create] para criar uma regra de saída no firewall. Essa regra de exemplo permite o acesso da sub-rede delegada às instâncias de contêiner do Azure para o FQDN `checkip.dyndns.org` . O acesso HTTP ao site é usado em uma etapa posterior para confirmar o endereço IP de saída das instâncias de contêiner do Azure.

```azurecli
az network firewall application-rule create \
  --collection-name myAppCollection \
  --firewall-name myFirewall \
  --name Allow-CheckIP \
  --protocols Http=80 Https=443 \
  --resource-group $RESOURCE_GROUP_NAME \
  --target-fqdns checkip.dyndns.org \
  --source-addresses 10.0.0.0/24 \
  --priority 200 \
  --action Allow
```

## <a name="test-container-group-access-through-the-firewall"></a>Teste o acesso ao grupo de contêineres por meio do firewall

As seções a seguir verificam se a sub-rede delegada para instâncias de contêiner do Azure está configurada corretamente por trás do firewall do Azure. As etapas anteriores Rotearam o tráfego de entrada para a sub-rede e o tráfego de saída da sub-rede por meio do firewall.

### <a name="test-ingress-to-a-container-group"></a>Testar a entrada em um grupo de contêineres

Teste o acesso de entrada para o *appcontainer* em execução na rede virtual navegando até o endereço IP público do firewall. Anteriormente, você armazenou o endereço IP público na variável $FW _PUBLIC_IP:

```bash
echo $FW_PUBLIC_IP
```

A saída é semelhante a:

```console
52.142.18.133
```

Se a regra de NAT no firewall estiver configurada corretamente, você verá o seguinte ao inserir o endereço IP público do firewall em seu navegador:

:::image type="content" source="media/container-instances-egress-ip-address/aci-ingress-ip-address.png" alt-text="Navegue até o endereço IP público do firewall":::

### <a name="test-egress-from-a-container-group"></a>Testar a saída de um grupo de contêineres


Implante o seguinte contêiner de exemplo na rede virtual. Quando é executado, ele envia uma única solicitação HTTP para `http://checkip.dyndns.org` , que exibe o endereço IP do remetente (o endereço IP de saída). Se a regra de aplicativo no firewall estiver configurada corretamente, o endereço IP público do firewall será retornado.

```azurecli
az container create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name testegress \
  --image mcr.microsoft.com/azuredocs/aci-tutorial-sidecar \
  --command-line "curl -s http://checkip.dyndns.org" \
  --restart-policy OnFailure \
  --vnet aci-vnet \
  --subnet aci-subnet
```

Exiba os logs de contêiner para confirmar se o endereço IP é o mesmo que o endereço IP público do firewall.

```azurecli
az container logs \
  --resource-group $RESOURCE_GROUP_NAME \
  --name testegress 
```

A saída é semelhante a:

```console
<html><head><title>Current IP Check</title></head><body>Current IP Address: 52.142.18.133</body></html>
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você configura grupos de contêineres em uma rede virtual por trás de um firewall do Azure. Você configurou uma rota definida pelo usuário e as regras de aplicativo e NAT no firewall. Usando essa configuração, você configura um único endereço IP estático para entrada e saída de instâncias de contêiner do Azure.

Para obter mais informações sobre como gerenciar o tráfego e proteger os recursos do Azure, consulte a documentação do [Firewall do Azure](../firewall/index.yml) .



[az-group-create]: /cli/azure/group#az-group-create
[az-container-create]: /cli/azure/container#az-container-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-update]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-update
[az-network-public-ip-show]: /cli/azure/network/public-ip/#az-network-public-ip-show
[az-network-route-table-create]:/cli/azure/network/route-table/#az-network-route-table-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
[az-network-firewall-ip-config-list]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-list
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-update
[az-container-exec]: /cli/azure/container#az-container-exec
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-vm-open-port]: /cli/azure/vm#az-vm-open-port
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[az-network-firewall-application-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/application-rule#ext-azure-firewall-az-network-firewall-application-rule-create
[az-network-firewall-nat-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/nat-rule#ext-azure-firewall-az-network-firewall-nat-rule-create






