---
title: Delegar um subdomínio do DNS do Azure usando o Azure PowerShell
description: Saiba como delegar um subdomínio do DNS do Azure usando o Azure PowerShell.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 2/7/2019
ms.author: victorh
ms.openlocfilehash: 40b2a4d98e6269d9740856ba44c1043af75ce1b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60791109"
---
# <a name="delegate-an-azure-dns-subdomain-using-azure-powershell"></a>Delegar um subdomínio do DNS do Azure usando o Azure PowerShell

Você pode usar o Azure PowerShell para delegar um subdomínio de DNS. Por exemplo, se você tem o domínio contoso.com, é possível delegar um subdomínio chamado *engineering* a outra zona separada, que pode ser administrada separadamente da zona contoso.com.

Se preferir, você pode delegar um subdomínio usando o [Portal do Azure](delegate-subdomain.md).

> [!NOTE]
> Contoso.com é usado como exemplo ao longo deste artigo. Substitua seu próprio nome de domínio para contoso.com.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para delegar um subdomínio de DNS do Azure, primeiro você deve delegar seu domínio público ao DNS do Azure. Veja [Delegar um domínio ao DNS do Azure](./dns-delegate-domain-azure-dns.md) para obter instruções sobre como configurar os servidores de nome para delegação. Depois que seu domínio tiver sido delegado para a zona DNS do Azure, você poderá delegar um subdomínio.

## <a name="create-a-zone-for-your-subdomain"></a>Criar uma zona para o subdomínio

Primeiro, crie a zona para o subdomínio **engineering**.

`New-AzDnsZone -ResourceGroupName <resource group name> -Name engineering.contoso.com`

## <a name="note-the-name-servers"></a>Observe os servidores de nome

Em seguida, observe os quatro servidores de nomes do subdomínio engineering.

`Get-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -RecordType NS`

## <a name="create-a-test-record"></a>Criar um registro de teste

Criar um registro **A** na zona engineering para uso em testes.

   `New-AzDnsRecordSet -ZoneName engineering.contoso.com -ResourceGroupName <resource group name> -Name www -RecordType A -ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address 10.10.10.10)`.

## <a name="create-an-ns-record"></a>Criar um registro NS

Em seguida, crie um registro de servidor de nome (NS) para a zona **engineering** na zona contoso.com.

```azurepowershell
$Records = @()
$Records += New-AzDnsRecordConfig -Nsdname <name server 1 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 2 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 3 noted previously>
$Records += New-AzDnsRecordConfig -Nsdname <name server 4 noted previously>
$RecordSet = New-AzDnsRecordSet -Name engineering -RecordType NS -ResourceGroupName <resource group name> -TTL 3600 -ZoneName contoso.com -DnsRecords $Records
```

## <a name="test-the-delegation"></a>Testar a delegação

Use nslookup para testar a delegação.

1. Abra uma janela do PowerShell.
2. No prompt de comando, digite `nslookup www.engineering.contoso.com.`
3. Você deve receber uma resposta não autoritativa mostrando o endereço **10.10.10.10**.

## <a name="next-steps"></a>Próximas etapas

Saiba como [Configurar DNS reverso para serviços hospedados no Azure](dns-reverse-dns-for-azure-services.md).