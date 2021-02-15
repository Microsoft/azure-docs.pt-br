---
title: Mover namespaces de parceiro de grade de eventos do Azure para outra região
description: Este artigo mostra como mover os namespaces do parceiro de grade de eventos do Azure de uma região para outra região.
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/20/2020
ms.openlocfilehash: 6783db6b9bb1c7d48b308234a179925d6f30e281
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89083040"
---
# <a name="move-azure-event-grid-partner-namespaces-to-another-region"></a>Mover namespaces de parceiro de grade de eventos do Azure para outra região
Talvez você queira mover seus recursos para outra região por vários motivos. Por exemplo, para aproveitar uma nova região do Azure, para atender aos requisitos internos de políticas e governança, ou em resposta aos requisitos de planejamento de capacidade. 

Aqui estão as etapas de alto nível abordadas neste artigo: 

- **Exporte o recurso de namespace de parceiro** para um modelo de Azure Resource Manager. Exclua as definições dos recursos do canal de eventos no modelo. Um canal de evento pode ter uma referência à ID de Azure Resource Manager do tópico de parceiro, que pertence a um cliente. Portanto, eles não podem ser criados usando o modelo na região de destino.  
- **Use o modelo para implantar o namespace do parceiro** na região de destino. Em seguida, crie canais de eventos no novo namespace de parceiro na região de destino. 
- Para **concluir a movimentação**, exclua o namespace do parceiro da região de origem. 

    > [!NOTE]
    > - Não há suporte para a exportação de **Tópicos de parceiros** para um modelo de Azure Resource Manager porque os clientes não podem criar um tópico de parceiro diretamente. 
    > - Os **registros de parceiros** são recursos globais (não vinculados a nenhuma região específica), portanto, movê-los de uma região para outra região não é aplicável. 

## <a name="prerequisites"></a>Pré-requisitos
- Verifique se o serviço de grade de eventos está disponível na região de destino. Consulte [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

## <a name="prepare"></a>Preparar
Para começar, exporte um modelo do Resource Manager para o namespace do parceiro. 

1. Entre no [portal do Azure](https://portal.azure.com).
2. Na barra de pesquisa na parte superior, digite **namespaces de parceiro de grade de eventos**e selecione **namespaces de parceiro de grade de eventos** na lista de resultados. 
3. Selecione o **namespace de parceiro** que você deseja exportar para um modelo do Resource Manager. 
4. Na página **namespace do parceiro de grade de eventos** , selecione **Exportar modelo** em **configurações** no menu à esquerda e, em seguida, selecione **baixar** na barra de ferramentas. 

    :::image type="content" source="./media/move-partner-namespaces-across-regions/download-template.png" alt-text="Exportar modelo-download de >" lightbox="./media/move-partner-namespaces-across-regions/download-template.png":::   
5. Localize o arquivo **. zip** que você baixou do portal e descompacte esse arquivo em uma pasta de sua escolha. Esse arquivo zip contém arquivos JSON de modelo e parâmetros. 
1. Abra o **template.jsem** um editor de sua escolha. 
8. Atualize `location` o recurso de **tópico** para a região ou local de destino. Para obter códigos de localização, consulte [locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/). O código de uma região é o nome da região sem espaços, por exemplo, `West US` é igual a `westus` .

    ```json
    {
        "type": "Microsoft.EventGrid/partnerNamespaces",
        "apiVersion": "2020-04-01-preview",
        "name": "[parameters('partnerNamespace_name')]",
        "location": "westus",
        "properties": {
            "partnerRegistrationFullyQualifiedId": "[parameters('partnerRegistrations_ContosoCorpAccount1_externalid')]"
        }
    }
    ``` 
1. **Salve** o modelo. 

## <a name="recreate"></a>Recriar 
Implante o modelo para criar um namespace de parceiro na região de destino. 

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
    1. Para **local**, selecione a região de destino. Se você selecionou um grupo de recursos existente, essa configuração será somente leitura. 
    1. Para o **nome do namespace do parceiro**, insira um nome para o novo namespace do parceiro. 
    1. Para a ID externa do registro do parceiro, insira a ID de recurso do registro do parceiro no seguinte formato: `/subscriptions/<Azure subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.EventGrid/partnerRegistrations/<Partner registration name>` .
    1. Selecione a caixa de seleção **Concordo com os termos e condições declarados acima**.     
    1. Selecione **examinar + criar** para iniciar o processo de implantação. 
    1. Na página **revisar + criar** , examine as configurações e selecione **criar**. 

## <a name="discard-or-clean-up"></a>Descartar ou limpar
Para concluir a movimentação, exclua o namespace do parceiro na região de origem.  

Se você quiser reiniciar, exclua o namespace do parceiro na região de destino e repita as etapas nas seções [preparar](#prepare) e [recriar](#recreate) deste artigo.

Para excluir um namespace de parceiro usando o portal do Azure:

1. Na janela de pesquisa na parte superior de portal do Azure, digite **namespaces de parceiro de grade de eventos**e selecione **namespaces de parceiro de grade de eventos** dos resultados da pesquisa. 
2. Selecione o namespace do parceiro a ser excluído e selecione **excluir** na barra de ferramentas. 
3. **Confirme** a exclusão para excluir o namespace do parceiro. 

## <a name="next-steps"></a>Próximas etapas
Você aprendeu a mover um namespace de parceiro de grade de eventos de uma região para outra. Consulte os artigos a seguir para mover tópicos do sistema, tópicos personalizados e domínios entre regiões.

- [Mover tópicos do sistema entre regiões](move-system-topics-across-regions.md). 
- [Mover tópicos personalizados entre regiões](move-custom-topics-across-regions.md). 
- [Mover domínios entre regiões](move-domains-across-regions.md).

Para saber mais sobre como mover recursos entre regiões e recuperação de desastres no Azure, consulte o seguinte artigo: [mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md).