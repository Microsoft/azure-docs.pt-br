---
title: "Criar um balanceador de carga voltado para a Internet dos serviços de nuvem do Azure| Microsoft Docs"
description: "Saiba como criar um balanceador de carga para a Internet no modelo de implantação clássico para serviços de nuvem"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-service-management
ms.assetid: 0bb16f96-56a6-429f-88f5-0de2d0136756
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
ms.openlocfilehash: 52824d5c39bb821351650584c33f70e2e84749cb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-creating-an-internet-facing-load-balancer-for-cloud-services"></a>Introdução à criação de um balanceador de carga para a Internet para serviços de nuvem

> [!div class="op_single_selector"]
> * [Portal clássico do Azure](../load-balancer/load-balancer-get-started-internet-classic-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [CLI do Azure](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Serviços de Nuvem do Azure](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Antes de trabalhar com os recursos do Azure, é importante entender que, no momento, o Azure apresenta dois modelos de implantação: Azure Resource Manager e clássico. Verifique se você entendeu [os modelos e as ferramentas de implantação](../azure-classic-rm.md) antes de trabalhar com qualquer recurso do Azure. Você pode exibir a documentação para ferramentas diferentes clicando nas guias na parte superior deste artigo. Este artigo aborda o modelo de implantação clássico. Também é possível [Saber como criar um balanceador de carga para a Internet usando o Gerenciador de Recursos do Azure](load-balancer-get-started-internet-arm-ps.md).

Serviços de nuvem são automaticamente configurados com um balanceador de carga e podem ser personalizados por meio do modelo de serviço.

## <a name="create-a-load-balancer-using-the-service-definition-file"></a>Criar um balanceador de carga usando o arquivo de definição de serviço

Você pode aproveitar o SDK do Azure para .NET 2.5 para atualizar seu serviço de nuvem. As configurações de ponto de extremidade para serviços de nuvem são feitas no arquivo .csdef da [definição de serviço](https://msdn.microsoft.com/library/azure/gg557553.aspx).

O exemplo a seguir mostra como um arquivo servicedefinition.csdef é configurado para uma implantação na nuvem:

Ao verificar o trecho de código do arquivo .csdef gerado por uma implantação na nuvem, você pode ver o ponto de extremidade externo configurado para usar portas HTTP na porta 10000, 10001 e 10002.

```xml
<ServiceDefinition name=“Tenant“>
    <WorkerRole name=“FERole” vmsize=“Small“>
<Endpoints>
    <InputEndpoint name=“FE_External_Http” protocol=“http” port=“10000“ />
    <InputEndpoint name=“FE_External_Tcp“  protocol=“tcp“  port=“10001“ />
    <InputEndpoint name=“FE_External_Udp“  protocol=“udp“  port=“10002“ />

    <InputEndpointname=“HTTP_Probe” protocol=“http” port=“80” loadBalancerProbe=“MyProbe“ />

    <InstanceInputEndpoint name=“InstanceEP” protocol=“tcp” localPort=“80“>
        <AllocatePublicPortFrom>
            <FixedPortRange min=“10110” max=“10120“  />
        </AllocatePublicPortFrom>
    </InstanceInputEndpoint>
    <InternalEndpoint name=“FE_InternalEP_Tcp” protocol=“tcp“ />
</Endpoints>
    </WorkerRole>
</ServiceDefinition>
```

## <a name="check-load-balancer-health-status-for-cloud-services"></a>Verificar o status de integridade do balanceador de carga para serviços de nuvem

A seguir, um exemplo de como criar uma investigação:

```xml
<LoadBalancerProbes>
    <LoadBalancerProbe name=“MyProbe” protocol=“http” path=“Probe.aspx” intervalInSeconds=“5” timeoutInSeconds=“100“ />
</LoadBalancerProbes>
```

O balanceador de carga combina as informações do ponto de extremidade e as informações de investigação para criar uma URL na forma de `http://{DIP of VM}:80/Probe.aspx`, que podem ser usadas para consultar a integridade do serviço.

O serviço detecta as investigações periódicas do mesmo endereço IP. Esta é a solicitação de investigação de integridade proveniente do host do nó no qual a máquina virtual está sendo executada. O serviço deve responder com um código de status HTTP 200 para que o balanceador de carga presuma que o serviço esteja íntegro. Qualquer outro código de status HTTP (por exemplo, 503) leva diretamente à máquina virtual da rotação.

A definição da investigação também controla a frequência da investigação. No caso acima, o balanceador de carga está investigando o ponto de extremidade a cada 5 segundos. Se nenhuma resposta positiva for recebida em 10 segundos (dois intervalos de investigação), a investigação será considerada inativa e a máquina virtual sairá da rotação. Da mesma forma, se o serviço estiver fora de rotação e uma resposta positiva for recebida, o serviço será colocado de volta à rotação imediatamente. Se o serviço estiver flutuando entre íntegro e não íntegro, o balanceador de carga pode optar por atrasar a reintrodução do serviço na rotação até que ele esteja íntegro para um número de investigações.

Verifique o esquema de definição de serviço para a [investigação de integridade](https://msdn.microsoft.com/library/azure/jj151530.aspx) para obter mais informações.

## <a name="next-steps"></a>Próximas etapas

[Introdução à configuração de um balanceador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar um modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)

