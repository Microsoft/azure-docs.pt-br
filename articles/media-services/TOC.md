# [Visão geral](media-services-overview.md)
## [Cenários e disponibilidade](scenarios-and-availability.md)
## [Conceitos](media-services-concepts.md)

# Introdução
## [Criar e gerenciar conta](media-services-portal-create-account.md)
## [Configurar seu ambiente de desenvolvimento](media-services-set-up-computer.md)
### [.NET](media-services-dotnet-how-to-use.md)
### [REST](media-services-rest-how-to-use.md)  
## [Usar autenticação do AAD para a API de acesso](media-services-use-aad-auth-to-access-ams-api.md)
### [Usar o portal para gerenciar a autenticação do AAD](media-services-portal-get-started-with-aad.md)
### [API de acesso com .NET](media-services-dotnet-get-started-with-aad.md)
### [API de acesso com REST](media-services-rest-connect-with-aad.md)
### [Usar a CLI do Azure para criar e configurar o aplicativo AAD](media-services-cli-create-and-configure-aad-app.md)
### [Usar o Azure PowerShell para criar e configurar o aplicativo do AAD](media-services-powershell-create-and-configure-aad-app.md)

## Fornecer vídeo sob demanda
### [Portal do Azure](media-services-portal-vod-get-started.md)
### [SDK .NET](media-services-dotnet-get-started.md)
### [Java](media-services-java-how-to-use.md)
### [REST](media-services-rest-get-started.md)
## Executar transmissão ao vivo
### [Portal do Azure](media-services-portal-live-passthrough-get-started.md)
### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)

# Como
## Gerenciar
### Entidades
#### [.NET](media-services-dotnet-manage-entities.md)
#### [REST](media-services-rest-manage-entities.md)
### [Pontos de extremidade de streaming](media-services-streaming-endpoints-overview.md)
#### [Portal do Azure](media-services-portal-manage-streaming-endpoints.md)
#### [.NET](media-services-dotnet-manage-streaming-endpoints.md)
### Armazenamento
#### [Atualizar os Serviços de Mídia após implantar chaves de acesso de armazenamento](media-services-roll-storage-access-keys.md)
#### [Gerenciar ativos entre diversas contas de armazenamento](meda-services-managing-multiple-storage-accounts.md)
### [Cotas e limitações](media-services-quotas-and-limitations.md)

## Carregar conteúdo
### Carregar arquivos em uma conta
#### [Portal do Azure](media-services-portal-upload-files.md)
#### [.NET](media-services-dotnet-upload-files.md)
#### [REST](media-services-rest-upload-files.md)
### [Carregar arquivos grandes com Aspera](media-services-upload-files-with-aspera.md)
### [Carregar arquivos com o StorSimple](media-services-upload-files-from-storsimple.md)
### [Copiar blobs existentes](media-services-copying-existing-blob.md)

