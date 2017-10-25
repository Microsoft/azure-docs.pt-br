---
title: "Guia de programação do SCP.NET | Microsoft Docs"
description: Saiba como usar SCP.NET para criar topologias do Storm baseadas em .NET para usar com o Storm no HDInsight.
services: hdinsight
documentationcenter: 
author: raviperi
manager: jhubbard
editor: cgronlun
ms.assetid: 34192ed0-b1d1-4cf7-a3d4-5466301cf307
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/16/2016
ms.author: raviperi
ms.openlocfilehash: 3d76aebd2a1fd729c8e0639e6afcbde4c3fb752b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="scp-programming-guide"></a>Guia de programação do SCP
SCP é uma plataforma para a criação de aplicativos de processamento de dados em tempo real, confiáveis, consistentes e de alto desempenho. Ele é baseado no [Apache Storm](http://storm.incubator.apache.org/) , um sistema de processamento de fluxo projetado pelas comunidades de OSS. O Storm foi projetado por Nathan Marz e tornou-se um software livre por meio do Twitter. Ele aproveita o [Apache ZooKeeper](http://zookeeper.apache.org/), outro projeto da Apache, para habilitar uma coordenação distribuída e gerenciamento de estado altamente confiáveis. 

O projeto SCP não apenas compatibilizou o Storm no Windows como também incluiu extensões e personalização para o ecossistema do Windows. As extensões incluem a experiência dos desenvolvedores e as bibliotecas do .NET; a personalização inclui a implantação baseada em Windows. 

A extensão e a personalização são realizadas de forma que não precisamos bifurcar projetos de OSS e podemos utilizar ecossistemas derivados baseados no Storm.

## <a name="processing-model"></a>Modelo de processamento
Os dados no SCP são modelados como fluxos contínuos de tuplas. Geralmente, as tuplas são transmitidas para alguma fila primeiro, depois, são apanhadas e transformadas pela lógica de negócios hospedada em uma topologia do Storm; por fim, o resultado pode ser conectado como tuplas a outro sistema do SCP ou ser confirmado para armazenamentos, como o sistema de arquivos distribuído, ou bancos de dados, como o SQL Server.

![Um diagrama de uma fila que fornece dados para processamento, alimentando um armazenamento de dados](media/hdinsight-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

No Storm, a topologia de um aplicativo define um gráfico de computação. Cada nó em uma topologia contém a lógica de processamento, e os links entre os nós indicam o fluxo de dados. Os nós que injetam dados de entrada na topologia são chamados Spouts, que podem ser usados para sequenciar os dados. Os dados de entrada podem residir em arquivos de log, banco de dados transacional e contadores de desempenho do sistema, entre outros. Os nós com fluxos de dados de entrada e de saída são chamados de Bolts, que realizam a filtragem, seleções e agregação reais dos dados.

O SCP oferece suporte aos melhores esforços de processamento de dados, pelo menos uma vez e exatamente uma vez. Em um aplicativo de processamento de streaming distribuído, vários erros podem ocorrer durante o processamento de dados, como interrupção da rede, falha do computador ou erro de código do usuário, entre outros. O processamento ao menos uma vez assegura que todos os dados serão processados pelo menos uma vez, reproduzindo automaticamente os mesmos dados quando o erro ocorre. O processamento de pelo menos uma vez é simples e confiável e bastante adequado a muitos aplicativos. No entanto, quando o aplicativo exige uma contagem exata, por exemplo, o processamento de pelo menos uma vez é insuficiente, já que os mesmos dados podem ser reproduzidos na topologia do aplicativo. Nesse caso, o processamento de exatamente uma vez é projetado para assegurar que o resultado esteja correto mesmo quando os dados possam ser reproduzidos e processados várias vezes.

O SCP permite que os desenvolvedores do .NET desenvolvam aplicativos de processamento de dados em tempo real e aproveitem o Storm baseado em JVM (Máquina Virtual Java) ao mesmo tempo. O .NET e o JVM se comunicam por meio do soquete local do TCP. Basicamente, cada Spout/Bolt é um par de processos do .Net/Java, em que a lógica do usuário é executada no processo do .Net como um plugin.

Para criar um aplicativo de processamento de dados baseado no SCP, várias etapas são necessárias:

* Projete e implemente os Spouts para extrair dados da fila.
* Projete e implemente os Bolts para processar os dados de entrada e salvar os dados em armazenamentos externos, como um banco de dados.
* Projete a topologia e depois envie-a e execute-a. A topologia define os vértices e os fluxos de dados entre os vértices. O SCP assume a especificação da topologia e a implanta em um cluster do Storm, em que cada vértice é executado em um nó lógico. O failover e o dimensionamento serão realizados pelo agendador de tarefas do Storm.

Este documento usará exemplos simples para mostrar como criar aplicativos de processamento de dados com o SCP.

## <a name="scp-plugin-interface"></a>Interface do plug-in do SCP
Plugins (ou aplicativos) do SCP são EXEs independentes que podem ser executados dentro do Visual Studio durante a fase de desenvolvimento e podem ser conectados ao pipeline do Storm após a implantação na produção. Escrever o plugin do SCP é igual escrever qualquer outro aplicativo do console do Windows padrão. A plataforma do SCP.NET declara alguma interface para spout/bolt, e o código de plugin do usuário deve implementar essas interfaces. O principal propósito desse design é que o usuário possa se concentrar em sua própria lógica de negócios, e deixar que as outras coisas sejam realizadas pela plataforma do SCP.NET.

O código do plugin do usuário deve implementar uma das seguintes interfaces, dependendo de se a topologia é transacional ou não transacional e se o componente é um spout ou bolt.

* ISCPSpout
* ISCPBolt
* ISCPTxSpout
* ISCPBatchBolt

### <a name="iscpplugin"></a>ISCPPlugin
ISCPPlugin é a interface comum para todos os tipos de plugins. Atualmente, trata-se de uma interface fictícia.

    public interface ISCPPlugin 
    {
    }

### <a name="iscpspout"></a>ISCPSpout
ISCPSpout é a interface para spout não transacional.

     public interface ISCPSpout : ISCPPlugin                    
     {
         void NextTuple(Dictionary<string, Object> parms);         
         void Ack(long seqId, Dictionary<string, Object> parms);   
         void Fail(long seqId, Dictionary<string, Object> parms);  
     }

Quando `NextTuple()` é chamado, o código do usuário C\# pode emitir uma ou mais tuplas. Se não houver nada a ser emitido, esse método deverá retornar sem emitir nada. Observe que `NextTuple()`, `Ack()` e `Fail()` são chamados em um loop coeso em um único thread no processo C\#. Quando não há tuplas a serem emitidas, é recomendável fazer com que NextTuple entre em repouso por um curto período de tempo (como 10 milissegundos) para não sobrecarregar a CPU.

`Ack()` e `Fail()` serão chamados somente quando o mecanismo de reconhecimento estiver habilitado no arquivo de especificações. O `seqId` é usado para identificar a tupla reconhecida ou com falha. Portanto, se o reconhecimento for habilitado em uma topologia não transacional, a seguinte função de emissão deverá ser usada no Spout:

    public abstract void Emit(string streamId, List<object> values, long seqId); 

Se não há suporte para o ack na topologia não transacional, `Ack()` e `Fail()` podem ser deixadas como funções vazias.

Os parâmetros de entrada `parms` nessas funções são apenas um Dicionário vazio e são reservados para uso futuro.

### <a name="iscpbolt"></a>ISCPBolt
ISCPBolt é a interface para bolt não transacional.

    public interface ISCPBolt : ISCPPlugin 
    {
    void Execute(SCPTuple tuple);           
    }

Quando uma nova tupla está disponível, a função `Execute()` é chamada para processá-la.

### <a name="iscptxspout"></a>ISCPTxSpout
ISCPTxSpout é a interface para spout transacional.

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);  
        void Ack(long seqId, Dictionary<string, Object> parms);         
        void Fail(long seqId, Dictionary<string, Object> parms);        
    }

