---
title: Gerenciamento de alertas nos produtos de monitoramento da Microsoft | Microsoft Docs
description: "Um alerta indica algum problema que requer atenção de um administrador.  Este artigo descreve as diferenças na forma como os alertas são criados e gerenciados no SCOM (System Center Operations Manager) e no Log Analytics, além de fornecer as melhores práticas ao aproveitar os dois produtos para uma estratégia de gerenciamento de alertas híbridos."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6572c3f8-78ca-4fa9-8fe1-d0b488590788
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: 7df2fd7ef838465a60e3b0ce2f889127b7487684
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="managing-alerts-with-microsoft-monitoring"></a>Gerenciando alertas com o monitoramento da Microsoft
Um alerta indica algum problema que requer atenção de um administrador.  Há diferenças marcantes entre o SCOM (System Center Operations Manager) e o Log Analytics no OMS (Operations Management Suite) em termos de como os alertas são criados, como são gerenciados e analisados e como você será notificado de que um problema crítico foi detectado.

## <a name="alerts-in-operations-manager"></a>Alertas no Operations Manager
Os alertas no SCOM são gerados por regras individuais ou monitores para indicar um problema específico.  Um monitor pode gerar um alerta quando ele entra em um estado de erro, enquanto uma regra pode gerar um alerta para indicar algum problema crítico que não está diretamente relacionado à integridade de um objeto gerenciado.  Os pacotes de gerenciamento incluem uma variedade de fluxos de trabalho que criam alertas para o aplicativo ou serviço que eles gerenciam.  Parte do processo de configuração de um novo pacote de gerenciamento trata de ajustá-lo, a fim de garantir que você não receberá alertas excessivos para problemas que não são considerados críticos.

![Exibição de alertas do SCOM](media/operations-management-suite-monitoring-alerts/scom-alert-view.png)

O SCOM fornece um gerenciamento completo de alertas com os alertas tendo um status que pode ser alterado pelos administradores, à medida que trabalham para resolver o problema.  Quando o problema tiver sido resolvido, o administrador definirá o alerta como fechado e, a partir desse momento, ele não será mais exibido nos modos de exibição que mostram os alertas ativos.  Os alertas gerados pelos monitores podem ser resolvidos automaticamente quando o monitor retorna a um estado íntegro.

![Status do alerta](media/operations-management-suite-monitoring-alerts/scom-alert-status.png)

## <a name="alerts-in-log-analytics"></a>Alertas no Log Analytics
Um alerta no Log Analytics é criado com base em uma consulta de log que é executada automaticamente em intervalos regulares.  É possível criar uma regra de alerta com base em qualquer consulta de log.  Caso a consulta retorne resultados que correspondam aos critérios especificados, um alerta será criado.  Isso poderia ser uma consulta específica que criaria um alerta se um evento específico fosse detectado ou você poderia usar uma consulta mais geral que procura qualquer evento de erro relacionado a determinado aplicativo.

Os alertas no Log Analytics são gravados no repositório do OMS como um evento e podem ser recuperados com uma consulta de log.  Eles não têm um status como os eventos do SCOM, para que você possa indicar quando o problema foi resolvido.

![Alerta do OMS](media/operations-management-suite-monitoring-alerts/oms-alert.png)

Quando o SCOM é usado como uma fonte de dados para o Log Analytics, os alertas do SCOM são gravados no repositório do OMS conforme são criados e modificados.  

![Alerta do SCOM](media/operations-management-suite-monitoring-alerts/scom-alert.png)

A [solução de Gerenciamento de Alertas](http://technet.microsoft.com/library/mt484092.aspx) fornece um resumo de alertas ativos e diversas consultas comuns para recuperar conjuntos diferentes de alertas.  Isso proporciona uma análise mais eficiente de seus alertas quando comparado a um relatório no SCOM.  Você pode fazer uma busca detalhada com base nos resumos para obter dados detalhados e criar consultas ad hoc para recuperar conjuntos diferentes de alertas.

![solução de Gerenciamento de Alertas](media/operations-management-suite-monitoring-alerts/alert-management.png)

## <a name="notifications"></a>Notificações
As notificações no SCOM enviam um email ou texto em resposta aos alertas que correspondem a critérios específicos.  Você pode criar assinaturas de notificação diferentes que notifica pessoas diferentes, dependendo de critérios como o objeto monitorado, a severidade do alerta, o tipo de problema detectado ou a hora do dia.

Poucas assinaturas podem ser usadas para implementar uma estratégia de notificação de conclusão para um grande número de pacotes de gerenciamento.

![Alertas do SCOM](media/operations-management-suite-monitoring-alerts/alerts-overview-scom.png)

O Log Analytics pode notificá-lo por email de que um alerta foi criado, definindo uma ação de notificação de email em cada [regra de alerta](http://technet.microsoft.com/library/mt614775.aspx).  Ele não tem a capacidade do SCOM de fazer assinar vários alertas com uma única regra.  Também é necessário criar suas próprias regras de alerta, pois o OMS não fornece nenhuma pré-configurada.

![Alertas do Log Analytics](media/operations-management-suite-monitoring-alerts/alerts-overview-oms.png)

Contudo, não é possível gerenciar por completo os alertas do SCOM no Log Analytics, já que você pode apenas modificá-los no Console de Operações.  Apesar disso, o Log Analytics é útil como parte de um processo de gerenciamento de alertas, para fornecer ferramentas de análise das quais o SCOM por si só não dispõe.

## <a name="alert-remediation"></a>Correção de alertas
[Correção](http://technet.microsoft.com/library/mt614775.aspx) refere-se a uma tentativa de corrigir automaticamente o problema identificado por um alerta.

O SCOM permite executar Diagnóstico e Recuperações em resposta a um monitor que entra em um estado não íntegro.  Isso ocorre simultaneamente à criação do alerta pelo monitor.  Em geral, o diagnóstico e as recuperações são implementados como um script executado no agente.  Um diagnóstico tenta coletar mais informações sobre o problema detectado, ao passo que uma recuperação tenta corrigir o problema.

O Log Analytics permite iniciar um [runbook da Automação do Azure](https://azure.microsoft.com/documentation/services/automation/) ou chamar um webhook em resposta a um alerta do Log Analytics.  Os runbooks podem conter uma lógica complexa implementada no PowerShell.  O script é executado no Azure e pode acessar quaisquer recursos do Azure ou recursos externos disponíveis na nuvem.  A Automação do Azure tem a capacidade de executar runbooks em um servidor em seu data center local, mas esse recurso não está disponível no momento ao iniciar o runbook em resposta a alertas do Log Analytics.

Tanto as recuperações no SCOM quanto os runbooks em OMS podem conter scripts do PowerShell, porém, as recuperações são mais difíceis de ser criadas e gerenciadas, já que devem estar contidas em um pacote de gerenciamento.  Os runbooks são armazenados na Automação do Azure, que fornece recursos para criação, teste e gerenciamento de runbooks.

Se você usar o SCOM como uma fonte de dados para o Log Analytics, você poderá criar um alerta do Log Analytics usando uma consulta de log para recuperar os alertas do SCOM armazenados no repositório do OMS.  Isso permitirá que você execute um runbook da Automação do Azure em resposta a um alerta do SCOM.  Evidentemente, como o runbook será executado no Azure, essa não seria uma estratégia viável para recuperações de problemas locais.

## <a name="next-steps"></a>Próximas etapas
* Conheça os detalhes dos [alertas no SCOM (System Center Operations Manager)](https://technet.microsoft.com/library/hh212913.aspx).