## [Codificar conteúdo](media-services-encode-asset.md)
### [Comparar os codificadores](media-services-compare-encoders.md)
### [Gerenciar a velocidade e a simultaneidade de sua codificação](media-services-manage-encoding-speed.md)
### MES (Media Encoder Standard)
#### [Codecs e formatos do Media Encoder Standard](media-services-media-encoder-standard-formats.md)
#### [Use o MES para gerar automaticamente uma escada de taxa de bits](media-services-autogen-bitrate-ladder-with-mes.md)
#### Codificar com o Media Encoder Standard
##### [Portal do Azure](media-services-portal-encode.md)
##### [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
##### [REST](media-services-rest-encode-asset.md)
#### [Codificação avançada com MES](media-services-advanced-encoding-with-mes.md)
##### [Personalizar as predefinições do Media Encoder Standard](media-services-custom-mes-presets-with-dotnet.md)
##### [Como gerar miniaturas usando o Media Encoder Standard com o .NET](media-services-dotnet-generate-thumbnail-with-mes.md)
##### [Cortar vídeos com o Codificador de Mídia Padrão](media-services-crop-video.md)
#### Esquemas MES
##### [Esquema Media Encoder Standard](media-services-mes-schema.md)
##### [Metadados de entrada](media-services-input-metadata-schema.md)
##### [Metadados de saída](media-services-output-metadata-schema.md)
#### [Predefinições de MES](media-services-mes-presets-overview.md) 
##### [H264 Taxas de Bits Múltiplas 1080p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md)
##### [H264 Taxas de Bits Múltiplas 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md)
##### [H264 Taxas de Bits Múltiplas 16x9 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md)
##### [H264 Taxas de Bits Múltiplas 16x9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md)
##### [H264 Taxas de Bits Múltiplas 16x9 para iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md)
##### [H264 Taxas de Bits Múltiplas 4K Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md)
##### [H264 Taxas de Bits Múltiplas 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md)
##### [H264 Taxas de Bits Múltiplas 4x3 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md)
##### [H264 Taxas de Bits Múltiplas 4x3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md)
##### [H264 Taxas de Bits Múltiplas 4x3 para iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md)
##### [H264 Taxas de Bits Múltiplas 720p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md)
##### [H264 Taxas de Bits Múltiplas 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md)
##### [H264 Taxa de Bits Única 1080p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md)
##### [H264 Taxa de Bits Única 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md)
##### [H264 Taxa de Bits Única 16x9 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md)
##### [H264 Taxa de Bits Única 16x9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md)
##### [H264 Taxa de Bits Única 4K Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md)
##### [H264 Taxa de Bits Única 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md)
##### [H264 Taxa de Bits Única 4x3 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md)
##### [H264 Taxa de Bits Única 4x3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md)
##### [H264 Taxa de Bits Única 720p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md)
##### [H264 Taxa de Bits Única 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md)
##### [H264 Taxa de Bits Única 720p para Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md)
##### [H264 Taxa de Bits Única de Alta Qualidade SD para Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md)
##### [H264 Taxa de Bits Única de Baixa Qualidade SD para Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md)
### Fluxo de trabalho do Media Encoder Premium
#### [Codecs e formatos de fluxo de trabalho do Media Encoder Premium](media-services-premium-workflow-encoder-formats.md)
#### Codificar com o fluxo de trabalho do Media Encoder Premium
##### [Fluxo de trabalho do Media Encoder Premium](media-services-encode-with-premium-workflow.md)
##### [Tutoriais do fluxo de trabalho do Media Encoder Premium](media-services-media-encoder-premium-workflow-tutorials.md)
##### [Criar fluxos de trabalho de codificação avançada com o Designer de Fluxo de Trabalho](media-services-workflow-designer.md)
##### [Fluxo de trabalho Premium com várias entradas](media-services-media-encoder-premium-workflow-multiplefilesinput.md)
### [Criar uma tarefa capaz de gerar partes de fMP4](media-services-generate-fmp4-chunks.md)
### Processadores de mídia
#### [.NET](media-services-get-media-processor.md)
#### [REST](media-services-rest-get-media-processor.md)
### [Códigos de erro](media-services-encoding-error-codes.md)
### Preteridos
#### [Empacotamento estático e criptografia](media-services-static-packaging.md)

## [Transmissão ao vivo](media-services-manage-channels-overview.md)
### [codificadores locais](media-services-live-streaming-with-onprem-encoders.md)
#### [Portal](media-services-portal-live-passthrough-get-started.md)
#### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)
### [Transmissão ao vivo com codificador na nuvem](media-services-manage-live-encoder-enabled-channels.md)
#### [Portal do Azure](media-services-portal-creating-live-encoder-enabled-channel.md)
#### [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
### [Configurar codificadores locais para usar com o codificador na nuvem](media-services-live-encoders-overview.md)
#### [Codificador dinâmico Elementar](media-services-configure-elemental-live-encoder.md)
#### [Codificador FMLE](media-services-configure-fmle-live-encoder.md)
#### [Codificador NewTek TriCaster](media-services-configure-tricaster-live-encoder.md)
#### [Codificador Wirecast](media-services-configure-wirecast-live-encoder.md)
### [Manipular operações de execução longa](media-services-dotnet-long-operations.md)
### [Especificação de ingestão dinâmica de MP4 fragmentado](media-services-fmp4-live-ingest-overview.md)