Assim como suas contrapartes não transacionais, `NextTx()`, `Ack()` e `Fail()` são chamados em um loop coeso em um único thread no processo C\#. Quando não há dados a serem emitidos, é recomendável fazer com que `NextTx` entre em repouso por um curto período de tempo (como 10 milissegundos) para não sobrecarregar a CPU.

O `NextTx()` é chamado para iniciar uma nova transação, e o parâmetro de saída `seqId` é usado para identificar a transação, que também é usado em `Ack()` e `Fail()`. No `NextTx()`, o usuário pode emitir dados para o lado do Java. Os dados serão armazenados no ZooKeeper para oferecer suporte à reprodução. Como a capacidade do ZooKeeper é bastante limitada, o usuário deve emitir somente metadados, e não dados em massa no spout transacional.

O Storm reproduzirá uma transação automaticamente se falhar, por isso, `Fail()` não deve ser chamado em um caso normal. Mas se o SCP puder verificar os metadados emitidos pelo spout transacional, ele poderá chamar `Fail()` quando os metadados forem inválidos.

Os parâmetros de entrada `parms` nessas funções são apenas um Dicionário vazio e são reservados para uso futuro.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt
ISCPBatchBolt é a interface para bolt transacional.

    public interface ISCPBatchBolt : ISCPPlugin           
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);  
    }

