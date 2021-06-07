---
title: 'Início Rápido: Diagnosticar um problema do filtro de tráfego de rede de VMs – CLI do Azure'
titleSuffix: Azure Network Watcher
description: Saiba como usar a CLI do Azure para diagnosticar um problema no filtro de tráfego de rede da máquina virtual usando a funcionalidade de verificação de fluxo de IP do Observador de Rede do Azure.
services: network-watcher
documentationcenter: network-watcher
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 01/07/2021
ms.author: kumud
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 701df4353e8d2e36baf0496bd6944c4a95395414
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763245"
---
# <a name="quickstart-diagnose-a-virtual-machine-network-traffic-filter-problem---azure-cli"></a>Início Rápido: diagnosticar um problema de filtro de tráfego de rede de máquina virtual – CLI do Azure

Neste início rápido você implanta uma VM (máquina virtual ) e, em seguida, verifica a comunicação com um endereço IP e uma URL e de um endereço IP. Você determina a causa de uma falha de comunicação e como resolvê-la.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este guia de início rápido exige a versão 2.0 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada. 

- Os comandos da CLI do Azure neste guia de início rápido são formatados para serem executados em um shell Bash.

## <a name="create-a-vm"></a>Criar uma máquina virtual

Antes de criar uma VM, você deve criar um grupo de recursos para conter a VM. Crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Crie uma VM com [az vm create](/cli/azure/vm). Se as chaves SSH ainda não existirem em uma localização de chave padrão, o comando criará. Para usar um conjunto específico de chaves, use a opção `--ssh-key-value`. O exemplo a seguir cria uma VM chamada *myVm*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

A VM demora alguns minutos para criar. Não continue com as etapas restantes até que a VM seja criada e a CLI do Azure retorne a saída.

## <a name="test-network-communication"></a>Testar comunicação de rede

Para testar a comunicação de rede com o Observador de Rede, primeiro habilite um observador de rede na região onde está localizada a VM que deseja testar e, em seguida, use a funcionalidade Verificação de fluxo de IP do Observador de Rede para testar a comunicação.

### <a name="enable-network-watcher"></a>Habilitar o observador de rede