## [Proteger](media-services-content-protection-overview.md)
### [Configurar a proteção de conteúdo no portal do Azure](media-services-portal-protect-content.md)
### [Configurar chave não criptografada AES-128 para sua transmissão](media-services-protect-with-aes128.md)
### [Use o REST para criptografar seu conteúdo com criptografia de armazenamento](media-services-rest-storage-encryption.md)
### [Visão geral do modelo de licença do PlayReady dos Serviços de Mídia](media-services-playready-license-template-overview.md)
### [Visão geral do modelo de licença do Widevine](media-services-widevine-license-template-overview.md)
### [Entrega de licença do DRM](media-services-deliver-keys-and-licenses.md)
### [Usando parceiros para fornecer licenças Widevine para os Serviços de Mídia](media-services-licenses-partner-integration.md)
#### [Usando o Axinom para fornecer licenças Widevine para os Serviços de Mídia  ](media-services-axinom-integration.md)
#### [Usando castLabs para fornecer licenças Widevine para os Serviços de Mídia](media-services-castlabs-integration.md)
### [Usando a PlayReady e/ou a criptografia comum dinâmica Widevine.](media-services-protect-with-drm.md)
### [Transmita seu conteúdo HLS protegido com o Apple FairPlay](media-services-protect-hls-with-fairplay.md)
### [Design híbrido do subsistema DRM](hybrid-design-drm-sybsystem.md)
### [CENC com DRM múltiplo e controle de acesso](media-services-cenc-with-multidrm-access-control.md)
### Configurar políticas de entrega de ativo
#### [.NET](media-services-dotnet-configure-asset-delivery-policy.md)
#### [REST](media-services-rest-configure-asset-delivery-policy.md)
### Criar ContentKeys
#### [.NET](media-services-dotnet-create-contentkey.md)
#### [REST](media-services-rest-create-contentkey.md)
### Configurar a Política de Autorização de Chave de Conteúdo
#### [Portal do Azure](media-services-portal-configure-content-key-auth-policy.md)
#### [.NET](media-services-dotnet-configure-content-key-auth-policy.md)
#### [REST](media-services-rest-configure-content-key-auth-policy.md)
### [Reproduzir HLS criptografado por AES no Safari](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)
### [Passar tokens de autenticação](http://mingfeiy.com/how-client-pass-tokens-to-azure-media-services-key-delivery-services)

## [Analise](media-services-analytics-overview.md)
### [Analisar mídia usando o portal do Azure](media-services-portal-analyze.md)
### [Processar com o Indexador 2](media-services-process-content-with-indexer2.md)
### [Processar com o Indexador](media-services-index-content.md)
#### [Predefinição de tarefa](indexer-task-preset.md)
### [Processar com o Hyperlapse](media-services-hyperlapse-content.md)
### [Processar com o Face Detector](media-services-face-and-emotion-detection.md)
### [Processar com o Motion Detector](media-services-motion-detection.md)
### [Processar com o Editor Facial](media-services-face-redaction.md)
#### [Passo a passo do Editor Facial](media-services-redactor-walkthrough.md)
### [Processar com miniaturas de vídeo](media-services-video-summarization.md)
### [Processar com OCR](media-services-video-optical-character-recognition.md)

## [Configurar telemetria](media-services-telemetry-overview.md)
###[.NET](media-services-dotnet-telemetry.md)
###[REST](media-services-rest-telemetry.md)

