---
title: Criação do pool de host de locatário da Área de Trabalho Virtual do Windows (clássica) – Azure
description: Como solucionar e resolver problemas do pool de host e de locatários durante a configuração de um ambiente de locatário da Área de Trabalho Virtual do Windows (clássica).
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 12be4611f0583c6da630b9e5a0e4b5a82ebfa650
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444268"
---
# <a name="tenant-and-host-pool-creation-in-windows-virtual-desktop-classic"></a>Criação de pool de host e de locatários na Área de Trabalho Virtual do Windows (clássica)

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows (clássica), que não é compatível com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver tentando gerenciar objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager, confira [este artigo](../troubleshoot-set-up-issues.md).

Este artigo aborda problemas durante a configuração inicial do locatário da Área de Trabalho Virtual do Windows e da infraestrutura do pool de host da sessão relacionada.

## <a name="provide-feedback"></a>Fornecer comentários

Visite a [Comunidade Tecnológica da Área de Trabalho Virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para comentar sobre o serviço da Área de Trabalho Virtual do Windows com a equipe do produto e membros ativos da comunidade.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Adquirir a imagem de várias sessões do Windows 10 Enterprise

Para usar a imagem de várias sessões do Windows 10 Enterprise, vá para o Azure Marketplace, selecione **Introdução** > **Microsoft Windows 10** > e [Windows 10 Enterprise para Áreas de Trabalho Virtuais, versão 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

> [!div class="mx-imgBorder"]
> ![Uma captura de tela de seleção do Windows 10 Enterprise para Áreas de Trabalho Virtuais, versão 1809.](../media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant&quot;></a>Criar um locatário da Área de Trabalho Virtual do Windows

Esta seção aborda possíveis problemas na criação do locatário da Área de Trabalho Virtual do Windows.

### <a name=&quot;error-aadsts650052-the-app-needs-access-to-a-service&quot;></a>Erro: AADSTS650052 O aplicativo precisa de acesso a um serviço.

Exemplo de erro bruto:

```Error
AADSTS650052 Message The app needs access to a service(\"{name}\") that your organization
\"{organization}\" has not subscribed to or enabled. Contact your IT Admin to review the
configuration of your service subscriptions.650052 Message The app needs access to a service
(\"{name}\") that your organization \"{organization}\" has not subscribed to or enabled.
Contact your IT Admin to review the configuration of your service subscriptions.
```

**Causa:** consentimento não concedido à Área de Trabalho Virtual do Windows na instância do Azure Active Directory.

**Correção:** [siga este guia](./tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop) para conceder consentimento.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Erro: O usuário não está autorizado a consultar o serviço de gerenciamento

> [!div class="mx-imgBorder"]
> ![Captura de tela da janela do PowerShell na qual um usuário não tem autorização para consultar o serviço de gerenciamento.](../media/UserNotAuthorizedNewTenant.png)

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

**Causa:** o usuário que entrou não recebeu a função TenantCreator no Azure Active Directory.

**Correção:** siga as instruções em [Atribuir a função de aplicativo TenantCreator a um usuário em seu locatário do Azure Active Directory](tenant-setup-azure-active-directory.md#assign-the-tenantcreator-application-role). Depois de seguir as instruções, um usuário será atribuído à função TenantCreator.

> [!div class="mx-imgBorder"]
> ![Captura de tela da função TenantCreator atribuída.](../media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Criar VMs do host de sessão de Área de Trabalho Virtual do Windows

As VMs de host de sessão podem ser criadas de várias maneiras, mas a equipe da Área de Trabalho Virtual do Windows dá suporte apenas a problemas de provisionamento de VM relacionados à oferta do [Azure Marketplace](https://azuremarketplace.microsoft.com/). Para obter mais informações, confira [Problemas no uso da Área de Trabalho Virtual do Windows – Provisionar uma oferta do Azure Marketplace do pool de host](#issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering).

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Problemas no uso da Área de Trabalho Virtual do Windows – Provisionar uma oferta do Azure Marketplace do pool de host

O modelo de Área de Trabalho Virtual do Windows – Provisionar um pool de host está disponível no Azure Marketplace.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Erro: quando você usa o link do GitHub, a mensagem "Criar uma conta gratuita" é exibida

> [!div class="mx-imgBorder"]
> ![Captura de tela para criar uma conta gratuita.](../media/be615904ace9832754f0669de28abd94.png)

**Causa 1:** não há assinaturas ativas na conta usada para entrar no Azure ou a conta usada não tem permissões para exibir as assinaturas.

**Correção 1:** entre com uma conta que tenha acesso de colaborador (no mínimo) à assinatura em que as VMs do host de sessão serão implantadas.

**Causa 2:** a assinatura que está sendo usada faz parte de um locatário do CSP (Provedor de Serviços de Nuvem da Microsoft).

**Correção 2:** acesse a localização do GitHub para **Criar e provisionar o pool de host da Área de Trabalho Virtual do Windows** e siga estas instruções:

1. Clique com o botão direito do mouse em **Implantar no Azure** e selecione **Copiar endereço do link**.
2. Abra o **Bloco de notas** e cole este link.
3. Antes do caractere #, insira o nome do locatário do cliente final do CSP.
4. Abra o novo link em um navegador e o portal do Azure carregará o modelo.

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

### <a name="error-you-receive-template-deployment-is-not-valid-error"></a>Erro: você recebe o erro "a implantação do modelo não é válida"

> [!div class="mx-imgBorder"]
> ![Captura de tela do erro "a implantação do não é válida…"](../media/troubleshooting-marketplace-validation-error-generic.png)

Antes de executar uma ação específica, você precisará verificar o log de atividades para ver o erro detalhado da validação de implantação com falha.

Para ver o erro no log de atividades:

1. Saia da oferta de implantação atual do Azure Marketplace.
2. Na barra de pesquisa na parte superior, procure e selecione **Log de Atividades**.
3. Localize uma atividade denominada **Validar Implantação** que tenha um status **Com falha** e selecione-a.

   > [!div class="mx-imgBorder"]
   > ![Captura de tela da atividade individual **Validar Implantação** com um status **Com falha**](../media/troubleshooting-marketplace-validation-error-activity-summary.png)

4. Selecione o JSON e role para baixo até a parte inferior da tela até ver o campo "statusMessage".

   > [!div class="mx-imgBorder"]
   > ![Captura de tela da atividade com falha, com uma caixa vermelha em torno da propriedade statusMessage do texto JSON.](../media/troubleshooting-marketplace-validation-error-json-boxed.png)

Se o modelo de operação exceder o limite de cota, você poderá executar uma das seguintes ações para corrigir esse problema:

 - Execute o Azure Marketplace com os parâmetros que você usou na primeira vez, mas desta vez use menos VMs e núcleos de VM.
 - Abra o link que você vê no campo **statusMessage** em um navegador para enviar uma solicitação para aumentar a cota da sua assinatura do Azure para o SKU de VM especificado.

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Erros do modelo do Azure Resource Manager e do DSC (Desired State Configuration) do PowerShell

Siga estas instruções para solucionar problemas de implantações sem êxito de modelos do Azure Resource Manager e do DSC do PowerShell.

1. Examine os erros na implantação usando [Exibir operações de implantação com o Azure Resource Manager](../../azure-resource-manager/templates/deployment-history.md).
2. Se não houver erros na implantação, examine os erros no log de atividades usando [Exibir logs de atividades para auditar ações em recursos](../../azure-resource-manager/management/view-activity-logs.md).
3. Depois que o erro for identificado, use a mensagem de erro e os recursos em [Solucionar erros comuns de implantação do Azure com o Azure Resource Manager](../../azure-resource-manager/templates/common-deployment-errors.md) para resolver o problema.
4. Exclua todos os recursos criados durante a implantação anterior e tente implantar o modelo novamente.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Erro: Falha na implantação…\<hostname>/joindomain

> [!div class="mx-imgBorder"]
> ![Captura de tela de Falha na implantação.](../media/e72df4d5c05d390620e07f0d7328d50f.png)

Exemplo de erro bruto:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Causa 1:** as credenciais fornecidas para ingressar VMs no domínio estão incorretas.

**Correção 1:** confira o erro "Credenciais incorretas" para as VMs que não foram ingressadas no domínio em [Configuração da VM do host da sessão](troubleshoot-vm-configuration-2019.md).

**Causa 2:** o nome de domínio não é resolvido.

**Correção 2:** confira o [Erro: o nome de domínio não é resolvido](troubleshoot-vm-configuration-2019.md#error-domain-name-doesnt-resolve) na [Configuração da VM do host da sessão](troubleshoot-vm-configuration-2019.md).

**Causa 3:** sua configuração de DNS da VNET (rede virtual) está definida como **Padrão**.

Para corrigir isso, execute as seguintes etapas:

1. Abra o portal do Azure e acesse a guia **Redes virtuais**.
2. Localize sua VNET e selecione **Servidores DNS**.
3. O menu de servidores DNS deve aparecer no lado direito da tela. Nesse menu, selecione **Personalizado**.
4. Verifique se os servidores DNS listados em Personalizar correspondem ao seu controlador de domínio ou ao domínio do Active Directory. Se você não vir o servidor DNS, poderá adicioná-lo inserindo o valor dele no campo **Adicionar servidor DNS**.

### <a name="error-your-deployment-failedunauthorized"></a>Erro: Falha na implantação...\Não autorizada

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**Causa:** a assinatura que você está usando é um tipo que não pode acessar os recursos necessários na região em que o cliente está tentando implantar. Por exemplo, as assinaturas do MSDN, Gratuitas ou Educação podem mostrar esse erro.

**Correção:** altere seu tipo de assinatura ou sua região para uma que possa acessar os recursos necessários.

### <a name="error-vmextensionprovisioningerror"></a>Erro: VMExtensionProvisioningError

> [!div class="mx-imgBorder"]
> ![Captura de tela de Falha na implantação com o estado de provisionamento do terminal.](../media/7aaf15615309c18a984673be73ac969a.png)

**Causa 1:** erro transitório com o ambiente da Área de Trabalho Virtual do Windows.

**Causa 2:** erro transitório com conexão.

**Correção:** confirme que o ambiente da Área de Trabalho Virtual do Windows está íntegro entrando com o PowerShell. Conclua o registro da VM manualmente em [Criar um pool de host com o PowerShell](create-host-pools-powershell-2019.md).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Erro: O nome de usuário administrador especificado não é permitido

> [!div class="mx-imgBorder"]
> ![A captura de tela de Falha na implantação na qual um administrador especificado não é permitido.](../media/f2b3d3700e9517463ef88fa41875bac9.png)

Exemplo de erro bruto:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**Causa:** a senha fornecida contém subcadeias de caracteres proibidas (admin, administrador, raiz).

**Correção:** atualize o nome de usuário ou use usuários diferentes.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Erro: A VM relatou uma falha durante o processamento da extensão

> [!div class="mx-imgBorder"]
> ![Captura de tela da operação de recurso concluída com o estado de provisionamento de terminal em Falha na implantação.](../media/49c4a1836a55d91cd65125cf227f411f.png)

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

**Causa:** a extensão do DSC do PowerShell não pôde obter acesso de administrador na VM.

**Correção:** confirme se o nome de usuário e a senha têm acesso administrativo na máquina virtual e execute o modelo do Azure Resource Manager novamente.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Erro: DeploymentFailed – Configuração do DSC do PowerShell 'FirstSessionHost' concluída com erros

> [!div class="mx-imgBorder"]
> ![Captura de tela da falha da implantação com Configuração do DSC do PowerShell 'FirstSessionHost' concluída com erros.](../media/64870370bcbe1286906f34cf0a8646ab.png)

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

**Causa:** a extensão do DSC do PowerShell não pôde obter acesso de administrador na VM.

**Correção:** confirme se o nome de usuário e a senha fornecidos têm acesso administrativo na máquina virtual e execute o modelo do Azure Resource Manager novamente.

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

**Causa:** parte do nome do grupo de recursos é usada para determinados recursos que estão sendo criados pelo modelo. Devido ao nome que corresponde aos recursos existentes, o modelo pode selecionar um recurso existente de um grupo diferente.

**Correção:** ao executar o modelo do Azure Resource Manager para implantar VMs de host de sessão, torne os dois primeiros caracteres exclusivos para o nome do grupo de recursos de assinatura.

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

**Causa:** esse erro ocorre porque a NIC criada com o modelo do Azure Resource Manager tem o mesmo nome que outra NIC que já está na VNET.

**Correção:** use um prefixo de host diferente.

### <a name="error-deploymentfailed--error-downloading"></a>Erro: DeploymentFailed – Erro no download

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

**Causa:** esse erro ocorre devido a uma rota estática, regra de firewall ou um NSG que bloqueia o download do arquivo zip vinculado ao modelo do Azure Resource Manager.

**Correção:** remova a rota estática de bloqueio, a regra de firewall ou o NSG. Opcionalmente, abra o arquivo JSON do modelo do Azure Resource Manager em um editor de texto, leve o link para o arquivo zip e baixe o recurso para uma localização permitida.

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

**Causa:** o administrador de locatários da Área de Trabalho Virtual do Windows especificado não tem uma atribuição de função válida.

**Correção:** o usuário que criou o locatário da Área de Trabalho Virtual do Windows precisa entrar no PowerShell da Área de Trabalho Virtual do Windows e atribuir uma atribuição de função ao usuário da tentativa. Se você estiver executando os parâmetros de modelo do Azure Resource Manager do GitHub, siga estas instruções usando os comandos do PowerShell:

```PowerShell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName "RDS Contributor" -SignInName <UPN>
```

### <a name="error-user-requires-azure-ad-multi-factor-authentication-mfa"></a>Erro: o usuário precisa ter a MFA (Autenticação Multifator) do Azure AD

> [!div class="mx-imgBorder"]
> ![Captura de tela da falha na implantação devido à ausência da MFA (Autenticação Multifator)](../media/MFARequiredError.png)

Exemplo de erro bruto:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**Causa:** o administrador de locatários da Área de Trabalho Virtual do Windows especificado requer a MFA (Autenticação Multifator) do Azure AD para entrar.

**Correção:** crie uma entidade de serviço e atribua a ela uma função para seu locatário da Área de Trabalho Virtual do Windows seguindo as etapas no [Tutorial: Criar entidades de serviço e atribuições de função com o PowerShell](create-service-principal-role-powershell.md). Depois de verificar que você pode entrar na Área de Trabalho Virtual do Windows com a entidade de serviço, execute novamente a oferta do Azure Marketplace ou o modelo do Azure Resource Manager do GitHub, dependendo do método que você está usando. Siga as instruções abaixo para inserir os parâmetros corretos para seu método.

Se você estiver executando a oferta do Azure Marketplace, forneça valores para os seguintes parâmetros para fazer a autenticação correta na Área de Trabalho Virtual do Windows:

- Proprietário do RDS do locatário da Área de Trabalho Virtual do Windows: entidade de serviço
- ID do aplicativo: a identificação do aplicativo da entidade de serviço que você criou
- Senha/Confirmar senha: o segredo de senha que você gerou para a entidade de serviço
- ID de locatário do Azure AD: a ID de locatário do Azure AD da entidade de serviço que você criou

Se você estiver executando o modelo do Azure Resource Manager do GitHub, forneça valores para os seguintes parâmetros para fazer a autenticação correta na Área de Trabalho Virtual do Windows:

- Nome UPN ou ID do aplicativo do administrador de locatários: a identificação do aplicativo da entidade de serviço que você criou
- Senha do administrador de locatários: o segredo de senha que você gerou para a entidade de serviço
- IsServicePrincipal: **true**
- AadTenantId: a ID de locatário do Azure AD da entidade de serviço que você criou

### <a name="error-vmsubnet-not-available-when-configuring-virtual-networks"></a>Erro: vmSubnet não disponível na configuração de redes virtuais

**Causa:** no modelo do Marketplace da Área de Trabalho Virtual do Windows, a interface do usuário exibe apenas as sub-redes que têm pelo menos o mesmo número de endereços IP disponíveis que o número total de VMs especificado no modelo. O número real de endereços IP disponíveis na sub-rede só precisa ser igual ao número de novas VMs que estão sendo implantadas, mas isso não pode ser calculado pela interface do usuário atual.

**Correção:** você pode especificar uma sub-rede com a mesma quantidade de endereços IP disponíveis que o número de VMs que estão sendo adicionadas não usando a interface do usuário do Marketplace. Isso pode ser feito especificando o nome da sub-rede no parâmetro "**existingSubnetName**" quando você [reimplanta uma implantação existente](expand-existing-host-pool-2019.md#redeploy-from-azure) ou [a implanta usando o modelo do ARM subjacente do GitHub](create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool).

## <a name="next-steps"></a>Próximas etapas

- Confira uma visão geral da solução de problemas da Área de Trabalho Virtual do Windows e das faixas de escalonamento em [Visão geral da solução de problemas, comentários e suporte](troubleshoot-set-up-overview-2019.md).
- Confira como solucionar problemas ao configurar uma VM (máquina virtual) na Área de Trabalho Virtual do Windows em [Configuração da máquina virtual do host da sessão](troubleshoot-vm-configuration-2019.md).
- Para solucionar problemas com conexões de cliente da Área de Trabalho Virtual do Windows, confira [Conexões do serviço da Área de Trabalho Virtual do Windows](troubleshoot-service-connection-2019.md).
- Para solucionar problemas com clientes da Área de Trabalho Remota, confira [Solucionar problemas do cliente da Área de Trabalho Remota](../troubleshoot-client.md)
- Veja como solucionar problemas ao usar o PowerShell com a Área de Trabalho Virtual do Windows em [PowerShell da Área de Trabalho Virtual do Windows](troubleshoot-powershell-2019.md).
- Para saber mais sobre o serviço, confira [Ambiente da Área de Trabalho Virtual do Windows](environment-setup-2019.md).
- Acompanhe um tutorial de solução de problemas em [Tutorial: Solucionar problemas de implantações de modelos do Resource Manager](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Para saber sobre as ações de auditoria, consulte [Auditar operações com o Gerenciador de Recursos](../../azure-resource-manager/management/view-activity-logs.md).
- Para saber sobre as ações para determinar os erros durante a implantação, consulte [Exibir operações de implantação](../../azure-resource-manager/templates/deployment-history.md).