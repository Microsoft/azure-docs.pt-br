---
title: Criação de locatário de área de trabalho virtual do Windows e pool de hosts-Azure
description: Como solucionar e resolver problemas de locatário e pool de hosts durante a instalação de um ambiente de locatário de área de trabalho virtual do Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: v-chjenk
ms.openlocfilehash: 9e58c3bfc0e74f9aac61085608a843954b0923c0
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249551"
---
# <a name="tenant-and-host-pool-creation"></a>Criação do pool de host e de locatário

Este artigo aborda problemas durante a configuração inicial do locatário da área de trabalho virtual do Windows e da infraestrutura do pool de hosts da sessão relacionada.

## <a name="provide-feedback"></a>Fornecer comentários

Atualmente, não estamos usando casos de suporte durante a versão prévia da Área de Trabalho Virtual do Windows. Visite a [Comunidade Tecnológica da Área de Trabalho Virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para comentar sobre o serviço da Área de Trabalho Virtual do Windows com a equipe do produto e membros ativos da comunidade.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Adquirindo a imagem de várias sessões do Windows 10 Enterprise

Para usar a imagem de várias sessões do Windows 10 Enterprise, vá para o Azure Marketplace, **selecione** > introdução ao**Microsoft Windows 10** > e [Windows 10 Enterprise para desktops virtuais Preview, versão 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

![Uma captura de tela de seleção do Windows 10 Enterprise para desktops virtuais Preview, versão 1809.](media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>Criando locatário da área de trabalho virtual do Windows

Esta seção aborda possíveis problemas ao criar o locatário da área de trabalho virtual do Windows.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Erro: O usuário não está autorizado a consultar o serviço de gerenciamento

![Captura de tela da janela do PowerShell na qual um usuário não está autorizado a consultar o serviço de gerenciamento.](media/UserNotAuthorizedNewTenant.png)

Exemplo de erro bruto:

```Error
   New-RdsTenant : User isn't authorized to query the management service.
   ActivityId: ad604c3a-85c6-4b41-9b81-5138162e5559
   Powershell commands to diagnose the failure:
   Get-RdsDiagnosticActivities -ActivityId ad604c3a-85c6-4b41-9b81-5138162e5559
   At line:1 char:1
   + New-RdsTenant -Name "testDesktopTenant" -AadTenantId "01234567-89ab-c ...
   + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
       + CategoryInfo          : FromStdErr: (Microsoft.RDInf...nt.NewRdsTenant:NewRdsTenant) [New-RdsTenant], RdsPowerSh
      ellException
       + FullyQualifiedErrorId : UnauthorizedAccess,Microsoft.RDInfra.RDPowershell.Tenant.NewRdsTenant
```

**Causa:** O usuário que está conectado não recebeu a função TenantCreator em seu Azure Active Directory.

**Soluciona** Siga as instruções em [atribuir a função de aplicativo TenantCreator a um usuário em seu locatário Azure Active Directory](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory#assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant). Depois de seguir as instruções, você terá um usuário atribuído à função TenantCreator.

![Captura de tela da função TenantCreator atribuída.](media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Criando VMs do host de sessão de área de trabalho virtual do Windows

As VMs de host de sessão podem ser criadas de várias maneiras, mas as equipes de área de trabalho virtual Serviços de Área de Trabalho Remota/Windows só dão suporte a problemas de provisionamento de VM relacionados ao modelo de Azure Resource Manager. O modelo de Azure Resource Manager está disponível no [Azure Marketplace](https://azuremarketplace.microsoft.com/) e no [GitHub](https://github.com/).

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Problemas ao usar a área de trabalho virtual do Windows – provisionar uma oferta do Azure Marketplace no pool

O modelo de área de trabalho virtual do Windows – provisionar um pool de hosts está disponível no Azure Marketplace.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Erro: Ao usar o link do GitHub, a mensagem "criar uma conta gratuita" é exibida

![Captura de tela para criar uma conta gratuita.](media/be615904ace9832754f0669de28abd94.png)

**Causa 1:** Não há assinaturas ativas na conta usada para entrar no Azure ou a conta usada não tem permissões para exibir as assinaturas.

**Correção 1:** Entre com uma conta que tenha acesso de colaborador (no mínimo) à assinatura em que as VMs do host de sessão serão implantadas.

**Causa 2:** A assinatura que está sendo usada faz parte de um locatário do CSP (provedor de serviços Microsoft Cloud).

**Correção 2:** Vá para o local do GitHub para **criar e provisionar o novo pool de hosts da área de trabalho virtual do Windows** e siga estas instruções:

1. Clique com o botão direito do mouse em **implantar no Azure** e selecione **copiar endereço do link**.
2. Abra o **bloco de notas** e cole o link.
3. Antes do caractere #, insira o nome do locatário do cliente final do CSP.
4. Abra o novo link em um navegador e o portal do Azure carregará o modelo.

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Azure Resource Manager modelo e erros de DSC (configuração de estado desejado) do PowerShell

Siga estas instruções para solucionar problemas de implantações sem êxito de modelos de Azure Resource Manager e DSC do PowerShell.

1. Examine os erros na implantação usando [Exibir operações de implantação com o Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
2. Se não houver erros na implantação, examine os erros no log de atividades usando [Exibir logs de atividades para auditar ações em recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
3. Depois que o erro for identificado, use a mensagem de erro e os recursos em [solucionar erros comuns de implantação do Azure com o Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors) para resolver o problema.
4. Exclua todos os recursos criados durante a implantação anterior e tente novamente implantar o modelo novamente.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Erro: Sua implantação falhou....\<hostname >/JoinDomain

![Falha na captura de tela da implantação.](media/e72df4d5c05d390620e07f0d7328d50f.png)

Exemplo de erro bruto:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. 
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Causa 1:** As credenciais fornecidas para ingressar VMs no domínio estão incorretas.

**Correção 1:** Consulte o erro "credenciais incorretas" para as VMs não são unidas ao domínio na [sessão máquina de host configuração](troubleshoot-vm-configuration.md).

**Causa 2:** O nome de domínio não é resolvido.

**Correção 2:** Consulte o erro "o nome de domínio não resolve" para as VMs não são unidas ao domínio em [sessão host configuração de VM](troubleshoot-vm-configuration.md).


### <a name="error-your-deployment-failedunauthorized"></a>Erro: Falha na implantação. ..\Unauthorized

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**Causa:** A assinatura que você está usando é um tipo que não pode acessar os recursos necessários na região em que o cliente está tentando implantar. Por exemplo, assinaturas do MSDN, gratuitas ou educacionais podem mostrar esse erro.

**Soluciona** Altere seu tipo de assinatura ou região para um que possa acessar os recursos necessários.

### <a name="error-vmextensionprovisioningerror"></a>Erro: VMExtensionProvisioningError

![Falha na captura de tela de sua implantação com o estado de provisionamento do terminal.](media/7aaf15615309c18a984673be73ac969a.png)

**Causa 1:** Erro transitório com o ambiente de área de trabalho virtual do Windows.

**Causa 2:** Erro transitório com conexão.

**Soluciona** Confirme se o ambiente de área de trabalho virtual do Windows está íntegro ao entrar usando o PowerShell. Conclua o registro da VM manualmente em [criar um pool de hosts com o PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Erro: O nome de usuário do administrador especificado não é permitido

![A captura de tela de sua implantação falhou na qual um administrador especificado não é permitido.](media/f2b3d3700e9517463ef88fa41875bac9.png)

Exemplo de erro bruto:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**Causa:** A senha fornecida contém subcadeias de caracteres proibidas (administrador, administrador, raiz).

**Soluciona** Atualize o nome de usuário ou use usuários diferentes.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Erro: A VM relatou uma falha ao processar a extensão

![Falha na captura de tela da operação de recurso concluída com o estado de provisionamento de terminal em sua implantação.](media/49c4a1836a55d91cd65125cf227f411f.png)

Exemplo de erro bruto:

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-hostpool4-preview-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
 { "provisioningOperation": "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T21:43:05.1416423Z",
 "duration": "PT7M56.8150879S", "trackingId": "43c4f71f-557c-4abd-80c3-01f545375455", "statusCode": "Conflict",
 "statusMessage": { "status": "Failed", "error": { "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'. 
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few: 
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message: 
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] } }, "targetResource": 
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft. 
 Compute/virtualMachines/desktop-1/extensions/dscextension",
 "resourceType": "Microsoft.Compute/virtualMachines/extensions", "resourceName": "desktop-1/dscextension" } }}
```

**Causa:** A extensão DSC do PowerShell não pôde obter acesso de administrador na VM.

**Soluciona** Confirme se o nome de usuário e a senha têm acesso administrativo na máquina virtual e execute o modelo de Azure Resource Manager novamente.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Erro: DeploymentFailed – configuração ' FirstSessionHost ' de DSC do PowerShell concluída com erro (s)

![Falha na captura de tela da implantação com a configuração DSC do PowerShell ' FirstSessionHost ' concluída com erro (s).](media/64870370bcbe1286906f34cf0a8646ab.png)

Exemplo de erro bruto:

```Error
{
    "code": "DeploymentFailed",
   "message": "At least one resource deployment operation failed. Please list 
 deployment operations for details. 4 Please see https://aka.ms/arm-debug for usage details.",
 "details": [
         { "code": "Conflict",  
         "message": "{\r\n \"status\": \"Failed\",\r\n \"error\": {\r\n \"code\":
         \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource
         operation completed with terminal provisioning state 'Failed'.\",\r\n
         \"details\": [\r\n {\r\n \"code\":
        \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has
              reported a failure when processing extension 'dscextension'.
              Error message: \\\"DSC Configuration 'FirstSessionHost'
              completed with error(s). Following are the first few:
              PowerShell DSC resource MSFT ScriptResource failed to
              execute Set-TargetResource functionality with error message:
              One or more errors occurred. The SendConfigurationApply
              function did not succeed.\\\".\"\r\n }\r\n ]\r\n }\r\n}"  }

```

**Causa:** A extensão DSC do PowerShell não pôde obter acesso de administrador na VM.

**Soluciona** Confirme se o nome de usuário e a senha fornecidos têm acesso administrativo na máquina virtual e execute o modelo de Azure Resource Manager novamente.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Erro: DeploymentFailed – InvalidResourceReference

Exemplo de erro bruto:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/networkInterfaces/erd. Please make sure that
the referenced resource exists, and that both resources are in the same
region.\\\",\\r\\n\\\"details\\\": []\\r\\n }\\r\\n}\"\r\n }\r\n ]\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Causa:** Parte do nome do grupo de recursos é usada para determinados recursos que estão sendo criados pelo modelo. Devido ao nome que corresponde aos recursos existentes, o modelo pode selecionar um recurso existente de um grupo diferente.

**Soluciona** Ao executar o modelo de Azure Resource Manager para implantar VMs de host de sessão, torne os dois primeiros caracteres exclusivos para o nome do grupo de recursos de assinatura.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Erro: DeploymentFailed – InvalidResourceReference

Exemplo de erro bruto:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/DEMO/providers/Microsoft.Network/networkInterfaces
/EXAMPLE was not found. Please make sure that the referenced resource exists, and that both
resources are in the same region.\\\",\\r\\n \\\"details\\\": []\\r\\n }\\r\\n}\"\r\n
}\r\n ]\r\n }\r\n ]\r\n }\r\n\
```

**Causa:** Esse erro ocorre porque a NIC criada com o modelo de Azure Resource Manager tem o mesmo nome que outra NIC já está na VNET.

**Soluciona** Use um prefixo de host diferente.

### <a name="error-deploymentfailed--error-downloading"></a>Erro: DeploymentFailed – erro ao baixar

Exemplo de erro bruto:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-hostpool-3-preview-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-private-preview-
1/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**Causa:** Esse erro ocorre devido a uma rota estática, regra de firewall ou NSG que bloqueia o download do arquivo zip vinculado ao modelo de Azure Resource Manager.

**Soluciona** Remova a rota estática de bloqueio, a regra de firewall ou NSG. Opcionalmente, abra o arquivo JSON do modelo de Azure Resource Manager em um editor de texto, leve o link para o arquivo zip e baixe o recurso para um local permitido.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Erro: O usuário não está autorizado a consultar o serviço de gerenciamento

Exemplo de erro bruto:

```Error
"response": { "content": { "startTime": "2019-04-01T17:45:33.3454563+00:00", "endTime": "2019-04-01T17:48:52.4392099+00:00", 
"status": "Failed", "error": { "code": "VMExtensionProvisioningError", "message": "VM has reported a failure when processing 
extension 'dscextension'. Error message: \"DSC Configuration 'FirstSessionHost' completed with error(s). 
Following are the first few: PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource
 functionality with error message: User is not authorized to query the management service.
\nActivityId: 1b4f2b37-59e9-411e-9d95-4f7ccd481233\nPowershell commands to diagnose the failure:
\nGet-RdsDiagnosticActivities -ActivityId 1b4f2b37-59e9-411e-9d95-4f7ccd481233\n 
The SendConfigurationApply function did not succeed.\"." }, "name": "2c3272ec-d25b-47e5-8d70-a7493e9dc473" } } }}
```

**Causa:** O administrador de locatários da área de trabalho virtual do Windows especificado não tem uma atribuição de função válida.

**Soluciona** O usuário que criou o locatário da área de trabalho virtual do Windows precisa entrar no PowerShell da área de trabalho virtual do Windows e atribuir o usuário tentado uma atribuição de função. Se você estiver executando os parâmetros do modelo de Azure Resource Manager do GitHub, siga estas instruções usando comandos do PowerShell:

```PowerShell
Add-RdsAccount -DeploymentUrl “https://rdbroker.wvd.microsoft.com”
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName “RDS Contributor” -SignInName <UPN>
```

### <a name="error-user-requires-azure-multi-factor-authentication-mfa"></a>Erro: O usuário requer a MFA (autenticação multifator) do Azure

![Falha na captura de tela de sua implantação devido à falta de autenticação multifator (MFA)](media/MFARequiredError.png)

Exemplo de erro bruto:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**Causa:** O administrador de locatários da área de trabalho virtual do Windows especificado requer a autenticação multifator do Azure (MFA) para entrar.

**Soluciona** Crie uma entidade de serviço e atribua a ela uma função para seu locatário da área de trabalho virtual do [Windows seguindo as etapas no tutorial: Crie entidades de serviço e atribuições de função](https://docs.microsoft.com/azure/virtual-desktop/create-service-principal-role-powershell)com o PowerShell. Depois de verificar se você pode entrar na área de trabalho virtual do Windows com a entidade de serviço, execute novamente a oferta do Azure Marketplace ou o modelo de Azure Resource Manager do GitHub, dependendo do método que você está usando. Siga as instruções abaixo para inserir os parâmetros corretos para seu método.

Se você estiver executando a oferta do Azure Marketplace, forneça valores para os seguintes parâmetros para se autenticar corretamente na área de trabalho virtual do Windows:

- Proprietário RDS do locatário da área de trabalho virtual do Windows: Entidade de serviço
- ID do aplicativo: A identificação do aplicativo da nova entidade de serviço que você criou
- Senha/Confirmar senha: O segredo de senha que você gerou para a entidade de serviço
- ID do locatário do Azure AD: A ID de locatário do Azure AD da entidade de serviço que você criou

Se você estiver executando o modelo de Azure Resource Manager do GitHub, forneça valores para os seguintes parâmetros para se autenticar corretamente na área de trabalho virtual do Windows:

- Nome UPN ou ID do aplicativo do administrador de locatários: A identificação do aplicativo da nova entidade de serviço que você criou
- Senha do administrador de locatários: O segredo de senha que você gerou para a entidade de serviço
- IsServicePrincipal: **true**
- AadTenantId: A ID de locatário do Azure AD da entidade de serviço que você criou

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral da solução de problemas da área de trabalho virtual do Windows e das faixas de escalonamento, consulte [visão geral da solução de problemas, comentários e suporte](troubleshoot-set-up-overview.md).
- Para solucionar problemas durante a configuração de uma VM (máquina virtual) na área de trabalho virtual do Windows, consulte [configuração de máquina virtual do host de sessão](troubleshoot-vm-configuration.md).
- Para solucionar problemas com conexões de cliente de área de trabalho virtual do Windows, consulte [área de trabalho remota conexões de cliente](troubleshoot-client-connection.md).
- Para solucionar problemas ao usar o PowerShell com a área de trabalho virtual do Windows, consulte [PowerShell da área de trabalho virtual do Windows](troubleshoot-powershell.md).
- Para saber mais sobre o serviço de visualização, consulte [ambiente do Windows Desktop Preview](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Para percorrer um tutorial de solução de problemas, confira [Tutorial: Solucionar problemas de implantações](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)de modelo do Resource Manager.
- Para saber sobre as ações de auditoria, consulte [Auditar operações com o Gerenciador de Recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Para saber sobre as ações para determinar os erros durante a implantação, consulte [Exibir operações de implantação](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
