---
title: Mover um namespace do barramento de serviço para outra região | Microsoft Docs
description: Este artigo mostra como mover um namespace do barramento de serviço do Azure da região atual para outra região.
ms.topic: how-to
ms.date: 06/23/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 573ea96437cf6cb76854ffa417fd3ad3fb86138b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88860728"
---
# <a name="move-an-azure-service-bus-namespace-to-another-region"></a>Mover um namespace do barramento de serviço do Azure para outra região
Há vários cenários em que você deseja mover o namespace do barramento de serviço existente de uma região para outra. Por exemplo, talvez você queira criar um namespace com a mesma configuração para teste. Você também pode querer criar um namespace secundário em outra região como parte do [planejamento de recuperação de desastre](service-bus-geo-dr.md).

Aqui estão as etapas de alto nível:

1. Exporte o namespace do barramento de serviço na região atual para um modelo de Azure Resource Manager. 
1. Local de atualização dos recursos no modelo. Além disso, remova o filtro de assinatura padrão do modelo porque não é possível criar uma regra padrão, pois ela é criada automaticamente para você. 
1. Use o modelo para implantar o namespace do barramento de serviço na região de destino. 
1. Verifique a implantação para garantir que o namespace, as filas, os tópicos e as assinaturas de tópicos sejam criados na região de destino. 
1. Conclua a movimentação excluindo o namespace da região de origem após o processamento de todas as mensagens. 

## <a name="prerequisites"></a>Pré-requisitos
Verifique se o barramento de serviço do Azure e os recursos que sua conta usa têm suporte na região de destino.
 
## <a name="prepare"></a>Preparar
Para começar, exporte um modelo do Resource Manager. Este modelo contém configurações que descrevem o namespace do barramento de serviço.

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **todos os recursos** e, em seguida, selecione o namespace do barramento de serviço.
3. Selecione **configurações** de >  >  **modelo de exportação**.
4. Escolha **baixar** na página **Exportar modelo** .

    :::image type="content" source="./media/move-across-regions/download-template.png" alt-text="Baixar modelo do Resource Manager":::
5. Localize o arquivo. zip que você baixou do portal e descompacte esse arquivo em uma pasta de sua escolha. Esse arquivo zip contém os arquivos JSON de modelo e parâmetros. 
1. Abra o template.jsno arquivo na pasta extraída. 
1. Procure `location` e substitua o valor da propriedade pelo novo nome da região ou local. Para obter códigos de localização, consulte [locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/). O código de uma região é o nome da região sem espaços, por exemplo, `West US` é igual a `westus` .
1. Remover definições de recursos do tipo: `Microsoft.ServiceBus/namespaces/topics/subscriptions/rules` . Não se esqueça de remover o caractere de vírgula ( `,` ) que precede esta seção para manter o JSON válido.  

    > [!NOTE]
    > Você não pode criar uma regra padrão para uma assinatura usando um modelo do Resource Manager. A regra padrão é criada automaticamente quando a assinatura é criada na região de destino. 

## <a name="move"></a>Mover
Implante o modelo para criar um namespace do barramento de serviço na região de destino. 

1. No portal do Azure, selecione **Criar um recurso**.
2. Em **Pesquisar no Marketplace**, digite **implantação de modelo** para o texto de pesquisa, selecione **implantação de modelo (implantar usando modelos personalizados)** e pressione **Enter**.

    :::image type="content" source="./media/move-across-regions/new-template-deployment.png" alt-text="Nova implantação de modelo":::    
1. Na página **implantação de modelo** , selecione **criar**.

    :::image type="content" source="./media/move-across-regions/template-deployment-create-button.png" alt-text="Novo modelo implantação-botão criar":::        
1. Na página **implantação personalizada** , selecione **criar seu próprio modelo no editor**.

    :::image type="content" source="./media/move-across-regions/build-template-link.png" alt-text="Crie seu próprio modelo no editor – link":::            
