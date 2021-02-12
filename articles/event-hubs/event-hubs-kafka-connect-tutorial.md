---
title: Integrar-se com o Apache Kafka Connect – Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo fornece informações sobre como usar o Kafka Connect com os hubs de eventos do Azure para Kafka.
ms.topic: how-to
ms.date: 01/06/2021
ms.openlocfilehash: f82dcdafa7921f4a994361371536b2f1ace7cbc5
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935148"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs"></a>Integrar o suporte ao Apache Kafka Connect nos Hubs de Eventos do Azure
O [Apache Kafka Connect](https://kafka.apache.org/documentation/#connect) é uma estrutura para se conectar e importar/exportar dados de/para qualquer sistema externo, como MySQL, HDFS e sistema de arquivos por meio de um cluster Kafka. Este tutorial orienta você pelo uso da estrutura do Kafka Connect com hubs de eventos.

> [!WARNING]
> O uso da estrutura do Apache Kafka Connect e seus conectores **não está qualificado para suporte ao produto por meio de Microsoft Azure**.
>
> O Apache Kafka Connect pressupõe que sua configuração dinâmica seja mantida em tópicos compactados com retenção ilimitada. Os hubs de eventos do Azure [não implementam a compactação como um recurso de agente](event-hubs-federation-overview.md#log-projections) e sempre impõe um limite de retenção com base no tempo em eventos retidos, raiz do princípio de que os hubs de eventos do Azure são um mecanismo de streaming de eventos em tempo real e não um repositório de dados ou de configuração de longo prazo.
>
> Embora o projeto de Apache Kafka possa se sentir confortável com a combinação dessas funções, o Azure acredita que essas informações são mais bem gerenciadas em um banco de dados ou repositório de configuração adequado.
>
> Muitos cenários do Apache Kafka Connect serão funcionais, mas essas diferenças conceituais entre os modelos de retenção dos Apache Kafka e dos hubs de eventos do Azure podem fazer com que determinadas configurações não funcionem conforme o esperado. 

Este tutorial orienta você pela integração do Kafka Connect a um hub de eventos e à implantação de conectores do filestreamname e do FileStreamSink básicos. Esse recurso está atualmente na visualização. Embora esses conectores não sirvam para uso em produção, eles demonstram um cenário Kafka Connect de ponta a ponta em que os Hubs de Eventos do Azure atuam como um agente do Kafka.

> [!NOTE]
> Este exemplo está disponível no [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect).

Neste tutorial, você deve executar as seguintes etapas:

> [!div class="checklist"]
> * Criar um namespace de Hubs de Eventos
> * Clonar o projeto de exemplo
> * Configurar o Kafka Connect para os Hubs de Eventos
> * Executar o Kafka Connect
> * Criar conectores

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este passo a passo, é necessário atender aos seguintes pré-requisitos:

- Assinatura do Azure. Se você não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/).
- [Git](https://www.git-scm.com/downloads)
- Linux/MacOS
- Kafka (versão 1.1.1, versão do Scala 2.11), disponível em [kafka.apache.org](https://kafka.apache.org/downloads#1.1.1)
- Leia o artigo introdutório [Hubs de Eventos para o Apache Kafka](./event-hubs-for-kafka-ecosystem-overview.md)

## <a name="create-an-event-hubs-namespace"></a>Criar um namespace de Hubs de Eventos
É necessário um namespace do Hubs de Eventos para enviar e receber de qualquer serviço de Hub de Eventos. Consulte [criando um hub de eventos](event-hubs-create.md) para obter instruções para criar um namespace e um hub de eventos. Obtenha a cadeia de conexão dos Hubs de Eventos e o FQDN (nome de domínio totalmente qualificado) para uso posterior. Para obter instruções, confira [Obter uma cadeia de conexão dos Hubs de Eventos](event-hubs-get-connection-string.md). 

## <a name="clone-the-example-project"></a>Clonar o projeto de exemplo
Clone o repositório dos Hubs de Eventos do Azure e navegue até a subpasta tutorials/connect: 

```
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/connect
```

## <a name="configure-kafka-connect-for-event-hubs"></a>Configurar o Kafka Connect para os Hubs de Eventos
É necessário um mínimo de reconfiguração para redirecionar a taxa de transferência do Kafka Connect para os Hubs de Eventos.  O exemplo `connect-distributed.properties` abaixo ilustra como configurar o Connect para fazer a autenticação e se comunicar com o ponto de extremidade do Kafka nos Hubs de Eventos:

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 # e.g. namespace.servicebus.windows.net:9093
group.id=connect-cluster-group

# connect internal topic names, auto-created if not exists
config.storage.topic=connect-cluster-configs
offset.storage.topic=connect-cluster-offsets
status.storage.topic=connect-cluster-status

# internal topic replication factors - auto 3x replication in Azure Storage
config.storage.replication.factor=1
offset.storage.replication.factor=1
status.storage.replication.factor=1

rest.advertised.host.name=connect
offset.flush.interval.ms=10000

key.converter=org.apache.kafka.connect.json.JsonConverter
value.converter=org.apache.kafka.connect.json.JsonConverter
internal.key.converter=org.apache.kafka.connect.json.JsonConverter
internal.value.converter=org.apache.kafka.connect.json.JsonConverter

internal.key.converter.schemas.enable=false
internal.value.converter.schemas.enable=false

# required EH Kafka security settings
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

producer.security.protocol=SASL_SSL
producer.sasl.mechanism=PLAIN
producer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

consumer.security.protocol=SASL_SSL
consumer.sasl.mechanism=PLAIN
consumer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

plugin.path={KAFKA.DIRECTORY}/libs # path to the libs directory within the Kafka release
```

> [!IMPORTANT]
> Substitua `{YOUR.EVENTHUBS.CONNECTION.STRING}` pela cadeia de conexão do seu namespace dos Hubs de Eventos. Para ver as instruções sobre como obter uma cadeia de conexão, confira [Obter cadeia de conexão para Hubs de Eventos](event-hubs-get-connection-string.md). Aqui está um exemplo de configuração: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`


## <a name="run-kafka-connect"></a>Executar o Kafka Connect

Nesta etapa, um trabalho do Kafka Connect é iniciado localmente em modo distribuído, usando os Hubs de Eventos para manter o estado do cluster.

1. Salve o arquivo acima `connect-distributed.properties` localmente.  Substitua todos os valores entre chaves.
2. Navegue até o local da versão do Kafka em seu computador.
4. Execute `./bin/connect-distributed.sh /PATH/TO/connect-distributed.properties`.  A API REST do trabalho do Connect estará pronta para interação quando você vir `'INFO Finished starting connectors and tasks'`. 

> [!NOTE]
> O Kafka Connect usa a API AdminClient do Kafka para criar automaticamente tópicos com configurações recomendadas, incluindo a compactação. Uma verificação rápida do namespace no portal do Azure revela que os tópicos internos do trabalho do Connect foram criados automaticamente.
>
>Os tópicos internos do Kafka Connect **precisam usar a compactação**.  A equipe dos Hubs de Eventos não será responsável por corrigir configurações inadequadas se os tópicos internos do Connect estiverem configurados incorretamente.

### <a name="create-connectors"></a>Criar conectores
Esta seção percorre com você a configuração de conectores FileStreamSource e FileStreamSink. 

1. Crie um diretório para os arquivos de dados de entrada e saída.
    ```bash
    mkdir ~/connect-quickstart
    ```

2. Crie dois arquivos: um arquivo com dados semente que é lido pelo conector FileStreamSource e outro no qual o conector FileStreamSink grava.
    ```bash
    seq 1000 > ~/connect-quickstart/input.txt
    touch ~/connect-quickstart/output.txt
    ```

3. Crie um conector FileStreamSource.  Substitua as chaves pelo caminho do diretório base.
    ```bash
    curl -s -X POST -H "Content-Type: application/json" --data '{"name": "file-source","config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSourceConnector","tasks.max":"1","topic":"connect-quickstart","file": "{YOUR/HOME/PATH}/connect-quickstart/input.txt"}}' http://localhost:8083/connectors
    ```
    Você deve ver o Hub de Eventos `connect-quickstart` em sua instância dos Hubs de Eventos depois de executar o comando acima.
4. Verifique o status do conector de origem.
    ```bash
    curl -s http://localhost:8083/connectors/file-source/status
    ```
    Opcionalmente, você pode usar o [Gerenciador do Barramento de Serviço](https://github.com/paolosalvatori/ServiceBusExplorer/releases) para verificar se os eventos chegaram no tópico `connect-quickstart`.

5. Crie um conector FileStreamSink.  Mais uma vez, substitua as chaves pelo caminho do diretório base.
    ```bash
    curl -X POST -H "Content-Type: application/json" --data '{"name": "file-sink", "config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSinkConnector", "tasks.max":"1", "topics":"connect-quickstart", "file": "{YOUR/HOME/PATH}/connect-quickstart/output.txt"}}' http://localhost:8083/connectors
    ```
 
6. Verifique o status do conector do coletor.
    ```bash
    curl -s http://localhost:8083/connectors/file-sink/status
    ```

7. Verifique se dados foram replicados entre os arquivos e se os dados são idênticos nos dois arquivos.
    ```bash
    # read the file
    cat ~/connect-quickstart/output.txt
    # diff the input and output files
    diff ~/connect-quickstart/input.txt ~/connect-quickstart/output.txt
    ```

### <a name="cleanup"></a>Limpeza
O Kafka Connect cria tópicos de Hub de Eventos para armazenar status, deslocamentos e configurações que persistirão mesmo depois que o cluster do Connect for desativado. A menos que você queira essa persistência, é recomendável excluir esses tópicos. Talvez seja ideal também excluir o Hub de Eventos `connect-quickstart` que foi criado no decorrer deste passo a passo.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os hubs de eventos para Kafka, consulte os seguintes artigos:  

- [Espelhar um agente do Kafka em um hub de eventos](event-hubs-kafka-mirror-maker-tutorial.md)
- [Conectar o Apache Spark a um hub de eventos](event-hubs-kafka-spark-tutorial.md)
- [Conectar o Apache Flink a um hub de eventos](event-hubs-kafka-flink-tutorial.md)
- [Explorar exemplos em nosso GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Conectar o Akka Streams a um hub de eventos](event-hubs-kafka-akka-streams-tutorial.md)
- [Guia do desenvolvedor de Apache Kafka para hubs de eventos do Azure](apache-kafka-developer-guide.md)
