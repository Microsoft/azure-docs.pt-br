---
title: Automatizar processos de log do Azure Monitor com o Microsoft Flow
description: Saiba como você pode usar o Microsoft Flow para automatizar rapidamente os processos repetidos usando o conector do Azure Log Analytics.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
ms.service: log-analytics
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/29/2017
ms.author: bwren
ms.openlocfilehash: 46a4544b86648ee99a751d4793013f6104d1d9df
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807043"
---
# <a name="automate-azure-monitor-log-processes-with-the-connector-for-microsoft-flow"></a>Automatizar processos de log do Azure Monitor com o conector para Microsoft Flow
O [Microsoft Flow](https://ms.flow.microsoft.com) permite que você crie fluxos de trabalho automatizados usando centenas de ações para vários serviços. A saída de uma ação pode ser usada como entrada para outra, permitindo que você crie a integração entre serviços diferentes.  O conector do Azure Log Analytics para Microsoft Flow permite que você crie fluxos de trabalho que incluam dados recuperados pelas consultas de log de um espaço de trabalho do Log Analytics no Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Por exemplo, você pode usar o Microsoft Flow para usar dados de log do Azure Monitor em uma notificação por email do Office 365, crie um bug no DevOps do Azure ou poste uma mensagem de Slack.  Você pode disparar um fluxo de trabalho com um agendamento simples ou a partir de alguma ação em um serviço conectado, por exemplo, quando um email ou tweet é recebido.  

O tutorial neste artigo mostra como criar um fluxo que envia automaticamente os resultados de uma consulta de logs do Azure Monitor por email, que é um dos exemplos de como você pode usar o conector do Log Analytics no Microsoft Flow. 


## <a name="step-1-create-a-flow"></a>Etapa 1: Criar um fluxo
1. Entre no [Microsoft Flow](https://flow.microsoft.com) e selecione **Meus Fluxos**.
2. Clique em **+ Criar em branco**.

## <a name="step-2-create-a-trigger-for-your-flow"></a>Etapa 2: Criar um gatilho para o fluxo
1. Clique em **Pesquisar centenas de conectores e gatilhos**.
2. Digite **Agenda** na caixa de pesquisa.
3. Selecione **Agendamento** e, depois, **Agendamento – Recorrência**.
4. Na caixa **frequência**, selecione **Dia** e, na caixa **Intervalo**, digite **1**.<br><br>![Caixa de diálogo de gatilho do Microsoft Flow](media/flow-tutorial/flow01.png)


## <a name="step-3-add-a-log-analytics-action"></a>Etapa 3: Adicionar uma ação do Log Analytics
1. Clique em **+ Nova etapa** e depois clique em **Adicionar uma ação**.
2. Pesquise por **Log Analytics**.
3. Clique em **Azure Log Analytics – Executar a consulta e visualizar os resultados**.<br><br>![Janela Executar consulta do Log Analytics](media/flow-tutorial/flow02.png)

## <a name="step-4-configure-the-log-analytics-action"></a>Etapa 4: Configurar a ação do Log Analytics

1. Especifique os detalhes de seu workspace incluindo a ID da Assinatura, o Grupo de Recursos e o Nome do Workspace.
2. Adicione a consulta de logs a seguir à janela **Consultar**.  Isso é apenas um exemplo de consulta, e você pode substituir por qualquer outra que retorne dados.
   ```
    Event
    | where EventLevelName == "Error" 
    | where TimeGenerated > ago(1day)
    | summarize count() by Computer
    | sort by Computer
   ```

2. Selecione **Tabela HTML** para o **Tipo de Gráfico**.<br><br>![Ação do Log Analytics](media/flow-tutorial/flow03.png)

## <a name="step-5-configure-the-flow-to-send-email"></a>Etapa 5: Configurar o fluxo para enviar email

1. Clique em **Nova etapa** e depois clique em **+ Adicionar uma ação**.
2. Pesquise por **Office 365 Outlook**.
3. Clique em **Office 365 Outlook – Enviar um email**.<br><br>![Janela de seleção do Office 365 Outlook](media/flow-tutorial/flow04.png)

4. Especifique o endereço de email de um destinatário na janela **Para** e um assunto para o email em **Assunto**.
5. Clique em qualquer lugar da caixa **Corpo**.  Uma janela de **Conteúdo dinâmico** é aberta com valores de ações anteriores.  
6. Selecione **Corpo**.  Esses são os resultados da consulta na ação do Log Analytics.
6. Clique em **Mostrar opções avançadas**.
7. Na caixa **É HTML**, selecione **Sim**.<br><br>![Janela de configuração de email do Office 365](media/flow-tutorial/flow05.png)

## <a name="step-6-save-and-test-your-flow"></a>Etapa 6: Salvar e testar o fluxo
1. Na caixa **Nome do fluxo**, adicione um nome para o fluxo e, em seguida, clique em **Criar fluxo**.<br><br>![Salvar o fluxo](media/flow-tutorial/flow06.png)
2. Agora, o fluxo é criado e será executado após um dia, que é a agenda especificada. 
3. Para testar o fluxo imediatamente, clique em **Executar Agora** e **Executar fluxo**.<br><br>![Executar fluxo](media/flow-tutorial/flow07.png)
3. Após a conclusão do fluxo, verifique o email do destinatário que você especificou.  Você deve ter recebido um email com um corpo semelhante ao seguinte:<br><br>![Email de exemplo](media/flow-tutorial/flow08.png)


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [consultas de log no Azure Monitor](../log-query/log-query-overview.md).
- Saiba mais sobre o [Microsoft Flow](https://ms.flow.microsoft.com).



