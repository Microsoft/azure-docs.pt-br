---
title: Usar JavaScript para gerenciar dados no Azure Data Lake Storage Gen2
description: Use o armazenamento do Azure Data Lake biblioteca de cliente para JavaScript para gerenciar diretórios e arquivos em contas de armazenamento com namespace hierárquico habilitado.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-js
ms.openlocfilehash: 8ce5df805ddce6cdb52e4225bb77e2d8dfa9b9b0
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100650160"
---
# <a name="use-javascript-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Usar o JavaScript para gerenciar diretórios e arquivos no Azure Data Lake Storage Gen2

Este artigo mostra como usar o JavaScript para criar e gerenciar diretórios e arquivos em contas de armazenamento que têm um namespace hierárquico.

Para saber mais sobre como obter, definir e atualizar as listas de controle de acesso (ACL) de diretórios e arquivos, consulte [usar JavaScript para gerenciar ACLs no Azure data Lake Storage Gen2](data-lake-storage-acl-javascript.md).

[Pacote (Gerenciador de pacotes do nó)](https://www.npmjs.com/package/@azure/storage-file-datalake)  |  [Exemplos](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)  |  [Enviar comentários](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

- Uma conta de armazenamento que tem o namespace hierárquico habilitado. Siga [estas](create-data-lake-storage-account.md) instruções para criar um.

- Se você estiver usando esse pacote em um aplicativo Node.js, precisará Node.js 8.0.0 ou superior.

## <a name="set-up-your-project"></a>Configurar o seu projeto

Instale Data Lake biblioteca de cliente para JavaScript abrindo uma janela de terminal e, em seguida, digitando o comando a seguir.

```javascript
npm install @azure/storage-file-datalake
```

Importe o `storage-file-datalake` pacote colocando essa instrução na parte superior do seu arquivo de código. 

```javascript
const AzureStorageDataLake = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>Conectar à conta 

Para usar os trechos de código neste artigo, você precisará criar uma instância de **DataLakeServiceClient** que representa a conta de armazenamento. 

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
> Esse método de autorização funciona apenas para aplicativos Node.js. Se você planeja executar seu código em um navegador, você pode autorizar usando Azure Active Directory (AD do Azure).

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Conectar-se usando Azure Active Directory (Azure AD)

Você pode usar a [biblioteca de cliente de identidade do Azure para JS](https://www.npmjs.com/package/@azure/identity) para autenticar seu aplicativo com o Azure AD.

Este exemplo cria uma instância de **DataLakeServiceClient** usando uma ID do cliente, um segredo do cliente e uma ID de locatário.  Para obter esses valores, consulte [adquirir um token do Azure ad para autorizar solicitações de um aplicativo cliente](../common/storage-auth-aad-app.md).

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

## <a name="create-a-container"></a>Criar um contêiner

Um contêiner atua como um sistema de arquivos para seus arquivos. Você pode criar uma obtendo uma instância de **FileSystemClient** e, em seguida, chamando o método **FileSystemClient. Create** .

Este exemplo cria um contêiner chamado `my-file-system` . 

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";
  
  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();
        
}
```

## <a name="create-a-directory"></a>Criar um diretório

Crie uma referência de diretório obtendo uma instância de **DirectoryClient** e, em seguida, chamando o método **DirectoryClient. Create** .

Este exemplo adiciona um diretório chamado `my-directory` a um contêiner. 

```javascript
async function CreateDirectory(fileSystemClient) {
   
  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  
  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>Renomear ou mover um diretório

Renomeie ou mova um diretório chamando o método **DirectoryClient. Rename** . Passe o caminho do diretório desejado de um parâmetro. 

Este exemplo renomeia um subdiretório para o nome `my-directory-renamed` .

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.move("my-directory-renamed");

}
```

Este exemplo move um diretório chamado `my-directory-renamed` para um subdiretório de um diretório chamado `my-directory-2` . 

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed"); 
  await directoryClient.move("my-directory-2/my-directory-renamed");      

}
```

## <a name="delete-a-directory"></a>Excluir um diretório

Exclua um diretório chamando o método **DirectoryClient. Delete** .

Este exemplo exclui um diretório chamado `my-directory`.   

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.delete();

}
```

## <a name="upload-a-file-to-a-directory"></a>Carregar um arquivo em um diretório

Primeiro, leia um arquivo. Este exemplo usa o `fs` módulo Node.js. Em seguida, crie uma referência de arquivo no diretório de destino criando uma instância de **fileclient** e, em seguida, chamando o método **fileclient. Create** . Carregue um arquivo chamando o método **fileclient. Append** . Certifique-se de concluir o carregamento chamando o método **fileclient. Flush** .

Este exemplo carrega um arquivo de texto em um diretório chamado `my-directory` . '

```javascript
async function UploadFile(fileSystemClient) {

  const fs = require('fs') 

  var content = "";
  
  fs.readFile('mytestfile.txt', (err, data) => { 
      if (err) throw err; 

      content = data.toString();

  }) 
  
  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");
  await fileClient.create();
  await fileClient.append(content, 0, content.length);
  await fileClient.flush(content.length);

}
```

## <a name="download-from-a-directory"></a>Baixar de um diretório

Primeiro, crie uma instância de **FileSystemClient** que representa o arquivo que você deseja baixar. Use o método **FileSystemClient. Read** para ler o arquivo. Em seguida, grave o arquivo. Este exemplo usa o `fs` módulo Node.js para fazer isso. 

> [!NOTE]
> Esse método de baixar um arquivo funciona apenas para aplicativos Node.js. Se você planeja executar seu código em um navegador, consulte o arquivo de Leiame [biblioteca de cliente do data Lake do armazenamento do Azure para JavaScript](https://www.npmjs.com/package/@azure/storage-file-datalake) para obter um exemplo de como fazer isso em um navegador.

```javascript
async function DownloadFile(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");

  const downloadResponse = await fileClient.read();

  const downloaded = await streamToString(downloadResponse.readableStreamBody);
 
  async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", (data) => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
  }   
  
  const fs = require('fs');

  fs.writeFile('mytestfiledownloaded.txt', downloaded, (err) => {
    if (err) throw err;
  });
}

```

## <a name="list-directory-contents"></a>Listar conteúdo do diretório

Este exemplo, imprime os nomes de cada diretório e arquivo que está localizado em um diretório chamado `my-directory` .

```javascript
async function ListFilesInDirectory(fileSystemClient) {
  
  let i = 1;

  let iter = await fileSystemClient.listPaths({path: "my-directory", recursive: true});

  for await (const path of iter) {
    
    console.log(`Path ${i++}: ${path.name}, is directory: ${path.isDirectory}`);
  }

}
```

## <a name="see-also"></a>Confira também

- [Pacote (Gerenciador de Pacotes do Node)](https://www.npmjs.com/package/@azure/storage-file-datalake)
- [Amostras](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
- [Enviar comentários](https://github.com/Azure/azure-sdk-for-java/issues)