---
title: 'Azure ExpressRoute: configurar ExpressRoute direto: Portal'
description: Esta página ajuda você a configurar o ExpressRoute direto usando o Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: acbd5c3aa88c2c8c14407ebda0c42d228aa6c9e3
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018931"
---
# <a name="create-expressroute-direct-using-the-azure-portal"></a>Criar ExpressRoute direto usando o portal do Azure

Este artigo mostra como criar o ExpressRoute direto usando o portal do Azure.
O ExpressRoute Direct permite que você se conecte diretamente à rede global da Microsoft em locais de emparelhamento distribuídos estrategicamente em todo o mundo. Para obter mais informações, veja [Sobre o ExpressRoute Direct](expressroute-erdirect-about.md).

## <a name="before-you-begin"></a><a name="before"></a>Antes de começar

Antes de usar o ExpressRoute Direct, você deve primeiro registrar sua assinatura. Para se inscrever, envie um e-mail para <ExpressRouteDirect@microsoft.com> com seu ID de assinatura, incluindo os seguintes detalhes:

* Cenários que você pretende para realizar com o **ExpressRoute Direct**
* Preferências de localização. Confira [Localizações de emparelhamento e parceiros do ExpressRoute](expressroute-locations-providers.md) para obter uma lista completa de todas as localizações
* Linha do tempo para implementação
* Tem mais perguntas?

Depois de registrado, verifique se o provedor de recursos **Microsoft. Network** está registrado em sua assinatura. O registro de um provedor de recursos configura sua assinatura para trabalhar com o provedor de recursos.

1. Acesse suas configurações de assinatura, conforme descrito em [provedores de recursos e tipos do Azure](../azure-resource-manager/management/resource-providers-and-types.md).
1. Em sua assinatura, para **provedores de recursos**, verifique se o provedor **Microsoft. Network** mostra um status **registrado** . Se o provedor de recursos Microsoft. Network não estiver presente na lista de provedores registrados, adicione-o.

## <a name="create-expressroute-direct"></a><a name="create-erdir"></a>Criar ExpressRoute direto

