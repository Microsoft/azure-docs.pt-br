---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a601b0c40f84832101e97a7abf7dd7418a0a5c69
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673228"
---
É possível criar uma VNet com um modelo de implantação do Resource Manager e com o portal do Azure seguindo estas etapas. Para obter mais informações sobre redes virtuais, confira [Visão geral da rede virtual](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>Para que essa VNet se conecte a uma localização local, coordene com o administrador de rede local para que ele consiga um intervalo de endereços IP que possa ser usado especificamente para essa rede virtual. Se houver um intervalo de endereços duplicado nos dois lados da conexão de VPN, o tráfego será roteado inesperadamente. Além disso, se você deseja se conectar esta rede virtual a outra rede virtual, o espaço de endereços não pode sobrepor outra rede virtual. Planeje sua configuração de rede de forma adequada.
>
>

1. Entre no [portal do Azure](https://portal.azure.com) e selecione **Criar um recurso**. A página **Novo** é aberta.

2. No campo **Pesquisar no marketplace**, insira *rede virtual* e selecione **Rede virtual** na lista retornada. A página **Rede virtual** é aberta.

   ![Localizar a página de recursos da Rede Virtual](./media/vpn-gateway-basic-vnet-rm-portal-include/newvnetportal700.png "Localizar a página de recursos da rede virtual")

3. Na lista **Selecionar um modelo de implantação** próxima à parte inferior da página, selecione **Resource Manager** e, em seguida, **Criar**. A página **Criar rede virtual** é aberta.

   ![Página Criar rede virtual](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet.png "Página Criar rede virtual")

4. Na página **Criar rede virtual**, defina as configurações da VNet. Quando preencher os campos, o ponto de exclamação vermelho se tornará um ponto de seleção verde quando os caracteres inseridos no campo forem validados. Alguns valores são preenchidos automaticamente, que podem ser substituídos por seus próprios valores:

   - **Nome**: insira o nome de sua rede virtual.

   - **Espaço de endereço**: insira o espaço de endereço. Se houver vários espaços de endereço para adicionar, insira o primeiro aqui. É possível adicionar outros espaços de endereço posteriormente, depois de criar a VNet.

   - **Assinatura**: verifique se a assinatura listada é a correta. Você pode alterar as assinaturas usando o menu suspenso.

   - **Grupo de recursos**: selecione um grupo de recursos existente ou crie um inserindo um nome para seu novo grupo de recursos. Se você estiver criando um novo grupo, dê o nome do grupo de recursos de acordo com seus valores de configuração planejados. Para saber mais sobre os grupos de recursos, confira [Visão geral do Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).

   - **Localização**: selecione a localização de sua VNet. A localização determina onde ficarão os recursos que você implanta nessa rede virtual.

   - **Sub-rede**: adicione o **Nome** da sub-rede e o **Intervalo de endereços** dela. É possível adicionar outras sub-redes posteriormente, depois de criar a VNet. 
     
5. Selecione **Criar**.
