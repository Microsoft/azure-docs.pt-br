---
title: Referência do desenvolvedor do PowerShell para Azure Functions
description: Entenda como desenvolver funções usando o PowerShell.
author: eamonoreilly
ms.topic: conceptual
ms.custom: devx-track-dotnet, devx-track-azurepowershell
ms.date: 04/22/2019
ms.openlocfilehash: 61ed3ed274505101c65e251260bd759fe78f7b31
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936780"
---
# <a name="azure-functions-powershell-developer-guide"></a>Guia do desenvolvedor do PowerShell do Azure Functions

Este artigo fornece detalhes sobre como você escreve Azure Functions usando o PowerShell.

Uma função do PowerShell do Azure (função) é representada como um script do PowerShell que é executado quando disparado. Cada script de função tem um `function.json` arquivo relacionado que define como a função se comporta, como como ele é disparado e seus parâmetros de entrada e saída. Para saber mais, confira o [artigo gatilhos e Associação](functions-triggers-bindings.md). 

Assim como outros tipos de funções, as funções de script do PowerShell assumem parâmetros que correspondem aos nomes de todas as associações de entrada definidas no `function.json` arquivo. `TriggerMetadata`Também é passado um parâmetro que contém informações adicionais sobre o gatilho que iniciou a função.

Este artigo pressupõe que você já tenha lido a [Referência do desenvolvedor do Azure Functions](functions-reference.md). Você também deve ter concluído o guia de [início rápido do Functions para o PowerShell](./create-first-function-vs-code-powershell.md) para criar sua primeira função do PowerShell.

## <a name="folder-structure"></a>Estrutura de pastas

