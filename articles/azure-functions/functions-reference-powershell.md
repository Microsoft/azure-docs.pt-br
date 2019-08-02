---
title: Referência do desenvolvedor do PowerShell para Azure Functions
description: Entenda como desenvolver funções usando o PowerShell.
services: functions
documentationcenter: na
author: tylerleonhardt
manager: jeconnoc
ms.service: azure-functions
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: tyleonha
ms.reviewer: glenga
ms.openlocfilehash: a75bdaf0e26193a5b2792b52923c085eff89b83f
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706408"
---
# <a name="azure-functions-powershell-developer-guide"></a>Guia do desenvolvedor do PowerShell de funções do Azure

Este artigo fornece detalhes sobre como escrever funções do Azure usando o PowerShell.

[!INCLUDE [functions-powershell-preview-note](../../includes/functions-powershell-preview-note.md)]

Uma função do PowerShell Azure (função) é representada como um script do PowerShell que é executado quando disparado. Cada script de função tem um relacionados `function.json` arquivo que define como a função se comporta, como como ele é acionado e seus parâmetros de entrada e saídos. Para obter mais informações, consulte o [artigo de associação e gatilhos](functions-triggers-bindings.md). 

Como outros tipos de funções, funções de script do PowerShell ter parâmetros que correspondem aos nomes de todas as associações de entrada definidos no `function.json` arquivo. Um `TriggerMetadata` que contém informações adicionais sobre o gatilho que iniciou a função também é passado no parâmetro.

Este artigo pressupõe que você já tenha lido a [Referência do desenvolvedor do Azure Functions](functions-reference.md). Você também deve ter concluído a [início rápido de funções do PowerShell](functions-create-first-function-powershell.md) para criar sua primeira função do PowerShell.

## <a name="folder-structure"></a>Estrutura de pastas

