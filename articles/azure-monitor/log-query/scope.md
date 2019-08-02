---
title: Escopo de consulta de log no Log Analytics do Azure Monitor | Microsoft Docs
description: Descreve o intervalo de tempo e de escopo para uma consulta de log no Log Analytics do Azure Monitor.
services: log-analytics
author: bwren
manager: carmonm
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: bwren
ms.openlocfilehash: a948b80f6524339f0908a2fb19c4a83d70b3b140
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67297314"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Intervalo de tempo e de escopo de consulta de log no Log Analytics do Azure Monitor
Quando você executa um [consulta de log](log-query-overview.md) na [Log Analytics no portal do Azure](get-started-portal.md), o conjunto de dados avaliados pela consulta depende do escopo e o intervalo de tempo que você selecionar. Este artigo descreve o intervalo de tempo e de escopo e como você pode definir cada dependendo dos seus requisitos. Ele também descreve o comportamento de diferentes tipos de escopos.


## <a name="query-scope"></a>Escopo de consulta
O escopo de consulta define os registros que são avaliados pela consulta. Geralmente, isso incluirá todos os registros em um único espaço de trabalho do Log Analytics ou o aplicativo de Application Insights. Log Analytics também permite que você defina um escopo para um determinado recurso do Azure monitorado. Isso permite que o proprietário do recurso para se concentrar apenas em seus dados, mesmo que esse recurso grava em vários espaços de trabalho.

O escopo será sempre exibido na parte superior esquerda da janela do Log Analytics. Um ícone indica se o escopo é um espaço de trabalho do Log Analytics ou um aplicativo de Application Insights. Nenhum ícone indica que outro recurso do Azure.

![Scope](media/scope/scope.png)

O escopo é determinado pelo método você usa para iniciar o Log Analytics e, em alguns casos, você pode alterar o escopo clicando nele. A tabela a seguir lista os diferentes tipos de escopo usado e detalhes diferentes para cada um.

| Escopo de consulta | Registros no escopo | Como selecionar | Alterar escopo |
|:---|:---|:---|:---|
| Espaço de trabalho do Log Analytics | Todos os registros no espaço de trabalho do Log Analytics. | Selecione **Logs** da **do Azure Monitor** menu ou a **espaços de trabalho do Log Analytics** menu.  | Pode alterar o escopo em qualquer outro tipo de recurso. |
| Aplicativo do Application Insights | Todos os registros do aplicativo do Application Insights. | Selecione **Analytics** partir **visão geral** página do Application Insights. | Só é possível alterar o escopo para outro aplicativo de Application Insights. |
| Grupo de recursos | Registros criados por todos os recursos no grupo de recursos. Pode incluir dados de vários espaços de trabalho do Log Analytics. | Selecione **Logs** no menu do grupo de recursos. | Não é possível alterar o escopo.|
| Assinatura | Registros criados por todos os recursos na assinatura. Pode incluir dados de vários espaços de trabalho do Log Analytics. | Selecione **Logs** no menu de assinatura.   | Não é possível alterar o escopo. |
| Outros recursos do Azure | Registros criados pelo recurso. Pode incluir dados de vários espaços de trabalho do Log Analytics.  | Selecione **Logs** no menu de recursos.<br>OU<br>Selecione **Logs** da **do Azure Monitor** menu e, em seguida, selecione um novo escopo. | Só pode alterar o escopo para o mesmo tipo de recurso. |

### <a name="limitations-when-scoped-to-a-resource"></a>Limitações ao escopo a um recurso

Quando o escopo de consulta é um espaço de trabalho do Log Analytics ou um aplicativo de Application Insights, todas as opções no portal e todos os comandos de consulta estão disponíveis. Quando o escopo a um recurso, no entanto, as seguintes opções no portal não está disponível porque elas são associadas um único espaço de trabalho ou aplicativo:

- Salvar
- Gerenciador de consultas
- Nova regra de alerta

Você não pode usar os seguintes comandos em uma consulta quando o escopo para um recurso, pois o escopo da consulta já incluirão nenhum espaço de trabalho com os dados para esse recurso ou conjunto de recursos:

- [app](app-expression.md)
- [workspace](workspace-expression.md)
 


## <a name="time-range"></a>Intervalo de tempo
O intervalo de tempo Especifica o conjunto de registros que estão sendo avaliados para a consulta com base em quando o registro foi criado. Isso é definido por uma propriedade padrão em todos os registros no espaço de trabalho ou aplicativo conforme especificado na tabela a seguir.

| Local padrão | Propriedade |
|:---|:---|
| Espaço de trabalho do Log Analytics          | TimeGenerated |
| Aplicativo do Application Insights | timestamp     |

Defina o intervalo de tempo, selecionando-o seletor de tempo na parte superior da janela do Log Analytics.  Você pode selecionar um período predefinido ou selecionar **personalizado** para especificar um intervalo de tempo específico.

![Seletor de tempo](media/scope/time-picker.png)

Se você definir um filtro na consulta que usa a propriedade de hora padrão, conforme mostrado na tabela acima, o seletor de hora será alterado para **definido na consulta**, e o seletor de hora está desabilitado. Nesse caso, é mais eficiente para colocar o filtro na parte superior da consulta para que qualquer processamento subsequente só precisa trabalhar com os registros filtrados.

![Consulta filtrada](media/scope/query-filtered.png)

Se você usar o [espaço de trabalho](workspace-expression.md) ou [aplicativo](app-expression.md) de comando para recuperar dados de outro espaço de trabalho ou aplicativo, o seletor de tempo pode se comportar de maneira diferente. Se o escopo é um espaço de trabalho do Log Analytics e você usar **app**, ou se o escopo é um aplicativo de Application Insights e você usar **espaço de trabalho**, em seguida, o Log Analytics talvez não compreendam que essa propriedade é usada no filtro deve determinar o filtro de tempo.

No exemplo a seguir, o escopo é definido como um espaço de trabalho do Log Analytics.  A consulta usa **espaço de trabalho** para recuperar dados de outro espaço de trabalho do Log Analytics. O seletor de hora é alterado para **definido na consulta** porque ele vê um filtro que usa o esperado **TimeGenerated** propriedade.

![Consultar com o espaço de trabalho](media/scope/query-workspace.png)

Se a consulta utiliza **app** para recuperar dados de um aplicativo de Application Insights, no entanto, o Log Analytics não reconhece o **timestamp** propriedade no filtro e o seletor de tempo permanece inalterada. Nesse caso, ambos os filtros são aplicados. No exemplo, somente os registros criados nas últimas 24 horas são incluídos na consulta, mesmo que ele especifica sete dias na **onde** cláusula.

![Consultar com o aplicativo](media/scope/query-app.png)

## <a name="next-steps"></a>Próximas etapas

- Percorrer um [tutorial sobre como usar o Log Analytics no portal do Azure](get-started-portal.md).
- Percorrer um [tutorial sobre como escrever consultas](get-started-queries.md).