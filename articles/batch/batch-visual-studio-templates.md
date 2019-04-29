---
title: Compilar soluções com modelos do Visual Studio - Lote do Azure | Microsoft Docs
description: Saiba como os modelos de projeto do Visual Studio podem ajudar você a implementar e executar suas cargas de trabalho de computação intensa no Lote do Azure.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 5e041ae2-25af-4882-a79e-3aa63c4bfb20
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 085bfa582b676f34a02e4c1c5ae7e69c49e5cb4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60550035"
---
# <a name="use-visual-studio-project-templates-to-jump-start-batch-solutions"></a>Usar modelos de projeto do Visual Studio para iniciar rapidamente soluções em lote

O **Gerenciador de Trabalho** e os **modelos do Visual Studio do Processador de Tarefa** para o Lote fornecem código para ajudar você a implementar e executar suas cargas de trabalho de computação intensa no Lote com o mínimo de esforço. Este documento descreve esses modelos e fornece diretrizes sobre como usá-los.

> [!IMPORTANT]
> Este artigo discute apenas as informações aplicáveis a esses dois modelos e pressupõe que você esteja familiarizado com o serviço do Lote e os principais conceitos relacionados a ele: pools, nós de computação, trabalhos e tarefas, tarefas do gerenciador de trabalho, variáveis de ambiente e outras informações relevantes. É possível encontrar mais informações em [Noções básicas do Lote do Azure](batch-technical-overview.md) e [Visão geral do recurso do Lote para desenvolvedores](batch-api-basics.md).
> 
> 

## <a name="high-level-overview"></a>Visão geral de alto nível
Os modelos do Gerenciador de Trabalho e do Processador de Tarefa podem ser usados para criar dois componentes úteis:

* Uma tarefa do gerenciador de trabalho que implementa um divisor de trabalho que pode, por sua vez, dividir um trabalho em várias tarefas que podem ser executadas de forma independente, em paralelo.
* Um processador de tarefa que pode ser usado para executar o pré-processamento e o pós-processamento em uma linha de comando do aplicativo.

Por exemplo, em um cenário de renderização de filme, o divisor de trabalho transformaria um trabalho único de filme em centenas ou milhares de tarefas separadas que processam quadros individuais separadamente. Do mesmo modo, o processador de tarefa invocaria o aplicativo de renderização e todos os processos dependentes exigidos para processar cada quadro, bem como executar quaisquer ações adicionais (por exemplo, copiar o quadro processado em um local de armazenamento).

> [!NOTE]
> Os modelos do Gerenciador de Trabalho e do Processador de Tarefa são independentes, para que você possa optar por usar ambos ou apenas um deles, dependendo dos requisitos do seu trabalho de computação e de suas preferências.
> 
> 

Conforme mostrado no diagrama a seguir, um trabalho de computação que usa esses modelos passará por três estágios:

1. O código do cliente (por exemplo, aplicativo, serviço Web etc.) envia um trabalho ao serviço do Lote no Azure, especificando como sua tarefa de gerenciamento de trabalho o programa Gerenciador de trabalho.
2. O serviço do Lote executa a tarefa do Gerenciador de trabalho em um nó de computação, e o divisor de trabalho inicia o número especificado de tarefas do processador de tarefas, na quantidade de nós de computação necessária, com base nos parâmetros e especificações no código do divisor de trabalho.
3. As tarefas do processador de tarefas são executadas de forma independente, em paralelo, a fim de processar os dados de entrada e gerar os dados de saída.

![Diagrama mostrando como o código do cliente interage com o serviço do Lote][diagram01]

## <a name="prerequisites"></a>Pré-requisitos
Para usar os modelos do Lote, você precisará do seguinte:

* Um computador com o Visual Studio 2015 instalado. Modelos de lote atualmente só têm suporte para o Visual Studio 2015.
* Os modelos do Lote, disponíveis na [Galeria do Visual Studio][vs_gallery] como extensões do Visual Studio. Há duas maneiras de obter os modelos:
  
  * Instalar os modelos usando a caixa de diálogo **Extensões e Atualizações** no Visual Studio (para saber mais, confira [Localizando e usando extensões do Visual Studio][vs_find_use_ext]). Na caixa de diálogo **Extensões e Atualizações** , procure e baixe as duas extensões a seguir:
    
    * Gerenciador de Trabalhos do Lote do Azure com o Divisor de Trabalho
    * Processador de Tarefas do Lote do Azure
  * Baixe os modelos da galeria online para o Visual Studio: [Modelos de projeto do Lote do Microsoft Azure][vs_gallery_templates]
