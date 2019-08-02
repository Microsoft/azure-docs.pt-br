---
title: Migrar clusters do Apache Hadoop local para o Azure HDInsight – motivação e benefícios
description: Aprenda a motivação e os benefícios de migrar clusters do Hadoop local para o Azure HDInsight.
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: f886cb71d4dbc191dae317e8ea72d8c896f37c03
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442083"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---motivation-and-benefits"></a>Migrar clusters do Apache Hadoop local para o Azure HDInsight – motivação e benefícios

Este artigo é o primeiro de uma série sobre as melhores práticas para a migração de implantações do ecossistema Apache Hadoop local para o Azure HDInsight. Esta série de artigos é para as pessoas responsáveis pelo design, pela implantação e pela migração de soluções do Apache Hadoop no Azure HDInsight. As funções que podem se beneficiar destes artigos incluem arquitetos de nuvem, administradores do Hadoop e engenheiros de DevOps. Desenvolvedores de software, engenheiros e cientistas de dados também devem se beneficiar da explicação de como os diferentes tipos de clusters funcionam na nuvem.

## <a name="why-to-migrate-to-azure-hdinsight"></a>Por que migrar para o Azure HDInsight

O Azure HDInsight é uma distribuição de nuvem dos componentes do Hadoop. O Azure HDInsight torna mais fácil, rápido e econômico processar grandes quantidades de dados. O HDInsight inclui as estruturas de software livre mais populares, como:

- Apache Hadoop
- Apache Spark
- Apache Hive com LLAP
- Apache Kafka
- Apache Storm
- HBase no Apache
- R

## <a name="azure-hdinsight-advantages-over-on-premises-hadoop"></a>Vantagens do Azure HDInsight em relação ao Hadoop local

- **Baixo custo** – os custos podem ser reduzidos [criando clusters sob demanda](../hdinsight-hadoop-create-linux-clusters-adf.md) e pagando somente pelo que é usado. Computação e armazenamento separados fornecem flexibilidade ao manter o volume de dados independente do tamanho do cluster.

- **Criação de cluster automatizada** – a criação de cluster automatizada requer uma mínima instalação e configuração. A automação pode ser usada para clusters sob demanda.

- **Hardware gerenciado e configuração** – não é necessário se preocupar com o hardware físico nem com a infraestrutura com um cluster do HDInsight. Basta especificar a configuração do cluster e o Azure o configurará.

- **Facilmente escalável** – o HDInsight permite que você [escale](../hdinsight-administer-use-portal-linux.md) as cargas de trabalho para mais ou para menos. O Azure cuida da redistribuição de dados e do rebalanceamento da carga de trabalho sem interromper os trabalhos de processamento de dados.

