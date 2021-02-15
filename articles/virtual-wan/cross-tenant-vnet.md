---
title: 'Conectar VNets de locatário cruzado a um Hub: PowerShell'
titleSuffix: Azure Virtual WAN
description: Este artigo ajuda você a conectar VNets de locatário cruzado a um hub virtual usando o PowerShell.
services: virtual-wan
author: wtnlee
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/28/2020
ms.author: wellee
ms.openlocfilehash: c49a85c71c9b877be7e143f5caf27dc307fe0c12
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381262"
---
# <a name="connect-cross-tenant-vnets-to-a-virtual-wan-hub"></a>Conectar VNets de locatário cruzado a um hub de WAN virtual

Este artigo ajuda você a usar a WAN virtual para conectar uma VNet a um hub virtual em um locatário diferente. Essa arquitetura será útil se você tiver cargas de trabalho de cliente que devem ser conectadas para serem da mesma rede, mas estiverem em locatários diferentes. Por exemplo, conforme mostrado no diagrama a seguir, você pode conectar uma VNet não contoso (o locatário remoto) a um hub virtual contoso (o locatário pai).

:::image type="content" source="./media/cross-tenant-vnet/connectivity.png" alt-text="Configurar a configuração de roteamento" :::

Neste artigo, você aprenderá como:

* Adicione outro locatário como um colaborador em sua assinatura do Azure.
* Conecte uma VNet entre locatários a um hub virtual.

As etapas para essa configuração são executadas usando uma combinação do portal do Azure e do PowerShell. No entanto, o recurso em si só está disponível no PowerShell e na CLI.

## <a name="before-you-begin"></a>Antes de começar

### <a name="prerequisites"></a>Pré-requisitos

Para usar as etapas neste artigo, você deve ter a configuração a seguir já configurada em seu ambiente:

* Uma WAN virtual e um hub virtual em sua assinatura principal.
* Uma rede virtual configurada em uma assinatura em um locatário diferente (remoto).
* Certifique-se de que o espaço de endereço da VNet no locatário remoto não se sobreponha a nenhum outro espaço de endereço em nenhum outro VNets já conectado ao Hub virtual pai.

### <a name="working-with-azure-powershell"></a>Trabalhando com o Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="assign-permissions"></a><a name="rights"></a>Atribuir permissões

Para que a assinatura pai com o Hub virtual modifique e acesse as redes virtuais no locatário remoto, você precisa atribuir permissões de **colaborador** à sua assinatura principal da assinatura de locatário remoto.

1. Adicione a atribuição de função de **colaborador** à conta pai (aquela com o Hub de WAN virtual). Você pode usar o PowerShell ou o portal do Azure para atribuir essa função. Consulte os artigos **Adicionar ou remover atribuições de função** a seguir para obter as etapas:

   * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
   * [Portal](../role-based-access-control/role-assignments-portal.md)

1. Em seguida, adicione a assinatura de locatário remoto e a assinatura de locatário pai à sessão atual do PowerShell. Execute o comando a seguir. Se você tiver entrado no pai, só precisará executar o comando para o locatário remoto.

   ```azurepowershell-interactive
   Add-AzAccount -SubscriptionId "xxxxx-b34a-4df9-9451-4402dcaecc5b"
   ```

1. Verifique se a atribuição de função foi bem-sucedida fazendo logon no Azure PowerShell usando as credenciais pai e executando o seguinte comando:

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

1. Se as permissões forem propagadas com êxito para o pai e tiverem sido adicionadas à sessão, a assinatura de Propriedade do locatário remoto aparecerá na saída do comando.

## <a name="connect-vnet-to-hub"></a><a name="connect"></a>Conectar VNet ao Hub

Nas etapas a seguir, você alternará entre o contexto das duas assinaturas ao vincular a rede virtual ao Hub virtual. Substitua os valores de exemplo para refletir seu próprio ambiente.

1. Verifique se você está no contexto da sua conta remota executando o seguinte comando:

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "[remote ID]"
   ```

1. Crie uma variável local para armazenar os metadados da rede virtual que você deseja conectar ao Hub.

   ```azurepowershell-interactive
   $remote = Get-AzVirtualNetwork -Name "[vnet name]" -ResourceGroupName "[resource group name]"
   ```

1. Volte para a conta pai.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "[parent ID]"
   ```

1. Conecte a VNet ao Hub.

   ```azurepowershell-interactive
   New-AzVirtualHubVnetConnection -ResourceGroupName "[parent resource group name]" -VirtualHubName "[virtual hub name]" -Name "[name of connection]" -RemoteVirtualNetwork $[local variable name]
   ```

1. Você pode exibir a nova conexão no PowerShell ou no portal do Azure.

   * **PowerShell:** Os metadados da conexão recém-criada serão mostrados no console do PowerShell se a conexão tiver sido formada com êxito.
   * **Portal do Azure:** Navegue até o Hub virtual, **conectividade-> conexões de rede virtual**. Você pode exibir o ponteiro para a conexão. Para ver o recurso real, você precisará das permissões adequadas.
   
## <a name="troubleshooting"></a><a name="troubleshoot"></a>Solução de problemas

* Verifique se os metadados em $remote (da [seção](#connect)anterior) correspondem às informações do portal do Azure.
* Você pode verificar as permissões usando as configurações IAM do grupo de recursos de locatário remoto ou usando comandos Azure PowerShell (Get-AzSubscription).
* Certifique-se de que as aspas estejam incluídas em volta dos nomes dos grupos de recursos ou de quaisquer outras variáveis específicas do ambiente (por exemplo, "VirtualHub1" ou "VirtualNetwork1").

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre WAN virtual, consulte:

* As [perguntas frequentes sobre](virtual-wan-faq.md) a WAN virtual
