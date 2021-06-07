---
title: Mover um namespace da Retransmissão do Azure para outra região
description: Este artigo mostra como mover um namespace da Retransmissão do Azure da região atual para outra região.
ms.topic: how-to
ms.date: 09/03/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 60a182764639341fcda159356dd9fe6c65cfabd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89463437"
---
# <a name="move-an-azure-relay-namespace-to-another-region"></a>Mover um namespace da Retransmissão do Azure para outra região
Este artigo mostra como mover um namespace da Retransmissão do Azure de uma região para outra. Aqui estão as etapas de alto nível:

1. **Exporte** o namespace de Retransmissão para um modelo do Azure Resource Manager.
1. **Atualize a localização (região)** dos recursos no modelo. Além disso, exclua todas as retransmissões **dinâmicas** do WFC do modelo. 

    As retransmissões do WCF têm dois modos. No primeiro modo, a retransmissão do WCF é criada explicitamente usando o modelo do Azure Resource Manager ou portal do Azure. Na página **Retransmissões do WCF** do portal do Azure, você verá a propriedade **isDynamic** definida como **false** para uma retransmissão nesse modo. 

    No segundo modo, a retransmissão do WCF é gerada automaticamente quando um ouvinte (servidor) se conecta a determinado endereço de ponto de extremidade. Desde que o ouvinte esteja conectado à retransmissão, você a verá retransmissão na lista de retransmissões do WCF no portal do Azure. Para uma retransmissão nesse modo, a propriedade **isDynamic** é definida como **true** porque ela é gerada dinamicamente. A retransmissão dinâmica do WCF desaparece quando o ouvinte se desconecta. 
1. **Implante** recursos usando o modelo da região de destino.

## <a name="prerequisites"></a>Pré-requisitos
Verifique se o serviço da Retransmissão do Azure está disponível na região de destino. Consulte [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=service-bus&regions=all). 
 
## <a name="prepare"></a>Preparar
Para começar, exporte um modelo do Resource Manager. Esse modelo contém configurações que descrevem o namespace da Retransmissão do Azure.

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os recursos** e o namespace da Retransmissão do Azure.
3. Selecione **Exportar modelo** em **Configurações** no menu à esquerda.
4. Escolha **Baixar** na página **Exportar modelo**.

    :::image type="content" source="./media/move-across-regions/download-template.png" alt-text="Baixar o modelo do Resource Manager":::
5. Localize o arquivo .zip que você baixou do portal e descompacte-o na pasta desejada. Esse arquivo zip contém os arquivos JSON de parâmetros e modelo. 
1. Abra o arquivo **template.json** da pasta extraída em um editor de sua escolha.
1. Procure por `location` e substitua o valor da propriedade pelo novo nome da região. Para obter códigos de localização, confira os [locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/). O código de uma região é o nome da região sem espaços, por exemplo, `West US` é igual a `westus`.
1. Remova definições de recursos de **retransmissão dinâmica do WCF** (tipo: `Microsoft.Relay/namespaces/WcfRelays`). As retransmissões dinâmicas do WCF são aquelas que têm a propriedade **isDynamic** definida como **true** na página **Retransmissões**. No exemplo a seguir, **echoservice** é uma retransmissão dinâmica do WCF, e sua definição deve ser removida do modelo. 

    :::image type="content" source="./media/move-across-regions/dynamic-relays.png" alt-text="Retransmissões dinâmicas":::

## <a name="move"></a>Mover
Implante o modelo para criar um namespace da Retransmissão na região de destino. 

1. No portal do Azure, selecione **Criar um recurso**.
2. Em **Pesquisar no Marketplace**, digite **implantação de modelo** para o texto de pesquisa, selecione **Implantação de modelo (implantar usando modelos personalizados)** e pressione **ENTER**.

    :::image type="content" source="./media/move-across-regions/new-template-deployment.png" alt-text="Nova implantação de modelo":::    
