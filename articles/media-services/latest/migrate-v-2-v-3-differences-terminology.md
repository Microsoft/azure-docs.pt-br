---
title: Terminologia dos serviços de mídia v3 e alterações de entidade
description: Este artigo descreve as diferenças de terminologia entre os serviços de mídia do Azure v2 a v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 5c1fbaf9be4cb128f0e4390a8c97c6f0b9330ce2
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559869"
---
# <a name="terminology-and-entity-changes-between-media-services-v2-and-v3"></a>Mudanças de terminologia e de entidade entre os serviços de mídia V2 e v3

![logotipo do guia de migração](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![etapas de migração 2](./media/migration-guide/steps-2.svg)

Este artigo descreve as diferenças de terminologia entre os serviços de mídia do Azure v2 a v3.

## <a name="naming-conventions"></a>Convenções de nomenclatura

Examine as convenções de nomenclatura que são aplicadas aos recursos dos serviços de mídia v3. Examine também os [blobs de nomenclatura](assets-concept.md#naming)

## <a name="terminology-changes"></a>Alterações de terminologia

- Um *localizador* agora é chamado de *localizador de streaming*.
- Um *canal* agora é chamado de *evento ao vivo*.
- Um *programa* agora é chamado de *saída dinâmica*.
- Uma *tarefa* agora é chamada de *JobOutput*, que faz parte de um trabalho.

## <a name="entity-changes"></a>Alterações de entidade
| **Entidade v2**<!-- row --> | **Entidade v3** | **Diretrizes** | **Acessível para v3** | **Atualizado por v3** |
|--|--|--|--|--|
| `AccessPolicy`<!-- row --> | <!-- empty --> |  A entidade `AccessPolicies` não existe na v3. | Não | Não |
| `Asset`<!-- row --> | `Asset` | <!-- empty --> | Sim | Sim |
| `AssetDeliveryPolicy`<!-- row --> | `StreamingPolicy` | <!-- empty --> | Sim | Não |
| `AssetFile`<!-- row --> | <!-- empty --> |A entidade `AssetFiles` não existe na v3. Embora arquivos (BLOBs de armazenamento) que você carregar ainda sejam considerados arquivos.<br/><br/> Use as APIs de armazenamento do Azure para enumerar os BLOBs em um contêiner em vez disso. Há duas maneiras de aplicar uma transformação aos arquivos com um trabalho:<br/><br/>Arquivos já carregados no armazenamento: o URI inclui a ID do ativo para que os trabalhos sejam feitos nos ativos em uma conta de armazenamento.<br/><br/>Arquivos a serem carregados durante o processo de transformação e trabalho: o ativo é criado no armazenamento, uma URL SAS é retornada, os arquivos são carregados no armazenamento e, em seguida, a transformação é aplicada aos arquivos. | Não | Não |
| `Channel`<!-- row --> | `LiveEvent` | Eventos ao vivo substituem canais da API v2. Eles carregam a maioria dos recursos e têm mais novos recursos, como transcrições ao vivo, modo de espera e suporte para ingestão de RTMPS. <br/><br/>Consulte [evento ao vivo no cenário com base na transmissão ao vivo](migrate-v-2-v-3-migration-scenario-based-live-streaming.md) | Não | Não |
| `ContentKey`<!-- row --> | <!-- empty --> | `ContentKeys` Não é mais uma entidade, agora é uma propriedade de um localizador de streaming.<br/><br/>  No v3, os dados de chave de conteúdo são associados ao `StreamingLocator` (para a criptografia de saída) ou ao próprio ativo (para criptografia de armazenamento do lado do cliente). | Sim | Não |
| `ContentKeyAuthorizationPolicy`<!-- row --> | `ContentKeyPolicy` | <!-- empty --> | Sim | Não |
| `ContentKeyAuthorizationPolicyOption` <!-- row --> | <!-- empty --> |  `ContentKeyPolicyOptions` estão incluídos no `ContentKeyPolicy` . | Sim | Não |
| `IngestManifest`<!-- row --> | <!-- empty --> | A entidade `IngestManifests` não existe na v3. O carregamento de arquivos na v3 envolve a API de armazenamento do Azure. Os ativos são criados primeiro e, em seguida, os arquivos são carregados no contêiner de armazenamento associado. Há várias maneiras de obter dados em um contêiner de armazenamento do Azure que pode ser usado em vez disso. `JobInputHttp` também fornece uma maneira de baixar uma entrada de trabalho de uma determinada URL, se desejado. | Não | Não |
| `IngestManifestAsset`<!-- row --> | <!-- empty --> | Há várias maneiras de obter dados em um contêiner de armazenamento do Azure que pode ser usado em vez disso. `JobInputHttp` também fornece uma maneira de baixar uma entrada de trabalho de uma determinada URL, se desejado. | Não | Não |
| `IngestManifestFile`<!-- row --> | <!-- empty --> | Há várias maneiras de obter dados em um contêiner de armazenamento do Azure que pode ser usado em vez disso. `JobInputHttp` também fornece uma maneira de baixar uma entrada de trabalho de uma determinada URL, se desejado. | Não | Não |
| `Job`<!-- row --> | `Job` | Crie um `Transform` antes de criar um `Job` . | Não | Não |
| `JobTemplate`<!-- row --> | `Transform` | Use um `Transform` em vez disso. Uma transformação é uma entidade separada de um trabalho e é reutilizável. | Não | Não |
| `Locator`<!-- row --> | `StreamingLocator` | <!--empty --> | Sim | Não |
| `MediaProcessor`<!-- row --> | <!-- empty --> | Em vez de procurar o `MediaProcessor` para usar por nome, use a predefinição desejada ao definir uma transformação. A predefinição usada determinará o processador de mídia usado pelo sistema de trabalho. Consulte Tópicos de codificação em [codificação baseada em cenário](migrate-v-2-v-3-migration-scenario-based-encoding.md). <!--Probably needs a link to its own article so customers know Indexerv1 maps to AudioAnalyzerPreset in basic mode, etc.--> | No | NA (ReadOnly na v2) |
| `NotificationEndPoint`<!-- row --> | <!--empty --> | As notificações no v3 são manipuladas por meio da grade de eventos do Azure. O `NotificationEndpoint` é substituído pelo registro de assinatura da grade de eventos que também encapsula a configuração dos tipos de notificações a receber (que, em v2, foi tratado pelo `JobNotificationSubscription` do trabalho, da `TaskNotificationSubscription` tarefa e da telemetria `ComponentMonitoringSetting` ). A telemetria v2 foi dividida entre a grade de eventos do Azure e Azure Monitor para se ajustar aos aprimoramentos do ecossistema maior do Azure. | Não | Não |
| `Program`<!-- row --> | `LiveOutput` | As saídas ao vivo agora substituem os programas na API v3.  | Não | Não |
| `StreamingEndpoint`<!-- row --> | `StreamingEndpoint` | Os pontos de extremidade de streaming permanecem principalmente os mesmos. Eles são usados para empacotamento dinâmico, criptografia e entrega de conteúdo de HLS e DASH para streaming ao vivo e sob demanda, seja direto da origem ou através da CDN. Os novos recursos incluem suporte para melhor integração e criação de gráficos de Azure Monitor. |  Sim | Sim |
| `Task`<!-- row --> | `JobOutput` | Substituído por `JobOutput` (que não é mais uma entidade separada na API).  Consulte Tópicos de codificação em [codificação baseada em cenário](migrate-v-2-v-3-migration-scenario-based-encoding.md). | Não | Não |
| `TaskTemplate`<!-- row --> | `TransformOutput` | Substituído por `TransformOutput` (que não é mais uma entidade separada na API). Consulte Tópicos de codificação em [codificação baseada em cenário](migrate-v-2-v-3-migration-scenario-based-encoding.md). | Não | Não |
| `Inputs`<!-- row --> | `Inputs` | As entradas e saídas agora estão no nível do trabalho. Consulte os tópicos de codificação na [codificação baseada em cenário](migrate-v-2-v-3-migration-scenario-based-encoding.md) | Não | Não |
| `Outputs`<!-- row --> | `Outputs` | As entradas e saídas agora estão no nível do trabalho.  Em v3, o formato de metadados mudou de XML para JSON.  As Saídas ao Vivo começam na criação e terminam quando são excluídas. Consulte os tópicos de codificação na [codificação baseada em cenário](migrate-v-2-v-3-migration-scenario-based-encoding.md) | Não | Não |


| **Outras alterações** | **V2**  | **V2** |
|---|---|---|
| **Armazenamento** <!--new row --> |||
| Armazenamento <!--new row --> | | Os SDKs do v3 agora são dissociados do SDK de armazenamento, que oferece mais controle sobre a versão do SDK de armazenamento que você deseja usar e evita problemas de controle de versão.                      |
| **Codificação** <!--new row --> |||
| Taxas de bits de codificação <!--new row --> | taxas de bits medidas em Kbps, por exemplo: 128 (Kbps)| bits por segundo ex: 128000 (bits/segundo)|
| Codificando FairPlay DRM <!--new row --> | Nos serviços de mídia v2, o vetor de inicialização (IV) pode ser especificado. | Nos serviços de mídia v3, o FairPlay IV não pode ser especificado.|
| Codificador Premium <!--new row --> | Codificador Premium e indexador herdado| O [codificador Premium](../previous/media-services-encode-asset.md) e os [processadores de análise de mídia](../previous/legacy-components.md) herdados (visualização do indexador do Azure Media Services 2, edição facial etc.) não estão acessíveis por meio de v3. Adicionamos suporte para o mapeamento de canal de áudio para o codificador Standard.  Consulte [áudio na documentação do Swagger de codificação dos serviços de mídia](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json).  | Consulte os tópicos de codificação na [codificação baseada em cenário](migrate-v-2-v-3-migration-scenario-based-encoding.md) |
| **Transformações e trabalhos** <!--new row -->|||
| HTTPS de processamento baseado em trabalho <!--new row --> |<!-- empty -->| Para o processamento de trabalho baseado em arquivo, você pode usar uma URL HTTPS como entrada. Você não precisa ter conteúdo já armazenado no Azure, nem precisa criar ativos. |
| Modelos de ARM para trabalhos <!--new row --> | Os modelos de ARM não existiam na v2. | Uma transformação pode ser usada para compilar configurações reutilizáveis, criar Azure Resource Manager modelos e isolar configurações de processamento entre vários clientes ou locatários. |
| **Eventos ao vivo** <!--new row --> |||
| ponto de extremidade de streaming <!--new row --> | Um ponto de extremidade de streaming representa um serviço de streaming que pode fornecer conteúdo diretamente a um aplicativo de player de cliente ou a uma CDN (rede de distribuição de conteúdo) para distribuição posterior. | Os pontos de extremidade de streaming permanecem principalmente os mesmos. Eles são usados para empacotamento dinâmico, criptografia e entrega de conteúdo de HLS e DASH para streaming ao vivo e sob demanda, seja direto da origem ou através da CDN.  Os novos recursos incluem suporte para melhor integração e criação de gráficos de Azure Monitor. |
| Canais de eventos ao vivo <!--new row --> | Os canais são responsáveis pelo processamento de conteúdo de transmissão ao vivo. Um Canal fornece um ponto de extremidade de entrada (URL de ingestão) que você então fornece a um transcodificador ao vivo. O canal recebe fluxos de entrada ao vivo do transcodificador ao vivo e o torna disponível para streaming por meio de um ou mais pontos de extremidade de streaming. Canais também fornecem um ponto de extremidade de visualização prévia (URL de visualização prévia) que você usa para visualizar e validar seu fluxo antes de processamento e da entrega.| Eventos ao vivo substituem canais da API v2. Eles carregam a maioria dos recursos e têm mais novos recursos, como transcrições ao vivo, modo de espera e suporte para ingestão de RTMPS. |
| Programas de eventos ao vivo <!--new row --> | Um Programa permite que você controle a publicação e o armazenamento de segmentos em uma transmissão ao vivo. Os canais gerenciam os programas. A relação entre Canal e Programa é semelhante à mídia tradicional, em que um canal tem um fluxo constante de conteúdo e um programa tem como escopo algum evento regulado naquele canal. Você pode especificar o número de horas que deseja manter o conteúdo registrado para o programa definindo a `ArchiveWindowLength` propriedade. Esse valor pode ser definido entre o mínimo de 5 minutos e o máximo de 25 horas.| As saídas ao vivo agora substituem os programas na API v3. |
| Comprimento do evento ao vivo <!--new row --> |<!-- empty -->| Você pode transmitir eventos ao vivo 24/7 ao usar os serviços de mídia para transcodificar um feed de contribuição de taxa de bits única em um fluxo de saída com várias taxas de bits.|
| Latência de evento ao vivo <!--new row --> |<!-- empty -->| Novo suporte de streaming ao vivo de baixa latência em eventos ao vivo. |
| Visualização de eventos ao vivo <!--new row --> |<!-- empty -->| A Versão Prévia do Evento ao Vivo dá suporte ao empacotamento dinâmico e à criptografia dinâmica. Isso permite proteção de conteúdo na Versão Prévia, bem como empacotamento HLS e DASH. |
| RTMPS de evento ao vivo <!--new row --> |<!-- empty-->| Suporte aprimorado a RTMPS com maior estabilidade e suporte a codificador de fonte. |
| Ingestão segura de RTMPS de eventos ao vivo <!--new row --> | | Ao criar um evento ao vivo, você obtém 4 URLs de ingestão. As 4 URLs de ingestão são quase idênticas, têm o mesmo token de streaming `AppId` , apenas a parte do número da porta é diferente. Duas das URLs são primárias e de backup para RTMPS.| 
| Transcrição de evento ao vivo <!--new row --> |<!-- empty--> | O serviço de mídia do Azure fornece vídeo, áudio e texto em diferentes protocolos. Quando você publica sua transmissão ao vivo usando MPEG-DASH ou HLS/CMAF, juntamente com vídeo e áudio, nosso serviço entrega o texto transcrevedo no TTML compatível com IMSC 1.1.|
| Modo de espera do evento ao vivo <!--new row --> | Não havia modo de espera para v2. | O modo de espera é um novo recurso v3 que ajuda a gerenciar pools ativos de eventos ao vivo. Agora, os clientes podem iniciar um evento ao vivo no modo de espera por um custo menor antes de fazer a transição para o estado de execução. Isso melhora os horários de início do canal e reduz os custos de pools em operação para iniciar ups mais rapidamente. |
| Cobrança de evento ao vivo <!--new row --> | <!-- empty-->| A cobrança de eventos ao vivo é baseada em medidores de canal ao vivo. |
| Saídas ao vivo <!--new row --> | Os programas precisaram ser iniciados após a criação. | As Saídas ao Vivo começam na criação e terminam quando são excluídas. |