1. Na página **Editar modelo** , selecione **carregar arquivo** na barra de ferramentas e siga as instruções para carregar o **template.jsno** arquivo que você baixou na última seção.

    :::image type="content" source="./media/move-across-regions/select-template.png" alt-text="Selecionar modelo":::                
1. Selecione **salvar** para salvar o modelo. 

    :::image type="content" source="./media/move-across-regions/save-template.png" alt-text="Salvar modelo":::                    
1. Na página **implantação personalizada** , siga estas etapas: 
    1. Selecione uma **assinatura** do Azure. 
    2. Selecione um **grupo de recursos** existente ou crie um. 
    3. Selecione o **local** ou a região de destino. Se você selecionou um grupo de recursos existente, essa configuração será somente leitura. 
    4. Insira um novo **nome para o namespace**.
    1. Selecione **Examinar + criar**. 

        :::image type="content" source="./media/move-across-regions/deploy-template.png" alt-text="Implantar modelo do Resource Manager":::
    1. Na página **revisar + criar** , selecione **criar** na parte inferior da página. 
    
## <a name="verify"></a>Verificar
1. Depois que a implantação for bem-sucedida, selecione **ir para o grupo de recursos**.

    :::image type="content" source="./media/move-across-regions/resource-group-navigation-link.png" alt-text="Ir para o link do grupo de recursos":::    
1. Na página **grupo de recursos** , selecione o namespace do barramento de serviço. 

    :::image type="content" source="./media/move-across-regions/select-namespace.png" alt-text="Selecionar namespace do barramento de serviço":::    
1. Na página **namespace do barramento de serviço** , verifique se você vê as filas, os tópicos e as assinaturas da região de origem. 
    1. Você vê as **filas** no namespace na parte inferior do painel direito.         
    
        :::image type="content" source="./media/move-across-regions/queue-namespace.png" alt-text="Filas no namespace":::
    2. Alterne para a guia **Tópicos** para ver os tópicos no namespace
    
        :::image type="content" source="./media/move-across-regions/topics-namespace.png" alt-text="Tópicos no namespace":::
    3. Selecione o tópico para verificar se as assinaturas foram criadas. 

        :::image type="content" source="./media/move-across-regions/topic-subscriptions.png" alt-text="Assinaturas de tópico":::      
    
    

## <a name="discard-or-clean-up"></a>Descartar ou limpar
Após a implantação, se você quiser iniciar novamente, poderá excluir o namespace do **barramento de serviço de destino** e repetir as etapas descritas nas seções [preparar](#prepare) e [mover](#move) deste artigo.

Para confirmar as alterações e concluir a movimentação de um namespace do barramento de serviço, exclua o **namespace do barramento de serviço de origem**. Certifique-se de processar todas as mensagens antes de excluir o namespace. 

Para excluir um namespace do barramento de serviço (origem ou destino) usando o portal do Azure:

1. Na janela de pesquisa na parte superior de portal do Azure, digite **barramento de serviço** e selecione **barramento de serviço** nos resultados da pesquisa. Você vê os namespaces do barramento de serviço em uma lista.
2. Selecione o namespace de destino a ser excluído e selecione **excluir** na barra de ferramentas. 

    ![Excluir namespace-botão](./media/move-across-regions/delete-namespace-button.png)
3. Na página **excluir recursos** , verifique os recursos selecionados e confirme a exclusão digitando **Sim** e, em seguida, selecione **excluir**. 

    Outra opção é excluir o grupo de recursos que tem o namespace do barramento de serviço. Na página **grupo de recursos** , selecione **excluir grupo de recursos** na barra de ferramentas e confirme a exclusão. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você moveu um namespace do barramento de serviço do Azure de uma região para outra e limpou os recursos de origem.  Para saber mais sobre como mover recursos entre regiões e recuperação de desastres no Azure, confira:

- [Mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md)
