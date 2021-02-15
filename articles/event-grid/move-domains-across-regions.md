---
title: Mover domínios da grade de eventos do Azure para outra região
description: Este artigo mostra como mover domínios de grade de eventos do Azure de uma região para outra região.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/20/2020
ms.openlocfilehash: fff8638a819511f84f15c52ad0695cdd5759f971
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89083041"
---
# <a name="move-azure-event-grid-domains-to-another-region"></a>Mover domínios da grade de eventos do Azure para outra região
Talvez você queira mover seus recursos para outra região por vários motivos. Por exemplo, para aproveitar uma nova região do Azure, para atender aos requisitos internos de políticas e governança, ou em resposta aos requisitos de planejamento de capacidade. 

Aqui estão as etapas de alto nível abordadas neste artigo: 

- **Exporte o** recurso de domínio para um modelo de Azure Resource Manager. 

    > [!IMPORTANT]
    > O recurso de domínio e os tópicos no domínio são exportados para o modelo. As assinaturas para tópicos de domínio não são exportadas. 
- **Use o modelo para implantar o domínio** na região de destino. 
- **Crie assinaturas para tópicos de domínio manualmente** na região de destino. Quando você exportou o domínio para um modelo na região atual, as assinaturas para tópicos de domínio não são exportadas. Portanto, crie-os depois que os tópicos de domínio e domínio forem criados na região de destino. 
- **verificar a implantação**. Envie um evento para um tópico de domínio no domínio e verifique se o manipulador de eventos associado à assinatura é invocado. 
- Para **concluir a movimentação**, exclua o domínio da região de origem. 

## <a name="prerequisites"></a>Pré-requisitos
- Verifique se o serviço de grade de eventos está disponível na região de destino. Consulte [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

## <a name="prepare"></a>Preparar
Para começar, exporte um modelo do Resource Manager para o domínio. 

1. Entre no [portal do Azure](https://portal.azure.com).
2. Na barra de pesquisa, digite **domínios de grade de eventos**e selecione **domínios de grade de eventos** na lista de resultados. 

    :::image type="content" source="./media/move-domains-across-regions/search-domains.png" alt-text="Pesquisar e selecionar domínios de grade de eventos":::
3. Selecione o **domínio** que você deseja exportar para um modelo do Resource Manager. 

    :::image type="content" source="./media/move-domains-across-regions/select-domain.png" alt-text="Pesquisar e selecionar domínios de grade de eventos":::   
4. Na página **domínio da grade de eventos** , selecione **Exportar modelo** em **configurações** no menu à esquerda e, em seguida, selecione **baixar** na barra de ferramentas. 

    :::image type="content" source="./media/move-domains-across-regions/export-template-download.png" alt-text="Pesquisar e selecionar domínios de grade de eventos" lightbox="./media/move-domains-across-regions/export-template-download.png":::   

    > [!IMPORTANT]
    > Os tópicos de domínio e domínio são exportados. As assinaturas para tópicos de domínio não são exportadas. Portanto, você precisa criar assinaturas para tópicos de domínio depois de mover tópicos de domínio. 
5. Localize o arquivo **. zip** que você baixou do portal e descompacte esse arquivo em uma pasta de sua escolha. Esse arquivo zip contém arquivos JSON de modelo e parâmetros. 
1. Abra o **template.jsem** um editor de sua escolha. 
8. Atualize `location` o recurso de **domínio** para a região ou local de destino. Para obter códigos de localização, consulte [locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/). O código de uma região é o nome da região sem espaços, por exemplo, `West US` é igual a `westus` .

    ```json
    "type": "Microsoft.EventGrid/domains",
    "apiVersion": "2020-06-01",
    "name": "[parameters('domains_spegriddomain_name')]",
    "location": "westus",
    ```
1. **Salve** o modelo. 

## <a name="recreate"></a>Recriar 
Implante o modelo para criar os tópicos de domínio e domínio na região de destino. 

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
    1. Para o **nome de domínio**, insira um novo nome para o domínio. 
    1. Selecione **Examinar + criar**. 
    
        :::image type="content" source="./media/move-domains-across-regions/deploy-template.png" alt-text="Pesquisar e selecionar domínios de grade de eventos":::        
    1. Depois que a validação do modelo for realizada com sucesso, selecione **criar** na parte inferior da página para implantar o recurso. 
    1. Depois que a implantação for realizada com sucesso, selecione **ir para o grupo de recursos** para navegar até a página do grupo de recursos. Confirme se há um domínio no grupo de recursos. Selecione o domínio. Confirme se há tópicos de domínio no domínio. 

## <a name="discard-or-clean-up"></a>Descartar ou limpar
Para concluir a movimentação, exclua o domínio na região de origem.  

Se você quiser recomeçar, exclua o domínio na região de destino e repita as etapas nas seções [preparar](#prepare) e [recriar](#recreate) deste artigo.

Para excluir um domínio usando o portal do Azure:

1. Na janela de pesquisa na parte superior de portal do Azure, digite **domínios de grade de eventos**e selecione **domínios de grade de eventos** nos resultados da pesquisa. 
2. Selecione o domínio a ser excluído e selecione **excluir** na barra de ferramentas. 
3. Na página confirmação, insira o nome do grupo de recursos e selecione **excluir**.  

Para excluir o grupo de recursos que contém o domínio usando o portal do Azure:

1. Na janela de pesquisa na parte superior de portal do Azure, digite **grupos de recursos**e selecione **grupos de recursos** nos resultados da pesquisa. 
2. Selecione o grupo de recursos a ser excluído e selecione **excluir** na barra de ferramentas. 
3. Na página confirmação, insira o nome do grupo de recursos e selecione **excluir**.  

## <a name="next-steps"></a>Próximas etapas
Você aprendeu como mover um domínio de grade de eventos de uma região para outra. Consulte os artigos a seguir para mover tópicos do sistema, tópicos personalizados e namespaces de parceiros entre regiões.

- [Mover tópicos do sistema entre regiões](move-system-topics-across-regions.md). 
- [Mover tópicos personalizados entre regiões](move-custom-topics-across-regions.md). 
- [Mover namespaces de parceiro entre regiões](move-partner-namespaces-across-regions.md).

Para saber mais sobre como mover recursos entre regiões e recuperação de desastres no Azure, consulte o seguinte artigo: [mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md).