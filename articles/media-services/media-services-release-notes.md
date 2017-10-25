---
title: "Notas de versão dos Serviços de Mídia | Microsoft Docs"
description: "Notas de versão dos Serviços de Mídia"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 3ca2d7af-1cf0-45fa-9585-3b73f3ee057d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: media
ms.devlang: dotnet
ms.topic: article
ms.date: 07/20/2017
ms.author: juliako
ms.openlocfilehash: 202cd5441401a91736a55ccba095fa08dc95aa26
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-media-services-release-notes"></a>Notas de versão dos Serviços de Mídia do Azure
Estas notas de versão resumem as alterações de versões anteriores e os problemas conhecidos.

> [!NOTE]
> Queremos ouvir nossos clientes e focar na correção de problemas que afetam você. Para relatar um problema ou fazer uma pergunta, publique no [Fórum MSDN de Serviços de Mídia do Azure].
> 
> 

## <a id="issues"></a>Problemas conhecidos no momento
### <a id="general_issues"></a>Problemas gerais dos Serviços de Mídia
| Problema | Descrição |
| --- | --- |
| Vários cabeçalhos HTTP comuns não são fornecidos na API REST. |Se você desenvolver aplicativos de Serviços de Mídia usando a API REST, verá que não há suporte a alguns campos de cabeçalho HTTP comuns (incluindo CLIENT-REQUEST-ID, REQUEST-ID e RETURN-CLIENT-REQUEST-ID). Os cabeçalhos serão adicionados em uma atualização futura. |
| Não é permitida a codificação por porcentagem. |Os serviços de mídia usam o valor da propriedade IAssetFile.Name ao construir URLs para o conteúdo de streaming (por exemplo, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Por esse motivo, não é permitida a codificação por porcentagem. O valor da propriedade **Name** não pode ter quaisquer dos seguintes [caracteres reservados para codificação de percentual](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#[]". Além disso, pode haver somente um ‘.’ Além disso, pode haver somente um '.' para a extensão de nome de arquivo. |
| O método ListBlobs que faz parte do SDK do Armazenamento do Azure versão 3.x falha. |Os Serviços de Mídia geram URLs SAS com base na versão de [12/02/2012](https://docs.microsoft.com/rest/api/storageservices/Version-2012-02-12) . Se desejar que o SDK de Armazenamento do Azure liste os blobs em um contêiner de blob, use o método [CloudBlobContainer.ListBlobs](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx) que faz parte do SDK de Armazenamento do Azure versão 2.x. O método ListBlobs que faz parte do SDK do Armazenamento do Azure versão 3.x falhará. |
| O mecanismo de aceleração dos Serviços de Mídia restringe o uso dos recursos para aplicativos que fazem solicitações excessivas ao serviço. O serviço pode retornar o código de status HTTP Serviço Não Disponível (503). |Para obter mais informações, confira a descrição do código de status HTTP 503 no tópico [Códigos de erro dos Serviços de Mídia do Azure](media-services-encoding-error-codes.md) . |
| Ao consultar entidades, um limite de 1.000 entidades podem ser retornadas ao mesmo tempo porque a REST v2 pública limita os resultados da consulta a 1.000 resultados. |Você precisa usar **Skip** e **Take** (.NET)/ **top** (REST), conforme descrito [neste exemplo de .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) e [neste exemplo de API REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). |
| Alguns clientes podem se deparar com um problema de marcas repetidas no manifesto do Smooth Streaming. |Para saber mais, consulte [esta](media-services-deliver-content-overview.md#known-issues) seção. |
| Os objetos do SDK do .NET dos Serviços de Mídia do Azure não podem ser serializados, e, por isso, não funcionam com o Caching do Azure. |Se você tentar serializar o objeto AssetCollection do SDK para adicioná-lo ao Caching do Azure, uma exceção será lançada. |
| Os trabalhos de codificação falham com uma cadeia de caracteres de mensagem "Stage: DownloadFile. Código: System.NullReferenceException". |O fluxo de trabalho de codificação típico é carregar arquivos de vídeo de entrada para um Ativo de entrada e enviar um ou mais trabalhos de codificação para esse Ativo de entrada, sem modificá-lo ainda mais. No entanto, se você modificar o Ativo de entrada (por exemplo, ao adicionar/excluir/renomear arquivos no Ativo), os trabalhos subsequentes poderão falhar com um erro DownloadFile. A solução alternativa é excluir o Ativo de entrada e carregar novamente os arquivos de entrada em um novo Ativo. |

## <a id="rest_version_history"></a>Histórico de versão da API REST
Para obter informações sobre o histórico de versões da API REST dos Serviços de Mídia, consulte [Referência da API REST dos Serviços de Mídia do Azure].

## <a name="june-2017-release"></a>Versão de junho de 2017

Os Serviços de Mídia agora dão suporte à [autenticação baseada no Azure AD (Azure Active Directory)](media-services-use-aad-auth-to-access-ams-api.md).

> [!IMPORTANT]
> Atualmente, os Serviços de Mídia dão suporte ao modelo de autenticação do serviço de Controle de Acesso do Azure. No entanto, a autorização de Controle de Acesso será preterida em 1º de junho de 2018. É recomendável que você migre para o modelo de autenticação do Azure AD assim que possível.

## <a name="march-2017-release"></a>Versão de março de 2017

Agora você pode usar o Azure Media Standard para [gerar automaticamente uma escada de taxa de bits](media-services-autogen-bitrate-ladder-with-mes.md), especificando a cadeia de caracteres de predefinição "Streaming Adaptável" ao criar uma tarefa de codificação. É recomendável usar a predefinição "Streaming Adaptável" caso você queira codificar um vídeo para streaming com os Serviços de Mídia. Se precisar personalizar uma codificação de predefinição para seu cenário específico, você poderá começar com [essas](media-services-mes-presets-overview.md) predefinições.

Agora você pode usar o Fluxo de Trabalho do Azure Media Standard ou do Media Encoder Premium para [criar uma tarefa de codificação que gera partes fMP4](media-services-generate-fmp4-chunks.md). 


## <a name="febuary-2017-release"></a>Versão de fevereiro de 2017

A partir de 1º de abril de 2017, qualquer registro de trabalho em sua conta com mais de 90 dias será excluído automaticamente, junto com seus registros de tarefas associados, mesmo que o número total de registros esteja abaixo da cota máxima. Se você precisar arquivar as informações de trabalho/tarefa, poderá usar o código descrito [aqui](media-services-dotnet-manage-entities.md).

## <a name="january-2017-release"></a>Versão de janeiro de 2017

No AMS (Serviços de Mídia do Microsoft Azure), um **Ponto de Extremidade de Streaming** representa um serviço de streaming que pode fornecer conteúdo diretamente a um aplicativo cliente player ou à CDN (Rede de Distribuição de Conteúdo) para distribuição posterior. Os Serviços de Mídia também fornecem integração perfeita da CDN do Azure. O fluxo de saída do serviço StreamingEndpoint pode ser um fluxo ao vivo, um vídeo por demanda ou um download progressivo do seu ativo na conta dos Serviços de Mídia. Cada conta dos Serviços de Mídia do Azure inclui um StreamingEndpoint padrão. StreamingEndpoints adicionais podem ser criados na conta. Há duas versões do StreamingEndpoints, 1.0 e 2.0. A partir de 10 de janeiro de 2017, todas as contas AMS recém-criadas incluirão a versão 2.0 **padrão** do StreamingEndpoint. Pontos de extremidade de streaming adicionais que você adicionar nessa conta também terão a versão 2.0. Essa alteração não afetará as contas existentes; StreamingEndpoints existente estarão na versão 1.0 e poderão ser atualizados para a versão 2.0. Com essa alteração, haverá alterações de comportamento, cobrança e recurso (para obter mais informações, confira [este](media-services-streaming-endpoints-overview.md) tópico).

Além disso, a partir da versão 2.15, os Serviços de Mídia do Azure adicionaram as seguintes propriedades à entidade do Ponto de Extremidade de Streaming: **CdnProvider**, **CdnProfile**, **FreeTrialEndTime**, **StreamingEndpointVersion**. Para obter uma visão detalhada dessas propriedades, clique [aqui](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

## <a name="december-2016-release"></a>Versão de dezembro de 2016

Os Serviços de Mídia do Azure agora permitem que você acesse dados de telemetria/métricas de seus serviços. A versão atual do AMS permite a coleta de dados de telemetria para entidades Channel, StreamingEndpoint e arquivamento dinâmicas. Para obter mais informações, consulte [este](media-services-telemetry-overview.md) tópico.

## <a id="july_changes16"></a>Versão de julho de 2016
### <a name="updates-to-manifest-file-ism-generated-by-encoding-tasks"></a>Atualizações do arquivo de manifesto (*.ISM) gerado por tarefas de codificação
Quando uma tarefa de codificação for enviada para o Codificador de Mídia Padrão ou para o Codificador de Mídia do Azure, a tarefa de codificação gerará um [arquivo de manifesto de streaming](media-services-deliver-content-overview.md) (*.ism) no Ativo de saída. Com a versão mais recente do serviço, a sintaxe desse arquivo de manifesto de streaming foi atualizada.

> [!NOTE]
> A sintaxe do arquivo de manifesto de streaming (.ism) é reservada para uso interno e está sujeita a alterações em versões futuras. Não modifique ou manipule o conteúdo do arquivo.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Um novo arquivo de manifesto de cliente (*.ISMC) é gerado no ativo de saída quando uma tarefa de codificação produz um ou mais arquivos em MP4
A partir da versão mais recente do serviço, após a conclusão de uma tarefa de codificação que gera um ou mais arquivos em MP4, a saída do ativo também conterá um arquivo de manifesto de streaming (*.ismc) do cliente. O arquivo .ismc ajuda a melhorar o desempenho do streaming dinâmico. 

> [!NOTE]
> A sintaxe do arquivo de manifesto do cliente (.ismc) é reservada para uso interno e está sujeita a alterações em versões futuras. Não modifique ou manipule o conteúdo do arquivo.
> 
> 

Para saber mais, confira [este](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/) blog.

### <a name="known-issues"></a>Problemas conhecidos
Alguns clientes podem se deparar com um problema de marcas repetidas no manifesto do Smooth Streaming. Para saber mais, consulte [esta](media-services-deliver-content-overview.md#known-issues) seção.

## <a id="apr_changes16"></a>Versão de abril de 2016
### <a name="azure-media-analytics"></a>Análise de Mídia do Azure
Os Serviços de Mídia do Azure introduziram a Análise de Mídia do Azure para proporcionar uma inteligência de vídeo avançada. Para obter informações detalhadas, confira [Visão Geral da Análise dos Serviços de Mídia do Azure](media-services-analytics-overview.md).

### <a name="apple-fairplay-preview"></a>Apple FairPlay (Preview)
Os Serviços de Mídia do Azure agora permitem criptografar dinamicamente seu conteúdo HLS (HTTP Live Streaming) com o Apple FairPlay. Também é possível usar o serviço de entrega de licença do AMS para fornecer licenças FairPlay para os clientes. Para obter informações mais detalhadas, confira o artigo [Use os Serviços de Mídia do Azure para transmitir seu conteúdo de HLS protegido com o Apple FairPlay ](media-services-protect-hls-with-fairplay.md).

## <a id="feb_changes16"></a>Versão de fevereiro de 2016
A versão mais recente do SDK dos Serviços de Mídia do Azure para .NET (3.5.3) contém uma correção de bug relacionada ao Widevine. O problema era: AssetDeliveryPolicy não pôde ser reutilizado para vários ativos criptografados com o Widevine. Como parte dessa correção de bug, a propriedade a seguir foi adicionada ao SDK: **WidevineBaseLicenseAcquisitionUrl**.

    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

    };

## <a id="jan_changes_16"></a>Versão de janeiro de 2016
Unidades Reservadas para Codificação renomeadas para reduzir a confusão com nomes de Codificador.

As unidades reservadas para codificação Básica, Standard e Premium foram renomeadas para unidades reservadas S1, S2 e S3, respectivamente.  Os clientes que usam o RUs de codificação Basic verão S1 como o rótulo no Portal do Azure (e na fatura), enquanto Standard e Premium verão os rótulos S2 e S3, respectivamente. 

## <a id="dec_changes_15"></a>Versão de dezembro de 2015

### <a name="azure-media-encoder-deprecation-announcement"></a>Comunicado de substituição do Codificador de Mídia do Azure

O Codificador de Mídia do Azure será preterido daqui a aproximadamente 12 meses a contar do lançamento do Media Encoder Standard.

### <a name="azure-sdk-for-php"></a>SDK do Azure para PHP
A equipe do SDK do Azure publicou uma nova versão do pacote [SDK do Azure para PHP](http://github.com/Azure/azure-sdk-for-php) que contém atualizações e novos recursos para os Serviços de Mídia do Microsoft Azure. Em particular, o SDK dos Serviços de Mídia do Azure para PHP agora oferece suporte aos recursos de [proteção de conteúdo](media-services-content-protection-overview.md) mais recentes: criptografia dinâmica com o AES e o DRM (PlayReady e Widevine) com e sem nenhuma restrição de Token. Ele também oferece suporte a [Unidades de Codificação](media-services-dotnet-encoding-units.md)de dimensionamento.

Para obter mais informações, confira:

* O blog do [SDK dos Serviços de Mídia do Microsoft Azure para PHP](http://southworks.com/blog/2015/12/09/new-microsoft-azure-media-services-sdk-for-php-release-available-with-new-features-and-samples/) .
* As [amostras de código](http://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) a seguir ajudam você a começar rapidamente:
  * **vodworkflow_aes.php**: é um arquivo PHP que mostra como usar a Criptografia Dinâmica AES-128 e o Serviço de Entrega de Chave. Ele se baseia no exemplo .NET explicado [neste](media-services-protect-with-aes128.md) artigo.
  * **vodworkflow_aes.php**: é um arquivo PHP que mostra como usar a Criptografia Dinâmica do PlayReady e o Serviço de Entrega de Licença. Ele se baseia no exemplo .NET explicado [neste](media-services-protect-with-drm.md) artigo.
  * **scale_encoding_units.php**: é um arquivo PHP que mostra como dimensionar a unidade reservada para codificação.

## <a id="nov_changes_15"></a>Versão de novembro de 2015
Os Serviços de Mídia do Azure agora oferece o serviço de entrega de licença do Google Widevine na nuvem. Para obter mais detalhes, veja [o blog deste comunicado](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Veja também [este tutorial](media-services-protect-with-drm.md) e o [repositório do GitHub](http://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

Observe que os serviços de entrega de licenças do Widevine fornecidos pelos Serviços de Mídia do Azure estão em versão de visualização. Para saber mais, confira [este blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

## <a id="oct_changes_15"></a>Versão de outubro de 2015
O AMS (Serviços de Mídia do Azure) agora também está disponível nos seguintes datacenters: Sul do Brasil, Índia Ocidental, Sul da Índia e Índia Central. Agora você pode usar o Portal do Azure para [criar contas do Serviço de Mídia](media-services-portal-create-account.md) e realizar diversas tarefas descritas [aqui](https://azure.microsoft.com/documentation/services/media-services/). No entanto, a Codificação Ativa não está habilitada nesses datacenters. Além disso, nem todos os tipos de Unidades Reservadas para Codificação estão disponíveis nesses datacenters.

* Sul do Brasil:                                          somente as Unidades Reservadas para Codificação Standard e Básica estão disponíveis
* Índia Ocidental, Sul da Índia e Índia Central:             somente as Unidades Reservadas para Codificação Básica estão disponíveis

## <a id="september_changes_15"></a>Versão de setembro de 2015
* AMS agora oferece a capacidade de proteger tanto VOD (vídeo sob demanda) quanto Fluxos Ao Vivo com tecnologia de DRM Modular Widevine. Você pode usar os seguintes parceiros de serviços de entrega para ajudá-lo a fornecer licenças do Widevine: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/) e [castLabs](http://castlabs.com/company/partners/azure/). Para saber mais, confira [este blog](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).
  
    Você pode usar o [SDK do .NET AMS](https://www.nuget.org/packages/windowsazure.mediaservices/) (a partir da versão 3.5.1) ou a API REST para configurar seu AssetDeliveryConfiguration para usar o Widevine.  
* O AMS adicionou suporte para vídeos ProRes da Apple. Agora você pode carregar os arquivos de vídeos de origem do QuickTime que usam Apple ProRes ou outros codecs. Para saber mais, confira [este blog](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).
* Agora você pode usar um Codificador de Mídia Padrão para fazer subrecortes e extração de arquivos dinâmicos. Para saber mais, confira [este blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).
* Foram feitas as seguintes atualizações de filtragem: 
  
  * Agora você pode usar o formato HLS (Apple HTTP Live Streaming) com filtro somente áudio. Essa atualização permite que você remova a faixa somente áudio especificando (somente áudio = false) na URL.
  * Ao definir filtros para os ativos, agora você pode combinar vários filtros (até três) em uma única URL.
    
    Para saber mais, confira [este](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.
* O AMS agora dá suporte ao I-Frames no HLS v4. O suporte do I-Frame otimiza as operações de avanço e retrocesso. Por padrão, todas as saídas do HLS v4 incluem o playlist do I-Frame (EXT-X-I-FRAME-STREAM-INF).
  
    Para saber mais, confira [este](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.

## <a id="august_changes_15"></a>Versão de agosto de 2015
* Agora, o SDK do Serviços de Mídia do Azure para Java versão V0.8.0  e novos exemplos estão disponíveis. Para obter mais informações, confira:
  
  * [Postagem no blog](http://southworks.com/blog/2015/08/25/microsoft-azure-media-services-sdk-for-java-v0-8-0-released-and-new-samples-available/)
  * [Repositório de amostras de Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
* Atualização do Player de Mídia do Azure com suporte de fluxo de vários áudios. Para obter mais informações, confira:
  * [Postagem no blog](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/)

## <a id="july_changes_15"></a>Versão de julho de 2015
* Anunciando a disponibilidade geral do Media Encoder Standard. Para saber mais, confira [esta postagem no blog](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).
  
    O Codificador de Mídia Padrão usa as predefinições descritas [nesta](http://go.microsoft.com/fwlink/?LinkId=618336) seção. Lembre-se de que, ao usar uma predefinição para codificações 4k, você deverá adquirir o tipo de unidade reservada **Premium** . Para saber mais, confira [Como dimensionar a codificação](media-services-scale-media-processing-overview.md).
* Legendas em tempo real ativas com os Serviços de Mídia e o Player do Azure. Para saber mais, veja [esta postagem no blog](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/)

### <a name="media-services-net-sdk-updates"></a>Atualizações do SDK do .NET dos Serviços de Mídia
O SDK do .NET dos Serviços de Mídia do Azure está agora na versão 3.4.0.0. A seguinte funcionalidade foi adicionada nessa versão:  

* Suporte implementado para arquivo morto dinâmico. Observe que você não pode baixar um ativo que contenha um arquivo morto dinâmico.
* Suporte implementado para filtros dinâmicos.
* Funcionalidade implementada que permite aos usuários manter o contêiner de armazenamento ao excluir ativos.
* Correções de bugs relacionados a políticas de repetição nos canais.
* **Fluxo de Trabalho Premium do Codificador de Mídia** habilitado.

## <a id="june_changes_15"></a>Versão de junho de 2015
### <a name="media-services-net-sdk-updates"></a>Atualizações do SDK do .NET dos Serviços de Mídia
O SDK do .NET dos Serviços de Mídia do Azure está agora na versão 3.3.0.0. A seguinte funcionalidade foi adicionada nessa versão:  

* suporte para especificação de Descoberta do OpenId Connect,
* suporte para tratamento de substituição de chaves no lado do provedor de identidade. 

Se você estiver usando um provedor de identidade que expõe o documento de descoberta OpenID Connect (assim como os seguintes provedores: Active Directory do Azure, Google, Salesforce), você pode instruir os Serviços de Mídia do Azure para obter chaves de assinatura para a validação de token JWT de especificação de descoberta OpenId Connect. 

Para saber mais, confira [Usar Chaves Web Json da especificação de descoberta OpenID Connect para trabalhar com autenticação de token JWT nos Serviços de Mídia do Azure](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).

## <a id="may_changes_15"></a>Versão de maio de 2015
Anunciando os seguintes novos recursos:

* [Uma visualização da Codificação Ativa com os Serviços de Mídia](media-services-manage-live-encoder-enabled-channels.md)
* [Manifesto dinâmico](media-services-dynamic-manifest-overview.md)
* [Uma visualização do processador de mídia Azure Media Hyperlapse](https://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

## <a id="april_changes_15"></a>Versão de abril de 2015
### <a name="general-media-services-updates"></a>Atualizações gerais dos Serviços de Mídia
* [Anunciando o Player de Mídia do Azure](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/).
* Começando com a REST 2.10 dos Serviços de Mídia, canais configurados para incluir um protocolo RTMP são criados com URLs de inclusão primária e secundária. Para saber mais, confira [Configurações de inclusão de canal](media-services-live-streaming-with-onprem-encoders.md#channel_input)
* Atualizações do Indexador de Mídia do Azure
* Suporte ao idioma espanhol
* Novo formato xml de configuração

Para saber mais, confira [este blog](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

### <a name="media-services-net-sdk-updates"></a>Atualizações do SDK do .NET dos Serviços de Mídia
O SDK do .NET dos Serviços de Mídia do Azure está agora na versão 3.2.0.0.

Estas são algumas das atualizações voltadas para o cliente:

* **Alteração interruptiva**: **TokenRestrictionTemplate.Issuer** e **TokenRestrictionTemplate.Audience** alterados para ser de um tipo de cadeia de caracteres.
* Atualizações relativas à criação personalizada de políticas de repetição.
* Correções de bugs relativas a upload/download de arquivos.
* A classe **MediaServicesCredentials** agora aceita os pontos de extremidade de controle de acesso primários e secundários para autenticar.

## <a id="march_changes_15"></a>Versão de março de 2015
### <a name="general-media-services-updates"></a>Atualizações gerais dos Serviços de Mídia
* Agora, os Serviços de Mídia fornecem integração com a CDN do Azure. Para dar suporte à integração, a propriedade **CdnEnabled** foi adicionada a **StreamingEndpoint**.  **CdnEnabled** pode ser usado com APIs REST a partir da versão 2.9 (para saber mais, confira [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint)).  **CdnEnabled** pode ser usado com o SDK do .NET a partir da versão 3.1.0.2 (para saber mais, confira [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint\(v=azure.10\).aspx)).
* Anúncio do **Fluxo de trabalho do Media Encoder Premium**. Para saber mais, confira [Apresentando a codificação Premium nos Serviços de Mídia do Azure](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).

## <a id="february_changes_15"></a>Versão de fevereiro de 2015
### <a name="general-media-services-updates"></a>Atualizações gerais dos Serviços de Mídia
A API REST dos Serviços de Mídia agora está na versão 2.9. A partir desta versão, você pode habilitar a integração da CDN do Azure com pontos de extremidade de streaming. Para saber mais, confira [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

## <a id="january_changes_15"></a>Versão de janeiro de 2015
### <a name="general-media-services-updates"></a>Atualizações gerais dos Serviços de Mídia
Anúncio da disponibilidade geral (GA) de proteção de conteúdo com criptografia dinâmica. Para saber mais, confira [Os Serviços de mídia do Azure aprimoram a segurança de streaming com a disponibilidade geral da tecnologia DRM](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

### <a name="media-services-net-sdk-updates"></a>Atualizações do SDK do .NET dos Serviços de Mídia
O SDK do .NET dos Serviços de Mídia do Azure está agora na versão 3.1.0.1.

Esta versão marcou o construtor Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestriction padrão como obsoleto. O novo construtor usa TokenType como um argumento.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


## <a id="december_changes_14"></a>Versão de dezembro de 2014
### <a name="general-media-services-updates"></a>Atualizações gerais dos Serviços de Mídia
* Algumas atualizações e novos recursos foram adicionados ao processador de mídia do indexador do Azure. Para saber mais, confira [Notas de versão de indexador de mídia do Azure 1.1.6.7](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
* Adicionada uma nova API REST que permite que você atualize a codificação de unidades reservadas: [EncodingReservedUnitType com REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype).
* Acrescentado suporte a CORS para o serviço de distribuição de chaves.
* Foram feitos aprimoramentos de desempenho nas opções das políticas de autorização de consulta.
* No data center da China, a [URL de entrega de chaves](https://docs.microsoft.com/rest/api/media/operations/contentkey#get_delivery_service_url) agora é por cliente (assim como em outros data centers).
* Duração de destino automático HLS adicionada. Ao fazer streaming ao vivo, o HLS é sempre empacotado dinamicamente. Por padrão, os Serviços de Mídia calculam automaticamente a taxa de empacotamento de segmento HLS (FragmentsPerSegment) com base no intervalo entre quadros-chave (KeyFrameInterval), também conhecido como grupo de imagens – GOP, que é recebido do codificador ao vivo. Para obter mais informações, consulte [Trabalhando com a Transmissão ao Vivo dos Serviços de Mídia do Azure].

### <a name="media-services-net-sdk-updates"></a>Atualizações do SDK do .NET dos Serviços de Mídia
* [O SDK do .NET dos Serviços de Mídia do Azure](http://www.nuget.org/packages/windowsazure.mediaservices/) está agora na versão 3.1.0.0.
* Atualizada a dependência do SDK do .Net para .NET 4.5 Framework.
* Adicionada uma nova API que permite que você atualize unidades reservadas de codificação. Para saber mais, confira [Atualizando tipo de unidade reservada e aumentando unidades reservadas de codificação usando o .NET](media-services-dotnet-encoding-units.md).
* Acrescentado suporte a JWT (JSON Web Token) para autenticação via token. Para saber mais, confira [Autenticação de token JWT nos Serviços de Mídia do Azure e criptografia dinâmica](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
* Adicionados deslocamentos relativos para BeginDate e ExpirationDate no modelo de licença PlayReady.

## <a id="november_changes_14"></a>Versão de novembro de 2014
* Os Serviços de Mídia agora permitem que você inclua um conteúdo de Smooth Streaming (FMP4) ao vivo em uma conexão SSL. Para inserir por SSL, certifique-se de atualizar a URL de inserção para HTTPS.  Observe que, atualmente, o AMS não dá suporte ao SSL com domínios personalizados.  Para saber mais sobre a transmissão ao vivo, confira [Trabalhando com a Transmissão ao Vivo dos Serviços de Mídia do Azure].
* Observe que, no momento, você não pode ingerir um fluxo ao vivo RTMP por uma conexão SSL.
* Você só poderá transmitir por SSL se o ponto de extremidade de streaming do qual você pode distribuir o conteúdo tiver sido criado depois de 10 de setembro de 2014. Se suas URLs de streaming baseiam-se nos pontos de extremidade de streaming após 10 de setembro, a URL contém "streaming.mediaservices.windows.net" (o novo formato). URLs de streaming que contêm "origin.mediaservices.windows.net" (o formato antigo) não dão suporte a SSL. Se sua URL está no formato antigo e você deseja ser capaz de transmitir por SSL, [crie um novo ponto de extremidade de streaming](media-services-portal-manage-streaming-endpoints.md). Use URLs criadas com base no novo ponto de extremidade de streaming para transmitir seu conteúdo por SSL.

## <a id="october_changes_14"></a>Versão de outubro de 2014
### <a id="new_encoder_release"></a>Versão do codificador de Serviços de Mídia
Anúncio da nova versão do Codificador de Mídia do Azure dos Serviços de Mídia. Com o mais recente Codificador de Mídia do Azure, você somente é cobrado por GBs de saída, mas, por outro lado, o novo codificador tem recursos compatíveis com o codificador anterior. Para mais informações [Detalhes dos preços dos Serviços de Mídia]).

### <a id="oct_sdk"></a>SDK do .NET dos Serviços de Mídia
O SDK dos Serviços de Mídia para extensões .NET agora está na versão 2.0.0.3.

O SDK dos Serviços de Mídia para .NET agora está na versão 3.0.0.8.

As seguintes alterações foram feitas:

* Refatoração em classes de política de repetição.
* Adicionando a sequência do agente do usuário para os cabeçalhos de solicitação http.
* Adicionando a etapa de compilação de restauração do nuget.
* Corrigindo testes de cenário para usar certificado X509 do repositório.
* Validando configurações durante a atualização de canal e do ponto de extremidade de streaming.

### <a name="new-github-repository-to-host-media-services-samples"></a>Novo repositório GitHub para hospedar exemplos de Serviços de Mídia
Os exemplos estão localizados em [Repositório GitHub de exemplos dos Serviços de Mídia do Azure](https://github.com/Azure/Azure-Media-Services-Samples).

## <a id="september_changes_14"></a>Versão de setembro de 2014
Os metadados REST dos Serviços de Mídia agora estão na versão 2.7. Para obter mais informações sobre as últimas atualizações de REST, consulte a [Referência da API REST dos Serviços de Mídia do Azure].

SDK dos Serviços de Mídia para .NET agora está na versão 3.0.0.7

### <a id="sept_14_breaking_changes"></a>Alterações interruptivas
* **Origem** foi renomeada para [StreamingEndpoint].
* Uma mudança no comportamento padrão quando você usa o **Portal do Azure** para codificar e depois publicar arquivos MP4.

Anteriormente, ao usar o Portal Clássico do Azure para publicar um ativo de vídeo MP4 de arquivo único, uma URL SAS seria criada (as URLs SAS permitem baixar o vídeo de um armazenamento de blobs). No momento, ao usar o Portal Clássico do Azure para codificar e depois publicar um ativo de vídeo MP4 de arquivo único, a URL gerada aponta para um ponto de extremidade de transmissão dos Serviços de Mídia do Azure.  Essa mudança não afeta vídeos MP4 que são carregados diretamente para os Serviços de Mídia e publicados sem serem codificados pelos Serviços de Mídia do Azure.

No momento, há as duas opções a seguir para solucionar o problema.

* Habilitar unidades de streaming e usar empacotamento dinâmico para transmitir o ativo .mp4 como uma apresentação de smooth streaming.
* Criar uma URL SAS para baixar (ou reproduzir progressivamente) o .mp4. Para obter mais informações sobre como criar um localizador SAS, consulte [Entregando Conteúdo].

### <a id="sept_14_GA_changes"></a>Novos recursos/cenários que fazem parte da versão do GA
* **Processador de Mídia do Indexador**. Para obter mais informações, consulte [Indexando arquivos de mídia com o Indexador de Mídia do Azure].
* A entidade [StreamingEndpoint] agora permite adicionar nomes (host) de domínio personalizados.
  
    Para um nome de domínio personalizado ser usado como nome do ponto de extremidade de streaming dos Serviços de Mídia, é preciso adicionar nomes de host personalizados ao ponto de extremidade de streaming. Use as APIs REST dos Serviços de Mídia ou o SDK do .NET para adicionar nomes de host personalizados.
  
    As seguintes considerações se aplicam:
  
  * É preciso ter propriedade do nome de domínio personalizado.
  * A propriedade do nome de domínio deve ser validada pelos Serviços de Mídia do Azure. Para validar o domínio, criar um CName que mapeia <MediaServicesAccountId>.<parent domain> para verifydns.<mediaservices-dns-zone>. 
  * Você deve criar outro CName que mapeia o nome de host personalizado (por exemplo, sports.contoso.com) em nome de host do seu StreamingEndpoint dos Serviços de Mídia (por exemplo, amstest.streaming.mediaservices.windows.net).

    Para obter mais informações, confira a propriedade **CustomHostNames** no tópico [StreamingEndpoint] .

### <a id="sept_14_preview_changes"></a>Novos recursos/cenários que fazem parte da versão de preview pública
* Visualização de Live Streaming. Para obter mais informações, consulte [Trabalhando com a Transmissão ao Vivo dos Serviços de Mídia do Azure].
* Serviço de entrega de chave. Para obter mais informações, consulte [Usando o serviço de entrega de chave e criptografia dinâmica do AES-128].
* Criptografia dinâmica do EAS. Para obter mais informações, consulte [Usando o serviço de entrega de chave e criptografia dinâmica do AES-128].
* Serviço de entrega de licença do PlayReady. Para obter mais informações, consulte [Usando o serviço de entrega de licença e criptografia dinâmica do PlayReady].
* Criptografia dinâmica do PlayReady. Para obter mais informações, consulte [Usando o serviço de entrega de licença e criptografia dinâmica do PlayReady].
* Modelo de licença do PlayReady dos Serviços de Mídia. Para obter mais informações, consulte [Visão geral do modelo de licença do PlayReady dos Serviços de Mídia].
* Streaming de ativos criptografados de armazenamento. Para obter mais informações, consulte [Streaming de conteúdo criptografado de armazenamento].

## <a id="august_changes_14"></a>Versão de agosto de 2014
Ao codificar um ativo, um ativo de saída é produzido quando o trabalho de codificação é concluído. Até esta versão, o Codificador de Serviços de Mídia do Azure produzia metadados sobre os ativos de saída. A partir desta versão, o codificador também produz metadados sobre ativos de entrada. Para obter mais informações, confira os tópicos [Metadados de entrada] e [Metadados de saída].

## <a id="july_changes_14"></a>Versão de julho de 2014
As seguintes correções de erro foram feitas ao Empacotador e Criptografador dos Serviços de Mídia do Azure:

* Apenas reproduções de áudio ao realizar transmux de um ativo de arquivo em tempo real para Live Streaming HTTP - isso foi corrigido e agora tanto áudio quanto vídeo são reproduzidos.
* Ao empacotar um ativo para Live Streaming HTTP e criptografia de envelope de 128 bits, os fluxos de pacote não são reproduzidos em dispositivos Android - esse erro foi corrigido e o fluxo empacotado é reproduzido em dispositivos Android com suporte a Live Streaming HTTP.

## <a id="may_changes_14"></a>Versão de maio de 2014
### <a id="may_14_changes"></a>Atualizações gerais dos Serviços de Mídia
Agora é possível usar o [Empacotamento Dinâmico] para transmitir Live Streaming HTTP (HLS) v3. Para transmitir HLS v3, adicione o seguinte formato para o caminho do localizador de origem: * .ism/manifest(format=m3u8-aapl-v3). Para obter mais informações, consulte o [Blog de Nick Drouin].

O Empacotamento Dinâmico agora também oferece suporte à entrega de HLS (v3 e v4) criptografado com PlayReady com base em Smooth Streaming estaticamente criptografado com PlayReady. Para obter informações sobre como criptografar Smooth Streaming com PlayReady, consulte [Protegendo Smooth Stream com PlayReady].

### <a name="may_14_donnet_changes"></a>Atualizações do SDK do .NET dos Serviços de Mídia
As seguintes melhorias são incluídas na versão 3.0.0.5 do SDK do .NET dos Serviços de Mídia:

* Melhor velocidade e resiliência para carregar/baixar ativos de mídia.
* Melhorias na lógica de repetição e manipulação de exceção temporária: 
  
  * Detecção de erro temporário e lógica de repetição foram aprimorados para as exceções causadas por consultar, salvar alterações, carregar ou baixar arquivos. 
  * Ao obter Exceções da Web (por exemplo, durante uma solicitação de token ACS), você observará que erros fatais estão falhando mais rapidamente agora.

Para obter mais informações, consulte [Lógica de repetição no SDK de Serviços de Mídia para .NET].

## <a id="april_changes_14"></a>Versão do Codificador de abril de 2014
### <a name="april_14_enocer_changes"></a>Atualizações do Codificador de Serviços de Mídia
* Adicionado suporte para capturar arquivos AVI criados usando o editor não linear Grass Valley EDIUS, em que o vídeo é levemente compactado usando o codec Grass Valley HQ/HQX. Para obter mais informações, consulte [Grass Valley anuncia streaming EDIUS 7 pela nuvem].
* Adicionado suporte para especificar a convenção de nomenclatura para arquivos produzidos pelo Codificador de Mídia. Para obter mais informações, consulte [Controlando nomes de arquivo de saída do Codificador do Serviço de Mídia].
* Adicionado suporte para sobreposições de vídeo e/ou áudio. Para obter mais informações, consulte [Criando sobreposições].
* Adicionado suporte para unir vários segmentos de vídeo. Para obter mais informações, consulte [Unindo segmentos de vídeo].
* Corrigido um erro relacionado à transcodificação de MP4s em que o áudio havia sido codificado com a camada de Áudio 3 MPEG-1 (ou seja, MP3).

## <a id="jan_feb_changes_14"></a>Versões de janeiro/fevereiro de 2014
### <a name="jan_fab_14_donnet_changes"></a>SDK do .NET dos Serviços de Mídia do Azure 3.0.0.1, 3.0.0.2 e 3.0.0.3
As alterações em 3.0.0.1 e 3.0.0.2 incluem:

* Corrigidos problemas relacionados ao uso de consultas LINQ com declarações OrderBy.
* Divida as soluções de teste em [Github] em testes baseados em unidade e testes baseados em cenário.

Para saber mais sobre as alterações, confira: [Versões do SDK do .NET dos Serviços de Mídia do Azure 3.0.0.1 e 3.0.0.2].

As seguintes alterações foram feitas na versão 3.0.0.3:

* Atualizadas as dependências de armazenamento do Azure para usar a versão 3.0.3.0. 
* Corrigido o problema de compatibilidade reversa para versões 3.0*.* . 

## <a id="december_changes_13"></a>Versão de dezembro de 2013
### <a name="dec_13_donnet_changes"></a>SDK do .NET dos Serviços de Mídia do Azure 3.0.0.0
> [!NOTE]
> As versões 3.0.x.x não são compatíveis com versões 2.4.x.x.
> 
> 

A versão mais recente do SKD dos Serviços de Mídia agora é a 3.0.0.0. É possível baixar o pacote mais recente do Nuget ou obter os bits do [Github].

A partir do SDK de Serviços de Mídia versão 3.0.0.0, é possível reutilizar os tokens do [Serviço de Controle de Acesso (ACS) do Active Directory do Azure] . Para obter mais informações, confira a seção “Reutilizando os tokens do Serviço de Controle de Acesso” no tópico [Conectando-se à conta dos Serviços de Mídia usando o SDK dos Serviços de Mídia para .NET] .

### <a name="dec_13_donnet_ext_changes"></a>Extensões do SDK do .NET dos Serviços de Mídia do Azure 2.0.0.0
As Extensões do SDK do .NET dos Serviços de Mídia do Azure são um conjunto de métodos de extensão e funções auxiliares que simplificarão seu código e tornarão mais fácil desenvolver com os Serviços de Mídia do Azure. Você pode obter os bits mais recentes das [Extensões do SDK do .NET dos Serviços de Mídia do Azure].

## <a id="november_changes_13"></a>Versão de novembro de 2013
### <a name="nov_13_donnet_changes"></a>Alterações do SDK do .NET dos Serviços de Mídia do Azure
A partir desta versão, o SDK para .NET dos Serviços de Mídia manipula erros de falhas temporários que podem ocorrer ao fazer chamadas à camada da API REST dos Serviços de Mídia.

## <a id="august_changes_13"></a>Versão de agosto de 2013
### <a name="aug_13_powershell_changes"></a>Cmdlets do PowerShell dos Serviços de Mídia incluídos nas Ferramentas de SDK do Azure
Os seguintes cmdlets do PowerShell dos Serviços de Mídia agora estão incluídos em [azure-sdk-tools].

* Get-AzureMediaServices 
  
    Por exemplo: `Get-AzureMediaServicesAccount`.
* New-AzureMediaServicesAccount 
  
    Por exemplo: `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`.
* New-AzureMediaServicesKey 
  
    Por exemplo: `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`.
* Remove-AzureMediaServicesAccount 
  
    Por exemplo: `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`.

## <a id="june_changes_13"></a>Versão de junho de 2013
### <a name="june_13_general_changes"></a>Alterações dos Serviços de Mídia do Azure
As alterações mencionadas nesta seção são atualizações incluídas nas versões de Serviços de Mídia de junho de 2013.

* Habilidade de vincular várias contas de armazenamento a uma conta de Serviço de Mídia. 
  
    StorageAccount
  
    Asset.StorageAccountName e Asset.StorageAccount
* Habilidade de atualizar Job.Priority. 
* Entidades e propriedades relacionadas à notificação: 
  
    JobNotificationSubscription
  
    NotificationEndPoint
  
    Trabalho
* Asset.Uri 
* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Alterações do SDK do .NET dos Serviços de Mídia do Azure
As seguintes alterações foram incluídas em versões do SDK dos Serviços de Mídia de junho de 2013. O SDK dos Serviços de Mídia mais recente está disponível em GitHub.

* A partir da versão 2.3.0.0, o SDK dos Serviços de Mídia oferece suporte para vincular várias contas de armazenamento a uma conta de Serviços de Mídia. As seguintes APIs têm suporte a esse recurso:
  
    O tipo IStorageAccount.
  
    A propriedade Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts.
  
    A propriedade StorageAccount.
  
    A propriedade StorageAccountName.
  
    Para obter mais informações, consulte [Gerenciando ativos de Serviços de Mídia através de várias contas de armazenamento].
* APIs relacionadas à notificação. A partir da versão 2.2.0.0, é possível escutar as notificações de armazenamento de Fila do Azure. Para obter mais informações, consulte [Manipulando notificações de trabalho dos Serviços de Mídia].
  
    A propriedade Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions.
  
    O tipo Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint.
  
    O tipo Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription.
  
    O tipo Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection.
  
    O tipo Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType.
  
    O tipo Microsoft.WindowsAzure.MediaServices.Client.NotificationJobState.
* Dependência do SDK do Cliente de Armazenamento do Azure 2.0 (Microsoft.WindowsAzure.StorageClient.dll).
* Dependência de OData 5.5 (Microsoft.Data.OData.dll).

## <a id="december_changes_12"></a>Versão de dezembro de 2012
### <a name="dec_12_dotnet_changes"></a>Alterações do SDK do .NET dos Serviços de Mídia do Azure
* Intellisense: adicionada a documentação do Intellisense que faltava para vários tipos.
* Microsoft.Practices.TransientFaultHandling.Core: corrigido um problema em que o SDK ainda tinha uma dependência a uma versão mais antiga desse assembly. O SDK agora faz referência à versão 5.1.1209.1 desse conjunto.

Correções para problemas encontrados no SDK de novembro de 2012:

* IAsset.Locators.Count : essa contagem agora é relatada corretamente em novas interfaces IAsset depois de todos os localizadores terem sido excluídos.
* IAssetFile.ContentFileSize : esse valor agora é definido adequadamente após um upload por IAssetFile.Upload(filePath).
* IAssetfile.ContentFileSize: essa propriedade agora pode ser definida durante a criação de um arquivo de ativo. Antes era somente leitura.
* IAssetFile.Upload(filePath) : foi corrigido um problema em que esse método de carregamento assíncrono lançava o erro abaixo ao carregar vários arquivos para o ativo. O erro era “O servidor falhou em autenticar a solicitação. Certifique-se de que o valor do cabeçalho Autorização seja formado corretamente, incluindo a assinatura.”
* IAssetFile.UploadAsync : corrigido um problema em que não mais de cinco arquivos podiam ser carregados ao mesmo tempo.
* IAssetFile.UploadProgressChanged: esse evento agora é fornecido pelo SDK.
* IAssetFile.DownloadAsync (string, BlobTransferClient, ILocator, CancellationToken): essa sobrecarga de método agora é fornecida.
* IAssetFile.DownloadAsync : corrigido um problema em que não mais de cinco arquivos podiam ser baixados ao mesmo tempo.
* IAssetFile.Delete() : corrigido um problema em que chamar o comando “excluir” poderia lançar uma exceção, caso nenhum arquivo fosse carregado para o IAssetFile.
* Trabalhos: corrigido um problema em que encadear uma “Tarefa de MP4 para Smooth Streams” com uma “Tarefa de proteção do PlayReady” usando um modelo de trabalho não criaria nenhuma tarefa.
* EncryptionUtils.GetCertificateFromStore() : esse método não lança mais uma exceção de referência nula devido a falhas em encontrar o certificado com base em problemas de configuração de certificado.

## <a id="november_changes_12"></a>Versão de novembro de 2012
As alterações mencionadas nesta seção eram atualizações incluídas no SDK de novembro de 2012 (versão 2.0.0.0). Essas alterações podem exigir que qualquer código escrito para a versão do SDK de visualização de junho de 2012 seja modificado ou reescrito.

* Ativos
  
    IAsset.Create(assetName) é a ÚNICA função de criação de ativo. IAsset.Create não carrega mais arquivos como parte do método de chamada. Use IAssetFile para carregar.
  
    O método IAsset.Publish e o valor de enumeração AssetState.Publish foram removidos do SDK de Serviços. Qualquer código que conte com esse valor deve ser reescrito.
* FileInfo
  
    Essa classe foi removida e substituída pelo IAssetFile.
  
    IAssetFiles
  
    IAssetFile substitui FileInfo e tem um comportamento diferente. Para usá-la, instancie o objeto IAssetFiles, seguido por um carregamento de arquivo usando o SDK de Serviços de Mídia ou o SDK de Armazenamento do Azure. As seguintes sobrecargas IAssetFile.Upload podem ser usadas:
  
  * IAssetFile.Upload(filePath): um método síncrono que bloqueia o encadeamento e é recomendado somente ao carregar um único arquivo.
  * IAssetFile.UploadAsync(filePath, blobTransferClient, locator, cancellationToken): um método assíncrono. Esse é o mecanismo de carregamento preferido. 
    
    Bug conhecido: usar o cancellationToken, de fato, cancelará o carregamento; porém, o estado de cancelamento das tarefas pode ser qualquer um entre vários estados. É preciso capturar e tratar as exceções adequadamente.
* Localizadores
  
    A versão específica da origem foi removida. O context.Locators.CreateSasLocator(ativo, accessPolicy) específico do SAS será marcado como preterido, ou removido pelo GA. Consulte a seção Localizadores em Nova Funcionalidade para comportamento atualizado.

## <a id="june_changes_12"></a>Versão de visualização de junho de 2012
A seguinte funcionalidade era nova na versão de novembro do SDK.

* Excluindo entidades
  
    Os objetos IAsset, IAssetFile, ILocator, IAccessPolicy, IContentKey agora são excluídos no nível do objeto, ou seja, IObject.Delete(), em vez de exigir uma exclusão na Coleção, que é cloudMediaContext.ObjCollection.Delete(objInstance).
* Localizadores
  
    O Localizadores agora devem ser criados usando o método CreateLocator e usando os valores enum LocatorType.SAS ou LocatorType.OnDemandOrigin como argumento para o tipo específico de localizador que você deseja criar.
  
    Novas propriedades foram adicionadas a Localizadores para tornar mais fácil obter URIs usáveis para seu conteúdo. Essa remodelagem de Localizadores foi feita para fornecer mais flexibilidade para capacidade de extensão de terceiros e aumentar a facilidade de uso para aplicativos cliente de mídia.
* Suporte ao método assíncrono
  
    O suporte assíncrono foi adicionado a todos os métodos.

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Fórum MSDN de Serviços de Mídia do Azure]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Referência da API REST dos Serviços de Mídia do Azure]: https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference
[Detalhes dos preços dos Serviços de Mídia]: http://azure.microsoft.com/pricing/details/media-services/
[Metadados de entrada]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[Metadados de saída]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[Entregando Conteúdo]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[Indexando arquivos de mídia com o Indexador de Mídia do Azure]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[Trabalhando com a Transmissão ao Vivo dos Serviços de Mídia do Azure]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[Usando o serviço de entrega de chave e criptografia dinâmica do AES-128]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[Usando o serviço de entrega de licença e criptografia dinâmica do PlayReady]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: http://azure.microsoft.com/services/preview/
[Visão geral do modelo de licença do PlayReady dos Serviços de Mídia]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[Streaming de conteúdo criptografado de armazenamento]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure portal]: https://manage.windowsazure.com
[Empacotamento Dinâmico]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[Blog de Nick Drouin]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protegendo Smooth Stream com PlayReady]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[Lógica de repetição no SDK de Serviços de Mídia para .NET]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[Grass Valley anuncia streaming EDIUS 7 pela nuvem]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Controlando nomes de arquivo de saída do Codificador do Serviço de Mídia]: http://msdn.microsoft.com/library/azure/dn303341.aspx
[Criando sobreposições]: http://msdn.microsoft.com/library/azure/dn640496.aspx
[Unindo segmentos de vídeo]: http://msdn.microsoft.com/library/azure/dn640504.aspx
[Versões do SDK do .NET dos Serviços de Mídia do Azure 3.0.0.1 e 3.0.0.2]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Serviço de Controle de Acesso (ACS) do Active Directory do Azure]: http://msdn.microsoft.com/library/hh147631.aspx
[Conectando-se à conta dos Serviços de Mídia usando o SDK dos Serviços de Mídia para .NET]: http://msdn.microsoft.com/library/azure/jj129571.aspx
[Extensões do SDK do .NET dos Serviços de Mídia do Azure]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[azure-sdk-tools]: https://github.com/Azure/azure-sdk-tools
[Github]: https://github.com/Azure/azure-sdk-for-media-services
[Gerenciando ativos de Serviços de Mídia através de várias contas de armazenamento]: http://msdn.microsoft.com/library/azure/dn271889.aspx
[Manipulando notificações de trabalho dos Serviços de Mídia]: http://msdn.microsoft.com/library/azure/dn261241.aspx

