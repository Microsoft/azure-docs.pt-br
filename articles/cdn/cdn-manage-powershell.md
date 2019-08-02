---
title: Gerenciar a CDN do Azure com o PowerShell | Microsoft Docs
description: Saiba como usar os cmdlets do Azure PowerShell para gerenciar a CDN do Azure.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: fb6f57a5-6e26-4847-8fd9-b51fb05a79eb
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: magattus
ms.openlocfilehash: 44274c2a46ce51e51d3d8f16d96a0b50c43a3aa1
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593699"
---
# <a name="manage-azure-cdn-with-powershell"></a>Gerenciar a CDN do Azure com o PowerShell
O PowerShell fornece um dos métodos mais flexíveis para gerenciar os perfis e os pontos de extremidade de CDN do Azure.  Você pode usar o PowerShell interativamente ou escrevendo scripts para automatizar as tarefas de gerenciamento.  Este tutorial demonstra várias tarefas mais comuns que você pode fazer com o PowerShell para gerenciar os perfis e os pontos de extremidade de CDN do Azure.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para usar o PowerShell para gerenciar os perfis e os pontos de extremidade de CDN do Azure, você deve ter o módulo do Azure PowerShell instalado.  Para aprender a instalar o Azure PowerShell e conectar o Azure usando o cmdlet `Connect-AzAccount` , consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

> [!IMPORTANT]
> Você deve fazer logon com `Connect-AzAccount` antes de executar os cmdlets do Azure PowerShell.
> 
> 

## <a name="listing-the-azure-cdn-cmdlets"></a>Listando os cmdlets de CDN do Azure
Você pode listar todos os cmdlets de CDN do Azure usando o cmdlet `Get-Command` .

```text
PS C:\> Get-Command -Module Az.Cdn

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Get-AzCdnCustomDomain                         2.0.0      Az.Cdn
Cmdlet          Get-AzCdnEndpoint                             2.0.0      Az.Cdn
Cmdlet          Get-AzCdnEndpointNameAvailability             2.0.0      Az.Cdn
Cmdlet          Get-AzCdnOrigin                               2.0.0      Az.Cdn
Cmdlet          Get-AzCdnProfile                              2.0.0      Az.Cdn
Cmdlet          Get-AzCdnProfileSsoUrl                        2.0.0      Az.Cdn
Cmdlet          New-AzCdnCustomDomain                         2.0.0      Az.Cdn
Cmdlet          New-AzCdnEndpoint                             2.0.0      Az.Cdn
Cmdlet          New-AzCdnProfile                              2.0.0      Az.Cdn
Cmdlet          Publish-AzCdnEndpointContent                  2.0.0      Az.Cdn
Cmdlet          Remove-AzCdnCustomDomain                      2.0.0      Az.Cdn
Cmdlet          Remove-AzCdnEndpoint                          2.0.0      Az.Cdn
Cmdlet          Remove-AzCdnProfile                           2.0.0      Az.Cdn
Cmdlet          Set-AzCdnEndpoint                             2.0.0      Az.Cdn
Cmdlet          Set-AzCdnOrigin                               2.0.0      Az.Cdn
Cmdlet          Set-AzCdnProfile                              2.0.0      Az.Cdn
Cmdlet          Start-AzCdnEndpoint                           2.0.0      Az.Cdn
Cmdlet          Stop-AzCdnEndpoint                            2.0.0      Az.Cdn
Cmdlet          Test-AzCdnCustomDomain                        2.0.0      Az.Cdn
Cmdlet          Unpublish-AzCdnEndpointContent                2.0.0      Az.Cdn
```

## <a name="getting-help"></a>Obtendo ajuda
Você pode obter ajuda com qualquer um desses cmdlets usando o cmdlet `Get-Help` .  `Get-Help` fornece o uso e a sintaxe, e opcionalmente mostra exemplos.

```text
PS C:\> Get-Help Get-AzCdnProfile

NAME
    Get-AzCdnProfile

SYNOPSIS
    Gets an Azure CDN profile.


SYNTAX
    Get-AzCdnProfile [-ProfileName <String>] [-ResourceGroupName <String>] [-InformationAction
    <ActionPreference>] [-InformationVariable <String>] [<CommonParameters>]


DESCRIPTION
    Gets an Azure CDN profile and all related information.


RELATED LINKS

REMARKS
    To see the examples, type: "get-help Get-AzCdnProfile -examples".
    For more information, type: "get-help Get-AzCdnProfile -detailed".
    For technical information, type: "get-help Get-AzCdnProfile -full".

```

## <a name="listing-existing-azure-cdn-profiles"></a>Listando os perfis CDN do Azure existentes
O cmdlet `Get-AzCdnProfile` sem nenhum parâmetro recupera todos os seus perfis CDN existentes.

```powershell
Get-AzCdnProfile
```

Essa saída pode ser transferida para os cmdlets para fazer uma enumeração.

```powershell
# Output the name of all profiles on this subscription.
Get-AzCdnProfile | ForEach-Object { Write-Host $_.Name }

# Return only **Azure CDN from Verizon** profiles.
Get-AzCdnProfile | Where-Object { $_.Sku.Name -eq "Standard_Verizon" }
```

Você também pode retornar um único perfil especificando o grupo de recursos e o nome do perfil.

```powershell
Get-AzCdnProfile -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
```

> [!TIP]
> É possível ter vários perfis CDN com o mesmo nome, desde que eles estejam em grupos de recursos diferentes.  Omitir o parâmetro `ResourceGroupName` retorna todos os perfis com um nome correspondente.
> 
> 

