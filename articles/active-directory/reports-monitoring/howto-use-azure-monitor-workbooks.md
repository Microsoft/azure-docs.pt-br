---
title: Pastas de trabalho do Azure Monitor para relatórios | Microsoft Docs
description: Saiba como usar pastas de trabalho do Azure Monitor para relatórios do Azure Active Directory.
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/30/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: b84c38b28b51f867160272883465242fc81ff2bf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100588058"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Como usar pastas de trabalho do Azure Monitor para relatórios do Azure Active Directory

> [!IMPORTANT]
> Para otimizar as consultas subjacentes nessa pasta de trabalho, clique em "Editar", clique no ícone de configurações e selecione o espaço de trabalho em que deseja executar essas consultas. Por padrão, as pastas de trabalho selecionarão todos os espaços de trabalho nos quais você está roteando os logs do Azure AD. 

Você deseja:

- Entender o efeito das [políticas de acesso condicional](../conditional-access/overview.md) na experiência de entrada do usuário?

- Solucionar problemas de falhas de entrada para obter uma exibição melhor da integridade das credenciais da sua organização e resolver problemas rapidamente?

- Saber quem está usando autenticações herdadas para entrar em seu ambiente? (Ao [bloquear a autenticação herdada](../conditional-access/block-legacy-authentication.md), você melhora a proteção do locatário.)

- Você precisa entender como as políticas de acesso condicional afetam seu locatário?

- Você gostaria de poder examinar: consultas de log de entrada, os relatórios da pasta de trabalho sobre quantos usuários tiveram o acesso concedido ou negado, bem como quantos usuários contornaram as políticas de acesso condicional ao acessar recursos?

- Tem interesse em aprofundar-se em: detalhes da pasta de trabalho por condição para que o impacto de uma política possa ser contextual por condição, como incluir a plataforma do dispositivo, o estado do dispositivo, o aplicativo cliente, o risco de entrada, a localização e o aplicativo?

- Tenha uma ideia mais aprofundada sobre consultas de log de entrada, os relatórios da pasta de trabalho sobre quantos usuários tiveram o acesso concedido ou negado, bem como quantos usuários contornaram as políticas de acesso condicional ao acessar recursos.

- Para ajudar você a tirar essas dúvidas, o Azure Active Directory fornece pastas de trabalho para monitoramento. As [pastas de trabalho do Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md) combinam texto, consultas analíticas, métricas e parâmetros em relatórios interativos avançados.



Este artigo:

- Pressupõe que você já saiba [criar relatórios interativos usando as pastas de trabalho do Monitor](../../azure-monitor/visualize/workbooks-overview.md).

- Explica como usar pastas de trabalho do Monitor para entender o efeito das políticas de acesso condicional, solucionar problemas de falhas de entrada e identificar autenticações herdadas.
 


## <a name="prerequisites"></a>Pré-requisitos

Para usar pastas de trabalho do Monitor, você precisa de:

- Um locatário do Azure Active Directory com uma licença premium (P1 ou P2). Saiba como [obter uma licença premium](../fundamentals/active-directory-get-started-premium.md).

- Um [workspace do Log Analytics](../../azure-monitor/logs/quick-create-workspace.md).

