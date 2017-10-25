---
title: "Criar uma instância do Observador de Rede do Azure | Microsoft Docs"
description: "Essa página oferece as etapas para criar uma instância do Observador de Rede usando o portal e a API REST do Azure"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 687e5b65e89ae2a79d8e9aa5c4345c91b4943d3f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-network-watcher-instance"></a>Criar uma instância do Observador de Rede do Azure

O Observador de Rede é um serviço regional que permite monitorar e diagnosticar as condições em um nível do cenário da rede em, para e a partir do Azure. O monitoramento de nível do cenário permite diagnosticar problemas em um modo de exibição de nível de rede de ponta a ponta. As ferramentas de diagnóstico e visualização da rede disponíveis com o Observador de Rede ajudam a entender, diagnosticar e ter informações para sua rede no Azure.

> [!NOTE]
> Como, no momento, o Observador de Rede dá suporte apenas à CLI 1.0, as instruções para criar uma nova instância do Observador de Rede são fornecidas para a CLI 1.0.

## <a name="create-a-network-watcher-in-the-portal"></a>Criar um Observador de Rede no portal

Navegue até **Mais Serviços** > **Rede** > **Observador de Rede**. Você pode selecionar todas as assinaturas para as quais deseja habilitar o Observador de Rede. Essa ação cria um Observador de Rede em todas as regiões que ele está disponível.

![criar um observador de rede][1]

Quando você habilita o Observador de Rede usando o Portal, o nome da instância do Observador de Rede é definido automaticamente para NetworkWatcher_nome_da_região, em que nome_da_região corresponde à região do Azure na qual a instância foi habilitada.  Por exemplo, um Observador de Rede habilitado na região Centro-Oeste dos EUA será nomeado NetworkWatcher_westcentralus

Além disso, a instância do Observador de Rede será automaticamente adicionada a um grupo de recursos denominado NetworkWatcherRG.  Esse grupo de recursos será criado, se ele ainda não existir.

Se você deseja personalizar o nome de uma instância do Observador de Rede e o grupo de recursos no qual ela é colocada, você pode usar os métodos do Powershell, API REST ou ARMClient descritos abaixo.  Em cada opção, o grupo de recursos deve existir antes de você colocar o Observador de Rede nele.  

## <a name="create-a-network-watcher-with-powershell"></a>Criar um Observador de Rede com o PowerShell

Para criar uma instância do Observador de Rede, execute o exemplo a seguir:

```powershell
New-AzureRmNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>Criar um Observador de Rede com a API REST

O ARMclient é usado para chamar a API REST usando o PowerShell. O ARMClient é encontrado no chocolatey em [ARMClient no Chocolatey](https://chocolatey.org/packages/ARMClient)

### <a name="log-in-with-armclient"></a>Fazer logon com o ARMClient

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>Criar o Observador de Rede

```powershell
$subscriptionId = '<subscription id>'
$networkWatcherName = '<name of network watcher>'
$resourceGroupName = '<resource group name>'
$apiversion = "2016-09-01"
$requestBody = @"
{
'location': 'West Central US'
}
"@

armclient put "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}?api-version=${api-version}" $requestBody
```

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma instância do Observador de Rede, saiba mais sobre os recursos disponíveis:

* [Topologia](network-watcher-topology-overview.md)
* [Captura de pacotes](network-watcher-packet-capture-overview.md)
* [Verificação de fluxo de IP](network-watcher-ip-flow-verify-overview.md)
* [Próximo salto](network-watcher-next-hop-overview.md)
* [Exibição de grupo de segurança](network-watcher-security-group-view-overview.md)
* [Registro do fluxo NSG](network-watcher-nsg-flow-logging-overview.md)
* [Solução de problemas do Gateway de Rede Virtual](network-watcher-troubleshoot-overview.md)

Quando uma instância do Observador de Rede tiver sido criada, a captura de pacotes pode ser configurada seguindo o artigo: [Criar uma captura de pacote acionada por alerta](network-watcher-alert-triggered-packet-capture.md)

[1]: ./media/network-watcher-create/figure1.png











