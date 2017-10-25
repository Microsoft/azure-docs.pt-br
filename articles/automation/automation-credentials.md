---
title: "Ativos de credenciais na Automação do Azure | Microsoft Docs"
description: "Os ativos de credenciais na Automação do Azure contêm credenciais de segurança que podem ser usadas para a autenticação em recursos acessados pelo runbook ou pela configuração DSC. Este artigo descreve como criar ativos de credenciais e usá-los em um runbook ou uma configuração DSC."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.assetid: 3209bf73-c208-425e-82b6-df49860546dd
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2017
ms.author: bwren
ms.openlocfilehash: 5adb76a779be7e82c864d67db7a2d3701805d874
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="credential-assets-in-azure-automation"></a>Ativos de credenciais na Automação do Azure
Um ativo da credencial de Automação tem um objeto [PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential) que contém as credenciais de segurança, como um nome de usuário e uma senha. Runbooks e configurações DSC podem usar cmdlets que aceitam um objeto PSCredential para autenticação ou eles podem extrair o nome de usuário e a senha do objeto PSCredential para fornecê-los a algum aplicativo ou serviço que exija a autenticação. As propriedades de uma credencial são armazenadas com segurança na Automação do Azure e podem ser acessadas no runbook ou na configuração DSC com a atividade [Get-AutomationPSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) .

> [!NOTE]
> Os ativos protegidos na Automação do Azure incluem credenciais, certificados, conexões e variáveis criptografadas. Esses ativos são criptografados e armazenados na Automação do Azure usando uma chave exclusiva que é gerada para cada conta de automação. Essa chave é criptografada por um certificado mestre e armazenada na Automação do Azure. Antes de armazenar um ativo seguro, a chave para a conta de automação é descriptografada usando o certificado mestre e usada para criptografar o ativo.  

## <a name="windows-powershell-cmdlets"></a>Cmdlets do Windows PowerShell
Os cmdlets na tabela a seguir são usados para criar e gerenciar ativos de credenciais de automação com o Windows PowerShell.  Eles são fornecidos como parte do [módulo do Azure PowerShell](/powershell/azure/overview) que está disponível para uso em runbooks e na configuração DSC da Automação.

