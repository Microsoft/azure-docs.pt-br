---
title: Usar a Preferência de Leitura do MongoDB com a API Azure Cosmos DB para MongoDB
description: Saiba como usar a Preferência de Leitura do MongoDB com a API Azure Cosmos DB para MongoDB
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 02/26/2018
ms.openlocfilehash: dfb1e0093893fadf22c7a92ef5f351ae8920a977
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60929280"
---
# <a name="how-to-globally-distribute-reads-using-azure-cosmos-dbs-api-for-mongodb"></a>Como configurar distribuir globalmente as leituras usando a API do Azure Cosmos DB para MongoDB

Este artigo mostra como distribuir globalmente operações de leitura usando configurações de [Preferência de Leitura do MongoDB](https://docs.mongodb.com/manual/core/read-preference/) com a API do Azure Cosmos DB para MongoDB.

## <a name="prerequisites"></a>Pré-requisitos 
Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Consulte esse artigo [Início Rápido](tutorial-global-distribution-mongodb.md) para obter instruções sobre como usar o portal do Azure para configurar a conta Cosmos com a distribuição global e, em seguida, conectar a ela.

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Abra uma janela de terminal do Git, como git bash, e `cd` para um diretório de trabalho.  

Execute os comandos a seguir para clonar o repositório de exemplo. Com base na sua plataforma de interesse, use um dos repositórios de exemplo a seguir:

1. [Aplicativo de exemplo .NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)
2. [Aplicativo de exemplo NodeJS]( https://github.com/Azure-Samples/azure-cosmos-db-mongodb-node-geo-readpreference)
3. [Aplicativo de exemplo Mongoose](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-mongoose-geo-readpreference)
4. [Aplicativo de exemplo Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)
5. [Aplicativo de exemplo SpringBoot](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-spring)


```bash
git clone <sample repo url>
```

## <a name="run-the-application"></a>Executar o aplicativo

Dependendo da plataforma utilizada, instale os pacotes necessários e inicie o aplicativo. Para instalar dependências, siga o LEIAME incluído no repositório de aplicativo de exemplo. Por exemplo, no aplicativo de exemplo do NodeJS, use os comandos a seguir para instalar os pacotes necessários e iniciar o aplicativo.

```bash
cd mean
npm install
node index.js
```
O aplicativo tenta conectar-se a uma fonte do MongoDB e falha porque a cadeia de conexão é inválida. Siga as etapas no LEIAME para atualizar a cadeia de conexão`url`. Além disso, atualize `readFromRegion` para uma região de leitura na sua conta Cosmos. As instruções a seguir são do exemplo NodeJS:

```
* Next, substitute the `url`, `readFromRegion` in App.Config with your Cosmos account's values. 
```

Após seguir essas etapas, o aplicativo de exemplo executa e produzir a seguinte saída:

```
connected!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromSecondaryfunc query completed!
```

## <a name="read-using-read-preference-mode"></a>Leia usando o modo Preferência de Leitura

O protocolo MongoDB fornece os seguintes modos de Preferência de Leitura a serem utilizados pelos clientes:

1. PRIMARY
2. PRIMARY_PREFERRED
3. SECONDARY
4. SECONDARY_PREFERRED
5. NEAREST

Consulte a documentação detalhada [Comportamento da Preferência de Leitura do MongoDB](https://docs.mongodb.com/manual/core/read-preference-mechanics/#replica-set-read-preference-behavior) para obter detalhes sobre o comportamento de cada um desses modos de preferência de leitura. No Azure Cosmos DB, primário mapeia para a região de GRAVAÇÃO e o secundário mapeia para região de LEITURA.

Com base em cenários comuns, é usar as seguintes configurações a seguir:

1. Se **leituras de baixa latência** forem necessárias, utilize o modo de preferência de leitura **NEAREST**. Essa configuração direciona as operações de leitura para a região disponível mais próxima. Observe que, se a região mais próxima for a região de GRAVAÇÃO, essas operações serão direcionadas a essa região.
2. Se **alta disponibilidade e distribuição geográfica de leituras** forem necessárias (latência não é uma restrição), utilize o modo de preferência de leitura **SECONDARY PREFERRED**. Esta configuração direciona as operações de leitura para uma região de LEITURA disponível. Se nenhuma região de LEITURA estiver disponível, as solicitações serão direcionados para a região de GRAVAÇÃO.

O snippet de código a seguir do aplicativo de exemplo mostra como configurar a Preferência de Leitura NEAREST no NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.NEAREST});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromNearestfunc query completed!");
  });