## Escala
### [Processamento de mídia](media-services-scale-media-processing-overview.md)
#### [Portal do Azure](media-services-portal-scale-media-processing.md)
#### [.NET](media-services-dotnet-encoding-units.md)
### Ponto de extremidade de streaming
#### [Portal do Azure](media-services-portal-scale-streaming-endpoints.md)

## [Entregar conteúdo](media-services-deliver-content-overview.md)
### [Empacotamento dinâmico](media-services-dynamic-packaging-overview.md)
### [Visão geral sobre filtros e manifestos dinâmicos](media-services-dynamic-manifest-overview.md)
#### [Criar filtros com .NET](media-services-dotnet-dynamic-manifest.md)
#### [Criar filtros com REST](media-services-rest-dynamic-manifest.md)
### [Política de Caching de CDN na extensão dos Serviços de Mídia](../cdn/cdn-caching-policy.md?toc=%2fazure%2fmedia-services%2ftoc.json)
### Publicar conteúdo
#### [Portal do Azure](media-services-portal-publish.md)
#### [.NET](media-services-deliver-streaming-content.md)
#### [REST](media-services-rest-deliver-streaming-content.md)
### [Entregar por Download](media-services-deliver-asset-download.md)
### [Cenário de streaming de failover](media-services-implement-failover.md)

## Consumir
### [Reproduzir mídia com os players existentes](media-services-playback-content-with-existing-players.md)
### [Reproduzir mídia com o Player de Mídia](media-services-develop-video-players.md)
### Outras opções de reprodução
#### [Aplicativo de streaming suave da Windows Store](media-services-build-smooth-streaming-apps.md)
#### [Aplicativo HTML5 com DASH.js](media-services-embed-mpeg-dash-in-html5.md)
#### [Players do Adobe Open Source Media Framework](media-services-use-osmf-smooth-streaming-client-plugin.md)
### [Inserir anúncios no lado do cliente](media-services-inserting-ads-on-client-side.md)
### [Licenciamento do Kit de Portabilidade de Cliente do Microsoft† Smooth Streaming](media-services-sspk.md)

## Integração
### [Usar Azure Functions com os Serviços de Mídia](media-services-dotnet-how-to-use-azure-functions.md)
### [Azure Functions com exemplos dos Serviços de Mídia](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

## Monitoramento
### Verificar o andamento do trabalho
#### [REST](media-services-rest-check-job-progress.md)
#### [Portal do Azure](media-services-portal-check-job-progress.md)
#### [.NET](media-services-check-job-progress.md)
### [Monitorar as notificações de trabalho com o armazenamento de filas](media-services-dotnet-check-job-progress-with-queues.md)
### [Monitorar as notificações de trabalho com webhooks](media-services-dotnet-check-job-progress-with-webhooks.md)

## Solucionar problemas
### [Perguntas frequentes](media-services-frequently-asked-questions.md)
### [Guia de solução de problemas da transmissão ao vivo](media-services-troubleshooting-live-streaming.md)
### [Códigos de erro](media-services-error-codes.md)
### [Lógica de repetição](media-services-retry-logic-in-dotnet-sdk.md)

# Referência
## [Exemplos de código](https://azure.microsoft.com/en-us/resources/samples/?service=media-services)
## [Azure PowerShell (Resource Manager)](/powershell/module/azurerm.media)
## [Azure PowerShell (Gerenciamento de Serviços)](/powershell/module/azure/?view=azuresmps-3.7.0)
## [.NET](/dotnet/api/microsoft.windowsazure.mediaservices.client)
## [REST](/rest/api/media/mediaservice)  

# Recursos
## [Comunidade dos Serviços de Mídia do Azure](media-services-community.md)
## [Roteiro do Azure](https://azure.microsoft.com/roadmap/?category=web-mobile)
## [Preços](https://azure.microsoft.com/pricing/details/media-services/)
## [Calculadora de preço](https://azure.microsoft.com/pricing/calculator/)
## [Notas de versão](media-services-release-notes.md)
## [Vídeos](https://azure.microsoft.com/resources/videos/index/?services=media-services)
