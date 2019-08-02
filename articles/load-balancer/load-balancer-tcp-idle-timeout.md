---
title: Configurar o tempo limite de ociosidade do TCP do Load Balancer no Azure
titlesuffix: Azure Load Balancer
description: Configurar tempo limite de ociosidade do TCP do balanceador de carga
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: b3df1ead7a3164ffd9a4b4acf8820d0f5b82cee3
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274175"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Definir as configurações de tempo limite de ociosidade do TCP para o Azure Load Balancer

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Em sua configuração padrão, o Azure Load Balancer tem uma configuração de tempo limite de ociosidade de quatro minutos. Se um período de inatividade for maior que o valor de tempo limite, não haverá nenhuma garantia de que a sessão TCP ou HTTP seja mantida entre o cliente e o serviço de nuvem.

Quando a conexão estiver fechada, o aplicativo cliente poderá receber a seguinte mensagem de erro: "A conexão subjacente estava fechada: Uma conexão que deveria permanecer ativa foi fechada pelo servidor."

Uma prática comum é usar um TCP keep alive. Essa prática mantém a conexão ativa por um período maior. Para obter mais informações, consulte estes [exemplos do .NET](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Com o keep alive habilitado, os pacotes são enviados durante os períodos de inatividade na conexão. Esses pacotes keep alive garantem que o valor de tempo limite de ociosidade nunca será atingido e a conexão será mantida por um longo período.

Essa configuração só funciona para conexões de entrada. Para evitar a perda da conexão, você deve configurar o TCP keep-alive com um intervalo menor do que a configuração de tempo limite de ociosidade ou aumentar o valor do tempo limite de ociosidade. Para dar suporte a esses cenários, adicionamos suporte para um tempo limite de ociosidade configurável. Agora você pode defini-lo para uma duração entre quatro e 30 minutos.

O TCP keep alive funciona bem para cenários em que a vida útil da bateria não é uma restrição. Não é recomendável para aplicativos móveis. Usar o TCP keep alive em um aplicativo móvel pode consumir a bateria do dispositivo mais rapidamente.

![Tempo limite de TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

As seções a seguir descrevem como alterar as configurações de tempo limite de ociosidade em máquinas virtuais e serviços de nuvem.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Configure o tempo limite de TCP para o IP Público em Nível de Instância para 15 minutos

```powershell
Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15
```

`IdleTimeoutInMinutes` é opcional. Se não for definido, o tempo limite padrão será 4 minutos. O intervalo de tempo limite aceitável é entre quatro e 30 minutos.

## <a name="set-the-idle-timeout-when-creating-an-azure-endpoint-on-a-virtual-machine"></a>Defina o tempo limite de ociosidade durante a criação de um ponto de extremidade do Azure em uma máquina virtual

Para alterar a configuração de tempo limite para um ponto de extremidade, use o seguinte:

```powershell
Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM
```

Para recuperar sua configuração de tempo limite de ociosidade, use o seguinte comando:

    PS C:\> Get-AzureVM -ServiceName "MyService" -Name "MyVM" | Get-AzureEndpoint
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

## <a name="set-the-tcp-timeout-on-a-load-balanced-endpoint-set"></a>Defina o tempo limite do TCP em um conjunto do ponto de extremidade com balanceamento de carga

Se os pontos de extremidade forem parte de um conjunto do ponto de extremidade com balanceamento de carga, o tempo limite do TCP deverá ser definido no conjunto de pontos de extremidade com balanceamento de carga. Por exemplo:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15
```

## <a name="change-timeout-settings-for-cloud-services"></a>Alterar as configurações de tempo limite para serviços de nuvem

É possível usar o SDK do Azure para atualizar seu serviço de nuvem. Você pode fazer configurações de ponto de extremidade para serviços de nuvem no arquivo .csdef. A atualização do tempo limite do TCP para a implantação de um serviço de nuvem exige uma atualização da implantação. Uma exceção é se o tempo limite do TCP é especificado somente para um IP público. As configurações de IP público estão no arquivo .cscfg e podem ser atualizadas por intermédio da atualização e do upgrade da implantação.

As alterações do .csdef para as configurações do ponto de extremidade são:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
    </Endpoints>
</WorkerRole>
```

As alterações no .cscfg para a configuração de tempo limite em IPs públicos são:

```xml
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

## <a name="rest-api-example"></a>Exemplo de API REST

Você pode configurar o tempo limite de ociosidade de TCP usando a API de Gerenciamento de Serviços. Certifique-se de que o cabeçalho `x-ms-version` esteja definido como a versão `2014-06-01` ou superior. Atualize a configuração dos pontos de extremidade de entrada com balanceamento de carga especificado em todas as máquinas virtuais em uma implantação.

### <a name="request"></a>Solicitação

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>

### <a name="response"></a>Response

```xml
<LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
    <InputEndpoint>
    <LoadBalancedEndpointSetName>endpoint-set-name</LoadBalancedEndpointSetName>
    <LocalPort>local-port-number</LocalPort>
    <Port>external-port-number</Port>
    <LoadBalancerProbe>
        <Path>path-of-probe</Path>
        <Port>port-assigned-to-probe</Port>
        <Protocol>probe-protocol</Protocol>
        <IntervalInSeconds>interval-of-probe</IntervalInSeconds>
        <TimeoutInSeconds>timeout-for-probe</TimeoutInSeconds>
    </LoadBalancerProbe>
    <LoadBalancerName>name-of-internal-loadbalancer</LoadBalancerName>
    <Protocol>endpoint-protocol</Protocol>
    <IdleTimeoutInMinutes>15</IdleTimeoutInMinutes>
    <EnableDirectServerReturn>enable-direct-server-return</EnableDirectServerReturn>
    <EndpointACL>
        <Rules>
        <Rule>
            <Order>priority-of-the-rule</Order>
            <Action>permit-rule</Action>
            <RemoteSubnet>subnet-of-the-rule</RemoteSubnet>
            <Description>description-of-the-rule</Description>
        </Rule>
        </Rules>
    </EndpointACL>
    </InputEndpoint>
</LoadBalancedEndpointList>
```

## <a name="next-steps"></a>Próximas etapas

[Visão geral do balanceador de carga interno](load-balancer-internal-overview.md)

[Introdução à configuração de um balanceador de carga para a Internet](load-balancer-get-started-internet-arm-ps.md)

[Configurar um modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md)
