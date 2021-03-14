---
title: Visão Geral do ponto de extremidade de streaming dos Serviços de Mídia do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral dos pontos de extremidade de streaming dos serviços de mídia do Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 0961b52ebc7271fabf4cc05ed99eea23d911a2d4
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103009077"
---
# <a name="streaming-endpoints-overview"></a>Visão geral dos pontos de extremidade de streaming  

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Não estão sendo adicionados novos recursos ou funcionalidades aos Serviços de Mídia v2. <br/>Confira a versão mais recente, [Serviços de Mídia v3](../latest/index.yml). Além disso, consulte s [diretrizes de migração da v2 para a v3](../latest/migrate-v-2-v-3-migration-introduction.md)

No AMS (Serviços de Mídia do Microsoft Azure), um **Ponto de Extremidade de Streaming** representa um serviço de streaming que pode fornecer conteúdo diretamente a um aplicativo cliente player ou à CDN (Rede de Distribuição de Conteúdo) para distribuição posterior. Os Serviços de Mídia também fornecem integração perfeita da CDN do Azure. O fluxo de saída do serviço StreamingEndpoint pode ser um fluxo ao vivo, um vídeo por demanda ou um download progressivo do seu ativo na conta dos Serviços de Mídia. Cada conta dos Serviços de Mídia do Azure inclui um StreamingEndpoint padrão. StreamingEndpoints adicionais podem ser criados na conta. Há duas versões do StreamingEndpoints, 1.0 e 2.0. A partir de 10 de janeiro de 2017, todas as contas AMS recém-criadas incluirão a versão 2.0 **padrão** do StreamingEndpoint. Pontos de extremidade de streaming adicionais que você adicionar nessa conta também terão a versão 2.0. Essa alteração não afetará as contas existentes; StreamingEndpoints existente estarão na versão 1.0 e poderão ser atualizados para a versão 2.0. Com essa alteração, haverá alterações de comportamento, cobrança e recurso (para obter mais informações, confira a seção **Tipos e versões de streaming** documentada abaixo).

Os serviços de mídia do Azure adicionaram as seguintes propriedades à entidade de ponto de extremidade de streaming: **CdnProvider**, **CdnProfile**, **StreamingEndpointVersion**. Para obter uma visão detalhada dessas propriedades, clique [aqui](/rest/api/media/operations/streamingendpoint). 

Quando você cria uma conta dos Serviços de Mídia do Azure, um ponto de extremidade de streaming padrão é criado para você no estado **Parado**. Não é possível excluir o ponto de extremidade de streaming padrão. Dependendo da disponibilidade da Azure CDN na região de destino, por padrão, o ponto de extremidade de streaming recém-criado padrão também inclui integração com o provedor de CDN “StandardVerizon”. 
                
> [!NOTE]
> A integração do Azure CDN pode ser desabilitada antes de iniciar o ponto de extremidade de streaming. O `hostname` e a URL de streaming permanecerão iguais se você habilitar ou não a CDN.

Este tópico fornece uma visão geral das principais funcionalidades fornecidas pelos ponto de extremidade de streaming.

## <a name="naming-conventions"></a>Convenções de nomenclatura

Para o ponto de extremidade padrão: `{AccountName}.streaming.mediaservices.windows.net`

Para pontos de extremidade adicionais: `{EndpointName}-{AccountName}.streaming.mediaservices.windows.net`

## <a name="streaming-types-and-versions"></a>Tipos e versões de streaming

### <a name="standardpremium-types-version-20"></a>Tipos Standard/Premium (versão 2.0)

A partir da versão de janeiro de 2017 dos serviços de mídia, você tem dois tipos de streaming: **Standard** (visualização) e **Premium**. Esses tipos fazem parte da versão do ponto de extremidade de streaming “2.0”.


|Tipo|Descrição|
|--------|--------|  
|**Standard**|O ponto de extremidade de streaming padrão é um tipo **padrão** , pode ser alterado para o tipo Premium, ajustando as unidades de streaming.|
|**Premium** |Esta opção é adequada para cenários profissionais que exigem maior escala ou controle. Você passa para um tipo **Premium** ajustando unidades de streaming.<br/>Os pontos de extremidade de streaming dedicados residem em um ambiente isolado e não conpetem por recursos.|

Para clientes que procuram fornecer conteúdo a grandes públicos da Internet, recomendamos que você habilite a CDN no ponto de extremidade de streaming.

