---
title: "Ativos de conexão na Automação do Azure | Microsoft Docs"
description: "Os ativos de conexão na Automação do Azure contêm as informações necessárias para se conectar a um aplicativo ou serviço externo de um runbook ou uma configuração DSC. Este artigo explica os detalhes de conexões e como trabalhar com elas na criação textual e gráfica."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: f0239017-5c66-4165-8cca-5dcb249b8091
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/13/2017
ms.author: magoedte; bwren
ms.openlocfilehash: f1746f4f6706835d43edc171b03d4ececfa3560c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connection-assets-in-azure-automation"></a>Ativos de conexão na Automação do Azure

Um ativo de conexão da Automação contém as informações necessárias para se conectar a um aplicativo ou serviço externo de um runbook ou da configuração DSC. Isso pode incluir as informações necessárias para autenticação, como um nome de usuário e uma senha, além das informações de conexão, como uma URL ou uma porta. O valor de uma conexão é manter todas as propriedades para se conectar a um aplicativo específico em um ativo, em vez de criar diversas variáveis. O usuário pode editar os valores para uma conexão em um único local, e você pode transmitir o nome de uma conexão para um runbook ou uma configuração DSC em um único parâmetro. As propriedades de uma conexão podem ser acessadas no runbook ou na configuração DSC com a atividade **Get-AutomationConnection** . 

