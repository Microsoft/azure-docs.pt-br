---
title: Proteger o conteúdo do HLS com o Microsoft PlayReady ou o Apple FairPlay | Microsoft Docs
description: Este tópico fornece uma visão geral e mostra como usar os Serviços de Mídia do Azure para criptografar de forma dinâmica o seu conteúdo de HLS (HTTP Live Streaming) com o FairPlay da Apple. Ele também mostra como usar o serviço de distribuição de licença dos Serviços de Mídia para entregar licenças do FairPlay aos clientes.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 7763338a5c2d5ccb58fe912d9989ab5bb57d5932
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60557437"
---
# <a name="protect-your-hls-content-with-apple-fairplay-or-microsoft-playready"></a>Proteger o conteúdo do HLS com o Apple FairPlay ou Microsoft PlayReady
Os Serviços de Mídia do Azure permitem que você criptografe seu conteúdo de HLS (HTTP Live Streaming) de maneira dinâmica, usando os seguintes formatos:  

* **Chave de limpeza do envelope AES-128**

    A parte inteira é criptografada usando o modo **AES-128 CBC**. A descriptografia da transmissão tem suporte nativo do iOS e player OSX. Para saber mais, confira [Uso da criptografia dinâmica AES-128 e serviço de distribuição de chaves](media-services-protect-with-aes128.md).
* **Apple FairPlay**

    Os exemplos de áudio e vídeo individuais são criptografados usando o modo **AES-128 CBC**. **FPS** (FairPlay Streaming) é integrado aos sistemas operacionais de dispositivos, com suporte nativo no iOS e na Apple TV. O Safari no OS X habilita o FPS usando o suporte à interface EME (Extensões de Mídia Criptografada).
* **Microsoft PlayReady**

A imagem a seguir mostra o fluxo de trabalho da **criptografia dinâmica do HLS + FairPlay ou PlayReady**.

![Diagrama do fluxo de trabalho de criptografia dinâmica](./media/media-services-content-protection-overview/media-services-content-protection-with-FairPlay.png)

Este artigo demonstra como usar os Serviços de Mídia para criptografar dinamicamente o seu conteúdo de HLS com o Apple FairPlay. Ele também mostra como usar o serviço de distribuição de licença dos Serviços de Mídia para entregar licenças do FairPlay aos clientes.

> [!NOTE]
> Se quiser criptografar o conteúdo do HLS com o PlayReady, você precisará criar uma chave de conteúdo comum e associá-la ao seu ativo. Você também precisa configurar a política de autorização da chave de conteúdo, como descrito em [Uso da criptografia comum dinâmica PlayReady](media-services-protect-with-playready-widevine.md).
>
>

## <a name="requirements-and-considerations"></a>Requisitos e considerações

