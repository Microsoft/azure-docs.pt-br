---
title: Grupos de IP na política de firewall do Azure
description: Os grupos de IPS permitem que você agrupe e gerencie endereços IP para regras de política de firewall do Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 07/30/2020
ms.author: victorh
ms.openlocfilehash: 921a8b6c808d4eef2b02ef0580d5b2ea9045366d
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659498"
---
# <a name="ip-groups-in-azure-firewall-policy"></a>Grupos de IP na política de firewall do Azure

Os grupos de IPS permitem agrupar e gerenciar endereços IP para a política de firewall do Azure das seguintes maneiras:

- Como um tipo de origem em regras DNAT
- Como um tipo de origem ou de destino em regras de rede
- Como um tipo de origem em regras de aplicativo


Um grupo de IPS pode ter um único endereço IP, vários endereços IP ou um ou mais intervalos de endereços IP.

Os grupos de IPS podem ser reutilizados no firewall do Azure DNAT, rede e regras de aplicativo para vários firewalls em regiões e assinaturas no Azure. Os nomes de grupo devem ser exclusivos. Você pode configurar um grupo de IP no portal do Azure, CLI do Azure ou API REST. Um modelo de exemplo é fornecido para ajudá-lo a começar.

## <a name="sample-format"></a>Formato de exemplo

Os exemplos de formato de endereço IPv4 a seguir são válidos para uso em grupos de IPS:

- Endereço único: 10.0.0.0
- Notação CIDR: 10.1.0.0/32
- Intervalo de endereços: 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>Criar um grupo de IPS

Um grupo de IPS pode ser criado usando o portal do Azure, CLI do Azure ou a API REST. Para obter mais informações, consulte [criar um grupo de IPS](../firewall/create-ip-group.md).

## <a name="browse-ip-groups"></a>Procurar grupos de IP
1. Na barra de pesquisa portal do Azure, digite **grupos de IPS** e selecione-o. Você pode ver a lista de grupos de IPS ou pode selecionar **Adicionar** para criar um novo grupo de IPS.
2. Selecione um grupo de IPS para abrir a página Visão geral. Você pode editar, adicionar ou excluir endereços IP ou grupos de IPS.

   ![Visão geral de grupos de IP](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>Gerenciar um grupo de IPS

Você pode ver todos os endereços IP no grupo de IPS e as regras ou os recursos associados a ele. Para excluir um grupo de IPS, primeiro você deve dissociar o grupo de IPS do recurso que o está usando.

1. Para exibir ou editar os endereços IP, selecione **endereços IP** em **configurações** no painel esquerdo.
2. Para adicionar um ou vários endereços IP, selecione **adicionar endereços IP**. Isso abre a página de **arrastar ou procurar** para um upload ou você pode inserir o endereço manualmente.
3.    Selecionando as reticências (**...**) à direita para editar ou excluir endereços IP. Para editar ou excluir vários endereços IP, marque as caixas e selecione **Editar** ou **excluir** na parte superior.
4. Por fim, o pode exportar o arquivo no formato de arquivo CSV.

> [!NOTE]
> Se você excluir todos os endereços IP em um grupo de IPS enquanto ele ainda estiver em uso em uma regra, essa regra será ignorada.


## <a name="use-an-ip-group"></a>Usar um grupo de IPS

Agora você pode selecionar o **grupo IP** como um tipo de **origem** ou de **destino** para os endereços IP ao criar uma política com regras de DNAT, aplicativo ou rede.

:::image type="content" source="media/ip-groups/firewall-ip-group.png" alt-text="Grupos de IPs no firewall":::

## <a name="ip-address-limits"></a>Limites de endereço IP

Você pode ter um máximo de 100 grupos de IP por firewall com um máximo de 5000 endereços IP individuais ou prefixos IP por cada grupo de IP.

## <a name="related-azure-powershell-cmdlets"></a>Cmdlets de Azure PowerShell relacionados

Os cmdlets Azure PowerShell a seguir podem ser usados para criar e gerenciar grupos de IPS:

- [New-AzIpGroup](/powershell/module/az.network/new-azipgroup?view=azps-3.4.0)
- [Remove-AzIPGroup](/powershell/module/az.network/remove-azipgroup?view=azps-3.4.0)
- [Get-AzIpGroup](/powershell/module/az.network/get-azipgroup?view=azps-3.4.0)
- [Set-AzIpGroup](/powershell/module/az.network/set-azipgroup?view=azps-3.4.0)
- [New-AzFirewallPolicyNetworkRule](/powershell/module/az.network/new-azfirewallpolicynetworkrule?view=azps-3.4.0)
- [New-AzFirewallPolicyApplicationRule](/powershell/module/az.network/new-azfirewallpolicyapplicationrule?view=azps-3.4.0)
- [New-AzFirewallPolicyNatRule](/powershell/module/az.network/new-azfirewallpolicynatrule?view=azps-3.4.0)

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Proteger sua WAN Virtual usando o Gerenciador de Firewall do Azure](secure-cloud-network.md)