---
title: Atualizar as ofertas do Marketplace – Azure Marketplace | Microsoft Docs
description: Atualizar as ofertas nos Marketplaces do Azure e do AppSource usando o Portal do Cloud Partner
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: pbutlerm
ms.openlocfilehash: 76b607502324c3ca25b3536d5197a97dbb80399d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60729590"
---
# <a name="update-azure-marketplace-and-appsource-offers"></a>Atualizar as ofertas do Azure Marketplace e do AppSource

Há vários tipos de atualizações que você poderá aplicar à oferta depois que ela for publicada.  O [Portal do Cloud Partner](https://cloudpartner.azure.com/) ajuda você a modificar corretamente os atributos de uma oferta, incluindo:

-  Adicionar uma nova versão de pacote ou imagem de VM (máquina virtual) a um SKU existente
-  Alterar as regiões em que um SKU está disponível
-  Adicionar novos SKUs
-  Atualizar metadados do Marketplace para SKUs ou ofertas 
-  Atualizar preços em ofertas pagas conforme o uso

O portal também tem recursos, tais como a capacidade de comparar recursos e exibir um histórico de recursos para uma oferta, que auxiliam no gerenciamento de alterações.  Depois de modificar uma oferta ou um SKU, ele deve ser republicado antes que as alterações sejam ativadas.  Este artigo explica os diferentes aspectos da atualização de sua oferta do Marketplace.

## <a name="unpermitted-changes-to-an-offersku"></a>Alterações não permitidas em uma oferta/SKU

Há alguns atributos de um SKU ou oferta que não poderão ser modificados depois que a oferta tiver sido publicada no Marketplace.  Os campos correspondentes estão desabilitados na guia **Editor** do portal, por exemplo:  

- ID da Oferta e ID do Publicador
- Id do SKU 
- Contagem de SKUs existentes do disco de dados
- Alterações no modelo de cobrança/licença de SKUs existentes
- Marcas de versão, por exemplo: `1.0.1`


## <a name="common-update-operations"></a>Operações comuns de atualização

As seções a seguir explicam como executar algumas das operações de atualização.  Essas operações não estão disponíveis para todos os tipos de oferta.  Você precisa entrar no Portal do Cloud Partner para iniciar qualquer uma dessas operações.


### <a name="update-offer-contacts"></a>Atualizar os contatos da oferta

Use as etapas a seguir para atualizar os contatos de suporte de sua oferta.
1. Na página **Todas as Ofertas**, selecione a oferta.
2. Selecione a guia **Contatos**. Atualize seus contatos.
3. Selecione o botão **Salvar**.
4. Selecione **publicar** para iniciar o processo de publicação.


### <a name="change-regions-an-offer-or-sku-is-available-in"></a>Alterar as regiões em que uma oferta ou SKU está disponível

Ao longo do tempo, talvez você queira disponibilizar sua oferta/SKU em regiões mais.
Como alternativa, talvez você queira parar de dar suporte à oferta/SKU em uma determinada região.
Para implementar essas alterações, siga as etapas abaixo.

1. Na página **Todas as ofertas**, localize a oferta que você gostaria de atualizar.

Para ofertas do Azure Marketplace:

1. Selecione a guia **SKUs**.  Selecione o SKU a modificar.
1. Clique no botão **Selecionar Países** no campo **Disponibilidade por país/região**.
1. Na caixa de diálogo de disponibilidade da região, adicione ou remova as regiões para este SKU.

Para ofertas do AppSource:

1. Selecione a guia **Detalhes da Vitrine**.
1. Ao lado do rótulo **Países/regiões com suporte**, clique em **Países/regiões com suporte**. 
1. Na caixa de diálogo de países/regiões com suporte, adicione ou remova as regiões para essa oferta.

Para qualquer um dos dois marketplaces:

1. Clique em **Publicar** para iniciar o processo de publicação. 

Se um SKU estiver sendo disponibilizado em uma nova região, você poderá especificar preços para essa região específica por meio da funcionalidade **Exportar Dados de Preços**. Se estiver adicionando uma região de volta que já estava disponível antes, você não poderá atualizar seus preços porque as alterações de preços não são permitidas.


### <a name="add-a-new-sku"></a>Adicionar uma nova SKU 

Use as etapas a seguir para disponibilizar um novo SKU para uma oferta existente:

1. Na página **Todas as ofertas**, encontre a oferta.
3. No formulário **SKUs**, clique em **Adicionar novo SKU** e forneça uma **ID do SKU** no pop-up.
4. Siga o restante das etapas detalhadas em [Publicar uma oferta de máquina virtual](../virtual-machine/cpp-publish-offer.md).
5. Clique em **Publicar** para iniciar o processo de publicação.


### <a name="update-offer-marketplace-assets"></a>Atualizar ativos de marketplace da oferta

Talvez haja situações em que você precisa atualizar os ativos de imagem e com base em texto do marketplace, tais como seus logotipos da empresa, descrição da oferta, etc. Use as etapas a seguir para atualizar esses ativos.

1. Na página **Todas as ofertas**, encontre a oferta. 
2. Selecione a guia **Marketplace** e siga as instruções no tópico da *guia Marketplace* da oferta.
3. Clique em **Publicar** para iniciar o processo de publicação.


### <a name="update-pricing-on-published-offers"></a>Atualizar o preço em ofertas publicadas

Quando sua oferta paga conforme o uso for publicada, você não poderá aumentar o preço de um SKU existente.  Em vez disso, crie um novo SKU com a mesma oferta, exclua o SKU antigo e republique sua oferta. Você pode diminuir o preço em ofertas publicadas anteriormente. Para diminuir o preço da oferta:

1. Selecione o SKU cujo preço você deseja diminuir.
2. Você deve definir o preço mais baixo pelo mesmo mecanismo usado originalmente: diretamente na interface do usuário do portal ou com a planilha de importação/exportação.
3. Clique em **Salvar**.
4. Clique em **Publicar** para iniciar o processo de publicação.

O preço ficará visível para novos clientes assim que ele estiver ativo no marketplace e todos os novos clientes pagarão o novo preço reduzido.  Para clientes existentes, a redução de preço é refletida retroativamente ao início do ciclo de faturamento, durante o qual a redução de preço se tornou efetiva.  Se eles já tiverem sido cobrados pelo ciclo durante o qual ocorreu uma redução de preço, eles receberão um reembolso durante o próximo ciclo de faturamento para cobrir o preço reduzido.


## <a name="compare-feature"></a>Recurso Comparar

Quando você fizer alterações em uma oferta publicada, você pode usar o recurso *Comparar* para auditar as alterações. Para usar esse recurso:

1. Em qualquer ponto no processo de edição, você pode clicar no botão **Comparar** na guia **Editor** para sua oferta.
2. Uma janela de comparação exibe versões lado a lado das alterações salvas para essa oferta em comparação com a oferta do marketplace. 

![Botão Comparar oferta na guia do editor](./media/offer-compare-button.png)


## <a name="history-of-publishing-actions"></a>Histórico de ações de publicação

Para exibir o histórico da atividade de publicação, selecione a guia **Histórico** na barra de menu vertical do Portal do Cloud Partner.  A página de histórico fornece filtragem flexível por várias características e dá suporte à ordenação de coluna.  Cada evento de publicação tem carimbo de data/hora.  Para obter mais informações, confira a [página de Histórico de auditoria](../portal-tour/cpp-history-page.md).


## <a name="next-steps"></a>Próximas etapas

Você também pode usar o Portal do Cloud Partner para [excluir um SKU ou oferta publicado](./cpp-delete-offer.md).
