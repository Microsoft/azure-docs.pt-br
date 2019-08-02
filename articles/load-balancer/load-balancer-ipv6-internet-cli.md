---
title: Criar um balanceador de carga público com IPv6 - CLI do Azure
titlesuffix: Azure Load Balancer
description: Saiba como criar um balanceador de carga público com IPv6 usando a CLI do Azure.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: ipv6, azure load balancer, pilha dual, ip público, ipv6 nativo, móvel, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/25/2018
ms.author: allensu
ms.openlocfilehash: 0ee85a92753845e0e67fff22da894a048acb1b14
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274949"
---
# <a name="create-a-public-load-balancer-with-ipv6-using-azure-cli"></a>Criar um balanceador de carga público com IPv6 usando a CLI do Azure


Um Azure Load Balancer é um balanceador de carga de Camada 4 (TCP, UDP). Os balanceadores de carga fornecem alta disponibilidade, distribuindo o tráfego de entrada entre instâncias do serviço íntegras em serviços de nuvem ou máquinas virtuais em um conjunto de balanceadores de carga. Os balanceadores de carga também podem apresentar esses serviços em várias portas, em vários endereços IP ou em ambos.

## <a name="example-deployment-scenario"></a>Exemplo de cenário de implantação

O diagrama a seguir ilustra a solução de balanceamento de carga que está sendo implantada usando o exemplo de modelo descrito neste artigo.

