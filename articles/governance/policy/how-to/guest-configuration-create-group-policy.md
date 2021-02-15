---
title: Como criar definições de política de configuração de convidado da linha de base de Política de Grupo para Windows
description: Saiba como converter Política de Grupo da linha de base de segurança do Windows Server 2019 em uma definição de política.
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: 7f7e2af70efa6771d94d7ceaa14d1408175b1d12
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348637"
---
# <a name="how-to-create-guest-configuration-policy-definitions-from-group-policy-baseline-for-windows"></a>Como criar definições de política de configuração de convidado da linha de base de Política de Grupo para Windows

Antes de criar definições de política personalizadas, é uma boa ideia ler as informações de visão geral conceitual em [Azure Policy configuração de convidado](../concepts/guest-configuration.md). Para saber mais sobre a criação de definições de política de configuração de convidado personalizadas para Linux, consulte [como criar políticas de configuração de convidado para Linux](./guest-configuration-create-linux.md). Para saber mais sobre a criação de definições de política de configuração de convidado personalizadas para o Windows, consulte [como criar políticas de configuração de convidado para o Windows](./guest-configuration-create.md).

Ao auditar o Windows, a Configuração de Convidado usa um módulo de recurso [Desired State Configuration](/powershell/scripting/dsc/overview/overview) (DSC) para criar o arquivo de configuração. A configuração DSC define a condição em que o computador deve estar. Se a avaliação da configuração não estiver **em conformidade** , o efeito de política *auditIfNotExists* será disparado.
[Azure Policy configuração de convidado](../concepts/guest-configuration.md) só audita configurações dentro de computadores.

> [!IMPORTANT]
> A extensão de Configuração de Convidado é necessária para executar auditorias em máquinas virtuais do Azure. Para implantar a extensão em escala em todos os computadores Windows, atribua a seguinte definição de política:
> - [Implantar os pré-requisitos para habilitar a Política de Configuração de Convidado nas VMs do Windows.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)
> 
> Não use informações confidenciais ou segredos em pacotes de conteúdo personalizados.

