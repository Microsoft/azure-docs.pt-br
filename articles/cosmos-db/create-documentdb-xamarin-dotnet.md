---
title: "Azure Cosmos DB: compilar um aplicativo Web com autenticação do Xamarin e do Facebook | Microsoft Docs"
description: "Apresenta um exemplo de código .NET que pode ser usado para conectar e consultar o Azure Cosmos DB"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/10/2017
ms.author: mimig
ms.openlocfilehash: ed1d30be780e0882b8e75c01bc7822c3350f3fee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-build-a-web-app-with-net-xamarin-and-facebook-authentication"></a>Azure Cosmos DB: compilar um aplicativo Web com autenticação do Xamarin, do Facebook e do .NET

O Azure Cosmos DB é o serviço multimodelo de banco de dados distribuído globalmente da Microsoft. É possível criar e consultar rapidamente documentos, chave/valor e bancos de dados do gráfico. Todos se beneficiam de recursos de escala horizontal e distribuição global no núcleo do Azure Cosmos DB. 

Este início rápido demonstra como criar uma conta do Azure Cosmos DB, um banco de dados de documento e uma coleção usando o Portal do Azure. Em seguida, você criará e implantará um aplicativo Web de lista de tarefas pendentes com base na [API do .NET do DocumentDB](documentdb-sdk-dotnet.md), no [Xamarin](https://www.xamarin.com/)e no mecanismo de autorização do Azure Cosmos DB. O aplicativo Web da lista de tarefas pendentes implementa um padrão de dados por usuário que permite que usuários façam o logon usando a Autenticação do Facebook e gerenciem seus próprios itens pendentes.

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não tem o Visual 2017 Studio instalado, poderá baixar e usar o **Visual Studio 2017 Community Edition** [gratuito](https://www.visualstudio.com/downloads/). Verifique se você habilitou o **desenvolvimento do Azure** durante a instalação do Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-a-database-account"></a>Crie uma conta de banco de dados

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>Adicionar uma coleção

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora, vamos clonar um aplicativo de API do DocumentDB do GitHub, defina a cadeia de conexão e execute-o. Você verá como é fácil trabalhar usando dados de forma programática. 

1. Abra uma janela de terminal do Git, como git bash, e `cd` para um diretório de trabalho.  

2. Execute o comando a seguir para clonar o repositório de exemplo. 

    ```bash
    git clone https://github.com/Azure/azure-documentdb-dotnet.git
    ```

3. Em seguida, abra o arquivo DocumentDBTodo.sln na pasta samples/xamarin/UserItems/xamarin.forms no Visual Studio. 

## <a name="review-the-code"></a>Examine o código

O código na pasta Xamarin contém:

* aplicativo Xamarin. O aplicativo armazena itens pendentes do usuário em uma coleção particionada chamada UserItems.
* API do agente de token de recurso. Uma API Web ASP.NET simples para tokens de recurso do Azure Cosmos DB do agente para os usuários conectados do aplicativo. Os tokens de recurso são tokens de acesso de curta duração que fornecem o aplicativo com acesso aos dados do usuário conectado.

O fluxo de dados e de autenticação está ilustrado no diagrama abaixo.

* A coleção UserItems foi criada com a chave de partição '/userid'. Especificar uma chave de partição para uma coleção permite que o Azure Cosmos DB dimensione infinitamente conforme o número de usuários e itens aumenta.
* O aplicativo Xamarin permite que os usuários façam logon com as credenciais do Facebook.
* O aplicativo Xamarin usa o token de acesso do Facebook para autenticar com a API do ResourceTokenBroker
* A API do agente de token de recurso autentica a solicitação, usando o recurso de Autenticação do Serviço de Aplicativo e solicita um token de recurso do Azure Cosmos DB com acesso de leitura/gravação para todos os documentos que compartilham a chave de partição do usuário autenticado.
* O agente de token de recurso retorna o token de recurso ao aplicativo cliente.
* O aplicativo acessa os itens pendentes do usuário por meio do token de recurso.

![Aplicativo de tarefas pendentes com os dados de exemplo](./media/create-documentdb-xamarin-dotnet/tokenbroker.png)
    
## <a name="update-your-connection-string"></a>Atualizar sua cadeia de conexão

Agora, volte ao portal do Azure para obter informações sobre a cadeia de conexão e copiá-las para o aplicativo.

1. No [Portal do Azure](http://portal.azure.com/), na sua conta do Azure Cosmos DB, no painel de navegação esquerdo, clique em **Chaves** e, em seguida, clique em **Chaves de leitura/gravação**. Você usará os botões de cópia no lado direito da tela para copiar o URI e a Chave Primária para o arquivo Web.config na próxima etapa.

    ![Exibir e copiar uma chave de acesso no Portal do Azure, folha Chaves](./media/create-documentdb-xamarin-dotnet/keys.png)

2. No Visual Studio de 2017, abra o arquivo Web.config na pasta azure-documentdb-dotnet/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker. 

3. Copie o valor do URI do portal (usando o botão de cópia) e torne-o o valor de accountUrl no Web.config. 

    `<add key="accountUrl" value="{Azure Cosmos DB account URL}"/>`

4. Em seguida, copie o valor da CHAVE PRIMÁRIA do portal e torne-o o valor de accountKey no Web.congif. 

    `<add key="accountKey" value="{Azure Cosmos DB secret}"/>`

Agora, você atualizou o aplicativo com todas as informações necessárias para se comunicar com o Azure Cosmos DB. 

## <a name="build-and-deploy-the-web-app"></a>Compilar e implantar o aplicativo Web

1. No Portal do Azure, crie um site do Serviço de Aplicativo para hospedar a API do agente de token de recurso.
2. No Portal do Azure, abra a folha de Configurações do Aplicativo do site da API do agente de token de recurso. Preencha as seguintes configurações de aplicativo:

    * accountUrl – A URL da conta do Azure Cosmos DB da guia Chaves da sua conta do Azure Cosmos DB.
    * accountKey – A chave mestra da conta do Azure Cosmos DB da guia Chaves da sua conta do Azure Cosmos DB.
    * databaseId e collectionId do seu banco de dados e coleção criados

3. Publique a solução ResourceTokenBroker no site criado.

4. Abra o projeto do Xamarin e navegue até TodoItemManager.cs. Preencha os valores para accountURL, collectionId, databaseId e também para resourceTokenBrokerURL como a URL HTTPS de base para o site do agente de token de recurso.

5. Conclua o tutorial [Como configurar seu aplicativo do Serviço de Aplicativo para usar o logon do Facebook](../app-service/app-service-mobile-how-to-configure-facebook-authentication.md) a fim de definir a autenticação do Facebook e configurar o site do ResourceTokenBroker.

    Execute o aplicativo Xamarin.

## <a name="review-slas-in-the-azure-portal"></a>Examinar SLAs no Portal do Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se você não continuar usando este aplicativo, exclua todos os recursos criados por esse início rápido no portal do Azure com as seguintes etapas: 

1. No menu à esquerda no portal do Azure, clique em **Grupos de recursos** e depois clique no nome do recurso que acabou de criar. 
2. Em sua página de grupo de recursos, clique em **Excluir**, digite o nome do recurso para excluir na caixa de texto e depois clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a criar uma conta do Azure Cosmos DB, criar uma coleção usando o Data Explorer e compilar e implantar um aplicativo Xamarin. Agora, é possível importar outros dados para sua conta do BD Cosmos. 

> [!div class="nextstepaction"]
> [Importar dados no Azure Cosmos DB](import-data.md)
