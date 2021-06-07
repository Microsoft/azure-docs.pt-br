---
title: Enviar um fluxo de trabalho usando a entrada de arquivo BAM
titleSuffix: Microsoft Genomics
description: Este artigo demonstra como enviar um fluxo de trabalho para o serviço de Microsoft Genomics se o arquivo de entrada for um único arquivo BAM.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 12/07/2017
ms.openlocfilehash: 5145aa0ffdc4095f178a214f63433e5bcece83b6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "72249160"
---
# <a name="submit-a-workflow-using-a-bam-file-input"></a>Enviar um fluxo de trabalho usando uma entrada de arquivo do BAM

Este artigo demonstra como enviar um fluxo de trabalho para o serviço de Microsoft Genomics se o arquivo de entrada for um único arquivo BAM. Este tópico pressupõe que você já instalou e executou o cliente `msgen` e está familiarizado sobre como usar o Armazenamento do Azure. Se você enviou com êxito um fluxo de trabalho usando os dados de exemplo fornecidos, você está pronto para continuar com este artigo. 

## <a name="set-up-upload-your-bam-file-to-azure-storage"></a>Configurar: carregar seu arquivo BAM no armazenamento do Azure
Vamos assumir que você tenha um arquivo BAM, *reads.bam*, e já o carregou na sua conta de armazenamento *myaccount* no Azure como **https://<span></span>myaccount.blob.core <span></span>.windows <span></span>.net <span></span>/inputs/reads <span></span>.bam <span></span>**. Você tem a URL da API e sua chave de acesso. Você deseja ter saídas em **https://<span></span>myaccount.blob.core <span></span>.windows <span></span>.net <span></span>/outputs <span></span>**.



## <a name="submit-your-job-to-the-msgen-client"></a>Enviar o trabalho para o cliente `msgen` 


Aqui está o conjunto mínimo de argumentos que você precisará fornecer para o cliente `msgen`; quebras de linha foram adicionadas para maior clareza:

Para Windows:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads.bam ^
  --output-storage-account-name myaccount ^
  --output-storage-account-key <storage access key to "myaccount"> ^
  --output-storage-account-container outputs
```


Para Unix

```
msgen submit \
  --api-url-base <Genomics API URL> \
  --access-key <Genomics access key> \
  --process-args R=b37m1 \
  --input-storage-account-name myaccount \
  --input-storage-account-key <storage access key to "myaccount"> \
  --input-storage-account-container inputs \
  --input-blob-name-1 reads.bam \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


Se você preferir usar um arquivo de configuração, é isto que ele deve conter:

``` config.txt
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads.bam
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Envie o arquivo `config.txt` com esta invocação: `msgen submit -f config.txt`

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você carregou um arquivo BAM no Armazenamento do Azure e enviou um fluxo de trabalho para o serviço do Microsoft Genomics por meio do cliente Python `msgen`. Para obter informações adicionais sobre o envio de fluxo de trabalho e outros comandos que você pode usar com o serviço do Microsoft Genomics, consulte nossas [Perguntas frequentes](frequently-asked-questions-genomics.md). 
