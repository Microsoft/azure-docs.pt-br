---
title: Os benefícios da migração para a API do Media Services V3
description: Este artigo lista os benefícios da migração dos serviços de mídia v2 para v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 114635722a0a131fc146eb2ab69984effb211a88
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559750"
---
# <a name="step-1---understand-the-benefits-of-migrating-to-media-services-api-v3"></a>Etapa 1-entender os benefícios da migração para a API do Media Services V3

![logotipo do guia de migração](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![etapas de migração 2](./media/migration-guide/steps-1.svg)

## <a name="use-the-latest-api"></a>Usar a API mais recente

Incentivamos você a começar a usar a versão 2020-05-01 da API dos serviços de mídia do Azure v3 agora para obter os benefícios porque novos recursos, funcionalidades e otimizações de desempenho estão disponíveis apenas na API v3 atual.

Você pode alterar a versão da API no portal, os SDKs mais recentes, a CLI mais recente e a API REST com a cadeia de caracteres de versão correta.

Houve melhorias significativas nos serviços de mídia com v3.  

## <a name="benefits-of-media-services-v3"></a>Benefícios do Serviços de Mídia do Microsoft Azure v3

| **Recurso v3** | **Benefício** |
| --- | --- |
| **Portal do Azure** | |
| Atualizações de portal do Azure | O portal do Azure foi atualizado para incluir o gerenciamento de entidades da API v3. Ele permite que os clientes usem o portal para iniciar a transmissão ao vivo, enviar trabalhos de transformação v3, gerenciar políticas de proteção de conteúdo, pontos de extremidade de streaming, obter acesso à API, gerenciar contas de armazenamento vinculadas e executar tarefas de monitoramento. |
| **Contas e armazenamento** | |
| RBAC (controle de acesso baseado em função) do Azure | Agora, os clientes podem definir suas próprias funções e controlar o acesso a cada entidade na API ARM dos serviços de mídia. Isso ajuda a controlar o acesso a recursos por contas do AAD. |
| Identidades gerenciadas | Identidades gerenciadas eliminam a necessidade de os desenvolvedores gerenciarem credenciais fornecendo uma identidade para o recurso do Azure no Azure AD. Veja detalhes sobre identidades gerenciadas [aqui](../../active-directory/managed-identities-azure-resources/overview.md). |
| Suporte a link privado | Os clientes acessarão pontos de extremidade dos serviços de mídia para distribuição de chave, LiveEvents e StreamingEndpoints por meio de um PrivateEndpoint em sua VNet. |
| Suporte [a chaves gerenciadas pelo cliente](concept-use-customer-managed-keys-byok.md) ou BYOK (traga sua própria chave) | Os clientes podem criptografar os dados em sua conta de serviços de mídia usando uma chave em seus Azure Key Vault. |
| **Ativos** | |
| Um ativo pode ter vários [localizadores de streaming](streaming-locators-concept.md) , cada um com diferentes configurações de criptografia dinâmica e de [empacotamento dinâmico](dynamic-packaging-overview.md) . | Há um limite de 100 localizadores de streaming permitidos em cada ativo. Os clientes podem armazenar uma única cópia do conteúdo de mídia no ativo, mas compartilhar diferentes URLs de streaming com diferentes políticas de streaming ou políticas de proteção de conteúdo que se baseiam em um público-alvo.
| **Processamento de trabalho** ||
| V3 apresenta o conceito de [transformações](transforms-jobs-concept.md)   para processamento de trabalho baseado em arquivo. | Uma transformação pode ser usada para criar configurações reutilizáveis, criar modelos do Azure Resource Manager e isolar configurações de processamento entre vários clientes ou locatários. |
| Para o processamento de trabalho baseado em arquivo, você pode usar uma URL HTTP (S) como a entrada. | Você não precisa ter conteúdo já armazenado no Azure, nem precisa criar ativos de entrada. |
| **Eventos ao vivo** ||
| Eventos ao vivo da 1080p Premium | O novo SKU de evento ao vivo permite que os clientes obtenham codificação de nuvem com saída de até 1080p na resolução. |
| Novo suporte de streaming ao vivo de [baixa latência](live-event-latency.md) em eventos ao vivo. | Isso permite que os usuários assistam a eventos ao vivo mais próximos ao tempo real do que se não tivessem essa configuração habilitada. |
| A visualização de eventos ao vivo dá suporte ao [empacotamento dinâmico](dynamic-packaging-overview.md)   e à criptografia dinâmica. | Isso habilita a proteção de conteúdo em pacotes de visualização e de traço e HLS. |
| Aplicativos de substituição de saídas ao vivo | A saída ao vivo é mais simples de usar do que a entidade do programa nas APIs v2. |
| A ingestão RTMP para eventos ao vivo foi aprimorada, com suporte para mais codificadores | Aumenta a estabilidade e fornece flexibilidade do codificador de origem. |
| Eventos ao vivo podem ser transmitidos 24x7 | Você pode hospedar um evento ao vivo e manter seu público-alvo por períodos mais longos. |
| Transcrição dinâmica em eventos ao vivo | A transcrição ao vivo permite aos clientes transcrever automaticamente o idioma falado em texto em tempo real durante a transmissão de eventos ao vivo. Isso melhora significativamente a acessibilidade de eventos ao vivo. |
| [Modo de espera](live-events-outputs-concept.md#standby-mode) em eventos ao vivo | Eventos ao vivo que estão no estado de espera são menos caros do que a execução de eventos ao vivo. Isso permite que os clientes mantenham um conjunto de eventos ao vivo que estão prontos para começar em segundos a um custo menor do que manter um conjunto de eventos ao vivo em execução. Os preços reduzidos para eventos de espera ativa entrarão em vigor em fevereiro de 2021 para a maioria das regiões, com o restante a seguir em abril de 2021.
|**Proteção de conteúdo** ||
| [Proteção](content-key-policy-concept.md)   de conteúdo dá suporte a recursos de várias chaves. | Agora, os clientes podem usar várias chaves de criptografia de conteúdo em seus localizadores de streaming. |
| **Monitoring** | |
| Suporte à notificação [do Azure EventGrid](monitoring/reacting-to-media-services-events.md) | As notificações do EventGrid são mais ricas em recursos. Há mais tipos de notificações, suporte mais amplo ao SDK para receber as notificações em seu próprio aplicativo e mais serviços do Azure existentes que podem atuar como manipuladores de eventos. |
| [Suporte a Azure Monitor e integração no portal do Azure](monitoring/monitor-events-portal-how-to.md) | Isso permite que os clientes visualizem o uso de cota de contas dos serviços de mídia, estatísticas em tempo real de pontos de extremidade de streaming e ingestão e arquivamento de estatísticas para eventos ao vivo. Agora, os clientes podem definir alertas e executar as ações necessárias com base em dados de métrica em tempo real. |
