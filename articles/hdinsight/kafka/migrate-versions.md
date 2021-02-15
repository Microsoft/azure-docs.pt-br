---
title: Migrar cargas de trabalho de Apache Kafka para o Azure HDInsight 4,0
description: Saiba como migrar cargas de trabalho Apache Kafka no HDInsight 3,6 para o HDInsight 4,0.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: 3967a5d96c35e4bac88dcd9a6c1fa95b78a6b2b1
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98939121"
---
# <a name="migrate-apache-kafka-workloads-to-azure-hdinsight-40"></a>Migrar cargas de trabalho de Apache Kafka para o Azure HDInsight 4,0

O Azure HDInsight 4,0 oferece os mais recentes componentes de software livre com aprimoramentos significativos em desempenho, conectividade e segurança. Este documento explica como migrar cargas de trabalho Apache Kafka no HDInsight 3,6 para o HDInsight 4,0. Depois de migrar suas cargas de trabalho para o HDInsight 4,0, você pode usar muitos dos novos recursos que não estão disponíveis no HDInsight 3,6.

## <a name="hdinsight-36-kafka-migration-paths"></a>Caminhos de migração do HDInsight 3,6 Kafka

O HDInsight 3,6 dá suporte a duas versões do Kafka: 1.0.0 e 1.1.0. O HDInsight 4,0 dá suporte às versões 1.1.0 e 2.1.0. Dependendo de qual versão do Kafka e qual versão do HDInsight você gostaria de executar, há vários caminhos de migração com suporte. Esses caminhos são explicados abaixo e ilustrados no diagrama a seguir.

* **Execute o Kafka e o HDInsight nas versões mais recentes (recomendado)**: migre um aplicativo hdinsight 3,6 e Kafka 1.0.0 ou 1.1.0 para o HDInsight 4,0 com Kafka 2.1.0 (paths D e e abaixo).
* **Execute o HDInsight na versão mais recente, mas Kafka apenas em uma versão mais recente**: migre um aplicativo hdinsight 3,6 e Kafka 1.0.0 para o HDInsight 4,0 com o Kafka 1.1.0 (caminho B abaixo).
* **Executar o HDInsight na versão mais recente, manter a versão Kafka**: migrar um aplicativo hdinsight 3,6 e Kafka 1.1.0 para o hdinsight 4,0 com o Kafka 1.1.0 (caminho C abaixo).
* **Executar o Kafka em uma versão mais recente, manter a versão do HDInsight**: migrar um aplicativo Kafka 1.0.0 para 1.1.0 e permanecer no HDInsight 3,6 (caminho a abaixo). Observe que essa opção ainda exigirá a implantação de um novo cluster. Não há suporte para a atualização da versão Kafka em um cluster existente. Depois de criar um cluster com a versão desejada, migre os clientes do Kafka para usar o novo cluster.

![Caminhos de atualização para Apache Kafka em 3,6](./media/upgrade-threesix-to-four/apache-kafka-upgrade-path.png)

## <a name="apache-kafka-versions"></a>Versões do Apache Kafka

### <a name="kafka-110"></a>Kafka 1.1.0
  
Se você migrar do Kafka 1.0.0 para o 1.1.0, poderá aproveitar os seguintes novos recursos:

