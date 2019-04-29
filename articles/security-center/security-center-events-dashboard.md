---
title: Monitoramento e processamento de eventos de segurança na Central de Segurança do Azure | Microsoft Docs
description: Saiba como você pode usar o painel de eventos da Central de Segurança para ver os eventos de segurança em suas VMs do Azure e em seus computadores não Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: rkarlin
ms.openlocfilehash: 01f6da4f5ad6b618c444949fce8d2b7aa3367e17
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60705012"
---
# <a name="monitoring-and-processing-security-events-in-azure-security-center"></a>Monitoramento e processamento de eventos de segurança na Central de Segurança do Azure
O painel Eventos fornece uma visão geral do número de eventos de segurança coletado ao longo do tempo, além de uma lista de eventos notáveis que possam demandar sua atenção.  

> [!NOTE]
> Para usar esse recurso, seu workspace deve estar executando a versão 2 do Log Analytics e estar na camada Standard da Central de Segurança. Consulte a [página de preços](security-center-pricing.md) da Central de Segurança para obter mais informações sobre a camada Standard.
>
>

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="what-is-a-security-event"></a>O que é um evento de segurança?
A Central de Segurança usa o Microsoft Monitoring Agent para coletar diversas configurações e eventos relacionados a segurança em seus computadores e armazena esses eventos em seu workspace. Exemplos desses dados são: logs do sistema operacional (logs de eventos do Windows), processos em execução e eventos de soluções de segurança integrados com a Central de Segurança. O Microsoft Monitoring Agent também copia os arquivos de despejo de falha para seus workspaces.

## <a name="events-processed-dashboard"></a>Painel de eventos processados
Você acessa o painel **Eventos** no menu principal da Central de Segurança ou na folha **Visão geral** da Central de Segurança.  

![Painel de eventos processados][1]

O bloco **Eventos**, em **Central de Segurança**, exibe o número de eventos que fluem de suas VMs do Azure e computadores não Azure para a Central de Segurança.

O **painel Eventos** fornece uma visão geral do número de eventos processados ao longo do tempo, bem como uma lista de eventos.

 ![painel][2]

 A metade superior do painel mostra as tendências de todos os eventos processados na última semana. A metade inferior do painel lista os eventos notáveis e todos os eventos por tipo:

 - Os **eventos notáveis** incluem as consultas de eventos que a Central de Segurança fornece e as consultas de evento que você cria e adiciona. O painel também oferece uma exibição rápida da contagem de cada evento notável.
 - **Todos os eventos por tipo** mostra os tipos de eventos que estão sendo recebidos e uma contagem de cada tipo. Exemplos de tipo de evento são SecurityEvent, CommonSecurityLog, WindowsFirewall e W3CIISLog.

