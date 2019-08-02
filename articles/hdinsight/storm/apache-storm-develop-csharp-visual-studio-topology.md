---
title: Topologias Apache Storm com Visual Studio e C# – Azure HDInsight
description: Aprenda como criar topologias Storm em C#. Crie uma topologia de contagem de palavras simples no Visual Studio usando as ferramentas do Hadoop para Visual Studio.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 11/27/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 9d459f88cd252303384acb4a72d0af0cce6ee226
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67428463"
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>Desenvolver topologias C# para Apache Storm usando ferramentas do Data Lake para Visual Studio

Aprenda como criar uma topologia do C# Apache Storm usando as ferramentas do Azure Data Lake (Apache Hadoop) para o Visual Studio. Este documento orienta o processo de criar um projeto do Storm no Visual Studio, testá-lo localmente e implantá-lo em um Apache Storm no cluster do Azure HDInsight.

Você também aprende a criar topologias híbridas que usam componentes C# e Java.

> [!NOTE]  
> Embora as etapas neste documento dependam de um ambiente de desenvolvimento do Windows com Visual Studio, o projeto compilado pode ser enviado a um cluster HDInsight baseado em Windows ou Linux. Somente os clusters baseados em Linux criados depois de 28 de outubro de 2016 são compatíveis com as topologias do SCP.NET.

Para usar uma topologia do C# com um cluster baseado em Linux, você deverá atualizar o pacote NuGet Microsoft.SCP.Net.SDK usado pelo seu projeto para a versão 0.10.0.6 ou posterior. A versão do pacote também deve coincidir com a versão principal do Storm instalada no HDInsight.

| Versão do HDInsight | Versão do Apache Storm | Versão do SCP.NET | Versão Mono padrão |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.3 |0.10.x |0.10.x.x</br>(somente em HDInsight baseado no Windows) | ND |
| 3.4 | 0.10.0.x | 0.10.0.x | 3.2.8 |
| 3,5 | 1.0.2.x | 1.0.0.x | 4.2.1 |
| 3.6 | 1.1.0.x | 1.0.0.x | 4.2.8 |

> [!IMPORTANT]  
> As topologias C# em clusters baseados em Linux devem usar o .NET 4.5 e o Mono para execução no cluster HDInsight. Verificar [compatibilidade Mono](https://www.mono-project.com/docs/about-mono/compatibility/) para possíveis incompatibilidades.

## <a name="install-visual-studio"></a>Instalar Visual Studio

Você pode desenvolver topologias C# com SCP.NET, usando uma das seguintes versões do Visual Studio:

* Visual Studio 2012 com Atualização 4

