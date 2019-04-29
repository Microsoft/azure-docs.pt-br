---
title: Gerenciar contas Executar como da Automação do Azure
description: Este artigo descreve como gerenciar as contas Executar como com PowerShell ou pelo portal.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 03/26/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: af67109fb7f55f365cd71714a3eefab2336b636a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61301061"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Gerenciar contas Executar como da Automação do Azure

As contas Executar como na Automação do Azure são usadas para fornecer autenticação para o gerenciamento de recursos no Azure com os cmdlets do Azure.

Quando você cria uma conta Executar como, ela cria uma nova entidade de serviço no Azure Active Directory e atribui a função Colaborador a esse usuário no nível da assinatura. Para runbooks que usam Hybrid Runbook Workers em máquinas virtuais do Azure, você pode usar [identidades gerenciadas para recursos do Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) em vez de contas Executar como para autenticar para recursos do Azure.

Há dois tipos de Contas Executar como:

* **Conta Executar como do Azure** - Essa conta é usada para gerenciar recursos do modelo de implantação do Resource Manager.
  * Cria um aplicativo do Azure AD com um certificado autoassinado, cria uma conta de entidade de serviço para o aplicativo no Azure AD e atribui a função de Colaborador à conta na assinatura atual. Você pode alterar essa configuração para Proprietário ou qualquer outra função. Para obter mais informações, confira [Controle de acesso baseado em função na Automação do Azure](automation-role-based-access-control.md).
  * Cria um ativo de certificado de Automação chamado *AzureRunAsCertificate* na conta de Automação especificada. O ativo de certificado contém a chave privada do certificado que é usada pelo aplicativo do Azure AD.
  * Cria um ativo de conexão de Automação chamado *AzureRunAsConnection* na conta de Automação especificada. O ativo de conexão contém applicationId, tenantId, subscriptionId e a impressão digital do certificado.

