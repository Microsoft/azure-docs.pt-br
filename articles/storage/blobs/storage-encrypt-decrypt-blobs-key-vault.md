---
title: Tutorial – Criptografar e descriptografar blobs usando o Azure Key Vault
titleSuffix: Azure Storage
description: Saiba como criptografar e descriptografar um blob usando a criptografia do lado do cliente com o Azure Key Vault.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 02/18/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: c2daed4a8df89ed176749900dc75eb231c00af87
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102049263"
---
# <a name="tutorial---encrypt-and-decrypt-blobs-using-azure-key-vault"></a>Tutorial – Criptografar e descriptografar blobs usando o Azure Key Vault

Este tutorial aborda como aproveitar a criptografia de armazenamento do cliente com o Cofre da Chave do Azure. Ele explica como criptografar e descriptografar um blob em um aplicativo de console usando essas tecnologias.

**Tempo estimado para conclusão:** 20 minutos

Para obter informações gerais sobre o Cofre de Chaves do Azure, consulte [O que é o Cofre de Chaves do Azure?](../../key-vault/general/overview.md).

Para obter informações gerais sobre a criptografia de cliente do armazenamento do Azure, consulte [Criptografia do lado do cliente e Cofre de Chaves do Azure para o Armazenamento do Microsoft Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, é necessário ter o seguinte:

* Uma conta de armazenamento do Azure
* Visual Studio 2013 ou posterior.
* Azure PowerShell

## <a name="overview-of-client-side-encryption"></a>Visão geral da criptografia do lado do cliente

Para obter uma visão geral da criptografia do lado do cliente do Armazenamento do Azure, consulte [Criptografia do lado do cliente e o Cofre de Chaves do Azure para o Armazenamento do Microsoft Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

Aqui está uma breve descrição de como funciona a criptografia do lado do cliente:

1. O SDK do cliente do Armazenamento do Azure gera uma CEK (chave de criptografia de conteúdo) que é uma chave simétrica de uso único.
2. Os dados do cliente são criptografados usando essa CEK.
3. O CEK é empacotada (criptografada) usando o KEK (Chave de Criptografia de Chave). A KEK é identificada por um identificador de chave e pode ser um par de chaves assimétricas ou uma chave simétrica, podendo ser gerenciada localmente ou armazenada no Cofre da Chave do Azure. O cliente de Armazenamento, por si só, nunca tem acesso à KEK. Ele simplesmente chama o algoritmo de quebra de chave fornecido pelo Cofre da Chave. Os clientes podem escolher usar provedores personalizados para desempacotamento/quebra da chave, se desejado.
4. Os dados criptografados, em seguida, são carregados para o serviço de Armazenamento do Azure.

## <a name="set-up-your-azure-key-vault"></a>Configure o seu Cofre da Chave do Azure

Para continuar com este tutorial, você precisa executar as seguintes etapas, que são descritas no tutorial [Início rápido: definir e recuperar um segredo do Azure Key Vault usando um aplicativo Web .NET](../../key-vault/secrets/quick-create-net.md):

* Crie um cofre da chave.
* Adicionar uma chave ou segredo ao cofre da chave.
* Registre um aplicativo com o Azure Active Directory.
* Autorize o aplicativo a usar a chave ou segredo.

Anote o ClientID e ClientSecret que foram gerados ao registrar um aplicativo com o Active Directory do Azure.

Crie ambas as chaves no cofre da chave. Vamos supor pelo restante do tutorial que você tenha usado os seguintes nomes: ContosoKeyVault e TestRSAKey1.

## <a name="create-a-console-application-with-packages-and-appsettings"></a>Criar um aplicativo de console com pacotes e AppSettings

No Visual Studio, crie um novo aplicativo de console.

Adicione pacotes nuget necessários no Console do Gerenciador de Pacotes.

```powershell
Install-Package Microsoft.Azure.ConfigurationManager
Install-Package Microsoft.Azure.Storage.Common
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

Install-Package Microsoft.Azure.KeyVault
Install-Package Microsoft.Azure.KeyVault.Extensions
```

Adicione AppSettings a App.Config.

```xml
<appSettings>
    <add key="accountName" value="myaccount"/>
    <add key="accountKey" value="theaccountkey"/>
    <add key="clientId" value="theclientid"/>
    <add key="clientSecret" value="theclientsecret"/>
    <add key="container" value="stuff"/>
</appSettings>
```

Adicione as seguintes diretivas do `using` e adicione uma referência a System.Configuration ao projeto.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

No momento, estamos trabalhando para criar snippets de código que refletem a versão 12.x das bibliotecas de clientes do Armazenamento do Azure. Para obter mais informações, confira [Comunicado sobre as bibliotecas de clientes do Armazenamento do Azure V12](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Configuration;
using Microsoft.Azure;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
using Microsoft.Azure.KeyVault;
using System.Threading;
using System.IO;
```
---

## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>Adicione um método para obter um token para o aplicativo de console

O método a seguir é usado pelas classes do Cofre da Chave que precisam ser autenticadas para acessar seu cofre da chave.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

No momento, estamos trabalhando para criar snippets de código que refletem a versão 12.x das bibliotecas de clientes do Armazenamento do Azure. Para obter mais informações, confira [Comunicado sobre as bibliotecas de clientes do Armazenamento do Azure V12](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
private async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(
        CloudConfigurationManager.GetSetting("clientId"),
        CloudConfigurationManager.GetSetting("clientSecret"));
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```
---

## <a name="access-azure-storage-and-key-vault-in-your-program"></a>Acessar o Armazenamento do Azure e o Key Vault em seu programa

No método Main(), adicione o código a seguir.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

No momento, estamos trabalhando para criar snippets de código que refletem a versão 12.x das bibliotecas de clientes do Armazenamento do Azure. Para obter mais informações, confira [Comunicado sobre as bibliotecas de clientes do Armazenamento do Azure V12](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
// This is standard code to interact with Blob storage.
StorageCredentials creds = new StorageCredentials(
    CloudConfigurationManager.GetSetting("accountName"),
    CloudConfigurationManager.GetSetting("accountKey")
);
CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
CloudBlobClient client = account.CreateCloudBlobClient();
CloudBlobContainer contain = client.GetContainerReference(CloudConfigurationManager.GetSetting("container"));
contain.CreateIfNotExists();

// The Resolver object is used to interact with Key Vault for Azure Storage.
// This is where the GetToken method from above is used.
KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);
```
---

> [!NOTE]
> Modelos de Objetos de Chave de Cofre
> 
> É importante entender que há realmente dois Chave de Cofre modelos de objeto estar atento: uma baseada na API REST (namespace KeyVault) e a outra é uma extensão para criptografia do lado do cliente.
> 
> O Cliente do Cofre da Chave interage com a API REST e compreende JSON Web chaves e segredos para os dois tipos de coisas que estão contidos no Cofre da Chave.
> 
> As Extensões de Chave de Cofre são classes que parecem criados especificamente para a criptografia de cliente no Armazenamento do Azure. Eles contêm uma interface para chaves (IKey) e classes com base no conceito de um Resolvedor de Chave. Há duas implementações de IKey que você precisa saber: RSAKey e SymmetricKey. Agora que eles aconteçam coincidir com as coisas que estão contidas em um Cofre da Chave, mas agora são classes independentes (para a chave e o segredo recuperada pelo cliente Cofre da Chave não implementam IKey).
> 
> 

## <a name="encrypt-blob-and-upload"></a>Criptografar o blob e carregar

Adicione o seguinte código para criptografar um blob e carregá-lo à sua conta de armazenamento do Azure. O método **ResolveKeyAsync** usado retorna uma IKey.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

No momento, estamos trabalhando para criar snippets de código que refletem a versão 12.x das bibliotecas de clientes do Armazenamento do Azure. Para obter mais informações, confira [Comunicado sobre as bibliotecas de clientes do Armazenamento do Azure V12](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
// Retrieve the key that you created previously.
// The IKey that is returned here is an RsaKey.
var rsa = cloudResolver.ResolveKeyAsync(
            "https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", 
            CancellationToken.None).GetAwaiter().GetResult();

// Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

// Reference a block blob.
CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

// Upload using the UploadFromStream method.
using (var stream = System.IO.File.OpenRead(@"C:\Temp\MyFile.txt"))
    blob.UploadFromStream(stream, stream.Length, null, options, null);
```
---

> [!NOTE]
> Se você examinar o construtor BlobEncryptionPolicy, você verá que ele pode aceitar uma chave e/ou um resolvedor. Lembre-se de que agora, você não pode usar um resolvedor para criptografia porque atualmente não dá suporte a uma chave padrão.


## <a name="decrypt-blob-and-download"></a>Descriptografar o blob e baixar

Descriptografia é quando o uso das classes de resolvedor realmente fazem sentido. A ID da chave usada para criptografia é associada ao blob em seus metadados, portanto, não há motivo para recuperar a chave e lembre-se a associação entre a chave e o blob. Você precisa certificar-se de que a chave permanece no Cofre da Chave.   

A chave privada de uma Chave RSA permanece no Cofre da Chave de modo que, para a descriptografia ocorrer, a Chave Criptografada dos metadados do blob que contêm a CEK é enviada ao Cofre da Chave para descriptografia.

Adicione o seguinte para descriptografar o blob que você acabou de carregar.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

No momento, estamos trabalhando para criar snippets de código que refletem a versão 12.x das bibliotecas de clientes do Armazenamento do Azure. Para obter mais informações, confira [Comunicado sobre as bibliotecas de clientes do Armazenamento do Azure V12](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
// In this case, we will not pass a key and only pass the resolver because
// this policy will only be used for downloading / decrypting.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
    blob.DownloadToStream(np, null, options, null);
```
---

> [!NOTE]
> Existem alguns outros tipos de resolvedores para facilitar o gerenciamento de chaves, incluindo: AggregateKeyResolver e CachingKeyResolver.

## <a name="use-key-vault-secrets"></a>Usar os segredos do Cofre da Chave

A maneira de usar um segredo com criptografia do lado do cliente é por meio da classe SymmetricKey, dado que um segredo é essencialmente uma chave simétrica. Mas, conforme observado anteriormente, um segredo no Cofre da Chave não é mapeado com exatidão para uma SymmetricKey. Há algumas coisas que deve compreender:

* A chave em uma SymmetricKey deve ter um comprimento fixo: 192, 128, 256, 384 ou 512 bits.
* A chave em uma SymmetricKey deve ser codificado em Base64.
* Um segredo do Cofre da Chave que será usado como uma SymmetricKey deve ter um tipo de conteúdo de "application/octet-stream" no Cofre da Chave.

Aqui está um exemplo no PowerShell sobre a criação de um segredo no Cofre da Chave que pode ser usado como uma SymmetricKey.
Observe que o valor codificado, $key, serve apenas para demonstração. Em seu próprio código, você desejará gerar essa chave.

```powershell
// Here we are making a 128-bit key so we have 16 characters.
//     The characters are in the ASCII range of UTF8 so they are
//    each 1 byte. 16 x 8 = 128.
$key = "qwertyuiopasdfgh"
$b = [System.Text.Encoding]::UTF8.GetBytes($key)
$enc = [System.Convert]::ToBase64String($b)
$secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

// Substitute the VaultName and Name in this command.
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"
```

Em seu aplicativo de console, você pode usar a mesma chamada como antes, para recuperar esse segredo como uma SymmetricKey.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

No momento, estamos trabalhando para criar snippets de código que refletem a versão 12.x das bibliotecas de clientes do Armazenamento do Azure. Para obter mais informações, confira [Comunicado sobre as bibliotecas de clientes do Armazenamento do Azure V12](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
    CancellationToken.None).GetAwaiter().GetResult();
```
---

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como usar o Armazenamento do Microsoft Azure com C#, consulte [Biblioteca de cliente de Armazenamento do Microsoft Azure para .NET](/previous-versions/azure/dn261237(v=azure.100)).

Para obter mais informações sobre a API REST do Blob, consulte [API REST do Serviço Blob](/rest/api/storageservices/Blob-Service-REST-API).

Para obter as informações mais recentes sobre o Armazenamento do Microsoft Azure, vá para o [Blog da equipe de Armazenamento do Microsoft Azure](/archive/blogs/windowsazurestorage/).