- **Disponibilidade global** - o HDInsight está disponível em mais [regiões](https://azure.microsoft.com/regions/services/) do que qualquer outra oferta de análise de Big Data. O Azure HDInsight também está disponível no Azure Governamental, na China e na Alemanha, o que permite atender às necessidades da sua empresa nas principais áreas soberanas.

- **Seguro e em conformidade** – o HDInsight permite a proteção dos ativos de dados empresariais com a [Rede Virtual do Microsoft Azure](../hdinsight-plan-virtual-network-deployment.md),  [criptografia](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md) e integração com o [Azure Active Directory](../domain-joined/hdinsight-security-overview.md). O HDInsight também atende aos [padrões de conformidade](https://azure.microsoft.com/overview/trusted-cloud) mais populares do setor e do governo.

- **Gerenciamento de versão simplificado** – o Azure HDInsight gerencia a versão dos componentes do ecossistema do Hadoop e os mantém atualizados. Atualizações de software geralmente são um processo complexo para implantações locais.

- **Clusters menores otimizados para cargas de trabalho específicas com menos dependências entre componentes** - uma configuração típica do Hadoop local usa um único cluster que serve para muitas finalidades. Com o Azure HDInsight, clusters de carga de trabalho específica podem ser criados. A criação de clusters para cargas de trabalho específicas elimina a complexidade de manter um único cluster com complexidade crescente.

- **Produtividade** – você pode usar várias ferramentas para o Hadoop e o Spark em seu ambiente de desenvolvimento preferido.

- **Extensibilidade com ferramentas personalizadas ou aplicativos de terceiros** – clusters do HDInsight podem ser estendidos com componentes instalados e também podem ser integrados com outras soluções de Big Data usando implantações com [um único clique](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) do Azure Marketplace.

- **Gerenciamento, administração e monitoramento fáceis** – o Azure HDInsight integra-se com [os logs](../hdinsight-hadoop-oms-log-analytics-tutorial.md) de Azure monitor para fornecer uma única interface com a qual você pode monitorar todos os seus clusters.

- **Integração com outros serviços do Azure** – o HDInsight pode ser facilmente integrado a outros serviços populares do Azure, como os seguintes:

    - ADF (Azure Data Factory)
    - Armazenamento de Blob do Azure
    - Azure Data Lake Storage Gen2
    - Azure Cosmos DB
    - Banco de Dados SQL do Azure
    - Azure Analysis Services

- **Processos e componentes de autorrecuperação** – o HDInsight verifica constantemente os componentes de infraestrutura e software livre usando sua própria infraestrutura de monitoramento. Ele também recupera automaticamente de falhas críticas, como de não disponibilidade de nós e de componentes de software livre. Os alertas serão disparados no Ambari se qualquer componente do OSS falhar.

Para obter mais informações, consulte o artigo [O que é o HDInsight do Azure e a pilha de tecnologia do Apache Hadoop](../hadoop/apache-hadoop-introduction.md).

## <a name="migration-planning-process"></a>Processo de planejamento de migração

As etapas a seguir são recomendadas para planejar uma migração de clusters do Hadoop locais para o Azure HDInsight:

1. Compreenda a implantação e as topologias locais atuais.
2. Compreenda o atual escopo do projeto, as linhas do tempo e a experiência da equipe atual.
3. Compreenda os requisitos do Azure.
4. Crie um plano detalhado com base em melhores práticas.

## <a name="gathering-details-to-prepare-for-a-migration"></a>Coleta de detalhes para se preparar para uma migração

Esta seção apresenta questionários de modelo para ajudar a coletar informações importantes sobre:

- A implantação local
- Detalhes do projeto
- Requisitos do Azure

### <a name="on-premises-deployment-questionnaire"></a>Questionário de implantação local

| **Pergunta** | **Exemplo** | **Resposta** |
|---|---|---|
|**Tópico**: **Ambiente**|||
|Versão de Distribuição do Cluster|HDP 2.6.5, CDH 5.7|
|Componentes do ecossistema de Big Data|HDFS, YARN, Hive, LLAP, Impala, Kudu, HBase, Spark, MapReduce, Kafka, ZooKeeper, Solr, Sqoop, Oozie, Ranger, Atlas, Falcon, Zeppelin, R|
|Tipos de cluster|Hadoop, Spark, Confluent Kafka, Storm, Solr|
|Número de clusters|4|
|Número de nós mestres|2|
|Número de nós de trabalho|100|
|Número de nós de borda| 5|
|Total de Espaço em Disco|100 TB|
|Configuração do Nó Mestre|m/y, cpu, disk, etc.|
|Configuração de Nós de Dados|m/y, cpu, disk, etc.|
|Configuração de Nós de Borda|m/y, cpu, disk, etc.|
|Criptografia de HDFS?|Sim|
|Alta Disponibilidade|HA do HDFS, HA do Metastore|
|Recuperação de Desastres/Backup|Realizar backup do cluster?|  
|Sistemas que dependem do Cluster|SQL Server, Teradata, Power BI, MongoDB|
|Integrações com terceiros|Tableau, GridGain, Qubole, Informatica, Splunk|
|**Tópico**: **Segurança**|||
|Segurança de perímetro|Firewalls|
|Autenticação e autorização de cluster|Active Directory, Ambari, Cloudera Manager, Sem autenticação|
|Controle de Acesso do HDFS|  Manual, usuários de SSH|
|Autenticação e autorização do Hive|Sentry, LDAP, AD com Kerberos, Ranger|
|Auditoria|Ambari, Cloudera Navigator, Ranger|
|Monitorando|Graphite, collectd, statsd, Telegraf, InfluxDB|
|Alertas|Kapacitor, Prometheus, Datadog|
|Duração da Retenção de Dados| 3 anos, 5 anos|
|Administradores de Cluster|Administrador Único, Vários Administradores|

### <a name="project-details-questionnaire"></a>Questionário de detalhes do projeto

|**Pergunta**|**Exemplo**|**Resposta**|
|---|---|---|
|**Tópico**: **Cargas de trabalho e frequência**|||
|Trabalhos do MapReduce|10 trabalhos – duas vezes por dia||
|Trabalhos do Hive|100 trabalhos – a cada hora||
|Trabalhos em lote do Spark|50 trabalhos – a cada 15 minutos||
|Trabalhos do Spark Streaming|5 trabalhos – a cada 3 minutos||
|Trabalhos de Streaming estruturados|5 trabalhos – a cada minuto||
|Trabalhos de treinamento do Modelo de ML|2 trabalhos – uma vez por semana||
|Linguagens de programação|Python, Scala, Java||
|Script|Shell do Python||
|**Tópico**: **Dados**|||
|Fontes de dados|Arquivos simples, JSON, Kafka, RDBMS||
|Orquestração de dados|Fluxos de trabalho do Oozie, Airflow||
|Em pesquisas de memória|Apache Ignite, Redis||
|Destinos de dados|HDFS, RDBMS, Kafka, MPP ||
|**Tópico**: **Metadados**|||
|Tipo de banco de dados do Hive|Mysql, Postgres||
|Nº de metastores do Hive|2||
|Nº de tabelas do Hive|100||
|Nº de políticas do Ranger|20||
|Nº de fluxos de trabalho do Oozie|100||
|**Tópico**: **Escala**|||
|Volume de dados incluindo Replicação|100 TB||
|Volume diário de ingestão|50 GB||
|Taxa de crescimento de dados|10% ao ano||
|Taxa de crescimento de Nós de Cluster|5% ao ano
|**Tópico**: **Utilização do cluster**|||
|% média de CPU usada|60%||
|% média de memória usada|75%||
|Espaço em disco usado|75%||
|% média de rede usada|25%
|**Tópico**: **Equipe**|||
|Nº de Administradores|2||
|Nº de Desenvolvedores|10||
|Nº de usuários finais|100||
|Habilidades|Hadoop, Spark||
|Nº de recursos disponíveis para esforços de Migração|2||
|**Tópico**: **Limitações**|||
|Limitações atuais|A latência é alta||
|Desafios atuais|Problema de simultaneidade||

### <a name="azure-requirements-questionnaire"></a>Questionário de requisitos do Azure

|**Tópico**: **Infraestrutura** |||
|---|---|---|
|**Pergunta**|**Exemplo**|**Resposta**|
| Região Preferida|Leste dos EUA||
|Rede virtual preferida?|Sim||
|HA/DR necessárias?|Sim||
|Integração com outros serviços de nuvem?|ADF, CosmosDB||
|**Tópico**:   **Movimentação de dados**  |||
|Preferência de carregamento inicial|DistCp, Data Box, ADF, WANDisco||
|Delta de transferência de dados|DistCp, AzCopy||
|Transferência de dados incremental em andamento|DistCp, Sqoop||
|**Tópico**:   **Monitoramento e alertas** |||
|Usar Monitoramento e Alertas do Azure vs. integrar monitoramento de terceiros|Usar Monitoramento e Alertas do Azure||
|**Tópico**:   **Preferências de segurança** |||
|Pipeline de dados privados e protegidos?|Sim||
|Cluster de domínio ingressado (ESP)?|     Sim||
|Sincronização do AD Local com a Nuvem?|     Sim||
|Nº de usuários do AD a sincronizar?|          100||
|OK sincronizar senhas com a nuvem?|    Sim||
|Somente usuários de nuvem?|                 Sim||
|MFA necessária?|                       Não|| 
|Requisitos de autorização de dados?|  Sim||
|Controle de Acesso Baseado em Função?|        Sim||
|Auditoria necessária?|                  Sim||
|Criptografia de dados em repouso?|          Sim||
|Criptografia de dados em trânsito?|       Sim||
|**Tópico**:   **Preferências de redefinição de arquitetura** |||
|Único cluster vs. tipos específicos de cluster|Tipos específicos de cluster||
|Armazenamento Colocalizados vs. Armazenamento Remoto?|Armazenamento Remoto||
|Menor tamanho de cluster já que os dados são armazenados remotamente?|Menor tamanho de cluster||
|Usar vários clusters menores em vez de um único cluster grande?|Usar vários clusters menores||
|Usar um metastore remoto?|Sim||
|Compartilhar metastores entre clusters diferentes?|Sim||
|Desconstruir cargas de trabalho?|Substituir trabalhos do Hive por trabalhos do Spark||
|Usar ADF para orquestração de dados?|Não||

## <a name="next-steps"></a>Próximas etapas

Leia o próximo artigo desta série:

- [Melhores práticas de arquitetura para migração do local para o Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-architecture.md)
