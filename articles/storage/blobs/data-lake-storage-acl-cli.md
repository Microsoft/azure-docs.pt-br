---
title: Use CLI do Azure para definir ACLs em Azure Data Lake Storage Gen2
description: Use o CLI do Azure para gerenciar listas de controle de acesso (ACL) em contas de armazenamento que têm um namespace hierárquico.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9814dc06e7e570a923ba3ea5b3b0df7ade99bb28
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653966"
---
# <a name="use-azure-cli-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Usar CLI do Azure para gerenciar ACLs no Azure Data Lake Storage Gen2

Este artigo mostra como usar a [CLI (interface de Command-Line do Azure)](/cli/azure/) para obter, definir e atualizar as listas de controle de acesso de diretórios e arquivos.

A herança de ACL já está disponível para novos itens filho que são criados em um diretório pai. Mas você também pode adicionar, atualizar e remover ACLs recursivamente nos itens filho existentes de um diretório pai sem precisar fazer essas alterações individualmente para cada item filho.

[Referência](/cli/azure/storage/fs/access)  |  do [Exemplos](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)  |  [Enviar comentários](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

- Uma conta de armazenamento que tem o namespace hierárquico habilitado. Siga [estas](create-data-lake-storage-account.md) instruções para criar um.

- CLI do Azure versão `2.6.0` ou superior

- Uma das seguintes permissões de segurança:

  - Uma [entidade de segurança](../../role-based-access-control/overview.md#security-principal) do Azure Active Directory (Azure AD) provisionada à qual foi atribuída a função de proprietário de dados do [blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) no escopo do contêiner de destino, grupo de recursos pai ou assinatura.  

  - Usuário proprietário do contêiner ou diretório de destino ao qual você planeja aplicar as configurações de ACL. Para definir ACLs recursivamente, isso inclui todos os itens filho no contêiner ou diretório de destino.
  
  - Chave da conta de armazenamento.

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>Certifique-se de ter a versão correta da CLI do Azure instalada

1. Abra o [Azure Cloud Shell](../../cloud-shell/overview.md) ou, se você [instalou](/cli/azure/install-azure-cli) a CLI do Azure localmente, abra um aplicativo de console de comando, como Windows PowerShell.

2. Verifique se a versão da CLI do Azure instalada é `2.14.0` ou superior usando o comando a seguir.

   ```azurecli
    az --version
   ```

   Se sua versão da CLI do Azure for inferior a `2.14.0`, instale uma versão posterior. Confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

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

## <a name="get-acls"></a>Obter ACLs

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

## <a name="set-acls"></a>Definir ACLs

Ao *definir* uma ACL, você **substitui** toda a ACL, incluindo todas as entradas. Se você quiser alterar o nível de permissão de uma entidade de segurança ou adicionar uma nova entidade de segurança à ACL sem afetar outras entradas existentes, deverá *Atualizar* a ACL em vez disso. Para atualizar uma ACL em vez de substituí-la, consulte a seção [Update ACLs](#update-acls) deste artigo.  

Se você optar por *definir* a ACL, deverá adicionar uma entrada para o usuário proprietário, uma entrada para o grupo proprietário e uma entrada para todos os outros usuários. Para saber mais sobre o usuário proprietário, o grupo proprietário e todos os outros usuários, consulte [usuários e identidades](data-lake-storage-access-control.md#users-and-identities).

Esta seção mostra como:

- Definir uma ACL
- Definir ACLs recursivamente

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

### <a name="set-acls-recursively"></a>Definir ACLs recursivamente

Defina ACLs recursivamente usando o comando [AZ Storage FS Access Set-recursive](/cli/azure/storage/fs/access#az_storage_fs_access_set_recursive) .

Este exemplo define a ACL de um diretório chamado `my-parent-directory` . Essas entradas fornecem ao usuário proprietário permissões de leitura, gravação e execução, fornece ao grupo proprietário somente permissões de leitura e execução e concede a todos os outros sem acesso. A última entrada ACL neste exemplo fornece a um usuário específico as permissões de leitura e execução da ID de objeto "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx".

```azurecli
az storage fs access set-recursive --acl "user::rwx,group::r-x,other::---,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Se você quiser definir uma entrada ACL **padrão** , adicione o prefixo `default:` a cada entrada. Por exemplo, `default:user::rwx` ou `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`.

## <a name="update-acls"></a>Atualizar ACLs

Ao *Atualizar* uma ACL, você modifica a ACL em vez de substituir a ACL. Por exemplo, você pode adicionar uma nova entidade de segurança à ACL sem afetar outras entidades de segurança listadas na ACL.  Para substituir a ACL em vez de atualizá-la, consulte a seção [set ACLs](#set-acls) deste artigo.

Para atualizar uma ACL, crie um novo objeto ACL com a entrada ACL que você deseja atualizar e, em seguida, use esse objeto em atualizar a operação de ACL. Não obtenha a ACL existente, basta fornecer as entradas de ACL a serem atualizadas.

Esta seção mostra como:

- Atualizar uma ACL
- Atualizar ACLs recursivamente

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

### <a name="update-acls-recursively"></a>Atualizar ACLs recursivamente

Atualize as ACLs recursivamente usando o comando  [AZ Storage FS Access Update-recursive](/cli/azure/storage/fs/access#az_storage_fs_access_update_recursive) .

Este exemplo atualiza uma entrada ACL com permissão de gravação.

```azurecli
az storage fs access update-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Se você quiser atualizar uma entrada ACL **padrão** , adicione o prefixo `default:` a cada entrada. Por exemplo, `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`.

## <a name="remove-acl-entries-recursively"></a>Remover entradas de ACL recursivamente

Você pode remover uma ou mais entradas de ACL recursivamente. Para remover uma entrada de ACL, crie um novo objeto ACL para a entrada de ACL a ser removida e use esse objeto em remover a operação de ACL. Não obtenha a ACL existente, basta fornecer as entradas de ACL a serem removidas.

Remova as entradas de ACL usando o comando [AZ Storage FS Access remove-recursive](/cli/azure/storage/fs/access#az_storage_fs_access_remove_recursive) .

Este exemplo remove uma entrada ACL do diretório raiz do contêiner.  

```azurecli
az storage fs access remove-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Se você quiser remover uma entrada ACL **padrão** , adicione o prefixo `default:` a cada entrada. Por exemplo, `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

## <a name="recover-from-failures"></a>Recuperar de falhas

Você pode encontrar erros de tempo de execução ou de permissão ao modificar ACLs recursivamente. Para erros de tempo de execução, reinicie o processo desde o início. Poderão ocorrer erros de permissão se a entidade de segurança não tiver permissão suficiente para modificar a ACL de um diretório ou arquivo que está na hierarquia de diretório que está sendo modificada. Resolva o problema de permissão e, em seguida, escolha retomar o processo a partir do ponto de falha usando um token de continuação ou reiniciar o processo do início. Você não precisará usar o token de continuação se preferir reiniciar desde o início. Você pode reaplicar entradas de ACL sem nenhum impacto negativo.

Em caso de falha, você pode retornar um token de continuação definindo o `--continue-on-failure` parâmetro como `false` . Depois de resolver os erros, você pode retomar o processo do ponto de falha executando o comando novamente e, em seguida, definindo o `--continuation` parâmetro para o token de continuação.

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure false --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

Se você quiser que o processo seja concluído sem interrupções por erros de permissão, poderá especificar isso.

Para garantir que o processo seja concluído sem interrupções, defina o `--continue-on-failure` parâmetro como `true` .

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure true --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Confira também

- [Amostras](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
- [Fornecer feedback](https://github.com/Azure/azure-cli-extensions/issues)
- [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Modelo de controle de acesso no Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [ACLs (listas de controle de acesso) no Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)