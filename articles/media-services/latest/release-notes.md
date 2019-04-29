---
title: Notas de versão dos Serviços de Mídia do Azure v3 | Microsoft Docs
description: Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece as atualizações mais recentes sobre os Serviços de Mídia do Azure v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 04/21/2019
ms.author: juliako
ms.openlocfilehash: 09f9731f78093354f324f263899519bc5d31fa5d
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63760770"
---
# <a name="azure-media-services-v3-release-notes"></a>Notas de versão dos Serviços de Mídia do Azure v3

Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

* As versões mais recentes
* Problemas conhecidos
* Correções de bug
* Funcionalidades preteridas

## <a name="known-issues"></a>Problemas conhecidos

> [!NOTE]
> Atualmente, você não pode usar o portal do Azure para gerenciar recursos da v3. Use a [API REST](https://aka.ms/ams-v3-rest-sdk), CLI ou um dos SDKs suportados.

Para obter mais informações, consulte [Guia de migração para migrar do Serviços de Mídia v2 para v3](migrate-from-v2-to-v3.md#known-issues).

## <a name="april-2019"></a>Abril de 2019

[FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset) foi adicionado para as predefinições do analisador interno.

## <a name="march-2019"></a>Março de 2019

O empacotamento dinâmico agora dá suporte a Atmos Dolby. Para obter mais informações, consulte [codecs de áudio com suporte pelo empacotamento dinâmico](dynamic-packaging-overview.md#audio-codecs-supported-by-dynamic-packaging).

Agora você pode especificar uma lista de filtros de ativo ou conta, que se aplica a localizador de Streaming. Para obter mais informações, consulte [associar filtros de localizador de Streaming](filters-concept.md#associate-filters-with-streaming-locator).

## <a name="february-2019"></a>fevereiro de 2019

Agora há suporte para serviços de mídia v3 nuvens nacionais do Azure. Nem todos os recursos estão disponíveis em todas as nuvens. Para obter mais detalhes, confira [Nuvens e regiões em que os Serviços de Mídia do Azure v3 existem](azure-clouds-regions.md).

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

O módulo de CLI 2.0 agora está disponível para [serviços de mídia do Azure v3 GA](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest) – v 2.0.50.

### <a name="new-commands"></a>Novos comandos

- [conta do ams AZ](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest)
- [filtro de conta do ams AZ](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)
- [ativo do ams AZ](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest)
- [ativos de ams AZ-filtro](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)
- [AZ ams chave de política de conteúdo](https://docs.microsoft.com/cli/azure/ams/content-key-policy?view=azure-cli-latest)
- [trabalho de ams AZ](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
- [AZ ams-evento ao vivo](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)
- [ams AZ saída ao vivo](https://docs.microsoft.com/cli/azure/ams/live-output?view=azure-cli-latest)
- [AZ ams streaming-ponto de extremidade](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest)
- [AZ ams-localizador de streaming](https://docs.microsoft.com/cli/azure/ams/streaming-locator?view=azure-cli-latest)
- [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) – permite que você gerencie Unidades Reservadas para Mídia. Para obter mais informações, confira [Dimensionar Unidades Reservadas para Mídia](media-reserved-units-cli-how-to.md).

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

#### <a name="azure-active-directory-authentication-and-rbac"></a>Autenticação do Azure Active Directory e RBAC

A Autenticação do Azure AD e o RBAC (Controle de Acesso Baseado em Função) permitem Transformações seguras, LiveEvents, Políticas de Chave de Conteúdo, Ativos por Função ou Usuários no Azure AD.

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

A versão de GA do Video Indexer foi anunciada em agosto. Para obter novas informações sobre recursos atualmente com suporte, consulte [O que é Video Indexer](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json). 

### <a name="plans-for-changes"></a>Planos de alterações

#### <a name="azure-cli-20"></a>CLI do Azure 2.0
 
O módulo da CLI 2.0 do Azure que inclui operações em todos os recursos (incluindo Live, Políticas de Chave de Conteúdo, Filtros de Conta/Ativo, Políticas de Streaming) estará disponível em breve. 

### <a name="known-issues"></a>Problemas conhecidos

Somente os clientes que usaram a API de versão prévia para material ativo ou AccountFilters são afetados pelo problema a seguir.

Se você criou Ativos ou Filtros de Conta entre 09/28 e 10/12 com CLI ou APIs dos Serviços de Mídia v3, será necessário remover todos os Ativos e AccountFilters e recriá-los devido a um conflito de versões. 

## <a name="may-2018---preview"></a>Maio de 2018 - Versão prévia

### <a name="net-sdk"></a>SDK .NET

Os recursos a seguir estão presentes no SDK do .NET:

* **Transformações** e **Trabalhos** para codificar ou analisar o conteúdo de mídia. Para obter exemplos, consulte [Transmitir arquivos por stream](stream-files-tutorial-with-api.md) e [Analisar](analyze-videos-tutorial-with-api.md).
* **Localizadores de streaming** para publicar e transmitir conteúdo aos dispositivos do usuário final
* **Políticas de streaming** e **Políticas de chave de conteúdo** para configurar a distribuição de chaves e o DRM (gerenciamento de direitos digitais) durante a entrega de conteúdo.
* **Eventos ao vivo** e **Saídas ao vivo** para configurar a ingestão e o arquivamento de conteúdo de transmissão ao vivo.
* **Ativos** para armazenar e publicar o conteúdo de mídia no Armazenamento do Azure. 
* **Pontos de extremidade de streaming** para configurar e dimensionar o empacotamento dinâmico, a criptografia e o streaming de conteúdo de mídia ao vivo e sob demanda.

### <a name="known-issues"></a>Problemas conhecidos

* Ao enviar um trabalho, você pode especificar para ingerir o vídeo de origem usando URLs HTTPS, URLs SAS ou caminhos para arquivos localizados no Armazenamento de Blobs do Azure. Atualmente, o AMS v3 não oferece suporte à codificação de transferência em partes sobre URLs HTTPS.

## <a name="provide-feedback"></a>Fornecer comentários

Confira o artigo [comunidade dos Serviços de Mídia do Azure](media-services-community.md) para ver diferentes maneiras de fazer perguntas, comentários e obter atualizações sobre os serviços de mídia.

## <a name="next-steps"></a>Próximos passos

[Visão geral](media-services-overview.md)
