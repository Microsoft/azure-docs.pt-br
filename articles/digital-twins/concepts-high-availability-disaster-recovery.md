---
title: Alta disponibilidade e recuperação de desastre
titleSuffix: Azure Digital Twins
description: Descreve os recursos do Azure digital gêmeos que ajudam você a criar soluções de IoT do Azure altamente disponíveis com recursos de recuperação de desastre.
author: baanders
ms.author: baanders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 3336a086fbe8f4291f752836a610cd80b773ec2d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98790809"
---
# <a name="azure-digital-twins-high-availability-and-disaster-recovery"></a>Alta disponibilidade e recuperação de desastre do Azure digital gêmeos

Uma importante área de consideração para soluções de IoT resilientes é a continuidade dos negócios e a recuperação de desastres. A criação de **ha (alta disponibilidade)** e **recuperação de desastres (Dr)** pode ajudá-lo a definir e atingir as metas de tempo de atividade apropriadas para sua solução.

Este artigo aborda os recursos de HA e DR oferecidos especificamente pelo serviço gêmeos do Azure digital.

O Azure digital gêmeos dá suporte a essas opções de recurso:
* *Ha entre regiões* – redundância interna para fornecer o tempo de atividade do serviço
* *Dr de região cruzada* – failover para uma região do Azure emparelhada geograficamente no caso de uma falha de data center inesperada

Você também pode ver a seção [*práticas recomendadas*](#best-practices) para obter diretrizes gerais do Azure sobre como projetar para ha/Dr.

## <a name="intra-region-ha"></a>HA entre regiões
 
O Azure digital gêmeos fornece HA de região interna implementando redundâncias dentro do serviço. Isso é refletido no [SLA de serviço](https://azure.microsoft.com/support/legal/sla/digital-twins) para tempo de atividade. **Nenhum trabalho adicional é exigido pelos desenvolvedores de uma solução de gêmeos digital do Azure para aproveitar esses recursos de alta disponibilidade.** Embora o Azure digital gêmeos ofereça uma garantia de tempo de atividade razoavelmente alta, as falhas transitórias ainda podem ser esperadas, como em qualquer plataforma de computação distribuída. As políticas de repetição apropriadas devem ser internas aos componentes que interagem com um aplicativo de nuvem para lidar com falhas transitórias.

## <a name="cross-region-dr"></a>Recuperação de desastres de região cruzada

Pode haver algumas situações raras em que um data center enfrenta interrupções extensas devido a falhas de energia ou outros eventos na região. Esses eventos são raros e, durante essas falhas, o recurso de HA de região interna descrito acima pode não ajudar. O gêmeos digital do Azure aborda isso com o failover iniciado pela Microsoft.

O **failover iniciado pela Microsoft** é exercido pela Microsoft em raras situações para realizar o failover de todas as instâncias de gêmeos digitais do Azure de uma região afetada para a região emparelhada geograficamente correspondente. Esse processo é uma opção padrão (sem nenhuma maneira para os usuários recusarem) e não requer nenhuma intervenção do usuário. A Microsoft se reserva o direito de determinar quando essa opção será exercida. Esse mecanismo não envolve um consentimento de usuário antes do failover da instância do usuário.

>[!NOTE]
> Alguns serviços do Azure também fornecem uma opção adicional chamada **failover iniciado pelo cliente**, que permite que os clientes iniciem um failover apenas para sua instância, como para executar uma análise de recuperação de desastre. No momento, esse mecanismo **não tem suporte** do Azure digital gêmeos. 

## <a name="monitor-service-health"></a>Monitorar a integridade do serviço

À medida que as instâncias do Azure digital gêmeos têm failover e são recuperadas, você pode monitorar o processo usando a ferramenta de [integridade do serviço do Azure](../service-health/service-health-overview.md) . A integridade do serviço rastreia a integridade dos seus serviços do Azure em diferentes regiões e assinaturas e compartilha comunicações de impacto de serviço sobre interrupções e tempos de inatividade.

Durante um evento de failover, a integridade do serviço pode fornecer uma indicação de quando o serviço está inoperante e quando ele está em backup.

Para exibir eventos de integridade do serviço...
1. Navegue até [integridade do serviço](https://portal.azure.com/?feature.customportal=false#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues) na portal do Azure (você pode usar este link ou pesquisá-lo usando a barra de pesquisa do Portal).
1. Use o menu à esquerda para alternar para a página *histórico de integridade* .
1. Procure um *nome de problema* começando com o **gêmeos digital do Azure** e selecione-o.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/navigate.png" alt-text="Captura de tela da portal do Azure mostrando a página Histórico de integridade. Há uma lista de vários problemas dos últimos dias e um problema chamado ' Azure digital gêmeos-Europa Ocidental-Mitigated ' é realçado." lightbox="media/concepts-high-availability-disaster-recovery/navigate.png":::

1. Para obter informações gerais sobre a interrupção, exiba a guia *Resumo* .

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/summary.png" alt-text="Na página Histórico de integridade, a guia Resumo é realçada. A guia exibe informações gerais, como o recurso que foi afetado, sua região e sua assinatura." lightbox="media/concepts-high-availability-disaster-recovery/summary.png":::
1. Para obter mais informações e atualizações sobre o problema ao longo do tempo, exiba a guia *Issue updates* .

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/issue-updates.png" alt-text="Na página Histórico de integridade, a guia Atualizações de problema é realçada. A guia exibe várias entradas mostrando o status atual de um dia atrás." lightbox="media/concepts-high-availability-disaster-recovery/issue-updates.png":::


Observe que as informações exibidas nesta ferramenta não são específicas para uma instância digital do Azure. Depois de usar a integridade do serviço para entender o que está acontecendo com o serviço de gêmeos digital do Azure em uma determinada região ou assinatura, você pode tomar o monitoramento de um passo além usando a [ferramenta Resource Health](troubleshoot-resource-health.md) para fazer uma busca detalhada em instâncias específicas e ver se elas são afetadas.

## <a name="best-practices"></a>Práticas recomendadas

Para obter as práticas recomendadas de HA/DR, consulte as seguintes diretrizes do Azure neste tópico: 
* O artigo [*orientação técnica da continuidade de negócios do Azure*](/azure/architecture/framework/resiliency/overview) descreve uma estrutura geral para ajudá-lo a pensar na continuidade dos negócios e na recuperação de desastres. 
* O artigo [*recuperação de desastres e alta disponibilidade para aplicativos do Azure*](/azure/architecture/framework/resiliency/backup-and-recovery) fornece diretrizes de arquitetura sobre estratégias para aplicativos do Azure para atingir alta disponibilidade (ha) e recuperação de desastres (Dr).

## <a name="next-steps"></a>Próximas etapas 

Leia mais sobre a introdução às soluções de gêmeos digital do Azure:
 
* [*O que são Gêmeos Digitais do Azure?*](overview.md)
* [*Início rápido: explorar um cenário de exemplo*](quickstart-adt-explorer.md)