`Execute()` é chamado quando há novas tuplas chegando no bolt. `FinishBatch()` é chamado quando essa transação é encerrada. O parâmetro de entrada `parms` é reservado para uso futuro.

Para topologia transacional, há um conceito importante: `StormTxAttempt`. Ele tem dois campos, `TxId` e `AttemptId`. `TxId` é usado para identificar uma transação específica e, para uma determinada transação, poderá haver várias tentativas se a transação falhar e for reproduzida. O SCP.NET terá um novo objeto ISCPBatchBolt diferente para processar cada `StormTxAttempt`, assim como o Storm faz no lado do Java. O propósito desse design é oferecer suporte ao processamento de transações paralelas. O usuário deve manter em mente que, se uma tentativa de transação for concluída, o objeto ISCPBatchBolt correspondente será destruído e o lixo será coletado.

## <a name="object-model"></a>Modelo de objeto
O SCP.NET também oferece um conjunto simples de objetos chave para que os desenvolvedores realizem a programação. Eles são **Context**, **StateStore** e **SCPRuntime**. Eles serão discutidos no restante desta seção.

### <a name="context"></a>Contexto
O Contexto oferece um ambiente de execução ao aplicativo. Cada instância ISCPPlugin (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) possui uma instância de Contexto correspondente. A funcionalidade fornecido pelo Contexto pode ser dividida em duas partes: (1) a parte estática, que está disponível em todo o processo C\#, (2) a parte dinâmica, que está disponível apenas para a instância específica do Contexto.

### <a name="static-part"></a>Parte Estática
    public static ILogger Logger = null;
    public static SCPPluginType pluginType;                      
    public static Config Config { get; set; }                    
    public static TopologyContext TopologyContext { get; set; }  

`Logger` é fornecido para fins de obtenção de log.

`pluginType` é usado para indicar o tipo de plug-in do processo do C\#. Se o processo do C\# for executado no modo de teste local (sem Java), o tipo de plug-in será `SCP_NET_LOCAL`.

    public enum SCPPluginType 
    {
        SCP_NET_LOCAL = 0,       
        SCP_NET_SPOUT = 1,       
        SCP_NET_BOLT = 2,        
        SCP_NET_TX_SPOUT = 3,   
        SCP_NET_BATCH_BOLT = 4  
    }

`Config` é fornecido para obter os parâmetros de configuração do lado do Java. Os parâmetros são transmitidos do lado do Java quando o plugin do C\# é inicializado. O parâmetros `Config` são divididos em duas partes: `stormConf` e `pluginConf`.

    public Dictionary<string, Object> stormConf { get; set; }  
    public Dictionary<string, Object> pluginConf { get; set; }  

`stormConf` são parâmetros definidos pelo Storm e `pluginConf` são os parâmetros definidos pelo SCP. Por exemplo:

    public class Constants
    {
        … …

        // constant string for pluginConf
        public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

        // constant string for stormConf
        public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";           
        public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";                 
    }

`TopologyContext` é fornecido para obter o contexto da tipologia, é mais útil para componentes com vários paralelismos. Aqui está um exemplo:

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

### <a name="dynamic-part"></a>Parte Dinâmica
As seguintes interfaces são pertinentes a uma determinada instância do Contexto. A instância do Contexto é criada pela plataforma do SCP.NET e transmitida ao código do usuário:

    // Declare the Output and Input Stream Schemas

    public void DeclareComponentSchema(ComponentStreamSchema schema);   

    // Emit tuple to default stream.
    public abstract void Emit(List<object> values);                   

    // Emit tuple to the specific stream.
    public abstract void Emit(string streamId, List<object> values);  

Para o reconhecimento do suporte a spout não transacional, o seguinte método é fornecido:

    // for non-transactional Spout which supports ack
    public abstract void Emit(string streamId, List<object> values, long seqId);  

Para ack de suporte a bolt não transacional, ele deve exibir explicitamente `Ack()` ou `Fail()` para a tupla recebida. E, ao emitir uma nova tupla, também deve especificar as âncoras da nova tupla. Os seguintes métodos são fornecidos.

    public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values); 
    public abstract void Ack(SCPTuple tuple);
    public abstract void Fail(SCPTuple tuple);

