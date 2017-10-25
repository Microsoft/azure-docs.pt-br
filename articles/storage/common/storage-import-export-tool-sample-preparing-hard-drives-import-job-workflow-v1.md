---
title: "Fluxo de trabalho de exemplo para preparo dos discos rígidos de um trabalho de importação do serviço de Importação/Exportação do Azure — v1 | Microsoft Docs"
description: "Veja um passo a passo para o processo completo de preparo de unidades para um trabalho de importação no serviço de Importação/Exportação do Azure."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 6eb1b1b7-c69f-4365-b5ef-3cd5e05eb72a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 179c6bac9a2d9509baa0007a7008d75d0874a25e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>Fluxo de trabalho de exemplo para preparo dos discos rígidos para um trabalho de importação
Este tópico explica o processo completo de preparar unidades para um trabalho de importação.  
  
Este exemplo importa os seguintes dados para uma conta de armazenamento do Azure no Windows denominada `mystorageaccount`:  
  
|Local|Descrição|  
|--------------|-----------------|  
|H:\Video|Uma coleção de vídeos, 5 TB no total.|  
|H:\Photo|Uma coleção de fotos, 30 GB no total.|  
|K:\Temp\FavoriteMovie.ISO|Uma imagem de disco Blu-ray™, 25 GB.|  
|\\\bigshare\john\music|Uma coleção de arquivos de música em um compartilhamento de rede, 10 GB no total.|  
  
O trabalho de importação importa estes dados nos destinos a seguir na conta de armazenamento:  
  
|Fonte|Blob de destino ou diretório virtual|  
|------------|-------------------------------------------|  
|H:\Video|https://mystorageaccount.blob.core.windows.net/video|  
|H:\Photo|https://mystorageaccount.blob.core.windows.net/photo|  
|K:\Temp\FavoriteMovie.ISO|https://mystorageaccount.blob.core.windows.net/favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|https://mystorageaccount.blob.core.windows.net/music|  
  
Com esse mapeamento, o arquivo `H:\Video\Drama\GreatMovie.mov` é importado para o blob `https://mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov`.  
  
Em seguida, para determinar quantos discos rígidos são necessários, calcule o tamanho dos dados:  
  
`5TB + 30GB + 25GB + 10GB = 5TB + 65GB`  
  
Neste exemplo, duas unidades de disco rígido de 3 TB devem ser suficientes. No entanto, como o diretório de origem `H:\Video` tem 5 TB de dados e a capacidade do disco rígido único é de apenas 3 TB, será necessário dividir `H:\Video` em dois diretórios menores, `H:\Video1` e `H:\Video2`, antes de executar a Ferramenta de Importação/Exportação do Microsoft Azure. Esta etapa gera os seguintes diretórios de origem:  
  
|Local|Tamanho|Blob de destino ou diretório virtual|  
|--------------|----------|-------------------------------------------|  
|H:\Video1|2.5 TB|https://mystorageaccount.blob.core.windows.net/video|  
|H:\Video2|2.5 TB|https://mystorageaccount.blob.core.windows.net/video|  
|H:\Photo|30 GB|https://mystorageaccount.blob.core.windows.net/photo|  
|K:\Temp\FavoriteMovies.ISO|25 GB|https://mystorageaccount.blob.core.windows.net/favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|10 GB|https://mystorageaccount.blob.core.windows.net/music|  
  
 Embora o diretório `H:\Video` tenha sido dividido em dois diretórios, eles apontam para o mesmo diretório virtual de destino na conta de armazenamento. Dessa forma, todos os arquivos de vídeo são mantidos em um único `video` contêiner na conta de armazenamento.  
  
 Em seguida, os diretórios de origem anteriores são distribuídos uniformemente nos dois discos rígidos:  
  
||||  
|-|-|-|  
|Disco rígido|Diretórios de origem|Tamanho total|  
|Primeira unidade|H:\Video1|2,5 TB + 30 GB|  
||H:\Photo||  
|Segunda unidade|H:\Video2|2,5 TB + 35 GB|  
||K:\Temp\BlueRay.ISO||  
||\\\bigshare\john\music||  
  
Além disso, você pode definir os metadados para todos os arquivos a seguir:  
  
-   **UploadMethod:** serviço de Importação/Exportação do Windows Azure  
  
-   **DataSetName:** SampleData  
  
-   **CreationDate:** 10/1/2013  
  
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
  
-   **Content-Type:** application/octet-stream  
  
-   **Content-MD5:** Q2hlY2sgSW50ZWdyaXR5IQ==  
  
-   **Cache-Control:** no-cache  
  
Para definir essas propriedades, crie um arquivo de texto `c:\WAImportExport\SampleProperties.txt`:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
    <Content-Type>application/octet-stream</Content-Type>  
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>  
    <Cache-Control>no-cache</Cache-Control>  
</Properties>  
```
  
Agora você está pronto para executar a Ferramenta de Importação/Exportação do Azure para preparar as duas unidades de disco rígido. Observe que:  
  
-   A primeira unidade é montada como unidade X.  
  
-   A segunda unidade é montada como unidade Y.  
  
-   A chave da conta de armazenamento `mystorageaccount` é `8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg==`.  

## <a name="preparing-disk-for-import-when-data-is-pre-loaded"></a>Preparando o disco para importação quando dados são carregados previamente
 
 Se os dados a serem importados já estão presentes no disco, use o sinalizador /skipwrite. O valor de /t e /srcdir deve apontar para o disco que está sendo preparado para importação. Se todos os dados a serem importados não forem para o mesmo diretório virtual de destino ou raiz da conta de armazenamento, execute o mesmo comando para cada diretório de destino separadamente, mantendo o valor de /id o mesmo em todas as execuções.

>[!NOTE] 
>Não especifique /format, pois ele apagará os dados no disco. Você pode especificar / criptografar ou /bk dependendo se o disco já está criptografado ou não. 
>

```
    When data is already present on the disk for each drive run the following command.
    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:x:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt /skipwrite
```

## <a name="copy-sessions---first-drive"></a>Sessões de cópia — primeira unidade

Para a primeira unidade, execute a Ferramenta de Importação/Exportação do Azure duas vezes para copiar os dois diretórios de origem:  

**Primeira sessão de cópia**
  
```
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:H:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```

**Segunda sessão de cópia**

```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Photo /srcdir:H:\Photo /dstdir:photo/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt
```

## <a name="copy-sessions---second-drive"></a>Sessões de cópia — segunda unidade
 
Para a segunda unidade, execute a Ferramenta de Importação/Exportação do Azure três vezes, uma vez para cada diretório de origem e uma vez para o arquivo de imagem autônomo Blu-Ray™):  
  
**Primeira sessão de cópia** 

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Video2 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:y /format /encrypt /srcdir:H:\Video2 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```
  
**Segunda sessão de cópia**

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Music /srcdir:\\bigshare\john\music /dstdir:music/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```  
  
**Terceira sessão de cópia**  

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```

## <a name="copy-session-completion"></a>Conclusão da sessão de cópia

Depois de concluir as sessões de cópia, você pode desconectar as duas unidades do computador de cópia e enviá-las para o datacenter do Microsoft Azure apropriado. Carregue os dois arquivos do diário, `FirstDrive.jrn` e `SecondDrive.jrn`, quando criar o trabalho de importação no [Portal do Microsoft Azure](https://manage.windowsazure.com/).  
  
## <a name="next-steps"></a>Próximas etapas

* [Preparação de discos rígidos para um trabalho de importação](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Referência rápida para comandos usados frequentemente](../storage-import-export-tool-quick-reference-v1.md) 
