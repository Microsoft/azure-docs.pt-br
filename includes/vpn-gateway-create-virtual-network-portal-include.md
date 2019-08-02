---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 714f46b7019b6e64c504c5925ef5eb5539558c2d
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673229"
---
Para criar uma Rede Virtual no modelo de implantação do Gerenciador de Recursos usando o portal do Azure, siga as etapas abaixo. Use o [valores de exemplo](#values) se você estiver usando estas etapas como um tutorial. Se você não estiver executando estas etapas como um tutorial, substitua os valores pelos seus próprios. Para saber mais sobre como trabalhar com as redes virtuais, consulte a [Visão Geral da Rede Virtual](../articles/virtual-network/virtual-networks-overview.md)

>[!NOTE]
>Para que essa rede virtual se conecte a um local, é necessário coordenar com o administrador de rede local para que ele consiga um intervalo de endereços IP que possa ser usado especificamente para essa rede virtual. Se um intervalo de endereços duplicado existir em ambos os lados da conexão de VPN, o tráfego não será roteado da maneira esperada. Além disso, se você deseja se conectar esta rede virtual a outra rede virtual, o espaço de endereços não pode sobrepor outra rede virtual. Tome cuidado e planeje sua configuração de rede de forma adequada.
>
>

1. Em um navegador, acesse o [Portal do Azure](https://portal.azure.com) e entre com sua conta do Azure.
2. Clique em **Criar um recurso**. No campo **Pesquisar no marketplace**, digite ‘rede virtual’. Localize a **Rede virtual** na lista retornada e clique para abrir a página **Rede Virtual**.
3. Perto da parte inferior da página Rede Virtual, na lista **Selecionar um modelo de implantação**, selecione **Gerenciador de Recursos** e clique em **Criar**. Isso abre a página ‘Criar rede virtual’.

   ![Criar página da rede virtual](./media/vpn-gateway-create-virtual-network-portal-include/create-virtual-network.png "Criar página da rede virtual")
4. Na página **Criar rede virtual**, defina as configurações da VNet. Durante o preenchimento dos campos, o ponto de exclamação vermelho se tornará um ponto de seleção verde quando os caracteres digitados no campo forem válidos.

   - **Nome**: insira o nome de sua rede virtual. Para este exemplo, usamos VNet1.
   - **Espaço de endereço**: insira o espaço de endereço. Se houver vários espaços de endereço para adicionar, adicione seu espaço de endereço primeiro. Você pode adicionar outros espaços de endereço posteriormente, depois de criar a rede virtual. Verifique se o espaço de endereço especificado não se sobrepõe ao espaço de endereço de sua localização.
   - **Assinatura**: verifique se a assinatura listada é a correta. Você pode alterar as assinaturas usando o menu suspenso.
   - **Grupo de recursos**: Selecione um grupo de recursos existente ou crie um novo inserindo um nome para seu novo grupo de recursos. Se você estiver criando um novo grupo, dê o nome do grupo de recursos de acordo com seus valores de configuração planejados. Para saber mais sobre grupos de recursos, visite [Visão geral do Gerenciador de Recursos do Azure](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
   - **Localização**: selecione a localização de sua VNet. O local determina onde ficarão os recursos que você implanta nessa rede virtual.
   - **Sub-rede**: Adicione o primeiro nome da sub-rede e o intervalo de endereços da sub-rede. Você pode adicionar outras sub-redes e a sub-rede de gateway mais tarde, depois de criar essa rede virtual. 

5. Selecione **Fixar no painel** se quiser ser capaz de encontrar sua VNet facilmente no painel, em seguida, clique em **Criar**. Depois de clicar em **Criar**, você verá um bloco em seu painel refletir o progresso de sua rede virtual. O bloco muda à medida que a rede virtual é criada.
