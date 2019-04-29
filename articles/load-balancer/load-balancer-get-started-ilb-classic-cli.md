---
title: Criar um Balanceador de Carga interno - CLI Clássica do Azure
titlesuffix: Azure Load Balancer
description: Saiba como criar um balanceador de carga interno usando a CLI Clássica do Azure no modelo de implantação clássico
services: load-balancer
documentationcenter: na
author: genlin
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 991e6554df62591dea5c126f8ea82704373d6ffd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60781232"
---
# <a name="get-started-creating-an-internal-load-balancer-using-the-azure-classic-cli"></a>Introdução à criação de um balanceador de carga interno usando a CLI Clássica do Azure

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-classic-ps.md)
> * [CLI do Azure](../load-balancer/load-balancer-get-started-ilb-classic-cli.md)
> * [Serviços de nuvem](../load-balancer/load-balancer-get-started-ilb-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos:  [Resource Manager e clássico](../azure-resource-manager/resource-manager-deployment-model.md).  Este artigo aborda o uso do modelo de implantação clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. Saiba como [executar estas etapas usando o modelo do Resource Manager](load-balancer-get-started-ilb-arm-cli.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="to-create-an-internal-load-balancer-set-for-virtual-machines"></a>Para criar um conjunto de balanceadores de carga internos para máquinas virtuais

Para criar um conjunto de balanceadores de carga internos e os servidores que enviarão o tráfego para ele, é necessário fazer o seguinte:

1. Crie uma instância do Balanceamento de Carga Interno que será o ponto de extremidade do tráfego de entrada a ser balanceado nos servidores de um conjunto de balanceamento de carga.
2. Adicione pontos de extremidade correspondentes às máquinas virtuais que receberão o tráfego de entrada.
3. Configure os servidores para enviar o tráfego para o endereço VIP (IP Virtual) da instância do Balanceamento de Carga Interno.

## <a name="step-by-step-creating-an-internal-load-balancer-using-classic-cli"></a>Passo a passo da criação de um balanceador de carga interno usando a CLI Clássica

Este guia mostra como criar um balanceador de carga interno com base no cenário acima.

1. Se você nunca usou a CLI Clássica do Azure, confira [Instalar e configurar a CLI do Azure](../cli-install-nodejs.md) e siga as instruções até o ponto em que você seleciona sua conta e assinatura do Azure.
2. Execute o comando **azure config mode** para alternar para o modo clássico, como mostrado abaixo.

    ```azurecli
    azure config mode asm
    ```

    Saída esperada:

        info:    New mode is asm

## <a name="create-endpoint-and-load-balancer-set"></a>Criar ponto de extremidade e conjunto de balanceadores de carga

O cenário pressupõe a existência das máquinas virtuais “DB1” e “DB2” em um serviço de nuvem chamado “mytestcloud”. As duas máquinas virtuais estão usando uma rede virtual chamada minha "testvnet" com a sub-rede "subnet-1".

Este guia criará um conjunto de balanceadores de carga internos usando a porta 1433 como a porta pública e 1433 como a porta local.

Esse é um cenário comum em que você tem máquinas virtuais do SQL no back-end usando um balanceador de carga interno para garantir que os servidores de banco de dados não sejam expostos diretamente usando um endereço IP público.

### <a name="step-1"></a>Etapa 1

Crie um conjunto do balanceador de carga interno usando o `azure network service internal-load-balancer add`.

```azurecli
azure service internal-load-balancer add --serviceName mytestcloud --internalLBName ilbset --subnet-name subnet-1 --static-virtualnetwork-ipaddress 192.168.2.7
```

Confira `azure service internal-load-balancer --help` para obter mais informações.

É possível verificar as propriedades do balanceador de carga interno usando o comando `azure service internal-load-balancer list` *nome do serviço de nuvem*.

Apresentamos a seguir um exemplo da saída:

    azure service internal-load-balancer list my-testcloud
    info:    Executing command service internal-load-balancer list
    + Getting cloud service deployment
    data:    Name    Type     SubnetName  StaticVirtualNetworkIPAddress
    data:    ------  -------  ----------  -----------------------------
    data:    ilbset  Private  subnet-1    192.168.2.7
    info:    service internal-load-balancer list command OK


### <a name="step-2"></a>Etapa 2

Você configura o conjunto de balanceadores de carga internos quando adicionar o primeiro ponto de extremidade. Você pode associar o ponto de extremidade, a máquina virtual e a porta de investigação ao conjunto de balanceador de carga interno nesta etapa.

```azurecli
azure vm endpoint create db1 1433 --local-port 1433 --protocol tcp --probe-port 1433 --probe-protocol tcp --probe-interval 300 --probe-timeout 600 --internal-load-balancer-name ilbset
```

### <a name="step-3"></a>Etapa 3

Verifique a configuração do balanceador de carga usando o `azure vm show` *nome da máquina virtual*

```azurecli
azure vm show DB1
```

A saída é da seguinte maneira:

    azure vm show DB1
    info:    Executing command vm show
    + Getting virtual machines
    data:    DNSName "mytestcloud.cloudapp.net"
    data:    Location "East US 2"
    data:    VMName "DB1"
    data:    IPAddress "192.168.2.4"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "db1-DB1-0-201511120457370846"
    data:    OSDisk mediaLink "https://XXXX.blob.core.windows.net/vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "137.116.64.107"
    data:    VirtualIPAddresses 0 name "db1ContractContract"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    VirtualIPAddresses 1 address "192.168.2.7"
    data:    VirtualIPAddresses 1 name "ilbset"
    data:    Network Endpoints 0 localPort 5986
    data:    Network Endpoints 0 name "PowerShell"
    data:    Network Endpoints 0 port 5986
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 3389
    data:    Network Endpoints 1 name "Remote Desktop"
    data:    Network Endpoints 1 port 60173
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 1433
    data:    Network Endpoints 2 name "tcp-1433-1433"
    data:    Network Endpoints 2 port 1433
    data:    Network Endpoints 2 loadBalancerProbe port 1433
    data:    Network Endpoints 2 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 2 loadBalancerProbe intervalInSeconds 300
    data:    Network Endpoints 2 loadBalancerProbe timeoutInSeconds 600
    data:    Network Endpoints 2 protocol "tcp"
    data:    Network Endpoints 2 virtualIPAddress "192.168.2.7"
    data:    Network Endpoints 2 enableDirectServerReturn false
    data:    Network Endpoints 2 loadBalancerName "ilbset"
    info:    vm show command OK

## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>Criar um ponto de extremidade da área de trabalho remota para uma máquina virtual

Você pode criar um ponto de extremidade da área de trabalho remota para encaminhar o tráfego de rede de uma porta pública para uma porta local, para uma máquina virtual específica, usando `azure vm endpoint create`.

```azurecli
azure vm endpoint create web1 54580 -k 3389
```

## <a name="remove-virtual-machine-from-load-balancer"></a>Remover máquina virtual do balanceador de carga

É possível remover uma máquina virtual de um conjunto de balanceadores de carga internos excluindo o ponto de extremidade associado. Depois que o ponto de extremidade for removido, a máquina virtual não pertencerá mais ao conjunto de balanceadores de carga.

Usando o exemplo acima, você pode remover o ponto de extremidade criado para a máquina virtual “DB1” do balanceador de carga interno usando o comando “lbset” `azure vm endpoint delete`.

```azurecli
azure vm endpoint delete DB1 tcp-1433-1433
```

Confira `azure vm endpoint --help` para obter mais informações.

## <a name="next-steps"></a>Próximas etapas

[Configurar um modo de distribuição do balanceador de carga usando a afinidade de IP de origem](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)
