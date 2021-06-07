---
title: Gerenciar serviços do Office 365 usando a Automação do Azure
description: Este artigo informa como usar a Automação do Azure para gerenciar serviços de assinatura do Office 365.
services: automation
ms.date: 11/05/2020
ms.topic: conceptual
ms.openlocfilehash: 70c8892969a3b13175c60a4e20e0cf9086112abe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93398038"
---
# <a name="manage-office-365-services"></a>Gerenciar serviços do Office 365

Você pode usar a Automação do Azure para gerenciamento de serviços de assinatura do Office 365, para produtos como o Microsoft Word e o Microsoft Outlook. As interações com o Office 365 são habilitadas pelo [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md). Veja [Usar o Azure Active Directory na Automação do Azure para autenticação no Azure](automation-use-azure-ad.md).

## <a name="prerequisites"></a>Pré-requisitos

Você precisa do seguinte para gerenciar os serviços de assinatura do Office 365 na Automação do Azure.

* Uma assinatura do Azure. Veja [Guia de decisão da assinatura](/azure/cloud-adoption-framework/decision-guides/subscriptions/).
* Um objeto de Automação no Azure para manter as credenciais de conta de usuário e os runbooks. Veja [Uma introdução à Automação do Azure](./automation-intro.md).
* Azure Active Directory. Veja [Usar o Azure Active Directory na Automação do Azure para autenticação no Azure](automation-use-azure-ad.md).
* Um locatário do Office 365, com uma conta. Veja [Configurar seu locatário do Office 365](/sharepoint/dev/spfx/set-up-your-developer-tenant).

## <a name="install-the-msonline-and-msonlineext-modules"></a>Instalar os módulos MSOnline e MSOnlineExt

