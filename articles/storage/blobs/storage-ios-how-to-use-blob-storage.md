---
title: Como usar o armazenamento de objeto (Blob) do iOS - Azure | Microsoft Docs
description: Armazene dados não estruturados na nuvem com o armazenamento de blobs do Azure (armazenamento de objeto).
services: storage
author: mhopkins-msft
ms.service: storage
ms.devlang: objective-c
ms.topic: article
ms.date: 11/20/2018
ms.author: mhopkins
ms.reviewer: seguler
ms.subservice: blobs
ms.openlocfilehash: 2d44dabe68900aa45b076f44cc38ed85d6938afa
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249942"
---
# <a name="how-to-use-blob-storage-from-ios"></a>Como usar o armazenamento de Blob no iOS

Este artigo mostra como executar cenários comuns usando o Armazenamento de Blobs do Microsoft Azure. Os exemplos são escritos em Objective-C e usam a [Azure Storage Client Library for iOS](https://github.com/Azure/azure-storage-ios)(Biblioteca do Cliente de Armazenamento do Azure para iOS). Os cenários abrangidos incluem carregar, listar, baixar e excluir blobs. Para obter mais informações sobre blobs, consulte a seção [Próximas etapas](#next-steps) . Você também pode baixar o [aplicativo de exemplo](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) para ver rapidamente o uso do Armazenamento do Azure em um aplicativo do iOS.

Para saber mais sobre armazenamento de Blobs, consulte [Introdução ao armazenamento de Blobs do Azure](storage-blobs-introduction.md).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Importar a biblioteca do iOS de Armazenamento do Azure para seu aplicativo
Você pode importar a biblioteca do iOS de Armazenamento do Azure para seu aplicativo usando o [CocoaPod para Armazenamento do Azure](https://cocoapods.org/pods/AZSClient) ou importando o arquivo **Framework** . O CocoaPod é a maneira recomendada, pois ele facilita a integração à biblioteca, enquanto a importação do arquivo de estrutura é menos intrusiva para seu projeto existente.

Para usar essa biblioteca, você precisará do seguinte:
- iOS 8+
- Xcode 7+

## <a name="cocoapod"></a>CocoaPod
1. Se você ainda não fez isso, [instale os CocoaPods](https://guides.cocoapods.org/using/getting-started.html#toc_3) em seu computador abrindo uma janela de terminal e executando o seguinte comando
    
    ```shell   
    sudo gem install cocoapods
    ```

2. Em seguida, no diretório do projeto (o diretório que contém o arquivo .xcodeproj), crie um novo arquivo chamado _Podfile_ (sem extensão de arquivo). Adicione o seguinte a _Podfile_ e salve.

    ```ruby
    platform :ios, '8.0'

    target 'TargetName' do
      pod 'AZSClient'
    end
    ```

3. Na janela do terminal, navegue até o diretório do projeto e execute o seguinte comando

    ```shell    
    pod install
    ```

4. Se o seu .xcodeproj estiver aberto no Xcode, feche-o. No diretório do projeto, abra o arquivo de projeto recém-criado que terá a extensão .xcworkspace. Esse é o arquivo no qual você trabalhará a partir de agora.

## <a name="framework"></a>Framework
A outra maneira de usar a biblioteca é criar a estrutura manualmente:

1. Primeiro, baixe ou clone o [repositório azure-storage-ios](https://github.com/azure/azure-storage-ios).
2. Vá para *azure-storage-ios* -> *Lib* -> *Biblioteca de cliente de Armazenamento do Azure* e abra `AZSClient.xcodeproj` no Xcode.
3. No canto superior esquerdo do Xcode, altere o esquema ativo de "Biblioteca de Cliente de Armazenamento do Azure" para "Estrutura".
4. Compile o projeto (⌘+B). Isso criará um arquivo `AZSClient.framework` na Área de Trabalho.

Você pode importar o arquivo de estrutura em seu aplicativo fazendo o seguinte:

1. Crie um novo projeto ou abra um projeto existente no Xcode.
2. Arraste e solte o `AZSClient.framework` em seu navegador de projeto Xcode.
3. Selecione *Copiar itens se necessário* e clique em *Concluir*.
4. Clique no projeto no painel de navegação esquerdo e clique na guia *Geral* na parte superior do editor de projeto.
5. Na seção *Estruturas e Bibliotecas Vinculadas* , clique no botão Adicionar (+).
6. Na lista de bibliotecas já fornecida, pesquise `libxml2.2.tbd` e adicione-a ao projeto.

## <a name="import-the-library"></a>Importar a Biblioteca 
```objc
// Include the following import statement to use blob APIs.
#import <AZSClient/AZSClient.h>
```

Se você estiver usando o Swift, será necessário criar um cabeçalho de ponte e importar \<AZSClient/AZSClient. h > lá:

1. Crie um arquivo de cabeçalho `Bridging-Header.h` e adicione a instrução de importação acima.
2. Vá para a guia *Configurações de Build* e pesquise por *Cabeçalho Ponte do Objective-C*.
3. Clique duas vezes no campo de *Cabeçalho Ponte do Objective-C* e adicione o caminho para o arquivo de cabeçalho:`ProjectName/Bridging-Header.h`
4. Compile o projeto (⌘+B) para verificar se o cabeçalho ponte foi recebido pelo Xcode.
5. Comece a usar a biblioteca diretamente em qualquer arquivo Swift, instruções de importação não são necessárias.

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Operações assíncronas
> [!NOTE]
> Todos os métodos que realizam uma solicitação ao serviço são operações assíncronas. Nos exemplos de código, você verá que esses métodos têm um manipulador de conclusão. O código no manipulador de conclusão será executado **após** a solicitação ser concluída. O código depois do manipulador de conclusão será executado **enquanto** a solicitação está sendo feita.
> 
> 

## <a name="create-a-container"></a>Criar um contêiner
Todos os blobs no Armazenamento do Azure devem residir em um contêiner. O exemplo a seguir mostra como criar um contêiner, chamado *newcontainer*, em sua Conta de armazenamento, se ele ainda não existir. Ao escolher um nome para o contêiner, leve em conta as regras de nomenclatura mencionadas acima.

```objc
-(void)createContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

Você pode confirmar que isso funciona observando o [Gerenciador de Armazenamento do Microsoft Azure](https://storageexplorer.com) e verificando se *newcontainer* está na lista de contêineres para sua conta de armazenamento.

## <a name="set-container-permissions"></a>Definir permissões de contêiner
As permissões do contêiner são configuradas para acesso **privado** por padrão. No entanto, os contêineres fornecem algumas opções diferentes para acesso ao contêiner:

* **Privado**: Dados de blob e contêiner podem ser lidos apenas pelo proprietário da conta.
* **Blob**: Os dados do blob nesse contêiner podem ser lidos por meio de solicitação anônima, mas os dados do contêiner não estão disponíveis. Os clientes não podem enumerar os blobs no contêiner por meio de uma solicitação anônima.
* **Contêiner**: Dados de blob e contêiner podem ser lidos por solicitação anônima. Os clientes podem enumerar os blobs no contêiner por meio de uma solicitação anônima, mas não podem enumerar os contêineres em uma conta de armazenamento.

O exemplo a seguir mostra como criar um contêiner com permissões de acesso de **Contêiner** que permitirão o acesso público e somente leitura para todos os usuários na Internet:

```objc
-(void)createContainerWithPublicAccess{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

## <a name="upload-a-blob-into-a-container"></a>Carregar um blob em um contêiner
Conforme mencionado na seção Conceitos do serviço de Blob, o Armazenamento de Blob oferece três tipos diferentes de blobs: blobs de bloco, blobs de acréscimo e blobs de página. A biblioteca do iOS de armazenamento do Azure dá suporte a todos os três tipos de blobs. Na maioria dos casos, o blob de blocos é o tipo recomendado.

O exemplo a seguir mostra como carregar um blob de bloco de um NSString. Se um blob com o mesmo nome já existir no contêiner, o conteúdo desse blob será substituído.

```objc
-(void)uploadBlobToContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
        {
            if (error){
                NSLog(@"Error in creating container.");
            }
            else{
                // Create a local blob object
                AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

                // Upload blob to Storage
                [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                    if (error){
                        NSLog(@"Error in creating blob.");
                    }
                }];
            }
        }];
}
```

Você pode confirmar que isso funciona observando o [Gerenciador de Armazenamento do Microsoft Azure](https://storageexplorer.com) e verificando se o contêiner *containerpublic*, contém o blob *sampleblob*. Neste exemplo, usamos um contêiner público, de modo que você também pode verificar se esse aplicativo funcionou indo para o URI de blobs:

    https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob

Além de carregar um blob de blocos de um NSString, existem métodos semelhantes para NSData, NSInputStream ou um arquivo local.

## <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner
O exemplo a seguir mostra como listar todos os blobs em um contêiner. Ao executar essa operação, leve em conta os seguintes parâmetros:     

* **continuationToken** - O token de continuação representa onde a operação de listagem deve começar. Se nenhum token for fornecido, ele listará os blobs desde o início. Qualquer número de blobs pode ser listado, desde zero até um máximo definido. Mesmo que esse método retorne zero resultado, se `results.continuationToken` não for nulo, poderá haver mais blobs no serviço que não foram listados.
* **prefixo** -Você pode especificar o prefixo a ser usado para a listagem de blobs. Somente os blobs que começarem com esse prefixo serão listados.
* **useFlatBlobListing** – conforme mencionado na seção [Nomeando e referenciando contêineres e blobs](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata), embora o serviço Blob seja um esquema de armazenamento simples, você pode criar uma hierarquia virtual nomeando blobs com informações de caminho. No entanto, atualmente não há suporte para listagem não plana. Este recurso estará disponível em breve. Por enquanto, esse valor deve ser **YES**.
* **blobListingDetails** - Você pode especificar os itens a serem incluídos ao listar blobs
  * _AZSBlobListingDetailsNone_: Lista somente blobs confirmados e não retornam metadados do blob.
  * _AZSBlobListingDetailsSnapshots_: Lista de blobs confirmados e instantâneos de blob.
  * _AZSBlobListingDetailsMetadata_: Recupera metadados de blob para cada blob retornado na listagem.
  * _AZSBlobListingDetailsUncommittedBlobs_: Lista de blobs confirmados e não confirmados.
  * _AZSBlobListingDetailsCopy_: Inclui propriedades de cópia na listagem.
  * _AZSBlobListingDetailsAll_: Lista todos os blobs confirmados disponíveis, blobs não confirmados e instantâneos e retorna todos os metadados e status de cópia para esses blobs.
* **maxResults** - O número máximo de resultados a serem retornados para a operação. Use -1 para não definir um limite.
* **completionHandler** - O bloco de código a ser executado com os resultados da operação de listagem.

Neste exemplo, um método auxiliar é usado para chamar recursivamente o método de listagem de blobs sempre que um token de continuação é retornado.

```objc
-(void)listBlobsInContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    //List all blobs in container
    [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
        if (error != nil){
            NSLog(@"Error in creating container.");
        }
    }];
}

//List blobs helper method
-(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
{
    [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
        if (error)
        {
            completionHandler(error);
        }
        else
        {
            for (int i = 0; i < results.blobs.count; i++) {
                NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
            }
            if (results.continuationToken)
            {
                [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
            }
            else
            {
                completionHandler(nil);
            }
        }
    }];
}
```

## <a name="download-a-blob"></a>Baixar um blob
O exemplo a seguir mostra como baixar um blob para um objeto NSString.

```objc
-(void)downloadBlobToString{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

    // Download blob
    [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
        if (error) {
            NSLog(@"Error in downloading blob");
        }
        else{
            NSLog(@"%@",text);
        }
    }];
}
```

## <a name="delete-a-blob"></a>Excluir um blob
O exemplo a seguir mostra como excluir um blob.

```objc
-(void)deleteBlob{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

    // Delete blob
    [blockBlob deleteWithCompletionHandler:^(NSError *error) {
        if (error) {
            NSLog(@"Error in deleting blob.");
        }
    }];
}
```

## <a name="delete-a-blob-container"></a>Excluir um contêiner de blob
O exemplo a seguir mostra como excluir um contêiner.

```objc
-(void)deleteContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Delete container
    [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
        if(error){
            NSLog(@"Error in deleting container");
        }
    }];
}
```

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu como usar o Armazenamento de Blobs do iOS, siga esses links para saber mais sobre a biblioteca do iOS e o serviço de armazenamento.

* [Biblioteca de Cliente do Armazenamento do Azure para iOS](https://github.com/azure/azure-storage-ios)
* [Documentação de referência do iOS do Armazenamento do Azure](https://azure.github.io/azure-storage-ios/)
* [API REST de serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog da equipe de Armazenamento do Azure](https://blogs.msdn.com/b/windowsazurestorage)

Se você tiver dúvidas sobre a biblioteca, fique à vontade para postar em nosso [Fórum do Azure do MSDN](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) ou no [Stack Overflow](https://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files).
Se você tiver sugestões de recursos para o Armazenamento do Azure, poste nos [Comentários do Armazenamento do Azure](https://feedback.azure.com/forums/217298-storage/).

