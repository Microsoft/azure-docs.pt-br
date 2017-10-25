---
title: "Práticas recomendadas da solução OMSManagement | Microsoft Docs"
description: 
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: bwren
ms.openlocfilehash: b3d07ad3164609a5628c0d9805de55a32870ab94
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="best-practices-for-creating-management-solutions-in-operations-management-suite-oms-preview"></a>Práticas recomendadas para a criação de soluções de gerenciamento no OMS (Operations Management Suite) (Preview)
> [!NOTE]
> Esta é uma documentação preliminar para criar soluções de gerenciamento no OMS, que estão atualmente em visualização. Os esquemas descritos a seguir estão sujeitos a alterações.  

Este artigo apresenta as práticas recomendadas para a [criação de um arquivo de solução de gerenciamento](operations-management-suite-solutions-solution-file.md) no OMS (Operations Management Suite).  Essas informações serão atualizadas à medida que práticas recomendadas adicionais forem identificadas.

## <a name="data-sources"></a>Fontes de dados
- As fontes de dados podem ser [configuradas com um modelo do Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), mas não devem ser incluídas em um arquivo de solução.  Isso porque configurar fontes de dados, atualmente, não é idempotente, o que significa que a solução pode substituir a configuração existente no espaço de trabalho do usuário.<br><br>Por exemplo, a solução pode exigir eventos de Aviso e Erro do log de eventos do Aplicativo.  Caso especifique isso como uma fonte de dados em sua solução, você correrá o risco de remover eventos de Informações se o usuário tiver configurado isso no respectivo espaço de trabalho.  Caso tenha incluído todos os eventos, talvez você esteja coletando eventos de Informações excessivos no espaço de trabalho do usuário.

- Se a solução exigir dados de uma das fontes de dados padrão, você deverá definir isso como um pré-requisito.  Determine na documentação que o cliente deverá configurar a fonte de dados por sua própria conta.  
- Adicione uma mensagem de [Verificação do Fluxo de Dados](../log-analytics/log-analytics-view-designer-tiles.md) a todas as exibições em sua solução a fim de orientar o usuário sobre as fontes de dados que precisam ser configuradas para que os dados necessários sejam coletados.  Essa mensagem será mostrada no bloco da exibição quando os dados necessários não forem encontrados.


## <a name="runbooks"></a>Runbooks
- Adicione um [Agendamento de automação](../automation/automation-schedules.md) para cada runbook da solução que precise ser executado em um agendamento.
- Inclua na solução o [módulo IngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) a ser usado pelos runbooks que gravam dados no repositório do Log Analytics.  Configure a solução para [fazer referência](operations-management-suite-solutions-solution-file.md#solution-resource) a esse recurso, de modo que ele permaneça caso a solução seja removida.  Isso permite que várias soluções compartilhem o módulo.
- Use [Variáveis de automação](../automation/automation-schedules.md) para fornecer à solução valores que os usuários talvez queiram mudar posteriormente.  Mesmo que a solução esteja configurada para conter a variável, seu valor ainda poderá ser alterado.

## <a name="views"></a>Modos de exibição
- Todas as soluções devem incluir uma única exibição que é mostrada no portal do usuário.  A exibição pode conter várias [partes de visualização](../log-analytics/log-analytics-view-designer-parts.md) para ilustrar diferentes conjuntos de dados.
- Adicione uma mensagem de [Verificação do Fluxo de Dados](../log-analytics/log-analytics-view-designer-tiles.md) a todas as exibições em sua solução a fim de orientar o usuário sobre as fontes de dados que precisam ser configuradas para que os dados necessários sejam coletados.
- Configure a solução para [conter](operations-management-suite-solutions-solution-file.md#solution-resource) a exibição, de modo que ela seja removida caso a solução seja removida.

## <a name="alerts"></a>Alertas
- Defina a lista de destinatários como um parâmetro no arquivo de solução para que o usuário possa defini-lo quando instala a solução.
- Configure a solução para [fazer referência](operations-management-suite-solutions-solution-file.md#solution-resource) a regras de alerta, de modo que os usuários possam alterar a configuração delas.  Talvez eles queiram modificar a lista de destinatários, mudar o limite do alerta ou desabilitar a regra de alerta. 


## <a name="next-steps"></a>Próximas etapas
* Veja o passo a passo do processo básico de [projetar e criar uma solução gerenciada](operations-management-suite-solutions-creating.md).
* Saiba como [criar um arquivo de solução](operations-management-suite-solutions-solution-file.md).
* [Adicionar alertas e pesquisas salvas](operations-management-suite-solutions-resources-searches-alerts.md) à sua solução de gerenciamento.
* [Adicionar exibições](operations-management-suite-solutions-resources-views.md) à sua solução de gerenciamento.
* [Adicionar runbooks de automação e outros recursos](operations-management-suite-solutions-resources-automation.md) à sua solução de gerenciamento.

