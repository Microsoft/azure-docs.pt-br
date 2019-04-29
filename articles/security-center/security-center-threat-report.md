---
title: Relatório de inteligência de ameaças da Central de Segurança do Azure | Microsoft Docs
description: Este documento ajuda a usar os Relatórios de Inteligência de Ameaças da Central de Segurança do Azure durante uma investigação para obter mais informações sobre um alerta de segurança.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 5662e312-e8c2-4736-974e-576eeb333484
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: rkarlin
ms.openlocfilehash: ba5ab7ce85933545a41f23e2ecd913acbb7e72d1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60703808"
---
# <a name="azure-security-center-threat-intelligence-report"></a>Relatório de Inteligência de Ameaças da Central de Segurança do Azure
Este documento explica como os Relatórios Inteligentes de Ameças da Central de Segurança do Azure podem ajudá-lo a saber mais sobre uma ameaça que gerou um alerta de segurança.

## <a name="what-is-a-threat-intelligence-report"></a>O que é um relatório de inteligência de ameaças?
A detecção de ameaças da Central de Segurança funciona monitorando informações de segurança de seus recursos do Azure, de rede e de soluções de parceiros conectados automaticamente. Ele analisa essas informações geralmente correlacionando informações de várias fontes para identificar ameaças. Esse processo faz parte dos [recursos de detecção](security-center-detection-capabilities.md) da Central de Segurança.

Quando a Central de Segurança identifica uma ameaça, ele dispara um [alerta de segurança](security-center-managing-and-responding-alerts.md), que contém informações sobre um evento específico, incluindo sugestões de correção detalhadas. Para ajudar as equipes de resposta a incidentes a investigar e a corrigir ameaças, a Central de Segurança inclui um relatório de inteligência de ameaças que contém informações sobre a ameaça detectada, incluindo informações como:

* Identidade ou associações do invasor (se essas informações estiverem disponíveis)
* Objetivos dos invasores
* Campanhas de ataque atuais e históricas (se essas informações estiverem disponíveis)
* Táticas, ferramentas e procedimentos dos invasores
* Indicadores associados de comprometimento (IoC), como URLs e hashes de arquivo
* Vitimologia, que é a prevalência do setor e geográfica para auxiliar você na determinação se seus recursos do Azure estão em risco
* Informações de atenuação e correção

> [!NOTE]
> A quantidade de informações em qualquer relatório específico varia; o nível de detalhes baseia-se na atividade e na prevalência do malware.
>
>

A Central de Segurança tem três tipos de relatórios de ameaça, que podem variar de acordo com o ataque. Os relatórios disponíveis são:

* **Relatório de Grupo de Atividades**: fornece análises avançadas sobre os invasores, seus objetivos e táticas.
* **Relatório de Campanha**: concentra-se nos detalhes de campanhas de ataque específicas.
* **Relatório de Resumo de Ameaças**: abrange todos os itens dos dois relatórios anteriores.

Esse tipo de informação é muito útil durante o processo de [resposta a incidentes](security-center-incident-response.md), em que há uma investigação em andamento para compreender a origem do ataque, as motivações do invasor e o que fazer para atenuar esse problema no futuro.

## <a name="how-to-access-the-threat-intelligence-report"></a>Como acessar o relatório de inteligência de ameaças?
Você pode examinar os alertas atuais observando o bloco **Alertas de segurança** . Abra o portal do Azure e siga as etapas abaixo para ver mais detalhes sobre cada alerta:

1. No painel Central de Segurança, você verá o bloco **Alertas de segurança** .
2. Clique no bloco para abrir a folha **Alertas de segurança** que contém mais detalhes sobre os alertas e clique no alerta de segurança sobre o qual você deseja obter mais informações.

    ![Alertas de segurança](./media/security-center-threat-report/security-center-threat-report-fig1.png)
3. Nesse caso, a folha **Processo suspeito executado** mostra os detalhes sobre o alerta, conforme mostrado na figura a seguir:

    ![Detalhes do alerta de segurança](./media/security-center-threat-report/security-center-threat-report-fig2.png)
4. A quantidade de informações disponíveis para cada alerta de segurança varia de acordo com o tipo de alerta. No campo **RELATÓRIOS**, você tem um link para o relatório de inteligência de ameaça. Clique nele e outra janela do navegador será exibida com o arquivo PDF.

   ![Seleção de armazenamento](./media/security-center-threat-report/security-center-threat-report-fig3.png)

Aqui você pode baixar o PDF para esse relatório e ler mais sobre o problema de segurança detectado e executar ações com base nas informações fornecidas.

## <a name="see-also"></a>Consulte também
Neste documento, você aprendeu como os Relatórios de Inteligência de Ameaças da Central de Segurança do Azure podem ajudar durante uma investigação sobre alertas de segurança. Para saber mais sobre a Central de Segurança do Azure, veja o seguinte:

* [Perguntas Frequentes sobre a Central de Segurança do Azure](security-center-faq.md). Encontre as perguntas frequentes sobre como usar o serviço.
* [Aproveitando a Central de Segurança do Azure para a resposta a incidentes](security-center-incident-response.md)
* [Recursos de detecção da Central de Segurança do Azure](security-center-detection-capabilities.md)
* [Guia de planejamento e operações da Central de Segurança do Azure](security-center-planning-and-operations-guide.md). Saiba como planejar e entender as considerações de design para adotar a Central de Segurança do Azure.
* [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md). Saiba como gerenciar e responder aos alertas de segurança.
* [Manipulação de incidente de segurança na Central de Segurança do Azure](security-center-incident.md)
* [Blog de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/). Encontre postagens no blog sobre a conformidade e segurança do Azure.
