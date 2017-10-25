---
title: Usar o MongoChef no Azure Cosmos DB | Microsoft Docs
description: 'Saiba como usar o MongoChef com uma conta do Azure Cosmos DB: API para MongoDB'
keywords: MongoChef
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: anhoh
ms.openlocfilehash: 54c9799bd646b827f602e2ea2f9a15a4fc853f00
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-mongochef-with-an-azure-cosmos-db-api-for-mongodb-account"></a>Usar o MongoChef com uma conta do Azure Cosmos DB: API para MongoDB

Para se conectar a uma conta do Azure Cosmos DB: API para MongoDB, é necessário:

* Baixar e instalar o [MongoChef](http://3t.io/mongochef)
* Ter as informações de [cadeia de conexão](connect-mongodb-account.md) de sua conta do Azure Cosmos DB: API para MongoDB

## <a name="create-the-connection-in-mongochef"></a>Criar a conexão no MongoChef
Para adicionar sua conta do Azure Cosmos DB: API para MongoDB ao gerenciador de conexões do MongoChef, realize as etapas a seguir.

1. Recupere as informações de conexão do Azure Cosmos DB: API para MongoDB usando as instruções descritas [aqui](connect-mongodb-account.md).

    ![Captura de tela da folha de cadeia de conexão](./media/mongodb-mongochef/ConnectionStringBlade.png)
2. Clique em **Conectar** para abrir o Gerenciador de Conexões e clique em **Nova Conexão**

    ![Captura de tela do gerenciador de conexões do MongoChef](./media/mongodb-mongochef/ConnectionManager.png)
3. Na janela **Nova Conexão**, na guia **Servidor**, insira o HOST (FQDN) da conta do Azure Cosmos DB: API para MongoDB e a PORTA.

    ![Captura de tela da guia servidor do gerenciador de conexões do MongoChef](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. Na janela **Nova Conexão**, na guia **Autenticação**, escolha o Padrão do Modo de Autenticação **(MONGODB-CR ou SCARM-SHA-1)** e insira USERNAME e PASSWORD.  Aceite o banco de dados de autenticação padrão (admin) ou forneça seu próprio valor.

    ![Captura de tela da guia autenticação do gerenciador de conexões do MongoChef](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. Na janela **Nova Conexão**, na guia **SSL**, marque a caixa de seleção **Usar protocolo SSL para se conectar** e o botão de opção **Aceitar certificados SSL de servidor autoassinados**.

    ![Captura de tela da guia SSL do gerenciador de conexões do MongoChef](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Clique no botão **Testar Conectividade** para validar as informações de conexão, clique em **OK** para retornar à janela Nova Conexão e clique em **Salvar**.

    ![Captura de tela da janela testar conectividade do MongoChef](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-mongochef-to-create-a-database-collection-and-documents"></a>Usar o MongoChef para criar um banco de dados, uma coleção e documentos
Para criar um banco de dados, uma coleção e documentos usando o MongoChef, execute as etapas a seguir.

1. No **Gerenciador de Conexões**, realce a conexão e clique em **Conectar**.

    ![Captura de tela do gerenciador de conexões do MongoChef](./media/mongodb-mongochef/ConnectToAccount.png)
2. Clique com o botão direito do mouse e escolha **Adicionar Banco de Dados**.  Forneça um nome de banco de dados e clique em **OK**.

    ![Captura de tela da opção Adicionar Banco de Dados do MongoChef](./media/mongodb-mongochef/AddDatabase1.png)
3. Clique com o botão direito do mouse no banco de dados e escolha **Adicionar Coleção**.  Forneça um nome de coleção e clique em **Criar**.

    ![Captura de tela da opção Adicionar Coleção do MongoChef](./media/mongodb-mongochef/AddCollection.png)
4. Clique no item de menu **Coleção** e clique em **Adicionar Documento**.

    ![Captura de tela do item menu Adicionar Documento do MongoChef](./media/mongodb-mongochef/AddDocument1.png)
5. Na caixa de diálogo Adicionar Documento, cole o conteúdo a seguir e clique em **Adicionar Documento**.

        {
        "_id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
               { "firstName": "Thomas" },
               { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
        }
6. Adicione outro documento, dessa vez com o conteúdo a seguir.

        {
        "_id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                 "givenName": "Jesse",
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            {
                "familyName": "Miller",
                 "givenName": "Lisa",
                 "gender": "female",
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
        }
7. Execute uma consulta de exemplo. Por exemplo, procure famílias com o sobrenome 'Andersen' e retorne os campos de estado e pais.

    ![Captura de tela dos resultados de consulta do MongoChef](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Próximas etapas
* Conheça as [amostras](mongodb-samples.md) do Azure Cosmos DB: API para MongoDB.
