---
title: "Visão Geral do DSC de Automação do Azure | Microsoft Docs"
description: "Uma visão geral da DSC (Configuração do Estado Desejado) da Automação do Azure, seus termos e problemas conhecidos"
services: automation
documentationcenter: dev-center-name
author: eslesar
manager: carmonm
keywords: "powershell dsc, configuração de estado desejada, powershell dsc azure"
ms.assetid: fd40cb68-c1a6-48c3-bba2-710b607d1555
ms.service: automation
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: TBD
ms.date: 06/15/2017
ms.author: eslesar
ms.openlocfilehash: 468321fa6863d78bc0d179fbe5c2ed6195040d50
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-dsc-overview"></a>Visão geral do DSC da Automação do Azure

O DSC de Automação do Azure é um serviço do Azure que permite gravar, gerenciar e compilar [configurações](https://msdn.microsoft.com/powershell/dsc/configurations) de DSC (Desired State Configuration) do PowerShell, importar [recursos de DSC](https://msdn.microsoft.com/powershell/dsc/resources) e atribuir configurações a nós de destino, tudo na nuvem.

## <a name="why-use-azure-automation-dsc"></a>Por que usar o DSC de Automação do Azure

O DSC de automação do Azure oferece várias vantagens a usar o DSC fora do Azure.

### <a name="built-in-pull-server"></a>Servidor de pull interno

A Automação do Azure oferece um [servidor de pull DSC](https://msdn.microsoft.com/en-us/powershell/dsc/pullserver) para que os nós de destino recebam configurações automaticamente, estejam em conformidade com o estado desejado e relatem a respectiva conformidade.
O servidor de pull interno na Automação do Azure elimina a necessidade de configurar e manter seu próprio servidor de pull.
A Automação do Azure pode destinar computadores Windows ou Linux físicos ou virtuais, na nuvem ou localmente.

### <a name="management-of-all-your-dsc-artifacts"></a>Gerenciamento de todos os seus artefatos de DSC

O DSC de Automação do Azure oferece a mesma camada de gerenciamento para a [Desired State Configuration do PowerShell](https://msdn.microsoft.com/powershell/dsc/overview) que a Automação do Azure oferece para scripts do PowerShell.

No portal do Azure ou do PowerShell, você pode gerenciar todas as suas configurações, recursos e nós de destino da DSC.

![Captura de tela da folha de Automação do Azure](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-log-analytics"></a>Importar dados de relatórios para o Log Analytics

Nós gerenciados com DSC de Automação do Azure enviam dados de status de relatórios detalhados para o servidor de pull interno.
Você pode configurar a DSC de Automação do Azure para enviar esses dados para seu espaço de trabalho do Log Analytics do OMS (Microsoft Operations Management Suite).
Para saber como enviar dados de status da DSC para seu espaço de trabalho de Log Analytics, consulte [Encaminhar dados de relatório de DSC de Automação do Azure para o Log Analytics do OMS](automation-dsc-diagnostics.md).

## <a name="introduction-video"></a>Vídeo de introdução

Prefere assistir do que ler? Assista ao vídeo abaixo, de maio de 2015, quando a DSC de Automação do Azure foi anunciada pela primeira vez.

>[!NOTE]
>Embora os conceitos e o ciclo de vida abordados neste vídeo estejam corretos, a DSC de Automação do Azure avançou muito desde que este vídeo foi gravado.
>Agora ele está disponível totalmente, tem uma interface do usuário mais ampla no Portal do Azure e dá suporte a vários recursos adicionais.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>Próximas etapas

* Para saber como integrar nós a serem gerenciados com DSC de Automação do Azure, consulte [Integração de computadores para gerenciamento de DSC de Automação do Azure](automation-dsc-onboarding.md)
* Para começar a usar a DSC de Automação do Azure, consulte [Introdução à DSC de Automação do Azure](automation-dsc-getting-started.md)
* Para saber mais sobre configurações da DSC de compilação para que você possa atribuí-las a nós de destino, consulte [Como compilar configurações na DSC de Automação do Azure](automation-dsc-compile.md)
* Para referência de cmdlet do PowerShell para a DSC de Automação do Azure, consulte [cmdlets da DSC de Automação do Azure](/powershell/module/azurerm.automation/#automation)
* Para obter informações sobre preços, consulte [Preços da DSC de Automação do Azure](https://azure.microsoft.com/pricing/details/automation/)
* Para ver um exemplo de como usar a DSC de Automação do Azure em um pipeline de implantação contínua, consulte [Implantação contínua em VMs de IaaS usando a DSC de Automação do Azure e o Chocolatey](automation-dsc-cd-chocolatey.md)