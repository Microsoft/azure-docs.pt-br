---
title: Como usar o armazenamento de Blobs do Node.js | Microsoft Docs
description: "Armazene dados não estruturados na nuvem com o armazenamento de blobs do Azure (armazenamento de objeto)."
services: storage
documentationcenter: nodejs
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 8b0df222-1ca8-4967-8248-6d6d720947b8
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: e52f38d5fb3c100e4275032f9a2a1234961c672b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-blob-storage-from-nodejs"></a>Como usar o armazenamento de Blob do Node.js
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>Visão geral
Este artigo mostra como executar cenários comuns usando o Armazenamento de Blobs. Os exemplos são escritos usando a API do Node.js. Os cenários abordados incluem como carregar, listar, baixar e excluir blobs.

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Criar um aplicativo do Node.js
Para obter instruções sobre como criar um aplicativo do Node.js, confira [Criar um aplicativo Web do Node.js no Serviço de Aplicativo do Azure], [Criar e implantar um aplicativo Node.js para um Serviço de Nuvem do Azure](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) usando o Windows PowerShell ou [Criar e implantar um aplicativo Web Node.js no Azure usando a Matriz da Web](https://www.microsoft.com/web/webmatrix/).

## <a name="configure-your-application-to-access-storage"></a>Configurar seu aplicativo para acessar o armazenamento
Para usar o armazenamento do Azure, você precisa do SDK de Armazenamento do Azure para Node.js, que inclui um conjunto de bibliotecas convenientes que se comunicam com os serviços REST do armazenamento.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Usar o NPM (gerenciador de pacotes de nós) para obter o pacote
1. Use uma interface de linha de comando, como **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix), para navegar até a pasta onde você criou o aplicativo de exemplo.
2. Digite **npm install azure-storage** na janela de comando. A saída do comando é semelhante ao exemplo de código a seguir.

        azure-storage@0.5.0 node_modules\azure-storage
        +-- extend@1.2.1
        +-- xmlbuilder@0.4.3
        +-- mime@1.2.11
        +-- node-uuid@1.4.3
        +-- validator@3.22.2
        +-- underscore@1.4.4
        +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
        +-- xml2js@0.2.7 (sax@0.5.2)
        +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
3. Você pode executar manualmente o comando **ls** para verificar se uma pasta **node\_modules** foi criada. Dentro dessa pasta, localize o pacote **azure-storage** , que contém as bibliotecas necessárias para acessar o armazenamento.

### <a name="import-the-package"></a>Importar o pacote
Usando o Bloco de Notas ou outro editor de texto, adicione o seguinte à parte superior do arquivo **server.js** do aplicativo no qual você pretende usar o armazenamento:

```nodejs
var azure = require('azure-storage');
```

## <a name="set-up-an-azure-storage-connection"></a>Configurar uma conexão do Armazenamento do Azure
O módulo do Azure lerá as variáveis do ambiente `AZURE_STORAGE_ACCOUNT` e `AZURE_STORAGE_ACCESS_KEY` ou `AZURE_STORAGE_CONNECTION_STRING`, a fim de obter as informações necessárias para se conectar à sua conta de armazenamento do Azure. Se essas variáveis de ambiente não estiverem definidas, você deverá especificar as informações da conta chamando **createBlobService**.

## <a name="create-a-container"></a>Criar um contêiner
O objeto **serviço Blob** permite que você trabalhe com contêineres e blobs. O código a seguir cria um objeto **BlobService** . Adicione o seguinte próximo à parte superior do **server.js**:

```nodejs
var blobSvc = azure.createBlobService();
```

> [!NOTE]
> Você pode acessar um blob anonimamente usando **createBlobServiceAnonymous** e fornecendo o endereço do host. Por exemplo, use `var blobSvc = azure.createBlobServiceAnonymous('https://myblob.blob.core.windows.net/');`.
>
>

