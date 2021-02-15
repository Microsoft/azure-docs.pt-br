---
title: Exibir as recomendações do Azure Advisor que são importantes para você
description: Exiba e filtre as recomendações do Azure Advisor para reduzir o ruído.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 43bdaba7d774bba8857a6eb3ef296d7ab8e1c264
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90986858"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>Exibir as recomendações do Azure Advisor que são importantes para você

O Azure Advisor fornece recomendações para ajudá-lo a otimizar suas implantações do Azure. No Advisor, você tem acesso a alguns recursos que ajudam a restringir suas recomendações apenas às que forem importantes para você.

## <a name="configure-subscriptions-and-resource-groups"></a>Configurar assinaturas e grupos de recursos

O Advisor oferece a capacidade de selecionar assinaturas e grupos de recursos que são importantes para você e sua organização. Você só vê recomendações para as assinaturas e os grupos de recursos que você selecionar. Por padrão, todos são selecionados. As definições de configuração se aplicam à assinatura ou ao grupo de recursos, portanto, as mesmas configurações se aplicam a todos que têm acesso a essa assinatura ou grupo de recursos. As definições de configuração podem ser alteradas no portal do Azure ou de forma programática.

Para fazer alterações no portal do Azure:

1. Abra o [Azure Advisor](https://aka.ms/azureadvisordashboard) no portal do Azure.

1. Selecione **configuração** no menu.

   ![Menu de configuração do Advisor](./media/view-recommendations/configuration.png)

1. Marque a caixa na coluna **incluir** para qualquer assinatura ou grupo de recursos para receber as recomendações do Advisor. Se a caixa estiver desabilitada, talvez você não tenha permissão para fazer uma alteração de configuração nessa assinatura ou grupo de recursos. Saiba mais sobre [as permissões no Azure Advisor](permissions.md).

1. Clique em **aplicar** na parte inferior depois de fazer uma alteração.

## <a name="filtering-your-view-in-the-azure-portal"></a>Filtrando sua exibição no portal do Azure

As definições de configuração permanecem ativas até serem alteradas. Se você quiser limitar a exibição de recomendações para uma única exibição, poderá usar os menus suspensos fornecidos na parte superior do painel do Advisor. Na visão geral, alta disponibilidade, segurança, desempenho, custo e todos os painéis de recomendação, você pode selecionar as assinaturas, os tipos de recursos e o status de recomendação que deseja ver.

   :::image type="content" source="./media/view-recommendations/filtering.png" alt-text="Captura de tela do Azure Advisor mostrando opções de filtragem.":::

## <a name="dismissing-and-postponing-recommendations"></a>Ignorando e adiando recomendações

O Azure Advisor permite ignorar ou adiar as recomendações em um único recurso. Se você ignorar uma recomendação, não a verá novamente, a menos que você a ative manualmente. No entanto, adiar uma recomendação permite que você especifique uma duração após a qual a recomendação é automaticamente ativada novamente. A adiação pode ser feita no portal do Azure ou de forma programática.

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>Adiar uma única recomendação no portal do Azure 

1. Abra o [Azure Advisor](https://aka.ms/azureadvisordashboard) no portal do Azure.
1. Selecione uma categoria de recomendação para exibir suas recomendações
1. Selecione uma recomendação na lista de recomendações
1. Selecione adiar ou ignorar para a recomendação que você deseja adiar ou ignorar

     :::image type="content" source="./media/view-recommendations/postpone-dismiss.png" alt-text="Captura de tela do Azure Advisor mostrando opções de filtragem.":::

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>Adiar ou ignorar várias recomendações no portal do Azure

1. Abra o [Azure Advisor](https://aka.ms/azureadvisordashboard) no portal do Azure.
1. Selecione uma categoria de recomendação para exibir suas recomendações.
1. Selecione uma recomendação na lista de recomendações.
1. Marque a caixa de seleção à esquerda da linha para todos os recursos que você deseja adiar ou ignorar a recomendação.
1. Selecione **adiar** ou **ignorar** na parte superior esquerda da tabela.

     :::image type="content" source="./media/view-recommendations/postpone-dismiss-multiple.png" alt-text="Captura de tela do Azure Advisor mostrando opções de filtragem.":::

> [!NOTE]
> Você precisa de um colaborador ou permissão de proprietário para ignorar ou adiar uma recomendação. Saiba mais sobre as permissões no Azure Advisor.

> [!NOTE]
> Se as caixas de seleção estiverem desabilitadas, as recomendações ainda poderão ser carregadas. Aguarde até que todas as recomendações sejam carregadas antes de tentar adiar ou ignorar.

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>Reativar uma recomendação adiada ou ignorada

Você pode ativar uma recomendação que foi adiada ou ignorada. Essa ação pode ser feita no portal do Azure ou de forma programática. No Portal do Azure:

1. Abra o [Azure Advisor](https://aka.ms/azureadvisordashboard) no portal do Azure.

1. Altere o filtro no painel Visão geral para **adiado**. Em seguida, o Advisor exibe recomendações adiadas ou ignoradas.

    :::image type="content" source="./media/view-recommendations/activate-postponed.png" alt-text="Captura de tela do Azure Advisor mostrando opções de filtragem.":::

1. Selecione uma categoria para ver as recomendações **adiadas** e **ignoradas** .

1. Selecione uma recomendação na lista de recomendações. Isso abre as recomendações com a guia **adiada & ignorada** já selecionada para mostrar os recursos para os quais essa recomendação foi adiada ou ignorada.

1. Clique em **Ativar** no final da linha. Depois de clicado, a recomendação está ativa para esse recurso e, portanto, removida dessa tabela. A recomendação agora está visível na guia **ativa** .
 
     :::image type="content" source="./media/view-recommendations/activate-postponed-2.png" alt-text="Captura de tela do Azure Advisor mostrando opções de filtragem.":::

## <a name="next-steps"></a>Próximas etapas

Este artigo explica como você pode exibir recomendações relevantes para você no Azure Advisor. Para saber mais sobre o Assistente, consulte: 

- [O que é o Azure Advisor?](advisor-overview.md)
- [Introdução com o Advisor](advisor-get-started.md)
- [Permissões no Azure Advisor](permissions.md)



