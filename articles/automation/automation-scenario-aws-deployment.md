---
title: "Automatizando a implantação de uma VM no Amazon Web Services | Microsoft Docs"
description: "Este artigo demonstra como usar a Automação do Azure para automatizar a criação de uma VM do Amazon Web Services"
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 1d85c01a-d795-4523-8194-84fc15b53838
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2017
ms.author: tiandert; bwren
ms.openlocfilehash: 828f9e2cc9a39e54933cd0e0db7273efa460d0c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-scenario---provision-an-aws-virtual-machine"></a>Cenário de Automação do Azure – provisionar uma máquina virtual do AWS
Neste artigo, nós demonstramos como você pode usar a Automação do Azure para provisionar uma máquina virtual na sua assinatura do AWS (Amazon Web Services) e dar um nome específico à VM – o que o AWS chama de "marcar" a VM.

## <a name="prerequisites"></a>Pré-requisitos
Para fins deste artigo, você precisa ter uma conta da Automação do Azure e uma assinatura do AWS. Para obter mais informações sobre como configurar uma conta da Automação do Azure e configurá-la com suas credenciais de assinatura do AWS, consulte [Configurar a autenticação com a Amazon Web Services](automation-config-aws-account.md).  Essa conta deve ser criada ou atualizada com suas credenciais de assinatura do AWS antes de prosseguir, pois faremos referência a ela nas etapas abaixo.

## <a name="deploy-amazon-web-services-powershell-module"></a>Implantar Módulo do PowerShell do Amazon Web Services
Nosso runbook de provisionamento de VM utilizará o módulo do PowerShell do AWS para fazer seu trabalho. Execute as seguintes etapas para adicionar o módulo à sua conta da Automação que está configurada com as credenciais de assinatura do AWS.  

