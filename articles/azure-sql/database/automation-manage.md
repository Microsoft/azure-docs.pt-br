---
title: Gerenciar bancos de dados com a automação do Azure
description: Saiba mais sobre como o serviço de automação do Azure pode ser usado para gerenciar o banco de dados SQL do Azure em escala.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: faf5b1108e28b352a0b8622feed8bdd99264ff16
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91327571"
---
# <a name="manage-databases-in-azure-sql-database-by-using-azure-automation"></a>Gerenciar bancos de dados no banco de dados SQL do Azure usando a automação do Azure

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Este guia apresentará a você o serviço de automação do Azure e como ele pode ser usado para simplificar o gerenciamento de bancos de dados no Azure SQL Database.

## <a name="about-azure-automation"></a>Sobre a Automação do Azure

[Automação do Azure](https://azure.microsoft.com/services/automation/) é um serviço do Azure para simplificar o gerenciamento de nuvem por meio da automação de processo. Com a Automação do Azure, tarefas de execução longa, manuais, propensas a erros e repetidas com frequência podem ser automatizadas para melhorar a confiabilidade, a eficiência e o tempo de implantação para sua organização. Para obter informações sobre como começar, consulte [introdução à automação do Azure](../../automation/automation-intro.md)

A Automação do Azure fornece um mecanismo de execução de fluxo de trabalho altamente confiável e altamente disponível, e que pode ser dimensionado para atender às suas necessidades à medida que sua organização cresce. Na Automação do Azure, os processos podem ser inicializados manualmente por sistemas de terceiros ou a intervalos agendados para que as tarefas aconteçam exatamente quando necessário.

Reduza o custo operacional e libere a equipe de TI/ DevOps para se concentrar no trabalho que adiciona valor comercial ao transferir as tarefas de gerenciamento de nuvem para serem executadas automaticamente pela Automação do Azure.

## <a name="how-azure-automation-can-help-manage-your-databases"></a>Como a automação do Azure pode ajudar a gerenciar seus bancos de dados

Com a automação do Azure, você pode gerenciar bancos de dados no banco de dados SQL do Azure usando [cmdlets do PowerShell](/powershell/module/servicemanagement/azure.service/#sql) que estão disponíveis nas [ferramentas de Azure PowerShell](/powershell/azure/). A automação do Azure tem esses cmdlets do PowerShell do banco de dados SQL do Azure disponíveis prontos para uso, para que você possa executar todas as tarefas de gerenciamento do banco de dados SQL dentro do serviço. Você também pode combinar esses cmdlets na Automação do Azure com os cmdlets de outros serviços do Azure para automatizar tarefas complexas em serviços do Azure e sistemas de terceiros.

A Automação do Azure também tem a capacidade de se comunicar com servidores SQL diretamente, emitindo comandos SQL usando o PowerShell.

As galerias de runbook e módulo para a [automação do Azure](../../automation/automation-runbook-gallery.md) oferecem uma variedade de Runbooks da Microsoft e da Comunidade que você pode importar para a automação do Azure. Para usar runbooks, baixe-os da galeria ou importe-os diretamente da galeria ou da sua conta da Automação no portal do Azure.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu os conceitos básicos da automação do Azure e como ele pode ser usado para gerenciar o banco de dados SQL do Azure, siga estes links para saber mais sobre a automação do Azure.

- [Visão geral da automação do Azure](../../automation/automation-intro.md)
- [Meu primeiro runbook](../../automation/learn/automation-tutorial-runbook-graphical.md)
