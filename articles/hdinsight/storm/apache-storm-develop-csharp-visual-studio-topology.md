---
title: Topologias Apache Storm com Visual Studio e C# – Azure HDInsight
description: Aprenda como criar topologias Storm em C#. Crie uma topologia de contagem de palavras no Visual Studio usando as ferramentas do Hadoop para Visual Studio.
ROBOTS: NOINDEX
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/31/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: a81f2b21545a5362168482f3f0a65fbbbf381c10
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929153"
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>Desenvolver topologias C# para Apache Storm usando ferramentas do Data Lake para Visual Studio

Aprenda como criar uma topologia do C# Apache Storm usando as ferramentas do Azure Data Lake (Apache Hadoop) para o Visual Studio. Este documento orienta o processo de criar um projeto do Storm no Visual Studio, testá-lo localmente e implantá-lo em um Apache Storm no cluster do Azure HDInsight.

Você também aprende a criar topologias híbridas que usam componentes C# e Java.

As topologias C# usam o .NET 4,5 e usam o mono para serem executados no cluster HDInsight. Para obter informações sobre possíveis incompatibilidades, consulte [compatibilidade mono](https://www.mono-project.com/docs/about-mono/compatibility/). Para usar uma topologia C#, você deve atualizar o `Microsoft.SCP.Net.SDK` pacote NuGet usado pelo seu projeto para a versão 0.10.0.6 ou posterior. A versão do pacote também deve coincidir com a versão principal do Storm instalada no HDInsight.

| Versão do HDInsight | Versão do Apache Storm | Versão do SCP.NET | Versão Mono padrão |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.4 | 0.10.0.x | 0.10.0.x | 3.2.8 |
| 3,5 | 1.0.2.x | 1.0.0.x | 4.2.1 |
| 3,6 | 1.1.0.x | 1.0.0.x | 4.2.8 |

## <a name="prerequisite"></a>Pré-requisito

