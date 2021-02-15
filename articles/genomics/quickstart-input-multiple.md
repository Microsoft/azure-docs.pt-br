---
title: Enviar um fluxo de trabalho usando várias entradas
titleSuffix: Microsoft Genomics
description: Este artigo demonstra como enviar um fluxo de trabalho para o serviço de Microsoft Genomics se o arquivo de entrada for vários arquivos FASTQ ou BAM do mesmo exemplo.
services: genomics
ms.service: genomics
author: grhuynh
manager: cgronlund
ms.author: grhuynh
ms.topic: conceptual
ms.date: 02/05/2018
ms.openlocfilehash: b426015906a8e17674123c0c3ad2fccb9c43798f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "72248563"
---
# <a name="submit-a-workflow-using-multiple-inputs-from-the-same-sample"></a>Enviar um fluxo de trabalho usando várias entradas do mesmo exemplo

Este artigo demonstra como enviar um fluxo de trabalho para o serviço de Microsoft Genomics se o arquivo de entrada for vários arquivos FASTQ ou BAM **provenientes do mesmo exemplo**. Por exemplo, se você executou o **mesmo exemplo** em várias rotas no sequenciador, o sequenciador pôde enviar um par de arquivos FASTQ para cada rota. Em vez de concatenar esses arquivos FASTQ antes do alinhamento e análise de variantes, você pode enviar diretamente todas essas entradas para o cliente `msgen`. A saída do cliente `msgen` seria um **conjunto único** de arquivos, incluindo um arquivo .bam, .bai, .vcf. 

Lembre-se, no entanto, de que você **não consegue** misturar arquivos FASTQ e BAM no mesmo envio. Além disso, **não é possível** enviar vários arquivos FASTQ ou BAM arquivos a partir de várias pessoas. 

Este artigo pressupõe que você já instalou e executou o cliente `msgen` e está familiarizado sobre como usar o Armazenamento do Azure. Se você enviou com êxito um fluxo de trabalho usando os dados de exemplo fornecidos, você está pronto para continuar com este artigo. 


## <a name="multiple-bam-files"></a>Vários arquivos BAM

### <a name="upload-your-input-files-to-azure-storage"></a>Carregar seus arquivos de entrada no armazenamento do Azure
Vamos assumir que você tem vários arquivos BAM como entrada, *reads.bam*, *additional_reads.bam* e *yet_more_reads.bam*, e já os carregou na sua conta de armazenamento *myaccount* no Azure. Você tem a URL da API e sua chave de acesso. Você deseja ter saídas em **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>**.


### <a name="submit-your-job-to-the-msgen-client"></a>Enviar o trabalho para o cliente `msgen` 

Você pode enviar vários arquivos BAM passando todos os seus nomes para o argumento --input-blob-name-1. Observe que todos os arquivos devem vir do mesmo exemplo, mas sua ordem não é importante. A seção a seguir detalha envios de exemplo de uma linha de comando no Windows, no Unix e usando um arquivo de configuração. Quebras de linha foram adicionadas para maior clareza:


Para Windows:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads.bam additional_reads.bam yet_more_reads.bam ^
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
  --input-blob-name-1 reads.bam additional_reads.bam yet_more_reads.bam \
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
input_blob_name_1:                reads.bam additional_reads.bam yet_more_reads.bam
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Envie o arquivo `config.txt` com esta invocação: `msgen submit -f config.txt`


## <a name="multiple-paired-fastq-files"></a>Vários arquivos FASTQ emparelhados

### <a name="upload-your-input-files-to-azure-storage"></a>Carregar seus arquivos de entrada no armazenamento do Azure
Vamos assumir que você tem diversos arquivos FASTQ emparelhados como entrada, *reads_1.fq.gz* e *reads_2.fq.gz*, *additional_reads_1.fq.gz* e *additional_reads_2.fq.gz*, *yet_more_reads_1.fq.gz* e *yet_more_reads_2.fq.gz*. Você os carregou para sua conta de armazenamento *myaccount* no Azure e você tem a URL da API e sua chave de acesso. Você deseja ter saídas em **https://<span></span>myaccount.blob.core<span></span>.windows<span></span>.net<span></span>/outputs<span></span>**.


### <a name="submit-your-job-to-the-msgen-client"></a>Enviar o trabalho para o cliente `msgen` 

Arquivos de FASTQ emparelhados não apenas precisam vir do mesmo exemplo, mas também precisam ser processados em conjunto.  A ordem dos nomes de arquivo faz diferença quando eles são passados como argumentos para--input-blob-name-1 e --input-blob-name-2. 

A seção a seguir detalha envios de exemplo de uma linha de comando no Windows, no Unix e usando um arquivo de configuração. Quebras de linha foram adicionadas para maior clareza:


Para Windows:

```
msgen submit ^
  --api-url-base <Genomics API URL> ^
  --access-key <Genomics access key> ^
  --process-args R=b37m1 ^
  --input-storage-account-name myaccount ^
  --input-storage-account-key <storage access key to "myaccount"> ^
  --input-storage-account-container inputs ^
  --input-blob-name-1 reads_1.fastq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz ^
  --input-blob-name-2 reads_2.fastq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz ^
  --output-storage-account-name myaccount ^
  --output-storage-account-key <storage access key to "myaccount"> ^
  --output-storage-account-container outputs
```


Para Unix:

```
msgen submit \
  --api-url-base <Genomics API URL> \
  --access-key <Genomics access key> \
  --process-args R=b37m1 \
  --input-storage-account-name myaccount \
  --input-storage-account-key <storage access key to "myaccount"> \
  --input-storage-account-container inputs \
  --input-blob-name-1 reads_1.fastq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz \
  --input-blob-name-2 reads_2.fastq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz \
  --output-storage-account-name myaccount \
  --output-storage-account-key <storage access key to "myaccount"> \
  --output-storage-account-container outputs
```


Se você preferir usar um arquivo de configuração, é isto que ele deve conter:

```
api_url_base:                     <Genomics API URL>
access_key:                       <Genomics access key>
process_args:                     R=b37m1
input_storage_account_name:       myaccount
input_storage_account_key:        <storage access key to "myaccount">
input_storage_account_container:  inputs
input_blob_name_1:                reads_1.fq.gz additional_reads_1.fastq.gz yet_more_reads_1.fastq.gz
input_blob_name_2:                reads_2.fq.gz additional_reads_2.fastq.gz yet_more_reads_2.fastq.gz
output_storage_account_name:      myaccount
output_storage_account_key:       <storage access key to "myaccount">
output_storage_account_container: outputs
```

Envie o arquivo `config.txt` com esta invocação: `msgen submit -f config.txt`

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você carregou vários arquivos BAM ou arquivos FASTQ emparelhados no Armazenamento do Azure e enviou um fluxo de trabalho para o serviço do Microsoft Genomics por meio do cliente Python `msgen`. Para saber mais informações sobre o envio de fluxo de trabalho e outros comandos que você pode usar com o serviço do Microsoft Genomics, consulte nossas [Perguntas frequentes](frequently-asked-questions-genomics.md). 