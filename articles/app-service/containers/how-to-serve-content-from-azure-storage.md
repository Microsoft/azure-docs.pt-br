---
title: Fornecer conteúdo do Armazenamento do Azure no Linux – Serviço de Aplicativo
description: Como configurar e fornecer conteúdo do Armazenamento do Microsoft Azure no Serviço de Aplicativo do Azure no Linux.
author: msangapu
manager: jeconnoc
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 2/04/2019
ms.author: msangapu-msft
ms.openlocfilehash: 40aa032654d81c947e2c31e9d93954ee050f78a2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60849941"
---
# <a name="serve-content-from-azure-storage-in-app-service-on-linux"></a>Servir conteúdo do Armazenamento do Microsoft Azure no Serviço de Aplicativo no Linux

Este guia mostra como exibir conteúdo estático no Serviço de Aplicativo no Linux usando [Armazenamento do Microsoft Azure](/azure/storage/common/storage-introduction). Os benefícios incluem conteúdo seguro, portabilidade de conteúdo, acesso a vários aplicativos e vários métodos de transferência. Neste guia, você aprenderá como servir conteúdo no armazenamento do Azure por meio da configuração de armazenamento personalizado.

## <a name="prerequisites"></a>Pré-requisitos

- Um aplicativo da web existente (App Service no Linux ou Aplicativo Web para Contêineres).
- [CLI do Azure](/cli/azure/install-azure-cli) (2.0.46 ou posterior).

## <a name="create-azure-storage"></a>Criar o Armazenamento do Microsoft Azure

> [!NOTE]
> O Armazenamento do Microsoft Azure é um armazenamento não padrão e é cobrado separadamente, não incluído no aplicativo da web.
>

Criar uma [conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli).

```azurecli
#Create Storage Account
az storage account create --name <storage_account_name> --resource-group myResourceGroup

#Create Storage Container
az storage container create --name <storage_container_name> --account-name <storage_account_name>
```

## <a name="upload-files-to-azure-storage"></a>Carregar arquivos no Armazenamento do Microsoft Azure

Para fazer upload de um diretório local para a conta de armazenamento, use o comando [`az storage blob upload-batch`](https://docs.microsoft.com/cli/azure/storage/blob?view=azure-cli-latest#az-storage-blob-upload-batch) como no exemplo a seguir:

```azurecli
az storage blob upload-batch -d <full_path_to_local_directory> --account-name <storage_account_name> --account-key "<access_key>" -s <source_location_name>
```

## <a name="link-storage-to-your-web-app-preview"></a>Armazenamento de link para seu aplicativo Web (versão prévia)

> [!CAUTION]
> Vincular um diretório existente em um aplicativo da Web a uma conta de armazenamento excluirá o conteúdo do diretório. Se você estiver migrando arquivos para um aplicativo existente, faça um backup do seu aplicativo e de seu conteúdo antes de começar.
>

Para montar uma conta de armazenamento em um diretório em seu aplicativo do Serviço de Aplicativo, use o comando [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add). Tipo de armazenamento pode ser AzureBlob ou AzureFiles. Você pode usar AzureBlob para esse contêiner.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureBlob --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

Você deve fazer isso para qualquer outro diretório que deseja vincular a uma conta de armazenamento.

## <a name="verify"></a>Verificar

Depois que um contêiner de armazenamento estiver vinculado a um aplicativo da Web, você poderá verificar isso executando o seguinte comando:

```azurecli
az webapp config storage-account list --resource-group <group_name> --name <app_name>
```

## <a name="next-steps"></a>Próximas etapas

- [Configurar aplicativos web no Serviço de Aplicativo do Azure](https://docs.microsoft.com/azure/app-service/web-sites-configure).
