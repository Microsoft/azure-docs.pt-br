---
title: Atualizar módulos do Azure na Automação do Azure
description: Este artigo descreve como você pode atualizar módulos comuns do Azure PowerShell fornecidos por padrão na Automação do Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 03174e6336589f8aa49a7fc7197da1301ff54400
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61304264"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Como atualizar módulos do Azure PowerShell na Automação do Azure

Para atualizar os módulos do Azure na sua conta de Automação, é recomendável que você use o [runbook de atualização de módulos do Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), que agora é de software livre. Além disso, você ainda pode usar o botão **Atualizar Módulos do Azure** no portal para atualizar os módulos do Azure. Para saber mais sobre como usar o runbook de software livre, confira o [runbook de atualização de módulos do Azure com software livre](#open-source).

Os módulos mais comuns do Azure PowerShell são fornecidos por padrão em cada conta de Automação. A equipe do Azure atualiza os módulos do Azure regularmente. Na sua conta de Automação, você terá uma maneira de atualizar os módulos na conta quando novas versões estiverem disponíveis no portal.

Como os módulos são atualizados regularmente pelo grupo de produtos, as alterações podem ocorrer com os cmdlets incluídos. Essa ação pode afetar negativamente seus runbooks dependendo do tipo de alteração, como renomear um parâmetro ou descontinuar um cmdlet inteiramente.

Para evitar o impacto nos runbooks e nos processos que eles automatizam, teste e valide antes de continuar. Se você não tiver uma conta de Automação dedicada destinada para essa finalidade, considere criar uma para que você possa testar vários cenários e permutações diferentes durante o desenvolvimento de seus runbooks. Esse teste deve incluir alterações iterativas, como atualizar os módulos do PowerShell. 

Se você desenvolve seus scripts localmente, é recomendável ter as mesmas versões do módulo localmente que você tem em sua Conta de Automação quando os testes para garantir que você receberá os mesmos resultados. Depois que os resultados forem validados e você tiver aplicado as alterações necessárias, você pode mover as alterações para a produção.

> [!NOTE]
> Uma nova conta de Automação pode não conter os últimos módulos.

## <a name="open-source"></a>Runbook Atualizar Módulos do Azure com software livre

Para começar a usar o runbook **Update-AutomationAzureModulesForAccount** para atualizar os módulos do Azure, baixe-o do [repositório de runbooks de atualização de módulos do Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) no GitHub. Em seguida, você pode importá-lo para sua conta de Automação ou executá-lo como um script. As instruções sobre como fazer isso podem ser encontradas no [repositório de runbooks de atualização de módulos do Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update).

### <a name="considerations"></a>Considerações

A seguir estão algumas considerações que se deve fazer ao usar esse processo para atualizar seus módulos do Azure:

* Se você importar esse runbook com o nome original `Update-AutomationAzureModulesForAccount`, ele substituirá o runbook interno com esse nome. Como resultado, o runbook importado será executado quando o botão **Atualizar Módulos do Azure** for pressionado ou quando esse runbook for invocado diretamente por meio da API do Azure Resource Manager para esta conta de Automação.

* Esse runbook dá suporte à atualização apenas o **Azure** e **AzureRm** módulos no momento. [Módulos do Azure do PowerShell Az](/powershell/azure/new-azureps-module-az) têm suporte em contas de automação, mas não pode ser atualizada com este runbook.

* Evite iniciar esse runbook em contas de Automação que contêm módulos Az.

* Antes de iniciar esse runbook, verifique se sua conta de Automação tem uma [credencial da conta Executar como do Azure](manage-runas-account.md) criada.

* Você pode usar esse código como um script do PowerShell comum, em vez de um runbook: basta fazer logon no Azure usando o comando [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) pela primeira vez e, em seguida, passar `-Login $false` ao script.

* Para usar esse runbook em nuvens soberanas, use o parâmetro `AzureRmEnvironment` para passar o ambiente correto para o runbook.  Os valores aceitáveis são **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud** e **AzureUSGovernment**. Esses valores podem ser recuperados usando `Get-AzureRmEnvironment | select Name`. Se você não passar um valor para esse parâmetro, o padrão do runbook será a nuvem pública do Azure **AzureCloud**

* Se você desejar usar uma versão específica do módulo Azure PowerShell, em vez da versão mais recente disponível na Galeria do PowerShell, passe essas versões para o parâmetro `ModuleVersionOverrides` opcional do runbook **Update-AutomationAzureModulesForAccount**. Para obter exemplos, confira o runbook [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
). Os módulos do Azure PowerShell não mencionados no parâmetro `ModuleVersionOverrides` são atualizados com as versões mais recentes do módulo na Galeria do PowerShell. Se você não passar nada para o `ModuleVersionOverrides` parâmetro, todos os módulos são atualizados com as versões mais recentes do módulo na Galeria do PowerShell. Esse comportamento é igual ao botão **Atualizar Módulos do Azure**.

## <a name="update-azure-modules-in-the-azure-portal"></a>Atualizar módulos do Azure no portal do Azure

1. Na página Módulos da sua conta de Automação, há uma opção chamada **Atualizar Módulos do Azure**. Ela está sempre habilitada.<br><br> ![Opção Atualizar Módulos do Azure na página Módulos](media/automation-update-azure-modules/automation-update-azure-modules-option.png)

   > [!NOTE]
   > Antes de atualizar os módulos do Azure, é recomendável que você os atualize em uma Conta de Automação de teste para garantir que seus scripts existentes funcionem conforme o esperado antes de atualizar os módulos do Azure.
   >
   > O botão **Atualizar módulos do Azure** só está disponível na nuvem pública. Não está disponível nas [regiões soberanas](https://azure.microsoft.com/global-infrastructure/). Use o runbook **Update-AutomationAzureModulesForAccount** para atualizar os módulos do Azure. Você pode baixá-lo do [repositório de runbooks de atualização de módulos do Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update). Para saber mais sobre como usar o runbook de software livre, confira o [runbook de atualização de módulos do Azure com software livre](#open-source).

2. Clique em **Atualizar Módulos do Azure** e uma notificação de confirmação será exibida perguntando se você deseja continuar.<br><br> ![Notificação de Atualizar Módulos do Azure](media/automation-update-azure-modules/automation-update-azure-modules-popup.png)

3. Clique em **Sim** e o processo de atualização do módulo será iniciado. O processo de atualização leva aproximadamente de 15 a 20 minutos para atualizar os seguintes módulos:

   * Azure
   * Azure.Storage
   * AzureRm.Automation
   * AzureRm.Compute
   * AzureRm.Profile
   * AzureRm.Resources
   * AzureRm.Sql
   * AzureRm.Storage

     Se os módulos já estiverem atualizados, o processo será concluído em alguns segundos. Quando o processo de atualização for concluído, você será notificado.<br><br> ![Status da atualização de Atualizar Módulos do Azure](media/automation-update-azure-modules/automation-update-azure-modules-updatestatus.png)

     Não há suporte para os módulos AzureRm do .NET Core (AzureRm.*.Core) na Automação do Azure e eles não podem ser importados.

> [!NOTE]
> A Automação do Azure usa os módulos mais recentes de sua conta de Automação quando um novo trabalho agendado é executado.  

Se você utilizar cmdlets desses módulos do Azure PowerShell nos runbooks, esse processo de atualização será executado a cada mês ou mais para garantir que tenha os módulos mais recentes. A Automação do Azure usa a `AzureRunAsConnection` conexão para se autenticar ao atualizar os módulos. Se a entidade de serviço expirou ou não existe mais no nível da assinatura, a atualização do módulo falhará.

## <a name="known-issues"></a>Problemas conhecidos

Há um problema conhecido com os módulos do AzureRM em uma conta de automação que está em um grupo de recursos com um nome numérico que começa com 0 de atualização. Para atualizar os módulos do Azure em sua conta de automação, ele deve ser em um grupo de recursos que tem um nome alfanumérico. Grupos de recursos com nomes numéricos, começando com 0 são não é possível atualizar os módulos AzureRM neste momento.

## <a name="next-steps"></a>Próximas etapas

Visite o [runbook de atualização de módulos do Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) de software livre para saber mais sobre ele.
