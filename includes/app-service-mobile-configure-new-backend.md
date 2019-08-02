---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: a7e543dcad9ad1b016d1244451cd87cda5ad7492
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440214"
---
1. Baixe o guias de início rápido do SDK para as seguintes plataformas de cliente:
    
    [iOS (Objective-C)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS)  
    [iOS (Swift)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS-Swift)  
    [Android (Java)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/android)  
    [Xamarin.iOS](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.iOS)  
    [Xamarin.Android](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.android)  
    [Xamarin.Forms](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.forms)  
    [Cordova](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/cordova)  
    [Windows (C#)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/windows-uwp-cs)  

    > [!NOTE]
    > Se você usar o projeto do iOS você precisa baixar "azuresdk-iOS -\*. zip" partir [versão mais recente do GitHub](https://github.com/Azure/azure-mobile-apps-ios-client/releases/latest). Descompacte e adicione o `MicrosoftAzureMobile.framework` arquivo para a raiz do projeto.
    >

2. Você precisará adicionar uma conexão de banco de dados ou conectar-se para uma conexão existente. Primeiro, determine se você criar um repositório de dados ou usar um existente.

    - **Criar um novo armazenamento de dados**: Se você pretende criar um armazenamento de dados, use o seguinte guia de início rápido:

        [Início Rápido: Guia de Introdução com bancos de dados individuais no banco de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-quickstart-guide)

    - **Fonte de dados existente**: Siga as instruções abaixo se você quiser usar uma conexão de banco de dados existente

        1. Formato de cadeia de Conexão de banco de dados SQL- `Data Source=tcp:{your_SQLServer},{port};Initial Catalog={your_catalogue};User ID={your_username};Password={your_password}`

           **{your_SQLServer}**  Nome do servidor, isso pode ser encontrado na página de visão geral do banco de dados e é normalmente na forma de "server_name.database.windows.net".
            **{port}**  geralmente 1433.
            **{your_catalogue}**  Nome do banco de dados.
            **{your_username}**  Nome de usuário para acessar seu banco de dados.
            **{your_password}**  Senha para acessar seu banco de dados.

            [Saiba mais sobre o formato de cadeia de caracteres de Conexão SQL](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings)

        2. Adicione a cadeia de conexão para seu **aplicativo móvel** no serviço de aplicativo, você pode gerenciar cadeias de caracteres de conexão para seu aplicativo usando o **configuração** opção no menu.

            Para adicionar uma cadeia de caracteres de conexão:

            1. Clique no **configurações do aplicativo** guia.

            2. Clique em **[+] nova cadeia de caracteres de conexão**.

            3. Você precisará fornecer **nome**, **valor** e **tipo** para a cadeia de conexão.

            4. Tipo de **nome** como `MS_TableConnectionString`

            5. Valor deve ser formada na etapa antes da cadeia de conexão.

            6. Se você estiver adicionando uma cadeia de caracteres de conexão para um banco de dados do SQL Azure, escolha **SQLAzure** sob **tipo**.

3. Aplicativos móveis do Azure tem SDKs para back-ends .NET e Node. js.

   - **Back-end do Node.js**
    
     Se você pretende usar o aplicativo de início rápido do Node. js, siga as instruções abaixo.

     1. No portal do Azure, acesse **tabelas fáceis**, você verá esta tela.
      
        ![Tabelas fáceis de nó](./media/app-service-mobile-configure-new-backend/node-easy-tables.png)

     2. Verifique se a cadeia de caracteres de conexão do SQL já foi adicionada a **configuração** guia. Em seguida, marque a caixa de **confirmo que isto substituirá todo o conteúdo do site** e clique no **criar tabela TodoItem** botão.
     
        ![Configuração do nó tabelas fáceis](./media/app-service-mobile-configure-new-backend/node-easy-tables-configuration.png)

     3. Na **tabelas fáceis**, clique no **+ adicionar** botão.
    
        ![Botão de adicionar tabelas fáceis de nó](./media/app-service-mobile-configure-new-backend/node-easy-tables-add.png)

     4. Criar um `TodoItem` tabela com acesso anônimo.
      
        ![Tabelas de nó fácil adicionar tabela](./media/app-service-mobile-configure-new-backend/node-easy-tables-table-add.png)

   - **Back-end do .NET**
    
        Se você pretende usar o aplicativo de início rápido do .NET, siga as instruções abaixo.

        1. Baixe o projeto de servidor .NET dos aplicativos móveis do Azure na [repositório azure-mobile-apps-guias de início rápido](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/backend/dotnet/Quickstart).

        2. Compile o projeto de servidor .NET localmente no Visual Studio.

        3. No Visual Studio, abra o Gerenciador de soluções, clique duas vezes em `ZUMOAPPNAMEService` do projeto, clique em **Publish**, você verá um `Publish to App Service` janela. Se você estiver trabalhando no Mac, check-out de outras maneiras de implantar o aplicativo [aqui](https://docs.microsoft.com/azure/app-service/deploy-local-git).
        
           ![Publicação do Visual studio](./media/app-service-mobile-configure-new-backend/visual-studio-publish.png)

        4. Selecione **serviço de aplicativo** como destino de publicação, em seguida, clique em **selecionar existente**, em seguida, clique no **publicar** botão na parte inferior da janela.

        5. Você precisará entrar no Visual Studio com sua assinatura do Azure pela primeira vez. Selecione o `Subscription`, `Resource Group`e, em seguida, selecione o nome do seu aplicativo. Quando você estiver pronto, clique em **Okey**, isso implantará o projeto do servidor .NET que você tiver localmente para o back-end do serviço de aplicativo. Quando a implantação for concluída, você será redirecionado para `http://{zumoappname}.azurewebsites.net/` no navegador.                   