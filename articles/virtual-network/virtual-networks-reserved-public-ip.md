---
title: "Gerenciar endereços IP reservados do Azure (Clássico) – PowerShell | Microsoft Docs"
description: "Entender os endereços IP reservados (Clássico) e como gerenciá-los usando o PowerShell."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 34652a55-3ab8-4c2d-8fb2-43684033b191
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2016
ms.author: jdial
ms.openlocfilehash: 5e9c83cebec96c6bc8afd53b0c637d7af899746f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="reserved-ip-addresses-classic"></a>Endereços IP reservados (Clássico)

> [!div class="op_single_selector"]
> * [portal do Azure](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [CLI do Azure](virtual-network-deploy-static-pip-arm-cli.md)
> * [Modelo](virtual-network-deploy-static-pip-arm-template.md)
> * [PowerShell (Clássico)](virtual-networks-reserved-public-ip.md)

Os endereços IP no Azure recaem em duas categorias: dinâmicos e reservados. Os endereços IP públicos gerenciados pelo Azure são dinâmicos por padrão. Isso significa que o endereço IP usado para um determinado serviço de nuvem (VIP) ou para acessar uma VM ou instância de função diretamente (ILPIP) pode mudar de tempos em tempos, quando recursos forem desligados ou interrompidos (desalocados).

Para evitar que endereços IP sejam alterados, é possível reservar um endereço IP. Os IPs reservados podem ser usados apenas como um VIP, garantindo que o endereço IP do serviço de nuvem permaneça o mesmo, mesmo se os recursos forem desligados ou interrompidos (desalocados). Além disso, você pode converter IPs dinâmicos existentes usados como um VIP para um endereço IP reservado.

> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e clássico](../azure-resource-manager/resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. Saiba como reservar um endereço IP estático público usando o [Modelo de implantação do Gerenciador de Recursos](virtual-network-ip-addresses-overview-arm.md).

Para saber mais sobre endereços IP no Azure, leia o artigo [Endereços IP](virtual-network-ip-addresses-overview-classic.md).

## <a name="when-do-i-need-a-reserved-ip"></a>Quando eu precisarei de um IP reservado?
* **Você deseja garantir que o IP seja reservado em sua assinatura**. Se você quiser reservar um endereço IP que não seja liberado da sua assinatura sob nenhuma circunstância, você deverá usar um IP público reservado.  
* **Você deseja que o IP permaneça com seu serviço de nuvem, mesmo nos estados parados ou desalocados (VMs)**. Se você quiser que seu serviço seja acessado usando um endereço IP que não seja alterado, mesmo quando VMs do serviço de nuvem estejam paradas ou desalocadas.
* **Você deseja garantir que o tráfego de saída do Azure use um endereço IP previsível**. Você pode ter seu firewall local configurado para permitir apenas o tráfego de endereços IP específicos. Ao reservar um IP, você conhecerá o endereço IP de origem e não terá de atualizar suas regras de firewall devido a uma alteração de IP.

## <a name="faq"></a>Perguntas frequentes
1. Posso usar um IP reservado para todos os serviços do Azure? <br>
    Não. Os IPs reservados só podem ser usados para VMs e funções de instância de serviço de nuvem exposto através de um VIP.
2. Quantos IPs reservados eu posso ter? <br>
    Para obter detalhes, consulte o artigo [Limites do Azure](../azure-subscription-service-limits.md#networking-limits).
3. Há uma cobrança para IPs reservados? <br>
    Às vezes. Veja [Detalhes Sobre Preços de Endereços IP Reservados](http://go.microsoft.com/fwlink/?LinkID=398482) para obter detalhes sobre preços.
4. Como eu reservo um endereço IP? <br>
    É possível usar o PowerShell, a [API REST de Gerenciamento do Azure](https://msdn.microsoft.com/library/azure/dn722420.aspx) ou o [Portal do Azure](https://portal.azure.com) para reservar um endereço IP em uma região do Azure. Um endereço IP reservado é associado à sua assinatura.
5. Posso usar um IP reservado com VNets baseadas em grupos de afinidades? <br>
    Não. Os IPs reservados têm suporte apenas em redes virtuais regionais. VNets associadas a grupos de afinidades não dão suporte a IPs reservados. Para obter mais informações sobre a associação de uma VNet a uma região ou a um grupo de afinidades, veja o artigo [Sobre VNets regionais e grupos de afinidades](virtual-networks-migrate-to-regional-vnet.md).

## <a name="manage-reserved-vips"></a>Gerenciar VIPs reservados

Certifique-se de ter instalado e configurado o Azure PowerShell seguindo as etapas do artigo [Instalar e configurar o PowerShell](/powershell/azure/overview). 

Antes de poder usar IPs reservados, você deverá adicioná-los à sua assinatura. Para criar um IP reservado do pool de endereços IP públicos disponíveis no local *EUA Central* , execute o seguinte comando:

```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
```

Observe, entretanto, que você não pode especificar qual IP está sendo reservado. Para exibir quais endereços IP estão reservados em sua assinatura, execute o seguinte comando do PowerShell e observe os valores de *ReservedIPName* e *Endereço*:

```powershell
Get-AzureReservedIP
```

Saída esperada:

    ReservedIPName       : MyReservedIP
    Address              : 23.101.114.211
    Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
    Label                :
    Location             : Central US
    State                : Created
    InUse                : False
    ServiceName          :
    DeploymentName       :
    OperationDescription : Get-AzureReservedIP
    OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
    OperationStatus      : Succeeded

>[!NOTE]
>Quando você cria um endereço IP reservado com o PowerShell, não é possível especificar um grupo de recursos no qual criar o IP reservado. O Azure o coloca automaticamente em um grupo de recursos denominado *Default-Networking*. Se você criar o IP reservado usando o [Portal do Azure](http://portal.azure.com), você poderá especificar qualquer grupo de recursos que você escolher. No entanto, se você criar o IP reservado em um grupo de recursos diferente do *Default-Networking*, sempre que você dizer referência ao IP reservado com comandos como `Get-AzureReservedIP` e `Remove-AzureReservedIP`, você deverá fazer referência ao nome *Group resource-group-name reserved-ip-name*.  Por exemplo, se você criar um IP reservado denominado *myReservedIP* em um grupo de recursos denominado *myResourceGroup*, você deverá fazer referência ao nome do IP reservado como *Group myResourceGroup myReservedIP*.   

Depois que um IP for reservado, ele permanecerá associado à sua assinatura até você excluí-lo. Para excluir um IP reservado, execute o seguinte comando do PowerShell:

```powershell
Remove-AzureReservedIP -ReservedIPName "MyReservedIP"
```

## <a name="reserve-the-ip-address-of-an-existing-cloud-service"></a>Reservar o endereço IP de um serviço de nuvem existente
É possível reservar o endereço IP de um serviço de nuvem existente adicionando o parâmetro `-ServiceName`. Para reservar o endereço IP de um serviço de nuvem *TestService* no local *EUA Central*, execute o seguinte comando do PowerShell:

```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService
```

## <a name="associate-a-reserved-ip-to-a-new-cloud-service"></a>Associar um IP reservado a um novo serviço de nuvem
O script a seguir cria um novo IP reservado e o associa a um novo serviço de nuvem chamado *TestService*.

```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"
```

> [!NOTE]
> Quando você cria um IP reservado a ser usado com um serviço de nuvem, você ainda fará referência à VM usando *VIP:&lt;número da porta>* para comunicação de entrada. Reservar um IP não significa que você pode conectar-se à VM diretamente. O IP reservado é atribuído ao serviço de nuvem no qual a VM foi implantada. Se você quiser se conectar diretamente a uma VM por IP, precisará configurar um IP público em nível de instância. Um IP público em nível de instância é um tipo de IP público (chamado de ILPIP) atribuído diretamente à sua VM. Ele não pode ser reservado. Para obter mais informações, leia o artigo [IP Público em Nível de Instância (ILPIP)](virtual-networks-instance-level-public-ip.md).
> 

## <a name="remove-a-reserved-ip-from-a-running-deployment"></a>Remover um IP reservado de uma implantação em execução
Para remover um IP reservado adicionado a um novo serviço de nuvem, execute o seguinte comando do PowerShell:

```powershell
Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService
```

> [!NOTE]
> Removendo um IP reservado de uma implantação em execução não remove a reserva de sua assinatura. Ele simplesmente libera o IP para ser usado por outro recurso em sua assinatura.
> 

## <a name="associate-a-reserved-ip-to-a-running-deployment"></a>Associar um IP reservado a uma implantação em execução
Os comandos a seguir criam um serviço de nuvem denominado *TestService2* com uma nova VM denominada *TestVM2*. O IP reservado existente chamado *MyReservedIP* é então associado ao serviço de nuvem.

```powershell
$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService2 -Location "Central US"

Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2
```

## <a name="associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>Associar um IP reservado a um serviço de nuvem usando um arquivo de configuração de serviço
Você também pode associar um IP reservado a um serviço de nuvem usando um arquivo de configuração de serviço (CSCFG). O XML de exemplo a seguir mostra como configurar um serviço de nuvem para usar um VIP reservado denominado *MyReservedIP*:

    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <ReservedIPs>
           <ReservedIP name="MyReservedIP"/>
          </ReservedIPs>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## <a name="next-steps"></a>Próximas etapas
* Entenda como o [endereçamento IP](virtual-network-ip-addresses-overview-classic.md) funciona no modelo de implantação clássica.
* Saiba mais sobre [endereços IP privados reservados](virtual-networks-reserved-private-ip.md).
* Saiba mais sobre [endereços ILPIP (IP Público de Nível de Instância)](virtual-networks-instance-level-public-ip.md).