1. No menu [portal do Azure](https://portal.azure.com) ou na **Home** Page do, selecione **criar um recurso**.

1. Na **nova** página, no **campo _Pesquisar no Marketplace_*_, digite _* ExpressRoute Direct** e, em seguida, selecione **Enter** para obter os resultados da pesquisa.

1. Nos resultados, selecione **ExpressRoute Direct**.

1. Na página do **Expressroute Direct** , selecione **criar** para abrir a página **criar ExpressRoute Direct** .

1. Comece concluindo os campos na página **noções básicas** .

    :::image type="content" source="./media/how-to-expressroute-direct-portal/basics.png" alt-text="Página de noções básicas":::

    * **Assinatura**: a assinatura do Azure que você deseja usar para criar um novo ExpressRoute Direct. O recurso do ExpressRoute Direct e os circuitos do ExpressRoute precisam estar na mesma assinatura.
    * **Grupo de recursos**: o grupo de recursos do Azure no qual o novo recurso direto do ExpressRoute será criado. Se você não tiver um grupo de recursos atualmente, crie um.
    * **Região**: A região pública do Azure na qual o recurso será criado.
    * **Nome**: o nome do novo recurso direto do ExpressRoute.

1. Em seguida, preencha os campos na página de **configuração** .

    :::image type="content" source="./media/how-to-expressroute-direct-portal/configuration.png" alt-text="Captura de tela que mostra a página ' criar ExpressRoute direto ' com a guia ' configuração ' selecionada.":::

    * **Local de emparelhamento**: o local de emparelhamento em que você se conectará ao recurso direto do ExpressRoute. Para obter mais informações sobre locais de emparelhamento, examine [locais de ExpressRoute](expressroute-locations-providers.md).
   * **Largura de banda**: a largura de banda do par de portas que você deseja reservar. O ExpressRoute Direct dá suporte a opções de largura de banda de 10 GB e 100 GB. Se a largura de banda desejada não estiver disponível no local de emparelhamento especificado, [abra uma solicitação de suporte no portal do Azure](https://aka.ms/azsupt).
   * **Encapsulamento**: o ExpressRoute Direct dá suporte a encapsulamento QinQ e Dot1Q.
     * Se o QinQ for selecionado, cada circuito do ExpressRoute será atribuído dinamicamente a uma marca S e será exclusivo em todo o recurso ExpressRoute Direct.
     *  Cada marca C no circuito precisa ser exclusiva no circuito, mas não no ExpressRoute Direct.
     * Se o encapsulamento Dot1Q for selecionado, você precisará gerenciar a exclusividade da marca C (VLAN) em todo o recurso ExpressRoute Direct.
     >[!IMPORTANT]
     >O ExpressRoute Direct só pode ser um tipo de encapsulamento. O encapsulamento não pode ser alterado após a criação do ExpressRoute Direct.
     >

1. Especifique todas as marcas de recurso e selecione **examinar + criar** para validar as configurações de recurso direto do ExpressRoute.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/validate.png" alt-text="Captura de tela que mostra a página ' criar ExpressRoute ' com a guia ' revisar + criar ' selecionada.":::

1. Selecione **Criar**. Você verá uma mensagem informando que a implantação está em andamento. O status será exibido nessa página conforme os recursos são criados. 

## <a name="generate-the-letter-of-authorization-loa"></a><a name="authorization"></a>Gerar a letra de autorização (LOA)

No momento, a geração da carta de autorização não está disponível no Portal. Use **[Azure PowerShell](expressroute-howto-erdirect.md#authorization)** para obter a carta de autorização.

## <a name="change-admin-state-of-links"></a><a name="state"></a>Alterar Estado de Administrador de links

Esse processo deve ser usado para realizar um teste de Camada 1, garantindo que cada conexão cruzada seja corrigida corretamente em cada roteador para o primário e o secundário.

1. Na página **visão geral** do recurso do ExpressRoute Direct, na seção **links** , selecione **LINK1**.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/link.png" alt-text="Link 1" lightbox="./media/how-to-expressroute-direct-portal/link-expand.png":::

1. Alterne a configuração de **estado do administrador** para **habilitado** e, em seguida, selecione **salvar**.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/state.png" alt-text="Estado do administrador":::

    >[!IMPORTANT]
    >A cobrança será iniciada quando o estado do administrador for habilitado em um dos links.
    >

1. Repita o mesmo processo para **Link2**.

## <a name="create-a-circuit"></a><a name="circuit"></a>Criar um circuito

Por padrão, você pode criar 10 circuitos na assinatura que contém o recurso ExpressRoute Direct. Esse número pode ser aumentado pelo suporte. Você é responsável por acompanhar a largura de banda provisionada e utilizada. A largura de banda provisionada é a soma da largura de banda de todos os circuitos no recurso direto do ExpressRoute. A largura de banda utilizada é o uso físico das interfaces físicas subjacentes.

* Há larguras de banda de circuito adicionais que podem ser utilizadas no ExpressRoute Direct apenas para dar suporte aos cenários descritos acima. Estes são: 40 Gbps e 100 Gbps.

* SkuTier pode ser local, Standard ou Premium.

* SkuFamily deve ser somente MeteredData. Não há suporte para ilimitado no ExpressRoute Direct.

As etapas a seguir ajudam a criar um circuito de ExpressRoute do fluxo de trabalho direto do ExpressRoute. Se preferir, você também pode criar um circuito usando o fluxo de trabalho de circuito regular, embora não haja vantagem em usar as etapas de fluxo de trabalho de circuito regular para essa configuração. Consulte [criar e modificar um circuito do ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md).

1. Na seção **configurações** diretas do ExpressRoute, selecione **circuitos** e, em seguida, selecione **+ Adicionar**. 

    :::image type="content" source="./media/how-to-expressroute-direct-portal/add.png" alt-text="Captura de tela mostra as configurações do ExpressRoute com circuitos selecionados e adicionadas realçadas." lightbox="./media/how-to-expressroute-direct-portal/add-expand.png":::

1. Defina as configurações na página **configuração** .

   :::image type="content" source="./media/how-to-expressroute-direct-portal/configuration2.png" alt-text="Página de configuração – ExpressRoute Direct":::

1. Especifique quaisquer marcas de recurso, selecione **revisar + criar** para validar os valores antes de criar o recurso.

   :::image type="content" source="./media/how-to-expressroute-direct-portal/review.png" alt-text="Examinar e criar-ExpressRoute direto":::

1. Selecione **Criar**. Você verá uma mensagem informando que a implantação está em andamento. O status será exibido nessa página conforme os recursos são criados. 

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o ExpressRoute Direct, consulte a [visão geral](expressroute-erdirect-about.md).
