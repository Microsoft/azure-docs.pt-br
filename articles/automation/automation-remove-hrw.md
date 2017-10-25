---
title: "Remover Hybrid Runbook Workers da Automação do Azure | Microsoft Docs"
description: "Este artigo fornece informações sobre como gerenciar Hybrid Runbook Workers implantados na Automação do Azure, que permite executar runbooks em computadores no seu datacenter local ou ambiente de nuvem."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2017
ms.author: magoedte
ms.openlocfilehash: 68031ba2ce5c47870c3e60dc469dcda2971b4d87
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="remove-azure-automation-hybrid-runbook-workers"></a>Remover Hybrid Runbook Workers da Automação do Azure

É possível 

## <a name="removing-hybrid-runbook-worker"></a>Removendo o Hybrid Runbook Worker

Você pode remover um ou mais Trabalhadores de Runbook Híbridos de um grupo ou remover o grupo, dependendo dos seus requisitos.  Para remover um Hybrid Runbook Worker de um computador local, execute as etapas a seguir.

1. No portal do Azure, abra sua Conta de Automação.  
2. Na folha **Configurações**, selecione **Teclas** e anote os valores para o campo **URL** e **Tecla de Acesso Primária**.  Você precisará dessas informações para a próxima etapa.
3. Abra uma sessão do PowerShell no modo de Administrador e execute o comando a seguir ‑ `Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>`.  Use a opção **-Verbose** para obter um log detalhado do processo de remoção.

> [!NOTE]
> Isso não remove o Microsoft Monitoring Agent do computador, apenas a funcionalidade e a configuração da função do Hybrid Runbook Worker.  

## <a name="remove-hybrid-worker-groups"></a>Remover grupos do Hybrid Worker

Para remover um grupo, primeiro você precisa remover o Hybrid Runbook Worker de cada computador membro do grupo usando o procedimento mostrado anteriormente e, em seguida, executar as seguintes etapas para remover o grupo.  

1. Abra a conta de Automação no Portal do Azure.
1. Selecione o bloco **Grupos Hybrid Worker** e, na folha **Grupos Hybrid Worker**, selecione o grupo que deseja excluir.  Depois de selecionar o grupo específico, a folha de propriedades do **grupo Hybrid Worker** é exibida.<br> ![Folha do grupo Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)   
1. Na folha de propriedades do grupo selecionado, clique em **Excluir**.  Uma mensagem será exibida solicitando que você confirme a ação. Selecione **Sim** se tiver certeza de que deseja continuar.<br> ![Caixa de diálogo de confirmação de exclusão de grupo](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)<br> Esse processo pode levar vários segundos e você pode acompanhar o progresso no menu **Notificações**. 

## <a name="next-steps"></a>Próximas etapas

* Leia [Executar runbooks em um Hybrid Runbook Worker](automation-hrw-run-runbooks.md) para aprender a configurar seus runbooks para automatizar processos em seu datacenter local ou em outro ambiente de nuvem.
* Para obter informações sobre como instalar Hybrid Runbook Workers no Windows, consulte [Hybrid Runbook Worker Windows da Automação do Azure](automation-windows-hrw-install.md).
* Para obter informações sobre como instalar Hybrid Runbook Workers no Linux, consulte [Hybrid Runbook Worker Linux da Automação do Azure](automation-linux-hrw-install.md).