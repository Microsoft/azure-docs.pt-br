---
title: Diretrizes de migração de proteção de conteúdo
description: Este artigo fornece orientação baseada em cenário de proteção de conteúdo que ajudará você a migrar dos serviços de mídia do Azure v2 para v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/26/2021
ms.author: inhenkel
ms.openlocfilehash: 7ef41b76f343d8997feebc4a366deda7ce6a2afa
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644071"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>Diretrizes de migração baseadas em cenários de proteção de conteúdo

![logotipo do guia de migração](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![etapas de migração 2](./media/migration-guide/steps-4.svg)

Este artigo fornece detalhes e orientações sobre a migração de casos de uso de proteção de conteúdo da API v2 para a nova API dos serviços de mídia do Azure v3.

## <a name="protect-content-in-v3-api"></a>Proteger o conteúdo na API v3

Use o suporte para recursos de [várias chaves](design-multi-drm-system-with-access-control.md) na nova API v3.

Consulte os conceitos, tutoriais e guias de proteção de conteúdo abaixo para obter etapas específicas.

## <a name="visibility-of-v2-assets-streaminglocators-and-properties-in-the-v3-api-for-content-protection-scenarios"></a>Visibilidade de ativos v2, StreamingLocators e propriedades na API V3 para cenários de proteção de conteúdo

Durante a migração para a API v3, você verá que precisa acessar algumas propriedades ou chaves de conteúdo de seus ativos v2. Uma diferença importante é que a API v2 usaria o **AssetID** como a chave de identificação primária e a nova API v3 usará o nome de gerenciamento de recursos do Azure da entidade como o identificador primário.  A propriedade v2 **Asset.Name** normalmente não é usada como um identificador exclusivo, portanto, ao migrar para o V3, você descobrirá que os nomes de seu ativo v2 agora aparecem no campo **Asset. Description** .

Por exemplo, se você tiver anteriormente um ativo V2 com a ID **"NB: CID: UUID: 8cb39104-122C-496e-9ac5-7f9e2c2547b8"**, encontrará ao listar os ativos v2 antigos por meio da API v3, o nome será a parte GUID no final (neste caso, **"8cb39104-122C-496e-9ac5-7f9e2c2547b8"**).

Você pode consultar o **StreamingLocators** associado aos ativos criados na API v2 usando o novo método v3 [ListStreamingLocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators) na entidade de ativo.  Também referenciar a versão SDK do cliente .NET do [ListStreamingLocatorsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocatorsasync?view=azure-dotnet)

Os resultados do método **ListStreamingLocators** fornecerão o **nome** e o **StreamingLocatorId** do localizador, juntamente com o **StreamingPolicyName**.

Para localizar o **ContentKeys** usado em seu **StreamingLocators** para proteção de conteúdo, você pode chamar o método [StreamingLocator. ListContentKeysAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.streaminglocatorsoperationsextensions.listcontentkeysasync?view=azure-dotnet) .  

Todos os **ativos** que foram criados e publicados usando a API v2 terão uma [política de chave de conteúdo](https://docs.microsoft.com/azure/media-services/latest/content-key-policy-concept) e uma chave de conteúdo definida nelas na API v3, em vez de usar uma política de chave de conteúdo padrão na política de [streaming](https://docs.microsoft.com/azure/media-services/latest/streaming-policy-concept).

Para obter mais informações sobre a proteção de conteúdo na API v3, consulte o artigo [proteger seu conteúdo com a criptografia dinâmica dos serviços de mídia.](https://docs.microsoft.com/azure/media-services/latest/content-protection-overview)

## <a name="how-to-list-your-v2-assets-and-content-protection-settings-using-the-v3-api"></a>Como listar os ativos V2 e as configurações de proteção de conteúdo usando a API v3

Na API v2, você normalmente usaria **ativos**, **StreamingLocators** e **ContentKeys** para proteger seu conteúdo de streaming.
Ao migrar para a API v3, seus ativos de API v2, StreamingLocators e ContentKeys são todos expostos automaticamente na API v3 e todos os dados neles estão disponíveis para você acessar.

## <a name="can-i-update-v2-properties-using-the-v3-api"></a>Posso atualizar as propriedades v2 usando a API v3?

Não, você não pode atualizar nenhuma propriedade em entidades v2 por meio da API v3 que foi criada usando StreamingLocators, StreamingPolicies, políticas de chave de conteúdo e chaves de conteúdo na v2.
Se você precisar atualizar, alterar ou alterar o conteúdo armazenado em entidades v2, será necessário atualizá-lo por meio da API v2 ou criar novas entidades da API V3 para migrá-las.

## <a name="how-do-i-change-the-contentkeypolicy-used-for-a-v2-asset-that-is-published-and-keep-the-same-content-key"></a>Como fazer alterar o ContentKeyPolicy usado para um ativo v2 que é publicado e manter a mesma chave de conteúdo?

Nessa situação, você deve primeiro cancelar a publicação (remover todos os localizadores de streaming) no ativo por meio do SDK v2 (excluir o localizador, desvincular a política de autorização de chave de conteúdo, desvincular a política de entrega de ativos, desvincular a chave de conteúdo, excluir a chave de conteúdo) e criar um novo **[StreamingLocator](https://docs.microsoft.com/azure/media-services/latest/streaming-locators-concept)** no v3 usando uma [StreamingPolicy](https://docs.microsoft.com/azure/media-services/latest/streaming-policy-concept) v3 e [ContentKeyPolicy](https://docs.microsoft.com/azure/media-services/latest/content-key-policy-concept).

Você precisaria especificar o identificador de chave de conteúdo específico e o valor de chave necessários ao criar o **[StreamingLocator](https://docs.microsoft.com/azure/media-services/latest/streaming-locators-concept)**.

Observe que é possível excluir o localizador v2 usando a API v3, mas isso não removerá a chave de conteúdo ou a política de chave de conteúdo usada se elas tiverem sido criadas na API v2.  

## <a name="using-amse-v2-and-amse-v3-side-by-side"></a>Usando o AMSE V2 e o AMSE v3 lado a lado

Ao migrar seu conteúdo de v2 para v3, é recomendável instalar a [ferramenta do Gerenciador de serviços de mídia do Azure v2](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0) junto com a [ferramenta V3 do Gerenciador de serviços de mídia do Azure](https://github.com/Azure/Azure-Media-Services-Explorer) para ajudar a comparar os dados que eles mostram lado a lado para um ativo que é criado e publicado por meio de APIs v2. Todas as propriedades devem estar visíveis, mas em locais um pouco diferentes agora.  


## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>Conceitos, tutoriais e guias de proteção de conteúdo

### <a name="concepts"></a>Conceitos

- [Proteger seu conteúdo com a criptografia dinâmica dos serviços de mídia](content-protection-overview.md)
- [Criação de um sistema de proteção de conteúdo de DRM múltiplo com controle de acesso](design-multi-drm-system-with-access-control.md)
- [Modelo de licença dos serviços de mídia v3 com PlayReady](playready-license-template-overview.md)
- [Visão geral do modelo de licença do Media Services V3 com Widevine](widevine-license-template-overview.md)
- [Requisitos e configuração de licença do Apple FairPlay](fairplay-license-overview.md)
- [Políticas de streaming](streaming-policy-concept.md)
- [Políticas de chave de conteúdo](content-key-policy-concept.md)

### <a name="tutorials"></a>Tutoriais

[Início Rápido: Usar o portal para criptografar o conteúdo](encrypt-content-quickstart.md)

### <a name="how-to-guides"></a>Guias de como fazer

- [Obter uma chave de assinatura da política existente](get-content-key-policy-dotnet-howto.md)
- [Streaming FairPlay offline para iOS com os serviços de mídia v3](offline-fairplay-for-ios.md)
- [Streaming Widevine offline para Android com os serviços de mídia v3](offline-widevine-for-android.md)
- [Streaming do PlayReady offline para Windows 10 com os serviços de mídia v3](offline-plaready-streaming-for-windows-10.md)

## <a name="samples"></a>Exemplos

Você também pode [comparar o código V2 e v3 nos exemplos de código](migrate-v-2-v-3-migration-samples.md).

## <a name="tools"></a>Ferramentas

- [ferramenta do Gerenciador de serviços de mídia do Azure v3](https://github.com/Azure/Azure-Media-Services-Explorer)
- [v2 ferramenta do Gerenciador de serviços de mídia do Azure](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0)