Para obter mais informações, consulte a seção [Comparar tipos de Streaming](#comparing-streaming-types) a seguir.

### <a name="classic-type-version-10"></a>Tipo Clássico (versão 1.0)

Usuários que criaram contas AMS antes da versão de 10 de janeiro de 2017 terão um tipo **Clássico** de ponto de extremidade de streaming. Esse tipo faz parte da versão do ponto de extremidade de streaming “1.0”.

Se seu ponto de extremidade de streaming **versão “1.0”** tiver >=1 SU (unidade de streaming) premium, ela será um ponto de extremidade de streaming premium e oferecerá todos os recursos do AMS (assim como o tipo **Standard/Premium**) sem etapas adicionais de configuração.

>[!NOTE]
>Os pontos de extremidade de streaming **Clássicos** (versão “1.0” e 0 unidades de streaming) fornecem recursos limitados e não incluem um SLA. É recomendável migrar para o tipo **Standard** para obter uma melhor experiência e usar recursos como empacotamento dinâmico ou criptografia, bem como outros recursos que acompanham o tipo **Standard**. Para migrar para o tipo **Standard**, acesse o [Portal do Azure](https://portal.azure.com/) e selecione **Aceitar o Standard**. Para saber mais sobre migração, veja a seção [migração](#migration-between-types).
>
>Lembre-se de que essa operação não pode ser revertida e tem um impacto no preço.
>
 
## <a name="comparing-streaming-types"></a>Comparando tipos de streaming

### <a name="versions"></a>Versões

|Tipo|StreamingEndpointVersion|ScaleUnits|CDN|Cobrança|
|--------------|----------|-----------------|-----------------|-----------------|
|Clássico|1.0|0|NA|Gratuita|
|Ponto de extremidade de streaming padrão (visualização)|2,0|0|Sim|Pago|
|Unidades de Streaming Premium|1.0|>0|Sim|Pago|
|Unidades de Streaming Premium|2,0|>0|Sim|Pago|

### <a name="features"></a>Recursos

Recurso|Standard|Premium
---|---|---
Produtividade |Até 600 Mbps e pode fornecer uma taxa de transferência muito mais eficiente quando uma CDN é usada.|200 Mbps por UA (unidade de streaming). Pode fornecer uma taxa de transferência muito mais eficiente quando uma CDN é usada.
CDN|Azure CDN, CDN de terceiros ou sem CDN.|Azure CDN, CDN de terceiros ou sem CDN.
A cobrança é rateada| Diariamente|Diariamente
Criptografia dinâmica|Sim|Sim
Empacotamento dinâmico|Sim|Sim
Escala|Escala verticalmente automaticamente com a taxa de transferência de destino.|Unidades de streaming adicionais.
Filtragem de IP/G20/host personalizado <sup>1</sup>|Sim|Sim
Download progressivo|Sim|Sim
Uso recomendado |Recomendado para a grande maioria dos cenários de streaming.|Uso profissional. 

<sup>1</sup> é usado somente diretamente no ponto de extremidade de streaming quando a CDN não está habilitada no ponto de extremidade.<br/>

Para obter informações de SLA, consulte [preços e SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="migration-between-types"></a>Migração entre tipos

De | Para | Ação
---|---|---
Clássico|Standard|Necessidade de aceitação
Clássico|Premium| Escala (unidades de streaming adicionais)
Standard/Premium|Clássico|Não disponível (se a versão do ponto de extremidade de streaming for 1.0. É permitido alterar para o clássico configurando scaleunits para “0”)
Standard (com/sem CDN)|Premium com as mesmas configurações|Permitido no estado **iniciado**. (por meio do Portal do Azure)
Premium (com/sem CDN)|Standard com as mesmas configurações|Permitido no estado **iniciado** (por meio do Portal do Azure)
Standard (com/sem CDN)|Premium com configuração diferente|Permitido no estado **parado** (por meio do Portal do Azure). Não permitido no estado de execução.
Premium (com/sem CDN)|Standard com configuração diferente|Permitido no estado **parado** (por meio do Portal do Azure). Não permitido no estado de execução.
Versão 1.0 com UA > = 1 com CDN|Standard/Premium sem qualquer CDN|Permitido no estado **parado**. Não permitido no estado **iniciado**.
Versão 1.0 com UA > = 1 com CDN|Standard com/sem CDN|Permitido no estado **parado**. Não permitido no estado **iniciado**. A CDN da Versão 1.0 será excluída e uma nova será criada e iniciada.
Versão 1.0 com UA > = 1 com CDN|Premium com/sem CDN|Permitido no estado **parado**. Não permitido no estado **iniciado**. A CDN Clássica será excluída e uma nova será criada e iniciada.

## <a name="next-steps"></a>Próximas etapas
Examine os roteiros de aprendizagem dos Serviços de Mídia.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
