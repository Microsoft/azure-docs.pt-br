---
title: Migrar seus dados para a conta da API do Cassandra no Azure Cosmos DB – Tutorial
description: Neste tutorial, aprenda a copiar dados do Apache Cassandra para a conta da API do Cassandra no Azure Cosmos DB.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 7c55cbf4b8739a885c499c820a7e68825522ea4e
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449283"
---
# <a name="tutorial-migrate-your-data-to-a-cassandra-api-account"></a>Tutorial: migrar seus dados para uma conta da API do Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Como desenvolvedor, você pode ter cargas de trabalho existentes do Cassandra em execução no local ou na nuvem, mas talvez você queira migrá-las para o Azure. Você pode migrar essas cargas de trabalho para um conta da API do Cassandra no Azure Cosmos DB. Este tutorial fornece instruções sobre as diversas opções disponíveis para migrar dados do Apache Cassandra para a conta da API do Cassandra no Azure Cosmos DB.

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Planejar a migração
> * Pré-requisitos para migração
> * Migrar dados usando o comando `cqlsh` `COPY`
> * Migrar dados usando o Spark

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites-for-migration"></a>Pré-requisitos para migração

* **Estime as necessidades da taxa de transferência:** antes de migrar dados para a conta da API do Cassandra no Azure Cosmos DB Cassandra, você deve estimar as necessidades da taxa de transferência de sua carga de trabalho. Em geral, comece com a taxa de transferência média exigida pelas operações CRUD e, em seguida, inclua a taxa de transferência adicional necessária para o Extract Transform Load ou operações pontiagudas. Você precisa dos seguintes detalhes para planejar a migração: 

  * **Tamanho dos dados existentes ou tamanho estimado dos dados:** Define o tamanho mínimo do banco de dados e o requisito de taxa de transferência. Se você estiver estimando o tamanho dos dados para um novo aplicativo, poderá assumir que os dados são distribuídos uniformemente pelas linhas e estimar o valor multiplicando-os pelo tamanho dos dados. 

  * **Taxa de transferência exigida:** taxa de transferência aproximada das operações de leitura (consulta/obtenção) e gravação (atualização/exclusão/inserção). Esse valor é necessário para calcular as unidades de solicitação exigidas, juntamente com o tamanho dos dados em estado estável.  

  * **O esquema:** conecte-se ao cluster do Cassandra existente por meio de `cqlsh` e exporte o esquema do Cassandra: 

    ```bash
    cqlsh [IP] "-e DESC SCHEMA" > orig_schema.cql
    ```

    Depois de identificar os requisitos da carga de trabalho existente, crie uma conta, um banco de dados e contêineres do Azure Cosmos DB de acordo com os requisitos de taxa de transferência reunidos.  

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

* **Crie tabelas na conta da API do Cassandra:** antes de iniciar a migração de dados, crie previamente todas as tabelas com base no portal do Azure ou no `cqlsh`. Se você estiver migrando para uma conta do Azure Cosmos DB com taxa de transferência de nível de banco de dados, não deixe de fornecer uma chave de partição ao criar os contêineres.

* **Aumentar a taxa de transferência:** a duração da migração de dados depende da quantidade de taxa de transferência provisionada para as tabelas no Azure Cosmos DB. Aumente o rendimento durante a migração. Com a taxa de transferência mais elevada, você pode evitar a limitação de taxa e migrar em menos tempo. Depois de concluir a migração, diminua a taxa de transferência para economizar custos. Também é recomendável que você tenha a conta do Azure Cosmos DB na mesma região que o seu banco de dados de origem. 

* **Habilitar o TLS:** O Azure Cosmos DB tem padrões e requisitos de segurança rígidos. Habilite o TLS ao interagir com sua conta. Quando você usar a CQL com SSH, terá a opção de fornecer informações de TLS.

## <a name="options-to-migrate-data"></a>Opções para migrar dados

Você pode mover dados de cargas de trabalho existentes do Cassandra para o Azure Cosmos DB usando o comando `cqlsh` `COPY` ou o Spark. 

### <a name="migrate-data-by-using-the-cqlsh-copy-command"></a>Migrar dados usando o comando cqlsh COPY

Use o [comando CQL COPY](https://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) para copiar dados locais para a conta da API do Cassandra no Azure Cosmos DB.

1. Receba as informações da string de conexão da sua conta da Cassandra:

   * Entre no [portal do Azure](https://portal.azure.com) e acesse sua conta do Azure Cosmos DB.

   * Abra o painel **Cadeia de Conexão**. Aqui você vê todas as informações necessárias para se conectar à conta da API do Cassandra por meio do `cqlsh`.

1. Entre no `cqlsh` usando as informações de conexão do portal.

1. Use o comando `CQL` `COPY` para copiar dados locais para a conta da API do Cassandra.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

### <a name="migrate-data-by-using-spark"></a>Migrar dados usando o Spark 

Use as etapas a seguir para migrar dados para a conta da API do Cassandra com o Spark:

1. Provisione um [cluster do Azure Databricks](cassandra-spark-databricks.md) ou um [cluster do Azure HDInsight](cassandra-spark-hdinsight.md). 

1. Mova dados para o ponto de extremidade da API do Cassandra de destino usando a [operação de cópia de tabela](cassandra-spark-table-copy-ops.md). 

A migração de dados usando trabalhos do Spark é uma opção recomendada se você tiver dados residindo em um cluster existente nas máquinas virtuais do Azure ou em qualquer outra nuvem. Para fazer isso, você deve configurar o Spark como um intermediário para ingestão única ou regular. Você pode acelerar essa migração usando a conectividade do Azure ExpressRoute entre o ambiente local e o Azure. 

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não forem necessários, você pode excluir o grupo de recursos, a conta do Azure Cosmos DB e todos os recursos relacionados. Para fazer isso, escolha o grupo de recursos da máquina virtual, escolha **Excluir** e, em seguida, confirme o nome do grupo de recursos que será excluído.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como migrar seus dados para uma conta da API do Cassandra no Azure Cosmos DB. Agora você pode aprender mais sobre outros conceitos no Azure Cosmos DB:

> [!div class="nextstepaction"]
> [Níveis ajustáveis de consistência de dados no Azure Cosmos DB](../cosmos-db/consistency-levels.md)




