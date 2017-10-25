---
title: "Visão Geral do ponto de extremidade de streaming dos Serviços de Mídia do Azure | Microsoft Docs"
description: "Este tópico fornece uma visão geral dos pontos de extremidade de streaming dos Serviços de Mídia do Azure."
services: media-services
documentationcenter: 
author: Juliako
writer: juliako
manager: cfowler
editor: 
ms.assetid: 097ab5e5-24e1-4e8e-b112-be74172c2701
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: e454778c558b9c17c47ad9eb651737aa0b5e2605
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="streaming-endpoints-overview"></a>Visão geral dos pontos de extremidade de streaming 

##<a name="overview"></a>Visão geral

No AMS (Serviços de Mídia do Microsoft Azure), um **Ponto de Extremidade de Streaming** representa um serviço de streaming que pode fornecer conteúdo diretamente a um aplicativo cliente player ou à CDN (Rede de Distribuição de Conteúdo) para distribuição posterior. Os Serviços de Mídia também fornecem integração perfeita da CDN do Azure. O fluxo de saída do serviço StreamingEndpoint pode ser um fluxo ao vivo, um vídeo por demanda ou um download progressivo do seu ativo na conta dos Serviços de Mídia. Cada conta dos Serviços de Mídia do Azure inclui um StreamingEndpoint padrão. StreamingEndpoints adicionais podem ser criados na conta. Há duas versões do StreamingEndpoints, 1.0 e 2.0. A partir de 10 de janeiro de 2017, todas as contas AMS recém-criadas incluirão a versão 2.0 **padrão** do StreamingEndpoint. Pontos de extremidade de streaming adicionais que você adicionar nessa conta também terão a versão 2.0. Essa alteração não afetará as contas existentes; StreamingEndpoints existente estarão na versão 1.0 e poderão ser atualizados para a versão 2.0. Com essa alteração, haverá alterações de comportamento, cobrança e recurso (para obter mais informações, confira a seção **Tipos e versões de streaming** documentada abaixo).

