---
title: Início Rápido – Criar uma zona DNS privada do Azure usando o Azure PowerShell
description: Neste guia de início rápido, você aprenderá a criar e gerenciar sua primeira zona e registro DNS privados usando o Azure PowerShell.
services: dns
author: rohinkoul
ms.author: rohink
ms.date: 10/20/2020
ms.topic: quickstart
ms.service: dns
ms.custom:
- mode-api
ms.openlocfilehash: 321cd9c9f85b7f1dc2540992d9db0aaefa1b9b62
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535478"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-azure-powershell"></a>Início Rápido: Criar uma zona DNS privada do Azure usando o Azure PowerShell

Este artigo explica as etapas para criar sua primeira zona e registro de DNS privado usando o Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Uma zona DNS é usada para hospedar os registros DNS para um domínio específico. Para iniciar a hospedagem do seu domínio no DNS do Azure, você precisará criar uma zona DNS para esse nome de domínio. Cada registro DNS para seu domínio é criado dentro dessa zona DNS. Para publicar uma zona de DNS privado em sua rede virtual, você deve especificar a lista de redes virtuais que podem resolver registros na zona.  Elas são chamadas de redes virtuais *vinculadas*. Quando o registro automático estiver habilitado, o DNS do Azure também atualizará os registros de zona sempre que ocorrer a criação de uma máquina virtual, o endereço IP dela mudar ou ela for excluída.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Se preferir, poderá concluir este início rápido usando a [CLI do Azure](private-dns-getstarted-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Primeiro, crie um grupo de recursos para conter a zona DNS: 

```azurepowershell
New-AzResourceGroup -name MyAzureResourceGroup -location "eastus"
```

## <a name="create-a-private-dns-zone"></a>Criar uma zona DNS privada

Uma zona DNS é criada usando o cmdlet `New-AzPrivateDnsZone` .

O seguinte exemplo cria uma rede virtual chamada **myAzureVNet**. Em seguida, ele cria uma zona DNS chamada **private.contoso.com** no grupo de recursos **MyAzureResourceGroup**, vincula a zona DNS à rede virtual **MyAzureVnet** e habilita o registro automático.

```azurepowershell
Install-Module -Name Az.PrivateDns -force

$backendSubnet = New-AzVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix "10.2.0.0/24"
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName MyAzureResourceGroup `
  -Location eastus `
  -Name myAzureVNet `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $backendSubnet

$zone = New-AzPrivateDnsZone -Name private.contoso.com -ResourceGroupName MyAzureResourceGroup

$link = New-AzPrivateDnsVirtualNetworkLink -ZoneName private.contoso.com `
  -ResourceGroupName MyAzureResourceGroup -Name "mylink" `
  -VirtualNetworkId $vnet.id -EnableRegistration
```

Se você quiser criar uma zona apenas para a resolução de nomes (sem registro de nome do host automático), poderá omitir o parâmetro `-EnableRegistration`.

### <a name="list-dns-private-zones"></a>Listar zonas de DNS privado

Omitindo o nome da zona de `Get-AzPrivateDnsZone`, você pode enumerar todas as zonas em um grupo de recursos. Essa operação retorna uma matriz de objetos de zona.

```azurepowershell
$zones = Get-AzPrivateDnsZone -ResourceGroupName MyAzureResourceGroup
$zones
```

Omitindo o nome da zona e o nome do grupo de recursos do `Get-AzPrivateDnsZone`, você pode enumerar todas as regiões na assinatura do Azure.

```azurepowershell
$zones = Get-AzPrivateDnsZone
$zones
```

## <a name="create-the-test-virtual-machines"></a>Criar as máquinas virtuais de teste

Agora, crie duas máquinas virtuais para que você possa testar a zona DNS privada:

```azurepowershell
New-AzVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM01" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389

New-AzVm `
    -ResourceGroupName "myAzureResourceGroup" `
    -Name "myVM02" `
    -Location "East US" `
    -subnetname backendSubnet `
    -VirtualNetworkName "myAzureVnet" `
    -addressprefix 10.2.0.0/24 `
    -OpenPorts 3389
```

Isso levará alguns minutos para ser concluído.

## <a name="create-an-additional-dns-record"></a>Criar um registro DNS adicional

Você cria conjuntos de registros usando o cmdlet `New-AzPrivateDnsRecordSet`. O exemplo a seguir cria um registro com o nome relativo **db** na Zona DNS **private.contoso.com** no grupo de recursos **MyAzureResourceGroup**. O nome totalmente qualificado do conjunto de registros é **db.private.contoso.com**. O tipo de registro é "A", com o endereço IP "10.2.0.4" e a TTL de 3600 segundos.

```azurepowershell
New-AzPrivateDnsRecordSet -Name db -RecordType A -ZoneName private.contoso.com `
   -ResourceGroupName MyAzureResourceGroup -Ttl 3600 `
   -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address "10.2.0.4")
```

### <a name="view-dns-records"></a>Exibir registros DNS

Para listar os registros DNS em sua zona, execute:

```azurepowershell
Get-AzPrivateDnsRecordSet -ZoneName private.contoso.com -ResourceGroupName MyAzureResourceGroup
```

## <a name="test-the-private-zone"></a>Testar a zona privada

Agora você pode testar a resolução de nome para a zona privada **private.contoso.com**.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Configurar VMs para permitir ICMP de entrada

Você pode usar o comando ping para testar a resolução de nome. Portanto, configure o firewall em ambas as máquinas virtuais para permitir pacotes ICMP de entrada.

1. Conecte-se a myVM01 e abra uma janela do Windows PowerShell com privilégios de administrador.
2. Execute o comando a seguir:

   ```powershell
   New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
   ```

Repita para myVM02.

### <a name="ping-the-vms-by-name"></a>Executar ping de VMs por nome

1. No prompt de comando do Windows PowerShell myVM02, execute ping em myVM01 usando o nome de host registrado automaticamente:

   ```
   ping myVM01.private.contoso.com
   ```

   Você deverá ver uma saída semelhante a esta:

   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```

2. Execute ping no nome **db** que você criou anteriormente:

   ```
   ping db.private.contoso.com
   ```

   Você deverá ver uma saída semelhante a esta:

   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milliseconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, exclua o grupo de recursos **MyAzureResourceGroup** para excluir os recursos criados neste artigo.

```azurepowershell
Remove-AzResourceGroup -Name MyAzureResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Cenários de Zonas Privadas do DNS do Azure](private-dns-scenarios.md)
