---
title: Replicação lógica e decodificação lógica-banco de dados do Azure para PostgreSQL-servidor flexível
description: Saiba mais sobre como usar a replicação lógica e a decodificação lógica no banco de dados do Azure para PostgreSQL-servidor flexível
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: b6689220873aaeb65337ba480e346e5d2c8020ce
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91707856"
---
# <a name="logical-replication-and-logical-decoding-in-azure-database-for-postgresql---flexible-server"></a>Replicação lógica e decodificação lógica no banco de dados do Azure para PostgreSQL-servidor flexível

> [!IMPORTANT]
> O Servidor Flexível do Banco de Dados do Azure para PostgreSQL está em versão prévia

Os recursos de replicação lógica e decodificação lógica do PostgreSQL têm suporte no banco de dados do Azure para PostgreSQL – servidor flexível, para postgres versão 11.

## <a name="comparing-logical-replication-and-logical-decoding"></a>Comparando a replicação lógica e decodificação lógica
A replicação lógica e a decodificação lógica têm várias semelhanças. Ambos
* permitir que você Replique dados fora do postgres
* usar o [log write-ahead (WAL)](https://www.postgresql.org/docs/current/wal.html) como a origem das alterações
* Use [Slots de replicação lógica](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS) para enviar dados. Um slot representa um fluxo de alterações.
* usar a propriedade de [identidade da réplica](https://www.postgresql.org/docs/current/sql-altertable.html#SQL-CREATETABLE-REPLICA-IDENTITY) de uma tabela para determinar quais alterações podem ser enviadas
* Não replicar alterações DDL


As duas tecnologias têm suas diferenças: replicação lógica 
* permite especificar uma tabela ou conjunto de tabelas a serem replicadas
* replica dados entre instâncias do PostgreSQL

Decodificação lógica 
* extrai alterações em todas as tabelas em um banco de dados 
* Não é possível enviar dados diretamente entre instâncias do PostgreSQL


## <a name="pre-requisites-for-logical-replication-and-logical-decoding"></a>Pré-requisitos para a replicação lógica e decodificação lógica

1. Defina o parâmetro de servidor `wal_level` como `logical` .
2. Reinicie o servidor para aplicar a `wal_level` alteração.
3. Confirme se a instância do PostgreSQL permite o tráfego de rede do seu recurso de conexão.
4. Conceda permissões de replicação de usuário administrador.
   ```SQL
   ALTER ROLE <adminname> WITH REPLICATION;
   ```


## <a name="using-logical-replication-and-logical-decoding"></a>Usando a replicação lógica e decodificação lógica

### <a name="logical-replication"></a>Replicação lógica
A replicação lógica usa os termos ' publicador ' e ' Assinante '. 
* O Publicador é o banco de dados PostgreSQL **do** qual você está enviando um dado. 
* O assinante é o banco de dados PostgreSQL **para** o qual você está enviando um dado.

Aqui está um código de exemplo que você pode usar para experimentar a replicação lógica.

1. Conecte-se ao banco de dados Publicador. Crie uma tabela e adicione alguns dados.
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   INSERT INTO basic(name) VALUES ('apple');
   INSERT INTO basic(name) VALUES ('banana');
   ```

2. Crie uma publicação para a tabela.
   ```SQL
   CREATE PUBLICATION pub FOR TABLE basic;
   ```

3. Conecte-se ao banco de dados do Assinante. Crie uma tabela com o mesmo esquema do Publicador.
   ```SQL
   CREATE TABLE basic(id SERIAL, name varchar(40));
   ```

4. Crie uma assinatura que se conectará à publicação que você criou anteriormente.
   ```SQL
   CREATE SUBSCRIPTION sub CONNECTION 'host=<server>.postgres.database.azure.com user=<admin> dbname=<dbname> password=<password>' PUBLICATION pub;
   ```

5. Agora você pode consultar a tabela no Assinante. Você verá que ele recebeu dados do Publicador.
   ```SQL
   SELECT * FROM basic;
   ```

Você pode adicionar mais linhas à tabela do Publicador e exibir as alterações no Assinante.

Visite a documentação do PostgreSQL para entender mais sobre a [replicação lógica](https://www.postgresql.org/docs/current/logical-replication.html).

### <a name="logical-decoding"></a>Decodificação lógica
A decodificação lógica pode ser consumida por meio do protocolo de streaming ou da interface do SQL. 

#### <a name="streaming-protocol"></a>Protocolo de streaming
O consumo de alterações usando o protocolo de streaming geralmente é preferível. Você pode criar seu próprio consumidor/conector ou usar um serviço de terceiros como o [Debezium](https://debezium.io/). 

Visite a documentação do wal2json para obter [um exemplo usando o protocolo de streaming com pg_recvlogical](https://github.com/eulerto/wal2json#pg_recvlogical).

#### <a name="sql-interface"></a>Interface do SQL 
No exemplo a seguir, usamos a interface do SQL com o plug-in wal2json.
 
1. Crie um slot.
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. Emita comandos SQL. Por exemplo:
   ```SQL
   CREATE TABLE a_table (
      id varchar(40) NOT NULL,
      item varchar(40),
      PRIMARY KEY (id)
   );
   
   INSERT INTO a_table (id, item) VALUES ('id1', 'item1');
   DELETE FROM a_table WHERE id='id1';
   ```

3. Consuma as alterações.
   ```SQL
   SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL, 'pretty-print', '1');
   ```

   A saída terá a seguinte aparência:
   ```
   {
         "change": [
         ]
   }
   {
         "change": [
                  {
                           "kind": "insert",
                           "schema": "public",
                           "table": "a_table",
                           "columnnames": ["id", "item"],
                           "columntypes": ["character varying(40)", "character varying(40)"],
                           "columnvalues": ["id1", "item1"]
                  }
         ]
   }
   {
         "change": [
                  {
                           "kind": "delete",
                           "schema": "public",
                           "table": "a_table",
                           "oldkeys": {
                                 "keynames": ["id"],
                                 "keytypes": ["character varying(40)"],
                                 "keyvalues": ["id1"]
                           }
                  }
         ]
   }
   ```

4. Descarte o slot quando terminar de usá-lo.
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```

Visite a documentação do PostgreSQL para saber mais sobre a [decodificação lógica](https://www.postgresql.org/docs/current/logicaldecoding.html).


## <a name="monitoring"></a>Monitoramento
Você deve monitorar a decodificação lógica. Qualquer slot de replicação não utilizado deve ser Descartado. Os slots mantêm os logs do postgres WAL e os catálogos de sistema relevantes até que as alterações tenham sido lidas. Se o assinante ou o consumidor falhar ou não tiver sido configurado corretamente, os logs não consumidos acumularão e preencherão o armazenamento. Além disso, os logs não consumidos aumentam o risco da ID de transação delimitador. Ambas as situações podem fazer com que o servidor fique indisponível. Portanto, é essencial que os slots de replicação lógica sejam consumidos continuamente. Se um slot de replicação lógica não for mais usado, descarte-o imediatamente.

A coluna ' ativa ' na exibição pg_replication_slots indicará se há um consumidor conectado a um slot.
```SQL
SELECT * FROM pg_replication_slots;
```

[Defina alertas](howto-alert-on-metrics.md) sobre as **IDs de transação máximas usadas** e o **armazenamento usava** métricas de servidor flexíveis para notificá-lo quando os valores aumentarem os limites normais anteriores. 

## <a name="limitations"></a>Limitações
* **Réplicas de leitura** -as réplicas de leitura do banco de dados do Azure para PostgreSQL não têm suporte atualmente para servidores flexíveis.
* **Slots e failover de ha** -os slots de replicação lógica no servidor primário não estão disponíveis no servidor em espera no AZ secundário. Isso se aplicará a você se o servidor usar a opção de alta disponibilidade com redundância de zona. No caso de um failover para o servidor em espera, os slots de replicação lógica não estarão disponíveis no modo de espera.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [as opções de rede](concepts-networking.md)
* Saiba mais sobre [as extensões](concepts-extensions.md) disponíveis no servidor flexível
* Saiba mais sobre a [alta disponibilidade](concepts-high-availability.md)