A estrutura de pastas necessárias para um projeto do PowerShell é semelhante ao seguinte. Este padrão pode ser alterado. Para mais informações, consulte a seção [scriptArquivo](#configure-function-scriptfile) abaixo.

```
PSFunctionApp
 | - MyFirstFunction
 | | - run.ps1
 | | - function.json
 | - MySecondFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - myFirstHelperModule
 | | | - myFirstHelperModule.psd1
 | | | - myFirstHelperModule.psm1
 | | - mySecondHelperModule
 | | | - mySecondHelperModule.psd1
 | | | - mySecondHelperModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
 | - profile.ps1
 | - extensions.csproj
 | - bin
```

Na raiz do projeto, há um compartilhado [ `host.json` ](functions-host-json.md) arquivo que pode ser usado para configurar o aplicativo de funções. Cada função tem uma pasta com seu próprio arquivo de código (. ps1) e o arquivo de configuração de associação (`function.json`). O nome da pasta pai do arquivo de Function. JSON é sempre o nome da sua função.

Determinadas associações exigem a presença de um `extensions.csproj` arquivo. Associando extensões, necessárias no [versão 2. x](functions-versions.md) de tempo de execução de funções, são definidos na `extensions.csproj` arquivo, com os arquivos de biblioteca real no `bin` pasta. Ao desenvolver localmente, você precisa [registrar as extensões de associação](functions-bindings-register.md#extension-bundles). Ao desenvolver funções no portal do Azure, esse registro é feito para você.

Em aplicativos de funções do PowerShell, você pode, opcionalmente, ter uma `profile.ps1` que é executado quando um aplicativo de função começa a ser executado (caso contrário, conhecido como um  *[inicialização a frio](#cold-start)* . Para obter mais informações, consulte [perfil do PowerShell](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>Definindo um script do PowerShell como uma função

Por padrão, o tempo de execução de Funções procura sua função em `run.ps1`, onde `run.ps1` compartilha o mesmo diretório pai que o `function.json` correspondente.

O script é passado um número de argumentos na execução. Para lidar com esses parâmetros, adicione um `param` bloco na parte superior do seu script, como no exemplo a seguir:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>Parâmetro TriggerMetadata

O `TriggerMetadata` parâmetro é usado para fornecer informações adicionais sobre o gatilho. Os metadados adicionais variam de associação para associação, mas todos eles contenham uma `sys` propriedade que contém os seguintes dados:

```powershell
$TriggerMetadata.sys
```

| Propriedade   | Description                                     | Tipo     |
|------------|-------------------------------------------------|----------|
| UtcNow     | Quando, no UTC, a função foi disparada        | DateTime |
| MethodName | O nome da função que foi disparado     | cadeia de caracteres   |
| RandGuid   | um guid exclusivo para a execução da função | cadeia de caracteres   |

Cada tipo de gatilho tem um conjunto diferente de metadados. Por exemplo, o `$TriggerMetadata` para `QueueTrigger` contém o `InsertionTime`, `Id`, `DequeueCount`, entre outras coisas. Para obter mais informações sobre os metadados do gatilho de fila, vá para o [documentação oficial para gatilhos de fila](functions-bindings-storage-queue.md#trigger---message-metadata). Verifique a documentação sobre o [gatilhos](functions-triggers-bindings.md) você está trabalhando para ver o que vem de dentro de metadados de gatilho.

## <a name="bindings"></a>Associações

No PowerShell, [associações](functions-triggers-bindings.md) são configurados e definido no Function. JSON de uma função. As funções interagem com as ligações de várias maneiras.

### <a name="reading-trigger-and-input-data"></a>Gatilho de leitura e dados de entrada

Gatilho e associações de entrada são lidas como parâmetros passados para a função. As associações de entrada tem um `direction` definido como `in` no Function. JSON. O `name` propriedade definida no `function.json` é o nome do parâmetro, no `param` bloco. Como o PowerShell usa parâmetros nomeados para associação, não importa a ordem dos parâmetros. No entanto, é uma prática recomendada de seguir a ordem das associações definidas no `function.json`.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Gravar dados de saída

Em funções, uma associação de saída tem um `direction` definido como `out` no Function. JSON. Você pode gravar em uma associação de saída usando o `Push-OutputBinding` cmdlet, que está disponível para o tempo de execução de funções. Em todos os casos, o `name` propriedade da associação, conforme definido na `function.json` corresponde à `Name` parâmetro do `Push-OutputBinding` cmdlet.

A seguir mostra como chamar `Push-OutputBinding` em seu script de função:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

Você também pode passar um valor para uma associação específica por meio do pipeline.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding` se comporta de maneira diferente com base no valor especificado para `-Name`:

* Quando o nome especificado não pode ser resolvido para uma associação de saída válido, um erro será gerado.

* Quando a associação de saída aceita uma coleção de valores, você pode chamar `Push-OutputBinding` repetidamente para enviar por push vários valores.

* Quando a associação de saída só aceita um valor singleton, chamando `Push-OutputBinding` uma segunda vez gerará um erro.

#### <a name="push-outputbinding-syntax"></a>`Push-OutputBinding` Sintaxe

Estes são os parâmetros válidos para a chamada `Push-OutputBinding`:

| Nome | Tipo | Position | DESCRIÇÃO |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | string | 1 | O nome da associação de saída que você deseja definir. |
| **`-Value`** | Objeto | 2 | O valor da associação de saída você deseja definir, que é aceito na pipeline ByValue. |
| **`-Clobber`** | SwitchParameter | nomeado | (Opcional) Quando especificado, obrigará o valor a ser definido para uma associação de saída especificado. | 

Também há suporte para os seguintes parâmetros comuns: 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

Para obter mais informações, consulte [CommonParameters sobre](https://go.microsoft.com/fwlink/?LinkID=113216).

#### <a name="push-outputbinding-example-http-responses"></a>Exemplo de envio por push OutputBinding: Respostas HTTP

Um gatilho HTTP retornará uma resposta usando uma associação de saída denominada `response`. No exemplo a seguir, a associação de saída de `response` tem o valor de "saída de #1":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Como a saída HTTP, que aceita apenas um valor de singleton, é lançado quando um erro `Push-OutputBinding` é chamado pela segunda vez.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

Para saídas que só aceitam valores singleton, você pode usar o `-Clobber` parâmetro para substituir o valor antigo em vez de tentar adicionar a uma coleção. O exemplo a seguir pressupõe que você já adicionou um valor. Usando `-Clobber`, a resposta do exemplo a seguir substitui o valor existente para retornar um valor de "saída de #3":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Exemplo de envio por push OutputBinding: Associação de saída de fila

`Push-OutputBinding` é usado para enviar dados para as associações de saída, como um [associação de saída do armazenamento de filas do Azure](functions-bindings-storage-queue.md#output). No exemplo a seguir, a mensagem gravada na fila tem um valor de "saída de #1":

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

A associação de saída para uma fila de armazenamento aceita vários valores de saída. Nesse caso, chamando o exemplo a seguir após o primeiro grava na fila uma lista com dois itens: "saídas #1" e "#2".

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

O exemplo a seguir, quando chamado depois que as duas anteriores, adiciona mais dois valores à coleção de saída:

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

Quando gravadas na fila, a mensagem contém esses quatro valores: "saída de #1", "saída de #2", "saída de #3" e "saída de #4".

#### <a name="get-outputbinding-cmdlet"></a>`Get-OutputBinding` cmdlet

Você pode usar o `Get-OutputBinding` cmdlet para recuperar os valores definidos atualmente para as associações de saída. Este cmdlet recupera um hashtable que contém os nomes das associações de saída com os respectivos valores. 

A seguir está um exemplo de uso `Get-OutputBinding` para retornar valores de associação atual:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding` também contém um parâmetro chamado `-Name`, que pode ser usado para filtrar a associação retornada, como no exemplo a seguir:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

Caracteres curinga (*) têm suporte no `Get-OutputBinding`.

## <a name="logging"></a>Registrando em log

Registro em log em funções do PowerShell funciona como log regular do PowerShell. Você pode usar os cmdlets de registro em log para gravar cada fluxo de saída. Cada cmdlet é mapeado para um nível de log usado pelas funções.

| Funções de nível de log | Cmdlet de registro em log |
| ------------- | -------------- |
| Erro | **`Write-Error`** |
| Aviso | **`Write-Warning`**  | 
| Informações | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Informações | Grava _informações_ log de nível. |
| Depurar | **`Write-Debug`** |
| Rastreamento | **`Write-Progress`** <br /> **`Write-Verbose`** |

Além desses cmdlets, todos os elementos escritos para o pipeline é redirecionado para a `Information` exibido com a formatação do PowerShell padrão e nível de log.

> [!IMPORTANT]
> Usando o `Write-Verbose` ou `Write-Debug` cmdlets não é suficiente para ver detalhado e o log de nível de depuração. Você também deve configurar o limite de nível de log, que declara qual nível de logs que realmente se importa. Para obter mais informações, consulte [configurar o nível de log do aplicativo de funções](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>Configurar o nível de log do aplicativo de funções

O Azure Functions permite definir o nível de limite para que seja fácil para as funções de maneira grava os logs de controle. Para definir o limite para todos os rastreamentos gravados no console, use o `logging.logLevel.default` propriedade em de [ `host.json` arquivo][referência de host. json]. Essa configuração se aplica a todas as funções em seu aplicativo de função.

O exemplo a seguir define o limite para habilitar o log detalhado para todas as funções, mas define o limite para habilitar o log de depuração para uma função chamada `MyFunction`:

```json
{
    "logging": {
        "logLevel": {
            "Function.MyFunction": "Debug",
            "default": "Trace"
        }
    }
}  
```

Para obter mais informações, consulte a [referência de host. JSON].

### <a name="viewing-the-logs"></a>Exibir os logs

Se seu aplicativo de funções está em execução no Azure, você pode usar o Application Insights para monitorá-lo. Leia [Monitorado o Azure Functions](functions-monitoring.md) para saber mais sobre como exibir e consultar logs de função.

Se você estiver executando seu aplicativo de funções localmente para desenvolvimento, registra em log padrão para o sistema de arquivos. Para ver os logs no console, defina as `AZURE_FUNCTIONS_ENVIRONMENT` variável de ambiente `Development` antes de iniciar o aplicativo de funções.

## <a name="triggers-and-bindings-types"></a>Gatilhos e associações de tipos

Há um número de gatilhos e associações para usar com seu aplicativo de funções. A lista completa de gatilhos e associações [podem ser encontradas aqui](functions-triggers-bindings.md#supported-bindings).

Todos os gatilhos e associações são representadas no código, como alguns tipos de dados real:

* Tabela de hash
* cadeia de caracteres
* byte[]
* int
* double
* HttpRequestContext
* HttpResponseContext

Os primeiros cinco tipos nessa lista são tipos de .NET padrão. As duas últimas são usadas somente pelo [HttpTrigger gatilho](#http-triggers-and-bindings).

Cada parâmetro de associação em suas funções deve ser um desses tipos.

### <a name="http-triggers-and-bindings"></a>Gatilhos e associações HTTP

HTTP e gatilhos de webhook e associações de saída HTTP usam objetos de solicitação e resposta para representar as mensagens HTTP.

#### <a name="request-object"></a>Objeto da solicitação

O objeto de solicitação que é passado para o script é do tipo `HttpRequestContext`, que tem as seguintes propriedades:

| Propriedade  | Description                                                    | Tipo                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Um objeto que contém o corpo da solicitação. `Body` é serializado no melhor tipo com base nos dados. Por exemplo, se os dados JSON, ele é passado como uma tabela de hash. Se os dados forem uma cadeia de caracteres, ele é passado como uma cadeia de caracteres. | objeto |
| **`Headers`** | Um dicionário que contém os cabeçalhos de solicitação.                | Dicionário < cadeia de caracteres, cadeia de caracteres ><sup>*</sup> |
| **`Method`** | O método HTTP da solicitação.                                | cadeia de caracteres                    |
| **`Params`**  | Um objeto que contém os parâmetros de roteamento da solicitação. | Dicionário < cadeia de caracteres, cadeia de caracteres ><sup>*</sup> |
| **`Query`** | Um objeto que contém os parâmetros da consulta.                  | Dicionário < cadeia de caracteres, cadeia de caracteres ><sup>*</sup> |
| **`Url`** | A URL da solicitação.                                        | cadeia de caracteres                    |

<sup>*</sup> Todos os `Dictionary<string,string>` chaves diferenciam maiusculas de minúsculas.

#### <a name="response-object"></a>Objeto de resposta

O objeto de resposta que você deve enviar de volta é do tipo `HttpResponseContext`, que tem as seguintes propriedades:

| Propriedade      | Description                                                 | Tipo                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Um objeto que contém o corpo da resposta.           | objeto                    |
| **`ContentType`** | Uma mão curta para definir o tipo de conteúdo para a resposta. | cadeia de caracteres                    |
| **`Headers`** | Um objeto que contém os cabeçalhos da resposta.               | Dicionário ou tabela de hash   |
| **`StatusCode`**  | O código de status HTTP da resposta.                       | cadeia de caracteres ou inteiro             |

#### <a name="accessing-the-request-and-response"></a>Acessar a solicitação e a resposta

Ao trabalhar com gatilhos HTTP, você pode acessar a solicitação HTTP da mesma maneira que faria com qualquer outra associação de entrada. Ele está no `param` bloco.

Use um `HttpResponseContext` objeto para retornar uma resposta, conforme mostrado a seguir:

`function.json`

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "anonymous"
    },
    {
      "type": "http",
      "direction": "out"
    }
  ]
}
```

`run.ps1`

```powershell
param($req, $TriggerMetadata)

$name = $req.Query.Name

Push-OutputBinding -Name res -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "Hello $name!"
})
```

O resultado de chamar essa função seria:

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>A conversão de tipo para os gatilhos e associações

Para determinadas associações, como a associação de blob, você é capaz de especificar o tipo do parâmetro.

Por exemplo, para que os dados do armazenamento de BLOBs fornecido como uma cadeia de caracteres, adicione o seguinte tipo convertido em meu `param` bloco:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>Perfil do PowerShell

No PowerShell, há o conceito de um perfil do PowerShell. Se você não estiver familiarizado com perfis do PowerShell, consulte [sobre os perfis](/powershell/module/microsoft.powershell.core/about/about_profiles).

Em funções do PowerShell, o script de perfil é executado quando o aplicativo de funções é iniciado. Aplicativos de função iniciar quando implantado pela primeira vez e depois que está sendo rodado em marcha lento ([inicialização a frio](#cold-start)).

Quando você cria um aplicativo de funções usando ferramentas como o Visual Studio Code e ferramentas básicas do Azure Functions, um padrão `profile.ps1` é criado para você. O perfil padrão é mantido [no repositório do GitHub de ferramentas principal](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) e contém:

* Autenticação automática MSI do Azure.
* A capacidade de ativar o Azure PowerShell `AzureRM` aliases do PowerShell se desejar.

## <a name="powershell-version"></a>Versão do PowerShell

A tabela a seguir mostra a versão do PowerShell usada por cada versão principal do tempo de execução de funções:

| Versão do Functions | Versão do PowerShell                             |
|-------------------|------------------------------------------------|
| 1.x               | Windows PowerShell 5.1 (bloqueada pelo tempo de execução) |
| 2. x               | O PowerShell Core 6                              |

Você pode ver a versão atual, imprimindo `$PSVersionTable` de qualquer função.

## <a name="dependency-management"></a>Gerenciamento de dependências

Funções do PowerShell dão suporte a gerenciamento de módulos do Azure pelo serviço. Modificando o host. JSON e definindo a propriedade managedDependency habilitada como true, o arquivo requirements.psd1 será processado. Os módulos do Azure mais recente serão baixados automaticamente e disponibilizados para a função.

host.json
```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

requirements.psd1

```powershell
@{
    Az = '1.*'
}
```

Aproveitando personalizado ou por módulos do [da Galeria do PowerShell](https://powershellgallery.com) é um pouco diferente de como você faria normalmente.

Quando você instala o módulo em seu computador local, ele passa em uma das pastas disponíveis globalmente em seu `$env:PSModulePath`. Uma vez que sua função é executada no Azure, você não terá acesso para os módulos instalados no seu computador. Isso requer que o `$env:PSModulePath` para uma função do PowerShell aplicativo difere `$env:PSModulePath` em um script do PowerShell regular.

Em funções, `PSModulePath` contém dois caminhos:

* Um `Modules` pasta que existe na raiz do seu aplicativo de funções.
* Um caminho para um `Modules` pasta em que reside dentro o trabalhador de linguagem do PowerShell.

### <a name="function-app-level-modules-folder"></a>Nível do aplicativo de funções `Modules` pasta

Para usar módulos personalizados ou módulos do PowerShell da Galeria do PowerShell, você pode colocar os módulos nos quais suas funções dependem de um `Modules` pasta. Nessa pasta, os módulos ficam automaticamente disponíveis no tempo de execução de funções. Qualquer função no aplicativo de funções pode usar esses módulos.

Para tirar proveito desse recurso, crie um `Modules` pasta na raiz do seu aplicativo de funções. Salve os módulos que você deseja usar em suas funções nesse local.

```powershell
mkdir ./Modules
Save-Module MyGalleryModule -Path ./Modules
```

Use `Save-Module` para salvar todos os módulos que usam suas funções, ou copie seus próprios módulos personalizados para o `Modules` pasta. Com uma pasta de módulos, seu aplicativo de função deve ter a seguinte estrutura de pasta:

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyGalleryModule
 | | - MyOtherGalleryModule
 | | - MyCustomModule.psm1
 | - local.settings.json
 | - host.json
```

Quando você inicia seu aplicativo de funções, o trabalhador de linguagem do PowerShell adiciona esses `Modules` pasta para o `$env:PSModulePath` para que você pode confiar no módulo realiza o carregamento automático, exatamente como você faria em um script do PowerShell regular.

### <a name="language-worker-level-modules-folder"></a>Nível de trabalho de linguagem `Modules` pasta

Vários módulos são normalmente usados pelo trabalhador de linguagem do PowerShell. Esses módulos são definidos na última posição de `PSModulePath`. 

A lista atual de módulos é da seguinte maneira:

* [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): módulo usado para trabalhar com arquivos mortos, como `.zip`, `.nupkg`e outros.
* **ThreadJob**: Uma implementação baseada em threads de trabalho do PowerShell APIs.

A versão mais recente desses módulos é usada pelas funções. Para usar uma versão específica desses módulos, você pode colocar a versão específica no `Modules` pasta do seu aplicativo de funções.

## <a name="environment-variables"></a>Variáveis de ambiente

Em funções, [configurações do aplicativo](functions-app-settings.md), como conexão de serviço cadeias de caracteres, são expostas como variáveis de ambiente durante a execução. Você pode acessar essas configurações usando `$env:NAME_OF_ENV_VAR`, conforme mostrado no exemplo a seguir:

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Ao executar localmente, as configurações do aplicativo são lidos a partir de [Settings](functions-run-local.md#local-settings-file) arquivo de projeto.

## <a name="concurrency"></a>Simultaneidade

Por padrão, o tempo de execução do PowerShell de funções só pode processar uma invocação de uma função por vez. No entanto, esse nível de simultaneidade pode não ser suficiente nas seguintes situações:

* Quando você estiver tentando manipular um grande número de invocações ao mesmo tempo.
* Quando você tem funções que chamam outras funções dentro do mesmo aplicativo de funções.

Você pode alterar esse comportamento, definindo a variável de ambiente a seguir como um valor inteiro:

```
PSWorkerInProcConcurrencyUpperBound
```

Definir essa variável de ambiente na [configurações do aplicativo](functions-app-settings.md) de seu aplicativo de funções.

### <a name="considerations-for-using-concurrency"></a>Considerações sobre o uso de simultaneidade

O PowerShell é um _thread único_ linguagem de scripts por padrão. No entanto, a simultaneidade pode ser adicionada por meio de vários espaços de execução do PowerShell no mesmo processo. Esse recurso é como funciona o tempo de execução do PowerShell do Azure Functions.

Existem algumas desvantagens nessa abordagem.

#### <a name="concurrency-is-only-as-good-as-the-machine-its-running-on"></a>Simultaneidade só é tão bom quanto o computador está em execução

Se seu aplicativo de funções é executado em um [plano do serviço de aplicativo](functions-scale.md#app-service-plan) que dá suporte apenas a um único núcleo e, em seguida, simultaneidade não ajudará muito. Isso ocorre porque há não há núcleos adicionais para ajudar a balancear a carga. Nesse caso, o desempenho pode variar quando tiver de núcleo único de alternância de contexto entre espaços de execução.

O [plano de consumo](functions-scale.md#consumption-plan) é executado usando apenas um núcleo, portanto, você não pode aproveitar a simultaneidade. Se você quiser tirar total proveito da simultaneidade, em vez disso, implante suas funções para um aplicativo de funções em execução em um plano de serviço de aplicativo dedicado com núcleos suficientes.

#### <a name="azure-powershell-state"></a>Estado do PowerShell do Azure

O Azure PowerShell usa alguns _nível de processo_ contextos e estado para ajudar a economizar digitando em excesso. No entanto, se você ativa a simultaneidade no aplicativo de funções e invocar ações que alteram o estado, você pode acabar com condições de corrida. Essas condições de corrida são difíceis de depurar porque uma invocação se baseia em um determinado estado e a outra invocação alterou o estado.

Há enorme valor em simultaneidade com o Azure PowerShell, já que algumas operações podem demorar um tempo considerável. No entanto, você deve continuar com cuidado. Se você suspeitar de que você está sofrendo uma condição de corrida, defina a simultaneidade para `1` e tente a solicitação novamente.

## <a name="configure-function-scriptfile"></a>Configurar função `scriptFile`

Por padrão, uma função do PowerShell é executada a partir `run.ps1`, um arquivo que compartilha o mesmo diretório pai correspondente `function.json`.

O `scriptFile` propriedade no `function.json` pode ser usado para obter uma estrutura de pastas que se parece com o exemplo a seguir:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

Nesse caso, o `function.json` para `myFunction` inclui um `scriptFile` propriedade referenciando o arquivo com a função exportada para ser executado.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>Usar módulos do PowerShell ao configurar um ponto de entrada

Este artigo tenha mostrado funções do PowerShell no padrão `run.ps1` gerado pelos modelos de arquivo de script.
No entanto, você também pode incluir suas funções em módulos do PowerShell. Você pode fazer referência a seu código de função específica no módulo usando o `scriptFile` e `entryPoint` campos no Function. JSON ' arquivo de configuração.

Nesse caso, `entryPoint` é o nome de uma função ou o cmdlet no módulo do PowerShell referenciado em `scriptFile`.

Considere a seguinte estrutura de pasta:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.psm1
```

Onde `PSFunction.psm1` contém:

```powershell
function Invoke-PSTestFunc {
    param($InputBinding, $TriggerMetadata)

    Push-OutputBinding -Name OutputBinding -Value "output"
}

Export-ModuleMember -Function "Invoke-PSTestFunc"
```

Neste exemplo, a configuração para `myFunction` inclui um `scriptFile` propriedade que faz referência `PSFunction.psm1`, que é um módulo do PowerShell em outra pasta.  O `entryPoint` referências de propriedade a `Invoke-PSTestFunc` função, que é o ponto de entrada no módulo.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

Com essa configuração, o `Invoke-PSTestFunc` é executado exatamente como um `run.ps1` seria.

## <a name="considerations-for-powershell-functions"></a>Considerações para funções do PowerShell

Ao trabalhar com funções do PowerShell, esteja ciente das considerações nas seções a seguir.

### <a name="cold-start"></a>Inicialização a frio

Ao desenvolver funções do Azure na [modelo de hospedagem sem servidor](functions-scale.md#consumption-plan), cold inicia é uma realidade. *Inicialização a frio* refere-se ao período de tempo ele leva para seu aplicativo de funções iniciar a execução para processar uma solicitação. Inicialização a frio ocorre mais frequentemente no consumo de plano porque o aplicativo de funções é desligado durante períodos de inatividade.

### <a name="bundle-modules-instead-of-using-install-module"></a>Módulos de pacote em vez de usar `Install-Module`

O script é executado em cada invocação. Evite usar `Install-Module` em seu script. Em vez disso use `Save-Module` antes da publicação para que sua função não tem a perder tempo para baixar o módulo. Se inicializações a frio têm impacto sobre suas funções, considere implantar seu aplicativo de funções para um [plano do serviço de aplicativo](functions-scale.md#app-service-plan) definido como *AlwaysOn* ou uma [plano Premium](functions-scale.md#premium-plan).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes recursos:

* [Práticas recomendadas para o Azure Functions](functions-best-practices.md)
* [Referência do desenvolvedor do Azure Functions](functions-reference.md)
* [Gatilhos e associações de Azure Functions](functions-triggers-bindings.md)

[referência de host. JSON]: functions-host-json.md
