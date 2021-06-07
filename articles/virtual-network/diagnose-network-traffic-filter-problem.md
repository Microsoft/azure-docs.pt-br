---
title: Diagnosticar um problema de filtro de tráfego de rede de máquina virtual | Microsoft Docs
description: Saiba como diagnosticar um problema de filtro de tráfego de rede de máquina virtual ao exibir as regras de segurança efetiva para uma máquina virtual.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: a54feccf-0123-4e49-a743-eb8d0bdd1ebc
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2018
ms.author: kumud
ms.openlocfilehash: 73562d8d32f265fa43ca80d2f8d4f84b1b631ec6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98223662"
---
# <a name="diagnose-a-virtual-machine-network-traffic-filter-problem"></a>Diagnosticar um problema de filtro de tráfego de rede de máquina virtual

Neste artigo, você aprenderá a diagnosticar um problema de filtro de tráfego de rede ao exibir as regras de segurança de grupo (NSG) de segurança de rede que são eficazes para uma máquina virtual (VM).

NSGs permitem que você controle os tipos de tráfego de fluxo para dentro e fora de uma VM. Você pode associar um NSG a uma sub-rede em uma rede virtual do Azure, uma interface de rede conectada a uma VM ou ambos. As regras de segurança efetivas aplicadas a uma interface de rede são uma agregação das regras existentes no NSG associado a uma interface de rede e a sub-rede na qual a interface de rede está inserida. As regras em diferentes NSGs podem, às vezes, entrar em conflito entre si e afetar a conectividade de rede da VM. Você pode visualizar todas as regras de segurança efetivas dos NSGs aplicados nas interfaces de rede da VM. Se você não estiver familiarizado com os conceitos de rede virtual, interface de rede ou NSG, consulte [ Visão geral da rede virtual ](virtual-networks-overview.md), [ Interface de rede ](virtual-network-network-interface.md) e [ Visão geral dos grupos de segurança de rede ](./network-security-groups-overview.md).

## <a name="scenario"></a>Cenário

