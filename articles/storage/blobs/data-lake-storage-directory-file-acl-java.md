---
title: Azure Data Lake Storage Gen2 SDK do Java para arquivos & ACLs
description: Use bibliotecas de armazenamento do Azure para Java para gerenciar diretórios e listas de controle de acesso (ACL) de arquivos e diretórios em contas de armazenamento que têm o namespace hierárquico (HNS) habilitado.
author: normesta
ms.service: storage
ms.date: 01/11/2021
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 1cc6954569c509c977634a8e1cdd52c5c55b2100
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108120"
---
# <a name="use-java-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Use o Java para gerenciar diretórios, arquivos e ACLs no Azure Data Lake Storage Gen2

Este artigo mostra como usar o Java para criar e gerenciar diretórios, arquivos e permissões em contas de armazenamento que têm o namespace hierárquico (HNS) habilitado. 

[Pacote (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)  |  [Exemplos](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)  |  [Referência](/java/api/overview/azure/storage-file-datalake-readme)  |  de API Mapeamento de Gen1 [para Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Enviar comentários](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Uma conta de armazenamento precisa ter o HNS (namespace hierárquico) habilitado. Siga [estas](../common/storage-account-create.md) instruções para criar um.

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

### <a name="connect-by-using-an-account-key"></a>Conectar-se usando uma chave de conta

Essa é a maneira mais fácil de se conectar a uma conta. 

Este exemplo cria uma instância de **DataLakeServiceClient** usando uma chave de conta.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithKey":::

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Conectar-se usando Azure Active Directory (Azure AD)

Você pode usar a [biblioteca de cliente de identidade do Azure para Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) para autenticar seu aplicativo com o Azure AD.

Este exemplo cria uma instância de **DataLakeServiceClient** usando uma ID do cliente, um segredo do cliente e uma ID de locatário.  Para obter esses valores, consulte [adquirir um token do Azure ad para autorizar solicitações de um aplicativo cliente](../common/storage-auth-aad-app.md).

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithAzureAD":::

> [!NOTE]
> Para obter mais exemplos, consulte a documentação da [biblioteca de cliente de identidade do Azure para Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) .


## <a name="create-a-container"></a>Criar um contêiner

Um contêiner atua como um sistema de arquivos para seus arquivos. Você pode criar um chamando o método **DataLakeServiceClient. createfilesystem** .

Este exemplo cria um contêiner chamado `my-file-system` . 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_CreateFileSystem":::

## <a name="create-a-directory"></a>Criar um diretório

Crie uma referência de diretório chamando o método **DataLakeFileSystemClient. CreateDirectory** .

Este exemplo adiciona um diretório chamado `my-directory` a um contêiner e, em seguida, adiciona um subdiretório chamado `my-subdirectory` . 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>Renomear ou mover um diretório

Renomeie ou mova um diretório chamando o método **DataLakeDirectoryClient. Rename** . Passe o caminho do diretório desejado de um parâmetro. 

Este exemplo renomeia um subdiretório para o nome `my-subdirectory-renamed` .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_RenameDirectory":::

Este exemplo move um diretório chamado `my-subdirectory-renamed` para um subdiretório de um diretório chamado `my-directory-2` . 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_MoveDirectory":::

## <a name="delete-a-directory"></a>Excluir um diretório

Exclua um diretório chamando o método **DataLakeDirectoryClient. deleteWithResponse** .

Este exemplo exclui um diretório chamado `my-directory`.   

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>Carregar um arquivo em um diretório

Primeiro, crie uma referência de arquivo no diretório de destino criando uma instância da classe **DataLakeFileClient** . Carregue um arquivo chamando o método **DataLakeFileClient. Append** . Certifique-se de concluir o carregamento chamando o método **DataLakeFileClient. FlushAsync** .

Este exemplo carrega um arquivo de texto em um diretório chamado `my-directory` .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_UploadFile":::

> [!TIP]
> Se o tamanho do arquivo for grande, seu código terá que fazer várias chamadas para o método **DataLakeFileClient. Append** . Em vez disso, considere usar o método **DataLakeFileClient. Uploadfromfile** . Dessa forma, você pode carregar o arquivo inteiro em uma única chamada. 
>
> Consulte a próxima seção para ver um exemplo.

## <a name="upload-a-large-file-to-a-directory"></a>Carregar um arquivo grande em um diretório

Use o método **DataLakeFileClient. Uploadfromfile** para carregar arquivos grandes sem precisar fazer várias chamadas para o método **DataLakeFileClient. Append** .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>Baixar de um diretório

Primeiro, crie uma instância de **DataLakeFileClient** que representa o arquivo que você deseja baixar. Use o método **DataLakeFileClient. Read** para ler o arquivo. Use qualquer API de processamento de arquivo .NET para salvar bytes do fluxo em um arquivo. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_DownloadFile":::

## <a name="list-directory-contents"></a>Listar conteúdo do diretório

Este exemplo, imprime os nomes de cada arquivo localizado em um diretório chamado `my-directory` .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/CRUD_DataLake.java" id="Snippet_ListFilesInDirectory":::

## <a name="manage-access-control-lists-acls"></a>Gerenciar listas de controle de acesso (ACLs)

Você pode obter, definir e atualizar as permissões de acesso de diretórios e arquivos.

> [!NOTE]
> Se você estiver usando Azure Active Directory (Azure AD) para autorizar o acesso, verifique se a entidade de segurança recebeu a [função de proprietário de dados do blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Para saber mais sobre como as permissões de ACL são aplicadas e os efeitos por alterá-las, confira [Controle de acesso no Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

### <a name="manage-a-directory-acl"></a>Gerenciar uma ACL de diretório

Este exemplo obtém e define a ACL de um diretório chamado `my-directory` . Este exemplo fornece as permissões de leitura, gravação e execução do usuário proprietário, fornece ao grupo proprietário somente permissões de leitura e execução e concede a todos os outros acesso de leitura.

> [!NOTE]
> Se seu aplicativo autorizar o acesso usando Azure Active Directory (Azure AD), verifique se a entidade de segurança que seu aplicativo usa para autorizar o acesso recebeu a [função de proprietário de dados do blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Para saber mais sobre como as permissões de ACL são aplicadas e os efeitos por alterá-las, confira [Controle de acesso no Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageDirectoryACLs":::

Você também pode obter e definir a ACL do diretório raiz de um contêiner. Para obter o diretório raiz, passe uma cadeia de caracteres vazia ( `""` ) para o método **DataLakeFileSystemClient. getDirectoryClient** .

### <a name="manage-a-file-acl"></a>Gerenciar uma ACL de arquivo

Este exemplo obtém e define a ACL de um arquivo chamado `upload-file.txt` . Este exemplo fornece as permissões de leitura, gravação e execução do usuário proprietário, fornece ao grupo proprietário somente permissões de leitura e execução e concede a todos os outros acesso de leitura.

> [!NOTE]
> Se seu aplicativo autorizar o acesso usando Azure Active Directory (Azure AD), verifique se a entidade de segurança que seu aplicativo usa para autorizar o acesso recebeu a [função de proprietário de dados do blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Para saber mais sobre como as permissões de ACL são aplicadas e os efeitos por alterá-las, confira [Controle de acesso no Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageFileACLs":::

### <a name="set-an-acl-recursively"></a>Definir uma ACL recursivamente

Você pode adicionar, atualizar e remover ACLs recursivamente nos itens filho existentes de um diretório pai sem precisar fazer essas alterações individualmente para cada item filho. Para obter mais informações, consulte [definir listas de controle de acesso (ACLs) recursivamente para Azure data Lake Storage Gen2](recursive-access-control-lists.md).

## <a name="see-also"></a>Confira também

* [Documentação de referência da API](/java/api/overview/azure/storage-file-datalake-readme)
* [Pacote (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
* [Amostras](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [Mapeamento de Gen1 para Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Problemas conhecidos](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Enviar comentários](https://github.com/Azure/azure-sdk-for-java/issues)