* Visual Studio 2013 com Atualização 4 ou [Visual Studio 2013 Community](https://go.microsoft.com/fwlink/?LinkId=517284)

* Visual Studio 2015 ou [Visual Studio 2015 Community](https://go.microsoft.com/fwlink/?LinkId=532606)

* Visual Studio 2017 (qualquer edição)

## <a name="install-data-lake-tools-for-visual-studio"></a>Instalar ferramentas do Data Lake para Visual Studio

Para instalar as ferramentas do Data Lake para Visual Studio, siga as etapas em [Introdução ao uso das ferramentas do Data Lake para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="install-java"></a>Instalar o Java

Quando você envia uma topologia Storm do Visual Studio, o SCP.NET gera um arquivo zip que contém a topologia e as respectivas dependências. O Java é usado para criar esses arquivos zip porque ele usa um formato que é mais compatível com clusters baseados em Linux.

1. Instale o JDK (Java Developer Kit) 7 ou posterior em seu ambiente de desenvolvimento. Você pode obter o Oracle JDK da [Oracle](https://aka.ms/azure-jdks). Você também pode usar [outras distribuições do Java](https://openjdk.java.net/).

2. A variável de ambiente `JAVA_HOME` deve apontar para o diretório que contém o Java.

3. A variável de ambiente `PATH` deve incluir o diretório `%JAVA_HOME%\bin`.

Você pode usar o seguinte aplicativo de console C# para verificar se Java e JDK estão instalados corretamente:

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
> Nem todos os modelos funcionam com o HDInsight baseado em Linux. Pacotes NuGet usados pelos modelos podem não ser compatíveis com o Mono. Verifique o documento [Compatibilidade com o Mono](https://www.mono-project.com/docs/about-mono/compatibility/) e use o [Analisador de Portabilidade do .NET](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis) para identificar problemas em potencial.

Nas etapas neste documento, você usará o tipo de projeto de aplicativo Storm básico para criar uma topologia.

### <a name="apache-hbase-templates-notes"></a>Apache HBase templates notes

Para se comunicar com um HBase no cluster HDInsight, os modelos de leitor e gravador do HBase não usam a API Java do HBase, mas sim a API REST do HBase.

### <a name="eventhub-templates-notes"></a>Notas de modelos do EventHub

> [!IMPORTANT]  
> O componente spout do EventHub baseado em Java incluído com o modelo de Leitor do EventHub pode não funcionar com o Storm no HDInsight versão 3.5 ou posterior. Uma versão atualizada deste componente está disponível em [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib).

Para uma topologia de exemplo que usa esse componente e funciona com Storm no HDInsight 3.5, consulte [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

## <a name="create-a-c-topology"></a>Criar uma topologia C#

1. Abra o Visual Studio, selecione **Arquivo** > **Novo** e **Projeto**.

2. Na janela **Novo Projeto**, expanda **Instalados** > **Modelos** e selecione **Azure Data Lake**. Na lista de modelos, selecione **Aplicativo Storm**. Na parte inferior da tela, insira **WordCount** como o nome do aplicativo.

    ![Captura de tela da janela Novo Projeto](./media/apache-storm-develop-csharp-visual-studio-topology/new-project.png)

3. Após você criar o projeto, você deverá ter os seguintes arquivos:

   * **Program.cs**: Esse arquivo define a topologia para seu projeto. Uma topologia padrão consistindo em um spout e um bolt é criada por padrão.

   * **Spout.cs**: Um spout de exemplo que emite números aleatórios.

   * **Bolt.cs**: Um bolt de exemplo que mantém uma contagem de números emitidos pelo spout.

     Quando você cria o projeto, o NuGet baixa a versão mais recente do [pacote SCP.NET](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/).

     [!INCLUDE [scp.net version important](../../../includes/hdinsight-storm-scpdotnet-version.md)]

### <a name="implement-the-spout"></a>Implementar o spout

1. Abra **Spout.cs**. Os spouts são usados para ler os dados em uma topologia de uma fonte externa. Os principais componentes de um spout são:

   * **NextTuple**: Chamado pelo Storm quando o spout pode emitir novas tuplas.

   * **Ack** (apenas topologia transacional): Lida com confirmações iniciadas por outros componentes na topologia, para cadeias de identificação enviadas do spout. Confirmar uma tupla informa ao spout que ele foi processado com êxito por componentes downstream.

   * **Falha** : (apenas topologia transacional): Lida com tuplas que falham ao processar outros componentes na topologia. A implementação um método Fail permite emitir novamente a tupla para que ela possa ser processada novamente.

2. Substitua o conteúdo da classe **Spout** pelo texto a seguir: Esse spout emite uma frase para a topologia aleatoriamente.

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

1. Exclua o arquivo **Bolt.cs** existente do projeto.

2. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **Novo item**. Na lista, selecione **Storm Bolt** e digite **Splitter.cs** como o nome. Repita esse processo para criar um segundo bolt chamado **Counter.cs**.

   * **Splitter.cs**: Implementa um bolt que divide as frases em palavras individuais e emite um novo fluxo de palavras.

   * **Counter.cs**: Implementa um bolt que conta cada palavra e emite um novo fluxo de palavras e a contagem de cada palavra.

     > [!NOTE]  
     > Esses bolts leem e gravam em fluxos, mas você também pode usar um bolt para se comunicar com um banco de dados ou serviço.

3. Abra **Splitter.cs**. Ele tem apenas um método por padrão: **Executar**. O método Execute é chamado quando o bolt recebe uma tupla para processamento. Aqui, você pode ler e processar tuplas de entradas e emitir tuplas de saída.

4. Substitua o conteúdo da classe **Splitter** com o seguinte código:

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

5. Abra **Counter.cs** e substitua o conteúdo da classe pelo seguinte código:

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

![Diagrama de como os componentes são organizados](./media/apache-storm-develop-csharp-visual-studio-topology/wordcount-topology.png)

As frases são emitidas do spout e são distribuídas para instâncias do bolt Splitter. O bolt Splitter divide as frases em palavras, que são distribuídas para o bolt Contador.

Como a contagem de palavras é mantida localmente na instância Contador, você quer se certificar de que palavras específicas sejam transmitidas para a mesma instância do bolt Contador. Cada instância controla palavras específicas. Uma vez que o bolt Splitter não mantém nenhum estado, não importa qual instância do Splitter recebe qual frase.

Abra **Program.cs**. O método importante é **GetTopologyBuilder**, que é usado para definir a topologia enviada ao Storm. Substitua o conteúdo de **GetTopologyBuilder** pelo seguinte código para implementar a topologia descrita anteriormente:

```csharp
// Create a new topology named 'WordCount'
TopologyBuilder topologyBuilder = new TopologyBuilder("WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

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

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Enviar para o Storm no HDInsight**.

   > [!NOTE]  
   > Se solicitado, insira as credenciais de logon para sua assinatura do Azure. Se você tiver mais de uma assinatura, entre naquela que contém seu cluster Storm no HDInsight.

2. Selecione seu Storm no cluster HDInsight no menu suspenso **Cluster Storm** e selecione **Enviar**. Você pode monitorar se o envio obteve êxito ou não usando a janela **Saída** .

3. Depois que a topologia tiver sido enviada com êxito, as **Topologias Storm** para o cluster deverão aparecer. Selecione a topologia **WordCount** na lista para exibir informações sobre a topologia em execução.

   > [!NOTE]  
   > Você também pode exibir **topologias Storm** do **Gerenciador de Servidores**. Expanda **Azure** > **HDInsight**, clique com o botão direito do mouse em cluster do Storm no HDInsight e selecione **Exibir Topologias do Storm**.

    Para exibir informações sobre os componentes na topologia, clique duas vezes no componente no diagrama.

4. Na exibição **Resumo da Topologia**, selecione **Eliminar** para parar a topologia.

   > [!NOTE]  
   > As topologias Storm continuarão em execução até serem paradas ou até que o cluster seja excluído.

## <a name="transactional-topology"></a>Topologia transacional

A topologia anterior não é transacional. Os componentes na topologia não implementam a funcionalidade para reproduzir as mensagens. Para um exemplo de uma topologia transacional, crie um projeto e selecione **Amostra do Storm** como o tipo de projeto.

As topologias transacionais implementam o seguinte para suportar a reprodução de dados:

* **Cache de metadados**: O spout deve armazenar metadados sobre os dados emitidos para que os dados possam ser recuperados e emitidos novamente caso ocorra uma falha. Como os dados emitidos pela amostra são pequenos, os dados brutos de cada tupla são armazenados em um dicionário para reprodução.

* **Ack**: Cada bolt na topologia pode chamar `this.ctx.Ack(tuple)` para confirmar que processou uma tupla com êxito. Quando todos os bolts reconheceram a tupla, o `Ack` método do spout é invocado. O método `Ack` permite que o spout remova os dados armazenados em cache para reprodução.

* **Falha**: Cada bolt pode chamar `this.ctx.Fail(tuple)` para indicar que o processamento falhou para uma tupla. A falha é propagada para o método `Fail` do spout, onde a tupla pode ser reproduzida usando os metadados armazenados em cache.

* **ID de Sequência**: Ao emitir uma tupla, uma ID de sequência exclusiva poderá ser especificada. Esse valor identifica a tupla para o processamento da reprodução (Confirmação e Falha). Por exemplo, o spout do projeto **Amostra do Storm** usa o seguinte ao emitir dados:

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);

    Esse código emite uma tupla que contém uma frase para o fluxo padrão com o valor da ID de sequência contido em **lastSeqId**. Neste exemplo, **lastSeqId** é incrementado para todas as tuplas emitidas.

Conforme demonstrado no projeto **Amostra do Storm**, se um componente for transacional, ele poderá ser definido no tempo de execução com base na configuração.

## <a name="hybrid-topology-with-c-and-java"></a>Topologia híbrida com C# e Java

Você também pode usar as ferramentas do Data Lake para Visual Studio para criar topologias híbridas, em que alguns componentes são C# e outros são Java.

Para um exemplo de topologia híbrida, crie um projeto e selecione **Amostra Híbrida do Storm**. Esse tipo de exemplo demonstra os seguintes conceitos:

* **Spout Java** e **bolt C#** : Definidos em **HybridTopology_javaSpout_csharpBolt**.

    * Uma versão transacional é definida em **HybridTopologyTx_javaSpout_csharpBolt**.

* **Spout C#** e **bolt Java**: Definidos em **HybridTopology_csharpSpout_javaBolt**.

    * Uma versão transacional é definida em **HybridTopologyTx_csharpSpout_javaBolt**.

  > [!NOTE]  
  > Essa versão também demonstra como usar o código Clojure de um arquivo de texto como um componente Java.


Para alternar a topologia que é usada quando o projeto é enviado, mova a instrução `[Active(true)]` para a topologia que você deseja usar antes de enviá-la para o cluster.

> [!NOTE]  
> Todos os arquivos Java necessários são fornecidos como parte desse projeto na pasta **JavaDependency** .

Considere o seguinte ao criar e enviar uma topologia híbrida:

* Use **JavaComponentConstructor** para criar uma instância da classe Java para um spout ou bolt.

* Use **microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer** para serializar dados de entrada ou saída de componentes Java desde objetos Java até o JSON.

* Ao enviar a topologia para o servidor, você deve usar a opção **Configurações adicionais** para especificar **Caminhos de arquivo Java**. O caminho especificado deve ser o diretório que contém os arquivos JAR com suas classes Java.

### <a name="azure-event-hubs"></a>Hubs de eventos do Azure

A versão 0.9.4.203 do SCP.NET introduz uma classe e um método novos especificamente para trabalhar com o spout do Hub de Eventos (um spout Java que lê de Hubs de Eventos). Ao criar uma topologia que usa um spout de Hub de Eventos, use os seguintes métodos:

* Classe **EventHubSpoutConfig**: Cria um objeto que contém a configuração do componente spout.

* Método **TopologyBuilder.SetEventHubSpout**: Adiciona o componente spout do Hub de eventos à topologia.

> [!NOTE]  
> Você ainda deve usar o **CustomizedInteropJSONSerializer** para serializar os dados produzidos pelo spout.

## <a id="configurationmanager"></a>Usar o ConfigurationManager

Não use o **ConfigurationManager** para recuperar valores de configuração dos componentes bolt e spout. Isso pode causar uma exceção de ponteiro nulo. Em vez disso, a configuração do seu projeto é passada para a topologia Storm como um par de chave/valor no contexto da topologia. Cada componente que se baseia em valores de configuração deve recuperá-los no contexto durante a inicialização.

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
            this.configuration = parms[Constants.USER_CONFIG] as System.Configuration.Configuration;
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

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e escolha **Gerenciar Pacotes NuGet**.

2. No Gerenciador de pacotes, selecione **Atualizações**. Se uma atualização estiver disponível, ela será listada. Clique em **Atualização** referente ao pacote para instalá-la.

> [!IMPORTANT]  
> Se o seu projeto foi criado com uma versão anterior do SCP.NET que não usou o NuGet, você deverá executar as seguintes etapas para atualizar para uma versão mais recente:
>
> 1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e escolha **Gerenciar Pacotes NuGet**.
> 2. Usando o campo **Pesquisa**, faça a pesquisa e adicione **Microsoft.SCP.Net.SDK** ao projeto.

## <a name="troubleshoot-common-issues-with-topologies"></a>Solucionar problemas comuns com topologias

### <a name="null-pointer-exceptions"></a>Exceções de ponteiro nulo

Ao usar uma topologia C# com um cluster HDInsight baseado em Linux, os componentes bolt e spout que usam o **ConfigurationManager** para ler configurações em tempo de execução podem retornar exceções de ponteiro nulo.

A configuração do seu projeto é passada para a topologia Storm como um par de chave/valor no contexto da topologia. Ele pode ser recuperado do objeto de dicionário que é passado para os seus componentes quando eles são inicializados.

Para obter mais informações, consulte a seção [ConfigurationManager](#configurationmanager) deste documento.

### <a name="systemtypeloadexception"></a>System.TypeLoadException

Ao usar uma topologia C# com um cluster HDInsight baseado em Linux, você pode encontrar o seguinte erro:

    System.TypeLoadException: Failure has occurred while loading a type.

Esse erro geralmente ocorre quando você usa um binário que não é compatível com a versão do .NET à qual o Mono dá suporte.

Para clusters HDInsight baseados em Linux, verifique se o projeto usa binários compilados para o .NET 4.5.

### <a name="test-a-topology-locally"></a>Testar uma topologia localmente

Embora seja fácil implantar uma topologia em um cluster, em alguns casos poderá ser necessário testar uma topologia localmente. Use as etapas a seguir para executar e testar a topologia de exemplo neste artigo localmente em seu ambiente de desenvolvimento.

> [!WARNING]  
> Testes locais funcionam somente para topologias básicas exclusivamente em C#. Você não pode usar o teste local para topologias híbridas ou topologias que usam vários fluxos.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Propriedades**. Nas propriedades do projeto, altere **Tipo de saída** para **Aplicativo de Console**.

    ![Captura de tela de propriedades do projeto, com Tipo de saída realçado](./media/apache-storm-develop-csharp-visual-studio-topology/outputtype.png)

   > [!NOTE]
   > Lembre-se de alterar o **Tipo de saída** de volta para **Biblioteca de Classes** antes de implantar a topologia em um cluster.

2. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **Novo Item**. Selecione **Classe** e insira **LocalTest.cs** como o nome de classe. Por fim, clique em **Adicionar**.

3. Abra **LocalTest.cs** e adicione a seguinte instrução **using** na parte superior:

    ```csharp
    using Microsoft.SCP;
    ```

4. Use o seguinte código como o conteúdo da classe **LocalTest**:

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

    Tome um momento para ler os comentários do código. Esse código usa **LocalContext** para executar os componentes no ambiente de desenvolvimento, persistindo o fluxo de dados entre componentes para arquivos de texto na unidade local.

1. Abra **Program.cs** e adicione o seguinte ao método **Main**:

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

2. Salve as alterações e use **F5** ou selecione **Depurar** > **Iniciar Depuração** para iniciar o projeto. Uma janela de console deve aparecer e o status do log colocado como progresso dos testes. Quando **Testes concluídos** aparecer, pressione qualquer tecla para fechar a janela.

3. Use **Windows Explorer** para localizar o diretório que contém seu projeto. Por exemplo:  **C:\Users\<your_user_name>\Documents\Visual Studio 2013\Projects\WordCount\WordCount**. Nesse diretório, abra **Bin** e clique em **Depurar**. Você deve ver os arquivos de texto produzidos após a execução dos testes: sentences.txt, counter.txt e splitter.txt. Abra cada arquivo de texto e inspecione os dados.

   > [!NOTE]  
   > Os dados da cadeia de caracteres persistem como uma matriz de valores decimais nesses arquivos. Por exemplo, \[[97,103,111]] no arquivo **splitter.txt** é a palavra *and*.

> [!NOTE]  
> Defina **Tipo de projeto** novamente como **Biblioteca de Classes** antes de implantar em um Storm no cluster HDInsight.

### <a name="log-information"></a>Registrando informações no log

É possível registrar em log com facilidade informações de seus componentes de topologia usando o `Context.Logger`. Por exemplo, o seguinte comando criará uma entrada do log de informações:

```csharp
Context.Logger.Info("Component started");
```

As informações registradas em log podem ser exibidas no **Log do Serviço do Hadoop**, que é encontrado no **Gerenciador de Servidores**. Expanda a entrada para o seu Storm no cluster HDInsight e expanda **Log de Serviço do Hadoop**. Por fim, selecione o arquivo de log para exibir.

> [!NOTE]  
> Os logs são armazenados na conta de Armazenamento do Azure usada pelo seu cluster. Para exibir os logs no Visual Studio, você deve entrar na assinatura do Azure proprietária da conta de armazenamento.

### <a name="view-error-information"></a>Exibir informações de erro

Para exibir erros ocorridos em uma topologia em execução, use as etapas a seguir:

1. No **Gerenciador de Servidores**, clique com o botão direito do mouse no Storm do cluster HDInsight e selecione **Exibir topologias Storm**.

2. Para **Spout** e **Bolts**, a coluna **Último Erro** contém informações sobre o último erro ocorrido.

3. Selecione **ID do Spout** ou **ID do Bolt** para o componente com um erro listado. Na página de detalhes exibida, as informações adicionais do erro serão listadas na seção **Erros** na parte inferior da página.

4. Para obter mais informações, selecione uma **Porta** na seção **Executores** da página para ver o log de trabalho do Storm nos últimos minutos.

### <a name="errors-submitting-topologies"></a>Erros durante o envio de topologias

Se você encontrar erros ao enviar uma topologia ao HDInsight, poderá encontrar logs para os componentes do lado do servidor que lidam com o envio de topologia em seu cluster HDInsight. Para recuperar esses logs, use o seguinte comando em uma linha de comando:

    scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .

Substitua __sshuser__ pela conta de usuário SSH para o cluster. Substitua __clustername__ pelo nome do cluster HDInsight. Para saber mais sobre como usar o `scp` e o `ssh` com o HDInsight, confira [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Envios podem falhar por vários motivos:

* O JDK não está instalado ou não está no caminho.
* Dependências de Java necessárias não são incluídas no envio.
* Dependências incompatíveis.
* Nomes de topologia duplicados.

Se o log `hdinsight-scpwebapi.out` contém um `FileNotFoundException`, isso pode ser causado pelas seguintes condições:

* O JDK não está no caminho no ambiente de desenvolvimento. Verifique se o JDK está instalado no ambiente de desenvolvimento e que `%JAVA_HOME%/bin` está no caminho.
* Uma dependência de Java está ausente. Verifique se você está incluindo todos os arquivos .jar necessários como parte do envio.

## <a name="next-steps"></a>Próximas etapas

Para obter um exemplo de processamento de dados de Hubs de eventos, consulte [Processar eventos dos Hubs de Eventos do Azure com Storm no HDInsight](apache-storm-develop-csharp-event-hub-topology.md).

Para obter um exemplo de uma topologia de C# que divide os dados de fluxo em vários fluxos, consulte [Exemplo de Storm C#](https://github.com/Blackmist/csharp-storm-example).

Para descobrir mais informações sobre como criar topologias de C#, veja [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Para obter outras maneiras de trabalhar com o HDInsight e mais amostras do Storm no HDInsight, consulte o seguintes documentos:

**Microsoft SCP.NET**

* [Guia de programação do SCP](apache-storm-scp-programming-guide.md)

**Apache Storm no HDInsight**

* [Implantar e monitorar topologias com o Apache Storm no HDInsight](apache-storm-deploy-monitor-topology.md)
* [Topologias de exemplo para Apache Storm no HDInsight](apache-storm-example-topology.md)

**Apache Hadoop no HDInsight**

* [Use o Apache Hive com o Apache Hadoop no HDInsight](../hadoop/hdinsight-use-hive.md)
* [Use o Apache Pig com o Apache Hadoop no HDInsight](../hadoop/hdinsight-use-pig.md)
* [Usar o Apache Hadoop MapReduce com o Apache Hadoop no HDInsight](../hadoop/hdinsight-use-mapreduce.md)

**Apache HBase no HDInsight**

* [Introdução ao Apache HBase no HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md)
