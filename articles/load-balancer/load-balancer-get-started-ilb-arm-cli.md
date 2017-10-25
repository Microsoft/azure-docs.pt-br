---
title: Criar um balanceador de carga interno - CLI do Azure | Microsoft Docs
description: Saiba como criar um balanceador de carga interno no Gerenciador de Recursos usando a CLI do Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
ms.assetid: c7a24e92-b4da-43c0-90f2-841c1b7ce489
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 83cf027d95018de61ea906268d8f24700203e0c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-internal-load-balancer-by-using-the-azure-cli"></a>Criar um balanceador de carga interno usando a CLI do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [CLI do Azure](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Modelo](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!NOTE]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e clássico](../azure-resource-manager/resource-manager-deployment-model.md).  Este artigo aborda usando o modelo de implantação do Gerenciador de Recursos, que a Microsoft recomenda para a maioria das novas implantações em vez de do [modelo de implantação clássico](load-balancer-get-started-ilb-classic-cli.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-solution-by-using-the-azure-cli"></a>Implantar a solução usando a CLI do Azure

As etapas a seguir mostram como criar um balanceador de carga para a Internet usando o Azure Resource Manager com a CLI. Com o Azure Resource Manager, todos os recursos são criados e configurados individualmente e colocados juntos para criar um recurso.

Você precisa criar e configurar os seguintes objetos para implantar um balanceador de carga:

* **Configuração de IP de front-end**: contém endereços IP públicos para o tráfego de rede de entrada
* **Pool de endereços de back-end**: contém NICs (interfaces de rede) que permitem que as máquinas virtuais recebam o tráfego de rede do balanceador de carga
* **Regras de balanceamento de carga**: contém regras que mapeiam uma porta pública no balanceador de carga para uma porta no pool de endereços de back-end
* **Regras NAT de entrada**: contém regras que mapeiam uma porta pública no balanceador de carga para uma porta de uma máquina virtual específica no pool de endereços de back-end
* **Investigações**: contém investigações de integridade usadas para verificar a disponibilidade de instâncias de máquinas virtuais no pool de endereços de back-end

Para saber mais, confira [Suporte do Azure Resource Manager para Balanceador de Carga](load-balancer-arm.md).

## <a name="set-up-cli-to-use-resource-manager"></a>Configurar a CLI para usar o Gerenciador de Recursos

1. Se você nunca tiver usado a CLI do Azure, consulte [Instalar e configurar a CLI do Azure](../cli-install-nodejs.md). Siga as instruções até o ponto onde você seleciona a conta e a assinatura do Azure.
2. Execute o comando **azure config mode** para alternar para o modo do Resource Manager, da seguinte maneira:

    ```azurecli
    azure config mode arm
    ```

    Saída esperada:

        info:    New mode is arm

## <a name="create-an-internal-load-balancer-step-by-step"></a>Criar um balanceador de carga interno, passo a passo

1. Entre no Azure.

    ```azurecli
    azure login
    ```

    Inserir as credenciais do Azure, quando for solicitado.

2. Altere as ferramentas de comando para o modo do Azure Resource Manager.

    ```azurecli
    azure config mode arm
    ```

## <a name="create-a-resource-group"></a>Criar um grupos de recursos

Todos os recursos no Azure Resource Manager estão associados a um grupo de recursos. Se você ainda não fez isso, crie um grupo de recursos.

```azurecli
azure group create <resource group name> <location>
```

## <a name="create-an-internal-load-balancer-set"></a>Criar um conjunto do balanceador de carga interno

1. Criar um balanceador de carga interno

    No cenário a seguir, é criado um grupo de recursos chamado nrprg na região Leste dos EUA.

    ```azurecli
    azure network lb create --name nrprg --location eastus
    ```

   > [!NOTE]
   > Todos os recursos de balanceadores de carga internos, como redes virtuais e sub-redes da rede virtual, devem estar no mesmo grupo de recursos e na mesma região.

2. Crie um endereço IP de front-end para o balanceador de carga interno.

    O endereço IP usado deve estar dentro do intervalo da sub-rede de sua rede virtual.

    ```azurecli
    azure network lb frontend-ip create --resource-group nrprg --lb-name ilbset --name feilb --private-ip-address 10.0.0.7 --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet
    ```

3. Criar um pool de endereços de back-end.

    ```azurecli
    azure network lb address-pool create --resource-group nrprg --lb-name ilbset --name beilb
    ```

    Depois de definir um endereço IP de front-end e um pool de endereços de back-end, você poderá criar regras de balanceador de carga, regras NAT de entrada e investigações de integridade personalizadas.