- [Acesso](../../azure-monitor/logs/manage-access.md#manage-access-using-workspace-permissions) ao workspace do Log Analytics
- As funções a seguir no Azure Active Directory (se você estiver acessando o Log Analytics por meio do portal do Azure Active Directory)
    - Administrador de segurança
    - Leitor de segurança
    - Leitor de relatórios
    - Administrador global

## <a name="roles"></a>Funções
Você deve ter uma das funções a seguir, bem como [acesso ao workspace do Log Analytics](../../azure-monitor/logs/manage-access.md#manage-access-using-azure-permissions), para gerenciar as pastas de trabalho:
-   Administrador global
-   Administrador de segurança
-   Leitor de segurança
-   Leitor de relatórios
-   Administrador de aplicativos

## <a name="workbook-access"></a>Acesso à pasta de trabalho 

Para acessar pastas de trabalho:

1. Entre no [portal do Azure](https://portal.azure.com).

1. Navegue até **Azure Active Directory** > **Monitoramento** > **Pastas de Trabalho**. 

1. Selecione um relatório ou modelo ou, na barra de ferramentas, escolha **Abrir**. 

![Localizar as pastas de trabalho do Azure Monitor no Azure AD](./media/howto-use-azure-monitor-workbooks/azure-monitor-workbooks-in-azure-ad.png)

## <a name="sign-in-analysis"></a>Análise de credenciais

Para acessar a pasta de trabalho de análise de credenciais, na seção **Uso**, selecione **Entradas**. 

A pasta de trabalho mostra as seguintes tendências de entrada:

- Todas as entradas

- Sucesso

- Ação do usuário pendente

- Falha

Você pode filtrar cada tendência pelas seguintes categorias:

- Intervalo de horas

- Aplicativos

- Usuários

![Análise de credenciais](./media/howto-use-azure-monitor-workbooks/43.png)


Para cada tendência, você obtém um detalhamento das seguintes categorias:

- Location

    ![Entradas por localização](./media/howto-use-azure-monitor-workbooks/45.png)

- Dispositivo

    ![Entradas por dispositivo](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Entradas usando a autenticação herdada 


Para acessar a pasta de trabalho das entradas que usam [autenticação herdada](../conditional-access/block-legacy-authentication.md), na seção **Uso**, selecione **Entradas usando a autenticação herdada**. 

A pasta de trabalho mostra as seguintes tendências de entrada:

- Todas as entradas

- Sucesso


Você pode filtrar cada tendência pelas seguintes categorias:

- Intervalo de horas

- Aplicativos

- Usuários

- Protocolos

![Entradas por autenticação herdada](./media/howto-use-azure-monitor-workbooks/47.png)


Para cada tendência, você obtém um detalhamento por aplicativo e protocolo.

![Entradas de autenticação herdada por aplicativo e protocolo](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Entradas por acesso condicional 


Para acessar a pasta de trabalho de entradas por [políticas de acesso condicional](../conditional-access/overview.md), na seção **Acesso condicional**, selecione **Entradas por acesso condicional**. 

Essa pasta de trabalho mostra as tendências das entradas desabilitadas. Você pode filtrar cada tendência pelas seguintes categorias:

- Intervalo de horas

- Aplicativos

- Usuários

![Entradas usando o acesso condicional](./media/howto-use-azure-monitor-workbooks/49.png)


Para entradas desabilitadas, você obtém um detalhamento pelo status do acesso condicional.

![Captura de tela mostra o status de acesso condicional e as entradas recentes.](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)


## <a name="conditional-access-insights"></a>Insights de Acesso Condicional

### <a name="overview"></a>Visão geral

As pastas de trabalho contêm consultas de log de entrada que podem ajudar os administradores de TI a monitorar o impacto das políticas de acesso condicional no locatário. Você pode emitir relatórios sobre quantos usuários teriam o acesso concedido ou negado. A pasta de trabalho contém insights sobre quantos usuários teriam contornado as políticas de acesso condicional com base nos atributos desses usuários no momento da entrada. Ela contém detalhes por condição para que o impacto de uma política possa ser contextual por condição, como incluir a plataforma do dispositivo, o estado do dispositivo, o aplicativo cliente, o risco de entrada, a localização e o aplicativo.

### <a name="instructions"></a>Instruções 
Para acessar a pasta de trabalho de insights de acesso condicional, selecione a pasta de trabalho **Políticas de Acesso Condicional**, na seção Acesso condicional. Essa pasta de trabalho mostra o impacto esperado de cada política de acesso condicional em seu locatário. Selecione uma ou mais políticas de acesso condicional na lista suspensa e restrinja o escopo da pasta de trabalho aplicando os seguintes filtros: 

- **Intervalo de tempo**

- **Usuário**

- **Aplicativos**

- **Exibição de dados**

![Captura de tela mostra o painel de acesso condicional em que é possível selecionar uma política de acesso condicional.](./media/howto-use-azure-monitor-workbooks/access-insights.png)


O resumo de impacto mostra o número de usuário ou credenciais para os quais as políticas selecionadas tiveram um resultado específico. O total é o número de usuários ou entradas para os quais as políticas selecionadas foram avaliadas no intervalo de tempo selecionado. Clique em um título para filtrar os dados na pasta de trabalho por esse tipo de resultado. 

![Captura de tela mostra blocos a serem usados para filtrar resultados como total, êxito e falha.](./media/howto-use-azure-monitor-workbooks/impact-summary.png)

Essa pasta de trabalho mostra o impacto das políticas selecionadas detalhado a cada seis condições: 
- **Estado do dispositivo**
- **Plataforma do dispositivo**
- **Aplicativos do cliente**
- **Risco de entrada**
- **Localidade**
- **Aplicativos**

![Captura de tela mostra os detalhes do filtro total de entradas.](./media/howto-use-azure-monitor-workbooks/device-platform.png)

Você também pode investigar entradas individuais, filtradas pelos parâmetros selecionados na pasta de trabalho. Pesquise por usuários individuais, classificados por frequência de entrada, e exiba seus eventos de entrada correspondentes. 

![Captura de tela mostra entradas individuais que você pode examinar.](./media/howto-use-azure-monitor-workbooks/filtered.png)

## <a name="sign-ins-by-grant-controls"></a>Entradas por controles de concessão

Para acessar a pasta de trabalho de entradas por [controles de concessão](../conditional-access/controls.md), na seção **Acesso condicional**, selecione **Entradas por controles de concessão**. 

Essa pasta de trabalho mostra as seguintes tendências de entrada desabilitadas:

- Exigir MFA
 
- Requerer termos de uso

- Política de privacidade do Azure

- Outros


Você pode filtrar cada tendência pelas seguintes categorias:

- Intervalo de horas

- Aplicativos

- Usuários

![Entradas por controles de concessão](./media/howto-use-azure-monitor-workbooks/50.png)


Para cada tendência, você obtém um detalhamento por aplicativo e protocolo.

![Detalhamento das entradas recentes](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Análise de falha de entradas

Use a pasta de trabalho **Análise de falha de entradas** para solucionar erros em:

- Entradas
- Políticas de Acesso Condicional
- Autenticação herdada 


Para acessar as entradas por dados de acesso condicional, na seção **Solução de problemas**, selecione **Entradas usando a autenticação herdada**. 

A pasta de trabalho mostra as seguintes tendências de entrada:

- Todas as entradas

- Sucesso

- Ação pendente

- Falha


Você pode filtrar cada tendência pelas seguintes categorias:

- Intervalo de horas

- Aplicativos

- Usuários

![Solução de problemas de entrada](./media/howto-use-azure-monitor-workbooks/52.png)


Para ajudar você a solucionar problemas de entrada, o Azure Monitor oferece um detalhamento pelas seguintes categorias:

- Principais erros

    ![Resumo dos principais erros](./media/howto-use-azure-monitor-workbooks/53.png)

- Entradas aguardando ação do usuário

    ![Resumo de entradas aguardando ação do usuário](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Próximas etapas

[Criar relatórios usando pastas de trabalho do Monitor](../../azure-monitor/visualize/workbooks-overview.md).