---
title: Configurar o armazenamento usando os arquivos do Azure
description: Como configurar e se conectar a arquivos do Azure no contêiner do Windows no serviço de aplicativo.
author: msangapu-msft
manager: gwallace
ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
ms.openlocfilehash: 2c12bf45c033fea185d976f1e9d644183407b5ac
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297219"
---
# <a name="configure-azure-files-in-a-windows-container-on-app-service"></a>Configurar arquivos do Azure em um contêiner do Windows no serviço de aplicativo

> [!NOTE]
> Este artigo se aplica a contêineres personalizados do Windows. Para implantar o serviço de aplicativo no _Linux_, consulte [fornecer conteúdo do armazenamento do Azure](./containers/how-to-serve-content-from-azure-storage.md).
>

Este guia mostra como acessar o armazenamento do Azure em contêineres do Windows. Somente compartilhamentos de [arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli) e compartilhamentos de [arquivos Premium](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-premium-fileshare) têm suporte. Você usa compartilhamentos de arquivos do Azure nestas instruções. Os benefícios incluem conteúdo seguro, portabilidade de conteúdo, acesso a vários aplicativos e vários métodos de transferência.

## <a name="prerequisites"></a>Pré-requisitos

- [CLI do Azure](/cli/azure/install-azure-cli) (2.0.46 ou posterior).
- [Um aplicativo de contêiner do Windows existente no serviço Azure App](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-windows-container)
- [Criar compartilhamento de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-cli)
- [Carregar arquivos no compartilhamento de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-deployment-guide)

> [!NOTE]
> Os arquivos do Azure são armazenamento não padrão e cobrados separadamente, não incluídos no aplicativo Web. Ele não dá suporte ao uso da configuração de firewall devido a limitações de infraestrutura.
>

## <a name="link-storage-to-your-web-app-preview"></a>Armazenamento de link para seu aplicativo Web (versão prévia)

 Para montar um compartilhamento de arquivos do Azure em um diretório em seu aplicativo do serviço de aplicativo [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) , use o comando. O tipo de armazenamento deve ser AzureFiles.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureFiles --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory of form c:<directory name> >
```

Você deve fazer isso para todos os outros diretórios que você deseja que sejam vinculados a um compartilhamento de arquivos do Azure.

## <a name="verify"></a>Verificar

Depois que um compartilhamento de arquivos do Azure estiver vinculado a um aplicativo Web, você poderá verificar isso executando o seguinte comando:

```azurecli
az webapp config storage-account list --resource-group <resource_group> --name <app_name>
```


## <a name="next-steps"></a>Próximas etapas

- [Migre um aplicativo ASP.net para Azure app serviço usando um contêiner do Windows (versão prévia)](app-service-web-tutorial-windows-containers-custom-fonts.md).