4. Crie uma regra do balanceador de carga para o balanceador de carga interno.

    Ao executar as etapas anteriores, o comando cria uma regra do balanceador de carga que escuta a porta 1433 no pool de front-end e envia o tráfego de rede com carga balanceada ao pool de endereços de back-end também usando a porta 1433.

    ```azurecli
    azure network lb rule create --resource-group nrprg --lb-name ilbset --name ilbrule --protocol tcp --frontend-port 1433 --backend-port 1433 --frontend-ip-name feilb --backend-address-pool-name beilb
    ```

5. Crie regras NAT de entrada.

    As regras NAT de entrada são usadas para criar pontos de extremidade em um balanceador de carga, que são destinados a uma instância específica de máquina virtual. As etapas anteriores criaram duas regras NAT para a área de trabalho remota.

    ```azurecli
    azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule1 --protocol TCP --frontend-port 5432 --backend-port 3389

    azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule2 --protocol TCP --frontend-port 5433 --backend-port 3389
    ```

6. Crie investigações de integridade para o balanceador de carga.

    Uma investigação de integridade verifica todas as instâncias da máquina virtual para se certificar de que ela pode enviar o tráfego de rede. A instância de máquina virtual com verificações de investigação com falha é removida do balanceador de carga até ele ficar online novamente e as verificações de investigação determinarem sua integridade.

    ```azurecli
    azure network lb probe create --resource-group nrprg --lb-name ilbset --name ilbprobe --protocol tcp --interval 300 --count 4
    ```

    > [!NOTE]
    > A plataforma Microsoft Azure usa um endereço IPv4 estático e publicamente roteável para uma variedade de cenários administrativos. O endereço IP é 168.63.129.16. Esse endereço IP não deve ser bloqueado por nenhum firewall porque ele pode causar um comportamento inesperado.
    > Em relação ao Balanceamento de Carga Interno do Azure, esse endereço IP é usado por testes de monitoramento do balanceador de carga para determinar o estado de integridade para máquinas virtuais em um conjunto com balanceamento de carga. Se um grupo de segurança de rede é usado para restringir o tráfego para máquinas virtuais do Azure em um conjunto com balanceamento de carga interno, ou então é aplicado a uma Sub-rede de Rede Virtual, certifique-se de que uma regra de segurança de rede seja adicionada para permitir o tráfego em 168.63.129.16.

## <a name="create-nics"></a>Criar NICs

Você precisa criar NICs (ou modificar as existentes) e associá-las a regras NAT, regras do balanceador de carga e testes.

1. Crie um NIC chamada *lb-nic1-be* e a associe à regra NAT *rdp1* e ao pool de endereços de back-end *beilb*.

    ```azurecli
    azure network nic create --resource-group nrprg --name lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" --location eastus
    ```

    Saída esperada:

        info:    Executing command network nic create
        + Looking up the network interface "lb-nic1-be"
        + Looking up the subnet "nrpvnetsubnet"
        + Creating network interface "lb-nic1-be"
        + Looking up the network interface "lb-nic1-be"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        data:    Name                            : lb-nic1-be
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : eastus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 10.0.0.4
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet
        data:      Load balancer backend address pools
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:      Load balancer inbound NAT rules:
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1
        data:
        info:    network nic create command OK

2. Crie um NIC chamada *lb-nic2-be* e a associe à regra NAT *rdp2* e ao pool de endereços de back-end *beilb*.

    ```azurecli
    azure network nic create --resource-group nrprg --name lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" --location eastus
    ```

3. Crie uma máquina virtual chamada *DB1* e a associe à NIC chamada *lb-nic1-be*. Uma conta de armazenamento denominada *web1nrp* é criada antes da execução do comando a seguir:

    ```azurecli
    azure vm create --resource--resource-grouproup nrprg --name DB1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825
    ```
    > [!IMPORTANT]
    > As VMs em um balanceador de carga precisam estar no mesmo conjunto de disponibilidade. Use `azure availset create` para criar um conjunto de disponibilidade.

4. Crie uma VM (máquina virtual) chamada *DB2* e a associe à NIC chamada *lb-nic2-be*. Uma conta de armazenamento denominada *web1nrp* foi criada antes da execução do comando a seguir.

    ```azurecli
    azure vm create --resource--resource-grouproup nrprg --name DB2 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825
    ```

## <a name="delete-a-load-balancer"></a>Excluir um balanceador de carga

Para remover um balanceador de carga, use o comando a seguir:

```azurecli
azure network lb delete --resource-group nrprg --name ilbset
```

## <a name="next-steps"></a>Próximas etapas

[Configurar um modo de distribuição do balanceador de carga usando a afinidade de IP de origem](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)