![Cenário com o balanceador de carga](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

Nesse cenário, você cria os seguintes recursos do Azure:

* Duas VMs (máquinas virtuais)
* Uma interface de rede virtual para cada VM com endereços IPv4 e IPv6 atribuídos
* Um balanceador de carga público com um endereço IP público IPv4 e um IPv6
* Um conjunto de disponibilidade que contém as duas VMs
* Duas regras de balanceamento de carga para mapear os VIPs públicos para os pontos de extremidade privados

## <a name="deploy-the-solution-by-using-azure-cli"></a>Implantar a solução usando a CLI do Azure

As etapas a seguir mostram como criar um balanceador de carga público usando a CLI do Azure. Use a CLI para criar e configurar todos os objetos individualmente e, em seguida, reúna-os para criar um recurso.

Para implantar um balanceador de carga, crie e configure os seguintes objetos:

* **Configuração de IP de front-end**: Contém endereços IP públicos para tráfego de entrada.
* **Pool de endereços de back-end**: Contém NICs (adaptadores de rede) para as máquinas virtuais receberem tráfego do balanceador de carga.
* **Regras de balanceamento de carga**: Contém regras que mapeiam uma porta pública no balanceador de carga para uma porta no pool de endereços de back-end.
* **Regras NAT de entrada**: Contém regras NAT (conversão de endereços de rede) que mapeiam uma porta pública no balanceador de carga para uma porta de uma máquina virtual específica no pool de endereços de back-end.
* **Investigações**: Contém investigações de integridade que são usadas para verificar a disponibilidade de instâncias de máquina virtual no pool de endereços de back-end.

## <a name="set-up-azure-cli"></a>Configurar a CLI do Azure

Neste exemplo, você executa as ferramentas da CLI do Azure em uma janela de comando do PowerShell. Para melhorar a legibilidade e a reutilização, use os recursos de script do PowerShell, não os cmdlets do Azure PowerShell.

1. [Instale e configure a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) seguindo as etapas do artigo relacionado e entre em sua conta do Azure.

2. Configure variáveis do PowerShell para uso com os comandos da CLI do Azure:

    ```powershell
    $subscriptionid = "########-####-####-####-############"  # enter subscription id
    $location = "southcentralus"
    $rgName = "pscontosorg1southctrlus09152016"
    $vnetName = "contosoIPv4Vnet"
    $vnetPrefix = "10.0.0.0/16"
    $subnet1Name = "clicontosoIPv4Subnet1"
    $subnet1Prefix = "10.0.0.0/24"
    $subnet2Name = "clicontosoIPv4Subnet2"
    $subnet2Prefix = "10.0.1.0/24"
    $dnsLabel = "contoso09152016"
    $lbName = "myIPv4IPv6Lb"
    ```

## <a name="create-a-resource-group-a-load-balancer-a-virtual-network-and-subnets"></a>Crie um grupo de recursos, um balanceador de carga, uma rede virtual e sub-redes

1. Crie um grupo de recursos:

    ```azurecli
    az group create --name $rgName --location $location
    ```

2. Crie um balanceador de carga:

    ```azurecli
    $lb = az network lb create --resource-group $rgname --location $location --name $lbName
    ```

3. Criar uma rede virtual:

    ```azurecli
    $vnet = az network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix
    ```

4. Nessa rede virtual, crie duas sub-redes:

    ```azurecli
    $subnet1 = az network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
    $subnet2 = az network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName
    ```

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Criar endereços IP públicos para o pool de front-end

1. Configure as variáveis do PowerShell:

    ```powershell
    $publicIpv4Name = "myIPv4Vip"
    $publicIpv6Name = "myIPv6Vip"
    ```

2. Crie um endereço IP público para o pool de IPs de front-end:

    ```azurecli
    $publicipV4 = az network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --version IPv4 --allocation-method Dynamic --dns-name $dnsLabel
    $publicipV6 = az network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --version IPv6 --allocation-method Dynamic --dns-name $dnsLabel
    ```

    > [!IMPORTANT]
    > O balanceador de carga usa o rótulo de domínio do IP público como FQDN (nome de domínio totalmente qualificado). Isso é uma mudança da implantação clássica, que usa o nome do serviço de nuvem como o FQDN do balanceador de carga.
    >
    > Neste exemplo, o FQDN é *contoso09152016.southcentralus.cloudapp.azure.com*.

## <a name="create-front-end-and-back-end-pools"></a>Criar pools de front-end e back-end

Nesta seção, você cria os seguintes pools de IP:
* O pool de IP de front-end que recebe o tráfego de rede de entrada no balanceador de carga.
* O pool de IP de back-end para qual o pool de front-end envia o tráfego de rede com balanceamento de carga.

1. Configure as variáveis do PowerShell:

    ```powershell
    $frontendV4Name = "FrontendVipIPv4"
    $frontendV6Name = "FrontendVipIPv6"
    $backendAddressPoolV4Name = "BackendPoolIPv4"
    $backendAddressPoolV6Name = "BackendPoolIPv6"
    ```

2. Crie um pool de IPs de front-end e associe-o ao IP público criado na etapa anterior e o balanceador de carga.

    ```azurecli
    $frontendV4 = az network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-address $publicIpv4Name --lb-name $lbName
    $frontendV6 = az network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-address $publicIpv6Name --lb-name $lbName
    $backendAddressPoolV4 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
    $backendAddressPoolV6 = az network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName
    ```

## <a name="create-the-probe-nat-rules-and-load-balancer-rules"></a>Criar a investigação, regras de NAT e regras do balanceador de carga

Este exemplo cria os seguintes itens:

* Uma regra de investigação para verificar se há conectividade com a porta TCP 80.
* Uma regra NAT para converter todo o tráfego de entrada na porta 3389 para a porta 3389 para RDP.\*
* Uma regra NAT para converter todo o tráfego de entrada na porta 3391 para a porta 3389 para o protocolo RDP (desktop remoto).\*
* Uma regra do balanceador de carga para equilibrar todo o tráfego de entrada na porta 80 para a porta 80 nos endereços no pool de back-end.

\* As regras NAT são associadas a uma instância de máquina virtual específica por trás do balanceador de carga. O tráfego de rede que chega à porta 3389 é enviado a uma máquina virtual específica e à porta que está associada a essa regra NAT. Você deve especificar um protocolo (UDP ou TCP) para uma regra NAT. Você não pode atribuir os dois protocolos para a mesma porta.

1. Configure as variáveis do PowerShell:

    ```powershell
    $probeV4V6Name = "ProbeForIPv4AndIPv6"
    $natRule1V4Name = "NatRule-For-Rdp-VM1"
    $natRule2V4Name = "NatRule-For-Rdp-VM2"
    $lbRule1V4Name = "LBRuleForIPv4-Port80"
    $lbRule1V6Name = "LBRuleForIPv6-Port80"
    ```

2. Crie a investigação.

    O exemplo a seguir cria uma investigação TCP que verifica a conectividade com a porta TCP 80 back-end a cada 15 segundos. Ele marca os recursos de back-end como não disponíveis após duas falhas consecutivas.

    ```azurecli
    $probeV4V6 = az network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --threshold 2 --lb-name $lbName
    ```

3. Criar regras NAT de entrada que permitam conexões RDP com os recursos de back-end:

    ```azurecli
    $inboundNatRuleRdp1 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
    $inboundNatRuleRdp2 = az network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
    ```

4. Crie regras de balanceador de carga que enviam tráfego para portas de back-end diferentes, dependendo de qual front-end recebeu a solicitação.

    ```azurecli
    $lbruleIPv4 = az network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
    $lbruleIPv6 = az network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName
    ```

5. Verifique suas configurações:

    ```azurecli
    az network lb show --resource-group $rgName --name $lbName
    ```

    Saída esperada:

        info:    Executing command network lb show
        info:    Looking up the load balancer "myIPv4IPv6Lb"
        data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/pscontosorg1southctrlus09152016/providers/Microsoft.Network/loadBalancers/myIPv4IPv6Lb
        data:    Name                            : myIPv4IPv6Lb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : southcentralus
        data:    Provisioning state              : Succeeded
        data:
        data:    Frontend IP configurations:
        data:    Name             Provisioning state  Private IP allocation  Private IP   Subnet  Public IP
        data:    ---------------  ------------------  ---------------------  -----------  ------  ---------
        data:    FrontendVipIPv4  Succeeded           Dynamic                                     myIPv4Vip
        data:    FrontendVipIPv6  Succeeded           Dynamic                                     myIPv6Vip
        data:
        data:    Probes:
        data:    Name                 Provisioning state  Protocol  Port  Path  Interval  Count
        data:    -------------------  ------------------  --------  ----  ----  --------  -----
        data:    ProbeForIPv4AndIPv6  Succeeded           Tcp       80          15        2
        data:
        data:    Backend Address Pools:
        data:    Name             Provisioning state
        data:    ---------------  ------------------
        data:    BackendPoolIPv4  Succeeded
        data:    BackendPoolIPv6  Succeeded
        data:
        data:    Load Balancing Rules:
        data:    Name                  Provisioning state  Load distribution  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    --------------------  ------------------  -----------------  --------  -------------  ------------  ------------------  -----------------------
        data:    LBRuleForIPv4-Port80  Succeeded           Default            Tcp       80             80            false               4
        data:    LBRuleForIPv6-Port80  Succeeded           Default            Tcp       80             8080          false               4
        data:
        data:    Inbound NAT Rules:
        data:    Name                 Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    -------------------  ------------------  --------  -------------  ------------  ------------------  -----------------------
        data:    NatRule-For-Rdp-VM1  Succeeded           Tcp       3389           3389          false               4
        data:    NatRule-For-Rdp-VM2  Succeeded           Tcp       3391           3389          false               4
        info:    network lb show

## <a name="create-nics"></a>Criar NICs

Crie NICs e associe-os às regras de NAT, regras do balanceador de carga e investigações.

1. Configure as variáveis do PowerShell:

    ```powershell
    $nic1Name = "myIPv4IPv6Nic1"
    $nic2Name = "myIPv4IPv6Nic2"
    $subnet1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet1Name"
    $subnet2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet2Name"
    $backendAddressPoolV4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV4Name"
    $backendAddressPoolV6Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV6Name"
    $natRule1V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule1V4Name"
    $natRule2V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule2V4Name"
    ```

2. Crie um NIC para cada back-end e adicione uma configuração de IPv6:

    ```azurecli
    $nic1 = az network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet1Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule1V4Id
    $nic1IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic1Name

    $nic2 = az network nic create --name $nic2Name --resource-group $rgname --location $location --private-ip-address-version "IPv4" --subnet $subnet2Id --lb-address-pools $backendAddressPoolV4Id --lb-inbound-nat-rules $natRule2V4Id
    $nic2IPv6 = az network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-address-version "IPv6" --lb-address-pools $backendAddressPoolV6Id --nic-name $nic2Name
    ```

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>Crie os recursos de VM de back-end e anexe cada NIC

Para criar VMs, você deve ter uma conta de armazenamento. Para o balanceamento de carga, as VMs precisam ser membros de um conjunto de disponibilidade. Para saber mais sobre a criação de VMs, consulte [Criar uma VM do Azure usando o PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

1. Configure as variáveis do PowerShell:

    ```powershell
    $availabilitySetName = "myIPv4IPv6AvailabilitySet"
    $vm1Name = "myIPv4IPv6VM1"
    $vm2Name = "myIPv4IPv6VM2"
    $nic1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic1Name"
    $nic2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic2Name"
    $imageurn = "MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest"
    $vmUserName = "vmUser"
    $mySecurePassword = "PlainTextPassword*1"
    ```

    > [!WARNING]
    > Este exemplo usa o nome de usuário e a senha para as VMs em texto sem formatação. Tome cuidado apropriado ao usar essas credenciais em texto não criptografado. Para um método mais seguro de tratamento de credenciais no PowerShell, consulte o cmdlet [`Get-Credential`](https://technet.microsoft.com/library/hh849815.aspx).

2. Criar o conjunto de disponibilidade:

    ```azurecli
    $availabilitySet = az vm availability-set create --name $availabilitySetName --resource-group $rgName --location $location
    ```

3. Crie as máquinas virtuais com NICs associados:

    ```azurecli
    az vm create --resource-group $rgname --name $vm1Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic1Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 

    az vm create --resource-group $rgname --name $vm2Name --image $imageurn --admin-username $vmUserName --admin-password $mySecurePassword --nics $nic2Id --location $location --availability-set $availabilitySetName --size "Standard_A1" 
    ```

## <a name="next-steps"></a>Próximas etapas

[Introdução à configuração de um balanceador de carga interno](load-balancer-get-started-ilb-arm-cli.md)  
[Configurar um modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md)  
[Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)
