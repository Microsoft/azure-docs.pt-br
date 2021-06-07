---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/20/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a66596ecb926c1bf3c6b61cc99e1eb1b56e99158
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92328413"
---
1. Entre no [portal do Azure](https://portal.azure.com).
1. Em **Pesquisar recursos, serviço e documentos (G+/)** , digite *rede virtual*.

   ![Localizar a página de recursos de Rede Virtual](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "Localizar a página de recursos de rede virtual")
1. Selecione **Rede Virtual** nos resultados do **Marketplace**.

   ![Selecione a rede virtual](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "Localizar a página de recursos de rede virtual")
1. Na página **Rede Virtual**, selecione **Criar**.

   ![página de rede virtual](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "Selecione Criar")
1. Depois que você selecionar, **Criar**, a página **Criar rede virtual** será aberta.
1. Na guia **Básico**, configure os **Detalhes do projeto** e as definições de VNet dos **Detalhes da instância**.

   ![Guia Básico](./media/vpn-gateway-basic-vnet-rm-portal-include/basics.png "Guia Básico") Quando você preencher os campos, verá a marca de seleção verde quando os caracteres inseridos no campo forem validados. Alguns valores são preenchidos automaticamente, que podem ser substituídos por seus próprios valores:

   - **Assinatura**: verifique se a assinatura listada é a correta. Você pode alterar as assinaturas usando o menu suspenso.
   - **Grupo de recursos**: Selecione um grupo de recursos ou clique em **Criar** para criar um. Para saber mais sobre os grupos de recursos, confira [Visão geral do Azure Resource Manager](../articles/azure-resource-manager/management/overview.md#resource-groups).
   - **Name**: insira o nome de sua rede virtual.
   - **Região**: selecione a localização de sua VNet. A localização determina onde ficarão os recursos que você implanta nessa rede virtual.

1. Na guia **endereços IP**, configure os valores. Os valores mostrados nos exemplos a seguir servem para fins de demonstração. Ajuste esses valores de acordo com as configurações que você precisa.

   ![Guia de endereços IP](./media/vpn-gateway-basic-vnet-rm-portal-include/addresses.png "Guia de endereços IP")  
   - **Espaço de endereço IPv4**: por padrão, um espaço de endereço é criado automaticamente. Você pode clicar no espaço de endereço para ajustá-lo a fim de refletir seus valores. Também é possível adicionar outros espaços de endereço.
   - **Sub-rede**: se você usar o espaço de endereço padrão, uma sub-rede padrão será criada automaticamente. Se você alterar o espaço de endereço, precisará adicionar uma sub-rede. Selecione **+ Adicionar sub-rede** para abrir a janela **Adicionar sub-rede**. Defina as seguintes configurações e selecione **Adicionar** para adicionar os valores:
      - **Nome da sub-rede**: neste exemplo, nomeamos a sub-rede "FrontEnd".
      - **Intervalo de endereços da sub-rede**: o intervalo de endereços para esta sub-rede.

1. Na guia **Segurança**, neste momento, deixe os valores padrão:

   - **Proteção contra DDoS**: Basic
   - **Firewall**: Desabilitado
1. Selecione **Examinar + criar** para validar as configurações de rede virtual.
1. Depois que as configurações forem validadas, selecione **Criar**.
