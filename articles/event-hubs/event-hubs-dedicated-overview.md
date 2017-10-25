---
title: "Visão geral da capacidade de Hubs de Eventos Dedicados do Azure | Microsoft Docs"
description: "Visão geral da capacidade de Hubs de Eventos Dedicados do Microsoft Azure ."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2017
ms.author: sethm;babanisa
ms.openlocfilehash: db8b119178de0e565b2064e9a52d5e9989d60d38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-event-hubs-dedicated"></a>Visão geral de Hubs de Eventos Dedicados

A capacidade dos *Hubs de Eventos Dedicados* oferece implantações de único locatário para clientes com exigências mais rígidas. Em larga escala, os Hubs de Eventos do Azure recebem mais de dois milhões de eventos por segundo ou até 2 GB por segundo de telemetria com latência de subsegundos e armazenamento durável. Isso também permite soluções integradas processando em lotes e em tempo real no mesmo sistema. Com a [Captura de Hubs de Evento](event-hubs-capture-overview.md) inclusa na oferta, você pode reduzir a complexidade da sua solução ao ter um único fluxo que dê suporte a pipelines baseados em tempo real e em lote.

A tabela a seguir compara os tipos de serviço disponíveis dos Hubs de Eventos. A oferta de Hubs de Eventos Dedicados tem um preço mensal fixo, em comparação ao preço de uso da maioria dos recursos Standard. A camada Dedicada oferece os recursos do plano Standard, mas com capacidade em escala empresarial para clientes com cargas de trabalho exigentes. 

| Recurso | Standard | Dedicado |
| --- |:---:|:---:|:---:|
| Eventos de entrada | Pagamento por milhão de eventos | Incluso |
| Unidade de taxa de transferência (entrada de 1 MB/s, saída de 2 MB/s) | Pagamento por hora | Incluso |
| Tamanho da mensagem | 256 KB | 1 MB |
| Políticas do publicador | Sim | Sim |   
| Grupos de consumidores | 20 | 20 |
| Reprodução de mensagem | Sim | Sim |
| Unidades de produtividade máxima | 20 (flexível até 100)   | 1 CU≈200 |
| Conexões orientadas | 1000 incluídos | 100.000 incluídos |
| Conexões agenciadas adicionais | Sim | Sim |
| Retenção de mensagem | 1 dia incluído | Até 7 dias incluídos |
| Captura | Pagamento por hora | Incluso |

## <a name="benefits-of-event-hubs-dedicated-capacity"></a>Os benefícios da capacidade dos Hubs de Eventos Dedicados

Os seguintes benefícios estão disponíveis ao usar os Hubs de Eventos Dedicados:

* Hospedagem de único locatário, sem ruído de outros locatários.
* O tamanho de mensagem aumenta para 1 MB em comparação aos 256 KB para Standard.
* Desempenho repetível em todas as ocasiões.
* Capacidade garantida para atender às suas necessidades de pico.
* Escalonável entre 1 e 8 CUs (unidades de capacidade) – permite até 2 milhões de eventos de entrada por segundo.
  * As CUs gerenciam a escala dos Hubs de Eventos dedicados, em que cada UC pode fornecer aproximadamente o equivalente a 200 TUs (unidades de produtividade).
* Sem manutenção – gerenciamos o balanceamento de carga, as atualizações do SO, os patches de segurança e o particionamento.
* Preço mensal fixo.

O recurso Hubs de Eventos Dedicados também remove algumas limitações de taxa de produtividade da oferta Standard. As unidades de produtividade na camada Standard dão a você 1000 eventos por segundo ou 1 MB por segundo de entrada por TU e duas vezes essa quantidade de saída. A oferta de escala Dedicada não tem restrições de contagens de eventos de entrada e saída. Esses limites são regidos apenas pela capacidade de processamento dos hubs de eventos adquiridos.

Este serviço destina-se aos usuários com maior telemetria e está disponível para clientes com contrato Enterprise.

## <a name="how-to-onboard"></a>Como fazer a integração

A plataforma dos Hubs de Eventos Dedicados é oferecida por meio de um contrato enterprise com tamanhos variados de CUs. Cada CU fornece aproximadamente o equivalente a 200 unidades de taxa de transferência. Você pode dimensionar sua capacidade para cima ou para baixo ao longo do mês a fim de atender às suas necessidades, adicionando ou removendo CUs. O plano dedicado é exclusivo porque oferece uma integração mais prática da equipe de produto dos Hubs de Eventos para lhe dar a implantação flexível ideal. 

## <a name="next-steps"></a>Próximas etapas
Entre em contato com seu representante de vendas da Microsoft ou com o Suporte da Microsoft para obter mais detalhes sobre a capacidade dos Hubs de Eventos Dedicados. Você também pode saber mais sobre Hubs de Eventos visitando estes links:

Para obter informações detalhadas sobre os preços, visite os links a seguir:

- [Preço de Hubs de Eventos Dedicados](https://azure.microsoft.com/pricing/details/event-hubs/). Você também pode entrar em contato com seu representante de vendas da Microsoft ou com o Suporte da Microsoft para obter mais detalhes sobre a capacidade dos Hubs de Eventos Dedicados.
- As [perguntas frequentes sobre Hubs de Eventos](event-hubs-faq.md) contêm informações sobre preços e tiram algumas dúvidas sobre os Hubs de Eventos. 
