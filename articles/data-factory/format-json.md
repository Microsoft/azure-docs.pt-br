---
title: Formato JSON no Azure Data Factory
description: Este tópico descreve como lidar com o formato JSON no Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: jingwang
ms.openlocfilehash: ebac108388071ed3dc0eb2b7bfc0494f2f7bb481
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181356"
---
# <a name="json-format-in-azure-data-factory"></a>Formato JSON no Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Siga este artigo quando desejar **analisar os arquivos JSON ou gravar os dados no formato JSON**. 

O formato JSON tem suporte para os seguintes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [blob do Azure](connector-azure-blob-storage.md), [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [armazenamento de arquivos do Azure](connector-azure-file-storage.md), [sistema de arquivos](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)e [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo DataSet JSON.

| Propriedade         | Descrição                                                  | Obrigatório |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | A propriedade Type do conjunto de conjuntos deve ser definida como **JSON**. | Sim      |
| local         | Configurações de local dos arquivos. Cada conector baseado em arquivo tem seu próprio tipo de local e propriedades com suporte em `location` . **Consulte os detalhes no artigo do conector – > seção Propriedades do conjunto de informações**. | Sim      |
| encodingName     | O tipo de codificação usado para ler/gravar arquivos de teste. <br>Os valores permitidos são os seguintes: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".| Não       |
| compactação | Grupo de propriedades para configurar a compactação de arquivo. Configure esta seção quando desejar fazer compactação/descompactação durante a execução da atividade. | Não |
| type<br/>(*em `compression`*) | O codec de compactação usado para ler/gravar arquivos JSON. <br>Os valores permitidos são **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **TarGzip**, **tar**, **snapshot** ou **lz4**. O padrão não é compactado.<br>**Observação** a atividade de cópia atualmente não dá suporte a "encaixar" & "lz4", e o fluxo de dados de mapeamento não dá suporte a "ZipDeflate" "," TarGzip "e" tar ".<br>**Observação** ao usar a atividade de cópia para descompactar /  / arquivo (s) ZipDeflate TarGzip **tar** e gravar no armazenamento de dados de coletor baseado em arquivo, por padrão os arquivos são extraídos para a pasta: `<path specified in dataset>/<folder named as source compressed file>/` , use `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` na [origem da atividade de cópia](#json-as-source) para controlar se deseja preservar o nome dos arquivos compactados como estrutura de pastas.| Não.  |
| nível<br/>(*em `compression`*) | A taxa de compactação. <br>Os valores permitidos são **ideal** ou **mais rápido**.<br>- **Mais rápido:** A operação de compactação deve ser concluída o mais rápido possível, mesmo que o arquivo resultante não seja compactado de maneira ideal.<br>- **Ideal**: a operação de compactação deve ser corretamente compactada, mesmo se a operação levar mais tempo para ser concluída. Para saber mais, veja o tópico [Nível de compactação](/dotnet/api/system.io.compression.compressionlevel) . | Não       |

Veja abaixo um exemplo de conjunto de um DataSet no armazenamento de BLOBs do Azure:

```json
{
    "name": "JSONDataset",
    "properties": {
        "type": "Json",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "gzip"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela origem e pelo coletor JSON.

Saiba mais sobre como extrair dados de arquivos JSON e mapear para o armazenamento/formato do coletor de dados ou vice-versa do [mapeamento de esquema](copy-activity-schema-and-type-mapping.md).

### <a name="json-as-source"></a>JSON como fonte

As propriedades a seguir têm suporte na seção ***\* origem \**** da atividade de cópia.

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A propriedade Type da fonte da atividade de cópia deve ser definida como **jsonname**. | Sim      |
| formatSettings | Um grupo de propriedades. Consulte a tabela de **configurações de leitura JSON** abaixo. | Não       |
| storeSettings | Um grupo de propriedades sobre como ler dados de um armazenamento de dados. Cada conector baseado em arquivo tem suas próprias configurações de leitura com suporte em `storeSettings` . **Veja os detalhes no artigo do conector – > seção Propriedades da atividade de cópia**. | Não       |

**Configurações de leitura de JSON** com suporte em `formatSettings` :

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | O tipo de formatSettings deve ser definido como **JsonReadSettings**. | Sim      |
| compactproperties | Um grupo de propriedades sobre como descompactar dados para um determinado codec de compactação. | Não       |
| preserveZipFileNameAsFolder<br>(*em `compressionProperties` -> `type` como `ZipDeflateReadSettings`*)  | Aplica-se quando o conjunto de dados de entrada é configurado com compactação **ZipDeflate** . Indica se o nome do arquivo zip de origem deve ser preservado como estrutura de pastas durante a cópia.<br>-Quando definido como **true (padrão)**, data Factory grava arquivos descompactados em `<path specified in dataset>/<folder named as source zip file>/` .<br>-Quando definido como **false**, data Factory grava arquivos descompactados diretamente no `<path specified in dataset>` . Verifique se você não tem nomes de arquivo duplicados em arquivos zip de origem diferentes para evitar a corrida ou comportamento inesperado.  | Não |
| preserveCompressionFileNameAsFolder<br>(*em `compressionProperties` -> `type` como `TarGZipReadSettings` ou `TarReadSettings`*) | Aplica-se quando o conjunto de dados de entrada é configurado com / compactação **tar** TarGzip. Indica se o nome do arquivo compactado de origem deve ser preservado como estrutura de pastas durante a cópia.<br>-Quando definido como **true (padrão)**, data Factory grava arquivos descompactados em `<path specified in dataset>/<folder named as source compressed file>/` . <br>-Quando definido como **false**, data Factory grava arquivos descompactados diretamente no `<path specified in dataset>` . Verifique se você não tem nomes de arquivo duplicados em arquivos de origem diferentes para evitar a corrida ou comportamento inesperado. | Não |

### <a name="json-as-sink"></a>JSON como coletor

As propriedades a seguir têm suporte na seção ***\* coletor \**** de atividade de cópia.

| Propriedade      | Descrição                                                  | Obrigatório |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | A propriedade Type da fonte da atividade de cópia deve ser definida como **JSONSink**. | Sim      |
| formatSettings | Um grupo de propriedades. Consulte a tabela **configurações de gravação JSON** abaixo. | Não       |
| storeSettings | Um grupo de propriedades sobre como gravar dados em um armazenamento de dados. Cada conector baseado em arquivo tem suas próprias configurações de gravação com suporte em `storeSettings` . **Veja os detalhes no artigo do conector – > seção Propriedades da atividade de cópia**. | Não       |

**Configurações de gravação JSON** com suporte em `formatSettings` :

| Propriedade      | Descrição                                                  | Obrigatório                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | O tipo de formatSettings deve ser definido como **JsonWriteSettings**. | Sim                                                   |
| filePattern |Indique o padrão de dados armazenados em cada arquivo JSON. Os valores permitidos são: **setOfObjects** (linhas JSON) e **arrayOfObjects**. O valor **padrão** é **setOfObjects**. Veja a seção [Padrões de arquivo JSON](#json-file-patterns) para obter detalhes sobre esses padrões. |Não |

### <a name="json-file-patterns"></a>Padrões de arquivo JSON

Ao copiar dados de arquivos JSON, a atividade de cópia pode detectar e analisar automaticamente os padrões de arquivos JSON a seguir. Ao gravar dados em arquivos JSON, você pode configurar o padrão de arquivo no coletor de atividade de cópia.

- **Tipo I: setOfObjects**

    Cada arquivo contém um único objeto, linhas JSON ou objetos concatenados.

    * **Exemplo de JSON de objeto único**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        ```

    * **Linhas JSON (padrão para coletor)**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **Exemplo de JSON concatenado**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        }
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
        ```

- **Tipo II: arrayOfObjects**

    Cada arquivo contém uma matriz de objetos.

    ```json
    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
    ```

## <a name="mapping-data-flow-properties"></a>Propriedades do fluxo de dados de mapeamento

No mapeamento de fluxos de dados, você pode ler e gravar no formato JSON nos seguintes armazenamentos de dados: [armazenamento de BLOBs do Azure](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)e [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

### <a name="source-properties"></a>Propriedades de origem

A tabela abaixo lista as propriedades com suporte por uma origem JSON. Você pode editar essas propriedades na guia **Opções de origem** .

| Nome | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Caminhos curinga | Todos os arquivos correspondentes ao caminho curinga serão processados. Substitui a pasta e o caminho do arquivo definido no conjunto de um. | não | String[] | wildcardPaths |
| Caminho raiz da partição | Para dados de arquivo particionados, você pode inserir um caminho raiz de partição para ler pastas particionadas como colunas | não | String | partitionRootPath |
| Lista de arquivos | Se sua fonte está apontando para um arquivo de texto que lista os arquivos a serem processados | não | `true` ou `false` | File |
| Coluna para armazenar o nome do arquivo | Criar uma nova coluna com o nome e o caminho do arquivo de origem | não | String | rowUrlColumn |
| Após a conclusão | Exclua ou mova os arquivos após o processamento. O caminho do arquivo inicia a partir da raiz do contêiner | não | Excluir: `true` ou `false` <br> Prosseguir `['<from>', '<to>']` | purgeFiles <br> MoveFile |
| Filtrar por última modificação | Escolher filtrar arquivos com base na última alteração | não | Timestamp | modifiedAfter <br> modifiedBefore |
| Documento único | O mapeamento de fluxos de dados lê um documento JSON de cada arquivo | não | `true` ou `false` | singleDocument |
| Nomes de colunas sem aspas | Se a seleção de **nomes de coluna sem aspas** for selecionada, o mapeamento de fluxos de dados lerá as colunas JSON que não estão entre aspas. | não | `true` ou `false` |  unquotedColumnNames |
| Tem comentários | Select **tem comentários** se os dados JSON tiverem comentários sobre estilo C ou C++ | não | `true` ou `false` | ascomes |
| Entre aspas simples | Lê as colunas JSON que não estão entre aspas | não | `true` ou `false` | singleQuoted |
| Barra invertida | Selecionar **barra invertida** se as barras invertidas forem usadas para escapar caracteres nos dados JSON | não | `true` ou `false` | backslashEscape |
| Não permitir nenhum arquivo encontrado | Se for true, um erro não será gerado se nenhum arquivo for encontrado | não | `true` ou `false` | ignoreNoFilesFound |

### <a name="source-format-options"></a>Opções de formato de origem

O uso de um conjunto de dados JSON como uma origem em seu Flow permite definir cinco configurações adicionais. Essas configurações podem ser encontradas sob o acordeão de **configurações JSON** na guia **Opções de origem** . Para configuração de **formulário de documento** , você pode selecionar um documento **único**, **documento por linha**  e **matriz de tipos de documentos** .

![Configurações de JSON](media/data-flow/json-settings.png "Configurações de JSON")

#### <a name="default"></a>Padrão

Por padrão, os dados JSON são lidos no formato a seguir.

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

#### <a name="single-document"></a>Documento único

Se **um único documento** estiver selecionado, o mapeamento de fluxos de dados lerá um documento JSON de cada arquivo. 

``` json
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```
Se o **documento por linha** estiver selecionado, o mapeamento de fluxos de dados lerá um documento JSON de cada linha em um arquivo. 

``` json
File1.json
{"json": "record 1 }

File2.json
 {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
 {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}

File3.json
 {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
 {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
 {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
```
Se a **matriz de documentos** for selecionada, o mapeamento de fluxos de dados lerá uma matriz de documento de um arquivo. 

``` json
File.json
[
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
```
> [!NOTE]
> Se os fluxos de dados lançarem um erro informando "corrupt_record" ao visualizar os dados JSON, é provável que os dados contidos contenham um único documento em seu arquivo JSON. A configuração de "documento único" deve limpar esse erro.

#### <a name="unquoted-column-names"></a>Nomes de colunas sem aspas

Se a seleção de **nomes de coluna sem aspas** for selecionada, o mapeamento de fluxos de dados lerá as colunas JSON que não estão entre aspas. 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

#### <a name="has-comments"></a>Tem comentários

Select **tem comentários** se os dados JSON tiverem comentários de estilo C ou C++.

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

#### <a name="single-quoted"></a>Entre aspas simples

Selecione **aspas simples** se os campos e valores JSON usarem aspas simples em vez de aspas duplas.

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

#### <a name="backslash-escaped"></a>Barra invertida

Selecione **barra invertida** se as barras invertidas forem usadas para escapar caracteres nos dados JSON.

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

### <a name="sink-properties"></a>Propriedades do coletor

A tabela abaixo lista as propriedades com suporte por um coletor JSON. Você pode editar essas propriedades na guia **configurações** .

| Nome | Descrição | Obrigatório | Valores permitidos | Propriedade de script de fluxo de dados |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Limpar a pasta | Se a pasta de destino for limpa antes da gravação | não | `true` ou `false` | truncate |
| Opção de nome de arquivo | O formato de nomenclatura dos dados gravados. Por padrão, um arquivo por partição no formato `part-#####-tid-<guid>` | não | Padrão: cadeia de caracteres <br> Por partição: cadeia de caracteres [] <br> Como dados na coluna: String <br> Saída para arquivo único: `['<fileName>']`  | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |

### <a name="creating-json-structures-in-a-derived-column"></a>Criando estruturas JSON em uma coluna derivada

Você pode adicionar uma coluna complexa ao fluxo de dados por meio do construtor de expressões de coluna derivada. Na transformação coluna derivada, adicione uma nova coluna e abra o construtor de expressões clicando na caixa azul. Para tornar uma coluna complexa, você pode inserir a estrutura JSON manualmente ou usar a UX para adicionar Subcolunas de forma interativa.

#### <a name="using-the-expression-builder-ux"></a>Usando a UX do construtor de expressões

No painel do lado do esquema de saída, passe o mouse sobre uma coluna e clique no ícone de adição. Selecione **Adicionar subcoluna** para tornar a coluna um tipo complexo.

![Adicionar subcoluna](media/data-flow/derive-add-subcolumn.png "Adicionar subcoluna")

Você pode adicionar colunas e Subcolunas adicionais da mesma maneira. Para cada campo não complexo, uma expressão pode ser adicionada no editor de expressão à direita.

![Adicionar coluna complexa](media/data-flow/derive-complex-column.png "Adicionar colunas")

#### <a name="entering-the-json-structure-manually"></a>Inserindo a estrutura JSON manualmente

Para adicionar manualmente uma estrutura JSON, adicione uma nova coluna e insira a expressão no editor. A expressão segue o seguinte formato geral:

```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```

Se essa expressão foi inserida para uma coluna chamada "complexColumn", ela seria gravada no coletor como o JSON a seguir:

```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>Exemplo de script manual para a definição hierárquica completa
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Fluxo de dados de mapeamento](concepts-data-flow-overview.md)
- [Atividade de pesquisa](control-flow-lookup-activity.md)
- [Atividade GetMetadata](control-flow-get-metadata-activity.md)
