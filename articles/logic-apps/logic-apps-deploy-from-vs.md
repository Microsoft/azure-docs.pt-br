---
title: "Criar, compilar e implantar aplicativos lógicos no Visual Studio – Aplicativo Lógico do Azure | Microsoft Docs"
description: "Crie projetos do Visual Studio para projetar, compilar e implantar Aplicativo Lógico do Azure."
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: e484e5ce-77e9-4fa9-bcbe-f851b4eb42a6
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 2/14/2017
ms.author: LADocs; jehollan
ms.openlocfilehash: e7f5cf483d22e4c60dedbe5176ceb0bc8b2b6e66
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="design-build-and-deploy-azure-logic-apps-in-visual-studio"></a>Criar, compilar e implantar Aplicativo Lógico do Azure no Visual Studio

Embora o [portal do Azure](https://portal.azure.com/) ofereça uma excelente maneira para criar e gerenciar Aplicativos Lógicos do Azure, é possível utilizar o Visual Studio para criar, compilar e implantar os aplicativos lógicos. O Visual Studio fornece ferramentas avançadas como o Designer de Aplicativos Lógicos para criar aplicativos lógicos, configurar modelos de implantação e automação e implantar em qualquer ambiente. 

Para começar a usar os Aplicativo Lógico do Azure, saiba [como criar seu primeiro aplicativo lógico no portal do Azure](logic-apps-create-a-logic-app.md).

## <a name="installation-steps"></a>Etapas de instalação

Para instalar e configurar as ferramentas do Visual Studio para Aplicativos Lógicos do Azure, siga estas etapas.

### <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2017](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) ou Visual Studio 2015
* [SDK mais recente do Azure](https://azure.microsoft.com/downloads/) (2.9.1 ou superior)
* [PowerShell do Azure](https://github.com/Azure/azure-powershell#installation)
* Acesso à Web ao usar o designer incorporado

### <a name="install-visual-studio-tools-for-azure-logic-apps"></a>Instalar ferramentas do Visual Studio para Aplicativos Lógicos do Azure

Após instalar os pré-requisitos:

1. Abra o Visual Studio. No menu **Ferramentas**, selecione **Extensões e Atualizações**.
2. Expanda a categoria **Online** para que você possa pesquisar online.
3. Procure por **Aplicativos Lógicos** até encontrar as **Ferramentas de Aplicativos Lógicos do Azure para Visual Studio**.
4. Clique em **Baixar** para baixar e instalar a extensão.
5. Reinicie o Visual Studio após a instalação.

> [!NOTE]
> Também é possível baixar as [Ferramentas do Aplicativo Lógico do Azure para Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551) e as [Ferramentas do Aplicativo Lógico do Azure para Visual Studio 2015](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio) diretamente no Visual Studio Marketplace.

Após concluir a instalação, use o projeto do Grupo de Recursos do Azure com o Designer de Aplicativos Lógicos.

## <a name="create-your-project"></a>Criar seu projeto

1. No menu **Arquivo**, acesse **Novo** e selecione **Projeto**. Ou para adicionar seu projeto a uma solução existente, acesse **Adicionar** e selecione **Novo Projeto**.

    ![Menu Arquivo](./media/logic-apps-deploy-from-vs/filemenu.png)

2. Na janela **Novo Projeto**, localize **Nuvem** e selecione **Grupo de Recursos do Azure**. Nomeie seu projeto e clique em **OK**.

    ![Adicionar novo projeto](./media/logic-apps-deploy-from-vs/addnewproject.png)

3. Selecione o modelo **Aplicativo Lógico**, que cria um modelo de implantação de aplicativo lógico em branco para você usar. Após selecionar o modelo, clique em **OK**.

    ![Selecione o modelo Aplicativo Lógico](./media/logic-apps-deploy-from-vs/selectazuretemplate1.png)

    Você adicionou o projeto de aplicativo lógico à sua solução. 
    No Gerenciador de Soluções, deve aparecer seu arquivo de implantação.

    ![Arquivo de implantação](./media/logic-apps-deploy-from-vs/deployment.png)

## <a name="create-your-logic-app-with-logic-app-designer"></a>Criar seu aplicativo lógico com o Designer de Aplicativos Lógicos

Quando você tiver um projeto do Grupo de Recursos do Azure que contenha um aplicativo lógico, poderá abrir o Designer de Aplicativo Lógico no Visual Studio para criar seu fluxo de trabalho. 

> [!NOTE]
> O designer requer uma conexão com a Internet para conectores de consulta para dados e propriedades disponíveis. Por exemplo, se você usar o conector do Dynamics CRM Online, o designer de consultará sua instância CRM para mostrar propriedades padrão e personalizadas disponíveis.

1. Clique com o botão direito do mouse no arquivo `<template>.json` e selecione **Abrir com o Designer de Aplicativo Lógico**. (`Ctrl+L`)

2. Escolha a assinatura do Azure, grupo de recursos e local para seu modelo de implantação.

    > [!NOTE]
    > A criação de um aplicativo lógico cria recursos de Conexão da API que consultam propriedades durante o design. O Visual Studio usa seu grupo de recursos selecionado para criar essas conexões durante o design. Para exibir ou modificar as Conexões de API, acesse o portal do Azure e navegue até as **Conexões de API**.

    ![Seletor de assinatura](./media/logic-apps-deploy-from-vs/designer_picker.png)

    O designer usa a definição no arquivo `<template>.json` para renderização.

4. Crie e projete seu aplicativo lógico. Seu modelo de implantação é atualizado com suas alterações.

    ![Designer de Aplicativo Lógico no Visual Studio](./media/logic-apps-deploy-from-vs/designer_in_vs.png)

O Visual Studio adiciona recursos `Microsoft.Web/connections` em seu arquivo de recursos para as conexões que seu aplicativo lógico precisa para funcionar. Essas propriedades de conexão podem ser definidas quando você implanta, e gerenciadas após a implantação nas **Conexões de API** no portal do Azure.

### <a name="switch-to-json-code-view"></a>Alternar para modo de exibição de código JSON

Para exibir a representação JSON de seu aplicativo lógico, selecione a guia **Modo de Exibição de Código** na parte inferior do designer.

Para voltar ao JSON de recursos completos, clique com o botão direito do mouse no arquivo `<template>.json` e selecione **Abrir**.

### <a name="add-references-for-dependent-resources-to-visual-studio-deployment-templates"></a>Adicione referências de recursos dependentes para modelos de implantação do Visual Studio

Quando desejar que seu aplicativo lógico faça referência a recursos dependentes, use [Funções de modelo do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions) em seu modelo de implantação de aplicativo lógico. Por exemplo, talvez você queira seu aplicativo lógico para fazer referência a uma conta de integração ou da Função do Azure que você deseja implantar junto com seu aplicativo lógico. Siga estas diretrizes sobre como usar parâmetros em seu modelo de implantação para que o Designer de Aplicativo Lógico seja renderizado corretamente. 

Você pode usar parâmetros do aplicativo lógico nesses tipos de gatilhos e ações:

*   Fluxo de trabalho filho
*   Aplicativo de funções
*   Chamada APIM
*   URL do tempo de execução da conexão de API
*   Caminho de conexão da API

E você pode utilizar funções de modelo, como parâmetros, variáveis, resourceId, concat, etc. Por exemplo, eis como você pode substituir a ID de recurso da Função do Azure:

```
"parameters":{
    "functionName": {
        "type":"string",
        "minLength":1,
        "defaultValue":"<FunctionName>"
    }
},
```

E onde os parâmetros seriam utilizados:

```
"MyFunction": {
    "type": "Function",
    "inputs": {
        "body":{},
        "function":{
            "id":"[resourceid('Microsoft.Web/sites/functions','functionApp',parameters('functionName'))]"
        }
    },
    "runAfter":{}
}
```
Como outro exemplo, é possível parametrizar a operação de mensagem de envio do Barramento de Serviço:

```
"Send_message": {
    "type": "ApiConnection",
        "inputs": {
            "host": {
                "connection": {
                    "name": "@parameters('$connections')['servicebus']['connectionId']"
                }
            },
            "method": "post",
            "path": "[concat('/@{encodeURIComponent(''', parameters('queueuname'), ''')}/messages')]",
            "body": {
                "ContentData": "@{base64(triggerBody())}"
            },
            "queries": {
                "systemProperties": "None"
            }
        },
        "runAfter": {}
    }
```
> [!NOTE] 
> host.runtimeUrl é opcional e pode ser removido do seu modelo, se houver.
> 


> [!NOTE] 
> Para o Designer de Aplicativo Lógico funcionar quando ao usar parâmetros, você deve fornecer valores padrão, por exemplo:
> 
> ```
> "parameters": {
>     "IntegrationAccount": {
>     "type":"string",
>     "minLength":1,
>     "defaultValue":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Logic/integrationAccounts/<integrationAccountName>"
>     }
> },
> ```

### <a name="save-your-logic-app"></a>Salve seu aplicativo lógico

Para salvar seu aplicativo lógico a qualquer momento, acesse **Arquivo** > **Salvar**. (`Ctrl+S`) 

Se seu aplicativo lógico tiver erros quando você salvar seu aplicativo, eles aparecerão na janela **Saídas** do Visual Studio.

## <a name="deploy-your-logic-app-from-visual-studio"></a>Implantar seu aplicativo lógico por meio do Visual Studio

Após configurar seu aplicativo, você pode implantar diretamente do Visual Studio em apenas algumas etapas. 

1. No Gerenciador de Soluções, clique com o botão direito do mouse em seu projeto e acesse **Implantar** > **Nova Implantação...**

    ![Nova implantação](./media/logic-apps-deploy-from-vs/newdeployment.png)

2. Quando for solicitado, entre em suas assinatura do Azure. 

3. Agora você deve selecionar os detalhes do grupo de recursos onde você deseja implantar seu aplicativo lógico. Quando terminar, selecione **Implantar**.

    > [!NOTE]
    > Verifique se você selecionou o modelo e o arquivo de parâmetros correto para o grupo de recursos. Por exemplo, se você deseja implantar em um ambiente de produção, escolha o arquivo de parâmetros de produção.

    ![Implantar no grupo de recursos](./media/logic-apps-deploy-from-vs/deploytoresourcegroup.png)

    O status da implantação é exibido na janela **Saída**. 
    Talvez você precise selecionar **Provisionamento do Azure** na lista **Mostrar saída de**.

    ![Saída Status da implantação](./media/logic-apps-deploy-from-vs/output.png)

No futuro, você pode editar seu aplicativo lógico no controle do código-fonte e usar o Visual Studio para implantar novas versões.

> [!NOTE]
> Se você alterar a definição no portal do Azure diretamente, essas alterações serão sobrescritas ao implantar a partir do Visual Studio posteriormente. 

## <a name="add-your-logic-app-to-an-existing-resource-group-project"></a>Adicione seu aplicativo lógico a um projeto existente do Grupo de Recursos

Se você tiver um projeto existente do Grupo de Recursos, é possível adicionar seu aplicativo lógico a esse projeto na janela Estrutura de tópicos JSON. Você também pode adicionar outro aplicativo lógico junto com o aplicativo criado anteriormente.

1. Abra o arquivo `<template>.json` .

2. Para abrir a janela de Estrutura de tópicos JSON, acesse **Exibir** > **Outras Janelas** > **Estrutura de tópicos JSON**.

3. Para adicionar um recurso ao arquivo de modelo, clique em **Adicionar Recurso** na parte superior da janela Estrutura de tópicos JSON. Ou, na janela Estrutura de tópicos JSON, clique com o botão direito do mouse em **recursos**e selecione **Adicionar Novo Recurso**.

    ![Janela Estrutura de tópicos JSON](./media/logic-apps-deploy-from-vs/jsonoutline.png)
    
4. Na caixa de diálogo **Adicionar Recurso**, localize e selecione **Aplicativo Lógico**. Nomeie seu aplicativo lógico e escolha **Adicionar**.

    ![Adicionar recurso](./media/logic-apps-deploy-from-vs/addresource.png)

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar aplicativos lógicos com o Visual Studio Cloud Explorer](logic-apps-manage-from-vs.md)
* [Veja exemplos e cenários comuns](logic-apps-examples-and-scenarios.md)
* [Saiba como automatizar processos de negócios com os Aplicativos Lógicos do Azure](http://channel9.msdn.com/Events/Build/2016/T694)
* [Saiba como integrar seus sistemas com os Aplicativos Lógicos do Azure](http://channel9.msdn.com/Events/Build/2016/P462)
