---
title: Usar pastas de trabalho do Azure Monitor para relatórios do Active Directory do Azure | Microsoft Docs
description: Saiba como usar as pastas de trabalho do Azure Monitor para relatórios do Active Directory do Azure.
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 9433714c06dfad09270a6033f38a99471bcd517a
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67513611"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Como usar pastas de trabalho do Azure Monitor para relatórios do Active Directory do Azure

Você deseja:

- Entender o efeito de sua [políticas de acesso condicional](../conditional-access/overview.md) na experiência de logon dos usuários?

- Solucionar problemas de falhas de entrada para obter uma exibição melhor da entrar integridade sua organização e para resolver problemas rapidamente?

- Saber quem está usando as autenticações herdadas para entrar no seu ambiente? (Por [bloqueios de autenticação herdada](../conditional-access/block-legacy-authentication.md), você pode melhorar a proteção do seu locatário.)

Para ajudar a resolver esses problemas, o Active Directory fornece pastas de trabalho para o monitoramento. [Pastas de trabalho do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) combinar texto, consultas de análise, métricas e parâmetros em relatórios interativos avançados. 

Este artigo:

- Pressupõe que você esteja familiarizado com a [crie relatórios interativos usando pastas de trabalho de Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).

- Explica como usar pastas de trabalho de Monitor para entender o efeito das políticas de acesso condicional, solucionar problemas de falhas de entrada e para identificar as autenticações herdadas.
 


## <a name="prerequisites"></a>Pré-requisitos

Para usar pastas de trabalho de Monitor, você precisa:

- Um locatário do Active Directory com uma licença do premium (P1 ou P2). Saiba como [obter uma licença premium](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium).

- Um [espaço de trabalho do Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

## <a name="workbook-access"></a>Acesso de pasta de trabalho 

Para acessar as pastas de trabalho:

1. Entre no [Portal do Azure](https://portal.azure.com).

2. No painel de navegação à esquerda, selecione **Azure Active Directory**.

3. No **Monitoring** seção, selecione **pastas de trabalho**. 

    ![Selecione Insights](./media/howto-use-azure-monitor-workbooks/41.png)

4. Selecione um relatório ou modelo ou na barra de ferramentas, selecione **aberto**. 

    ![Selecione Abrir](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>Análise de entrada

Para acessar a pasta de trabalho de análise de entrar, o **uso** seção, selecione **entradas**. 

Esta pasta de trabalho mostra as seguintes tendências de entrada:

- Todas as entradas

- Êxito

- Ação de usuário pendentes

- Failure

Você pode filtrar cada tendência por categorias a seguir:

- Intervalo de tempo

- Aplicativos

- Usuários

![Análise de entrada](./media/howto-use-azure-monitor-workbooks/43.png)


Para cada tendência, você pode obter uma análise nas categorias seguintes:

- Location

    ![Entradas por local](./media/howto-use-azure-monitor-workbooks/45.png)

- Dispositivo

    ![Entradas por dispositivo](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Usando a autenticação herdada entradas 


Para acessar a pasta de trabalho para logons que usam [autenticação herdados](../conditional-access/block-legacy-authentication.md), no **uso** seção, selecione **entradas usando a autenticação herdada**. 

Esta pasta de trabalho mostra as seguintes tendências de entrada:

- Todas as entradas

- Êxito


Você pode filtrar cada tendência por categorias a seguir:

- Intervalo de tempo

- Aplicativos

- Usuários

- Protocolos

![Entradas por autenticação herdados](./media/howto-use-azure-monitor-workbooks/47.png)


Para cada tendência, você obtém uma divisão por aplicativo e protocolo.

![Entradas de autenticação herdada por aplicativo e protocolo](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Entradas por acesso condicional 


Para acessar a pasta de trabalho para entradas por [políticas de acesso condicional](../conditional-access/overview.md), no **acesso condicional** seção, selecione **entradas de acesso condicional ao**. 

Esta pasta de trabalho mostra as tendências para entradas desabilitadas. Você pode filtrar cada tendência por categorias a seguir:

- Intervalo de tempo

- Aplicativos

- Usuários

![Entradas usando o acesso condicional](./media/howto-use-azure-monitor-workbooks/49.png)


Para desabilitado entradas, você pode obter uma análise pelo status de acesso condicional.

![Status de acesso condicional](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>Entradas por controles de concessão

Para acessar a pasta de trabalho para entradas por [conceder controles](../conditional-access/controls.md), no **acesso condicional** seção, selecione **entradas por controles de concessão**. 

Esta pasta de trabalho mostra as seguintes desabilitadas entrar tendências:

- Exigir MFA
 
- Requerer termos de uso

- Exigir a declaração de privacidade

- Outros


Você pode filtrar cada tendência por categorias a seguir:

- Intervalo de tempo

- Aplicativos

- Usuários

![Entradas por controles de concessão](./media/howto-use-azure-monitor-workbooks/50.png)


Para cada tendência, você obtém uma divisão por aplicativo e protocolo.

![Análise das entradas recentes](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Análise de falha de entradas

Use o **análise de falha de entradas** pasta de trabalho para solucionar problemas de erros com o seguinte:

- Entradas
- Políticas de acesso condicional
- Autenticação herdada 


Para acessar as entradas pelos dados de acesso condicional na **solução de problemas** seção, selecione **entradas usando a autenticação herdada**. 

Esta pasta de trabalho mostra as seguintes tendências de entrada:

- Todas as entradas

- Êxito

- Ação pendente

- Failure


Você pode filtrar cada tendência por categorias a seguir:

- Intervalo de tempo

- Aplicativos

- Usuários

![Entradas de solução de problemas](./media/howto-use-azure-monitor-workbooks/52.png)


Para ajudá-lo a solucionar problemas de entradas, do Azure Monitor fornece uma divisão por categorias a seguir:

- Principais erros

    ![Resumo dos principais erros](./media/howto-use-azure-monitor-workbooks/53.png)

- Entradas aguardando ação do usuário

    ![Resumo das entradas aguardando ação do usuário](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Próximas etapas

[Crie relatórios interativos usando pastas de trabalho de Monitor](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).
