---
title: Usar JavaScript (Node.js) para definir ACLs em Azure Data Lake Storage Gen2
description: Use o armazenamento do Azure Data Lake biblioteca de cliente para JavaScript para gerenciar listas de controle de acesso (ACL) em contas de armazenamento que têm o namespace hierárquico (HNS) habilitado.
author: normesta
ms.service: storage
ms.date: 03/19/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-js
ms.openlocfilehash: 21b4977102a484d8a3a680450a9cb6f77c7e3fbd
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722745"
---
# <a name="use-javascript-sdk-in-nodejs-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Usar o SDK do JavaScript no Node.js para gerenciar ACLs no Azure Data Lake Storage Gen2

Este artigo mostra como usar Node.js para obter, definir e atualizar as listas de controle de acesso de diretórios e arquivos. 

[Pacote (Gerenciador de pacotes do nó)](https://www.npmjs.com/package/@azure/storage-file-datalake)  |  [Exemplos](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)  |  [Enviar comentários](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

- Uma conta de armazenamento precisa ter o HNS (namespace hierárquico) habilitado. Siga [estas](create-data-lake-storage-account.md) instruções para criar um.

- CLI do Azure versão `2.6.0` ou superior

- Uma das seguintes permissões de segurança:

  - Uma [entidade de segurança](../../role-based-access-control/overview.md#security-principal) de Azure Active Directory (AD) provisionada à qual foi atribuída a função de proprietário de dados do [blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) no escopo do contêiner de destino, grupo de recursos pai ou assinatura.  

  - Usuário proprietário do contêiner ou diretório de destino ao qual você planeja aplicar as configurações de ACL. Para definir ACLs recursivamente, isso inclui todos os itens filho no contêiner ou diretório de destino.
  
  - Chave da conta de armazenamento..

## <a name="set-up-your-project"></a>Configurar o seu projeto

Instale Data Lake biblioteca de cliente para JavaScript abrindo uma janela de terminal e, em seguida, digitando o comando a seguir.

```javascript
npm install @azure/storage-file-datalake
```

Importe o `storage-file-datalake` pacote colocando essa instrução na parte superior do seu arquivo de código. 

```javascript
const {
AzureStorageDataLake,
DataLakeServiceClient,
StorageSharedKeyCredential
} = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>Conectar à conta

Para usar os trechos de código neste artigo, você precisará criar uma instância de **DataLakeServiceClient** que representa a conta de armazenamento. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Conectar usando o Azure Active Directory (AD)

> [!NOTE]
> Se você estiver usando Azure Active Directory (Azure AD) para autorizar o acesso, verifique se a entidade de segurança recebeu a [função de proprietário de dados do blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Para saber mais sobre como as permissões de ACL são aplicadas e os efeitos de alterá-las, consulte  [modelo de controle de acesso em Azure data Lake Storage Gen2](./data-lake-storage-access-control-model.md).

Você pode usar a [biblioteca de cliente de identidade do Azure para JS](https://www.npmjs.com/package/@azure/identity) para autenticar seu aplicativo com o Azure AD.

Obtenha uma ID do cliente, um segredo do cliente e uma ID de locatário. Para fazer isso, consulte [adquirir um token do Azure ad para autorizar solicitações de um aplicativo cliente](../common/storage-auth-aad-app.md). Como parte desse processo, você precisará atribuir uma das seguintes funções do Azure [RBAC (controle de acesso baseado em função](../../role-based-access-control/overview.md) do Azure) à sua entidade de segurança. 

|Função|Funcionalidade de configuração de ACL|
|--|--|
|[Proprietário de Dados do Blob de Armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Todos os diretórios e arquivos na conta.|
|[Colaborador de dados de blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Somente diretórios e arquivos de propriedade da entidade de segurança.|

Este exemplo cria uma instância de **DataLakeServiceClient** usando uma ID do cliente, um segredo do cliente e uma ID de locatário.  

```javascript
function GetDataLakeServiceClientAD(accountName, clientID, clientSecret, tenantID) {

  const credential = new ClientSecretCredential(tenantID, clientID, clientSecret);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, credential);

  return datalakeServiceClient;             
}
```

> [!NOTE]
> Para obter mais exemplos, consulte a documentação da [biblioteca de cliente de identidade do Azure para JS](https://www.npmjs.com/package/@azure/identity) .

### <a name="connect-by-using-an-account-key"></a>Conectar-se usando uma chave de conta

Essa é a maneira mais fácil de se conectar a uma conta. 

Este exemplo cria uma instância de **DataLakeServiceClient** usando uma chave de conta.

```javascript

function GetDataLakeServiceClient(accountName, accountKey) {

  const sharedKeyCredential = 
     new StorageSharedKeyCredential(accountName, accountKey);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, sharedKeyCredential);

  return datalakeServiceClient;             
}      

