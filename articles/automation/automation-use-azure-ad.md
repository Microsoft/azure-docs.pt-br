---
title: Usar o Azure AD na Automação do Azure para autenticação no Azure
description: Este artigo informa como usar o Azure Active Directory na Automação do Azure como o provedor para autenticação no Azure.
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 336c0387ac9febcc517c2ce358d0b04c80d10678
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99576796"
---
# <a name="use-azure-ad-to-authenticate-to-azure"></a>Usar o Azure AD para a autenticação no Azure

O serviço [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) permite uma série de tarefas administrativas, como gerenciamento de usuários, gerenciamento de domínio e configuração de logon único. Este artigo descreve como usar o Azure Active Directory na Automação do Azure como o provedor para autenticação no Azure. 

## <a name="install-azure-ad-modules"></a>Instalar módulos do Azure Active Directory

É possível habilitar o Azure Active Directory por meio dos seguintes módulos do PowerShell:

* PowerShell do Azure Active Directory para Microsoft Azure AD Graph (módulos AzureRM e Az). A Automação do Azure é fornecida com o módulo AzureRM e sua atualização recente, o módulo Az. A funcionalidade inclui autenticação não interativa para o Azure usando a autenticação baseada em credencial do usuário do Azure Active Directory (OrgId). Veja [Azure Active Directory 2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76).

* Microsoft Azure Active Directory para Windows PowerShell (módulo MSOnline). Este módulo permite interações com a Microsoft Online, incluindo Microsoft 365.

>[!NOTE]
>O PowerShell Core não é compatível com o módulo MSOnline. Para usar os cmdlets do módulo, você deve executá-los pelo Windows PowerShell. Recomendamos usar os módulos mais recentes do PowerShell do Azure Active Directory para Graph em vez do módulo MSOnline. 

### <a name="preinstallation"></a>Pré-instalação

Antes de instalar os módulos do Azure Active Directory em seu computador:

* Desinstale todas as versões anteriores do módulo AzureRM/Az e do módulo MSOnline. 

* Desinstale o Assistente de Conexão do Microsoft Online Services para garantir a operação correta dos novos módulos do PowerShell.  

### <a name="install-the-azurerm-and-az-modules"></a>Instalar os módulos AzureRM e Az

>[!NOTE]
>Para trabalhar com esses módulos, é preciso usar o PowerShell versão 5.1 ou posterior com uma versão de 64 bits do Windows. 

1. Instalar o Windows Management Framework (WMF) 5.1. Veja [Instalar e configurar o WMF 5.1](/powershell/scripting/wmf/setup/install-configure).

2. Instale o AzureRM e/ou Az usando as instruções em [Instalar Azure PowerShell no Windows com o PowerShellGet](/powershell/azure/azurerm/install-azurerm-ps).

### <a name="install-the-msonline-module"></a>Instalar o módulo MSOnline

>[!NOTE]
>Para instalar o módulo MSOnline, você deve ser um membro de uma função de administrador. Veja [Sobre funções de administrador](/microsoft-365/admin/add-users/about-admin-roles).

1. Verifique se o recurso Microsoft .NET Framework 3.5.x está habilitado no seu computador. É provável que o computador tenha uma versão mais recente instalada, mas a compatibilidade com versões anteriores do .NET Framework pode ser habilitada ou desabilitada. 

