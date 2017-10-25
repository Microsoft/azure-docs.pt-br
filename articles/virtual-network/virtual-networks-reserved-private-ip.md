---
title: "IP privado interno estático – VM do Azure – Clássico"
description: "Noções básicas sobre IPs estáticos internos (DIPs) e como gerenciá-los"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: 93444c6f-af1b-41f8-a035-77f5c0302bf0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2016
ms.author: jdial
ms.openlocfilehash: cf9ee59ca4e44ed01836c2efb1f4df5f073bf6e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-set-a-static-internal-private-ip-address-using-powershell-classic"></a>Como definir um endereço IP privado interno estático usando o PowerShell (clássico)
Na maioria dos casos, você não precisará especificar um endereço IP interno estático para sua máquina virtual. As VMs de uma rede virtual receberão automaticamente endereço IP interno de um intervalo especificado por você. Mas, em alguns casos, a especificação de um endereço IP estático para uma determinada VM fará sentido. Por exemplo, se a sua VM se destinar à execução de DNS ou a ser um controlador de domínio. Um endereço IP interno estático permanece com a VM mesmo em um estado de interrupção/desprovisionamento. 

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e clássico](../azure-resource-manager/resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação clássica. A Microsoft recomenda que a maioria das implantações novas use o [modelo de implantação do Gerenciador de Recursos](virtual-networks-static-private-ip-arm-ps.md).
> 
> 

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Você pode verificar se um endereço IP específico está disponível
Para verificar se o endereço IP *10.0.0.7* está disponível em uma VNet *TestVnet*, execute o seguinte comando do PowerShell e verifique o valor de *IsAvailable*:

    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

> [!NOTE]
> Se você quiser testar o comando acima em um ambiente seguro, siga as diretrizes em [Criar uma rede virtual (clássica)](virtual-networks-create-vnet-classic-pportal.md) para criar uma rede virtual denominada *TestVnet* e verificar se ela usa o espaço de endereço *10.0.0.0/8*.
> 
> 

## <a name="how-to-specify-a-static-internal-ip-when-creating-a-vm"></a>Como especificar um endereço IP interno estático ao criar uma máquina virtual
O script do PowerShell abaixo cria um novo serviço de nuvem chamado *TestService* e, em seguida, recupera uma imagem do Azure e cria uma VM denominada *TestVM* no novo serviço de nuvem usando a imagem recuperada, define a VM em uma sub-rede denominada *Subnet-1* e define *10.0.0.7* como um endereço IP interno estático para a VM:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzureSubnet –SubnetNames Subnet-1 `
    | Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
    | New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## <a name="how-to-retrieve-static-internal-ip-information-for-a-vm"></a>Como recuperar informações de IP interno estático para uma VM
Para exibir as informações do IP interno estático da VM criado com o script acima, execute o seguinte comando do PowerShell e observe os valores de *IpAddress*:

    Get-AzureVM -Name TestVM -ServiceName TestService

    DeploymentName              : TestService
    Name                        : TestVM
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 10.0.0.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : TestVM
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

## <a name="how-to-remove-a-static-internal-ip-from-a-vm"></a>Como remover um endereço IP interno estático de uma VM
Para remover o IP interno estático adicionado à VM no script acima, execute o seguinte comando do PowerShell:

    Get-AzureVM -ServiceName TestService -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM

## <a name="how-to-add-a-static-internal-ip-to-an-existing-vm"></a>Como adicionar um IP interno estático a uma VM existente
Para adicionar um IP interno estático à VM criada usando o script acima, execute o comando a seguir:

    Get-AzureVM -ServiceName TestService000 -Name TestVM `
    | Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
    | Update-AzureVM

## <a name="next-steps"></a>Próximas etapas
[IP Reservado](virtual-networks-reserved-public-ip.md)

[IP Público em Nível de Instância (ILPIP)](virtual-networks-instance-level-public-ip.md)

[APIs REST com IP Reservado](https://msdn.microsoft.com/library/azure/dn722420.aspx)

