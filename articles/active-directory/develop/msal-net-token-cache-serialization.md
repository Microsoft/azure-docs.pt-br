---
title: Serialização de cache de token (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre serialização e serialização de cliente do cache de token usando a MSAL.NET (biblioteca de autenticação da Microsoft para .NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/16/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 60ce3d32ffa20fc9117890528eac053d1af9fdf2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99583899"
---
# <a name="token-cache-serialization-in-msalnet"></a>Serialização do cache de token na MSAL.NET
Depois que um [token é adquirido](msal-acquire-cache-tokens.md), ele é armazenado em cache pela MSAL (biblioteca de autenticação da Microsoft).  O código do aplicativo deve tentar obter um token a partir do cache antes de adquirir um token por outro método.  Este artigo aborda a serialização padrão e personalizada do cache de token na MSAL.NET.

Este artigo serve para MSAL.NET 3.x. Se você estiver interessado na MSAL.NET 2.x, consulte [Serialização de cache de token da MSAL.NET 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Token-cache-serialization-2x).

## <a name="default-serialization-for-mobile-platforms"></a>Serialização padrão para plataformas móveis

Na MSAL.NET, um cache de token na memória é fornecido por padrão. A serialização é fornecida por padrão para plataformas em que o armazenamento seguro está disponível para um usuário como parte da plataforma. Esse é o caso da Plataforma Universal do Windows (UWP), Xamarin.iOS e Xamarin.Android.

> [!Note]
> Ao migrar um projeto Xamarin.Android da MSAL.NET 1.x para a MSAL.NET 3.x, é possível optar por adicionar `android:allowBackup="false"` ao projeto, de modo a evitar que tokens antigos armazenados em cache retornem quando as implantações do Visual Studio disparam uma restauração do armazenamento local. Consulte [Problema #659](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/659#issuecomment-436181938).

## <a name="custom-serialization-for-windows-desktop-apps-and-web-appsweb-apis"></a>Serialização personalizada para aplicativos da área de trabalho do Windows e aplicativos Web/APIs Web

Lembre-se de que a serialização personalizada não está disponível em plataformas móveis (UWP, Xamarin.iOS e Xamarin.Android). A MSAL já define um mecanismo de serialização seguro e de alto desempenho para essas plataformas. No entanto, os aplicativos da área de trabalho do .NET e os aplicativos do .NET Core têm variadas arquiteturas e a MSAL não pode implementar um mecanismo de serialização para uso geral. Por exemplo, sites da Web podem escolher armazenar tokens em um Cache Redis ou armazenar tokens da loja de aplicativos da área de trabalho em um arquivo criptografado. Portanto, a serialização não é fornecida pronta para uso. Para ter um aplicativo de cache de token persistente no .NET desktop ou no .NET Core, personalize a serialização.

As seguintes classes e interfaces são usadas na serialização do cache de token:

- `ITokenCache`, que define os eventos para assinar solicitações de serialização do cache de token, bem como métodos para serializar ou desserializar o cache em vários formatos (ADAL v3.0, MSAL 2.x e MSAL 3.x = ADAL v5.0).
- `TokenCacheCallback` é um retorno de chamada passado aos eventos para permitir que você controle a serialização. Eles serão chamados com argumentos do tipo `TokenCacheNotificationArgs`.
- `TokenCacheNotificationArgs` fornece apenas o `ClientId` do aplicativo e uma referência ao usuário para o qual o token está disponível.

  ![Diagrama de classe](media/msal-net-token-cache-serialization/class-diagram.png)

> [!IMPORTANT]
> A MSAL.NET cria os caches de token para você e fornece o cache `IToken` quando você chama as propriedades `UserTokenCache` e `AppTokenCache` de um aplicativo. Você não deve implementar a interface por conta própria. Ao implementar uma serialização do cache de token personalizada, sua responsabilidade é:
> - Reagir a "eventos" `BeforeAccess` e `AfterAccess` (ou suas variantes Assíncronas). O delegado `BeforeAccess` é responsável por desserializar o cache, enquanto o delegado `AfterAccess` é responsável por serializar o cache.
> - Parte desses eventos armazena ou carrega blobs, os quais são passados por meio do argumento do evento para qualquer armazenamento desejado.

As estratégias são diferentes dependendo se você está escrevendo uma serialização de cache de token para um [aplicativo cliente público](msal-client-applications.md) (desktop) ou um [aplicativo cliente confidencial](msal-client-applications.md) (aplicativo Web/API Web, aplicativo daemon).

### <a name="token-cache-for-a-public-client"></a>Cache de token para um cliente público

Desde a MSAL.NET v2.x, existem várias opções para serializar o cache de token de um cliente público. É possível serializar o cache somente para o formato MSAL.NET (o cache de formato unificado é comum entre a MSAL e as plataformas).  Também é possível dar suporte à serialização do cache de token [herdada](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) da ADAL V3.

A personalização da serialização do cache de token para compartilhar o estado de logon único entre ADAL.NET 3.x, ADAL.NET 5.x e MSAL.NET é parcialmente explicado no exemplo a seguir: [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

> [!Note]
> Não há mais suporte para o formato de cache de token MSAL.NET 1.1.4-versão prévia na MSAL 2.x. Se você tiver aplicativos que aproveitam MSAL.NET 1.x, os usuários terão que entrar novamente. Como alternativa, há suporte para a migração da ADAL 4.x (e 3.x).

#### <a name="simple-token-cache-serialization-msal-only"></a>Serialização do cache de token simples (somente MSAL)

Segue abaixo um exemplo de uma implementação ingênua da serialização personalizada de um cache de token para aplicativos da área de trabalho. Aqui, o cache de token do usuário é um arquivo na mesma pasta que a do aplicativo.

Depois de compilar o aplicativo, você habilita a serialização chamando o método `TokenCacheHelper.EnableSerialization()` e passando o aplicativo `UserTokenCache`.

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

A classe auxiliar `TokenCacheHelper` é definida como:

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache. Note that this could be something different for instance for MSIX applications:
  /// private static readonly string CacheFilePath =
  /// $"{Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData)}\{AppName}\msalcache.bin";
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

Um serializador baseado em arquivo de cache de token de qualidade de produto para aplicativos cliente públicos (para aplicativos de área de trabalho em execução no Windows, Mac e Linux) está disponível na biblioteca de código-fonte aberto [Microsoft. Identity. cliente. Extensions. MSAL](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) . É possível incluí-lo nos aplicativos do pacote NuGet a seguir: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

#### <a name="dual-token-cache-serialization-msal-unified-cache-and-adal-v3"></a>Serialização do cache de token duplo (cache unificado MSAL e ADAL v3)

Se você quiser implementar a serialização do cache de token com ambos os formatos de cache unificado (comuns à ADAL.NET 4.x, MSAL.NET 2.x e a outras MSALs da mesma geração ou mais antigas, na mesma plataforma), observe o código a seguir:

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

Desta vez, a classe auxiliar é definida como:

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Enables the serialization of the token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache tokenCache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

### <a name="token-cache-for-a-web-app-confidential-client-application"></a>Cache de token para um aplicativo Web (aplicativo cliente confidencial)

Em aplicativos Web ou APIs Web, o cache pode aproveitar a sessão, um cache Redis ou um banco de dados. Você deve manter um cache de token por conta em aplicativos Web ou APIs Web. 

Para aplicativos Web, o cache de token deve ser codificado pela ID da conta.

Para APIs Web, a conta deve ser codificada pelo hash do token usado para chamar a API.

O MSAL.NET fornece serialização personalizada de cache de token em subplataformas .NET Framework e .NET Core. Os eventos são acionados quando o cache é acessado, os aplicativos podem escolher se deseja serializar ou desserializar o cache. Em aplicativos cliente confidenciais que manipulam usuários (aplicativos Web que conectam usuários e chamam APIs da Web e APIs Web que chamam APIs da Web downstream), pode haver muitos usuários e os usuários são processados em paralelo. Por motivos de segurança e desempenho, nossa recomendação é serializar um cache por usuário. Os eventos de serialização calculam uma chave de cache com base na identidade do usuário processado e serializam/desserializam um cache de token para esse usuário.

A biblioteca [Microsoft. Identity. Web](https://github.com/AzureAD/microsoft-identity-web) fornece um pacote NuGet de visualização [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) que contém a serialização de cache de token:

| Método de extensão | Namespace Microsoft. Identity. Web sub | Descrição  |
| ---------------- | --------- | ------------ |
| `AddInMemoryTokenCaches` | `TokenCacheProviders.InMemory` | Em serialização de cache de token de memória. Essa implementação é excelente em exemplos. Também é bom em aplicativos de produção, desde que você não se preocupe se o cache do token for perdido quando o aplicativo Web for reiniciado. `AddInMemoryTokenCaches` usa um parâmetro opcional do tipo `MsalMemoryTokenCacheOptions` que permite que você especifique a duração após a qual a entrada de cache expirará, a menos que seja usada.
| `AddSessionTokenCaches` | `TokenCacheProviders.Session` | O cache do token está associado à sessão do usuário. Essa opção não será ideal se o token de ID contiver muitas declarações, uma vez que o cookie se tornaria muito grande.
| `AddDistributedTokenCaches` | `TokenCacheProviders.Distributed` | O cache de token é um adaptador em relação à implementação de ASP.NET Core `IDistributedCache` , permitindo que você escolha entre um cache de memória distribuída, um cache Redis, um NCache distribuído ou um cache SQL Server. Para obter detalhes sobre as `IDistributedCache` implementações, consulte https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache .

Veja um exemplo de como usar o cache na memória no método [configureservices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) da classe [Startup](/aspnet/core/fundamentals/startup) em um aplicativo ASP.NET Core:

```C#
// or use a distributed Token Cache by adding
    services.AddSignIn(Configuration);
    services.AddWebAppCallsProtectedWebApi(Configuration, new string[] { scopesToRequest })
            .AddInMemoryTokenCaches();
```

Exemplos de possíveis caches distribuídos:

```C#
// or use a distributed Token Cache by adding
    services.AddSignIn(Configuration);
    services.AddWebAppCallsProtectedWebApi(Configuration, new string[] { scopesToRequest })
            .AddDistributedTokenCaches();

// and then choose your implementation

// For instance the distributed in memory cache (not cleared when you stop the app)
services.AddDistributedMemoryCache()

// Or a Redis cache
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

Seu uso é apresentado no [tutorial de ASP.NET Core aplicativo Web](/aspnet/core/tutorials/first-mvc-app/) no cache de [Token](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache)da fase 2-2.

## <a name="next-steps"></a>Próximas etapas

Os exemplos a seguir ilustram a serialização do cache de token.

| Amostra | Plataforma | Descrição|
| ------ | -------- | ----------- |
|[active-directory-dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | Desktop (WPF) | Aplicativo Windows Desktop .NET (WPF) chamando a API do Microsoft Graph. ![O diagrama mostra uma topologia com o aplicativo de área de trabalho W P F TodoListClient fluindo para o Azure A D, adquirindo um token interativamente e para Microsoft Graph.](media/msal-net-token-cache-serialization/topology.png)|
|[active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) | Área de trabalho (Console) | Conjunto de soluções do Visual Studio que ilustram a migração de aplicativos do Azure AD v 1.0 (usando o ADAL.NET) para aplicativos da plataforma Microsoft Identity (usando o MSAL.NET). Em particular, consulte [migração de cache de token](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/blob/master/TokenCacheMigration/README.md)|
