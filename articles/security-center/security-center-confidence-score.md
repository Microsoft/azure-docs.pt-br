---
title: Pontuação de confiança na Central de Segurança do Azure | Microsoft Docs
description: " Saiba como trabalhar com a pontuação de confiança da Central de Segurança do Azure. "
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e88198f8-2e16-409d-a0b0-a62e68c2f999
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: rkarlin
ms.openlocfilehash: 64bab5c1b99720eecb189834d7a11802cf919ca3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60916556"
---
# <a name="alert-confidence-score"></a>Pontuação de confiança de alerta 

A Central de Segurança do Azure fornece visibilidade dos recursos que você executa no Azure e emite alertas quando detecta possíveis problemas. Com o aumento do volume de alertas, tratar os alertas individualmente pode ser um desafio para a equipe de operações de segurança. Nesse caso, torna-se necessário priorizar quais alertas devem ser investigados. A investigação dos alertas pode ser complexa e demorada, assim, como resultado, alguns alertas podem ser ignorados.

A pontuação de confiança na Central de Segurança pode ajudar a sua equipe a fazer a triagem e priorizar alertas. A Central de Segurança aplica automaticamente as práticas recomendadas do setor, algoritmos inteligentes e processos usados por analistas para determinar se uma ameaça é legítima e fornece insights significativos na forma de uma pontuação de confiança.

## <a name="how-the-confidence-score-is-triggered"></a>Como a pontuação de confiança é disparada

Os alertas são gerados quando processos suspeitos são detectados em execução em suas máquinas virtuais. A Central de Segurança examina e analisa esses alertas nas máquinas virtuais do Windows em execução no Azure. Ela executa verificações e correlações automatizadas usando algoritmos avançados em várias entidades e fontes de dados em toda a organização e em todos os recursos do Azure e apresenta uma pontuação de confiança, que é uma medida de quanto a Central de Segurança confia que o alerta é original e precisa ser investigado.

## <a name="understanding-the-confidence-score"></a>Noções básicas sobre a pontuação de confiança

A pontuação de confiança varia entre 1 e 100 e representa a confiança que a Central de Segurança tem de que o alerta precisa ser investigado. Quanto maior é a pontuação, mais a Central de Segurança confia que o alerta indica atividade mal-intencionada original. A pontuação de confiança inclui uma lista das principais razões pelas quais o alerta recebeu sua pontuação de confiança. A pontuação de confiança facilita para que os analistas de segurança priorizem suas respostas a alertas e lidem com os ataques mais urgentes primeiro, resultando na redução do tempo necessário para responder a ataques e a violações.

Para exibir a pontuação de confiança:
- No portal da Central de Segurança, abra a folha Alertas de segurança.
-  Os alertas e incidentes são organizados do mais alto para o mais baixo, o que significa que quanto mais a Central de Segurança confia que um alerta representa uma ameaça, mais próximo ele está do topo da página. 


 ![Pontuação de confiança][1]

Para exibir os dados que contribuíram para a confiança da Central de Segurança em um alerta:
- Na folha Alerta de segurança, em **Confiança**, exiba as observações que contribuíram para a pontuação de confiança e obtenha insights relacionados ao alerta. Isso fornece mais insights sobre a natureza das atividades que causaram o alerta.

  ![Pontuação de confiança suspeita][2]

Use a pontuação de confiança da Central de Segurança para priorizar a triagem de alertas em seu ambiente. A pontuação de confiança economiza tempo e esforço por investigar automaticamente os alertas, aplicando as práticas recomendadas do setor e algoritmos inteligentes e atuando como um analista virtual para determinar quais ameaças são reais e onde você precisa concentrar sua atenção.


## <a name="next-steps"></a>Próximas etapas
Este artigo explicou como usar a pontuação de confiança para priorizar a investigação de alertas. Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md): encontre perguntas frequentes sobre como usar o serviço.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md)– saiba como monitorar a integridade dos recursos do Azure.



<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