[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

Para criar um novo contêiner, use **createContainerIfNotExists**. O exemplo de código a seguir cria um novo contêiner denominado 'mycontainer':

```nodejs
blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
    if(!error){
      // Container exists and is private
    }
});
```

Se o contêiner tiver sido criado recentemente, `result.created` será verdadeiro. Se o contêiner já existir, `result.created` será falso. `response` contém informações sobre a operação, incluindo as informações de Etag do contêiner.

### <a name="container-security"></a>Segurança do contêiner
Por padrão, novos contêineres são privados e não podem ser acessados ​​anonimamente. Para tornar o contêiner público, de modo que seja possível acessá-lo anonimamente, você poderá definir o nível de acesso do contêiner como **blob** ou **contêiner**.

* **blob** – permite o acesso anônimo de leitura ao conteúdo e aos metadados do blob dentro desse contêiner, mas não aos metadados do contêiner, como a listagem de todos os blobs de um contêiner
* **contêiner** – permite o acesso anônimo de leitura ao conteúdo e aos metadados do blob e também aos metadados do contêiner

O exemplo de código a seguir demonstra a configuração do nível de acesso para **blob**:

```nodejs
blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
    if(!error){
      // Container exists and allows
      // anonymous read access to blob
      // content and metadata within this container
    }
});
```

Como alternativa, você poderá modificar o nível de acesso de um contêiner, usando **setContainerAcl** para especificar o nível de acesso. O exemplo de código a seguir altera o nível de acesso para contêiner:

```nodejs
blobSvc.setContainerAcl('mycontainer', null /* signedIdentifiers */, {publicAccessLevel : 'container'} /* publicAccessLevel*/, function(error, result, response){
  if(!error){
    // Container access level set to 'container'
  }
});
```

O resultado contém informações sobre a operação, incluindo o **ETag** atual do contêiner.

### <a name="filters"></a>Filtros
Você pode aplicar operações de filtragem opcionais às operações executadas usando o **BlobService**. As operações de filtragem podem incluir registro em log, repetição automática, etc. Os filtros são objetos que implementam um método com a assinatura:

```nodejs
function handle (requestOptions, next)
```

Após fazer seu pré-processamento nas opções de solicitação, o método precisará chamar "next", passando um retorno de chamada com a assinatura abaixo:

```nodejs
function (returnObject, finalCallback, next)
```

Nesse retorno de chamada, e após processar o returnObject (a resposta da solicitação ao servidor), o retorno de chamada precisará invocar next, se ele existir, para continuar processando outros filtros ou simplesmente invocar finalCallback para terminar a invocação de serviço.

Dois filtros que implementam a lógica de repetição estão incluídos no SDK do Azure para Node.js, **ExponentialRetryPolicyFilter** e **LinearRetryPolicyFilter**. O seguinte código cria um objeto **BlobService** que usa **ExponentialRetryPolicyFilter**:

```nodejs
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var blobSvc = azure.createBlobService().withFilter(retryOperations);
```

## <a name="upload-a-blob-into-a-container"></a>Carregar um blob em um contêiner
Há três tipos de blobs: blob de blocos, blob de páginas e blob de anexo. Blobs de bloco permitem que você carregue grandes volumes de dados com mais eficiência. Blobs de anexo otimizados para operações de acréscimo. Blobs de página são otimizados para operações de leitura/gravação. Para saber mais, confira [Noções básicas sobre Blobs de bloco, Blobs de acréscimo e Blobs de página](http://msdn.microsoft.com/library/azure/ee691964.aspx).

### <a name="block-blobs"></a>Blobs de bloco
Para transferir dados para um blob de bloco, use o seguinte:

* **createBlockBlobFromLocalFile** – cria um novo blob de blocos e carrega o conteúdo de um arquivo
* **createBlockBlobFromStream** – cria um novo blob de blocos e carrega o conteúdo de um fluxo
* **createBlockBlobFromText** – cria um novo blob de blocos e carrega o conteúdo de uma cadeia de caracteres
* **createWriteStreamToBlockBlob** – fornece um fluxo de gravação para um blob de blocos

O exemplo de código a seguir carrega o conteúdo do arquivo **test.txt** em **myblob**.

```nodejs
blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
  if(!error){
    // file uploaded
  }
});
```

O `result` retornado por esses métodos contém informações sobre a operação, como o **ETag** do blob.

### <a name="append-blobs"></a>Blobs de acréscimo
Para carregar dados para um novo blob de acréscimo, use o seguinte:

* **createAppendBlobFromLocalFile** – cria um novo blob de acréscimo e carrega o conteúdo de um arquivo
* **createAppendBlobFromStream** – cria um novo blob de acréscimo e carrega o conteúdo de uma transmissão
* **createAppendBlobFromText** – cria um novo blob de acréscimo e carrega o conteúdo de uma cadeia de caracteres
* **createWriteStreamToNewAppendBlob** – cria um novo blob de acréscimo e, então, fornece uma transmissão para gravar nela

O exemplo de código a seguir carrega o conteúdo do arquivo **test.txt** em **myappendblob**.

```nodejs
blobSvc.createAppendBlobFromLocalFile('mycontainer', 'myappendblob', 'test.txt', function(error, result, response){
  if(!error){
    // file uploaded
  }
});
```

Para acrescentar um bloco em um blob de acréscimo existente, use o seguinte:

* **appendFromLocalFile** – acrescentar o conteúdo de um arquivo para um blob de acréscimo
* **appendFromStream** – acrescentar o conteúdo de uma transmissão para um blob de acréscimo existente
* **appendFromText** – acrescentar o conteúdo de uma cadeia de caracteres para um blob de acréscimo existente
* **appendBlockFromStream** – acrescentar o conteúdo de uma transmissão para um blob de acréscimo existente
* **appendBlockFromText** – acrescentar o conteúdo de uma cadeia de caracteres para um blob de acréscimo existente

> [!NOTE]
> As APIs appendFromXXX causarão uma falha rápida da validação do lado do cliente para evitar chamadas de servidor desnecessárias. appendBlockFromXXX não fará isso.
>
>

O exemplo de código a seguir carrega o conteúdo do arquivo **test.txt** em **myappendblob**.

```nodejs
blobSvc.appendFromText('mycontainer', 'myappendblob', 'text to be appended', function(error, result, response){
  if(!error){
    // text appended
  }
});
```

### <a name="page-blobs"></a>Blobs de página
Para carregar dados para um blob de página, use o seguinte:

* **createPageBlob** – cria um novo blob de páginas de um comprimento específico
* **createPageBlobFromLocalFile** – cria um novo blob de páginas e carrega o conteúdo de um arquivo
* **createPageBlobFromStream** – cria um novo blob de páginas e carrega o conteúdo de um fluxo
* **createWriteStreamToExistingPageBlob** – fornece um fluxo de gravação para um blob de páginas
* **createWriteStreamToNewPageBlob** – cria um novo blob de páginas e, então, fornece uma transmissão para gravar nela

O exemplo de código a seguir carrega o conteúdo do arquivo **test.txt** em **mypageblob**.

```nodejs
blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
  if(!error){
    // file uploaded
  }
});
```

> [!NOTE]
> Blobs de página consistem em 'páginas’ de 512 bytes. Você receberá um erro ao carregar dados com um tamanho que não seja um múltiplo de 512.
>
>

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner
Para listar os blobs em um contêiner, use o método **listBlobsSegmented** . Se você quiser retornar blobs com um prefixo específico, use **listBlobsSegmentedWithPrefix**.

```nodejs
blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
  if(!error){
      // result.entries contains the entries
      // If not all blobs were returned, result.continuationToken has the continuation token.
  }
});
```

O `result` contém uma coleção de `entries`, que é uma matriz de objetos que descrevem cada blob. Se todos os blobs não puderem ser retornados, o `result` também fornecerá um `continuationToken`, que poderá ser usado como o segundo parâmetro para recuperação de entradas adicionais.

## <a name="download-blobs"></a>Baixar blobs
Para baixar dados de um blob, use o seguinte:

* **getBlobToLocalFile** – grava o conteúdo do blob em um arquivo
* **getBlobToStream** – grava o conteúdo do blob em um fluxo
* **getBlobToText** – grava o conteúdo do blob em uma cadeia de caracteres
* **createReadStream** – fornece um fluxo de leitura a partir do blob

O exemplo de código a seguir demonstra como usar **getBlobToStream** para baixar o conteúdo do blob **myblob** e armazená-lo no arquivo **output.txt** usando um fluxo:

```nodejs
var fs = require('fs');
blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
  if(!error){
    // blob retrieved
  }
});
```

O `result` contém informações sobre o blob, incluindo informações do **ETag** .

## <a name="delete-a-blob"></a>Excluir um blob
Finalmente, para excluir um blob, chame **deleteBlob**. O exemplo de código a seguir exclui o blob denominado **myblob**.

```nodejs
blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
  if(!error){
    // Blob has been deleted
  }
});
```

## <a name="concurrent-access"></a>Acesso simultâneo
Para suportar o acesso simultâneo a uma blob por meio de vários clientes ou várias instâncias do processo, você pode usar **ETags** ou **concessões**.

* **Etag** – fornece uma maneira de detectar se o blob ou o contêiner foi modificado por outro processo
* **Concessão** – fornece acesso exclusivo e renovável para a gravação ou a exclusão de um blob por determinado período

### <a name="etag"></a>ETag
Use ETags se você precisar permitir que vários clientes ou instâncias realizem gravações no Blob de blocos ou Blob de páginas simultaneamente. O ETag permite determinar se o contêiner ou o blob foi modificado desde que foi criado ou lido, o que permite evitar a substituição de alterações aplicadas por outro cliente ou processo.

Você pode definir condições de ETag usando o parâmetro `options.accessConditions` opcional. O exemplo de código a seguir só carregará o arquivo **test.txt** se o blob já existir e tiver o valor de ETag contido por `etagToMatch`.

```nodejs
blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { EtagMatch: etagToMatch} }, function(error, result, response){
    if(!error){
    // file uploaded
  }
});
```

O padrão geral ao usar ETags é:

1. Obter a ETag como o resultado de uma operação de criação, lista ou obtenção.
2. Executar uma ação, verificando se o valor da ETag não foi modificado.

Se o valor tiver sido modificado, isso indica que outro cliente ou instância modificou o blob ou o contêiner desde que você obteve o valor do ETag.

### <a name="lease"></a>Concessão
Você pode adquirir uma nova concessão usando o método **acquireLease** especificando o blob ou o contêiner para o qual você deseja obter uma concessão. Por exemplo, o código a seguir adquire uma concessão em **myblob**.

```nodejs
blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
  if(!error) {
    console.log('leaseId: ' + result.id);
  }
});
```

As operações posteriores em **myblob** devem fornecer o parâmetro `options.leaseId`. A ID de concessão é retornada de **acquireLease** como `result.id`.

> [!NOTE]
> Por padrão, a duração da concessão é infinita. Você pode especificar uma duração não infinita (entre 15 e 60 segundos) fornecendo o parâmetro `options.leaseDuration` .
>
>

Para remover uma concessão, use **releaseLease**. Para interromper uma concessão, mas evitar que outras pessoas obtenham uma nova concessão até a expiração da duração original, use **breakLease**.

## <a name="work-with-shared-access-signatures"></a>Trabalhar com assinaturas de acesso compartilhado
As assinaturas de acesso compartilhado (SAS) são uma forma segura de fornecer acesso granular a blobs e contêiner sem fornecer o nome ou as chaves da conta de armazenamento. As assinaturas de acesso compartilhado são frequentemente usadas para fornecer acesso limitado aos seus dados, por exemplo, ao permitir que um aplicativo móvel acesse os blobs.

> [!NOTE]
> Embora você também possa permitir o acesso anônimo aos blobs, a assinatura de acesso compartilhado permite que você ofereça um acesso mais controlado, pois é necessário gerar a SAS.
>
>

Um aplicativo confiável, como um serviço baseado em nuvem, gera uma assinatura de acesso compartilhado usando **generateSharedAccessSignature** de **BlobService** e o oferece a um aplicativo não confiável ou semiconfiável, como um aplicativo móvel. As assinaturas de acesso compartilhado são geradas usando uma política, que descreve as datas de início e de término da validade das assinaturas de acesso compartilhado, além do nível de acesso concedido ao titular das assinaturas.

O exemplo de código a seguir gera uma nova política de acesso compartilhado que permite ao titular das assinaturas de acesso compartilhado executar operações de leitura no blob **myblob** e expira 100 minutos após a hora de sua criação.

```nodejs
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
    Start: startDate,
    Expiry: expiryDate
  },
};

var blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', 'myblob', sharedAccessPolicy);
var host = blobSvc.host;
```

Observe que também devem ser fornecidas as informações do host, já que elas são necessárias quando o titular das assinaturas de acesso compartilhado tenta acessar o contêiner.

Em seguida, o aplicativo cliente usa as assinaturas de acesso compartilhado com **BlobServiceWithSAS** para executar operações no blob. O seguinte obtém informações sobre **myblob**.

```nodejs
var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
  if(!error) {
    // retrieved info
  }
});
```

Como as assinaturas de acesso compartilhado foram geradas com acesso somente leitura, se for feita uma tentativa de modificar o blob, será retornado um erro.

### <a name="access-control-lists"></a>Listas de controle de acesso
Você também pode usar uma lista de controle de acesso (ACL) para definir a política de acesso das SAS. Isso será útil se você quiser permitir que vários clientes acessem um contêiner, mas com políticas de acesso diferentes para cada cliente.

Uma ACL é implementada através de um conjunto de políticas de acesso, com uma ID associada a cada política. O exemplo de código a seguir define duas políticas, uma para "user1" e outra para 'user2':

```nodejs
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.BlobUtilities.SharedAccessPermissions.WRITE,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

O exemplo de código a seguir obtém a ACL atual para **mycontainer** e adiciona as novas políticas usando **setBlobAcl**. Essa abordagem permite:

```nodejs
var extend = require('extend');
blobSvc.getBlobAcl('mycontainer', function(error, result, response) {
  if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    blobSvc.setBlobAcl('mycontainer', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

Após a definição da ACL, você poderá criar assinaturas de acesso compartilhado com base na ID de uma política. O exemplo de código a seguir cria novas assinaturas de acesso compartilhado para ‘user2’:

```nodejs
blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });
```

## <a name="next-steps"></a>Próximas etapas
Para saber mais, consulte os recursos a seguir.

* [SDK de Armazenamento do Azure para Referência de API de Nó][SDK de Armazenamento do Azure para Referência de API de Nó]  
* [Blog da equipe de Armazenamento do Azure][Blog da equipe de Armazenamento do Azure]  
* Repositório [Microsoft Azure Storage SDK for Node.js][Azure Storage SDK for Node] (SDK do Armazenamento do Microsoft Azure para Node.js) no GitHub  
* [Centro de Desenvolvedores do Node.js](https://azure.microsoft.com/develop/nodejs/)  
* [Transferir dados com o utilitário de linha de comando AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)  

[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node  

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/  
[Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx  
[Azure portal]: https://portal.azure.com  
[Criar e implantar um aplicativo Node.js em um Serviço de Nuvem do Azure](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)  
[Blog da equipe de Armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/  
[SDK de Armazenamento do Azure para Referência de API de Nó]: http://dl.windowsazure.com/nodestoragedocs/index.html  
