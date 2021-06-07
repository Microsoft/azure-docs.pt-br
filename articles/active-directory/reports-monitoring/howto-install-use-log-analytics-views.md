---
title: Como instalar e usar as exibições de análise de logs | Microsoft Docs
description: Saiba como instalar e usar as exibições de análise de logs do Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2290de3c-2858-4da0-b4ca-a00107702e26
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86ad698793d562f93f9972903ca21e50c209c79c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100580013"
---
# <a name="install-and-use-the-log-analytics-views-for-azure-active-directory"></a>Instalar e usar os modos de exibição do Log Analytics do Azure Active Directory

As exibições do Log Analytics do Azure Active Directory ajuda você a analisar e pesquisar os logs de atividades do Azure AD no locatário do Azure AD. Logs de atividade do Azure AD incluem:

* Logs de auditoria: o [relatório de atividade de logs de auditoria](concept-audit-logs.md) dá acesso ao histórico de todas as tarefas executadas em seu locatário.
* Logs de entrada: com o [relatório de atividades de entrada](concept-sign-ins.md), você pode determinar quem realizou as tarefas indicadas pelo relatório das trilhas de auditoria.

## <a name="prerequisites"></a>Pré-requisitos

Para usar as exibições do Log Analytics, você precisará de:

* Um espaço de trabalho do Log Analytics em sua assinatura do Azure. Saiba como [criar um espaço de trabalho do Log Analytics](../../azure-monitor/logs/quick-create-workspace.md).
* Em primeiro lugar, conclua as etapas para [rotear os logs de atividades do Azure AD para seu espaço de trabalho do Log Analytics](howto-integrate-activity-logs-with-log-analytics.md).
* Baixar as exibições do [Repositório do GitHub](https://aka.ms/AADLogAnalyticsviews) para seu computador local.

## <a name="install-the-log-analytics-views"></a>Instalar as exibições do Log Analytics

1. Navegue para seu espaço de trabalho do Log Analytics. Para fazer isso, primeiro navegue até o [portal do Azure](https://portal.azure.com) e selecione **Todos os serviços**. Digite **Log Analytics** na caixa de texto e escolha **workspaces do Log Analytics**. Selecione o workspace para o qual você roteou os logs de atividade como parte dos pré-requisitos.
2. Selecione **Designer de Exibição**, **Importe** e, em seguida, **Escolher Arquivo** para importar as exibições do seu computador local.
3. Selecione as exibições que você baixou dos pré-requisitos e selecione **Salvar** para salvar a importação. Faça isso para a exibição **Eventos de provisionamento de conta do Azure AD** e a exibição **Eventos de Entradas**.

## <a name="use-the-views"></a>Usar as exibições

1. Navegue para seu espaço de trabalho do Log Analytics. Para fazer isso, primeiro navegue até o [portal do Azure](https://portal.azure.com) e selecione **Todos os serviços**. Digite **Log Analytics** na caixa de texto e escolha **workspaces do Log Analytics**. Selecione o workspace para o qual você roteou os logs de atividade como parte dos pré-requisitos.

2. Quando você estiver usando o workspace, selecione **Resumo do Workspace**. Você deve ver as três exibições a seguir:

    * **Eventos de provisionamento de conta do Azure AD**: Essa exibição mostra os relatórios relacionados à auditoria da atividade de provisionamento, como o número de novos usuários provisionados e falhas de provisionamento, número de usuários atualizados e falhas de atualização, além de número de usuários desprovisionados e falhas correspondentes.    
    * **Eventos de entradas**: Essa exibição mostra os relatórios mais relevantes relacionados à atividade de entrada de monitoramento, como entradas por aplicativo, usuário, dispositivo, bem como exibição resumida de acompanhamento do número de entradas ao longo do tempo.

3. Selecione qualquer uma dessas exibições para ir para os relatórios individuais. Você também pode definir alertas para qualquer um dos parâmetros do relatório. Por exemplo, vamos definir um alerta sempre que houver um erro de entrada. Para fazer isso, primeiro selecione a exibição **Eventos de Entradas**, selecione o relatório **Erros de entrada ao longo do tempo** e, em seguida, selecione **Analytics** para abrir a página de detalhes com a consulta real subjacente ao relatório. 

    ![A captura de tela mostra a página de detalhes de análise que tem a consulta para o relatório.](./media/howto-install-use-log-analytics-views/details.png)


4. Selecione **Definir Alerta** e, em seguida, **Sempre que a pesquisa de logs personalizada for &lt;lógica não definida&gt;** na seção **Critérios de alerta**. Como queremos alertar sempre que houver um erro de conexão, defina o **Limite** da lógica de alerta padrão como **1** e, em seguida, selecione **Concluído**. 

    ![Configurar sinal lógico](./media/howto-install-use-log-analytics-views/configure-signal-logic.png)

5. Insira um nome e uma descrição para o alerta e defina a gravidade como **Aviso**.

    ![Criar regra](./media/howto-install-use-log-analytics-views/create-rule.png)

6. Selecione o grupo de ações para alerta. Em geral, pode ser uma equipe que você deseja notificar por email ou mensagem de texto, ou pode ser uma tarefa automatizada usando webhooks, runbooks, funções, aplicativos lógicos ou soluções ITSM externas. Este artigo mostra como [criar e gerenciar grupos de ações no portal do Azure](../../azure-monitor/alerts/action-groups.md).

7. Selecione **Criar regra de alerta** para criar o alerta. Agora você será alertado sempre que houver um erro de conexão.

## <a name="next-steps"></a>Próximas etapas

* [Como analisar logs de atividade com os logs do Azure Monitor](howto-analyze-activity-logs-log-analytics.md)
* [Introdução aos logs do Azure Monitor no portal do Azure](../../azure-monitor/logs/log-analytics-tutorial.md)