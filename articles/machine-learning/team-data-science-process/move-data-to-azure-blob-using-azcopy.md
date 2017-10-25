---
title: Mover dados de e para o Armazenamento de Blobs do Azure usando AzCopy | Microsoft Docs
description: Mover dados de e para o Armazenamento de Blobs do Azure usando o AzCopy
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: c309ceb2-0e83-4a07-b16d-c997dcd62d5c
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
ms.openlocfilehash: f7a924b576690f5b76418a3de6221ef6c39c39f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azcopy"></a>Mover dados de e para o Armazenamento de Blobs do Azure usando o AzCopy
O AzCopy é um utilitário de linha de comando projetado para carregar, baixar e copiar dados do/para o armazenamento de blobs, arquivos e tabelas do Microsoft Azure.

Para obter instruções sobre como instalar o AzCopy e informações adicionais sobre como usá-lo com a plataforma do Azure, consulte [Introdução ao utilitário de linha de comando AzCopy](../../storage/common/storage-use-azcopy.md).

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Se você estiver usando a VM que foi configurada com os scripts fornecidos pelas [Máquinas virtuais de ciência de dados no Azure](virtual-machines.md), o AzCopy já estará instalado na VM.
> 
> [!NOTE]
> Para obter uma introdução completa ao Armazenamento de Blobs do Azure, consulte [Noções básicas do Blob do Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) e [Serviço de Blob do Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Este documento pressupõe que você tenha uma assinatura, uma conta de armazenamento do Azure e a chave de armazenamento correspondente dessa conta. Antes de carregar/baixar os dados, você deve conhecer o nome e a chave da sua conta do Armazenamento do Azure.

* Para configurar uma assinatura do Azure, consulte [Avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Para obter instruções sobre como criar uma conta de armazenamento e obter informações sobre conta e chave, consulte [Sobre contas do armazenamento do Azure](../../storage/common/storage-create-storage-account.md).

## <a name="run-azcopy-commands"></a>Executar comandos do AzCopy
Para executar comandos do AzCopy, abra uma janela de comando e navegue até o diretório de instalação do AzCopy no computador, no qual o executável AzCopy.exe está localizado. 

A sintaxe básica dos comandos do AzCopy é:

    AzCopy /Source:<source> /Dest:<destination> [Options]

> [!NOTE]
> Você pode adicionar a localização de instalação do AzCopy ao caminho do sistema e, em seguida, executar os comandos de qualquer diretório. Por padrão, o AzCopy está instalado em *%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy* ou *%ProgramFiles%\Microsoft SDKs\Azure\AzCopy*.
> 
> 

## <a name="upload-files-to-an-azure-blob"></a>Carregar arquivos para um blob do Azure
Para carregar um arquivo, use o seguinte comando:

    # Upload from local file system
    AzCopy /Source:<your_local_directory> /Dest: https://<your_account_name>.blob.core.windows.net/<your_container_name> /DestKey:<your_account_key> /S


## <a name="download-files-from-an-azure-blob"></a>Baixe arquivos em um blob do Azure
Para baixar um arquivo de um blob do Azure, use o comando a seguir:

    # Downloading blobs to local file system
    AzCopy /Source:https://<your_account_name>.blob.core.windows.net/<your_container_name>/<your_sub_directory_at_blob>  /Dest:<your_local_directory> /SourceKey:<your_account_key> /Pattern:<file_pattern> /S


## <a name="transfer-blobs-between-azure-containers"></a>Transferir os blobs entre os contêineres do Azure
Para transferir blobs entre os contêineres do Azure, use o comando a seguir:

    # Transferring blobs between Azure containers
    AzCopy /Source:https://<your_account_name1>.blob.core.windows.net/<your_container_name1>/<your_sub_directory_at_blob1> /Dest:https://<your_account_name2>.blob.core.windows.net/<your_container_name2>/<your_sub_directory_at_blob2> /SourceKey:<your_account_key1> /DestKey:<your_account_key2> /Pattern:<file_pattern> /S

    <your_account_name>: your storage account name
    <your_account_key>: your storage account key
    <your_container_name>: your container name
    <your_sub_directory_at_blob>: the sub directory in the container
    <your_local_directory>: directory of local file system where files to be uploaded from or the directory of local file system files to be downloaded to
    <file_pattern>: pattern of file names to be transferred. The standard wildcards are supported


## <a name="tips-for-using-azcopy"></a>Dicas para usar o AzCopy
> [!TIP]
> 1. Ao **carregar** arquivos, */S* carregará arquivos recursivamente. Sem esse parâmetro, arquivos em subpastas não serão carregados.  
> 2. Ao **baixar** arquivos, */S* pesquisa recursivamente o contêiner até que todos os arquivos no diretório especificado e de seus subdiretórios, ou todos os arquivos que correspondam ao padrão especificado no diretório especificado e seus subdiretórios, sejam baixados.  
> 3. Não é possível especificar um **arquivo de blob específico** para baixar usando o parâmetro */Source* . Para baixar um arquivo específico, especifique o nome do arquivo de blob a ser baixado usando o parâmetro */Pattern* . **/S** pode ser usado para que AzCopy procure recursivamente um padrão de nome de arquivo. Sem o parâmetro padrão, o AzCopy baixa todos os arquivos nesse diretório.
> 
> 

