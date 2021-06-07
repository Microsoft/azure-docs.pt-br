---
title: O que é o Azure Sentinel? | Microsoft Docs
description: Saiba mais sobre o Azure Sentinel, uma solução escalonável e nativa de nuvem de SIEM (gerenciamento de eventos e informações de segurança) e SOAR (resposta automatizada de orquestração de segurança).
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: 4bc5732c1b2349f34297ecaacc7eb3176fdba611
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100570490"
---
# <a name="what-is-azure-sentinel"></a>O que é o Azure Sentinel?

O Microsoft Azure Sentinel é uma solução escalonável e nativa de nuvem para **gerenciamento de eventos e informações de segurança (SIEM)** e **resposta automatizada de orquestração de segurança (SOAR)** . O Azure Sentinel oferece análise inteligente de segurança e inteligência contra ameaças em toda a empresa, fornecendo uma única solução para detecção de alertas, visibilidade de ameaças, procura proativa e resposta a ameaças. 

O Azure Sentinel é sua exibição geral da empresa, amenizando o estresse de ataques cada vez mais sofisticados, volumes crescentes de alertas e longos períodos de resolução.

- **Colete dados na escala de nuvem** de todos os usuários, dispositivos, aplicativos e infraestrutura, local e em múltiplas nuvens. 

- **Detecte ameaças não detectadas antes** e minimize falsos positivos usando a análise e a inteligência contra ameaças incomparáveis da Microsoft. 

- **Investigue ameaças com inteligência artificial** e busque por atividades suspeitas em escala, acessando anos de trabalho sobre segurança cibernética na Microsoft. 

- **Responda a incidentes de forma rápida** com orquestração interna e automação de tarefas comuns.

![Principais recursos do Azure Sentinel](./media/overview/core-capabilities.png)

Aproveitando a gama completa de serviços existentes do Azure, o Azure Sentinel incorpora nativamente bases comprovadas, como Log Analytics e Aplicativos Lógicos. O Azure Sentinel enriquece a investigação e a detecção com IA, além de oferecer o fluxo de inteligência da Microsoft contra ameaças e permitir que você use sua própria inteligência contra ameaças. 

## <a name="connect-to-all-your-data"></a>Conecte-se a todos os seus dados

Para a integração do Azure Sentinel, você precisa primeiro [se conectar às suas fontes de segurança](connect-data-sources.md). O Azure Sentinel vem com vários conectores para soluções da Microsoft, disponíveis prontamente e fornecendo integração em tempo real, incluindo as soluções do Microsoft 365 Defender (anteriormente conhecido como Proteção contra Ameaças da Microsoft) e fontes do Microsoft 365, incluindo o Office 365, Azure AD, Microsoft Defender para Identidade (anteriormente, ATP do Azure), Microsoft Cloud App Security e muito mais. Além disso, existem conectores internos no ecossistema de segurança mais amplo para soluções que não são da Microsoft. Você também pode usar formato comum de eventos, Syslog ou API REST para conectar suas fontes de dados ao Azure Sentinel. 

![Coletores de dados](./media/collect-data/collect-data-page.png)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="workbooks"></a>Pastas de trabalho

Depois de [conectar suas fontes de dados](quickstart-onboard.md) ao Azure Sentinel, você poderá monitorar os dados usando a integração do Azure Sentinel às Pastas de Trabalho do Azure Monitor, que oferece versatilidade na criação de pastas de trabalho personalizadas. Embora as Pastas de Trabalho sejam exibidas de maneiras diferentes no Azure Sentinel, pode ser útil para você ver como [Criar relatórios interativos com as Pastas de Trabalho do Azure Monitor](../azure-monitor/visualize/workbooks-overview.md). O Azure Sentinel permite que você crie pastas de trabalho personalizadas em seus dados e também vem com modelos de pasta de trabalho internos para que você possa obter insights rapidamente em seus dados assim que você conectar uma fonte de dados.

![Painéis](./media/tutorial-monitor-data/access-workbooks.png)

## <a name="analytics"></a>Análise

