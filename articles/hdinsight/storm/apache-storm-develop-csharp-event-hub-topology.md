---
title: Processar eventos de Hubs de Eventos com o Storm – Azure HDInsight
description: Saiba como processar dados de Hubs de Eventos do Azure com uma topologia Storm C# criada no Visual Studio usando as ferramentas do HDInsight para Visual Studio.
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 4393c6797f5a164a063b55f8994d7d37d278f3c4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929195"
---
# <a name="process-events-from-azure-event-hubs-with-apache-storm-on-hdinsight-c"></a>Processar eventos dos Hubs de Eventos do Azure com o Apache Storm no HDInsight (C#)

Saiba como trabalhar com hubs de eventos do Azure de [Apache Storm](https://storm.apache.org/) no HDInsight. Este documento usa uma topologia C# Storm para ler e gravar dados de Hubs de Eventos

> [!NOTE]  
> Para uma versão Java deste projeto, consulte [Processar eventos dos Hubs de Eventos do Azure com o Apache Storm no HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub).

## <a name="scpnet"></a>SCP.NET

As etapas neste documento usam SCP.NET, um pacote NuGet que facilita a criação de componentes e topologias C# para uso com o Storm no HDInsight.

O HDInsight 3.4 e superior usam o Mono para executar topologias C#. O exemplo usado neste documento funciona com HDInsight 3.6. Se você planeja criar suas próprias soluções do .NET para HDInsight, verifique o documento [Compatibilidade do Mono](https://www.mono-project.com/docs/about-mono/compatibility/) em busca de possíveis incompatibilidades.

### <a name="cluster-versioning"></a>Controle de versão do cluster

O pacote NuGet Microsoft.SCP.Net.SDK usado pelo seu projeto deve corresponder à versão principal do Storm instalada no HDInsight. As versões 3.5 e 3.6 do HDInsight usam o Storm 1.x, assim, você deve usar a versão 1.0.x.x do SCP.NET com esses clusters.

As topologias C# também devem ser destinadas ao .NET 4.5.

## <a name="how-to-work-with-event-hubs"></a>Como trabalhar com Hubs de Eventos

A Microsoft fornece um conjunto de componentes Java que podem ser usados para comunicação com os Hubs de Eventos de uma topologia Storm. Você pode encontrar o arquivo Java Archive (JAR) que contém uma versão compatível do HDInsight 3,6 desses componentes em [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar) .

> [!IMPORTANT]  
> Embora os componentes sejam escritos em Java, você pode usá-los facilmente em uma topologia C#.

Os componentes a seguir são usados neste exemplo:

* __EventHubSpout__: lê dados nos Hubs de Eventos.
* __EventHubBolt__: grava dados nos Hubs de Eventos.
* __EventHubSpoutConfig__: usado para configurar EventHubSpout.
* __EventHubBoltConfig__: usado para configurar EventHubBolt.

### <a name="example-spout-usage"></a>Exemplo de uso do spout

O SCP.NET fornece métodos para adição de um EventHubSpout à sua topologia. Esses métodos facilitam a adição de um spout no lugar de métodos genéricos para adição de um componente Java. O exemplo a seguir demonstra como criar um spout usando os métodos __SetEventHubSpout__ e **EventHubSpoutConfig** fornecidos pelo SCP.NET:

```csharp
 topologyBuilder.SetEventHubSpout(
    "EventHubSpout",
    new EventHubSpoutConfig(
        ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"],
        ConfigurationManager.AppSettings["EventHubSharedAccessKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        ConfigurationManager.AppSettings["EventHubEntityPath"],
        eventHubPartitions),
    eventHubPartitions);
```

O exemplo anterior cria um novo componente spout chamado __EventHubSpout__ e o configura para comunicar-se com um hub de eventos. A dica de paralelismo do componente é definir para o número de partições no hub de eventos. Essa configuração permite que o Storm crie uma instância do componente para cada partição.

### <a name="example-bolt-usage"></a>Exemplo de uso de bolt

Use o método **JavaComponmentConstructor** para criar uma instância do parafuso. O exemplo a seguir demonstra como criar e configurar uma nova instância do **EventHubBolt**:

```csharp
// Java construcvtor for the Event Hub Bolt
JavaComponentConstructor constructor = JavaComponentConstructor.CreateFromClojureExpr(
    String.Format(@"(org.apache.storm.eventhubs.bolt.EventHubBolt. (org.apache.storm.eventhubs.bolt.EventHubBoltConfig. " +
        @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
        ConfigurationManager.AppSettings["EventHubPolicyName"],
        ConfigurationManager.AppSettings["EventHubPolicyKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        "servicebus.windows.net",
        ConfigurationManager.AppSettings["EventHubName"],
        "true"));

// Set the bolt to subscribe to data from the spout
topologyBuilder.SetJavaBolt(
    "eventhubbolt",
    constructor,
    partitionCount)
        .shuffleGrouping("Spout");
```

> [!NOTE]  
> Esse exemplo usa uma expressão Clojure passada como uma cadeia de caracteres, em vez de usar **JavaComponentConstructor** para criar um **EventHubBoltConfig** como no exemplo de spout. Ambos os métodos funcionam. Use o método que for melhor para você.

## <a name="download-the-completed-project"></a>Baixar o projeto completo

Você pode baixar uma versão completa do projeto criado neste artigo do [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). No entanto, você ainda precisa fornecer definições de configuração seguindo as etapas neste artigo.

### <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Storm no HDInsight. Confira [Criar clusters Apache Hadoop usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **Storm** como **Tipo de cluster**.

* Um [Hub de eventos do Azure](../../event-hubs/event-hubs-create.md).

* O [SDK do .net do Azure](https://azure.microsoft.com/downloads/).

* As [Ferramentas do HDInsight para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

* Java JDK 1.8 ou posterior em seu ambiente de desenvolvimento. Downloads de JDK estão disponíveis na [Oracle](/azure/developer/java/fundamentals/java-jdk-long-term-support).

  * A variável de ambiente **JAVA_HOME** deve apontar ao diretório que contém o Java.
  * O diretório **%JAVA_HOME%/bin** deve estar no caminho.

## <a name="download-the-event-hubs-components"></a>Baixar os componentes dos Hubs de Eventos

Baixe o componente Spout e dehubs de eventos de [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar) .

Crie um diretório chamado `eventhubspout` e salve o arquivo no diretório.

## <a name="configure-event-hubs"></a>Configurar os Hubs de Eventos

Hubs de Eventos é a fonte de dados para este exemplo. Use as informações na seção “Criar um hub de eventos” de [Introdução a Hubs de Eventos](../../event-hubs/event-hubs-create.md).

1. Depois de criar o hub de eventos, exiba as configurações de **EventHub** no Portal do Azure e selecione **Políticas de acesso compartilhado**. Selecione **+ Adicionar** para criar as seguintes políticas:

   | Nome | Permissões |
   | --- | --- |
   | gravador |Enviar |
   | leitor |Escutar |

    ![Captura de tela da janela Políticas de acesso compartilhadas](./media/apache-storm-develop-csharp-event-hub-topology/share-access-policies.png)

2. Selecione as políticas **leitor** e **gravador**. Copie e salve o valor de chave primária de ambas as políticas, pois esses valores serão usados posteriormente.

## <a name="configure-the-eventhubwriter"></a>Configurar o EventHubWriter

1. Se você ainda não instalou a versão mais recente das ferramentas do HDInsight para Visual Studio, consulte Introdução ao [uso das ferramentas do hdinsight para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

2. Baixe a solução de [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

3. Abra **EventHubExample. sln**. No projeto **EventHubWriter**, abra o arquivo **App.config**. Use as informações do hub de eventos que você configurou antes para preencher o valor das seguintes chaves:

   | Chave | Valor |
   | --- | --- |
   | EventHubPolicyName |gravador (se você usou um nome diferente para a política com a permissão *Enviar*, use-o.) |
   | EventHubPolicyKey |A chave para a política de gravador. |
   | EventHubNamespace |O namespace que contém o seu hub de eventos. |
   | EventHubName |O nome do hub de eventos. |
   | EventHubPartitionCount |O número de partições no seu hub de eventos. |

4. Salve e feche o arquivo **App.config**.

## <a name="configure-the-eventhubreader"></a>Configurar o EventHubReader

1. Abra o projeto **EventHubReader**.

2. Abra o arquivo **App.config** para o **EventHubReader**. Use as informações do hub de eventos que você configurou antes para preencher o valor das seguintes chaves:

   | Chave | Valor |
   | --- | --- |
   | EventHubPolicyName |leitor (se você usou um nome diferente para a política com a permissão *Escutar*, use-o.) |
   | EventHubPolicyKey |A chave para a política de leitor. |
   | EventHubNamespace |O namespace que contém o seu hub de eventos. |
   | EventHubName |O nome do hub de eventos. |
   | EventHubPartitionCount |O número de partições no seu hub de eventos. |

3. Salve e feche o arquivo **App.config**.

## <a name="deploy-the-topologies"></a>Implantar as topologias

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto **EventHubReader** e selecione **Enviar para o Storm no HDInsight**.

    ![Captura de tela do Gerenciador de Soluções, com Enviar para Storm no HDInsight realçado](./media/apache-storm-develop-csharp-event-hub-topology/submit-to-apache-storm.png)

2. Na caixa de diálogo **Enviar Topologia** caixa de diálogo, selecione seu **Cluster Storm**. Expanda **Configurações Adicionais**, selecione **Caminhos de Arquivo Java**, selecione **…** e selecione o diretório que contém o arquivo JAR baixado anteriormente. Por fim, clique em **Enviar**.

    ![Captura de tela da caixa de diálogo Enviar Topologia](./media/apache-storm-develop-csharp-event-hub-topology/submit-storm-topology.png)

3. Depois que a topologia tiver sido enviada, o **Visualizador de Topologias Storm** será exibido. Selecione a topologia **EventHubReader** do painel esquerdo para exibir informações sobre ela.

    ![Captura de tela do Visualizador de Topologias do Storm](./media/apache-storm-develop-csharp-event-hub-topology/storm-topology-viewer.png)

4. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **EventHubWriter** e escolha **Enviar para o Storm no HDInsight**.

5. Na caixa de diálogo **Enviar Topologia** caixa de diálogo, selecione seu **Cluster Storm**. Expanda **Configurações Adicionais**, selecione **Caminhos de Arquivo Java**, selecione **…** e selecione o diretório que contém o arquivo JAR baixado anteriormente. Por fim, clique em **Enviar**.

6. Depois que a topologia tiver sido enviada, atualize a lista de topologia no **Visualizador de Topologias Storm** para verificar se ambas estão em execução no cluster.

7. No **Visualizador de topologias Storm**, selecione a topologia **EventHubReader** .

8. Para abrir o resumo do componente para o parafuso, clique duas vezes no componente **LogBolt** no diagrama.

9. Na seção **Executores**, selecione um dos links na coluna **Porta**. Isso exibe as informações registradas pelo componente. As informações registradas no log são semelhantes ao texto a seguir:

    ```output
    2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
    2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
    2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}
    ```

## <a name="stop-the-topologies"></a>Interromper as topologias

Para interromper as topologias, selecione cada topologia no **Visualizador de Topologia Storm** e clique em **Eliminar**.

![Captura de tela do Visualizador de Topologia do Storm, com o botão Encerrar realçado](./media/apache-storm-develop-csharp-event-hub-topology/kill-storm-topology1.png)

## <a name="delete-your-cluster"></a>Excluir o cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a usar o Spout dos hubs de eventos Java e o rolo de uma topologia C# para trabalhar com dados nos hubs de eventos do Azure. Para saber mais sobre a criação de topologias C#, consulte o seguinte:

* [Desenvolver topologias C# para o Apache Storm no HDInsight usando o Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Guia de programação do SCP](apache-storm-scp-programming-guide.md)
* [Topologias de exemplo para Apache Storm no HDInsight](apache-storm-example-topology.md)