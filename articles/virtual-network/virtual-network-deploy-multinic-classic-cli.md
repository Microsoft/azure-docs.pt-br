---
title: Criar uma VM (clássica) com várias NICs – CLI Clássica do Azure | Microsoft Docs
description: Saiba como criar uma VM (Clássica) com várias placas de rede usando a CLI (interface de linha de comando) Clássica do Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: b436e41e-866c-439f-a7c7-7b4b041725ef
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1e47b1e548516960c6aab3c48d64255370c94a77
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743238"
---
# <a name="create-a-vm-classic-with-multiple-nics-using-the-azure-classic-cli"></a>Criar uma VM (Clássica) com várias NICs usando a CLI Clássica do Azure

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Você pode criar máquinas virtuais (VMs) no Azure e anexar várias interfaces de rede (NICs) para cada uma de suas VMs. Várias NICs permitem a separação dos tipos de tráfego entre NICs. Por exemplo, uma NIC pode se comunicar com a Internet, enquanto outra se comunica apenas com recursos internos que não estão conectados à Internet. A capacidade de separar o tráfego de rede entre as várias NICs é necessária para vários dispositivos de rede virtual, como a entrega de aplicativos e soluções de otimização de WAN.

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos:  [Resource Manager e clássico](../resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. Saiba como executar essas etapas usando o [modelo de implantação do Resource Manager](../virtual-machines/linux/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

As etapas a seguir usam um grupo de recursos chamado *IaaSStory* para os serviços Web e o grupo de recursos e *IaaSStory-BackEnd* para os servidores DB.

## <a name="prerequisites"></a>Pré-requisitos
Antes de criar os servidores DB, você precisa criar o grupo de recursos *IaaSStory* com todos os recursos necessários para este cenário. Para criar esses recursos, conclua as etapas a seguir. Criar uma rede virtual, seguindo as etapas do artigo [Criar uma rede virtual](virtual-networks-create-vnet-classic-cli.md).

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>Implantar VMs de back-end
As VMs de back-end dependem da criação dos seguintes recursos:

* **Conta de armazenamento para discos de dados**. Para obter um melhor desempenho, os discos de dados dos servidores de banco de dados usam a tecnologia SDD (unidade de estado sólido), que requer uma conta de Armazenamento Premium. Verifique se o local do Azure no qual você vai implantar é compatível com o Armazenamento Premium.
* **NICs**. Cada VM tem duas NICs, uma para acesso ao banco de dados e outra para gerenciamento.
* **Conjunto de disponibilidade**. Todos os servidores de banco de dados são adicionados a um conjunto de disponibilidade único, para garantir que pelo menos uma das VMs está ativa e em execução durante a manutenção.

### <a name="step-1---start-your-script"></a>Etapa 1 – Iniciar o script
Você pode baixar o script bash completo usado [aqui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh). Realize as seguintes etapas para alterar o script para funcionar em seu ambiente:

1. Altere os valores das variáveis a seguir com base no grupo de recursos existente implantado acima, no tópico [Pré-requisitos](#prerequisites).

    ```azurecli
    location="useast2"
    vnetName="WTestVNet"
    backendSubnetName="BackEnd"
    ```
2. Altere os valores das variáveis a seguir de acordo com os valores que deseja usar na implantação do back-end.

    ```azurecli
    backendCSName="IaaSStory-Backend"
    prmStorageAccountName="iaasstoryprmstorage"
    image="0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1"
    avSetName="ASDB"
    vmSize="Standard_DS3"
    diskSize=127
    vmNamePrefix="DB"
    osDiskName="osdiskdb"
    dataDiskPrefix="db"
    dataDiskName="datadisk"
    ipAddressPrefix="192.168.2."
    username='adminuser'
    password='adminP@ssw0rd'
    numberOfVMs=2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Etapa 2: criar recursos necessários para as VMs
1. Crie um novo serviço de nuvem para todas as VMs de back-end. Observe o uso da variável `$backendCSName` para o nome do grupo de recursos e `$location` para a região do Azure.

    ```azurecli
    azure service create --serviceName $backendCSName \
        --location $location
    ```

2. Crie uma conta de armazenamento Premium para o sistema operacional e discos de dados a ser usada por suas VMs.

    ```azurecli
    azure storage account create $prmStorageAccountName \
        --location $location \
        --type PLRS
    ```

### <a name="step-3---create-vms-with-multiple-nics"></a>Etapa 3 - Criar VMs com várias NICs
1. Inicie um loop para criar várias VMs, com base em variáveis `numberOfVMs` .

    ```azurecli
    for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
    do
    ```

2. Para cada VM, especifique o nome e o endereço IP de cada uma das duas NICs.

    ```azurecli
    nic1Name=$vmNamePrefix$suffixNumber-DA
    x=$((suffixNumber+3))
    ipAddress1=$ipAddressPrefix$x

    nic2Name=$vmNamePrefix$suffixNumber-RA
    x=$((suffixNumber+53))
    ipAddress2=$ipAddressPrefix$x
    ```

3. Crie a VM. Observe o uso do parâmetro `--nic-config` , que contém uma lista de todas as NICs com nome, sub-rede e endereço IP.

    ```azurecli
    azure vm create $backendCSName $image $username $password \
        --connect $backendCSName \
        --vm-name $vmNamePrefix$suffixNumber \
        --vm-size $vmSize \
        --availability-set $avSetName \
        --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
        --virtual-network-name $vnetName \
        --subnet-names $backendSubnetName \
        --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::
    ```

4. Para cada VM, crie dois discos de dados.

    ```azurecli
    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd

    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
    done
    ```

### <a name="step-4---run-the-script"></a>Etapa 4 – Executar o script
Agora que você baixou e alterou o script de acordo com suas necessidades, execute o script para criar VMs do banco de dados de back-end com várias NICs.

1. Salve seu script e execute-o em seu terminal **Bash** . A saída inicial será exibida, conforme mostrado abaixo:

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name IaaSStory-Backend
        info:    service create command OK
        info:    Executing command storage account create
        info:    Creating storage account
        info:    storage account create command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM

2. Depois de alguns minutos, a execução será encerrada e você verá o restante da saída conforme mostrado abaixo.

        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM
        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>Etapa 5 – Configurar o roteamento dentro do sistema operacional da VM

O protocolo DHCP do Azure atribui um gateway padrão ao primeiro adaptador de rede (primário) anexado à máquina virtual. O Azure não atribui um gateway padrão aos adaptadores de rede adicionais (secundários) anexados à máquina virtual. Portanto, por padrão, não é possível se comunicar com os recursos fora da sub-rede na qual um adaptador de rede secundária se encontra. No entanto, os adaptador de rede secundários podem se comunicar com os recursos fora da sua sub-rede. Para configurar o roteamento de adaptadores de rede secundários, consulte [Roteamento em um sistema operacional de máquina virtual com vários adaptadores de rede](virtual-network-network-interface-vm.md).
