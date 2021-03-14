---
title: Perguntas frequentes sobre a central de segurança do Azure-perguntas sobre agentes de Log Analytics existentes
description: Essas perguntas FREQUENTEs respondem a perguntas para clientes que já usam o agente de Log Analytics e considerando a central de segurança do Azure, um produto que ajuda você a prevenir, detectar e responder a ameaças.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 1a52b2fec6155959a570f2438a59c14d9f79f368
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101701953"
---
# <a name="faq-for-customers-already-using-azure-monitor-logs"></a>Perguntas frequentes para clientes que já estão usando logs de Azure Monitor<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>A Central de Segurança substitui todas as conexões existentes entre as VMs e os workspaces?

Se uma VM já tiver o agente de Log Analytics instalado como uma extensão do Azure, a central de segurança não substituirá a conexão de espaço de trabalho existente. Em vez disso, a Central de Segurança usará o workspace existente. A VM será protegida desde que a solução "segurança" ou "SecurityCenterFree" tenha sido instalada no espaço de trabalho para o qual está se comunicando. 

Uma solução da central de segurança é instalada no espaço de trabalho selecionado na tela de coleta de dados, se ainda não estiver presente, e a solução será aplicada somente às VMs relevantes. Quando você adiciona uma solução, ela é implantada automaticamente por padrão em todos os agentes do Windows e Linux conectados ao seu espaço de trabalho do Log Analytics. [Direcionamento de Solução](../azure-monitor/insights/solution-targeting.md) permite a aplicação de um escopo às suas soluções.

> [!TIP]
> Se o agente de Log Analytics estiver instalado diretamente na VM (não como uma extensão do Azure), a central de segurança não instalará o agente de Log Analytics e o monitoramento de segurança será limitado.

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>A Central de Segurança instala soluções em meus workspaces existentes do Log Analytics? Quais são as implicações de cobrança?
Quando a central de segurança identifica que uma VM já está conectada a um espaço de trabalho que você criou, a central de segurança habilita soluções nesse espaço de trabalho de acordo com sua configuração de preço. As soluções são aplicadas apenas às VMs do Azure relevantes, por meio do [direcionamento de solução](../azure-monitor/insights/solution-targeting.md), portanto, a cobrança permanece igual.

- **Azure defender desativado** – a central de segurança instala a solução "SecurityCenterFree" no espaço de trabalho. Você não será cobrado.
- **Azure defender on** – central de segurança instala a solução ' Security ' no espaço de trabalho.

   ![Soluções no workspace padrão](./media/security-center-platform-migration-faq/solutions.png)

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Eu já tenho workspaces em meu ambiente, posso usá-los para coletar dados de segurança?
Se uma VM já tiver o agente de Log Analytics instalado como uma extensão do Azure, a central de segurança usará o espaço de trabalho conectado existente. Uma solução da Central de Segurança será instalado no workspace, se ainda não estiver presente, e a solução será aplicada apenas às VMs relevantes por meio do [direcionamento de solução](../azure-monitor/insights/solution-targeting.md).

Quando a central de segurança instala o agente de Log Analytics em VMs, ela usa os espaços de trabalho padrão criados pela central de segurança se a central de segurança não for apontada para um espaço de trabalho existente.

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Já tenho a solução de segurança em meus workspaces. Quais são as implicações de cobrança?
A solução de auditoria de & de segurança é usada para habilitar o **Azure defender para servidores**. Se a solução de Segurança e Auditoria já estiver instalada em um workspace, a Central de Segurança usará a solução existente. Não há nenhuma alteração na cobrança.