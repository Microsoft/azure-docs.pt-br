---
title: Usar a criptografia comum dinâmica PlayReady e/ou Widevine | Microsoft Docs
description: Você pode usar os Serviços de Mídia do Microsoft Azure para fornecer fluxos MPEG-DASH, Smooth Streaming e HLS (HTTP Live Streaming) protegidos com o DRM do Microsoft PlayReady. Você também pode usá-los para o fornecimento de DASH criptografado com o DRM do Widevine. Este tópico mostra como criptografar dinamicamente com o DRM do PlayReady e do Widevine.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 548d1a12-e2cb-45fe-9307-4ec0320567a2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: df967c56d84650894d2e07054e9ec8d6f830192b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711720"
---
# <a name="use-playready-andor-widevine-dynamic-common-encryption"></a>Usar a criptografia comum dinâmica PlayReady e/ou Widevine

> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-playready-widevine.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
>

> [!NOTE]
> Para obter a versão mais recente do SDK do Java e começar a desenvolver com Java, confira [Introdução ao SDK de cliente Java para os Serviços de Mídia do Azure](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use). <br/>
> Para baixar o SDK mais recente do PHP para os Serviços de Mídia, procure a versão 0.5.7 do pacote do Microsoft/WindowsAzure no [Repositório do Packagist](https://packagist.org/packages/microsoft/windowsazure#v0.5.7). 

## <a name="overview"></a>Visão geral

 Você pode usar os Serviços de Mídia para fornecer fluxos MPEG-DASH, Smooth Streaming e HLS (HTTP Live Streaming) protegidos com o [DRM do PlayReady](https://www.microsoft.com/playready/overview/). Também pode fornecer fluxos DASH criptografados com licenças DRM do Widevine. O PlayReady e o Widevine são criptografados de acordo com a especificação de criptografia comum (ISO/IEC 23001-7 CENC). Você pode usar o [SDK do .NET dos Serviços de Mídia](https://www.nuget.org/packages/windowsazure.mediaservices/) (começando da versão 3.5.1) ou a API REST para configurar seu AssetDeliveryConfiguration ao uso do Widevine.

Os Serviços de Mídia fornecem um serviço para entregar licenças DRM do PlayReady e do Widevine. Os Serviços de Mídia também fornecem APIs que você pode usar para configurar os direitos e restrições que você deseja que sejam impostos pelo tempo de execução do DRM do PlayReady ou do Widevine quando um usuário reproduz conteúdo protegido. Quando um usuário solicitar conteúdo protegido por DRM, o player de mídia solicitará uma licença do serviço de licença dos Serviços de Mídia. Se o player de mídia estiver autorizado, o serviço de licença dos Serviços de Mídia emitirá uma licença para o player. Uma licença do PlayReady ou do Widevine contém a chave de descriptografia que pode ser usada pelo player cliente para descriptografar e transmitir o conteúdo.

Você também pode usar os seguintes parceiros dos Serviços de Mídia para ajudá-lo a fornecer licenças do Widevine: 

* [Axinom](https://www.axinom.com/press/ibc-axinom-drm-6/) 
* [EZDRM](https://ezdrm.com/) 
* [castLabs](https://castlabs.com/company/partners/azure/) 

Para saber mais, veja: integração ao [Axinom](media-services-axinom-integration.md) e ao [castLabs](media-services-castlabs-integration.md).

Os serviços de mídia oferecem suporte a várias maneiras de autorizar os usuários que fazem solicitações de chave. A política de autorização da chave de conteúdo pode ter uma ou mais restrições de autorização: abertas ou de token. A política restrita do token deve ser acompanhada por um token emitido por um Serviço de Token de Segurança (STS). Os Serviços de Mídia oferecem suporte a tokens nos formatos [Simple Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) (SWT) e [Token Web JSON](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT). 

Para saber mais, veja [Configure a política de autorização da chave de conteúdo](media-services-protect-with-aes128.md#configure_key_auth_policy).

Para aproveitar a criptografia dinâmica, você precisa ter um ativo que contenha um conjunto de arquivos MP4 com múltiplas taxas de bits ou arquivos de origem de Smooth Streaming com múltiplas taxas de bits. Você também precisa configurar as políticas de entrega para o ativo (descrita mais adiante neste tópico). Em seguida, com base no formato especificado na URL de streaming, o servidor de streaming sob demanda garante que você receba o fluxo no protocolo escolhido. Como resultado, você armazena e paga por arquivos em um único formato de armazenamento. Os Serviços de Mídia criam e fornecem a resposta HTTP apropriada com base nas solicitações do cliente.

Este artigo é útil para os desenvolvedores que trabalham em aplicativos que fornecem mídia protegida com vários DRMs, como PlayReady e Widevine. O artigo mostra como configurar o serviço de distribuição de licenças do PlayReady com políticas de autorização para que somente os clientes autorizados possam receber licenças do PlayReady ou do Widevine. Também mostra como usar a criptografia dinâmica com gerenciamento de direitos digitais do PlayReady ou Widevine em vez de DASH.

>[!NOTE]
>Quando sua conta dos Serviços de Mídia é criada, um ponto de extremidade de streaming padrão é adicionado à sua conta em estado "Parado". Para iniciar seu conteúdo de streaming e aproveitar o empacotamento dinâmico e a criptografia dinâmica, o ponto de extremidade de streaming do qual você deseja transmitir o conteúdo deve estar no estado "Executando". 

## <a name="download-the-sample"></a>Baixar o exemplo
Você pode baixar o exemplo descrito neste artigo nos [exemplos do Azure no GitHub](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm).

## <a name="configure-dynamic-common-encryption-and-drm-license-delivery-services"></a>Configurar a criptografia dinâmica comum e os serviços de distribuição de licenças de DRM

Execute as seguintes etapas gerais ao proteger seus ativos com o PlayReady usando o serviço de distribuição de licenças dos Serviços de Mídia, bem como usando a criptografia dinâmica:

1. Crie um ativo e faça upload dos arquivos no ativo.

2. Codifique o ativo que contém o arquivo para o conjunto de MP4 da taxa de bits adaptável.

3. Crie uma chave de conteúdo e associe-a ao ativo codificado. Nos Serviços de Mídia, a chave de conteúdo contém a chave de criptografia do ativo.

4. Configure a política de autorização da chave de conteúdo. Você deve configurar a política de autorização da chave de conteúdo. O cliente deve estar em conformidade com a política antes de a chave de conteúdo ser entregue ao cliente.

    Quando você cria a política de autorização da chave de conteúdo, deve especificar o método de entrega (PlayReady ou Widevine) e as restrições (abertas ou de token). Você também deve especificar informações peculiares ao tipo de distribuição de chave que define como a chave é entregue ao cliente (modelo de licença [PlayReady](media-services-playready-license-template-overview.md) ou [Widevine](media-services-widevine-license-template-overview.md)).

5. Configurar a política de entrega para um ativo. A configuração de política de entrega inclui o protocolo de entrega (por exemplo, MPEG-DASH, HLS, Smooth Streaming ou todos). A configuração também inclui o tipo de criptografia dinâmica (por exemplo, criptografia comum) e a URL de aquisição de licenças PlayReady ou Widevine.

    Você pode aplicar uma política diferente a cada protocolo no mesmo ativo. Por exemplo, você poderia aplicar a criptografia PlayReady a Smooth/DASH e aplicar Envelope de AES a HLS. Todos os protocolos não definidos em uma política de entrega (por exemplo, se você adicionar uma única política que só especifica HLS como o protocolo) serão bloqueados do streaming. Só há exceção se você não tiver nenhuma política de entrega de ativos definida. Em seguida, todos os protocolos podem ser criptografados.

6. Criar um localizador OnDemand para obter uma URL de streaming.

Você encontra um exemplo de .NET completo no final do artigo.

A imagem a seguir demonstra o fluxo de trabalho descrito acima. Aqui, o token é usado para a autenticação.

![Proteger com o PlayReady](media/media-services-content-protection-overview/media-services-content-protection-with-drm.png)

O restante deste artigo fornece explicações detalhadas, exemplos de código e links para tópicos que mostram como realizar as tarefas descritas anteriormente.

## <a name="current-limitations"></a>Limitações atuais
Se adicionar ou atualizar uma política de entrega de ativos, você deverá excluir todo localizador associado e criar um novo.

Atualmente, várias chaves de conteúdo não são suportadas quando se criptografa usando o Widevine com os Serviços de Mídia. 

## <a name="create-an-asset-and-upload-files-into-the-asset"></a>Criar um ativo e carregar arquivos no ativo
Para gerenciar, codificar e transmitir seus vídeos, você deve primeiro carregar o conteúdo nos Serviços de Mídia. Depois de carregado, seu conteúdo é armazenado com segurança na nuvem para processamento adicional e transmissão.

Para mais informações, veja [Carregar arquivos em uma conta dos Serviços de Mídia](media-services-dotnet-upload-files.md).

## <a name="encode-the-asset-that-contains-the-file-to-the-adaptive-bitrate-mp4-set"></a>Codificar o ativo que contém o arquivo para o conjunto de MP4 da taxa de bits adaptável
Com a criptografia dinâmica, você cria um ativo que contenha um conjunto de arquivos MP4 com múltiplas taxas de bits ou arquivos de origem de Smooth Streaming com múltiplas taxas de bits. Em seguida, com base no formato especificado na solicitação de fragmento e de manifesto, o servidor de Streaming OnDemand garante que você receba o fluxo no protocolo escolhido por você. Depois, você armazena e paga por arquivos em um único formato de armazenamento. Os Serviços de Mídia criam e fornecem a resposta apropriada com base nas solicitações de um cliente. Para saber mais, confira [Visão geral do empacotamento dinâmico](media-services-dynamic-packaging-overview.md).

Para obter instruções sobre como codificar, veja [Codificar um ativo usando o Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md).

## <a id="create_contentkey"></a>Criar uma chave de conteúdo e associá-la ao ativo codificado
Nos Serviços de Mídia, a chave de conteúdo contém a chave com a qual você deseja criptografar um ativo.

Para saber mais, consute [Criar uma chave de conteúdo](media-services-dotnet-create-contentkey.md).

## <a id="configure_key_auth_policy"></a>Configurar a política de autorização da chave de conteúdo
Os serviços de mídia oferecem suporte a várias maneiras de autenticar os usuários que fazem solicitações de chave. Você deve configurar a política de autorização da chave de conteúdo. O cliente (player) deve estar em conformidade com a política antes de a chave ser entregue ao cliente. A política de autorização da chave de conteúdo pode ter uma ou mais restrições de autorização: abertas ou de token.

Para saber mais, veja [Configurar uma política de autorização da chave de conteúdo](media-services-dotnet-configure-content-key-auth-policy.md#playready-dynamic-encryption).

## <a id="configure_asset_delivery_policy"></a>Configurar uma política de entrega de ativos
Configure a política de entrega para seu ativo. Algumas coisas incluídas na configuração de política de entrega de ativos são:

* A URL de aquisição de licença de DRM.
* O protocolo de entrega de ativos (por exemplo, MPEG DASH, HLS, Smooth Streaming ou todos).
* O tipo de criptografia dinâmica (nesse caso, criptografia comum).

Para obter mais informações, veja [Configurar a política de entrega de ativos](media-services-dotnet-configure-asset-delivery-policy.md).

## <a id="create_locator"></a>Criar um localizador de streaming OnDemand para obter uma URL de streaming
Você precisa fornecer ao seu usuário a URL de streaming para Smooth Streaming, DASH ou HLS.

> [!NOTE]
> Se você adicionar ou atualizar a política de entrega do ativo, deverá excluir um localizador existente e criar um novo localizador.
>
>

Para obter instruções sobre como publicar um ativo e criar uma URL de streaming, consulte [Criar uma URL de streaming](media-services-deliver-streaming-content.md).

## <a name="get-a-test-token"></a>Obter um token de teste
Obtenha um token de teste com base na restrição de token que foi usada para a política de autorização da chave.

```csharp
// Deserializes a string containing an XML representation of a TokenRestrictionTemplate
// back into a TokenRestrictionTemplate class instance.
TokenRestrictionTemplate tokenTemplate =
TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

// Generate a test token based on the data in the given TokenRestrictionTemplate.
//The GenerateTestToken method returns the token without the word "Bearer" in front,
//so you have to add it in front of the token string.
string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
```

Pode usar o [Azure Media Services Player](https://amsplayer.azurewebsites.net/azuremediaplayer.html) para testar seu fluxo.

## <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto do Visual Studio

1. Configure seu ambiente de desenvolvimento e preencha o arquivo de configuração app.config com as informações de conexão, conforme descrito em [Desenvolvimento dos Serviços de Mídia com o .NET](media-services-dotnet-how-to-use.md).

2. Adicione os seguintes elementos para **appSettings** definidos no seu arquivo app.config:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

## <a name="example"></a>Exemplo

O exemplo a seguir demonstra a funcionalidade que foi introduzida no SDK dos Serviços de Mídia para o .NET versão 3.5.2. (Especificamente, inclui a capacidade de definir um modelo de licença Widevine e solicitar uma licença Widevine dos Serviços de Mídia.)

Substitua o código no seu arquivo Program.cs pelo código mostrado nesta seção.

>[!NOTE]
>Há um limite de 1 milhão de políticas para diferentes políticas dos Serviços de Mídia (por exemplo, para política de Localizador ou ContentKeyAuthorizationPolicy). Se você sempre pode usar as mesmas permissões de acesso/dias, use a mesma ID de política. Podemos citar como exemplo as políticas de localizadores que devam permanecer em vigor por um longo período (políticas sem carregamento). 

Para obter mais informações, veja [Gerenciar ativos e entidades relacionadas ao SDK dos Serviços de Mídia .NET](media-services-dotnet-manage-entities.md#limit-access-policies).

Certifique-se de atualizar as variáveis para que indiquem as pastas onde estão localizados os arquivos de entrada.

```csharp
using System;
using System.Collections.Generic;
using System.Configuration;
using System.IO;
using System.Linq;
using System.Threading;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
using Microsoft.WindowsAzure.MediaServices.Client.Widevine;
using Newtonsoft.Json;

namespace DynamicEncryptionWithDRM
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static readonly Uri _sampleIssuer =
            new Uri(ConfigurationManager.AppSettings["Issuer"]);
        private static readonly Uri _sampleAudience =
            new Uri(ConfigurationManager.AppSettings["Audience"]);

        // Field for service context.
        private static CloudMediaContext _context = null;

        private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            bool tokenRestriction = false;
            string tokenTemplateString = null;

            IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
            Console.WriteLine("Uploaded asset: {0}", asset.Id);

            IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
            Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);

            IContentKey key = CreateCommonTypeContentKey(encodedAsset);
            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
            Console.WriteLine("PlayReady License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));
            Console.WriteLine();

            if (tokenRestriction)
                tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
            else
                AddOpenAuthorizationPolicy(key);

            Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
            Console.WriteLine();

            CreateAssetDeliveryPolicy(encodedAsset, key);
            Console.WriteLine("Created asset delivery policy. \n");
            Console.WriteLine();

            if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
            {
                // Deserializes a string containing an XML representation of a TokenRestrictionTemplate
                // back into a TokenRestrictionTemplate class instance.
                TokenRestrictionTemplate tokenTemplate =
                    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

                // Generate a test token based on the data in the given TokenRestrictionTemplate.
                // Note that you need to pass the key ID GUID because 
                // TokenClaim.ContentKeyIdentifierClaim was specified during the creation of TokenRestrictionTemplate.
                Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey,
                                            DateTime.UtcNow.AddDays(365));
                Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                Console.WriteLine();
            }

            // You can use the https://amsplayer.azurewebsites.net/azuremediaplayer.html player to test streams.
            // Note that DASH works on Internet Explorer 11 (via PlayReady), Microsoft Edge (via PlayReady), and Chrome (via Widevine).

            string url = GetStreamingOriginLocator(encodedAsset);
            Console.WriteLine("Encrypted DASH URL: {0}/manifest(format=mpd-time-csf)", url);

            Console.ReadLine();
        }

        static public IAsset UploadFileAndCreateAsset(string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
        {
            var encodingPreset = "Adaptive Streaming";

            IJob job = _context.Jobs.Create(String.Format("Encoding into Mp4 {0} to {1}",
                        inputAsset.Name,
                        encodingPreset));

            var mediaProcessors =
            _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();

            var latestMediaProcessor =
            mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();

            ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
            encodeTask.InputAssets.Add(inputAsset);
            encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), AssetCreationOptions.StorageEncrypted);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }


        static public IContentKey CreateCommonTypeContentKey(IAsset asset)
        {

            Guid keyId = Guid.NewGuid();
            byte[] contentKey = GetRandomBuffer(16);

            IContentKey key = _context.ContentKeys.Create(
                        keyId,
                        contentKey,
                        "ContentKey",
                        ContentKeyType.CommonEncryption);

            // Associate the key with the asset.
            asset.ContentKeys.Add(key);

            return key;
        }

        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
        {

            // Create ContentKeyAuthorizationPolicy with open restrictions
            // and create an authorization policy.         

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                {
                new ContentKeyAuthorizationPolicyRestriction
                {
                    Name = "Open",
                    KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                    Requirements = null
                }
                };

            // Configure PlayReady and Widevine license templates.
            string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

            string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();

            IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.PlayReadyLicense,
                restrictions, PlayReadyLicenseTemplate);

            IContentKeyAuthorizationPolicyOption WidevinePolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.Widevine,
                restrictions, WidevineLicenseTemplate);

            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common Content Key with no restrictions").
                Result;


            contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
            contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
            // Associate the content key authorization policy with the content key.
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;
        }

        public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
        {
            string tokenTemplateString = GenerateTokenRequirements();

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                {
                new ContentKeyAuthorizationPolicyRestriction
                {
                    Name = "Token Authorization Policy",
                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                    Requirements = tokenTemplateString,
                }
                };

            // Configure PlayReady and Widevine license templates.
            string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

            string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();

            IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.PlayReadyLicense,
                restrictions, PlayReadyLicenseTemplate);

            IContentKeyAuthorizationPolicyOption WidevinePolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.Widevine,
                restrictions, WidevineLicenseTemplate);

            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common Content Key with token restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
            contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);

            // Associate the content key authorization policy with the content key.
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;

            return tokenTemplateString;
        }

        static private string GenerateTokenRequirements()
        {
            TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

            template.PrimaryVerificationKey = new SymmetricVerificationKey();
            template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();
            template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

            return TokenRestrictionTemplateSerializer.Serialize(template);
        }

        static private string ConfigurePlayReadyLicenseTemplate()
        {
            // The following code configures the PlayReady license template by using .NET classes
            // and returns the XML string.

            //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user.
            //It contains a field for a custom data string between the license server and the application
            //(which might be useful for custom app logic) as well as a list of one or more license templates.
            PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();

            // The PlayReadyLicenseTemplate class represents a license template you can use to create PlayReady licenses
            // to be returned to end users.
            //It contains the data on the content key in the license and any rights or restrictions to be
            //enforced by the PlayReady DRM runtime when you use the content key.
            PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
            //Configure whether the license is persistent (saved in persistent storage on the client)
            //or nonpersistent (held in memory only while the player uses the license).  
            licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;

            // AllowTestDevices controls whether test devices can use the license or not.  
            // If true, the MinimumSecurityLevel property of the license
            // is set to 150. If false (the default), the MinimumSecurityLevel property of the license is set to 2,000.
            licenseTemplate.AllowTestDevices = true;

            // You also can configure the PlayRight in the PlayReady license by using the PlayReadyPlayRight class.
            // It grants the user the ability to play back the content subject to the zero or more restrictions
            // configured in the license and on the PlayRight itself (for playback-specific policy).
            // Much of the policy on the PlayRight has to do with output restrictions,
            // which control the types of outputs that the content can be played over and
            // any restrictions that must be put in place when you use a given output.
            // For example, if DigitalVideoOnlyContentRestriction is enabled,
            // the DRM runtime allows the video to be displayed only over digital outputs
            //(analog video outputs aren't allowed to pass the content).

            //IMPORTANT: These types of restrictions can be very powerful but also can affect the consumer experience.
            // If output protections are too restrictive, 
            // content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.

            // For example:
            //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

            responseTemplate.LicenseTemplates.Add(licenseTemplate);

            return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
        }

        private static string ConfigureWidevineLicenseTemplate()
        {
            var template = new WidevineMessage
            {
                allowed_track_types = AllowedTrackTypes.SD_HD,
                content_key_specs = new[]
            {
                    new ContentKeySpecs
                    {
                    required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                    security_level = 1,
                    track_type = "SD"
                    }
                },
                policy_overrides = new
                {
                    can_play = true,
                    can_persist = true,
                    can_renew = false
                }
            };

            string configuration = JsonConvert.SerializeObject(template);
            return configuration;
        }

        static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
        {
            // Get the PlayReady license service URL.
            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);

            // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
            // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
            // WidevineBaseLicenseAcquisitionUrl (used in the following) also tells dynamic encryption
            // to append /? KID =< keyId > to the end of the URL when you create the manifest.
            // As a result, the Widevine license acquisition URL has the KID appended twice,
            // so you need to remove the KID in the URL when you call GetKeyDeliveryUrl.

            Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
            UriBuilder uriBuilder = new UriBuilder(widevineUrl);
            uriBuilder.Query = String.Empty;
            widevineUrl = uriBuilder.Uri;

            Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                    {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
                    {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl, widevineUrl.ToString()}

            };

            // In this case, we specify only the DASH streaming protocol in the delivery policy.
            // All other protocols are blocked from streaming.
            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


            // Add AssetDelivery Policy to the asset.
            asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        }

        /// <summary>
        /// Gets the streaming origin locator.
        /// </summary>
        /// <param name="assets"></param>
        /// <returns></returns>
        static public string GetStreamingOriginLocator(IAsset asset)
        {

            // Get a reference to the streaming manifest file from the 
            // collection of files in the asset.

            var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                         EndsWith(".ism")).
                         FirstOrDefault();

            // Create a 30-day read-only access policy.
            IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

            // Create a locator to the streaming content on an origin.
            ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

            // Create a URL to the manifest file.
            return originLocator.Path + assetFile.Name;
        }

        static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
            ((IJob)sender).Name,
            e.CurrentState,
            DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
        }

        static private byte[] GetRandomBuffer(int length)
        {
            var returnValue = new byte[length];

            using (var rng =
            new System.Security.Cryptography.RNGCryptoServiceProvider())
            {
                rng.GetBytes(returnValue);
            }

            return returnValue;
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Consulte também
* [Use o CENC com DRM múltiplo e controle de acesso](media-services-cenc-with-multidrm-access-control.md)
* [Configurar o empacotamento Widevine com os Serviços de Mídia](https://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services)
* [Anunciando os serviços de entrega de licenças do Google Widevine nos Serviços de Mídia do Azure](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
