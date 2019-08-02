---
title: Atualizar módulos do Azure na Automação do Azure
description: Este artigo descreve como você pode atualizar módulos comuns do Azure PowerShell fornecidos por padrão na Automação do Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 06/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a42fae4e7ff9ba9edc29c64480983987e41cf9c1
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476793"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Como atualizar módulos do Azure PowerShell na Automação do Azure

Para atualizar os módulos do Azure em sua conta de automação, você precisa usar o [Azure atualizar módulos runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), que está disponível como código-fonte aberto. Para começar a usar o runbook **Update-AutomationAzureModulesForAccount** para atualizar os módulos do Azure, baixe-o do [repositório de runbooks de atualização de módulos do Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) no GitHub. Em seguida, você pode importá-lo para sua conta de Automação ou executá-lo como um script. Para saber como importar um runbook na sua conta de automação, consulte [importar um runbook](manage-runbooks.md#import-a-runbook).

Os módulos do AzureRM PowerShell mais comuns são fornecidos por padrão em cada conta de automação. A equipe do Azure atualiza os módulos do Azure regularmente, portanto mantenha atualizado você desejará usar o [AutomationAzureModulesForAccount atualização](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) runbook para atualizar os módulos em suas contas de automação.

Como os módulos são atualizados regularmente pelo grupo de produtos, as alterações podem ocorrer com os cmdlets incluídos. Essa ação pode afetar negativamente seus runbooks dependendo do tipo de alteração, como renomear um parâmetro ou descontinuar um cmdlet inteiramente.

Para evitar o impacto nos runbooks e nos processos que eles automatizam, teste e valide antes de continuar. Se você não tiver uma conta de Automação dedicada destinada para essa finalidade, considere criar uma para que você possa testar vários cenários e permutações diferentes durante o desenvolvimento de seus runbooks. Esse teste deve incluir alterações iterativas, como atualizar os módulos do PowerShell.

Se você desenvolve seus scripts localmente, é recomendável ter as mesmas versões do módulo localmente que você tem em sua Conta de Automação quando os testes para garantir que você receberá os mesmos resultados. Depois que os resultados forem validados e você tiver aplicado as alterações necessárias, você pode mover as alterações para a produção.

> [!NOTE]
> Uma nova conta de Automação pode não conter os últimos módulos.

## <a name="considerations"></a>Considerações

A seguir estão algumas considerações que se deve fazer ao usar esse processo para atualizar seus módulos do Azure:

* Esse runbook dá suporte à atualização do **Azure** e **AzureRm** módulos por padrão. Esse runbook dá suporte à atualização do **Az** módulos também. Examine os [runbook de módulos do Azure atualização Leiame](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) para obter mais informações sobre como atualizar `Az` módulos com este runbook. Há fatores importantes adicionais que você precisa levar em conta ao usar o `Az` módulos em sua conta de automação, para saber mais, consulte [módulos de Az usando em sua conta de automação](az-modules.md).

* Antes de iniciar esse runbook, verifique se sua conta de Automação tem uma [credencial da conta Executar como do Azure](manage-runas-account.md) criada.

* Você pode usar esse código como um script do PowerShell regular, em vez de um runbook: basta entrar no Azure usando o [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) comando pela primeira vez, em seguida, passar `-Login $false` ao script.

* Para usar esse runbook em nuvens soberanas, use o parâmetro `AzureRmEnvironment` para passar o ambiente correto para o runbook.  Os valores aceitáveis são **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud** e **AzureUSGovernment**. Esses valores podem ser recuperados usando `Get-AzureRmEnvironment | select Name`. Se você não passar um valor para esse parâmetro, o padrão do runbook será a nuvem pública do Azure **AzureCloud**

* Se você desejar usar uma versão específica do módulo Azure PowerShell, em vez da versão mais recente disponível na Galeria do PowerShell, passe essas versões para o parâmetro `ModuleVersionOverrides` opcional do runbook **Update-AutomationAzureModulesForAccount**. Para obter exemplos, confira o runbook [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
). Os módulos do Azure PowerShell não mencionados no parâmetro `ModuleVersionOverrides` são atualizados com as versões mais recentes do módulo na Galeria do PowerShell. Se você não passar nada para o `ModuleVersionOverrides` parâmetro, todos os módulos são atualizados com as versões mais recentes do módulo na Galeria do PowerShell. Esse comportamento é igual ao botão **Atualizar Módulos do Azure**.

## <a name="known-issues"></a>Problemas conhecidos

Há um problema conhecido com os módulos do AzureRM em uma conta de automação que está em um grupo de recursos com um nome numérico que começa com 0 de atualização. Para atualizar os módulos do Azure em sua conta de automação, ele deve ser em um grupo de recursos que tem um nome alfanumérico. Grupos de recursos com nomes numéricos, começando com 0 são não é possível atualizar os módulos AzureRM neste momento.

## <a name="next-steps"></a>Próximas etapas

Visite o [runbook de atualização de módulos do Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) de software livre para saber mais sobre ele.
