---
title: "Gerenciar o Armazenamento do Azure usando a Automação do Azure"
description: "Saiba mais sobre como o serviço de Automação do Azure pode ser usado para gerenciar o Armazenamento do Azure em grande escala."
services: storage, automation
documentationcenter: 
author: jodoglevy
manager: eamono
editor: 
ms.assetid: bac41c39-1d95-46aa-a481-ec17bbb21b40
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2016
ms.author: eamono
ms.openlocfilehash: 4649e42a628307e15f8b067503e4e8e13f16f1af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="managing-azure-storage-using-azure-automation"></a>Gerenciando o armazenamento do Azure usando a Automação do Azure
Este guia apresentará o serviço de Automação do Azure e como ele pode ser usado para simplificar o gerenciamento dos bancos de dados SQL do Azure.

## <a name="what-is-azure-automation"></a>O que é Automação do Azure?
[Automação do Azure](https://azure.microsoft.com/services/automation/) é um serviço do Azure para simplificar o gerenciamento de nuvem por meio da automação de processo. Com a Automação do Azure, tarefas manuais, de longa execução, propensas a erros e repetidas com frequência podem ser automatizadas para melhorar a confiabilidade, a eficiência e reduzir o tempo de implantação para sua organização.

A Automação do Azure fornece um mecanismo de execução de fluxo de trabalho altamente confiável e altamente disponível que pode ser dimensionado para atender às suas necessidades à medida que sua organização cresce. Na Automação do Azure, os processos podem ser inicializados manualmente por sistemas de terceiros ou a intervalos agendados para que as tarefas aconteçam exatamente quando necessário.

Reduza o custo operacional e libere a equipe de TI/ DevOps para se concentrar no trabalho que adiciona valor comercial ao transferir as tarefas de gerenciamento de nuvem para serem executadas automaticamente pela Automação do Azure.

## <a name="how-can-azure-automation-help-manage-azure-storage"></a>Como a Automação do Azure ajuda a gerenciar o Armazenamento do Azure?
O Armazenamento do Azure pode ser gerenciado na Automação do Azure usando os cmdlets do PowerShell disponíveis no [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). A Automação do Azure tem esses cmdlets do PowerShell do Armazenamento disponíveis imediatamente para que você possa executar todas as suas tarefas de blob, de tabela e de gerenciamento de fila no serviço. Você também pode combinar esses cmdlets na Automação do Azure com os cmdlets para outros serviços do Azure, para automatizar tarefas complexas nos serviços do Azure e sistemas de terceiros.

A [Galeria de runbooks de Automação do Azure](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) contém uma variedade de runbooks da comunidade e da equipe de produto para começar a automatizar o gerenciamento do Armazenamento do Azure, outros serviços do Azure e sistemas de terceiros. A galeria de runbooks inclui:

* [Remover os Blobs de Armazenamento do Azure que têm determinados dias de idade usando o serviço de automação](https://gallery.technet.microsoft.com/scriptcenter/Remove-Storage-Blobs-that-aae4b761)
* [Baixar um Blob do Armazenamento do Azure](https://gallery.technet.microsoft.com/scriptcenter/a-Blob-from-Azure-Storage-6bc13745)
* [Fazer backup de todos os discos para uma única VM do Azure ou para todas as VMs em um Serviço de Nuvem](https://gallery.technet.microsoft.com/scriptcenter/Backup-all-disks-for-a-ede940d5)

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu os fundamentos de Automação do Azure e como ela pode ser usada para gerenciar blobs de Armazenamento do Azure, siga estes links para obter mais informações sobre a Automação do Azure.

Consulte o tutorial de Automação do Azure [Criando ou importando um runbook na Automação do Azure](../../automation/automation-creating-importing-runbook.md).