## <a name="listing-existing-cdn-endpoints"></a>Listando os pontos de extremidade CDN existentes
`Get-AzCdnEndpoint` pode recuperar um ponto de extremidade individual ou todos os pontos de extremidade em um perfil.  

```powershell
# Get a single endpoint.
Get-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Get all of the endpoints on a given profile. 
Get-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG

# Return all of the endpoints on all of the profiles.
Get-AzCdnProfile | Get-AzCdnEndpoint

# Return all of the endpoints in this subscription that are currently running.
Get-AzCdnProfile | Get-AzCdnEndpoint | Where-Object { $_.ResourceState -eq "Running" }
```

## <a name="creating-cdn-profiles-and-endpoints"></a>Criando perfis e pontos de extremidade CDN
`New-AzCdnProfile` e `New-AzCdnEndpoint` são usados para criar perfis e pontos de extremidade CDN. Há suporte para as SKUs a seguir:
- Standard_Verizon
- Premium_Verizon
- Custom_Verizon
- Standard_Akamai
- Standard_Microsoft
- Standard_ChinaCdn

```powershell
# Create a new profile
New-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku Standard_Akamai -Location "Central US"

# Create a new endpoint
New-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Location "Central US" -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

# Create a new profile and endpoint (same as above) in one line
New-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku Standard_Akamai -Location "Central US" | New-AzCdnEndpoint -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

```

## <a name="checking-endpoint-name-availability"></a>Verificando a disponibilidade do nome do ponto de extremidade
`Get-AzCdnEndpointNameAvailability` retorna um objeto indicando se um nome do ponto de extremidade está disponível.

```powershell
# Retrieve availability
$availability = Get-AzCdnEndpointNameAvailability -EndpointName "cdnposhdoc"

# If available, write a message to the console.
If($availability.NameAvailable) { Write-Host "Yes, that endpoint name is available." }
Else { Write-Host "No, that endpoint name is not available." }
```

## <a name="adding-a-custom-domain"></a>Adicionando um domínio personalizado
`New-AzCdnCustomDomain` adiciona um nome de domínio personalizado a um ponto de extremidade existente.

> [!IMPORTANT]
> Você deve configurar o CNAME com seu provedor DNS como descrito em [Como mapear o Domínio Personalizado para o ponto de extremidade CDN (Rede de Distribuição de Conteúdo)](cdn-map-content-to-custom-domain.md).  Você pode testar o mapeamento antes de modificar o ponto de extremidade usando `Test-AzCdnCustomDomain`.
> 
> 

```powershell
# Get an existing endpoint
$endpoint = Get-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Check the mapping
$result = Test-AzCdnCustomDomain -CdnEndpoint $endpoint -CustomDomainHostName "cdn.contoso.com"

# Create the custom domain on the endpoint
If($result.CustomDomainValidated){ New-AzCdnCustomDomain -CustomDomainName Contoso -HostName "cdn.contoso.com" -CdnEndpoint $endpoint }
```

## <a name="modifying-an-endpoint"></a>Modificando um ponto de extremidade
`Set-AzCdnEndpoint` modifica um ponto de extremidade existente.

```powershell
# Get an existing endpoint
$endpoint = Get-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Set up content compression
$endpoint.IsCompressionEnabled = $true
$endpoint.ContentTypesToCompress = "text/javascript","text/css","application/json"

# Save the changed endpoint and apply the changes
Set-AzCdnEndpoint -CdnEndpoint $endpoint
```

## <a name="purgingpre-loading-cdn-assets"></a>Limpando/Pré-carregando ativos CDN
`Unpublish-AzCdnEndpointContent` limpa ativos armazenados em cache enquanto `Publish-AzCdnEndpointContent` pré-carrega os ativos nos pontos de extremidade com suporte.

```powershell
# Purge some assets.
Unpublish-AzCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -PurgeContent "/images/kitten.png","/video/rickroll.mp4"

# Pre-load some assets.
Publish-AzCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -LoadContent "/images/kitten.png","/video/rickroll.mp4"

# Purge everything in /images/ on all endpoints.
Get-AzCdnProfile | Get-AzCdnEndpoint | Unpublish-AzCdnEndpointContent -PurgeContent "/images/*"
```

## <a name="startingstopping-cdn-endpoints"></a>Iniciando/Parando os pontos de extremidade CDN
`Start-AzCdnEndpoint` e `Stop-AzCdnEndpoint` podem ser usados para iniciar e parar os pontos de extremidade individuais ou grupos de pontos de extremidade.

```powershell
# Stop the cdndocdemo endpoint
Stop-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Stop all endpoints
Get-AzCdnProfile | Get-AzCdnEndpoint | Stop-AzCdnEndpoint

# Start all endpoints
Get-AzCdnProfile | Get-AzCdnEndpoint | Start-AzCdnEndpoint
```

## <a name="deleting-cdn-resources"></a>Excluindo os recursos CDN
`Remove-AzCdnProfile` e `Remove-AzCdnEndpoint` podem ser usados para remover os pontos de extremidade e os perfis.

```powershell
# Remove a single endpoint
Remove-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Remove all the endpoints on a profile and skip confirmation (-Force)
Get-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG | Get-AzCdnEndpoint | Remove-AzCdnEndpoint -Force

# Remove a single profile
Remove-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG
```

## <a name="next-steps"></a>Próximas etapas
Saiba como automatizar a CDN do Azure com [.NET](cdn-app-dev-net.md) ou [Node.js](cdn-app-dev-node.md).

Para saber mais sobre os recursos CDN, consulte [Visão Geral da CDN](cdn-overview.md).

