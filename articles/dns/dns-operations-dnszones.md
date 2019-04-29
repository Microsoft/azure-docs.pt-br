---
title: Gerenciar as zonas DNS no DNS do Azure - PowerShell | Microsoft Docs
description: Você pode gerenciar zonas DNS usando o Azure Powershell. Este artigo descreve como atualizar, excluir e criar zonas DNS no DNS do Azure
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.assetid: a67992ab-8166-4052-9b28-554c5a39e60c
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: victorh
ms.openlocfilehash: 1ef44c16a8ae3b6254a6cea252501b72ddb24a5c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61293605"
---
# <a name="how-to-manage-dns-zones-using-powershell"></a>Como gerenciar as zonas DNS usando o PowerShell

> [!div class="op_single_selector"]
> * [Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [CLI clássica do Azure](dns-operations-dnszones-cli-nodejs.md)
> * [CLI do Azure](dns-operations-dnszones-cli.md)

Este artigo mostra como gerenciar suas zonas DNS usando o Azure PowerShell. Você também pode gerenciar as zonas DNS usando a plataforma cruzada [CLI do Azure](dns-operations-dnszones-cli.md) ou o portal do Azure.

Este guia trata especificamente das zonas DNS públicas. Para obter informações sobre como usar o Azure PowerShell para gerenciar Zonas Privadas no DNS do Azure, consulte [Introdução às Zonas Privadas do DNS do Azure usando o Azure PowerShell](private-dns-getstarted-powershell.md).

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]


## <a name="create-a-dns-zone"></a>Criar uma zona DNS

Uma zona DNS é criada usando o cmdlet `New-AzureRmDnsZone` .

O exemplo a seguir cria uma zona DNS chamada *contoso.com* no grupo de recursos chamado *MyResourceGroup*:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

