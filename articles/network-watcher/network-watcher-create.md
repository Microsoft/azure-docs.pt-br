---
title: Criar uma instância do Observador de Rede do Azure | Microsoft Docs
description: Saiba como criar um observador de rede do Azure em uma região do Azure usando a portal do Azure ou outras tecnologias e como excluir um observador de rede.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: c308824afdcae5f5c04a316c199bad71ad84a429
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961984"
---
# <a name="create-an-azure-network-watcher-instance"></a>Criar uma instância do Observador de Rede do Azure

O Observador de Rede é um serviço regional que permite monitorar e diagnosticar as condições em um nível do cenário da rede em, para e a partir do Azure. O monitoramento de nível do cenário permite diagnosticar problemas em um modo de exibição de nível de rede de ponta a ponta. As ferramentas de diagnóstico e visualização da rede disponíveis com o Observador de Rede ajudam a entender, diagnosticar e ter informações para sua rede no Azure. O observador de Rede é ativado por meio da criação de um recurso do Observador de Rede. Este recurso permite que você utilize os recursos do Observador de Rede.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="network-watcher-is-automatically-enabled"></a>O observador de rede é habilitado automaticamente
Quando você cria ou atualiza uma Rede Virtual do Microsoft Azure em sua assinatura, o Observador de Rede será habilitado automaticamente na região da sua rede virtual. Não há impacto em seus recursos ou cobranças associadas para habilitar automaticamente o Observador de Rede.

#### <a name="opt-out-of-network-watcher-automatic-enablement"></a>Desativação da ativação automática do Observador de Rede
Se você desejar desativar a ativação automática do Observador de Rede, poderá fazer isso executando os seguintes comandos:

> [!WARNING]
> A desativação da ativação automática do Observador de Rede é uma alteração permanente. Quando você recusa você não pode participar sem [contatando o suporte](https://azure.microsoft.com/support/options/)

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName DisableNetworkWatcherAutocreation -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

```azurecli-interactive
az feature register --name DisableNetworkWatcherAutocreation --namespace Microsoft.Network
az provider register -n Microsoft.Network
```



## <a name="create-a-network-watcher-in-the-portal"></a>Criar um Observador de Rede no portal

Navegue até **todos os serviços**  >  **rede**  >  **observador de rede**. Você pode selecionar todas as assinaturas para as quais deseja habilitar o Observador de Rede. Essa ação cria um Observador de Rede em todas as regiões que ele está disponível.

![criar um observador de rede](./media/network-watcher-create/figure1.png)

Ao habilitar o Observador de Rede usando o portal, o nome da instância do Observador de Rede é definido automaticamente como *NetworkWatcher_region_name*, em que *region_name* corresponde à região do Azure onde a instância está habilitada. Por exemplo, um Observador de Rede habilitado na região Centro-oeste dos EUA é nomeado *NetworkWatcher_westcentralus*.

A instância do Observador de Rede é criada automaticamente em um grupo de recursos nomeado *NetworkWatcherRG*. O grupo de recursos será criado, se ainda não existir.

Se desejar personalizar o nome de uma instância do observador de rede e o grupo de recursos no qual ele é colocado, você poderá usar o PowerShell, o CLI do Azure, a API REST ou os métodos ARMClient descritos nas seções a seguir. Em cada opção, o grupo de recursos deverá existir antes de você criar um Observador de Rede.  

## <a name="create-a-network-watcher-with-powershell"></a>Criar um Observador de Rede com o PowerShell

Para criar uma instância do Observador de Rede, execute o exemplo a seguir:

```powershell
New-AzNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-azure-cli"></a>Criar um Observador de Rede com a CLI do Azure

Para criar uma instância do Observador de Rede, execute o exemplo a seguir:

```azurecli
az network watcher configure --resource-group NetworkWatcherRG --locations westcentralus --enabled
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>Criar um Observador de Rede com a API REST

O ARMclient é usado para chamar a API REST usando o PowerShell. O ARMClient está localizado no chocolatey em [ARMClient no Chocolatey](https://chocolatey.org/packages/ARMClient)

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

## <a name="create-a-network-watcher-using-azure-quickstart-template"></a>Criar um observador de rede usando o modelo de início rápido do Azure

Para criar uma instância do observador de rede, consulte este [modelo de início rápido](https://azure.microsoft.com/resources/templates/101-networkwatcher-create/)

## <a name="delete-a-network-watcher-in-the-portal"></a>Excluir um observador de rede no portal

Navegue até **todos os serviços**  >  **rede**  >  **observador de rede**.

Selecione a guia Visão geral, se você ainda não estiver lá. Use a lista suspensa para selecionar a assinatura na qual você deseja desabilitar o observador de rede.
Expanda a lista de regiões para sua assinatura escolhida clicando na seta. Para qualquer dado, use os 3 pontos à direita para acessar o menu de contexto.
Clique em "desabilitar observador de rede" para iniciar a desabilitação. Você será solicitado a confirmar esta etapa. Clique em Sim para continuar.
No portal, você precisará fazer isso individualmente para cada região em cada assinatura.


## <a name="delete-a-network-watcher-with-powershell"></a>Excluir um observador de rede com o PowerShell

Para excluir uma instância do observador de rede, execute o seguinte exemplo:

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location westcentralus
New-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG -Location westcentralus
Remove-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG
```

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma instância do Observador de Rede, saiba mais sobre os recursos disponíveis:

* [Topologia](./view-network-topology.md)
* [Captura de pacotes](network-watcher-packet-capture-overview.md)
* [Verificação de fluxo de IP](network-watcher-ip-flow-verify-overview.md)
* [Próximo salto](network-watcher-next-hop-overview.md)
* [Exibição de grupo de segurança](network-watcher-security-group-view-overview.md)
* [Registro do fluxo NSG](network-watcher-nsg-flow-logging-overview.md)
* [Solução de problemas do Gateway de Rede Virtual](network-watcher-troubleshoot-overview.md)