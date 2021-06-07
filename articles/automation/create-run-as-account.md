---
title: Criar uma conta Executar como da Automação do Azure
description: Este artigo informa como criar uma conta Executar como com o PowerShell ou do portal do Azure.
services: automation
ms.subservice: process-automation
ms.date: 01/06/2021
ms.topic: conceptual
ms.openlocfilehash: ef6afff30da48b79b42e5fb4b3c72c3500f22dd1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102172296"
---
# <a name="how-to-create-an-azure-automation-run-as-account"></a>Como criar uma conta Executar como da automação do Azure

As contas Executar como na automação do Azure fornecem autenticação para gerenciar recursos no Azure Resource Manager ou no modelo de implantação clássico do Azure usando runbooks de automação e outros recursos de automação. Este artigo descreve como criar uma conta Executar como ou executar como clássica no portal do Azure ou Azure PowerShell.

## <a name="create-account-in-azure-portal"></a>Criar conta no portal do Azure

Execute as seguintes etapas para atualizar sua conta de Automação do Azure no portal do Azure. As contas Executar como e executar como clássica são criadas separadamente. Se você não precisa gerenciar recursos clássicos, é possível criar apenas a conta Executar Como do Azure.

1. Conecte-se no Portal do Azure com uma conta que seja membro da função Administradores da Assinatura e coadministradora da assinatura.

2. Pesquise e escolha **Contas de Automação**.

3. Na página **contas de automação** , selecione sua conta de automação na lista.

4. No painel esquerdo, selecione **contas Executar como** na seção **configurações de conta** .

    :::image type="content" source="media/create-run-as-account/automation-account-properties-pane.png" alt-text="Selecione a opção conta Executar como.":::

5. Dependendo da conta que você precisa, use a **conta Executar como do Azure** ou **+ painel da conta Executar como clássica do Azure** . Depois de examinar as informações de visão geral, clique em **Criar**.

    :::image type="content" source="media/create-run-as-account/automation-account-create-run-as.png" alt-text="Selecione a opção para criar uma conta Executar como":::

6. Enquanto o Azure cria a conta Executar como, você poderá acompanhar o andamento em **Notificações** no menu. Uma faixa também é exibida informando que a conta está sendo criada. O processo pode levar alguns minutos.

## <a name="create-account-using-powershell"></a>Criar conta usando o PowerShell

A lista a seguir fornece os requisitos para criar uma conta Executar como no PowerShell usando um script fornecido. Esses requisitos se aplicam a ambos os tipos de contas Executar como.

* Windows 10 ou Windows Server 2016 com os módulos 3.4.1 e posteriores do Azure Resource Manager. O script do PowerShell não é compatível com versões anteriores do Windows.
* Azure PowerShell PowerShell 6.2.4 ou posterior. Para obter informações, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/install-az-ps).
* Uma Conta de automação, que é referenciada como o valor para os parâmetros `AutomationAccountName` e `ApplicationDisplayName`.
* Permissões equivalentes às listadas em [Permissões necessárias para configurar contas Executar como](automation-security-overview.md#permissions).

Para obter os valores para `AutomationAccountName` , `SubscriptionId` e `ResourceGroupName` , que são parâmetros necessários para o script do PowerShell, conclua as etapas a seguir.

1. Entre no portal do Azure.

1. Pesquise e escolha **Contas de Automação**.

1. Na página Contas de Automação, escolha sua conta de Automação na lista.

1. No painel esquerdo, selecione **Propriedades**.

1. Observe os valores de **nome**, **ID da assinatura** e **grupo de recursos** na página **Propriedades** .

   ![Painel de propriedades da conta de automação](media/create-run-as-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>Criar o script do PowerShell da conta Executar como

O script do PowerShell inclui suporte para várias configurações.

* Crie uma conta Executar como usando um certificado autoassinado.
* Crie uma conta Executar como e/ou uma conta Executar como clássica usando um certificado autoassinado.
* Crie uma conta Executar como e/ou uma conta Executar como clássica usando um certificado emitido pela autoridade de certificação (CA) corporativa.
* Crie uma conta Executar como e/ou uma conta Executar como clássica usando um certificado autoassinado na nuvem do Azure governamental.

1. Baixe e salve o script em uma pasta local usando o comando a seguir.

    ```powershell
    wget https://raw.githubusercontent.com/azureautomation/runbooks/master/Utility/AzRunAs/Create-RunAsAccount.ps1 -outfile Create-RunAsAccount.ps1
    ```

2. Inicie o PowerShell com direitos de usuário elevados e navegue até a pasta que contém o script.

3. Execute um dos comandos a seguir para criar uma conta Executar como e/ou executar como clássica com base em seus requisitos.

    * Crie uma conta Executar como usando um certificado autoassinado.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
        ```

    * Crie uma conta Executar como e uma conta Executar como Clássica usando um certificado autoassinado.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
        ```

    * Crie uma conta Executar como e uma conta Executar como Clássica usando um certificado corporativo.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
        ```

        Se tiver criado uma conta Executar como Clássica com um certificado público corporativo (arquivo .cer), use este certificado. O script cria e salva-o na pasta arquivos temporários no computador, no perfil do usuário `%USERPROFILE%\AppData\Local\Temp` usado para executar a sessão do PowerShell. Consulte [Carregar um certificado de gerenciamento de API](../cloud-services/cloud-services-configure-ssl-certificate-portal.md) no portal do Azure.

    * Crie uma conta Executar como e uma conta Executar como Clássica usando um certificado autoassinado na nuvem do Azure Governamental

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnvironmentName AzureUSGovernment
        ```

4. Depois que o script for executado, você deverá se autenticar no Azure. Entre com uma conta que seja membro da função Administradores de assinatura. Se você estiver criando uma conta Executar como clássica, sua conta deverá ser um coadministrador da assinatura.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a criação de gráficos, veja [Criar runbooks gráficos na Automação do Azure](automation-graphical-authoring-intro.md).
* Para começar a usar runbooks do PowerShell, veja [Tutorial: Criação de um runbook do PowerShell](learn/automation-tutorial-runbook-textual-powershell.md).
* Para começar com um runbook do Python 3, consulte [tutorial: criar um runbook do Python 3](learn/automation-tutorial-runbook-textual-python-3.md).