A comunidade de DSC publicou o [módulo BaselineManagement](https://github.com/microsoft/BaselineManagement) para converter modelos de política de grupo exportados para o formato DSC. Junto com o cmdlet GuestConfiguration, o módulo BaselineManagement cria Azure Policy pacote de configuração de convidado para Windows do conteúdo Política de Grupo. Para obter detalhes sobre como usar o módulo BaselineManagement, consulte o artigo [início rápido: converter política de grupo em DSC](/powershell/scripting/dsc/quickstarts/gpo-quickstart).

Neste guia, percorreremos o processo para criar um pacote de configuração de convidado Azure Policy de um objeto de Política de Grupo (GPO). Enquanto a explicação descreve a conversão da linha de base de segurança do Windows Server 2019, o mesmo processo pode ser aplicado a outros GPOs.  

## <a name="download-windows-server-2019-security-baseline-and-install-related-powershell-modules"></a>Baixe a linha de base de segurança do Windows Server 2019 e instale os módulos do PowerShell relacionados

Para instalar o **DSC** , o **GuestConfiguration** , o **Gerenciamento de linha de base** e os módulos do Azure relacionados no PowerShell:

1. Em um prompt do PowerShell, execute o seguinte comando:

   ```azurepowershell-interactive
   # Install the BaselineManagement module, Guest Configuration DSC resource module, and relevant Azure modules from PowerShell Gallery
   Install-Module az.resources, az.policyinsights, az.storage, guestconfiguration, gpregistrypolicyparser, securitypolicydsc, auditpolicydsc, baselinemanagement -scope currentuser -Repository psgallery -AllowClobber
   ```

1. Crie um diretório para e baixe a linha de base de segurança do Windows Server 2019 do kit de ferramentas de conformidade de segurança do Windows.

   ```azurepowershell-interactive
   # Download the 2019 Baseline files from https://docs.microsoft.com/windows/security/threat-protection/security-compliance-toolkit-10
   New-Item -Path 'C:\git\policyfiles\downloads' -Type Directory
   Invoke-WebRequest -Uri 'https://download.microsoft.com/download/8/5/C/85C25433-A1B0-4FFA-9429-7E023E7DA8D8/Windows%2010%20Version%201909%20and%20Windows%20Server%20Version%201909%20Security%20Baseline.zip' -Out C:\git\policyfiles\downloads\Server2019Baseline.zip
   ```

1. Desbloqueie e expanda a linha de base do servidor baixado 2019.

   ```azurepowershell-interactive
   Unblock-File C:\git\policyfiles\downloads\Server2019Baseline.zip
   Expand-Archive -Path C:\git\policyfiles\downloads\Server2019Baseline.zip -DestinationPath C:\git\policyfiles\downloads\
   ```

1. Valide o conteúdo da linha de base do servidor 2019 usando **MapGuidsToGpoNames.ps1**.

   ```azurepowershell-interactive
   # Show content details of downloaded GPOs
   C:\git\policyfiles\downloads\Scripts\Tools\MapGuidsToGpoNames.ps1 -rootdir C:\git\policyfiles\downloads\GPOs\ -Verbose
   ```

## <a name="convert-from-group-policy-to-azure-policy-guest-configuration"></a>Converter de Política de Grupo para Azure Policy configuração de convidado

Em seguida, convertemos a linha de base do servidor baixado 2019 em um pacote de configuração de convidado usando os módulos configuração de convidado e gerenciamento de linha de base.

1. Converta o Política de Grupo para a configuração de estado desejado usando o módulo de gerenciamento de linha de base.

   ```azurepowershell-interactive
   ConvertFrom-GPO -Path 'C:\git\policyfiles\downloads\GPOs\{3657C7A2-3FF3-4C21-9439-8FDF549F1D68}\' -OutputPath 'C:\git\policyfiles\' -OutputConfigurationScript -Verbose
   ```

1. Renomeie, reformate e execute os scripts convertidos antes de criar um pacote de conteúdo de política.

   ```azurepowershell-interactive
   Rename-Item -Path C:\git\policyfiles\DSCFromGPO.ps1 -NewName C:\git\policyfiles\Server2019Baseline.ps1
   (Get-Content -Path C:\git\policyfiles\Server2019Baseline.ps1).Replace('DSCFromGPO', 'Server2019Baseline') | Set-Content -Path C:\git\policyfiles\Server2019Baseline.ps1
   (Get-Content -Path C:\git\policyfiles\Server2019Baseline.ps1).Replace('PSDesiredStateConfiguration', 'PSDscResources') | Set-Content -Path C:\git\policyfiles\Server2019Baseline.ps1
   C:\git\policyfiles\Server2019Baseline.ps1
   ```

1. Crie um pacote de conteúdo de configuração de convidado Azure Policy.

   ```azurepowershell-interactive
   New-GuestConfigurationPackage -Name Server2019Baseline -Configuration c:\git\policyfiles\localhost.mof -Verbose
   ```

## <a name="create-azure-policy-guest-configuration"></a>Criar Azure Policy configuração de convidado

1. A próxima etapa é publicar o arquivo no armazenamento de BLOBs do Azure. O comando `Publish-GuestConfigurationPackage` requer o `Az.Storage` módulo.

   ```azurepowershell-interactive
   Publish-GuestConfigurationPackage -Path ./AuditBitlocker.zip -ResourceGroupName  myResourceGroupName -StorageAccountName myStorageAccountName
   ```

1. Depois que um pacote de política personalizada de Configuração de Convidado tiver sido criado e carregado, crie a definição de política de Configuração de Convidado. Use o `New-GuestConfigurationPolicy` cmdlet para criar a configuração de convidado.

   ```azurepowershell-interactive
    $NewGuestConfigurationPolicySplat = @{
        ContentUri = $Uri 
        DisplayName = 'Server 2019 Configuration Baseline' 
        Description 'Validation of using a completely custom baseline configuration for Windows VMs' 
        Path = 'C:\git\policyfiles\policy'  
        Platform = Windows 
        }
   New-GuestConfigurationPolicy @NewGuestConfigurationPolicySplat
   ```
    
1. Publique as definições de política usando o `Publish-GuestConfigurationPolicy` cmdlet. O cmdlet tem apenas o parâmetro **Path** que aponta para o local dos arquivos JSON criados por `New-GuestConfigurationPolicy`. Para executar o comando Publish, você precisa ter acesso para criar definições de política no Azure. Os requisitos de autorização específicos estão documentados na página [Visão geral do Azure Policy](../overview.md#getting-started). A melhor função interna é a de **Colaborador da política de recurso**.

   ```azurepowershell-interactive
   Publish-GuestConfigurationPolicy -Path C:\git\policyfiles\policy\ -Verbose
   ```

## <a name="assign-guest-configuration-policy-definition"></a>Atribuir definição de política de configuração de convidado

Com a política criada no Azure, a última etapa é atribuir a iniciativa. Veja como atribuir a iniciativa com [Portal](../assign-policy-portal.md), [CLI do Azure](../assign-policy-azurecli.md) e [Azure PowerShell](../assign-policy-powershell.md).

> [!IMPORTANT]
> As definições de política de configuração de convidado devem **sempre** ser atribuídas usando a iniciativa que combina as políticas _AuditIfNotExists_ e _DeployIfNotExists_ . Caso apenas a política _AuditIfNotExists_ seja atribuída, os pré-requisitos não serão implantados, e a política sempre mostrará que “0” servidores estão em conformidade.

Atribuir uma definição de política com o efeito _DeployIfNotExists_ requer um nível adicional de acesso. Para conceder o privilégio mínimo, você pode criar uma definição de função personalizada que estenda o **Colaborador da política de recursos**. O exemplo a seguir cria uma função denominada **Colaborador da política de recurso restaurante** com a permissão adicional _Microsoft.Authorization/roleAssignments/write_.

   ```azurepowershell-interactive
   $subscriptionid = '00000000-0000-0000-0000-000000000000'
   $role = Get-AzRoleDefinition "Resource Policy Contributor"
   $role.Id = $null
   $role.Name = "Resource Policy Contributor DINE"
   $role.Description = "Can assign Policies that require remediation."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.Authorization/roleAssignments/write")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/$subscriptionid")
   New-AzRoleDefinition -Role $role
   ```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as VMs de auditoria com a [Configuração de Convidado](../concepts/guest-configuration.md).
- Entenda como [criar políticas de forma programática](./programmatically-create.md).
- Saiba como [obter dados de conformidade](./get-compliance-data.md).
