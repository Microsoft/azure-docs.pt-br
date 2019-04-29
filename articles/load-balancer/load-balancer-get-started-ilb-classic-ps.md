---
title: Criar um balanceador de carga interno – PowerShell clássico
titlesuffix: Azure Load Balancer
description: Saiba como criar um balanceador de carga interno no modelo de implantação clássico usando o PowerShell
services: load-balancer
documentationcenter: na
author: genlin
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms:custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: genli
ms.openlocfilehash: ef6aac0d97c38798f826304475779ea8059875c7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60848531"
---
# <a name="get-started-creating-an-internal-load-balancer-classic-using-powershell"></a>Introdução à criação de um balanceador de carga interno (clássico) usando o PowerShell

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-classic-ps.md)
> * [CLI do Azure](../load-balancer/load-balancer-get-started-ilb-classic-cli.md)
> * [Serviços de nuvem](../load-balancer/load-balancer-get-started-ilb-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos:  [Resource Manager e clássico](../azure-resource-manager/resource-manager-deployment-model.md).  Este artigo aborda o uso do modelo de implantação clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. Saiba como [executar estas etapas usando o modelo do Resource Manager](load-balancer-get-started-ilb-arm-ps.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-internal-load-balancer-set-for-virtual-machines"></a>Criar um conjunto de balanceadores de carga internos para máquinas virtuais

Para criar um conjunto de balanceamento de carga interno e os servidores que enviarão o tráfego para ele, faça o seguinte:

1. Crie uma instância do Balanceamento de Carga Interno que será o ponto de extremidade do tráfego de entrada a ser balanceado nos servidores de um conjunto de balanceamento de carga.
2. Adicione pontos de extremidade correspondentes às máquinas virtuais que receberão o tráfego de entrada.
3. Configure os servidores que enviarão o tráfego com a carga a ser balanceada para enviar o tráfego para o endereço VIP (IP Virtual) da instância do Balanceamento de Carga Interno.

### <a name="step-1-create-an-internal-load-balancing-instance"></a>Etapa 1: Criar uma instância de Balanceamento de Carga Interno

Para um serviço de nuvem existente ou um serviço de nuvem implantado em uma rede virtual regional, você pode criar uma instância do Balanceamento de Carga Interno com os comandos do Windows PowerShell a seguir:

```powershell
$svc="<Cloud Service Name>"
$ilb="<Name of your ILB instance>"
$subnet="<Name of the subnet within your virtual network>"
$IP="<The IPv4 address to use on the subnet-optional>"

Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP
```

Observe que esse uso do cmdlet [Add-AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx) do Windows PowerShell usa o conjunto de parâmetros DefaultProbe. Para obter mais informações sobre conjuntos de parâmetros adicionais, consulte [Add-AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx).

### <a name="step-2-add-endpoints-to-the-internal-load-balancing-instance"></a>Etapa 2: Adicionar pontos de extremidade à instância do Balanceamento de Carga Interno

Veja um exemplo:

```powershell
$svc="mytestcloud"
$vmname="DB1"
$epname="TCP-1433-1433"
$lbsetname="lbset"
$prot="tcp"
$locport=1433
$pubport=1433
$ilb="ilbset"
Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -Lbset $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM
```

### <a name="step-3-configure-your-servers-to-send-their-traffic-to-the-new-internal-load-balancing-endpoint"></a>Etapa 3: Configurar os servidores para enviar o tráfego para o novo ponto de extremidade do Balanceamento de Carga Interno

Configure os servidores, cujo tráfego terá carga balanceada, para usar o novo endereço IP (o VIP) da instância do Balanceamento de Carga Interno. Este é o endereço no qual a instância do Balanceamento de Carga Interno está escutando. Na maioria dos casos, basta adicionar ou modificar um registro DNS ao VIP da instância do Balanceamento de Carga Interno.

Se você especificar o endereço IP durante a criação da instância do Balanceamento de Carga Interno, você já terá o VIP. Caso contrário, você poderá ver o VIP por meio dos comandos a seguir:

```powershell
$svc="<Cloud Service Name>"
Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer
```

Para usar esses comandos, preencha os valores e remova < e >. Veja um exemplo:

```powershell
$svc="mytestcloud"
Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer
```

Na exibição do comando Get-AzureInternalLoadBalancer, anote o endereço IP e faça as alterações necessárias em seus servidores ou registros DNS para garantir que o tráfego seja enviado para o VIP.

> [!NOTE]
> A plataforma Microsoft Azure usa um endereço IPv4 estático e publicamente roteável para uma variedade de cenários administrativos. O endereço IP é 168.63.129.16. Esse endereço IP não deve ser bloqueado por nenhum firewall porque ele pode causar um comportamento inesperado.
> Em relação ao Balanceamento de Carga Interno do Azure, esse endereço IP é usado por testes de monitoramento do balanceador de carga para determinar o estado de integridade para máquinas virtuais em um conjunto com balanceamento de carga. Se um grupo de segurança de rede é usado para restringir o tráfego para máquinas virtuais do Azure em um conjunto com balanceamento de carga interno, ou então é aplicado a uma Sub-rede de Rede Virtual, certifique-se de que uma regra de segurança de rede seja adicionada para permitir o tráfego em 168.63.129.16.

## <a name="example-of-internal-load-balancing"></a>Exemplo de balanceamento de carga interna

Para percorrer o processo completo de criar um conjunto de balanceamento de carga para duas configurações de exemplo, consulte as seções a seguir.

### <a name="an-internet-facing-multi-tier-application"></a>Um aplicativo para a Internet de diversas camadas

Você deseja fornecer um serviço de banco de dados com balanceamento de carga para um conjunto de servidores web voltados para a Internet. Os dois conjuntos de servidores são hospedados em um único serviço de nuvem do Azure. O tráfego do servidor Web para a porta TCP 1433 deve ser distribuído entre duas máquinas virtuais na camada de banco de dados. A Figura 1 mostra a configuração.

![Conjunto de balanceamento de carga interno para a camada de banco de dados](./media/load-balancer-internal-getstarted/IC736321.png)

A configuração consiste em:

* O serviço de nuvem existente que hospeda as máquinas virtuais é denominado mytestcloud.
* Os dois servidores de banco de dados existentes são nomeados DB1 e DB2.
* Servidores Web na camada Web se conectam aos servidores de banco de dados na camada de banco de dados usando o endereço de IP privado. Outra opção é usar seu próprio DNS para a rede virtual e registrar manualmente um registro A para o conjunto de balanceador de carga interno.

Os comandos a seguir configuram uma nova instância do Balanceamento de Carga Interno denominada **ILBset** e adicionam pontos de extremidade às máquinas virtuais correspondentes aos dois servidores de banco de dados:

```powershell
$svc="mytestcloud"
$ilb="ilbset"
Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb
$prot="tcp"
$locport=1433
$pubport=1433
$epname="TCP-1433-1433"
$lbsetname="lbset"
$vmname="DB1"
Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

$epname="TCP-1433-1433-2"
$vmname="DB2"
Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM
```

## <a name="remove-an-internal-load-balancing-configuration"></a>Remover uma configuração de Balanceamento de Carga Interno

Para remover uma máquina virtual como um ponto de extremidade de uma instância do balanceamento de carga interno, use os comandos a seguir:

```powershell
$svc="<Cloud service name>"
$vmname="<Name of the VM>"
$epname="<Name of the endpoint>"
Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM
```

Para usar esses comandos, preencha os valores e remova < e >.

Veja um exemplo:

```powershell
$svc="mytestcloud"
$vmname="DB1"
$epname="TCP-1433-1433"
Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM
```

Para remover uma instância do balanceador de carga interno de um serviço de nuvem, use os comandos a seguir:

```powershell
$svc="<Cloud service name>"
Remove-AzureInternalLoadBalancer -ServiceName $svc
```

Para usar esses comandos, preencha os valores e remova < e >.

Veja um exemplo:

```powershell
$svc="mytestcloud"
Remove-AzureInternalLoadBalancer -ServiceName $svc
```

## <a name="additional-information-about-internal-load-balancer-cmdlets"></a>Informações adicionais sobre cmdlets de balanceador de carga interno

Para obter informações adicionais sobre cmdlets de Balanceamento de Carga Interno, execute os comandos a seguir em um prompt do Windows PowerShell:

```powershell
Get-Help New-AzureInternalLoadBalancerConfig -full
Get-Help Add-AzureInternalLoadBalancer -full
Get-Help Get-AzureInternalLoadbalancer -full
Get-Help Remove-AzureInternalLoadBalancer -full
```

## <a name="next-steps"></a>Próximas etapas

[Configurar um modo de distribuição do balanceador de carga usando a afinidade de IP de origem](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)

