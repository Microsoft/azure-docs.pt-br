---
title: Migrar dados do Cassandra para o API do Cassandra de Azure Cosmos DB usando o Blitzz
description: Saiba como migrar dados do Apache Cassandra para Azure Cosmos DB API do Cassandra usando o Blitzz.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: c26d21e74e9808fe65890b7f4eba31ee742552a4
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339976"
---
# <a name="migrate-data-from-cassandra-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>Migrar dados do Cassandra para a conta de API do Cassandra Azure Cosmos DB usando o Blitzz
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

API do Cassandra no Azure Cosmos DB se tornou uma ótima opção para cargas de trabalho corporativas em execução no Apache Cassandra por vários motivos, como: 

* **Sem sobrecarga de gerenciamento e monitoramento:** Ele elimina a sobrecarga de gerenciar e monitorar uma infinidade de configurações em arquivos do sistema operacional, JVM e YAML e suas interações.

* **Economia de custos significativa:** Você pode economizar custos com Azure Cosmos DB, o que inclui o custo da VM, a largura de banda e todas as licenças aplicáveis. Além disso, você não precisa gerenciar os custos de data centers, servidores, armazenamento de SSD, rede e eletricidade. 

* **Capacidade de usar código e ferramentas existentes:** O Azure Cosmos DB fornece compatibilidade de nível de protocolo de conexão com SDKs e ferramentas Cassandra existentes. Essa compatibilidade garante que você pode usar a base de código existente com a API do Cassandra do Azure Cosmos DB com alterações triviais.

