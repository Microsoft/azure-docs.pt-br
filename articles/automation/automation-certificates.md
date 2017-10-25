---
title: "Ativos de certificado na Automação do Azure | Microsoft Docs"
description: "Os certificados podem ser armazenados com segurança na Automação do Azure para que possam ser acessados pelos runbooks ou pelas configurações DSC para serem autenticados em recursos do Azure e de terceiros.  Este artigo explica os detalhes de certificados e como trabalhar com elas na criação textual e gráfica."
services: automation
documentationcenter: 
author: eslesar
manager: stevenka
editor: tysonn
ms.assetid: ac9c22ae-501f-42b9-9543-ac841cf2cc36
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2017
ms.author: magoedte;bwren
ms.openlocfilehash: e434292485ef9da1a8e23da25ac731d9bf0177ce
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="certificate-assets-in-azure-automation"></a>Ativos de certificado na Automação do Azure

Os certificados podem ser armazenados com segurança na Automação do Azure para que possam ser acessados pelos runbooks ou pelas configurações DSC usando a atividade **Get-AzureRmAutomationCertificate** para recursos do Azure Resource Manager. Isso permite criar runbooks e configurações DSC que usam certificados para autenticação ou adicioná-los a recursos do Azure ou de terceiros.

> [!NOTE] 
> Os ativos protegidos na Automação do Azure incluem credenciais, certificados, conexões e variáveis criptografadas. Esses ativos são criptografados e armazenados na Automação do Azure usando uma chave exclusiva que é gerada para cada conta de automação. Essa chave é criptografada por um certificado mestre e armazenada na Automação do Azure. Antes de armazenar um ativo seguro, a chave para a conta de automação é descriptografada usando o certificado mestre e usada para criptografar o ativo.
> 

## <a name="windows-powershell-cmdlets"></a>Cmdlets do Windows PowerShell

Os cmdlets na tabela a seguir são usados para criar e gerenciar ativos de certificados de automação com o Windows PowerShell. Eles são fornecidos como parte do [módulo do Azure PowerShell](../powershell-install-configure.md) que está disponível para uso em runbooks e na configuração DSC da Automação.

|Cmdlets|Descrição|
|:---|:---|
|[Get-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationcertificate?view=azurermps-4.3.1)|Obtém informações sobre um certificado a ser usado em um runbook ou configuração DSC. Você só pode recuperar o certificado propriamente dito da atividade Get-AutomationCertificate.|
|[New-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/new-azurermautomationcertificate?view=azurermps-4.3.1)|Cria um novo certificado para a Automação do Azure.|
[Remove-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationcertificate?view=azurermps-4.3.1)|Remove um certificado da Automação do Azure.|Cria um novo certificado para a Automação do Azure.
|[Set-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/set-azurermautomationcertificate?view=azurermps-4.3.1)|Define as propriedades para um certificado existente, incluindo o carregamento do arquivo de certificado e a definição da senha para um. pfx.|
|[Add-AzureCertificate](https://msdn.microsoft.com/library/azure/dn495214.aspx)|Carrega um certificado de serviço para o serviço de nuvem especificado.|


## <a name="python2-functions"></a>Funções Python2

A função na tabela a seguir é usada para acessar certificados em um runbook Python2.

| Função | Descrição |
|:---|:---|
| automationassets.get_automation_certificate | Recupera informações sobre um ativo de certificado. |

> [!NOTE]
> É necessário importar o módulo **automationassets** no início do runbook Python para acessar as funções do ativo.


## <a name="creating-a-new-certificate"></a>Criando um novo certificado

Ao criar um novo certificado, você carrega um arquivo .cer ou .pfx na Automação do Azure. Se marcar certificado como exportável, você poderá transferi-lo do repositório de certificados da Automação do Azure. Se ele não for exportável, ele só poderá ser usado para a assinatura no runbook ou na configuração DSC.


### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Para criar um novo certificado com o portal do Azure

1. Na sua conta de automação, clique no bloco **Ativos** para abrir a folha **Ativos**.
1. Clique no bloco **Certificados** para abrir a folha **Certificados**.
1. Clique em **Adicionar um certificado** na parte superior da folha.
2. Digite um nome para o certificado na caixa **Nome** .
2. Clique em **Selecionar um arquivo** em **Carregar um arquivo de certificado** para procurar um arquivo .cer ou .pfx.  Se você selecionar um arquivo .pfx, especifique uma senha e se ele deve ter permissão para ser exportado.
1. Clique em **Criar** para salvar o novo ativo de certificado.


### <a name="to-create-a-new-certificate-with-windows-powershell"></a>Para criar um novo certificado com o Windows PowerShell

O exemplo a seguir mostra como criar um novo Certificado de automação e marcá-lo como exportável. Isso importa um arquivo .pfx existente.

    $certName = 'MyCertificate'
    $certPath = '.\MyCert.pfx'
    $certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
    $ResourceGroup = "ResourceGroup01"
    
    New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable -ResourceGroupName $ResourceGroup

## <a name="using-a-certificate"></a>Usando um certificado

Você deve usar a atividade **Get-AutomationCertificate** para usar um certificado. Não é possível usar o cmdlet [Get-AzureRmAutomationCertificate](https://msdn.microsoft.com/library/mt603765.aspx), pois ele retorna informações sobre o ativo de certificado, mas não sobre o próprio certificado.

### <a name="textual-runbook-sample"></a>Exemplo de runbook textual

O código de exemplo a seguir mostra como adicionar um certificado a um serviço de nuvem em um runbook. Nesse exemplo, a senha é recuperada de uma variável de automação criptografada.

    $serviceName = 'MyCloudService'
    $cert = Get-AutomationCertificate -Name 'MyCertificate'
    $certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResouceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
    Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert

### <a name="graphical-runbook-sample"></a>Exemplo de runbook gráfico

Você adiciona **Get-AutomationCertificate** a um runbook gráfico clicando com o botão direito no certificado no painel Biblioteca do editor gráfico e selecionando **Adicionar à tela**.

![Adicionar certificado à tela](media/automation-certificates/automation-certificate-add-to-canvas.png)

A imagem a seguir mostra um exemplo do uso de um certificado em um runbook gráfico.  Esse é o mesmo exemplo mostrado acima para adicionar um certificado a um serviço de nuvem de um runbook textual.

![Exemplo de criação gráfica ](media/automation-certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Exemplo de Python2
O exemplo a seguir mostra como acessar certificados em runbooks do Python2.

    # get a reference to the Azure Automation certificate
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
    
    # returns the binary cert content  
    print cert 

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como trabalhar com links para controlar o fluxo lógico de atividades que o seu runbook é projetado para realizar, consulte [Links na criação gráfica](automation-graphical-authoring-intro.md#links-and-workflow). 
