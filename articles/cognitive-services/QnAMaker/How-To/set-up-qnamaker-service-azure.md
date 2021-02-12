---
title: Configurar um serviço de QnA Maker-QnA Maker
description: Antes de criar quaisquer bases de dados de conhecimento do QnA Maker, primeiro você deve configurar um serviço de QnA Maker no Azure. Qualquer pessoa com autorização para criar novos recursos em uma assinatura pode configurar o serviço QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: beb45d0d650b07f6106a3307d2d3a955095ee8b1
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592255"
---
# <a name="manage-qna-maker-resources"></a>Gerenciar QnA Maker recursos

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
    * Como medida de economia de custo, você pode [compartilhar](#configure-qna-maker-to-use-different-cognitive-search-resource) alguns, mas não todos os recursos do Azure criados para o QnA Maker.

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

## <a name="recommended-settings-for-network-isolation"></a>Configurações recomendadas para isolamento de rede

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

1. Proteja o recurso de serviço cognitiva do acesso público [Configurando a rede virtual](../../cognitive-services-virtual-networks.md?tabs=portal).
2. Proteger o serviço de aplicativo (tempo de execução QnA) do acesso público.

   ##### <a name="add-ips-to-app-service-allowlist"></a>Adicionar IPs àlist de permissão do serviço de aplicativo

    * Permitir tráfego somente de IPs de serviço cognitiva. Eles já estão incluídos na marca de serviço `CognitiveServicesManagement` . Isso é necessário para criar APIs (criar/atualizar KB) para invocar o serviço de aplicativo e atualizar o serviço de Azure Search de acordo. Confira [mais informações sobre marcas de serviço.](../../../virtual-network/service-tags-overview.md)
    * Certifique-se também de permitir outros pontos de entrada como o serviço de bot, QnA Maker Portal (pode ser seu corpnet) etc. para acesso à API de previsão "GenerateAnswer".
    * Siga estas etapas para adicionar os intervalos de endereços IP a uma permissão:

      * Baixe [intervalos de IP para todas as marcas de serviço](https://www.microsoft.com/download/details.aspx?id=56519).
      * Selecione os IPs de "CognitiveServicesManagement".
      * Navegue até a seção rede do recurso do serviço de aplicativo e clique na opção "configurar restrição de acesso" para adicionar os IPs a uma permissão.

    Também temos um script automatizado para fazer o mesmo para seu serviço de aplicativo. Você pode encontrar o [script do PowerShell para configurar umalist de permissão](https://github.com/pchoudhari/QnAMakerBackupRestore/blob/master/AddRestrictedIPAzureAppService.ps1) no github. Você precisa inserir a ID da assinatura, o grupo de recursos e o nome real do serviço de aplicativo como parâmetros de script. A execução do script adicionará automaticamente os IPs àlist de permissão do serviço de aplicativo.

    ##### <a name="configure-app-service-environment-to-host-qna-maker-app-service"></a>Configurar Ambiente do Serviço de Aplicativo para hospedar QnA Maker serviço de aplicativo
    O Ambiente do Serviço de Aplicativo (ASE) pode ser usado para hospedar QnA Maker serviço de aplicativo. Siga as etapas abaixo:

    1. Crie um Ambiente do Serviço de Aplicativo e marque-o como "externo". Siga o [tutorial](../../../app-service/environment/create-external-ase.md) para obter instruções.
    2.  Crie um serviço de aplicativo dentro do Ambiente do Serviço de Aplicativo.
        * Verifique a configuração do serviço de aplicativo e adicione ' PrimaryEndpointKey ' como uma configuração de aplicativo. O valor de ' PrimaryEndpointKey ' deve ser definido como " \<app-name\> -PrimaryEndpointKey". O nome do aplicativo é definido na URL do serviço de aplicativo. Por exemplo, se a URL do serviço de aplicativo for "mywebsite.myase.p.azurewebsite.net", o nome do aplicativo será "mysite". Nesse caso, o valor de ' PrimaryEndpointKey ' deve ser definido como "mysite-PrimaryEndpointKey".
        * Crie um serviço de Azure Search.
        * Verifique se Azure Search e as configurações do aplicativo estão configuradas adequadamente. 
          Siga este [tutorial](../reference-app-service.md?tabs=v1#app-service).
    3.  Atualizar o grupo de segurança de rede associado ao Ambiente do Serviço de Aplicativo
        * Atualize as regras de segurança de entrada criadas previamente de acordo com seus requisitos.
        * Adicione uma nova regra de segurança de entrada com a fonte como ' marca de serviço ' e a marca de serviço de origem como ' CognitiveServicesManagement '.
    4.  Crie um QnA Maker instância de serviço cognitiva (Microsoft. Cognitivaservices/accounts) usando Azure Resource Manager, em que QnA Maker ponto de extremidade deve ser definido para o ponto de extremidade do serviço de aplicativo criado acima (https://mywebsite.myase.p.azurewebsite.net).
    
3. Configurando Pesquisa Cognitiva como um ponto de extremidade privado dentro de uma VNET

    Quando uma instância de pesquisa é criada durante a criação de um recurso de QnA Maker, você pode forçar Pesquisa Cognitiva a dar suporte a uma configuração de ponto de extremidade particular criada inteiramente dentro da VNet de um cliente.

    Todos os recursos devem ser criados na mesma região para usar um ponto de extremidade privado.

    * Recurso do QnA Maker
    * novo recurso de Pesquisa Cognitiva
    * novo recurso de rede virtual

    Conclua as seguintes etapas no [portal do Azure](https://portal.azure.com):

    1. Crie um [recurso de QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker).
    1. Crie um novo recurso de Pesquisa Cognitiva com conectividade de ponto de extremidade (dados) definida como _particular_. Crie o recurso na mesma região que o QnA Maker recurso criado na etapa 1. Saiba mais sobre como [criar um recurso de pesquisa cognitiva](../../../search/search-create-service-portal.md)e, em seguida, use este link para ir diretamente para a [página de criação do recurso](https://ms.portal.azure.com/#create/Microsoft.Search).
    1. Crie um novo [recurso de rede virtual](https://ms.portal.azure.com/#create/Microsoft.VirtualNetwork-ARM).
    1. Configure a VNET no recurso do serviço de aplicativo criado na etapa 1 deste procedimento.
        1. Crie uma nova entrada DNS na VNET para o novo recurso Pesquisa Cognitiva criado na etapa 2. para o endereço IP Pesquisa Cognitiva.
    1. [Associe o serviço de aplicativo ao novo recurso de pesquisa cognitiva](#configure-qna-maker-to-use-different-cognitive-search-resource) criado na etapa 2. Em seguida, você pode excluir o recurso de Pesquisa Cognitiva original criado na etapa 1.

    No [portal de QnA Maker](https://www.qnamaker.ai/), crie sua primeira base de dados de conhecimento.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

1. Proteja o recurso de serviço cognitiva do acesso público [Configurando a rede virtual](../../cognitive-services-virtual-networks.md?tabs=portal).
2. [Crie pontos de extremidade privados](../reference-private-endpoint.md) para o recurso de Azure Search.

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

## <a name="configure-azure-resources"></a>Configurar recursos do Azure

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

### <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>Configurar QnA Maker para usar diferentes recursos de Pesquisa Cognitiva

Se você criar um serviço QnA e suas dependências (como pesquisa) por meio do portal, um serviço de pesquisa será criado para você e vinculado ao serviço de QnA Maker. Depois que esses recursos forem criados, você poderá atualizar a configuração do serviço de aplicativo para usar um serviço de pesquisa existente anteriormente e remover o que você acabou de criar.

O recurso de **serviço de aplicativo** do QnA Maker usa o recurso pesquisa cognitiva. Para alterar o recurso de Pesquisa Cognitiva usado pelo QnA Maker, você precisa alterar a configuração no portal do Azure.

1. Obtenha a **chave de administração** e o **nome** do pesquisa cognitiva recurso que você deseja que QnA Maker use.

1. Entre no [portal do Azure](https://portal.azure.com) e localize o serviço de **aplicativo** associado ao recurso de QnA Maker. Ambos têm o mesmo nome.

1. Selecione **configurações** e **configuração**. Isso exibirá todas as configurações existentes para o serviço de aplicativo do QnA Maker.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela de portal do Azure mostrando as definições de configuração do serviço de aplicativo](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. Altere os valores para as seguintes chaves:

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. Para usar as novas configurações, você precisa reiniciar o serviço de aplicativo. Selecione **visão geral** e, em seguida, selecione **reiniciar**.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela de portal do Azure reiniciar o serviço de aplicativo após a alteração das definições de configuração](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

Se você criar um serviço QnA por meio de modelos de Azure Resource Manager, poderá criar todos os recursos e controlar a criação do serviço de aplicativo para usar um serviço de pesquisa existente.

Saiba mais sobre como definir as [configurações do aplicativo](../../../app-service/configure-common.md#configure-app-settings)do serviço de aplicativo.

### <a name="get-the-latest-runtime-updates"></a>Obter as atualizações de tempo de execução mais recentes

O tempo de execução do QnAMaker faz parte da instância do serviço de Azure App que é implantada quando você [cria um serviço QnAMaker](./set-up-qnamaker-service-azure.md) no portal do Azure. Atualizações são feitas periodicamente para o runtime. A instância do serviço de aplicativo QnA Maker está no modo de atualização automática após a versão da extensão do site de abril de 2019 (versão 5 +). Essa atualização foi projetada para cuidar do tempo de inatividade ZERO durante as atualizações.

Você pode verificar a versão atual em https://www.qnamaker.ai/UserSettings . Se sua versão for anterior à versão 5. x, você deverá reiniciar o serviço de aplicativo para aplicar as atualizações mais recentes:

1. Vá para o serviço QnAMaker (grupo de recursos) no [portal do Azure](https://portal.azure.com).

    > [!div class="mx-imgBorder"]
    > ![Grupo de recursos do Azure do QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Selecione a instância do serviço de aplicativo e abra a seção **visão geral** .

    > [!div class="mx-imgBorder"]
    > ![Instância do serviço de aplicativo QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. Reinicie o serviço de aplicativo. O processo de atualização deve ser concluído em alguns segundos. Quaisquer aplicativos dependentes ou bots que usam esse serviço QnAMaker não estarão disponíveis para os usuários finais durante esse período de reinicialização.

    ![Reinicialização da instância do serviço de aplicativo QnAMaker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

### <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>Definir a configuração de ociosidade do serviço de aplicativo para evitar o tempo limite

O serviço de aplicativo, que serve o tempo de execução de previsão de QnA Maker para uma base de dados de conhecimento publicada, tem uma configuração de tempo limite de ociosidade, que assume o tempo limite automaticamente se o serviço estiver ocioso. Por QnA Maker, isso significa que sua API generateAnswer de tempo de execução de previsão ocasionalmente expira após períodos sem nenhum tráfego.

Para manter o aplicativo de ponto de extremidade de previsão carregado mesmo quando não houver tráfego, defina o ocioso como sempre ativo.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Pesquise e selecione o serviço de aplicativo do QnA Maker recurso. Ele terá o mesmo nome que o recurso QnA Maker, mas terá um **tipo** diferente de serviço de aplicativo.
1. Localize **as configurações** e selecione **configuração**.
1. No painel configuração, selecione **configurações gerais** e, em seguida, localizar **AlwaysOn** e selecione **ativado** como o valor.

    > [!div class="mx-imgBorder"]
    > ![No painel configuração, selecione * * configurações gerais * *, em seguida, localize * * Always on * * e selecione * * em * * como o valor.](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. Selecione **salvar** para salvar a configuração.
1. Você será perguntado se deseja reiniciar o aplicativo para usar a nova configuração. Selecione **Continuar**.

Saiba mais sobre como definir as [configurações gerais](../../../app-service/configure-common.md#configure-general-settings)do serviço de aplicativo.

### <a name="business-continuity-with-traffic-manager"></a>Continuidade dos negócios com o Gerenciador de tráfego

O principal objetivo do plano de continuidade de negócios é criar um ponto de extremidade resiliente da base de conhecimento que garantiria a total ausência de tempo de inatividade para o bot ou aplicativo que a estivesse consumindo.

> [!div class="mx-imgBorder"]
> ![Plano de backup do QnA Maker](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

A ideia de alto nível como representada acima é a seguinte:

1. Configurar dois [serviços QnA Maker](set-up-qnamaker-service-azure.md) em paralelo nas [regiões emparelhadas do Azure](../../../best-practices-availability-paired-regions.md).

1. [Faça backup](../../../app-service/manage-backup.md) de seu serviço de aplicativo de QnA Maker primário e [restaure](../../../app-service/web-sites-restore.md) -o na instalação secundária. Isso garantirá que ambas as configurações funcionem com o mesmo nome de host e chaves.

1. Mantenha os índices primário e secundário do Azure Search sincronizados. Use o exemplo do GitHub [aqui](https://github.com/pchoudhari/QnAMakerBackupRestore) para ver como fazer backup-restaurar índices do Azure.

1. Fazer backup do Application Insights usando [exportação contínua](../../../azure-monitor/app/export-telemetry.md).

1. Depois que as pilhas primárias e secundárias forem configuradas, use o [gerenciador de tráfego](../../../traffic-manager/traffic-manager-overview.md) para configurar os dois pontos de extremidade e configurar um método de roteamento.

1. Você precisaria criar uma TLS (segurança de camada de transporte), anteriormente conhecida como protocolo SSL (SSL), certificado para o ponto de extremidade do Gerenciador de tráfego. [Associe o certificado TLS/SSL](../../../app-service/configure-ssl-bindings.md) nos serviços de aplicativo.

1. Por fim, use o ponto de extremidade do gerenciador de tráfego em seu bot ou aplicativo.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

### <a name="configure-qna-maker-managed-preview-service-to-use-different-cognitive-search-resource"></a>Configurar QnA Maker serviço gerenciado (versão prévia) para usar diferentes recursos de Pesquisa Cognitiva

Se você criar um serviço QnA gerenciado (versão prévia) e suas dependências (como pesquisa) por meio do portal, um serviço de pesquisa será criado para você e vinculado ao serviço gerenciado (versão prévia) QnA Maker. Depois que esses recursos forem criados, você poderá atualizar o serviço de pesquisa na guia **configuração** .

1. Vá para o serviço QnA Maker gerenciado (versão prévia) no portal do Azure.

1. Selecione **configuração** e selecione o serviço de pesquisa cognitiva do Azure que você deseja vincular ao serviço QnA Maker gerenciado (versão prévia).

    ![Captura de tela de QnA Maker página de configuração gerenciada (versão prévia)](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-configuration.png)

1. Clique em **Salvar**.

> [!NOTE]
> Se você alterar o serviço de Azure Search associado ao QnA Maker, você perderá o acesso a todas as bases de dados de conhecimento já presentes nela. Certifique-se de exportar as bases de dados de conhecimento existentes antes de alterar o serviço Azure Search.

---

## <a name="delete-azure-resources"></a>Excluir recursos do Azure

Se você excluir qualquer um dos recursos do Azure usados em suas bases de conhecimento do QnA Maker, as bases de conhecimento não funcionarão mais. Antes de excluir qualquer recurso, exporte suas bases de conhecimento da página **Configurações**.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o serviço de [aplicativo](../../../app-service/index.yml) e o [serviço de pesquisa](../../../search/index.yml).

> [!div class="nextstepaction"]
> [Saiba como criar com outras pessoas](../index.yml)
