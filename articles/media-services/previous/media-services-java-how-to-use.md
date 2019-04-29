---
title: Iniciar o uso do SDK de Java para Serviços de Mídia do Azure | Microsoft Docs
description: Este tutorial orienta você ao longo das etapas de implementação de um serviço básico de fornecimento de conteúdo de VoD (Vídeo sob Demanda) com o aplicativo AMS (Serviços de Mídia do Azure) usando Java.
services: media-services
documentationcenter: java
author: juliako
manager: femila
editor: johndeu
ms.assetid: b884bd61-dbdb-42ea-b170-8fb02e7fded7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: a12d0e7d4382d1f8feac721c103d9231e54ad249
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61463821"
---
# <a name="get-started-with-the-java-client-sdk-for-azure-media-services"></a>Introdução ao SDK de cliente Java para Serviços de Mídia do Azure  

[!INCLUDE [media-services-selector-get-started](../../../includes/media-services-selector-get-started.md)]

Este tutorial orienta você ao longo das etapas de implementação de um serviço básico de fornecimento de conteúdo de vídeo com o Serviços de Mídia do Azure usando o SDK de cliente Java.

## <a name="prerequisites"></a>Pré-requisitos

Os itens a seguir são necessários para concluir este tutorial:

* Uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Uma conta dos Serviços de Mídia. Para criar uma conta de Serviços de Mídia, consulte [Como criar uma conta de Serviços de Mídia](media-services-portal-create-account.md).
* O atual [SDK de Java dos Serviços de Mídia do Azure](https://mvnrepository.com/artifact/com.microsoft.azure/azure-media/latest)

## <a name="how-to-import-the-azure-media-services-java-client-sdk-package"></a>Como: importar o pacote de SDK de cliente Java dos Serviços de Mídia do Azure

Para começar a usar o SDK do Media Services para Java, adicione uma referência para a versão atual (0.9.8) do pacote `azure-media` do [SDK de Java do Serviços de Mídia do Azure](https://mvnrepository.com/artifact/com.microsoft.azure/azure-media/latest)

Por exemplo, se a ferramenta de compilação é `gradle`, adicione a seguinte dependência ao seu arquivo `build.gradle`:

    compile group: 'com.microsoft.azure', name: 'azure-media', version: '0.9.8'

>[!IMPORTANT]
>Começando com o pacote `azure-media` versão `0.9.8`, o SDK adicionou suporte para autenticação do Azure Active Directory (AAD) e removeu o suporte para autenticação do Serviço de Controle de Acesso do Azure (ACS). Recomendamos que você migre para o modelo de autenticação do Azure AD assim que possível. Para obter detalhes sobre migração, leia o artigo [Acessar a API dos Serviços de Mídia do Azure com autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md).

>[!NOTE]
>Você pode encontrar o código-fonte do SDK de Java do Serviços de Mídia do Azure em nosso [repositório GitHub](https://github.com/Azure/azure-sdk-for-java/tree/0.9/services/azure-media). Não se esqueça de alternar para o branch 0.9 e não o branch mestre. 

## <a name="how-to-use-azure-media-services-with-java"></a>Como: usar os Serviços de Mídia do Azure com o Java

>[!NOTE]
>Quando sua conta dos Serviços de Mídia é criada, um ponto de extremidade de streaming **padrão** é adicionado à sua conta em estado **Parado**. Para iniciar seu conteúdo de streaming e tirar proveito do empacotamento dinâmico e da criptografia dinâmica, o ponto de extremidade de streaming do qual você deseja transmitir o conteúdo deve estar em estado **Executando**.

O código a seguir mostra como criar um ativo, carregar um arquivo de mídia para o ativo, executar um trabalho com uma tarefa para transformar o ativo e criar um localizador para transmitir seu vídeo.

Configure uma conta de Serviços de Mídia antes de usar esse código. Para obter informações sobre como configurar uma conta, consulte [Como criar uma conta de Serviços de Mídia](media-services-portal-create-account.md).

O código conecta-se à API de Serviços de Mídia do Azure usando a autenticação de entidade de serviço do Azure AD. Crie um aplicativo do Azure AD e especifique os valores para as seguintes variáveis no código:
* `tenant`: o domínio de locatário do Azure AD em que reside o aplicativo do Azure AD
* `clientId`: a ID do cliente do aplicativo do Azure AD
* `clientKey`: a chave de cliente do aplicativo do Azure AD
* `restApiEndpoint`: o ponto de extremidade da API REST da conta de Serviços de Mídia do Azure

Você pode criar um aplicativo do Azure AD e obter os valores de configuração anteriores do portal do Azure. Para obter mais informações, confira a seção **Introdução da entidade de serviço** da [Introdução à autenticação do Azure AD usando o portal do Azure](https://docs.microsoft.com/azure/media-services/media-services-portal-get-started-with-aad).

O código também depende de um arquivo de vídeo armazenado localmente. Você deve editar o código para fornecer seu próprio arquivo local para carregar.

```java
    import java.io.*;
    import java.net.URI;
    import java.security.NoSuchAlgorithmException;
    import java.util.EnumSet;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;

    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.exception.ServiceException;
    import com.microsoft.windowsazure.services.media.MediaConfiguration;
    import com.microsoft.windowsazure.services.media.MediaContract;
    import com.microsoft.windowsazure.services.media.MediaService;
    import com.microsoft.windowsazure.services.media.WritableBlobContainerContract;
    import com.microsoft.windowsazure.services.media.authentication.AzureAdClientSymmetricKey;
    import com.microsoft.windowsazure.services.media.authentication.AzureAdTokenCredentials;
    import com.microsoft.windowsazure.services.media.authentication.AzureAdTokenProvider;
    import com.microsoft.windowsazure.services.media.authentication.AzureEnvironments;
    import com.microsoft.windowsazure.services.media.models.AccessPolicy;
    import com.microsoft.windowsazure.services.media.models.AccessPolicyInfo;
    import com.microsoft.windowsazure.services.media.models.AccessPolicyPermission;
    import com.microsoft.windowsazure.services.media.models.Asset;
    import com.microsoft.windowsazure.services.media.models.AssetFile;
    import com.microsoft.windowsazure.services.media.models.AssetFileInfo;
    import com.microsoft.windowsazure.services.media.models.AssetInfo;
    import com.microsoft.windowsazure.services.media.models.Job;
    import com.microsoft.windowsazure.services.media.models.JobInfo;
    import com.microsoft.windowsazure.services.media.models.JobState;
    import com.microsoft.windowsazure.services.media.models.ListResult;
    import com.microsoft.windowsazure.services.media.models.Locator;
    import com.microsoft.windowsazure.services.media.models.LocatorInfo;
    import com.microsoft.windowsazure.services.media.models.LocatorType;
    import com.microsoft.windowsazure.services.media.models.MediaProcessor;
    import com.microsoft.windowsazure.services.media.models.MediaProcessorInfo;
    import com.microsoft.windowsazure.services.media.models.Task;

    public class Program
    {
        // Media Services account credentials configuration
        private static String tenant = "tenant.domain.com";
        private static String clientId = "<client id>";
        private static String clientKey = "<client key>";
        private static String restApiEndpoint = "https://account_name.restv2.region_name.media.azure.net/api/";

        // Media Services API
        private static MediaContract mediaService;

        // Encoder configuration
        // This is using the default Adaptive Streaming encoding preset. 
        // You can choose to use a custom preset, or any other sample defined preset. 
        // In addition you can use other processors, like Speech Analyzer, or Redactor if desired.
        private static String preferredEncoder = "Media Encoder Standard";
        private static String encodingPreset = "Adaptive Streaming";

        public static void main(String[] args)
        {
            ExecutorService executorService = Executors.newFixedThreadPool(1);

            try {
                // Setup Azure AD Service Principal Symmetric Key Credentials
                AzureAdTokenCredentials credentials = new AzureAdTokenCredentials(
                        tenant,
                        new AzureAdClientSymmetricKey(clientId, clientKey),
                        AzureEnvironments.AZURE_CLOUD_ENVIRONMENT);

                AzureAdTokenProvider provider = new AzureAdTokenProvider(credentials, executorService);

                // Create a new configuration with the credentials
                Configuration configuration = MediaConfiguration.configureWithAzureAdTokenProvider(
                        new URI(restApiEndpoint),
                        provider);

                // Create the media service provisioned with the new configuration
                mediaService = MediaService.create(configuration);

                // Upload a local file to an Asset
                AssetInfo uploadAsset = uploadFileAndCreateAsset("Video Name", "C:/path/to/video.mp4");
                System.out.println("Uploaded Asset Id: " + uploadAsset.getId());

                // Transform the Asset
                AssetInfo encodedAsset = encode(uploadAsset);
                System.out.println("Encoded Asset Id: " + encodedAsset.getId());

                // Create the Streaming Origin Locator
                String url = getStreamingOriginLocator(encodedAsset);

                System.out.println("Origin Locator URL: " + url);
                System.out.println("Sample completed!");

            } catch (ServiceException se) {
                System.out.println("ServiceException encountered.");
                System.out.println(se.toString());
            } catch (Exception e) {
                System.out.println("Exception encountered.");
                System.out.println(e.toString());
            } finally {
                executorService.shutdown();
            }
        }

        private static AssetInfo uploadFileAndCreateAsset(String assetName, String fileName)
            throws ServiceException, FileNotFoundException, NoSuchAlgorithmException {

            WritableBlobContainerContract uploader;
            AssetInfo resultAsset;
            AccessPolicyInfo uploadAccessPolicy;
            LocatorInfo uploadLocator = null;

            // Create an Asset
            resultAsset = mediaService.create(Asset.create().setName(assetName).setAlternateId("altId"));
            System.out.println("Created Asset " + fileName);

            // Create an AccessPolicy that provides Write access for 15 minutes
            uploadAccessPolicy = mediaService
                .create(AccessPolicy.create("uploadAccessPolicy", 15.0, EnumSet.of(AccessPolicyPermission.WRITE)));

            // Create a Locator using the AccessPolicy and Asset
            uploadLocator = mediaService
                .create(Locator.create(uploadAccessPolicy.getId(), resultAsset.getId(), LocatorType.SAS));

            // Create the Blob Writer using the Locator
            uploader = mediaService.createBlobWriter(uploadLocator);

            File file = new File(fileName);

            // The local file that will be uploaded to your Media Services account
            InputStream input = new FileInputStream(file);

            System.out.println("Uploading " + fileName);

            // Upload the local file to the media asset
            uploader.createBlockBlob(file.getName(), input);

            // Inform Media Services about the uploaded files
            mediaService.action(AssetFile.createFileInfos(resultAsset.getId()));
            System.out.println("Uploaded Asset File " + fileName);

            mediaService.delete(Locator.delete(uploadLocator.getId()));
            mediaService.delete(AccessPolicy.delete(uploadAccessPolicy.getId()));

            return resultAsset;
        }

        // Create a Job that contains a Task to transform the Asset
        private static AssetInfo encode(AssetInfo assetToEncode)
            throws ServiceException, InterruptedException {

            // Retrieve the list of Media Processors that match the name
            ListResult<MediaProcessorInfo> mediaProcessors = mediaService
                            .list(MediaProcessor.list().set("$filter", String.format("Name eq '%s'", preferredEncoder)));

            // Use the latest version of the Media Processor
            MediaProcessorInfo mediaProcessor = null;
            for (MediaProcessorInfo info : mediaProcessors) {
                if (null == mediaProcessor || info.getVersion().compareTo(mediaProcessor.getVersion()) > 0) {
                    mediaProcessor = info;
                }
            }

            System.out.println("Using Media Processor: " + mediaProcessor.getName() + " " + mediaProcessor.getVersion());

            // Create a task with the specified Media Processor
            String outputAssetName = String.format("%s as %s", assetToEncode.getName(), encodingPreset);
            String taskXml = "<taskBody><inputAsset>JobInputAsset(0)</inputAsset>"
                    + "<outputAsset assetCreationOptions=\"0\"" // AssetCreationOptions.None
                    + " assetName=\"" + outputAssetName + "\">JobOutputAsset(0)</outputAsset></taskBody>";

            Task.CreateBatchOperation task = Task.create(mediaProcessor.getId(), taskXml)
                    .setConfiguration(encodingPreset).setName("Encoding");

            // Create the Job; this automatically schedules and runs it.
            Job.Creator jobCreator = Job.create()
                    .setName(String.format("Encoding %s to %s", assetToEncode.getName(), encodingPreset))
                    .addInputMediaAsset(assetToEncode.getId()).setPriority(2).addTaskCreator(task);
            JobInfo job = mediaService.create(jobCreator);

            String jobId = job.getId();
            System.out.println("Created Job with Id: " + jobId);

            // Check to see if the Job has completed
            checkJobStatus(jobId);
            // Done with the Job

            // Retrieve the output Asset
            ListResult<AssetInfo> outputAssets = mediaService.list(Asset.list(job.getOutputAssetsLink()));
            return outputAssets.get(0);
        }


        public static String getStreamingOriginLocator(AssetInfo asset) throws ServiceException {
            // Get the .ISM AssetFile
            ListResult<AssetFileInfo> assetFiles = mediaService.list(AssetFile.list(asset.getAssetFilesLink()));
            AssetFileInfo streamingAssetFile = null;
            for (AssetFileInfo file : assetFiles) {
                if (file.getName().toLowerCase().endsWith(".ism")) {
                    streamingAssetFile = file;
                    break;
                }
            }

            AccessPolicyInfo originAccessPolicy;
            LocatorInfo originLocator = null;

            // Create a 30-day read only AccessPolicy
            double durationInMinutes = 60 * 24 * 30;
            originAccessPolicy = mediaService.create(
                    AccessPolicy.create("Streaming policy", durationInMinutes, EnumSet.of(AccessPolicyPermission.READ)));

            // Create a Locator using the AccessPolicy and Asset
            originLocator = mediaService
                    .create(Locator.create(originAccessPolicy.getId(), asset.getId(), LocatorType.OnDemandOrigin));

            // Create a Smooth Streaming base URL
            return originLocator.getPath() + streamingAssetFile.getName() + "/manifest";
        }

        private static void checkJobStatus(String jobId) throws InterruptedException, ServiceException {
            boolean done = false;
            JobState jobState = null;
            while (!done) {
                // Sleep for 5 seconds
                Thread.sleep(5000);

                // Query the updated Job state
                jobState = mediaService.get(Job.get(jobId)).getState();
                System.out.println("Job state: " + jobState);

                if (jobState == JobState.Finished || jobState == JobState.Canceled || jobState == JobState.Error) {
                    done = true;
                }
            }
        }
    }
```

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="additional-resources"></a>Recursos adicionais
Para obter mais informações sobre o desenvolvimento de aplicativos Java no Azure, confira [Centro de Desenvolvimento Java do Azure][Azure Java Developer Center] e [Azure para desenvolvedores Java][Azure for Java developers].


Para obter a documentação do Javadoc dos Serviços de Mídia, confira a [Documentação das bibliotecas do Azure para Java][Documentação das bibliotecas do Azure para Java].

<!-- URLs. -->

[Azure Media Services SDK Maven Package]: https://mvnrepository.com/artifact/com.microsoft.azure/azure-media/latest
[Azure Java Developer Center]: https://azure.microsoft.com/develop/java/
[Azure for Java developers]: https://docs.microsoft.com/java/azure/
[Media Services Client Development]: https://msdn.microsoft.com/library/windowsazure/dn223283.aspx

