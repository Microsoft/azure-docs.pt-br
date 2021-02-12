---
title: Solucionar erros de gateway inválidos-Aplicativo Azure gateway
description: 'Saiba como solucionar problemas de erro do servidor do gateway de aplicativo: 502-o servidor Web recebeu uma resposta inválida ao atuar como um gateway ou servidor proxy.'
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: d44f9109540c3899ab50bd5c4c02afa19045bafb
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182930"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Solução de problemas de erros de gateway incorreto no Application Gateway

Saiba como solucionar problemas de erros de gateway inválidos (502) recebidos ao usar o gateway de Aplicativo Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Visão geral

Depois de configurar um gateway de aplicativo, um dos erros que você pode ver é "erro de servidor: 502-o servidor Web recebeu uma resposta inválida ao atuar como um gateway ou servidor proxy". Esse erro pode ocorrer pelos seguintes motivos principais:

* NSG, UDR ou DNS personalizado está bloqueando o acesso aos membros do pool de back-end.
* As VMs de back-end ou as instâncias do conjunto de dimensionamento de máquinas virtuais não estão respondendo à investigação de integridade padrão.
* Configuração inválida ou incorreta de investigações de integridade personalizadas.
* O pool de [back-end do Gateway aplicativo Azure não está configurado ou vazio](#empty-backendaddresspool).
* Nenhuma das VMs ou instâncias no [conjunto de dimensionamento de máquinas virtuais é íntegra](#unhealthy-instances-in-backendaddresspool).
* [Tempo limite de solicitação ou problemas de conectividade](#request-time-out) com solicitações do usuário.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Problema com o Grupo de Segurança de Rede, com a Rota definida pelo usuário ou com o DNS personalizado

### <a name="cause"></a>Causa

Se o acesso ao back-end for bloqueado por causa de um DNS NSG, UDR ou personalizado, as instâncias do gateway de aplicativo não poderão alcançar o pool de back-end. Isso causa falhas de investigação, resultando em erros 502.

O NSG/UDR pode estar presente na sub-rede do gateway de aplicativo ou na sub-rede em que as VMs de aplicativo são implantadas.

Da mesma forma, a presença de um DNS personalizado na VNet também poderia causar problemas. Um FQDN usado para membros do pool de back-end pode não ser resolvido corretamente pelo servidor DNS configurado pelo usuário para a VNet.

### <a name="solution"></a>Solução

Valide a configuração de DNS, UDR e NSG realizando as seguintes etapas:

* Verifique NSGs associado à sub-rede do gateway de aplicativo. Verifique se a comunicação com o back-end não está bloqueada.
* Verifique UDR associado à sub-rede do gateway de aplicativo. Verifique se o UDR não está direcionando o tráfego para fora da sub-rede de back-end. Por exemplo, verifique o roteamento para dispositivos virtuais de rede ou rotas padrão sendo anunciadas para a sub-rede do gateway de aplicativo via ExpressRoute/VPN.

```azurepowershell
$vnet = Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Verifique se o NSG e a rota com a VM de back-end estão funcionando corretamente

```azurepowershell
Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* Verifique se há um DNS personalizado na VNet. O DNS pode ser verificado examinando os detalhes das propriedades da VNet na saída.

```json
Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
Se estiver presente, verifique se o servidor DNS pode resolver corretamente o FQDN do membro do pool de back-end.

## <a name="problems-with-default-health-probe"></a>Problemas com a investigação de integridade padrão

### <a name="cause"></a>Causa

502 erros também podem ser indicadores frequentes que a investigação de integridade padrão não pode alcançar VMs de back-end.

Quando uma instância do gateway de aplicativo é provisionada, ela configura automaticamente uma investigação de integridade padrão para cada BackendAddressPool usando as propriedades de BackendHttpSetting. Nenhuma entrada do usuário é necessária para definir essa investigação. Especificamente, quando uma regra de balanceamento de carga é configurada, uma associação é feita entre um BackendHttpSetting e um BackendAddressPool. Uma investigação padrão é configurada para cada uma dessas associações e o gateway de aplicativo inicia uma conexão de verificação de integridade periódica para cada instância no BackendAddressPool na porta especificada no elemento BackendHttpSetting. 

A tabela a seguir lista os valores associados à investigação de integridade padrão:

| Propriedades da investigação | Valor | Descrição |
| --- | --- | --- |
| URL de investigação |`http://127.0.0.1/` |Caminho da URL |
| Intervalo |30 |Intervalo da investigação em segundos |
| Tempo limite |30 |Tempo limite da investigação em segundos |
| Limite não íntegro |3 |Contagem de repetições da investigação. O servidor de back-end é marcado após a contagem de falhas de investigação consecutivas atingir o limite de não íntegro. |

### <a name="solution"></a>Solução

* Verifique se um site padrão está configurado e está escutando em 127.0.0.1.
* Se BackendHttpSetting especificar uma porta diferente de 80, o site padrão deverá ser configurado para escutar nessa porta.
* A chamada para `http://127.0.0.1:port` deve retornar um código de resultado de HTTP 200. Isso deve ser retornado dentro do período de tempo limite de 30 segundos.
* Verifique se a porta configurada está aberta e se não há regras de firewall ou grupos de segurança de rede do Azure, que bloqueiam o tráfego de entrada ou saída na porta configurada.
* Se as VMs clássicas do Azure ou o serviço de nuvem forem usados com um FQDN ou um IP público, verifique se o [ponto de extremidade](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints?toc=%2fazure%2fapplication-gateway%2ftoc.json) correspondente está aberto.
* Se a VM estiver configurada via Azure Resource Manager e estiver fora da VNet em que o gateway de aplicativo está implantado, um [grupo de segurança de rede](../virtual-network/network-security-groups-overview.md) deverá ser configurado para permitir o acesso na porta desejada.

## <a name="problems-with-custom-health-probe"></a>Problemas com a investigação de integridade personalizada

### <a name="cause"></a>Causa

Investigações de integridade personalizadas oferecem flexibilidade adicional para o comportamento de investigação padrão. Ao usar investigações personalizadas, você pode configurar o intervalo de investigação, a URL, o caminho a ser testado e quantas respostas com falha aceitar antes de marcar a instância do pool de back-end como não íntegra.

As seguintes propriedades adicionais são adicionadas:

| Propriedades da investigação | Descrição |
| --- | --- |
| Nome |O nome da investigação. Este é o nome usado para se referir à investigação nas configurações de HTTP de back-end. |
| Protocolo |O protocolo usado para enviar a investigação. A investigação usa o protocolo definido nas configurações de HTTP do back-end |
| Host |O nome do host para enviar a investigação. Aplicável somente quando vários sites estão configurados no gateway de aplicativo. Isso é diferente do nome de host de VM. |
| Caminho |O caminho relativo da investigação. Um caminho válido começa com '/'. A investigação é enviada para \<protocol\> :// \<host\> :\<port\>\<path\> |
| Intervalo |Intervalo de investigação em segundos. Este é o intervalo de tempo entre duas investigações consecutivas. |
| Tempo limite |Tempo limite da investigação em segundos. Se uma resposta válida não for recebida nesse período limite, a investigação será marcada como com falha. |
| Limite não íntegro |Contagem de repetições da investigação. O servidor de back-end é marcado após a contagem de falhas de investigação consecutivas atingir o limite de não íntegro. |

### <a name="solution"></a>Solução

Valide se a Investigação de Integridade Personalizada está configurada corretamente conforme a tabela anterior. Além das etapas de solução de problemas anteriores, também verifique o seguinte:

* Verifique se a investigação foi especificada corretamente, conforme o [guia](application-gateway-create-probe-ps.md).
* Se o gateway de aplicativo estiver configurado para um único site, por padrão, o nome do host deverá ser especificado como `127.0.0.1` , a menos que configurado de outra forma na investigação personalizada.
* Certifique-se de que uma chamada para http:// \<host\> : \<port\> \<path\> retorna um código de resultado http de 200.
* Certifique-se de que intervalo, tempo limite e UnhealtyThreshold estão dentro dos intervalos aceitáveis.
* Se usar uma investigação HTTPS, certifique-se de que o servidor de back-end não requer o SNI, configurando para isso um certificado fallback no próprio servidor de back-end.

## <a name="request-time-out"></a>Tempo limite de solicitação

### <a name="cause"></a>Causa

Quando uma solicitação de usuário é recebida, o gateway de aplicativo aplica as regras configuradas à solicitação e a roteia para uma instância de pool de back-end. Ele aguarda por um intervalo de tempo configurável para receber uma resposta da instância de back-end. Por padrão, esse intervalo é de **20** segundos. Se o gateway de aplicativo não receber uma resposta do aplicativo de back-end nesse intervalo, a solicitação do usuário receberá um erro 502.

### <a name="solution"></a>Solução

O gateway de aplicativo permite que você defina essa configuração por meio do BackendHttpSetting, que pode ser aplicado a diferentes pools. Diferentes pools de back-end podem ter BackendHttpSetting diferentes e um tempo limite de solicitação diferente configurado.

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>BackendAddressPool vazio

### <a name="cause"></a>Causa

Se o gateway de aplicativo não tiver VMs ou um conjunto de dimensionamento de máquinas virtuais configurado no pool de endereços de back-end, ele não poderá rotear nenhuma solicitação de cliente e enviará um erro de gateway inadequado.

### <a name="solution"></a>Solução

Verifique se o pool de endereços de back-end não está vazio. Isso pode ser feito por meio do PowerShell, da CLI ou do portal.

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

A saída do cmdlet anterior deve conter o pool de endereços de back-end não vazio. O exemplo a seguir mostra dois pools retornados que são configurados com um FQDN ou endereços IP para as VMs de back-end. O estado de provisionamento de BackendAddressPool deve ser 'Bem-sucedido'.

BackendAddressPoolsText:

```json
[{
    "BackendAddresses": [{
        "ipAddress": "10.0.0.10",
        "ipAddress": "10.0.0.11"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool01",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
}, {
    "BackendAddresses": [{
        "Fqdn": "xyx.cloudapp.net",
        "Fqdn": "abc.cloudapp.net"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool02",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
}]
```

## <a name="unhealthy-instances-in-backendaddresspool"></a>Instâncias não íntegras em BackendAddressPool

### <a name="cause"></a>Causa

Se todas as instâncias de BackendAddressPool não estiverem íntegras, o gateway de aplicativo não terá nenhum back-end para rotear a solicitação do usuário para. Esse também pode ser o caso quando as instâncias de back-end estão íntegras, mas não têm o aplicativo necessário implantado.

### <a name="solution"></a>Solução

Verifique se as instâncias estão íntegras e se o aplicativo está configurado corretamente. Verifique se as instâncias de back-end podem responder a um ping de outra VM na mesma VNet. Se configurado com um ponto de extremidade público, verifique se uma solicitação de navegador para o aplicativo Web é de serviço.

## <a name="next-steps"></a>Próximas etapas

Se as etapas anteriores não resolverem o problema, abra um [tíquete de suporte](https://azure.microsoft.com/support/options/).