> [!NOTE]
> Eventos importantes incluem a [avaliação de linha de base da Web](https://docs.microsoft.com/azure/operations-management-suite/oms-security-web-baseline-assessment). O objetivo da avaliação de linha de base da Web é localizar as possíveis configurações de servidor Web vulneráveis.

## <a name="view-processed-event-details"></a>Exibir detalhes de eventos processados
1. No menu principal da **Central de Segurança**, selecione **Eventos**.
2. O seletor de workspace do **Painel de eventos** pode ser aberto. Se você tiver apenas um workspace, esse seletor de workspace não aparecerá. Se você tiver mais de um workspace, você precisará selecionar um workspace para exibir os respectivos detalhes de eventos processados. Selecione um workspace na lista, se você tiver mais de um workspace.

   ![Lista de workspace][3]

3. O **Painel de eventos** é aberto, mostrando detalhes de eventos do workspace selecionado. Você pode exibir os eventos notáveis e todos os eventos por tipo.  Neste exemplo, selecionamos **Eventos notáveis**.

   ![Eventos notáveis][4]

4. Você pode consultar mais dados no workspace, selecionando um tipo de evento. Neste exemplo, selecionamos **SecurityEvent**.

   ![Selecionando um tipo de evento][5]

5. A **pesquisa de logs** abre-se, com detalhes adicionais sobre o tipo de evento.

   ![Pesquisa de log][6]

## <a name="add-a-notable-event"></a>Adicionar um evento notável
A Central de Segurança fornece eventos notáveis prontos para uso. Você pode adicionar eventos notáveis com base em sua própria consulta usando o [linguagem de consulta Kusto](../log-analytics/log-analytics-search-reference.md). Retornaremos ao **Painel de eventos** para adicionar um evento notável.

1. Selecione **Adicionar Evento Notável**.

   ![Adicionar um evento notável][7]

2. A janela **Adicionar eventos notáveis personalizados** é aberta.  Em **Nome de Exibição**, insira um nome para o evento notável. Em **Consulta de Pesquisa**, insira a consulta para o evento.

   ![Inserir sua consulta][8]

4. Selecione **OK**.

## <a name="update-your-workspace-for-events-processing"></a>Atualizar seu workspace para processamento de eventos
Seu workspace deve estar executando a versão 2 do Log Analytics e estar na camada Standard da Central de Segurança para usar o processamento de eventos na Central de Segurança. O seletor de workspace do **Painel de eventos** identifica os workspaces que não atendem a esses requisitos.

![O workspace não atende aos requisitos][9]

Se a linha do workspace:

- Contém **REQUER ATUALIZAÇÃO** – você precisa atualizar seu workspace para a versão 2 do Log Analytics
- Contém **ATUALIZAR PLANO** – você precisa atualizar seu workspace para a camada Standard da Central de Segurança
- Está em branco – seu workspace atende aos requisitos e, ao selecionar um workspace, você será levado ao painel

> [!NOTE]
> No **Painel de eventos**, a coluna **EVENTOS** indica a quantidade de eventos em cada workspace.  Essa coluna aparece em branco para alguns workspaces porque a camada Gratuita da Central de Segurança está aplicada a esse workspace. Na camada gratuita, a Central de segurança coletará eventos, mas os eventos não são salvas nos logs do Azure Monitor e não estão disponíveis no painel de controle.
>
>

## <a name="update-workspace-to-log-analytics-version-2"></a>Atualizar o workspace para a versão 2 do Log Analytics
1. Selecione um workspace que **REQUER ATUALIZAÇÃO**.
2. A **Pesquisar Atualização** é aberta. Selecione **Atualizar Agora**.

   ![Atualizar agora][10]

## <a name="upgrade-to-security-centers-standard-tier"></a>Atualizar para a camada Standard da Central de Segurança
1. Selecione um workspace com **ATUALIZAR PLANO**.
2. O **Painel de eventos** é aberto. Selecione **Testar o Painel de eventos**.

   ![Experimentar o painel][11]

3. Em **Integração à segurança avançada**, selecione o workspace que você está atualizando.
4. Em **Preços**, selecione **Standard**.
5. Clique em **Salvar**.

   ![Atualizar para a camada Standard][12]

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a usar o Painel de eventos da Central de Segurança. Para saber mais sobre como funciona o painel e escrever suas próprias consultas de evento, consulte:

- [O que é o Azure Monitor logs?](../log-analytics/log-analytics-overview.md) – Visão geral sobre os logs do Azure Monitor
- [Entender pesquisas de log em Kusto](../log-analytics/log-analytics-log-search-new.md) – descreve como as pesquisas de log são usadas nos logs do Azure Monitor e fornece conceitos que devem ser compreendidos antes de criar uma pesquisa de logs
- [Referência de pesquisa do Kusto](../log-analytics/log-analytics-search-reference.md) – Saiba como escrever suas próprias consultas de evento usando a linguagem de consulta no Log

Para saber mais sobre a Central de Segurança, confira:

- [Visão geral da Central de Segurança](security-center-intro.md) – Descreve as principais funcionalidades da Central de Segurança

<!--Image references-->
[1]: ./media/security-center-events-dashboard/events-processed.png
[2]: ./media/security-center-events-dashboard/dashboard.png
[3]: ./media/security-center-events-dashboard/view-processed-event.png
[4]: ./media/security-center-events-dashboard/notable-event.png
[5]: ./media/security-center-events-dashboard/events-by-type.png
[6]: ./media/security-center-events-dashboard/log-search-detail.png
[7]: ./media/security-center-events-dashboard/add-notable-event.png
[8]: ./media/security-center-events-dashboard/create-query.png
[9]: ./media/security-center-events-dashboard/requires-update.png
[10]: ./media/security-center-events-dashboard/search-upgrade.png
[11]: ./media/security-center-events-dashboard/try-dashboard.png
[12]: ./media/security-center-events-dashboard/onboard-workspace.png
