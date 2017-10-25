---
title: "Serviço Conectado do Visual Studio para o Azure Cosmos DB"
description: "Permite que os desenvolvedores conectem suas contas do Azure Cosmos DB facilmente e gerenciem recursos por meio dos Serviços Conectados do Visual Studio"
services: cosmos-db
documentationcenter: 
author: jejiang
manager: DJ
+tags: cosmos-db
editor: Jenny Jiang
ms.assetid: 
ms.service: cosmos-db
ms.custom: Azure Cosmos DB
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/19/2017
ms.author: jejiang
ms.openlocfilehash: de0c83e4c99894f98de18eb089ce11cdf5c70f2e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-visual-studio-connected-service-preview"></a>Azure Cosmos DB: Serviço Conectado do Visual Studio (versão prévia)

Os Serviços Conectados do Visual Studio permitem que desenvolvedores conectem suas contas do Azure Cosmos DB e gerenciem seus recursos.

Você também pode usar o Data Explorer no Serviço Conectado para criar procedimentos armazenados, UDFs e gatilhos para executar a lógica de negócios do servidor. O Data Explorer expõe todo o acesso a dados internos via programação disponível nas APIs, mas oferece acesso fácil aos dados.

## <a name="prerequisites"></a>Pré-requisitos

Verifique se você tem o seguinte:

