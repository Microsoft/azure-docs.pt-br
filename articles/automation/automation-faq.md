---
title: Perguntas frequentes sobre a Automação do Azure | Microsoft Docs
description: Este artigo dá respostas a perguntas frequentes sobre a Automação do Azure.
services: automation
ms.subservice: ''
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: 2b40cc3d4cea4476ffde8bee8cec694975eb5083
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724265"
---
# <a name="azure-automation-frequently-asked-questions"></a>Perguntas frequentes sobre a Automação do Azure

Essas perguntas frequentes da Microsoft são uma lista de perguntas comuns sobre a Automação do Azure. Se você tiver outras dúvidas sobre seus recursos, vá para o fórum de discussão e poste suas perguntas. Quando uma pergunta é frequente, a adicionamos a este artigo para que ela seja encontrada de forma rápida e fácil.

## <a name="update-management"></a>Gerenciamento de atualizações

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>Posso impedir atualizações de nível de sistema operacional inesperadas?

Em algumas variantes do Linux, como o Red Hat Enterprise Linux, as atualizações no nível do SO podem ocorrer através de pacotes. Isso pode originar execuções de Gerenciamento de Atualizações em que o número de versão do SO é alterado. Como o Gerenciamento de Atualizações usa os mesmos métodos para atualizar pacotes que um administrador usa localmente em um computador Linux, esse comportamento é intencional.

Para evitar atualização da versão do SO por meio de implantações do Gerenciamento de Atualizações, use o recurso **Exclusão**.

No Red Hat Enterprise Linux, o nome do pacote a ser excluído seria: `redhat-release-server.x86_64`.

### <a name="why-arent-criticalsecurity-updates-applied"></a>Por que as atualizações críticas/de segurança não são aplicadas?

Ao implantar atualizações em um computador Linux, você poderá selecionar classificações de atualização. Essa opção filtra as atualizações que atendem aos critérios especificados. Esse filtro é aplicado localmente no computador quando a atualização é implantada.

Como o Gerenciamento de Atualizações realiza o enriquecimento de atualização na nuvem, você pode sinalizar algumas atualizações no Gerenciamento de Atualizações como tendo um impacto de segurança, embora o computador local não tenha essas informações. Se você aplicar atualizações críticas a um computador Linux, pode haver atualizações, que não estão marcadas como tendo um impacto de segurança nesse computador e, portanto, não são aplicadas. No entanto, o Gerenciamento de Atualizações ainda pode relatar que o computador não é compatível porque possui informações adicionais sobre a atualização relevante.

Implantar atualizações por classificação de atualização não funciona em versões RTM do CentOS. Para implantar corretamente atualizações para CentOS, selecione todas as classificações para garantir que as atualizações sejam aplicadas. Para o SUSE, a seleção de **outras atualizações** como a classificação pode instalar outras atualizações de segurança se elas estiverem relacionadas ao zypper (Gerenciador de pacotes) ou suas dependências forem necessárias primeiro. Esse comportamento é uma limitação do zypper. Em alguns casos, você pode requerer executar novamente a implantação da atualização e verificar a implantação por meio do log de atualização.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Posso implantar atualizações em locatários do Azure?

Se você tiver computadores que precisam de correções em outro locatário do Azure relatando ao Gerenciamento de Atualizações, use a seguinte solução alternativa para agendá-las. Você pode usar o cmdlet [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) com o parâmetro `ForUpdateConfiguration` especificado para criar uma agenda. Use o cmdlet [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) e passe os computadores no outro locatário para o parâmetro `NonAzureComputer`. O exemplo a seguir mostra como fazer isso.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="process-automation---python-runbooks"></a>Automação de processos-runbooks do Python

### <a name="which-python-3-version-is-supported-in-azure-automation"></a>Qual versão do Python 3 tem suporte na automação do Azure?

Para trabalhos de nuvem, há suporte para Python 3,8. Os scripts e pacotes de qualquer versão 3. x podem funcionar se o código for compatível entre diferentes versões.

Para trabalhos híbridos em Hybrid runbook Workers do Windows, você pode optar por instalar qualquer versão 3. x que deseja usar. Para trabalhos híbridos em Hybrid runbook Workers do Linux, dependemos da versão do Python 3 instalada no computador para executar o programa omsconfig do DSC e o Linux Hybrid Worker. É recomendável instalar a versão 3,6; no entanto, versões diferentes também devem funcionar se não houver alterações significativas em assinaturas de método ou contratos entre versões do Python 3.

### <a name="can-python-2-and-python-3-runbooks-run-in-same-automation-account"></a>Os runbooks do Python 2 e do Python 3 são executados na mesma conta de automação?

Sim, não há nenhuma limitação para usar runbooks do Python 2 e Python 3 na mesma conta de automação.  

### <a name="what-is-the-plan-for-migrating-existing-python-2-runbooks-and-packages-to-python-3"></a>Qual é o plano para migrar pacotes e runbooks existentes do Python 2 para o Python 3?

A automação do Azure não planeja migrar pacotes e runbooks do Python 2 para o Python 3. Você mesmo precisará executar essa migração. Os novos runbooks e pacotes existentes do Python 2 continuarão a funcionar.

### <a name="what-are-the-packages-supported-by-default-in-python-3-environment"></a>Quais são os pacotes com suporte por padrão no ambiente Python 3?

O pacote do Azure 4.0.0 é instalado por padrão no ambiente de automação do Python 3. Você pode importar manualmente uma versão superior do pacote do Azure para substituir a versão padrão.

### <a name="what-if-i-run-a-python-3-runbook-that-references-a-python-2-package-or-vice-versa"></a>E se eu executar um runbook 3 do Python que faça referência a um pacote Python 2 ou vice-versa?

O Python 2 e o Python 3 têm ambientes de execução diferentes. Enquanto um runbook Python 2 está em execução, somente pacotes python 2 podem ser importados e semelhantes para o Python 3.

### <a name="how-do-i-differentiate-between-python-2-and-python-3-runbooks-and-packages"></a>Como fazer diferenciar entre pacotes e runbooks do Python 2 e do Python 3?

O Python 3 é uma nova definição de runbook, que se distingue entre os runbooks do Python 2 e do Python 3. Da mesma forma, outro tipo de pacote é introduzido para pacotes do Python 3.

## <a name="next-steps"></a>Próximas etapas

Se sua pergunta não for respondida aqui, você poderá consultar as seguintes fontes para obter mais perguntas e respostas.

- [Automação do Azure](/answers/topics/azure-automation.html)
- [Fórum de comentários](https://feedback.azure.com/forums/905242-update-management)
