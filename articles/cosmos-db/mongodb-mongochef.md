---
title: Conectar-se à API do Azure Cosmos DB para o MongoDB usando o Studio 3T
titleSuffix: Azure Cosmos DB
description: Saiba como se conectar ao Cosmos DB usando o Studio 3T e a API do Azure Cosmos DB para MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
origin.date: 12/26/2018
ms.date: 03/18/2019
author: rockboyfor
ms.author: v-yeche
ms.custom: seodec18
ms.openlocfilehash: 9b0a6393d2372c831fdc964dee18acbccfd39a77
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61331867"
---
# <a name="connect-to-cosmos-account-using-studio-3t"></a>Conectar-se à conta do Cosmos usando o Studio 3T

Para se conectar a uma conta do Cosmos usando a API do Cosmos DB para MongoDB, é necessário:

* Fazer o download e instalar o [Studio 3T](https://studio3t.com/)
* Obtenha informações da [cadeia de conexão](connect-mongodb-account.md) do Cosmos DB

## <a name="create-the-connection-in-studio-3t"></a>Criar a conexão no Studio 3T
Para adicionar sua conta do Cosmos ao gerenciador de conexões do Studio 3T, execute as seguintes etapas:

1. Recupere as informações de conexão para sua conta do Cosmos configurada com a API do Azure Cosmos DB para MongoDB usando as instruções no artigo [Conectar um aplicativo MongoDB ao Azure Cosmos DB](connect-mongodb-account.md).

    ![Captura de tela da página de cadeia de conexão](./media/mongodb-mongochef/ConnectionStringBlade.png)
2. Clique em **Conectar** para abrir o Gerenciador de Conexões e clique em **Nova Conexão**

    ![Captura de tela do Gerenciador de conexão do Studio 3T](./media/mongodb-mongochef/ConnectionManager.png)
3. Na janela **Nova Conexão**, na guia **Servidor**, insira o HOST (FQDN) da conta do Azure Cosmos DB e a PORTA.

    ![Captura de tela da guia do servidor do Gerenciador de conexão Studio 3T](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. Na janela **Nova Conexão**, na guia **Autenticação**, escolha o Modo de Autenticação **(MONGODB-CR ou SCARM-SHA-1) Básico** e insira NOME DE USUÁRIO e SENHA.  Aceite o banco de dados de autenticação padrão (admin) ou forneça seu próprio valor.

    ![Captura de tela da guia de autenticação do Gerenciador de conexão Studio 3T](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. Na janela **Nova Conexão**, na guia **SSL**, marque a caixa de seleção **Usar protocolo SSL para se conectar** e o botão de opção **Aceitar certificados SSL de servidor autoassinados**.

    ![Captura de tela da guia SSL do Gerenciador de conexão Studio 3T](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Clique no botão **Testar Conectividade** para validar as informações de conexão, clique em **OK** para retornar à janela Nova Conexão e clique em **Salvar**.

    ![Janela de conexão de teste de captura de tela do Studio 3T](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Usar o Studio 3T para criar um banco de dados, coleção e documentos
Para criar um banco de dados, coleta e documentos usando o Studio 3T, execute as seguintes etapas:

1. No **Gerenciador de Conexões**, realce a conexão e clique em **Conectar**.

    ![Captura de tela do Gerenciador de conexão do Studio 3T](./media/mongodb-mongochef/ConnectToAccount.png)
2. Clique com o botão direito do mouse no host e escolha **Adicionar Banco de Dados**.  Forneça um nome de banco de dados e clique em **OK**.

    ![Captura de tela da opção de adicionar banco de dados do Studio 3T](./media/mongodb-mongochef/AddDatabase1.png)
3. Clique com o botão direito do mouse no banco de dados e escolha **Adicionar Coleção**.  Forneça um nome de coleção e clique em **Criar**.

    ![Captura de tela da opção de adicionar coleção do Studio 3T](./media/mongodb-mongochef/AddCollection.png)
4. Clique no item de menu **Coleção** e clique em **Adicionar Documento**.

    ![Captura de tela do item de menu Adicionar documento do Studio 3T](./media/mongodb-mongochef/AddDocument1.png)
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
6. Adicione outro documento, dessa vez com o seguinte conteúdo:

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

    ![Resultados da consulta de captura de tela do Mongochef](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba como [usar o Robo 3T](mongodb-robomongo.md) com uma API do Azure Cosmos DB para o MongoDB.
- Explore [exemplos](mongodb-samples.md) do MongoDB com a API do Azure Cosmos DB para MongoDB.

<!-- Update_Description: update meta properties, wording update -->