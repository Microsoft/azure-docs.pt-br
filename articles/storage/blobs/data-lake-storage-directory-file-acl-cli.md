---
title: Usar CLI do Azure para gerenciar dados (Azure Data Lake Storage Gen2)
description: Use o CLI do Azure para gerenciar diretórios e arquivos em contas de armazenamento que têm um namespace hierárquico.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3e9afd4617eb7445ba83948d46eef0890832e2be
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100650347"
---
# <a name="use-azure-cli-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Use CLI do Azure para gerenciar diretórios e arquivos no Azure Data Lake Storage Gen2

Este artigo mostra como usar a [CLI (interface de Command-Line do Azure)](/cli/azure/) para criar e gerenciar diretórios e arquivos em contas de armazenamento que têm um namespace hierárquico.

Para saber mais sobre como obter, definir e atualizar as listas de controle de acesso (ACL) de diretórios e arquivos, consulte [usar CLI do Azure para gerenciar ACLs no Azure data Lake Storage Gen2](data-lake-storage-acl-cli.md).

[Exemplos](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)  |  [Enviar comentários](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

- Uma conta de armazenamento que tem o namespace hierárquico habilitado. Siga [estas](create-data-lake-storage-account.md) instruções para criar um.

- CLI do Azure versão `2.6.0` ou superior

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>Certifique-se de ter a versão correta da CLI do Azure instalada

1. Abra o [Azure Cloud Shell](../../cloud-shell/overview.md) ou, se você [instalou](/cli/azure/install-azure-cli) a CLI do Azure localmente, abra um aplicativo de console de comando, como Windows PowerShell.

2. Verifique se a versão da CLI do Azure instalada é `2.6.0` ou superior usando o comando a seguir.

   ```azurecli
    az --version
   ```

   Se sua versão da CLI do Azure for inferior a `2.6.0`, instale uma versão posterior. Confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="connect-to-the-account"></a>Conectar à conta

1. Se você estiver usando a CLI do Azure localmente, execute o comando de logon.

   ```azurecli
   az login
   ```

   Se a CLI puder abrir o navegador padrão, ela o fará e carregará uma página de entrada do Azure.

   Caso contrário, abra uma página de navegador em [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e insira o código de autorização exibido no terminal. Em seguida, entre com suas credenciais de conta no navegador.

   Para saber mais sobre os diferentes métodos de autenticação, confira [Autorizar acesso a dados de BLOB ou fila com a CLI do Azure](./authorize-data-operations-cli.md).

2. Se sua identidade estiver associada a mais de uma assinatura, defina sua assinatura ativa para a assinatura da conta de armazenamento que hospedará seu site estático.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Substitua o valor de espaço reservado `<subscription-id>` pela ID da sua assinatura.

> [!NOTE]
> O exemplo apresentado neste artigo mostra a autorização do Azure Active Directory (Azure AD). Para saber mais sobre os métodos de autorização, confira [Autorizar o acesso a dados de blob ou fila com a CLI do Azure](./authorize-data-operations-cli.md).

## <a name="create-a-container"></a>Criar um contêiner

Um contêiner atua como um sistema de arquivos para seus arquivos. Você pode criar um usando o comando `az storage fs create`. 

Este exemplo cria um contêiner chamado `my-file-system` .

```azurecli
az storage fs create -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-container-properties"></a>Mostrar propriedades do contêiner

Você pode imprimir as propriedades de um contêiner no console usando o `az storage fs show` comando.

```azurecli
az storage fs show -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="list-container-contents"></a>Listar conteúdo do contêiner

Liste o conteúdo de um diretório usando o comando `az storage fs file list`.

Este exemplo lista o conteúdo de um contêiner chamado `my-file-system` .

```azurecli
az storage fs file list -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-container"></a>Excluir um contêiner

Exclua um contêiner usando o `az storage fs delete` comando.

Este exemplo exclui um contêiner chamado `my-file-system` . 

```azurecli
az storage fs delete -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="create-a-directory"></a>Criar um diretório

Crie uma referência de diretório usando o comando `az storage fs directory create`. 

Este exemplo adiciona um diretório chamado `my-directory` a um contêiner chamado `my-file-system` que está localizado em uma conta chamada `mystorageaccount` .

```azurecli
az storage fs directory create -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-directory-properties"></a>Obter propriedades do diretório

Você pode imprimir as propriedades de um diretório no console do usando o comando `az storage fs directory show`.

```azurecli
az storage fs directory show -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-directory"></a>Renomear ou mover um diretório

Renomeie ou mova um diretório usando o comando `az storage fs directory move`.

Este exemplo renomeia um diretório do nome `my-directory` para o nome `my-new-directory` no mesmo contêiner.

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

Este exemplo move um diretório para um contêiner chamado `my-second-file-system` .

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-second-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-directory"></a>Excluir um diretório

Exclui um diretório usando o comando `az storage fs directory delete`.

Este exemplo exclui um diretório chamado `my-directory`. 

```azurecli
az storage fs directory delete -n my-directory -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="check-if-a-directory-exists"></a>Verificar se há um diretório

Determine se um diretório específico existe no contêiner usando o `az storage fs directory exists` comando.

Este exemplo revela se um diretório chamado `my-directory` existe no `my-file-system` contêiner. 

```azurecli
az storage fs directory exists -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login 
```

## <a name="download-from-a-directory"></a>Baixar de um diretório

Baixe um arquivo de um diretório usando o comando `az storage fs file download`.

Este exemplo baixa um arquivo chamado `upload.txt` de um diretório chamado `my-directory`. 

```azurecli
az storage fs file download -p my-directory/upload.txt -f my-file-system -d "C:\myFolder\download.txt" --account-name mystorageaccount --auth-mode login
```

## <a name="list-directory-contents"></a>Listar conteúdo do diretório

Liste o conteúdo de um diretório usando o comando `az storage fs file list`.

Este exemplo lista o conteúdo de um diretório chamado `my-directory` que está localizado no `my-file-system` contêiner de uma conta de armazenamento denominada `mystorageaccount` . 

```azurecli
az storage fs file list -f my-file-system --path my-directory --account-name mystorageaccount --auth-mode login
```

## <a name="upload-a-file-to-a-directory"></a>Carregar um arquivo em um diretório

Carregue um arquivo em um diretório usando o comando `az storage fs directory upload`.

Este exemplo carrega um arquivo chamado `upload.txt` em um diretório chamado `my-directory`. 

```azurecli
az storage fs file upload -s "C:\myFolder\upload.txt" -p my-directory/upload.txt  -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-properties"></a>Mostrar propriedades do arquivo

Você pode imprimir as propriedades de um arquivo no console do usando o comando `az storage fs file show`.

```azurecli
az storage fs file show -p my-file.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-file"></a>Renomear ou mover um arquivo

Renomeie ou mova um arquivo usando o comando `az storage fs file move`.

Este exemplo renomeia um arquivo do nome `my-file.txt` para o nome `my-file-renamed.txt`.

```azurecli
az storage fs file move -p my-file.txt -f my-file-system --new-path my-file-system/my-file-renamed.txt --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file"></a>Excluir um arquivo

Exclua um arquivo usando o comando `az storage fs file delete`.

Este exemplo exclui um arquivo chamado `my-file.txt`

```azurecli
az storage fs file delete -p my-directory/my-file.txt -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="see-also"></a>Veja também

- [Amostras](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
- [Fornecer feedback](https://github.com/Azure/azure-cli-extensions/issues)
- [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Usar CLI do Azure para gerenciar ACLs no Azure Data Lake Storage Gen2](data-lake-storage-acl-cli.md)