---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6bc8b6cc18f4a4bc41cabf7f8fefe78a5aaf7827
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94427812"
---
1. No [portal do Azure](https://portal.azure.com), em **Recursos de pesquisa, serviços e documentos (G+/)** , digite **gateway de rede virtual**. Localize **Gateway de rede virtual** nos resultados da pesquisa e selecione-o.

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/search.png" alt-text="Campo de pesquisa" lightbox="./media/vpn-gateway-add-gw-rm-portal-include/search-expand.png":::

1. Na página **Gateway de rede virtual**, selecione **+ Adicionar**. Isso abre a página **Criar gateway de rede virtual**.

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/add.png" alt-text="página de gateways da rede virtual":::
1. Na guia **Noções básicas**, preencha os valores do gateway de rede virtual.

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/gateway.png" alt-text="Campos de gateway":::

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/gateway-vnet.png" alt-text="Campos de gateway adicionais":::

   * **Assinatura**: Selecione na lista suspensa a assinatura que deseja usar.
   * **Grupo de Recursos**: Essa configuração é preenchida automaticamente quando você seleciona a rede virtual nesta página.

   **Detalhes da instância**

   * **Nome**: nomeie o seu gateway. Dar um nome ao gateway não é igual a dar um nome a uma sub-rede de gateway. É o nome do objeto de gateway que você está criando.
   * **Região**: Selecione a região na qual deseja criar este recurso. A região do gateway deve ser a mesma que da rede virtual.
   * **Tipo de gateway**: selecione **VPN**. Gateways VPN usam o tipo de gateway de rede virtual do tipo **VPN**.
   * **Tipo de VPN**: selecione o Tipo de VPN especificado para sua configuração. A maioria das configurações exige um tipo de VPN baseado em rota.
   * **SKU**: selecione o SKU de gateway no menu suspenso. As SKUs listadas na lista suspensa dependem do tipo de VPN selecionado. Para saber mais sobre os SKUs de gateway, consulte [SKUs de Gateway](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).
   * **Geração**: para informações sobre a Geração do Gateway de VPN, confira [SKUs de Gateway](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).
   * **Rede virtual**: na lista suspensa, selecione a rede virtual à qual você deseja adicionar este gateway.
   * **Intervalo de endereços da Sub-rede do Gateway:** Esse campo só aparecerá se a VNet não tiver uma sub-rede de gateway. Se possível, faça o intervalo /27 ou maior (/26, /25 etc.). Não recomendamos criar um intervalo menor que /28. Se você já tiver uma sub-rede de gateway, poderá ver os detalhes do GatewaySubnet navegando até a rede virtual. Clique em **sub-redes** para ver o intervalo. Se quiser alterar o intervalo, você poderá excluir e recriar o GatewaySubnet.

   **Endereço IP público**

   Essa configuração especifica o objeto de endereço IP público associado ao gateway da VPN. O endereço IP público é atribuído dinamicamente a esse objeto quando o gateway de VPN é criado. A única vez em que o endereço IP Público é alterado é quando o gateway é excluído e recriado. Isso não altera o redimensionamento, a redefinição ou outras manutenções/atualizações internas do seu gateway de VPN.

     * **Endereço IP público**: Deixe **criar novo** selecionado.
     * **Nome do endereço IP público**: Na caixa de texto, digite um nome para a instância do endereço IP público.
     * **Atribuição**: o gateway de VPN é compatível somente com o Dinâmico.
     * **Habilitar o modo ativo-ativo**: Selecione apenas **Ativar o modo ativo-ativo** se estiver criando uma configuração de gateway ativa-ativa. Caso contrário, deixe essa configuração **Desabilitada**.
     * Deixe **Configurar BGP** **Desabilitado**, a menos que sua configuração exija especificamente essa configuração. Se você precisar dessa configuração, o ASN padrão será 65515, embora isso possa ser alterado.
1. Selecione **Examinar + criar** para executar a validação.
1. Depois que a validação for aprovada, selecione **Criar** para implantar o Gateway de VPN.

Um gateway pode levar até 45 minutos para ser criado e implantado por completo. Você poderá ver o status de implantação na página de Visão Geral do gateway. Depois de criar o gateway você pode exibir, observando a Rede Virtual no portal, o endereço IP que foi atribuído a esse gateway. O gateway aparecerá como um dispositivo conectado.
