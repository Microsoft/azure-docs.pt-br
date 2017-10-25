---
title: "Visão geral das Análise de Séries Temporais do Azure | Microsoft Docs"
description: "Introdução à Análise de Séries Temporais do Azure: um novo serviço para soluções de IoT e de análises de dados de séries temporais"
keywords: 
services: tsi
documentationcenter: 
author: op-ravi
manager: jhubbard
editor: 
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/20/2017
ms.author: omravi
ms.openlocfilehash: 1814459e47280af62450a4093140ab6ab9b765fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-time-series-insights"></a>O que é o Azure Time Series Insights

O Azure Time Series Insights é um serviço de nuvem totalmente gerenciado com componentes de armazenamento, análise e visualização que facilitam muito ingerir, armazenar, explorar e analisar bilhões de eventos simultaneamente. O Azure Time Series Insights oferece uma visão global dos dados, permitindo que você valide rapidamente suas Soluções de IoT e evite o tempo de inatividade dispendioso ajudando você a descobrir tendências ocultas e anomalias e realizar análises de causa raiz em tempo quase real. A Análise de Séries Temporais ingerem os dados de série temporal de agentes de evento (por exemplo, Hubs IoT ou Hubs de eventos), indexa os dados e desativa os dados com base em uma política de retenção configurável. Os usuários consomem os dados por meio de uma experiência do usuário intuitiva ou APIs de Consulta REST.

![Visão geral da Análise de Séries Temporais](media/overview/time-series-insights-overview-flow.png)

## <a name="primary-scenarios"></a>Principais cenários

* Monitorar e validar soluções de IoT em minutos.
* Visualizar e analisar dados IoT em escala.
* Acelerar a detecção de anomalias e análise de causas raiz.
* Criar uma exibição global de vários dispositivos, plantas e dados.

## <a name="capabilities-and-benefits"></a>Recursos e benefícios

* **Fácil de iniciar**: o Azure Time Series Insights não exige nenhuma preparação de dados inicial e é inacreditavelmente rápido. Conecte-se a bilhões de eventos em seu Hub IoT do Azure ou Hub de Eventos em minutos. Uma vez conectado, visualize e interaja com os dados do sensor em segundos para validar rapidamente suas soluções de IoT. O Time Series Insights é fácil de usar. Você pode interagir com os seus dados sem ter que escrever uma única linha de código.  Não há nenhuma nova linguagem a aprender. O Time Series Insights fornece uma superfície de consulta de texto livre granular para usuários avançados, assim como a exploração do tipo apontar e clicar, para todos.

* **Análises em tempo quase real**: o Time Series Insights pode incluir centenas de milhões de eventos de sensor por dia, com latência de um minuto, para que você possa reagir às alterações rapidamente. O Time Series Insights ajuda você a realizar análises profundas em seus dados de sensor ajudando-o a identificar tendências e anomalias rapidamente, realize facilmente análises complexas de causas raiz e evitar um tempo de inatividade dispendioso. Ao habilitar a correlação cruzada entre dados de histórico e em tempo real, o Time Series Insights ajuda você a desbloquear tendências ocultas nos dados.

* **Crie soluções personalizadas**: Integre a Análise de Séries Temporais do Azure aos seus aplicativos existentes ou crie novas soluções personalizadas com as APIs de consulta REST da Análise de Séries Temporais. Como criar e compartilhar exibições personalizadas que você pode compartilhar para que outras pessoas explorem suas descobertas.

* **Escalabilidade**: o Time Series Insights foi projetado para dar suporte à IoT em escala. Na Versão Prévia, é possível inserir de 1 milhão a 100 milhões de eventos por dia, com um período de tempo de retenção padrão de 31 dias. Você pode visualizar e analisar os fluxos de dados ativos quase em tempo real, juntamente com grandes quantidades de dados de histórico. Mais adiante, taxas de entrada e retenção aumentarão para acomodar uma escala empresarial em constante evolução.

## <a name="time-series-insights-glossary"></a>Glossário do Time Series Insights

* **Ambiente**: Um é um recurso do Azure com capacidade de entrada e armazenamento.  Os clientes provisionam os ambientes por meio do portal do Azure com a capacidade necessária.
* **Origem de Evento**: Uma Origem de Evento é derivada de um agente de evento, como o Hubs de Eventos do Azure.  O Time Series Insights se conecta diretamente às Origens de Eventos, ingerindo o fluxo de dados sem escrever uma única linha de código. Atualmente, a Análise de Séries Temporais dá suporte a Hubs de Eventos do Azure e a Hubs IoT do Azure.
* **Dados de referência**: A Análise de Séries Temporais fornece aos usuários a capacidade de unir dados de séries temporais com os dados de referência.  Os dados de referência podem incluir metadados sobre dispositivos ou outros dados estáticos que não são alterados com tanta frequência. A Análise de Séries Temporais une dados de referência com fluxos de dados, permitindo aos usuários visualizar e analisar esses dados quase em tempo real.
