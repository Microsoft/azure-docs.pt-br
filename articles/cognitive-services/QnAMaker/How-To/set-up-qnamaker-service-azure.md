---
title: Configurar um serviço de QnA Maker-QnA Maker
description: Antes de criar quaisquer bases de dados de conhecimento do QnA Maker, primeiro você deve configurar um serviço de QnA Maker no Azure. Qualquer pessoa com autorização para criar novos recursos em uma assinatura pode configurar o serviço QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: b6ab131c0fa81609b956de53f2b15d445e8979dd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102219260"
---
# <a name="manage-qna-maker-resources"></a>Gerenciar recursos do QnA Maker

Antes de criar quaisquer bases de dados de conhecimento do QnA Maker, primeiro você deve configurar um serviço de QnA Maker no Azure. Qualquer pessoa com autorização para criar novos recursos em uma assinatura pode configurar o serviço QnA Maker.

Uma compreensão sólida dos conceitos a seguir é útil antes de criar o recurso:

* [Recursos do QnA Maker](../Concepts/azure-resources.md)
* [Chaves de criação e publicação](../Concepts/azure-resources.md#keys-in-qna-maker)

## <a name="create-a-new-qna-maker-service"></a>Criar um novo serviço do QnA Maker

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

Este procedimento cria os recursos do Azure necessários para gerenciar o conteúdo da base de dados de conhecimento. Depois de concluir essas etapas, você encontrará as chaves de _assinatura_ na página **chaves** do recurso no portal do Azure.

1. Entre no portal do Azure e [crie um recurso de QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) .

1. Selecione **criar** depois de ler os termos e condições:

    ![Criar um novo serviço do QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. Em **QnA Maker**, selecione as camadas e regiões apropriadas:

    ![Criar um serviço do QnA Maker – tipo de preço e regiões](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * No campo **nome** , insira um nome exclusivo para identificar este QnA Maker serviço. Esse nome também identifica o ponto de extremidade QnA Maker ao qual suas bases de dados de conhecimento serão associadas.
    * Escolha a **assinatura** sob a qual o recurso de QnA Maker será implantado.
    * Selecione o **tipo de preço** para os serviços de gerenciamento de QnA Maker (portal e APIs de gerenciamento). Veja [mais detalhes sobre os preços de SKU](https://aka.ms/qnamaker-pricing).
    * Crie um novo **grupo de recursos** (recomendado) ou use um existente para implantar esse QnA Maker recurso. QnA Maker cria vários recursos do Azure. Ao criar um grupo de recursos para manter esses recursos, você pode facilmente localizar, gerenciar e excluir esses recursos pelo nome do grupo de recursos.
    * Selecione um **local do grupo de recursos**.
    * Escolha o **tipo de preço de pesquisa** do serviço de pesquisa cognitiva do Azure. Se a opção camada gratuita não estiver disponível (aparece esmaecida), isso significa que você já tem um serviço gratuito implantado por meio de sua assinatura. Nesse caso, você precisará começar com a camada básica. Consulte [detalhes de preços do Azure pesquisa cognitiva](https://azure.microsoft.com/pricing/details/search/).
    * Escolha o **local de pesquisa** onde você deseja que os índices de pesquisa cognitiva do Azure sejam implantados. As restrições sobre onde os dados do cliente devem ser armazenados ajudarão a determinar o local escolhido para o Azure Pesquisa Cognitiva.
    * No campo **nome do aplicativo** , insira um nome para sua instância de serviço de Azure app.
    * Por padrão, o serviço de aplicativo assume como padrão a camada padrão (S1). Você pode alterar o plano após a criação. Saiba mais sobre os [preços do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/).
    * Escolha o **local do site** em que o serviço de aplicativo será implantado.

        > [!NOTE]
        > O **local de pesquisa** pode ser diferente do **local do site**.

    * Escolha se deseja ou não habilitar **Application insights**. Se o **Application Insights** estiver habilitado, o QnA Maker coletará a telemetria em tráfego, logs de chat e erros.
    * Escolha o **local do Application insights** no qual o recurso de Application insights será implantado.
    * Como medida de economia de custo, você pode [compartilhar](configure-QnA-Maker-resources.md#configure-qna-maker-to-use-different-cognitive-search-resource) alguns, mas não todos os recursos do Azure criados para o QnA Maker.

1. Depois que todos os campos forem validados, selecione **criar**. O processo pode levar alguns minutos.

1. Após a conclusão da implantação, você verá os seguintes recursos criados em sua assinatura:

   ![O recurso criou um serviço do QnA Maker](../media/qnamaker-how-to-setup-service/resources-created.png)

    O recurso com o tipo de _Serviços cognitivas_ tem suas chaves de _assinatura_ .

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

Este procedimento cria os recursos do Azure necessários para gerenciar o conteúdo da base de dados de conhecimento. Depois de concluir essas etapas, você encontrará as chaves de *assinatura* na página **chaves** do recurso no portal do Azure.

1. Entre no portal do Azure e [crie um recurso de QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) .

1. Selecione **criar** depois de ler os termos e condições:

    ![Criar um novo serviço do QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. Em **QnA Maker**, marque a caixa de seleção gerenciado (visualização) e selecione as camadas e regiões apropriadas:

    ![Criar um novo serviço gerenciado QnA Maker-tipo de preço e regiões](../media/qnamaker-how-to-setup-service/enter-qnamaker-v2-info.png)

    * Escolha a **assinatura** sob a qual o recurso de QnA Maker será implantado.
    * Crie um novo **grupo de recursos** (recomendado) ou use um existente para implantar esse QnA Maker recurso gerenciado (versão prévia). QnA Maker gerenciado (versão prévia) cria alguns recursos do Azure. Ao criar um grupo de recursos para manter esses recursos, você pode facilmente localizar, gerenciar e excluir esses recursos pelo nome do grupo de recursos.
    * No campo **nome** , insira um nome exclusivo para identificar esse QnA Maker serviço gerenciado (versão prévia). 
    * Escolha o **local** onde você deseja que o serviço QnA Maker gerenciado (versão prévia) seja implantado. As APIs de gerenciamento e o ponto de extremidade de serviço serão hospedados neste local. 
    * Selecione o **tipo de preço** para o serviço QnA Maker gerenciado (versão prévia) (gratuito para visualização). Veja [mais detalhes sobre os preços de SKU](https://aka.ms/qnamaker-pricing).
    * Escolha o **local de pesquisa** onde você deseja que os índices de pesquisa cognitiva do Azure sejam implantados. As restrições sobre onde os dados do cliente devem ser armazenados ajudarão a determinar o local escolhido para o Azure Pesquisa Cognitiva.
    * Escolha o **tipo de preço de pesquisa** do serviço de pesquisa cognitiva do Azure. Se a opção camada gratuita não estiver disponível (aparece esmaecida), isso significa que você já tem um serviço gratuito implantado por meio de sua assinatura. Nesse caso, você precisará começar com a camada básica. Consulte [detalhes de preços do Azure pesquisa cognitiva](https://azure.microsoft.com/pricing/details/search/).

1. Depois que todos os campos forem validados, selecione **revisar + criar**. O processo pode levar alguns minutos.

1. Após a conclusão da implantação, você verá os seguintes recursos criados em sua assinatura:

    ![O recurso criou um novo serviço gerenciado (versão prévia) QnA Maker](../media/qnamaker-how-to-setup-service/resources-created-v2.png)

    O recurso com o tipo de _Serviços cognitivas_ tem suas chaves de _assinatura_ .

---

## <a name="upgrade-azure-resources"></a>Atualizar recursos do Azure

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

### <a name="upgrade-qna-maker-sku"></a>Atualizar QnA Maker SKU

Quando você quiser ter mais perguntas e respostas em sua base de dados de conhecimento, além da sua camada atual, atualize seu tipo de preço do QnA Maker Service.

Para fazer upgrade da SKU de gerenciamento do QnA Maker:

1. Vá para o recurso QnA Maker no portal do Azure e selecione **Camada de preços**.

    ![Recurso do QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Escolha o SKU apropriado e pressione **Selecionar**.

    ![Preços do QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)
    
### <a name="upgrade-app-service"></a>Atualizar serviço de aplicativo

Quando sua base de dados de conhecimento precisar atender a mais solicitações de seu aplicativo cliente, atualize seu tipo de preço do serviço de aplicativo.

Você pode [escalar verticalmente](../../../app-service/manage-scale-up.md) ou escalar horizontalmente o serviço de aplicativo.

Vá para o recurso serviço de aplicativo no portal do Azure e selecione a opção **escalar verticalmente** ou **escalar** horizontalmente, conforme necessário.

![Escala do serviço de aplicativo QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-the-azure-cognitive-search-service"></a>Atualizar o serviço de Pesquisa Cognitiva do Azure

Ao planejar ter muitas bases de dados de conhecimento, atualize o tipo de preço do serviço do Azure Cognitive Search.

No momento, não é possível realizar uma atualização in-loco da SKU do Azure Search. No entanto, pode criar um novo recurso de pesquisa do Azure com o SKU desejado, restaurar os dados para o novo recurso e vinculá-lo com a pilha do QnA Maker. Para fazer isso, siga estas etapas:

1. Crie um novo recurso de Azure Search no portal do Azure e selecione o SKU desejado.

    ![Recurso de pesquisa do Azure do QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Restaure os índices de seu recurso original da pesquisa do Azure para o novo. Consulte o [código de exemplo de backup e restauração](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. Depois que os dados forem restaurados, vá para o novo recurso do Azure Search, selecione **chaves** e anote o **nome** e a **chave de administração**:

    ![Chaves de pesquisa do QnA Maker Azure](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Para vincular o novo recurso de Azure Search à pilha de QnA Maker, vá para a instância do serviço de aplicativo QnA Maker.

    ![Instância do serviço de aplicativo QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Selecione **Configurações do aplicativo** e modifique as configurações nos campos **AzureSearchName** e **AzureSearchAdminKey** da etapa 3.

    ![QnA Maker configuração do serviço de aplicativo](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Reinicie a instância do Serviço de Aplicativo.

    ![Reinicialização da instância do serviço de aplicativo QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)
    
### <a name="inactivity-policy-for-free-search-resources"></a>Política de inatividade para recursos de pesquisa gratuitos

Se você não estiver usando um recurso do QnA Maker, deverá remover todos os recursos. Se você não remover os recursos não utilizados, sua base de dados de conhecimento deixará de funcionar se você tiver criado um recurso de pesquisa gratuito.

Os recursos de pesquisa gratuitos são excluídos após 90 dias sem receber uma chamada à API.
    
# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

### <a name="upgrade-the-azure-cognitive-search-service"></a>Atualizar o serviço de Pesquisa Cognitiva do Azure

Ao planejar ter muitas bases de dados de conhecimento, atualize o tipo de preço do serviço do Azure Cognitive Search.

No momento, não é possível realizar uma atualização in-loco da SKU do Azure Search. No entanto, pode criar um novo recurso de pesquisa do Azure com o SKU desejado, restaurar os dados para o novo recurso e vinculá-lo com a pilha do QnA Maker. Para fazer isso, siga estas etapas:

1. Crie um novo recurso de Azure Search no portal do Azure e selecione o SKU desejado.

    ![Recurso de pesquisa do Azure do QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Restaure os índices de seu recurso original da pesquisa do Azure para o novo. Consulte o [código de exemplo de backup e restauração](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. Para vincular o novo recurso de Azure Search ao serviço QnA Maker gerenciado (versão prévia), consulte o tópico abaixo.

### <a name="inactivity-policy-for-free-search-resources"></a>Política de inatividade para recursos de pesquisa gratuitos

Se você não estiver usando um recurso do QnA Maker, deverá remover todos os recursos. Se você não remover os recursos não utilizados, sua base de dados de conhecimento deixará de funcionar se você tiver criado um recurso de pesquisa gratuito.

Os recursos de pesquisa gratuitos são excluídos após 90 dias sem receber uma chamada à API.

---

## <a name="delete-azure-resources"></a>Excluir recursos do Azure

Se você excluir qualquer um dos recursos do Azure usados em suas bases de conhecimento do QnA Maker, as bases de conhecimento não funcionarão mais. Antes de excluir qualquer recurso, exporte suas bases de conhecimento da página **Configurações**.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o serviço de [aplicativo](../../../app-service/index.yml) e o [serviço de pesquisa](../../../search/index.yml).

> [!div class="nextstepaction"]
> [Saiba como criar com outras pessoas](../index.yml)
