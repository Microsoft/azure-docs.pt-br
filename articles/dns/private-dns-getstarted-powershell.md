---
title: "Introdução às zonas privadas do DNS do Azure usando o PowerShell | Microsoft Docs"
description: "Saiba como criar uma zona e registro de DNS privado no DNS do Azure. Este é uma guia passo a passo para criar e gerenciar sua primeira zona e registro de DNS privado usando o PowerShell."
services: dns
documentationcenter: na
author: garbrad
manager: 
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/04/2017
ms.author: garbrad
ms.openlocfilehash: c5d4b33682f9b5d33e74d45973d5fbd76cf16465
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-dns-private-zones-using-powershell"></a>Introdução às zonas privadas do DNS do Azure usando o PowerShell

Este artigo explica as etapas para criar sua primeira zona e registro de DNS privado usando o Azure PowerShell.

Uma zona DNS é usada para hospedar os registros DNS para um domínio específico. Para iniciar a hospedagem do seu domínio no DNS do Azure, você precisará criar uma zona DNS para esse nome de domínio. Cada registro DNS para seu domínio é criado dentro dessa zona DNS. Para publicar uma zona de DNS privado em sua rede virtual, você deve especificar a lista de redes virtuais que podem resolver registros na zona.  Chamamos essas redes de "redes de resolução".  Você também pode especificar um conjunto de redes virtuais para o qual o DNS do Azure manterá registros de nome de host sempre que uma VM for criada, mudar de IP ou for destruída.  Chamamos essas redes de "redes de registro".

Como, no momento, esse recurso é uma visualização gerenciada, um módulo de visualização do PowerShell será fornecido.

[!INCLUDE [private-dns-preview-notice](../../includes/private-dns-preview-notice.md)]

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Antes de criar a zona DNS, um grupo de recursos é criado para conter a zona DNS. O código a seguir mostra o comando.

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "westus"
```

## <a name="create-a-dns-zone"></a>Criar uma zona DNS

Uma zona DNS é criada usando o cmdlet `New-AzureRmDnsZone` . O exemplo a seguir cria uma zona DNS chamada *contoso.local* no grupo de recursos chamado *MyResourceGroup* e a disponibiliza na rede virtual chamada *MyAzureVnet* . Use o exemplo para criar uma zona DNS, substituindo os valores pelos seus próprios.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -ResolutionVirtualNetworkId @($vnet.Id)
```

Se você precisar que o Azure crie automaticamente os registros de nome de host na zona, use o parâmetro *RegistrationVirtualNetworkId* em vez de *ResolutionVirtualNetworkId*.  Redes virtuais de registro são habilitadas automaticamente para a resolução.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -RegistrationVirtualNetworkId @($vnet.Id)
```


## <a name="create-a-dns-record"></a>Criar um registro DNS

Você cria conjuntos de registros usando o cmdlet `New-AzureRmDnsRecordSet`. O exemplo a seguir cria um registro com o nome relativo "db" na Zona DNS "contoso.local", no grupo de recursos "MyResourceGroup". O nome totalmente qualificado do conjunto de registros é "db.contoso.local". O tipo de registro é "A", com o endereço IP "10.0.0.4" e a TTL de 3600 segundos.

```powershell
New-AzureRmDnsRecordSet -Name db -RecordType A -ZoneName contoso.local -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "10.0.0.4")
```

Para outros tipos de registros, para conjuntos de registros com mais de um registro, e para modificar registros existentes, consulte [Gerenciar registros DNS e conjuntos de registros usando o Azure PowerShell](dns-operations-recordsets.md). 


## <a name="view-records"></a>Exibir registros

Para listar os registros DNS em sua zona, use:

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.local -ResourceGroupName MyResourceGroup
```

## <a name="delete-all-resources"></a>Excluir todos os recursos

Para excluir todos os recursos criados neste artigo, execute as seguintes etapas:

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as zonas DNS privadas, consulte [Usar o Azure DNS para domínios privados](private-dns-overview.md).

Para saber mais sobre como gerenciar os registros DNS no DNS do Azure, consulte [Gerenciar registros DNS e conjuntos de registros no DNS do Azure usando o PowerShell](dns-operations-recordsets.md).

