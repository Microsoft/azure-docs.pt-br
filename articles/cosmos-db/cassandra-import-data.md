---
title: Migrar seus dados para a conta da API do Cassandra no Azure Cosmos DB – Tutorial
description: Neste tutorial, você aprenderá a usar o comando CQL Copy e o Spark para copiar dados do Apache Cassandra para a conta da API do Cassandra no Azure Cosmos DB
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: bd2d27addb6860e49ac12eb36d8b625b8bf92001
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100398"
---
# <a name="tutorial-migrate-your-data-to-cassandra-api-account-in-azure-cosmos-db"></a>Tutorial: Migrar seus dados para a conta da API do Cassandra no Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Como desenvolvedor, você pode ter cargas de trabalho existentes do Cassandra em execução no local ou na nuvem, mas talvez você queira migrá-las para o Azure. Você pode migrar essas cargas de trabalho para um conta da API do Cassandra no Azure Cosmos DB. Este tutorial fornece instruções sobre as diversas opções disponíveis para migrar dados do Apache Cassandra para a conta da API do Cassandra no Azure Cosmos DB.

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Planejar a migração
> * Pré-requisitos para migração
> * Migrar dados usando o comando cqlsh COPY
> * Migrar dados usando Spark

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites-for-migration"></a>Pré-requisitos para migração

* **Estime as necessidades da taxa de transferência:** antes de migrar dados para a conta da API do Cassandra no Azure Cosmos DB Cassandra, você deve estimar as necessidades da taxa de transferência de sua carga de trabalho. Em geral, é recomendado começar com a taxa de transferência média exigida pelas operações CRUD e, em seguida, incluir a taxa de transferência adicional necessária para o Extract Transform Load (ETL) ou operações pontiagudas. Você precisa dos seguintes detalhes para planejar a migração: 

  * **Tamanho dos dados existentes ou tamanho estimado dos dados:** Define o tamanho mínimo do banco de dados e o requisito de taxa de transferência. Se você estiver estimando o tamanho dos dados para um novo aplicativo, poderá assumir que os dados são distribuídos uniformemente pelas linhas e estimar o valor multiplicando-os pelo tamanho dos dados. 

  * **Taxa de transferência necessária:** Taxa de transferência aproximada de leitura (consulta/obtenção) e de gravação (atualização/exclusão/inserção). Esse valor é necessário para calcular as unidades de solicitação necessárias junto com o tamanho dos dados de estado estável.  

  * **O esquema:** Conecte-se ao cluster do Cassandra existente por meio do cqlsh e exporte o esquema do Cassandra: 

    ```bash
    cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
    ```

    Depois de identificar os requisitos de sua carga de trabalho existente, você deve criar uma conta, banco de dados e contêineres do Azure Cosmos de acordo com os requisitos de taxa de transferência reunidos.  

  * **Determine a cobrança do RU para uma operação:** é possível determinar as RUs usando o SDK compatível com a API do Cassandra. Este exemplo mostra a versão do .NET da obtenção de custos de RU.

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
      {
         byte[] valueInBytes = customPayload[key];
         double value = Encoding.UTF8.GetString(valueInBytes);
         Console.WriteLine($"CustomPayload:  {key}: {value}");
      }
    ```

* **Aloque a taxa de transferência necessária:** o Azure Cosmos DB pode dimensionar automaticamente o armazenamento e a taxa de transferência conforme seus requisitos aumentam. Você pode estimar suas necessidades de taxa de transferência usando a [calculadora da unidade de solicitação do Azure Cosmos DB](https://www.documentdb.com/capacityplanner). 

* **Criar tabelas na conta de API do Cassandra:** antes de começar a migrar dados, crie previamente todas as suas tabelas no portal do Azure ou no cqlsh. Se você estiver migrando para uma conta do Azure Cosmos com produtividade de nível de banco de dados, não deixe de fornecer uma chave de partição ao criar os contêineres do Azure Cosmos.

* **Aumentar a taxa de transferência:** a duração da migração de dados depende da quantidade de taxa de transferência provisionada para as tabelas no Azure Cosmos DB. Aumente o rendimento durante a migração. Com a taxa de transferência mais elevada, você pode evitar a limitação de taxa e migrar em menos tempo. Depois de concluir a migração, diminua a taxa de transferência para economizar custos. Também é recomendável ter uma conta do Azure Cosmos na mesma região que seu banco de dados de origem. 

* **Habilitar o TLS:** O Azure Cosmos DB tem padrões e requisitos de segurança rígidos. Habilite o TLS ao interagir com sua conta. Quando você usar a CQL com SSH, terá a opção de fornecer informações de TLS.

## <a name="options-to-migrate-data"></a>Opções para migrar dados

Você pode mover dados de cargas de trabalho existentes do Cassandra para o Azure Cosmos DB usando as seguintes opções:

* [Usando o comando cqlsh COPY](#migrate-data-using-cqlsh-copy-command)  
* [Usando o Spark](#migrate-data-using-spark) 

## <a name="migrate-data-using-cqlsh-copy-command"></a>Migrar dados usando o comando cqlsh COPY

O [comando CQL COPY](https://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) é usado para copiar dados locais para a conta da API do Cassandra no Azure Cosmos DB. Use as etapas a seguir para copiar dados:

1. Receba as informações da string de conexão da sua conta da Cassandra:

   * Faça login no [Portal do Azure](https://portal.azure.com) e navegue até sua conta do Azure Cosmos.

   * Abra o painel **Connection String** que contém todas as informações necessárias para se conectar à sua conta da Cassandra API a partir do cqlsh.

2. Entre no cqlsh usando as informações de conexão do portal.

3. Use o comando CQL COPY para copiar dados locais para a conta da API do Cassandra.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="migrate-data-using-spark"></a>Migrar dados usando Spark 

Use as etapas a seguir para migrar dados para a conta da API do Cassandra com o Spark:

- Provisione um [cluster do Azure Databricks](cassandra-spark-databricks.md) ou um [cluster do HDInsight](cassandra-spark-hdinsight.md) 

- Mova dados para o ponto de extremidade da API do Cassandra de destino usando a [operação de cópia de tabela](cassandra-spark-table-copy-ops.md) 

A migração de dados usando trabalhos do Spark é uma opção recomendada se você tiver dados residindo em um cluster existente nas máquinas virtuais do Azure ou em qualquer outra nuvem. Esta opção requer que o Spark seja configurado como intermediária por um período ou ingestão regular. Você pode acelerar essa migração usando a conectividade do Microsoft Azure ExpressRoute entre o local e o Azure. 

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não forem necessários, você pode excluir o grupo de recursos, a conta do Azure Cosmos e todos os recursos relacionados. Para fazer isso, escolha o grupo de recursos da máquina virtual, escolha **Excluir** e, em seguida, confirme o nome do grupo de recursos que será excluído.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como migrar seus dados para a conta da API do Cassandra no Azure Cosmos DB. Agora você pode seguir para o artigo seguinte para aprender sobre outros conceitos do Azure Cosmos DB:

> [!div class="nextstepaction"]
> [Níveis ajustáveis de consistência de dados no Azure Cosmos DB](../cosmos-db/consistency-levels.md)


