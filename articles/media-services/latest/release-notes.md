---
title: Notas de versão dos Serviços de Mídia do Azure v3
description: Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece as atualizações mais recentes sobre os Serviços de Mídia do Azure v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 10/21/2020
ms.author: inhenkel
ms.openlocfilehash: 2254762ec2cc9d99946afaef12f452fdfbfea9e2
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898232"
---
# <a name="azure-media-services-v3-release-notes"></a>Notas de versão dos Serviços de Mídia do Azure v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

>Seja notificado sobre quando revisitar esta página para atualizações copiando e colando esta URL `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+v3+release+notes%22&locale=en-us` no leitor de feed de RSS.

Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

* As versões mais recentes
* Problemas conhecidos
* Correções de bug
* Funcionalidades preteridas

## <a name="known-issues"></a>Problemas conhecidos

> [!NOTE]
> Você pode usar o [portal do Azure](https://portal.azure.com/) para gerenciar [eventos ao vivo](live-events-outputs-concept.md)v3, exibir [ativos](assets-concept.md) e trabalhos v3, obter informações sobre como acessar APIs, criptografar conteúdo. Para todas as outras tarefas de gerenciamento (por exemplo, gerenciar transformações e trabalhos), use a [API REST](/rest/api/media/accountfilters), a [CLI](/cli/azure/ams)ou um dos [SDKs](media-services-apis-overview.md#sdks)com suporte.
>
> Para obter detalhes, consulte: [as limitações de portal do Azure para os serviços de mídia v3](frequently-asked-questions.md#what-are-the-azure-portal-limitations-for-media-services-v3).

## <a name="december-2020"></a>Dezembro de 2020

### <a name="regional-availability"></a>Disponibilidade regional

Os serviços de mídia do Azure agora estão disponíveis na região do leste da Noruega na portal do Azure.  Não há nenhum restV2 nessa região.

## <a name="october-2020"></a>Outubro de 2020

### <a name="basic-audio-analysis"></a>Análise de áudio básica

A predefinição de análise de áudio agora inclui um tipo de preço de modo básico. O novo modo básico do analisador de áudio fornece uma opção de baixo custo para extrair a transcrição de fala e formatar legendas de saída e legendas. Esse modo executa a transcrição de fala em texto e a geração de um arquivo de subtítulo/legenda do VTT. A saída desse modo inclui um arquivo JSON do insights, incluindo apenas palavras-chave, transcrição e informações de tempo. A detecção automática de idioma e os diarization de orador não estão incluídos nesse modo. Consulte a lista de [idiomas com suporte.](analyzing-video-audio-files-concept.md#built-in-presets)

Os clientes que usam o indexador v1 e o indexador v2 devem migrar para a predefinição de análise de áudio básica.

Para obter mais informações sobre o modo básico do analisador de áudio, consulte [analisando arquivos de vídeo e áudio](analyzing-video-audio-files-concept.md).  Para aprender a usar o modo básico do analisador de áudio com a API REST, consulte [como criar uma transformação de áudio básica](how-to-create-basic-audio-transform.md).

### <a name="live-events"></a>Eventos ao Vivo

As atualizações para a maioria das propriedades agora são permitidas quando eventos ao vivo são interrompidos. Além disso, os usuários têm permissão para especificar um prefixo para o nome do host estático para as URLs de entrada e visualização do evento ao vivo. VanityUrl agora é chamado `useStaticHostName` para refletir melhor a intenção da propriedade.

Os eventos ao vivo agora têm um estado de espera.  Consulte [eventos ao vivo e saídas ao vivo nos serviços de mídia](./live-events-outputs-concept.md).

Um evento ao vivo dá suporte ao recebimento de várias taxas de proporção de entrada. O modo de ampliação permite que os clientes especifiquem o comportamento de alongamento para a saída.

A codificação ativa agora adiciona o recurso de saída de fragmentos de intervalo de quadro chave fixo entre 0,5 e 20 segundos.

### <a name="accounts"></a>Contas

> [!WARNING]
> Se você criar uma conta de serviços de mídia com a versão de API 2020-05-01, ela não funcionará com RESTv2 

## <a name="august-2020"></a>Agosto de 2020

### <a name="dynamic-encryption"></a>Criptografia Dinâmica
O suporte para a criptografia herdada do formato de arquivo protegido do PlayReady (PIFF 1,1) agora está disponível no empacotador dinâmico. Isso fornece suporte para conjuntos de TV inteligente herdados da Samsung e do LG que implementaram os rascunhos iniciais Criptografia Comum do CENC (Standard) publicado pela Microsoft.  O formato PIFF 1,1 também é conhecido como o formato de criptografia anteriormente suportado pela biblioteca de cliente do Silverlight. Hoje, o único cenário de caso de uso para esse formato de criptografia é direcionar para o mercado de TV inteligente herdado, onde resta um número não trivial de TVs inteligentes em algumas regiões que só dão suporte a Smooth Streaming com a criptografia PIFF 1,1. 

Para usar o novo suporte à criptografia do PIFF 1,1, altere o valor de criptografia para ' PIFF ' no caminho da URL do localizador de streaming. Para obter mais detalhes, consulte a [visão geral de proteção de conteúdo.](content-protection-overview.md)
Por exemplo: `https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=piff)`|

> [!NOTE]
> O suporte do PIFF 1,1 é fornecido como uma solução compatível com versões anteriores para Smart TV (Samsung, LG) que implementou a versão "Silverlight" inicial do Criptografia Comum. É recomendável usar apenas o formato PIFF, quando necessário, para dar suporte a TVs inteligentes Samsung ou LG herdadas fornecidas entre 2009-2015 que têm suporte na versão PIFF 1,1 da criptografia PlayReady. 

## <a name="july-2020"></a>Julho de 2020

### <a name="live-transcriptions"></a>Transcrições ao vivo

As transcrições ao vivo agora dão suporte a 19 idiomas e 8 regiões.

### <a name="protecting-your-content-with-media-services-and-azure-ad"></a>Protegendo seu conteúdo com os serviços de mídia e o Azure AD

Publicamos um tutorial chamado [proteção de conteúdo de ponta a ponta usando o Azure ad](./azure-ad-content-protection.md).

### <a name="high-availability"></a>Alta disponibilidade

Publicamos uma alta disponibilidade com os serviços de mídia e [visão geral](./media-services-high-availability-encoding.md) de VOD (vídeo por demanda) e [exemplo](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming).

## <a name="june-2020"></a>Junho de 2020

### <a name="live-video-analytics-on-iot-edge-preview-release"></a>Análise de vídeo ao vivo na versão de visualização IoT Edge

A visualização da análise de vídeo ao vivo em IoT Edge ficou pública. Para obter mais informações, consulte [notas de versão](../live-video-analytics-edge/release-notes.md).

A análise de vídeo ao vivo no IoT Edge é uma expansão para a família de serviços de mídia. Ele permite que você analise vídeos ao vivo com modelos de ia de sua escolha em seus próprios dispositivos de borda e, opcionalmente, Capture e grave esse vídeo. Agora você pode criar aplicativos com análise de vídeo em tempo real na borda sem se preocupar com a complexidade de criar e operar um pipeline de vídeo ao vivo.

## <a name="may-2020"></a>Maio de 2020

Os Serviços de Mídia do Azure está em disponibilidade geral nas seguintes regiões: "Norte da Alemanha", "Centro-oeste da Alemanha", "Norte da Suíça" e "Oeste da Suíça". Os clientes podem implantar os Serviços de Mídia nessas regiões usando o portal do Azure.

## <a name="april-2020"></a>Abril de 2020

### <a name="improvements-in-documentation"></a>Melhorias na documentação

Os documentos do Player de Mídia do Azure foram migrados para a [documentação do Azure](../azure-media-player/azure-media-player-overview.md).

## <a name="january-2020"></a>Janeiro de 2020

### <a name="improvements-in-media-processors"></a>Melhorias nos processadores de mídia

- Suporte aprimorado para fontes entrelaçadas na Análise de Vídeo – esse conteúdo agora é desentrelaçado corretamente antes de ser enviado aos mecanismos de inferência.
- Ao gerar miniaturas com o modo "Melhor", o codificador agora pesquisa além dos 30 segundos para selecionar um quadro que não seja monocromático.

### <a name="azure-government-cloud-updates"></a>Atualizações da nuvem do Azure Government

Os Serviços de Mídia estão em disponibilidade geral nas seguintes regiões do Azure Government: *USGov – Arizona* e *USGov – Texas*.

## <a name="december-2019"></a>Dezembro de 2019

Suporte adicionado à CDN para os cabeçalhos *Origin-Assist-Prefetch* para streaming sob demanda em tempo real e de vídeo; disponível para clientes que têm contrato direto com a CDN da Akamai. O recurso Origin-Assist-CDN-Prefetch envolve as seguintes trocas de cabeçalho HTTP entre a CDN da Akamai e a origem dos Serviços de Mídia do Azure:

|Cabeçalho HTTP|Valores|Remetente|Receptor|Finalidade|
| ---- | ---- | ---- | ---- | ----- |
|CDN-Origin-Assist-Prefetch-Enabled | 1 (padrão) ou 0 |CDN|Origem|Para indicar que a CDN está habilitada para pré-busca|
|CDN-Origin-Assist-Prefetch-Path| Exemplo: <br/>Fragments(video=1400000000,format=mpd-time-cmaf)|Origem|CDN|Para fornecer o caminho de pré-busca para a CDN|
|CDN-Origin-Assist-Prefetch-Request|1 (solicitação de pré-busca) ou 0 (solicitação regular)|CDN|Origem|Para indicar que a solicitação da CDN é uma pré-busca|

Para ver parte da troca de cabeçalho em ação, você pode tentar as seguintes etapas:

1. Use Postman ou curl para emitir uma solicitação para a origem dos Serviços de Mídia para um segmento ou fragmento de áudio ou vídeo. Certifique-se de adicionar o cabeçalho CDN-Origin-Assist-Prefetch-Enabled: 1 na solicitação.
2. Na resposta, você deve ver o cabeçalho CDN-Origin-Assist-Prefetch-Path com um caminho relativo como seu valor.

## <a name="november-2019"></a>Novembro de 2019

### <a name="live-transcription-preview"></a>Transcrição ao Vivo (Versão prévia)

A Transcrição ao Vivo agora está em versão prévia pública e disponível para uso na região Oeste dos EUA 2.

A Transcrição ao Vivo foi projetada para trabalhar em conjunto com eventos ao vivo como uma funcionalidade complementar.  Há suporte para eventos ao vivo de codificação de passagem e Standard ou Premium.  Quando esse recurso é habilitado, o serviço usa o recurso de [Conversão de Fala em Texto](../../cognitive-services/speech-service/speech-to-text.md) dos Serviços Cognitivos para transcrever as palavras faladas no áudio de entrada em texto. Esse texto é disponibilizado para entrega junto com vídeo e áudio em protocolos MPEG-DASH e HLS. A cobrança é baseada em um novo medidor de complemento que é um custo adicional para o evento ao vivo quando ele está no estado "Em execução".  Para obter detalhes sobre a transcrição ao vivo e a cobrança, consulte [Transcrição ao vivo](live-transcription.md)

> [!NOTE]
> Atualmente, a transcrição ao vivo só está disponível como uma versão prévia do recurso na região Oeste dos EUA 2. Nesse momento, ela somente é compatível com a transcrição de palavras faladas em inglês (en-US).

### <a name="content-protection"></a>Proteção de conteúdo

O recurso de *Prevenção de reprodução de token* disponibilizado em regiões limitadas em setembro agora está disponível em todas as regiões.
Os clientes dos Serviços de Mídia agora podem definir um limite no número de vezes que o mesmo token pode ser usado para solicitar uma chave ou uma licença. Para obter mais informações, consulte [Prevenção de reprodução de token](content-protection-overview.md#token-replay-prevention).

### <a name="new-recommended-live-encoder-partners"></a>Novos parceiros de codificador dinâmico recomendados

Suporte adicionado para os seguintes novos codificadores de parceiros recomendados para streaming ao vivo de RTMP:

- [Cambria Live 4.3](https://www.capellasystems.net/products/cambria-live/)
- [Câmeras de ação GoPro Hero7/8 e Max](https://gopro.com/help/articles/block/getting-started-with-live-streaming)
- [Restream.io](https://restream.io/)

### <a name="file-encoding-enhancements"></a>Aprimoramentos de codificação de arquivo

- Uma nova predefinição de codificação com reconhecimento de conteúdo está disponível. Ela produz um conjunto de MP4s alinhados a GOP usando a codificação com reconhecimento de conteúdo. Usando qualquer conteúdo de entrada, o serviço executa uma análise leve inicial do conteúdo de entrada. Ele usa esses resultados para determinar o número ideal de camadas, a taxa de bits apropriada e as configurações de resolução para entrega por streaming adaptável. Essa predefinição é particularmente eficaz para vídeos de baixa complexidade e de complexidade média, em que os arquivos de saída têm taxas de bits menores, mas com uma qualidade que ainda oferece uma boa experiência aos espectadores. A saída conterá arquivos MP4 com vídeo e áudio intercalados. Para obter mais informações, confira as [especificações da API aberta](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json).
- Desempenho aprimorado e multithreading para o redimensionador no codificador Standard. Em condições específicas, o cliente deve ver um aumento de desempenho de 5 a 40% da codificação de VOD. O conteúdo de baixa complexidade codificado em várias taxas de bits terá o maior aumento de desempenho. 
- A codificação padrão agora mantém uma cadência de GOP regular para conteúdo de VFR (taxa de quadros variável) durante a codificação de VOD ao usar a configuração de GOP baseada em tempo.  Isso significa que o cliente que envia conteúdo de taxa de quadros misto que varia entre 15-30 fps, por exemplo, agora deve ver as distâncias GOP regulares calculadas na saída para arquivos MP4 de streaming de taxa de bits adaptável. Isso vai aprimorar a capacidade de alternar diretamente entre as faixas ao entregar HLS ou DASH. 
-  Sincronização antivírus aprimorada para conteúdo de origem de VFR (taxa de quadros variável)

### <a name="video-indexer-video-analytics"></a>Video Indexer, análise de vídeo

- Os quadros-chave extraídos usando a predefinição VideoAnalyzer agora estão na resolução original do vídeo em vez de serem redimensionados. A extração de quadro-chave de alta resolução fornece imagens de qualidade original e permite que você use os modelos de inteligência artificial baseados em imagem fornecidos pelo Microsoft Pesquisa Visual Computacional e Visão Personalizada Services para obter ainda mais informações do seu vídeo.

## <a name="september-2019"></a>Setembro de 2019

###  <a name="media-services-v3"></a>Serviços de Mídia v3  

#### <a name="live-linear-encoding-of-live-events"></a>Codificação linear dinâmica de eventos ao vivo

Os Serviços de Mídia v3 estão anunciando a versão prévia por 365 dias, 24 horas por dia, da codificação linear dinâmica dos eventos ao vivo.

###  <a name="media-services-v2"></a>Serviços de Mídia v2  

#### <a name="deprecation-of-media-processors"></a>Substituição dos processadores de mídia

Estamos anunciando a substituição do *Azure Media Indexer* e da *versão prévia do Azure Media Indexer 2*. Para as datas de desativação, consulte o artigo  [componentes herdados](../previous/legacy-components.md) . O [Video Indexer dos Serviços de Mídia do Azure](../video-indexer/index.yml) substitui esses processadores de mídia herdados.

Para obter mais informações, confira [Migrar do Azure Media Indexer e do Azure Media Indexer 2 para o Video Indexer dos Serviços de Mídia do Azure](../previous/migrate-indexer-v1-v2.md).

## <a name="august-2019"></a>Agosto de 2019

###  <a name="media-services-v3"></a>Serviços de Mídia v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>O par regional da África do Sul está aberto para os Serviços de Mídia 

Os Serviços de Mídia agora estão disponíveis nas regiões Norte da África do Sul e Oeste da África do Sul.

Para obter mais informações, confira [Nuvens e regiões em que os Serviços de Mídia v3 existem](azure-clouds-regions.md).

###  <a name="media-services-v2"></a>Serviços de Mídia v2  

#### <a name="deprecation-of-media-processors"></a>Substituição dos processadores de mídia

Estamos anunciando a substituição dos processadores de mídia *WAME (Codificador de Mídia do Azure para Windows)* e *AME (Codificador de Mídia do Azure)* , que estão sendo desativados. Para as datas de desativação, consulte este artigo sobre [componentes herdados](../previous/legacy-components.md) .

Para obter detalhes, consulte [Migrar WAME para Media Encoder Standard](../previous/migrate-windows-azure-media-encoder.md) e [Migrar AME para Media Encoder Standard](../previous/migrate-azure-media-encoder.md).
 
## <a name="july-2019"></a>Julho de 2019

### <a name="content-protection"></a>Proteção de conteúdo

Ao transmitir por streaming de conteúdo protegido com restrição de token, os usuários finais precisam obter um token que é enviado como parte da solicitação de entrega de chave. O recurso de *Prevenção de reprodução de token* permite que os clientes dos Serviços de Mídia definam um limite de quantas vezes o mesmo token pode ser usado para solicitar uma chave ou uma licença. Para obter mais informações, consulte [Prevenção de reprodução de token](content-protection-overview.md#token-replay-prevention).

Desde julho, a versão prévia do recurso só estava disponível nas regiões Centro dos EUA e Centro-Oeste dos EUA.

## <a name="june-2019"></a>Junho de 2019

### <a name="video-subclipping"></a>Sub-recorte de vídeo

Agora você pode cortar ou sub-recortar um vídeo ao codificá-lo usando um [Trabalho](/rest/api/media/jobs). 

Essa funcionalidade funciona com qualquer [Transformação](/rest/api/media/transforms) criada usando as predefinições [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) ou [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset). 

Veja exemplos:

* [Sub-recortar um vídeo com .NET](subclip-video-dotnet-howto.md)
* [Sub-recortar um vídeo com REST](subclip-video-rest-howto.md)

## <a name="may-2019"></a>Maio de 2019

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Suporte do Azure Monitor para métricas e logs de diagnóstico dos Serviços de Mídia

Agora você pode usar o Azure Monitor para exibir dados de telemetria emitidos pelos Serviços de Mídia.

* Use os logs de diagnóstico Azure Monitor para monitorar solicitações enviadas pelo ponto de extremidade de entrega de chave dos Serviços de Mídia. 
* Monitore as métricas emitidas pelos [pontos de extremidade de streaming](streaming-endpoint-concept.md) dos Serviços de Mídia.   

Para obter mais detalhes, confira [Monitorar logs de diagnóstico e métricas dos Serviços de Mídia](media-services-metrics-diagnostic-logs.md).

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>Suporte a múltiplas faixas de áudio no Empacotamento Dinâmico 

Ao transmitir por streaming ativos com múltiplas faixas de áudio com vários codecs e idiomas, o [Empacotamento Dinâmico](dynamic-packaging-overview.md) agora é compatível com múltiplas faixas de áudio para a saída de HLS (versão 4 ou superior).

### <a name="korea-regional-pair-is-open-for-media-services"></a>O par regional da Coreia do Sul está aberto para os Serviços de Mídia 

Os Serviços de Mídia agora estão disponíveis nas regiões Coreia Central e Sul da Coreia. 

Para obter mais informações, confira [Nuvens e regiões em que os Serviços de Mídia v3 existem](azure-clouds-regions.md).

### <a name="performance-improvements"></a>Melhorias de desempenho

Atualizações adicionadas que incluem melhorias de desempenho dos Serviços de Mídia.

* O tamanho máximo do arquivo com suporte para processamento foi atualizado. Ver, [cotas e limites](limits-quotas-constraints.md).
* [A codificação acelera as melhorias](concept-media-reserved-units.md).

## <a name="april-2019"></a>Abril de 2019

### <a name="new-presets"></a>Novas predefinições

* [FaceDetectorPreset](/rest/api/media/transforms/createorupdate#facedetectorpreset) foi adicionado às predefinições do analisador interno.
* [ContentAwareEncodingExperimental](/rest/api/media/transforms/createorupdate#encodernamedpreset) foi adicionado às predefinições do codificador interno. Para obter mais informações, consulte [Codificação com reconhecimento de conteúdo](content-aware-encoding.md). 

## <a name="march-2019"></a>Março de 2019

O Empacotamento Dinâmico agora é compatível com o Dolby Atmos. Para obter mais informações, consulte [Codecs de áudio com suporte do Empacotamento Dinâmico](dynamic-packaging-overview.md#audio-codecs-supported-by-dynamic-packaging).

Agora você pode especificar uma lista de filtros de conta ou ativos, que se aplicariam ao seu Localizador de Streaming. Para obter mais informações, consulte [Associar filtros com o Localizador de Streaming](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="february-2019"></a>Fevereiro de 2019

Os Serviços de Mídia v3 agora são compatíveis nas nuvens nacionais do Azure. Nem todos os recursos estão disponíveis em todas as nuvens. Para obter mais detalhes, confira [Nuvens e regiões em que os Serviços de Mídia do Azure v3 existem](azure-clouds-regions.md).

O evento [Microsoft.Media.JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) foi adicionado aos esquemas da Grade de Eventos do Azure para Serviços de Mídia.

## <a name="january-2019"></a>Janeiro de 2019

### <a name="media-encoder-standard-and-mpi-files"></a>Arquivos do Media Encoder Standard e MPI 

Ao codificar usando o Media Encoder Standard para produzir arquivos MP4, um novo arquivo .mpi será gerado e adicionado ao Ativo de saída. Esse arquivo MPI destina-se a melhorar o desempenho dos cenários de empacotamento e [streaming dinâmicos](dynamic-packaging-overview.md).

Você não deve modificar nem remover o arquivo MPI, nem usar qualquer dependência em seu serviço na existência (ou não) desse arquivo.

## <a name="december-2018"></a>Dezembro de 2018

As atualizações da versão disponível ao público geral da API V3 incluem:
       
* As propriedades **PresentationTimeRange** não são mais “obrigatórias” para **Filtros de Ativo** e **Filtros de Conta**. 
* As opções de consulta $top e $skip para **Trabalhos** e **Transformações** foram removidas e $orderby foi adicionado. Como parte da adição da nova funcionalidade de ordenação, foi descoberto que as opções $top e $skip acidentalmente tinham sido expostas anteriormente, embora não tenham sido implementadas.
* A extensibilidade da enumeração foi reabilitada. Esse recurso estava habilitado nas versões prévias do SDK e foi acidentalmente desabilitado na versão disponível ao público geral.
* Duas políticas predefinidas de transmissão foram renomeadas. **SecureStreaming** agora é **MultiDrmCencStreaming**. **SecureStreamingWithFairPlay** agora é **Predefined_MultiDrmStreaming**.

## <a name="november-2018"></a>Novembro de 2018

O módulo de CLI 2.0 agora está disponível para [serviços de mídia do Azure v3 GA](/cli/azure/ams?view=azure-cli-latest&preserve-view=true) – v 2.0.50.

### <a name="new-commands"></a>Novos comandos

- [conta do ams AZ](/cli/azure/ams/account?view=azure-cli-latest&preserve-view=true)
- [filtro de conta do ams AZ](/cli/azure/ams/account-filter?view=azure-cli-latest&preserve-view=true)
- [ativo do ams AZ](/cli/azure/ams/asset?view=azure-cli-latest&preserve-view=true)
- [ativos de ams AZ-filtro](/cli/azure/ams/asset-filter?view=azure-cli-latest&preserve-view=true)
- [AZ ams chave de política de conteúdo](/cli/azure/ams/content-key-policy?view=azure-cli-latest&preserve-view=true)
- [trabalho de ams AZ](/cli/azure/ams/job?view=azure-cli-latest&preserve-view=true)
- [AZ ams-evento ao vivo](/cli/azure/ams/live-event?view=azure-cli-latest&preserve-view=true)
- [ams AZ saída ao vivo](/cli/azure/ams/live-output?view=azure-cli-latest&preserve-view=true)
- [AZ ams streaming-ponto de extremidade](/cli/azure/ams/streaming-endpoint?view=azure-cli-latest&preserve-view=true)
- [AZ ams-localizador de streaming](/cli/azure/ams/streaming-locator?view=azure-cli-latest&preserve-view=true)
- [az ams account mru](/cli/azure/ams/account/mru?view=azure-cli-latest&preserve-view=true) – permite que você gerencie Unidades Reservadas para Mídia. Para obter mais informações, confira [Dimensionar Unidades Reservadas para Mídia](media-reserved-units-cli-how-to.md).

### <a name="new-features-and-breaking-changes"></a>Novos recursos e alterações de quebra

#### <a name="asset-commands"></a>Comandos de ativos

- ```--storage-account``` e ```--container``` argumentos adicionados.
- Valores padrão para o tempo de expiração (Now + 23h) e permissões (Read) no comando ```az ams asset get-sas-url``` adicionado.

#### <a name="job-commands"></a>Comandos de trabalho

- ```--correlation-data``` e ```--label``` argumentos adicionados
- ```--output-asset-names``` foi renomeado para ```--output-assets```. Agora, ele aceita uma lista separada por espaços dos ativos no formato 'assetName=label'. Um ativo sem rótulo pode ser enviado assim: 'assetName ='.

#### <a name="streaming-locator-commands"></a>Comandos do Localizador de Fluxo

- O comando base ```az ams streaming locator``` foi substituído por ```az ams streaming-locator```.
- ```--streaming-locator-id``` e ```--alternative-media-id support``` argumentos adicionados.
- ```--content-keys argument``` argumento atualizado.
- ```--content-policy-name``` foi renomeado para ```--content-key-policy-name```.

#### <a name="streaming-policy-commands"></a>Comandos de Política de Fluxo

- O comando base ```az ams streaming policy``` foi substituído por ```az ams streaming-policy```.
- Suporte para parâmetros de criptografia em ```az ams streaming-policy create``` adicionado.

#### <a name="transform-commands"></a>Transformar comandos

- ```--preset-names``` argumento substituído por ```--preset```. Agora, você só pode definir uma saída/predefinição de cada vez (para adicionar mais, é preciso executar ```az ams transform output add```). Além disso, você pode definir o StandardEncoderPreset personalizado passando o caminho para seu JSON personalizado.
- ```az ams transform output remove``` pode ser executado passando o índice de saída para remover.
- ```--relative-priority, --on-error, --audio-language and --insights-to-extract``` argumentos adicionados no ```az ams transform create``` e ```az ams transform output add``` comandos.

## <a name="october-2018---ga"></a>Outubro de 2018 - GA

Esta seção descreve as atualizações de outubro do AMS (Serviços de Mídia do Azure).

### <a name="rest-v3-ga-release"></a>Versão GA v3 de REST

A [versão GA v3 de REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) inclui mais APIs para Live, filtros de manifesto de nível de Conta/Ativo e suporte a DRM.

#### <a name="azure-resource-management"></a>Gerenciamento de Recursos do Azure 

O suporte para o Gerenciamento de Recursos do Azure permite o gerenciamento unificado e a API de operações (agora, tudo em um só lugar).

A partir desta versão, é possível usar modelos do Resource Manager para criar Eventos ao Vivo.

#### <a name="improvement-of-asset-operations"></a>Melhoria das operações de Ativos 

As seguintes melhorias foram introduzidas:

- Ingestão de URLs HTTP(s) ou URLs de SAS do Armazenamento de Blobs do Azure.
- Especifique seus próprios nomes de contêiner para os Ativos. 
- Suporte de saída mais fácil para criar fluxos de trabalho personalizados com o Azure Functions.

#### <a name="new-transform-object"></a>Novo objeto de Transformação

O novo objeto de **Transformação** simplifica o modelo de Codificação. O novo objeto facilita a criação e o compartilhamento de modelos e predefinições de codificação do Resource Manager. 

#### <a name="azure-active-directory-authentication-and-azure-rbac"></a>Autenticação de Azure Active Directory e RBAC do Azure

A autenticação do Azure AD e o Azure RBAC (controle de acesso baseado em função) permitem transformações seguras, LiveEvents, políticas de chave de conteúdo ou ativos por função ou usuários no Azure AD.

#### <a name="client-sdks"></a>SDKs do cliente  

Linguagens com suporte nos Serviços de Mídia v3: .NET Core, Java, Node.js, Ruby, Datilografado, Python, Go.

#### <a name="live-encoding-updates"></a>Atualizações de codificação ao vivo

As seguintes atualizações de codificação ao vivo são apresentadas:

- Novo modo de baixa latência para ao vivo (10 segundos de ponta a ponta).
- Suporte aprimorado do RTMP (maior estabilidade e mais suporte de codificador de código-fonte).
- Ingestão segura de RTMPS.

    Ao criar um evento ao vivo, você receberá quatro URLs de ingestão. As 4 URLs de ingestão são quase idênticas, têm o mesmo token de streaming (AppId) e apenas a parte do número da porta é diferente. Duas das URLs são primárias e de backup para RTMPS. 
- Suporte de transcodificação 24 horas por dia. 
- Suporte aprimorado de sinalização de anúncio no RTMP via SCTE35.

#### <a name="improved-event-grid-support"></a>Suporte aprimorado a Grade de Eventos

É possível ver os seguintes aprimoramentos de suporte à Grade de Eventos:

- Integração à Grade de Eventos do Azure para facilitar o desenvolvimento com os Aplicativos Lógicos e o Azure Functions. 
- Inscreva-se para eventos sobre Codificação, Canais ao vivo, e muito mais.

### <a name="cmaf-support"></a>Suporte CMAF

Suporte de criptografia CMAF e 'cbcs' para players Apple HLS (iOS 11+) e MPEG-DASH que dão suporte a CMAF.

### <a name="video-indexer"></a>Video Indexer

A versão de GA do Video Indexer foi anunciada em agosto. Para obter novas informações sobre recursos atualmente com suporte, consulte [O que é Video Indexer](../video-indexer/video-indexer-overview.md?bc=/azure/media-services/video-indexer/breadcrumb/toc.json&toc=/azure/media-services/video-indexer/toc.json). 

### <a name="plans-for-changes"></a>Planos de alterações

#### <a name="azure-cli-20"></a>CLI do Azure 2.0
 
O módulo da CLI 2.0 do Azure que inclui operações em todos os recursos (incluindo Live, Políticas de Chave de Conteúdo, Filtros de Conta/Ativo, Políticas de Streaming) estará disponível em breve. 

### <a name="known-issues"></a>Problemas conhecidos

Somente os clientes que usaram a API de versão prévia para material ativo ou AccountFilters são afetados pelo problema a seguir.

Se você criou Ativos ou Filtros de Conta entre 09/28 e 10/12 com CLI ou APIs dos Serviços de Mídia v3, será necessário remover todos os Ativos e AccountFilters e recriá-los devido a um conflito de versões. 

## <a name="may-2018---preview"></a>Maio de 2018 - Versão prévia

### <a name="net-sdk"></a>SDK .NET

Os seguintes recursos estão presentes no SDK do .NET:

* **Transformações** e **Trabalhos** para codificar ou analisar o conteúdo de mídia. Para obter exemplos, consulte [Transmitir arquivos por stream](stream-files-tutorial-with-api.md) e [Analisar](analyze-videos-tutorial-with-api.md).
* **Localizadores de streaming** para publicar e transmitir conteúdo aos dispositivos do usuário final
* **Políticas de streaming** e **Políticas de chave de conteúdo** para configurar a distribuição de chaves e o DRM (gerenciamento de direitos digitais) durante a entrega de conteúdo.
* **Eventos ao vivo** e **Saídas ao vivo** para configurar a ingestão e o arquivamento de conteúdo de transmissão ao vivo.
* **Ativos** para armazenar e publicar o conteúdo de mídia no Armazenamento do Azure. 
* **Pontos de extremidade de streaming** para configurar e dimensionar o empacotamento dinâmico, a criptografia e o streaming de conteúdo de mídia ao vivo e sob demanda.

### <a name="known-issues"></a>Problemas conhecidos

* Ao enviar um trabalho, você pode especificar para ingerir o vídeo de origem usando URLs HTTPS, URLs SAS ou caminhos para arquivos localizados no Armazenamento de Blobs do Azure. Atualmente, os serviços de mídia v3 não dão suporte à codificação de transferência em partes sobre URLs HTTPS.

## <a name="ask-questions-give-feedback-get-updates"></a>Fazer perguntas, comentar, obter atualizações

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="see-also"></a>Confira também

[Diretrizes de migração para mudar dos serviços de mídia v2 para v3](migrate-v-2-v-3-migration-introduction.md).

## <a name="next-steps"></a>Próximas etapas

- [Visão geral](media-services-overview.md)
- [Atualizações na documentação dos Serviços de Mídia do Azure v3](docs-release-notes.md)
- [Notas sobre a versão dos Serviços de Mídia v2](../previous/media-services-release-notes.md)