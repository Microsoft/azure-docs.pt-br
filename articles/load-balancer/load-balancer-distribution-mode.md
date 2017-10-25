---
title: "Configurar modo de distribuição do Balanceador de Carga | Microsoft Docs"
description: "Como configurar o modo de distribuição do balanceador de carga do Azure para dar suporte à afinidade de IP de origem"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: 7df27a4d-67a8-47d6-b73e-32c0c6206e6e
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: a6b3c346358e0aed4c60c4903932236edc237379
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-the-distribution-mode-for-load-balancer"></a>Configurar modo de distribuição para o balanceador de carga

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

## <a name="hash-based-distribution-mode"></a>Modo de distribuição baseado em hash

O algoritmo de distribuição padrão é um hash de 5 tuplas (IP de origem, porta de origem, IP de destino, porta de destino, tipo de protocolo) para mapear o tráfego até os servidores disponíveis. Ele fornece permanência somente dentro de uma sessão de transporte. Os pacotes na mesma sessão serão direcionados para a mesma instância do DIP (IP de Datacenter) atrás do ponto de extremidade com balanceamento de carga. Quando o cliente inicia uma nova sessão por meio do mesmo IP de origem, a porta de origem é alterada e faz com que o tráfego vá para um ponto de extremidade DIP diferente.

![balanceador de carga baseado em hash](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

Figura 1 – Distribuição das 5 tuplas

## <a name="source-ip-affinity-mode"></a>Modo de afinidade de IP de origem

Temos outro modo de distribuição chamado Afinidade de IP de origem (também conhecido como afinidade de sessão ou afinidade de IP do cliente). O Azure Load Balancer pode ser configurado para usar 2 tuplas (IP de origem, IP de destino) ou 3 tuplas (IP de origem, IP de destino, Protocolo) para mapear o tráfego até os servidores disponíveis. Ao usar a afinidade de IP de origem, as conexões iniciadas no mesmo computador cliente vão para o mesmo ponto de extremidade DIP.

O diagrama a seguir ilustra uma configuração de 2 tuplas. Observe como a tupla 2 é executada por meio do balanceador de carga para a VM1 (máquina virtual 1) cujo backup é feito pela VM2 e pela VM3.

![afinidade de sessão](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Figura 2 – Distribuição das 2 tuplas

A afinidade de IP de origem resolve uma incompatibilidade entre o Azure Load Balancer e o Gateway RD (Área de Trabalho Remota). Agora é possível criar um farm de gateway de Área de Trabalho Remota em um único serviço de nuvem.

Outro cenário de caso de uso é o upload de mídia em que o upload de dados ocorre por meio de UDP, mas o plano de controle é obtido por meio de TCP:

* Primeiro, um cliente inicia uma sessão TCP com o endereço público do balanceamento de carga e é direcionado para um DIP específico; esse canal permanece ativo para monitorar a integridade da conexão
* Uma nova sessão UDP no mesmo computador cliente é iniciada para o mesmo ponto de extremidade público de balanceamento de carga. A expectativa aqui é que essa conexão seja também direcionada para o mesmo ponto de extremidade DIP que a conexão TCP anterior, para que o carregamento da mídia possa ser executado em alta taxa de transferência enquanto mantém um canal de controle pelo TCP.

> [!NOTE]
> Quando um conjunto de balanceamento de carga for alterado (removendo ou adicionando uma máquina virtual), a distribuição de solicitações de cliente será recalculada. Não é possível depender de novas conexões de clientes existentes que terminam no mesmo servidor. Além disso, o uso do modo de distribuição de afinidade do IP de origem pode causar uma distribuição desigual de tráfego. Clientes que executam proxies subjacentes podem ser vistos como um aplicativo cliente exclusivo.

## <a name="configuring-source-ip-affinity-settings-for-load-balancer"></a>Definindo configurações de afinidade de IP de origem para o balanceador de carga

Para máquinas virtuais, é possível usar o PowerShell para alterar as configurações de tempo limite:

Adicione um ponto de extremidade do Azure a uma máquina virtual e defina o modo de distribuição do balanceador de carga

```powershell
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

O LoadBalancerDistribution poderá ser definido como sourceIP para balanceamento de carga de 2 tuplas (IP de origem, IP de destino), sourceIPProtocol para balanceamento de carga de 3 tuplas (IP de destino, IP de origem, protocolo) ou nenhum se você quiser o comportamento padrão de balanceamento de carga de 5 tuplas.

Use o seguinte para recuperar uma configuração de modo de distribuição do balanceador de carga do ponto de extremidade:

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

Se o elemento LoadBalancerDistribution não estiver presente, o balanceador de carga do Azure usará o algoritmo padrão de tupla 5.

### <a name="set-the-distribution-mode-on-a-load-balanced-endpoint-set"></a>Defina o modo de distribuição em um conjunto de pontos de extremidade com balanceamento de carga

Se os pontos de extremidade forem parte de um conjunto de pontos de extremidade de balanceamento de carga, o modo de distribuição deverá ser definido no conjunto de pontos de extremidade de balanceamento de carga:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="cloud-service-configuration-to-change-distribution-mode"></a>Configuração de serviço de nuvem para alterar o modo de distribuição

Você pode aproveitar o SDK do Azure para .NET 2.5 para atualizar o serviço de nuvem. Configurações de ponto de extremidade para serviços de nuvem são feitas na. csdef. Para atualizar o modo de distribuição do balanceador de carga para uma implantação de serviços de nuvem, é necessária uma atualização da implantação.
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

Você pode configurar a distribuição do balanceador de carga usando a API de gerenciamento de serviços. Certifique-se de adicionar o cabeçalho `x-ms-version` definido como a versão `2014-09-01` ou superior.

### <a name="update-the-configuration-of-the-specified-load-balanced-set-in-a-deployment"></a>Atualize a configuração do conjunto de balanceamento de carga especificado em uma implantação

#### <a name="request-example"></a>Exemplo de solicitação

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
    Content-Type: application/xml

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
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

O valor de LoadBalancerDistribution pode ser sourceIP para afinidade de 2 tuplas, sourceIPProtocol para afinidade de 3 tuplas ou nenhum (para nenhuma afinidade. por exemplo, 5 tuplas)

#### <a name="response"></a>Resposta

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Próximas etapas

[Visão geral do balanceador de carga interno](load-balancer-internal-overview.md)

[Introdução à configuração de um balanceador de carga para a Internet](load-balancer-get-started-internet-arm-ps.md)

[Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)
