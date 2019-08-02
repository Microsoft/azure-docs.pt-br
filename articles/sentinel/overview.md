---
title: O que é a versão prévia do Azure Sentinel? | Microsoft Docs
description: Saiba mais sobre o Azure Sentinel, seus principais recursos e como ele funciona.
services: sentinel
documentationcenter: na
author: rkarlin
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
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 2c730c240ed5ff68a1d48ef17d15de1aaa73ab1e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67619916"
---
# <a name="what-is-azure-sentinel-preview"></a>O que é a versão prévia do Azure Sentinel?

> [!IMPORTANT]
> No momento, o Azure Sentinel está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O Microsoft Azure Sentinel é uma solução escalonável e nativa de nuvem para **gerenciamento de eventos e informações de segurança (SIEM)** e **resposta automatizada de orquestração de segurança (SOAR)** . O Azure Sentinel oferece análise inteligente de segurança e inteligência contra ameaças em toda a empresa, fornecendo uma única solução para detecção de alertas, visibilidade de ameaças, procura proativa e resposta a ameaças. 

O Azure Sentinel é sua exibição geral da empresa, amenizando o estresse de ataques cada vez mais sofisticados, volumes crescentes de alertas e longos períodos de resolução.

- **Colete dados na escala de nuvem** de todos os usuários, dispositivos, aplicativos e infraestrutura, local e em múltiplas nuvens. 

- **Detecte ameaças não detectadas antes** e minimize falsos positivos usando a análise a e inteligência contra ameaças inigualáveis da Microsoft. 

- **Investigue ameaças com inteligência artificial** e busque por atividades suspeitas em escala, acessando anos de trabalho sobre segurança cibernética na Microsoft. 

- **Responda a incidentes de forma rápida** com orquestração interna e automação de tarefas comuns.


![Principais recursos do Azure Sentinel](./media/overview/core-capabilities.png)

Aproveitando a gama completa de serviços existentes do Azure, o Azure Sentinel incorpora nativamente bases comprovadas, como Log Analytics e Aplicativos Lógicos. O Azure Sentinel enriquece a investigação e a detecção com IA, além de oferecer um fluxo de inteligência contra ameaças da Microsoft e permitir que você traga sua própria inteligência contra ameaças. 

 
## <a name="connect-to-all-your-data"></a>Conecte-se a todos os seus dados

Para a integração do Azure Sentinel, você precisa primeiro [se conectar às suas fontes de segurança](connect-data-sources.md). O Azure Sentinel vem com uma série de conectores para soluções da Microsoft, disponíveis prontas para o uso e fornecendo integração em tempo real, incluindo soluções de Proteção contra Ameaças da Microsoft, bem como fontes do Microsoft 365, como Office 365, Azure AD, ATP do Azure, Microsoft Cloud App Security e muito mais. Além disso, existem conectores internos no ecossistema de segurança mais amplo para soluções que não são da Microsoft. Você também pode usar formato comum de eventos, Syslog ou API REST para conectar suas fontes de dados ao Azure Sentinel.  

![Coletores de dados](./media/collect-data/collect-data-page.png)

## <a name="dashboards"></a>Painéis

