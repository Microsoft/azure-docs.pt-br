---
title: "Criar conectores personalizados de APIs Web – Aplicativos Lógicos do Azure | Microsoft Docs"
description: Criar conectores personalizados de APIs Web
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 22bf335718721d29933557142b436e75778f8c86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-custom-connectors-from-web-apis"></a>Criar conectores personalizados de APIs Web

Para criar um conector personalizado que você pode usar nos Aplicativos Lógicos do Azure, no Microsoft Flow ou no Microsoft PowerApps, primeiro, crie uma API Web que pode ser hospedada com Aplicativos Web do Azure, autentique com o Azure Active Directory e registre como um conector com Aplicativos Lógicos, Flow ou PowerApps. Este tutorial mostra como executar essas tarefas, criando um aplicativo de API Web ASP.NET. Para padrões que mostram como estruturar o código para os gatilhos e ações de seu conector, consulte [Criar APIs personalizadas que podem ser chamadas de fluxos de trabalho de aplicativo lógico](../logic-apps/logic-apps-create-api-app.md).

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2013 ou posterior](https://www.visualstudio.com/vs/). Este tutorial usa o Visual Studio 2015.

* Código para sua API Web. Se não houver nenhum, tente este tutorial: [Guia de Introdução à API Web ASP.NET 2 (C#)](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).

* Uma assinatura do Azure. Se você não tiver uma assinatura, poderá iniciar com uma [conta gratuita do Azure](https://azure.microsoft.com/free/). Caso contrário, inscreva-se para uma [assinatura de Pagamento conforme o uso](https://azure.microsoft.com/pricing/purchase-options/).

## <a name="create-and-deploy-an-aspnet-web-app-to-azure"></a>Criar e implantar um aplicativo Web do ASP.NET no Azure

Para este tutorial, crie um aplicativo Web ASP.NET do Visual C#. 

1. Abra o Visual Studio e escolha **Arquivo** > **Novo Projeto**.

   1. Expanda **Instalado**, vá para **Modelos**>**Visual C#**>**Web** e selecione **Aplicativos Web ASP.NET**.

   2. Forneça um nome de projeto, o local e o nome da solução para seu aplicativo e escolha **OK**.

   Por exemplo:

   ![Criar um aplicativo Web ASP.NET do Visual C#](./media/custom-connector-build-web-api-app-tutorial/visual-studio-new-project-aspnet-web-app.png)

2. Na caixa **Novo aplicativo Web ASP.NET**, selecione o modelo **API Web**. Se ainda não tiver selecionado, selecione **Host na nuvem**. Selecione **Alterar Autenticação**.

   ![Selecionar modelo de "API Web", "Host na nuvem", "Alterar autenticação"](./media/custom-connector-build-web-api-app-tutorial/visual-studio-web-api-template.png)

3. Selecione **Sem autenticação** e selecione **OK**. Você pode configurar a autenticação mais tarde.

   ![Selecione "Sem autenticação"](./media/custom-connector-build-web-api-app-tutorial/visual-studio-change-authentication.png)

4. Quando a caixa **Novo aplicativo Web ASP.NET** aparecer novamente, escolha **OK**. 

5. Na caixa **Criar Serviço de Aplicativo**, examine as configurações de hospedagem descritas na tabela, faça as alterações desejadas e escolha **Criar**. 

   Um [Plano de Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) representa uma coleção de recursos físicos usados para hospedar aplicativos em sua assinatura do Azure. Saiba mais sobre [Serviço de Aplicativo](../app-service/app-service-value-prop-what-is.md).

   ![Criar Serviço de Aplicativo](./media/custom-connector-build-web-api-app-tutorial/visual-studio-create-app-service.png)

   | Configuração | Valor sugerido | Descrição | 
   | ------- | --------------- | ----------- | 
   | Sua conta corporativa ou de estudante do Azure ou sua conta pessoal da Microsoft | *your-user-account* | Selecione sua conta de usuário. | 
   | **Nome do aplicativo Web** | *custom-web-api-app-name* ou o nome padrão | Insira o nome de seu aplicativo de API da Web que é usado na URL do aplicativo, por exemplo: http://*web-api-app-name*. | 
   | **Assinatura** | *Azure-subscription-name* | Selecione a assinatura do Azure que você deseja usar. | 
   | **Grupo de recursos** | *Azure-resource-group-name* | Selecione um grupo de recursos do Azure existente ou, se ainda não tiver, crie um grupo de recursos. <p>**Observação**: um grupo de recursos do Azure organiza os recursos do Azure em sua assinatura do Azure. | 
   | **Plano do Serviço de Aplicativo** | *App-Service-plan-name* | Selecione um plano do Serviço de Aplicativo existente ou, se ainda não tiver, crie um plano. | 
   |||| 

   Se você criar um plano do Serviço de Aplicativo, especifique estas configurações:

   | Configuração | Valor sugerido | Descrição | 
   | ------- | --------------- | ----------- | 
   | **Localidade** | *deployment-region* | Selecione a região para implantar seu aplicativo. | 
   | **Tamanho** | *App-Service-plan-size* | Selecione o tamanho do plano, que determina o custo e a capacidade de recursos de computação para o plano de serviço. | 
   |||| 

   Para configurar todos os outros recursos exigidos pelo seu aplicativo, escolha **Explorar serviços adicionais do Azure**.

   | Configuração | Valor sugerido | Descrição | 
   | ------- | --------------- | ----------- | 
   | **Tipo de recurso** | *Azure-resource-type* | Selecione e configure todos os recursos adicionais necessários para seu aplicativo. | 
   |||| 

6. Depois que o Visual Studio implantar o projeto, compile o código para seu aplicativo.

## <a name="create-an-openapi-swagger-file-that-describes-your-web-api"></a>Criar um arquivo OpenAPI (Swagger) que descreve a API da Web

Para conectar seu aplicativo de API da Web a Aplicativos Lógicos, é necessário um [arquivo OpenAPI (anteriormente conhecido como Swagger)](http://swagger.io/) que descreve as operações da API. Você pode escrever sua própria definição de OpenAPI para sua API com o [editor online Swagger](http://editor.swagger.io/), mas este tutorial usa uma ferramenta de software livre chamada [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle/blob/master/README.md).

1. Se você ainda não fez isso, instale o pacote do Swashbuckle Nuget em seu projeto do Visual Studio.

   1. No Visual Studio, selecione **Ferramentas** > **Gerenciador de Pacotes do NuGet** > 
   **Console do Gerenciador de Pacotes**.

   2. No **Console de Gerenciador de Pacotes**, vá para o diretório do projeto do seu aplicativo se ainda não estiver lá (execute `Set-Location "project-path"`) e execute este commandlet do PowerShell: 
   
      `Install-Package Swashbuckle`

      Por exemplo:

      ![Console do Gerenciador de Pacotes, instalar Swashbuckle](./media/custom-connector-build-web-api-app-tutorial/visual-studio-package-manager-install-swashbuckle.png)

   > [!TIP]
   > Se você executar o aplicativo após a instalação do Swashbuckle, o Swashbuckle gerará um arquivo OpenAPI nesta URL: 
   >
   > http://*{your-web-api-app-root-URL}*/swagger/docs/v1
   > 
   > O Swashbuckle também gera uma interface do usuário nesta URL: 
   > 
   > http://*{your-web-api-app-root-URL}*/swagger

3. Quando estiver pronto, publique seu aplicativo de API da Web no Azure. Para publicar no Visual Studio, clique com o botão direito do mouse em seu projeto Web no Gerenciador de Soluções, escolha **Publicar...** e siga os prompts.

   > [!IMPORTANT]
   > IDs de operação duplicadas invalidam um documento OpenAPI. Se você usou o modelo C# de exemplo, o modelo *repete essa ID de operação duas vezes*:`Values_Get` 
   > 
   > Para corrigir esse problema, altere uma instância para `Value_Get` e republique.

4. Obtenha o documento do OpenAPI navegando até este local: 

   http://*{your-web-api-app-root-URL}*/swagger/docs/v1

   Você também pode baixar um [documento do OpenAPI de exemplo](https://pwrappssamples.blob.core.windows.net/samples/webAPI.json) deste tutorial. 
   Certifique-se de que removeu os comentários que começam com "//", antes de usar o documento.

5. Salve o conteúdo como um arquivo JSON. Com base no seu navegador, você pode ter que copiar e colar o texto em um arquivo de texto vazio.

## <a name="next-steps"></a>Próximas etapas

* [Configurar a autenticação para conectores personalizados](../logic-apps/custom-connector-azure-active-directory-authentication.md)











