---
title: Conectar um aplicativo Mongoose do Node.js ao Azure Cosmos DB
description: Saiba como usar o Mongoose Framework para armazenar e gerenciar dados no Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.custom: seodec18
ms.openlocfilehash: 23275bc639b445b55cafb72c929514541ba00660
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61333135"
---
# <a name="connect-a-nodejs-mongoose-application-to-azure-cosmos-db"></a>Conectar um aplicativo Mongoose do Node.js ao Azure Cosmos DB

Este tutorial demonstra como usar o [Mongoose Framework](https://mongoosejs.com/) ao armazenar o Azure Cosmos DB. Vamos usar a API do Azure Cosmos DB para o MongoDB para este passo a passo. Para aqueles não familiarizados, Mongoose é uma estrutura de modelagem de objeto para o MongoDB no Node.js e fornece uma solução simples, com base em esquema para modelar os dados do aplicativo.

O Azure Cosmos DB é um serviço de banco de dados multimodelo distribuído globalmente da Microsoft. É possível criar e consultar rapidamente bancos de dados de documentos, de chave/valor e de grafo, que se beneficiem das funcionalidades de escala horizontal e distribuição global no núcleo do Cosmos DB.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

[Node.js](https://nodejs.org/) versão v0.10.29 ou superior.

## <a name="create-a-cosmos-account"></a>Criar uma conta do Cosmos

Vamos criar uma conta do Cosmos. Caso já tenha uma conta que queira usar, você poderá pular para Configurar seu aplicativo Node.js. Se estiver usando o Emulador do Azure Cosmos DB, siga as etapas em [Emulador do Azure Cosmos DB](local-emulator.md) para configurar o emulador e pule para Configurar seu aplicativo Node.js.

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="set-up-your-nodejs-application"></a>Configurar seu aplicativo Node.js

>[!Note]
> Se você quiser apenas uma apresentação do código de exemplo em vez da instalação do aplicativo em si, clone o [exemplo](https://github.com/Azure-Samples/Mongoose_CosmosDB) usado para este tutorial e crie seu aplicativo Node.js do Mongoose no Azure Cosmos DB.

1. Para criar um aplicativo Node.js na pasta de sua escolha, execute o seguinte comando em um prompt de comando do nó.

    ```npm init```

    Responda às perguntas e seu projeto estará pronto para começar.

1. Adicione um novo arquivo à pasta e denomine ```index.js```.
1. Instalar os pacotes necessários usando uma das opções ```npm install```:
   * Mongoose: ```npm install mongoose@5 --save```

     > [!Note]
     > A conexão de exemplo do Mongoose abaixo é baseada no Mongoose 5+, que alterou desde as versões anteriores.
    
   * Dotenv (se você gostaria de carregar seus segredos de um arquivo de .env): ```npm install dotenv --save```

     >[!Note]
     > O sinalizador ```--save``` adiciona a dependência ao arquivo package.json.

1. Importe as dependências em seu arquivo index.js.
    ```JavaScript
    var mongoose = require('mongoose');
    var env = require('dotenv').load();    //Use the .env file to load the variables
    ```

1. Adicione sua cadeia de conexão do Cosmos DB e nome do Cosmos DB ao arquivo ```.env```.

    ```JavaScript
    COSMOSDB_CONNSTR=mongodb://{cosmos-user}.documents.azure.com:10255/{dbname}
    COSMODDB_USER=cosmos-user
    COSMOSDB_PASSWORD=cosmos-secret
    ```

1. Conecte-se ao Cosmos DB usando a estrutura de Mongoose adicionando o seguinte código ao final do index.js.
    ```JavaScript
    mongoose.connect(process.env.COSMOSDB_CONNSTR+"?ssl=true&replicaSet=globaldb", {
      auth: {
        user: process.env.COSMODDB_USER,
        password: process.env.COSMOSDB_PASSWORD
      }
    })
    .then(() => console.log('Connection to CosmosDB successful'))
    .catch((err) => console.error(err));
    ```
    >[!Note]
    > Aqui, as variáveis de ambiente são carregadas usando process.env.{variableName} usando o pacote npm 'dotenv'.

    Quando você estiver conectado ao Azure Cosmos DB, agora você pode começar a configuração de modelos de objeto em Mongoose.

## <a name="caveats-to-using-mongoose-with-cosmos-db"></a>Limitações usando o Mongoose com o Azure Cosmos DB

Para cada modelo que você cria, o Mongoose cria uma nova coleção. No entanto, considerando o modelo de cobrança por coleção do Azure Cosmos DB, talvez não seja a maneira mais eficiente, se você tiver vários modelos de objeto que estão escassamente preenchidos.

Este passo a passo aborda ambos modelos. Abordaremos primeiro a apresentação sobre como armazenar um tipo de dados por coleção. Esse é o comportamento de verdadeiro para Mongoose.

Mongoose também tem um conceito chamado [Discriminators](https://mongoosejs.com/docs/discriminators.html). Discriminators são um mecanismo de herança do esquema. Elas permitem que você tenha vários modelos com esquemas sobrepostos sobre a mesma coleção subjacente do MongoDB.

Você pode armazenar vários modelos de dados na mesma coleção e, em seguida, usar uma cláusula de filtro no momento da consulta para baixar os dados necessários.

### <a name="one-collection-per-object-model"></a>Uma coleção por modelo de objeto

O comportamento de Mongoose padrão é criar uma coleção do MongoDB toda vez que você criar um modelo de objeto. Esta seção explora como conseguir isso com a API do Azure Cosmos DB para MongoDB. Esse método é recomendado quando você tiver modelos de objeto com grandes quantidades de dados. Esse é o padrão operacional modelo para Mongoose, portanto, você pode estar familiarizado com isso, se você estiver familiarizado com o Mongoose.

1. Abra seu ```index.js``` novamente.

1. Crie a definição de esquema para 'Família'.

    ```JavaScript
    const Family = mongoose.model('Family', new mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
            gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }));
    ```

1. Crie um objeto para 'Família'.

    ```JavaScript
    const family = new Family({
        lastName: "Volum",
        parents: [
            { firstName: "Thomas" },
            { firstName: "Mary Kay" }
        ],
        children: [
            { firstName: "Ryan", gender: "male", grade: 8 },
            { firstName: "Patrick", gender: "male", grade: 7 }
        ],
        pets: [
            { givenName: "Blackie" }
        ],
        address: { country: "USA", state: "WA", city: "Seattle" }
    });
    ```

1. Por fim, vamos salvar o objeto para o Azure Cosmos DB. Isso cria uma coleção nos bastidores.

    ```JavaScript
    family.save((err, saveFamily) => {
        console.log(JSON.stringify(saveFamily));
    });
    ```

1. Agora, vamos criar outro esquema e objeto. Esta vez, vamos criar um para 'Destinos de Férias' que as famílias podem se interessar.
   1. Assim como a última vez, vamos criar o esquema
      ```JavaScript
      const VacationDestinations = mongoose.model('VacationDestinations', new mongoose.Schema({
       name: String,
       country: String
      }));
      ```

   1. Crie um objeto de exemplo (você pode adicionar vários objetos a este esquema) e salvá-lo.
      ```JavaScript
      const vacaySpot = new VacationDestinations({
       name: "Honolulu",
       country: "USA"
      });

      vacaySpot.save((err, saveVacay) => {
       console.log(JSON.stringify(saveVacay));
      });
      ```

1. Agora, ao ir ao portal do Azure, você observa duas coleções criadas no Azure Cosmos DB.

    ![Banco de dados do nó de - captura de tela do portal do Azure, mostrando uma conta do Azure Cosmos DB, com vários nomes de coleção realçados - tutorial Node. js][multiple-coll]

1. Por fim, vamos ler os dados do Azure Cosmos DB. Como estamos usando o modelo operacional de Mongoose padrão, as leituras são as mesmas que quaisquer outras leituras com o Mongoose.

    ```JavaScript
    Family.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family: " + JSON.stringify(fam)));
    });
    ```

### <a name="using-mongoose-discriminators-to-store-data-in-a-single-collection"></a>Usando o Mongoose Discriminators para armazenar dados em uma única coleção

Nesse método, usamos o [Mongoose Discriminators](https://mongoosejs.com/docs/discriminators.html) para ajudar a otimizar os custos de cada coleção. O Discriminators permite que você defina uma 'chave' de diferenciação, que permite armazenar, diferenciar e filtrar em modelos de objeto diferente.

Aqui, criamos um modelo do objeto base, definimos uma chave de diferenciação e adicionar 'Família' e 'Destinos de Férias' como uma extensão para o modelo de base.

1. Vamos definir a configuração de base e definir a chave discriminadora.

    ```JavaScript
    const baseConfig = {
        discriminatorKey: "_type", //If you've got a lot of different data types, you could also consider setting up a secondary index here.
        collection: "alldata"   //Name of the Common Collection
    };
    ```

1. Em seguida, vamos definir o modelo de objeto comum

    ```JavaScript
    const commonModel = mongoose.model('Common', new mongoose.Schema({}, baseConfig));
    ```

1. Agora, podemos definir o modelo 'Família'. Observe aqui que estamos usando ```commonModel.discriminator``` em vez de ```mongoose.model```. Além disso, também estamos adicionando a configuração de base para o esquema de mongoose. Portanto, aqui, a chave discriminadora é ```FamilyType```.

    ```JavaScript
    const Family_common = commonModel.discriminator('FamilyType', new     mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
           gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }, baseConfig));
    ```

1. Da mesma forma, vamos adicionar outro esquema, esta vez para 'Destinos de Férias'. Aqui, a chave discriminadora é ```VacationDestinationsType```.

    ```JavaScript
    const Vacation_common = commonModel.discriminator('VacationDestinationsType', new mongoose.Schema({
        name: String,
        country: String
    }, baseConfig));
    ```

1. Por fim, vamos criar objetos para o modelo e salvá-lo.
   1. Vamos adicionar o(s) objeto(s) ao modelo 'Família'.
      ```JavaScript
      const family_common = new Family_common({
       lastName: "Volum",
       parents: [
           { firstName: "Thomas" },
           { firstName: "Mary Kay" }
       ],
       children: [
           { firstName: "Ryan", gender: "male", grade: 8 },
           { firstName: "Patrick", gender: "male", grade: 7 }
       ],
       pets: [
           { givenName: "Blackie" }
       ],
       address: { country: "USA", state: "WA", city: "Seattle" }
      });

      family_common.save((err, saveFamily) => {
       console.log("Saved: " + JSON.stringify(saveFamily));
      });
      ```

   1. Em seguida, vamos adicionar o(s) objeto(s) ao modelo 'Destino de Férias' e salvá-lo.
      ```JavaScript
      const vacay_common = new Vacation_common({
       name: "Honolulu",
       country: "USA"
      });

      vacay_common.save((err, saveVacay) => {
       console.log("Saved: " + JSON.stringify(saveVacay));
      });
      ```

1. Agora, se você voltar ao portal do Azure, observe que você tem apenas uma coleção chamada ```alldata``` com dados de 'Família' e 'Destinos de Férias'.

    ![Banco de dados do nó de - captura de tela do portal do Azure, mostrando uma conta do Azure Cosmos DB, com o nome da coleção realçado - tutorial Node. js][alldata]

1. Além disso, observe que cada objeto tem outro atributo chamado de ```__type```, que ajuda você a diferenciar entre os dois modelos de objeto diferente.

1. Por fim, vamos ler os dados do Azure Cosmos DB. Mongoose se encarrega de filtrar os dados com base no modelo. Assim, você não precisa fazer nada diferente durante a leitura de dados. Apenas especifique seu modelo (neste caso, ```Family_common```) e o Mongoose manipula a filtragem na ‘chave discriminadora’.

    ```JavaScript
    Family_common.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family (using discriminator): " + JSON.stringify(fam)));
    });
    ```

Como você pode ver, é fácil trabalhar com Mongoose discriminators. Portanto, se você tiver um aplicativo que usa a estrutura de Mongoose, este tutorial é uma maneira para que você obtenha o seu aplicativo e execute a API do Azure Cosmos para MongoDB sem a necessidade de muitas alterações.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

- Saiba como [usar o Studio 3T](mongodb-mongochef.md) com a API do Azure Cosmos DB para MongoDB.
- Saiba como [usar o Robo 3T](mongodb-robomongo.md) com a API do Azure Cosmos DB para MongoDB.
- Explore [exemplos](mongodb-samples.md) do MongoDB com a API do Azure Cosmos DB para MongoDB.

[alldata]: ./media/mongodb-mongoose/mongo-collections-alldata.png
[multiple-coll]: ./media/mongodb-mongoose/mongo-mutliple-collections.png
