---
title: Compartilhamento de arquivo do Azure – Falha ao excluir arquivos do compartilhamento de arquivo do Azure
description: Identificar e solucionar problemas de falha ao excluir arquivos do compartilhamento de arquivos do Azure.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/25/2019
ms.service: storage
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: 3d4f10745d90ccd83e7251af40d3e92a230f2fcd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94629675"
---
# <a name="azure-file-share--failed-to-delete-files-from-azure-file-share"></a>Compartilhamento de arquivo do Azure – Falha ao excluir arquivos do compartilhamento de arquivo do Azure

A falha ao excluir arquivos do compartilhamento de arquivos do Azure pode ter vários sintomas:

**Sintoma 1:**

Falha ao excluir um arquivo no compartilhamento de arquivos do Azure devido a um dos dois problemas abaixo:

* O arquivo marcado para exclusão
* O recurso especificado pode estar sendo usado por um cliente SMB

**Sintoma 2:**

Não há cota suficiente para processar este comando

## <a name="cause"></a>Causa

O erro 1816 ocorre quando você atinge o limite superior de identificadores abertos simultâneos permitidos para um arquivo, no computador em que o compartilhamento de arquivos está sendo montado. Para obter mais informações, consulte a [lista de verificação de escalabilidade e desempenho do armazenamento do Azure](../blobs/storage-performance-checklist.md).

## <a name="resolution"></a>Resolução

Reduza o número de identificadores abertos simultâneos fechando alguns identificadores.

## <a name="prerequisite"></a>Pré-requisito

### <a name="install-the-latest-azure-powershell-module"></a>Instalar o módulo de Azure PowerShell mais recente

* [Instalar módulo do Azure PowerShell](/powershell/azure/install-az-ps)

### <a name="connect-to-azure"></a>Conectar-se ao Azure:

```
# Connect-AzAccount
```

### <a name="select-the-subscription-of-the-target-storage-account"></a>Selecione a assinatura da conta de armazenamento de destino:

```
# Select-AzSubscription -subscriptionid "SubscriptionID"
```

### <a name="create-context-for-the-target-storage-account"></a>Criar contexto para a conta de armazenamento de destino:

```
$Context = New-AzStorageContext -StorageAccountName "StorageAccountName" -StorageAccountKey "StorageAccessKey"
```

### <a name="get-the-current-open-handles-of-the-file-share"></a>Obtenha os identificadores abertos atuais do compartilhamento de arquivos:

```
# Get-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Recursive
```

## <a name="example-result"></a>Resultado do exemplo:

|Identificadorid|Caminho|ClientIp|ClientPort|Opentime|LastReconnectTime|FileId|ParentId|SessionId|
|---|---|---|---|---|---|---|---|---|
|259101229083|---|10.222.10.123|62758|2019-10-05|12:16:50Z|0|0|9507758546259807489|
|259101229131|---|10.222.10.123|62758|2019-10-05|12:36:20Z|0|0|9507758546259807489|
|259101229137|---|10.222.10.123|62758|2019-10-05|12:36:53Z|0|0|9507758546259807489|
|259101229136|Nova pasta/test.zip|10.222.10.123|62758|2019-10-05|12:36:29Z|13835132822072852480|9223446803645464576|9507758546259807489|
|259101229135|test.zip|37.222.22.143|62758|2019-10-05|12:36:24Z|11529250230440558592|0|9507758546259807489|

### <a name="close-an-open-handle"></a>Fechar um identificador aberto:

Para fechar um identificador aberto, use o seguinte comando:

```
# Close-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Path 'New folder/test.zip' -CloseAll
```

## <a name="next-steps"></a>Próximas etapas

* [Solucionar problemas do Arquivos do Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Solucionar problemas do Arquivos do Azure no Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Solucionar problemas da Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md)