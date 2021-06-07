---
title: Solucionar problemas de gateway e conexões de VNet do Azure-Azure PowerShell
titleSuffix: Azure Network Watcher
description: Esta página explica como usar o cmdlet do PowerShell para solucionar problemas do Observador de rede do Azure
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 99a30065e11a55f4c21b9e6ffc69b0a1693ecbdc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019730"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-powershell"></a>Como solucionar problemas de conexões e gateway de rede virtual do usando o PowerShell do Observador de rede do Azure

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI do Azure](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

O observador de rede oferece muitos recursos que dizem respeito às noções básicas sobre os recursos de rede no Azure. Um desses recursos é a solução de problemas de recursos. A solução de problemas de recursos pode ser chamada pelo Portal, pelo PowerShell, pela CLI ou pela API REST. Quando chamado, o Observador de rede inspeciona a integridade de uma conexão ou um gateway de rede virtual e faz um relatório sobre suas descobertas.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Antes de começar

Este cenário pressupõe que você seguiu as etapas em [Criação de um Observador de rede](network-watcher-create.md) para criar um Observador de rede.

Para obter uma lista de tipos de gateway com suporte, visite [Tipos de Gateway com suporte](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Visão geral

A solução de problemas de recursos fornece a capacidade de solucionar problemas que podem surgir com as conexões e o gateway de rede virtual. Quando uma solução de problemas de recursos recebe uma solicitação, os logs são consultados e inspecionadas. Quando a inspeção estiver concluída, você receberá um relatório com os resultados. As solicitações da solução de problemas de recursos são solicitações de execução longa e podem demorar para gerar um relatório. Os logs de solução de problemas são armazenados em um contêiner em uma conta de armazenamento especificada.

## <a name="retrieve-network-watcher"></a>Recuperar o Observador de Rede

A primeira etapa é recuperar a instância do Observador de Rede. A variável `$networkWatcher` é passada para o cmdlet `Start-AzNetworkWatcherResourceTroubleshooting` na etapa 4.

```powershell
$networkWatcher = Get-AzNetworkWatcher -Location "WestCentralUS" 
```

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Como recuperar uma conexão de gateway de rede virtual

Neste exemplo, a solução de problemas de recursos está sendo executada em uma conexão. Você também pode passá-lo por um gateway de rede virtual.

```powershell
$connection = Get-AzVirtualNetworkGatewayConnection -Name "2to3" -ResourceGroupName "testrg"
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

A solução de problemas de recursos produz relatório de dados sobre a integridade do recurso, ela também salva os logs em uma conta de armazenamento a ser revisada. Nesta etapa, criaremos uma conta de armazenamento. Você pode usar uma conta de armazenamento existente se já tiver uma.

```powershell
$sa = New-AzStorageAccount -Name "contosoexamplesa" -SKU "Standard_LRS" -ResourceGroupName "testrg" -Location "WestCentralUS"
Set-AzCurrentStorageAccount -ResourceGroupName $sa.ResourceGroupName -Name $sa.StorageAccountName
$sc = New-AzStorageContainer -Name logs
```

## <a name="run-network-watcher-resource-troubleshooting"></a>Como executar a solução de problemas de recursos do Observador de rede

Você usa o cmdlet `Start-AzNetworkWatcherResourceTroubleshooting` para solucionar problemas de recursos. Passamos o cmdlet do objeto do Observador de Rede, a Id da Conexão ou Gateway de Rede Virtual, a id da conta de armazenamento e o caminho para armazenar os resultados.

> [!NOTE]
> O cmdlet `Start-AzNetworkWatcherResourceTroubleshooting` é demorado e pode levar alguns minutos para ser concluído.

```powershell
Start-AzNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath "$($sa.PrimaryEndpoints.Blob)$($sc.name)"
```

Depois que você executar o cmdlet, o Observador de rede revisará o recurso para verificar a integridade. Ele envia um relatório com os resultados para o shell e armazena os logs dos resultados na conta de armazenamento especificada.

## <a name="understanding-the-results"></a>Como entender os resultados

O texto de ação fornece orientação geral sobre como resolver o problema. Se for possível executar uma ação para solucionar o problema, você receberá um link com orientações adicionais. Nos casos em que não há orientações adicionais, a resposta fornecerá a url para abrir um caso de suporte.  Para obter mais informações sobre as propriedades da resposta e o do que está incluído, acesse [Visão geral da solução de problemas do observador de rede](network-watcher-troubleshoot-overview.md)

Para obter instruções sobre como baixar os arquivos de contas de armazenamento do Azure, confira [Introdução ao armazenamento de Blobs do Azure usando o .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md). Outra ferramenta que pode ser usada é o Gerenciador de armazenamento. Mais informações sobre Gerenciador de Armazenamento podem ser encontradas aqui no seguinte link: [Gerenciador de armazenamento](https://storageexplorer.com/)

## <a name="next-steps"></a>Próximas etapas

Se as configurações para a conectividade VPN foram alteradas, confira [Gerenciamento de grupos de segurança de rede](../virtual-network/manage-network-security-group.md) para acompanhar quais são as regras de segurança e o grupo de segurança de rede envolvidos na questão.