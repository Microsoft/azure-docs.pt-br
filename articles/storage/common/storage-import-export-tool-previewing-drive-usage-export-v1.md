---
title: Visualizando o uso da unidade de um trabalho de exportação do serviço de Importação/Exportação do Azure — v1 | Microsoft Docs
description: Saiba como visualizar a lista de blobs selecionada para um trabalho de exportação no serviço de Importação/Exportação do Azure.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 53ab1e28c5864b403d52bf5e73f0c5c41b8f18a8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478446"
---
# <a name="previewing-drive-usage-for-an-export-job"></a>Visualizando o uso da unidade de um trabalho de exportação
Antes de criar um trabalho de exportação, você precisa escolher um conjunto de blobs a ser exportado. O serviço de Importação/Exportação do Microsoft Azure permite que você use uma lista de caminhos ou prefixos de blob para representar os blobs selecionados.  
  
Em seguida, é necessário determinar quantas unidades você precisa enviar. A Ferramenta de Importação/Exportação fornece o comando `PreviewExport` para visualização do uso da unidade dos blobs selecionados, com base no tamanho das unidades que você pretende usar.

## <a name="command-line-parameters"></a>Parâmetros de linha de comando

Você pode usar os parâmetros a seguir com o comando `PreviewExport` da Ferramenta de Importação/Exportação.

|Parâmetro de linha de comando|DESCRIÇÃO|  
|--------------------------|-----------------|  
|**/logdir:**<LogDirectory\>|Opcional. O diretório de log. Os arquivos de log detalhados serão gravados nesse diretório. Se nenhum diretório de log for especificado, o diretório atual será usado como o diretório de log.|  
|**/sn:**<StorageAccountName\>|Obrigatório. O nome da conta de armazenamento do trabalho de exportação.|  
|**/sk:**<StorageAccountKey\>|Necessário somente se uma SAS do contêiner não for especificada. A chave de conta da conta de armazenamento do trabalho de exportação.|  
|**/csas:**<ContainerSas\>|Necessário somente se uma chave de conta de armazenamento não for especificada. A SAS do contêiner para listar os blobs a serem exportados no trabalho de exportação.|  
|**/ExportBlobListFile:**<ExportBlobListFile\>|Obrigatório. Caminho até o arquivo XML que contém a lista de caminhos de blob ou prefixos de caminhos de blob para os blobs a serem exportados. O formato de arquivo usado no elemento `BlobListBlobPath` da operação [Put Job](/rest/api/storageimportexport/jobs) da API REST do serviço de Importação/Exportação.|  
|**/DriveSize:**<DriveSize\>|Obrigatório. O tamanho das unidades a ser usado para um trabalho de exportação, *por exemplo*, 500 GB, 1,5 TB.|  

## <a name="command-line-example"></a>Exemplo de linha de comando

O seguinte exemplo demonstra o comando `PreviewExport`:  
  
```  
WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB    
```  
  
O arquivo de lista de blobs de exportação pode conter nomes e prefixos de blob, conforme mostrado aqui:  
  
```xml 
<?xml version="1.0" encoding="utf-8"?>  
<BlobList>  
<BlobPath>pictures/animals/koala.jpg</BlobPath>  
<BlobPathPrefix>/vhds/</BlobPathPrefix>  
<BlobPathPrefix>/movies/</BlobPathPrefix>  
</BlobList>  
```

A Ferramenta de Importação/Exportação do Azure lista todos os blobs a serem exportados e calcula como empacotá-los em unidades do tamanho especificado, levando em consideração qualquer sobrecarga necessária. Em seguida, estima o número de unidades necessárias para manter os blobs e as informações de uso da unidade.  
  
Este é um exemplo da saída, com a omissão dos logs informativos:  
  
```  
Number of unique blob paths/prefixes:   3  
Number of duplicate blob paths/prefixes:        0  
Number of nonexistent blob paths/prefixes:      1  
  
Drive size:     500.00 GB  
Number of blobs that can be exported:   6  
Number of blobs that cannot be exported:        2  
Number of drives needed:        3  
        Drive #1:       blobs = 1, occupied space = 454.74 GB  
        Drive #2:       blobs = 3, occupied space = 441.37 GB  
        Drive #3:       blobs = 2, occupied space = 131.28 GB    
```  
  
## <a name="next-steps"></a>Próximas etapas

* [Referência da Ferramenta de Importação/Exportação do Azure](../storage-import-export-tool-how-to-v1.md)
