---
title: Mover um namespace de retransmissão do Azure para outra região
description: Este artigo mostra como mover um namespace de retransmissão do Azure da região atual para outra região.
ms.topic: how-to
ms.date: 09/03/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 60a182764639341fcda159356dd9fe6c65cfabd9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89463437"
---
# <a name="move-an-azure-relay-namespace-to-another-region"></a>Mover um namespace de retransmissão do Azure para outra região
Este artigo mostra como mover um namespace de retransmissão do Azure de uma região para outra região. Aqui estão as etapas de alto nível:

1. **Exporte** o namespace de retransmissão para um modelo de Azure Resource Manager.
1. **Local de atualização (região)** para recursos no modelo. Além disso, exclua todas as retransmissões **dinâmicas** do WCF do modelo. 

    As retransmissões do WCF têm dois modos. No primeiro modo, a retransmissão do WCF é criada explicitamente usando o modelo de portal do Azure ou Azure Resource Manager. Na página **retransmissões do WCF** da portal do Azure, você vê a propriedade **isdinâmica** definida como **false** para uma retransmissão nesse modo. 

    No segundo modo, a retransmissão do WCF é gerada automaticamente quando um ouvinte (servidor) se conecta a um determinado endereço de ponto de extremidade. Desde que o ouvinte esteja conectado à retransmissão, você verá a retransmissão na lista de retransmissões do WCF na portal do Azure. Para uma retransmissão nesse modo, a propriedade **IsDynamic** é definida como **true** porque ela é gerada dinamicamente. A retransmissão dinâmica do WCF desaparece quando o ouvinte se desconecta. 
1. **Implante** recursos usando o modelo na região de destino.

## <a name="prerequisites"></a>Pré-requisitos
Verifique se o serviço de retransmissão do Azure está disponível na região de destino. Consulte [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=service-bus&regions=all). 
 
## <a name="prepare"></a>Preparar
Para começar, exporte um modelo do Resource Manager. Este modelo contém configurações que descrevem o namespace de retransmissão do Azure.

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **todos os recursos** e, em seguida, selecione o namespace de retransmissão do Azure.
3. Selecione **Exportar modelo** em **configurações** no menu à esquerda.
4. Escolha **baixar** na página **Exportar modelo** .

    :::image type="content" source="./media/move-across-regions/download-template.png" alt-text="Baixar modelo do Resource Manager":::
5. Localize o arquivo. zip que você baixou do portal e descompacte esse arquivo em uma pasta de sua escolha. Esse arquivo zip contém os arquivos JSON de modelo e parâmetros. 
1. Abra o **template.jsno** arquivo da pasta extraída em um editor de sua escolha.
1. Procure `location` e substitua o valor da propriedade pelo novo nome da região. Para obter códigos de localização, consulte [locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/). O código de uma região é o nome da região sem espaços, por exemplo, `West US` é igual a `westus` .
1. Remover definições de recursos **dinâmicos de retransmissão do WCF** (tipo: `Microsoft.Relay/namespaces/WcfRelays` ). As retransmissões dinâmicas do WCF são aquelas que têm a propriedade **IsDynamic** definida como **true** na página **retransmissões** . No exemplo a seguir, **echoservice** é uma retransmissão dinâmica do WCF e sua definição deve ser removida do modelo. 

    :::image type="content" source="./media/move-across-regions/dynamic-relays.png" alt-text="Baixar modelo do Resource Manager":::

## <a name="move"></a>Mover
Implante o modelo para criar um namespace de retransmissão na região de destino. 

1. Na portal do Azure, selecione **criar um recurso**.
2. Em **Pesquisar no Marketplace**, digite **implantação de modelo** para o texto de pesquisa, selecione **implantação de modelo (implantar usando modelos personalizados)** e pressione **Enter**.

    :::image type="content" source="./media/move-across-regions/new-template-deployment.png" alt-text="Baixar modelo do Resource Manager":::    