| Cmdlets | Descrição |
|:--- |:--- |
| [Get-AzureAutomationCredential](/powershell/module/azure/get-azureautomationcredential?view=azuresmps-3.7.0) |Recupera informações sobre um ativo de credencial. Você só pode recuperar a credencial propriamente dita da atividade **Get-AutomationPSCredential** . |
| [New-AzureAutomationCredential](/powershell/module/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Cria uma nova credencial de Automação. |
| [Remove- AzureAutomationCredential](/powershell/module/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Remove uma credencial de Automação. |
| [Set- AzureAutomationCredential](/powershell/module/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Define as propriedades de uma credencial de Automação existente. |

## <a name="runbook-activities"></a>Atividades de runbook
As atividades na tabela a seguir são usadas para acessar credenciais em um runbook ou em uma configuração DSC.

| Atividades | Descrição |
|:--- |:--- |
| Get-AutomationPSCredential |Obtém uma credencial a ser usada em um runbook ou configuração DSC. Retorna um objeto [System.Management.Automation.PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential) . |

> [!NOTE]
> Evite usar variáveis no parâmetro –Name de Get-AutomationPSCredential, pois isso pode complicar a descoberta de dependências entre runbooks ou configurações DSC e ativos de credenciais no momento do design.

## <a name="python2-functions"></a>Funções Python2
A função na tabela a seguir é usada para acessar credenciais em um runbook em Python2.

| Função | Descrição |
|:---|:---|
| automationassets.get_automation_credential | Recupera informações sobre um ativo de credencial. |

> [!NOTE]
> É necessário importar o módulo "automationassets", na parte superior do runbook Python para acessar as funções do ativo.

## <a name="creating-a-new-credential-asset"></a>Criando um novo ativo de credencial

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Para criar um novo ativo de credencial com o portal do Azure
1. Em sua conta de automação, clique na parte **Ativos** para abrir a folha **Ativos**.
2. Clique na parte **Credenciais** para abrir a folha **Credenciais**.
3. Clique em **Adicionar uma credencial** na parte superior da folha.
4. Preencha o formulário e clique em **Criar** para salvar a nova credencial.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Para criar um novo ativo de credencial com o Windows PowerShell
Os comandos de exemplo a seguir mostram como criar uma nova credencial de automação. Um objeto PSCredential é criado pela primeira vez com o nome e a senha e, em seguida, é usado para criar o ativo de credencial. Como alternativa, você pode usar o cmdlet **Get-Credential** para ser solicitado a digitar um nome e uma senha.

    $user = "MyDomain\MyUser"
    $pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
    $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
    New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred

### <a name="to-create-a-new-credential-asset-with-the-azure-classic-portal"></a>Para criar um novo ativo de credencial com o portal clássico do Azure
1. Em sua conta de automação, clique em **Ativos** na parte superior da janela.
2. Clique em **Adicionar Configuração**na parte inferior da janela.
3. Clique em **Adicionar Credencial**.
4. No menu suspenso **Tipo de Credencial**, selecione a **Credencial do PowerShell**.
5. Conclua o assistente e clique na caixa de seleção para salvar a nova credencial.

## <a name="using-a-powershell-credential"></a>Usando uma credencial do PowerShell
Recupere um ativo de credencial em um runbook ou configuração DSC com a atividade **Get-AutomationPSCredential** . Isso retorna um [objeto PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) que você pode usar com uma atividade ou cmdlet que exija um parâmetro PSCredential. Você também pode recuperar as propriedades do objeto de credencial para usar individualmente. O objeto tem uma propriedade para o nome de usuário e a senha segura, ou você pode usar o método **GetNetworkCredential** para retornar um objeto [NetworkCredential](http://msdn.microsoft.com/library/system.net.networkcredential.aspx) que fornecerá uma versão sem segurança da senha.

### <a name="textual-runbook-sample"></a>Exemplo de runbook textual
Os comandos de exemplo a seguir mostram como usar uma credencial do PowerShell em um runbook. Neste exemplo, a credencial é recuperada e seu nome de usuário e senha são atribuídos a variáveis.

    $myCredential = Get-AutomationPSCredential -Name 'MyCredential'
    $userName = $myCredential.UserName
    $securePassword = $myCredential.Password
    $password = $myCredential.GetNetworkCredential().Password


### <a name="graphical-runbook-sample"></a>Exemplo de runbook gráfico
Adicione uma atividade **Get-AutomationPSCredential** a um runbook gráfico clicando com o botão direito na credencial no painel Biblioteca do editor gráfico e selecionando **Adicionar à tela**.

![Adicionar a credencial à tela](media/automation-credentials/credential-add-canvas.png)

A imagem a seguir mostra um exemplo do uso de uma credencial em um runbook gráfico.  Nesse caso, ela está sendo usada para fornecer autenticação para um runbook para os recursos do Azure, conforme descrito em [Autenticar Runbooks com uma conta de Usuário do Azure AD](automation-create-aduser-account.md).  A primeira atividade recupera a credencial que tem acesso à assinatura do Azure.  A atividade **Add-AzureAccount** usa essa credencial para fornecer autenticação para qualquer atividade que vier depois dela.  Um [link de pipeline](automation-graphical-authoring-intro.md#links-and-workflow) está disponível, já que **Get-AutomationPSCredential** está esperando um único objeto.  

![Adicionar a credencial à tela](media/automation-credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>Usando uma credencial do PowerShell na DSC
Embora as Configurações DSC na Automação do Azure possam fazer referência aos ativos de credencial usando **Get-AutomationPSCredential**, os ativos de credencial também podem ser transmitidos por meio de parâmetros, se desejado. Para obter mais informações, veja [Compilando configurações na DSC de Automação do Azure](automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>Usando credenciais no Python2
A seguir, veja um exemplo de como acessar as credenciais em runbooks Python2.

    import automationassets
    from automationassets import AutomationAssetNotFound

    # get a credential
    cred = automationassets.get_automation_credential("credtest")
    print cred["username"]
    print cred["password"]

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre links na criação gráfica, veja [Links na criação gráfica](automation-graphical-authoring-intro.md#links-and-workflow)
* Para entender os diferentes métodos de autenticação com Automação, consulte [Segurança da Automação do Azure](automation-security-overview.md)
* Para começar a usar os runbooks Gráficos, confira [O meu primeiro runbook gráfico](automation-first-runbook-graphical.md)
* Para começar a usar os runbooks do fluxo de trabalho do PowerShell, consulte [Meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md) 
* Para começar a usar runbooks Python2, consulte [Meu primeiro runbook Python2](automation-first-runbook-textual-python2.md) 

