---
title: Notas sobre a versão dos Serviços de Mídia do Azure | Microsoft Docs
description: Este artigo aborda as notas sobre a versão dos Serviços de Mídia do Microsoft Azure v2.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: media
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 801d810a82e4e484ca1ab30c5c6d510860dbab6b
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566006"
---
# <a name="azure-media-services-release-notes"></a>Notas de versão dos Serviços de Mídia do Azure

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Estas notas de versão para os Serviços de Mídia do Azure resumem as alterações de versões anteriores e os problemas conhecidos.

> [!NOTE]
> Não estão sendo adicionados novos recursos aos Serviços de Mídia v2. <br/>Confira a versão mais recente, [Serviços de Mídia v3](../latest/index.yml). Além disso, consulte s [diretrizes de migração da v2 para a v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Queremos ouvir nossos clientes para que possamos focar na correção de problemas que afetam você. Para relatar um problema ou fazer uma pergunta, faça uma postagem no [Fórum MSDN de Serviços de Mídia do Azure]. 

## <a name="known-issues"></a><a name="issues"></a>Problemas conhecidos
### <a name="media-services-general-issues"></a><a name="general_issues"></a>Problemas gerais dos Serviços de Mídia

| Problema | Descrição |
| --- | --- |
| Vários cabeçalhos HTTP comuns não são fornecidos na API REST. |Se você desenvolver aplicativos de Serviços de Mídia usando a API REST, verá que não há compatibilidade com alguns campos de cabeçalho HTTP comuns (incluindo CLIENT-REQUEST-ID, REQUEST-ID e RETURN-CLIENT-REQUEST-ID). Os cabeçalhos serão adicionados em uma atualização futura. |
| Não é permitida a codificação por porcentagem. |Os Serviços de Mídia usam o valor da propriedade IAssetFile.Name ao criar URLs para o conteúdo de streaming (por exemplo, `http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters`). Por esse motivo, não é permitida a codificação por porcentagem. O valor da propriedade Name não pode ter quaisquer dos seguintes [caracteres reservados para codificação por porcentagem](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#[]". Além disso, pode haver somente um "." para a extensão de nome de arquivo. |
| O método ListBlobs que faz parte do SDK do Armazenamento do Azure versão 3.x falha. |Os Serviços de Mídia geram URLs SAS com base na versão de [12/02/2012](/rest/api/storageservices/version-2012-02-12) . Se desejar que o SDK de Armazenamento liste os blobs em um contêiner de blob, use o método [CloudBlobContainer.ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs) que faz parte do SDK de Armazenamento versão 2.x. |
| O mecanismo de limitação dos Serviços de Mídia restringe o uso dos recursos para aplicativos que fazem solicitações excessivas ao serviço. O serviço pode retornar o código de status HTTP 503, "Serviço Não Disponível". |Para obter mais informações, confira a descrição do código de status HTTP 503 em [Códigos de erro dos Serviços de Mídia](media-services-encoding-error-codes.md). |
| Ao consultar entidades, no máximo 1.000 entidades são retornadas ao mesmo tempo porque a REST versão 2 pública limita os resultados da consulta a 1.000 resultados. |Use Skip e Take (.NET)/ top (REST), conforme descrito [neste exemplo de .NET](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) e [neste exemplo de API REST](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). |
| Alguns clientes podem se deparar com um problema de marcas repetidas no manifesto do Smooth Streaming. |Para saber mais, consulte [esta seção](media-services-deliver-content-overview.md#known-issues). |
| Os objetos do SDK do .NET dos Serviços de Mídia não podem ser serializados e, por isso, não funcionam com o Cache para Redis do Azure. |Se você tentar serializar o objeto AssetCollection do SDK para adicioná-lo ao Cache para Redis do Azure, uma exceção será lançada. |
|A API REST responde com a mensagem de erro "O filtro não pode ser acessado por esta versão da API REST" ao tentar obter um filtro de nível de Conta ou Ativo.|O filtro foi criado ou modificado com uma versão de API mais recente da que está sendo usada para tentar obter o filtro. Isso pode acontecer se duas versões da API estiverem sendo usadas pelo código ou por ferramentas que estão sendo usadas pelo cliente.  A melhor solução aqui é atualizar o código ou as ferramentas para usar as duas versões de API mais recentes.|

## <a name="rest-api-version-history"></a><a name="rest_version_history"></a>Histórico de versão da API REST
Para obter informações sobre o histórico de versões da API REST dos Serviços de Mídia, consulte a [Referência da API REST dos Serviços de Mídia do Azure].

## <a name="february-2021"></a>Fevereiro de 2021

### <a name="azure-media-services-v2-api-and-sdks-deprecation-announcement"></a>Anúncio de substituição de API e SDKs dos serviços de mídia do Azure v2

#### <a name="update-your-azure-media-services-rest-api-and-sdks-to-v3-by-29-february-2024"></a>Atualize seus API REST de Serviços de Mídia do Azure e SDKs para v3 em 29 de fevereiro de 2024

Como a versão 3 de SDKs de API REST de Serviços de Mídia do Azure e cliente para .NET e Java oferece mais recursos do que a versão 2, estamos desativando a versão 2 dos SDKs de API REST de Serviços de Mídia do Azure e cliente para .NET e Java. Incentivamos você a fazer o comutador mais cedo para obter os benefícios mais avançados da versão 3 de API REST de Serviços de Mídia do Azure e SDKs de cliente para .NET e Java.
A versão 3 fornece:
 
- suporte a eventos ao vivo 24x7
- APIs REST do ARM, SDKs de cliente para .NET Core, Node.js, Python, Java, Go e Ruby.
- Chaves gerenciadas pelo cliente, integração de armazenamento confiável, suporte a link privado e [muito mais](https://review.docs.microsoft.com/en-us/azure/media-services/latest/migrate-v-2-v-3-migration-benefits)

#### <a name="action-required"></a>Ação necessária:

Para minimizar a interrupção em suas cargas de trabalho, examine o [Guia de migração](../latest/migrate-v-2-v-3-migration-introduction.md) para fazer a transição do código da API e dos SDKs da versão 2 para a API e o SDK da versão 3 antes de 29 de fevereiro de 2024.
**Depois de 29 de fevereiro de 2024**, os serviços de mídia do Azure deixarão de aceitar o tráfego na API REST da versão 2, a API de gerenciamento de conta do ARM versão 2015-10-01 ou dos SDKs do cliente .NET da versão 2. Isso inclui todos os SDKS de cliente de software livre de terceiros que podem chamar a API da versão 2.  

Consulte o anúncio oficial de [atualizações do Azure](https://azure.microsoft.com/updates/update-your-azure-media-services-rest-api-and-sdks-to-v3-by-29-february-2024/).

## <a name="september-2020"></a>Setembro de 2020

As seguintes propriedades v2 não serão mais preenchidas com dados de progresso do trabalho histórico:

* [HistoricalEvents](/dotnet/api/microsoft.windowsazure.mediaservices.client.itask.historicalevents)
* [PerfMessage](/dotnet/api/microsoft.windowsazure.mediaservices.client.itask.perfmessage)

Para obter o histórico de tarefas, você deve usar as notificações de trabalho v2 por meio de WebHooks ou mensagens de fila usando pontos de extremidade de notificação. Para obter mais informações, consulte:

* [Usar o Armazenamento de Fila do Azure para monitorar as notificações de trabalho dos Serviços de Mídia](media-services-dotnet-check-job-progress-with-queues.md)
* [Usar WebHooks do Azure para monitorar notificações de trabalho dos serviços de mídia](media-services-dotnet-check-job-progress-with-webhooks.md)

## <a name="february-2020"></a>Fevereiro de 2020

Alguns processadores de mídia de análise serão desativados. Para ver as datas de desativação, consulte o tópico [Componentes herdados](legacy-components.md).

## <a name="september-2019"></a>Setembro de 2019

### <a name="deprecation-of-media-processors"></a>Substituição dos processadores de mídia

Estamos anunciando a substituição do *Azure Media Indexer* e da *versão preliminar do Azure Media Indexer 2*. O [Video Indexer dos Serviços de Mídia do Azure](../video-indexer/index.yml) substitui esses processadores de mídia herdados.

Para ver as datas de desativação, consulte o tópico de [componentes herdados](legacy-components.md).

Consulte também [Migrar do Azure Media Indexer e do Azure Media Indexer 2 para o Video Indexer dos Serviços de Mídia do Azure](migrate-indexer-v1-v2.md).

## <a name="august-2019"></a>Agosto de 2019

### <a name="deprecation-of-media-processors"></a>Substituição dos processadores de mídia

Estamos anunciando a substituição dos processadores de mídia *Codificador de Mídia do Azure para Windows* (WAME) e *Codificador de Mídia do Azure* (AME). Para ver as datas de desativação, consulte o tópico de [componentes herdados](legacy-components.md).

Para obter detalhes, consulte [Migrar WAME para Media Encoder Standard](./migrate-windows-azure-media-encoder.md) e [Migrar AME para Media Encoder Standard](./migrate-azure-media-encoder.md).

## <a name="march-2019"></a>Março de 2019

A versão prévia do recurso Media Hyperlapse dos Serviços de Mídia do Azure foi substituída.

## <a name="december-2018"></a>Dezembro de 2018

A versão prévia do recurso Media Hyperlapse dos Serviços de Mídia do Azure será desativada em breve. A partir de 19 dezembro de 2018, os Serviços de Mídia deixará de fazer alterações ou melhorias ao recurso Media Hyperlapse. Em 29 de março de 2019, ele será desativado e não está mais disponível.

## <a name="october-2018"></a>Outubro de 2018

### <a name="cmaf-support"></a>Suporte CMAF

Suporte de criptografia CMAF e 'cbcs' para players Apple HLS (iOS 11+) e MPEG-DASH que dão suporte a CMAF.

### <a name="web-vtt-thumbnail-sprites"></a>Sprites em miniatura do Web VTT

Agora você pode usar o Media Services para gerar sprites de miniatura do Web VTT usando nossas APIs v2. Para obter mais informações, consulte [gerar um miniatura sprite](generate-thumbnail-sprite.md).

## <a name="july-2018"></a>Julho de 2018

Com a versão mais recente do serviço, há pequenas alterações de formatação às mensagens de erro retornadas pelo serviço quando uma tarefa falhar em relação a como ela é dividida em duas ou mais linhas.

## <a name="may-2018"></a>Maio de 2018 

A partir de 12 de maio de 2018, os canais ao vivo não darão mais suporte ao protocolo de ingestão de fluxo de transporte RTP/MPEG-2. Faça a migração de RTP/MPEG-2 para protocolos de ingestão RTMP ou MP4 fragmentado (Smooth Streaming).

## <a name="october-2017-release"></a>Versão de outubro de 2017
> [!IMPORTANT] 
> O suporte dos Serviços de Mídia ao modelo de autenticação do Serviço de Controle de Acesso do Azure está sendo preterido. No dia 22 de junho de 2018, não será mais possível autenticar com o back-end de Serviços de Mídia por meio de código usando as chaves do Serviço de Controle de Acesso. Você deve atualizar seu código para usar o Azure AD (Azure Active Directory) de acordo com o artigo [Autenticação baseada no Azure AD](media-services-use-aad-auth-to-access-ams-api.md). Fique atento a avisos sobre essa alteração no Portal do Azure.

### <a name="updates-for-october-2017"></a>Atualizações de outubro de 2017
#### <a name="sdks"></a>SDKs
* O SDK do .NET foi atualizado para ser compatível com a autenticação do Azure AD. A compatibilidade com a autenticação de Serviço de Controle de Acesso foi removida do SDK mais recente do .NET no Nuget.org a fim de incentivar a migração mais rápida para o Azure AD. 
* O SDK do Java foi atualizado para ser compatível com a autenticação do Azure AD. A compatibilidade com a autenticação do Azure AD foi adicionada ao SDK do Java. Para obter informações sobre como usar o SDK de Java com os Serviços de Mídia, consulte [Introdução ao SDK de cliente Java para os Serviços de Mídia do Azure](media-services-java-how-to-use.md)

#### <a name="file-based-encoding"></a>Codificação baseada em arquivo
* Agora você pode usar o codificador Premium para codificar o conteúdo para o codec de vídeo de HEVC (codificação de vídeo de alta eficiência) H.265. A escolha do H.265 sobre outros codecs como o H.264 não afeta o preço. Para obter informações sobre licenças de patente HEVC, consulte [Termos de Serviços Online](https://azure.microsoft.com/support/legal/).
* Para vídeo de origem codificado com o codec de vídeo H.265(HEVC), como o vídeo capturado usando iOS11 ou GoPro Hero 6, você poderá usar o Codificador Premium ou o Codificador Standard para codificar esses vídeos. Para obter informações sobre licenças de patente, consulte [Termos de Serviços Online](https://azure.microsoft.com/support/legal/).
* Para conteúdo que contém várias faixas de áudio de idioma, os valores de idioma devem ser rotulados corretamente de acordo com a especificação de formato de arquivo correspondente (por exemplo, ISO MP4). Em seguida, você pode usar o Codificador Standard para codificar o conteúdo para streaming. O localizador de streaming resultante lista os idiomas de áudio disponíveis.
* Agora, o Codificador Standard é compatível com duas novas predefinições de sistema somente de áudio, "Áudio AAC" e "Áudio AAC de Boa Qualidade". Os dois produzem saída AAC (Codificação de Áudio Avançada) estéreo, com taxas de bits de 128 kbps e 192 kbps, respectivamente.
* O codificador Premium agora é compatível com formatos de arquivo QuickTime/MOV como entrada. O codec de vídeo deve ser um dos [tipos ProRes Apple listados neste artigo do GitHub](./media-services-media-encoder-standard-formats.md). O áudio deve ser AAC ou PCM (modulação por código de pulso). O Codificador Premium não é compatível, por exemplo, com vídeo DVC/DVCPro encapsulado em arquivos QuickTime/MOV como entrada. No entanto, o Codificador Standard é compatível com esses codecs de vídeo.
* As seguintes correções de bugs foram feitas nos codificadores:

    * Agora você pode enviar trabalhos usando um ativo de entrada. Depois de concluir essas tarefas, você pode modificar o ativo (por exemplo, adicionar, excluir ou renomear arquivos dentro do ativo) e enviar trabalhos adicionais.
    * A qualidade de miniaturas JPEG produzidas pelo Codificador Standard melhorou.
    * O Codificador Standard manipula melhor os metadados de entrada e a geração de miniaturas em vídeos de curtíssima duração.
    * Os aprimoramentos no decodificador de H.264 usado no Codificador Standard eliminam determinados artefatos raros. 

#### <a name="media-analytics"></a>Análise de Mídia
Disponibilidade geral do Azure Media Redactor: Esse processador de mídia executa anonimização de vídeo ao embaçar os rostos de indivíduos selecionados e é ideal para uso em cenários de mídia de notícias e em segurança pública. 

Para obter uma visão geral desse novo processador consulte [esta postagem no blog](https://azure.microsoft.com/blog/azure-media-redactor/). Para obter informações sobre a documentação detalhada e configurações, consulte [Edição facial com a Análise de Mídia do Azure](media-services-face-redaction.md).



## <a name="june-2017-release"></a>Versão de junho de 2017

Os Serviços de Mídia agora são compatíveis com a [autenticação baseada no Azure AD](media-services-use-aad-auth-to-access-ams-api.md).

> [!IMPORTANT]
> Atualmente, os Serviços de Mídia são compatíveis com o modelo de autenticação do Serviço de Controle de Acesso do Azure. A autorização do Serviço de Controle de Acesso será preterida em 1º de junho de 2018. Recomendamos que você migre para o modelo de autenticação do Azure AD assim que possível.

## <a name="march-2017-release"></a>Versão de março de 2017

Agora você pode usar o Codificador Standard para [gerar automaticamente uma escada de taxa de bits](media-services-autogen-bitrate-ladder-with-mes.md), especificando a cadeia de caracteres de predefinição "Streaming Adaptável" ao criar uma tarefa de codificação. Para codificar um vídeo para streaming com os Serviços de Mídia, use a predefinição "Streaming Adaptativo". Para personalizar uma codificação de predefinição para seu cenário específico, você poderá começar com [essas predefinições](media-services-mes-presets-overview.md).

Agora você pode usar o Media Encoder Standard ou o Media Encoder Premium Workflow para [criar uma tarefa de codificação que gera partes fMP4](media-services-generate-fmp4-chunks.md). 

## <a name="february-2017-release"></a>Versão de fevereiro de 2017

A partir de 1º de abril de 2017, qualquer registro de trabalho em sua conta com mais de 90 dias é excluído automaticamente, junto com os registros de suas tarefas associadas. A exclusão ocorre mesmo que o número total de registros esteja abaixo da cota máxima. Para arquivar as informações de tarefa/trabalho, você pode usar o código descrito em [gerenciar ativos e entidades relacionadas com o SDK do .NET dos Serviços de Mídia](media-services-dotnet-manage-entities.md).

## <a name="january-2017-release"></a>Versão de janeiro de 2017

Nos Serviços de Mídia, um ponto de extremidade de streaming representa um serviço de streaming que pode fornecer conteúdo diretamente a um aplicativo de player de cliente ou à CDN (rede de distribuição de conteúdo) para a distribuição posterior. Os Serviços de Mídia também fornecem integração perfeita da Rede de Distribuição de Conteúdo do Azure. O fluxo de saída do serviço StreamingEndpoint pode ser um fluxo ao vivo, um vídeo por demanda ou um download progressivo do seu ativo na conta dos Serviços de Mídia. Cada conta dos Serviços de Mídia inclui um ponto de extremidade de streaming padrão. Pontos de extremidade de streaming adicionais podem ser criados na conta. 

Há duas versões de pontos de extremidade de streaming, 1.0 e 2.0. Começando em 10 de janeiro de 2017, quaisquer contas de Serviços de Mídia recém-criadas incluem o ponto de extremidade de streaming padrão versão 2.0. Pontos de extremidade de streaming adicionais que você adicionar nessa conta também são da versão 2.0. Essa alteração não afeta as contas existentes. Pontos de extremidade de streaming existentes são da versão 1.0 e podem ser atualizados para a versão 2.0. Há alterações de recurso, cobrança e comportamento com essa alteração. Para saber mais, confira [Streaming endpoints overview](media-services-streaming-endpoints-overview.md) (Visão geral dos pontos de extremidade de streaming).

Começando com a versão 2.15, os Serviços de Mídia adicionaram as seguintes propriedades para a entidade de ponto de extremidade de streaming:

* CdnProvider 
* CdnProfile
* FreeTrialEndTime 
* StreamingEndpointVersion 

Para obter mais informações sobre essas propriedades, consulte [StreamingEndpoint](/rest/api/media/operations/streamingendpoint). 

## <a name="december-2016-release"></a>Versão de dezembro de 2016

 Você agora pode usar os Serviços de Mídia para acessar dados de telemetria/métricas de seus serviços. A versão atual dos Serviços de Mídia permite a coleta de dados de telemetria para entidades de canal ao vivo, arquivo e ponto de extremidade de streaming. Para obter mais informações, consulte [Telemetria dos Serviços de Mídia](media-services-telemetry-overview.md).

## <a name="july-2016-release"></a><a name="july_changes16"></a>Versão de julho de 2016
### <a name="updates-to-the-manifest-file-ism-generated-by-encoding-tasks"></a>Atualizações do arquivo de manifesto (*.ISM) gerado por tarefas de codificação
Quando uma tarefa de codificação for enviada para o Media Encoder Standard ou o Media Encoder Premium, a tarefa de codificação gerará um [arquivo de manifesto de streaming](media-services-deliver-content-overview.md) (*.ism) no ativo de saída. Com a versão mais recente do serviço, a sintaxe desse arquivo de manifesto de streaming foi atualizada.

> [!NOTE]
> A sintaxe do arquivo de manifesto de streaming (.ism) é reservada para uso interno. Isso está sujeito a alterações em versões futuras. Não modifique ou manipule o conteúdo do arquivo.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Um novo arquivo de manifesto de cliente (*.ISMC) é gerado no ativo de saída quando uma tarefa de codificação produz um ou mais arquivos em MP4
Começando pela versão mais recente do serviço, após a conclusão de uma tarefa de codificação que gera um ou mais arquivos em MP4, a saída do ativo também contém um arquivo de manifesto de streaming (*.ismc) do cliente. O arquivo .ismc ajuda a melhorar o desempenho do streaming dinâmico. 

> [!NOTE]
> A sintaxe do arquivo de manifesto do cliente (.ismc) é reservada para uso interno. Isso está sujeito a alterações em versões futuras. Não modifique ou manipule o conteúdo do arquivo.
> 
> 

Para saber mais, confira [este blog](/archive/blogs/randomnumber/encoder-changes-within-azure-media-services-now-create-ismc-file).

### <a name="known-issues"></a>Problemas conhecidos
Alguns clientes podem se deparar com um problema de marcas repetidas no manifesto do Smooth Streaming. Para saber mais, consulte [esta seção](media-services-deliver-content-overview.md#known-issues).

## <a name="april-2016-release"></a><a id="apr_changes16"></a>Versão de abril de 2016
### <a name="media-analytics"></a>Análise de Mídia
 Os Serviços de Mídia do Azure introduziram a Análise de Mídia para proporcionar uma inteligência de vídeo avançada. Para obter mais informações, consulte [Visão geral da Análise dos Serviços de Mídia](./legacy-components.md).

### <a name="apple-fairplay-preview"></a>Apple FairPlay (versão prévia)
Os Serviços de Mídia do Azure agora permitem criptografar dinamicamente seu conteúdo HLS (HTTP Live Streaming) com o Apple FairPlay. Ele também mostra como usar o serviço de entrega de licenças dos Serviços de Mídia para entregar licenças do FairPlay aos clientes. Para obter mais informações, confira o artigo "Usar os Serviços de Mídia do Azure para transmitir seu conteúdo de HLS protegido com o Apple FairPlay".

## <a name="february-2016-release"></a><a id="feb_changes16"></a>Versão de fevereiro de 2016
A versão mais recente do SDK dos Serviços de Mídia para .NET (3.5.3) contém uma correção de bug relacionada ao Google Widevine. Era impossível reutilizar a AssetDeliveryPolicy para vários ativos criptografados com o Widevine. Como parte dessa correção de bug, a propriedade a seguir foi adicionada ao SDK: WidevineBaseLicenseAcquisitionUrl.

```csharp
Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
    new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
{
    {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

};
```

## <a name="january-2016-release"></a><a id="jan_changes_16"></a>Versão de janeiro de 2016
Unidades reservadas para codificação foram renomeadas para diminuir a confusão com nomes de codificador.

As unidades reservadas para codificação Básica, Standard e Premium foram renomeadas para unidades reservadas S1, S2 e S3, respectivamente. Os clientes que usam unidades reservadas de codificação básicas hoje veem S1 como rótulo no Portal do Azure (e na fatura). Clientes que usam Standard e Premium veem os rótulos S2 e S3, respectivamente. 

## <a name="december-2015-release"></a><a id="dec_changes_15"></a>Versão de dezembro de 2015

### <a name="media-encoder-deprecation-announcement"></a>Comunicado de substituição do Codificador de Mídia

 O Codificador de Mídia do Azure será preterido daqui a aproximadamente 12 meses, a partir do lançamento do Media Encoder Standard.

### <a name="azure-sdk-for-php"></a>SDK do Azure para PHP
A equipe do SDK do Azure publicou uma nova versão do pacote [SDK do Azure para PHP](https://github.com/Azure/azure-sdk-for-php) que contém atualizações e novos recursos para os Serviços de Mídia. Em particular, o SDK dos Serviços de Mídia para PHP agora é compatível com os recursos de [proteção de conteúdo](media-services-content-protection-overview.md) mais recentes. Esses recursos são a criptografia dinâmica com AES e DRM (PlayReady e Widevine) com e sem restrições de token. Ele também é compatível com o dimensionamento de [unidades de codificação](media-services-dotnet-encoding-units.md).

Para obter mais informações, consulte:

* As [amostras de código](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) a seguir ajudam você a começar rapidamente:
  * **vodworkflow_aes.php**: Esse arquivo PHP mostra como usar a criptografia dinâmica AES-128 e o serviço de entrega de chaves. Ele se baseia o exemplo .NET explicado em [Usar a criptografia dinâmica AES-128 e o serviço de entrega de chaves](media-services-protect-with-aes128.md).
  * **vodworkflow_aes.php**: Esse arquivo PHP mostra como usar a criptografia dinâmica PlayReady e o serviço de entrega de licenças. Ele se baseia o exemplo .NET explicado em [Usar a criptografia dinâmica comum PlayReady e/ou Widevine](media-services-protect-with-playready-widevine.md).
  * **scale_encoding_units.php**: Esse arquivo PHP mostra como dimensionar unidades reservadas para codificação.

## <a name="november-2015-release"></a><a id="nov_changes_15"></a>Versão de novembro de 2015
 Os Serviços de Mídia agora oferecem o serviço de entrega de licença do Widevine na nuvem. Para saber mais, confira [este blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Veja também [este tutorial](media-services-protect-with-playready-widevine.md) e o [repositório do GitHub](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

Os serviços de entrega de licenças do Widevine fornecidos pelos Serviços de Mídia estão em versão prévia. Para saber mais, confira [este blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

## <a name="october-2015-release"></a><a id="oct_changes_15"></a>Versão de outubro de 2015
Os Serviços de Mídia agora estão ativos nos seguintes datacenters: Sul do Brasil, Oeste da Índia, Sul da Índia e Índia Central. Agora você pode usar o Portal do Azure para [criar contas dos Serviços de Mídia](media-services-portal-create-account.md) e realizar diversas tarefas descritas na [página da Web da documentação dos Serviços de Mídia](https://azure.microsoft.com/documentation/services/media-services/). A Codificação Ativa não está habilitada nesses data centers. Além disso, nem todos os tipos de unidades reservadas para codificação estão disponíveis nesses data centers.

* Sul do Brasil:                                          Somente as unidades reservadas para codificação Standard e Básica estão disponíveis.
* Oeste da Índia, Sul da Índia e Índia Central:             Somente as unidades reservadas para codificação Básica estão disponíveis.

## <a name="september-2015-release"></a><a id="september_changes_15"></a>Versão de setembro de 2015
Os Serviços de Mídia agora oferecem a capacidade de proteger tanto vídeo por demanda quanto fluxos ao vivo com tecnologia de DRM modular Widevine. Você pode usar os seguintes parceiros de serviços de entrega para ajudá-lo a fornecer licenças do Widevine:
* [Axinom](https://www.axinom.com) 
* [EZDRM](https://ezdrm.com/) 
* [castLabs](https://castlabs.com/company/partners/azure/) 

Para saber mais, confira [este blog](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).
  
Você pode usar o [SDK do .NET dos Serviços de Mídia](https://www.nuget.org/packages/windowsazure.mediaservices/) (começando da versão 3.5.1) ou a API REST para configurar seu AssetDeliveryConfiguration ao uso do Widevine. 
* Os Serviços de Mídia adicionaram compatibilidade com vídeos ProRes da Apple. Agora você pode carregar os arquivos de vídeos de origem do QuickTime que usam Apple ProRes ou outros codecs. Para saber mais, confira [este blog](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).
* Agora você pode usar um Media Encoder Standard para fazer sub-recortes e extração de arquivos dinâmicos. Para saber mais, confira [este blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).
* Foram feitas as seguintes atualizações de filtragem: 
  
  * Agora você pode usar o formato HLS da Apple com um filtro somente áudio. Essa atualização permite que você remova a faixa somente áudio especificando (audio-only=false) na URL.
  * Ao definir filtros para os ativos, agora você pode combinar vários filtros (até três) em uma única URL.
    
    Para saber mais, confira [este blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).
* Os Serviços de Mídia agora são compatíveis com o I-Frames no HLS v4. O suporte do I-frame otimiza as operações de avanço e retrocesso. Por padrão, todas as saídas do HLS versão 4 incluem a playlist do I-frame (EXT-X-I-FRAME-STREAM-INF).
Para saber mais, confira [este blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a name="august-2015-release"></a><a id="august_changes_15"></a>Versão de agosto de 2015
* O SDK do Serviços de Mídia para Java versão 0.8.0 e novos exemplos estão agora disponíveis. Para obter mais informações, consulte:
    
* A atualização do Player de Mídia do Azure compatível com fluxo de vários áudios. Para saber mais, confira [esta postagem no blog](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/).

## <a name="july-2015-release"></a><a id="july_changes_15"></a>Versão de julho de 2015
* A disponibilidade geral do Media Encoder Standard foi anunciada. Para saber mais, confira [esta postagem no blog](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).
  
    O Media Encoder Standard usa as predefinições descritas [nesta seção](./media-services-mes-presets-overview.md). Ao usar uma predefinição para codificações 4K, você obtém o tipo de unidade reservada Premium. Para obter mais informações, consulte [Codificação de escala](media-services-scale-media-processing-overview.md).
* Legendas em tempo real ao vivo foram usadas com os Serviços de Mídia e o Player de Mídia. Para saber mais, confira [esta postagem no blog](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/).

### <a name="media-services-net-sdk-updates"></a>Atualizações do SDK do .NET dos Serviços de Mídia
O SDK do .NET dos Serviços de Mídia agora está na versão 3.4.0.0. Foram feitas as seguintes atualizações: 

* Foi implementada compatibilidade com arquivo-morto dinâmico. Não é possível baixar um ativo que contenha um arquivo-morto dinâmico.
* Foi implementada compatibilidade com filtros dinâmicos.
* Uma funcionalidade foi implementada para que os usuários possam manter um contêiner de armazenamento enquanto excluem um ativo.
* Foram feitas correções de bugs relacionados a políticas de repetição nos canais.
* O Media Encoder Premium Workflow foi habilitado.

## <a name="june-2015-release"></a><a id="june_changes_15"></a>Versão de junho de 2015
### <a name="media-services-net-sdk-updates"></a>Atualizações do SDK do .NET dos Serviços de Mídia
O SDK do .NET dos Serviços de Mídia agora está na versão 3.3.0.0. Foram feitas as seguintes atualizações: 

* Foi adicionada compatibilidade com especificações de descoberta do OpenId Connect.
* Foi adicionada compatibilidade com manipulação de substituição de chaves no lado do provedor de identidade.

Se você usar um provedor de identidade que expõe um documento de descoberta OpenID Connect (assim como o Azure AD, Google e Salesforce fazem), poderá instruir os Serviços de Mídia a obter chaves de assinatura para a validação de JWTs (tokens Web JSON) da especificação de descoberta OpenID Connect. 

Para obter mais informações, consulte [Usar chaves Web JSON da especificação de descoberta OpenID Connect para trabalhar com autenticação JWT nos Serviços de Mídia](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).

## <a name="may-2015-release"></a><a id="may_changes_15"></a>Versão de maio de 2015
Os seguintes novos recursos foram anunciados:

* [Uma versão prévia da codificação ativa com os Serviços de Mídia](media-services-manage-live-encoder-enabled-channels.md)
* [Manifesto dinâmico](media-services-dynamic-manifest-overview.md)

## <a name="april-2015-release"></a><a id="april_changes_15"></a>Versão de abril de 2015
### <a name="general-media-services-updates"></a>Atualizações gerais dos Serviços de Mídia
* O [Player de Mídia](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/) foi anunciado.
* Começando com a REST 2.10 dos Serviços de Mídia, canais configurados para incluir um protocolo RTMP (Protocolo de Mensagens em Tempo Real) são criados com URLs de ingestão primária e secundária. Para obter mais informações, consulte [Configurações de ingestão de canal](media-services-live-streaming-with-onprem-encoders.md#channel_input).
* O Azure Media Indexer foi atualizado.
* Foi adicionado suporte ao idioma espanhol.
* Foi adicionada uma nova configuração para o formato XML.

Para saber mais, confira [este blog](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

### <a name="media-services-net-sdk-updates"></a>Atualizações do SDK do .NET dos Serviços de Mídia
O SDK do .NET dos Serviços de Mídia agora está na versão 3.2.0.0. Foram feitas as seguintes atualizações:

* Alterações da falha: TokenRestrictionTemplate.Issuer e TokenRestrictionTemplate.Audience foram alterados para ser de um tipo de cadeia de caracteres.
* Foram feitas atualizações relativas à criação personalizada de políticas de repetição.
* Foram feitas correções de bugs relativas a upload/download de arquivos.
* A classe MediaServicesCredentials agora aceita os pontos de extremidade de controle de acesso primários e secundários nos quais autenticar.

## <a name="march-2015-release"></a><a id="march_changes_15"></a>Versão de março de 2015
### <a name="general-media-services-updates"></a>Atualizações gerais dos Serviços de Mídia
* Os Serviços de Mídia agora fornecem integração da Rede de Distribuição de Conteúdo. Para dar suporte à integração, a propriedade CdnEnabled foi adicionada a StreamingEndpoint. CdnEnabled pode ser usado com APIs REST, começando com a versão 2.9. Para saber mais, confira [StreamingEndpoint](/rest/api/media/operations/streamingendpoint). CdnEnabled pode ser usado com o SDK do .NET, começando com a versão 3.1.0.2. Para saber mais, confira [StreamingEndpoint](/archive/blogs/randomnumber/encoder-changes-within-azure-media-services-now-create-ismc-file).
* O Media Encoder Premium Workflow foi anunciado. Para saber mais, confira [Apresentando a codificação Premium nos Serviços de Mídia do Azure](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).

## <a name="february-2015-release"></a><a id="february_changes_15"></a>Versão de fevereiro de 2015
### <a name="general-media-services-updates"></a>Atualizações gerais dos Serviços de Mídia
A API REST dos Serviços de Mídia agora está na versão 2.9. Começando com esta versão, você pode habilitar a integração da Rede de Distribuição de Conteúdo com pontos de extremidade de streaming. Para saber mais, confira [StreamingEndpoint](/rest/api/media/operations/streamingendpoint).

## <a name="january-2015-release"></a><a id="january_changes_15"></a>Versão de janeiro de 2015
### <a name="general-media-services-updates"></a>Atualizações gerais dos Serviços de Mídia
A disponibilidade geral da proteção de conteúdo com criptografia dinâmica foi anunciada. Para saber mais, confira [Os Serviços de Mídia aprimoram a segurança de streaming com a disponibilidade geral da tecnologia DRM](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

### <a name="media-services-net-sdk-updates"></a>Atualizações do SDK do .NET dos Serviços de Mídia
O SDK do .NET dos Serviços de Mídia agora está na versão 3.1.0.1.

Esta versão marcou o construtor Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestriction padrão como obsoleto. O novo construtor usa TokenType como um argumento.

```csharp
TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
```


## <a name="december-2014-release"></a><a id="december_changes_14"></a>Versão de dezembro de 2014
### <a name="general-media-services-updates"></a>Atualizações gerais dos Serviços de Mídia
* Algumas atualizações e novos recursos foram adicionados ao Media Indexer. Para saber mais, confira [Notas de versão do Azure Media Indexer 1.1.6.7](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
* Foi adicionada uma nova API REST, que você pode usar para atualizar unidades reservadas de codificação. Para obter mais informações, consulte [EncodingReservedUnitType com REST](/rest/api/media/operations/encodingreservedunittype).
* Foi adicionado suporte a CORS para o serviço de distribuição de chaves.
* Foram feitos aprimoramentos de desempenho nas opções das políticas de autorização de consulta.
* No data center da China, a [URL de entrega de chaves](/rest/api/media/operations/contentkey#get_delivery_service_url) agora é por cliente (assim como em outros data centers).
* A duração de destino automático HLS foi adicionada. Ao fazer streaming ao vivo, o HLS é sempre empacotado dinamicamente. Por padrão, os Serviços de Mídia calculam automaticamente a taxa de empacotamento de segmento HLS (FragmentsPerSegment) com base no intervalo de quadros-chave (KeyFrameInterval). Este método também é conhecido como um GOP (grupo de imagens) que é recebido do codificador dinâmico. Para obter mais informações, consulte [Trabalhar com Live Streaming dos Serviços de Mídia](/previous-versions/azure/dn783466(v=azure.100)).

### <a name="media-services-net-sdk-updates"></a>Atualizações do SDK do .NET dos Serviços de Mídia
O [SDK do .NET dos Serviços de Mídia](https://www.nuget.org/packages/windowsazure.mediaservices/) agora está na versão 3.1.0.0. Foram feitas as seguintes atualizações:

* A dependência do SDK do .NET foi atualizada para o .NET 4.5 Framework.
* Foi adicionada uma nova API que você pode usar para atualizar unidades reservadas de codificação. Para obter mais informações, consulte [Atualizar tipo de unidade reservada e aumentar unidades reservadas de codificação usando o .NET](media-services-dotnet-encoding-units.md).
* Foi adicionado suporte a JWT para autenticação de token. Para obter mais informações, consulte [Autenticação de token JWT nos Serviços de Mídia e criptografia dinâmica](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
* Foram adicionados deslocamentos relativos para BeginDate e ExpirationDate no modelo de licença PlayReady.

## <a name="november-2014-release"></a><a id="november_changes_14"></a>Versão de novembro de 2014
* Os Serviços de Mídia agora permitem que você faça a ingestão de um conteúdo ao vivo de Smooth Streaming (fMP4) em uma conexão TLS. Para ingerir por TLS, certifique-se de atualizar a URL de inserção para HTTPS. Atualmente, os Serviços de Mídia não são compatíveis com o TLS com domínios personalizados. Para saber mais sobre a transmissão ao vivo, confira [Trabalhar com a Transmissão ao Vivo dos Serviços de Mídia do Azure](/previous-versions/azure/dn783466(v=azure.100)).
* No momento, não é possível ingerir um fluxo ao vivo RTMP por uma conexão TLS.
* Só será possível transmitir por TLS se o ponto de extremidade de streaming do qual o conteúdo é distribuído tiver sido criado depois de 10 de setembro de 2014. Se suas URLs de streaming se baseiam nos pontos de extremidade de streaming criados após 10 de setembro de 2014, a URL contém "streaming.mediaservices.windows.net" (o novo formato). As URLs de streaming que contêm "origin.mediaservices.windows.net" (o formato antigo) não são compatíveis com TLS. Se sua URL está no formato antigo e você deseja transmitir por TLS, [crie um novo ponto de extremidade de streaming](media-services-portal-manage-streaming-endpoints.md). Use as URLs baseadas no novo ponto de extremidade de streaming para transmitir conteúdo por TLS.

### <a name="media-services-net-sdk"></a><a id="oct_sdk"></a>SDK do .NET dos Serviços de Mídia
O SDK dos Serviços de Mídia para extensões .NET agora está na versão 2.0.0.3.

O SDK dos Serviços de Mídia para .NET agora está na versão 3.0.0.8. Foram feitas as seguintes atualizações:

* A refatoração foi implementada em classes de política de novas tentativas.
* Uma sequência do agente do usuário foi adicionada aos cabeçalhos de solicitação HTTP.
* Uma etapa de build de restauração do NuGet foi adicionada.
* Testes de cenário foram corrigidos para usar um certificado X.509 do repositório.
* Configurações de validação foram adicionadas para a conclusão da atualização do canal e do streaming.

### <a name="new-github-repository-to-host-media-services-samples"></a>Novo repositório GitHub para hospedar exemplos de Serviços de Mídia
Os exemplos estão no [repositório GitHub de exemplos dos Serviços de Mídia](https://github.com/Azure/Azure-Media-Services-Samples).

## <a name="september-2014-release"></a><a id="september_changes_14"></a>Versão de setembro de 2014
Os metadados REST dos Serviços de Mídia agora estão na versão 2.7. Para obter mais informações sobre as últimas atualizações de REST, consulte a [Referência da API REST dos Serviços de Mídia](/rest/api/media/operations/azure-media-services-rest-api-reference).

O SDK dos Serviços de Mídia para .NET agora está na versão 3.0.0.7

### <a name="breaking-changes"></a><a id="sept_14_breaking_changes"></a>Alterações significativas
* A Origem foi renomeada para [StreamingEndpoint].
* Foi realizada uma alteração no comportamento padrão quando você usa o Portal do Azure para codificar e depois publicar arquivos MP4.

### <a name="new-featuresscenarios-that-are-part-of-the-general-availability-release"></a><a id="sept_14_GA_changes"></a>Novos recursos/cenários que fazem parte da versão de disponibilidade geral
* O processador de mídia do Media Indexer foi introduzido. Para obter mais informações, consulte [Indexar arquivos de mídia com o Media Indexer](/previous-versions/azure/dn783455(v=azure.100)).
* Você pode usar a entidade [StreamingEndpoint] para adicionar nomes (host) de domínio personalizados.
  
    Para usar um nome de domínio personalizado como nome do ponto de extremidade de streaming dos Serviços de Mídia, é preciso adicionar nomes de host personalizados ao ponto de extremidade de streaming. Use as APIs REST dos Serviços de Mídia ou o SDK do .NET para adicionar nomes de host personalizados.
  
    As seguintes considerações se aplicam:
  
  * É preciso ter propriedade do nome de domínio personalizado.
  * A propriedade do nome de domínio deve ser validada pelos Serviços de Mídia. Para validar o domínio, crie um CName que mapeia o domínio pai MediaServicesAccountId para verificar o DNS mediaservices-dns-zone.
  * Você deve criar outro CName que mapeia o nome de host personalizado (por exemplo, sports.contoso.com) em nome de host do seu StreamingEndpoint dos Serviços de Mídia (por exemplo, amstest.streaming.mediaservices.windows.net).

    Para obter mais informações, confira a propriedade CustomHostNames no artigo [StreamingEndpoint](/rest/api/media/operations/streamingendpoint).

### <a name="new-featuresscenarios-that-are-part-of-the-public-preview-release"></a><a id="sept_14_preview_changes"></a>Novos recursos/cenários que fazem parte da versão de preview pública
* Versão prévia de transmissão ao vivo. Para obter mais informações, consulte [Trabalhar com Live Streaming dos Serviços de Mídia](/previous-versions/azure/dn783466(v=azure.100)).
* Serviço de entrega de chave. Para obter mais informações, consulte [Usar o serviço de entrega de chave e a criptografia dinâmica AES-128](/previous-versions/azure/dn783457(v=azure.100)).
* Criptografia dinâmica do EAS. Para obter mais informações, consulte [Usar o serviço de entrega de chave e a criptografia dinâmica AES-128](/previous-versions/azure/dn783457(v=azure.100)).
* Serviço de entrega de licença do PlayReady. 
* Criptografia dinâmica do PlayReady. 
* Modelo de licença do PlayReady dos Serviços de Mídia. Para obter mais informações, consulte [Visão geral do modelo de licença do PlayReady dos Serviços de Mídia].
* Transmitir ativos criptografados no armazenamento. Para obter mais informações, consulte [Transmitir conteúdo criptografado no armazenamento](/previous-versions/azure/dn783451(v=azure.100)).

## <a name="august-2014-release"></a><a id="august_changes_14"></a>Versão de agosto de 2014
Ao codificar um ativo, um ativo de saída é produzido quando o trabalho de codificação é concluído. Até esta versão, o Codificador de Serviços de Mídia produzia metadados sobre os ativos de saída. Desta versão em diante, o codificador também produz metadados sobre ativos de entrada. Para obter mais informações, confira [Metadados de entrada] e [Metadados de saída].

## <a name="july-2014-release"></a><a id="july_changes_14"></a>Versão de julho de 2014
As seguintes correções de erro foram feitas ao Empacotador e Criptografador dos Serviços de Mídia do Azure:

* Quando um ativo de arquivo morto dinâmico é transmitido para HLS, apenas o áudio é reproduzido novamente: Esse problema foi corrigido e agora é possível reproduzir áudio e vídeo.
* Quando um ativo é empacotado para criptografia de envelope HLS e AES de 128 bits, os fluxos empacotados não são reproduzidos em dispositivos Android: Esse bug foi corrigido e o fluxo empacotado é reproduzido em dispositivos Android compatíveis com HLS.

## <a name="may-2014-release"></a><a id="may_changes_14"></a>Versão de maio de 2014
### <a name="general-media-services-updates"></a><a id="may_14_changes"></a>Atualizações gerais dos Serviços de Mídia
Agora é possível usar o [empacotamento dinâmico] para transmitir HLS versão 3. Para transmitir HLS versão 3, adicione o seguinte formato para o caminho do localizador de origem: * .ism/manifest(format=m3u8-aapl-v3). Para obter mais informações, confira [este fórum](https://social.msdn.microsoft.com/Forums/en-US/13b8a776-9519-4145-b9ed-d2b632861fde/dynamic-packaging-to-hls-v3).

O empacotamento dinâmico agora também é compatível com a entrega de HLS (versões 3 e 4) criptografado com PlayReady com base em Smooth Streaming estaticamente criptografado com PlayReady. Para obter informações sobre como criptografar Smooth Streaming com PlayReady, consulte [Proteger Smooth Streaming com PlayReady](/previous-versions/azure/dn189154(v=azure.100)).

### <a name="media-services-net-sdk-updates"></a><a name="may_14_donnet_changes"></a>Atualizações do SDK do .NET dos Serviços de Mídia
O SDK do .NET dos Serviços de Mídia agora está na versão 3.0.0.5. Foram feitas as seguintes atualizações:

* Velocidade e resiliência são melhores quando você carregar e baixar ativos de mídia.
* Foram feitas melhorias na lógica de repetição e manipulação de exceção temporária: 
  
  * Detecção de erro temporário e lógica de repetição foram aprimorados para as exceções causadas ao consultar, salvar alterações, carregar ou baixar arquivos. 
  * Quando você obtém exceções da Web (por exemplo, durante uma solicitação de token do Serviço de Controle de Acesso), erros fatais falham mais rápido agora.

Para obter mais informações, consulte [Lógica de repetição no SDK de Serviços de Mídia para .NET].

## <a name="januaryfebruary-2014-releases"></a><a id="jan_feb_changes_14"></a>Versões de janeiro/fevereiro de 2014
### <a name="media-services-net-sdk-3001-3002-and-3003"></a><a name="jan_fab_14_donnet_changes"></a>SDK do .NET dos Serviços de Mídia 3.0.0.1, 3.0.0.2 e 3.0.0.3
As alterações em 3.0.0.1 e 3.0.0.2 incluem:

* Foram corrigidos problemas relacionados ao uso de consultas LINQ com declarações OrderBy.
* As soluções de teste em [GitHub] foram divididas em testes baseados em unidade e testes baseados em cenário.

Para saber mais sobre as alterações, confira: [Versões 3.0.0.1 e 3.0.0.2 do SDK do .NET dos Serviços de Mídia](http://gtrifonov.com/2014/02/07/windows-azure-media-services-net-sdk-3-0-0-2-release/index.html).

As seguintes alterações foram feitas na versão 3.0.0.3:

* As dependências de armazenamento do Azure foram atualizadas para usar a versão 3.0.3.0.
* Um problema de compatibilidade reversa foi corrigido para versões 3.0 *.* .

## <a name="december-2013-release"></a><a id="december_changes_13"></a>Versão de dezembro de 2013
### <a name="media-services-net-sdk-3000"></a><a name="dec_13_donnet_changes"></a>SDK do .NET dos Serviços de Mídia 3.0.0.0
> [!NOTE]
> As versões 3.0.x.x não são compatíveis com versões 2.4.x.x.
> 
> 

A versão mais recente do SKD dos Serviços de Mídia agora é a 3.0.0.0. É possível baixar o pacote mais recente do NuGet ou obter os bits do [GitHub].

Começando pelo SDK de Serviços de Mídia versão 3.0.0.0, é possível reutilizar os tokens do [Serviço de Controle de Acesso do Azure AD](/previous-versions/azure/azure-services/hh147631(v=azure.100)). Para obter mais informações, confira a seção “Reutilizar tokens do Serviço de Controle de Acesso” em [Conectar-se à conta dos Serviços de Mídia usando o SDK dos Serviços de Mídia para .NET](/previous-versions/azure/jj129571(v=azure.100)).

### <a name="media-services-net-sdk-extensions-2000"></a><a name="dec_13_donnet_ext_changes"></a>Extensões 2.0.0.0 do SDK do .NET dos Serviços de Mídia
 As Extensões do SDK do .NET dos Serviços de Mídia do Azure são um conjunto de métodos de extensão e funções auxiliares que simplificam seu código e facilitam o desenvolvimento com os Serviços de Mídia. Você pode obter os bits mais recentes das [Extensões do SDK do .NET dos Serviços de Mídia](https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev).

## <a name="november-2013-release"></a><a id="november_changes_13"></a>Versão de novembro de 2013
### <a name="media-services-net-sdk-changes"></a><a name="nov_13_donnet_changes"></a>Alterações do SDK do .NET dos Serviços de Mídia
Começando desta versão, o SDK para .NET dos Serviços de Mídia manipula erros de falhas temporários que podem ocorrer ao fazer chamadas à camada da API REST dos Serviços de Mídia.

## <a name="august-2013-release"></a><a id="august_changes_13"></a>Versão de agosto de 2013
### <a name="media-services-powershell-cmdlets-included-in-azure-sdk-tools"></a><a name="aug_13_powershell_changes"></a>Cmdlets do PowerShell dos Serviços de Mídia incluídos nas Azure SDK tools
Os seguintes cmdlets do PowerShell dos Serviços de Mídia agora estão incluídos nas [Azure SDK tools](https://github.com/Azure/azure-sdk-tools):

* Get-AzureMediaServices 

    Por exemplo: `Get-AzureMediaServicesAccount`
* New-AzureMediaServicesAccount 
  
    Por exemplo: `New-AzureMediaServicesAccount -Name "MediaAccountName" -Location "Region" -StorageAccountName "StorageAccountName"`
* New-AzureMediaServicesKey 
  
    Por exemplo: `New-AzureMediaServicesKey -Name "MediaAccountName" -KeyType Secondary -Force`
* Remove-AzureMediaServicesAccount 
  
    Por exemplo: `Remove-AzureMediaServicesAccount -Name "MediaAccountName" -Force`

## <a name="june-2013-release"></a><a id="june_changes_13"></a>Versão de junho de 2013
### <a name="media-services-changes"></a><a name="june_13_general_changes"></a>Alterações dos Serviços de Mídia
As alterações mencionadas a seguir nesta seção são atualizações incluídas nas versões de Serviços de Mídia de junho de 2013:

* Habilidade de vincular várias contas de armazenamento a uma conta de Serviço de Mídia. 
    * StorageAccount
    * Asset.StorageAccountName e Asset.StorageAccount
* Habilidade de atualizar Job.Priority. 
* Entidades e propriedades relacionadas à notificação: 
    * JobNotificationSubscription
    * NotificationEndPoint
    * Trabalho
* Asset.Uri 
* Locator.Name 

### <a name="media-services-net-sdk-changes"></a><a name="june_13_dotnet_changes"></a>Alterações do SDK do .NET dos Serviços de Mídia
As alterações a seguir foram incluídas em versões do SDK dos Serviços de Mídia de junho de 2013. O SDK dos Serviços de Mídia mais recente está disponível em GitHub.

* Começando pela versão 2.3.0.0, o SDK dos Serviços de Mídia é compatível com a vinculação de várias contas de armazenamento a uma conta de Serviços de Mídia. As seguintes APIs têm suporte a esse recurso:
  
    * Tipo IStorageAccount
    * Propriedade Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts
    * Propriedade StorageAccount
    * Propriedade StorageAccountName
  
      Para obter mais informações, consulte [Gerenciar ativos de Serviços de Mídia entre várias contas de armazenamento](/previous-versions/azure/dn271889(v=azure.100)).
* APIs relacionadas à notificação. Começando pela versão 2.2.0.0, é possível escutar as notificações de armazenamento de Fila do Azure. Para obter mais informações, consulte [Manipular notificações de trabalho dos Serviços de Mídia](/previous-versions/azure/dn261241(v=azure.100)).
  
    * Propriedade Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions
    * Tipo Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint
    * Tipo Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription
    * Tipo Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection
    * Tipo Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType
* Dependência do SDK do cliente de Armazenamento 2.0 (Microsoft.WindowsAzure.StorageClient.dll)
* Dependência de OData 5.5 (Microsoft.Data.OData.dll)

## <a name="december-2012-release"></a><a id="december_changes_12"></a>Versão de dezembro de 2012
### <a name="media-services-net-sdk-changes"></a><a name="dec_12_dotnet_changes"></a>Alterações do SDK do .NET dos Serviços de Mídia
* IntelliSense: A documentação do IntelliSense que faltava para vários tipos foi adicionada.
* Microsoft.Practices.TransientFaultHandling.Core: Foi corrigido um problema em que o SDK ainda tinha uma dependência a uma versão mais antiga desse assembly. O SDK agora faz referência à versão 5.1.1209.1 desse conjunto.

Correções para problemas encontrados no SDK de novembro de 2012:

* IAsset.Locators.Count: Essa contagem agora é relatada corretamente em novas interfaces IAsset depois que todos os localizadores são excluídos.
* IAssetFile.ContentFileSize: Esse valor agora é definido adequadamente após um upload por IAssetFile.Upload(filepath).
* IAssetFile.ContentFileSize: Essa propriedade agora pode ser definida durante a criação de um arquivo de ativo. Ela antes era somente leitura.
* IAssetFile.Upload(filepath): Corrigido um problema em que esse método de upload assíncrono lançava o erro abaixo ao carregar vários arquivos para o ativo. O erro era “O servidor falhou em autenticar a solicitação. Certifique-se de que o valor do cabeçalho Autorização seja formado corretamente, incluindo a assinatura.”
* IAssetFile.UploadAsync: Corrigido um problema que limitava o upload simultâneo de arquivos a cinco arquivos.
* IAssetFile.UploadProgressChanged: Esse evento agora é fornecido pelo SDK.
* IAssetFile.DownloadAsync(string, BlobTransferClient, ILocator, CancellationToken): Essa sobrecarga de método agora é fornecida.
* IAssetFile.DownloadAsync: Corrigido um problema que limitava o download simultâneo de arquivos a cinco arquivos.
* IAssetFile.Delete(): Corrigido um problema em que chamar delete poderia gerar uma exceção caso nenhum arquivo fosse carregado para o IAssetFile.
* Trabalhos: Corrigido um problema em que encadear uma “Tarefa de MP4 para Smooth Streams” com uma “Tarefa de proteção do PlayReady” usando um modelo de trabalho não criava nenhuma tarefa.
* EncryptionUtils.GetCertificateFromStore(): Esse método não lança mais uma exceção de referência nula devido a falhas em encontrar o certificado com base em problemas de configuração de certificado.

## <a name="november-2012-release"></a><a id="november_changes_12"></a>Versão de novembro de 2012
As alterações mencionadas nesta seção eram atualizações incluídas no SDK de novembro de 2012 (versão 2.0.0.0). Essas alterações podem exigir que qualquer código escrito para a versão prévia do SDK de junho de 2012 seja modificado ou reescrito.

* Ativos
  
    * IAsset.Create(assetName) é a *única* função de criação de ativo. IAsset.Create não carrega mais arquivos como parte do método de chamada. Use IAssetFile para carregar.
    * O método IAsset.Publish e o valor de enumeração AssetState.Publish foram removidos do SDK de Serviços. Qualquer código que conte com esse valor deve ser reescrito.
* FileInfo
  
    * Essa classe foi removida e substituída por IAssetFile.
  
* IAssetFiles
  
    * IAssetFile substitui FileInfo e tem um comportamento diferente. Para usá-la, instancie o objeto IAssetFiles, seguido por um upload de arquivo usando o SDK dos Serviços de Mídia ou o SDK de Armazenamento. As seguintes sobrecargas IAssetFile.Upload podem ser usadas:
  
        * IAssetFile.Upload(filePath): Esse método síncrono bloqueia o thread e é recomendado somente ao carregar um único arquivo.
        * IAssetFile.UploadAsync(filePath, blobTransferClient, locator, cancellationToken): Este método assíncrono é o mecanismo de upload de preferência. 
    
            Bug conhecido: Se você usar o token de cancelamento, o upload será cancelado. As tarefas podem ter muitos estados de cancelamento. É preciso capturar e tratar as exceções adequadamente.
* Localizadores
  
    * As versões específicas da origem foram removidas. O context.Locators.CreateSasLocator (asset, accessPolicy) específico do SAS será marcado como preterido, ou removido pela disponibilidade geral. Consulte a seção Localizadores em Nova Funcionalidade para comportamento atualizado.

## <a name="june-2012-preview-release"></a><a id="june_changes_12"></a>Versão prévia de junho de 2012
A funcionalidade a seguir era nova na versão de novembro do SDK:

* Excluindo entidades
  
    * Os objetos IAsset, IAssetFile, ILocator, IAccessPolicy e IContentKey agora são excluídos no nível do objeto, ou seja, IObject.Delete(), em vez de exigir uma exclusão na Coleção, ou seja, cloudMediaContext.ObjCollection.Delete(objInstance).
* Localizadores
  
    * Os localizadores agora devem ser criados usando o método CreateLocator. Eles devem usar os valores enum LocatorType.SAS ou LocatorType.OnDemandOrigin como argumento para o tipo específico de localizador que você deseja criar.
    * Novas propriedades foram adicionadas a localizadores para tornar mais fácil obter URIs usáveis para seu conteúdo. Essa remodelagem de Localizadores fornece mais flexibilidade para capacidade de extensão de terceiros e aumenta a facilidade de uso para aplicativos cliente de mídia.
* Suporte ao método assíncrono
  
    * O suporte assíncrono foi adicionado a todos os métodos.

## <a name="additional-notes"></a>Observações adicionais

* O Widevine é um serviço fornecido pela Google Inc. e está sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Microsoft Q&A question page for Azure Media Services]: /answers/topics/azure-media-services.html
[Referência da API REST dos Serviços de Mídia do Azure]: /rest/api/media/operations/azure-media-services-rest-api-reference
[Media Services pricing details]: https://azure.microsoft.com/pricing/details/media-services/
[Metadados de entrada]: ./media-services-input-metadata-schema.md
[Metadados de saída]: ./media-services-output-metadata-schema.md
[Deliver content]: /previous-versions/azure/hh973618(v=azure.100)
[Index media files with the Azure Media Indexer]: /previous-versions/azure/dn783455(v=azure.100)
[StreamingEndpoint]: /rest/api/media/operations/streamingendpoint
[Work with Media Services live streaming]: /previous-versions/azure/dn783466(v=azure.100)
[Use AES-128 dynamic encryption and the key delivery service]: /previous-versions/azure/dn783457(v=azure.100)
[Use PlayReady dynamic encryption and the license delivery service]: /previous-versions/azure/dn783467(v=azure.100)
[Preview features]: https://azure.microsoft.com/services/preview/
[Visão geral do modelo de licença do PlayReady dos Serviços de Mídia]: /previous-versions/azure/dn783459(v=azure.100)
[Stream storage-encrypted content]: /previous-versions/azure/dn783451(v=azure.100)
[Azure portal]: https://portal.azure.com
[Empacotamento dinâmico]: /previous-versions/azure/jj889436(v=azure.100)
[Nick Drouin's blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protect Smooth Streaming with PlayReady]: /previous-versions/azure/dn189154(v=azure.100)
[Lógica de repetição no SDK de Serviços de Mídia para .NET]: ./media-services-retry-logic-in-dotnet-sdk.md
[Grass Valley announces EDIUS 7 streaming through the cloud]: https://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Control Media Services Encoder output file names]: /previous-versions/azure/dn303341(v=azure.100)
[Create overlays]: /previous-versions/azure/dn640496(v=azure.100)
[Stitch video segments]: /previous-versions/azure/dn640504(v=azure.100)
[Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure AD Access Control Service]: /previous-versions/azure/azure-services/hh147631(v=azure.100)
[Connect to Media Services with the Media Services SDK for .NET]: /previous-versions/azure/jj129571(v=azure.100)
[Media Services .NET SDK extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[Azure SDK tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Manage Media Services assets across multiple Storage accounts]: /previous-versions/azure/dn271889(v=azure.100)
[Handle Media Services job notifications]: /previous-versions/azure/dn261241(v=azure.100)