* **Conta Executar como Clássico do Azure** - Essa conta é usada para gerenciar recursos do modelo de implantação Clássico.
  * Cria um certificado de gerenciamento na assinatura
  * Cria um ativo de certificado de Automação chamado *AzureClassicRunAsCertificate* na conta de Automação especificada. O ativo de certificado contém a chave privada do certificado usada pelo certificado de gerenciamento.
  * Cria um ativo de conexão de Automação chamado *AzureClassicRunAsConnection* na conta de Automação especificada. O ativo de conexão contém o nome da assinatura, subscriptionId e o nome do ativo de certificado.
  * Deve ser um coadministrador na assinatura para criar ou renovar
  
  > [!NOTE]
  > As assinaturas do Azure CSP (Provedor de Soluções na Nuvem do Azure) dão suporte apenas ao modelo do Azure Resource Manager, serviços que não são do Azure Resource Manager não estão disponíveis no programa. Ao usar uma assinatura do CSP, a conta Executar Como Clássica do Azure não é criada. A conta Executar Como do Azure ainda é criada. Para saber mais sobre assinaturas de CSP, consulte [Serviços disponíveis em assinaturas do CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services#comments).

## <a name="permissions"></a>Permissões para configurar contas Executar como

Para criar ou atualizar uma conta Executar como, é necessário ter privilégios e permissões específicos. Um Administrator Global/Coadministrator pode completar todas as tarefas. Em uma situação em que você tem separação de tarefas, a tabela a seguir mostra uma listagem das tarefas, o cmdlet equivalente e as permissões necessárias:

|Tarefa|Cmdlet  |Permissões mínimas  |Onde você define as permissões|
|---|---------|---------|---|
|Criar aplicativo do Azure AD|[New-AzureRmADApplication](/powershell/module/azurerm.resources/new-azurermadapplication)     | Função Desenvolvedor de Aplicativos<sup>1</sup>        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Página Inicial > Azure Active Directory > Registros de Aplicativo |
|Adicione uma credencial ao aplicativo.|[New-AzureRmADAppCredential](/powershell/module/AzureRM.Resources/New-AzureRmADAppCredential)     | Administrador do aplicativo ou ADMINISTRADOR GLOBAL<sup>1</sup>         |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Página Inicial > Azure Active Directory > Registros de Aplicativo|
|Criar e obter uma entidade de serviço do Azure AD|[New-AzureRMADServicePrincipal](/powershell/module/AzureRM.Resources/New-AzureRmADServicePrincipal)</br>[Get-AzureRmADServicePrincipal](/powershell/module/AzureRM.Resources/Get-AzureRmADServicePrincipal)     | Administrador do aplicativo ou ADMINISTRADOR GLOBAL        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Página Inicial > Azure Active Directory > Registros de Aplicativo|
|Atribuir ou obter a função RBAC para a entidade especificada|[New-AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment)</br>[Get-AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/Get-AzureRmRoleAssignment)      | Proprietário ou Administrador de Acesso do Usuário        | [Assinatura](../role-based-access-control/role-assignments-portal.md)</br>Página Inicial > Assinaturas > \<nome da assinatura\> – Controle de Acesso (IAM)|
|Criar ou remover um certificado de Automação|[New-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/New-AzureRmAutomationCertificate)</br>[Remove-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationCertificate)     | Colaborador no Grupo de Recursos         |Grupo de Recursos da Conta de Automação|
|Criar ou remover uma conexão de Automação|[New-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/New-AzureRmAutomationConnection)</br>[Remove-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationConnection)|Colaborador no Grupo de Recursos |Grupo de Recursos da Conta de Automação|

<sup>1</sup> Os usuários não administradores em seu locatário do Azure Active Directory poderão [registrar aplicativos do AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) se a opção **Os usuários podem registrar aplicativos** do locatário do Azure Active Directory na página **Configurações do usuário** estiver definida como **Sim**. Se a configuração de registros do aplicativo estiver definido como **Não**, o usuário que executa esta ação deverá ser um administrador global no Azure AD.

Se você não for membro da instância do Active Directory da assinatura antes de ser adicionado à função de administrador/coadministrador global da assinatura, você será adicionado como convidado. Nessa situação, você receberá um `You do not have permissions to create…` aviso na página **Adicionar Conta de Automação**. Os usuários adicionados à função de administrador global/coadministrador primeiro podem ser removidos das assinaturas da instância do Active Directory e adicionados novamente para torná-los Usuários completos no Active Directory. Para verificar essa situação, no painel **Azure Active Directory** no portal do Azure, selecione **Usuários e grupos**, selecione **Todos os usuários** e, depois de selecionar o usuário específico, selecione **Perfil**. O valor do atributo **Tipo de usuário** sob o perfil de usuários não deve ser igual a **Convidado**.

## <a name="permissions-classic"></a>Permissões para configurar contas executar como clássica

Para configurar ou renovar as contas executar como clássica, você deve ter o **coadministrador** função no nível de assinatura. Para saber mais sobre permissões do clássico, consulte [os administradores de assinatura clássicos do Azure](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="create-a-run-as-account-in-the-portal"></a>Criar uma conta Executar como no portal

Nesta seção, execute as seguintes etapas para atualizar sua conta de Automação do Azure no portal do Azure. Você pode criar as contas Executar Como e Executar Como Clássicas individualmente. Se você não precisa gerenciar recursos clássicos, é possível criar apenas a conta Executar Como do Azure.  

1. Conecte-se no Portal do Azure com uma conta que seja membro da função Administradores da Assinatura e coadministradora da assinatura.
2. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Automação**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Contas de Automação**.
3. Na página **Contas de Automação**, selecione sua conta de Automação da lista de contas de Automação.
4. No painel do lado esquerdo, selecione **Contas Executar como** na seção **Configurações de conta**.  
5. Dependendo da conta de que você precisa, selecione **Conta Executar como do Azure** ou **Conta Executar como Clássica do Azure**. Após a seleção, o painel **Adicionar Executar como do Azure** ou **Adicionar conta Executar como Clássica do Azure** aparecerá e após a revisão das informações de visão geral, clique em **Criar** para prosseguir com a criação da conta Executar como.  
6. Enquanto o Azure cria a conta Executar como, você poderá acompanhar o andamento em **Notificações** no menu. Uma faixa também é exibida informando que a conta está sendo criada. A conclusão desse processo pode levar alguns minutos.  

## <a name="create-run-as-account-using-powershell"></a>Criar conta Executar como usando PowerShell

## <a name="prerequisites"></a>Pré-requisitos

A lista a seguir fornece os requisitos para criar uma conta Executar como no PowerShell:

* Windows 10 ou Windows Server 2016 com os módulos 3.4.1 e posteriores do Azure Resource Manager. O script do PowerShell não oferece suporte a versões anteriores do Windows.
* Azure PowerShell 1.0 e posterior. Para obter informações sobre a versão 1.0 do PowerShell, confira [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Uma conta de Automação, que é referenciada como o valor para os parâmetros *– AutomationAccountName* e *- ApplicationDisplayName*.
* Permissões equivalentes às listadas em [Permissões necessárias para configurar contas Executar como](#permissions)

Para obter os valores para *SubscriptionID*, *ResourceGroup* e *AutomationAccountName*, que são parâmetros obrigatórios para o script, conclua as etapas a seguir:

1. No portal do Azure, clique em **Todos os serviços**. Na lista de recursos, digite **Automação**. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Selecione **Contas de Automação**.
1. Na página conta de Automação, selecione sua conta de Automação e, em seguida, em **Configurações de conta** selecione **Propriedades**.  
1. Anote os valores da **ID da Assinatura**, do **Nome** e **Grupo de Recursos** na página **Propriedades**.

   ![Página "Propriedades" da conta de Automação](media/manage-runas-account/automation-account-properties.png)

Este script do PowerShell inclui suporte para as seguintes configurações:

* Crie uma conta Executar como usando um certificado autoassinado.
* Crie uma conta Executar como e uma conta Executar como Clássica usando um certificado autoassinado.
* Criar uma conta Executar como e uma conta Clássica Executar como usando um certificado emitido por sua autoridade de certificação corporativa (CA).
* Crie uma conta Executar como e uma conta Executar como Clássica usando um certificado autoassinado na nuvem do Azure Governamental.

>[!NOTE]
> Se selecionar a opção para criar uma conta Executar como Clássica, depois que o script for executado, carregue o certificado público (extensão de nome de arquivo .cer) para o repositório de gerenciamento da assinatura em que a conta de Automação foi criada.

1. Salve o script a seguir em seu computador. Neste exemplo, salve-o com o nome de arquivo *New-RunAsAccount.ps1*.

   O script usa vários cmdlets do Azure Resource Manager para criar recursos. A tabela a seguir mostra os cmdlets e as permissões necessárias.

    ```powershell
    #Requires -RunAsAdministrator
    Param (
        [Parameter(Mandatory = $true)]
        [String] $ResourceGroup,

        [Parameter(Mandatory = $true)]
        [String] $AutomationAccountName,

        [Parameter(Mandatory = $true)]
        [String] $ApplicationDisplayName,

        [Parameter(Mandatory = $true)]
        [String] $SubscriptionId,

        [Parameter(Mandatory = $true)]
        [Boolean] $CreateClassicRunAsAccount,

        [Parameter(Mandatory = $true)]
        [String] $SelfSignedCertPlainPassword,

        [Parameter(Mandatory = $false)]
        [string] $EnterpriseCertPathForRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPlainPasswordForRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPathForClassicRunAsAccount,

        [Parameter(Mandatory = $false)]
        [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

        [Parameter(Mandatory = $false)]
        [ValidateSet("AzureCloud", "AzureUSGovernment")]
        [string]$EnvironmentName = "AzureCloud",

        [Parameter(Mandatory = $false)]
        [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
    )

    function CreateSelfSignedCertificate([string] $certificateName, [string] $selfSignedCertPlainPassword,
        [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
        $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
            -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
            -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired) -HashAlgorithm SHA256

        $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
    }

    function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {  
        $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
        $keyId = (New-Guid).Guid

        # Create an Azure AD application, AD App Credential, AD ServicePrincipal

        # Requires Application Developer Role, but works with Application administrator or GLOBAL ADMIN
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId) 
        # Requires Application administrator or GLOBAL ADMIN
        $ApplicationCredential = New-AzureRmADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $PfxCert.NotBefore -EndDate $PfxCert.NotAfter
        # Requires Application administrator or GLOBAL ADMIN
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId 
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        # Requires User Access Administrator or Owner.
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6) {
            Sleep -s 10
            New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
            $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
            $Retries++;
        }
        return $Application.ApplicationId.ToString();
    }

    function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName, [string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force   
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
    }

    function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
    }

    Import-Module AzureRM.Profile
    Import-Module AzureRM.Resources

    $AzureRMProfileVersion = (Get-Module AzureRM.Profile).Version
    if (!(($AzureRMProfileVersion.Major -ge 3 -and $AzureRMProfileVersion.Minor -ge 4) -or ($AzureRMProfileVersion.Major -gt 3))) {
        Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
        return
    }

    # To use the new Az modules to create your Run As accounts please uncomment the following lines and ensure you comment out the previous 8 lines that import the AzureRM modules to avoid any issues. To learn about about using Az modules in your Automation Account see https://docs.microsoft.com/azure/automation/az-modules

    # Import-Module Az.Automation
    # Enable-AzureRmAlias


    Connect-AzureRmAccount -Environment $EnvironmentName 
    $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId

    # Create a Run As account by using a service principal
    $CertifcateAssetName = "AzureRunAsCertificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    $ConnectionTypeName = "AzureServicePrincipal"

    if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
        $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
        $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
    }
    else {
        $CertificateName = $AutomationAccountName + $CertifcateAssetName
        $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
        $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
        $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
        CreateSelfSignedCertificate $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
    }

    # Create a service principal
    $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
    $ApplicationId = CreateServicePrincipal $PfxCert $ApplicationDisplayName

    # Create the Automation certificate asset
    CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

    # Populate the ConnectionFieldValues
    $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
    $TenantID = $SubscriptionInfo | Select TenantId -First 1
    $Thumbprint = $PfxCert.Thumbprint
    $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}

    # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

    if ($CreateClassicRunAsAccount) {
        # Create a Run As account by using a service principal
        $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
        $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
        $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
        $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
        "Log in to the Microsoft Azure portal (https://portal.azure.com) and select Subscriptions -> Management Certificates." + [Environment]::NewLine +
        "Then click Upload and upload the .cer format of #CERT#"

        if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
            $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
            $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
            $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
        }
        else {
            $ClassicRunAsAccountCertificateName = $AutomationAccountName + $ClassicRunAsAccountCertifcateAssetName
            $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
            $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
            $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
            $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
            CreateSelfSignedCertificate $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.Name
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName   $ClassicRunAsAccountConnectionFieldValues

        Write-Host -ForegroundColor red       $UploadMessage
    }
    ```

    > [!IMPORTANT]
    > **Connect-AzureRmAccount** agora é um alias para **Connect-AzureRMAccount**. Ao pesquisar os itens da biblioteca, se não visualizar **Connect-AzureRMAccount**, você poderá usar **Add-AzureRmAccount** ou poderá [atualizar os módulos](automation-update-azure-modules.md) na conta de Automação.

1. Em sua máquina, inicie o **Windows PowerShell** na tela **Iniciar** com direitos de usuário elevados.
1. A partir do shell da linha de comando com privilégios elevados, acesse a pasta que contém o script que você criou na etapa 1.  
1. Execute o script usando os valores de parâmetro para a configuração necessária.

    **Criar uma conta Executar como usando um certificado autoassinado**  

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
    ```

    **Criar uma conta Executar como e uma conta Executar como Clássica usando um certificado autoassinado**  

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
    ```

    **Criar uma conta Executar como e uma conta Executar como Clássica usando um certificado corporativo**  

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
    ```

    **Criar uma conta Executar como e uma conta Executar como Clássica usando um certificado autoassinado na nuvem do Azure Governamental**
  
    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
    ```

    > [!NOTE]
    > Depois que o script for executado, você deverá se autenticar no Azure. Ente com uma conta que seja membro da função de administradores de assinatura e coadministrador da assinatura.

Depois que o script for executado com êxito, observe o seguinte:

* Se você tiver criado uma conta Executar como Clássica com um certificado autoassinado público (arquivo .cer), o script a criará e salvará na pasta de arquivos temporários no computador sob o perfil de usuário *%USERPROFILE%\AppData\Local\Temp*, que é usado para executar a sessão do PowerShell.

* Se tiver criado uma conta Executar como Clássica com um certificado público corporativo (arquivo .cer), use este certificado. Siga as instruções para [baixar um certificado da API de gerenciamento para o portal do Azure](../azure-api-management-certs.md).

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Excluir uma conta Executar como ou Executar como Clássica

Esta seção descreve como excluir e recriar uma conta Executar como ou Executar como Clássica. Quando você executa essa ação, a conta de Automação é mantida. Após excluir uma conta Executar como ou Executar como Clássica, você pode recriá-la no portal do Azure.

1. No Portal do Azure, abra a Conta de automação.

2. Na página **Conta de automação**, selecione **Contas Executar como**.

3. Na página de propriedades de **Contas Executar como**, selecione a conta Executar como ou a conta Executar como Clássica que você deseja excluir. Em seguida, no painel **Propriedades** da conta selecionada, clique em **Excluir**.

   ![Excluir Conta Executar como](media/manage-runas-account/automation-account-delete-runas.png)

1. Enquanto a conta está sendo excluída, você poderá acompanhar o andamento em **Notificações** no menu.

1. Depois que a conta for excluída, você poderá recriá-la na página de propriedades **Contas Executar como** selecionando a opção de criação **Executar como Conta do Azure**.

   ![Recriar a conta de Automação Executar como](media/manage-runas-account/automation-account-create-runas.png)

## <a name="cert-renewal"></a>Renovação do certificado autoassinado

Antes da conta Executar como expirar, você deverá renovar o certificado. Se achar que a conta Executar como foi comprometida, você poderá excluí-la e recriá-la. Esta seção descreve como realizar essas operações.

O certificado autoassinado que você criou para a conta Executar como expira um ano a contar da data de criação. Você pode renová-lo a qualquer momento antes que ele expire. Ao renová-lo, o certificado válido atual é retido para garantir que todos os runbooks colocados na fila ou em execução ativa e autenticados com a conta Executar Como não sejam afetados negativamente. O certificado permanece válido até a data de expiração.

> [!NOTE]
> Se tiver configurado a conta Executar como da Automação para usar um certificado emitido por a autoridade de certificação corporativa e usar essa opção, o certificado da empresa será substituído por um certificado autoassinado.

Para renovar o certificado, faça o seguinte:

1. No Portal do Azure, abra a Conta de automação.

1. Selecione **Contas Executar como** em **Configurações de Conta**.

    ![Painel de propriedades da conta de Automação](media/manage-runas-account/automation-account-properties-pane.png)

1. Na página de propriedades **Contas Executar como**, selecione a conta Executar como ou a conta Executar como Clássica para a qual você deseja renovar o certificado.

1. No painel **Propriedades** da conta selecionada, clique em **Renovar certificado**.

    ![Renovar o certificado da conta Executar como](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Enquanto o certificado está sendo renovado, você poderá acompanhar o andamento em **Notificações** no menu.

## <a name="limiting-run-as-account-permissions"></a>Limitar as permissões da conta Executar como

Para controlar o direcionamento da automação nos recursos na Automação do Azure, a conta Executar como tem direitos de colaborador concedidos na assinatura. Se for necessário restringir o que a entidade de serviço RunAs pode fazer, você poderá remover a conta da função de Colaborador para a assinatura e adicioná-lo como um colaborador para os grupos de recursos que deseja especificar.

No Portal do Azure, selecione **Assinaturas** e escolha a assinatura de sua Conta de Automação. Selecione **Controle de acesso (IAM)** e, em seguida, selecione a guia **Atribuições de função**. Pesquise a entidade de serviço da Conta de Automação (é semelhante ao \<identificador AutomationAccountName\>_unique). Selecione a conta e clique em **Remover** para removê-lo da assinatura.

![Colaboradores de assinatura](media/manage-runas-account/automation-account-remove-subscription.png)

Para adicionar a entidade de serviço a um grupo de recursos, selecione o grupo de recursos no Portal do Azure e selecione **IAM (Controle de acesso)**. Selecione **Adicionar atribuição de função**, **isso abre a página Adicionar atribuição de função**. Para **Função**, selecione **Colaborador**. Na caixa de texto **Selecionar** digite o nome da entidade de serviço da conta Executar como e selecione-a na lista. Clique em **Salvar** para salvar as alterações. Conclua essas etapas para os grupos de recursos aos quais você quer conceder acesso de entidade de serviço Executar Como da Automação do Azure.

## <a name="misconfiguration"></a>Configuração incorreta

Alguns itens de configuração necessários para que a conta Executar como ou Executar como Clássica funcione corretamente podem foram excluídos ou criados incorretamente durante a instalação inicial. Os itens incluem:

* Ativo de certificado
* Ativo de conexão
* A Conta Executar como foi removida da função de Colaborador
* Entidade de serviço ou aplicativo no Azure AD

Nos casos anteriores e em outras instâncias de uma configuração incorreta, a conta de Automação detecta as alterações e exibe o status *Incompleto* na página de propriedades **Contas Executar como** para a conta.

![Status incompleto de configuração de conta Executar como](media/manage-runas-account/automation-account-runas-incomplete-config.png)

Quando você selecionar a conta Executar como, o painel **Propriedades** da conta exibirá a seguinte mensagem de erro:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Você pode resolver rapidamente esses problemas de conta Executar como excluindo e recriando a conta.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre os Entidades de Serviço, consulte [Objetos de aplicativo e objetos de entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md).
* Para saber mais sobre certificados e serviços do Azure, consulte [Visão geral dos certificados para Serviços de Nuvem do Microsoft Azure](../cloud-services/cloud-services-certs-create.md).