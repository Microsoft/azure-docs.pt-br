---
title: Mover dados de e para o Armazenamento de Blobs do Azure usando Python | Microsoft Docs
description: Mover dados de e para o Armazenamento de Blobs do Azure usando Python
services: machine-learning,storage
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 24276252-b3dd-4edf-9e5d-f6803f8ccccc
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
ms.openlocfilehash: d7847f695a77ad469f56a20518cb979c41384d1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="move-data-to-and-from-azure-blob-storage-using-python"></a>Mover dados de e para o Armazenamento de Blobs do Azure usando Python
Este tópico descreve como listar, carregar e baixar blobs usando a API Python. Com a API do Python fornecida no SDK do Azure, você pode:

* Criar um contêiner
* Carregar um blob em um contêiner
* Baixar blobs
* Listar os blobs em um contêiner
* Excluir um blob

Para obter mais informações sobre como usar a API Python, consulte [Como usar o serviço de armazenamento de blob do Python](../../storage/blobs/storage-python-how-to-use-blob-storage.md).

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

## <a name="upload-data-to-blob"></a>Carregar dados para Blob
Adicione o trecho de código a seguir à parte superior de qualquer código Python no qual você deseja acessar programaticamente o Armazenamento do Azure:

    from azure.storage.blob import BlobService

O objeto **serviço Blob** permite que você trabalhe com contêineres e blobs. O código a seguir cria um objeto BlobService usando o nome da conta de armazenamento e a chave da conta. Substitua o nome e a chave de conta pela sua conta e chave reais.

    blob_service = BlobService(account_name="<your_account_name>", account_key="<your_account_key>")

Use os seguintes métodos para carregar dados para um blob:

1. put\_block\_blob\_from\_path (carrega o conteúdo de um arquivo do caminho especificado)
2. put\_block_blob\_from\_file (carrega o conteúdo de um arquivo/fluxo já aberto)
3. put\_block\_blob\_from\_bytes (carrega uma matriz de bytes)
4. put\_block\_blob\_from\_text (carrega o valor de texto especificado usando a codificação especificada)

O código de exemplo a seguir carrega um arquivo local para um contêiner:

    blob_service.put_block_blob_from_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

O código de exemplo a seguir carrega todos os arquivos (exceto diretórios) em um diretório local para o armazenamento de blob:

    from azure.storage.blob import BlobService
    from os import listdir
    from os.path import isfile, join

    # Set parameters here
    ACCOUNT_NAME = "<your_account_name>"
    ACCOUNT_KEY = "<your_account_key>"
    CONTAINER_NAME = "<your_container_name>"
    LOCAL_DIRECT = "<your_local_directory>"        

    blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)
    # find all files in the LOCAL_DIRECT (excluding directory)
    local_file_list = [f for f in listdir(LOCAL_DIRECT) if isfile(join(LOCAL_DIRECT, f))]

    file_num = len(local_file_list)
    for i in range(file_num):
        local_file = join(LOCAL_DIRECT, local_file_list[i])
        blob_name = local_file_list[i]
        try:
            blob_service.put_block_blob_from_path(CONTAINER_NAME, blob_name, local_file)
        except:
            print "something wrong happened when uploading the data %s"%blob_name


## <a name="download-data-from-blob"></a>Baixar Dados de Blob
Use os métodos a seguir para baixar dados de um blob:

1. get\_blob\_to\_path
2. get\_blob\_to\_file
3. get\_blob\_to\_bytes
4. get\_blob\_to\_text

Esses métodos realizam a divisão necessária quando o tamanho dos dados excede 64 MB.

O código de exemplo a seguir baixa o conteúdo de um blob em um contêiner para um arquivo local:

    blob_service.get_blob_to_path("<your_container_name>", "<your_blob_name>", "<your_local_file_name>")

O código de exemplo a seguir baixa todos os blobs de um contêiner. Ele usa list\_blobs para obter a lista de blobs disponíveis no contêiner e baixá-los para um diretório local.

    from azure.storage.blob import BlobService
    from os.path import join

    # Set parameters here
    ACCOUNT_NAME = "<your_account_name>"
    ACCOUNT_KEY = "<your_account_key>"
    CONTAINER_NAME = "<your_container_name>"
    LOCAL_DIRECT = "<your_local_directory>"        

    blob_service = BlobService(account_name=ACCOUNT_NAME, account_key=ACCOUNT_KEY)

    # List all blobs and download them one by one
    blobs = blob_service.list_blobs(CONTAINER_NAME)
    for blob in blobs:
        local_file = join(LOCAL_DIRECT, blob.name)
        try:
            blob_service.get_blob_to_path(CONTAINER_NAME, blob.name, local_file)
        except:
            print "something wrong happened when downloading the data %s"%blob.name
