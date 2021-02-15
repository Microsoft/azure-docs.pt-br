---
title: Gerenciar serviços de nuvem do Azure (clássico) usando a automação do Azure | Microsoft Docs
description: Saiba mais sobre como o serviço de Automação do Azure pode ser usado para gerenciar serviços de nuvem do Azure em grande escala.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 530efd09f3632637c6a12648495dcff0e7bf0e6d
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743484"
---
# <a name="managing-azure-cloud-services-classic-using-azure-automation"></a>Gerenciando os serviços de nuvem do Azure (clássico) usando a automação do Azure

> [!IMPORTANT]
> Os [serviços de nuvem do Azure (suporte estendido)](../cloud-services-extended-support/overview.md) são um novo modelo de implantação baseado em Azure Resource Manager para o produto de serviços de nuvem do Azure.Com essa alteração, os serviços de nuvem do Azure em execução no modelo de implantação baseado no Azure Service Manager foram renomeados como serviços de nuvem (clássicos) e todas as novas implantações devem usar os [serviços de nuvem (suporte estendido)](../cloud-services-extended-support/overview.md).
Este guia apresentará o serviço de Automação do Azure e como ele pode ser usado para simplificar o gerenciamento de seus serviços de nuvem do Azure.

## <a name="what-is-azure-automation"></a>O que é Automação do Azure?
[Automação do Azure](https://azure.microsoft.com/services/automation/) é um serviço do Azure para simplificar o gerenciamento de nuvem por meio da automação de processo. Com a Automação do Azure, tarefas de execução longa, manuais, propensas a erros e repetidas com frequência podem ser automatizadas para melhorar a confiabilidade, a eficiência e o tempo de implantação para sua organização.

A Automação do Azure fornece um mecanismo de execução de fluxo de trabalho altamente confiável e altamente disponível que pode ser dimensionado para atender às suas necessidades à medida que sua organização cresce. Na Automação do Azure, os processos podem ser inicializados manualmente por sistemas de terceiros ou a intervalos agendados para que as tarefas aconteçam exatamente quando necessário.

Reduza o custo operacional e libere a equipe de TI/ DevOps para se concentrar no trabalho que adiciona valor comercial ao transferir as tarefas de gerenciamento de nuvem para serem executadas automaticamente pela Automação do Azure.

## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Como a Automação do Azure ajuda a gerenciar os serviços de nuvem do Azure?
Os serviços de nuvem do Azure podem ser gerenciados na Automação do Azure usando os cmdlets do PowerShell disponíveis em [Ferramentas PowerShell do Azure](/powershell/). A Automação do Azure tem esses cmdlets do PowerShell do serviço de nuvem disponíveis imediatamente para que você possa executar todas as tarefas de gerenciamento do serviço de nuvem no serviço. Você também pode combinar esses cmdlets na automação do Azure com os cmdlets para outros serviços do Azure para automatizar tarefas complexas em serviços do Azure e sistemas de terceiros.

Entre alguns usos de exemplo da Automação do Azure para gerenciar os Serviços de Nuvem do Azure estão:

* [Implantação contínua de um Serviço de Nuvem sempre que cscfg ou cspkg é atualizado no Armazenamento de Blobs do Azure](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [Reinicialização das instâncias do Serviço de Nuvem em paralelo, com um domínio de atualização por vez](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu os fundamentos de Automação do Azure e como ela pode ser usada para serviços de nuvem do Azure, siga estes links para obter mais informações sobre a Automação do Azure.

* [Visão geral da automação do Azure](../automation/automation-intro.md)
* [Meu primeiro runbook](../automation/learn/automation-tutorial-runbook-graphical.md)