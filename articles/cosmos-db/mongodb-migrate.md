---
title: Usar mongoimport e o mongorestore com a API para MongoDB do Azure Cosmos DB | Microsoft Docs
description: Saiba como usar mongoimport e mongorestore para importar dados para uma conta da API para MongoDB
keywords: mongoimport, mongorestore
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
ms.date: 06/12/2017
ms.author: anhoh
ms.custom: mvc
ms.openlocfilehash: 1555f13c3ea88b61be0ea240b51218b83f6f9724
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-import-mongodb-data"></a>Azure Cosmos DB: Importar dados do MongoDB 

Para migrar dados do MongoDB para uma conta do Azure Cosmos DB para uso com a API para MongoDB será necessário:

* Baixar o *mongoimport.exe* ou o *mongorestore.exe* do [Centro de Download do MongoDB](https://www.mongodb.com/download-center).
* Obter a [cadeia de conexão da API para MongoDB](connect-mongodb-account.md).

Se você estiver importando dados do MongoDB e se pretende usá-los com o Azure Cosmos DB, deverá usar a [Ferramenta de Migração de Dados](import-data.md) para importar os dados.

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Recuperando sua cadeia de conexão
> * Importar dados do MongoDB utilizando mongoimport
> * Importar dados do MongoDB utilizando mongorestore

## <a name="prerequisites"></a>Pré-requisitos

* Aumentar a taxa de transferência: a duração da sua migração de dados depende da taxa de transferência que você configurar para suas coleções. Certifique-se de aumentar a taxa de transferência para migrações de dados maiores. Depois de concluir a migração, diminua a taxa de transferência para economizar custos. Para obter mais informações sobre como aumentar a produtividade no [portal do Azure](https://portal.azure.com), consulte [Níveis de desempenho e tipos de preço no Azure Cosmos DB](performance-levels.md).

* Habilitar SSL: o Azure Cosmos DB tem padrões e requisitos de segurança rígidos. Certifique-se de habilitar o SSL ao interagir com sua conta. Os procedimentos no restante do artigo incluem como habilitar o SSL para mongoimport e mongorestore.

## <a name="find-your-connection-string-information-host-port-username-and-password"></a>Localizar informações de cadeia de conexão (host, porta, nome de usuário e senha)

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, clique na entrada **Azure Cosmos DB**.
2. No painel **Assinaturas**, selecione o nome da sua conta.
3. No folha **Cadeia de Conexão**, clique em **Cadeia de Conexão**.  
O painel direito contém todas as informações necessárias para conectar-se à sua conta com êxito.

    ![Folha Cadeia de conexão](./media/mongodb-migrate/ConnectionStringBlade.png)

## <a name="import-data-to-the-api-for-mongodb-by-using-mongoimport"></a>Importe dados para a API para MongoDB utilizando mongoimport

Para importar dados para sua conta Azure Cosmos DB, utilize o seguinte modelo. Preencha *host*, *nome de usuário* e *senha* com os valores que são específicos para a sua conta.  

Modelo:

    mongoimport.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates --type json --file C:\sample.json

Exemplo:  

    mongoimport.exe --host anhoh-host.documents.azure.com:10255 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates --db sampleDB --collection sampleColl --type json --file C:\Users\anhoh\Desktop\*.json

## <a name="import-data-to-the-api-for-mongodb-by-using-mongorestore"></a>Importe dados para a API para MongoDB utiliznado mongorestore

Para restaurar dados para sua conta da API para MongoDB, use o modelo a seguir para executar a importação. Preencha *host*, *nome de usuário* e *senha* com os valores que são específicos para a sua conta.

Modelo:

    mongorestore.exe --host <your_hostname>:10255 -u <your_username> -p <your_password> --db <your_database> --collection <your_collection> --ssl --sslAllowInvalidCertificates <path_to_backup>

Exemplo:

    mongorestore.exe --host anhoh-host.documents.azure.com:10255 -u anhoh-host -p tkvaVkp4Nnaoirnouenrgisuner2435qwefBH0z256Na24frio34LNQasfaefarfernoimczciqisAXw== --ssl --sslAllowInvalidCertificates ./dumps/dump-2016-12-07
    
## <a name="guide-for-a-successful-migration"></a>Guia para uma migração bem-sucedida

1. Crie previamente e dimensione suas coleções:
        
    * Por padrão, o Azure Cosmos DB prevê uma nova coleção MongoDB com 1.000 unidades de solicitação (RUs). Antes de iniciar a migração utilizando mongoimport, mongorestore ou mongomirror, crie previamente todas as coleções a partir do [portal do Azure](https://portal.azure.com) ou de ferramentas e drivers do MongoDB. Se a coleção for superior a 10 GB, certifique-se de criar uma [coleção fragmentada/particionada ](partition-data.md) com uma chave de fragmentos apropriada.

    * A partir do [portal do Azure](https://portal.azure.com), aumente o rendimento das coleções de 1.000 RUs para uma coleção de partição única e 2.500 RUs para uma coleção fragmentada apenas para a migração. Com a taxa de transferência mais elevada, você pode evitar limitação e migrar em menos tempo. Com cobrança horária no Azure Cosmos DB, você poderá reduzir a taxa de transferência imediatamente após a migração para economizar custos.

2. Calcule a taxa de RU aproximada para gravação de um documento único:

    a. Conecte-se ao seu banco de dados do MongoDB do Azure Cosmos DB a partir do MongoDB Shell. É possível localizar as instruções em [Conectar um aplicativo do MongoDB ao Azure Cosmos DB](connect-mongodb-account.md).
    
    b. Execute um comando de inserção de exemplo utilizando um dos documentos de exemplo do MongoDB Shell:
    
        ```db.coll.insert({ "playerId": "a067ff", "hashedid": "bb0091", "countryCode": "hk" })```
        
    c. Execute ```db.runCommand({getLastRequestStatistics: 1})``` e uma resposta semelhante à seguinte será exibida:
     
        ```
        globaldb:PRIMARY> db.runCommand({getLastRequestStatistics: 1})
        {
            "_t": "GetRequestStatisticsResponse",
            "ok": 1,
            "CommandName": "insert",
            "RequestCharge": 10,
            "RequestDurationInMilliSeconds": NumberLong(50)
        }
        ```
        
    d. Tome nota da cobrança da solicitação.
    
3. Determine a latência d computador para o serviço de nuvem do Azure Cosmos DB:
    
    a. Habilite o log detalhado do MongoDB Shell utilizando esse comando: ```setVerboseShell(true)```
    
    b. Execute uma consulta simples no banco de dados: ```db.coll.find().limit(1)```. Uma resposta semelhante à seguinte será exibida:

        ```
        Fetched 1 record(s) in 100(ms)
        ```
        
4. Remova o documento inserido antes da migração para garantir que não haja documentos duplicados. É possível remover documentos utilizando esse comando: ```db.coll.remove({})```

5. Calcule os valores aproximados de *batchSize* e *numInsertionWorkers*:

    * Para  *batchSize* , divida as RUs provisionadas totais pelas RUs consumidas a partir do seu documento único, gravado na etapa 3.
    
    * Se o *batchSize* calculado for <= 24, utilize esse número como seu valor de *batchSize*.
    
    * Se o *batchSize* calculado for > 24, defina o valor de *batchSize* para 24.
    
    * Para *numInsertionWorkers*, use esta equação: *numInsertionWorkers =  (taxa de transferência provisionada * latência em segundos) / (tamanho do lote * RUs consumidas para uma gravação única)*.
        
    |Propriedade|Valor|
    |--------|-----|
    |batchSize| 24 |
    |RUs provisionadas | 10000 |
    |Latência | 0,100 s |
    |RU cobrada por 1 doc gravado | 10 RUs |
    |numInsertionWorkers | ? |
    
    *numInsertionWorkers = (10000 RUs x 0,1 s) / (24 x 10 RUs) = 4.1666*

6. Execute o comando de migração final:

   ```
   mongoimport.exe --host anhoh-mongodb.documents.azure.com:10255 -u anhoh-mongodb -p wzRJCyjtLPNuhm53yTwaefawuiefhbauwebhfuabweifbiauweb2YVdl2ZFNZNv8IU89LqFVm5U0bw== --ssl --sslAllowInvalidCertificates --jsonArray --db dabasename --collection collectionName --file "C:\sample.json" --numInsertionWorkers 4 --batchSize 24
   ```

## <a name="next-steps"></a>Próximas etapas

Agora, você pode seguir para o próximo tutorial e saber como consultar dados do MongoDB usando o Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Como consultar dados do MongoDB?](../cosmos-db/tutorial-query-mongodb.md)
