---
title: Como usar a replicação de Apache Hive em clusters do Azure HDInsight
description: Saiba como usar a replicação do hive em clusters HDInsight para replicar o metastore do Hive e o Azure Data Lake Storage Data Lake da Gen 2.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 2e57b7d8121ac0bd6fc9cb693750c41a0ef6a5bc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101733050"
---
# <a name="how-to-use-apache-hive-replication-in-azure-hdinsight-clusters"></a>Como usar a replicação de Apache Hive em clusters do Azure HDInsight

No contexto de bancos de dados e depósitos, a replicação é o processo de duplicação de entidades de um depósito para outro. A duplicação pode ser aplicada a um banco de dados inteiro ou a um nível menor, como uma tabela ou partição. O objetivo é ter uma réplica que seja alterada sempre que a entidade base for alterada. A replicação no Apache Hive se concentra na recuperação de desastres e oferece replicação de cópia primária unidirecional. Em clusters HDInsight, a replicação do hive pode ser usada para replicar unidirecionalmente o metastore do Hive e o data Lake subjacente associado em Azure Data Lake Storage Gen2.  

A replicação do hive evoluiu ao longo dos anos com versões mais recentes, proporcionando melhor funcionalidade e com uso mais rápido e menos intensivo de recursos. Neste artigo, discutiremos a replicação do hive (Replv2), que é suportada nos tipos de cluster HDInsight 3,6 e HDInsight 4,0.

## <a name="advantages-of-replv2"></a>Vantagens do replv2

