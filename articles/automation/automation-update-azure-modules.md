---
title: "Atualizar módulos do Azure na Automação do Azure | Microsoft Docs"
description: "Este artigo descreve como você pode atualizar módulos comuns do Azure PowerShell fornecidos por padrão na Automação do Azure."
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
ms.date: 06/13/2017
ms.author: magoedte
ms.openlocfilehash: 6bd259f3da1005228b8137415c30660221507909
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Como atualizar módulos do Azure PowerShell na Automação do Azure

Os módulos mais comuns do Azure PowerShell são fornecidos por padrão em cada conta de Automação.  A equipe do Azure atualiza os módulos do Azure regularmente, assim, na conta de Automação fornecemos uma maneira de atualizar os módulos na conta quando novas versões são disponibilizadas do portal.  

Uma vez que os módulos são atualizados regularmente pelo grupo de produtos, as alterações podem ocorrer com os cmdlets incluídos, o que pode afetar negativamente seus runbooks dependendo do tipo de alteração, como renomear um parâmetro ou descontinuar um cmdlet inteiramente. Para evitar impacto a seus runbooks e os processos que eles automatizam, é altamente recomendável testar e validar antes de continuar.  Se você não tiver uma conta de Automação dedicada destinada para essa finalidade, considere criar uma para que você possa testar vários cenários e permutações diferentes durante o desenvolvimento de seus runbooks, além das alterações iterativas, como atualizar os módulos do PowerShell.  Depois que os resultados forem validados e você tiver aplicado as alterações necessárias, continue com a coordenação da migração de todos os runbooks que precisem de modificação e execute a atualização conforme descrito abaixo em produção.     

## <a name="updating-azure-modules"></a>Atualizando os módulos do Azure

1. Na folha Módulos da sua conta de Automação, há uma opção chamada **Atualizar Módulos do Azure**.  Ela está sempre habilitada.<br><br> ![Opção Atualizar Módulos do Azure na folha Módulos](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

2. Clique em **Atualizar Módulos do Azure** e você receberá uma notificação de confirmação que pergunta se deseja continuar.<br><br> ![Notificação de Atualizar Módulos do Azure](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. Clique em **Sim** e o processo de atualização do módulo será iniciado.  O processo de atualização leva aproximadamente de 15 a 20 minutos para atualizar os seguintes módulos:

  * As tabelas
  * Azure.Storage
  * AzureRm.Automation
  * AzureRm.Compute
  * AzureRm.Profile
  * AzureRm.Resources
  * AzureRm.Sql
  * AzureRm.Storage

    Se os módulos já estiverem atualizados, o processo será concluído em alguns segundos.  Quando o processo de atualização for concluído, você será notificado.<br><br> ![Status da atualização de Atualizar Módulos do Azure](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

> [!NOTE]
> A Automação do Azure usará os módulos mais recentes em sua conta de Automação quando um novo trabalho agendado for executado.    

Caso use cmdlets desses módulos do Azure PowerShell em seus runbooks para gerenciar recursos do Azure, convém executar esse processo de atualização todos os meses, ou quase, para garantir que você tenha os módulos mais recentes.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre Módulos de Integração e como criar módulos personalizados para integrar a Automação a outros sistemas, serviços ou soluções, confira [Módulos de integração](automation-integration-modules.md).

* Considere a integração de controle do código-fonte usando [GitHub Enterprise](automation-scenario-source-control-integration-with-github-ent.md) ou [Visual Studio Team Services](automation-scenario-source-control-integration-with-vsts.md) para gerenciar e controlar centralmente versões do seu portfólio de runbook e configuração de Automação.  