2. Instale a versão de 64 bits do [Assistente de conexão do Microsoft Online Services](https://www.microsoft.com/Download/details.aspx?id=28177).

3. Execute o Windows PowerShell como administrador para criar um prompt de comando elevado do Windows PowerShell.

4. Implantar o Azure Active Directory a partir do [MSOnline 1.0](http://www.powershellgallery.com/packages/MSOnline/1.0).

5. Se precisar instalar o provedor do NuGet, digite Y e pressione ENTER.

6. Se você for solicitado a instalar o módulo a partir de [PSGallery](https://www.powershellgallery.com/), digite Y e pressione ENTER.

### <a name="install-support-for-pscredential"></a>Suporte de instalação para PSCredential

A Automação do Azure usa a classe [PSCredential](/dotnet/api/system.management.automation.pscredential) para representar um ativo de credencial. Seus scripts recuperam objetos `PSCredential` usando o cmdlet `Get-AutomationPSCredential`. Para obter mais informações, veja [Ativos de credencial na Automação do Azure](shared-resources/credentials.md).

## <a name="assign-a-subscription-administrator"></a>Atribuir um administrador de assinatura

Você deve atribuir um administrador para a assinatura do Azure. Essa pessoa tem a função de proprietário no escopo da assinatura. Veja [Controle de acesso com base em função na Automação do Azure](automation-role-based-access-control.md). 

## <a name="change-the-azure-ad-users-password"></a>Mudar a senha do usuário do Azure Active Directory

Para mudar a senha do usuário do Azure Active Directory:

1. Faça logoff do Azure.

2. Peça para o administrador fazer logon no Azure como o usuário do Azure Active Directory recém-criado, usando o nome de usuário completo (incluindo o domínio) e uma senha temporária. 

3. Peça para o administrador mudar a senha quando solicitado.

## <a name="configure-azure-automation-to-manage-the-azure-subscription"></a>Configurar a Automação do Azure para gerenciar a assinatura do Azure

Para que a Automação do Azure se comunique com o Azure Active Directory, é preciso recuperar as credenciais associadas à conexão do Azure com o Azure Active Directory. Exemplos dessas credenciais são ID de locatário, ID da assinatura e similares. Para obter mais informações sobre a conexão entre o Azure e o Azure Active Directory, veja [Conectar sua organização ao Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="create-a-credential-asset"></a>Cria um ativo de credencial

Com as credenciais do Azure para o Azure Active Directory disponíveis, é hora de criar um ativo de credencial de Automação do Azure para armazenar com segurança as credenciais do Azure Active Directory para que os runbooks e os scripts de Desired State Configuration (DSC) possam acessá-las. Faça isso usando o portal do Azure ou cmdlets do PowerShell.

### <a name="create-the-credential-asset-in-azure-portal"></a>Criar o ativo de credencial no portal do Azure

É possível usar o portal do Azure para criar o ativo de credencial. Execute esta operação em sua conta de Automação usando **Credenciais** em **Recursos Compartilhados**. Veja [Ativos de credenciais na Automação do Azure](shared-resources/credentials.md).

### <a name="create-the-credential-asset-with-windows-powershell"></a>Criar o ativo de credencial com o Windows PowerShell

Para preparar um novo ativo de credencial no Windows PowerShell, seu script cria primeiro um objeto `PSCredential` usando o nome de usuário e a senha atribuídos. Em seguida, o script usa esse objeto para criar o ativo por meio de uma chamada para o cmdlet [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure.service/new-azureautomationcredential). Como alternativa, o script pode chamar o cmdlet [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) para solicitar que o usuário digite um nome e uma senha. Veja [Ativos de credenciais na Automação do Azure](shared-resources/credentials.md). 

## <a name="manage-azure-resources-from-an-azure-automation-runbook"></a>Gerenciar recursos do Azure a partir de um runbook de Automação do Azure

É possível gerenciar recursos do Azure a partir de runbooks de Automação do Azure usando o ativo de credencial. Veja abaixo um exemplo de runbook do PowerShell que coleta o ativo de credencial a ser usado para parar e iniciar máquinas virtuais em uma assinatura do Azure. Esse runbook usa primeiro `Get-AutomationPSCredential` para recuperar a credencial a ser usada para autenticar no Azure. Em seguida, ele chama o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) para se conectar ao Azure usando a credencial. O script usa o cmdlet [Select-AzureSubscription](/powershell/module/servicemanagement/azure.service/select-azuresubscription) para escolher a assinatura com a qual trabalhar. 

```azurepowershell
Workflow Stop-Start-AzureVM 
{ 
    Param 
    (    
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureSubscriptionId, 
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureVMList="All", 
        [Parameter(Mandatory=$true)][ValidateSet("Start","Stop")] 
        [String] 
        $Action 
    ) 
     
    $credential = Get-AutomationPSCredential -Name 'AzureCredential' 
    Connect-AzAccount -Credential $credential 
    Select-AzureSubscription -SubscriptionId $AzureSubscriptionId 
 
    if($AzureVMList -ne "All") 
    { 
        $AzureVMs = $AzureVMList.Split(",") 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
    } 
    else 
    { 
        $AzureVMs = (Get-AzVM).Name 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
 
    } 
 
    foreach($AzureVM in $AzureVMsToHandle) 
    { 
        if(!(Get-AzVM | ? {$_.Name -eq $AzureVM})) 
        { 
            throw " AzureVM : [$AzureVM] - Does not exist! - Check your inputs " 
        } 
    } 
 
    if($Action -eq "Stop") 
    { 
        Write-Output "Stopping VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Stop-AzVM -Force 
        } 
    } 
    else 
    { 
        Write-Output "Starting VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Start-AzVM 
        } 
    } 
}
```  

## <a name="next-steps"></a>Próximas etapas

* Para obter detalhes sobre o uso de credenciais, veja [Gerenciar credenciais na Automação do Azure](shared-resources/credentials.md).
* Para obter informações sobre módulos, veja [Gerenciar módulos na Automação do Azure](shared-resources/modules.md).
* Para iniciar um runbook, veja [Iniciar um runbook na Automação do Azure](start-runbooks.md).
* Para obter detalhes do PowerShell, veja [Documentos do PowerShell](/powershell/scripting/overview).