1. Abra seu navegador da Web, navegue até a [Galeria do PowerShell](http://www.powershellgallery.com/packages/AWSPowerShell/) e clique no botão **Implantar na Automação do Azure**.<br><br> ![Importação de módulo de PS do AWS](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Você será levado para a página de logon do Azure e, depois de autenticar, será encaminhado para o portal do Azure e verá a seguinte página.<br><br> ![Página Importar Módulo](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Selecione o Grupo de Recursos na lista suspensa **Grupo de Recursos** e, no painel Parâmetros, forneça as seguintes informações:
   
   * Na lista suspensa **Conta de Automação Nova ou Existente (string)**, selecione **Existente**.  
   * Na caixa **Nome da Conta de Automação (string)** , digite o nome exato da conta de automação que inclui as credenciais de sua assinatura do AWS.  Por exemplo, se tiver criado uma conta dedicada chamada **AWSAutomation**, será isso que você digitará na caixa.
   * Selecione a região apropriada na lista suspensa **Local da Conta de Automação** .
4. Quando tiver terminado de inserir as informações necessárias, clique em **Criar**.
   
   > [!NOTE]
   > Quando um módulo do PowerShell é importado para a Automação do Azure, ele também extrai os cmdlets. Essas atividades não são exibidas até que o módulo tenha terminado de importar e extrair completamente os cmdlets. Esse processo pode levar alguns minutos.  
   > <br>
   > 
   > 
5. No portal do Azure, abra sua conta da Automação mencionada na etapa 3.
6. Clique no bloco **Ativos** no painel **Ativos** e selecione o bloco **Módulos**.
7. Na página **Módulos**, você verá o módulo **AWSPowerShell** na lista.

## <a name="create-aws-deploy-vm-runbook"></a>Criar runbook de implantação de VM no AWS
Após implantar o Módulo do PowerShell do AWS, podemos criar um runbook para automatizar o provisionamento de uma máquina virtual no AWS usando um script do PowerShell. As etapas a seguir demonstram como utilizar o script nativo do PowerShell na Automação do Azure.  

> [!NOTE]
> Para ver mais opções e informações sobre esse script, visite a [Galeria do PowerShell](https://www.powershellgallery.com/packages/New-AwsVM/DisplayScript).
> 

1. Baixe o script New-AwsVM da Galeria do PowerShell abrindo uma sessão do PowerShell e digitando o seguinte:<br>
   ```
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. No portal do Azure, abra sua conta da Automação e selecione **Runbooks** na seção **Automação de Processos** à esquerda.  
3. Na página **Runbooks**, selecione **Adicionar um runbook**.
4. No painel **Adicionar um runbook**, selecione **Criação Rápida** (Criar um novo runbook).
5. No painel de propriedades do **Runbook**, digite na caixa Nome um nome para seu runbook e, na lista suspensa **Tipo de runbook**, selecione **PowerShell** e clique em **Criar**.<br><br> ![Painel Criar runbook](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
6. Quando a página Editar Runbook do PowerShell aparecer, copie e cole o script do PowerShell na tela de criação do runbook.<br><br> ![Script do PowerShell de Runbook](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    > [!NOTE]
    > Observe o seguinte ao trabalhar com o script do PowerShell de exemplo:
    > 
    > * O runbook contém diversos valores de parâmetros padrão. Avalie todos os valores padrão e atualize-os conforme necessário.
    > * Se tiver armazenado suas credenciais do AWS como ativos de credencial com nomes diferentes de **AWScred**, você precisará atualizar o script na linha 57 para que os nomes correspondam da forma correta.  
    > * Ao trabalhar com comandos da CLI do AWS no PowerShell, especialmente com este runbook de exemplo, você deve especificar a região do AWS. Caso contrário, os cmdlets falharão.  Para obter mais detalhes, consulte o tópico [Specify AWS Region (Especificar região do AWS)](http://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) no documento AWS Tools for PowerShell (Ferramentas do AWS para PowerShell).  
    >

7. Para recuperar uma lista de nomes de imagem de sua assinatura do AWS, inicie o ISE do PowerShell e importe o Módulo do PowerShell do AWS.  Autentique no AWS substituindo **Get-AutomationPSCredential** em seu ambiente do ISE por **AWScred = Get-Credential**.  Serão solicitadas suas credenciais e você poderá fornecer a **ID de Chave de Acesso** como nome de usuário e a **Chave de Acesso Secreta** como senha.  Veja o exemplo abaixo:  

        #Sample to get the AWS VM available images
        #Please provide the path where you have downloaded the AWS PowerShell module
        Import-Module AWSPowerShell
        $AwsRegion = "us-west-2"
        $AwsCred = Get-Credential
        $AwsAccessKeyId = $AwsCred.UserName
        $AwsSecretKey = $AwsCred.GetNetworkCredential().Password
   
        # Set up the environment to access AWS
        Set-AwsCredentials -AccessKey $AwsAccessKeyId -SecretKey $AwsSecretKey -StoreAs AWSProfile
        Set-DefaultAWSRegion -Region $AwsRegion
   
        Get-EC2ImageByName -ProfileName AWSProfile

    A seguinte saída será retornada:<br><br>
   ![Obter imagens do AWS](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
8. Copie e cole um dos nomes de imagem em uma variável da Automação referenciada no runbook, como **$InstanceType**. Como neste exemplo estamos usando a assinatura em camadas gratuita do AWS, vamos usar **t2.micro** em nosso exemplo de runbook.  
9. Salve o runbook, clique em **Publicar** para publicá-lo e em **Sim** quando solicitado.

### <a name="testing-the-aws-vm-runbook"></a>Testando o runbook de VM do AWS
Antes de continuarmos testando o runbook, precisamos verificar algumas coisas. Especificamente:  

* Um ativo para se autenticar no AWS foi criado chamado **AWScred** ou o script foi atualizado para referenciar o nome do seu ativo de credencial.    
* O módulo do PowerShell do AWS foi importado para a Automação do Azure  
* Foi criado um novo runbook e valores de parâmetro foram verificados e atualizados conforme necessário  
* **Registros detalhados de log** e, opcionalmente, **Registros de progresso de log** nas configurações do runbook **Log e rastreamento** foram definidos como **Ativado**.<br><br> ![Log e rastreamento de Runbook](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png)  

1. Nós queremos iniciar o runbook, sendo assim, clique em **Iniciar** e em **OK** quando o painel Iniciar Runbook for aberta.
2. No painel Iniciar Runbook, forneça um **VMname**.  Aceite os valores padrão para os outros parâmetros que foram pré-configurados no script.  Clique em **OK** para iniciar o trabalho de runbook.<br><br> ![Iniciar runbook New-AwsVM](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
3. Um painel de trabalho é aberto para o trabalho de runbook que acabamos de criar. Feche esse painel.
4. Podemos exibir o andamento do trabalho e **Fluxos** de saída selecionando o bloco **Todos os Logs** na página do trabalho do runbook.<br><br> ![Saída de fluxo](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
5. Para confirmar que a VM está sendo provisionada, faça logon no Console de Gerenciamento do AWS se não estiver conectado.<br><br> ![VM implantada por console do AWS](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Próximas etapas
* Para começar a usar runbooks gráficos, veja [O meu primeiro runbook gráfico](automation-first-runbook-graphical.md)
* Para começar a usar runbooks de fluxo de trabalho do PowerShell, veja [Meu primeiro runbook de Fluxo de Trabalho do PowerShell](automation-first-runbook-textual.md)
* Para saber mais sobre os tipos de runbook, suas vantagens e limitações, veja [Tipos de runbook da Automação do Azure](automation-runbook-types.md)
* Para saber mais sobre o recurso de suporte de script do PowerShell, veja [Native PowerShell script support in Azure Automation (Suporte a scripts nativos do PowerShell na Automação do Azure)](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)

