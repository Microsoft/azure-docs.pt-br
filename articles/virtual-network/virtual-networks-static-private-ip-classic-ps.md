---
title: Configurar endereços IP para máquinas virtuais (Clássicas) - Azure PowerShell | Microsoft Docs
description: Saiba como configurar endereços IP para máquinas virtuais (Clássico) usando o PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
tags: azure-service-management
ms.assetid: 60c7b489-46ae-48af-a453-2b429a474afd
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3fa82c2397b4c2601cdd943158054e1c8efb6b57
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596335"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-powershell"></a>Configurar endereços IP particulares para uma máquina virtual (Clássica) usando o PowerShell

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo aborda o modelo de implantação clássico. Você também pode [gerenciar um endereço IP privado estático no modelo de implantação do Gerenciador de Recursos](virtual-networks-static-private-ip-arm-ps.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Os exemplos de comando abaixo do PowerShell esperam um ambiente simples já criado. Se quiser executar os comandos da forma como eles aparecem neste documento, primeiro crie o ambiente de teste descrito em [Criar uma VNet](virtual-networks-create-vnet-classic-netcfg-ps.md).

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Você pode verificar se um endereço IP específico está disponível
Para verificar se o endereço IP *192.168.1.101* está disponível em uma VNet *TestVNet*, execute o seguinte comando do PowerShell e verifique o valor de *IsAvailable*:

    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 192.168.1.101 

Saída esperada:

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Como especificar um endereço IP privado estático ao criar uma VM
O script do PowerShell abaixo cria um novo serviço de nuvem chamado *TestService* e, em seguida, recupera uma imagem do Azure e cria uma VM denominada *DNS01* no novo serviço de nuvem usando a imagem recuperada, define a VM em uma sub-rede denominada *FrontEnd* e define *192.168.1.7* como um endereço IP privado estático para a VM:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage | where {$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name DNS01 -InstanceSize Small -ImageName $image.ImageName |
      Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! |
      Set-AzureSubnet –SubnetNames FrontEnd |
      Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
      New-AzureVM -ServiceName TestService –VNetName TestVNet

Saída esperada:

    WARNING: No deployment found in service: 'TestService'.
    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    New-AzureService     fcf705f1-d902-011c-95c7-b690735e7412 Succeeded      
    New-AzureVM          3b99a86d-84f8-04e5-888e-b6fc3c73c4b9 Succeeded  

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Como recuperar informações do endereço IP privado estático de uma VM
Para exibir as informações do IP privado estático da VM criada com o script acima, execute o seguinte comando do PowerShell e observe os valores de *IpAddress*:

    Get-AzureVM -Name DNS01 -ServiceName TestService

Saída esperada:

    DeploymentName              : TestService
    Name                        : DNS01
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 192.168.1.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : DNS01
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : rsR2-797
    AvailabilitySetName         : 
    DNSName                     : http://testservice000.cloudapp.net/
    Status                      : Provisioning
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 
    PublicIPName                : 
    NetworkInterfaces           : {}
    ServiceName                 : TestService
    OperationDescription        : Get-AzureVM
    OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
    OperationStatus             : OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Como remover um endereço IP privado estático de uma VM
Para remover o endereço IP estático e privado adicionado à VM no script acima, execute o seguinte comando do PowerShell:

    Get-AzureVM -ServiceName TestService -Name DNS01 |
      Remove-AzureStaticVNetIP |
      Update-AzureVM

Saída esperada:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Update-AzureVM       052fa6f6-1483-0ede-a7bf-14f91f805483 Succeeded

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Como adicionar um endereço IP privado estático a uma VM existente
Para adicionar um IP privado estático à VM criada com o script acima, execute o comando a seguir:

    Get-AzureVM -ServiceName TestService -Name DNS01 |
      Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
      Update-AzureVM

Saída esperada:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Update-AzureVM       77d8cae2-87e6-0ead-9738-7c7dae9810cb Succeeded 

## <a name="set-ip-addresses-within-the-operating-system"></a>Definir endereços IP no sistema operacional

É recomendável que você não atribua estaticamente o IP privado atribuído à máquina virtual do Azure no sistema operacional de uma VM, a menos que seja necessário. Se você definir manualmente o endereço IP privado no sistema operacional, verifique se é o mesmo endereço que o endereço IP privado atribuído à VM do Azure ou se é possível perder a conectividade com a máquina virtual. Nunca atribua manualmente o endereço IP público atribuído a uma máquina virtual do Azure no sistema operacional da máquina virtual.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre endereços [IP públicos reservados](virtual-networks-reserved-public-ip.md) .
* Saiba mais sobre endereços [ILPIP (IP público em nível de instância)](virtual-networks-instance-level-public-ip.md) .
* Consulte as [APIs REST de IP reservado](https://msdn.microsoft.com/library/azure/dn722420.aspx).

