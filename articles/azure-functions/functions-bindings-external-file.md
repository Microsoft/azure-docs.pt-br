---
title: "Associações de arquivo externo do Azure Functions (versão prévia) | Microsoft Docs"
description: "Usando associações de arquivo externo no Azure Functions"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: alkarche
ms.openlocfilehash: 7e3b396d290212d3875385521bd7ae92da196b95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-external-file-bindings-preview"></a>Associações de arquivo externo do Azure Functions (versão prévia)
Este artigo mostra como manipular arquivos de provedores SaaS diferentes (por exemplo, OneDrive, Dropbox) em sua função utilizando associações internas. O Azure Functions dá suporte a associações de gatilho, entrada e saída de arquivos externos.

Essa associação cria conexões de API com provedores SaaS, ou usa conexões de API existentes do grupo de recursos do aplicativo da função.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="supported-file-connections"></a>Conexões de arquivo com suporte

|Conector|Gatilho|Entrada|Saída|
|:-----|:---:|:---:|:---:|
|[Box](https://www.box.com)|x|x|x
|[Dropbox](https://www.dropbox.com)|x|x|x
|[FTP](https://docs.microsoft.com/azure/app-service/app-service-deploy-ftp)|x|x|x
|[OneDrive](https://onedrive.live.com)|x|x|x
|[OneDrive for Business](https://onedrive.live.com/about/business/)|x|x|x
|[SFTP](https://docs.microsoft.com/azure/connectors/connectors-create-api-sftp)|x|x|x
|[Google Drive](https://www.google.com/drive/)||x|x|

> [!NOTE]
> Conexões de arquivo externo também podem ser usadas no [Aplicativo Lógico do Azure](https://docs.microsoft.com/azure/connectors/apis-list)

## <a name="external-file-trigger-binding"></a>Associação de gatilho de arquivo externo

O gatilho de arquivo externo do Azure permite monitorar uma pasta remota e executar o código da função quando são detectadas alterações.

O gatilho de arquivo externo usa os seguintes objetos JSON na matriz `bindings` de function.json

```json
{
  "type": "apiHubFileTrigger",
  "name": "<Name of input parameter in function signature>",
  "direction": "in",
  "path": "<folder to monitor, and optionally a name pattern - see below>",
  "connection": "<name of external file connection - see above>"
}
```
<!---
See one of the following subheadings for more information:

* [Name patterns](#pattern)
* [File receipts](#receipts)
* [Handling poison files](#poison)
--->

<a name="pattern"></a>

### <a name="name-patterns"></a>Padrões de nome
É possível especificar um padrão de nome de arquivo na propriedade `path`. A pasta referenciada deve existir no provedor de SaaS.
Exemplos:

```json
"path": "input/original-{name}",
```

Esse caminho deve encontrar um arquivo chamado *original-File1.txt* na *entrada* pasta e o valor da variável `name` no código de função seria `File1.txt`.

Outro exemplo:

```json
"path": "input/{filename}.{fileextension}",
```

Esse caminho também poderia ser um blob chamado *original-File1.txt* e o valor das variáveis `filename` e `fileextension` no código de função seria *original-File1* e *txt*.

Você pode restringir o tipo de arquivo dos arquivos usando um valor fixo para a extensão de arquivo. Por exemplo:

```json
"path": "samples/{name}.png",
```

Nesse caso, apenas arquivos *.png* na pasta *amostras* disparam a função.

As chaves são caracteres especiais nos padrões de nome. Para especificar nomes de arquivo com chaves, duplique as chaves.
Por exemplo:

```json
"path": "images/{{20140101}}-{name}",
```

Esse caminho encontraria um arquivo denominado *{20140101}-soundfile.mp3* na pasta *imagens* e o valor da variável `name` no código da função seria *soundfile.mp3*.

<a name="receipts"></a>

<!--- ### File receipts
The Azure Functions runtime makes sure that no external file trigger function gets called more than once for the same new or updated file.
It does so by maintaining *file receipts* to determine if a given file version has been processed.

File receipts are stored in a folder named *azure-webjobs-hosts* in the Azure storage account for your function app
(specified by the `AzureWebJobsStorage` app setting). A file receipt has the following information:

* The triggered function ("*&lt;function app name>*.Functions.*&lt;function name>*", for example: "functionsf74b96f7.Functions.CopyFile")
* The folder name
* The file type ("BlockFile" or "PageFile")
* The file name
* The ETag (a file version identifier, for example: "0x8D1DC6E70A277EF")

To force reprocessing of a file, delete the file receipt for that file from the *azure-webjobs-hosts* folder manually.
--->
<a name="poison"></a>

### <a name="handling-poison-files"></a>Tratando arquivos suspeitos
Quando a função de gatilho de um arquivo externo falha, o Azure Functions repete essa função até 5 vezes por padrão (incluindo a primeira tentativa) para determinado arquivo.
Se todas as 5 tentativas falharem, o Functions adicionará uma mensagem para uma fila de armazenamento denominada *webjobs-apihubtrigger-poison*. A mensagem da fila para arquivos suspeitos é um objeto JSON que contém as seguintes propriedades:

* FunctionId (no formato *&lt;nome do aplicativo de funções>*.Functions.*&lt;nome da função>*)
* FileType
* FolderName
* FileName
* ETag (um identificador de versão de arquivo, por exemplo: "0x8D1DC6E70A277EF")


<a name="triggerusage"></a>

## <a name="trigger-usage"></a>Uso de gatilho
Em funções do C#, você associa aos dados do arquivo de entrada usando um parâmetro nomeado na assinatura da função, como `<T> <name>`.
Em que `T` é o tipo de dados no qual você deseja desserializar os dados e `paramName` é o nome especificado no [gatilho JSON](#trigger). Em funções do Node.js, você acessa os dados do arquivo de entrada usando `context.bindings.<name>`.

O arquivo pode ser desserializado em qualquer um destes tipos:

* Qualquer [Objeto](https://msdn.microsoft.com/library/system.object.aspx) – útil para dados de arquivo serializado para JSON.
  Se você declarar um tipo de entrada personalizado (por exemplo, `FooType`), o Azure Functions tenta desserializar os dados JSON para o tipo especificado.
* Cadeia de caracteres – útil para dados de arquivo de texto.

Em funções do C#, você também pode associar a qualquer um dos seguintes tipos e o tempo de execução do Functions tentará desserializar os dados da tabela usando esse tipo:

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`

## <a name="trigger-sample"></a>Exemplo de gatilho
Suponha que você tem o seguinte function.json, que define um gatilho de arquivo externo:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myFile",
            "type": "apiHubFileTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection": "<name of external file connection>"
        }
    ]
}
```

Veja a amostra específica a um idioma que registra o conteúdo de cada arquivo adicionado à pasta monitorada.

* [C#](#triggercsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-usage-in-c"></a>Uso de gatilhos em C# #

```cs
public static void Run(string myFile, TraceWriter log)
{
    log.Info($"C# File trigger function processed: {myFile}");
}
```

<!--
<a name="triggerfsharp"></a>
### Trigger usage in F# ##
```fsharp

```
-->

<a name="triggernodejs"></a>

### <a name="trigger-usage-in-nodejs"></a>Uso de gatilho em Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js File trigger function processed', context.bindings.myFile);
    context.done();
};
```

<a name="input"></a>

## <a name="external-file-input-binding"></a>Associação de entrada de arquivo externo
A associação de entrada de arquivo externo do Azure permite que você use um arquivo de uma pasta externa em sua função.

A entrada do arquivo externo em uma função usa os seguintes objetos JSON na matriz `bindings` de function.json:

```json
{
  "name": "<Name of input parameter in function signature>",
  "type": "apiHubFile",
  "direction": "in",
  "path": "<Path of input file - see below>",
  "connection": "<name of external file connection>"
},
```

Observe o seguinte:

* `path` deve conter o nome da pasta e o nome do arquivo. Por exemplo, se você tiver um [gatilho de fila](functions-bindings-storage-queue.md) em sua função, poderá usar `"path": "samples-workitems/{queueTrigger}"` para apontar para um arquivo na pasta `samples-workitems` com um nome que corresponda ao nome do arquivo especificado na mensagem de gatilho.   

<a name="inputusage"></a>

## <a name="input-usage"></a>Uso de entrada
Em funções do C#, você associa aos dados do arquivo de entrada usando um parâmetro nomeado na assinatura da função, como `<T> <name>`.
Em que `T` é o tipo de dados no qual você deseja desserializar os dados e `paramName` é o nome especificado na [associação de entrada](#input). Em funções do Node.js, você acessa os dados do arquivo de entrada usando `context.bindings.<name>`.

O arquivo pode ser desserializado em qualquer um destes tipos:

* Qualquer [Objeto](https://msdn.microsoft.com/library/system.object.aspx) – útil para dados de arquivo serializado para JSON.
  Se você declarar um tipo de entrada personalizado (por exemplo, `InputType`), o Azure Functions tenta desserializar os dados JSON para o tipo especificado.
* Cadeia de caracteres – útil para dados de arquivo de texto.

Em funções do C#, você também pode associar a qualquer um dos seguintes tipos e o tempo de execução do Functions tentará desserializar os dados da tabela usando esse tipo:

* `string`
* `byte[]`
* `Stream`
* `StreamReader`
* `TextReader`


<a name="output"></a>

## <a name="external-file-output-binding"></a>Associação de saída de arquivo externo
A associação de saída de arquivo externo do Azure permite que você grave arquivos em uma pasta externa de sua função.

A saída do arquivo externo para uma função usa os seguintes objetos JSON na matriz `bindings` de function.json:

```json
{
  "name": "<Name of output parameter in function signature>",
  "type": "apiHubFile",
  "direction": "out",
  "path": "<Path of input file - see below>",
  "connection": "<name of external file connection>"
}
```

Observe o seguinte:

* `path` deve conter o nome da pasta e o nome do arquivo no qual gravar. Por exemplo, se você tiver um [gatilho de fila](functions-bindings-storage-queue.md) em sua função, poderá usar `"path": "samples-workitems/{queueTrigger}"` para apontar para um arquivo na pasta `samples-workitems` com um nome que corresponda ao nome do arquivo especificado na mensagem de gatilho.   

<a name="outputusage"></a>

## <a name="output-usage"></a>Uso de saída
Em funções C#, você associa ao arquivo de saída usando o parâmetro `out` nomeado na assinatura da função, como `out <T> <name>`, em que `T` é o tipo de dados no qual você quer serializar os dados e `paramName` é o nome especificado na [associação de saída](#output). Nas funções do Node.js, você acessa o arquivo de saída usando `context.bindings.<name>`.

Você pode gravar no arquivo de saída usando qualquer um dos seguintes tipos:

* Qualquer [Objeto](https://msdn.microsoft.com/library/system.object.aspx) - útil para serialização JSON.
  Se você declarar um tipo de saída personalizada (por exemplo, `out OutputType paramName`), o Azure Functions tenta serializar o objeto em JSON. Se o parâmetro de saída for nulo quando a função for encerrada, o tempo de execução do Functions criará um arquivo como um objeto nulo.
* Cadeia de caracteres – (`out string paramName`) útil para dados de arquivo de texto. O tempo de execução do Functions só criará um arquivo se o parâmetro de cadeia de caracteres não for nulo quando a função for encerrada.

Em funções do C#, também é possível executar a saída para qualquer um dos seguintes tipos:

* `TextWriter`
* `Stream`
* `CloudFileStream`
* `ICloudFile`
* `CloudBlockFile`
* `CloudPageFile`

<a name="outputsample"></a>

<a name="sample"></a>

## <a name="input--output-sample"></a>Amostra de entrada + saída
Suponha que você tem o function.json a seguir, que define um [gatilho de fila de armazenamento](functions-bindings-storage-queue.md), uma entrada de arquivo externo e uma saída de arquivo externo:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "<name of external file connection>",
      "direction": "in"
    },
    {
      "name": "myOutputFile",
      "type": "apiHubFile",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "<name of external file connection>",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Consulte a amostra específica a um idioma que copia o arquivo de entrada no arquivo de saída.

* [C#](#incsharp)
* [Node.js](#innodejs)

<a name="incsharp"></a>

### <a name="usage-in-c"></a>Uso em C# #

```cs
public static void Run(string myQueueItem, string myInputFile, out string myOutputFile, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputFile = myInputFile;
}
```

<!--
<a name="infsharp"></a>
### Input usage in F# ##
```fsharp

```
-->

<a name="innodejs"></a>

### <a name="usage-in-nodejs"></a>Uso em Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