1. Na página **Implantação de modelo**, selecione **Criar**.

    :::image type="content" source="./media/move-across-regions/template-deployment-create-button.png" alt-text="Nova implantação de modelo – botão Criar":::        
1. Na página **Implantação personalizada**, selecione **Criar seu modelo no editor**.

    :::image type="content" source="./media/move-across-regions/build-template-link.png" alt-text="Criar seu modelo no editor – link":::            
1. Na página **Editar modelo**, selecione **Carregar arquivo** na barra de ferramentas e siga as instruções para carregar o arquivo **template.json** que você baixou na última seção.

    :::image type="content" source="./media/move-across-regions/select-template.png" alt-text="Selecionar modelo":::                
1. Selecione **Salvar** para salvar o modelo. 

    :::image type="content" source="./media/move-across-regions/save-template.png" alt-text="Salvar modelo":::                    
1. Na página **Implantação personalizada**, siga estas etapas: 
    1. Selecione uma **assinatura** do Azure. 
    2. Selecione um **grupo de recursos** existente ou crie um. 
    3. Escolha o **local** ou a região de destino. Se você selecionou um grupo de recursos existente, essa configuração é somente leitura. 
    4. Insira um novo **nome para o namespace**.
    1. Selecione **Examinar + criar**. 

        :::image type="content" source="./media/move-across-regions/deploy-template.png" alt-text="Implantar modelo do Resource Manager":::
    1. Na página **Examinar + criar**, selecione **Criar** na parte inferior da página. 
    
## <a name="verify"></a>Verificar
1. Após a implantação bem-sucedida, selecione **Ir para o grupo de recursos**.

    :::image type="content" source="./media/move-across-regions/resource-group-navigation-link.png" alt-text="Link Ir para o grupo de recursos":::    
1. Na página **Grupo de recursos**, selecione o namespace da Retransmissão do Azure. 

    :::image type="content" source="./media/move-across-regions/select-namespace.png" alt-text="Selecionar o namespace da Retransmissão do Azure":::    
1. Na página **Namespace da Retransmissão do Azure**, selecione **Conexões Híbridas** ou **Retransmissões do WCF** no menu à esquerda para verificar se as conexões híbridas e as retransmissões do WCF foram criadas. Se você se esqueceu de excluir definições para retransmissões dinâmicas do WCF antes de importar o modelo, exclua-as na página **Retransmissões do WCF**. As retransmissões dinâmicas do WCF são criadas automaticamente quando os clientes se conectam ao namespace da Retransmissão. 

## <a name="discard-or-clean-up"></a>Descartar ou limpar
Se você quiser recomeçar depois da implantação, poderá excluir o **namespace da Retransmissão do Azure de destino** e repetir as etapas descritas nas seções [Preparar](#prepare) e [Mover](#move) deste artigo.

Para confirmar as alterações e concluir a movimentação de um namespace, exclua o **namespace da Retransmissão do Azure** na região de origem. 

Para excluir um namespace da Retransmissão do Azure (origem ou destino) usando o portal do Azure:

1. Na janela de pesquisa na parte superior do portal do Azure, digite **Retransmissões** e selecione **Retransmissões** em **Serviços** nos resultados da pesquisa. Você verá todos os namespaces da Retransmissão do Azure em uma lista.
2. Selecione o namespace de destino a ser excluído para abrir a página de **Retransmissão**. 
1. Na página **Retransmissão**, selecione **Excluir** na barra de ferramentas. 

    ![Excluir namespace – botão](./media/move-across-regions/delete-namespace-button.png)
3. Na página **Excluir Namespace**, digite o nome do namespace da Retransmissão do Azure para confirmar a exclusão e selecione **Excluir**. 

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você moveu um namespace da Retransmissão do Azure de uma região para outra. Para saber mais sobre como mover recursos entre regiões e recuperação de desastres no Azure, confira:

- [Mover recursos para um novo grupo de recursos ou assinatura](../azure-resource-manager/management/move-resource-group-and-subscription.md)