Além disso, a partir da versão 2.15 (lançados em janeiro de 2017), os Serviços de Mídia do Azure adicionaram as seguintes propriedades à entidade do Ponto de Extremidade de Streaming: **CdnProvider**, **CdnProfile**, **FreeTrialEndTime**, **StreamingEndpointVersion**. Para obter uma visão detalhada dessas propriedades, clique [aqui](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

Quando você cria uma conta dos Serviços de Mídia do Azure, um ponto de extremidade de streaming padrão é criado para você no estado **Parado**. Não é possível excluir o ponto de extremidade de streaming padrão. Dependendo da disponibilidade da Azure CDN na região de destino, por padrão, o ponto de extremidade de streaming recém-criado padrão também inclui integração com o provedor de CDN “StandardVerizon”. 

>[!NOTE]
>A integração do Azure CDN pode ser desabilitada antes de iniciar o ponto de extremidade de streaming.

Este tópico fornece uma visão geral das principais funcionalidades fornecidas pelos ponto de extremidade de streaming.

## <a name="streaming-types-and-versions"></a>Tipos e versões de streaming

### <a name="standardpremium-types-version-20"></a>Tipos Standard/Premium (versão 2.0)

A partir da versão dos Serviços de Mídia de janeiro de 2017, você terá dois tipos de streaming: **Standard** e **Premium**. Esses tipos fazem parte da versão do ponto de extremidade de streaming “2.0”.

Tipo|Descrição
---|---
**Standard**|Essa é a opção padrão que funciona para a maioria dos cenários.<br/>Com essa opção, você obtém SLA fixo/limitado e os primeiros 15 dias após iniciar o ponto de extremidade de streaming são gratuitos.<br/>Se você criar mais de um ponto de extremidade de streaming, somente o primeiro deles será gratuito pelos primeiros 15 dias, os outros serão cobrados assim forem iniciados. <br/>Observe que a avaliação gratuita se aplica somente a contas de serviços de mídia e do ponto de extremidade de streaming recém-criadas. Pontos de extremidade de streaming existentes e criados adicionalmente não incluem o período de avaliação gratuita mesmo se forem atualizados para a versão 2.0 ou criados nela.
**Premium**|Esta opção é adequada para cenários profissionais que exigem maior escala ou controle.<br/>SLA variável com base na capacidade da UA (unidade de streaming) premium adquirida, pontos de extremidade de streaming dedicados residem em um ambiente isolado e não competem por recursos.

Para obter mais informações, consulte a seção **Comparar tipos de Streaming** a seguir.

### <a name="classic-type-version-10"></a>Tipo Clássico (versão 1.0)

Usuários que criaram contas AMS antes da versão de 10 de janeiro de 2017 terão um tipo **Clássico** de ponto de extremidade de streaming. Esse tipo faz parte da versão do ponto de extremidade de streaming “1.0”.

Se seu ponto de extremidade de streaming **versão “1.0”** tiver >=1 SU (unidade de streaming) premium, ela será um ponto de extremidade de streaming premium e oferecerá todos os recursos do AMS (assim como o tipo **Standard/Premium**) sem etapas adicionais de configuração.

>[!NOTE]
>Pontos de extremidade de streaming **Clássicos** (versão “1.0” e 0 UA) fornecem recursos limitados e não incluem um SLA. É recomendável migrar para o tipo **Standard** para obter uma melhor experiência e usar recursos como empacotamento dinâmico ou criptografia, bem como outros recursos que acompanham o tipo **Standard**. Para migrar para o tipo **Standard**, acesse o [Portal do Azure](https://portal.azure.com/) e selecione **Aceitar o Standard**. Para saber mais sobre migração, veja a seção [migração](#migration-between-types).
>
>Lembre-se de que essa operação não pode ser revertida e tem um impacto no preço.
>
 
## <a name="comparing-streaming-types"></a>Comparando tipos de streaming

### <a name="versions"></a>Versões

|Tipo|StreamingEndpointVersion|ScaleUnits|CDN|Cobrança|Contrato de Nível de Serviço| 
|--------------|----------|-----------------|-----------------|-----------------|-----------------|    
|Clássico|1.0|0|ND|Grátis|ND|
|Ponto de Extremidade de Streaming Standard|2,0|0|Sim|Pago|Sim|
|Unidades de Streaming Premium|1.0|>0|Sim|Pago|Sim|
|Unidades de Streaming Premium|2,0|>0|Sim|Pago|Sim|

### <a name="features"></a>Recursos

Recurso|Standard|Premium
---|---|---
Gratuito pelos primeiros 15 dias| Sim |Não
Taxa de transferência |Até 600 Mbps quando a Azure CDN não é usada. Escala com CDN.|200 Mbps por UA (unidade de streaming). Escala com CDN.
Contrato de Nível de Serviço | 99.9|99,9 (200 Mbps por UA).
CDN|Azure CDN, CDN de terceiros ou sem CDN.|Azure CDN, CDN de terceiros ou sem CDN.
A cobrança é rateada| Diário|Diário
Criptografia dinâmica|Sim|Sim
Empacotamento dinâmico|Sim|Sim
Escala|Escala verticalmente automaticamente com a taxa de transferência de destino.|Unidades de streaming adicionais
Filtragem de IP/G20/Host personalizado|Sim|Sim
Download progressivo|Sim|Sim
Uso recomendado |Recomendado para a grande maioria dos cenários de streaming.|Uso profissional.<br/>Se você achar que pode ter necessidades além do Standard. Entre em contato conosco (amsstreaming em microsoft.com) se você espera ter uma audiência simultânea superior a 50.000 visualizadores.


## <a name="migration-between-types"></a>Migração entre tipos

Da | Para | Ação
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

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