O uso do Office 365 na Automação do Azure exige Microsoft Azure Active Directory para o Windows PowerShell (módulo `MSOnline`). Você também precisará do módulo [`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35), o que simplifica o gerenciamento do Azure Active Directory em ambientes de um locatário e multilocatários. Instale os módulos conforme descrito em [Usar o Azure Active Directory na Automação do Azure para autenticar no Azure](automation-use-azure-ad.md).

>[!NOTE]
>Para usar o MSOnline PowerShell, você deve ser um membro do Azure Active Directory. Os usuários convidados não podem usar o módulo.

## <a name="create-an-azure-automation-account"></a>Criar uma conta de Automação do Azure

Para concluir as etapas neste artigo, você precisa de uma conta na Automação do Azure. Veja [Criar uma conta de Automação do Azure](automation-quickstart-create-account.md).
 
## <a name="add-msonline-and-msonlineext-as-assets"></a>Adicionar MSOnline e MSOnlineExt como ativos

Agora, adicione os módulos MSOnline e MSOnlineExt instalados para habilitar a funcionalidade do Office 365. Veja [Gerenciar módulos na Automação do Azure](shared-resources/modules.md).

1. No portal do Azure, selecione **Contas de Automação**.
2. Escolha sua conta de Automação.
3. Escolha **Galeria de Módulos** em **Recursos Compartilhados**.
4. Procure MSOnline.
5. Escolha o módulo `MSOnline` PowerShell e clique em **Importar** para importar o módulo como um ativo.
6. Repita as etapas 4 e 5 para encontrar e importar o módulo `MSOnlineExt`.

## <a name="create-a-credential-asset-optional"></a>Criar um ativo de credencial (opcional)

É opcional criar um ativo de credencial para o usuário administrativo do Office 365 que tem permissões para executar o script. No entanto, isso pode ajudar a manter a exposição de nomes de usuário e senhas dentro de scripts do PowerShell. Para obter instruções, veja [Criar um ativo de credencial](automation-use-azure-ad.md#create-a-credential-asset).

## <a name="create-an-office-365-service-account"></a>Criar uma conta de serviço do Office 365

Para executar os serviços de assinatura do Office 365, você precisa de uma conta de serviço do Office 365 com permissões para fazer o que você deseja. Você pode usar uma conta de administrador global, uma conta por serviço ou ter uma função ou script para executar. Em qualquer um dos casos, a conta de serviço exige uma senha complexa e segura. Veja [Configurar o Office 365 for Business](/microsoft-365/admin/setup/setup).

## <a name="connect-to-the-azure-ad-online-service"></a>Conectar-se ao serviço online do Azure Active Directory

>[!NOTE]
>Para usar os cmdlets do módulo MSOnline, é preciso executá-los no Windows PowerShell. O PowerShell Core não é compatível com esses cmdlets.

É possível usar o módulo MSOnline para se conectar ao Azure Active Directory a partir da assinatura do Office 365. A conexão usa um nome de usuário e uma senha do Office 365 ou usa a MFA (autenticação multifator). Você pode se conectar usando o portal do Azure ou um prompt de comando do Windows PowerShell (não precisa ser elevado).

Um exemplo do PowerShell é mostrado abaixo. O cmdlet [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) solicita credenciais e as armazena na variável `Msolcred`. Em seguida, o cmdlet [Connect-MsolService](/powershell/module/msonline/connect-msolservice) usa as credenciais para se conectar ao serviço do diretório do Azure online. Se quiser se conectar a um ambiente específico do Azure, use o parâmetro `AzureEnvironment`.

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

Se não receber nenhum erro, você se conectou com êxito. Um teste rápido é executar um cmdlet do Office 365, por exemplo, `Get-MsolUser` e ver os resultados. Se você receber erros, observe que um problema comum é senha incorreta.

>[!NOTE]
>Você também pode usar o módulo AzureRM ou o módulo Az para se conectar ao Azure Active Directory a partir da assinatura do Office 365. O cmdlet de conexão principal é [Connect-AzureAD](/powershell/module/azuread/connect-azuread). Este cmdlet permite o parâmetro `AzureEnvironmentName` em ambientes específicos do Office 365.

## <a name="create-a-powershell-runbook-from-an-existing-script"></a>Criar um runbook do PowerShell com base em um script existente

Acesse a funcionalidade do Office 365 a partir de um script do PowerShell. Aqui está um exemplo de um script para uma credencial chamada `Office-Credentials` com o nome de usuário `admin@TenantOne.com`. Ele usa `Get-AutomationPSCredential` para importar a credencial do Office 365.

```powershell
$emailFromAddress = "admin@TenantOne.com"
$emailToAddress = "servicedesk@TenantOne.com"
$emailSMTPServer = "outlook.office365.com"
$emailSubject = "Office 365 License Report"

$credObject = Get-AutomationPSCredential -Name "Office-Credentials"
Connect-MsolService -Credential $credObject

$O365Licenses = Get-MsolAccountSku | Out-String
Send-MailMessage -Credential $credObject -From $emailFromAddress -To $emailToAddress -Subject $emailSubject -Body $O365Licenses -SmtpServer $emailSMTPServer -UseSSL
```

## <a name="run-the-script-in-a-runbook"></a>Executar o script em um runbook

Você pode usar o script em um runbook de Automação do Azure. Para fins de exemplo, usaremos o tipo de runbook do PowerShell.

1. Crie um novo runbook do PowerShell. Veja [Criar um runbook de Automação do Azure](./automation-quickstart-create-runbook.md).
2. Na sua conta de Automação, escolha **Runbooks** em **Automação de Processos**.
3. Escolha o novo runbook e clique em **Editar**.
4. Copie o script e cole-o no editor de texto do runbook.
5. Escolha **ATIVOS**, expanda **Credenciais** e verifique se a credencial do Office 365 está lá.
6. Clique em **Save** (Salvar).
7. Escolha **painel Teste** e clique em **Iniciar** para começar a testar seu runbook. Veja [Gerenciar runbooks na Automação do Azure](./manage-runbooks.md).
8. Quando o teste estiver concluído, saia do painel Teste.

## <a name="publish-and-schedule-the-runbook"></a>Publicar e agendar o runbook

Para publicar e agendar o runbook, veja [Gerenciar runbooks na Automação do Azure](./manage-runbooks.md).

## <a name="next-steps"></a>Próximas etapas

* Para obter detalhes sobre o uso de credenciais, veja [Gerenciar credenciais na Automação do Azure](shared-resources/credentials.md).
* Para obter informações sobre módulos, veja [Gerenciar módulos na Automação do Azure](shared-resources/modules.md).
* Para iniciar um runbook, veja [Iniciar um runbook na Automação do Azure](start-runbooks.md).
* Para obter detalhes do PowerShell, veja [Documentos do PowerShell](/powershell/scripting/overview).