O [Hive ReplicationV2](https://cwiki.apache.org/confluence/display/Hive/HiveReplicationv2Development) (também chamado de Replv2) tem as seguintes vantagens em relação à primeira versão da replicação do hive que usava o hive [Import-Export](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ImportExport):

- Replicação incremental baseada em evento
- Replicação point-in-time  
- Requisitos de largura de banda reduzidos  
- Redução no número de cópias intermediárias  
- O estado de replicação é mantido
- Replicação restrita  
- Suporte para um modelo de Hub e spoke  
- Suporte para tabelas ACID (no HDInsight 4,0)

## <a name="replication-phases"></a>Fases de replicação

A replicação baseada em evento do hive é configurada entre os clusters primário e secundário. Essa replicação consiste em duas fases distintas: inicialização e execução incremental.

### <a name="bootstrapping"></a>Inicialização

A inicialização deve ser executada uma vez para replicar o estado de base dos bancos de dados do primário para o secundário. Você pode configurar a inicialização, se necessário, para incluir um subconjunto das tabelas no banco de dados de destino em que a replicação precisa ser habilitada.

### <a name="incremental-runs"></a>Execuções incrementais

Após a inicialização, as execuções incrementais são automatizadas no cluster primário e os eventos gerados durante essas execuções incrementais são reproduzidos no cluster secundário. Quando o cluster secundário é atualizado com o cluster primário, o secundário se torna consistente com os eventos do primário.

## <a name="replication-commands"></a>Comandos de replicação

O hive oferece um conjunto de comandos REPL – `DUMP` , `LOAD` , e `STATUS` -para orquestrar o fluxo de eventos. O `DUMP` comando gera um log local de todos os eventos DDL/DML no cluster primário. O `LOAD` comando é uma abordagem para copiar lentamente metadados e dados registrados para a saída de despejo de replicação extraída e é executado no cluster de destino. O `STATUS` comando é executado do cluster de destino para fornecer a última ID de evento que a carga de replicação mais recente foi replicada com êxito.

### <a name="set-replication-source"></a>Definir origem de replicação

Antes de começar com a replicação, verifique se o banco de dados que será replicado está definido como a origem de replicação. Você pode usar o `DESC DATABASE EXTENDED <db_name>` comando para determinar se o parâmetro `repl.source.for` está definido com o nome da política.

Se a política estiver agendada e o `repl.source.for` parâmetro não estiver definido, você precisará primeiro definir esse parâmetro usando `ALTER DATABASE <db_name> SET DBPROPERTIES ('repl.source.for'='<policy_name>')` .

```sql
ALTER DATABASE tpcds_orc SET DBPROPERTIES ('repl.source.for'='replpolicy1') 
```

### <a name="dump-metadata-to-the-data-lake"></a>Despejar metadados para o data Lake

O `REPL DUMP [database name]. => location / event_id` comando é usado na fase de inicialização para despejar metadados relevantes para Azure data Lake Storage Gen2. O `event_id` especifica o evento mínimo para o qual os metadados relevantes foram colocados em Azure data Lake Storage Gen2. 
 
```sql
repl dump tpcds_orc; 
```
Saída de exemplo:

| dump_dir|last_repl_id
|-|-|
|/tmp/hive/repl/38896729-67d5-41b2-90dc-46eeed4c5dd0|2925|

### <a name="load-data-to-the-target-cluster"></a>Carregar dados no cluster de destino

O `REPL LOAD [database name] FROM [ location ] { WITH ( ‘key1’=‘value1’{, ‘key2’=‘value2’} ) }` comando é usado para carregar dados no cluster de destino para as fases de inicialização e incrementais da replicação. O `[database name]` pode ser o mesmo que a origem ou um nome diferente no cluster de destino. O `[location]` representa o local da saída do comando anterior `REPL DUMP` . Isso significa que o cluster de destino deve ser capaz de se comunicar com o cluster de origem. A `WITH` cláusula foi adicionada principalmente para evitar uma reinicialização do cluster de destino, permitindo a replicação.

```sql
repl load tpcds_orc from '/tmp/hive/repl/38896729-67d5-41b2-90dc-46eeed4c5dd0'; 
```

### <a name="output-the-last-replicated-event-id"></a>Saída da última ID de evento replicado

O `REPL STATUS [database name]` comando é executado em clusters de destino e gera a última replicação `event_id` . O comando também permite que os usuários saibam em qual estado seu cluster de destino é replicado. Você pode usar a saída desse comando para construir o próximo `REPL DUMP` comando para replicação incremental.

```sql
repl status tpcds_orc;
```

Saída de exemplo:

|last_repl_id|
|-|
|2925|

### <a name="dump-relevant-data-and-metadata-to-the-data-lake"></a>Despejar dados e metadados relevantes para o data Lake

O `REPL DUMP [database name] FROM [event-id] { TO [event-id] } { LIMIT [number of events] }` comando é usado para despejar metadados e dados relevantes para Azure data Lake Storage. Esse comando é usado na fase incremental e é executado no depósito de origem. O `FROM [event-id]` é necessário para a fase incremental e o valor de `event-id` pode ser derivado executando o `REPL STATUS [database name]` comando no depósito de destino.

```sql
repl dump tpcds_orc from 2925;
```

Saída de exemplo:

|dump_dir|last_repl_id|
|-|-|
| /tmp/hive/repl/38896729-67d5-41b2-90dc-466466agadd0 | 2960|

## <a name="hive-replication-process"></a>Processo de replicação do hive

As etapas a seguir são os eventos sequenciais que ocorrem durante o processo de replicação do hive.

1. Verifique se as tabelas a serem replicadas estão definidas como a origem de replicação para uma determinada política.

1. O `REPL_DUMP` comando é emitido para o cluster primário com restrições associadas, como nome do banco de dados, intervalo de ID de evento e Azure data Lake Storage Gen2 URL de armazenamento.

1. O sistema serializa um despejo de todos os eventos rastreados do metastore para o mais recente. Esse despejo é armazenado na conta de armazenamento Azure Data Lake Storage Gen2 no cluster primário na URL especificada pelo `REPL_DUMP` .  

1. O cluster primário persiste os metadados de replicação para o armazenamento de Azure Data Lake Storage Gen2 do cluster primário. O caminho é configurável na interface do usuário de configuração do hive no Ambari. O processo fornece o caminho em que os metadados são armazenados e a ID do evento DML/DDL rastreado mais recente.

1. O `REPL_LOAD` comando é emitido a partir do cluster secundário. O comando aponta para o caminho configurado na etapa 3.

1. O cluster secundário lê o arquivo de metadados com eventos rastreados que foram criados na etapa 3. Verifique se o cluster secundário tem conectividade de rede com o armazenamento de Azure Data Lake Storage Gen2 do cluster primário em que os eventos rastreados `REPL_DUMP` são armazenados.  

1. O cluster secundário gera a computação de cópia distribuída ( `DistCP` ).

1. O cluster secundário copia dados do armazenamento do cluster primário.  

1. O metastore no cluster secundário é atualizado.  

1. A última ID do evento rastreado é armazenada no metastore primário.

A replicação incremental segue o mesmo processo e requer a última ID de evento replicado como entrada. Isso leva a uma cópia incremental desde o último evento de replicação. As replicações incrementais normalmente são automatizadas com uma frequência predeterminada para atingir RPOs (objetivos de ponto de recuperação) necessários.

:::image type="content" source="media/apache-hive-replication/hive-replication-diagram.png" alt-text="Diagrama de replicação do hive":::

## <a name="replication-patterns"></a>Padrões de replicação  

A replicação normalmente é configurada de forma unidirecional entre o primário e o secundário, em que o primário atende às solicitações de leitura e gravação. O cluster secundário atende apenas às solicitações de leitura. As gravações são permitidas no secundário se houver um desastre, mas a replicação inversa precisa ser configurada de volta para a primária.

:::image type="content" source="media/apache-hive-replication/replication-pattern.png" alt-text="Padrão de replicação do hive":::

Há muitos padrões adequados para a replicação do hive, incluindo primário – secundário, Hub e spoke e retransmissão.

No HDInsight ativo primário – o secundário em espera é um padrão comum de BCDR (continuidade de negócios e recuperação de desastre) e HiveReplicationV2 pode usar esse padrão com clusters HDInsight Hadoop separados de modo regional com emparelhamento VNet. Uma máquina virtual comum emparelhada com ambos os clusters pode ser usada para hospedar os scripts de automação de replicação. Para obter mais informações sobre possíveis padrões de BCDR do HDInsight, consulte a [documentação de continuidade de negócios do hdinsight](../hdinsight-business-continuity.md).  

### <a name="hive-replication-with-enterprise-security-package"></a>Replicação do hive com Enterprise Security Package  

Nos casos em que a replicação do hive está planejada em clusters Hadoop do HDInsight com Enterprise Security Package, você tem que considerar os mecanismos de replicação para o Ranger metastore e Azure Active Directory Domain Services (AD DS).  

Use o recurso conjuntos de réplicas AD DS do Azure para criar mais de um conjunto de réplicas do Azure AD DS por locatário do Azure AD em várias regiões. Cada conjunto de réplicas individuais precisa ser emparelhado com o HDInsight VNets em suas respectivas regiões. Nessa configuração, as alterações no Azure AD DS, incluindo configuração, identidade do usuário e credenciais, grupos, objetos de política de grupo, objetos de computador e outras alterações, são aplicadas a todos os conjuntos de réplicas no domínio gerenciado usando a replicação de AD DS do Azure.
  
As políticas de Ranger podem ser periodicamente submetidas a backup e replicadas do primário para o secundário usando a funcionalidade do Ranger Import-Export. Você pode optar por replicar todos ou um subconjunto de políticas de Ranger, dependendo do nível de autorizações que você está procurando implementar no cluster secundário.  

## <a name="sample-code"></a>Código de exemplo  

A sequência de código a seguir fornece um exemplo de como a inicialização e a replicação incremental podem ser implementadas em uma tabela de exemplo chamada `tpcds_orc` .  

1. Defina a tabela como a origem para uma política de replicação.

   ```sql
   ALTER DATABASE tpcds_orc SET DBPROPERTIES ('repl.source.   for'='replpolicy1');
   ```

1. Despejo de inicialização no cluster primário.

   ```sql
   repl dump tpcds_orc with ('hive.repl.rootdir'='/tmpag/hiveag/replag'); 
   ```
   
   Saída de exemplo:
   
   |dump_dir|last_repl_id|
   |-|-|
   |/tmpag/hiveag/replag/675d1bea-2361-4cad-bcbf-8680d305a27a|2925|
 
1. Carga de inicialização no cluster secundário. 

   ```sql
   repl load tpcds_orc from '/tmpag/hiveag/replag 675d1bea-2361-4cad-bcbf-8680d305a27a'; 
   ```

1. Verifique o `REPL` status no cluster secundário.

   ```sql
   repl status tpcds_orc; 
   ```
 
   |last_repl_id|
   |-|
   |2925|

1. Despejo incremental no cluster primário.

   ```sql
   repl dump tpcds_orc from 2925 with ('hive.repl.rootdir'='/tmpag/hiveag/ replag');
   ```

   Saída de exemplo:
   
   |dump_dir | last_repl_id|
   |-|-|
   |/tmpag/hiveag/replag/31177ff7-a40f-4f67-a613-3b64ebe3bb31|2960|

1. Carga incremental no cluster secundário.  

   ```sql
   repl load tpcds_orc from '/tmpag/hiveag/replag/31177ff7-a40f-4f67-a613-3b64ebe3bb31';
   ```

1. Verifique o `REPL` status no cluster secundário.

   ```sql
   repl status tpcds_orc;
   ```

   |last_repl_id|
   |-|
   |2960|

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os itens discutidos neste artigo, consulte:

- [Continuidade de negócios do Azure HDInsight](../hdinsight-business-continuity.md)
- [Arquiteturas de continuidade de negócios do Azure HDInsight](../hdinsight-business-continuity-architecture.md)
- [Estudo de caso da arquitetura de solução de alta disponibilidade do Azure HDInsight](../hdinsight-high-availability-case-study.md)
- [Saiba mais sobre o Apache Hive e o HiveQL no Azure HDInsight?](../hadoop/hdinsight-use-hive.md)