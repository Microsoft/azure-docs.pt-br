---
title: "Fluxo de trabalho de exemplo para preparo dos discos rígidos de um trabalho de importação do serviço de Importação/Exportação do Azure | Microsoft Docs"
description: "Veja um passo a passo para o processo completo de preparo de unidades para um trabalho de importação no serviço de Importação/Exportação do Azure."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: muralikk
ms.openlocfilehash: 60139ff36b66432620591ceaf201e046ad30217f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>Fluxo de trabalho de exemplo para preparo dos discos rígidos para um trabalho de importação

Este artigo explica o processo completo de preparar unidades para um trabalho de importação.

## <a name="sample-data"></a>Dados de amostra

Este exemplo importa os seguintes dados para uma conta de armazenamento do Azure denominada `mystorageaccount`:

|Local|Descrição|Tamanho dos dados|
|--------------|-----------------|-----|
|H:\Video\ |Uma coleção de vídeos|12 TB|
|H:\Photo\ |Uma coleção de fotos|30 GB|
|K:\Temp\FavoriteMovie.ISO|Uma imagem de disco Blu-ray™|25 GB|
|\\\bigshare\john\music\ |Uma coleção de arquivos de música em um compartilhamento de rede|10 GB|

## <a name="storage-account-destinations"></a>Destinos de conta de armazenamento

O trabalho de importação importará os dados nos destinos a seguir na conta de armazenamento:

|Fonte|Blob de destino ou diretório virtual|
|------------|-------------------------------------------|
|H:\Video\ |video/|
|H:\Photo\ |photo/|
|K:\Temp\FavoriteMovie.ISO|favorite/FavoriteMovies.ISO|
|\\\bigshare\john\music\ |music|

Com esse mapeamento, o arquivo `H:\Video\Drama\GreatMovie.mov` será importado para o blob `https://mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov`.

## <a name="determine-hard-drive-requirements"></a>Determinar os requisitos de disco rígido

Em seguida, para determinar quantos discos rígidos são necessários, calcule o tamanho dos dados:

`12TB + 30GB + 25GB + 10GB = 12TB + 65GB`

Neste exemplo, duas unidades de disco rígido de 8TB devem ser suficientes. No entanto, como o diretório de origem `H:\Video` tem 12 TB de dados e a capacidade do disco rígido único é de apenas 8 TB, você poderá especificar isso da seguinte forma no arquivo **driveset.csv**:

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
X,Format,SilentMode,Encrypt,
Y,Format,SilentMode,Encrypt,
```
A ferramenta distribui dados em dois discos rígidos de forma otimizada.

## <a name="attach-drives-and-configure-the-job"></a>Anexar discos e configurar o trabalho
Você anexará ambos os discos ao computador e criará volumes. Em seguida, crie o arquivo **dataset.csv**:
```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
H:\Video\,video/,BlockBlob,rename,None,H:\mydirectory\properties.xml
H:\Photo\,photo/,BlockBlob,rename,None,H:\mydirectory\properties.xml
K:\Temp\FavoriteVideo.ISO,favorite/FavoriteVideo.ISO,BlockBlob,rename,None,H:\mydirectory\properties.xml
\\myshare\john\music\,music/,BlockBlob,rename,None,H:\mydirectory\properties.xml
```

Além disso, você pode definir os metadados para todos os arquivos a seguir:

* **UploadMethod:** serviço de Importação/Exportação do Windows Azure
* **DataSetName:** SampleData
* **CreationDate:** 10/1/2013

Para definir metadados para os arquivos importados, crie um arquivo de texto `c:\WAImportExport\SampleMetadata.txt`, com o seguinte conteúdo:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Metadata>
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>
    <DataSetName>SampleData</DataSetName>
    <CreationDate>10/1/2013</CreationDate>
</Metadata>
```

Você também pode definir algumas propriedades para o `FavoriteMovie.ISO` blob:

* **Content-Type:** application/octet-stream
* **Content-MD5:** Q2hlY2sgSW50ZWdyaXR5IQ==
* **Cache-Control:** no-cache

Para definir essas propriedades, crie um arquivo de texto `c:\WAImportExport\SampleProperties.txt`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Properties>
    <Content-Type>application/octet-stream</Content-Type>
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>
    <Cache-Control>no-cache</Cache-Control>
</Properties>
```

## <a name="run-the-azure-importexport-tool-waimportexportexe"></a>Execute a Ferramenta de Importação/Exportação do Azure (WAImportExport.exe)

Agora você está pronto para executar a Ferramenta de Importação/Exportação do Azure para preparar as duas unidades de disco rígido.

**Para a primeira sessão:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

Se mais dados precisam ser adicionados, crie outro arquivo de conjunto de dados (mesmo formato que Initialdataset).

**Para a segunda sessão:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

Depois de concluir as sessões de cópia, você pode desconectar as duas unidades do computador de cópia e enviá-las para o centro de dados Azure apropriado. Você vai carregar os dois arquivos do diário, `<FirstDriveSerialNumber>.xml` e `<SecondDriveSerialNumber>.xml`, quando você cria a tarefa de importação no portal do Azure.

## <a name="next-steps"></a>Próximas etapas

* [Preparação de discos rígidos para um trabalho de importação](../storage-import-export-tool-preparing-hard-drives-import.md)
* [Referência rápida para comandos usados frequentemente](../storage-import-export-tool-quick-reference.md)
