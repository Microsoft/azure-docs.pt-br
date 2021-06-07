---
title: Relatório de inteligência de ameaças da Central de Segurança do Azure | Microsoft Docs
description: Esta página ajuda você a usar relatórios de inteligência contra ameaças da central de segurança do Azure durante uma investigação para encontrar mais informações sobre alertas de segurança
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 06/15/2020
ms.author: memildin
ms.openlocfilehash: ec6d227059c3f4fd1285f224e13169a2479bc65f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438226"
---
# <a name="azure-security-center-threat-intelligence-report"></a>Relatório de inteligência contra ameaças da central de segurança do Azure

Esta página explica como os relatórios de inteligência contra ameaças da central de segurança do Azure podem ajudá-lo a saber mais sobre uma ameaça que disparou um alerta de segurança.


## <a name="what-is-a-threat-intelligence-report"></a>O que é um relatório de inteligência de ameaças?

A proteção de ameaças da Central de Segurança funciona monitorando informações de segurança de seus recursos do Azure, de rede e de soluções de parceiros conectados automaticamente. Ele analisa essas informações geralmente correlacionando informações de várias fontes para identificar ameaças. Para obter mais informações, consulte [como a central de segurança do Azure detecta e responde às ameaças](security-center-alerts-overview.md#detect-threats).

Quando a central de segurança identifica uma ameaça, ela dispara um [alerta de segurança](security-center-managing-and-responding-alerts.md), que contém informações detalhadas sobre o evento, incluindo sugestões de correção. Para ajudar as equipes de resposta a incidentes a investigar e corrigir ameaças, a central de segurança fornece relatórios de inteligência contra ameaças que contêm informações sobre ameaças detectadas. O relatório inclui informações como:

* Identidade ou associações do invasor (se essas informações estiverem disponíveis)
* Objetivos dos invasores
* Campanhas de ataque atuais e históricas (se essas informações estiverem disponíveis)
* Táticas, ferramentas e procedimentos dos invasores
* Indicadores associados de comprometimento (IoC), como URLs e hashes de arquivo
* Vitimologia, que é a prevalência do setor e geográfica para auxiliar você na determinação se seus recursos do Azure estão em risco
* Informações de atenuação e correção

> [!NOTE]
> A quantidade de informações em qualquer relatório específico varia; o nível de detalhes baseia-se na atividade e na prevalência do malware.

A Central de Segurança tem três tipos de relatórios de ameaça, que podem variar de acordo com o ataque. Os relatórios disponíveis são:

* **Relatório do grupo de atividades**: fornece aprofundamento nos invasores, seus objetivos e táticas.
* **Relatório de campanha**: concentra-se em detalhes de campanhas de ataque específicas.
* **Relatório de Resumo de ameaças**: cobre todos os itens nos dois relatórios anteriores.

Esse tipo de informação é muito útil durante o processo de resposta a incidentes, em que há uma investigação em andamento para compreender a origem do ataque, as motivações do invasor e o que fazer para atenuar esse problema no futuro.



## <a name="how-to-access-the-threat-intelligence-report"></a>Como acessar o relatório de inteligência de ameaças?

1. Na barra lateral da central de segurança, abra a página **alertas de segurança** .
1. Selecione um alerta. 
    A página de detalhes de alertas é aberta com mais detalhes sobre o alerta. Veja a seguir os **indicadores de ransomware detectados** na página detalhes do alerta.

    [![Indicadores de ransomware detectados página detalhes do alerta](media/security-center-threat-report/ransomware-indicators-detected-link-to-threat-intel-report.png)](media/security-center-threat-report/ransomware-indicators-detected-link-to-threat-intel-report.png#lightbox)

1. Selecione o link para o relatório, e um PDF será aberto no navegador padrão.

    [![Página de detalhes de alerta de ação potencialmente não segura](media/security-center-threat-report/threat-intelligence-report.png)](media/security-center-threat-report/threat-intelligence-report.png#lightbox)

    Opcionalmente, você pode baixar o relatório PDF. 

    >[!TIP]
    > A quantidade de informações disponíveis para cada alerta de segurança varia de acordo com o tipo de alerta.



## <a name="next-steps"></a>Próximas etapas

Esta página explicou como abrir relatórios de inteligência contra ameaças ao investigar alertas de segurança. Para obter informações relacionadas, consulte as seguintes páginas:

* [Gerenciando e respondendo a alertas de segurança na central de segurança do Azure](security-center-managing-and-responding-alerts.md). Saiba como gerenciar e responder aos alertas de segurança.
* [Lidando com incidentes de segurança na central de segurança do Azure](security-center-incident.md)