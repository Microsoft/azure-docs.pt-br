---
title: Configurar o modo de distribuição do Azure Load Balancer
titlesuffix: Azure Load Balancer
description: Como configurar o modo de distribuição para o Azure Load Balancer dar suporte à afinidade do IP de origem.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 98fdf76dc2e1cb8171e7b0b37216d5f5405a1e6a
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275430"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Configurar o modo de distribuição para o Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="hash-based-distribution-mode"></a>Modo de distribuição baseado em hash

O modo de distribuição padrão para o Azure Load Balancer é um hash de 5 tuplas. A tupla é composta pelo IP de origem, porta de origem, IP de destino, porta de destino e tipo de protocolo. O hash é usado para mapear o tráfego para os servidores disponíveis e o algoritmo fornece adesão apenas dentro de uma sessão de transporte. Os pacotes que estão na mesma sessão são direcionados para a mesma instância do DIP (IP de datacenter) atrás do ponto de extremidade com balanceamento de carga. Quando o cliente inicia uma nova sessão por meio do mesmo IP de origem, a porta de origem é alterada e faz com que o tráfego vá para um ponto de extremidade DIP diferente.

![Modo de distribuição baseado em hash de 5 tuplas](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Modo de afinidade de IP de origem

O Load Balancer também pode ser configurado usando o modo de distribuição de afinidade do IP de origem. Esse modo de distribuição também é conhecido como afinidade de sessão ou afinidade do IP do cliente. O modo usa um hash de 2 tuplas (IP de origem e IP de destino) ou de 3 tuplas (IP de origem, IP de destino e tipo de protocolo) para mapear o tráfego para os servidores disponíveis. Ao usar a afinidade do IP de origem, as conexões iniciadas no mesmo computador cliente vão para o mesmo ponto de extremidade DIP.

A figura a seguir ilustra uma configuração de 2 tuplas. Observe como o de 2 tuplas é executado por meio do balanceador de carga para a VM1 (máquina virtual 1). O backup da VM1 é feito pela VM2 e VM3.

![Modo de distribuição de afinidade de sessão de 2 tuplas](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

A afinidade do IP de origem resolve uma incompatibilidade entre o Azure Load Balancer e o Gateway de Área de Trabalho Remota (Gateway de RD). Usando esse modo, é possível criar um farm de Gateway de Área de Trabalho Remota em um único serviço de nuvem.

Outro cenário de caso de uso é o upload de mídia. O upload de dados ocorre por meio de UDP, mas o plano de controle é obtido por meio de TCP:

* Um cliente inicia uma sessão TCP para o endereço público com balanceamento de carga e é direcionado para um DIP específico. O canal permanece ativo para monitorar a integridade da conexão.
* Uma nova sessão UDP do mesmo computador cliente é iniciada para o mesmo ponto de extremidade público com balanceamento de carga. A conexão é direcionada para o mesmo ponto de extremidade DIP que a conexão TCP anterior. O upload da mídia pode ser executado com alta taxa de transferência, mantendo um canal de controle por meio de TCP.

> [!NOTE]
> Quando um conjunto com balanceamento de carga for alterado, removendo ou adicionando uma máquina virtual, a distribuição de solicitações de cliente será recalculada. Não é possível depender de novas conexões de clientes existentes que terminam no mesmo servidor. Além disso, o uso do modo de distribuição de afinidade do IP de origem pode causar uma distribuição desigual de tráfego. Os clientes que executam atrás de proxies podem ser vistos como um aplicativo cliente exclusivo.

## <a name="configure-source-ip-affinity-settings"></a>Definir configurações de afinidade do IP de origem

Para máquinas virtuais implantadas com o Resource Manager, use o Azure PowerShell para alterar as configurações de distribuição do balanceador de carga em uma regra de balanceamento de carga existente. Isso atualiza o modo de distribuição: 

```powershell
$lb = Get-AzLoadBalancer -Name MyLb -ResourceGroupName MyLbRg
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

Para máquinas virtuais clássicas, use o Azure PowerShell para alterar as configurações de distribuição. Adicione um ponto de extremidade do Azure a uma máquina virtual e configure o modo de distribuição do balanceador de carga:

```powershell
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Definir o valor do elemento `LoadBalancerDistribution` para a quantidade desejada de balanceamento de carga. Especifique sourceIP para o balanceamento de carga de 2 tuplas (IP de origem e IP de destino). Especifique sourceIPProtocol para o balanceamento de carga de 3 tuplas (IP de origem, IP de destino e tipo de protocolo). Especifica nenhum para o comportamento padrão de balanceamento de carga de 5 tuplas.

Recupere uma configuração de modo de distribuição do balanceador de carga do ponto de extremidade usando estas configurações:

    PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

    VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
    LBSetName : MyLoadBalancedSet
    LocalPort : 80
    Name : HTTP
    Port : 80
    Protocol : tcp
    Vip : 65.52.xxx.xxx
    ProbePath :
    ProbePort : 80
    ProbeProtocol : tcp
    ProbeIntervalInSeconds : 15
    ProbeTimeoutInSeconds : 31
    EnableDirectServerReturn : False
    Acl : {}
    InternalLoadBalancerName :
    IdleTimeoutInMinutes : 15
    LoadBalancerDistribution : sourceIP

Quando o elemento `LoadBalancerDistribution` não está presente, o Azure Load Balancer usa o algoritmo padrão de 5 tuplas.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Configurar o modo de distribuição no conjunto do ponto de extremidade com balanceamento de carga

Quando os pontos de extremidade formarem parte de um conjunto de pontos de extremidade com balanceamento de carga, o modo de distribuição deverá ser configurado no conjunto de pontos de extremidade com balanceamento de carga:

```azurepowershell
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>Configurar o modo de distribuição para pontos de extremidade dos Serviços de Nuvem

Use o SDK do Azure para .NET 2.5 para atualizar seu serviço de nuvem. As configurações de ponto de extremidade para Serviços de Nuvem são feitas no arquivo .csdef. Para atualizar o modo de distribuição do balanceador de carga para uma implantação de Serviços de Nuvem, é necessária uma atualização da implantação.

Aqui está um exemplo de alterações .csdef para configurações do ponto de extremidade:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
    </Endpoints>
</WorkerRole>
<NetworkConfiguration>
    <VirtualNetworkSite name="VNet"/>
    <AddressAssignments>
<InstanceAddress roleName="VMRolePersisted">
    <PublicIPs>
    <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
    </PublicIPs>
</InstanceAddress>
    </AddressAssignments>
</NetworkConfiguration>
```

## <a name="api-example"></a>Exemplo de API

O exemplo a seguir mostra como reconfigurar o modo de distribuição do balanceador de carga para um conjunto com balanceamento de carga especificado em uma implantação. 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>Alterar o modo de distribuição para o conjunto com balanceamento de carga implantado

Use o modelo de implantação clássico do Azure para alterar uma configuração de implantação existente. Adicione o cabeçalho `x-ms-version` e defina o valor para a versão 2014-09-01 ou posterior.

#### <a name="request"></a>Solicitação

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
    Content-Type: application/xml

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName>
        <LocalPort> local-port-number </LocalPort>
        <Port> external-port-number </Port>
        <LoadBalancerProbe>
          <Port> port-assigned-to-probe </Port>
          <Protocol> probe-protocol </Protocol>
          <IntervalInSeconds> interval-of-probe </IntervalInSeconds>
          <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds>
        </LoadBalancerProbe>
        <Protocol> endpoint-protocol </Protocol>
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn>
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes>
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution>
      </InputEndpoint>
    </LoadBalancedEndpointList>

Conforme descrito anteriormente, defina o elemento `LoadBalancerDistribution` como sourceIP para afinidade de 2 tuplas, sourceIPProtocol para afinidade de 3 tuplas ou nenhum para nenhuma afinidade (afinidade de 5 tuplas).

#### <a name="response"></a>Response

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Próximas etapas

* [Visão geral do Azure Load Balancer interno](load-balancer-internal-overview.md)
* [Introdução à configuração de um balanceador de carga para a Internet](load-balancer-get-started-internet-arm-ps.md)
* [Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)
