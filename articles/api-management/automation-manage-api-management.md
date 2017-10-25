---
title: "Gerenciar o Gerenciamento de API do Azure usando a Automação do Azure"
description: "Saiba mais sobre como o serviço de Automação do Azure pode ser usado para gerenciar o Gerenciamento de API do Azure."
services: api-management, automation
documentationcenter: 
author: vladvino
manager: eamono
editor: 
ms.assetid: 2e53c9af-f738-47f8-b1b6-593050a7c51b
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: cc08f006fabf07db0f89578144f7269e3bc7a510
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="managing-azure-api-management-using-azure-automation"></a>Gerenciando o Gerenciamento de API do Azure usando a Automação do Azure
Este guia apresentará o serviço de Automação do Azure e como ele pode ser usado para simplificar o gerenciamento do Gerenciamento de API.

## <a name="what-is-azure-automation"></a>O que é Automação do Azure?
[Automação do Azure](https://azure.microsoft.com/services/automation/) é um serviço do Azure para simplificar o gerenciamento de nuvem por meio da automação de processo. Com o uso da Automação do Azure, as tarefas manuais, repetidas, de execução longa e propensas a erros podem ser automatizadas para aumentar a confiabilidade, a eficiência e o tempo de retorno para sua organização.

A Automação do Azure fornece um mecanismo de execução de fluxo de trabalho altamente confiável e altamente disponível que é dimensionado para atender às suas necessidades. Na Automação do Azure, processos podem ser inicializados manualmente, por sistemas de terceiros ou em intervalos agendados para que as tarefas acontecem exatamente quando necessário.

Reduza o custo operacional e libere a equipe de TI e DevOps para se concentrar no trabalho que agrega valor de negócios, transferindo as tarefas de gerenciamento de nuvem para serem executadas automaticamente pela Automação do Azure.

## <a name="how-can-azure-automation-help-manage-azure-api-management"></a>Como a Automação do Azure ajudar a gerenciar o Gerenciamento de API?
O Gerenciamento de API pode ser gerenciado na Automação do Azure usando a [cmdlets do Windows PowerShell para a API de Gerenciamento de API](https://azure.microsoft.com/updates/full-set-of-windows-powershell-cmdlets-for-azure-api-management-api/). Dentro de Automação do Azure, você pode escrever scripts de fluxo de trabalho do PowerShell para executar muitas de suas tarefas de Gerenciamento de API usando os cmdlets. Você também pode combinar esses cmdlets na Automação do Azure com os cmdlets para outros serviços do Azure, para automatizar tarefas complexas nos serviços do Azure e sistemas de terceiros.

Aqui estão alguns exemplos de uso do Gerenciamento de API com Automação:

* [Gerenciamento de API do Azure – Usando o PowerShell para backup e restauração](https://blogs.msdn.microsoft.com/katriend/2015/10/02/azure-api-management-using-powershell-for-backup-and-restore/)

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu os fundamentos de Automação do Azure e como ela pode ser usada para gerenciar o Gerenciamento de API do Azure, siga estes links para obter mais informações.

* Veja o [tutorial de introdução](../automation/automation-first-runbook-graphical.md)da Automação do Azure.