Se você já tiver um observador de rede habilitado na região Este dos EUA, vá para [Usar verificação de fluxo de IP](#use-ip-flow-verify). Use o comando [az network watcher configure](/cli/azure/network/watcher#az_network_watcher_configure) para criar um observador de rede na região EastUS:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-ip-flow-verify"></a>Usar verificação de fluxo de IP

Quando você cria uma VM, o Azure permite e nega o tráfego de rede na VM, por padrão. Mais tarde, você pode substituir os padrões do Azure, permitindo ou negando tipos adicionais de tráfego. Para testar se o tráfego é permitido ou negado para diferentes destinos e de um endereço IP de origem, use o comando [az network watcher test-ip-flow](/cli/azure/network/watcher#az_network_watcher_test_ip_flow).

Teste a comunicação de saída da VM para um dos endereços IP para www.bing.com:

```azurecli-interactive
az network watcher test-ip-flow \
  --direction outbound \
  --local 10.0.0.4:60000 \
  --protocol TCP \
  --remote 13.107.21.200:80 \
  --vm myVm \
  --nic myVmVMNic \
  --resource-group myResourceGroup \
  --out table
```

Após alguns segundos, o resultado retornado informa que o acesso é permitido por uma regra de segurança chamada **AllowInternetOutbound**.

Teste a comunicação de saída da VM em 172.31.0.100:

```azurecli-interactive
az network watcher test-ip-flow \
  --direction outbound \
  --local 10.0.0.4:60000 \
  --protocol TCP \
  --remote 172.31.0.100:80 \
  --vm myVm \
  --nic myVmVMNic \
  --resource-group myResourceGroup \
  --out table
```

O resultado retornado informa que o acesso é negado por uma regra de segurança chamada **DefaultOutboundDenyAll**.

Teste a comunicação de entrada para a VM em 172.31.0.100:

```azurecli-interactive
az network watcher test-ip-flow \
  --direction inbound \
  --local 10.0.0.4:80 \
  --protocol TCP \
  --remote 172.31.0.100:60000 \
  --vm myVm \
  --nic myVmVMNic \
  --resource-group myResourceGroup \
  --out table
```

O resultado retornado informa que o acesso é negado devido a uma regra de segurança chamada **DefaultInboundDenyAll**. Agora que você sabe quais regras de segurança permitem ou negam o tráfego bidirecionalmente em uma VM, você pode determinar como resolver os problemas.

## <a name="view-details-of-a-security-rule"></a>Exibir os detalhes de uma regra de segurança

Para determinar por que as regras em [Usar verificação de fluxo de IP](#use-ip-flow-verify) permitem ou impedem a comunicação, examine as regras de segurança em vigor para o adaptador de rede com [az network nic list-effective-nsg](/cli/azure/network/nic#az_network_nic_list_effective_nsg):

```azurecli-interactive
az network nic list-effective-nsg \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

A saída retornada inclui o seguinte texto para a regra **AllowInternetOutbound** que permitiu o acesso de saída a www.bing.com em um passo anterior em [Usar verificação do fluxo IP](#use-ip-flow-verify):

```console
{
 "access": "Allow",
 "additionalProperties": {},
 "destinationAddressPrefix": "Internet",
 "destinationAddressPrefixes": [
  "Internet"
 ],
 "destinationPortRange": "0-65535",
 "destinationPortRanges": [
  "0-65535"
 ],
 "direction": "Outbound",
 "expandedDestinationAddressPrefix": [
  "1.0.0.0/8",
  "2.0.0.0/7",
  "4.0.0.0/6",
  "8.0.0.0/7",
  "11.0.0.0/8",
  "12.0.0.0/6",
  ...
 ],
 "expandedSourceAddressPrefix": null,
 "name": "defaultSecurityRules/AllowInternetOutBound",
 "priority": 65001,
 "protocol": "All",
 "sourceAddressPrefix": "0.0.0.0/0",
 "sourceAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "sourcePortRange": "0-65535",
 "sourcePortRanges": [
  "0-65535"
 ]
},
```

Você pode ver na saída anterior que **DestinationAddressPrefix** é **Internet**. Entretanto não está claro como 13.107.21.200 se relaciona com **Internet**. Você verá vários prefixos de endereços listados em **expandedDestinationAddressPrefix**. Um dos prefixos na lista é **12.0.0.0/6**, que abrange o intervalo 12.0.0.1-15.255.255.254 de endereços IP. Como 13.107.21.200 está dentro desse intervalo de endereços, a regra **AllowInternetOutBound** permite o tráfego de saída. Além disso, não existem regras de prioridade mais alta (número inferior) mostradas na saída anterior que substituem essa regra. Para negar a comunicação de saída a um endereço IP, você pode adicionar uma regra de segurança com uma prioridade mais alta, que nega a porta 80 de saída para o endereço IP.

Quando você executou o comando `az network watcher test-ip-flow` para testar a comunicação de saída para 172.131.0.100 em [Usar verificação de fluxo de IP](#use-ip-flow-verify), a saída informou que a regra **DefaultOutboundDenyAll** negou a comunicação. A regra **DefaultOutboundDenyAll** é equivalente à regra **DenyAllOutBound** listada na seguinte saída do comando `az network nic list-effective-nsg`:

```console
{
 "access": "Deny",
 "additionalProperties": {},
 "destinationAddressPrefix": "0.0.0.0/0",
 "destinationAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "destinationPortRange": "0-65535",
 "destinationPortRanges": [
  "0-65535"
 ],
 "direction": "Outbound",
 "expandedDestinationAddressPrefix": null,
 "expandedSourceAddressPrefix": null,
 "name": "defaultSecurityRules/DenyAllOutBound",
 "priority": 65500,
 "protocol": "All",
 "sourceAddressPrefix": "0.0.0.0/0",
 "sourceAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "sourcePortRange": "0-65535",
 "sourcePortRanges": [
  "0-65535"
 ]
}
```

A regra lista **0.0.0.0/0** como o **DestinationAddressPrefix**. A regra nega a comunicação de saída para 172.131.0.100, porque o endereço não está dentro do **destinationAddressPrefix** de uma das outras regras de saída na saída do comando `az network nic list-effective-nsg`. Para permitir a comunicação de saída, você pode adicionar uma regra de segurança de entrada com uma prioridade mais alta que permite o tráfego de entrada para a porta 80 em de 172.131.0.100.

Quando você executou o comando `az network watcher test-ip-flow` em [Usar verificação de fluxo IP](#use-ip-flow-verify) para testar a comunicação de entrada de 172.131.0.100, a saída informou que a regra **DefaultInboundDenyAll** negou a comunicação. A regra **DefaultInboundDenyAll** é equivalente à regra **DenyAllInBound** listada na seguinte saída do comando `az network nic list-effective-nsg`:

```console
{
 "access": "Deny",
 "additionalProperties": {},
 "destinationAddressPrefix": "0.0.0.0/0",
 "destinationAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "destinationPortRange": "0-65535",
 "destinationPortRanges": [
  "0-65535"
 ],
 "direction": "Inbound",
 "expandedDestinationAddressPrefix": null,
 "expandedSourceAddressPrefix": null,
 "name": "defaultSecurityRules/DenyAllInBound",
 "priority": 65500,
 "protocol": "All",
 "sourceAddressPrefix": "0.0.0.0/0",
 "sourceAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "sourcePortRange": "0-65535",
 "sourcePortRanges": [
  "0-65535"
 ]
},
```

A regra **DenyAllInBound** é aplicada, porque, conforme mostrado na saída, não há nenhuma outra regra de prioridade mais alta na saída do comando `az network nic list-effective-nsg` que permite a porta 80 de entrada para a VM de 172.131.0.100. Para permitir a comunicação de entrada, adicione uma regra de segurança de entrada com uma prioridade mais alta que permite a porta 80 de entrada de 172.131.0.100.

As verificações deste início rápido testaram a configuração do Azure. Se as verificações retornarem resultados esperados e você ainda tiver problemas de rede, verifique se você não tem um firewall entre a VM e o ponto de extremidade com o qual está se comunicando e se o sistema operacional na VM não tem um firewall que permite ou nega a comunicação.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, você poderá usar [az group delete](/cli/azure/group) para remover o grupo de recursos e todos os recursos que ele contém:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou uma VM e diagnosticou os filtros de tráfego de rede de entrada e saída. Você aprendeu que as regras do grupo de segurança de rede permitem ou negam o tráfego bidirecionalmente em uma VM. Saiba mais sobre [regras de segurança](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) e como [criar regras de segurança](../virtual-network/manage-network-security-group.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-security-rule).

Mesmo com os filtros de tráfego de rede apropriados em vigor, a comunicação com uma VM ainda poderá falhar, devido à configuração de roteamento. Para saber como diagnosticar problemas de roteamento de rede de VMs, consulte [Diagnosticar problemas de roteamento de VM](diagnose-vm-network-routing-problem-cli.md) ou, para diagnosticar problemas de roteamento de saída, latência e filtragem de tráfego, com uma única ferramenta, consulte [Solução de problemas de conexão](network-watcher-connectivity-cli.md).