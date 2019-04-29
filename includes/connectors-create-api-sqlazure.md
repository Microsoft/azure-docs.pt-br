---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 05/15/2018
ms.author: estfan
ms.custom: include file
ms.openlocfilehash: da03c5247b8ebe0a3305b08a05d661264497663f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60540681"
---
* Se você estiver usando o banco de dados do SQL do Microsoft Azure, siga as etapas em [Conectar-se ao Banco de Dados SQL do Azure](#connect-azure-sql-db). 

* Se você estiver usando o SQL Server, siga as etapas em [Conectar ao SQL Server](#connect-sql-server).

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Conectar-se ao Banco de Dados SQL do Azure

1. Quando o gatilho SQL ou a ação solicitar informações de conexão, siga estas etapas:

   1. Forneça um nome para a sua conexão.

   2. Selecione seu servidor SQL, em seguida, selecione seu banco de dados. 

      A lista de banco de dados é exibida somente depois que você selecionar o SQL Server.
 
   3. Forneça o nome de usuário e a senha de sua conta do Microsoft Azure.

      Você pode encontrar essas informações no portal do Microsoft Azure em suas propriedades de banco de dados SQL ou em sua cadeia de conexão: 
      
      "ID de usuário = <*seunomedeusuário*>"
      <br>
      "Senha = <*sua senha*>"

   Este exemplo mostra as informações de conexão para um gatilho, mas essas etapas também funcionam para as ações.

   ![Ciar a conexão do Banco de Dados SQL do Microsoft Azure](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)
   <br>
   Asteriscos (*) indicam valores necessários.

   | Propriedade | Value | Detalhes | 
   |----------|-------|---------| 
   | Nome da Conexão | <*my-sql-connection*> | O nome para a sua conexão | 
   | Nome do SQL Server | <*my-sql-server*> | O nome para o seu SQL Server |
   | Nome do Banco de Dados SQL | <*my-sql-database*>  | O nome para o seu banco de dados SQL | 
   | Nome de Usuário | <*my-sql-username*> | Nome de usuário para acessar o banco de dados |
   | Senha | <*my-sql-password*> | Senha para acessar o banco de dados | 
   |||| 

2. Quando terminar, escolha **Criar**.

3. Depois de criar sua conexão, continue com [Adicionar gatilho SQL](#add-sql-trigger) ou [Adicionar ação SQL](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>Conectar-se ao SQL Server

Antes de selecionar seu gateway, verifique se você já [configurou seu gateway de dados](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection). Dessa forma, o gateway aparece na lista de gateways quando você criar sua conexão.

1. Quando o gatilho SQL ou a ação solicitar informações de conexão, siga estas etapas:

   1. No gatilho ou ação, selecione **Conectar por meio do gateway de dados locais** para que sejam exibidas as opções do SQL Server.

   2. Forneça um nome para a sua conexão.

   3. Forneça o endereço para o SQL Server e forneça o nome do banco de dados.
   
      Você pode encontrar essas informações em sua cadeia de conexão: 
      
      * "Server = <*yourServerAddress*>"
      * "Database=<*yourDatabaseName*>"

   4. Forneça o nome de usuário e a senha de sua conta do Microsoft Azure.

      Você pode encontrar essas informações em sua cadeia de conexão: 
      
      * "ID de usuário = <*seunomedeusuário*>"
      * "Senha = <*sua senha*>"

   5. Se o SQL Server usa a autenticação básica ou do Windows, selecione o tipo de autenticação.

   6. Selecione o nome do seu gateway de dados local que você criou anteriormente.
   
      Se o seu gateway não aparecer na lista, verifique se você configurou [corretamente seu gateway](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection).

   Este exemplo mostra as informações de conexão para um gatilho, mas essas etapas também funcionam para as ações.

   ![Criar conexão do SQL Server](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)
   <br>
   Asteriscos (*) indicam valores necessários.

   | Propriedade | Value | Detalhes | 
   |----------|-------|---------| 
   | Conectar-se por meio do gateway local | Selecione essa opção primeiro para as configurações do SQL Server. | | 
   | Nome da Conexão | <*my-sql-connection*> | O nome para a sua conexão | 
   | Nome do SQL Server | <*my-sql-server*> | O nome para o seu SQL Server |
   | Nome do Banco de Dados SQL | <*my-sql-database*>  | O nome para o seu banco de dados SQL |
   | Nome de Usuário | <*my-sql-username*> | Nome de usuário para acessar o banco de dados |
   | Senha | <*my-sql-password*> | Senha para acessar o banco de dados | 
   | Tipo de autenticação | Windows ou Basic | Opcional: O tipo de autenticação usado pelo SQL server | 
   | Gateways | <*my-data-gateway*> | O nome do seu gateway de dados local | 
   |||| 

2. Quando terminar, escolha **Criar**. 

3. Depois de criar sua conexão, continue com [Adicionar gatilho SQL](#add-sql-trigger) ou [Adicionar ação SQL](#add-sql-action).
