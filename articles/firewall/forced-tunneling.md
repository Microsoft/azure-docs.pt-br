---
title: Túnel forçado do Firewall do Azure
description: Você pode configurar o túnel forçado para rotear o tráfego vinculado à Internet para um firewall adicional ou solução de virtualização de rede para processamento adicional.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 08/19/2020
ms.author: victorh
ms.openlocfilehash: da2b206bf24cb33180305e32e270b989eb64dfa3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88612581"
---
# <a name="azure-firewall-forced-tunneling"></a>Túnel forçado do Firewall do Azure

Quando configura um novo Firewall do Azure, você pode rotear todo o tráfego vinculado à Internet para um próximo salto designado em vez de ir diretamente para a Internet. Por exemplo, você pode ter um firewall de borda local ou outra solução de virtualização de rede (NVA) para processar o tráfego de rede antes que ele seja passado para a Internet. No entanto, você não pode configurar um firewall existente para túnel forçado.

Por padrão, o túnel forçado não é permitido no Firewall do Azure para garantir que todas as suas dependências de saída do Azure sejam atendidas. As configurações de Rota Definida pelo Usuário (UDR) no *AzureFirewallSubnet* que têm uma rota padrão que não vai diretamente para a Internet estão desabilitadas.

## <a name="forced-tunneling-configuration"></a>Configuração de túnel forçado

Para dar suporte ao túnel forçado, o tráfego de Gerenciamento de Serviços é separado do tráfego do cliente. Uma sub-rede adicional dedicada denominada *AzureFirewallManagementSubnet* (tamanho mínimo de /26 da sub-rede) é necessária com seu próprio endereço IP público associado. A única rota permitida nessa sub-rede é uma rota padrão para a Internet, e a propagação de rotas BGP deve ser desabilitada.

Se você tiver uma rota padrão anunciada via BGP para forçar o tráfego para o local, deverá criar o *AzureFirewallSubnet* e o *AzureFirewallManagementSubnet* antes de implantar o firewall e ter um UDR com uma rota padrão para a Internet e **propagar as rotas de gateway** desabilitadas.

Nessa configuração, o *AzureFirewallSubnet* agora pode incluir rotas para qualquer firewall local ou NVA para processar o tráfego antes que ele seja passado para a Internet. Você também pode publicar essas rotas via BGP para *AzureFirewallSubnet* se a **propagação de rotas de gateway** estiver habilitada nessa sub-rede.

Por exemplo, você pode criar uma rota padrão no *AzureFirewallSubnet* com seu gateway de VPN como o próximo salto para acessar seu dispositivo local. Ou você pode habilitar as **rotas de gateway propagadas** para obter as rotas apropriadas para a rede local.

:::image type="content" source="media/forced-tunneling/route-propagation.png" alt-text="Propagação de rota do gateway de rede virtual":::

Se você habilitar o túnel forçado, o tráfego de entrada na Internet será no modo SNAT a um dos endereços IP privados do firewall no AzureFirewallSubnet, ocultando a origem do seu firewall local.

Se a sua organização usa um intervalo de endereços IP públicos para redes privadas, o Firewall do Azure realiza SNAT do tráfego para um dos endereços IP privados do firewall no AzureFirewallSubnet. No entanto, você pode configurar o Firewall do Azure para **não** SNAT de seu intervalo de endereços IP públicos. Para obter mais informações, confira [Intervalos de endereços IP privados do SNAT do Firewall do Azure](snat-private-range.md).

Depois de configurar o Firewall do Azure para dar suporte ao túnel forçado, não é possível desfazer a configuração. Se remover todas as outras configurações de IP em seu firewall, a configuração de IP de gerenciamento também será removida e o firewall será desalocado. O endereço IP público atribuído à configuração de IP de gerenciamento não pode ser removido, mas você pode atribuir um endereço IP público diferente.

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Implantar e configurar o Firewall do Azure em uma rede híbrida usando o portal do Azure](tutorial-hybrid-portal.md)
