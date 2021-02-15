---
title: Usar a CLI do Azure para arquivos e ACLs no Azure Data Lake Storage Gen2
description: Use a CLI do Azure para gerenciar diretórios e listas de controle de acesso (ACL) e arquivos nas contas de armazenamento que têm um namespace hierárquico.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 05/18/2020
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: 42359eb8a2bfdad23589e0302b80e7806b388510
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95913599"
---
# <a name="use-azure-cli-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Usar a CLI do Azure para gerenciar diretórios, arquivos e ACLs no Azure Data Lake Storage Gen2

Este artigo mostra como usar a [CLI (Interface de Linha de Comando) do Azure](/cli/azure/) para criar e gerenciar diretórios, arquivos e permissões em contas de armazenamento que têm um namespace hierárquico. 

[Exemplos](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)  |  [Enviar comentários](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Uma conta de armazenamento precisa ter o HNS (namespace hierárquico) habilitado. Siga [estas](../common/storage-account-create.md) instruções para criar um.
> * CLI do Azure versão `2.6.0` ou superior

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
> O exemplo apresentado neste artigo mostra a autorização do Azure Active Directory (AD). Para saber mais sobre os métodos de autorização, confira [Autorizar o acesso a dados de blob ou fila com a CLI do Azure](./authorize-data-operations-cli.md).

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

## <a name="manage-access-control-lists-acls"></a>Gerenciar listas de controle de acesso (ACLs)

Você pode obter, definir e atualizar as permissões de acesso de diretórios e arquivos.

> [!NOTE]
> Se você estiver usando o Azure AD (Azure Active Directory) para autorizar comandos, confira se a entidade de segurança foi atribuída com a [função de Proprietário dos dados do blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Para saber mais sobre como as permissões de ACL são aplicadas e os efeitos por alterá-las, confira [Controle de acesso no Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

### <a name="get-an-acl"></a>Obter uma ACL

Obtenha a ACL de um **diretório** usando o comando `az storage fs access show`.

Este exemplo obtém a ACL de um diretório e, em seguida, imprime a ACL no console.

```azurecli
az storage fs access show -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Obtenha as permissões de acesso de um **arquivo**  usando o comando `az storage fs access show`. 

Este exemplo obtém a ACL de um arquivo e, em seguida, imprime a ACL no console.

```azurecli
az storage fs access show -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

A imagem a seguir mostra a saída depois de obter a ACL de um diretório.

![Obter saída de ACL](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

Neste exemplo, o usuário proprietário tem permissões de leitura, gravação e execução. O grupo proprietário tem permissões de somente leitura e execução. Para obter mais informações sobre as listas de controle de acesso, confira [Controle de acesso no Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-an-acl"></a>Definir uma ACL

Use o comando `az storage fs access set` para definir a ACL de um **diretório**. 

Este exemplo define a ACL em um diretório para o usuário proprietário, o grupo proprietário ou outros usuários e imprime a ACL no console.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Este exemplo define a ACL *padrão* em um diretório para o usuário proprietário, o grupo proprietário ou outros usuários e imprime a ACL no console.

```azurecli
az storage fs access set --acl "default:user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Use o comando `az storage fs access set` para definir a ACL de um **arquivo**. 

Este exemplo define a ACL em um arquivo para o usuário proprietário, o grupo proprietário ou outros usuários e imprime a ACL no console.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

A imagem a seguir mostra a saída depois de definir a ACL de um arquivo.

![Obter saída de ACL 2](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

Neste exemplo, o usuário proprietário e o grupo proprietário têm permissões de somente leitura e gravação. Todos os outros usuários têm permissões de gravação e execução. Para obter mais informações sobre as listas de controle de acesso, confira [Controle de acesso no Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="update-an-acl"></a>Atualizar uma ACL

Outra maneira de definir esta permissão é usar o comando `az storage fs access set`. 

Atualize a ACL de um diretório ou arquivo definindo o parâmetro `-permissions` como a forma abreviada de uma ACL.

Este exemplo atualiza a ACL de um **diretório**.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Este exemplo atualiza a ACL de um **arquivo**.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

Você também pode atualizar o usuário proprietário e o grupo de um diretório ou arquivo definindo os parâmetros `--owner` ou `group` para a ID da entidade ou o UPN (Nome Principal do Usuário) de um usuário. 

Este exemplo altera o proprietário de um diretório. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Este exemplo altera o proprietário de um arquivo. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login

```

### <a name="set-an-acl-recursively"></a>Definir uma ACL recursivamente

Você pode adicionar, atualizar e remover ACLs recursivamente nos itens filho existentes de um diretório pai sem precisar fazer essas alterações individualmente para cada item filho. Para obter mais informações, consulte [definir listas de controle de acesso (ACLs) recursivamente para Azure data Lake Storage Gen2](recursive-access-control-lists.md).

## <a name="see-also"></a>Veja também

* [Amostras](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
* [Fornecer feedback](https://github.com/Azure/azure-cli-extensions/issues)
* [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)