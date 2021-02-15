---
title: Criptografia do lado do cliente com .NET para o Armazenamento do Microsoft Azure | Microsoft Docs
description: A Biblioteca de Clientes do Armazenamento do Azure para .NET oferece suporte à criptografia do lado do cliente e à integração com o Cofre da Chave do Azure para segurança máxima para seus aplicativos do Armazenamento do Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 11/10/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 5f2d3ba12fa65beb7156e056c23e44b028cbb520
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445057"
---
# <a name="client-side-encryption-and-azure-key-vault-for-microsoft-azure-storage"></a>Criptografia do lado do cliente e o Cofre da Chave do Azure para o Armazenamento do Microsoft Azure
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Visão geral
A [biblioteca de cliente de armazenamento do Azure para .net](/dotnet/api/overview/azure/storage) dá suporte à criptografia de dados em aplicativos cliente antes do carregamento no armazenamento do Azure e à descriptografia de dados durante o download para o cliente. A biblioteca também dá suporte à integração com o [Cofre da Chave do Azure](https://azure.microsoft.com/services/key-vault/) para o gerenciamento de chaves de contas de armazenamento.

Para obter um tutorial passo a passo que orienta você pelo processo de criptografia de blobs usando criptografia do lado do cliente e o Cofre da Chave do Azure, confira [Criptografar e descriptografar blobs no Armazenamento do Microsoft Azure usando a Chave do Cofre do Azure](../blobs/storage-encrypt-decrypt-blobs-key-vault.md).

Para a criptografia do lado do cliente com Java, veja [Criptografia do lado do cliente com Java para o Armazenamento do Microsoft Azure](storage-client-side-encryption-java.md).

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Criptografia e descriptografia com a técnica de envelope
Os processos de criptografia e descriptografia seguem a técnica de envelope.

### <a name="encryption-via-the-envelope-technique"></a>Criptografia com a técnica de envelope
A criptografia com a técnica de envelope funciona da seguinte maneira:

1. A biblioteca do cliente do Armazenamento do Azure gera um CEK (Chave de Criptografia de Conteúdo) que é uma chave simétrica de uso único.
2. Os dados do usuário são criptografados usando esse CEK.
3. O CEK é empacotada (criptografada) usando o KEK (Chave de Criptografia de Chave). O KEK é identificado por um identificador de chave e pode ser um par de chaves assimétricas ou uma chave simétrica e pode ser gerenciada localmente ou armazenada no Cofre da Chave do Azure.
   
    A biblioteca de cliente de armazenamento em si nunca tem acesso ao KEK. Ela simplesmente invoca o algoritmo de disposição de chave fornecido pelo Cofre da Chave. Os usuários podem escolher usar provedores personalizados para desempacotamento/quebra da chave, se desejado.

4. Os dados criptografados, em seguida, são carregados para o serviço de Armazenamento do Azure. A chave empacotada junto com alguns metadados adicionais de criptografia está armazenada como metadados (em um blob) ou interpolada com os dados criptografados (fila de mensagens e entidades de tabela).

### <a name="decryption-via-the-envelope-technique"></a>Descriptografia com a técnica de envelope
A descriptografia com a técnica de envelope funciona da seguinte maneira:

1. A biblioteca de cliente presume que o usuário está gerenciando o KEK (chave de criptografia de chave) localmente ou no Cofre da Chave do Azure. O usuário não precisa conhecer a chave específica que foi usada para criptografia. Em vez disso, um resolvedor de chave que resolve diferentes identificadores de chaves pode ser configurado e usado.
2. A biblioteca de cliente baixa os dados criptografados junto com demais materiais de criptografia armazenados no serviço.
3. O CEK (chave de criptografia de conteúdo) empacotado é então desempacotado usando KEK. Novamente aqui, a biblioteca de cliente não tem acesso a KEK. Ela simplesmente invoca o algoritmo de descompactação do provedor do Key Vault ou personalizado.
4. A CEK (chave de criptografia de conteúdo) é então usada para descriptografar os dados de usuário criptografados.

## <a name="encryption-mechanism"></a>Mecanismo de criptografia
A biblioteca de cliente de armazenamento usa [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) para criptografar os dados do usuário. Especificamente, o modo [CBC (Encadeamento de Blocos de Criptografia)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) com AES. Cada serviço funciona ligeiramente diferente, portanto, discutiremos cada uma deles aqui.

### <a name="blobs"></a>Blobs
Atualmente, a biblioteca de cliente dá suporte à criptografia somente de blobs completos. Para downloads, tanto os downloads completos quanto os de intervalo têm suporte.

Durante a criptografia, a biblioteca de cliente gerará um vetor de inicialização aleatório (IV) de 16 bytes, juntamente com uma chave de criptografia aleatória de conteúdo (CEK) de 32 bytes e executará a criptografia de envelope dos dados blob usando essas informações. O CEK encapsulado e alguns metadados adicionais de criptografia são armazenadas como metadados com o blob criptografado no serviço de blob.

> [!WARNING]
> Se você estiver editando ou carregando seus próprios metadados para o blob, deverá garantir que esses metadados sejam preservado. Se você carregar novos metadados sem esses metadados, o CEK encapsulado, IV e outros metadados serão perdidos e o conteúdo do blob nunca mais poderá ser recuperado.
> 
> 

Ao baixar um blob inteiro, o CEK encapsulado é desencapsulado e usado junto com o IV (armazenado como metadados de blob nesse caso) para retornar os dados descriptografados para os usuários.

O download de um intervalo arbitrário no blob criptografado envolve o ajuste do intervalo fornecido pelos usuários para obter uma pequena quantidade de dados adicionais que podem ser usados para descriptografar com êxito o intervalo solicitado.

Todos os tipos de blob (blobs de blocos, blobs de páginas e blobs de anexo) podem ser criptografados/descriptografados usando este esquema.

### <a name="queues"></a>Filas
Uma vez que a fila de mensagens pode ser de qualquer formato, a biblioteca de cliente define um formato personalizado que inclui o vetor de inicialização (IV) e a chave de criptografia do conteúdo criptografado (CEK) no texto da mensagem.

Durante a criptografia, a biblioteca de cliente gera um IV aleatório de 16 bytes com um CEK aleatória de 32 bytes e executa criptografia de envelope do texto da mensagem de fila usando essas informações. O CEK encapsulado e alguns metadados adicionais de criptografia são adicionados à mensagem da fila criptografada. Essa mensagem modificada (mostrada abaixo) é armazenada no serviço.

```xml
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

Durante a descriptografia, a chave encapsulada é extraída da mensagem da fila e envolta. O IV também é extraído da mensagem da fila e usado juntamente com a chave desencapsulada para descriptografar os dados de mensagem da fila. Observe que os metadados de criptografia são pequeno (abaixo de 500 bytes), por isso embora contem para o limite de 64 KB para uma mensagem da fila, tal impacto é administrável. Observe que a mensagem criptografada será codificada em base64, conforme mostrado no trecho acima, que também expandirá o tamanho da mensagem que está sendo enviada.

### <a name="tables"></a>Tabelas
> [!NOTE]
> O serviço tabela tem suporte na biblioteca do cliente de armazenamento do Azure somente por meio da versão 9. x.
> 
> 

A biblioteca de cliente dá suporte à criptografia de propriedades de entidade para as operações de inserção e substituição.

> [!NOTE]
> Atualmente não há suporte para a mesclagem. Como um subconjunto de propriedades pode ter sido criptografado anteriormente usando uma chave diferente, simplesmente mesclar as novas propriedades e atualizar os metadados resultará em perda de dados. Mesclar requer fazer chamadas de serviço extra para ler a entidade já existente no serviço ou usar uma nova chave por propriedade, os quais não são ambos adequados por motivos de desempenho.
> 
> 

Criptografia de dados de tabela funciona da seguinte maneira:  

1. Os usuários especificam as propriedades que devem ser criptografadas.
2. A biblioteca de cliente gera um vetor de inicialização aleatório (IV) de 16 bytes com uma chave de criptografia aleatória de conteúdo (CEK) de 32 bytes para cada entidade e executa a criptografia de envelope sobre as propriedades individuais que devem ser criptografadas, derivando uma nova IV por propriedade. A propriedade criptografada é armazenada como dados binários.
3. O CEK encapsulado e alguns metadados adicionais de criptografia, em seguida, são armazenados como duas propriedades reservadas adicionais. A primeira propriedade reservada (_ClientEncryptionMetadata1) é uma propriedade de cadeia de caracteres que contém informações sobre o IV, versão e chave encapsuladas. A segunda propriedade reservada (_ClientEncryptionMetadata2) é uma propriedade binária que armazena as informações sobre as propriedades criptografadas. As informações nessa segunda propriedade (_ClientEncryptionMetadata2) são criptografadas.
4. Devido a essas propriedades reservadas adicionais necessárias para a criptografia, os usuários agora podem ter apenas 250 propriedades personalizadas, em vez de 252. O tamanho total da entidade deve ser inferior a 1 MB.

Observe que somente as propriedades de cadeia de caracteres podem ser criptografadas. Se outros tipos de propriedades precisarem ser criptografados, elas devem ser convertidas em cadeias de caracteres. As cadeias de caracteres criptografadas são armazenadas no serviço como propriedades binárias, e são convertidas novamente em cadeias de caracteres após a descriptografia.

Para tabelas, além da política de criptografia, os usuários devem especificar as propriedades que devem ser criptografadas. Isso pode ser feito especificando o atributo [EncryptProperty] \(para entidades POCO que derivam de TableEntity) ou um resolvedor de criptografia nas opções de solicitação. Um resolvedor de criptografia é um delegado que usa uma chave de partição, a chave de linha e o nome da propriedade e retorna um valor booliano que indica se essa propriedade deve ser criptografada. Durante a criptografia, a biblioteca de cliente usará essas informações para decidir se uma propriedade deve ser criptografada durante a gravação para a transmissão. O representante também oferece a possibilidade de lógica em torno de como as propriedades são criptografadas. (Por exemplo, se X, em seguida, criptografe A propriedade A; caso contrário, criptografe as propriedades A e B.) Observe que não é necessário fornecer essas informações durante a leitura ou consulta de entidades.

### <a name="batch-operations"></a>Operações em lote
Em operações em lote, o mesmo KEK será usado em todas as linhas de operação em lote porque a biblioteca de cliente permite apenas um objeto de opções (e, portanto, uma política/KEK) por operação em lote. No entanto, a biblioteca de cliente gerará internamente um novo IV e CEK aleatórios por linha no lote. Os usuários também podem optar por criptografar propriedades diferentes para cada operação em lote definindo esse comportamento no resolvedor de criptografia.

### <a name="queries"></a>Consultas
> [!NOTE]
> Como as entidades são criptografadas, é possível executar consultas que filtram uma propriedade criptografada.  Se você tentar, os resultados estarão incorretos, porque o serviço tentará comparar dados criptografados com dados não criptografados.
> 
> 
> Para executar operações de consulta, você deve especificar que um resolvedor de chave é capaz de resolver todas as chaves no conjunto de resultados. Se uma entidade contida no resultado da consulta não puder ser resolvida para um provedor, a biblioteca de cliente gerará um erro. Para qualquer consulta que realiza as projeções do lado do servidor, a biblioteca de cliente adicionará as propriedades de metadados de criptografia especial (_ClientEncryptionMetadata1 e ClientEncryptionMetadata2) por padrão às colunas selecionadas.

## <a name="azure-key-vault"></a>Cofre de Chave do Azure
O Cofre da Chave do Azure ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços em nuvem. Usando o Cofre da Chave do Azure, os usuários podem criptografar chaves e segredos (como chaves de autenticação, chaves de conta de armazenamento, chaves de criptografia de dados, arquivos .PFX e senhas) usando chaves que são protegidas por HSMs (módulos de segurança de hardware). Para obter mais informações, veja [O que é o Cofre da Chave do Azure?](../../key-vault/general/overview.md).

A biblioteca de cliente de armazenamento usa as interfaces de Key Vault na biblioteca principal para fornecer uma estrutura comum no Azure para o gerenciamento de chaves. Os usuários podem aproveitar as bibliotecas de Key Vault para todos os benefícios adicionais que eles fornecem, como uma funcionalidade útil em relação aos provedores de chave de nuvem e de local simétrico/RSA simples e contínuos, além de ajudar com a agregação e o Caching.

### <a name="interface-and-dependencies"></a>Interface e dependências

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Há dois pacotes necessários para a integração de Key Vault:

* O Azure. Core contém `IKeyEncryptionKey` as `IKeyEncryptionKeyResolver` interfaces e. A biblioteca de cliente de armazenamento para .NET já a define como uma dependência.
* Azure. Security. keyvault. Keys (v4. x) contém o cliente REST Key Vault, bem como clientes criptográficos usados com criptografia do lado do cliente.

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

Há três pacotes do Cofre da Chave:

* Microsoft.Azure.KeyVault.Core contém IKey e IKeyResolver. Este é um pequeno pacote sem dependências. A biblioteca de cliente de armazenamento para .NET define isso como uma dependência.
* Microsoft. Azure. keyvault (v3. x) contém o cliente REST Key Vault.
* Microsoft. Azure. keyvault. Extensions (v3. x) contém o código de extensão que inclui implementações de algoritmos de criptografia e um RSAKey e um SymmetricKey. Ele depende dos namespaces básicos e KeyVault, e fornece a funcionalidade para definir um resolvedor de agregação (quando os usuários desejam usar vários provedores de chave) e um resolvedor de chave em cache. Embora a biblioteca de cliente de armazenamento não dependa diretamente deste pacote, se os usuários quiserem usar o Cofre da Chave do Azure para armazenar suas chaves ou usar as extensões do Cofre da Chave para consumir provedores criptográficos local e na nuvem, eles precisarão esse pacote.

Mais informações sobre o uso de Key Vault no v11 podem ser encontradas nos [exemplos de código de criptografia do v11](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples).

---

O Cofre da Chave foi criado para chaves mestras de alto valor e os limites por Cofre da Chave são criados com tal prioridade em mente. Ao executar a criptografia do lado do cliente com o Cofre da Chave, o modelo preferido é usar chaves mestras simétricas armazenadas como segredos no Cofre da Chave e armazenadas em cache localmente. Os usuários devem fazer o seguinte:

1. Crie um segredo offline e carregue-o no Cofre da Chave.
2. Use o identificador de base do segredo como um parâmetro para resolver a versão atual do segredo para criptografia e armazenar essas informações localmente em cache. Use CachingKeyResolver para armazenamento em cache. Os usuários não deverão implementar sua própria lógica de cache.
3. Use o resolvedor de cache como uma entrada ao criar a política de criptografia.

## <a name="best-practices"></a>Práticas recomendadas
O suporte à criptografia está disponível somente na biblioteca de cliente de armazenamento para .NET. Atualmente, o Windows Phone e o Windows Runtime não dão suporte à criptografia.

> [!IMPORTANT]
> Lembre-se dos seguintes pontos importantes ao usar a criptografia no lado do cliente:
> 
> * Durante a leitura ou gravação de um blob criptografado, use comandos de carregamento de blob completos e comandos de download de blob completos/em intervalos. Evite gravar em um blob criptografado usando operações de protocolo tais como Put Block, Put Block List, Write Pages, Clear Pages ou Append Block; caso contrário, você poderá corromper o blob criptografado e torná-lo ilegível.
> * Para tabelas, existe uma restrição semelhante. Tenha cuidado para não atualizar propriedades criptografadas sem atualizar os metadados de criptografia.
> * Se você definir os metadados no blob criptografado, poderá substituir os metadados relacionados à criptografia necessários para a descriptografia, uma vez que a definição de metadados não é aditiva. Isso também ocorre em instantâneos. Evite especificar metadados ao criar um instantâneo de um blob criptografado. Se for necessário definir os metadados, lembre-se de chamar o método **FetchAttributes** primeiro para obter os atuais metadados de criptografia e evite gravações simultâneas durante a definição dos metadados.
> * Habilite a propriedade **RequireEncryption** nas opções de solicitação padrão para os usuários que devem trabalhar somente com dados criptografados. Saiba mais logo abaixo.
>
>

## <a name="client-api--interface"></a>API do cliente / Interface
Os usuários podem fornecer apenas uma chave, apenas um resolvedor ou ambos. As chaves são identificadas usando um identificador de chave e fornecem a lógica para encapsulamento/desencapsulamento. Os resolvedores são usados para resolver uma chave durante o processo de descriptografia. Ele define um método resolve que retorna uma chave, dado um identificador de chave. Isso fornece aos usuários a capacidade de escolher entre várias chaves que são gerenciadas em vários locais.

* Para criptografia, a chave é usada sempre e a ausência de uma chave resultará em um erro.
* Para descriptografar:
  * Se a chave for especificada e seu identificador corresponder ao identificador de chave necessário, essa chave será usada para descriptografia. Caso contrário, o resolvedor será tentado. Se não houver nenhum resolvedor para essa tentativa, um erro será gerado.
  * O resolvedor de chave é invocado se especificado para obter a chave. Se o resolvedor for especificado, mas não tiver um mapeamento para o identificador de chave, um erro será gerado.

### <a name="requireencryption-mode-v11-only"></a>Modo RequireEncryption (somente v11)
Os usuários podem habilitar opcionalmente um modo de operação no qual todos os downloads e uploads devem ser criptografados. Nesse modo, as tentativas de carregamento de dados sem uma política de criptografia ou o download de dados que não são criptografados no serviço falharão no cliente. A propriedade **RequireEncryption** do objeto de opções da solicitação controla esse comportamento. Se o seu aplicativo for criptografar todos os objetos armazenados no Armazenamento do Azure, é possível definir a propriedade **RequireEncryption** nas opções de solicitação padrão para o objeto de cliente do serviço. Por exemplo, defina **CloudBlobClient.DefaultRequestOptions.RequireEncryption** como **true** para exigir criptografia de todas as operações executadas de blob executadas por meio desse objeto de cliente.


### <a name="blob-service-encryption"></a>Criptografia do serviço Blob


# <a name="net-v12"></a>[.NET V12](#tab/dotnet)
Crie um objeto **ClientSideEncryptionOptions** e defina-o na criação do cliente com **SpecializedBlobClientOptions**. Não é possível definir as opções de criptografia por API. Todo o resto será tratado pela biblioteca de cliente internamente.

```csharp
// Your key and key resolver instances, either through KeyVault SDK or an external implementation
IKeyEncryptionKey key;
IKeyEncryptionKeyResolver keyResolver;

// Create the encryption options to be used for upload and download.
ClientSideEncryptionOptions encryptionOptions = new ClientSideEncryptionOptions(ClientSideEncryptionVersion.V1_0)
{
   KeyEncryptionKey = key,
   KeyResolver = keyResolver,
   // string the storage client will use when calling IKeyEncryptionKey.WrapKey()
   KeyWrapAlgorithm = "some algorithm name"
};

// Set the encryption options on the client options
BlobClientOptions options = new SpecializedBlobClientOptions() { ClientSideEncryption = encryptionOptions };

// Get your blob client with client-side encryption enabled.
// Client-side encryption options are passed from service to container clients, and container to blob clients.
// Attempting to construct a BlockBlobClient, PageBlobClient, or AppendBlobClient from a BlobContainerClient
// with client-side encryption options present will throw, as this functionality is only supported with BlobClient.
BlobClient blob = new BlobServiceClient(connectionString, options).GetBlobContainerClient("myContainer").GetBlobClient("myBlob");

// Upload the encrypted contents to the blob.
blob.Upload(stream);

// Download and decrypt the encrypted contents from the blob.
MemoryStream outputStream = new MemoryStream();
blob.DownloadTo(outputStream);
```

Um **BlobServiceClient** não é necessário para aplicar as opções de criptografia. Elas também podem ser passadas para construtores **BlobContainerClient** / **BlobClient** que aceitam objetos **BlobClientOptions** .

Se um objeto **BlobClient** desejado já existir, mas sem opções de criptografia do lado do cliente, um método de extensão existirá para criar uma cópia desse objeto com o **ClientSideEncryptionOptions** especificado. Esse método de extensão evita a sobrecarga de construir um novo objeto **BlobClient** do zero.

```csharp
using Azure.Storage.Blobs.Specialized;

// Your existing BlobClient instance and encryption options
BlobClient plaintextBlob;
ClientSideEncryptionOptions encryptionOptions;

// Get a copy of plaintextBlob that uses client-side encryption
BlobClient clientSideEncryptionBlob = plaintextBlob.WithClientSideEncryptionOptions(encryptionOptions);
```

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)
Crie um objeto **BlobEncryptionPolicy** e o defina nas opções de solicitação (por API ou no nível do cliente usando **DefaultRequestOptions** ). Todo o resto será tratado pela biblioteca de cliente internamente.

```csharp
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

// Set the encryption policy on the request options.
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

// Upload the encrypted contents to the blob.
blob.UploadFromStream(stream, size, null, options, null);

// Download and decrypt the encrypted contents from the blob.
MemoryStream outputStream = new MemoryStream();
blob.DownloadToStream(outputStream, null, options, null);
```

---

### <a name="queue-service-encryption"></a>Criptografia do serviço Fila
# <a name="net-v12"></a>[.NET V12](#tab/dotnet)
Crie um objeto **ClientSideEncryptionOptions** e defina-o na criação do cliente com **SpecializedQueueClientOptions**. Não é possível definir as opções de criptografia por API. Todo o resto será tratado pela biblioteca de cliente internamente.

```csharp
// Your key and key resolver instances, either through KeyVault SDK or an external implementation
IKeyEncryptionKey key;
IKeyEncryptionKeyResolver keyResolver;

// Create the encryption options to be used for upload and download.
ClientSideEncryptionOptions encryptionOptions = new ClientSideEncryptionOptions(ClientSideEncryptionVersion.V1_0)
{
   KeyEncryptionKey = key,
   KeyResolver = keyResolver,
   // string the storage client will use when calling IKeyEncryptionKey.WrapKey()
   KeyWrapAlgorithm = "some algorithm name"
};

// Set the encryption options on the client options
QueueClientOptions options = new SpecializedQueueClientOptions() { ClientSideEncryption = encryptionOptions };

// Get your queue client with client-side encryption enabled.
// Client-side encryption options are passed from service to queue clients.
QueueClient queue = new QueueServiceClient(connectionString, options).GetQueueClient("myQueue");

// Send an encrypted queue message.
queue.SendMessage("Hello, World!");

// Download queue messages, decrypting ones that are detected to be encrypted
QueueMessage[] queue.ReceiveMessages(); 
```

Um **QueueServiceClient** não é necessário para aplicar as opções de criptografia. Elas também podem ser passadas para construtores **QueueClient** que aceitam objetos **QueueClientOptions** .

Se um objeto **QueueClient** desejado já existir, mas sem opções de criptografia do lado do cliente, um método de extensão existirá para criar uma cópia desse objeto com o **ClientSideEncryptionOptions** especificado. Esse método de extensão evita a sobrecarga de construir um novo objeto **QueueClient** do zero.

```csharp
using Azure.Storage.Queues.Specialized;

// Your existing QueueClient instance and encryption options
QueueClient plaintextQueue;
ClientSideEncryptionOptions encryptionOptions;

// Get a copy of plaintextQueue that uses client-side encryption
QueueClient clientSideEncryptionQueue = plaintextQueue.WithClientSideEncryptionOptions(encryptionOptions);
```

Alguns usuários podem ter filas onde nem todas as mensagens recebidas podem ser descriptografadas com êxito e a chave ou o resolvedor deve gerar. A linha final do exemplo acima gerará nesse caso, e nenhuma das mensagens recebidas será acessível. Nesses cenários, o **QueueClientSideEncryptionOptions** de subclasse pode ser usado para fornecer opções de criptografia aos clientes. Ele expõe um evento **DecryptionFailed** que será disparado sempre que uma mensagem de fila não for descriptografada, desde que pelo menos uma invocação tenha sido adicionada ao evento. As mensagens com falha individualmente podem ser tratadas dessa forma e serão filtradas do QueueMessage final **[]** retornado pelo **ReceiveMessages**.

```csharp
// Create your encryption options using the sub-class.
QueueClientSideEncryptionOptions encryptionOptions = new QueueClientSideEncryptionOptions(ClientSideEncryptionVersion.V1_0)
{
   KeyEncryptionKey = key,
   KeyResolver = keyResolver,
   // string the storage client will use when calling IKeyEncryptionKey.WrapKey()
   KeyWrapAlgorithm = "some algorithm name"
};

// Add a handler to the DecryptionFailed event.
encryptionOptions.DecryptionFailed += (source, args) => {
   QueueMessage failedMessage = (QueueMessage)source;
   Exception exceptionThrown = args.Exception;
   // do something
};

// Use these options with your client objects.
QueueClient queue = new QueueClient(connectionString, queueName, new SpecializedQueueClientOptions()
{
   ClientSideEncryption = encryptionOptions
});

// Retrieve 5 messages from the queue.
// Assume 5 messages come back and one throws during decryption.
QueueMessage[] messages = queue.ReceiveMessages(maxMessages: 5).Value;
Debug.Assert(messages.Length == 4)
```

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)
Crie um objeto **QueueEncryptionPolicy** e o defina nas opções de solicitação (por API ou no nível do cliente usando **DefaultRequestOptions** ). Todo o resto será tratado pela biblioteca de cliente internamente.

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

 // Add message
 QueueRequestOptions options = new QueueRequestOptions() { EncryptionPolicy = policy };
 queue.AddMessage(message, null, null, options, null);

 // Retrieve message
 CloudQueueMessage retrMessage = queue.GetMessage(null, options, null);
```

---

### <a name="table-service-encryption-v11-only"></a>Criptografia do serviço tabela (somente v11)
Além de criar uma política de criptografia e defini-la nas opções de solicitação, você precisa especificar um **EncryptionResolver** em **TableRequestOptions** ou definir o atributo [EncryptProperty] na entidade.

#### <a name="using-the-resolver"></a>Usando o resolvedor

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

 TableRequestOptions options = new TableRequestOptions()
 {
    EncryptionResolver = (pk, rk, propName) =>
     {
        if (propName == "foo")
         {
            return true;
         }
         return false;
     },
     EncryptionPolicy = policy
 };

 // Insert Entity
 currentTable.Execute(TableOperation.Insert(ent), options, null);

 // Retrieve Entity
 // No need to specify an encryption resolver for retrieve
 TableRequestOptions retrieveOptions = new TableRequestOptions()
 {
    EncryptionPolicy = policy
 };

 TableOperation operation = TableOperation.Retrieve(ent.PartitionKey, ent.RowKey);
 TableResult result = currentTable.Execute(operation, retrieveOptions, null);
```

#### <a name="using-attributes"></a>Usando atributos
Como mencionado acima, se a entidade implementar TableEntity, as propriedades poderão ser decoradas com o atributo [EncryptProperty] em vez de especificar o **EncryptionResolver**.

```csharp
[EncryptProperty]
 public string EncryptedProperty1 { get; set; }
```

## <a name="encryption-and-performance"></a>Criptografia e desempenho
Observe que criptografar seu armazenamento de dados resulta em uma sobrecarga adicional no desempenho. O IV e a chave de conteúdo devem ser gerados, o próprio conteúdo deve ser criptografado e os metadados adicionais devem ser formatados e carregados. Essa sobrecarga poderá variar dependendo da quantidade de dados que está sendo criptografada. Recomendamos que os clientes sempre testem seus aplicativos a fim de verificar o desempenho durante o desenvolvimento.

## <a name="next-steps"></a>Próximas etapas
* [Tutorial: criptografar e descriptografar blobs no Armazenamento do Microsoft Azure usando o Cofre da Chave do Azure](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)
* Baixar o [pacote NuGet da Biblioteca de Clientes do Armazenamento do Azure para o .NET](https://www.nuget.org/packages/WindowsAzure.Storage)
* Baixar os pacotes NuGet de [Núcleo](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/), [Cliente](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/) e [Extensões](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/) do Cofre de Chaves do Azure  
* Visitar a [Documentação do Cofre de Chaves do Azure](../../key-vault/general/overview.md)