O exemplo a seguir mostra como criar uma zona DNS com duas [marcas do Azure Resource Manager](dns-zones-records.md#tags), *project = demo* e *env = test*:

```powershell
New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

O DNS do Azure agora também oferece suporte a zonas de DNS privado (atualmente em visualização pública).  Para saber mais sobre as zonas DNS privadas, consulte [Usar o Azure DNS para domínios privados](private-dns-overview.md). Para obter um exemplo de como criar uma zona DNS privada, consulte [Introdução às zonas privadas do DNS do Azure usando o PowerShell](./private-dns-getstarted-powershell.md).

## <a name="get-a-dns-zone"></a>Obter uma zona DNS

Para recuperar uma zona DNS, use o cmdlet `Get-AzureRmDnsZone` . Esta operação retornará um objeto de zona DNS correspondente a uma zona existente no DNS do Azure. O objeto contém dados sobre a zona (como o número de conjuntos de registros), mas não contém os conjuntos de registro em si (veja `Get-AzureRmDnsRecordSet`).

```powershell
Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

Name                  : contoso.com
ResourceGroupName     : myresourcegroup
Etag                  : 00000003-0000-0000-8ec2-f4879750d201
Tags                  : {project, env}
NameServers           : {ns1-01.azure-dns.com., ns2-01.azure-dns.net., ns3-01.azure-dns.org.,
                        ns4-01.azure-dns.info.}
NumberOfRecordSets    : 2
MaxNumberOfRecordSets : 5000
```

## <a name="list-dns-zones"></a>Listar as zonas DNS

Omitindo o nome da zona de `Get-AzureRmDnsZone`, você pode enumerar todas as zonas em um grupo de recursos. Essa operação retorna uma matriz de objetos de zona.

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

Omitindo o nome da zona e o nome do grupo de recursos do `Get-AzureRmDnsZone`, você pode enumerar todas as regiões na assinatura do Azure.

```powershell
$zoneList = Get-AzureRmDnsZone
```

## <a name="update-a-dns-zone"></a>Atualizar uma zona DNS

As alterações a um recurso da zona DNS podem ser feitas usando o `Set-AzureRmDnsZone`. Este cmdlet não atualiza nenhum dos conjuntos de registros DNS dentro da zona (consulte [Como gerenciar registros DNS](dns-operations-recordsets.md)). Ele só é usado para atualizar as propriedades do recurso da zona em si. As propriedades de zona graváveis são limitadas às [‘marcas’ do Azure Resource Manager para o recurso de zona](dns-zones-records.md#tags).

Use um dos dois métodos a seguir para atualizar uma zona DNS:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>Especifique a zona usando o nome da zona e o grupo de recursos

Essa abordagem substitui as marcas de zona existente com os valores especificados.

```powershell
Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @{ project="demo"; env="test" }
```

### <a name="specify-the-zone-using-a-zone-object"></a>Especifique a zona usando um objeto de $zone

Essa abordagem recupera o objeto de zona existente, modifica as marcas e, em seguida, confirma as alterações. Dessa forma, as marcas existentes podem ser preservadas.

```powershell
# Get the zone object
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

# Remove an existing tag
$zone.Tags.Remove("project")

# Add a new tag
$zone.Tags.Add("status","approved")

# Commit changes
Set-AzureRmDnsZone -Zone $zone
```

Ao usar `Set-AzureRmDnsZone` com um objeto $zone, [verificações de Etag](dns-zones-records.md#etags) serão usadas para garantir que as alterações simultâneas não sejam substituídas. Você pode usar o argumento `-Overwrite` opcional para omitir essas verificações.

## <a name="delete-a-dns-zone"></a>Excluir uma zona DNS

As zonas DNS podem ser excluídas usando o cmdlet `Remove-AzureRmDnsZone`.

> [!NOTE]
> Excluir uma zona DNS também excluirá todos os registros DNS na zona. Essa operação não pode ser desfeita. Se a zona DNS estiver em uso, serviços que usam a zona falharão quando a zona for excluída.
>
>Para se proteger contra a exclusão acidental da zona, consulte [Proteger registros e zonas DNS](dns-protect-zones-recordsets.md).


Use um dos dois métodos a seguir para excluir uma zona DNS:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Especifique a zona usando o nome da zona e o nome do grupo de recursos

```powershell
Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>Especifique a zona usando um objeto de $zone

Você pode especificar a zona a ser excluído usando um `$zone` objeto retornado por `Get-AzureRmDnsZone`.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
Remove-AzureRmDnsZone -Zone $zone
```

O objeto de zona também pode ser redirecionado em vez de ser passado como um parâmetro:

```powershell
Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone

```

Assim como com `Set-AzureRmDnsZone`, especificar a zona usando um objeto `$zone` habilita as verificações de Etag para garantir que as alterações simultâneas não sejam excluídas. Use o `-Overwrite` para suprimir essas verificações.

## <a name="confirmation-prompts"></a>Prompts de confirmação

Os cmdlets `New-AzureRmDnsZone`, `Set-AzureRmDnsZone` e `Remove-AzureRmDnsZone` oferecem suporte aos prompts de confirmação.

`New-AzureRmDnsZone` e `Set-AzureRmDnsZone` solicitam confirmação caso a variável de preferência do PowerShell `$ConfirmPreference` tenha um valor `Medium` ou inferior. Dado o impacto potencialmente alto da exclusão de uma zona DNS, o `Remove-AzureRmDnsZone` cmdlet solicita confirmação se a `$ConfirmPreference` variável do PowerShell tem qualquer valor diferente de `None`.

Desde o valor padrão para `$ConfirmPreference` é `High`, apenas `Remove-AzureRmDnsZone` solicitará uma confirmação por padrão.

Você pode substituir a configuração `$ConfirmPreference` atual usando o parâmetro `-Confirm`. Se você especificar `-Confirm` ou `-Confirm:$True`, o cmdlet solicitará uma confirmação antes de executar. Se você especificar `-Confirm:$False`, o cmdlet não solicitará uma confirmação.

Para obter mais informações sobre `-Confirm` e `$ConfirmPreference`, consulte [Sobre as Variáveis de Preferência](/powershell/module/microsoft.powershell.core/about/about_preference_variables).

## <a name="next-steps"></a>Próximas etapas

Saiba como [gerenciar conjuntos de registros e registros](dns-operations-recordsets.md) em sua zona DNS.
<br>
Saiba como [delegar seu domínio ao DNS do Azure](dns-domain-delegation.md).
<br>
Examine a [documentação de referência do PowerShell do DNS do Azure](/powershell/module/azurerm.dns).

