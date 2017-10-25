---
title: Como criar um compartilhamento de Arquivos do Azure | Microsoft Docs
description: Como criar um compartilhamento de arquivos do Azure nos Arquivos do Azure usando o portal do Azure, PowerShell e a CLI do Azure.
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: renash
ms.openlocfilehash: bc01e5427f32e9532e39694f6de9f0b1146eda35
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-file-share-in-azure-files"></a>Criar um compartilhamento de arquivos nos Arquivos do Azure
Você pode criar compartilhamentos de Arquivos do Azure usando o [portal do Azure](https://portal.azure.com/), os cmdlets do PowerShell do Armazenamento do Azure, as bibliotecas de cliente do Armazenamento do Azure ou a API REST do Armazenamento do Azure. Neste tutorial, você irá aprender:
* [Como criar um Compartilhamento de arquivos do Azure usando o portal do Azure](#Create file share through the Portal)
* [Como criar um Compartilhamento de arquivos do Azure usando o Powershell](#Create file share using PowerShell)
* [Como criar um Compartilhamento de arquivos do Azure usando a CLI](#create-file-share-using-command-line-interface-cli)

## <a name="prerequisites"></a>Pré-requisitos
Para criar um compartilhamento de Arquivos do Azure, você pode usar uma Conta de Armazenamento que já existe ou [criar uma nova conta do Armazenamento do Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Para criar um compartilhamento de Arquivos do Azure com o PowerShell, serão necessários a chave da conta e o nome de sua conta de armazenamento. Você precisará da chave da Conta de armazenamento se planejar usar o Powershell ou a CLI.

## <a name="create-file-share-through-the-azure-portal"></a>Criar um compartilhamento de arquivos por meio do portal do Azure
1. **Vá para a folha Conta de Armazenamento no portal do Azure**:    
    ![Folha Conta de Armazenamento](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

2. **Clique no botão Adicionar Compartilhamento de arquivos**:    
    ![Clique no botão adicionar compartilhamento de arquivos](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

3. **Forneça o Nome e a Cota. No momento, a cota pode ter um máximo de 5 TiB**:    
    ![Forneça um nome e uma cota desejada para o novo compartilhamento de arquivos](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

4. **Exiba o novo compartilhamento de arquivos**: ![Exiba o novo compartilhamento de arquivos](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

5. **Carregue um arquivo**: ![Carregue um arquivo](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

6. **Navegue o compartilhamento de arquivos e gerencie seus diretórios e arquivos**: ![Navegue o compartilhamento de arquivos](./media/storage-how-to-create-file-share/create-file-share-portal6.png)


## <a name="create-file-share-through-powershell"></a>Criar um compartilhamento de arquivos com o PowerShell
Para se preparar para usar o PowerShell, baixe e instale os cmdlets do PowerShell do Azure. Consulte [Como instalar e configurar o PowerShell do Azure](https://azure.microsoft.com/documentation/articles/powershell-install-configure/) para obter o ponto e as instruções de instalação.

> [!Note]  
> É recomendável baixar e instalar ou atualizar para o módulo mais recente do PowerShell do Azure.

1. **Crie um contexto para a conta de armazenamento e a chave** O contexto encapsula o nome da conta de armazenamento e a chave da conta. Para obter instruções sobre como copiar a chave da conta no [portal do Azure](https://portal.azure.com/), veja [Exibir e copiar chaves de acesso de armazenamento](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#view-and-copy-storage-access-keys).

    ```powershell
    $storageContext = New-AzureStorageContext <storage-account-name> <storage-account-key>
    ```
    
2. **Criar um novo compartilhamento de arquivos**:    
    
    ```powershell
    $share = New-AzureStorageShare logs -Context $storageContext
    ```

> [!Note]  
> O nome do seu compartilhamento de arquivo deve estar em minúsculas. Para obter detalhes completos sobre como nomear arquivos e compartilhamentos de arquivos, confira [Nomenclatura e referência de compartilhamentos, diretórios, arquivos e metadados](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="create-file-share-through-command-line-interface-cli"></a>Criar um compartilhamento de arquivos com a Interface da Linha de Comando (CLI)
1. **Para se preparar para usar uma Interface da Linha de Comando (CLI), baixe e instale a CLI do Azure.**  
    Consulte [Instalar a CLI do Azure 2.0](/cli/azure/install-az-cli2.md) e [Introdução à CLI do Azure 2.0](/cli/azure/get-started-with-azure-cli.md).

2. **Crie uma cadeia de conexão para a conta de armazenamento na qual você deseja criar o compartilhamento.**  
    Substitua ```<storage-account>``` e ```<resource_group>``` pelo nome da conta de armazenamento e o grupo de recursos no exemplo a seguir:

   ```azurecli
    current_env_conn_string = $(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

3. **Criar um compartilhamento de arquivos**
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string 1 > /dev/null
    ```

## <a name="next-steps"></a>Próximas etapas
* [Conecte e Monte o Compartilhamento de Arquivos - Windows](storage-how-to-use-files-windows.md)
* [Conecte e Monte o Compartilhamento de Arquivos - Linux](../storage-how-to-use-files-linux.md)
* [Conecte e Monte o Compartilhamento de Arquivos - macOS](storage-how-to-use-files-mac.md)

Veja estes links para obter mais informações sobre o Arquivos do Azure.

* [Perguntas frequentes](../storage-files-faq.md)
* [Solução de problemas no Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Solução de problemas no Linux](storage-troubleshoot-linux-file-connection-problems.md)   