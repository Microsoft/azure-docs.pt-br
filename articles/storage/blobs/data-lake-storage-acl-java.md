---
title: Use Java para definir ACLs no Azure Data Lake Storage Gen2
description: Use bibliotecas de armazenamento do Azure para Java para gerenciar listas de controle de acesso (ACL) em contas de armazenamento que têm o namespace hierárquico (HNS) habilitado.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: e7d6156fe5cd8ab32ff159bda64e0c06cfbac406
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653963"
---
# <a name="use-java-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Usar Java para gerenciar ACLs no Azure Data Lake Storage Gen2

Este artigo mostra como usar o Java para obter, definir e atualizar as listas de controle de acesso de diretórios e arquivos. 

A herança de ACL já está disponível para novos itens filho que são criados em um diretório pai. Mas você também pode adicionar, atualizar e remover ACLs recursivamente nos itens filho existentes de um diretório pai sem precisar fazer essas alterações individualmente para cada item filho. 

[Pacote (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)  |  [Exemplos](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)  |  [Referência](/java/api/overview/azure/storage-file-datalake-readme)  |  de API Mapeamento de Gen1 [para Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Enviar comentários](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

- Uma conta de armazenamento precisa ter o HNS (namespace hierárquico) habilitado. Siga [estas](create-data-lake-storage-account.md) instruções para criar um.

- CLI do Azure versão `2.6.0` ou superior

- Uma das seguintes permissões de segurança:

  - Uma [entidade de segurança](../../role-based-access-control/overview.md#security-principal) de Azure Active Directory (AD) provisionada à qual foi atribuída a função de proprietário de dados do [blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) no escopo do contêiner de destino, grupo de recursos pai ou assinatura.  

  - Usuário proprietário do contêiner ou diretório de destino ao qual você planeja aplicar as configurações de ACL. Para definir ACLs recursivamente, isso inclui todos os itens filho no contêiner ou diretório de destino.
  
  - Chave da conta de armazenamento.

## <a name="set-up-your-project"></a>Configurar o seu projeto

Para começar, abra [esta página](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) e localize a versão mais recente da biblioteca do Java. Em seguida, abra o arquivo *pom.xml* em seu editor de texto. Adicione um elemento Dependency que faz referência a essa versão.

Se você planeja autenticar seu aplicativo cliente usando o Azure Active Directory (AD), adicione uma dependência à biblioteca do cliente de segredo do Azure. Consulte  [adicionando o pacote de biblioteca de cliente secreto ao seu projeto](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

Em seguida, adicione essas instruções Imports ao arquivo de código.

```java
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.AccessControlChangeCounters;
import com.azure.storage.file.datalake.models.AccessControlChangeResult;
import com.azure.storage.file.datalake.models.AccessControlType;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.PathRemoveAccessControlEntry;
import com.azure.storage.file.datalake.models.RolePermissions;
import com.azure.storage.file.datalake.options.PathSetAccessControlRecursiveOptions;
```

## <a name="connect-to-the-account"></a>Conectar à conta

Para usar os trechos de código neste artigo, você precisará criar uma instância de **DataLakeServiceClient** que representa a conta de armazenamento. 

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Conectar-se usando Azure Active Directory (Azure AD)

Você pode usar a [biblioteca de cliente de identidade do Azure para Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) para autenticar seu aplicativo com o Azure AD.

Obtenha uma ID do cliente, um segredo do cliente e uma ID de locatário. Para fazer isso, consulte [adquirir um token do Azure ad para autorizar solicitações de um aplicativo cliente](../common/storage-auth-aad-app.md). Como parte desse processo, você precisará atribuir uma das seguintes funções do Azure [RBAC (controle de acesso baseado em função](../../role-based-access-control/overview.md) do Azure) à sua entidade de segurança. 

|Função|Funcionalidade de configuração de ACL|
|--|--|
|[Proprietário de Dados do Blob de Armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Todos os diretórios e arquivos na conta.|
|[Colaborador de dados de blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Somente diretórios e arquivos de propriedade da entidade de segurança.|

Este exemplo cria uma instância de **DataLakeServiceClient** usando uma ID do cliente, um segredo do cliente e uma ID de locatário.  

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithAzureAD":::

> [!NOTE]
> Para obter mais exemplos, consulte a documentação da [biblioteca de cliente de identidade do Azure para Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) .

### <a name="connect-by-using-an-account-key"></a>Conectar-se usando uma chave de conta

Essa é a maneira mais fácil de se conectar a uma conta. 

Este exemplo cria uma instância de **DataLakeServiceClient** usando uma chave de conta.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithKey":::

## <a name="set-acls"></a>Definir ACLs

Ao *definir* uma ACL, você **substitui** toda a ACL, incluindo todas as entradas. Se você quiser alterar o nível de permissão de uma entidade de segurança ou adicionar uma nova entidade de segurança à ACL sem afetar outras entradas existentes, deverá *Atualizar* a ACL em vez disso. Para atualizar uma ACL em vez de substituí-la, consulte a seção [Update ACLs](#update-acls) deste artigo.  

Se você optar por *definir* a ACL, deverá adicionar uma entrada para o usuário proprietário, uma entrada para o grupo proprietário e uma entrada para todos os outros usuários. Para saber mais sobre o usuário proprietário, o grupo proprietário e todos os outros usuários, consulte [usuários e identidades](data-lake-storage-access-control.md#users-and-identities).

Esta seção mostra como:

- Definir a ACL de um diretório
- Definir a ACL de um arquivo
- Definir ACLs recursivamente 

### <a name="set-the-acl-of-a-directory"></a>Definir a ACL de um diretório

Este exemplo obtém e define a ACL de um diretório chamado `my-directory` . Este exemplo fornece as permissões de leitura, gravação e execução do usuário proprietário, fornece ao grupo proprietário somente permissões de leitura e execução e concede a todos os outros acesso de leitura.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageDirectoryACLs":::

Você também pode obter e definir a ACL do diretório raiz de um contêiner. Para obter o diretório raiz, passe uma cadeia de caracteres vazia ( `""` ) para o método **DataLakeFileSystemClient. getDirectoryClient** .

### <a name="set-the-acl-of-a-file"></a>Definir a ACL de um arquivo

Este exemplo obtém e define a ACL de um arquivo chamado `upload-file.txt` . Este exemplo fornece as permissões de leitura, gravação e execução do usuário proprietário, fornece ao grupo proprietário somente permissões de leitura e execução e concede a todos os outros acesso de leitura.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageFileACLs":::

### <a name="set-acls-recursively"></a>Definir ACLs recursivamente

Defina ACLs recursivamente chamando o método **DataLakeDirectoryClient. setAccessControlRecursive** . Passe este método uma [lista](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) de objetos [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) . Cada [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) define uma entrada de ACL.

Se você quiser definir uma entrada ACL **padrão** , poderá chamar o método **setDefaultScope** do [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) e passar um valor de **true**. 

Este exemplo define a ACL de um diretório chamado `my-parent-directory` . Esse método aceita um parâmetro booliano chamado `isDefaultScope` que especifica se a ACL padrão deve ser definida. Esse parâmetro é usado em cada chamada para o método **setDefaultScope** do [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). As entradas da ACL fornecem ao usuário proprietário permissões de leitura, gravação e execução, fornece ao grupo proprietário somente permissões de leitura e execução e concede a todos os outros sem acesso. A última entrada ACL neste exemplo fornece a um usuário específico as permissões de leitura e execução da ID de objeto "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx".

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_SetACLRecursively":::

## <a name="update-acls"></a>Atualizar ACLs

Ao *Atualizar* uma ACL, você modifica a ACL em vez de substituir a ACL. Por exemplo, você pode adicionar uma nova entidade de segurança à ACL sem afetar outras entidades de segurança listadas na ACL.  Para substituir a ACL em vez de atualizá-la, consulte a seção [set ACLs](#set-acls) deste artigo.

Esta seção mostra como:

- Atualizar uma ACL
- Atualizar ACLs recursivamente

### <a name="update-an-acl"></a>Atualizar uma ACL

Primeiro, obtenha a ACL de um diretório chamando o método [PathAccessControl. Getaccesscontrollist](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrol.getaccesscontrollist) . Copie a lista de entradas de ACL para um novo objeto de **lista** do tipo [PathAccessControlListEntry](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrolentry). Em seguida, localize a entrada que você deseja atualizar e a substitua na lista. Defina a ACL chamando o método [DataLakeDirectoryClient. Setaccesscontrollist](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) .

Este exemplo atualiza a ACL de um diretório chamado `my-parent-directory` substituindo a entrada para todos os outros usuários. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_UpdateACL":::

Você também pode obter e definir a ACL do diretório raiz de um contêiner. Para obter o diretório raiz, passe uma cadeia de caracteres vazia ( `""` ) para o método **DataLakeFileSystemClient. getDirectoryClient** .

### <a name="update-acls-recursively"></a>Atualizar ACLs recursivamente

Para atualizar uma ACL recursivamente, crie um novo objeto ACL com a entrada ACL que você deseja atualizar e, em seguida, use esse objeto em atualizar a operação de ACL. Não obtenha a ACL existente, basta fornecer as entradas de ACL a serem atualizadas.

Atualize as ACLs recursivamente chamando o método **DataLakeDirectoryClient. updateAccessControlRecursive** .  Passe este método uma [lista](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) de objetos [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) . Cada [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) define uma entrada de ACL. 

Se desejar atualizar uma entrada de ACL **padrão** , você poderá o método **setDefaultScope** do [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) e passar um valor de **true**. 

Este exemplo atualiza uma entrada ACL com permissão de gravação. Esse método aceita um parâmetro booliano chamado `isDefaultScope` que especifica se a ACL padrão deve ser atualizada. Esse parâmetro é usado na chamada para o método **setDefaultScope** do [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_UpdateACLRecursively":::

## <a name="remove-acl-entries"></a>Remover entradas de ACL

Você pode remover uma ou mais entradas de ACL. Esta seção mostra como:

- Remover uma entrada de ACL
- Remover entradas de ACL recursivamente

### <a name="remove-an-acl-entry"></a>Remover uma entrada de ACL

Primeiro, obtenha a ACL de um diretório chamando o método [PathAccessControl. Getaccesscontrollist](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrol.getaccesscontrollist) . Copie a lista de entradas de ACL para um novo objeto de **lista** do tipo [PathAccessControlListEntry](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrolentry). Em seguida, localize a entrada que você deseja remover e chame o método **Remove** do objeto **list** . Defina a ACL atualizada chamando o método [DataLakeDirectoryClient. Setaccesscontrollist](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_RemoveACLEntry":::

### <a name="remove-acl-entries-recursively"></a>Remover entradas de ACL recursivamente

Para remover as entradas de ACL recursivamente, crie um novo objeto ACL para a entrada de ACL a ser removida e, em seguida, use esse objeto em remover a operação de ACL. Não obtenha a ACL existente, basta fornecer as entradas de ACL a serem removidas.

Remova as entradas de ACL chamando o método **DataLakeDirectoryClient. removeAccessControlRecursive** . Passe este método uma [lista](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) de objetos [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) . Cada [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) define uma entrada de ACL. 

Se desejar remover uma entrada ACL **padrão** , você poderá o método **setDefaultScope** do [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) e passar um valor de **true**.  

Este exemplo remove uma entrada ACL da ACL do diretório chamado `my-parent-directory` . Esse método aceita um parâmetro booliano chamado `isDefaultScope` que especifica se a entrada deve ser removida da ACL padrão. Esse parâmetro é usado na chamada para o método **setDefaultScope** do [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html).

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_RemoveACLRecursively":::

## <a name="recover-from-failures"></a>Recuperar de falhas

Você pode encontrar erros de tempo de execução ou de permissão. Para erros de tempo de execução, reinicie o processo desde o início. Poderão ocorrer erros de permissão se a entidade de segurança não tiver permissão suficiente para modificar a ACL de um diretório ou arquivo que está na hierarquia de diretório que está sendo modificada. Resolva o problema de permissão e, em seguida, escolha retomar o processo a partir do ponto de falha usando um token de continuação ou reiniciar o processo do início. Você não precisará usar o token de continuação se preferir reiniciar desde o início. Você pode reaplicar entradas de ACL sem nenhum impacto negativo.

Este exemplo retorna um token de continuação no caso de uma falha. O aplicativo pode chamar esse método de exemplo novamente depois que o erro for resolvido e passar o token de continuação. Se esse método de exemplo for chamado pela primeira vez, o aplicativo poderá passar um valor de `null` para o parâmetro de token de continuação.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ResumeSetACLRecursively":::

Se você quiser que o processo seja concluído sem interrupções por erros de permissão, poderá especificar isso.

Para garantir que o processo seja concluído sem interrupções, chame o método **setContinueOnFailure** de um objeto [PathSetAccessControlRecursiveOptions](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) e passe um valor de **true**.

Este exemplo define as entradas de ACL recursivamente. Se esse código encontrar um erro de permissão, ele registrará essa falha e continuará a execução. Este exemplo imprime o número de falhas no console.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ContinueOnFailure":::

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Confira também

- [Documentação de referência da API](/java/api/overview/azure/storage-file-datalake-readme)
- [Pacote (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
- [Amostras](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
- [Mapeamento de Gen1 para Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Enviar comentários](https://github.com/Azure/azure-sdk-for-java/issues)
- [Modelo de controle de acesso no Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [ACLs (listas de controle de acesso) no Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)