Veja a seguir o que é necessário ao usar os Serviços de Mídia para distribuir HLS criptografado com o FairPlay e distribuir licenças do FairPlay:

  * Uma conta do Azure. Para obter detalhes, confira [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
  * Uma conta dos Serviços de Mídia. Para criar uma, confira [Criar uma conta dos Serviços de Mídia do Azure usando o portal do Azure](media-services-portal-create-account.md).
  * Inscreva-se no [Programa de Desenvolvimento da Apple](https://developer.apple.com/).
  * A Apple exige que o proprietário do conteúdo obtenha o [pacote de implantação](https://developer.apple.com/contact/fps/). Declare que você já implementou o KSM (Módulo de Segurança de Chave) com os Serviços de Mídia e que está solicitando o pacote final do FPS. Há instruções no pacote final do FPS para gerar certificação e obter a ASK (Chave de Segredo do Aplicativo). Você usa a ASK para configurar o FairPlay.
  * SDK do .NET dos Serviços de Mídia do Azure na versão **3.6.0** ou posterior.

Os seguintes itens devem ser definidos no lado de distribuição de chaves dos Serviços de Mídia:

  * **Certificado do aplicativo (AC)**: Trata-se de um arquivo .pfx que contém a chave privada. Você cria esse arquivo e o criptografa com uma senha.

       Ao configurar a política de distribuição de chaves, você deve fornecer a senha e o .pfx no formato Base64.

      As etapas a seguir descrevem como gerar um arquivo de certificado .pfx para FairPlay:

    1. Instale o OpenSSL a partir de https://slproweb.com/products/Win32OpenSSL.html.

        Vá para a pasta onde se encontram o certificado FairPlay e outros arquivos enviados pela Apple.
    2. Execute o comando a seguir na linha de comando. Isso converte o arquivo .cer em um arquivo .pem.

        "C:\OpenSSL-Win32\bin\openssl.exe" x509 -inform der -in FairPlay.cer -out FairPlay-out.pem
    3. Execute o comando a seguir na linha de comando. Isso converte o arquivo .pem em um arquivo .pfx com a chave privada. A senha para o arquivo .pfx é solicitada pelo OpenSSL.

        "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12 -export -out FairPlay-out.pfx -inkey privatekey.pem -in FairPlay-out.pem -passin file:privatekey-pem-pass.txt
  * **Senha do certificado do aplicativo**: A senha para criar o arquivo .pfx.
  * **ID da senha do Certificado do Aplicativo**: você deve fazer upload da senha, de maneira semelhante a como faz upload de outras chaves dos Serviços de Mídia. Use o valor de enumeração **ContentKeyType.FairPlayPfxPassword** para obter a ID dos Serviços de Mídia. É necessário usá-la na opção de política de distribuição de chaves.
  * **iv**: é um valor aleatório de 16 bytes. Ele deve corresponder ao iv na política de distribuição de ativos. Você gera o iv e o coloca em dois locais: na política de distribuição de ativos e na opção de política de distribuição de chaves.
  * **ASK**: Essa chave é recebida quando você gera a certificação usando o portal do Desenvolvedor da Apple. Cada equipe de desenvolvimento receberá uma ASK exclusiva. Salve uma cópia da ASK e armazene-a em um local seguro. Você precisará configurar posteriormente a ASK como FairPlayAsk nos Serviços de Mídia.
  * **ID da ASK**: ID obtida quando você faz upload da ASK nos Serviços de Mídia. Você deve fazer upload da ASK usando o valor de enumeração **ContentKeyType.FairPlayAsk**. Como resultado, a ID dos Serviços de Mídia é retornada, que deve ser usada na configuração da opção de política de distribuição de chaves.

Os seguintes itens devem ser definidos pelo lado do cliente FPS:

  * **Certificado do aplicativo (AC)**: Trata-se de um arquivo .cer/.der que contém a chave pública que o sistema operacional usa para criptografar conteúdo. Os Serviços de Mídia precisam ter conhecimento sobre ele, uma vez que ele é exibido pelo player. O serviço de distribuição de chaves descriptografa-o usando a chave privada correspondente.

Para reproduzir uma transmissão criptografada do FairPlay, obtenha a ASK real primeiro e, em seguida, gere um certificado real. Esse processo cria três partes:

  * arquivo .der
  * arquivo .pfx
  * senha do .pfx

Os clientes a seguir dão suporte ao HLS com a criptografia **AES-128 CBC**: Safari no SO X, Apple TV, iOS.

## <a name="configure-fairplay-dynamic-encryption-and-license-delivery-services"></a>Configurar a criptografia dinâmica do FairPlay e os serviços de distribuição de licenças
Veja a seguir as etapas gerais para proteger seus ativos com o FairPlay usando o serviço de distribuição de licenças dos Serviços de Mídia e também usando a criptografia dinâmica.

1. Crie um ativo e faça upload dos arquivos no ativo.
2. Codifique o ativo que contém o arquivo para o conjunto de MP4 da taxa de bits adaptável.
3. Crie uma chave de conteúdo e associe-a ao ativo codificado.  
4. Configurar a política de autorização da chave de conteúdo. Especifique o seguinte:

   * O método de entrega (nesse caso, o FairPlay).
   * a configuração de opções de política do FairPlay. Para obter detalhes sobre como configurar o FairPlay, confira o método **ConfigureFairPlayPolicyOptions()** no exemplo abaixo.

     > [!NOTE]
     > Normalmente, convém configurar as opções de política do FairPlay apenas uma vez, visto que você terá apenas um conjunto de uma certificação e uma ASK.
     >
     >
   * Restrições (abertas ou token).
   * Informações específicas sobre o tipo de distribuição de chaves que define como a chave é fornecida ao cliente.
5. Configure a política de distribuição de ativos. A configuração da política de entrega inclui:

   * O protocolo de entrega (HLS).
   * O tipo de criptografia dinâmica (criptografia CBC comum).
   * A URL de aquisição de licença.

     > [!NOTE]
     > Se quiser fazer uma transmissão que seja criptografada com o FairPlay e outro sistema DRM (Gerenciamento de Direitos Digitais), você precisará configurar políticas de entrega separadas:
     >
     > * Uma IAssetDeliveryPolicy para configurar DASH (Transmissão Adaptável Dinâmica por HTTP) com CENC (Criptografia Comum) (PlayReady + Widevine) e Smooth com PlayReady
     > * Outro IAssetDeliveryPolicy para configurar o FairPlay para o HLS
     >
     >
6. Criar um localizador OnDemand para obter uma URL de streaming.

## <a name="use-fairplay-key-delivery-by-player-apps"></a>Usar a distribuição de chaves do FairPlay para aplicativos de player
Você pode desenvolver aplicativos player usando o SDK do iOS. Para poder reproduzir conteúdo do FairPlay, você precisa implementar o protocolo de troca de licenças. Esse protocolo não é especificado pela Apple. Depende de cada aplicativo o modo de enviar solicitações de distribuição de chaves. O serviço de distribuição de chaves do FairPlay nos Serviços de Mídia espera que o SPC seja recebido como um mensagem de postagem codificada www-form-url da seguinte forma:

    spc=<Base64 encoded SPC>

> [!NOTE]
> O Player de Mídia do Azure suporta a reprodução FairPlay. Consulte [Documentação do Player de Mídia do Azure](https://amp.azure.net/libs/amp/latest/docs/index.html) para obter mais informações.
>
>

## <a name="streaming-urls"></a>URLs de streaming
Se o ativo foi criptografado com mais de um DRM, você deve usar uma marcação de criptografia na URL de streaming: (formato='m3u8-aapl' criptografia='xxx').

As seguintes considerações se aplicam:

* Pode ser especificado apenas zero ou um tipo de criptografia.
* O tipo de criptografia não precisa ser especificado na URL se apenas uma criptografia foi aplicada no ativo.
* O tipo de criptografia não diferencia letras maiúsculas de minúsculas.
* Os seguintes tipos de criptografia podem ser especificados:  
  * **cenc**:  criptografia comum (PlayReady ou Widevine)
  * **cbcs-aapl**: FairPlay
  * **cbc**: criptografia de envelope AES

## <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto do Visual Studio

1. Configure seu ambiente de desenvolvimento e preencha o arquivo de configuração app.config com as informações de conexão, conforme descrito em [Desenvolvimento de Serviços de Mídia com o .NET](media-services-dotnet-how-to-use.md). 
2. Adicione os seguintes elementos para **appSettings** definidos no seu arquivo app.config:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

## <a name="example"></a>Exemplo

O exemplo a seguir demonstra a capacidade de usar os Serviços de Mídia para distribuir conteúdo criptografado com o FairPlay. Essa funcionalidade foi introduzida no SDK dos Serviços de Mídia do Azure para a versão 3.6.0 do .NET. 

Substitua o código no seu arquivo Program.cs pelo código mostrado nesta seção.

>[!NOTE]
>Há um limite de 1.000.000 políticas para diferentes políticas de AMS (por exemplo, para política de Localizador ou ContentKeyAuthorizationPolicy). Use a mesma ID de política, se você estiver sempre usando os mesmos dias/permissões de acesso, por exemplo, políticas de localizadores que devem permanecer no local por um longo período (políticas de não carregamento). Para saber mais, confira [este artigo](media-services-dotnet-manage-entities.md#limit-access-policies).

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
using Microsoft.WindowsAzure.MediaServices.Client.FairPlay;
using Newtonsoft.Json;
using System.Security.Cryptography.X509Certificates;

namespace DynamicEncryptionWithFairPlay
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

            IContentKey key = CreateCommonCBCTypeContentKey(encodedAsset);
            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
            Console.WriteLine("FairPlay License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay));
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
                // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
                // back into a TokenRestrictionTemplate class instance.
                TokenRestrictionTemplate tokenTemplate =
                    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

                // Generate a test token based on the data in the given TokenRestrictionTemplate.
                // Note, you need to pass the key id Guid because we specified
                // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey,
                                            DateTime.UtcNow.AddDays(365));
                Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                Console.WriteLine();
            }

            string url = GetStreamingOriginLocator(encodedAsset);
            Console.WriteLine("Encrypted HLS URL: {0}/manifest(format=m3u8-aapl)", url);

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

            IJob job = _context.Jobs.Create(String.Format("Encoding {0}", inputAsset.Name));

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

        static public IContentKey CreateCommonCBCTypeContentKey(IAsset asset)
        {
            // Create HLS SAMPLE AES encryption content key
            Guid keyId = Guid.NewGuid();
            byte[] contentKey = GetRandomBuffer(16);

            IContentKey key = _context.ContentKeys.Create(
                        keyId,
                        contentKey,
                        "ContentKey",
                        ContentKeyType.CommonEncryptionCbcs);

            // Associate the key with the asset.
            asset.ContentKeys.Add(key);

            return key;
        }


        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
        {
            // Create ContentKeyAuthorizationPolicy with Open restrictions
            // and create authorization policy          

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                    {
                    new ContentKeyAuthorizationPolicyRestriction
                    {
                        Name = "Open",
                        KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                        Requirements = null
                    }
                    };


            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();

            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
            ContentKeyDeliveryType.FairPlay,
            restrictions,
            FairPlayConfiguration);


            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with no restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

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

            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();


            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                   ContentKeyDeliveryType.FairPlay,
                   restrictions,
                   FairPlayConfiguration);

            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with token restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

            // Associate the content key authorization policy with the content key
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;

            return tokenTemplateString;
        }

        private static string ConfigureFairPlayPolicyOptions()
        {
            // For testing you can provide all zeroes for ASK bytes together with the cert from Apple FPS SDK.
            // However, for production you must use a real ASK from Apple bound to a real prod certificate.
            byte[] askBytes = Guid.NewGuid().ToByteArray();
            var askId = Guid.NewGuid();
            // Key delivery retrieves askKey by askId and uses this key to generate the response.
            IContentKey askKey = _context.ContentKeys.Create(
                        askId,
                        askBytes,
                        "askKey",
                        ContentKeyType.FairPlayASk);

            //Customer password for creating the .pfx file.
            string pfxPassword = "<customer password for creating the .pfx file>";
            // Key delivery retrieves pfxPasswordKey by pfxPasswordId and uses this key to generate the response.
            var pfxPasswordId = Guid.NewGuid();
            byte[] pfxPasswordBytes = System.Text.Encoding.UTF8.GetBytes(pfxPassword);
            IContentKey pfxPasswordKey = _context.ContentKeys.Create(
                        pfxPasswordId,
                        pfxPasswordBytes,
                        "pfxPasswordKey",
                        ContentKeyType.FairPlayPfxPassword);

            // iv - 16 bytes random value, must match the iv in the asset delivery policy.
            byte[] iv = Guid.NewGuid().ToByteArray();

            //Specify the .pfx file created by the customer.
            var appCert = new X509Certificate2("path to the .pfx file created by the customer", pfxPassword, X509KeyStorageFlags.Exportable);

            string FairPlayConfiguration =
            Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
                appCert,
                pfxPassword,
                pfxPasswordId,
                askId,
                iv);

            return FairPlayConfiguration;
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

        static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
        {
            var kdPolicy = _context.ContentKeyAuthorizationPolicies.Where(p => p.Id == key.AuthorizationPolicyId).Single();

            var kdOption = kdPolicy.Options.Single(o => o.KeyDeliveryType == ContentKeyDeliveryType.FairPlay);

            FairPlayConfiguration configFP = JsonConvert.DeserializeObject<FairPlayConfiguration>(kdOption.KeyDeliveryConfiguration);

            // Get the FairPlay license service URL.
            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay);

            // The reason the below code replaces "https://" with "skd://" is because
            // in the IOS player sample code which you obtained in Apple developer account,
            // the player only recognizes a Key URL that starts with skd://.
            // However, if you are using a customized player,
            // you can choose whatever protocol you want.
            // For example, "https".

            Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                    {AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, acquisitionUrl.ToString().Replace("https://", "skd://")},
                    {AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs, configFP.ContentEncryptionIV}
            };

            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            assetDeliveryPolicyConfiguration);

            // Add AssetDelivery Policy to the asset
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

            // Create a 30-day readonly access policy.
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

## <a name="next-steps-media-services-learning-paths"></a>Próximas etapas: Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