* As melhorias no controlador Kafka aceleram o desligamento controlado, para que você possa reiniciar os agentes e recuperar-se de problemas com mais rapidez. 
* Melhorias na [lógica FetchRequests](https://issues.apache.org/jira/browse/KAFKA-6254) que permitem que você tenha mais partições (e, portanto, mais tópicos) no cluster. 
* O Kafka Connect dá suporte a [cabeçalhos de registro](https://issues.apache.org/jira/browse/KAFKA-5142) e [expressões regulares](https://issues.apache.org/jira/browse/KAFKA-3073) para tópicos. 

Para obter uma lista completa de atualizações, consulte [Apache Kafka release notes 1,1](https://archive.apache.org/dist/kafka/1.1.0/RELEASE_NOTES.html).

### <a name="apache-kafka-210"></a>Apache Kafka 2.1.0

Se você migrar para o Kafka 2,1, poderá aproveitar os seguintes recursos:

* Melhor resiliência do agente devido a um protocolo de replicação aprimorado.
* Nova funcionalidade na API KafkaAdminClient.
* Gerenciamento de cota configurável.
* Suporte para compactação Zstandard.

Para obter uma lista completa de atualizações, consulte [notas de versão do Apache Kafka 2,0](https://archive.apache.org/dist/kafka/2.0.0/RELEASE_NOTES.html) e [notas de versão do Apache Kafka 2,1](https://archive.apache.org/dist/kafka/2.1.0/RELEASE_NOTES.html).

## <a name="kafka-client-compatibility"></a>Compatibilidade de cliente do Kafka

Novos agentes Kafka dão suporte a clientes mais antigos. [Gnorar-35-a versão de protocolo de recuperação](https://cwiki.apache.org/confluence/display/KAFKA/KIP-35+-+Retrieving+protocol+version) introduziu um mecanismo para determinar dinamicamente a funcionalidade de um agente de Kafka e [gnorar-97: a política de compatibilidade de RPC de cliente de Kafka aprimorada](https://cwiki.apache.org/confluence/display/KAFKA/KIP-97%3A+Improved+Kafka+Client+RPC+Compatibility+Policy) introduziu uma nova política de compatibilidade e garante que o cliente Java. Anteriormente, um cliente Kafka tinha que interagir com um agente da mesma versão ou uma versão mais recente. Agora, as versões mais recentes dos clientes Java e outros clientes que dão suporte a GNORAR-35, como o, `librdkafka` podem retornar a tipos de solicitação mais antigos ou gerar erros apropriados se a funcionalidade não estiver disponível.

![Atualizar a compatibilidade do cliente Kafka](./media/upgrade-threesix-to-four/apache-kafka-client-compatibility.png)

Observe que isso não significa que o cliente ofereça suporte a agentes mais antigos.  Para obter mais informações, consulte [matriz de compatibilidade](https://cwiki.apache.org/confluence/display/KAFKA/Compatibility+Matrix).

## <a name="general-migration-process"></a>Processo de migração geral

As diretrizes de migração a seguir pressupõem um cluster Apache Kafka 1.0.0 ou 1.1.0 implantado no HDInsight 3,6 em uma única rede virtual. O agente existente tem alguns tópicos e está sendo usado ativamente por produtores e consumidores.

![Ambiente presumido Kafka atual](./media/upgrade-threesix-to-four/apache-kafka-presumed-environment.png)

Para concluir a migração, execute as seguintes etapas:

1. **Implante um novo cluster e clientes do HDInsight 4,0 para teste.** Implante um novo cluster HDInsight 4,0 Kafka. Se várias versões de cluster Kafka puderem ser selecionadas, é recomendável selecionar a versão mais recente. Após a implantação, defina alguns parâmetros conforme necessário e crie um tópico com o mesmo nome de seu ambiente existente. Além disso, defina TLS e criptografia BYOK (traga sua própria chave) conforme necessário. Em seguida, verifique se ele funciona corretamente com o novo cluster.

    ![Implantar novos clusters HDInsight 4,0](./media/upgrade-threesix-to-four/deploy-new-hdinsight-clusters.png)

1. **Alterne o cluster para o aplicativo produtor e aguarde até que todos os dados da fila sejam consumidos pelos consumidores atuais.** Quando o novo cluster HDInsight 4,0 Kafka estiver pronto, alterne o destino do produtor existente para o novo cluster. Deixe-o como é até que o aplicativo consumidor existente tenha consumido todos os dados do cluster existente.

    ![Alternar cluster para o aplicativo produtor](./media/upgrade-threesix-to-four/switch-cluster-producer-app.png)

1. **Alterne o cluster no aplicativo de consumidor.** Depois de confirmar que o aplicativo de consumidor existente concluiu o consumo de todos os dados do cluster existente, alterne a conexão para o novo cluster.

    ![Alternar cluster no aplicativo consumidor](./media/upgrade-threesix-to-four/switch-cluster-consumer-app.png)

1. **Remova o cluster antigo e os aplicativos de teste, conforme necessário.** Depois que o comutador estiver concluído e funcionando corretamente, remova o antigo cluster HDInsight 3,6 Kafka e os produtores e consumidores usados no teste, conforme necessário.

## <a name="next-steps"></a>Próximas etapas

* [Otimização de desempenho para clusters do Apache Kafka HDInsight](apache-kafka-performance-tuning.md)
* [Início Rápido: Criar cluster do Apache Kafka no Azure HDInsight usando o portal do Azure](apache-kafka-get-started.md)
