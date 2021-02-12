---
title: 'Início Rápido: Criar um aplicativo ASP.NET C#'
description: Saiba como executar aplicativos Web no Serviço de Aplicativo do Azure implantando o modelo de aplicativo Web ASP.NET C# padrão por meio do Visual Studio.
ms.assetid: 04a1becf-7756-4d4e-92d8-d9471c263d23
ms.topic: quickstart
ms.date: 11/20/2020
ms.custom: devx-track-csharp, mvc, devcenter, seodec18
ms.openlocfilehash: a4f7ba288bc27d6079deea9caf0ea315a55d0745
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "96004018"
---
# <a name="create-an-aspnet-framework-web-app-in-azure"></a>Criar um aplicativo Web do ASP.NET Framework no Azure

O [Serviço de Aplicativo do Azure](overview.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches.

Este início rápido mostra como implantar seu primeiro aplicativo Web ASP.NET no Serviço de Aplicativo do Azure. Quando terminar, você terá um plano do Serviço de Aplicativo. Você também terá um aplicativo do Serviço de Aplicativo com um aplicativo Web implantado.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, instale o <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> com a carga de trabalho do **ASP.NET e de desenvolvimento para a Web**.

Se você já instalou o Visual Studio 2019:

- Instale as atualizações mais recentes no Visual Studio selecionando **Ajuda** > **Verificar Atualizações**.
- Adicione a carga de trabalho selecionando **Ferramentas** > **Obter Ferramentas e Recursos**.

## <a name="create-an-aspnet-web-app"></a>Criar um aplicativo Web ASP.NET <a name="create-and-publish-the-web-app"></a>

Crie um aplicativo Web ASP.NET seguindo estas etapas:

1. Abra o Visual Studio e selecione **Criar novo projeto**.

2. Em **Criar novo projeto**, localize e escolha **Aplicativo Web ASP .NET (.NET Framework)** e, em seguida, selecione **Avançar**.

3. Em **Configurar seu novo projeto**, dê ao aplicativo o nome _myFirstAzureWebApp_ e, em seguida, selecione, **Criar**.

   ![Configurar seu projeto de aplicativo Web](./media/quickstart-dotnet-framework/configure-web-app-project-framework.png)

4. Você pode implantar qualquer tipo de aplicativo Web ASP.NET no Azure. Para este início rápido, escolha o modelo **MVC**.

5. Verifique se a autenticação está definida como **Sem Autenticação**. Selecione **Criar**.

   ![Criar aplicativo Web ASP.NET](./media/quickstart-dotnet-framework/select-mvc-template-vs2019.png)

6. No menu do Visual Studio, selecione **Depurar** > **Iniciar sem Depuração** para executar o aplicativo Web localmente.

   ![Executar o aplicativo localmente](./media/quickstart-dotnet-framework/local-web-app.png)

## <a name="publish-your-web-app"></a>Publicar seu aplicativo Web <a name="launch-the-publish-wizard"></a>

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **myFirstAzureWebApp** e selecione **Publicar**.

1. Em **Publicar**, selecione **Azure** e clique em **Avançar**.

1. Selecione **Serviço de Aplicativo do Azure (Windows)** e clique em **Avançar**.

   <!-- ![Publish from project overview page](./media/quickstart-dotnet-framework/publish-app-framework-vs2019.png) -->

1. As opções dependem de você já ter entrado no Azure e de ter uma conta do Visual Studio vinculada a uma conta do Azure. Selecione **Adicionar uma conta** ou **Entrar** para entrar em sua assinatura do Azure. Se você já estiver conectado, selecione a conta que deseja.

   ![Entrar no Azure](./media/quickstart-dotnet-framework/sign-in-azure-framework-vs2019.png)

   [!INCLUDE [resource group intro text](../../includes/resource-group.md)]

1. À direita de **Instâncias do Serviço de Aplicativo**, clique em **+** .

   ![Novo aplicativo do Serviço de Aplicativo](./media/quickstart-dotnet-framework/publish-new-app-service.png)

1. Para **Grupo de recursos**, selecione **Novo**.

1. Em **Nome do novo grupo de recursos**, insira *myResourceGroup* e selecione **OK**.

   [!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. Em **Plano de Hospedagem**, selecione **Novo**.

1. Na caixa de diálogo **Plano de Hospedagem**, insira os valores da tabela a seguir e selecione **OK**.

   | Configuração | Valor sugerido | Descrição |
   |-|-|-|
   | Plano de Hospedagem| myAppServicePlan | O nome do plano do Serviço de Aplicativo. |
   | Location | Europa Ocidental | O datacenter onde o aplicativo Web está hospedado. |
   | Tamanho | Grátis | O [Tipo de preço](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) determina os recursos de hospedagem. |

   ![Criar plano de Serviço de Aplicativo](./media/quickstart-dotnet-framework/app-service-plan-framework-vs2019.png)

1. Em **Nome**, insira um nome de aplicativo exclusivo que inclua somente os caracteres válidos `a-z`, `A-Z`, `0-9` e `-`. Você pode aceitar o nome exclusivo gerado automaticamente. A URL do aplicativo Web é `http://<app-name>.azurewebsites.net`, em que `<app-name>` é o nome do aplicativo.

2. Selecione **Criar** para criar os recursos do Azure.

   ![Configurar nome do aplicativo](./media/quickstart-dotnet-framework/web-app-name-framework-vs2019.png)

    Quando o assistente for concluído, os recursos do Azure serão criados para você e você estará pronto para publicar.

3. Escolha **Concluir** para fechar o assistente.

3. Na página **Publicar**, clique em **Publicar**. O Visual Studio compila, empacota e publica o aplicativo no Azure e, em seguida, inicia o aplicativo no navegador padrão.

    ![Aplicativo Web ASP.NET publicado no Azure](./media/quickstart-dotnet-framework/published-azure-web-app.png)

O nome do aplicativo especificado na página **Criar Novo Serviço de Aplicativo** é usado como o prefixo da URL no formato `http://<app-name>.azurewebsites.net`.

**Parabéns!** Seu aplicativo Web ASP.NET está em execução no Serviço de Aplicativo do Azure.

## <a name="update-the-app-and-redeploy"></a>Atualizar o aplicativo e reimplantar

1. No **Gerenciador de Soluções**, em seu projeto, abra **Exibições** > **Home** > **Index.cshtml**.

1. Encontre o rótulo HTML `<div class="jumbotron">` próximo à parte superior e substitua o elemento inteiro pelo seguinte código:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Para implantar novamente no Azure, clique com o botão direito do mouse no projeto **myFirstAzureWebApp**, no **Gerenciador de Soluções** e selecione **Publicar**. Em seguida, selecione **Publicar**.

    Quando a publicação está concluída, o Visual Studio inicia um navegador para a URL do aplicativo Web.

    ![Aplicativo Web ASP.NET atualizado no Azure](./media/quickstart-dotnet-framework/updated-azure-web-app.png)

## <a name="manage-the-azure-app"></a>Gerenciar o aplicativo do Azure

1. Para gerenciar o aplicativo Web, vá para o [portal do Azure](https://portal.azure.com) e procure e selecione **Serviços de Aplicativos**.

   ![Selecionar Serviços de Aplicativos](./media/quickstart-dotnet-framework/app-services.png)

2. Na página **Serviços de Aplicativos**, selecione o nome do seu aplicativo Web.

   ![Navegação no Portal para o aplicativo do Azure](./media/quickstart-dotnet-framework/access-portal-framework-vs2019.png)

   A página Visão Geral do seu aplicativo Web é exibida. Aqui, você pode executar tarefas básicas de gerenciamento como procurar, parar, iniciar, reiniciar e excluir.

   ![Visão geral do Serviço de Aplicativo no portal do Microsoft Azure](./media/quickstart-dotnet-framework/web-app-general-framework-vs2019.png)

   O menu à esquerda fornece páginas diferentes para configurar seu aplicativo.

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [ASP.NET com o Banco de dados SQL](app-service-web-tutorial-dotnet-sqldatabase.md)

> [!div class="nextstepaction"]
> [Configurar o aplicativo ASP.NET](configure-language-dotnet-framework.md)
