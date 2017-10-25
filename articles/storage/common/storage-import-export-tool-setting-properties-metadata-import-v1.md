---
title: "Configuração de propriedades e metadados usando a Importação/Exportação do Azure - v1 | Microsoft Docs"
description: "Saiba como especificar as propriedades e os metadados a serem definidos nos blobs de destino durante a execução da Ferramenta de Importação/Exportação do Azure para preparar as unidades. Este documento refere-se à v1 da Ferramenta de Importação/Exportação."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: e8541695-bcfb-4bf0-84d9-72c9de32a0a8
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 77bdaa5559de86cd1de9f30e70656e47fd5719e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="setting-properties-and-metadata-during-the-import-process"></a>Configurando propriedades e metadados durante o processo de importação
Quando você executa a Ferramenta de Importação/Exportação do Microsoft Azure para preparar as unidades, é possível especificar as propriedades e os metadados a serem definidos nos blobs de destino. Siga estas etapas:  
  
1.  Para definir propriedades de blob, crie um arquivo de texto no computador local que especifica os nomes e valores das propriedades.  
  
2.  Para definir metadados de blob, crie um arquivo de texto no computador local que especifica os nomes e valores dos metadados.  
  
3.  Passe o caminho completo para um ou ambos os arquivos à Ferramenta de Importação/Exportação do Azure como parte da operação `PrepImport`.  
  
> [!NOTE]
>  Ao especificar um arquivo de propriedades ou de metadados como parte de uma sessão de cópia, essas propriedades ou esses metadados são definidos para cada blob importado como parte da sessão de cópia. Se desejar especificar outro conjunto de propriedades ou de metadados para alguns dos blobs importados, você precisará criar uma sessão de cópia separada com arquivos de propriedades ou de metadados diferentes.  
  
## <a name="specify-blob-properties-in-a-text-file"></a>Especificar as propriedades de blob em um arquivo de texto  
Para especificar as propriedades de blob, crie um arquivo de texto local e inclua um XML que especifica os nomes da propriedade como elementos e os valores da propriedade como valores. Este é um exemplo que especifica alguns valores de propriedade:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
    <Content-Type>application/octet-stream</Content-Type>  
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>  
    <Cache-Control>no-cache</Cache-Control>  
</Properties>  
```
  
Salve o arquivo em uma localização local como `C:\WAImportExport\ImportProperties.txt`.  
  
## <a name="specify-blob-metadata-in-a-text-file"></a>Especificar os metadados de blob em um arquivo de texto  
Da mesma forma, para especificar os metadados de blob, crie um arquivo de texto local que especifica os nomes dos metadados como elementos e os valores dos metadados como valores. Este é um exemplo que especifica alguns valores de metadados:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>  
    <DataSetName>SampleData</DataSetName>  
    <CreationDate>10/1/2013</CreationDate>  
</Metadata>  
```
  
Salve o arquivo em uma localização local como `C:\WAImportExport\ImportMetadata.txt`.  
  
## <a name="create-a-copy-session-including-the-properties-or-metadata-files"></a>Criar uma sessão de cópia, incluindo os arquivos de propriedades ou metadados  
Ao executar a Ferramenta de Importação/Exportação do Azure para preparar o trabalho de importação, especifique o arquivo de propriedades na linha de comando usando o parâmetro `PropertyFile`. Especifique o arquivo de metadados na linha de comando usando o parâmetro `/MetadataFile`. Este é um exemplo que especifica os dois arquivos:  
  
```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```
  
## <a name="next-steps"></a>Próximas etapas

* [Formato de arquivo de propriedades e metadados de serviço de Importação/Exportação](../storage-import-export-file-format-metadata-and-properties.md)