Depois de conectar fontes de dados, é possível escolher em uma galeria de [painéis criados com habilidade](quickstart-get-visibility.md#dashboards) que produzem insights de suas fontes de dados. Cada painel é totalmente personalizável. Você pode adicionar sua própria lógica, modificar consultas ou criar um painel do zero.

Os painéis fornecem visualização interativa usando análise avançada para ajudar seus analistas de segurança a entender melhor o que está acontecendo durante um ataque. Com as ferramentas de investigação, é possível se aprofundar em qualquer campo, com base em quaisquer dados, a fim de desenvolver rapidamente o contexto da ameaça. 

![Painéis](./media/overview/dashboards.png)

## <a name="analytics"></a>Análise

Para ajudar a reduzir o ruído e minimizar o número de alertas que você precisa revisar e investigar, o Azure Sentinel usa [análise para correlacionar alertas a casos](tutorial-detect-threats.md). **Casos** são grupos de alertas relacionados que, juntos, criam uma ameaça acionável possível que você pode investigar e resolver. Use as regras de correlação internas no estado em que se encontram ou use-as como ponto de partida para criar suas próprias. O Azure Sentinel também fornece regras de aprendizado de máquina para mapear o comportamento da rede e, em seguida, buscar anomalias em todos os seus recursos. Essas análises ligam os pontos, pois combinam alertas de baixa fidelidade sobre diferentes entidades em possíveis incidentes de segurança de alta fidelidade.

![Casos](./media/overview/cases.png)

## <a name="user-analytics"></a>Análise do usuário

Com a integração nativa de aprendizado de máquina (ML) e a [análise de usuário](user-analytics.md), o Azure Sentinel pode ajudar a detectar ameaças rapidamente. O Azure Sentinel integra-se perfeitamente com a Proteção Avançada contra Ameaças do Azure para analisar o comportamento do usuário e priorizar quais usuários você deve investigar primeiro, com base nos alertas e padrões de atividade suspeita no Azure Sentinel e no Microsoft 365.

![Análise do usuário](./media/overview/user-analytics.png)


## <a name="security-automation--orchestration"></a>Automação e orquestração de segurança

Automatize as tarefas comuns e [simplifique a orquestração de segurança com guias estratégicos](tutorial-respond-threats-playbook.md) que se integram com os serviços do Azure, assim como com suas ferramentas existentes. Desenvolvida com base nos Aplicativos Lógicos do Azure, a solução de automação e orquestração do Azure Sentinel oferece uma arquitetura altamente extensível que possibilita a automação escalonável à medida que surgem novas tecnologias e ameaças. Para criar guias estratégicos com os Aplicativos Lógicos do Azure, você pode escolher em uma galeria crescente de guias estratégicos internos. Eles incluem [mais de 200 conectores](https://docs.microsoft.com/azure/connectors/apis-list) para serviços como o Azure Functions. Os conectores permitem que você aplique qualquer lógica personalizada em código, ServiceNow, Jira, Zendesk, solicitações HTTP, Microsoft Teams, Slack, Windows Defender ATP e Cloud App Security.

Por exemplo, se você usar o sistema de emissão de tíquetes do ServiceNow, poderá usar as ferramentas fornecidas para usar Aplicativos Lógicos do Azure a fim de automatizar os fluxos de trabalho e abrir um tíquete no ServiceNow cada vez que um evento específico é detectado.

![Guias estratégicos](./media/tutorial-respond-threats-playbook/logic-app.png)



## <a name="investigation"></a>Investigação

As ferramentas de [investigação profunda](tutorial-investigate-cases.md) do Azure Sentinel ajudam a entender o escopo e a encontrar a causa raiz de uma possível ameaça à segurança. Você pode escolher uma entidade no gráfico interativo para fazer perguntas interessantes para uma entidade específica, além de fazer uma busca detalhada nessa entidade e em suas conexões para chegar à causa raiz da ameaça. 

![Investigação](./media/overview/investigation.png)


## <a name="hunting"></a>Buscando

Use as [ferramentas de pesquisa e consulta para buscas eficientes](hunting.md) do Azure Sentinel, baseadas na estrutura MITRE, que permitem buscar proativamente ameaças à segurança nas fontes de dados de sua organização, antes do disparo de um alerta. Depois de descobrir qual consulta de busca fornece insights de alto valor sobre possíveis ataques, também é possível criar regras de detecção personalizadas baseadas em sua consulta e exibir tais insights como alertas para seus respondentes de incidentes de segurança. Você pode criar indicadores para eventos interessantes durante a busca. Assim, poderá retornar a eles posteriormente, compartilhá-los com outras pessoas e agrupá-los com outros eventos correlacionados para criar um argumento convincente para investigação.

![Buscando](./media/overview/hunting.png)

## <a name="community"></a>Comunidade

A comunidade do Azure Sentinel é um recurso eficiente para automação e detecção de ameaças. Os analistas de segurança da Microsoft criam e adicionam constantemente novos painéis, guias estratégicos, consultas de busca e muito mais, publicando-os na comunidade para uso em seu ambiente. Você pode baixar o conteúdo de exemplo no [repositório](https://aka.ms/asicommunity) GitHub particular da comunidade para criar painéis personalizados, consultas de busca, notebooks e guias estratégicos para o Azure Sentinel. 

![Comunidade](./media/overview/community.png)

## <a name="next-steps"></a>Próximas etapas

- Para começar a usar o Azure Sentinel, você precisa ter uma assinatura do Microsoft Azure. Se você não tiver uma assinatura, você pode se inscrever em uma [avaliação gratuita](https://azure.microsoft.com/free/).
- Saiba como [integrar seus dados ao Azure Sentinel](quickstart-onboard.md) e [obtenha visibilidade de seus dados, além de possíveis ameaças](quickstart-get-visibility.md).