* Se você planeja usar o recurso [Pacotes de aplicativos](batch-application-packages.md) para implantar o Gerenciador de trabalho e o processador de tarefas nos nós de computação do Lote, será necessário vincular uma conta de armazenamento à sua conta do Lote.

## <a name="preparation"></a>Preparação
Recomendamos a criação de uma solução que pode conter seu Gerenciador de trabalho, bem como o processador de tarefas, pois isso pode facilitar o compartilhamento do código entre os programas Gerenciador de trabalho e Processador de tarefas. Para criar essa solução, execute estas etapas:

1. Abra o Visual Studio e selecione **Arquivo** > **Novo** > **Projeto**.
2. Em **Modelos**, expanda **Outros Tipos de Projeto**, clique em **Soluções do Visual Studio** e, em seguida, selecione **Solução em Branco**.
3. Digite um nome que descreva seu aplicativo e a finalidade dessa solução (por exemplo, "ProgramasDeTarefasdoLoteLitware").
4. Para criar a nova solução, clique em **OK**.

## <a name="job-manager-template"></a>Modelo do Gerenciador de trabalho
O modelo do Gerenciador de trabalho ajuda você a implementar uma tarefa do Gerenciador de trabalho que pode executar as seguintes ações:

* Dividir um trabalho em várias tarefas.
* Enviar essas tarefas para execução no Lote.