1. Na página **implantação de modelo** , selecione **criar**.

    :::image type="content" source="./media/move-across-regions/template-deployment-create-button.png" alt-text="Baixar modelo do Resource Manager":::        
1. Na página **implantação personalizada** , selecione **criar seu próprio modelo no editor**.

    :::image type="content" source="./media/move-across-regions/build-template-link.png" alt-text="Baixar modelo do Resource Manager":::            
1. Na página **Editar modelo** , selecione **carregar arquivo** na barra de ferramentas e siga as instruções para carregar o **template.jsno** arquivo que você baixou na última seção.

    :::image type="content" source="./media/move-across-regions/select-template.png" alt-text="Baixar modelo do Resource Manager":::                
1. Selecione **salvar** para salvar o modelo. 

    :::image type="content" source="./media/move-across-regions/save-template.png" alt-text="Baixar modelo do Resource Manager":::                    
1. Na página **implantação personalizada** , siga estas etapas: 
    1. Selecione uma **assinatura**do Azure. 
    2. Selecione um **grupo de recursos** existente ou crie um. 
    3. Selecione o **local** ou a região de destino. Se você selecionou um grupo de recursos existente, essa configuração será somente leitura. 
    4. Insira um novo **nome para o namespace**.
    1. Selecione **Examinar + criar**. 

        :::image type="content" source="./media/move-across-regions/deploy-template.png" alt-text="Baixar modelo do Resource Manager":::
    1. Na página **revisar + criar** , selecione **criar** na parte inferior da página. 
    
## <a name="verify"></a>Verificar
1. Depois que a implantação for bem-sucedida, selecione **ir para o grupo de recursos**.

    :::image type="content" source="./media/move-across-regions/resource-group-navigation-link.png" alt-text="Baixar modelo do Resource Manager":::    
1. Na página **grupo de recursos** , selecione o namespace de retransmissão do Azure. 

    :::image type="content" source="./media/move-across-regions/select-namespace.png" alt-text="Baixar modelo do Resource Manager":::    
1. Na página **namespace de retransmissão do Azure** , selecione **conexões híbridas** ou **retransmissões do WCF** no menu à esquerda para verificar se as conexões híbridas e as retransmissões do WCF foram criadas. Se você se esqueceu de excluir definições para retransmissões dinâmicas do WCF antes de importar o modelo, exclua-as na página **retransmissões do WCF** . As retransmissões dinâmicas do WCF são criadas automaticamente quando os clientes se conectam ao namespace de retransmissão. 

## <a name="discard-or-clean-up"></a>Descartar ou limpar
Após a implantação, se você quiser começar novamente, poderá excluir o namespace de **destino do Azure Relay**e repetir as etapas descritas nas seções [preparar](#prepare) e [mover](#move) deste artigo.

Para confirmar as alterações e concluir a movimentação de um namespace, exclua o **namespace de retransmissão do Azure** na região de origem. 

Para excluir um namespace de retransmissão do Azure (origem ou destino) usando o portal do Azure:

1. Na janela de pesquisa na parte superior de portal do Azure, digite **retransmissões**e selecione **retransmissões** de **Serviços** nos resultados da pesquisa. Você verá todos os namespaces de retransmissão do Azure em uma lista.
2. Selecione o namespace de destino a ser excluído para abrir a página de **retransmissão** . 
1. Na página **retransmissão** , selecione **excluir** na barra de ferramentas. 

    ![Excluir namespace-botão](./media/move-across-regions/delete-namespace-button.png)
3. Na página **excluir namespace** , digite o nome do namespace de retransmissão do Azure para confirmar a exclusão e, em seguida, selecione **excluir**. 

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você moveu um namespace de retransmissão do Azure de uma região para outra região. Para saber mais sobre como mover recursos entre regiões e recuperação de desastres no Azure, confira:

- [Mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md)