* Uma conta ativa do Azure. Se não tiver uma, você poderá se inscrever em uma [conta gratuita](https://azure.microsoft.com/free/). 
* Uma conta do Azure Cosmos DB. Se você ainda não tiver uma, siga as etapas em [Criando uma conta do Azure Cosmos DB](create-documentdb-dotnet.md) para criar uma no portal do Azure ou confira [Criar uma conta do Azure Cosmos DB na ferramenta Serviço Conectado](#Create-an-Azure-Cosmo-DB-account-in-Connected-Service-tool). 
* Se você quiser usar um ambiente local para fins de desenvolvimento, poderá usar o [Emulador do Azure Cosmos DB](local-emulator.md). O ambiente emula o serviço do Azure Cosmos DB.
* [Visual Studio](http://www.visualstudio.com/).
* Os bits mais recentes do Serviço Conectado do Azure Cosmos DB. Você pode baixar o Serviço Conectado do Azure Cosmos DB do marketplace do Visual Studio conforme mostrado na captura de tela a seguir. Abra o **Visual Studio** em seu computador. No menu **Ferramentas**, selecione **Extensões e atualização...**, e escolha **Online** / **Visual Studio Marketplace**. Digite **cosmosdb** para pesquisar os bits.

    Você também pode instalar o Serviço Conectado do Azure Cosmos DB do [Visual Studio Marketplace](https://go.microsoft.com/fwlink/?linkid=858709).

    ![Captura de tela do download de bits do Serviço Conectado.png](./media/connected-service/connected-service-downloadbits.png) 

## <a id="SetupVS"></a>Configurar sua solução do Visual Studio
1. Abra o **Visual Studio** no seu computador.
2. No menu **Arquivo**, selecione **Novo** e depois **Projeto**.
3. Na caixa de diálogo **Novo Projeto**, selecione **Visual C#** / **Aplicativo de Console (.NET Framework)** ou **Aplicativo WPF (.NET Framework)**, nomeie o projeto e clique em **OK**.

    ![Captura de tela da janela Novo Projeto](./media/connected-service/connected-service-new-project.png)
    
## <a name="add-connected-service-and-add-account"></a>Adicionar Serviço Conectado e adicionar conta
1. No Gerenciador de Soluções, clique com o botão direito do mouse no nó do projeto e selecione **Adicionar** / **Serviço Conectado**. Ou clique no menu **Projeto** e selecione **Adicionar Serviço Conectado**.

    ![Captura de tela da janela Adicionar Serviço Conectado](./media/connected-service/connected-service-add-connectedservice-rightclick.png)
2. Na página do serviço conectado, clique em **Serviços Conectados** / **Azure Cosmos DB** para abrir a página **Azure Cosmos DB**.

    ![Captura de tela da janela Azure Cosmos DB](./media/connected-service/connected-service-choose-azure-cosmosdb.png)
3. Clique na seta para baixo para entrar pela primeira vez ou adicionar uma conta. Depois de entrar, todas as contas do Azure Cosmos DB são mostradas na área em branco. Escolha uma conta do Azure Cosmos DB para adicionar ao seu projeto.

    ![Captura de tela da janela de entrada e conta do banco de dados listada](./media/connected-service/connected-service-add-db-account.png)
4. Depois de adicionar uma conta do Azure Cosmos DB, uma pasta de serviço conectado da conta do Azure Cosmos DB é adicionada ao projeto. Você pode adicionar mais de uma conta do Azure Cosmos DB repetindo as etapas 1 a 3.

    ![Captura de tela da janela da pasta do serviço conectado](./media/connected-service/connected-service-add-connectedservice-folder.png)

5. Depois de adicionar um serviço conectado do Azure Cosmos DB, modifique seu projeto para habilitar o acesso ao Azure Cosmos DB de uma das seguintes maneiras:

    * Alguns pacotes do NuGet exigidos pelo cliente do Azure Cosmos DB estão instalados. Você pode vê-los em seu arquivo de configuração de pacotes. 

        ![A nova configuração de pacotes do Azure Cosmos DB](./media/connected-service/connected-service-packages-config.png)   
    
    * O URI de conexão do Azure Cosmos DB e a chave são adicionados ao arquivo de configuração de projeto, nesse caso, App.config. 

        ![A nova configuração do aplicativo Azure Cosmos DB](./media/connected-service/connected-service-app-config.png) 

## <a name="open-azure-cosmos-db-explorer"></a>Abrir o Azure Cosmos DB Explorer
1. Clique com o botão direito do mouse no nó do projeto e selecione **Abrir Cosmos DB Explorer...** .

    ![Captura de tela da janela tentando abrir o Data Explorer](./media/connected-service/connected-service-right-click-open-data-exporer.png)
2. Na página **Escolha uma conta do Azure Cosmos DB**, clique na lista suspensa para selecionar uma conta do Azure Cosmos DB.

    ![Captura de tela da janela Conta do Serviço Conectado adicionada](./media/connected-service/connected-service-open-explorer.png)
3. Clique em **Abrir** para exibir a janela do Data Explorer.

## <a id="Create-an-Azure-Cosmo-DB-account-in-Connected-Service-tool"></a>Criar uma conta do Azure Cosmos DB na ferramenta Serviço Conectado
1. Na página do serviço conectado, na parte inferior esquerda do painel, clique em **Criar uma nova conta do Azure Cosmos DB** para abrir a página **Criar conta do Azure Cosmos DB**.

    ![Captura de tela da janela de ponto de entrada Criar conta do Azure Cosmos DB aberta](./media/connected-service/connected-service-click-new-db-account.png)
2. Na página **Criar conta do Cosmos DB**, especifique a configuração desejada para essa conta do Azure Cosmos DB.

    Preencha os campos na página **Criar conta do Azure Cosmos DB** usando as informações na captura de tela a seguir como referência. 
 
    ![A nova página do Azure Cosmos DB](./media/connected-service/connected-service-create-new-account.png)        
3. Clique em **Criar** para criar a conta.

## <a name="use-data-explorer"></a>Usar o Data Explorer

Depois de abrir o Data Explorer, você pode fazer o seguinte:
* Criar e excluir bancos de dados
* Criar e excluir coleções
* Criar, excluir e filtrar documentos
* Criar e excluir procedimentos armazenados
* Criar e excluir gatilhos e funções definidas pelo usuário para executar lógica de negócios do lado do servidor. 

![A nova página do Azure Cosmos DB](./media/connected-service/connected-service-dataexplorerui.png)

## <a name="demo"></a>Demonstração

Assista ao vídeo a seguir para saber como usar o Serviço Conectado do Azure Cosmos DB no Visual Studio: [Usar o Serviço Conectado do Azure Cosmos DB no Visual Studio](https://go.microsoft.com/fwlink/?linkid=858711)

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu o seguinte:

> [!div class="checklist"]
> * Criar uma conta do Azure Cosmos DB
> * Adicionar Serviço Conectado e adicionar conta
> * Abrir o Azure Cosmos DB Explorer
> * Usar o Data Explorer

Agora que você tem os Serviços Conectados em funcionamento na sua conta do Azure Cosmos DB, vá para um dos tutoriais para começar a desenvolver sua solução:

* [Desenvolver com a API do DocumentDB em .NET](tutorial-develop-documentdb-dotnet.md).
* [Azure Cosmos DB: tutorial de introdução à API do DocumentDB](documentdb-get-started.md).
* Quer executar testes de desempenho e escala com o Azure Cosmos DB? Confira [Teste de desempenho e escala com o Azure Cosmos DB](performance-testing.md).
* Saiba como [Monitorar uma conta do Azure Cosmos DB](monitor-accounts.md).