```

Da mesma forma, snippet de código abaixo mostra como configurar a Preferência de Leitura SECONDARY_PREFERRED no NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.SECONDARY_PREFERRED});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromSecondaryPreferredfunc query completed!");
  });
```

A Preferência de Leitura também pode ser configurada passando `readPreference` como um parâmetro nas opções de URI da cadeia de conexão:

```javascript
const MongoClient = require('mongodb').MongoClient;
const assert = require('assert');

// Connection URL
const url = 'mongodb://localhost:27017?ssl=true&replicaSet=globaldb&readPreference=nearest';

// Database Name
const dbName = 'myproject';

// Use connect method to connect to the Server
MongoClient.connect(url, function(err, client) {
  console.log("Connected correctly to server");

  const db = client.db(dbName);

  client.close();
});
```

Consulte o repos de aplicativo de exemplo correspondente para outras plataformas, como [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) e [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

## <a name="read-using-tags"></a>Leitura usando marcas

Além do modo de Preferência de Leitura, o protocolo MongoDB permite o uso de marcas para direcionar as operações de leitura. Na API do Azure Cosmos DB para MongoDB, a marca `region` está incluída por padrão como parte da resposta `isMaster`:

```json
"tags": {
         "region": "West US"
      }
```

Portanto, o MongoClient pode usar a marca `region` juntamente com o nome da região para direcionar operações de leitura para regiões específicas. Para contas Cosmos, os nomes das regiões podem ser encontrados no portal do Azure à esquerda em **Configurações->Replicar dados globalmente**. Essa configuração é útil para alcançar **isolamento de leitura** - casos em que o aplicativo cliente deseja direcionar operações de leitura apenas para uma região específica. Essa configuração é ideal para cenários de tipo análise/sem produção, que são executados em segundo plano e não são serviços críticos de produção.

O snippet de código a seguir do aplicativo de exemplo mostra como configurar a Preferência de Leitura com marcas no NodeJS:

```javascript
 var query = {};
  var readcoll = client.db('regionDB').collection('regionTest',{readPreference: new ReadPreference(ReadPreference.SECONDARY_PREFERRED, {"region": "West US"})});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromRegionfunc query completed!");
  });
```

Consulte o repos de aplicativo de exemplo correspondente para outras plataformas, como [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) e [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

Neste artigo, você aprendeu a distribuir globalmente operações de leitura usando a Preferência de Leitura com API do Azure Cosmos DB para MongoDB.

## <a name="clean-up-resources"></a>Limpar recursos

Se você não continuar a usar esse aplicativo, exclua todos os recursos criados por este artigo no Portal do Azure com as seguintes etapas:

1. No menu à esquerda no Portal do Azure, clique em **Grupos de recursos** e depois clique no nome do recurso criado. 
2. Em sua página de grupo de recursos, clique em **Excluir**, digite o nome do recurso para excluir na caixa de texto e depois clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

* [Importar dados do MongoDB no Azure Cosmos DB](mongodb-migrate.md)
* [Configurar o banco de dados distribuído globalmente com a API do Azure Cosmos DB para MongoDB](tutorial-global-distribution-mongodb.md)
* [Desenvolver localmente com o emulador do Azure Cosmos DB](local-emulator.md)