```

> [!NOTE]
> Esse método de autorização funciona apenas para aplicativos Node.js. Se você planeja executar seu código em um navegador, você pode autorizar usando Azure Active Directory (AD).

## <a name="get-and-set-a-directory-acl"></a>Obter e definir uma ACL de diretório

Este exemplo obtém e define a ACL de um diretório chamado `my-directory` . Este exemplo fornece as permissões de leitura, gravação e execução do usuário proprietário, fornece ao grupo proprietário somente permissões de leitura e execução e concede a todos os outros acesso de leitura.

> [!NOTE]
> Se seu aplicativo autorizar o acesso usando Azure Active Directory (Azure AD), verifique se a entidade de segurança que seu aplicativo usa para autorizar o acesso recebeu a [função de proprietário de dados do blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Para saber mais sobre como as permissões de ACL são aplicadas e os efeitos por alterá-las, confira [Controle de acesso no Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

```javascript
async function ManageDirectoryACLs(fileSystemClient) {

    const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
    const permissions = await directoryClient.getAccessControl();

    console.log(permissions.acl);

    const acl = [
    {
      accessControlType: "user",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: true
      }
    },
    {
      accessControlType: "group",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: false,
        execute: true
      }
    },
    {
      accessControlType: "other",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: false
      }

    }

  ];

  await directoryClient.setAccessControl(acl);
}
```

Você também pode obter e definir a ACL do diretório raiz de um contêiner. Para obter o diretório raiz, passe uma cadeia de caracteres vazia ( `/` ) para o método **DataLakeFileSystemClient. getDirectoryClient** .

### <a name="get-and-set-a-file-acl"></a>Obter e definir uma ACL de arquivo

Este exemplo obtém e define a ACL de um arquivo chamado `upload-file.txt` . Este exemplo fornece as permissões de leitura, gravação e execução do usuário proprietário, fornece ao grupo proprietário somente permissões de leitura e execução e concede a todos os outros acesso de leitura.

> [!NOTE]
> Se seu aplicativo autorizar o acesso usando Azure Active Directory (Azure AD), verifique se a entidade de segurança que seu aplicativo usa para autorizar o acesso recebeu a [função de proprietário de dados do blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Para saber mais sobre como as permissões de ACL são aplicadas e os efeitos por alterá-las, confira [Controle de acesso no Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

```javascript
async function ManageFileACLs(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt"); 
  const permissions = await fileClient.getAccessControl();

  console.log(permissions.acl);

  const acl = [
  {
    accessControlType: "user",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: true
    }
  },
  {
    accessControlType: "group",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: false,
      execute: true
    }
  },
  {
    accessControlType: "other",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: false
    }

  }

];

await fileClient.setAccessControl(acl);        
}
```

## <a name="see-also"></a>Veja também

- [Pacote (Gerenciador de Pacotes do Node)](https://www.npmjs.com/package/@azure/storage-file-datalake)
- [Amostras](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
- [Envie comentários](https://github.com/Azure/azure-sdk-for-java/issues)
- [Modelo de controle de acesso no Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [ACLs (listas de controle de acesso) no Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)