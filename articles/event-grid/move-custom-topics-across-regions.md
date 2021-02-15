---
title: Mover tópicos personalizados da grade de eventos do Azure para outra região
description: Este artigo mostra como mover tópicos personalizados da grade de eventos do Azure de uma região para outra.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/28/2020
ms.openlocfilehash: d0656a4f6ec1c7431cf7111f786b0f1d779166e3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89145325"
---
# <a name="move-azure-event-grid-custom-topics-to-another-region"></a>Mover tópicos personalizados da grade de eventos do Azure para outra região
Talvez você queira mover seus recursos para outra região por vários motivos. Por exemplo, para aproveitar uma nova região do Azure, para atender aos requisitos internos de políticas e governança, ou em resposta aos requisitos de planejamento de capacidade. 

Aqui estão as etapas de alto nível abordadas neste artigo: 

- **Exporte o recurso de tópico personalizado** para um modelo de Azure Resource Manager. 

    > [!IMPORTANT]
    > Somente o tópico personalizado é exportado para o modelo. Todas as assinaturas do tópico não são exportadas.
- **Use o modelo para implantar o tópico personalizado** na região de destino. 
- **Crie assinaturas manualmente** na região de destino. Quando você exportou o tópico personalizado para um modelo na região atual, somente o tópico é exportado. As assinaturas não estão incluídas no modelo, portanto, crie-as manualmente depois que o tópico personalizado for criado na região de destino. 
- **verificar a implantação**. Verifique se o tópico personalizado foi criado na região de destino. 
- Para **concluir a movimentação**, exclua o tópico personalizado da região de origem. 

## <a name="prerequisites"></a>Pré-requisitos
- Conclua o [início rápido: rotear eventos personalizados para o ponto de extremidade da Web](custom-event-quickstart-portal.md) na região de origem. Execute esta etapa para que você possa testar as etapas neste artigo. 
- Verifique se o serviço de grade de eventos está disponível na região de destino. Consulte [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

## <a name="prepare"></a>Preparar
Para começar, exporte um modelo do Resource Manager para o tópico personalizado. 

1. Entre no [portal do Azure](https://portal.azure.com).
2. Na barra de pesquisa, digite **Tópicos da grade de eventos**e selecione **Tópicos da grade de eventos** na lista de resultados. 

    :::image type="content" source="./media/move-custom-topics-across-regions/search-topics.png" alt-text="Pesquisar e selecionar tópicos de grade de eventos":::
3. Selecione o **tópico** que você deseja exportar para um modelo do Resource Manager. 

    :::image type="content" source="./media/move-custom-topics-across-regions/select-custom-topic.png" alt-text="Pesquisar e selecionar tópicos de grade de eventos":::   
4. Na página **tópico da grade de eventos** , selecione **Exportar modelo** em **configurações** no menu à esquerda e, em seguida, selecione **baixar** na barra de ferramentas. 

    :::image type="content" source="./media/move-custom-topics-across-regions/export-template-download.png" alt-text="Pesquisar e selecionar tópicos de grade de eventos"
    ```
1. **Salve** o modelo. 

## <a name="recreate"></a>Recriar 
Implante o modelo para criar um tópico personalizado na região de destino. 

1. Na portal do Azure, selecione **criar um recurso**.
2. Em **Pesquisar no Marketplace**, digite **implantação de modelo**e pressione **Enter**.
3. Selecione **implantação de modelo**.
4. Selecione **Criar**.
5. Selecione **Criar seu próprio modelo no editor**.
6. Selecione **carregar arquivo**e siga as instruções para carregar o **template.jsno** arquivo que você baixou na última seção.
7. Selecione **salvar** para salvar o modelo. 
8. Na página **implantação personalizada** , siga estas etapas: 
    1. Selecione uma **assinatura**do Azure. 
    1. Selecione um **grupo de recursos** existente na região de destino ou crie um. 
    1. Para **região**, selecione a região de destino. Se você selecionou um grupo de recursos existente, essa configuração será somente leitura. 
    1. Para o **nome do tópico**, insira um novo nome para o tópico. 
    1. Selecione **Revisar + criar** na parte inferior da página. 
    
        :::image type="content" source="./media/move-custom-topics-across-regions/deploy-template.png" alt-text="Pesquisar e selecionar tópicos de grade de eventos":::
    1. Na página **revisar + criar** , examine as configurações e selecione **criar**. 

## <a name="verify"></a>Verificar

1. Depois que a implantação for realizada com sucesso, selecione **ir para o recurso**. 

    :::image type="content" source="./media/move-custom-topics-across-regions/navigate-custom-topic.png" alt-text="Pesquisar e selecionar tópicos de grade de eventos":::
1. Confirme que você vê a página de **tópico da grade de eventos** para o tópico personalizado.   
1. Siga as etapas em [rotear eventos personalizados para um ponto de extremidade da Web](custom-event-quickstart-portal.md#send-an-event-to-your-topic) para enviar eventos para o tópico. Verifique se o manipulador de eventos de webhook é invocado. 

## <a name="discard-or-clean-up"></a>Descartar ou limpar
Para concluir a movimentação, exclua o tópico personalizado na região de origem.  

Se você quiser recomeçar, exclua o tópico na região de destino e repita as etapas nas seções [preparar](#prepare) e [recriar](#recreate) deste artigo.

Para excluir um tópico personalizado usando o portal do Azure:

1. Na janela de pesquisa na parte superior de portal do Azure, digite **Tópicos da grade de eventos**e selecione **Tópicos da grade de eventos** nos resultados da pesquisa. 
2. Selecione o tópico a ser excluído e selecione **excluir** na barra de ferramentas. 
3. Na página confirmação, insira o nome do grupo de recursos e selecione **excluir**.  

Para excluir o grupo de recursos que contém o tópico personalizado usando o portal do Azure:

1. Na janela de pesquisa na parte superior de portal do Azure, digite **grupos de recursos**e selecione **grupos de recursos** nos resultados da pesquisa. 
2. Selecione o grupo de recursos a ser excluído e selecione **excluir** na barra de ferramentas. 
3. Na página confirmação, insira o nome do grupo de recursos e selecione **excluir**.  

## <a name="next-steps"></a>Próximas etapas
Você aprendeu a mover um tópico personalizado da grade de eventos de uma região para outra. Consulte os artigos a seguir para mover os tópicos do sistema, domínios e namespaces de parceiro entre regiões.

- [Mover tópicos do sistema entre regiões](move-system-topics-across-regions.md). 
- [Mover domínios entre regiões](move-domains-across-regions.md). 
- [Mover namespaces de parceiro entre regiões](move-partner-namespaces-across-regions.md).

Para saber mais sobre como mover recursos entre regiões e recuperação de desastres no Azure, consulte o seguinte artigo: [mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md).