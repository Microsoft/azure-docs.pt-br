---
title: Definir ACLs recursivamente para Azure Data Lake Storage Gen2 | Microsoft Docs
description: Você pode adicionar, atualizar e remover listas de controle de acesso recursivamente para itens filho existentes de um diretório pai.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/05/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 737608e4f83846187decb92b090f2385a8ff4ac6
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627333"
---
# <a name="set-access-control-lists-acls-recursively-for-azure-data-lake-storage-gen2"></a>Definir listas de controle de acesso (ACLs) recursivamente para Azure Data Lake Storage Gen2

A herança de ACL já está disponível para novos itens filho que são criados em um diretório pai. Agora, você também pode adicionar, atualizar e remover ACLs recursivamente para itens filho existentes de um diretório pai sem precisar fazer essas alterações individualmente para cada item filho.

[Bibliotecas](#libraries)  |  do [Exemplos](#code-samples)  |  [Práticas recomendadas](#best-practice-guidelines)

> [!NOTE]
> Gerenciador de Armazenamento do Azure agora dá suporte à capacidade de aplicar ACLs recursivamente. Para saber mais, confira [aplicar ACLs recursivamente no Gerenciador de armazenamento do Azure](data-lake-storage-explorer.md#apply-acls-recursively). 

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

- Uma conta de armazenamento precisa ter o HNS (namespace hierárquico) habilitado. Siga [estas](create-data-lake-storage-account.md) instruções para criar um.

- As permissões corretas para executar o processo de ACL recursivo. A permissão correta inclui um dos seguintes: 

  - Uma [entidade de segurança](../../role-based-access-control/overview.md#security-principal) de Azure Active Directory (AD) provisionada à qual foi atribuída a função de proprietário de dados do [blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) no escopo do contêiner de destino, grupo de recursos pai ou assinatura.   

  - Usuário proprietário do contêiner ou diretório de destino ao qual você planeja aplicar o processo de ACL recursivo. Isso inclui todos os itens filho no diretório ou contêiner de destino. 

- Uma compreensão de como as ACLs são aplicadas a diretórios e arquivos. Consulte [controle de acesso em Azure data Lake Storage Gen2](./data-lake-storage-access-control.md). 

Consulte a seção **configurar seu projeto** deste artigo para exibir as diretrizes de instalação para o PowerShell, o SDK do .net e o SDK do Python.

## <a name="set-up-your-project"></a>Configurar o seu projeto

Instale as bibliotecas necessárias.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Verifique se a versão do PowerShell instalada é `5.1` ou superior usando o comando a seguir.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Para atualizar sua versão do PowerShell, consulte [atualizando o Windows PowerShell existente](/powershell/scripting/install/installing-windows-powershell)
    
2. Instale o módulo **AZ. Storage** .

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   Para obter mais informações sobre como instalar módulos do PowerShell, consulte [instalar o Azure PowerShell Module](/powershell/azure/install-az-ps)

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Abra o [Azure Cloud Shell](../../cloud-shell/overview.md) ou, se você [instalou](/cli/azure/install-azure-cli) a CLI do Azure localmente, abra um aplicativo de console de comando, como Windows PowerShell.

2. Verifique se a versão da CLI do Azure instalada é `2.14.0` ou superior usando o comando a seguir.

   ```azurecli
    az --version
   ```
   Se sua versão da CLI do Azure for inferior a `2.14.0`, instale uma versão posterior. Confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

### <a name="net"></a>[.NET](#tab/dotnet)

1. Abra uma janela de comando (por exemplo: Windows PowerShell).

2. No diretório do projeto, instale o pacote de visualização do Azure. Storage. files. datalake usando o `dotnet add package` comando.

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.5.0-preview.1 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

3. Adicione-as usando as instruções na parte superior do seu arquivo de código.

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
    ```

### <a name="java"></a>[Java](#tab/java)

Para começar, abra [esta página](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) e localize a versão mais recente da biblioteca do Java. Em seguida, abra o arquivo *pom.xml* em seu editor de texto. Adicione um elemento Dependency que faz referência a essa versão.

Se você planeja autenticar seu aplicativo cliente usando o Azure Active Directory (AD), adicione uma dependência à biblioteca do cliente de segredo do Azure. Consulte  [adicionando o pacote de biblioteca de cliente secreto ao seu projeto](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

Em seguida, adicione essas instruções Imports ao arquivo de código.

```java
import com.azure.identity.ClientSecretCredential;
import com.azure.identity.ClientSecretCredentialBuilder;
import com.azure.core.http.rest.PagedIterable;
import com.azure.core.http.rest.Response;
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.AccessControlChangeResult;
import com.azure.storage.file.datalake.models.AccessControlType;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.PathRemoveAccessControlEntry;
import com.azure.storage.file.datalake.models.RolePermissions;
import com.azure.storage.file.datalake.options.PathSetAccessControlRecursiveOptions;
```

### <a name="python"></a>[Python](#tab/python)

1. Baixe a [biblioteca de cliente Azure data Lake Storage para Python](https://recursiveaclpr.blob.core.windows.net/privatedrop/azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl?sv=2019-02-02&st=2020-08-24T07%3A47%3A01Z&se=2021-08-25T07%3A47%3A00Z&sr=b&sp=r&sig=H1XYw4FTLJse%2BYQ%2BfamVL21UPVIKRnnh2mfudA%2BfI0I%3D).

2. Instale a biblioteca que você baixou usando [Pip](https://pypi.org/project/pip/).

   ```
   pip install azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl
   ```

3. Adicione essas instruções de importação na parte superior do seu arquivo de código.

   ```python
   import os, uuid, sys
   from azure.storage.filedatalake import DataLakeServiceClient
   from azure.core._match_conditions import MatchConditions
   from azure.storage.filedatalake._models import ContentSettings
   ```

---

## <a name="connect-to-your-account"></a>Conectar-se à sua conta

Você pode se conectar usando o Azure Active Directory (AD) ou usando uma chave de conta. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="connect-by-using-azure-active-directory-ad"></a>Conectar usando o Azure Active Directory (AD)

Com essa abordagem, o sistema garante que sua conta de usuário tenha as atribuições apropriadas do Azure RBAC (controle de acesso baseado em função) e permissões de ACL. 

A tabela a seguir mostra cada uma das funções com suporte e sua funcionalidade de configuração de ACL.

|Função|Funcionalidade de configuração de ACL|
|--|--|
|[Proprietário de Dados do Blob de Armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Todos os diretórios e arquivos na conta.|
|[Colaborador de dados de blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Somente diretórios e arquivos de propriedade da entidade de segurança.|

1. Abra uma janela de comando do Windows PowerShell e entre em sua assinatura do Azure com o `Connect-AzAccount` comando e siga as instruções na tela.

   ```powershell
   Connect-AzAccount
   ```

2. Se sua identidade estiver associada a mais de uma assinatura, defina sua assinatura ativa para a assinatura da conta de armazenamento na qual você deseja criar e gerenciar diretórios. Neste exemplo, substitua o `<subscription-id>` valor do espaço reservado pela ID da sua assinatura.

   ```powershell
   Select-AzSubscription -SubscriptionId <subscription-id>
   ```
3. Obtenha o contexto da conta de armazenamento.

   ```powershell
   $ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
   ```

#### <a name="connect-by-using-an-account-key"></a>Conectar-se usando uma chave de conta

Com essa abordagem, o sistema não verifica as permissões de ACL ou RBAC do Azure. Obtenha o contexto da conta de armazenamento usando uma chave de conta.

```powershell
$ctx = New-AzStorageContext -StorageAccountName "<storage-account-name>" -StorageAccountKey "<storage-account-key>"
```

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

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

### <a name="net"></a>[.NET](#tab/dotnet)

Para usar os trechos de código neste artigo, você precisará criar uma instância de [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) que representa a conta de armazenamento.

#### <a name="connect-by-using-azure-active-directory-ad"></a>Conectar usando o Azure Active Directory (AD)

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

#### <a name="connect-by-using-an-account-key"></a>Conectar-se usando uma chave de conta

Essa abordagem é a maneira mais fácil de se conectar a uma conta. 

Este exemplo cria uma instância de [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) usando uma chave de conta.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithKey":::

> [!NOTE]
> Para obter mais exemplos, consulte a documentação da [biblioteca do cliente de identidade do Azure para .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) .

### <a name="java"></a>[Java](#tab/java)

Para usar os trechos de código neste artigo, você precisará criar uma instância de **DataLakeServiceClient** que representa a conta de armazenamento. 

#### <a name="connect-by-using-an-account-key"></a>Conectar-se usando uma chave de conta

Essa é a maneira mais fácil de se conectar a uma conta. 

Este exemplo cria uma instância de **DataLakeServiceClient** usando uma chave de conta.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithKey":::

#### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Conectar-se usando Azure Active Directory (Azure AD)

Você pode usar a [biblioteca de cliente de identidade do Azure para Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) para autenticar seu aplicativo com o Azure AD.

Este exemplo cria uma instância de **DataLakeServiceClient** usando uma ID do cliente, um segredo do cliente e uma ID de locatário.  Para obter esses valores, consulte [adquirir um token do Azure ad para autorizar solicitações de um aplicativo cliente](../common/storage-auth-aad-app.md).

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithAzureAD":::

> [!NOTE]
> Para obter mais exemplos, consulte a documentação da [biblioteca de cliente de identidade do Azure para Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) .

### <a name="python"></a>[Python](#tab/python)

Para usar os trechos de código neste artigo, você precisará criar uma instância de **DataLakeServiceClient** que representa a conta de armazenamento. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Conectar usando o Azure Active Directory (AD)

Você pode usar a [biblioteca de cliente de identidade do Azure para Python](https://pypi.org/project/azure-identity/) para autenticar seu aplicativo com o Azure AD.

Este exemplo cria uma instância de **DataLakeServiceClient** usando uma ID do cliente, um segredo do cliente e uma ID de locatário.  Para obter esses valores, consulte [adquirir um token do Azure ad para autorizar solicitações de um aplicativo cliente](../common/storage-auth-aad-app.md). Como parte desse processo, você precisará atribuir uma das seguintes funções do Azure [RBAC (controle de acesso baseado em função](../../role-based-access-control/overview.md) do Azure) à sua entidade de segurança. 

|Função|Funcionalidade de configuração de ACL|
|--|--|
|[Proprietário de Dados do Blob de Armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Todos os diretórios e arquivos na conta.|
|[Colaborador de dados de blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Somente diretórios e arquivos de propriedade da entidade de segurança.|

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Para obter mais exemplos, consulte a documentação da [biblioteca de cliente de identidade do Azure para Python](https://pypi.org/project/azure-identity/) .

### <a name="connect-by-using-an-account-key"></a>Conectar-se usando uma chave de conta

Essa é a maneira mais fácil de se conectar a uma conta. 

Este exemplo cria uma instância de **DataLakeServiceClient** usando uma chave de conta.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::
 
- Substitua o valor de espaço reservado `storage_account_name` pelo nome da sua conta de armazenamento.

- Substitua o `storage_account_key` valor de espaço reservado pela chave de acesso da conta de armazenamento.

---

## <a name="set-an-acl-recursively"></a>Definir uma ACL recursivamente

Ao *definir* uma ACL, você **substitui** toda a ACL, incluindo todas as entradas. Se você quiser alterar o nível de permissão de uma entidade de segurança ou adicionar uma nova entidade de segurança à ACL sem afetar outras entradas existentes, deverá *Atualizar* a ACL em vez disso. Para atualizar uma ACL em vez de substituí-la, consulte a seção [atualizar uma ACL recursivamente](#update-an-acl-recursively) deste artigo.  

Se você optar por *definir* a ACL, deverá adicionar uma entrada para o usuário proprietário, uma entrada para o grupo proprietário e uma entrada para todos os outros usuários. Para saber mais sobre o usuário proprietário, o grupo proprietário e todos os outros usuários, consulte [usuários e identidades](data-lake-storage-access-control.md#users-and-identities). 

Esta seção contém exemplos de como definir uma ACL.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Defina uma ACL recursivamente usando o cmdlet **set-AzDataLakeGen2AclRecursive** .

Este exemplo define a ACL de um diretório chamado `my-parent-directory` . Essas entradas fornecem ao usuário proprietário permissões de leitura, gravação e execução, fornece ao grupo proprietário somente permissões de leitura e execução e concede a todos os outros sem acesso. A última entrada ACL neste exemplo fornece a um usuário específico as permissões de leitura e execução da ID de objeto "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx".

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission r-x -InputObject $acl 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "---" -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission r-x -InputObject $acl 

Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Se você quiser definir uma entrada ACL **padrão** , use o parâmetro **-DefaultScope** ao executar o comando **set-AzDataLakeGen2ItemAclObject** . Por exemplo, `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`.

Para ver um exemplo que define ACLs recursivamente em lotes especificando um tamanho de lote, consulte o artigo de referência [set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) .

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Defina uma ACL recursivamente usando o comando [AZ Storage FS Access Set-recursive](/cli/azure/storage/fs/access#az_storage_fs_access_set_recursive) .

Este exemplo define a ACL de um diretório chamado `my-parent-directory` . Essas entradas fornecem ao usuário proprietário permissões de leitura, gravação e execução, fornece ao grupo proprietário somente permissões de leitura e execução e concede a todos os outros sem acesso. A última entrada ACL neste exemplo fornece a um usuário específico as permissões de leitura e execução da ID de objeto "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx".

```azurecli
az storage fs access set-recursive --acl "user::rwx,group::r-x,other::---,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Se você quiser definir uma entrada ACL **padrão** , adicione o prefixo `default:` a cada entrada. Por exemplo, `default:user::rwx` ou `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`. 

### <a name="net"></a>[.NET](#tab/dotnet)

Defina uma ACL recursivamente chamando o método **DataLakeDirectoryClient. SetAccessControlRecursiveAsync** . Passe este método uma [lista](/dotnet/api/system.collections.generic.list-1) de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Cada [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) define uma entrada de ACL. 

Se você quiser definir uma entrada ACL **padrão** , poderá definir a propriedade [PathAccessControlItem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) como **true**. 

Este exemplo define a ACL de um diretório chamado `my-parent-directory` . Esse método aceita um parâmetro booliano chamado `isDefaultScope` que especifica se a ACL padrão deve ser definida. Esse parâmetro é usado no construtor do [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). As entradas da ACL fornecem ao usuário proprietário permissões de leitura, gravação e execução, fornece ao grupo proprietário somente permissões de leitura e execução e concede a todos os outros sem acesso. A última entrada ACL neste exemplo fornece um usuário específico com a ID de objeto "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "permissões de leitura e execução.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_SetACLRecursively":::

Para ver um exemplo que define ACLs recursivamente em lotes especificando um tamanho de lote, consulte o [exemplo](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).net.

### <a name="java"></a>[Java](#tab/java)

Defina uma ACL recursivamente chamando o método **DataLakeDirectoryClient. setAccessControlRecursive** . Passe este método uma [lista](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) de objetos [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) . Cada [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) define uma entrada de ACL. 

Se você quiser definir uma entrada ACL **padrão** , poderá chamar o método **setDefaultScope** do [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) e passar um valor de **true**. 

Este exemplo define a ACL de um diretório chamado `my-parent-directory` . Esse método aceita um parâmetro booliano chamado `isDefaultScope` que especifica se a ACL padrão deve ser definida. Esse parâmetro é usado em cada chamada para o método **setDefaultScope** do [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). As entradas da ACL fornecem ao usuário proprietário permissões de leitura, gravação e execução, fornece ao grupo proprietário somente permissões de leitura e execução e concede a todos os outros sem acesso. A última entrada ACL neste exemplo fornece a um usuário específico as permissões de leitura e execução da ID de objeto "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx".

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_SetACLRecursively":::

### <a name="python"></a>[Python](#tab/python)

Defina uma ACL recursivamente chamando o método **DataLakeDirectoryClient.set_access_control_recursive** .

Se você quiser definir uma entrada ACL **padrão** , adicione a cadeia de caracteres `default:` ao início de cada cadeia de caracteres de entrada de ACL. 

Este exemplo define a ACL de um diretório chamado `my-parent-directory` . 

Esse método aceita um parâmetro booliano chamado `is_default_scope` que especifica se a ACL padrão deve ser definida. Se esse parâmetro for `True` , a lista de entradas de ACL será precedida pela cadeia de caracteres `default:` . 

As entradas da ACL fornecem ao usuário proprietário permissões de leitura, gravação e execução, fornece ao grupo proprietário somente permissões de leitura e execução e concede a todos os outros sem acesso. A última entrada ACL neste exemplo fornece um usuário específico com a ID de objeto "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "permissões de leitura e execução. Essas entradas fornecem ao usuário proprietário permissões de leitura, gravação e execução, fornece ao grupo proprietário somente permissões de leitura e execução e concede a todos os outros sem acesso. A última entrada ACL neste exemplo fornece um usuário específico com a ID de objeto "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "permissões de leitura e execução.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_SetACLRecursively":::

Para ver um exemplo que processa ACLs recursivamente em lotes especificando um tamanho de lote, consulte o [exemplo](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)de Python.

---

## <a name="update-an-acl-recursively"></a>Atualizar uma ACL recursivamente

Ao *Atualizar* uma ACL, você modifica a ACL em vez de substituir a ACL. Por exemplo, você pode adicionar uma nova entidade de segurança à ACL sem afetar outras entidades de segurança listadas na ACL.  Para substituir a ACL em vez de atualizá-la, consulte a seção [definir uma ACL recursivamente](#set-an-acl-recursively) deste artigo. 

Para atualizar uma ACL, crie um novo objeto ACL com a entrada ACL que você deseja atualizar e, em seguida, use esse objeto em atualizar a operação de ACL. Não obtenha a ACL existente, basta fornecer as entradas de ACL a serem atualizadas.

Esta seção contém exemplos de como atualizar uma ACL.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Atualize uma ACL recursivamente usando o cmdlet  **Update-AzDataLakeGen2AclRecursive** . 

Este exemplo atualiza uma entrada ACL com permissão de gravação. 

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Se você quiser atualizar uma entrada ACL **padrão** , use o parâmetro **-DefaultScope** ao executar o comando **set-AzDataLakeGen2ItemAclObject** . Por exemplo, `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx -DefaultScope`.

Para ver um exemplo que atualiza as ACLs recursivamente em lotes especificando um tamanho de lote, consulte o artigo de referência [Update-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/update-azdatalakegen2aclrecursive) .

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Atualize uma ACL recursivamente usando o comando  [AZ Storage FS Access Update-recursive](/cli/azure/storage/fs/access#az_storage_fs_access_update_recursive) . 

Este exemplo atualiza uma entrada ACL com permissão de gravação. 

```azurecli
az storage fs access update-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Se você quiser atualizar uma entrada ACL **padrão** , adicione o prefixo `default:` a cada entrada. Por exemplo, `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`.

### <a name="net"></a>[.NET](#tab/dotnet)

Atualize uma ACL recursivamente chamando o método **DataLakeDirectoryClient. UpdateAccessControlRecursiveAsync** .  Passe este método uma [lista](/dotnet/api/system.collections.generic.list-1) de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Cada [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) define uma entrada de ACL. 

Se você quiser atualizar uma entrada ACL **padrão** , poderá definir a propriedade [PathAccessControlItem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) como **true**. 

Este exemplo atualiza uma entrada ACL com permissão de gravação. Esse método aceita um parâmetro booliano chamado `isDefaultScope` que especifica se a ACL padrão deve ser atualizada. Esse parâmetro é usado no construtor do [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_UpdateACLsRecursively":::

Para ver um exemplo que atualiza as ACLs recursivamente em lotes especificando um tamanho de lote, consulte o [exemplo](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).net.

### <a name="java"></a>[Java](#tab/java)

Atualize uma ACL recursivamente chamando o método **DataLakeDirectoryClient. updateAccessControlRecursive** .  Passe este método uma [lista](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) de objetos [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) . Cada [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) define uma entrada de ACL. 

Se desejar atualizar uma entrada de ACL **padrão** , você poderá o método **setDefaultScope** do [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) e passar um valor de **true**. 

Este exemplo atualiza uma entrada ACL com permissão de gravação. Esse método aceita um parâmetro booliano chamado `isDefaultScope` que especifica se a ACL padrão deve ser atualizada. Esse parâmetro é usado na chamada para o método **setDefaultScope** do [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_UpdateACLRecursively":::

### <a name="python"></a>[Python](#tab/python)

Atualize uma ACL recursivamente chamando o método **DataLakeDirectoryClient.update_access_control_recursive** . Se você quiser atualizar uma entrada de ACL **padrão** , adicione a cadeia de caracteres `default:` ao início de cada cadeia de caracteres de entrada de ACL. 

Este exemplo atualiza uma entrada ACL com permissão de gravação.

Este exemplo define a ACL de um diretório chamado `my-parent-directory` . Esse método aceita um parâmetro booliano chamado `is_default_scope` que especifica se a ACL padrão deve ser atualizada. Se esse parâmetro for `True` , a entrada de ACL atualizada será precedida pela cadeia de caracteres `default:` .  

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_UpdateACLsRecursively":::

Para ver um exemplo que processa ACLs recursivamente em lotes especificando um tamanho de lote, consulte o [exemplo](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)de Python.

---

## <a name="remove-acl-entries-recursively"></a>Remover entradas de ACL recursivamente

Você pode remover uma ou mais entradas de ACL recursivamente. Para remover uma entrada de ACL, crie um novo objeto ACL para a entrada de ACL a ser removida e use esse objeto em remover a operação de ACL. Não obtenha a ACL existente, basta fornecer as entradas de ACL a serem removidas. 

Esta seção contém exemplos de como remover uma ACL. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Remova as entradas ACL usando o cmdlet **Remove-AzDataLakeGen2AclRecursive** . 

Este exemplo remove uma entrada ACL do diretório raiz do contêiner.  

```powershell
$filesystemName = "my-container"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" 

Remove-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName  -Acl $acl
```

> [!NOTE]
> Se você quiser remover uma entrada ACL **padrão** , use o parâmetro **-DefaultScope** ao executar o comando **set-AzDataLakeGen2ItemAclObject** . Por exemplo, `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" -DefaultScope`.

Para ver um exemplo que remove ACLs recursivamente em lotes especificando um tamanho de lote, consulte o artigo de referência [Remove-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/remove-azdatalakegen2aclrecursive) .

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Remova as entradas de ACL usando o comando [AZ Storage FS Access remove-recursive](/cli/azure/storage/fs/access#az_storage_fs_access_remove_recursive) . 

Este exemplo remove uma entrada ACL do diretório raiz do contêiner.  

```azurecli
az storage fs access remove-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Se você quiser remover uma entrada ACL **padrão** , adicione o prefixo `default:` a cada entrada. Por exemplo, `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

### <a name="net"></a>[.NET](#tab/dotnet)

Remova as entradas de ACL chamando o método **DataLakeDirectoryClient. RemoveAccessControlRecursiveAsync** . Passe este método uma [lista](/dotnet/api/system.collections.generic.list-1) de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Cada [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) define uma entrada de ACL. 

Se você quiser remover uma entrada ACL **padrão** , poderá definir a propriedade [PathAccessControlItem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) como **true**. 

Este exemplo remove uma entrada ACL da ACL do diretório chamado `my-parent-directory` . Esse método aceita um parâmetro booliano chamado `isDefaultScope` que especifica se a entrada deve ser removida da ACL padrão. Esse parâmetro é usado no construtor do [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_RemoveACLRecursively":::

Para ver um exemplo que remove ACLs recursivamente em lotes especificando um tamanho de lote, consulte o [exemplo](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).net.

### <a name="java"></a>[Java](#tab/java)

Remova as entradas de ACL chamando o método **DataLakeDirectoryClient. removeAccessControlRecursive** . Passe este método uma [lista](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) de objetos [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) . Cada [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) define uma entrada de ACL. 

Se desejar remover uma entrada ACL **padrão** , você poderá o método **setDefaultScope** do [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) e passar um valor de **true**.  

Este exemplo remove uma entrada ACL da ACL do diretório chamado `my-parent-directory` . Esse método aceita um parâmetro booliano chamado `isDefaultScope` que especifica se a entrada deve ser removida da ACL padrão. Esse parâmetro é usado na chamada para o método **setDefaultScope** do [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html).

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_RemoveACLRecursively":::

### <a name="python"></a>[Python](#tab/python)

Remova as entradas ACL chamando o método **DataLakeDirectoryClient.remove_access_control_recursive** . Se você quiser remover uma entrada ACL **padrão** , adicione a cadeia de caracteres `default:` ao início da cadeia de caracteres de entrada ACL. 

Este exemplo remove uma entrada ACL da ACL do diretório chamado `my-parent-directory` . Esse método aceita um parâmetro booliano chamado `is_default_scope` que especifica se a entrada deve ser removida da ACL padrão. Se esse parâmetro for `True` , a entrada de ACL atualizada será precedida pela cadeia de caracteres `default:` . 

```python
def remove_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

Para ver um exemplo que processa ACLs recursivamente em lotes especificando um tamanho de lote, consulte o [exemplo](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)de Python.

---

## <a name="recover-from-failures"></a>Recuperar de falhas

Você pode encontrar erros de tempo de execução ou de permissão. Para erros de tempo de execução, reinicie o processo desde o início. Poderão ocorrer erros de permissão se a entidade de segurança não tiver permissão suficiente para modificar a ACL de um diretório ou arquivo que está na hierarquia de diretório que está sendo modificada. Resolva o problema de permissão e, em seguida, escolha retomar o processo a partir do ponto de falha usando um token de continuação ou reiniciar o processo do início. Você não precisará usar o token de continuação se preferir reiniciar desde o início. Você pode reaplicar entradas de ACL sem nenhum impacto negativo.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Este exemplo retorna resultados para a variável e, em seguida, canaliza entradas com falha para uma tabela formatada.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$result
$result.FailedEntries | ft 
```

Com base na saída da tabela, você pode corrigir quaisquer erros de permissão e, em seguida, retomar a execução usando o token de continuação.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinuationToken $result.ContinuationToken
$result

```

Para ver um exemplo que define ACLs recursivamente em lotes especificando um tamanho de lote, consulte o artigo de referência [set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) .

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Em caso de falha, você pode retornar um token de continuação definindo o `--continue-on-failure` parâmetro como `false` . Depois de resolver os erros, você pode retomar o processo do ponto de falha executando o comando novamente e, em seguida, definindo o `--continuation` parâmetro para o token de continuação. 

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure false --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

## <a name="net"></a>[.NET](#tab/dotnet)

Este exemplo retorna um token de continuação no caso de uma falha. O aplicativo pode chamar esse método de exemplo novamente depois que o erro for resolvido e passar o token de continuação. Se esse método de exemplo for chamado pela primeira vez, o aplicativo poderá passar um valor de `null` para o parâmetro de token de continuação. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ResumeContinuationToken":::

Para ver um exemplo que define ACLs recursivamente em lotes especificando um tamanho de lote, consulte o [exemplo](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).net.

### <a name="java"></a>[Java](#tab/java)

Este exemplo retorna um token de continuação no caso de uma falha. O aplicativo pode chamar esse método de exemplo novamente depois que o erro for resolvido e passar o token de continuação. Se esse método de exemplo for chamado pela primeira vez, o aplicativo poderá passar um valor de `null` para o parâmetro de token de continuação. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ResumeSetACLRecursively":::

### <a name="python"></a>[Python](#tab/python)

Este exemplo retorna um token de continuação no caso de uma falha. O aplicativo pode chamar esse método de exemplo novamente depois que o erro for resolvido e passar o token de continuação. Se esse método de exemplo for chamado pela primeira vez, o aplicativo poderá passar um valor de ``None`` para o parâmetro de token de continuação. 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ResumeContinuationToken":::

Para ver um exemplo que processa ACLs recursivamente em lotes especificando um tamanho de lote, consulte o [exemplo](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)de Python.

---

Se você quiser que o processo seja concluído sem interrupções por erros de permissão, poderá especificar isso.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Este exemplo usa o `ContinueOnFailure` parâmetro para que a execução continue mesmo que a operação encontre um erro de permissão. 

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinueOnFailure

echo "[Result Summary]"
echo "TotalDirectoriesSuccessfulCount: `t$($result.TotalFilesSuccessfulCount)"
echo "TotalFilesSuccessfulCount: `t`t`t$($result.TotalDirectoriesSuccessfulCount)"
echo "TotalFailureCount: `t`t`t`t`t$($result.TotalFailureCount)"
echo "FailedEntries:"$($result.FailedEntries | ft) 
```

Para ver um exemplo que define ACLs recursivamente em lotes especificando um tamanho de lote, consulte o artigo de referência [set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) .

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para garantir que o processo seja concluído sem interrupções, defina o `--continue-on-failure` parâmetro como `true` . 

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure true --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

### <a name="net"></a>[.NET](#tab/dotnet)

Para garantir que o processo seja concluído sem interrupções, passe um objeto **AccessControlChangedOptions** e defina a propriedade **ContinueOnFailure** desse objeto como ``true`` .

Este exemplo define as entradas de ACL recursivamente. Se esse código encontrar um erro de permissão, ele registrará essa falha e continuará a execução. Este exemplo imprime o número de falhas no console. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ContinueOnFailure":::

Para ver um exemplo que define ACLs recursivamente em lotes especificando um tamanho de lote, consulte o [exemplo](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).net.

### <a name="java"></a>[Java](#tab/java)

Para garantir que o processo seja concluído sem interrupções, chame o método **setContinueOnFailure** de um objeto [PathSetAccessControlRecursiveOptions](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) e passe um valor de **true**.

Este exemplo define as entradas de ACL recursivamente. Se esse código encontrar um erro de permissão, ele registrará essa falha e continuará a execução. Este exemplo imprime o número de falhas no console. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ContinueOnFailure":::

### <a name="python"></a>[Python](#tab/python)

Para garantir que o processo seja concluído sem interrupções, não passe um token de continuação para o método **DataLakeDirectoryClient.set_access_control_recursive** .

Este exemplo define as entradas de ACL recursivamente. Se esse código encontrar um erro de permissão, ele registrará essa falha e continuará a execução. Este exemplo imprime o número de falhas no console. 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ContinueOnFailure":::

Para ver um exemplo que processa ACLs recursivamente em lotes especificando um tamanho de lote, consulte o [exemplo](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)de Python.

---

## <a name="resources"></a>Recursos

Esta seção contém links para bibliotecas e exemplos de código.

#### <a name="libraries"></a>Bibliotecas

- [PowerShell](https://www.powershellgallery.com/packages/Az.Storage/3.0.0)
- [CLI do Azure](/cli/azure/storage/fs/access)
- [.NET](https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json)
- [Java](/java/api/overview/azure/storage-file-datalake-readme)
- [Python](https://recursiveaclpr.blob.core.windows.net/privatedrop/azure_storage_file_datalake-12.1.0b99-py2.py3-none-any.whl?sv=2019-02-02&st=2020-08-24T07%3A47%3A01Z&se=2021-08-25T07%3A47%3A00Z&sr=b&sp=r&sig=H1XYw4FTLJse%2BYQ%2BfamVL21UPVIKRnnh2mfudA%2BfI0I%3D)
- [REST](/rest/api/storageservices/datalakestoragegen2/path/update)

#### <a name="code-samples"></a>Exemplos de código

- PowerShell: exemplo de [Leiame](https://recursiveaclpr.blob.core.windows.net/privatedrop/README.txt?sv=2019-02-02&st=2020-08-24T17%3A03%3A18Z&se=2021-08-25T17%3A03%3A00Z&sr=b&sp=r&sig=sPdKiCSXWExV62sByeOYqBTqpGmV2h9o8BLij3iPkNQ%3D)  |  [](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D)

- CLI do Azure: [exemplo](https://github.com/Azure/azure-cli/blob/2a55a5350696a3a93a13f364f2104ec8bc82cdd3/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)

- NET: exemplo de [Leiame](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520net%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A20%253A42Z%26se%3D2021-08-26T23%253A20%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DKrnHvasHoSoVeUyr2g%2fSc2aDVW3De4A%2fvx0lFWZs494%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503767961&sdata=gd%2B2LphTtDFVb7pZko9rkGO9OG%2FVvmeXprHB9IOEYXE%3D&reserved=0)  |  [](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)

- Python: exemplo de [Leiame](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FREADME%2520for%2520python%3Fsv%3D2019-02-02%26st%3D2020-08-25T23%253A21%253A47Z%26se%3D2021-08-26T23%253A21%253A00Z%26sr%3Db%26sp%3Dr%26sig%3DRq6Bl5lXrtYk79thy8wX7UTbjyd2f%252B6xzVBFFVYbdYg%253D&data=02%7C01%7Cnormesta%40microsoft.com%7Cda902e4fe6c24e6a07d908d8494fd4bd%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637339954503777915&sdata=3e46Lp2miOHj755Gh0odH3M0%2BdTF3loGCCBENrulVTM%3D&reserved=0)  |  [](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)

## <a name="best-practice-guidelines"></a>Diretrizes de práticas recomendadas

Esta seção fornece algumas diretrizes de práticas recomendadas para definir as ACLs recursivamente. 

#### <a name="handling-runtime-errors"></a>Manipulando erros de tempo de execução

Um erro de tempo de execução pode ocorrer por vários motivos (por exemplo: uma interrupção ou um problema de conectividade de cliente). Se você encontrar um erro de tempo de execução, reinicie o processo de ACL recursivo. As ACLs podem ser reaplicadas a itens sem causar um impacto negativo. 

#### <a name="handling-permission-errors-403"></a>Tratamento de erros de permissão (403)

Se você encontrar uma exceção de controle de acesso durante a execução de um processo de ACL recursivo, a [entidade de segurança](../../role-based-access-control/overview.md#security-principal) do AD poderá não ter permissão suficiente para aplicar uma ACL a um ou mais dos itens filho na hierarquia de diretórios. Quando ocorre um erro de permissão, o processo é interrompido e um token de continuação é fornecido. Corrija o problema de permissão e, em seguida, use o token de continuação para processar o conjunto de um restante. Os diretórios e arquivos que já foram processados com êxito não precisam ser processados novamente. Você também pode optar por reiniciar o processo de ACL recursivo. As ACLs podem ser reaplicadas a itens sem causar um impacto negativo. 

#### <a name="credentials"></a>Credenciais 

Recomendamos que você provisione uma entidade de segurança do Azure AD que tenha sido atribuída à função de [proprietário de dados do blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) no escopo da conta de armazenamento de destino ou do contêiner. 

#### <a name="performance"></a>Desempenho 

Para reduzir a latência, recomendamos que você execute o processo de ACL recursivo em uma VM (máquina virtual) do Azure localizada na mesma região que a sua conta de armazenamento. 

#### <a name="acl-limits"></a>Limites de ACL

O número máximo de ACLs que você pode aplicar a um diretório ou arquivo é de 32 ACLs de acesso e 32 ACLs padrão. Para obter mais informações, consulte [Controle de acesso no Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

## <a name="see-also"></a>Consulte também

- [Controle de acesso no Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md)
- [Problemas conhecidos](data-lake-storage-known-issues.md)
