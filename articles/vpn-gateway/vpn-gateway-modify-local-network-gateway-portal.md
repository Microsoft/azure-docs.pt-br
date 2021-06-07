---
title: 'Gateway de VPN: modificar configurações de endereço IP do gateway: portal do Azure'
description: Este artigo mostra o passo a passo da alteração dos prefixos de endereço IP do seu gateway de rede local usando o portal do Azure
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/16/2020
ms.author: cherylmc
ms.openlocfilehash: 4ff4f1238764d7bdab6e74d29254a6388ea76d78
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92143161"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Modificar as configurações de gateway de rede local usando o portal do Azure

Às vezes, as configurações do seu gateway de rede local AddressPrefix ou GatewayIPAddress mudam. Este artigo mostra como modificar as configurações de gateway de rede local. Também é possível modificar essas configurações usando um método diferente, selecionando uma opção diferente da lista a seguir:

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [CLI do Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>

## <a name="local-network-gateway-configuration"></a><a name="configure-lng"></a>Configuração do gateway de rede local

A captura de tela abaixo mostra a página de **configuração** de um recurso de gateway de rede local usando o ponto de extremidade de endereço IP público:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/settings.png" alt-text="Configurações de endereço IP" lightbox="./media/vpn-gateway-modify-local-network-gateway-portal/settings-expand.png":::

Esta é a página de configuração com um ponto de extremidade FQDN:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/name.png" alt-text="Configurações de FQDN":::

## <a name="to-modify-the-gateway-ip-address-or-fqdn"></a><a name="ip"></a>Para modificar o endereço IP do gateway ou o FQDN

> [!NOTE]
> Você não pode alterar um gateway de rede local entre o ponto de extremidade FQDN e o ponto de extremidade de endereço IP. Você deve excluir todas as conexões associadas a esse gateway de rede local, criar uma nova com o novo ponto de extremidade (endereço IP ou FQDN) e recriar as conexões.
>

Se o dispositivo VPN ao qual você deseja se conectar tiver alterado seu endereço IP público, modifique o gateway de rede local usando as seguintes etapas:

1. No recurso de gateway de rede local, na seção **configurações** , selecione **configuração**.
2. Na caixa **Endereço IP**, modifique o endereço IP.
3. Clique em **Salvar** para salvar as configurações.

Se o dispositivo VPN ao qual você deseja se conectar tiver alterado seu FQDN (nome de domínio totalmente qualificado), modifique o gateway de rede local usando as seguintes etapas:

1. No recurso de gateway de rede local, na seção **configurações** , selecione **configuração**.
2. Na caixa **FQDN** , modifique o nome de domínio.
3. Clique em **Salvar** para salvar as configurações.

## <a name="to-modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Para modificar os prefixos de endereço IP

Para adicionar prefixos de endereço adicional:

1. No recurso de gateway de rede local, na seção **configurações** , selecione **configuração**.
2. Adicione o espaço de endereço IP na caixa *Adicionar intervalo de endereços adicional*.
3. Selecione **salvar** para salvar suas configurações.

Para remover os prefixo de endereço:

1. No recurso de gateway de rede local, na seção **configurações** , selecione **configuração**.
2. Selecione **'... '** na linha que contém o prefixo que você deseja remover.
3. Selecione **Remover**.
4. Selecione **salvar** para salvar suas configurações.

## <a name="to-modify-bgp-settings"></a><a name="bgp"></a>Para modificar as configurações de BGP

Para adicionar ou atualizar as configurações de BGP:

1. No recurso de gateway de rede local, na seção **configurações** , selecione **configuração**.
2. Selecione **"definir configurações de BGP"** para exibir ou atualizar as configurações de BGP para esse gateway de rede local
3. Adicionar ou atualizar o número do sistema autônomo ou o endereço IP do par de BGP nos campos correspondentes
4. Selecione **salvar** para salvar suas configurações.

Para remover as configurações de BGP:

1. No recurso de gateway de rede local, na seção **configurações** , selecione **configuração**.
2. Desmarque a seleção de **"definir configurações de BGP"** para remover o endereço IP do par de BGP e do protocolo BGP existente
3. Selecione **salvar** para salvar suas configurações.

## <a name="next-steps"></a>Próximas etapas

Você pode verificar a conexão de gateway. Confira [Verificar uma conexão de gateway](vpn-gateway-verify-connection-resource-manager.md).