Para ajudar a reduzir o ruído e a minimizar o número de alertas que você precisa examinar e investigar, o Azure Sentinel usa [análise para correlacionar os alertas aos incidentes](tutorial-detect-threats-built-in.md). **Incidentes** são grupos de alertas relacionados que, juntos, criam uma possível ameaça acionável que você pode investigar e resolver. Use as regras de correlação internas no estado em que se encontram ou use-as como ponto de partida para criar suas próprias. O Azure Sentinel também fornece regras de aprendizado de máquina para mapear o comportamento da rede e, em seguida, buscar anomalias em todos os seus recursos. Essas análises ligam os pontos, pois combinam alertas de baixa fidelidade sobre diferentes entidades em possíveis incidentes de segurança de alta fidelidade.

![Incidentes](./media/tutorial-investigate-cases/incident-severity.png)


## <a name="security-automation--orchestration"></a>Automação e orquestração de segurança

Automatize as tarefas comuns e [simplifique a orquestração de segurança com guias estratégicos](tutorial-respond-threats-playbook.md) que se integram com os serviços do Azure, assim como com suas ferramentas existentes. Desenvolvida com base nos Aplicativos Lógicos do Azure, a solução de automação e orquestração do Azure Sentinel oferece uma arquitetura altamente extensível que possibilita a automação escalonável à medida que surgem novas tecnologias e ameaças. Para criar guias estratégicos com os Aplicativos Lógicos do Azure, você pode escolher em uma galeria crescente de guias estratégicos internos. Eles incluem [mais de 200 conectores](../connectors/apis-list.md) para serviços como o Azure Functions. Os conectores permitem que você aplique qualquer lógica personalizada em código, ServiceNow, Jira, Zendesk, solicitações HTTP, Microsoft Teams, Slack, Windows Defender ATP e Cloud App Security.

Por exemplo, se você usar o sistema de emissão de tíquetes do ServiceNow, poderá usar as ferramentas fornecidas para usar Aplicativos Lógicos do Azure a fim de automatizar os fluxos de trabalho e abrir um tíquete no ServiceNow cada vez que um evento específico é detectado.

![Guias estratégicos](./media/tutorial-respond-threats-playbook/logic-app.png)


## <a name="investigation"></a>Investigação

Em versão prévia no momento, as ferramentas de [investigação profunda](tutorial-investigate-cases.md) do Azure Sentinel ajudam a entender o escopo e a encontrar a causa raiz de uma possível ameaça à segurança. Você pode escolher uma entidade no gráfico interativo para fazer perguntas interessantes para uma entidade específica, além de fazer uma busca detalhada nessa entidade e em suas conexões para chegar à causa raiz da ameaça. 

![Investigação](./media/tutorial-investigate-cases/map-timeline.png)


## <a name="hunting"></a>Buscando

Use as [ferramentas de pesquisa e consulta para buscas eficientes](hunting.md) do Azure Sentinel, baseadas na estrutura MITRE, que permitem buscar proativamente ameaças à segurança nas fontes de dados de sua organização, antes do disparo de um alerta. Depois de descobrir qual consulta de busca fornece insights de alto valor sobre possíveis ataques, crie também regras de detecção personalizadas baseadas na sua consulta e revele esses insights como alertas de incidentes de segurança para seus respondentes. É possível criar indicadores para eventos interessantes durante a busca. Assim, poderá retornar a eles posteriormente, compartilhá-los com outras pessoas e agrupá-los com outros eventos correlacionados para criar um incidente convincente para investigação.

![Visão geral do recurso de busca](./media/overview/hunting.png)

## <a name="community"></a>Comunidade

A comunidade do Azure Sentinel é um recurso eficiente para automação e detecção de ameaças. Os analistas de segurança da Microsoft criam e adicionam constantemente novas pastas de trabalho, guias estratégicos, consultas de busca e muito mais, publicando-os na comunidade para uso em seu ambiente. É possível baixar o conteúdo de exemplo no [repositório](https://aka.ms/asicommunity) GitHub privado da comunidade para criar pastas de trabalho personalizadas, consultas de busca, notebooks e guias estratégicos para o Azure Sentinel. 

![Explore a comunidade de usuários](./media/overview/community.png)

## <a name="next-steps"></a>Próximas etapas

- Para começar a usar o Azure Sentinel, você precisa ter uma assinatura do Microsoft Azure. Se você não tiver uma assinatura, você pode se inscrever em uma [avaliação gratuita](https://azure.microsoft.com/free/).
- Saiba como [integrar seus dados ao Azure Sentinel](quickstart-onboard.md) e [obtenha visibilidade de seus dados e de possíveis ameaças](quickstart-get-visibility.md).