### <a name="statestore"></a>StateStore
`StateStore` oferece serviços de metadados, geração de sequência monotônica e coordenação sem espera. Abstrações de simultaneidade distribuídas de níveis mais altos podem ser baseadas no `StateStore`, incluindo bloqueios distribuídos, filas distribuídas, barreiras e serviços transacionais.

Os aplicativos do SCP podem usar o objeto `State` para persistir algumas informações no ZooKeeper, especialmente para a topologia transacional. Ao fazer isso, se o spout transacional travar e for reiniciado, ele poderá recuperar as informações necessárias do ZooKeeper e reiniciar o pipeline.

O objeto `StateStore` tem principalmente os seguintes métodos:

    /// <summary>
    /// Static method to retrieve a state store of the given path and connStr 
    /// </summary>
    /// <param name="storePath">StateStore Path</param>
    /// <param name="connStr">StateStore Address</param>
    /// <returns>Instance of StateStore</returns>
    public static StateStore Get(string storePath, string connStr);

    /// <summary>
    /// Create a new state object in this state store instance
    /// </summary>
    /// <returns>State from StateStore</returns>
    public State Create();

    /// <summary>
    /// Retrieve all states that were previously uncommitted, excluding all aborted states 
    /// </summary>
    /// <returns>Uncommited States</returns>
    public IEnumerable<State> GetUnCommitted();

    /// <summary>
    /// Get all the States in the StateStore
    /// </summary>
    /// <returns>All the States</returns>
    public IEnumerable<State> States();

    /// <summary>
    /// Get state or registry object
    /// </summary>
    /// <param name="info">Registry Name(Registry only)</param>
    /// <typeparam name="T">Type, Registry or State</typeparam>
    /// <returns>Return Registry or State</returns>
    public T Get<T>(string info = null);

    /// <summary>
    /// List all the committed states
    /// </summary>
    /// <returns>Registries contain the Committed State </returns> 
    public IEnumerable<Registry> Commited();

    /// <summary>
    /// List all the Aborted State in the StateStore
    /// </summary>
    /// <returns>Registries contain the Aborted State</returns>
    public IEnumerable<Registry> Aborted();

    /// <summary>
    /// Retrieve an existing state object from this state store instance 
    /// </summary>
    /// <returns>State from StateStore</returns>
    /// <typeparam name="T">stateId, id of the State</typeparam>
    public State GetState(long stateId)

O objeto `State` tem principalmente os seguintes métodos:

    /// <summary>
    /// Set the status of the state object to commit 
    /// </summary>
    public void Commit(bool simpleMode = true); 

    /// <summary>
    /// Set the status of the state object to abort 
    /// </summary>
    public void Abort();

    /// <summary>
    /// Put an attribute value under the give key 
    /// </summary>
    /// <param name="key">Key</param> 
    /// <param name="attribute">State Attribute</param> 
    public void PutAttribute<T>(string key, T attribute); 

    /// <summary>
    /// Get the attribute value associated with the given key 
    /// </summary>
    /// <param name="key">Key</param> 
    /// <returns>State Attribute</returns>               
    public T GetAttribute<T>(string key);                    

Para o método `Commit()` , quando simpleMode é configurado como verdadeiro, ele simplesmente exclui o ZNode correspondente no ZooKeeper. Caso contrário, ele exclui o ZNode atual e adiciona um novo nó no COMMITTED\_PATH.

### <a name="scpruntime"></a>SCPRuntime
SCPRuntime oferece os seguintes dois métodos.

    public static void Initialize();

    public static void LaunchPlugin(newSCPPlugin createDelegate);  

`Initialize()` é usado para inicializar o ambiente de tempo de execução do SCP. Nesse método, o processo do C\# se conecta ao lado do Java e obtém os parâmetros de configuração e o contexto da topologia.

`LaunchPlugin()` é usado para iniciar o loop de processamento de mensagem. Nesse loop, o plugin do C\# recebe mensagens do lado do Java (incluindo tuplas e sinais de controle) e, então, processa as mensagens, talvez chamando o método da interface fornecido pelo código do usuário. O parâmetro de entrada para o método `LaunchPlugin()` é um delegado que pode retornar um objeto que implementa a interface ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt.

    public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary\<string, Object\> parms); 

