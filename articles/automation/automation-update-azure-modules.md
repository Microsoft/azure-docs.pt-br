---
title: Atualizar módulos Azure PowerShell na Automação do Azure
description: Este artigo informa como atualizar os módulos comuns de Azure PowerShell fornecidos por padrão na Automação do Azure.
services: automation
ms.subservice: process-automation
ms.date: 06/14/2019
ms.topic: conceptual
ms.openlocfilehash: f05c3247ecdd77fe4cf02db70c7ba4b2c534f959
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055341"
---
# <a name="update-azure-powershell-modules"></a>Atualizar os módulos do Azure PowerShell

Os módulos mais comuns do PowerShell são fornecidos por padrão em cada conta de Automação. Consulte [Módulos padrão](shared-resources/modules.md#default-modules). À medida que a equipe do Azure atualiza os módulos do Azure regularmente, as alterações podem ocorrer com os cmdlets incluídos. Essas alterações, por exemplo, renomear um parâmetro ou substituir totalmente um cmdlet, podem afetar negativamente seus runbooks. 

> [!NOTE]
> Não é possível excluir módulos globais, que são módulos que a Automação do Azure fornece prontos para uso.

## <a name="set-up-an-automation-account"></a>Configurar uma conta de Automação do Azure

Para evitar o impacto nos runbooks e nos processos que eles automatizam, teste e valide conforme atualiza. Se você não tiver uma conta de Automação dedicada destinada para essa finalidade, considere criar uma para que você possa testar vários cenários e permutações diferentes durante o desenvolvimento de seus runbooks. Esse teste deve incluir alterações iterativas, como atualizar os módulos do PowerShell.

Verifique se sua conta de automação tem uma [conta Executar como do Azure](automation-security-overview.md#run-as-accounts) criada.

Se você desenvolve seus scripts localmente, é recomendável ter localmente as mesmas versões do módulo que você tem em sua conta de Automação durante os testes para garantir que você receberá os mesmos resultados. Depois que os resultados forem validados e você tiver aplicado as alterações necessárias, você pode mover as alterações para a produção.

> [!NOTE]
> Uma nova conta de Automação pode não conter os últimos módulos.

## <a name="obtain-a-runbook-to-use-for-updates"></a>Obter um runbook a ser usado para atualizações

Para atualizar os módulos do Azure em sua conta de Automação, você deve usar o runbook [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), que está disponível como código-fonte aberto. Para começar a usar esse runbook para atualizar seus módulos do Azure, baixe-o no repositório do GitHub. Em seguida, é possível importá-lo para sua conta de Automação ou executá-lo como um script. Para saber como importar um runbook em sua conta de Automação do Azure, veja [Importar um runbook](manage-runbooks.md#import-a-runbook).

O runbook **Update-AutomationAzureModulesForAccount** permite a atualização dos módulos Azure, AzureRM e Az por padrão. Examine o arquivo [LEIAME Atualizar runbook dos módulos do Azure](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) para obter mais informações sobre como atualizar os módulos Az.Automation com este runbook. Há fatores adicionais importantes que é preciso levar em conta ao usar os módulos Az em sua conta de Automação. Para saber mais, veja [Gerenciar módulos na Automação do Azure](shared-resources/modules.md).

## <a name="use-update-runbook-code-as-a-regular-powershell-script"></a>Usar o código de runbook de atualização como um script regular do PowerShell

É possível usar o código do runbook como um script regular do PowerShell em vez de um runbook. Para fazer isso, entre no Azure usando o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) primeiro e, em seguida, transmita `-Login $false` para o script.

## <a name="use-the-update-runbook-on-sovereign-clouds"></a>Usar o runbook de atualização em nuvens soberanas

Para usar esse runbook em nuvens soberanas, use o parâmetro `AzEnvironment` para transmitir o ambiente correto para o runbook. Os valores aceitáveis são AzureCloud (nuvem pública do Azure), AzureChinaCloud, AzureGermanCloud, e AzureUSGovernment. Esses valores podem ser recuperados usando `Get-AzEnvironment | select Name`. Se você não passar um valor para esse cmdlet, o runbook será padronizado como AzureCloud.

## <a name="use-the-update-runbook-to-update-a-specific-module-version"></a>Usar o runbook de atualização para atualizar uma versão de módulo específica

Se você desejar usar uma versão específica do módulo Azure PowerShell, em vez do módulo mais recente disponível na Galeria do PowerShell, transmita essas versões para o parâmetro `ModuleVersionOverrides` opcional do runbook **Update-AutomationAzureModulesForAccount**. Para obter exemplos, confira o runbook [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1). Os módulos do Azure PowerShell não mencionados no parâmetro `ModuleVersionOverrides` são atualizados com as versões mais recentes do módulo na Galeria do PowerShell. Se você não passar nada para o `ModuleVersionOverrides` parâmetro, todos os módulos são atualizados com as versões mais recentes do módulo na Galeria do PowerShell. Esse comportamento é o mesmo para o botão **Atualizar Módulos do Azure** no portal do Azure.

## <a name="next-steps"></a>Próximas etapas

* Para obter detalhes sobre como usar módulos, veja [Gerenciar módulos na Automação do Azure](shared-resources/modules.md).
* Para obter informações sobre o runbook de atualização, veja [Atualizar runbook de módulos do Azure](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update).