> [!NOTE]
> Para saber mais sobre as tarefas do Gerenciador de trabalho, confira [Visão geral do recurso Lote para desenvolvedores](batch-api-basics.md#job-manager-task).
> 
> 

### <a name="create-a-job-manager-using-the-template"></a>Criar um Gerenciador de trabalho usando o modelo
Para adicionar um Gerenciador de trabalho à solução que você criou anteriormente, execute estas etapas:

1. Abra sua solução existente no Visual Studio.
2. No Gerenciador de Soluções, clique com o botão direito do mouse na solução e clique em **Adicionar** > **Novo Projeto**.
3. No **Visual C#**, clique em **Nuvem** e em **Gerenciador de trabalho do Lote do Azure com o Divisor de trabalho**.
4. Digite um nome que descreva o aplicativo e identifique esse projeto como o Gerenciador de trabalho (por exemplo, "GerenciadorDeTrabalhoLitware").
5. Para criar o projeto, clique em **OK**.
6. Por fim, compile o projeto para forçar o Visual Studio a carregar todos os pacotes NuGet referenciados e verificar se o projeto é válido antes de você começar a modificá-lo.

### <a name="job-manager-template-files-and-their-purpose"></a>Arquivos do modelo do Gerenciador de trabalho e suas finalidades
Quando você cria um projeto usando o modelo do Gerenciador de trabalho, ele gera três grupos de arquivos de código:

* O arquivo de programa principal (Program.cs). Esse arquivo contém o ponto de entrada do programa e a manipulação de exceção de nível superior. Normalmente, não é necessário modificar isso.
* O diretório Framework. Esse diretório contém os arquivos responsáveis pelo trabalho “clichê” realizado pelo programa Gerenciador de trabalho – desempacotar parâmetros, adicionar tarefas ao trabalho do Lote etc. Normalmente, não é necessário modificar esses arquivos.
* O arquivo do Divisor de trabalho (JobSplitter.cs). É nesse arquivo que você colocará a lógica específica ao aplicativo para a divisão de um trabalho em tarefas.

Obviamente você pode adicionar outros arquivos conforme o necessário a fim de oferecer suporte ao código do divisor de trabalho, dependendo da complexidade da lógica de divisão do trabalho.

O modelo também gera arquivos de projeto padrão do .NET, como um arquivo .csproj, app.config, packages.config etc.

O restante desta seção descreve os diferentes arquivos e sua estrutura de código, e explica o que cada classe faz.

![Visual Studio Solution Explorer mostrando a solução do modelo do Gerenciador de trabalhos][solution_explorer01]

**Arquivos do Framework**

* `Configuration.cs`: Encapsula o carregamento de dados de configuração de trabalho, como detalhes da conta do Lote, credenciais da conta de armazenamento vinculada, informações sobre o trabalho e as tarefas e parâmetros do trabalho. Ele também fornece acesso a variáveis de ambiente definidas pelo Lote (consulte Configurações de ambiente para tarefas, na documentação do Lote) por meio da classe Configuration.EnvironmentVariable.
* `IConfiguration.cs`: Abstrai a implementação da classe Configuration, para que você possa testar a unidade de seu divisor de trabalho usando um objeto de configuração falso ou fictício.
* `JobManager.cs`: Coordena os componentes do programa Gerenciador de trabalho. Ele é responsável pela inicialização do divisor de trabalho, invocação do divisor de trabalho e distribuição de tarefas retornadas pelo divisor de trabalho para o emissor de tarefas.
* `JobManagerException.cs`: Representa um erro que exige o encerramento do gerenciador de trabalho. JobManagerException é usado para encapsular erros “esperados”, em que as informações de diagnóstico específicas podem ser fornecidas como parte do encerramento.
* `TaskSubmitter.cs`: Essa classe é responsável por adicionar tarefas retornadas pelo divisor de trabalho para o trabalho do Lote. A classe JobManager agrega a sequência de tarefas em lotes para uma adição eficiente, e em tempo hábil, ao trabalho. Em seguida, ela chama TaskSubmitter.SubmitTasks em um thread em segundo plano para cada lote.

**Divisor de trabalho**

`JobSplitter.cs`: Essa classe contém uma lógica específica ao aplicativo para dividir o trabalho em tarefas. O framework invoca o método JobSplitter.Split para obter uma sequência de tarefas, a qual ele adiciona ao trabalho à medida que o método as retorna. Essa é a classe na qual você injetará a lógica de seu trabalho. Implemente o método Split para retornar uma sequência de instâncias CloudTask representando as tarefas nas quais você deseja dividir o trabalho.

**Arquivos de projeto de linha de comando .NET padrão**

* `App.config`: Arquivo de configuração padrão de aplicativo .NET.
* `Packages.config`: Arquivo de dependência padrão do pacote NuGet.
* `Program.cs`: Contém o ponto de entrada do programa e a manipulação de exceção de nível superior.

### <a name="implementing-the-job-splitter"></a>Implementação do divisor de trabalho
Quando você abre o projeto de modelo do Gerenciador de trabalhos, o projeto fará o arquivo JobSplitter.cs ser aberto por padrão. Você pode implementar a lógica de divisão para as tarefas em sua carga de trabalho usando o método Split() exibido abaixo:

```csharp
/// <summary>
/// Gets the tasks into which to split the job. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The job manager framework invokes the Split method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation should return tasks lazily, for example using a C#
/// iterator and the "yield return" statement; this allows tasks to be added
/// and to start running while splitting is still in progress.
/// </summary>
/// <returns>The tasks to be added to the job. Tasks are added automatically
/// by the job manager framework as they are returned by this method.</returns>
public IEnumerable<CloudTask> Split()
{
    // Your code for the split logic goes here.
    int startFrame = Convert.ToInt32(_parameters["StartFrame"]);
    int endFrame = Convert.ToInt32(_parameters["EndFrame"]);

    for (int i = startFrame; i <= endFrame; i++)
    {
        yield return new CloudTask("myTask" + i, "cmd /c dir");
    }
}
```

> [!NOTE]
> A seção anotada nos métodos `Split()` é a única seção do código do modelo do Gerenciador de trabalho que você pode modificar adicionando a lógica para dividir os trabalhos em tarefas diferentes. Se você quiser modificar uma seção diferente do modelo, primeiro você precisará ter certeza de que está familiarizado com o funcionamento do Lote e experimente alguns dos [exemplos de código do Lote][github_samples].
> 
> 

Sua implementação de Split() tem acesso a:

* Os parâmetros do trabalho, por meio do campo `_parameters` .
* O objeto CloudJob que representa o trabalho, por meio do campo `_job` .
* O objeto CloudTask que representa a tarefa do gerenciador de trabalho, por meio do campo `_jobManagerTask` .

Sua implementação do `Split()` não precisa adicionar tarefas diretamente ao trabalho. Em vez disso, seu código deve retornar uma sequência de objetos CloudTask, e eles serão adicionados ao trabalho automaticamente pelas classes do framework que invocam o divisor de trabalho. É comum usar o recurso do iterador do C# (`yield return`) para implementar divisores de trabalho, pois isso permite o início da execução das tarefas assim que é possível, em vez de esperar que todas as tarefas sejam calculadas.

**Falha do divisor de trabalho**

Se o divisor de trabalho encontrar um erro, ele deverá:

* Encerrar a sequência usando a instrução em C# `yield break` Nesse caso, o Gerenciador de trabalho será tratado como bem-sucedido; ou
* Lançar uma exceção. Nesse caso, o Gerenciador de trabalho será tratado como se tivesse apresentado uma falha e poderá repetir dependendo do modo de configuração do cliente).

Em ambos os casos, quaisquer tarefas que já foram retornadas pelo divisor de trabalho e adicionadas ao trabalho do Lote poderão ser executadas. Se você não quiser que isso aconteça, você poderá:

* Encerrar o trabalho antes de retornar do divisor de trabalho
* Foumular toda a coleção de tarefas inteira antes de retouná-la (ou seja, retouna um `ICollection<CloudTask>` ou `IList<CloudTask>` instead of implementing your job splitter using a C# iteratou)
* Usar dependências de tarefas para fazer com que todas as tarefas dependam da conclusão bem-sucedida do Gerenciador de trabalho

**Novas tentativas do Gerenciador de trabalho**

Se o gerenciador de trabalho falhar, o serviço do Lote poderá tentar executá-lo outra vez, dependendo das configurações de repetição do cliente. Em geral, isso é seguro, pois quando o framework adiciona tarefas ao trabalho, ele ignora quaisquer tarefas já existentes. No entanto, se o cálculo de tarefas for caro, talvez você não queira incorrer em custos de recálculo de tarefas que já foram adicionadas ao trabalho; por outro lado, se não houver garantia de que a nova execução gerará as mesmas IDs de tarefa , o comportamento de “ignorar duplicatas” será não iniciado. Nesses casos, projete o divisor de trabalho para detectar o trabalho que já foi realizado e não repeti-lo, por exemplo, executando um CloudJob.ListTasks antes de começar a gerar tarefas.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Códigos de saída e exceções no modelo do Gerenciador de trabalho
Os códigos de saída e exceções fornecem um mecanismo para determinar o resultado da execução de um programa, e podem ajudar a identificar problemas com a execução do programa. O modelo do Gerenciador de trabalho implementa os códigos e exceções de saída descritos nesta seção.

Uma tarefa do Gerenciador de trabalho implementada com o modelo do Gerenciador de trabalho pode retornar três códigos de saída possíveis:

| Código | DESCRIÇÃO |
| --- | --- |
| 0 |O Gerenciador de trabalho foi concluído com sucesso. O código do divisor de trabalho executou até a conclusão, e todas as tarefas foram adicionadas ao trabalho. |
| 1 |A tarefa do Gerenciador de trabalho falhou com uma exceção em uma parte “esperada” do programa. A exceção foi convertida para um JobManagerException com informações de diagnóstico e, quando possível, sugestões para resolver a falha. |
| 2 |A tarefa do Gerenciador de trabalho falhou com uma exceção “inesperada”. A exceção foi registrada na saída padrão, mas o Gerenciador de trabalho não conseguiu adicionar informações de diagnóstico ou correção adicionais. |

No caso de falha da tarefa do Gerenciador de trabalho, algumas tarefas ainda podem ter sido adicionadas ao serviço antes do erro ocorrer. Essas tarefas serão executadas normalmente. Consulte "Falha do divisor de trabalho" acima para conferir uma discussão desse caminho de código.

Todas as informações retornadas pelas exceções são gravadas nos arquivos stdout.txt e stderr.txt. Para saber mais, confira [Manipulação de erro](batch-api-basics.md#error-handling).

### <a name="client-considerations"></a>Considerações do cliente
Esta seção descreve alguns requisitos de implementação do cliente ao invocar um Gerenciador de trabalho com base nesse modelo. Consulte [Como passar parâmetros e variáveis de ambiente no código do cliente](#pass-environment-settings) para obter detalhes sobre como passar parâmetros e configurações de ambiente.

**Credenciais obrigatórias**

Para adicionar tarefas ao trabalho do Lote do Azure, a tarefa do Gerenciador de trabalho exige a URL e a chave da conta do Lote do Azure. Você deve passá-la nas variáveis de ambiente chamadas YOUR_BATCH_URL e YOUR_BATCH_KEY. Defina-as nas configurações de ambiente da tarefa do Gerenciador de trabalho. Por exemplo, em um cliente C#:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Credenciais de armazenamento**

Normalmente, o cliente não precisa fornecer as credenciais da conta de armazenamento vinculado à tarefa do Gerenciador de trabalho, pois (a) a maioria dos gerenciadores de trabalho não precisa acessar explicitamente a conta de armazenamento vinculado e (b) a conta de armazenamento vinculado normalmente é fornecida para todas as tarefas como uma configuração de ambiente comum para o trabalho. Se você não estiver fornecendo a conta de armazenamento vinculada por meio das configurações de ambiente comum, e o Gerenciador de trabalho exigir acesso ao armazenamento vinculado, você deverá fornecer as credenciais de armazenamento vinculado da seguinte maneira:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Configurações da tarefa do Gerenciador de trabalho**

O cliente deve definir o sinalizador *killJobOnCompletion* do Gerenciador de trabalho como **false**.

Normalmente, é seguro para o cliente definir *runExclusive* como **false**.

O cliente deve usar a coleção *resourceFiles* ou *applicationPackageReferences* para que o executável do Gerenciador de trabalho (e suas DLLs necessárias) seja implantado no nó de computação.

Por padrão, o Gerenciador de trabalho não será repetido em caso de falha. Dependendo de sua lógica do Gerenciador de trabalho, talvez o cliente queira habilitar novas tentativas por meio de *constraints*/*maxTaskRetryCount*.

**Configurações do trabalho**

Se o divisor de trabalho emitir tarefas com dependências, o cliente deverá definir usesTaskDependencies do trabalho como true.

No modelo do divisor de trabalho, não é comum os clientes quererem adicionar tarefas a trabalhos além do que foi criado pelo divisor de trabalho. Portanto, o cliente deve definir normalmente o *onAllTasksComplete* do trabalho como **terminatejob**.

## <a name="task-processor-template"></a>Modelo de Processador de tarefas
Um modelo do Processador de tarefas ajuda você a implementar um processador de tarefas que pode executar as seguintes ações:

* Configurar as informações exigidas para a execução de cada tarefa do Lote.
* Executar todas as ações exigidas por cada tarefa do Lote.
* Salvar as saídas da tarefa no armazenamento persistente.

Apesar de um Processador de tarefas não ser exigido para a execução de tarefas do Lote, a principal vantagem de usar um Processador de tarefas é que ele fornece um wrapper para implementar todas as ações de execução de tarefas em um único local. Por exemplo, se você precisar executar vários aplicativos no contexto de cada tarefa, ou se você precisar copiar dados no armazenamento persistente após a conclusão de cada tarefa.

As ações executadas pelo Processador de tarefas podem simples ou complexas, e muitas ou poucas, conforme a exigência de sua carga de trabalho. Além disso, ao implementar todas as ações de tarefa em um processador de tarefa, você pode atualizar ou adicionar ações prontamente com base em alterações nos requisitos dos aplicativos ou da carga de trabalho. No entanto, em alguns casos, talvez um Processador de tarefas não seja a solução ideal para sua implementação, pois ele pode adicionar uma complexidade desnecessária, por exemplo, ao executar trabalhos que podem ser iniciados rapidamente em uma linha de comando simples.

### <a name="create-a-task-processor-using-the-template"></a>Criar um Processador de tarefas usando o modelo
Para adicionar um Processador de tarefas à solução que você criou anteriormente, execute estas etapas:

1. Abra sua solução existente no Visual Studio.
2. No Gerenciador de Soluções, clique com o botão direito na solução, clique em **Adicionar** e em **Novo Projeto**.
3. No **Visual C#**, clique em **Nuvem** e em **Processador de Tarefas do Lote do Azure**.
4. Digite um nome que descreva o aplicativo e identifique esse projeto como o Processador de tarefas (por exemplo, "ProcessadorDeTarefasLitware").
5. Para criar o projeto, clique em **OK**.
6. Por fim, compile o projeto para forçar o Visual Studio a carregar todos os pacotes NuGet referenciados e verificar se o projeto é válido antes de você começar a modificá-lo.

### <a name="task-processor-template-files-and-their-purpose"></a>Arquivos do modelo do Processador de tarefas e suas finalidades
Quando você cria um projeto usando o modelo do Processador de tarefas, ele gera três grupos de arquivos de código:

* O arquivo de programa principal (Program.cs). Esse arquivo contém o ponto de entrada do programa e a manipulação de exceção de nível superior. Normalmente, não é necessário modificar isso.
* O diretório Framework. Esse diretório contém os arquivos responsáveis pelo trabalho “clichê” realizado pelo programa Gerenciador de trabalho – desempacotar parâmetros, adicionar tarefas ao trabalho do Lote etc. Normalmente, não é necessário modificar esses arquivos.
* O arquivo do Processador de tarefas (TaskProcessor.cs). É nele que você colocará a lógica específica ao aplicativo para execução de uma tarefa (normalmente chamando um executável existente). Código de pré e pós-processamento, como o download dos dados adicionais ou upload de arquivos de resultados, também é colocado nesse local.

Obviamente você pode adicionar outros arquivos conforme o necessário a fim de oferecer suporte ao código do Processador de tarefas, dependendo da complexidade da lógica de divisão do trabalho.

O modelo também gera arquivos de projeto padrão do .NET, como um arquivo .csproj, app.config, packages.config etc.

O restante desta seção descreve os diferentes arquivos e sua estrutura de código, e explica o que cada classe faz.

![Visual Studio Solution Explorer mostrando a solução do modelo do Processador de tarefas][solution_explorer02]

**Arquivos do Framework**

* `Configuration.cs`: Encapsula o carregamento de dados de configuração de trabalho, como detalhes da conta do Lote, credenciais da conta de armazenamento vinculada, informações sobre o trabalho e as tarefas e parâmetros do trabalho. Ele também fornece acesso a variáveis de ambiente definidas pelo Lote (consulte Configurações de ambiente para tarefas, na documentação do Lote) por meio da classe Configuration.EnvironmentVariable.
* `IConfiguration.cs`: Abstrai a implementação da classe Configuration, para que você possa testar a unidade de seu divisor de trabalho usando um objeto de configuração falso ou fictício.
* `TaskProcessorException.cs`: Representa um erro que exige o encerramento do gerenciador de trabalho. TaskProcessorException é usado para encapsular erros “esperados”, em que as informações de diagnóstico específicas podem ser fornecidas como parte do encerramento.

**Processador de tarefas**

* `TaskProcessor.cs`: Executa a tarefa. O framework invoca o método TaskProcessor.Run. Essa é a classe na qual você injetará a lógica específica ao aplicativo de sua tarefa. Implemente o método Run para:
  * Analisar e validar qualquer parâmetro de tarefa
  * Compor a linha de comando para qualquer programa externo que você deseja invocar
  * Registrar quaisquer informações de diagnóstico que você possa precisar para fins de depuração
  * Iniciar um processo usando a linha de comando
  * Aguardar o processo sair
  * Capturar o código de saída do processo para determinar se ele teve êxito ou falhou
  * Salvar os arquivos de saída que você deseja manter no armazenamento persistente

**Arquivos de projeto de linha de comando .NET padrão**

* `App.config`: Arquivo de configuração padrão de aplicativo .NET.
* `Packages.config`: Arquivo de dependência padrão do pacote NuGet.
* `Program.cs`: Contém o ponto de entrada do programa e a manipulação de exceção de nível superior.

## <a name="implementing-the-task-processor"></a>Implementação do Processador de tarefa
Quando você abre o projeto de modelo do Processador de tarefas, o projeto fará o arquivo TaskProcessor.cs ser aberto por padrão. Você pode implementar a lógica de execução para as tarefas em sua carga de trabalho usando o método Run() exibido abaixo:

```csharp
/// <summary>
/// Runs the task processing logic. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The task processor framework invokes the Run method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation will execute an external program (from resource files or
/// an application package), check the exit code of that program and
/// save output files to persistent storage.
/// </summary>
public async Task<int> Run()

{
    try
    {
        //Your code for the task processor goes here.
        var command = $"compare {_parameters["Frame1"]} {_parameters["Frame2"]} compare.gif";
        using (var process = Process.Start($"cmd /c {command}"))
        {
            process.WaitForExit();
            var taskOutputStorage = new TaskOutputStorage(
            _configuration.StorageAccount,
            _configuration.JobId,
            _configuration.TaskId
            );
            await taskOutputStorage.SaveAsync(
            TaskOutputKind.TaskOutput,
            @"..\stdout.txt",
            @"stdout.txt"
            );
            return process.ExitCode;
        }
    }
    catch (Exception ex)
    {
        throw new TaskProcessorException(
        $"{ex.GetType().Name} exception in run task processor: {ex.Message}",
        ex
        );
    }
}
```
> [!NOTE]
> A seção anotada no método Run() é a única seção do código do modelo do Processador de tarefas que você pode modificar adicionando a lógica de execução às tarefas em sua carga de trabalho. Se você quiser modificar uma seção diferente do modelo, primeiro você precisa ter certeza de que está familiarizado com o funcionamento do Lote revisando a documentação do Lote e experimentando alguns dos exemplos de código do Lote.
> 
> 

O método Run() é responsável por iniciar a linha de comando, iniciar um ou mais processos, aguardar a conclusão de todos os processos, salvar os resultados e, finalmente, retornar um código de saída. O método Run() é onde você implementa a lógica de processamento das tarefas. A estrutura do Processador de tarefas invoca o método Run() para você. Não é necessário chamá-lo.

Sua implementação de Run() tem acesso a:

* Os parâmetros da tarefa, por meio do campo `_parameters`.
* As IDs do trabalho e da tarefa, por meio dos campos `_jobId` e `_taskId`.
* A configuração da tarefa, por meio do campo `_configuration` .

**Falha da tarefa**

Em caso de falha, você poderá sair do método Run() lançando uma exceção, mas isso deixa o manipulador de exceção de nível superior no controle do código de saída da tarefa. Se você precisar controlar o código de saída para que possa diferenciar tipos diferentes de falha, por exemplo, para fins de diagnóstico ou porque alguns modos de falha devem encerrar o trabalho e outros não, saia do método Run() retornando um código de saída diferente de zero. Isso se torna o código de saída da tarefa.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Códigos e exceções de saída no modelo do Processador de tarefas
Os códigos de saída e exceções fornecem um mecanismo para determinar o resultado da execução de um programa, e podem ajudar a identificar problemas com a execução do programa. O modelo do Processador de tarefas implementa os códigos e exceções de saída descritos nesta seção.

Uma tarefa do Processador de tarefas implementada com o modelo do Processador de tarefas pode retornar três códigos de saída possíveis:

| Código | DESCRIÇÃO |
| --- | --- |
| [Process.ExitCode][process_exitcode] |O Processador de tarefas foi executado até a conclusão. Observe que isso não significa que o programa invocado foi bem-sucedida, apenas que o Processador de tarefas o invocou e executou com êxito qualquer pós-processamento sem exceções. O significado do código de saída depende do programa invocado, normalmente o código de saída 0 significa que o programa foi bem-sucedido e qualquer outro código de saída significa que o programa falhou. |
| 1 |A tarefa do Processador de tarefas falhou com uma exceção em uma parte “esperada” do programa. A exceção foi convertida para um `TaskProcessorException` com informações de diagnóstico e, quando possível, sugestões para resolver a falha. |
| 2 |A tarefa do Processador de tarefas falhou com uma exceção “inesperada”. A exceção foi registrada na saída padrão, mas o Processador de tarefas não conseguiu adicionar informações de diagnóstico ou correção adicionais. |

> [!NOTE]
> Se o programa que você invocar usar códigos de saída 1 e 2 para indicar modos de falha específicos, o uso de códigos de saída 1 e 2 para erros do processador de tarefas é ambíguo. Você pode alterar esses códigos de erro do Processador de tarefas para códigos de saída diferentes, editando os casos de exceção no arquivo Program.cs.
> 
> 

Todas as informações retornadas pelas exceções são gravadas nos arquivos stdout.txt e stderr.txt. Para saber mais, confira Manipulação de erros na documentação do Lote.

### <a name="client-considerations"></a>Considerações do cliente
**Credenciais de armazenamento**

Se o seu Processador de tarefas usa o Armazenamento de Blobs do Azure para persistir saídas, por exemplo, usando a biblioteca do auxiliar de convenções de arquivo, ele precisará acessar *ou* as credenciais da conta de armazenamento de nuvem *ou* uma URL do contêiner de blobs que inclui uma SAS (assinatura de acesso compartilhado). O modelo inclui suporte para o fornecimento das credenciais por meio de variáveis de ambiente comuns. O cliente pode passar as credenciais de armazenamento da seguinte maneira:

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

Assim, a conta de armazenamento fica disponível na classe TaskProcessor por meio da propriedade `_configuration.StorageAccount` .

Se você preferir usar uma URL de contêiner com a SAS, também será possível passar isso por meio de uma configuração de ambiente de trabalho comum, mas o modelo do Processador de tarefas não inclui suporte interno para isso no momento.

**Configuração de armazenamento**

Recomendamos que o cliente ou a tarefa do Gerenciador de trabalho crie os contêineres exigidos pelas tarefas antes de adicionar as tarefas ao trabalho. Isso será obrigatório se você usar uma URL de contêiner com SAS, pois essa URL não inclui a permissão para criar o contêiner. Isso é recomendado mesmo se você passar as credenciais da conta de armazenamento, pois libera cada tarefa de precisar chamar CloudBlobContainer.CreateIfNotExistsAsync no contêiner.

## <a name="pass-parameters-and-environment-variables"></a>Passar parâmetros e variáveis de ambiente
### <a name="pass-environment-settings"></a>Passar configurações de ambiente
Um cliente pode passar informações para a tarefa do Gerenciador de trabalho na forma de configurações de ambiente. Assim, essas informações podem ser usadas pelo Gerenciador de trabalho ao gerar as tarefas do Processador de tarefas que serão executadas como parte do trabalho de computação. Veja alguns exemplos das informações que você pode passar como configurações de ambiente:

* Nome da conta de armazenamento e chaves da conta
* URL da conta do Lote
* Chave da conta do Lote

O serviço do Lote tem um mecanismo simples para passar configurações de ambiente para uma tarefa do Gerenciador de trabalho usando a propriedade `EnvironmentSettings` em [Microsoft.Azure.Batch.JobManagerTask][net_jobmanagertask].

Por exemplo, para obter a instância do `BatchClient` para uma conta do Lote, você pode passar a URL e credenciais de chave compartilhadas como variáveis do ambiente a partir do código do cliente para a conta do Lote. Da mesma forma, para acessar a conta de armazenamento vinculada à conta do Lote, você pode passar o nome da conta de armazenamento e a chave da conta de armazenamento como variáveis de ambiente.

### <a name="pass-parameters-to-the-job-manager-template"></a>Passar parâmetros para o modelo do Gerenciador de trabalho
Em muitos casos, é útil passar parâmetros por trabalho para a tarefa de Gerenciador de trabalho, a fim de controlar o processo de divisão de trabalho ou configurar as tarefas do trabalho. Faça isso carregando um arquivo JSON chamado parameters.json como um arquivo de recurso para a tarefa do Gerenciador de trabalho. Assim, os parâmetros podem ser disponibilizados no campo `JobSplitter._parameters` no modelo do Gerenciador de trabalho.

> [!NOTE]
> O manipulador de parâmetro interno oferece suporte somente aos dicionários de cadeia de caracteres para cadeia de caracteres. Se você quiser passar valores JSON complexos como valores de parâmetro, será necessário passá-los como cadeias de caracteres e analisá-los no divisor de trabalho, ou modificar o método `Configuration.GetJobParameters` do framework.
> 
> 

### <a name="pass-parameters-to-the-task-processor-template"></a>Passar parâmetros para o modelo de Processador de tarefas
Você também pode passar parâmetros para tarefas individuais implementadas usando o modelo do Processador de tarefas. Assim como acontece com o modelo do Gerenciador de trabalho, o modelo do Processador de tarefas procura um arquivo de recurso chamado

parameters.json e, se o encontrar, o carrega como o dicionário de parâmetros. Há algumas opções de como passar parâmetros para as tarefas do Processador de tarefa:

* Reutilize os parâmetros de trabalho JSON. Isso funciona bem se os únicos parâmetros forem de todo o trabalho (por exemplo, uma renderização de altura e largura). Para fazer isso, durante a criação de um CloudTask no divisor de trabalho, adicione uma referência ao objeto do arquivo de recurso parameters.json do ResourceFiles da tarefa do Gerenciador de trabalho (`JobSplitter._jobManagerTask.ResourceFiles`) à coleção de ResourceFiles do CloudTask.
* Gere e carregue um documento parameters.json específico à tarefa como parte da execução do divisor de trabalho, e faça referência a esse blob na coleção de arquivos de recursos da tarefa. Isso é necessário se tarefas diferentes tiverem parâmetros diferentes. Um exemplo seria um cenário de renderização 3D no qual o índice do quadro é passado para a tarefa como um parâmetro.

> [!NOTE]
> O manipulador de parâmetro interno oferece suporte somente aos dicionários de cadeia de caracteres para cadeia de caracteres. Se você quiser passar valores JSON complexos como valores de parâmetro, será necessário passá-los como cadeias de caracteres e analisá-los no Processador de tarefas, ou modificar o método `Configuration.GetTaskParameters` do framework.
> 
> 

## <a name="next-steps"></a>Próximas etapas
### <a name="persist-job-and-task-output-to-azure-storage"></a>Persistir saída de tarefa e de trabalho no Armazenamento do Azure
Outra ferramenta útil no desenvolvimento de soluções do Lote são as [Convenções de Arquivo do Lote do Azure][nuget_package]. Use essa biblioteca de classes .NET (em versão de visualização) em seus aplicativos .NET do Lote para armazenar e recuperar com facilidade as saídas de tarefas no Armazenamento do Azure. [Persistir e saída de tarefa e de trabalho do Lote do Azure](batch-task-output.md) contém uma discussão completa sobre a biblioteca e seu uso.


[net_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobmanagertask.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[process_exitcode]: https://msdn.microsoft.com/library/system.diagnostics.process.exitcode.aspx
[vs_gallery]: https://visualstudiogallery.msdn.microsoft.com/
[vs_gallery_templates]: https://go.microsoft.com/fwlink/?linkid=820714
[vs_find_use_ext]: https://msdn.microsoft.com/library/dd293638.aspx

[diagram01]: ./media/batch-visual-studio-templates/diagram01.png
[solution_explorer01]: ./media/batch-visual-studio-templates/solution_explorer01.png
[solution_explorer02]: ./media/batch-visual-studio-templates/solution_explorer02.png