Para ISCPBatchBolt, podemos obter `StormTxAttempt` de `parms` e usá-lo para julgar se trata-se de uma tentativa reproduzida. Isso geralmente é feito no bolt de confirmação, e é demonstrado no exemplo `HelloWorldTx` .

Em termos gerais, os plugins do SCP podem ser executados em dois modos aqui:

1. Modo de Teste Local: nesse modo, os plug-ins do SCP (o código de usuário C\#) é executado dentro do Visual Studio durante a fase de desenvolvimento. `LocalContext` pode ser usado nesse modo, oferecendo um método para serializar as tuplas emitidas para os arquivos locais e lê-los de volta na memória.
   
        public interface ILocalContext
        {
            List\<SCPTuple\> RecvFromMsgQueue();
            void WriteMsgQueueToFile(string filepath, bool append = false);  
            void ReadFromFileToMsgQueue(string filepath);                    
        }
2. Modo Regular: nesse modo, os plug-ins do SCP são lançados pelo processo de Java do Storm.
   
    Aqui está um exemplo da inicialização do plugin do SCP:
   
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

## <a name="topology-specification-language"></a>Linguagem de Especificação da Topologia
A Especificação da Topologia do SCP é uma linguagem específica do domínio para descrever e configurar topologias do SCP. Ela se baseia no Clojure DSL do Storm (<http://storm.incubator.apache.org/documentation/Clojure-DSL.html>) e é estendida pelo SCP.

As especificações de topologia podem ser enviadas diretamente ao cluster do Storm para execução por meio do comando ***runspec***.

O SCP.NET adicionou as seguintes funções para definir a Topologia Transacional:

| **Novas funções** | **Parâmetros** | **Descrição** |
| --- | --- | --- |
| **tx-topolopy** |topology-name<br />spout-map<br />bolt-map |Defina uma topologia transacional com o nome da topologia, o &nbsp;mapa de definição de spouts e o mapa de definição de bolts |
| **scp-tx-spout** |exec-name<br />args<br />fields |Defina um spout transacional. Isso executará o aplicativo com ***exec-name*** usando ***args***.<br /><br />O ***fields*** são os Campos de Saída do spout |
| **scp-tx-batch-bolt** |exec-name<br />args<br />fields |Defina um bolt em lote transacional. Executará um aplicativo com o ***exec-name*** usando ***args.***<br /><br />Os Fields são os Campos de Saída do bolt. |
| **scp-tx-commit-bolt** |exec-name<br />args<br />fields |Defina um bolt confirmador transacional. Isso executará o aplicativo com ***exec-name*** usando ***args***.<br /><br />O ***fields*** são os Campos de Saída do bolt |
| **nontx-topolopy** |topology-name<br />spout-map<br />bolt-map |Defina uma topologia não transacional com o nome da topologia, o &nbsp;mapa de definição de spouts e o mapa de definição de bolts |
| **scp-spout** |exec-name<br />args<br />fields<br />Parâmetros |Defina um spout não transacional. Isso executará o aplicativo com ***exec-name*** usando ***args***.<br /><br />O ***fields*** são os Campos de Saída do spout<br /><br />O ***parameters*** são opcionais, usados para especificar alguns parâmetros como "nontransactional.ack.enabled". |
| **scp-bolt** |exec-name<br />args<br />fields<br />Parâmetros |Defina um bolt não transacional. Isso executará o aplicativo com ***exec-name*** usando ***args***.<br /><br />O ***fields*** são os Campos de Saída do bolt<br /><br />O ***parameters*** são opcionais, usados para especificar alguns parâmetros como "nontransactional.ack.enabled". |

O SCP.NET possui as seguintes palavras-chave definidas:

| **Palavras-chave** | **Descrição** |
| --- | --- |
| **:name** |Defina o nome da topologia |
| **:topology** |Defina a Topologia usando as funções acima e as integradas. |
| **:p** |Defina a dica de paralelismo para cada spout ou bolt. |
| **:config** |Defina o parâmetro de configuração ou atualize os existentes |
| **:schema** |Defina o esquema do fluxo. |

Todos os parâmetros utilizados com frequência:

| **Parâmetro** | **Descrição** |
| --- | --- |
| **"plugin.name"** |nome do arquivo exe do plugin do C# |
| **"plugin.args"** |args do plugin |
| **"output.schema"** |Esquema de saída |
| **"nontransactional.ack.enabled"** |Se o reconhecimento está ativado para a topologia não transacional |

O comando runspec será implantado juntamente com os bits, o uso é semelhante a:

    .\bin\runSpec.cmd
    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
    ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target

O parâmetro***resource-dir*** é opcional, você precisa especificá-lo quando quiser conectar um aplicativo do C\#, e esse diretório conterá o aplicativo, as dependências e as configurações.

O parâmetro ***classpath*** também é opcional. Ele é usado para especificar o caminho de classe do Java se o arquivo de especificações contiver Spout ou Bolt do Java.

## <a name="miscellaneous-features"></a>Recursos diversos
### <a name="input-and-output-schema-declaration"></a>Declaração de esquema de entrada e saída
O usuário pode emitir uma tupla no processo do C\#, a plataforma precisa serializar a tupla em bytes, transferi-la para o lado do Java e o Storm transferirá essa tupla aos destinos. Enquanto isso, no componente downstream, o processo do C\# receberá a tupla de volta do lado do Java e a converterá para os tipos originais pela plataforma; todas essas operações são ocultadas pela plataforma.

Para oferecer suporte à serialização e desserialização, o código do usuário precisa declarar o esquema das entradas e saídas.

O esquema de fluxo de entrada/saída é definido como um dicionário, a chave é o StreamId e o valor são os Tipos das colunas. O componente pode ter múltiplos fluxos declarados.

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


No objeto de Contexto, temos a seguinte API adicionada:

    public void DeclareComponentSchema(ComponentStreamSchema schema)

O código do usuário deve assegurar que as tuplas emitidas obedecem ao esquema definido para esse fluxo, ou o sistema lançará uma exceção de tempo de execução.

### <a name="multi-stream-support"></a>Suportes a múltiplos fluxos
O SCP oferece suporte ao código do usuário para emitir ou receber de vários fluxos diferentes ao mesmo tempo. O suporte é refletido no objeto de Contexto conforme o método de Emissão assume um parâmetro de ID de fluxo opcional.

Dois métodos no objeto de Contexto do SCP.NET foram adicionados. Eles são usados para emitir uma Tupla ou Tuplas para especificar o StreamId. O StreamId é uma cadeia de caracteres e precisa ser consistente no C\# e na Especificação de Definição da Topologia.

        /* Emit tuple to the specific stream. */
        public abstract void Emit(string streamId, List<object> values);

        /* for non-transactional Spout only */
        public abstract void Emit(string streamId, List<object> values, long seqId);

A emissão para um fluxo não existente causará exceções de tempo de execução.

### <a name="fields-grouping"></a>Agrupamento de Campos
O Agrupamento de Campos interno no Storm não está funcionando adequadamente no SCP.NET. No lado do Proxy Java, todos os tipos de dados de campo são na verdade byte[] e o agrupamento de campos usa o código hash do objeto byte[] para realizar o agrupamento. O código hash do objeto byte[] é o endereço desse objeto na memória. Por isso, o agrupamento estará incorreto para dois objetos byte[] que compartilharem o mesmo conteúdo, mas não o mesmo endereço.

O SCP.NET adiciona um método de agrupamento personalizado e utiliza o conteúdo de byte[] para realizar o agrupamento. No arquivo **SPEC** , a sintaxe é parecida com:

    (bolt-spec
        {
            "spout_test" (scp-field-group :non-tx [0,1])
        }
        …
    )


Aqui,

1. “scp-field-group” significa “Agrupamento de campo personalizado implementado pelo SCP”.
2. “:tx” ou “:non-tx” significa que se trata de uma topologia transacional. Precisamos dessa informação, uma vez que o índice de início é diferente em topologias tx em relação às não tx.
3. [0,1] significa o hashset dos IDs do campo, começando do 0.

### <a name="hybrid-topology"></a>Topologia híbrida
O Storm nativo é escrito em Java. E SCP.Net foi aprimorado para permitir que nossas personalizações sejam escritas em código C\# para lidar com a lógica de negócios. Mas também damos suporte a topologias híbridas, que contêm não apenas spouts/bolts do C\#, mas também do Java.

### <a name="specify-java-spoutbolt-in-spec-file"></a>Especificar o Spout/Bol do Java no arquivo de especificações
No arquivo de especificações, "scp-spout" e "scp-bolt" também podem ser usados para especificar Spouts e Bolts do Java, aqui está um exemplo:

    (spout-spec 
      (microsoft.scp.example.HybridTopology.Generator.)           
      :p 1)

Aqui `microsoft.scp.example.HybridTopology.Generator` é o nome da classe Spout do Java.

### <a name="specify-java-classpath-in-runspec-command"></a>Especificar o caminho de classe do Java no comando runSpec
Se quiser enviar uma topologia contendo Spouts ou Bolts do Java, será necessário primeiro compilar os Spouts ou Bolts do Java e obter os arquivos Jar. Depois, você deve especificar o caminho de classe do Java que contém os arquivos Jar ao enviar a topologia. Veja um exemplo:

    bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*

Aqui, **examples\\HybridTopology\\java\\target\\** é a pasta que contém o arquivo Jar do Spout/Bolt.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Serialização e desserialização entre Java e C\
Nosso componente SCP inclui lado de Java e de C\#. Para interagir com Spouts/Bolts Java nativos, a Serialização/Desserialização deve ser realizada entre o lado do Java e C\#, conforme ilustrado no gráfico a seguir.

![diagrama do componente java enviando ao componente SCP enviando ao componente Java](media/hdinsight-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

1. **Serialização no lado do Java e desserialização no lado do C\#**
   
   Primeiro, fornecemos a implementação padrão para serialização no lado do Java e a desserialização no lado do C\#. O método de serialização no lado do Java pode ser especificado no arquivo SPEC:
   
       (scp-bolt
           {
               "plugin.name" "HybridTopology.exe"
               "plugin.args" ["displayer"]
               "output.schema" {}
               "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
           })
   
   O método de desserialização no lado do C\# deve ser especificado no código do usuário do C\#:
   
       Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
       inputSchema.Add("default", new List<Type>() { typeof(Person) });
       this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
       this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());            
   
   Essa implementação padrão deve lidar com a maioria dos casos se o tipo de dados não for muito complexo. Para determinados casos, seja porque o tipo de dados do usuário é muito complexo ou porque o desempenho de nossa implementação padrão não atende o requisito do usuário, o usuário pode conectar sua própria implementação.
   
   A interface de serialização no lado do Java é definida como:
   
       public interface ICustomizedInteropJavaSerializer {
           public void prepare(String[] args);
           public List<ByteBuffer> serialize(List<Object> objectList);
       }
   
   A interface de desserialização no lado do C\# é definida como:
   
   interface pública ICustomizedInteropCSharpDeserializer
   
       public interface ICustomizedInteropCSharpDeserializer
       {
           List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
       }
2. **Serialização no lado do C\# e desserialização no lado do Java**
   
   O método de serialização no lado do C\# deve ser especificado no código do usuário do C\#:
   
       this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
   
   O método de Desserialização no lado do Java deve ser especificado no arquivo SPEC:
   
     (scp-spout
   
       {
         "plugin.name" "HybridTopology.exe"
         "plugin.args" ["generator"]
         "output.schema" {"default" ["person"]}
         "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
       })
   
   Aqui "microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" é o nome do Desserializador e "microsoft.scp.example.HybridTopology.Person" é a classe de destino para a qual os dados são desserializados.
   
   O usuário também pode usar um plug-in para sua própria implementação de serializador de C\# e desserializador de Java. Essa é a interface para o serializador do C\#:
   
       public interface ICustomizedInteropCSharpSerializer
       {
           List<byte[]> Serialize(List<object> dataList);
       }
   
   Essa é a interface para o Deserializador Java:
   
       public interface ICustomizedInteropJavaDeserializer {
           public void prepare(String[] targetClassNames);
           public List<Object> Deserialize(List<ByteBuffer> dataList);
       }

## <a name="scp-host-mode"></a>Modo de host do SCP
Nesse modo, o usuário pode compilar seus códigos como DLL e usar o SCPHost.exe fornecido pelo SCP para enviar a topologia. O arquivo de especificações tem a seguinte aparência:

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

Aqui, `plugin.name` é especificado como `SCPHost.exe` fornecido pelo SDK do SCP. O SCPHost.exe aceita exatamente três parâmetros:

1. O primeiro é o nome DLL, que é `"HelloWorld.dll"` neste exemplo.
2. O segundo é o nome da Classe, que é `"Scp.App.HelloWorld.Generator"` neste exemplo.
3. O terceiro é o nome de um método estático público, que pode ser invocado para obter uma instância do ISCPPlugin.

No modo do host, o código do usuário é compilado como DLL e é invocado pela plataforma SCP. Por isso, a plataforma do SCP pode ter controle total de toda a lógica de processamento. Portanto, recomendamos que nossos clientes enviem a topologia no modo do host do SCP, uma vez que isso pode simplificar a experiência do desenvolvimento e oferecer mais flexibilidade e uma maior compatibilidade com versões anteriores também para versões posteriores.

## <a name="scp-programming-examples"></a>Exemplos de programação do SCP
### <a name="helloworld"></a>HelloWorld
**HelloWorld** é um exemplo muito simples para mostrar um pouco do SCP.Net. Ele usa uma topologia não transacional com um spout chamado **generator** e dois bolts chamados **splitter** e **counter**. O spout **generator** gerará aleatoriamente algumas frases e as enviará para o **splitter**. O bolt **splitter** as divide em palavras e envia essas palavras ao bolt **counter**. O bolt “counter” usa um dicionário para registrar o número de ocorrências de cada palavra.

Há dois arquivos de especificações, **HelloWorld.spec** e **HelloWorld\_EnableAck.spec** para este exemplo. No código do C\#, ele pode descobrir se o reconhecimento está habilitado obtendo o pluginConf no lado do Java.

    /* demo how to get pluginConf info */
    if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
    {
        enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
    }
    Context.Logger.Info("enableAck: {0}", enableAck);

No spout, se o reconhecimento estiver habilitado, um dicionário é usado para armazenar em cache as tuplas que não foram reconhecidas. Se Fail() for chamado, a tupla com falha é reproduzida:

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

### <a name="helloworldtx"></a>HelloWorldTx
O exemplo **HelloWorldTx** demonstra como implementar a topologia transacional. Ele tem um spout chamado **generator**, um bolt em lote chamado **partial-count** e um bolt de confirmação chamado **count-sum**. Também há três arquivos txt pré-criados: **DataSource0.txt**, **DataSource1.txt** e **DataSource2.txt**.

Em cada transação, o spout **generator** escolhe aleatoriamente dois arquivos dos três arquivos pré-criados e emite os dois nomes dos arquivos ao bolt **partial-count**. O bolt **partial-count** primeiro obtém o nome do arquivo da tupla recebida, depois, abre o arquivo e conta o número de palavras nesse arquivo e, por fim, emite o número de palavras ao bolt **count-sum**. O bolt **count-sum** resume a contagem total.

Para alcançar a semântica **exatamente uma vez**, o bolt de confirmação **count-sum** precisa julgar que se trata de uma transação reproduzida. Neste exemplo, ele possui uma variável do membro estático:

    public static long lastCommittedTxId = -1; 

Quando uma instância ISCPBatchBolt é criada, ela obtém `txAttempt` dos parâmetros de entrada:

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

Quando `FinishBatch()` é chamado, o `lastCommittedTxId` será atualizado se não for uma transação reproduzida.

    public void FinishBatch(Dictionary<string, Object> parms)
    {
        /* judge whether it is a replayed transaction? */
        bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

        if (!replay)
        {
            /* If it is not replayed, update the toalCount and lastCommittedTxId vaule */
            totalCount = totalCount + this.count;
            lastCommittedTxId = this.txAttempt.TxId;
        }
        … …
    }


### <a name="hybridtopology"></a>HybridTopology
Essa topologia contém um Spout do Java e um Bolt do C\#. Ela usa a implementação de serialização e desserialização padrão fornecida pela plataforma do SCP. Consulte **HybridTopology.spec** na pasta **examples\\HybridTopology** para obter os detalhes do arquivo de especificações e **SubmitTopology.bat** para saber como especificar o caminho de classe de Java.

### <a name="scphostdemo"></a>SCPHostDemo
Este exemplo é igual ao HelloWorld em essência. A única diferença é que o código do usuário é compilado como DLL e a topologia é enviada usando SCPHost.exe. Consulte a seção “Modo de host do SCP” para obter explicações mais detalhadas.

## <a name="next-steps"></a>Próximas etapas
Para obter exemplos de topologias Storm criadas usando o SCP, consulte o seguinte:

* [Desenvolver topologias C# para o Apache Storm no HDInsight usando o Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)
* [Processar eventos dos Hubs de Evento do Azure com o Storm no HDInsight](hdinsight-storm-develop-csharp-event-hub-topology.md)
* [Criar vários fluxos de dados em uma topologia Storm em C#](hdinsight-storm-twitter-trending.md)
* [Usar o Power BI para visualizar dados da topologia Storm](hdinsight-storm-power-bi-topology.md)
* [Processar dados do sensor do veículo a partir de Hubs de Evento usando o Storm no HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [ETL (Extração, Transformação e Carregamento) de Hubs de Eventos do Azure para HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
* [Correlacionar eventos usando Storm e HBase no HDInsight](hdinsight-storm-correlation-topology.md)