A estrutura de pastas necessária para um projeto do PowerShell é semelhante ao seguinte. Este padrão pode ser alterado. Para mais informações, consulte a seção [scriptArquivo](#configure-function-scriptfile) abaixo.

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

Na raiz do projeto, há um [`host.json`](functions-host-json.md) arquivo compartilhado que pode ser usado para configurar o aplicativo de funções. Cada função tem uma pasta com seu próprio arquivo de código (. ps1) e arquivo de configuração de associação ( `function.json` ). O nome do function.jsno diretório pai do arquivo é sempre o nome da sua função.

Determinadas associações exigem a presença de um `extensions.csproj` arquivo. As extensões de associação, necessárias na [versão 2. x e versões posteriores](functions-versions.md) do tempo de execução do functions, são definidas no `extensions.csproj` arquivo, com os arquivos de biblioteca reais na `bin` pasta. Ao desenvolver localmente, você precisa [registrar as extensões de associação](functions-bindings-register.md#extension-bundles). Ao desenvolver funções no portal do Azure, esse registro é feito para você.

Em aplicativos de funções do PowerShell, você pode opcionalmente ter um `profile.ps1` que é executado quando um aplicativo de funções começa a ser executado (caso contrário, saiba como um *[início frio](#cold-start)*. Para obter mais informações, consulte [perfil do PowerShell](#powershell-profile).

## <a name="defining-a-powershell-script-as-a-function"></a>Definindo um script do PowerShell como uma função

Por padrão, o runtime de Funções procura sua função em `run.ps1`, onde `run.ps1` compartilha o mesmo diretório pai que o `function.json` correspondente.

Seu script é passado por vários argumentos na execução. Para lidar com esses parâmetros, adicione um `param` bloco à parte superior do seu script, como no exemplo a seguir:

```powershell
# $TriggerMetadata is optional here. If you don't need it, you can safely remove it from the param block
param($MyFirstInputBinding, $MySecondInputBinding, $TriggerMetadata)
```

### <a name="triggermetadata-parameter"></a>Parâmetro TriggerMetadata

O `TriggerMetadata` parâmetro é usado para fornecer informações adicionais sobre o gatilho. Os metadados adicionais variam de associação à associação, mas todos contêm uma `sys` propriedade que contém os seguintes dados:

```powershell
$TriggerMetadata.sys
```

| Propriedade   | Descrição                                     | Type     |
|------------|-------------------------------------------------|----------|
| UtcNow     | Quando, em UTC, a função foi disparada        | DateTime |
| MethodName | O nome da função que foi disparada     | string   |
| RandGuid   | um GUID exclusivo para esta execução da função | string   |

Cada tipo de gatilho tem um conjunto diferente de metadados. Por exemplo, o `$TriggerMetadata` para `QueueTrigger` contém o `InsertionTime` , `Id` , `DequeueCount` , entre outras coisas. Para obter mais informações sobre os metadados do gatilho de fila, acesse a [documentação oficial para gatilhos de fila](functions-bindings-storage-queue-trigger.md#message-metadata). Verifique a documentação nos [gatilhos](functions-triggers-bindings.md) com os quais você está trabalhando para ver o que acontece nos metadados do gatilho.

## <a name="bindings"></a>Associações

No PowerShell, as [associações](functions-triggers-bindings.md) são configuradas e definidas no function.jsde uma função em. As funções interagem com as ligações de várias maneiras.

### <a name="reading-trigger-and-input-data"></a>Gatilho de leitura e dados de entrada

As associações de entrada e gatilho são lidas como parâmetros passados para sua função. As associações de entrada têm um `direction` definido como `in` no function.jsem. A `name` propriedade definida em `function.json` é o nome do parâmetro, no `param` bloco. Como o PowerShell usa parâmetros nomeados para associação, a ordem dos parâmetros não importa. No entanto, é uma prática recomendada seguir a ordem das associações definidas no `function.json` .

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)
```

### <a name="writing-output-data"></a>Gravar dados de saída

No functions, uma associação de saída tem um `direction` definido como `out` no function.jsem. Você pode gravar em uma associação de saída usando o `Push-OutputBinding` cmdlet, que está disponível para o tempo de execução do functions. Em todos os casos, a `name` propriedade da associação, conforme definido em, `function.json` corresponde ao `Name` parâmetro do `Push-OutputBinding` cmdlet.

O seguinte mostra como chamar `Push-OutputBinding` em seu script de função:

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Push-OutputBinding -Name myQueue -Value $myValue
```

Você também pode passar um valor para uma ligação específica por meio do pipeline.

```powershell
param($MyFirstInputBinding, $MySecondInputBinding)

Produce-MyOutputValue | Push-OutputBinding -Name myQueue
```

`Push-OutputBinding` comporta-se de forma diferente com base no valor especificado para `-Name` :

* Quando o nome especificado não puder ser resolvido para uma associação de saída válida, um erro será gerado.

* Quando a associação de saída aceita uma coleção de valores, você pode chamar `Push-OutputBinding` repetidamente para enviar vários valores por push.

* Quando a associação de saída aceita apenas um valor singleton, chamar `Push-OutputBinding` uma segunda vez gera um erro.

#### <a name="push-outputbinding-syntax"></a>Sintaxe de `Push-OutputBinding`

Veja a seguir os parâmetros válidos para chamar `Push-OutputBinding` :

| Nome | Tipo | Posição | Descrição |
| ---- | ---- |  -------- | ----------- |
| **`-Name`** | String | 1 | O nome da Associação de saída que você deseja definir. |
| **`-Value`** | Objeto | 2 | O valor da Associação de saída que você deseja definir, que é aceita do pipeline ByValue. |
| **`-Clobber`** | SwitchParameter | nomeado | Adicional Quando especificado, força o valor a ser definido para uma associação de saída especificada. | 

Os seguintes parâmetros comuns também têm suporte: 
* `Verbose`
* `Debug`
* `ErrorAction`
* `ErrorVariable`
* `WarningAction`
* `WarningVariable`
* `OutBuffer`
* `PipelineVariable`
* `OutVariable` 

Para obter mais informações, consulte [sobre CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

#### <a name="push-outputbinding-example-http-responses"></a>Exemplo de Push-OutputBinding: respostas HTTP

Um gatilho HTTP retorna uma resposta usando uma associação de saída denominada `response` . No exemplo a seguir, a associação de saída de `response` tem o valor de "saída #1":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #1"
})
```

Como a saída é para HTTP, que aceita apenas um valor singleton, um erro é gerado quando `Push-OutputBinding` é chamado uma segunda vez.

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #2"
})
```

Para saídas que aceitam apenas valores singleton, você pode usar o `-Clobber` parâmetro para substituir o valor antigo em vez de tentar adicionar a uma coleção. O exemplo a seguir pressupõe que você já adicionou um valor. Usando `-Clobber` , a resposta do exemplo a seguir substitui o valor existente para retornar um valor de "saída #3":

```powershell
PS >Push-OutputBinding -Name response -Value ([HttpResponseContext]@{
    StatusCode = [System.Net.HttpStatusCode]::OK
    Body = "output #3"
}) -Clobber
```

#### <a name="push-outputbinding-example-queue-output-binding"></a>Exemplo de Push-OutputBinding: Associação de saída de fila

`Push-OutputBinding` é usado para enviar dados para associações de saída, como uma [Associação de saída do armazenamento de filas do Azure](functions-bindings-storage-queue-output.md). No exemplo a seguir, a mensagem gravada na fila tem um valor de "saída #1":

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #1"
```

A associação de saída para uma fila de armazenamento aceita vários valores de saída. Nesse caso, chamar o exemplo a seguir após a primeira gravação na fila em uma lista com dois itens: "output #1" e "output #2".

```powershell
PS >Push-OutputBinding -Name outQueue -Value "output #2"
```

O exemplo a seguir, quando chamado após os dois anteriores, adiciona mais dois valores à coleção de saída:

```powershell
PS >Push-OutputBinding -Name outQueue -Value @("output #3", "output #4")
```

Quando gravado na fila, a mensagem contém estes quatro valores: "saída #1", "saída #2", "saída #3" e "#4 de saída".

#### <a name="get-outputbinding-cmdlet"></a>cmdlet `Get-OutputBinding`

Você pode usar o `Get-OutputBinding` cmdlet para recuperar os valores definidos atualmente para suas associações de saída. Esse cmdlet recupera uma tabela de hash que contém os nomes das associações de saída com seus respectivos valores. 

Veja a seguir um exemplo de `Get-OutputBinding` como usar para retornar valores de associação atuais:

```powershell
Get-OutputBinding
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
MyOtherQueue                   myData
```

`Get-OutputBinding` também contém um parâmetro chamado `-Name` , que pode ser usado para filtrar a associação retornada, como no exemplo a seguir:

```powershell
Get-OutputBinding -Name MyQ*
```

```Output
Name                           Value
----                           -----
MyQueue                        myData
```

Há suporte para caracteres curinga (*) no `Get-OutputBinding` .

## <a name="logging"></a>Log

O registro em log nas funções do PowerShell funciona como log normal do PowerShell. Você pode usar os cmdlets de log para gravar em cada fluxo de saída. Cada cmdlet é mapeado para um nível de log usado pelas funções.

| Nível de log de funções | Cmdlet de registro em log |
| ------------- | -------------- |
| Erro | **`Write-Error`** |
| Aviso | **`Write-Warning`**  | 
| Informação | **`Write-Information`** <br/> **`Write-Host`** <br /> **`Write-Output`**      | Informação | Grava no log do nível de _informações_ . |
| Depurar | **`Write-Debug`** |
| Trace | **`Write-Progress`** <br /> **`Write-Verbose`** |

Além desses cmdlets, qualquer coisa gravada no pipeline é redirecionada para o `Information` nível de log e exibida com a formatação padrão do PowerShell.

> [!IMPORTANT]
> Usar os `Write-Verbose` `Write-Debug` cmdlets ou não é suficiente para ver o log detalhado e de nível de depuração. Você também deve configurar o limite de nível de log, que declara o nível de logs que você realmente importa. Para saber mais, confira [Configurar o nível de log do aplicativo de funções](#configure-the-function-app-log-level).

### <a name="configure-the-function-app-log-level"></a>Configurar o nível de log do aplicativo de funções

Azure Functions permite que você defina o nível de limite para facilitar o controle da maneira como as funções são gravadas nos logs. Para definir o limite para todos os rastreamentos gravados no console, use a `logging.logLevel.default` propriedade no [ `host.json` arquivo] [host.jsem referência]. Essa configuração se aplica a todas as funções em seu aplicativo de função.

O exemplo a seguir define o limite para habilitar o log detalhado para todas as funções, mas define o limite para habilitar o log de depuração para uma função chamada `MyFunction` :

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

Para obter mais informações, consulte a [referência para host.json].

### <a name="viewing-the-logs"></a>Exibindo os logs

Se o Aplicativo de funções estiver em execução no Azure, você poderá usar Application Insights para monitorá-lo. Leia [Monitorado o Azure Functions](functions-monitoring.md) para saber mais sobre como exibir e consultar logs de função.

Se você estiver executando o Aplicativo de funções localmente para desenvolvimento, os logs serão padrão para o sistema de arquivos. Para ver os logs no console, defina a `AZURE_FUNCTIONS_ENVIRONMENT` variável de ambiente como `Development` antes de iniciar o aplicativo de funções.

## <a name="triggers-and-bindings-types"></a>Tipos de associações e gatilhos

Há vários gatilhos e associações disponíveis para você usar com seu aplicativo de funções. A lista completa de gatilhos e associações [pode ser encontrada aqui](functions-triggers-bindings.md#supported-bindings).

Todos os gatilhos e associações são representados no código como alguns tipos de dados reais:

* Hashtable
* string
* byte[]
* INT
* double
* HttpRequestContext
* HttpResponseContext

Os cinco primeiros tipos nessa lista são tipos .NET padrão. Os dois últimos são usados somente pelo [gatilho HttpTrigger](#http-triggers-and-bindings).

Cada parâmetro de associação em suas funções deve ser um desses tipos.

### <a name="http-triggers-and-bindings"></a>Gatilhos e associações HTTP

HTTP e gatilhos de webhook e associações de saída HTTP usam objetos de solicitação e resposta para representar as mensagens HTTP.

#### <a name="request-object"></a>Objeto da solicitação

O objeto de solicitação que é passado para o script é do tipo `HttpRequestContext` , que tem as seguintes propriedades:

| Propriedade  | Descrição                                                    | Type                      |
|-----------|----------------------------------------------------------------|---------------------------|
| **`Body`**    | Um objeto que contém o corpo da solicitação. `Body` é serializado no melhor tipo com base nos dados. Por exemplo, se os dados forem JSON, eles serão passados como uma tabela de hash. Se os dados forem uma cadeia de caracteres, eles serão passados como uma cadeia de caracteres. | objeto |
| **`Headers`** | Um dicionário que contém os cabeçalhos de solicitação.                | <de cadeia de caracteres de dicionário, Cadeia de caracteres><sup>*</sup> |
| **`Method`** | O método HTTP da solicitação.                                | string                    |
| **`Params`**  | Um objeto que contém os parâmetros de roteamento da solicitação. | <de cadeia de caracteres de dicionário, Cadeia de caracteres><sup>*</sup> |
| **`Query`** | Um objeto que contém os parâmetros da consulta.                  | <de cadeia de caracteres de dicionário, Cadeia de caracteres><sup>*</sup> |
| **`Url`** | A URL da solicitação.                                        | string                    |

<sup>*</sup> Todas as chaves não diferenciam `Dictionary<string,string>` maiúsculas de minúsculas.

#### <a name="response-object"></a>Objeto de resposta

O objeto de resposta que você deve enviar de volta é do tipo `HttpResponseContext` , que tem as seguintes propriedades:

| Propriedade      | Descrição                                                 | Type                      |
|---------------|-------------------------------------------------------------|---------------------------|
| **`Body`**  | Um objeto que contém o corpo da resposta.           | objeto                    |
| **`ContentType`** | Uma pequena mão para definir o tipo de conteúdo para a resposta. | string                    |
| **`Headers`** | Um objeto que contém os cabeçalhos da resposta.               | Dicionário ou Hashtable   |
| **`StatusCode`**  | O código de status HTTP da resposta.                       | cadeia de caracteres ou inteiro             |

#### <a name="accessing-the-request-and-response"></a>Acessar a solicitação e a resposta

Ao trabalhar com gatilhos HTTP, você pode acessar a solicitação HTTP da mesma maneira que faria com qualquer outra associação de entrada. Está no `param` bloco.

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

O resultado da invocação dessa função seria:

```
PS > irm http://localhost:5001?Name=Functions
Hello Functions!
```

### <a name="type-casting-for-triggers-and-bindings"></a>Conversão de tipo para gatilhos e associações

Para determinadas associações como a associação de BLOB, você pode especificar o tipo do parâmetro.

Por exemplo, para ter dados do armazenamento de BLOBs fornecidos como uma cadeia de caracteres, adicione a seguinte conversão de tipo ao meu `param` bloco:

```powershell
param([string] $myBlob)
```

## <a name="powershell-profile"></a>Perfil do PowerShell

No PowerShell, há o conceito de um perfil do PowerShell. Se você não estiver familiarizado com os perfis do PowerShell, consulte [about Profiles](/powershell/module/microsoft.powershell.core/about/about_profiles).

No PowerShell functions, o script de perfil é executado uma vez por instância de trabalho do PowerShell no aplicativo quando implantado pela primeira vez e depois de ficar ocioso ([inicialização a frio](#cold-start). Quando a simultaneidade é habilitada definindo o valor [PSWorkerInProcConcurrencyUpperBound](#concurrency) , o script de perfil é executado para cada runspace criado.

Quando você cria um aplicativo de funções usando ferramentas, como Visual Studio Code e Azure Functions Core Tools, um padrão `profile.ps1` é criado para você. O perfil padrão é mantido [no repositório GitHub das ferramentas principais](https://github.com/Azure/azure-functions-core-tools/blob/dev/src/Azure.Functions.Cli/StaticResources/profile.ps1) e contém:

* Autenticação automática de MSI para o Azure.
* A capacidade de ativar o Azure PowerShell `AzureRM` aliases do PowerShell, se desejar.

## <a name="powershell-versions"></a>Versões do PowerShell

A tabela a seguir mostra as versões do PowerShell disponíveis para cada versão principal do tempo de execução do Functions e a versão do .NET necessária:

| Versão do Functions | Versão do PowerShell                               | Versão do .NET  | 
|-------------------|--------------------------------------------------|---------------|
| 3. x (recomendado) | PowerShell 7 (recomendado)<br/>PowerShell Core 6 | .NET Core 3.1<br/>.NET Core 2.1 |
| 2. x               | PowerShell Core 6                                | .NET Core 2.2 |

Você pode ver a versão atual imprimindo `$PSVersionTable` de qualquer função.

### <a name="running-local-on-a-specific-version"></a>Em execução local em uma versão específica

Ao executar localmente, o tempo de execução do Azure Functions usa o PowerShell Core 6 como padrão. Para usar o PowerShell 7 ao executar localmente, você precisa adicionar a configuração `"FUNCTIONS_WORKER_RUNTIME_VERSION" : "~7"` à `Values` matriz no local.setting.jsno arquivo na raiz do projeto. Ao executar localmente no PowerShell 7, seu local.settings.jsno arquivo é semelhante ao exemplo a seguir: 

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "powershell",
    "FUNCTIONS_WORKER_RUNTIME_VERSION" : "~7"
  }
}
```

### <a name="changing-the-powershell-version"></a>Alterando a versão do PowerShell

Seu aplicativo de funções deve estar em execução na versão 3. x para poder atualizar do PowerShell Core 6 para o PowerShell 7. Para saber como fazer isso, consulte [Exibir e atualizar a versão de tempo de execução atual](set-runtime-version.md#view-and-update-the-current-runtime-version).

Use as etapas a seguir para alterar a versão do PowerShell usada pelo seu aplicativo de funções. Isso pode ser feito na portal do Azure ou usando o PowerShell.

# <a name="portal"></a>[Portal](#tab/portal)

1. No [portal do Azure](https://portal.azure.com), navegue até o aplicativo de funções.

1. Em **configurações**, escolha **configuração**. Na guia **configurações gerais** , localize a **versão do PowerShell**. 

    :::image type="content" source="media/functions-reference-powershell/change-powershell-version-portal.png" alt-text="Escolha a versão do PowerShell usada pelo aplicativo de funções"::: 

1. Escolha a **versão do PowerShell Core** desejada e selecione **salvar**. Quando avisado sobre a reinicialização pendente, escolha **continuar**. O aplicativo de funções é reiniciado na versão escolhida do PowerShell. 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Execute o script a seguir para alterar a versão do PowerShell: 

```powershell
Set-AzResource -ResourceId "/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<FUNCTION_APP>/config/web" -Properties @{  powerShellVersion  = '<VERSION>' } -Force -UsePatchSemantics

```

Substitua `<SUBSCRIPTION_ID>` , `<RESOURCE_GROUP>` e `<FUNCTION_APP>` pela ID da sua assinatura do Azure, o nome do seu grupo de recursos e do aplicativo de funções, respectivamente.  Além disso, substitua `<VERSION>` por um `~6` ou `~7` . Você pode verificar o valor atualizado da `powerShellVersion` configuração na `Properties` tabela de hash retornada. 

---

O aplicativo de funções é reiniciado depois que a alteração é feita na configuração.

## <a name="dependency-management"></a>Gerenciamento de dependência

O Functions permite aproveitar a [Galeria do PowerShell](https://www.powershellgallery.com) para gerenciar dependências. Com o gerenciamento de dependência habilitado, o arquivo requirements.psd1 é usado para baixar automaticamente os módulos necessários. Você habilita esse comportamento definindo a `managedDependency` propriedade como `true` na raiz do [host.jsno arquivo](functions-host-json.md), como no exemplo a seguir:

```json
{
  "managedDependency": {
          "enabled": true
       }
}
```

Quando você cria um novo projeto de funções do PowerShell, o gerenciamento de dependência é habilitado por padrão, com o [ `Az` módulo](/powershell/azure/new-azureps-module-az) do Azure incluído. O número máximo de módulos com suporte no momento é 10. A sintaxe com suporte é uma _`MajorNumber`_ `.*` versão de módulo exata, conforme mostrado no exemplo a seguir requirements.psd1:

```powershell
@{
    Az = '1.*'
    SqlServer = '21.1.18147'
}
```

Quando você atualiza o arquivo requirements.psd1, os módulos atualizados são instalados após uma reinicialização.

> [!NOTE]
> As dependências gerenciadas exigem acesso ao www.powershellgallery.com para baixar módulos. Ao executar localmente, verifique se o tempo de execução pode acessar essa URL adicionando as regras de firewall necessárias.

> [!NOTE]
> As dependências gerenciadas atualmente não dão suporte a módulos que exigem que o usuário aceite uma licença, seja aceitando a licença interativamente ou fornecendo a `-AcceptLicense` opção ao invocar `Install-Module` .

As configurações de aplicativo a seguir podem ser usadas para alterar a forma como as dependências gerenciadas são baixadas e instaladas. A atualização do aplicativo é iniciada no `MDMaxBackgroundUpgradePeriod` e o processo de atualização é concluído em aproximadamente `MDNewSnapshotCheckPeriod` .

| Aplicativo de funções configuração              | Valor padrão             | Descrição                                         |
|   -----------------------------   |   -------------------     |  -----------------------------------------------    |
| **`MDMaxBackgroundUpgradePeriod`**      | `7.00:00:00` (7 dias)     | Cada processo de trabalho do PowerShell inicia a verificação de atualizações de módulo no Galeria do PowerShell no início do processo e `MDMaxBackgroundUpgradePeriod` a cada depois disso. Quando uma nova versão de módulo está disponível no Galeria do PowerShell, ela é instalada no sistema de arquivos e disponibilizada para trabalhadores do PowerShell. Diminuir esse valor permite que seu aplicativo de funções obtenha versões mais recentes do módulo mais cedo, mas também aumenta o uso de recursos do aplicativo (e/s de rede, CPU, armazenamento). Aumentar esse valor diminui o uso de recursos do aplicativo, mas também pode atrasar a entrega de novas versões de módulo ao seu aplicativo. | 
| **`MDNewSnapshotCheckPeriod`**         | `01:00:00` (1 hora)       | Depois que novas versões de módulo são instaladas no sistema de arquivos, todos os processos de trabalho do PowerShell devem ser reiniciados. Reiniciar os trabalhadores do PowerShell afeta a disponibilidade do aplicativo, pois ele pode interromper a execução da função atual. Até que todos os processos de trabalho do PowerShell sejam reiniciados, as invocações de função podem usar as versões de módulo antiga ou nova. A reinicialização de todos os trabalhos do PowerShell é concluída no `MDNewSnapshotCheckPeriod` . Aumentar esse valor diminui a frequência de interrupções, mas também pode aumentar o período de tempo em que as invocações de função usam as versões de módulo antigo ou novo de forma não determinística. |
| **`MDMinBackgroundUpgradePeriod`**      | `1.00:00:00` (1 dia)     | Para evitar atualizações excessivas de módulo em reinicializações de trabalho frequentes, a verificação de atualizações de módulo não será executada quando algum trabalho já tiver iniciado essa verificação no último `MDMinBackgroundUpgradePeriod` . |

Aproveitar seus próprios módulos personalizados é um pouco diferente de como você faria normalmente.

No computador local, o módulo é instalado em uma das pastas disponíveis globalmente no seu `$env:PSModulePath` . Ao executar no Azure, você não tem acesso aos módulos instalados em seu computador. Isso significa que o `$env:PSModulePath` para um aplicativo de funções do PowerShell é diferente de `$env:PSModulePath` em um script do PowerShell regular.

No functions, o `PSModulePath` contém dois caminhos:

* Uma `Modules` pasta que existe na raiz do seu aplicativo de funções.
* Um caminho para uma `Modules` pasta que é controlada pelo operador de idioma do PowerShell.


### <a name="function-app-level-modules-folder"></a>Pasta de nível de aplicativo de função `Modules`

Para usar módulos personalizados, você pode posicionar os módulos nos quais suas funções dependem de uma `Modules` pasta. Nessa pasta, os módulos ficam automaticamente disponíveis para o tempo de execução do functions. Qualquer função no aplicativo de funções pode usar esses módulos. 

> [!NOTE]
> Os módulos especificados no arquivo requirements.psd1 são baixados e incluídos automaticamente no caminho para que você não precise incluí-los na pasta modules. Eles são armazenados localmente na `$env:LOCALAPPDATA/AzureFunctions` pasta e na `/data/ManagedDependencies` pasta quando executados na nuvem.

Para aproveitar o recurso de módulo personalizado, crie uma `Modules` pasta na raiz do seu aplicativo de funções. Copie os módulos que você deseja usar em suas funções para esse local.

```powershell
mkdir ./Modules
Copy-Item -Path /mymodules/mycustommodule -Destination ./Modules -Recurse
```

Com uma `Modules` pasta, seu aplicativo de funções deve ter a seguinte estrutura de pastas:

```
PSFunctionApp
 | - MyFunction
 | | - run.ps1
 | | - function.json
 | - Modules
 | | - MyCustomModule
 | | - MyOtherCustomModule
 | | - MySpecialModule.psm1
 | - local.settings.json
 | - host.json
 | - requirements.psd1
```

Quando você inicia seu aplicativo de funções, o operador de linguagem do PowerShell adiciona essa `Modules` pasta ao para `$env:PSModulePath` que você possa contar com o carregamento automático de módulo da mesma forma que faria em um script do PowerShell normal.

### <a name="language-worker-level-modules-folder"></a>Pasta de nível de trabalho do idioma `Modules`

Vários módulos são geralmente usados pelo operador de linguagem do PowerShell. Esses módulos são definidos na última posição de `PSModulePath` . 

A lista atual de módulos é a seguinte:

* [Microsoft. PowerShell. Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive): módulo usado para trabalhar com arquivos mortos, como `.zip` , `.nupkg` e outros.
* **ThreadJob**: uma implementação baseada em thread das APIs de trabalho do PowerShell.

Por padrão, o Functions usa a versão mais recente desses módulos. Para usar uma versão de módulo específica, coloque essa versão específica na `Modules` pasta do seu aplicativo de funções.

## <a name="environment-variables"></a>Variáveis de ambiente

Em funções, [configurações do aplicativo](functions-app-settings.md), como conexão de serviço cadeias de caracteres, são expostas como variáveis de ambiente durante a execução. Você pode acessar essas configurações usando `$env:NAME_OF_ENV_VAR` , conforme mostrado no exemplo a seguir:

```powershell
param($myTimer)

Write-Host "PowerShell timer trigger function ran! $(Get-Date)"
Write-Host $env:AzureWebJobsStorage
Write-Host $env:WEBSITE_SITE_NAME
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Ao executar localmente, as configurações do aplicativo são lidos a partir de [Settings](functions-run-local.md#local-settings-file) arquivo de projeto.

## <a name="concurrency"></a>Simultaneidade

Por padrão, o tempo de execução do PowerShell do Functions só pode processar uma invocação de uma função por vez. No entanto, esse nível de simultaneidade pode não ser suficiente nas seguintes situações:

* Quando você está tentando lidar com um grande número de invocações ao mesmo tempo.
* Quando você tem funções que invocam outras funções dentro do mesmo aplicativo de funções.

Há alguns modelos de simultaneidade que você pode explorar dependendo do tipo de carga de trabalho:

* Aumentar ```FUNCTIONS_WORKER_PROCESS_COUNT``` . Isso permite manipular invocações de função em vários processos dentro da mesma instância, o que introduz determinada sobrecarga de CPU e memória. Em geral, as funções vinculadas a e/s não serão afetadas dessa sobrecarga. Para funções associadas à CPU, o impacto pode ser significativo.

* Aumente o ```PSWorkerInProcConcurrencyUpperBound``` valor de configuração do aplicativo. Isso permite a criação de vários Runspaces dentro do mesmo processo, o que reduz significativamente a sobrecarga de CPU e de memória.

Defina essas variáveis de ambiente nas [configurações de aplicativo](functions-app-settings.md) do seu aplicativo de funções.

Dependendo do seu caso de uso, Durable Functions pode melhorar significativamente a escalabilidade. Para saber mais, consulte [padrões de aplicativo Durable Functions](./durable/durable-functions-overview.md?tabs=powershell#application-patterns).

>[!NOTE]
> Você pode receber avisos de "solicitações sendo enfileiradas devido a nenhum Runspaces disponível", observe que isso não é um erro. A mensagem está informando que as solicitações estão sendo enfileiradas e elas serão tratadas quando as solicitações anteriores forem concluídas.

### <a name="considerations-for-using-concurrency"></a>Considerações sobre o uso de simultaneidade

O PowerShell é uma linguagem de script de _thread único_ por padrão. No entanto, a simultaneidade pode ser adicionada usando vários espaços de uso do PowerShell no mesmo processo. A quantidade de Runspaces criados corresponderá à ```PSWorkerInProcConcurrencyUpperBound``` configuração do aplicativo. A taxa de transferência será afetada pela quantidade de CPU e memória disponíveis no plano selecionado.

Azure PowerShell usa alguns contextos de _nível de processo_ e um estado para ajudá-lo a evitar a digitação de excesso de tipos. No entanto, se você ativar a simultaneidade em seu aplicativo de funções e invocar ações que alteram o estado, poderá acabar com condições de corrida. Essas condições de corrida são difíceis de depurar porque uma invocação depende de um determinado Estado e a outra invocação alterou o estado.

Há um grande valor em simultaneidade com Azure PowerShell, já que algumas operações podem levar um tempo considerável. No entanto, você deve continuar com cautela. Se você suspeitar de que está enfrentando uma condição de corrida, defina a configuração de aplicativo PSWorkerInProcConcurrencyUpperBound como `1` e, em vez disso, use o [isolamento de nível de processo de trabalho de idioma](functions-app-settings.md#functions_worker_process_count) para simultaneidade.

## <a name="configure-function-scriptfile"></a>Configurar função `scriptFile`

Por padrão, uma função do PowerShell é executada no `run.ps1` , um arquivo que compartilha o mesmo diretório pai que o correspondente `function.json` .

A `scriptFile` propriedade no `function.json` pode ser usada para obter uma estrutura de pastas parecida com o exemplo a seguir:

```
FunctionApp
 | - host.json
 | - myFunction
 | | - function.json
 | - lib
 | | - PSFunction.ps1
```

Nesse caso, o `function.json` para `myFunction` inclui uma `scriptFile` propriedade que referencia o arquivo com a função exportada a ser executada.

```json
{
  "scriptFile": "../lib/PSFunction.ps1",
  "bindings": [
    // ...
  ]
}
```

## <a name="use-powershell-modules-by-configuring-an-entrypoint"></a>Usar módulos do PowerShell Configurando um ponto de entrada

Este artigo mostrou as funções do PowerShell no `run.ps1` arquivo de script padrão gerado pelos modelos.
No entanto, você também pode incluir suas funções em módulos do PowerShell. Você pode referenciar seu código de função específico no módulo usando os `scriptFile` `entryPoint` campos e na function.jsno arquivo de configuração.

Nesse caso, `entryPoint` é o nome de uma função ou cmdlet no módulo do PowerShell referenciado em `scriptFile` .

Considere a seguinte estrutura de pastas:

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

Neste exemplo, a configuração de `myFunction` inclui uma `scriptFile` propriedade que faz referência a `PSFunction.psm1` , que é um módulo do PowerShell em outra pasta.  A `entryPoint` propriedade faz referência à `Invoke-PSTestFunc` função, que é o ponto de entrada no módulo.

```json
{
  "scriptFile": "../lib/PSFunction.psm1",
  "entryPoint": "Invoke-PSTestFunc",
  "bindings": [
    // ...
  ]
}
```

Com essa configuração, o `Invoke-PSTestFunc` é executado exatamente como `run.ps1` faria.

## <a name="considerations-for-powershell-functions"></a>Considerações para funções do PowerShell

Ao trabalhar com as funções do PowerShell, esteja ciente das considerações nas seções a seguir.

### <a name="cold-start"></a>Inicialização a frio

Ao desenvolver Azure Functions no [modelo de hospedagem sem servidor](consumption-plan.md), inícios frios são uma realidade. *Início frio* refere-se ao período de tempo que leva para seu aplicativo de funções iniciar a execução para processar uma solicitação. A inicialização a frio acontece com mais frequência no plano de consumo porque seu aplicativo de funções é desligado durante períodos de inatividade.

### <a name="bundle-modules-instead-of-using-install-module"></a>Agrupar módulos em vez de usar `Install-Module`

O script é executado em cada invocação. Evite usar `Install-Module` em seu script. Em vez disso, use `Save-Module` antes de publicar para que sua função não precise perder tempo baixando o módulo. Se a frio for iniciada, afetando suas funções, considere implantar seu aplicativo de funções em um [plano do serviço de aplicativo](dedicated-plan.md) definido como *Always on* ou em um [plano Premium](functions-premium-plan.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os recursos a seguir:

* [Práticas recomendadas para o Azure Functions](functions-best-practices.md)
* [Referência do desenvolvedor do Azure Functions](functions-reference.md)
* [Gatilhos e associações de Azure Functions](functions-triggers-bindings.md)

[Referência de host.JSON]: functions-host-json.md