Um cluster do Apache Storm no HDInsight. Confira [Criar clusters Apache Hadoop usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **Storm** como **Tipo de cluster**.

## <a name="install-visual-studio"></a>Instalar o Visual Studio

Você pode desenvolver topologias C# com o SCP.NET usando o [Visual Studio](https://visualstudio.microsoft.com/downloads/). As instruções aqui usam o Visual Studio 2019, mas você também pode usar versões anteriores do Visual Studio.

## <a name="install-data-lake-tools-for-visual-studio"></a>Instalar ferramentas do Data Lake para Visual Studio

Para instalar as ferramentas do Data Lake para Visual Studio, siga as etapas em [Introdução ao uso das ferramentas do Data Lake para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio).

## <a name="install-java"></a>Instalar o Java

Quando você envia uma topologia Storm do Visual Studio, o SCP.NET gera um arquivo zip que contém a topologia e as respectivas dependências. O Java é usado para criar esses arquivos zip porque ele usa um formato que é mais compatível com clusters baseados em Linux.

1. Instale o JDK (Java Developer Kit) 7 ou posterior em seu ambiente de desenvolvimento. Você pode obter o Oracle JDK da [Oracle](https://openjdk.java.net/). Você também pode usar [outras distribuições do Java](/java/azure/jdk/).

2. Defina a `JAVA_HOME` variável de ambiente para o diretório que contém Java.

3. Defina a `PATH` variável de ambiente para incluir o `%JAVA_HOME%\bin` diretório.

Você pode compilar e executar o seguinte aplicativo de console em C# para verificar se o Java e o JDK estão instalados corretamente:

```csharp
using System;
using System.IO;
namespace ConsoleApplication2
{
   class Program
   {
       static void Main(string[] args)
       {
           string javaHome = Environment.GetEnvironmentVariable("JAVA_HOME");
           if (!string.IsNullOrEmpty(javaHome))
           {
               string jarExe = Path.Combine(javaHome + @"\bin", "jar.exe");
               if (File.Exists(jarExe))
               {
                   Console.WriteLine("JAVA Is Installed properly");
                    return;
               }
               else
               {
                   Console.WriteLine("A valid JAVA JDK is not found. Looks like JRE is installed instead of JDK.");
               }
           }
           else
           {
             Console.WriteLine("A valid JAVA JDK is not found. JAVA_HOME environment variable is not set.");
           }
       }  
   }
}
```

## <a name="apache-storm-templates"></a>Modelos do Apache Storm

As ferramentas do Data Lake para o Visual Studio fornecem os seguintes modelos:

| Tipo de projeto | Demonstra |
| --- | --- |
| Aplicativo Storm |Um projeto de topologia Storm vazio. |
| Amostra de gravador do SQL Azure Storm |Como gravar em um Banco de Dados SQL do Azure. |
| Amostra de leitor do Azure Cosmos DB do Storm |Como ler do Azure Cosmos DB. |
| Amostra do gravador do Azure Cosmos DB do Storm |Como gravar no Azure Cosmos DB. |
| Amostra de leitor de Hub de Eventos do Storm |Como ler dos Hubs de Eventos do Azure. |
| Amostra do gravador de Hub de Eventos do Storm |Como gravar nos Hubs de Eventos do Azure. |
| Amostra de leitor HBase do Storm |Como ler do HBase em clusters HDInsight. |
| Amostra de gravador HBase do Storm |Como gravar no HBase em clusters HDInsight. |
| Amostra híbrida do Storm |Como usar um componente Java. |
| Amostra do Storm |Uma topologia básica de contagem de palavras. |

> [!WARNING]  
> Nem todos os modelos funcionam com o HDInsight baseado em Linux. Pacotes NuGet usados pelos modelos podem não ser compatíveis com o Mono. Para identificar possíveis problemas, consulte [compatibilidade mono](https://www.mono-project.com/docs/about-mono/compatibility/) e use o [analisador de portabilidade do .net](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis).

Nas etapas neste documento, você usará o tipo de projeto de aplicativo Storm básico para criar uma topologia.

### <a name="apache-hbase-templates"></a>Modelos do Apache HBase

Para se comunicar com um HBase no cluster HDInsight, os modelos de leitor e gravador do HBase não usam a API Java do HBase, mas sim a API REST do HBase.

### <a name="eventhub-templates"></a>Modelos do EventHub

> [!IMPORTANT]  
> O componente spout do EventHub baseado em Java incluído com o modelo de Leitor do EventHub pode não funcionar com o Storm no HDInsight versão 3.5 ou posterior. Uma versão atualizada deste componente está disponível em [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib).

Para uma topologia de exemplo que usa esse componente e funciona com Storm no HDInsight 3.5, consulte [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

## <a name="create-a-c-topology"></a>Criar uma topologia C#

Para criar um projeto de topologia C# no Visual Studio:

1. Abra o Visual Studio.

1. Na janela **Iniciar**, selecione **Criar projeto**.

1. Na janela **criar um novo projeto** , role para e selecione **aplicativo Storm** e, em seguida, selecione **Avançar**.

1. Na janela **configurar seu novo projeto** , insira um **nome de projeto** de *WordCount*, vá para ou crie um caminho de diretório de **localização** para o projeto e, em seguida, selecione **criar**.

    ![Aplicativo Storm, configurar a caixa de diálogo novo projeto, Visual Studio](./media/apache-storm-develop-csharp-visual-studio-topology/apache-storm-new-project.png)

Depois de criar o projeto, você deverá ter os seguintes arquivos:

* *Program.cs*: a definição de topologia para seu projeto. Uma topologia padrão consistindo em um spout e um bolt é criada por padrão.

* *Spout.cs*: um spout de exemplo que emite números aleatórios.

* *Bolt.cs*: um bolt de exemplo que mantém uma contagem de números emitidos pelo spout.

Quando você cria o projeto, o NuGet baixa a versão mais recente do [pacote SCP.NET](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/).

### <a name="implement-the-spout"></a>Implementar o spout

Em seguida, adicione o código para o Spout, que é usado para ler dados em uma topologia de uma fonte externa. Esse spout emite uma frase para a topologia aleatoriamente.

1. Abra *Spout.cs*. Os principais componentes de um spout são:

   * `NextTuple`: Chamado pelo Storm quando o Spout tem permissão para emitir novas tuplas.

   * `Ack` (somente topologia transacional): lida com confirmações iniciadas por outros componentes na topologia para tuplas enviadas do Spout. Confirmar uma tupla informa ao spout que ele foi processado com êxito por componentes downstream.

   * `Fail` (somente topologia transacional): lida com tuplas que estão processando outros componentes na topologia. A implementação de um `Fail` método permite emitir novamente a tupla para que ela possa ser processada novamente.

2. Substitua o conteúdo da `Spout` classe pelo seguinte texto:

    ```csharp
    private Context ctx;
    private Random r = new Random();
    string[] sentences = new string[] {
        "the cow jumped over the moon",
        "an apple a day keeps the doctor away",
        "four score and seven years ago",
        "snow white and the seven dwarfs",
        "i am at two with nature"
    };

    public Spout(Context ctx)
    {
        // Set the instance context
        this.ctx = ctx;

        Context.Logger.Info("Generator constructor called");

        // Declare Output schema
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // The schema for the default output stream is
        // a tuple that contains a string field
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
    }

    // Get an instance of the spout
    public static Spout Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Spout(ctx);
    }

    public void NextTuple(Dictionary<string, Object> parms)
    {
        Context.Logger.Info("NextTuple enter");
        // The sentence to be emitted
        string sentence;

        // Get a random sentence
        sentence = sentences[r.Next(0, sentences.Length - 1)];
        Context.Logger.Info("Emit: {0}", sentence);
        // Emit it
        this.ctx.Emit(new Values(sentence));

        Context.Logger.Info("NextTuple exit");
    }

    public void Ack(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }

    public void Fail(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }
    ```

### <a name="implement-the-bolts"></a>Implementar os bolts

Agora, crie dois parafusos Storm neste exemplo:

1. Exclua o arquivo *Bolt.cs* existente do projeto.

2. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar**  >  **novo item**. Na lista, selecione **Storm Bolt** e digite *Splitter.cs* como o nome. No código do novo arquivo, altere o nome do namespace para `WordCount` . Em seguida, repita esse processo para criar um segundo parafuso chamado *Counter.cs*.

   * *Splitter.cs*: implementa um raio que divide frases em palavras individuais e emite um novo fluxo de palavras.

   * *Counter.cs*: implementa um raio que conta cada palavra e emite um novo fluxo de palavras e a contagem de cada palavra.

     > [!NOTE]  
     > Esses bolts leem e gravam em fluxos, mas você também pode usar um bolt para se comunicar com um banco de dados ou serviço.

3. Abra *Splitter.cs*. Ele tem apenas um método por padrão: `Execute` . O `Execute` método é chamado quando o raio recebe uma tupla para processamento. Aqui, você pode ler e processar tuplas de entradas e emitir tuplas de saída.

4. Substitua o conteúdo da classe `Splitter` pelo seguinte código:

    ```csharp
    private Context ctx;

    // Constructor
    public Splitter(Context ctx)
    {
        Context.Logger.Info("Splitter constructor called");
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // Input contains a tuple with a string field (the sentence)
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // Outbound contains a tuple with a string field (the word)
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance of the bolt
    public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Splitter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the sentence from the tuple
        string sentence = tuple.GetString(0);
        // Split at space characters
        foreach (string word in sentence.Split(' '))
        {
            Context.Logger.Info("Emit: {0}", word);
            //Emit each word
            this.ctx.Emit(new Values(word));
        }

        Context.Logger.Info("Execute exit");
    }
    ```

5. Abra *Counter.cs* e substitua o conteúdo da classe pelo seguinte código:

    ```csharp
    private Context ctx;

    // Dictionary for holding words and counts
    private Dictionary<string, int> counts = new Dictionary<string, int>();

    // Constructor
    public Counter(Context ctx)
    {
        Context.Logger.Info("Counter constructor called");
        // Set instance context
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string field - the word
        inputSchema.Add("default", new List<Type>() { typeof(string) });

        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string and integer field - the word and the word count
        outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance
    public static Counter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Counter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the word from the tuple
        string word = tuple.GetString(0);
        // Do we already have an entry for the word in the dictionary?
        // If no, create one with a count of 0
        int count = counts.ContainsKey(word) ? counts[word] : 0;
        // Increment the count
        count++;
        // Update the count in the dictionary
        counts[word] = count;

        Context.Logger.Info("Emit: {0}, count: {1}", word, count);
        // Emit the word and count information
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));
        Context.Logger.Info("Execute exit");
    }
    ```

### <a name="define-the-topology"></a>Definir a topologia

Spouts e bolts são organizados em um grafo, que define como os dados fluem entre componentes. Para essa topologia, o grafo é o seguinte:

![Diagrama de organização de componentes Spout e parafuso, topologia Storm](./media/apache-storm-develop-csharp-visual-studio-topology/word-count-topology1.png)

O Spout emite frases que são distribuídas para instâncias do parafuso do divisor. O bolt Splitter divide as frases em palavras, que são distribuídas para o bolt Contador.

Como a instância do contador mantém a contagem de palavras localmente, você deseja garantir que as palavras específicas fluam para a mesma instância do balcão do contador. Cada instância controla palavras específicas. Uma vez que o bolt Splitter não mantém nenhum estado, não importa qual instância do Splitter recebe qual frase.

Abra *Program.cs*. O método importante é `GetTopologyBuilder` , que é usado para definir a topologia que é enviada ao Storm. Substitua o conteúdo de `GetTopologyBuilder` pelo código a seguir para implementar a topologia descrita anteriormente:

```csharp
// Create a new topology named 'WordCount'
TopologyBuilder topologyBuilder = new TopologyBuilder(
    "WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

// Add the spout to the topology.
// Name the component 'sentences'
// Name the field that is emitted as 'sentence'
topologyBuilder.SetSpout(
    "sentences",
    Spout.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
    },
    1);
// Add the splitter bolt to the topology.
// Name the component 'splitter'
// Name the field that is emitted 'word'
// Use suffleGrouping to distribute incoming tuples
//   from the 'sentences' spout across instances
//   of the splitter
topologyBuilder.SetBolt(
    "splitter",
    Splitter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
    },
    1).shuffleGrouping("sentences");

// Add the counter bolt to the topology.
// Name the component 'counter'
// Name the fields that are emitted 'word' and 'count'
// Use fieldsGrouping to ensure that tuples are routed
//   to counter instances based on the contents of field
//   position 0 (the word). This could also have been
//   List<string>(){"word"}.
//   This ensures that the word 'jumped', for example, will always
//   go to the same instance
topologyBuilder.SetBolt(
    "counter",
    Counter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
    },
    1).fieldsGrouping("splitter", new List<int>() { 0 });

// Add topology config
topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
{
    {"topology.kryo.register","[\"[B\"]"}
});

return topologyBuilder;
```

## <a name="submit-the-topology"></a>Enviar a topologia

Agora você está pronto para enviar a topologia para o cluster HDInsight.

1. Navegue para **Exibir**  >  **Gerenciador de servidores**.

1. Clique com o botão direito do mouse em **Azure**, selecione **conectar-se a Microsoft Azure assinatura...** e conclua o processo de entrada.

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto e escolha **Enviar para o Storm no HDInsight**.

1. Na caixa de diálogo **Enviar topologia** , na lista suspensa **cluster Storm** , escolha seu Storm no cluster HDInsight e, em seguida, selecione **Enviar**. Você pode verificar se o envio foi bem-sucedido exibindo o painel de **saída** .

    Quando a topologia for enviada com êxito, a janela de **exibição topologias do Storm** para o cluster deverá aparecer. Escolha a topologia **WordCount** na lista para exibir informações sobre a topologia em execução.

    ![Janela de exibição de topologia do Storm, cluster HDInsight, Visual Studio](./media/apache-storm-develop-csharp-visual-studio-topology/storm-topology-view.png)

    > [!NOTE]  
    > Você também pode exibir **topologias Storm** do **Gerenciador de Servidores**. Expanda **Azure**  >  **HDInsight**, clique com o botão direito do mouse em um cluster Storm no HDInsight e escolha **Exibir topologias Storm**.

    Para exibir informações sobre os componentes na topologia, selecione um componente no diagrama.

1. Na seção **Resumo da topologia** , selecione **Kill** para interromper a topologia.

    > [!NOTE]  
    > As topologias Storm continuarão em execução até serem paradas ou até que o cluster seja excluído.

## <a name="transactional-topology"></a>Topologia transacional

A topologia anterior não é transacional. Os componentes na topologia não implementam a funcionalidade para reproduzir mensagens. Para um exemplo de uma topologia transacional, crie um projeto e selecione **Amostra do Storm** como o tipo de projeto.

As topologias transacionais implementam o seguinte para suportar a reprodução de dados:

* **Cache de metadados**: o Spout deve armazenar metadados sobre os dados emitidos, para que os dados possam ser recuperados e emitidos novamente se ocorrer uma falha. Como os dados emitidos pela amostra são pequenos, os dados brutos de cada tupla são armazenados em um dicionário para reprodução.

* **Ack**: cada bolt na topologia pode chamar `this.ctx.Ack(tuple)` para confirmar que processou uma tupla com êxito. Quando todos os parafusos confirmaram a tupla, o `Ack` método de Spout é invocado. O método `Ack` permite que o spout remova os dados armazenados em cache para reprodução.

* **Falha**: cada bolt pode chamar `this.ctx.Fail(tuple)` para indicar que o processamento falhou para uma tupla. A falha é propagada para o método `Fail` do spout, onde a tupla pode ser reproduzida usando os metadados armazenados em cache.

* **ID de Sequência**: ao emitir uma tupla, uma ID de sequência exclusiva poderá ser especificada. Esse valor identifica a tupla para o processamento de reprodução ( `Ack` e `Fail` ). Por exemplo, o Spout no projeto de **exemplo do Storm** usa a seguinte chamada de método ao emitir dados:

  `this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);`

  Esse código emite uma tupla que contém uma frase para o fluxo padrão, com o valor de ID de sequência contido em `lastSeqId` . Para este exemplo, `lastSeqId` é incrementado para cada tupla emitida.

Conforme demonstrado no projeto **Amostra do Storm**, se um componente for transacional, ele poderá ser definido no runtime com base na configuração.

## <a name="hybrid-topology-with-c-and-java"></a>Topologia híbrida com C# e Java

Você também pode usar as ferramentas do Data Lake para Visual Studio para criar topologias híbridas, em que alguns componentes são C# e outros são Java.

Para um exemplo de topologia híbrida, crie um projeto e selecione **Amostra Híbrida do Storm**. Esse tipo de exemplo demonstra os seguintes conceitos:

* **Spout Java** e o **parafuso C#**: definidos na `HybridTopology_javaSpout_csharpBolt` classe.

  Uma versão transacional é definida na `HybridTopologyTx_javaSpout_csharpBolt` classe.

* **Spout C#** e **parafuso Java**: definidos na `HybridTopology_csharpSpout_javaBolt` classe.

  Uma versão transacional é definida na `HybridTopologyTx_csharpSpout_javaBolt` classe.

  > [!NOTE]  
  > Essa versão também demonstra como usar o código Clojure de um arquivo de texto como um componente Java.

Para alternar a topologia que é usada quando o projeto é enviado, mova a instrução `[Active(true)]` para a topologia que você deseja usar antes de enviá-la para o cluster.

> [!NOTE]  
> Todos os arquivos Java necessários são fornecidos como parte desse projeto na pasta *JavaDependency* .

Considere o seguinte quando você estiver criando e enviando uma topologia híbrida:

* Use `JavaComponentConstructor` para criar uma instância da classe Java para um Spout ou um parafuso.

* Use `microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer` para serializar dados dentro ou fora de componentes Java de objetos Java para JSON.

* Ao enviar a topologia para o servidor, você deve usar a opção **Configurações adicionais** para especificar **Caminhos de arquivo Java**. O caminho especificado deve ser o diretório que tem os arquivos JAR que contêm as classes Java.

### <a name="azure-event-hubs"></a>Hubs de eventos do Azure

A versão 0.9.4.203 do SCP.NET introduz uma classe e um método novos especificamente para trabalhar com o spout do Hub de Eventos (um spout Java que lê de Hubs de Eventos). Quando você cria uma topologia que usa um Spout do hub de eventos (por exemplo, usando o modelo de **exemplo de leitor de EventHub do Storm** ), use as seguintes APIs:

* `EventHubSpoutConfig` classe: cria um objeto que contém a configuração para o componente Spout.

* `TopologyBuilder.SetEventHubSpout` método: Adiciona o componente Spout do hub de eventos à topologia.

> [!NOTE]  
> Você ainda deve usar o `CustomizedInteropJSONSerializer` para serializar dados produzidos pelo Spout.

## <a name="use-configurationmanager"></a>Usar o ConfigurationManager

Não use **ConfigurationManager** para recuperar valores de configuração dos componentes de Spout e de base. Isso pode causar uma exceção de ponteiro nulo. Em vez disso, passe a configuração do seu projeto para a topologia do Storm como um par de chave e valor no contexto de topologia. Cada componente que se baseia em valores de configuração deve recuperá-los no contexto durante a inicialização.

O seguinte código demonstra como recuperar esses valores:

```csharp
public class MyComponent : ISCPBolt
{
    // To hold configuration information loaded from context
    Configuration configuration;
    ...
    public MyComponent(Context ctx, Dictionary<string, Object> parms)
    {
        // Save a copy of the context for this component instance
        this.ctx = ctx;
        // If it exists, load the configuration for the component
        if(parms.ContainsKey(Constants.USER_CONFIG))
        {
            this.configuration = parms[Constants.USER_CONFIG] 
                as System.Configuration.Configuration;
        }
        // Retrieve the value of "Foo" from configuration
        var foo = this.configuration.AppSettings.Settings["Foo"].Value;
    }
    ...
}
```

Se você usar um método `Get` para retornar uma instância do seu componente, será preciso garantir que ele passe os parâmetros `Context` e `Dictionary<string, Object>` ao construtor. O seguinte exemplo é um método `Get` básico que passa corretamente esses valores:

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>Como atualizar o SCP.NET

As versões recentes do SCP.NET oferecem suporte à atualização de pacote por meio do NuGet. Quando uma nova atualização estiver disponível, você receberá uma notificação de atualização. Para verificar manualmente se há uma atualização, execute estas etapas:

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto e selecione **gerenciar pacotes NuGet**.

2. No Gerenciador de pacotes, selecione **Atualizações**. Se uma atualização para o pacote de suporte do SCP.NET estiver disponível, ela será listada. Selecione **Atualizar** para o pacote e, em seguida, na caixa de diálogo **alterações de visualização** , selecione **OK** para instalá-lo.

> [!IMPORTANT]  
> Se o seu projeto foi criado com uma versão anterior do SCP.NET que não usou o NuGet, você deverá executar as seguintes etapas para atualizar para uma versão mais recente:
>
> 1. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto e selecione **gerenciar pacotes NuGet**.
> 2. Usando o campo de **pesquisa** , procure e adicione, `Microsoft.SCP.Net.SDK` ao projeto.

## <a name="troubleshoot-common-issues-with-topologies"></a>Solucionar problemas comuns com topologias

### <a name="null-pointer-exceptions"></a>Exceções de ponteiro nulo

Quando você estiver usando uma topologia do C# com um cluster HDInsight baseado em Linux, os componentes de Spout e do componente que usam **ConfigurationManager** para ler as definições de configuração em tempo de execução podem retornar exceções de ponteiro nulo.

A configuração do seu projeto é passada para a topologia Storm como um par de chave/valor no contexto da topologia. Ele pode ser recuperado do objeto Dictionary que é passado para seus componentes quando eles são inicializados.

Para obter mais informações, consulte a seção [usar ConfigurationManager](#use-configurationmanager) deste documento.

### <a name="systemtypeloadexception"></a>System.TypeLoadException

Quando você estiver usando uma topologia do C# com um cluster HDInsight baseado em Linux, poderá chegar ao seguinte erro:

`System.TypeLoadException: Failure has occurred while loading a type.`

Esse erro ocorre quando você usa um binário que não é compatível com a versão do .NET à qual o mono dá suporte.

Para clusters HDInsight baseados em Linux, verifique se o projeto usa binários compilados para o .NET 4.5.

### <a name="test-a-topology-locally"></a>Testar uma topologia localmente

Embora seja fácil implantar uma topologia em um cluster, em alguns casos, talvez seja necessário testar uma topologia localmente. Use as etapas a seguir para executar e testar a topologia de exemplo neste artigo localmente em seu ambiente de desenvolvimento.

> [!WARNING]  
> Testes locais funcionam somente para topologias básicas exclusivamente em C#. Você não pode usar o teste local para topologias híbridas ou topologias que usam vários fluxos.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Propriedades**. Nas propriedades do projeto. Em seguida, altere o **tipo de saída** para **aplicativo de console**.

   ![Aplicativo do Storm do HDInsight, propriedades do projeto, tipo de saída](./media/apache-storm-develop-csharp-visual-studio-topology/hdi-output-type-window.png)

   > [!NOTE]
   > Lembre-se de alterar o **Tipo de saída** de volta para **Biblioteca de Classes** antes de implantar a topologia em um cluster.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **Novo Item**. Selecione **classe** e insira *LocalTest.cs* como o nome da classe. Por fim, selecione **Adicionar**.

1. Abra *LocalTest.cs* e adicione a seguinte `using` instrução na parte superior:

    ```csharp
    using Microsoft.SCP;
    ```

1. Use o código a seguir como o conteúdo da `LocalTest` classe:

    ```csharp
    // Drives the topology components
    public void RunTestCase()
    {
        // An empty dictionary for use when creating components
        Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();

        #region Test the spout
        {
            Console.WriteLine("Starting spout");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext spoutCtx = LocalContext.Get();
            // Get a new instance of the spout, using the local context
            Spout sentences = Spout.Get(spoutCtx, emptyDictionary);

            // Emit 10 tuples
            for (int i = 0; i < 10; i++)
            {
                sentences.NextTuple(emptyDictionary);
            }
            // Use LocalContext to persist the data stream to file
            spoutCtx.WriteMsgQueueToFile("sentences.txt");
            Console.WriteLine("Spout finished");
        }
        #endregion

        #region Test the splitter bolt
        {
            Console.WriteLine("Starting splitter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext splitterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);

            // Set the data stream to the data created by the spout
            splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                splitter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            splitterCtx.WriteMsgQueueToFile("splitter.txt");
            Console.WriteLine("Splitter bolt finished");
        }
        #endregion

        #region Test the counter bolt
        {
            Console.WriteLine("Starting counter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext counterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Counter counter = Counter.Get(counterCtx, emptyDictionary);

            // Set the data stream to the data created by splitter bolt
            counterCtx.ReadFromFileToMsgQueue("splitter.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                counter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            counterCtx.WriteMsgQueueToFile("counter.txt");
            Console.WriteLine("Counter bolt finished");
        }
        #endregion
    }
    ```

    Tome um momento para ler os comentários do código. Esse código usa `LocalContext` o para executar os componentes no ambiente de desenvolvimento. Ele persiste o fluxo de dados entre componentes em arquivos de texto na unidade local.

1. Abra *Program.cs* e adicione o seguinte código ao `Main` método:

    ```csharp
    Console.WriteLine("Starting tests");
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
    // Initialize the runtime
    SCPRuntime.Initialize();

    //If we are not running under the local context, throw an error
    if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
    {
        throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
    }
    // Create test instance
    LocalTest tests = new LocalTest();
    // Run tests
    tests.RunTestCase();
    Console.WriteLine("Tests finished");
    Console.ReadKey();
    ```

1. Salve as alterações e, em seguida, selecione **F5** ou escolha **depurar**  >  **Iniciar Depuração** para iniciar o projeto. Uma janela de console deve aparecer e o status do log colocado como progresso dos testes. Quando `Tests finished` aparecer, selecione qualquer chave para fechar a janela.

1. Use **Windows Explorer** para localizar o diretório que contém seu projeto. (Por exemplo: *C: \\ Users \\ \<your_user_name> \\ Source \\ repositórios \\ WordCount \\ WordCount*.) Em seguida, nesse diretório, abra *bin* e, em seguida, selecione *depurar*. Você deve ver os arquivos de texto que foram produzidos quando os testes foram executados: *sentences.txt*, *counter.txt* e *splitter.txt*. Abra cada arquivo de texto e inspecione os dados.

   > [!NOTE]  
   > Os dados da cadeia de caracteres persistem como uma matriz de valores decimais nesses arquivos. Por exemplo, `[[97,103,111]]` no arquivo **splitter.txt** representa a palavra *atrás*.

> [!NOTE]  
> Certifique-se de definir o **tipo de projeto** de volta para biblioteca de **classes** nas propriedades do projeto antes de implantá-lo em um Storm no cluster HDInsight.

### <a name="log-information"></a>Informações do log

É possível registrar em log com facilidade informações de seus componentes de topologia usando o `Context.Logger`. Por exemplo, o seguinte comando criará uma entrada do log de informações:

`Context.Logger.Info("Component started");`

As informações registradas em log podem ser exibidas no **Log do Serviço do Hadoop**, que é encontrado no **Gerenciador de Servidores**. Expanda a entrada para o seu Storm no cluster HDInsight e expanda **Log de Serviço do Hadoop**. Por fim, selecione o arquivo de log para exibir.

> [!NOTE]  
> Os logs são armazenados na conta de Armazenamento do Azure usada pelo seu cluster. Para exibir os logs no Visual Studio, você deve entrar na assinatura do Azure proprietária da conta de armazenamento.

### <a name="view-error-information"></a>Exibir informações de erro

Para exibir erros ocorridos em uma topologia em execução, use as etapas a seguir:

1. Em **Gerenciador de servidores**, clique com o botão direito do mouse no cluster Storm no HDInsight e selecione **Exibir topologias Storm**.

   Para **Spout** e **Bolts**, a coluna **Último Erro** contém informações sobre o último erro ocorrido.

2. Selecione a **ID do Spout** ou a **ID do parafuso** do componente que tem um erro listado. A página de detalhes exibe informações adicionais sobre o erro na seção **erros** na parte inferior da página.

3. Para obter mais informações, selecione uma **porta** na seção **executores** da página para ver o log do trabalho do Storm nos últimos minutos.

### <a name="errors-submitting-topologies"></a>Erros durante o envio de topologias

Se você vir vários erros ao enviar uma topologia para o HDInsight, poderá encontrar logs para os componentes do lado do servidor que lidam com o envio de topologia em seu cluster HDInsight. Para baixar esses logs, use o seguinte comando em uma linha de comando:

```cmd
scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .
```

Substitua *sshuser* pela conta de usuário SSH para o cluster. Substitua *ClusterName* pelo nome do cluster HDInsight. Para saber mais sobre como usar o `scp` e o `ssh` com o HDInsight, confira [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Envios podem falhar por vários motivos:

* O JDK não está instalado ou não está no caminho.
* As dependências de Java necessárias não estão incluídas no envio.
* As dependências são incompatíveis.
* Os nomes de topologia são duplicados.

Se o arquivo de log *hdinsight-scpwebapi. out* contiver um `FileNotFoundException` , a exceção poderá ser causada pelas seguintes condições:

* O JDK não está no caminho do ambiente de desenvolvimento. Verifique se o JDK está instalado no ambiente de desenvolvimento e que `%JAVA_HOME%/bin` está no caminho.
* Você está sem uma dependência de Java. Verifique se você está incluindo todos os arquivos. jar necessários como parte do envio.

## <a name="next-steps"></a>Próximas etapas

Para obter um exemplo de processamento de dados de Hubs de eventos, consulte [Processar eventos dos Hubs de Eventos do Azure com Storm no HDInsight](apache-storm-develop-csharp-event-hub-topology.md).

Para obter um exemplo de uma topologia de C# que divide os dados de fluxo em vários fluxos, consulte [Exemplo de Storm C#](https://github.com/Blackmist/csharp-storm-example).

Para descobrir mais informações sobre como criar topologias de C#, veja [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Para obter outras maneiras de trabalhar com o HDInsight e mais amostras do Storm no HDInsight, consulte o seguintes documentos:

**Microsoft SCP.NET**

* [Guia de programação do SCP para Apache Storm no Azure HDInsight](apache-storm-scp-programming-guide.md)

**Apache Storm no HDInsight**

* [Implantar e gerenciar topologias do Apache Storm no Azure HDInsight](apache-storm-deploy-monitor-topology-linux.md)
* [Exemplo de topologias Apache Storm no Microsoft Azure HDInsight](apache-storm-example-topology.md)

**Apache Hadoop no HDInsight**

* [Saiba mais sobre o Apache Hive e o HiveQL no Azure HDInsight?](../hadoop/hdinsight-use-hive.md)
* [Usar MapReduce no Apache Hadoop em HDInsight](../hadoop/hdinsight-use-mapreduce.md)

**Apache HBase no HDInsight**

* [Usar o Apache HBase no Azure HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md)
