---
title: Tutorial do Node.js para a API do DocumentDB para o Azure Cosmos DB | Microsoft Docs
description: Um tutorial do Node.js que cria um Azure Cosmos DB com a API do DocumentDB.
keywords: "tutorial do node.js, banco de dados do nó"
services: cosmos-db
documentationcenter: node.js
author: AndrewHoh
manager: jhubbard
editor: monicar
ms.assetid: 14d52110-1dce-4ac0-9dd9-f936afccd550
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: node
ms.topic: article
ms.date: 08/14/2017
ms.author: anhoh
ms.openlocfilehash: 02e98aadc6a001c7275266d89a196a57bb366b3c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="nodejs-tutorial-use-the-documentdb-api-in-azure-cosmos-db-to-create-a-nodejs-console-application"></a>Tutorial do Node.js: usar a API do DocumentDB no Azure Cosmos DB para criar um aplicativo de console Node.js
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Node.js para MongoDB](mongodb-samples.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 

Bem-vindo ao tutorial do Node.js para o SDK do Node.js do Azure Cosmos DB! Após seguir este tutorial, você terá um aplicativo de console que cria e consulta recursos do Azure Cosmos DB.

Abordaremos:

* Criar e conectar-se a uma conta do Azure Cosmos DB
* Configurando o aplicativo
* Criando um banco de dados do nó
* Criar uma coleção
* Criando documentos JSON
* Consultar a coleção
* Substituição de um documento
* Exclusão de um documento
* Excluindo o banco de dados do nó

Você não tem tempo? Não se preocupe! A solução completa está disponível em [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started). Consulte [Obter a solução completa](#GetSolution) para as instruções rápidas.

Depois de concluir o tutorial do Node.js, use os botões de votação na parte superior e inferior desta página para nos enviar comentários. Se quiser que entremos em contato com você diretamente, inclua seu endereço de email em seu comentário.

Agora vamos começar!

## <a name="prerequisites-for-the-nodejs-tutorial"></a>Pré-requisitos para o tutorial do Node.js
Certifique-se que você tem o seguinte:

* Uma conta ativa do Azure. Se não tiver uma, você poderá se inscrever em uma [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js](https://nodejs.org/) versão v0.10.29 ou superior.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Etapa 1: Criar uma conta de banco de dados do Azure Cosmos DB
Vamos criar uma conta do Azure Cosmos DB. Caso tenha uma conta que queira usar, você poderá pular para [Configurar seu aplicativo Node.js](#SetupNode). Se estiver usando o Emulador do Azure Cosmos DB, siga as etapas em [Emulador do Azure Cosmos DB](local-emulator.md) para configurar o emulador e pule para [Configurar seu aplicativo Node.js](#SetupNode).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupNode"></a>Etapa 2: Configurar seu aplicativo Node.js
1. Abra seu terminal favorito.
2. Localize a pasta ou o diretório em que você deseja salvar o aplicativo do Node.js.
3. Crie dois arquivos JavaScript vazios com os seguintes comandos:
   * Windows:
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```
   * Linux/OS X:
     * ```touch app.js```
     * ```touch config.js```
4. Instale o módulo documentdb via npm. Use o seguinte comando:
   * ```npm install documentdb --save```

Ótimo! Agora que você terminou a configuração, vamos começar a escrever o código.

## <a id="Config"></a>Etapa 3: definir as configurações do aplicativo
Abra ```config.js``` em seu editor de texto favorito.

Então, copie e cole o trecho de código a seguir e defina as propriedades ```config.endpoint``` e ```config.primaryKey``` para o uri do ponto de extremidade do Azure Cosmos DB e a chave primária. Ambas as configurações podem ser encontradas no [portal do Azure](https://portal.azure.com).

![Tutorial do Node.js ‒ captura de tela do portal do Azure mostrando uma conta do Azure Cosmos DB com o hub ATIVO realçado, o botão CHAVES realçado na folha da conta do Azure Cosmos DB e os valores de URI, CHAVE PRIMÁRIA e CHAVE SECUNDÁRIA realçados na folha Chaves ‒ banco de dados Nó][keys]

    // ADD THIS PART TO YOUR CODE
    var config = {}

    config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

Copie e cole ```database id```, ```collection id``` e ```JSON documents``` de seu objeto ```config``` abaixo de onde você configura suas propriedades ```config.endpoint``` e ```config.primaryKey```. Se já tiver dados que gostaria de armazenar em seu banco de dados, você pode usar a [ferramenta de Migração de Dados](import-data.md) do Azure Cosmos DB em vez de adicionar as definições do documento.

    config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

    // ADD THIS PART TO YOUR CODE
    config.database = {
        "id": "FamilyDB"
    };

    config.collection = {
        "id": "FamilyColl"
    };

    config.documents = {
        "Andersen": {
            "id": "Anderson.1",
            "lastName": "Andersen",
            "parents": [{
                "firstName": "Thomas"
            }, {
                    "firstName": "Mary Kay"
                }],
            "children": [{
                "firstName": "Henriette Thaulow",
                "gender": "female",
                "grade": 5,
                "pets": [{
                    "givenName": "Fluffy"
                }]
            }],
            "address": {
                "state": "WA",
                "county": "King",
                "city": "Seattle"
            }
        },
        "Wakefield": {
            "id": "Wakefield.7",
            "parents": [{
                "familyName": "Wakefield",
                "firstName": "Robin"
            }, {
                    "familyName": "Miller",
                    "firstName": "Ben"
                }],
            "children": [{
                "familyName": "Merriam",
                "firstName": "Jesse",
                "gender": "female",
                "grade": 8,
                "pets": [{
                    "givenName": "Goofy"
                }, {
                        "givenName": "Shadow"
                    }]
            }, {
                    "familyName": "Miller",
                    "firstName": "Lisa",
                    "gender": "female",
                    "grade": 1
                }],
            "address": {
                "state": "NY",
                "county": "Manhattan",
                "city": "NY"
            },
            "isRegistered": false
        }
    };


As definições do banco de dados, coleção e documento agirão como ```database id```, ```collection id``` de seu Azure Cosmos DB e dados dos documentos.

Por fim, exporte o objeto ```config```, para que você possa fazer referência a ele no arquivo ```app.js```.

            },
            "isRegistered": false
        }
    };

    // ADD THIS PART TO YOUR CODE
    module.exports = config;

## <a id="Connect"></a>Etapa 4: Conectar-se a uma conta do Azure Cosmos DB
Abra o arquivo ```app.js``` vazio no editor de texto. Copie e cole o código a seguir para importar o módulo ```documentdb``` e o módulo recém-criado ```config```.

    // ADD THIS PART TO YOUR CODE
    "use strict";

    var documentClient = require("documentdb").DocumentClient;
    var config = require("./config");
    var url = require('url');

Copie e cole o código para usar ```config.endpoint``` e ```config.primaryKey``` salvos anteriormente para criar um novo DocumentClient.

    var config = require("./config");
    var url = require('url');

    // ADD THIS PART TO YOUR CODE
    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

Agora que você tem o código para inicializar o cliente do Azure Cosmos DB, veremos como trabalhar com os recursos do Azure Cosmos DB.

## <a name="step-5-create-a-node-database"></a>Etapa 5: criar um banco de dados do Nó
Copie e cole o código a seguir para configurar o status HTTP para Não Encontrado, a url do banco de dados e a url da coleção. Essas urls são como o cliente do Azure Cosmos DB encontrará o banco de dados correto e a coleção.

    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

    // ADD THIS PART TO YOUR CODE
    var HttpStatusCodes = { NOTFOUND: 404 };
    var databaseUrl = `dbs/${config.database.id}`;
    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

Um [banco de dados](documentdb-resources.md#databases) pode ser criado usando a função [createDatabase](https://azure.github.io/azure-documentdb-node/DocumentClient.html) da classe **DocumentClient**. Um banco de dados é o contêiner lógico de armazenamento de documentos particionado em coleções.

Copie e cole a função **getDatabase** para criar seu novo banco de dados no arquivo app.js com a ```id``` especificada no objeto ```config```. A função verificará se o banco de dados com a mesma id ```FamilyRegistry``` já não existe. Se existir, vamos retornar esse banco de dados, em vez de criar um novo.

    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

    // ADD THIS PART TO YOUR CODE
    function getDatabase() {
        console.log(`Getting database:\n${config.database.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDatabase(databaseUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDatabase(config.database, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

Copie e cole o código a seguir onde você configurou a função **getDatabase** para adicionar a função auxiliar **exit** que imprimirá a mensagem de saída e a chamada para a função **getDatabase**.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function exit(message) {
        console.log(message);
        console.log('Press any key to exit');
        process.stdin.setRawMode(true);
        process.stdin.resume();
        process.stdin.on('data', process.exit.bind(process, 0));
    }

    getDatabase()
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

No terminal, localize o arquivo ```app.js``` e execute o comando: ```node app.js```

Parabéns! Você criou um banco de dados do Azure Cosmos DB com êxito.

## <a id="CreateColl"></a>Etapa 6: Criar uma coleção
> [!WARNING]
> **createCollection** criará uma nova coleção, que tem implicações de preços. Para obter mais detalhes, visite a nossa [página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

Uma [coleção](documentdb-resources.md#collections) pode ser criada usando a função [createCollection](https://azure.github.io/azure-documentdb-node/DocumentClient.html) da classe **DocumentClient**. Uma coleção é um contêiner de documentos JSON e uma lógica de aplicativo JavaScript associada.

Copie e cole a função **getCollection** embaixo da função **getDatabase** no arquivo app.js para criar sua nova coleção com a ```id``` especificada no objeto ```config```. Novamente, iremos verificar para assegurar se uma coleção com a mesma id ```FamilyCollection``` já não existe. Se existir, vamos retornar essa coleção, em vez de criar uma nova.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function getCollection() {
        console.log(`Getting collection:\n${config.collection.id}\n`);

        return new Promise((resolve, reject) => {
            client.readCollection(collectionUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createCollection(databaseUrl, config.collection, { offerThroughput: 400 }, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

Copie e cole o código abaixo da chamada para **getDatabase** para executar a função **getCollection**.

    getDatabase()

    // ADD THIS PART TO YOUR CODE
    .then(() => getCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

No terminal, localize o arquivo ```app.js``` e execute o comando: ```node app.js```

Parabéns! Você criou uma coleção do Azure Cosmos DB com êxito.

## <a id="CreateDoc"></a>Etapa 7: Criar um documento
Um [documento](documentdb-resources.md#documents) pode ser criado usando a função [createDocument](https://azure.github.io/azure-documentdb-node/DocumentClient.html) da classe **DocumentClient**. Os documentos são conteúdo JSON (arbitrário) definido pelo usuário. Agora você pode inserir um documento no Azure Cosmos DB.

Copie e cole a função **getFamilyDocument** embaixo da função **getCollection** para criar documentos que contêm os dados JSON salvos no objeto ```config```. Novamente, vamos verificar se um documento com a mesma ID ainda não existe.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function getFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Getting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDocument(documentUrl, { partitionKey: document.district }, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDocument(collectionUrl, document, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    };

Copie e cole o código abaixo da chamada para **getCollection** para executar a função **getFamilyDocument**.

    getDatabase()
    .then(() => getCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

No terminal, localize o arquivo ```app.js``` e execute o comando: ```node app.js```

Parabéns! Você criou um documento do Azure Cosmos DB com êxito.

![Tutorial do Node.js ‒ diagrama que ilustra a relação hierárquica entre a conta, o banco de dados, a coleção e os documentos ‒ banco de dados do Nó](./media/documentdb-nodejs-get-started/node-js-tutorial-cosmos-db-account.png)

## <a id="Query"></a>Etapa 8: Consultar recursos do Azure Cosmos DB
O Azure Cosmos DB tem suporte para [consultas](documentdb-sql-query.md) avançadas de documentos JSON armazenados em cada coleção. O código de exemplo a seguir mostra uma consulta que pode ser executada em documentos em sua coleção.

Copie e cole a função **queryCollection** embaixo da função **getFamilyDocument** no arquivo app.js. O Azure Cosmos DB dá suporte a consultas do tipo SQL, conforme mostrado abaixo. Para saber mais sobre como criar consultas complexas, consulte o [Espaço de Consulta](https://www.documentdb.com/sql/demo) e a [documentação de consulta](documentdb-sql-query.md).

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function queryCollection() {
        console.log(`Querying collection through index:\n${config.collection.id}`);

        return new Promise((resolve, reject) => {
            client.queryDocuments(
                collectionUrl,
                'SELECT VALUE r.children FROM root r WHERE r.lastName = "Andersen"'
            ).toArray((err, results) => {
                if (err) reject(err)
                else {
                    for (var queryResult of results) {
                        let resultString = JSON.stringify(queryResult);
                        console.log(`\tQuery returned ${resultString}`);
                    }
                    console.log();
                    resolve(results);
                }
            });
        });
    };


O diagrama a seguir ilustra como a sintaxe de consulta SQL do Azure Cosmos DB é chamada em relação à coleção que você criou.

![Tutorial do Node.js ‒ Diagrama que ilustra o escopo e o significado da consulta ‒ banco de dados do Nó](./media/documentdb-nodejs-get-started/node-js-tutorial-collection-documents.png)

A palavra-chave [FROM](documentdb-sql-query.md#FromClause) é opcional na consulta, pois as consultas do Azure Cosmos DB já têm o escopo para uma única coleção. Portanto, "FROM Families f" pode ser trocado por "FROM root r" ou qualquer outra variável de nome que você escolher. O Azure Cosmos DB fará com que Families, root ou o nome de variável escolhido por você faça referência à coleção atual, por padrão.

Copie e cole o código abaixo da chamada para **getFamilyDocument** para executar a função **queryCollection**.

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))

    // ADD THIS PART TO YOUR CODE
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

No terminal, localize o arquivo ```app.js``` e execute o comando: ```node app.js```

Parabéns! Você consultou com sucesso documentos do Azure Cosmos DB.

## <a id="ReplaceDocument"></a>Etapa 9: substituir um documento
O Azure Cosmos DB dá suporte à substituição de documentos JSON.

Copie e cole a função **replaceFamilyDocument** sob a função **queryCollection** no arquivo app.js.

                    }
                    console.log();
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function replaceFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Replacing document:\n${document.id}\n`);
        document.children[0].grade = 6;

        return new Promise((resolve, reject) => {
            client.replaceDocument(documentUrl, document, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

Copie e cole o código abaixo da chamada para **queryCollection** para executar a função **replaceDocument**. Além disso, adicione o código para chamar **queryCollection** novamente para verificar se o documento foi alterado com êxito.

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

No terminal, localize o arquivo ```app.js``` e execute o comando: ```node app.js```

Parabéns! Você substituiu um documento do Azure Cosmos DB com sucesso.

## <a id="DeleteDocument"></a>Etapa 10: excluir um documento
O Azure Cosmos DB dá suporte à exclusão de documentos JSON.

Copie e cole a função **deleteFamilyDocument** sob a função **replaceFamilyDocument**.

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function deleteFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Deleting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.deleteDocument(documentUrl, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

Copie e cole o código abaixo da chamada para a segunda **queryCollection** para executar a função **deleteDocument**.

    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

No terminal, localize o arquivo ```app.js``` e execute o comando: ```node app.js```

Parabéns! Você excluiu um documento do Azure Cosmos DB com êxito.

## <a id="DeleteDatabase"></a>Etapa 11: excluir o banco de dados do Nó
Excluir o banco de dados criado removerá o banco de dados e todos os recursos filhos (coleções, documentos, etc.).

Copie e cole a função **cleanup** sob a função **deleteFamilyDocument** para remover o banco de dados e todos os recursos filhos.

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function cleanup() {
        console.log(`Cleaning up by deleting database ${config.database.id}`);

        return new Promise((resolve, reject) => {
            client.deleteDatabase(databaseUrl, (err) => {
                if (err) reject(err)
                else resolve(null);
            });
        });
    }

Copie e cole o código abaixo da chamada para **deleteFamilyDocument** para executar a função **cleanup**.

    .then(() => deleteFamilyDocument(config.documents.Andersen))

    // ADD THIS PART TO YOUR CODE
    .then(() => cleanup())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

## <a id="Run"></a>Etapa 12: Executar todo o aplicativo Node.js!
Ao todo, a sequência para chamar as funções deve ser assim:

    getDatabase()
    .then(() => getCollection())
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    .then(() => cleanup())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

No terminal, localize o arquivo ```app.js``` e execute o comando: ```node app.js```

Você deverá ver a saída do aplicativo iniciado. A saída deve corresponder ao texto de exemplo abaixo.

    Getting database:
    FamilyDB

    Getting collection:
    FamilyColl

    Getting document:
    Anderson.1

    Getting document:
    Wakefield.7

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing document:
    Anderson.1

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleting document:
    Anderson.1

    Cleaning up by deleting database FamilyDB
    Completed successfully
    Press any key to exit

Parabéns! Você concluiu o tutorial do Node.js e tem seu primeiro aplicativo de console do Azure Cosmos DB!

## <a id="GetSolution"></a>Obter a solução completa do tutorial do Node.js
Se você não teve tempo para concluir as etapas neste tutorial ou se apenas deseja baixar o código, poderá obtê-lo no [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started).

Para executar a solução de Introdução que contém todos os exemplos neste artigo, você precisará do seguinte:

* [Conta do Azure Cosmos DB][create-account].
* A solução [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started) disponível no GitHub.

Instale o módulo **documentdb** via npm. Use o seguinte comando:

* ```npm install documentdb --save```

Em seguida, no arquivo ```config.js```, atualize os valores config.endpoint e config.primaryKey, como descrito na [Etapa 3: Definir as configurações do aplicativo](#Config). 

No terminal, localize o arquivo ```app.js``` e execute o comando: ```node app.js```.

Pronto, compile-o e você pode continuar! 

## <a name="next-steps"></a>Próximas etapas
* Deseja obter um exemplo mais complexo do Node.js? Confira [Compilar um aplicativo Web Node.js usando o Azure Cosmos DB](documentdb-nodejs-application.md).
* Saiba como [monitorar uma conta do Azure Cosmos DB](monitor-accounts.md).
* Executar consultas em nosso conjunto de dados de exemplo no [Query Playground](https://www.documentdb.com/sql/demo).
* Saiba mais sobre o modelo de programação na seção Desenvolvimento da [página de documentação do Azure Cosmos DB](https://azure.microsoft.com/documentation/services/documentdb/).

[create-account]: create-documentdb-dotnet.md#create-account
[keys]: media/documentdb-nodejs-get-started/node-js-tutorial-keys.png
