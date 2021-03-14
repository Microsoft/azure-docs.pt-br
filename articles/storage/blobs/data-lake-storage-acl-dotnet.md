---
title: Use o .NET para definir ACLs no Azure Data Lake Storage Gen2
description: Use o .NET para gerenciar listas de controle de acesso (ACL) em contas de armazenamento que têm o namespace hierárquico (HNS) habilitado.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 626e89d8d758d5fe31ef6c913076a9154274bb61
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653964"
---
# <a name="use-net-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Usar o .NET para gerenciar ACLs no Azure Data Lake Storage Gen2

Este artigo mostra como usar o .NET para obter, definir e atualizar as listas de controle de acesso de diretórios e arquivos.

A herança de ACL já está disponível para novos itens filho que são criados em um diretório pai. Mas você também pode adicionar, atualizar e remover ACLs recursivamente nos itens filho existentes de um diretório pai sem precisar fazer essas alterações individualmente para cada item filho.

[Pacote (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)  |  [Exemplos](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)  |  Exemplo de ACL [recursiva](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)  |  [Referência](/dotnet/api/azure.storage.files.datalake)  |  de API Mapeamento de Gen1 [para Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)  |  [Enviar comentários](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

- Uma conta de armazenamento precisa ter o HNS (namespace hierárquico) habilitado. Siga [estas](create-data-lake-storage-account.md) instruções para criar um.

- CLI do Azure versão `2.6.0` ou superior

- Uma das seguintes permissões de segurança:

  - Uma [entidade de segurança](../../role-based-access-control/overview.md#security-principal) de Azure Active Directory (AD) provisionada à qual foi atribuída a função de proprietário de dados do [blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) no escopo do contêiner de destino, grupo de recursos pai ou assinatura.  

  - Usuário proprietário do contêiner ou diretório de destino ao qual você planeja aplicar as configurações de ACL. Para definir ACLs recursivamente, isso inclui todos os itens filho no contêiner ou diretório de destino.
  
  - Chave da conta de armazenamento.

## <a name="set-up-your-project"></a>Configurar o seu projeto

Para começar, instale o pacote NuGet [do Azure. Storage. files. datalake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) .

1. Abra uma janela de comando (por exemplo: Windows PowerShell).

2. No diretório do projeto, instale o pacote de visualização do Azure. Storage. files. datalake usando o `dotnet add package` comando.

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.6.0 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

   Em seguida, adicione essas instruções using à parte superior do seu arquivo de código.

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   ```

## <a name="connect-to-the-account"></a>Conectar à conta

Para usar os trechos de código neste artigo, você precisará criar uma instância de [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) que representa a conta de armazenamento. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Conectar usando o Azure Active Directory (AD)

> [!NOTE]
> Se você estiver usando Azure Active Directory (Azure AD) para autorizar o acesso, verifique se a entidade de segurança recebeu a [função de proprietário de dados do blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Para saber mais sobre como as permissões de ACL são aplicadas e os efeitos de alterá-las, consulte  [modelo de controle de acesso em Azure data Lake Storage Gen2](./data-lake-storage-access-control-model.md).

Você pode usar a [biblioteca de cliente de identidade do Azure para .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) para autenticar seu aplicativo com o Azure AD.

Depois de instalar o pacote, adicione esta instrução using à parte superior do seu arquivo de código.

```csharp
using Azure.Identity;
```

Obtenha uma ID do cliente, um segredo do cliente e uma ID de locatário. Para fazer isso, consulte [adquirir um token do Azure ad para autorizar solicitações de um aplicativo cliente](../common/storage-auth-aad-app.md). Como parte desse processo, você precisará atribuir uma das seguintes funções do Azure [RBAC (controle de acesso baseado em função](../../role-based-access-control/overview.md) do Azure) à sua entidade de segurança. 

|Função|Funcionalidade de configuração de ACL|
|--|--|
|[Proprietário de Dados do Blob de Armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Todos os diretórios e arquivos na conta.|
|[Colaborador de dados de blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Somente diretórios e arquivos de propriedade da entidade de segurança.|

Este exemplo cria uma instância de [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) usando uma ID do cliente, um segredo do cliente e uma ID de locatário.  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Para obter mais exemplos, consulte a documentação da [biblioteca de clientes do Azure Identity para .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) .

### <a name="connect-by-using-an-account-key"></a>Conectar-se usando uma chave de conta

Essa é a maneira mais fácil de se conectar a uma conta.

Este exemplo cria uma instância de [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) usando uma chave de conta.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithKey":::

## <a name="set-acls"></a>Definir ACLs

Ao *definir* uma ACL, você **substitui** toda a ACL, incluindo todas as entradas. Se você quiser alterar o nível de permissão de uma entidade de segurança ou adicionar uma nova entidade de segurança à ACL sem afetar outras entradas existentes, deverá *Atualizar* a ACL em vez disso. Para atualizar uma ACL em vez de substituí-la, consulte a seção [Update ACLs](#update-acls) deste artigo.  

Se você optar por *definir* a ACL, deverá adicionar uma entrada para o usuário proprietário, uma entrada para o grupo proprietário e uma entrada para todos os outros usuários. Para saber mais sobre o usuário proprietário, o grupo proprietário e todos os outros usuários, consulte [usuários e identidades](data-lake-storage-access-control.md#users-and-identities).

Esta seção mostra como:

- Definir a ACL de um diretório
- Definir a ACL de um arquivo
- Definir ACLs recursivamente

### <a name="set-the-acl-of-a-directory"></a>Definir a ACL de um diretório

Obtenha a ACL (lista de controle de acesso) de um diretório chamando o método [DataLakeDirectoryClient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) e defina a ACL chamando o método [DataLakeDirectoryClient. setaccesscontrollist](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) .

Este exemplo obtém e define a ACL de um diretório chamado `my-directory` . A cadeia de caracteres `user::rwx,group::r-x,other::rw-` fornece as permissões de leitura, gravação e execução do usuário proprietário, fornece ao grupo proprietário somente permissões de leitura e execução e concede a todos os outros permissões de leitura e gravação.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ACLDirectory":::

Você também pode obter e definir a ACL do diretório raiz de um contêiner. Para obter o diretório raiz, passe uma cadeia de caracteres vazia ( `""` ) para o método [DataLakeFileSystemClient. GetDirectoryClient](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getdirectoryclient) .

### <a name="set-the-acl-of-a-file"></a>Definir a ACL de um arquivo

Obtenha a ACL (lista de controle de acesso) de um arquivo chamando o método [DataLakeFileClient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) e defina a ACL chamando o método [DataLakeFileClient. setaccesscontrollist](/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist) .

Este exemplo obtém e define a ACL de um arquivo chamado `my-file.txt` . A cadeia de caracteres `user::rwx,group::r-x,other::rw-` fornece as permissões de leitura, gravação e execução do usuário proprietário, fornece ao grupo proprietário somente permissões de leitura e execução e concede a todos os outros permissões de leitura e gravação.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_FileACL":::

### <a name="set-acls-recursively"></a>Definir ACLs recursivamente

Defina ACLs recursivamente chamando o método **DataLakeDirectoryClient. SetAccessControlRecursiveAsync** . Passe este método uma [lista](/dotnet/api/system.collections.generic.list-1) de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Cada [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) define uma entrada de ACL.

Se você quiser definir uma entrada ACL **padrão** , poderá definir a propriedade [PathAccessControlItem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) como **true**. 

Este exemplo define a ACL de um diretório chamado `my-parent-directory` . Esse método aceita um parâmetro booliano chamado `isDefaultScope` que especifica se a ACL padrão deve ser definida. Esse parâmetro é usado no construtor do [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). As entradas da ACL fornecem ao usuário proprietário permissões de leitura, gravação e execução, fornece ao grupo proprietário somente permissões de leitura e execução e concede a todos os outros sem acesso. A última entrada ACL neste exemplo fornece um usuário específico com a ID de objeto "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "permissões de leitura e execução.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_SetACLRecursively":::

Para ver um exemplo que define ACLs recursivamente em lotes especificando um tamanho de lote, consulte o [exemplo](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).net.

## <a name="update-acls"></a>Atualizar ACLs

Ao *Atualizar* uma ACL, você modifica a ACL em vez de substituir a ACL. Por exemplo, você pode adicionar uma nova entidade de segurança à ACL sem afetar outras entidades de segurança listadas na ACL.  Para substituir a ACL em vez de atualizá-la, consulte a seção [set ACLs](#set-acls) deste artigo.

Esta seção mostra como:

- Atualizar uma ACL
- Atualizar ACLs recursivamente

### <a name="update-an-acl"></a>Atualizar uma ACL

Primeiro, obtenha a ACL de um diretório chamando o método [DataLakeDirectoryClient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) . Copie a lista de entradas de ACL para uma nova [lista](/dotnet/api/system.collections.generic.list-1) de objetos [PathAccessControl](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrol) . Em seguida, localize a entrada que você deseja atualizar e a substitua na lista. Defina a ACL chamando o método [DataLakeDirectoryClient. Setaccesscontrollist](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) .

Este exemplo atualiza a ACL raiz de um contêiner substituindo a entrada de ACL para todos os outros usuários. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_UpdateACL":::

### <a name="update-acls-recursively"></a>Atualizar ACLs recursivamente

Para atualizar uma ACL recursivamente, crie um novo objeto ACL com a entrada ACL que você deseja atualizar e, em seguida, use esse objeto em atualizar a operação de ACL. Não obtenha a ACL existente, basta fornecer as entradas de ACL a serem atualizadas.

Atualize uma ACL recursivamente chamando o método **DataLakeDirectoryClient. UpdateAccessControlRecursiveAsync** .  Passe este método uma [lista](/dotnet/api/system.collections.generic.list-1) de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Cada [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) define uma entrada de ACL.

Se você quiser atualizar uma entrada ACL **padrão** , poderá definir a propriedade [PathAccessControlItem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) como **true**.

Este exemplo atualiza uma entrada ACL com permissão de gravação. Esse método aceita um parâmetro booliano chamado `isDefaultScope` que especifica se a ACL padrão deve ser atualizada. Esse parâmetro é usado no construtor do [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_UpdateACLsRecursively":::

Para ver um exemplo que atualiza as ACLs recursivamente em lotes especificando um tamanho de lote, consulte o [exemplo](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).net.

## <a name="remove-acl-entries"></a>Remover entradas de ACL

Você pode remover uma ou mais entradas de ACL. Esta seção mostra como:

- Remover uma entrada de ACL
- Remover entradas de ACL recursivamente

### <a name="remove-an-acl-entry"></a>Remover uma entrada de ACL

Primeiro, obtenha a ACL de um diretório chamando o método [DataLakeDirectoryClient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) . Copie a lista de entradas de ACL para uma nova [lista](/dotnet/api/system.collections.generic.list-1) de objetos [PathAccessControl](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrol) . Em seguida, localize a entrada que você deseja remover e chame o método [Remove](/dotnet/api/system.collections.ilist.remove) da coleção. Defina a ACL atualizada chamando o método [DataLakeDirectoryClient. Setaccesscontrollist](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) .

Este exemplo atualiza a ACL raiz de um contêiner substituindo a entrada de ACL para todos os outros usuários. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_RemoveACLEntry":::

### <a name="remove-acl-entries-recursively"></a>Remover entradas de ACL recursivamente

Para remover as entradas de ACL recursivamente, crie um novo objeto ACL para a entrada de ACL a ser removida e, em seguida, use esse objeto em remover a operação de ACL. Não obtenha a ACL existente, basta fornecer as entradas de ACL a serem removidas. 

Remova as entradas de ACL chamando o método **DataLakeDirectoryClient. RemoveAccessControlRecursiveAsync** . Passe este método uma [lista](/dotnet/api/system.collections.generic.list-1) de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Cada [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) define uma entrada de ACL. 

Se você quiser remover uma entrada ACL **padrão** , poderá definir a propriedade [PathAccessControlItem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) como **true**. 

Este exemplo remove uma entrada ACL da ACL do diretório chamado `my-parent-directory` . Esse método aceita um parâmetro booliano chamado `isDefaultScope` que especifica se a entrada deve ser removida da ACL padrão. Esse parâmetro é usado no construtor do [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_RemoveACLRecursively":::

Para ver um exemplo que remove ACLs recursivamente em lotes especificando um tamanho de lote, consulte o [exemplo](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).net.

## <a name="recover-from-failures"></a>Recuperar de falhas

Você pode encontrar erros de tempo de execução ou de permissão ao modificar ACLs recursivamente. Para erros de tempo de execução, reinicie o processo desde o início. Poderão ocorrer erros de permissão se a entidade de segurança não tiver permissão suficiente para modificar a ACL de um diretório ou arquivo que está na hierarquia de diretório que está sendo modificada. Resolva o problema de permissão e, em seguida, escolha retomar o processo a partir do ponto de falha usando um token de continuação ou reiniciar o processo do início. Você não precisará usar o token de continuação se preferir reiniciar desde o início. Você pode reaplicar entradas de ACL sem nenhum impacto negativo.

Este exemplo retorna um token de continuação no caso de uma falha. O aplicativo pode chamar esse método de exemplo novamente depois que o erro for resolvido e passar o token de continuação. Se esse método de exemplo for chamado pela primeira vez, o aplicativo poderá passar um valor de `null` para o parâmetro de token de continuação. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ResumeContinuationToken":::

Para ver um exemplo que define ACLs recursivamente em lotes especificando um tamanho de lote, consulte o [exemplo](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).net.

Se você quiser que o processo seja concluído sem interrupções por erros de permissão, poderá especificar isso.

Para garantir que o processo seja concluído sem interrupções, passe um objeto **AccessControlChangedOptions** e defina a propriedade **ContinueOnFailure** desse objeto como ``true`` .

Este exemplo define as entradas de ACL recursivamente. Se esse código encontrar um erro de permissão, ele registrará essa falha e continuará a execução. Este exemplo imprime o número de falhas no console.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ContinueOnFailure":::

Para ver um exemplo que define ACLs recursivamente em lotes especificando um tamanho de lote, consulte o [exemplo](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).net.

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Confira também

- [Documentação de referência da API](/dotnet/api/azure.storage.files.datalake)
- [Pacote (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
- [Amostras](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
- [Mapeamento de Gen1 para Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
- [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Enviar comentários](https://github.com/Azure/azure-sdk-for-net/issues)
- [Modelo de controle de acesso no Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [ACLs (listas de controle de acesso) no Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)