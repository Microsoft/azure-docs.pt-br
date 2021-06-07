---
title: Usar logs de Azure Monitor com os aplicativos lógicos do Azure e a automatização de energia
description: Saiba como você pode usar os aplicativos lógicos do Azure e automatizar a automação para automatizar rapidamente os processos repetíveis usando o conector de Azure Monitor.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/13/2020
ms.openlocfilehash: 4a25d7a23d486c8ce22fa433cc1ead390726facc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048855"
---
# <a name="azure-monitor-logs-connector-for-logic-apps-and-power-automate"></a>Conector de logs de Azure Monitor para aplicativos lógicos e automatização de energia
Os [aplicativos lógicos do Azure](../../logic-apps/index.yml) e a [automatização de energia](https://flow.microsoft.com) permitem que você crie fluxos de trabalho automatizados usando centenas de ações para uma variedade de serviços. O conector de logs de Azure Monitor permite que você crie fluxos de trabalho que recuperam dados de um espaço de Log Analytics ou de um aplicativo Application Insights no Azure Monitor. Este artigo descreve as ações incluídas com o conector e fornece uma explicação para criar um fluxo de trabalho usando esses dados.

Por exemplo, você pode criar um aplicativo lógico para usar Azure Monitor dados de log em uma notificação por email do Office 365, criar um bug no Azure DevOps ou postar uma mensagem de margem de atraso.  Você pode disparar um fluxo de trabalho com um agendamento simples ou a partir de alguma ação em um serviço conectado, por exemplo, quando um email ou tweet é recebido. 

## <a name="connector-limits"></a>Limites de conector
O conector de logs de Azure Monitor tem estes limites:
* Tamanho máximo dos dados: 16 MB
* Tamanho máximo de resposta de consulta de 100 MB
* Número máximo de registros: 500.000
* Tempo limite máximo de consulta 110 segundo.
* As visualizações de gráfico podem estar disponíveis na página logs e ausentes no conector, já que a página conectores e logs não usam as mesmas bibliotecas de gráficos atualmente.

Dependendo do tamanho dos dados e da consulta usada, o conector pode atingir seus limites e falhar. Você pode contornar esses casos ao ajustar a recorrência de gatilho para executar com mais frequência e consultar menos dados. Você pode usar consultas que agregam seus dados para retornar menos registros e colunas.

## <a name="actions"></a>Ações
A tabela a seguir descreve as ações incluídas com o conector de logs de Azure Monitor. Ambos permitem que você execute uma consulta de log em um espaço de trabalho Log Analytics ou Application Insights aplicativo. A diferença está no modo como os dados são retornados.

> [!NOTE]
> O conector de logs de Azure Monitor substitui o [conector do Azure log Analytics](/connectors/azureloganalytics/) e o [conector do aplicativo Azure insights](/connectors/applicationinsights/). Esse conector fornece a mesma funcionalidade que as outras e é o método preferencial para executar uma consulta em um espaço de trabalho Log Analytics ou em um aplicativo Application Insights.


| Ação | Descrição |
|:---|:---|
| [Executar consulta e e listar resultados](/connectors/azuremonitorlogs/#run-query-and-list-results) | Retorna cada linha como seu próprio objeto. Use essa ação quando desejar trabalhar com cada linha separadamente no restante do fluxo de trabalho. A ação é normalmente seguida por uma [para cada atividade](../../logic-apps/logic-apps-control-flow-loops.md#foreach-loop). |
| [Executar consulta e Visualizar resultados](/connectors/azuremonitorlogs/#run-query-and-visualize-results) | Retorna todas as linhas do conjunto de resultados como um único objeto formatado. Use essa ação quando desejar usar o conjunto de resultados juntos no restante do fluxo de trabalho, como enviar os resultados em um email.  |

## <a name="walkthroughs"></a>Passo a passo
Os tutoriais a seguir ilustram o uso dos conectores de Azure Monitor no aplicativo lógico do Azure. Você pode executar esses mesmos exemplos com o Power Automate, a única diferença é como criar o fluxo de trabalho inicial e executá-lo quando concluído. A configuração do fluxo de trabalho e das ações é a mesma entre ambos. Consulte [criar um fluxo de um modelo no Power Automate](/power-automate/get-started-logic-template) para começar.


### <a name="create-a-logic-app"></a>Criar um aplicativo lógico

Vá para **aplicativos lógicos** no portal do Azure e clique em **Adicionar**. Selecione uma **assinatura**, um **grupo de recursos** e uma **região** para armazenar o novo aplicativo lógico e dê a ele um nome exclusivo. Você pode ativar **log Analytics** configuração para coletar informações sobre dados de tempo de execução e eventos, conforme descrito em [configurar logs de Azure monitor e coletar dados de diagnóstico para aplicativos lógicos do Azure](../../logic-apps/monitor-logic-apps-log-analytics.md). Essa configuração não é necessária para usar o conector de logs de Azure Monitor.

![Criar aplicativo lógico](media/logicapp-flow-connector/create-logic-app.png)


Clique em **examinar + criar** e em **criar**. Quando a implantação for concluída, clique em **ir para o recurso** para abrir o **Designer de aplicativos lógicos**.

### <a name="create-a-trigger-for-the-logic-app"></a>Criar um gatilho para o aplicativo lógico
Em **Iniciar com um gatilho comum**, selecione **recorrência**. Isso cria um aplicativo lógico que é executado automaticamente em intervalos regulares. Na caixa **frequência** da ação, selecione **dia** e, na caixa **intervalo** , digite **1** para executar o fluxo de trabalho uma vez por dia.

![Ação de recorrência](media/logicapp-flow-connector/recurrence-action.png)

## <a name="walkthrough-mail-visualized-results"></a>Walkthrough: resultados visualizados por email
O tutorial a seguir mostra como criar um aplicativo lógico que envia os resultados de uma consulta Azure Monitor log por email. 

### <a name="add-azure-monitor-logs-action"></a>Ação de adicionar logs de Azure Monitor
Clique em **+ nova etapa** para adicionar uma ação que é executada após a ação de recorrência. Em **escolher uma ação**, digite **Azure monitor** e, em seguida, selecione **logs de Azure monitor**.

![Ação de logs de Azure Monitor](media/logicapp-flow-connector/select-azure-monitor-connector.png)

Clique em **Azure Log Analytics – Executar a consulta e visualizar os resultados**.

![Captura de tela de uma nova ação que está sendo adicionada a uma etapa no designer do aplicativo lógico. Azure Monitor logs é realçado em escolher uma ação.](media/logicapp-flow-connector/select-query-action-visualize.png)


### <a name="add-azure-monitor-logs-action"></a>Ação de adicionar logs de Azure Monitor

Selecione a **assinatura** e o **grupo de recursos** para seu espaço de trabalho log Analytics. Selecione *log Analytics espaço de trabalho* para o **tipo de recurso** e, em seguida, selecione o nome do espaço de trabalho em **nome do recurso**.

Adicione a consulta de logs a seguir à janela **Consultar**.  

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1day)
| summarize TotalErrors=count() by Computer
| sort by Computer asc   
```

Selecione *definir na consulta* para o **intervalo de tempo** e a **tabela HTML** para o **tipo de gráfico**.
   
![Captura de tela das configurações da nova ação logs de Azure Monitor chamada executar consulta e Visualizar resultados.](media/logicapp-flow-connector/run-query-visualize-action.png)

O email será enviado pela conta associada à conexão atual. Você pode especificar outra conta clicando em **alterar conexão**.

### <a name="add-email-action"></a>Adicionar ação de email

Clique em **+ nova etapa** e, em seguida, clique em **+ Adicionar uma ação**. Em **escolher uma ação**, digite **Outlook** e, em seguida, selecione **Office 365 Outlook**.

![Selecionar conector do Outlook](media/logicapp-flow-connector/select-outlook-connector.png)

Selecione **enviar um email (v2)**.

![Janela de seleção do Office 365 Outlook](media/logicapp-flow-connector/select-mail-action.png)

Clique em qualquer lugar na caixa **corpo** para abrir uma janela de **conteúdo dinâmico** aberta com valores das ações anteriores no aplicativo lógico. Selecione **Ver mais** e, em seguida, o **corpo** , que são os resultados da consulta na ação log Analytics.

![Selecione corpo](media/logicapp-flow-connector/select-body.png)

Especifique o endereço de email de um destinatário na janela **Para** e um assunto para o email em **Assunto**. 

![Ação de email](media/logicapp-flow-connector/mail-action.png)


### <a name="save-and-test-your-logic-app"></a>Salvar e testar o aplicativo lógico
Clique em **salvar** e em **executar** para executar uma execução de teste do aplicativo lógico.

![Salvar e Executar](media/logicapp-flow-connector/save-run.png)


Quando o aplicativo lógico for concluído, verifique o email do destinatário que você especificou.  Você deve ter recebido um email com um corpo semelhante ao seguinte:

![Email de exemplo](media/logicapp-flow-connector/sample-mail.png)



## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [consultas de log no Azure Monitor](./log-query-overview.md).
- Saiba mais sobre os [aplicativos lógicos](../../logic-apps/index.yml)
- Saiba mais sobre a [automatização de energia](https://flow.microsoft.com).