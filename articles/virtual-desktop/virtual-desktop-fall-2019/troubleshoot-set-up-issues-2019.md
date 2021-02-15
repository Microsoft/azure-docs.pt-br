---
title: Criação do pool de hosts de locatários da área de trabalho virtual (clássica) do Windows
description: Como solucionar e resolver problemas de locatário e pool de hosts durante a instalação de um ambiente de locatário da área de trabalho virtual do Windows (clássico).
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8c6d26de62364b6aca671d1e4283a01c1b78c397
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95014825"
---
# <a name="tenant-and-host-pool-creation-in-windows-virtual-desktop-classic"></a>Criação de locatários e pool de hosts na área de trabalho virtual do Windows (clássico)

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows (clássica), que não é compatível com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver tentando gerenciar objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager, confira [este artigo](../troubleshoot-set-up-issues.md).

Este artigo aborda problemas durante a configuração inicial do locatário da área de trabalho virtual do Windows e da infraestrutura do pool de hosts da sessão relacionada.

## <a name="provide-feedback"></a>Fornecer comentários

Visite a [Comunidade Tecnológica da Área de Trabalho Virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para comentar sobre o serviço da Área de Trabalho Virtual do Windows com a equipe do produto e membros ativos da comunidade.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Adquirindo a imagem de várias sessões do Windows 10 Enterprise

Para usar a imagem de várias sessões do Windows 10 Enterprise, vá para o Azure Marketplace, **selecione introdução** ao  >  **Microsoft Windows 10** > e [Windows 10 Enterprise para áreas de trabalho virtuais, versão 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

> [!div class="mx-imgBorder"]
> ![Uma captura de tela de seleção do Windows 10 Enterprise para áreas de trabalho virtuais, versão 1809.](../media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>Criando locatário da área de trabalho virtual do Windows

Esta seção aborda possíveis problemas ao criar o locatário da área de trabalho virtual do Windows.

### <a name="error-aadsts650052-the-app-needs-access-to-a-service"></a>Erro: AADSTS650052 o aplicativo precisa de acesso a um serviço.

Exemplo de erro bruto:

```Error
AADSTS650052 Message The app needs access to a service(\"{name}\") that your organization
\"{organization}\" has not subscribed to or enabled. Contact your IT Admin to review the
configuration of your service subscriptions.650052 Message The app needs access to a service
(\"{name}\") that your organization \"{organization}\" has not subscribed to or enabled.
Contact your IT Admin to review the configuration of your service subscriptions.
```

**Causa:** Consentimento não concedido à área de trabalho virtual do Windows na instância do Azure Active Directory.

**Correção:** [siga este guia](./tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop) para conceder consentimento.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Erro: O usuário não está autorizado a consultar o serviço de gerenciamento

> [!div class="mx-imgBorder"]
> ![Captura de tela da janela do PowerShell na qual um usuário não está autorizado a consultar o serviço de gerenciamento.](../media/UserNotAuthorizedNewTenant.png)

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

**Correção:** Siga as instruções em [atribuir a função de aplicativo TenantCreator a um usuário em seu locatário Azure Active Directory](tenant-setup-azure-active-directory.md#assign-the-tenantcreator-application-role). Depois de seguir as instruções, você terá um usuário atribuído à função TenantCreator.

> [!div class="mx-imgBorder"]
> ![Captura de tela da função TenantCreator atribuída.](../media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Criando VMs do host de sessão de área de trabalho virtual do Windows

As VMs de host de sessão podem ser criadas de várias maneiras, mas a equipe de área de trabalho virtual do Windows dá suporte apenas a problemas de provisionamento de VM relacionados à oferta do [Azure Marketplace](https://azuremarketplace.microsoft.com/) . Para obter mais informações, consulte [problemas usando a área de trabalho virtual do Windows-provisionar uma oferta do Azure Marketplace no pool de hosts](#issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering).

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Problemas ao usar a área de trabalho virtual do Windows – provisionar uma oferta do Azure Marketplace no pool

O modelo de área de trabalho virtual do Windows – provisionar um pool de hosts está disponível no Azure Marketplace.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Erro: ao usar o link do GitHub, a mensagem "criar uma conta gratuita" aparece

> [!div class="mx-imgBorder"]
> ![Captura de tela para criar uma conta gratuita.](../media/be615904ace9832754f0669de28abd94.png)

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

### <a name="error-you-receive-template-deployment-is-not-valid-error"></a>Erro: você recebe o erro "a implantação do modelo não é válida"

> [!div class="mx-imgBorder"]
> ![Captura de tela de "implantação de modelo... erro inválido "](../media/troubleshooting-marketplace-validation-error-generic.png)

Antes de tomar uma ação específica, você precisará verificar o log de atividades para ver o erro detalhado da validação de implantação com falha.

Para exibir o erro no log de atividades:

1. Saia da oferta de implantação atual do Azure Marketplace.
2. Na barra de pesquisa superior, procure e selecione **log de atividades**.
3. Localize uma atividade denominada **validar implantação** que tenha um status de **falha** e selecione a atividade.

   > [!div class="mx-imgBorder"]
   > ![Captura de tela da atividade individual * * validar implantação * * com um * * status * * com falha](../media/troubleshooting-marketplace-validation-error-activity-summary.png)

4. Selecione JSON e role para baixo até a parte inferior da tela até ver o campo "statusMessage".

   > [!div class="mx-imgBorder"]
   > ![Captura de tela da atividade com falha, com uma caixa vermelha em torno da propriedade statusMessage do texto JSON.](../media/troubleshooting-marketplace-validation-error-json-boxed.png)

Se o modelo de operação passar pelo limite de cota, você poderá executar uma das seguintes ações para corrigi-lo:

 - Execute o Azure Marketplace com os parâmetros que você usou na primeira vez, mas desta vez use menos VMs e núcleos de VM.
 - Abra o link que você vê no campo **statusMessage** em um navegador para enviar uma solicitação para aumentar a cota de sua assinatura do Azure para a SKU de VM especificada.

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Azure Resource Manager modelo e erros de DSC (configuração de estado desejado) do PowerShell

Siga estas instruções para solucionar problemas de implantações sem êxito de modelos de Azure Resource Manager e DSC do PowerShell.

1. Examine os erros na implantação usando [Exibir operações de implantação com o Azure Resource Manager](../../azure-resource-manager/templates/deployment-history.md).
2. Se não houver erros na implantação, examine os erros no log de atividades usando [Exibir logs de atividades para auditar ações em recursos](../../azure-resource-manager/management/view-activity-logs.md).
3. Depois que o erro for identificado, use a mensagem de erro e os recursos em [solucionar erros comuns de implantação do Azure com o Azure Resource Manager](../../azure-resource-manager/templates/common-deployment-errors.md) para resolver o problema.
4. Exclua todos os recursos criados durante a implantação anterior e tente novamente implantar o modelo novamente.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Erro: falha na implantação... \<hostname> /JoinDomain

> [!div class="mx-imgBorder"]
> ![Falha na captura de tela da implantação.](../media/e72df4d5c05d390620e07f0d7328d50f.png)

Exemplo de erro bruto:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Causa 1:** As credenciais fornecidas para ingressar VMs no domínio estão incorretas.

**Correção 1:** Consulte o erro "credenciais incorretas" para as VMs não são unidas ao domínio na [sessão máquina de host configuração](troubleshoot-vm-configuration-2019.md).

**Causa 2:** O nome de domínio não é resolvido.

**Correção 2:** Consulte o [erro: o nome de domínio não é resolvido](troubleshoot-vm-configuration-2019.md#error-domain-name-doesnt-resolve) na [configuração da VM do host de sessão](troubleshoot-vm-configuration-2019.md).

**Causa 3:** Sua configuração de DNS de VNET (rede virtual) está definida como **padrão**.

Para corrigir isso, faça o seguinte:

1. Abra o portal do Azure e vá para a guia **redes virtuais** .
2. Localize sua VNET e, em seguida, selecione **servidores DNS**.
3. O menu servidores DNS deve aparecer no lado direito da tela. Nesse menu, selecione **personalizado**.
4. Verifique se os servidores DNS listados em Personalizar correspondem ao seu controlador de domínio ou Active Directory domínio. Se você não vir o servidor DNS, poderá adicioná-lo inserindo seu valor no campo **Adicionar servidor DNS** .

### <a name="error-your-deployment-failedunauthorized"></a>Erro: Falha na implantação...\Não autorizada

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**Causa:** A assinatura que você está usando é um tipo que não pode acessar os recursos necessários na região em que o cliente está tentando implantar. Por exemplo, assinaturas do MSDN, gratuitas ou educacionais podem mostrar esse erro.

**Correção:** Altere seu tipo de assinatura ou região para um que possa acessar os recursos necessários.

### <a name="error-vmextensionprovisioningerror"></a>Erro: VMExtensionProvisioningError

> [!div class="mx-imgBorder"]
> ![Falha na captura de tela de sua implantação com o estado de provisionamento do terminal.](../media/7aaf15615309c18a984673be73ac969a.png)

**Causa 1:** Erro transitório com o ambiente de área de trabalho virtual do Windows.

**Causa 2:** Erro transitório com conexão.

**Correção:** Confirme se o ambiente de área de trabalho virtual do Windows está íntegro ao entrar usando o PowerShell. Conclua o registro da VM manualmente em [criar um pool de hosts com o PowerShell](create-host-pools-powershell-2019.md).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Erro: O nome de usuário administrador especificado não é permitido

> [!div class="mx-imgBorder"]
> ![A captura de tela de sua implantação falhou na qual um administrador especificado não é permitido.](../media/f2b3d3700e9517463ef88fa41875bac9.png)

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

**Correção:** Atualize o nome de usuário ou use usuários diferentes.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Erro: A VM relatou uma falha durante o processamento da extensão

> [!div class="mx-imgBorder"]
> ![Falha na captura de tela da operação de recurso concluída com o estado de provisionamento de terminal em sua implantação.](../media/49c4a1836a55d91cd65125cf227f411f.png)

Exemplo de erro bruto:

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-provision-host-pool-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
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

**Correção:** Confirme se o nome de usuário e a senha têm acesso administrativo na máquina virtual e execute o modelo de Azure Resource Manager novamente.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Erro: DeploymentFailed – a configuração de DSC do PowerShell ' FirstSessionHost ' foi concluída com erro (s)

> [!div class="mx-imgBorder"]
> ![Falha na captura de tela da implantação com a configuração DSC do PowerShell ' FirstSessionHost ' concluída com erro (s).](../media/64870370bcbe1286906f34cf0a8646ab.png)

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

**Correção:** Confirme se o nome de usuário e a senha fornecidos têm acesso administrativo na máquina virtual e execute o modelo de Azure Resource Manager novamente.

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

**Correção:** Ao executar o modelo de Azure Resource Manager para implantar VMs de host de sessão, torne os dois primeiros caracteres exclusivos para o nome do grupo de recursos de assinatura.

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

**Correção:** Use um prefixo de host diferente.

### <a name="error-deploymentfailed--error-downloading"></a>Erro: DeploymentFailed – erro ao baixar

Exemplo de erro bruto:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-provision-host-pool-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-prod
/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**Causa:** Esse erro ocorre devido a uma rota estática, regra de firewall ou NSG que bloqueia o download do arquivo zip vinculado ao modelo de Azure Resource Manager.

**Correção:** Remova a rota estática de bloqueio, a regra de firewall ou NSG. Opcionalmente, abra o arquivo JSON do modelo de Azure Resource Manager em um editor de texto, leve o link para o arquivo zip e baixe o recurso para um local permitido.

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

**Correção:** O usuário que criou o locatário da área de trabalho virtual do Windows precisa entrar no PowerShell da área de trabalho virtual do Windows e atribuir o usuário tentado uma atribuição de função. Se você estiver executando os parâmetros do modelo de Azure Resource Manager do GitHub, siga estas instruções usando comandos do PowerShell:

```PowerShell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName "RDS Contributor" -SignInName <UPN>
```

### <a name="error-user-requires-azure-ad-multi-factor-authentication-mfa"></a>Erro: o usuário requer a MFA (autenticação multifator) do Azure AD

> [!div class="mx-imgBorder"]
> ![Falha na captura de tela de sua implantação devido à falta de autenticação multifator (MFA)](../media/MFARequiredError.png)

Exemplo de erro bruto:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**Causa:** O administrador de locatários da área de trabalho virtual do Windows especificado requer a autenticação multifator (MFA) do Azure AD para entrar.

**Correção:** Crie uma entidade de serviço e atribua a ela uma função para seu locatário da área de trabalho virtual do Windows seguindo as etapas em [tutorial: criar entidades de serviço e atribuições de função com o PowerShell](create-service-principal-role-powershell.md). Depois de verificar se você pode entrar na área de trabalho virtual do Windows com a entidade de serviço, execute novamente a oferta do Azure Marketplace ou o modelo de Azure Resource Manager do GitHub, dependendo do método que você está usando. Siga as instruções abaixo para inserir os parâmetros corretos para seu método.

Se você estiver executando a oferta do Azure Marketplace, forneça valores para os seguintes parâmetros para se autenticar corretamente na área de trabalho virtual do Windows:

- Proprietário RDS do locatário da área de trabalho virtual do Windows: entidade de serviço
- ID do aplicativo: a identificação do aplicativo da nova entidade de serviço que você criou
- Senha/Confirmar senha: o segredo de senha que você gerou para a entidade de serviço
- ID de locatário do Azure AD: a ID de locatário do Azure AD da entidade de serviço que você criou

Se você estiver executando o modelo de Azure Resource Manager do GitHub, forneça valores para os seguintes parâmetros para se autenticar corretamente na área de trabalho virtual do Windows:

- Nome UPN ou ID do aplicativo do administrador de locatários: a identificação do aplicativo da nova entidade de serviço que você criou
- Senha do administrador de locatários: o segredo de senha que você gerou para a entidade de serviço
- IsServicePrincipal: **true**
- AadTenantId: a ID de locatário do Azure AD da entidade de serviço que você criou

### <a name="error-vmsubnet-not-available-when-configuring-virtual-networks"></a>Erro: vmSubnet não disponível ao configurar redes virtuais

**Causa:** No modelo WVD Marketplace, a interface do usuário exibe apenas as sub-redes que têm pelo menos quantos endereços IP disponíveis como o número total de VMs especificado no modelo. O número real de endereços IP disponíveis na sub-rede só precisa ser igual ao número de novas VMs que estão sendo implantadas, mas isso não pode ser calculado pela interface do usuário atual.

**Correção:** Você pode especificar uma sub-rede com pelo menos tantos endereços IP disponíveis quanto o número de VMs que estão sendo adicionadas não usando a interface do usuário do Marketplace, isso pode ser feito especificando o nome da sub-rede no parâmetro "**existingSubnetName**" ao [reimplantar uma implantação existente](expand-existing-host-pool-2019.md#redeploy-from-azure) ou implantá-la [usando o modelo ARM subjacente do GitHub](create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool).

## <a name="next-steps"></a>Próximas etapas

- Confira uma visão geral da solução de problemas da Área de Trabalho Virtual do Windows e das faixas de escalonamento em [Visão geral da solução de problemas, comentários e suporte](troubleshoot-set-up-overview-2019.md).
- Confira como solucionar problemas ao configurar uma VM (máquina virtual) na Área de Trabalho Virtual do Windows em [Configuração da máquina virtual do host da sessão](troubleshoot-vm-configuration-2019.md).
- Para solucionar problemas com conexões de cliente de área de trabalho virtual do Windows, consulte [conexões do serviço área de trabalho virtual do Windows](troubleshoot-service-connection-2019.md).
- Para solucionar problemas com clientes Área de Trabalho Remota, consulte [solucionar problemas do cliente área de trabalho remota](../troubleshoot-client.md)
- Veja como solucionar problemas ao usar o PowerShell com a Área de Trabalho Virtual do Windows em [PowerShell da Área de Trabalho Virtual do Windows](troubleshoot-powershell-2019.md).
- Para saber mais sobre o serviço, consulte [ambiente de área de trabalho virtual do Windows](environment-setup-2019.md).
- Acompanhe um tutorial de solução de problemas em [Tutorial: Solucionar problemas de implantações de modelos do Resource Manager](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Para saber sobre as ações de auditoria, consulte [Auditar operações com o Gerenciador de Recursos](../../azure-resource-manager/management/view-activity-logs.md).
- Para saber sobre as ações para determinar os erros durante a implantação, consulte [Exibir operações de implantação](../../azure-resource-manager/templates/deployment-history.md).