Ao criar uma conexão, você deve especificar um *tipo de conexão*. O tipo de conexão é um modelo que define um conjunto de propriedades. A conexão define valores para cada propriedade definida em seu tipo de conexão. Tipos de conexão são adicionados à automação do Azure nos módulos de integração ou criados com a [API de automação do Azure](http://msdn.microsoft.com/library/azure/mt163818.aspx) se o módulo de integração inclui um tipo de conexão e é importado para sua conta de automação. Caso contrário, você precisará criar um arquivo de metadados para especificar um tipo de conexão de automação.  Para saber mais sobre isso, veja [Módulos de integração](automation-integration-modules.md).  

>[!NOTE] 
>Os ativos protegidos na Automação do Azure incluem credenciais, certificados, conexões e variáveis criptografadas. Esses ativos são criptografados e armazenados na Automação do Azure usando uma chave exclusiva que é gerada para cada conta de automação. Essa chave é criptografada por um certificado mestre e armazenada na Automação do Azure. Antes de armazenar um ativo seguro, a chave para a conta de automação é descriptografada usando o certificado mestre e usada para criptografar o ativo.

## <a name="windows-powershell-cmdlets"></a>Cmdlets do Windows PowerShell

Os cmdlets na tabela a seguir são usados para criar e gerenciar conexões de Automação com o Windows PowerShell. Eles são fornecidos como parte do [módulo do Azure PowerShell](/powershell/azure/overview) que está disponível para uso em runbooks e na configuração DSC da Automação.

|Cmdlet|Descrição|
|:---|:---|
|[Get-AzureRmAutomationConnection](/powershell/module/azurerm.automation/get-azurermautomationconnection)|Recupera uma conexão. Inclui uma tabela de hash com os valores dos campos da conexão.|
|[New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection)|Cria uma nova conexão.|
|[Remove-AzureRmAutomationConnection](/powershell/module/azurerm.automation/remove-azurermautomationconnection)|Remove uma conexão existente.|
|[Set-AzureRmAutomationConnectionFieldValue](/powershell/module/azurerm.automation/set-azurermautomationconnectionfieldvalue)|Define o valor de determinado campo para uma conexão existente.|

## <a name="activities"></a>Atividades

As atividades na tabela a seguir são usadas para acessar conexões em um runbook ou em uma configuração DSC.

|Atividades|Descrição|
|---|---|
|[Get-AutomationConnection](/powershell/module/azure/get-azureautomationconnection?view=azuresmps-3.7.0)|Obtém uma conexão a ser usada. Retorna uma tabela de hash com as propriedades da conexão.|

>[!NOTE] 
>Evite usar variáveis no parâmetro –Name de **Get-AutomationConnection**, pois isso pode complicar a descoberta de dependências entre configurações de runbooks ou de DSC e ativos de conexão no momento do design.

 
## <a name="python2-functions"></a>Funções Python2 
A função na tabela a seguir é usada para acessar conexões em um runbook Python2. 

| Função | Descrição | 
|:---|:---| 
| automationassets.get_automation_connection | Recupera uma conexão. Retorna um dicionário com as propriedades da conexão. | 

> [!NOTE] 
> É necessário importar o módulo "automationassets", na parte superior do runbook Python para acessar as funções do ativo.

## <a name="creating-a-new-connection"></a>Criando uma nova conexão

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Para criar uma nova conexão com o portal do Azure

1. Em sua conta de automação, clique na parte **Ativos** para abrir a folha **Ativos**.
2. Clique na parte **Conexões** para abrir a folha **onexões**.
3. Clique em **Adicionar uma conexão** na parte superior da folha.
4. No menu suspenso **Tipo** , selecione o tipo de conexão que você deseja criar. O formulário apresentará as propriedades para esse tipo específico.
5. Preencha o formulário e clique em **Criar** para salvar a nova conexão.

### <a name="to-create-a-new-connection-with-the-azure-classic-portal"></a>Para criar uma nova conexão com o portal clássico do Azure

1. Em sua conta de automação, clique em **Ativos** na parte superior da janela.
2. Clique em **Adicionar Configuração**na parte inferior da janela.
3. Clique em **Adicionar Conexão**.
4. No menu suspenso **Tipo de Conexão** , selecione o tipo de conexão que você deseja criar.  O assistente apresentará as propriedades para esse tipo específico.
5. Conclua o assistente e clique na caixa de seleção para salvar a nova conexão.

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Para criar uma nova conexão com o Windows PowerShell

Criar uma nova conexão com o Windows PowerShell usando o cmdlet [New-AzureRmAutomationConnection](/powershell/module/azurerm.automation/new-azurermautomationconnection). Esse cmdlet tem um parâmetro chamado **ConnectionFieldValues** que espera uma [tabela de hash](http://technet.microsoft.com/library/hh847780.aspx) definindo valores para cada uma das propriedades definidas pelo tipo de conexão.

Se você estiver familiarizado com a [conta Executar Como](automation-sec-configure-azure-runas-account.md) da Automação para autenticar runbooks usando a entidade de serviço, o script do PowerShell, fornecido como uma alternativa para criar a conta Executar Como do portal, cria um novo ativo de conexão usando os seguintes comandos de exemplo.  

    $ConnectionAssetName = "AzureRunAsConnection"
    $ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
    New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues 

É possível usar o script para criar o ativo de conexão, porque quando você cria sua conta de automação, ele automaticamente inclui vários módulos globais por padrão com o tipo de conexão **AzurServicePrincipal** para criar o ativo de conexão **AzureRunAsConnection**.  É importante lembrar-se disso porque se você tentar criar um novo ativo de conexão para se conectar a um serviço ou aplicativo com um método de autenticação diferente, ele falhará porque o tipo de conexão já não está definido na sua conta de automação.  Para saber mais sobre como criar seu próprio tipo de conexão para um módulo personalizado da [Galeria do PowerShell](https://www.powershellgallery.com), veja [Módulos de integração](automation-integration-modules.md)
  
## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Usando uma conexão em um runbook ou configuração DSC

Recupere uma conexão em um runbook ou configuração DSC com o cmdlet **Get-AutomationConnection** .  Não é possível usar a atividade [Get-AzureRmAutomationConnection](https://docs.microsoft.com/powershell/resourcemanager/azurerm.automation/v1.0.12/Get-AzureRmAutomationConnection?redirectedfrom=msdn).  Essa atividade recupera os valores dos diferentes campos na conexão e os retorna como um [tabela de hash](http://go.microsoft.com/fwlink/?LinkID=324844) que pode então ser usada com os comandos apropriados no runbook ou na configuração DSC.

### <a name="textual-runbook-sample"></a>Exemplo de runbook textual

Os comandos de exemplo a seguir mostram como usar a conta Executar como mencionada anteriormente para autenticar com os recursos do Azure Resource Manager em seu runbook.  Eles usam o ativo de conexão que representa a conta Executar como, que faz referência à entidade de serviço baseada em certificado, não às credenciais.  

    $Conn = Get-AutomationConnection -Name AzureRunAsConnection 
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint 

### <a name="graphical-runbook-samples"></a>Exemplos de runbook gráfico

Adicione uma atividade **Get-AutomationConnection** a um runbook gráfico clicando com o botão direito na conexão no painel Biblioteca do editor gráfico e selecionando **Adicionar à tela**.

![](media/automation-connections/connection-add-canvas.png)

A imagem a seguir mostra um exemplo do uso de uma conexão em um runbook gráfico.  Este é o mesmo exemplo mostrado acima para autenticar usando a conta Executar como com um runbook textual.  Este exemplo usa o conjunto de dados **Valor Constante** para a atividade **Obter Conexão Executar como**, que usa um objeto de conexão para autenticação.  Usa-se um [link de pipeline](automation-graphical-authoring-intro.md#links-and-workflow) aqui, pois o parâmetro ServicePrincipalCertificate está esperando um único objeto.

![](media/automation-connections/automation-get-connection-object.png)

### <a name="python2-runbook-sample"></a>Exemplo de runbook Python2
O exemplo a seguir mostra como autenticar usando a conexão Executar Como em um runbook Python2.

    """ Tutorial to show how to authenticate against Azure resource manager resources """
    import azure.mgmt.resource
    import automationassets


    def get_automation_runas_credential(runas_connection):
        """ Returns credentials to authenticate against Azure resoruce manager """
        from OpenSSL import crypto
        from msrestazure import azure_active_directory
        import adal

        # Get the Azure Automation Run As service principal certificate
        cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
        pks12_cert = crypto.load_pkcs12(cert)
        pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

        # Get Run As connection information for the Azure Automation service principal
        application_id = runas_connection["ApplicationId"]
        thumbprint = runas_connection["CertificateThumbprint"]
        tenant_id = runas_connection["TenantId"]

        # Authenticate with service principal certificate
        resource = "https://management.core.windows.net/"
        authority_url = ("https://login.microsoftonline.com/" + tenant_id)
        context = adal.AuthenticationContext(authority_url)
        return azure_active_directory.AdalAuthentication(
            lambda: context.acquire_token_with_client_certificate(
                resource,
                application_id,
                pem_pkey,
                thumbprint)
        )


    # Authenticate to Azure using the Azure Automation Run As service principal
    runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
    azure_credential = get_automation_runas_credential(runas_connection)

## <a name="next-steps"></a>Próximas etapas

- Revise [Links na criação gráfica](automation-graphical-authoring-intro.md#links-and-workflow) para entender como direcionar e controlar o fluxo de lógica em seus runbooks.  

- Para saber mais sobre o uso de módulos do PowerShell pela automação do Azure e práticas recomendadas para a criação de seus próprios módulos do PowerShell, para que funcionem como Módulos de integração na automação do Azure, veja [Módulos de integração](automation-integration-modules.md).  
