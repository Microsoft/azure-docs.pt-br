---
title: Adicionar armazenamento do Azure (contêiner)
description: Saiba como anexar um compartilhamento de rede personalizado em um aplicativo em contêineres no serviço Azure App. Compartilhar arquivos entre aplicativos, gerenciar conteúdo estático remotamente e acessar localmente, etc.
author: msangapu-msft
ms.topic: article
ms.date: 7/01/2019
ms.author: msangapu
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: c3e5ca30b7f0f00b6d647f5fdaeb772900996adc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101720232"
---
# <a name="access-azure-storage-preview-as-a-network-share-from-a-container-in-app-service"></a>Acessar o armazenamento do Azure (visualização) como um compartilhamento de rede de um contêiner no serviço de aplicativo

::: zone pivot="container-windows"

Este guia mostra como anexar arquivos de armazenamento do Azure como um compartilhamento de rede a um contêiner do Windows no serviço de aplicativo. Somente [compartilhamentos de arquivos do Azure](../storage/files/storage-how-to-use-files-cli.md) e [compartilhamentos de arquivos Premium](../storage/files/storage-how-to-create-file-share.md) têm suporte. Os benefícios incluem conteúdo seguro, portabilidade de conteúdo, acesso a vários aplicativos e vários métodos de transferência.

> [!NOTE]
>O armazenamento do Azure no serviço de aplicativo está **em versão prévia** e **não tem suporte** para **cenários de produção**.

::: zone-end

::: zone pivot="container-linux"

Este guia mostra como anexar o armazenamento do Azure a um serviço de aplicativo de contêiner do Linux. Os benefícios incluem conteúdo protegido, portabilidade de conteúdo, armazenamento persistente, acesso a vários aplicativos e vários métodos de transferência.

> [!NOTE]
>O armazenamento do Azure no serviço de aplicativo está **em versão prévia** para o serviço de aplicativo no Linux e aplicativo Web para contêineres. **Não há suporte** para **cenários de produção**.

::: zone-end

## <a name="prerequisites"></a>Pré-requisitos

::: zone pivot="container-windows"

- [Um aplicativo de contêiner do Windows existente no serviço Azure App](quickstart-custom-container.md)
- [Criar compartilhamento de arquivos do Azure](../storage/files/storage-how-to-use-files-cli.md)
- [Carregar arquivos no compartilhamento de arquivos do Azure](../storage/files/storage-how-to-create-file-share.md)

::: zone-end

::: zone pivot="container-linux"

- Um [serviço de aplicativo existente no aplicativo Linux](index.yml).
- Uma [conta de armazenamento do Azure](../storage/common/storage-account-create.md?tabs=azure-cli)
- Um [compartilhamento de arquivos do Azure e um diretório](../storage/files/storage-how-to-use-files-cli.md).

::: zone-end

> [!NOTE]
> Os arquivos do Azure são armazenamento não padrão e cobrados separadamente, não incluídos no aplicativo Web. Ele não dá suporte ao uso da configuração de firewall devido a limitações de infraestrutura.
>

## <a name="limitations"></a>Limitações

::: zone pivot="container-windows"

- No momento, o armazenamento do Azure no serviço de aplicativo **não tem suporte** para trazer seus próprios cenários de código (aplicativos não-contêineres do Windows).
- O armazenamento do Azure no serviço de aplicativo **não dá suporte** ao uso da configuração de **Firewall de armazenamento** devido a limitações de infraestrutura.
- O armazenamento do Azure com o serviço de aplicativo permite especificar **até cinco** pontos de montagem por aplicativo.
- O armazenamento do Azure montado em um aplicativo não pode ser acessado por pontos de extremidade de FTP/FTPs do serviço de aplicativo. Usar o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/).

::: zone-end

::: zone pivot="container-linux"

- O armazenamento do Azure no serviço de aplicativo dá suporte à montagem de **contêineres de arquivos do Azure** (leitura/gravação) e **contêineres de blob do Azure** (somente leitura
- O armazenamento do Azure no serviço de aplicativo permite que você especifique **até cinco** pontos de montagem por aplicativo.
- O armazenamento do Azure montado em um aplicativo não pode ser acessado por pontos de extremidade de FTP/FTPs do serviço de aplicativo. Usar o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/).

::: zone-end

## <a name="link-storage-to-your-app"></a>Vincular o armazenamento ao seu aplicativo

::: zone pivot="container-windows"

Depois de criar sua [conta de armazenamento do Azure, o compartilhamento de arquivos e o diretório](#prerequisites), agora você pode configurar seu aplicativo com o armazenamento do Azure.

Para montar um compartilhamento de arquivos do Azure em um diretório em seu aplicativo do serviço de aplicativo, use o [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) comando. O tipo de armazenamento deve ser AzureFiles.

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory of form c:<directory name> >
```

Você deve fazer isso para todos os outros diretórios que você deseja que sejam vinculados a um compartilhamento de arquivos do Azure.

::: zone-end

::: zone pivot="container-linux"

Depois de criar sua [conta de armazenamento do Azure, o compartilhamento de arquivos e o diretório](#prerequisites), agora você pode configurar seu aplicativo com o armazenamento do Azure.

Para montar uma conta de armazenamento em um diretório em seu aplicativo do serviço de aplicativo, use o [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) comando. Tipo de armazenamento pode ser AzureBlob ou AzureFiles. AzureFiles é usado neste exemplo. A configuração do caminho de montagem corresponde à pasta dentro do contêiner que você deseja montar no armazenamento do Azure. Configurá-lo como '/' monta o contêiner inteiro no armazenamento do Azure.


> [!CAUTION]
> O diretório especificado como o caminho de montagem em seu aplicativo Web deve estar vazio. Qualquer conteúdo armazenado nesse diretório será excluído quando uma montagem externa for adicionada. Se você estiver migrando arquivos para um aplicativo existente, faça um backup do seu aplicativo e de seu conteúdo antes de começar.
>

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory>
```

Você deve fazer isso para qualquer outro diretório que deseja vincular a uma conta de armazenamento.

::: zone-end

## <a name="verify-linked-storage"></a>Verificar o armazenamento vinculado

Depois que o compartilhamento estiver vinculado ao aplicativo, você poderá verificar isso executando o seguinte comando:

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

## <a name="next-steps"></a>Próximas etapas

::: zone pivot="container-windows"

- [Migre o software personalizado para Azure app serviço usando um contêiner personalizado](tutorial-custom-container.md?pivots=container-windows).

::: zone-end

::: zone pivot="container-linux"

- [Configure um contêiner personalizado](configure-custom-container.md?pivots=platform-linux).

::: zone-end