Você tenta se conectar a uma VM pela porta 80 da internet, mas a conexão falha. Para determinar por que você não pode acessar a porta 80 da Internet, é possível visualizar as regras de segurança eficazes para uma interface de rede usando o [ Portal do Azure ](#diagnose-using-azure-portal), [ PowerShell ](#diagnose-using-powershell) ou o [ CLI do Azure ](#diagnose-using-azure-cli).

As etapas a seguir supõem que você tenha uma VM existente para visualizar as regras de segurança efetivas. Se você não tiver uma VM existente, primeiro implemente uma VM [ Linux ](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [ Windows ](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para concluir as tarefas deste artigo. Os exemplos neste artigo são para uma VM nomeada *myVM* com um adaptador de rede nomeado *myVMVMNic*. A VM e o adaptador de rede estão em um grupo de recursos nomeado *myResourceGroup*, e estão na região *Leste dos EUA*. Altere os valores nas etapas, conforme apropriado, para a VM em que você diagnosticando o problema.

## <a name="diagnose-using-azure-portal"></a>Diagnosticar usando o portal do Azure

1. Faça logon no [portal do Azure](https://portal.azure.com) com uma conta do Azure que tenha as [permissões necessárias](virtual-network-network-interface.md#permissions).
2. Na parte superior do portal do Azure, insira o nome da VM na caixa de pesquisa. Quando o nome da VM aparecer nos resultados da pesquisa, selecione-o.
3. Em **configurações**, selecione **rede**, conforme mostrado na figura a seguir:

   ![Captura de tela mostra a portal do Azure com as configurações de rede da minha NIC V M V M.](./media/diagnose-network-traffic-filter-problem/view-security-rules.png)

   As regras que você consulte listadas na figura anterior são para uma interface de rede denominada **myVMVMNic**. É possível ver que há **REGRAS DE PORTA DE ENTRADA** para o adaptador de rede de dois grupos de segurança de rede diferentes:
   
   - **mySubnetNSG**: associado à sub-rede na qual o adaptador de rede está.
   - **myVMNSG**: associado ao adaptador de rede na VM nomeada **myVMVMNic**.

   A regra nomeada **DenyAllInBound** é o que está impedindo a comunicação de entrada para a máquina virtual pela porta 80, da internet, conforme descrito no [cenário](#scenario). As listas de regra *0.0.0.0/0* para **fonte**, que inclui a internet. Nenhuma outra regra com uma prioridade mais alta (número menor) permite que a porta 80 entrada. Para permitir que a porta 80 para a máquina virtual de entrada da internet, consulte [resolver um problema](#resolve-a-problem). Para saber mais sobre as regras de segurança e como o Azure aplica-las, consulte [grupos de segurança de rede](./network-security-groups-overview.md).

   Na parte inferior da imagem, você também ver **as regras de porta de saída**. Em que são as regras de porta de saída para a interface de rede. Embora a imagem mostra apenas quatro regras de entrada para cada NSG, seus NSGs podem ter muitas regras mais de quatro. Na imagem, você deve ver **VirtualNetwork** em **fonte** e **destino** e **AzureLoadBalancer** em **ORIGEM**. **VirtualNetwork** e **AzureLoadBalancer** são [marcas de serviço](./network-security-groups-overview.md#service-tags). As tags de serviço representam um grupo de prefixos de endereço IP para ajudar a minimizar a complexidade da criação de regras de segurança.

4. Certifique-se de que a VM está em execução, estada e, em seguida, selecione **regras de segurança efetiva**, conforme mostrado na figura anterior, para ver as regras de segurança efetiva, mostradas na figura a seguir:

   ![Captura de tela mostra o painel de regras de segurança em vigor com o download selecionado e a regra de entrada AllowAzureLoadBalancerInbound selecionada.](./media/diagnose-network-traffic-filter-problem/view-effective-security-rules.png)

   As regras listadas são o mesmo que você viu no passo 3, embora existam em diferentes guias para o NSG associado à interface de rede e a sub-rede. Como você pode ver na imagem, somente as primeiras 50 regras são mostradas. Para fazer o download de um arquivo. csv que contém todas as regras, selecione **baixar**.

   Para ver quais prefixos representam cada tag de serviço, selecione uma regra, como a regra denominada **AllowAzureLoadBalancerInbound**. A figura a seguir mostra os prefixos da tag de serviço **AzureLoadBalancer**:

   ![A captura de tela mostra os prefixos de endereço para AllowAzureLoadBalancerInbound inseridos.](./media/diagnose-network-traffic-filter-problem/address-prefixes.png)

   Embora o **AzureLoadBalancer** etiqueta de serviço representa apenas um prefixo, outras marcas de serviço representam vários prefixos.

5. As etapas anteriores mostraram as regras de segurança para uma interface de rede denominado **myVMVMNic**, mas você viu também uma interface de rede denominada **myVMVMNic2** em algumas das imagens anteriores. A VM neste exemplo tem duas interfaces de rede conectados a ele. As regras de segurança efetivo podem ser diferentes para cada interface de rede.

   Para ver as regras para a interface de rede **myVMVMNic2**, selecione-a. Conforme mostrado na imagem a seguir, a interface de rede tem as mesmas regras associadas à sua sub-rede do **myVMVMNic** interface de rede porque ambas as interfaces de rede estão na mesma sub-rede. Quando você associa um NSG a uma sub-rede, suas regras são aplicadas a todas as interfaces de rede na sub-rede.

   ![Captura de tela mostra a portal do Azure com as configurações de rede para a minha NIC V M V M 2.](./media/diagnose-network-traffic-filter-problem/view-security-rules2.png)

   Ao contrário de **myVMVMNic** interface de rede, o **myVMVMNic2** interface de rede não tem um grupo de segurança de rede associado a ele. Cada interface de rede e sub-rede podem ter zero ou um NSG associado a ele. O NSG associado a cada interface de rede ou sub-rede pode ser o mesmo ou diferente. Você pode associar o mesmo grupo de segurança de rede a quantas interfaces de rede e sub-redes desejar.

Embora as regras de segurança eficazes tenham sido visualizadas na VM, também é possível exibir regras de segurança efetivas por meio de um:
- **Adaptador de rede**: Saiba como [exibir um adaptador de rede](virtual-network-network-interface.md#view-network-interface-settings).
- **NSG**: Saiba como [exibir um NSG](manage-network-security-group.md#view-details-of-a-network-security-group).

## <a name="diagnose-using-powershell"></a>Diagnosticar usando o PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

É possível executar os comandos a seguir no [Azure Cloud Shell](https://shell.azure.com/powershell) ou executando o PowerShell no computador. O Azure Cloud Shell é um shell interativo gratuito. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. Se você executar o PowerShell do seu computador, precisará do módulo Azure PowerShell, versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az` no computador para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar `Connect-AzAccount` para fazer logon no Azure com uma conta que tenha as [permissões necessárias](virtual-network-network-interface.md#permissions)].

Obtenha as regras de segurança efetivas para uma interface de rede com [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup). O exemplo a seguir obtém as regras de segurança efetivas para uma interface de rede denominada *myVMVMNic*, que está em um grupo de recursos denominado *myResourceGroup*:

```azurepowershell-interactive
Get-AzEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup
```

Saída é retornada no formato json. Para reconhecimento da saída, consulte [ interpretar a saída do comando ](#interpret-command-output).
Saída é retornada somente se um NSG está associado com a interface de rede, a sub-rede em que da interface de rede está ou ambos. A máquina virtual deve estar no estado de execução. Uma VM pode ter várias interfaces de rede com diferentes NSGs aplicados. Ao solucionar problemas, execute o comando para cada interface de rede.

Se você ainda estiver com problemas de conectividade, veja [ considerações adicionais sobre ](#additional-diagnosis) e [ diagnósticos ](#considerations).

Se você não souber o nome de um adaptador de rede, mas souber o nome da VM à qual o adaptador de rede está conectado, os comandos a seguir retornarão as IDs de todos os adaptadores de rede conectados a uma VM:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Você receberá uma saída semelhante ao exemplo a seguir:

```output
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

Na saída anterior, o nome do adaptador de rede é *myVMVMNic*.

## <a name="diagnose-using-azure-cli"></a>Diagnosticar usando a CLI do Azure

Se usar os comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, execute os comandos no [Azure Cloud Shell](https://shell.azure.com/bash) ou então executando a CLI do computador. Este artigo requer a CLI do Azure versão 2.0.32 ou posterior. Execute `az --version` para localizar a versão instalada. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se você estiver executando a CLI do Azure localmente, também precisará executar `az login` e fazer logon no Azure com uma conta que tenha as [permissões necessárias](virtual-network-network-interface.md#permissions).

Obtenha as regras de segurança efetivas para uma interface de rede com [ az network nic list-effective-nsg ](/cli/azure/network/nic#az-network-nic-list-effective-nsg). O exemplo a seguir obtém as regras de segurança em vigor para uma interface de rede chamada *myVMVMNic* que está em um grupo de recursos chamado *MyResource* Group:

```azurecli-interactive
az network nic list-effective-nsg \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

Saída é retornada no formato json. Para reconhecimento da saída, consulte [ interpretar a saída do comando ](#interpret-command-output).
Saída é retornada somente se um NSG está associado com a interface de rede, a sub-rede em que da interface de rede está ou ambos. A máquina virtual deve estar no estado de execução. Uma VM pode ter várias interfaces de rede com diferentes NSGs aplicados. Ao solucionar problemas, execute o comando para cada interface de rede.

Se você ainda estiver com problemas de conectividade, veja [ considerações adicionais sobre ](#additional-diagnosis) e [ diagnósticos ](#considerations).

Se você não souber o nome de um adaptador de rede, mas souber o nome da VM à qual o adaptador de rede está conectado, os comandos a seguir retornarão as IDs de todos os adaptadores de rede conectados a uma VM:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

Dentro de saída retornados, você verá informações semelhantes ao exemplo a seguir:

```output
"networkProfile": {
    "additionalProperties": {},
    "networkInterfaces": [
      {
        "additionalProperties": {},
        "id": "/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
        "primary": true,
        "resourceGroup": "myResourceGroup"
      },
```

Na saída anterior, o nome da interface de rede é *myVMVMNic interface*.

## <a name="interpret-command-output"></a>Interpretar a saída de comando

Independentemente de se você usou o [PowerShell](#diagnose-using-powershell), ou o [CLI do Azure](#diagnose-using-azure-cli) para diagnosticar o problema, você recebe uma resposta que contém as seguintes informações:

- **NetworkSecurityGroup**: o ID do grupo de segurança de rede.
- **Associação**: se o grupo de segurança de rede está associado a um *NetworkInterface* ou *sub-rede*. Se um NSG está associado a ambos, a saída é retornada com **NetworkSecurityGroup**, **associação**, e **EffectiveSecurityRules**, para cada NSG. Se o NSG estiver associado ou desassociado imediatamente antes de executar o comando para visualizar as regras de segurança efetivas, talvez seja necessário aguardar alguns segundos para que a alteração reflita na saída do comando.
- **EffectiveSecurityRules**: obter uma explicação de cada propriedade é detalhada na [criar uma regra de segurança](manage-network-security-group.md#create-a-security-rule). Regra nomes precedidos de *defaultSecurityRules /* são padrão regras de segurança que existem em cada NSG. Regra nomes precedidos de *securityRules /* são regras que você criou. Regras que especificam um [serviço marca](./network-security-groups-overview.md#service-tags), como **Internet**, **VirtualNetwork**, e **AzureLoadBalancer** para o **destinationAddressPrefix** ou **sourceAddressPrefix** propriedades, também têm valores para o **expandedDestinationAddressPrefix** propriedade. O **expandedDestinationAddressPrefix** listas de propriedades de todos os prefixos de endereço representados pela marca de serviço.

Se você vir regras duplicadas listadas na saída, é porque um NSG está associado à interface de rede e a sub-rede. Ambos os NSGs têm as mesmas regras padrão e podem ter regras duplicadas adicionais, se você tiver criado suas próprias regras que são os mesmos em ambos os NSGs.

A regra denominada **defaultSecurityRules / DenyAllInBound** é o que está impedindo a comunicação de entrada para a VM pela porta 80, da Internet, conforme descrito no [cenário](#scenario). Nenhuma outra regra com prioridade mais alta (número menor) permite a entrada da porta 80 a partir da Internet.

## <a name="resolve-a-problem"></a>Resolver um problema

Se você usar o Azure [portal](#diagnose-using-azure-portal), [PowerShell](#diagnose-using-powershell), ou o [CLI do Azure](#diagnose-using-azure-cli) para diagnosticar o problema apresentado a [cenário](#scenario) neste artigo, a solução é criar uma regra de segurança de rede com as seguintes propriedades:

| Propriedade                | Valor                                                                              |
|---------                |---------                                                                           |
| Fonte                  | Qualquer                                                                                |
| Intervalos de portas de origem      | Qualquer                                                                                |
| Destino             | O endereço IP da VM, um intervalo de endereços IP ou todos os endereços na sub-rede. |
| Intervalos de portas de destino | 80                                                                                 |
| Protocolo                | TCP                                                                                |
| Ação                  | Allow                                                                              |
| Prioridade                | 100                                                                                |
| Nome                    | Permitir-HTTP-All                                                                     |

Depois de criar a regra, a porta 80 é permitida de entrada da internet, como a prioridade da regra é maior do que a regra de segurança padrão chamada *DenyAllInBound*, que impede que o tráfego. Saiba como [ criar uma regra de segurança ](manage-network-security-group.md#create-a-security-rule). Se os NSGs diferentes estão associados a interface de rede e a sub-rede, você deve criar a mesma regra em ambos os NSGs.

Quando o tráfego de entrada de processos do Azure, ele processa as regras no NSG associado à sub-rede (se houver um NSG associado) e, em seguida, ele processa as regras no NSG associado à interface de rede. Se houver um NSG associado para a interface de rede e a sub-rede, a porta deve estar aberta em ambos os NSGs, para o tráfego para chegar à VM. Para facilitar os problemas de administração e comunicação, recomendamos que você associe um NSG a uma sub-rede, em vez de interfaces de rede individuais. Se precisam de VMs em uma sub-rede diferentes regras de segurança, você pode fazer a rede membros de interfaces de um grupo de segurança do aplicativo (ASG) e especifique um ASG como origem e destino de uma regra de segurança. Saiba mais sobre [ grupos de segurança de aplicativos ](./network-security-groups-overview.md#application-security-groups).

Se ainda houver problemas de comunicação, confira [Considerações](#considerations) e Diagnóstico adicional.

## <a name="considerations"></a>Considerações

Considere os seguintes pontos ao solucionar problemas de conectividade:

* As regras de segurança padrão bloqueiam o acesso de entrada da Internet e permitem somente o tráfego de entrada da rede virtual. Para permitir o tráfego de entrada da Internet, adicione regras de segurança com uma prioridade mais alta que as regras padrão. Saiba mais sobre [padrão de regras de segurança](./network-security-groups-overview.md#default-security-rules), ou como [adicionar uma regra de segurança](manage-network-security-group.md#create-a-security-rule).
* Se você observou as redes virtuais, por padrão, a tag de serviço **VIRTUAL_NETWORK** expande automaticamente para incluir prefixos para redes virtuais emparelhadas. Para solucionar quaisquer problemas relacionados ao emparelhamento de rede virtual, você pode exibir os prefixos na lista **ExpandedAddressPrefix**. Saiba mais sobre as [ redes de emparelhamento ](virtual-network-peering-overview.md) e [ de serviço de rede virtual ](./network-security-groups-overview.md#service-tags).
* Regras de segurança efetivo somente são mostradas para uma interface de rede, se houver um NSG associados a interface de rede da VM e, ou, sub-rede, e se a VM está em estado de execução.
* Se não houver NSGs associados à interface de rede ou à sub-rede e você tiver um [ endereço IP público ](virtual-network-public-ip-address.md) atribuído a uma VM, todas as portas estarão abertas para acesso de entrada e saída para qualquer lugar. Se a VM tem um endereço IP público, é recomendável aplicar um NSG à sub-rede da interface de rede.

## <a name="additional-diagnosis"></a>Diagnóstico adicional

* Para executar um teste rápido para determinar se o tráfego é permitido para ou de uma VM, use o [Verifique se o fluxo IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) capacidade do Observador de rede do Azure. Fluxo IP verificar informa se o tráfego é permitido ou negado. Se negado, a verificação de fluxo de IP informa qual regra de segurança está negando o tráfego.
* Se não houver regras de segurança que façam com que a conectividade de rede da VM falhe, o problema pode ser devido a:
  * Software de firewall em execução no sistema operacional da VM
  * Rotas configuradas para soluções de virtualização ou tráfego local. O tráfego da Internet pode ser redirecionado para sua rede local por meio de [ encapsulamento forçado ](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se você forçar o tráfego de internet de túnel em um dispositivo virtual ou no local, você não poderá se conectar à VM da internet. Para saber como diagnosticar problemas de rota que podem impedir o fluxo de tráfego de VM, consulte [diagnosticar um problema de roteamento do tráfego de rede de máquina virtual](diagnose-network-routing-problem.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre todas as tarefas, propriedades e configurações para um [grupo de segurança de rede](manage-network-security-group.md#work-with-network-security-groups) e [as regras de segurança](manage-network-security-group.md#work-with-security-rules).
- Saiba mais sobre [padrão de regras de segurança](./network-security-groups-overview.md#default-security-rules), [serviço marcas](./network-security-groups-overview.md#service-tags), e [como o Azure processa as regras de segurança para o tráfego de entrada e saída](./network-security-groups-overview.md#network-security-groups) para uma máquina virtual.