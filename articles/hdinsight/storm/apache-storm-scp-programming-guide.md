---
title: Guia de programação do SCP.NET para Storm no HDInsight do Azure
description: Saiba como usar SCP.NET para criar topologias do Storm baseadas em .NET para usar com o a execução do Storm no Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 01/13/2020
ms.openlocfilehash: bd52157e2f0e20e9282d944b07f656c08d9e57da
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932637"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>Guia de programação do SCP para Apache Storm no Azure HDInsight

O SCP é uma plataforma para a criação de aplicativos de processamento de dados em tempo real, confiáveis, consistentes e de alto desempenho. Ele é criado com base em [Apache Storm](https://storm.incubator.apache.org/), que é um sistema de processamento de fluxo projetado por comunidades de software livre. Nathan Marz criou Storm. Ele foi publicado como código-fonte aberto pelo Twitter. O Storm usa [Apache ZooKeeper](https://zookeeper.apache.org/), que é outro projeto do Apache que permite a coordenação distribuída e o gerenciamento de estado altamente confiáveis.

O projeto SCP não apenas Storm no Windows, mas também extensões e personalizações adicionadas ao projeto para o ambiente do Windows. As extensões incluem a experiência do desenvolvedor .NET e as bibliotecas .NET. A personalização inclui a implantação baseada no Windows.

Com as extensões e a personalização, você não precisa bifurcar os projetos de software livre. Você pode usar ambientes derivados criados com base no Storm.

## <a name="processing-model"></a>Modelo de processamento

Os dados no SCP são modelados como fluxos contínuos de tuplas. Normalmente, as tuplas:

1. Fluir para uma fila.
1. São coletados e transformados pela lógica comercial hospedada dentro de uma topologia Storm.
1. Faça com que sua saída seja canalizada como tuplas para outro sistema SCP ou que seja confirmada em lojas como sistemas de arquivos distribuídos e bancos de dados como SQL Server.

![Um diagrama de uma fila que alimenta dados para processamento, que por sua vez alimenta um armazenamento de dados](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

No Storm, uma topologia de aplicativo define um grafo de computação. Cada nó em uma topologia contém a lógica de processamento. Os links entre nós indicam o fluxo de dados.

Os nós que inserem dados de entrada na topologia são chamados de _esgotamentos_. Você pode usá-los para sequenciar os dados. Os dados de entrada podem vir de uma fonte como logs de arquivo, um banco de dados transacional ou um contador de desempenho do sistema.

Os nós que têm fluxos de dados de entrada e saída são chamados de _parafusos_. Eles fazem a filtragem de dados, as seleções e a agregação reais.

O SCP dá suporte ao processamento de dados de melhores esforços, pelo menos uma vez e exatamente uma vez.

Em um aplicativo de processamento de fluxo distribuído, erros podem ocorrer durante o processamento de dados. Esses erros incluem uma interrupção de rede, uma falha de máquina ou um erro em seu código. O processamento pelo menos uma vez garante que todos os dados sejam processados pelo menos uma vez, repetindo automaticamente os mesmos dados quando ocorrer um erro.

O processamento pelo menos uma vez é simples e confiável e atende a muitos aplicativos. Mas quando um aplicativo requer contagem exata, o processamento pelo menos uma vez é insuficiente porque os mesmos dados podem ser reproduzidos na topologia do aplicativo. Nesse caso, o processamento exatamente uma vez garante que o resultado estará correto mesmo quando os dados forem reproduzidos e processados várias vezes.

O SCP permite que os desenvolvedores do .NET criem aplicativos de processamento de dados em tempo real ao usar uma JVM (Máquina Virtual Java) com Storm. Uma JVM e o .NET se comunicam por meio de soquetes TCP locais. Cada Spout/parafuso é um par de processos .NET/Java, onde a lógica do usuário é executada em um processo do .NET como um plug-in.

Para criar um aplicativo de processamento de dados na parte superior do SCP, siga estas etapas:

1. Projete e implemente esgotamentos para efetuar pull de dados de filas.
1. Projete e implemente os parafusos que processam os dados de entrada e salve-os em armazenamentos externos como um banco de dados.
1. Crie a topologia e, em seguida, envie-a e execute-a.

A topologia define os vértices e os dados que fluem entre eles. O SCP usa uma especificação de topologia e a implanta em um cluster Storm, onde cada vértice é executado em um nó lógico. O Agendador de tarefas do Storm cuida do failover e do dimensionamento.

Este artigo usa alguns exemplos simples para explicar como criar aplicativos de processamento de dados com o SCP.

## <a name="scp-plug-in-interface"></a>Interface de plug-in do SCP

Plug-ins do SCP são aplicativos autônomos. Eles podem ser executados dentro do Visual Studio durante o desenvolvimento e ser conectados ao pipeline Storm após a implantação de produção.

Escrever um plug-in SCP é o mesmo que escrever qualquer outro aplicativo de console do Windows. A plataforma SCP.NET declara algumas interfaces para Spout/parafuso. Seu código de plug-in implementa essas interfaces. A principal finalidade desse design é permitir que você se concentre em sua lógica de negócios e, ao mesmo tempo, permita que a plataforma SCP.NET manipule outras coisas.

Seu código de plug-in implementa uma das seguintes interfaces. Qual interface depende se a topologia é transacional ou não-Transaction e se o componente é um Spout ou um parafuso.

* **ISCPSpout**
* **ISCPBolt**
* **ISCPTxSpout**
* **ISCPBatchBolt**

### <a name="iscpplugin"></a>ISCPPlugin

**ISCPPlugin** é a interface comum para muitos plug-ins. Atualmente, é uma interface fictícia.

```csharp
public interface ISCPPlugin
{
}
```

### <a name="iscpspout"></a>ISCPSpout

**ISCPSpout** é a interface para um Spout não transacional.

```csharp
public interface ISCPSpout : ISCPPlugin
{
    void NextTuple(Dictionary<string, Object> parms);
    void Ack(long seqId, Dictionary<string, Object> parms); 
    void Fail(long seqId, Dictionary<string, Object> parms);
}
```

Quando **NextTuple** é chamado, seu código C# pode emitir uma ou mais tuplas. Se não houver nada a emitir, esse método deverá retornar sem emitir nada.

Os métodos **NextTuple**, **ACK** e **Fail** são todos chamados em um loop rígido em um único thread de um processo em C#. Quando não há tuplas a serem emitidas, tenha **NextTuple** suspensão por um curto período de tempo como 10 milissegundos. Essa suspensão ajuda a evitar desperdiçar disponibilidade da CPU.

Os métodos **ACK** e **Fail** são chamados somente quando um arquivo de especificação habilita o mecanismo de confirmação. O parâmetro *seqId* identifica a tupla que é confirmada ou falhou. Se a confirmação estiver habilitada em uma topologia não transacional, a seguinte função de **emissão** deverá ser usada em um Spout:

```csharp
public abstract void Emit(string streamId, List<object> values, long seqId);
```

Se uma topologia não transacional não der suporte à confirmação, a **confirmação** e a **falha** poderão ser deixadas como funções vazias.

O parâmetro de entrada *parms* nessas funções especifica um dicionário vazio e é reservado para uso futuro.

### <a name="iscpbolt"></a>ISCPBolt

**ISCPBolt** é a interface para um parafuso não transacional.

```csharp
public interface ISCPBolt : ISCPPlugin
{
void Execute(SCPTuple tuple);
}
```

Quando uma nova tupla está disponível, a função **Execute** é chamada para processá-la.

### <a name="iscptxspout"></a>ISCPTxSpout

**ISCPTxSpout** é a interface para um Spout transacional.

```csharp
public interface ISCPTxSpout : ISCPPlugin
{
    void NextTx(out long seqId, Dictionary<string, Object> parms);  
    void Ack(long seqId, Dictionary<string, Object> parms);         
    void Fail(long seqId, Dictionary<string, Object> parms);        
}
```

Assim como suas contrapartes não transacionais, **NextTx**, **ACK** e **Fail** são todas chamadas em um loop rígido em um único thread de um processo em C#. Quando não há tuplas a serem emitidas, tenha **NextTx** suspensão por um curto período de tempo como 10 milissegundos. Essa suspensão ajuda a evitar desperdiçar disponibilidade da CPU.

Quando **NextTx** é chamado para iniciar uma nova transação, o parâmetro de saída *seqId* identifica a transação. A transação também é usada em **ACK** e **falha**. O método **NextTx** pode emitir dados para o lado do Java. Os dados são armazenadas no ZooKeeper para dar suporte à reprodução. Como ZooKeeper tem capacidade limitada, seu código deve emitir somente metadados e não dados em massa em um Spout transacional.

Como o Storm repete automaticamente uma transação com **falha, geralmente** não será chamado. Mas se o SCP puder verificar os metadados emitidos por um Spout transacional, ele poderá chamar **falha** quando os metadados forem inválidos.

O parâmetro de entrada *parms* nessas funções especifica um dicionário vazio e é reservado para uso futuro.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt

**ISCPBatchBolt** é a interface para um raio transacional.

```csharp
public interface ISCPBatchBolt : ISCPPlugin
{
    void Execute(SCPTuple tuple);
    void FinishBatch(Dictionary<string, Object> parms);  
}
```

O método **Execute** é chamado quando uma nova tupla chega no parafuso. O método **FinishBatch** é chamado quando essa transação termina. O parâmetro de entrada *parms* é reservado para uso futuro.

Para uma topologia transacional, **StormTxAttempt** é uma classe importante. Ele tem dois membros: **TxId** e **tentativaid**. O membro **TxId** identifica uma transação específica. Uma transação pode ser tentada várias vezes se ela falhar e for reproduzida.

SCP.NET cria um novo objeto **ISCPBatchBolt** para processar cada objeto **StormTxAttempt** , assim como o Storm faz em Java. A finalidade desse design é dar suporte ao processamento de transações paralelas. Após a conclusão de uma tentativa de transação, o objeto **ISCPBatchBolt** correspondente é destruído e coletado como lixo.

## <a name="object-model"></a>Modelo de objeto

O SCP.NET também oferece um conjunto simples de objetos chave para que os desenvolvedores realizem a programação. Os objetos são **Context**, **StateStore** e **SCPRuntime**. Eles são discutidos nesta seção.

### <a name="context"></a>Contexto

O objeto de **contexto** fornece um ambiente em execução para um aplicativo. Cada instância de **ISCPPlugin** de **ISCPSpout**, **ISCPBolt**, **ISCPTxSpout** ou **ISCPBatchBolt** tem uma instância de **contexto** correspondente. A funcionalidade fornecida pelo **contexto** é dividida nestas duas partes:

* A parte estática, que está disponível em todo o processo C#
* A parte dinâmica, que está disponível apenas para a instância de **contexto** específica

### <a name="static-part"></a>Parte estática

```csharp
public static ILogger Logger = null;
public static SCPPluginType pluginType;
public static Config Config { get; set; }
public static TopologyContext TopologyContext { get; set; }  
```

O objeto de **agente** é fornecido para fins de log.

O objeto **plugintype** indica o tipo de plug-in do processo C#. Se o processo for executado no modo de teste local sem Java, o tipo de plug-in será **SCP_NET_LOCAL**.

```csharp
public enum SCPPluginType 
{
    SCP_NET_LOCAL = 0,
    SCP_NET_SPOUT = 1,
    SCP_NET_BOLT = 2,
    SCP_NET_TX_SPOUT = 3,
    SCP_NET_BATCH_BOLT = 4  
    }
```

A propriedade **config** Obtém parâmetros de configuração do lado do Java, que os transmite quando um plug-in C# é inicializado. Os parâmetros de **configuração** são divididos em duas partes: **stormConf** e **pluginConf**.

```csharp
public Dictionary<string, Object> stormConf { get; set; }  
public Dictionary<string, Object> pluginConf { get; set; }  
```

A parte **stormConf** é parâmetros definidos pelo Storm, e a parte **pluginConf** é parâmetros definidos pelo SCP. Veja um exemplo:

```csharp
public class Constants
{
    … …

    // constant string for pluginConf
    public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

    // constant string for stormConf
    public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";
    public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";
}
```

O tipo **TopologyContext** Obtém o contexto de topologia. É mais útil para vários componentes paralelos. Veja um exemplo:

```csharp
//demo how to get TopologyContext info
if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
{
    Context.Logger.Info("TopologyContext info:");
    TopologyContext topologyContext = Context.TopologyContext;
    Context.Logger.Info("taskId: {0}", topologyContext.GetThisTaskId());
    taskIndex = topologyContext.GetThisTaskIndex();
    Context.Logger.Info("taskIndex: {0}", taskIndex);
    string componentId = topologyContext.GetThisComponentId();
    Context.Logger.Info("componentId: {0}", componentId);
    List<int> componentTasks = topologyContext.GetComponentTasks(componentId);  
    Context.Logger.Info("taskNum: {0}", componentTasks.Count);
}
```

### <a name="dynamic-part"></a>Parte dinâmica

As interfaces a seguir são pertinentes a uma determinada instância de **contexto** , que é criada pela plataforma SCP.net e passada para seu código:

```csharp
// Declare the Output and Input Stream Schemas

public void DeclareComponentSchema(ComponentStreamSchema schema);

// Emit tuple to default stream.
public abstract void Emit(List<object> values);

// Emit tuple to the specific stream.
public abstract void Emit(string streamId, List<object> values);  
```

Para um Spout não transacional que dá suporte à confirmação, o seguinte método é fornecido:

```csharp
// for nontransactional spout that supports ack
public abstract void Emit(string streamId, List<object> values, long seqId);  
```

Um raio não transacional que dá suporte à confirmação deve chamar **ACK** explicitamente ou **falhar** com a tupla recebida. Ao emitir uma nova tupla, o parafuso também deve especificar as âncoras da tupla. Os seguintes métodos são fornecidos:

```csharp
public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);
public abstract void Ack(SCPTuple tuple);
public abstract void Fail(SCPTuple tuple);
```

### <a name="statestore"></a>StateStore

O objeto **StateStore** fornece serviços de metadados, geração de sequência monotônico e coordenação de espera livre. Você pode criar abstrações de simultaneidade distribuídas de nível superior em **StateStore**. Essas abstrações incluem bloqueios distribuídos, filas distribuídas, barreiras e serviços de transação.

Os aplicativos SCP podem usar o objeto **State** para serializar informações em [Apache ZooKeeper](https://zookeeper.apache.org/). Essa capacidade é especialmente valiosa para uma topologia transacional. Se um Spout transacional parar de responder e reinicializar, o **estado** poderá recuperar as informações necessárias do ZooKeeper e reiniciar o pipeline.

O objeto **StateStore** tem estes métodos principais:

```csharp
/// <summary>
/// Static method to retrieve a state store of the given path and connStr 
/// </summary>
/// <param name="storePath">StateStore path</param>
/// <param name="connStr">StateStore address</param>
/// <returns>Instance of StateStore</returns>
public static StateStore Get(string storePath, string connStr);

/// <summary>
/// Create a new state object in this state store instance
/// </summary>
/// <returns>State from StateStore</returns>
public State Create();

/// <summary>
/// Retrieve all states that were previously uncommitted, excluding all exited states
/// </summary>
/// <returns>Uncommitted states</returns>
public IEnumerable<State> GetUnCommitted();

/// <summary>
/// Get all the states in the StateStore
/// </summary>
/// <returns>All the states</returns>
public IEnumerable<State> States();

/// <summary>
/// Get state or registry object
/// </summary>
/// <param name="info">Registry name (registry only)</param>
/// <typeparam name="T">Type, registry or state</typeparam>
/// <returns>Return registry or state</returns>
public T Get<T>(string info = null);

/// <summary>
/// List all the committed states
/// </summary>
/// <returns>Registries containing the committed state </returns>
public IEnumerable<Registry> Committed();

/// <summary>
/// List all the exited states in the StateStore
/// </summary>
/// <returns>Registries containing the exited states</returns>
public IEnumerable<Registry> Aborted();

/// <summary>
/// Retrieve an existing state object from this state store instance 
/// </summary>
/// <returns>State from StateStore</returns>
/// <typeparam name="T">stateId, id of the State</typeparam>
public State GetState(long stateId)
```

O objeto de **estado** tem estes métodos principais:

```csharp
/// <summary>
/// Set the status of the state object to commit
/// </summary>
public void Commit(bool simpleMode = true);

/// <summary>
/// Set the status of the state object to exit
/// </summary>
public void Abort();

/// <summary>
/// Put an attribute value under the given key
/// </summary>
/// <param name="key">Key</param>
/// <param name="attribute">State attribute</param>
    public void PutAttribute<T>(string key, T attribute);

/// <summary>
/// Get the attribute value associated with the given key
/// </summary>
/// <param name="key">Key</param>
/// <returns>State attribute</returns>
    public T GetAttribute<T>(string key);
```

Quando **simplemode** é definido como **true**, o método **Commit** exclui o ZNode correspondente em ZooKeeper. Caso contrário, o método excluirá o ZNode atual e adicionará um novo nó no \_ caminho confirmado.

### <a name="scpruntime"></a>SCPRuntime

A classe **SCPRuntime** fornece os dois métodos a seguir:

```csharp
public static void Initialize();

public static void LaunchPlugin(newSCPPlugin createDelegate);  
```

O método **Initialize** Inicializa o ambiente de tempo de execução do SCP. Nesse método, o processo C# se conecta ao lado do Java para obter os parâmetros de configuração e o contexto da topologia.

O método **LaunchPlugin** inicia o loop de processamento de mensagens. Nesse loop, o plug-in C# recebe mensagens do lado do Java. Essas mensagens incluem tuplas e sinais de controle. Em seguida, o plug-in processa as mensagens, talvez chamando o método de interface fornecido pelo seu código.

O parâmetro de entrada para **LaunchPlugin** é um delegado. O método pode retornar um objeto que implementa a interface **ISCPSpout**, **ISCPBolt**, **ISCPTxSpout** ou **ISCPBatchBolt** .

```csharp
public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary<string, Object> parms);
```

Para **ISCPBatchBolt**, você pode obter um objeto **StormTxAttempt** do parâmetro *parms* e usá-lo para avaliar se a tentativa é uma tentativa reproduzida. A verificação de uma tentativa de reprodução geralmente é feita no parafuso de confirmação. O exemplo de HelloWorldTx mais adiante neste artigo demonstra essa verificação.

Plug-ins do SCP geralmente podem ser executados em dois modos: modo de teste local e modo normal.

#### <a name="local-test-mode"></a>Modo de teste local

Nesse modo, os plug-ins do SCP em seu código C# são executados dentro do Visual Studio durante a fase de desenvolvimento. Você pode usar a interface **ILocalContext** nesse modo. A interface fornece métodos para serializar as tuplas emitidas para arquivos locais e lê-las de volta na RAM.

```csharp
public interface ILocalContext
{
    List<SCPTuple> RecvFromMsgQueue();
    void WriteMsgQueueToFile(string filepath, bool append = false);  
    void ReadFromFileToMsgQueue(string filepath);
}
```

#### <a name="regular-mode"></a>Modo normal

Nesse modo, o processo Java do Storm executa os plug-ins do SCP. Aqui está um exemplo:

```csharp
namespace Scp.App.HelloWorld
{
public class Generator : ISCPSpout
{
    … …
    public static Generator Get(Context ctx, Dictionary<string, Object> parms)
    {
    return new Generator(ctx);
    }
}

class HelloWorld
{
    static void Main(string[] args)
    {
    /* Setting the environment variable here can change the log file name */
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "HelloWorld");

    SCPRuntime.Initialize();
    SCPRuntime.LaunchPlugin(new newSCPPlugin(Generator.Get));
    }
}
}
```

## <a name="topology-specification-language"></a>Linguagem de especificação de topologia

A especificação de topologia SCP é uma DSL (linguagem específica de domínio) para descrever e configurar topologias de SCP. Ele é baseado na [DSL Clojure do Storm](https://storm.incubator.apache.org/documentation/Clojure-DSL.html) e é estendido pelo SCP.

Você pode enviar especificações de topologia diretamente para um cluster Storm para execução por meio do comando **runSpec** .

SCP.NET adicionou as seguintes funções para definir topologias transacionais:

| Nova função | Parâmetros | Descrição |
| --- | --- | --- |
| **tx-topolopy** |*topology-name*<br />*spout-map*<br />*bolt-map* |Define uma topologia transacional com o nome da topologia, o mapa de definição de esgotamento e o mapa de definição dos parafusos. |
| **scp-tx-spout** |*exec-name*<br />*argumento*<br />*campos* |Define um Spout transacional. A função executa o aplicativo que é especificado pelo *exec-Name* e usa *args*.<br /><br />O parâmetro *Fields* especifica os campos de saída para o Spout. |
| **scp-tx-batch-bolt** |*exec-name*<br />*argumento*<br />*campos* |Define um parafuso de lote transacional. A função executa o aplicativo que é especificado pelo *exec-Name* e usa *args.*<br /><br />O parâmetro *Fields* especifica os campos de saída para o parafuso. |
| **scp-tx-commit-bolt** |*exec-name*<br />*argumento*<br />*campos* |Define um parafuso de confirmação transacional. A função executa o aplicativo que é especificado pelo *exec-Name* e usa *args*.<br /><br />O parâmetro *Fields* especifica os campos de saída para o parafuso. |
| **nontx-topologia** |*topology-name*<br />*spout-map*<br />*bolt-map* |Define uma topologia não transacional com o nome da topologia, o mapa de definição de esgotamento e o mapa de definição dos parafusos. |
| **scp-spout** |*exec-name*<br />*argumento*<br />*campos*<br />*parameters* |Define um Spout não transacional. A função executa o aplicativo que é especificado pelo *exec-Name* e usa *args*.<br /><br />O parâmetro *Fields* especifica os campos de saída para o Spout.<br /><br />O parâmetro *Parameters* é opcional. Use-o para especificar parâmetros como "transtransactioning. ACK. Enabled". |
| **scp-bolt** |*exec-name*<br />*argumento*<br />*campos*<br />*parameters* |Define um parafuso não transacional. A função executa o aplicativo que é especificado pelo *exec-Name* e usa *args*.<br /><br />O parâmetro *Fields* especifica os campos de saída para o parafuso<br /><br />O parâmetro *Parameters* é opcional. Use-o para especificar parâmetros como "transtransactioning. ACK. Enabled". |

SCP.NET define as seguintes palavras-chave:

| Palavra-chave | Descrição |
| --- | --- |
| **: nome** |O nome da topologia |
| **: topologia** |A topologia que usa as funções na tabela anterior e funções internas |
| **:p** |A dica de paralelismo para cada Spout ou parafuso |
| **: config** |Se deseja configurar parâmetros ou atualizar os existentes |
| **: esquema** |O esquema do fluxo |

SCP.NET também define esses parâmetros usados com frequência:

| Parâmetro | Descrição |
| --- | --- |
| "plugin.name" |O nome do arquivo. exe do plug-in do C# |
| "plugin.args" |Os argumentos de plug-in |
| "output.schema" |O esquema de saída |
| "nontransactional.ack.enabled" |Se a confirmação está habilitada para uma topologia não transacional |

O comando **runSpec** é implantado junto com os bits. Aqui está o uso do comando:

```csharp
.\bin\runSpec.cmd
usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target
```

O parâmetro *Resource-dir* é opcional. Especifique-o quando desejar conectar um aplicativo C#. O diretório especificado contém o aplicativo, as dependências e as configurações.

O parâmetro *classpath* também é opcional. Especifica o classpath Java se o arquivo de especificação contiver um Spout ou um parafuso Java.

## <a name="miscellaneous-features"></a>Recursos diversos

### <a name="input-and-output-schema-declarations"></a>Declarações de esquema de entrada e saída

Seus processos em C# podem emitir tuplas. Para fazer isso, a plataforma serializa tuplas em objetos **byte []** e transfere os objetos para o lado do Java. Em seguida, o Storm transfere essas tuplas para os destinos.

Em componentes downstream, os processos C# recebem tuplas de volta do lado do Java e os convertem para os tipos originais da plataforma. Todas essas operações estão ocultas pela plataforma.

Para dar suporte à serialização e desserialização, seu código precisa declarar o esquema da entrada e da saída. O esquema é definido como um dicionário. A ID do fluxo é a chave do dicionário. O valor da chave é o tipo das colunas. Um componente pode declarar vários fluxos.

```csharp
public class ComponentStreamSchema
{
    public Dictionary<string, List<Type>> InputStreamSchema { get; set; }
    public Dictionary<string, List<Type>> OutputStreamSchema { get; set; }
    public ComponentStreamSchema(Dictionary<string, List<Type>> input, Dictionary<string, List<Type>> output)
    {
        InputStreamSchema = input;
        OutputStreamSchema = output;
    }
}
```

A função a seguir é adicionada a um objeto de **contexto** :

```csharp
public void DeclareComponentSchema(ComponentStreamSchema schema)
```

Os desenvolvedores devem garantir que as tuplas emitidas obedeçam ao esquema definido para um fluxo. Caso contrário, o sistema gerará uma exceção de tempo de execução.

### <a name="multistream-support"></a>Suporte a MultiStream

O SCP permite que seu código emita ou receba de vários fluxos distintos ao mesmo tempo. O objeto de **contexto** reflete esse suporte como o parâmetro de ID de fluxo opcional do método de **emissão** .

Dois métodos no objeto de **contexto** SCP.net foram adicionados. Eles emitem uma ou mais tuplas para fluxos específicos. O parâmetro *streamid* é uma cadeia de caracteres. Seu valor deve ser o mesmo no código C# e na especificação de definição de topologia.

```csharp
/* Emit tuple to the specific stream. */
public abstract void Emit(string streamId, List<object> values);

/* for nontransactional spout only */
public abstract void Emit(string streamId, List<object> values, long seqId);
```

Emitir para um fluxo inexistente causa exceções de tempo de execução.

### <a name="fields-grouping"></a>Agrupamento de campos

O agrupamento de campos internos no Storm não funciona corretamente no SCP.NET. No lado do proxy Java, o tipo de dados de todos os campos é, na verdade, **byte []**. O agrupamento de campos usa o código hash do objeto **byte []** para fazer o agrupamento. O código hash é o endereço desse objeto na RAM. Portanto, o agrupamento ficará incorreto para objetos multibyte que compartilham o mesmo conteúdo, mas não o mesmo endereço.

SCP.NET adiciona um método de agrupamento personalizado e usa o conteúdo do objeto **byte []** para fazer o agrupamento. Em um arquivo de especificação, a sintaxe é semelhante a este exemplo:

```csharp
(bolt-spec
    {
        "spout_test" (scp-field-group :non-tx [0,1])
    }
    …
)
```

No arquivo de especificação anterior:

* `scp-field-group` Especifica que o agrupamento é um agrupamento de campos personalizado implementado pelo SCP.
* `:tx` ou `:non-tx` especifica se a topologia é transacional. Você precisa dessas informações porque o índice inicial é diferente entre as topologias transacionais e não transações.
* `[0,1]` Especifica um conjunto de hash de IDs de campo que começam com zero.

### <a name="hybrid-topology"></a>Topologia híbrida

O código do Storm nativo é escrito em Java. O SCP.NET tem um Storm aprimorado para permitir que você escreva código C# para lidar com a lógica de negócios. Mas o SCP.NET também dá suporte a topologias híbridas, que contêm não apenas esgotamentos/parafusos do C#, mas também esgotamentos/limites de Java.

### <a name="specify-java-spoutbolt-in-a-specification-file"></a>Especificar Spout/parafuso do Java em um arquivo de especificação

Você pode usar o **SCP-Spout** e o **SCP-raio** em um arquivo de especificação para especificar esgotamentos e parafusos de Java. Veja um exemplo:

```csharp
(spout-spec 
  (microsoft.scp.example.HybridTopology.Generator.)
  :p 1)
```

Aqui `microsoft.scp.example.HybridTopology.Generator` está o nome da classe Java Spout.

### <a name="specify-the-java-classpath-in-a-runspec-command"></a>Especificar o classpath Java em um comando runSpec

Se você quiser enviar uma topologia que contenha esgotamentos ou parafusos de Java, primeiro compile-os para produzir arquivos JAR. Em seguida, especifique o classpath Java que contém os arquivos JAR ao enviar a topologia. Veja um exemplo:

```csharp
bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*
```

Aqui `examples\HybridTopology\java\target\` está a pasta que contém o arquivo JAR Spout/pote do Java.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Serialização e desserialização entre Java e C #

Um componente SCP inclui o lado do Java e o lado do C#. Para interagir com esgotamentos/limites de Java nativos, a serialização e a desserialização devem ocorrer entre o lado do Java e o lado do C#, conforme ilustrado no grafo a seguir:

![Diagrama do componente Java enviando para o componente SCP, que então envia para um componente Java diferente](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

#### <a name="serialization-in-the-java-side-and-deserialization-in-the-c-side"></a>Serialização no lado do Java e desserialização no lado do C#

Primeiro, forneça a implementação padrão para serialização no lado do Java e desserialização no lado do C#.

Especifique o método de serialização do lado do Java em um arquivo de especificação.

```csharp
(scp-bolt
    {
        "plugin.name" "HybridTopology.exe"
        "plugin.args" ["displayer"]
        "output.schema" {}
        "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
    })
```

Especifique o método de desserialização do lado do C# em seu código C#.

```csharp
Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
inputSchema.Add("default", new List<Type>() { typeof(Person) });
this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());
```  

Se o tipo de dados não for muito complexo, essa implementação padrão deverá lidar com a maioria dos casos. Aqui estão os casos em que você pode conectar sua própria implementação:

* O tipo de dados é muito complexo para a implementação padrão.
* O desempenho de sua implementação padrão não atende às suas necessidades.

A interface de serialização no lado do Java é definida como:

```csharp
public interface ICustomizedInteropJavaSerializer {
    public void prepare(String[] args);
    public List<ByteBuffer> serialize(List<Object> objectList);
}
```

A interface de desserialização no lado do C# é definida como:

```csharp
public interface ICustomizedInteropCSharpDeserializer
{
    List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
}
```

#### <a name="serialization-in-the-c-side-and-deserialization-in-the-java-side"></a>Serialização no lado do C# e desserialização no lado do Java

Especifique o método de serialização do lado do C# em seu código C#.

```csharp
this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
```

Especifique o método de desserialização do lado do Java em um arquivo de especificação.

```csharp
(scp-spout
   {
     "plugin.name" "HybridTopology.exe"
     "plugin.args" ["generator"]
     "output.schema" {"default" ["person"]}
     "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
   }
)
```

Aqui, `"microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer"` é o nome do desserializador e `"microsoft.scp.example.HybridTopology.Person"` é a classe de destino para a qual os dados são desserializados.

Você também pode conectar sua própria implementação de um serializador C# e um desserializador Java.

Esse código é a interface para o serializador do C#:

```csharp
public interface ICustomizedInteropCSharpSerializer
{
    List<byte[]> Serialize(List<object> dataList);
}
```

Esse código é a interface para o desserializador Java:

```csharp
public interface ICustomizedInteropJavaDeserializer {
    public void prepare(String[] targetClassNames);
    public List<Object> Deserialize(List<ByteBuffer> dataList);
}
```

## <a name="scp-host-mode"></a>Modo de host SCP

Nesse modo, você pode compilar seu código como uma DLL e usar SCPHost.exe conforme fornecido pelo SCP para enviar uma topologia. Um arquivo de especificação é semelhante a este código:

```csharp
(scp-spout
  {
    "plugin.name" "SCPHost.exe"
    "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
    "output.schema" {"default" ["sentence"]}
  })
```

Aqui, `"plugin.name"` é especificado como `"SCPHost.exe"` , que é fornecido pelo SCP SDK. SCPHost.exe aceita três parâmetros na seguinte ordem:

1. O nome da DLL, que está `"HelloWorld.dll"` neste exemplo.
1. O nome da classe, que está `"Scp.App.HelloWorld.Generator"` neste exemplo.
1. O nome de um método estático público, que pode ser invocado para obter uma instância de **ISCPPlugin**.

No modo de host, compile seu código como uma DLL para invocação pela plataforma SCP. Como a plataforma pode obter controle total de toda a lógica de processamento, recomendamos que você envie a topologia no modo de host do SCP. Isso simplifica a experiência de desenvolvimento. Ele também oferece mais flexibilidade e melhor compatibilidade com versões posteriores.

## <a name="scp-programming-examples"></a>Exemplos de programação do SCP

### <a name="helloworld"></a>HelloWorld

O exemplo de HelloWorld simples a seguir mostra um gosto de SCP.NET. Ele usa uma topologia não transacional com um Spout chamado **Generator** e dois pinos chamados **Splitter** e **Counter**. O **gerador** Spout gera sentenças aleatoriamente e emite essas sentenças para o **divisor**. O **parafuso do** separador divide as frases em palavras e emite essas palavras para o parafuso do **contador** . O parafuso do **contador** usa um dicionário para registrar a ocorrência de cada palavra.

Este exemplo tem dois arquivos de especificação: HelloWorld. spec e HelloWorld \_ EnableAck. spec. O código C# pode descobrir se a confirmação está habilitada obtendo o `pluginConf` objeto do lado do Java.

```csharp
/* demo how to get pluginConf info */
if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
{
    enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
}
Context.Logger.Info("enableAck: {0}", enableAck);
```

Se a confirmação estiver habilitada no Spout, um dicionário armazenará em cache as tuplas que não foram confirmadas. Se `Fail` for chamado, a tupla com falha será reproduzida.

```csharp
public void Fail(long seqId, Dictionary<string, Object> parms)
{
    Context.Logger.Info("Fail, seqId: {0}", seqId);
    if (cachedTuples.ContainsKey(seqId))
    {
        /* get the cached tuple */
        string sentence = cachedTuples[seqId];

        /* replay the failed tuple */
        Context.Logger.Info("Re-Emit: {0}, seqId: {1}", sentence, seqId);
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), seqId);
    }
    else
    {
        Context.Logger.Warn("Fail(), can't find cached tuple for seqId {0}!", seqId);
    }
}
```

### <a name="helloworldtx"></a>HelloWorldTx

O exemplo de HelloWorldTx a seguir demonstra como implementar a topologia transacional. O exemplo tem um Spout chamado **gerador**, um parafuso de lote chamado **contagem parcial** e um parafuso de confirmação chamado **contagem-soma**. O exemplo também tem três arquivos de texto existentes: DataSource0.txt, DataSource1.txt e DataSource2.txt.

Em cada transação, o **gerador** Spout seleciona aleatoriamente dois arquivos dos três arquivos existentes e emite os dois nomes de arquivo para o parafuso de **contagem parcial** . O parafuso de **contagem parcial** :

1. Obtém um nome de arquivo da tupla recebida.
1. Abre o arquivo correspondente.
1. Conta o número de palavras no arquivo.
1. Emite a contagem de palavras ao parafuso da **soma da contagem** .

O bolt **count-sum** resume a contagem total.

Para atingir a semântica exatamente uma vez, o parafuso de confirmação **Count-Sum** precisa avaliar se é uma transação reproduzida. Neste exemplo, ele tem a seguinte variável de membro estático:

```csharp
public static long lastCommittedTxId = -1; 
```

Quando uma instância de **ISCPBatchBolt** é criada, ela obtém o valor do `txAttempt` objeto dos parâmetros de entrada.

```csharp
public static CountSum Get(Context ctx, Dictionary<string, Object> parms)
{
    /* for transactional topology, we can get txAttempt from the input parms */
    if (parms.ContainsKey(Constants.STORM_TX_ATTEMPT))
    {
        StormTxAttempt txAttempt = (StormTxAttempt)parms[Constants.STORM_TX_ATTEMPT];
        return new CountSum(ctx, txAttempt);
    }
    else
    {
        throw new Exception("null txAttempt");
    }
}
```

Quando `FinishBatch` for chamado, `lastCommittedTxId` será atualizado se não for uma transação reproduzida.

```csharp
public void FinishBatch(Dictionary<string, Object> parms)
{
    /* judge whether it is a replayed transaction */
    bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

    if (!replay)
    {
        /* If it is not replayed, update the totalCount and lastCommittedTxId value */
        totalCount = totalCount + this.count;
        lastCommittedTxId = this.txAttempt.TxId;
    }
    … …
}
```

### <a name="hybridtopology"></a>HybridTopology

Essa topologia contém um Spout Java e um parafuso de C#. Ele usa a implementação padrão de serialização e desserialização fornecida pela plataforma SCP. Consulte o arquivo HybridTopology. spec na pasta examples \\ HybridTopology para obter os detalhes do arquivo de especificação. Consulte também SubmitTopology.bat para saber como especificar o classpath Java.

### <a name="scphostdemo"></a>SCPHostDemo

Este exemplo é basicamente o mesmo que HelloWorld. A única diferença é que seu código é compilado como uma DLL e a topologia é enviada usando SCPHost.exe. Consulte a seção modo de host SCP para obter uma explicação mais detalhada.

## <a name="next-steps"></a>Próximas etapas

Para obter exemplos de topologias de Apache Storm criadas usando o SCP, consulte os seguintes artigos:

* [Desenvolver topologias C# para o Apache Storm no HDInsight usando o Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Processar eventos dos Hubs de Eventos do Azure com o Apache Storm no HDInsight](apache-storm-develop-csharp-event-hub-topology.md)
* [Processe os dados do sensor do veículo dos Hubs de Eventos usando o Apache Storm no HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [ETL (extração, transformação e carregamento) dos hubs de eventos do Azure para o Apache HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