Há várias maneiras de migrar cargas de trabalho de banco de dados de uma plataforma para outra. O [Blitzz](https://www.blitzz.io) é uma ferramenta que oferece uma maneira segura e confiável de executar sem migração de tempo de inatividade de uma variedade de bancos de dados para Azure Cosmos DB. Este artigo descreve as etapas necessárias para migrar dados do Apache Cassandra para Azure Cosmos DB API do Cassandra usando o Blitzz.

## <a name="benefits-using-blitzz-for-migration"></a>Benefícios ao usar o Blitzz para migração

A solução de migração do Blitzz segue uma abordagem passo a passo para migrar cargas de trabalho operacionais complexas. A seguir estão alguns dos principais aspectos do plano de migração sem tempo de inatividade do Blitzz:

* Ele oferece migração automática de lógica de negócios (tabelas, índices, exibições) do banco de dados Apache Cassandra para Azure Cosmos DB. Você não precisa criar esquemas manualmente.

* O Blitzz oferece replicação de banco de dados paralela e de alto volume. Ele permite que as plataformas de origem e de destino estejam em sincronia durante a migração usando uma técnica chamada de captura de dados de alteração (CDC). Usando o CDC, o Blitzz Obtém continuamente um fluxo de alterações do banco de dados de origem (Apache Cassandra) e a aplica ao banco de dados de destino (Azure Cosmos DB).

* Ele é tolerante a falhas e fornece uma entrega de dados exatamente uma vez, mesmo durante uma falha de hardware ou software no sistema.

* Ele protege os dados durante o trânsito usando uma variedade de metodologias de segurança como TLS, criptografia.

## <a name="steps-to-migrate-data"></a>Etapas para migrar dados

Esta seção descreve as etapas necessárias para configurar o Blitzz e migrar dados do Apache Cassandra para Azure Cosmos DB.

1. No computador em que você planeja instalar o Blitzz Replicant, adicione um certificado de segurança. Esse certificado é exigido pelo Blitzz Replicant para estabelecer uma conexão TLS com a conta de Azure Cosmos DB especificada. Você pode adicionar o certificado com as seguintes etapas:

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. Você pode obter a instalação do Blitzz e os arquivos binários solicitando uma demonstração no [site do Blitzz](https://www.blitzz.io). Como alternativa, você também pode enviar um [email](mailto:success@blitzz.io) para a equipe.

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/blitzz-replicant-download.png" alt-text="Download da ferramenta Blitzz Replicant":::

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/replicant-files.png" alt-text="Arquivos Blitzz Replicant":::

1. No terminal da CLI, defina a configuração do banco de dados de origem. Abra o arquivo de configuração usando o **`vi conf/conn/cassandra.yml`** comando e adicione uma lista separada por vírgulas de endereços IP dos nós Cassandra, número da porta, nome de usuário, senha e quaisquer outros detalhes necessários. Veja a seguir um exemplo de conteúdo no arquivo de configuração:

   ```bash
   type: CASSANDRA
  
   host: 172.17.0.2
   port: 9042

   username: 'cassandra'
   password: 'cassandra'

   max-connections: 30

   ```

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/open-connection-editor-cassandra.png" alt-text="Abrir o editor de conexão do Cassandra":::

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/cassandra-connection-configuration.png" alt-text="Configuração de conexão do Cassandra":::

   Depois de preencher os detalhes de configuração, salve e feche o arquivo.

1. Opcionalmente, você pode configurar o arquivo de filtro do banco de dados de origem. O arquivo de filtro especifica quais esquemas ou tabelas migrar. Abra o arquivo de configuração usando o **`vi filter/cassandra_filter.yml`** comando e insira os seguintes detalhes de configuração:

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```

   Depois de preencher os detalhes do filtro de banco de dados, salve e feche o arquivo.

1. Em seguida, você definirá a configuração do banco de dados de destino. Antes de definir a configuração, [crie uma conta de API do Cassandra Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account) e, em seguida, crie um keyspace e uma tabela para armazenar os dados migrados. Como você está migrando do Apache Cassandra para o API do Cassandra no Azure Cosmos DB, você pode usar a mesma chave de partição que usou com o Apache Cassandra.

1. Antes de migrar os dados, aumente a taxa de transferência do contêiner para o valor necessário para que seu aplicativo migre rapidamente. Por exemplo, você pode aumentar a taxa de transferência para 100000 RUs. Dimensionar a taxa de transferência antes de iniciar a migração ajudará você a migrar seus dados em menos tempo.

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/scale-throughput.png" alt-text="Dimensionar o contêiner Cosmos do Azure em todo o":::

   Diminua a taxa de transferência após a conclusão da migração. Com base na quantidade de dados armazenados e RUs necessários para cada operação, você pode estimar a taxa de transferência necessária após a migração de dados. Para saber mais sobre como estimar o RUs necessário, confira [provisionar taxa de transferência em contêineres e bancos de dados](set-throughput.md) e [estimar ru/s usando os artigos Azure Cosmos DB Capacity Planner](estimate-ru-with-capacity-planner.md) .

1. Obtenha o **ponto de contato, a porta, o nome de usuário** e a **senha primária** da sua conta do Azure Cosmos no painel de **cadeia de conexão** . Você usará esses valores no arquivo de configuração.

1. No terminal da CLI, defina a configuração do banco de dados de destino. Abra o arquivo de configuração usando o **`vi conf/conn/cosmosdb.yml`** comando e adicione uma lista separada por vírgulas de URI de host, número da porta, nome de usuário, senha e outros parâmetros necessários. O exemplo a seguir mostra o conteúdo do arquivo de configuração:

   ```bash
   type: COSMOSDB

   host: '<Azure Cosmos account’s Contact point>'
   port: 10350

   username: 'blitzzdemo'
   password: '<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   ```

1. Em seguida, migre os dados usando Blitzz. Você pode executar o Blizz Replicant no modo de **instantâneo** ou **completo** :

   * **Modo completo** – nesse modo, o Replicant continua a ser executado após a migração e escuta as alterações no sistema Apache Cassandra de origem. Se detectar quaisquer alterações, elas serão replicadas na conta de destino do Azure Cosmos em tempo real.

   * **Modo de instantâneo** – nesse modo, você pode executar a migração de esquema e replicação de dados de uso único. A replicação em tempo real não tem suporte com essa opção.

   Usando os dois modos acima, a migração pode ser executada sem nenhum tempo de inatividade. 

1. Para migrar dados, no terminal da CLI do Blitzz Replicant, execute o seguinte comando:

   ```bash
   ./bin/replicant full conf/conn/cassandra.yaml conf/conn/cosmosdb.yaml --filter filter/cassandra_filter.yaml --replace-existing
   ```

   A interface do usuário do amreplicant mostra o progresso da replicação. Depois que a operação de migração e instantâneo do esquema for concluída, o progresso mostrará 100%. Depois que a migração for concluída, você poderá validar os dados no banco de dado de destino do Azure Cosmos.

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/cassandra-data-migration-output.png" alt-text="Saída de migração de dados Cassandra":::


1. Como você usou o modo completo para a migração, você pode executar operações como inserir, atualizar ou excluir dados no banco de dado Apache Cassandra de origem. Posteriormente, valide que eles são replicados em tempo real no banco de dados Cosmos do Azure de destino. Após a migração, certifique-se de diminuir a taxa de transferência configurada para o contêiner Cosmos do Azure.

1. Você pode parar o Replicant de qualquer ponto e reiniciá-lo com **a opção--retomar** . A replicação continua do ponto em que foi interrompida sem comprometer a consistência dos dados. O comando a seguir mostra como usar a opção retomar.

   ```bash
   ./bin/replicant full conf/conn/cassandra.yaml conf/conn/cosmosdb.yaml --filter filter/cassandra_filter.yaml --replace-existing --resume
   ```

Para saber mais sobre a migração de dados para o destino, migração em tempo real, consulte a [demonstração do Blitzz Replicant](https://www.youtube.com/watch?v=fsUhF9LUZmM).

## <a name="next-steps"></a>Próximas etapas

* [Provisionar a taxa de transferência para contêineres e bancos de dados](set-throughput.md) 
* [Práticas recomendadas da chave de partição](partitioning-overview.md#choose-partitionkey)
* [Estimar ru/s usando os artigos Azure Cosmos DB Capacity Planner](estimate-ru-with-capacity-planner.md)
