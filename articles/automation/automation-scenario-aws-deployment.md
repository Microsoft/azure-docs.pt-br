---
title: Implantar uma VM do Amazon Web Services com um runbook da Automação do Azure
description: Este artigo mostra como automatizar a criação de uma VM do Amazon Web Services.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 03f1f1659d120a2aa1b827063cb2bc84138f3655
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896063"
---
# <a name="deploy-an-amazon-web-services-vm-with-a-runbook"></a>Implantar uma VM do Amazon Web Services com um runbook

Neste artigo, você aprenderá a usar a Automação do Azure para provisionar uma máquina virtual na sua assinatura do AWS (Amazon Web Services) e dar um nome específico à VM – o que o AWS chama de "marcar" a VM.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa ter uma conta da Automação do Azure e uma assinatura do AWS (Amazon Web Services). Para obter mais informações sobre como configurar uma conta da Automação do Azure e configurá-la com suas credenciais de assinatura do AWS, consulte [Configurar a autenticação com a Amazon Web Services](automation-config-aws-account.md). Essa conta deve ser criada ou atualizada com suas credenciais de assinatura do AWS antes de prosseguir, pois você a usará nas etapas abaixo.

## <a name="deploy-amazon-web-services-powershell-module"></a>Implantar Módulo do PowerShell do Amazon Web Services

Seu runbook de provisionamento de VM usa o módulo do PowerShell do AWS para fazer o trabalho. Execute as etapas a seguir para adicionar o módulo à sua conta de Automação que está configurada com as credenciais de assinatura do AWS.  

1. Abra seu navegador da Web, navegue até a [Galeria do PowerShell](https://www.powershellgallery.com/packages/AWSPowerShell/) e clique no botão **Implantar na Automação do Azure**.<br><br> ![Importação de módulo de PS do AWS](./media/automation-scenario-aws-deployment/powershell-gallery-download-awsmodule.png)
2. Você será levado para a página de logon do Azure e, depois de autenticar, será encaminhado para o portal do Azure e verá a seguinte página:<br><br> ![Página Importar Módulo](./media/automation-scenario-aws-deployment/deploy-aws-powershell-module-parameters.png)
3. Selecione a conta de Automação a ser usada e clique em **OK** para iniciar a implantação.

   > [!NOTE]
   > Quando a Automação do Azure importa um módulo do PowerShell, ela extrai os cmdlets. As atividades não aparecem até que a Automação tenha concluído completamente a importação do módulo e a extração dos cmdlets. Esse processo pode levar alguns minutos.  
   > <br>

1. No portal do Azure, abra sua conta da Automação.
2. Clique no bloco **Ativos** e, no painel Ativos, selecione **Módulos**.
3. Na página Módulos, você verá o módulo **AWSPowerShell** na lista.

## <a name="create-aws-deploy-vm-runbook"></a>Criar runbook de implantação de VM no AWS

Após implantar o Módulo do PowerShell do AWS, você poderá criar um runbook para automatizar o provisionamento de uma máquina virtual no AWS usando um script do PowerShell. As etapas a seguir demonstram como usar o script nativo do PowerShell na Automação do Azure.  

> [!NOTE]
> Para ver mais opções e informações sobre esse script, visite a [Galeria do PowerShell](https://www.powershellgallery.com/packages/New-AwsVM/).
> 

1. Baixe o script New-AwsVM do PowerShell na Galeria do PowerShell abrindo uma sessão do PowerShell e digitando o seguinte comando:<br>
   ```powershell
   Save-Script -Name New-AwsVM -Path <path>
   ```
   <br>
2. No portal do Azure, abra sua conta de Automação e selecione **Runbooks** em **Automação de Processos**.  
3. Na página Runbooks, selecione **Adicionar um runbook**.
4. No painel Adicionar um runbook, selecione **Criação Rápida** para criar um runbook.
5. No painel Propriedades do runbook, digite um nome para o runbook.
6. Na lista suspensa **Tipo de Runbook**, selecione **PowerShell** e, em seguida, clique em **Criar**.<br><br> ![Painel Criar runbook](./media/automation-scenario-aws-deployment/runbook-quickcreate-properties.png)
7. Quando a página Editar Runbook do PowerShell aparecer, copie e cole o script do PowerShell na tela de criação do runbook.<br><br> ![Script do PowerShell de Runbook](./media/automation-scenario-aws-deployment/runbook-powershell-script.png)<br>
   
    Observe o seguinte ao trabalhar com o script do PowerShell de exemplo:

    * O runbook contém diversos valores de parâmetros padrão. Avalie todos os valores padrão e atualize-os conforme necessário.
    * Se você armazenou suas credenciais do AWS como um ativo de credencial denominado de forma diferente de `AWScred`, precisará atualizar o script na linha 57 para que ele seja correspondente.  
    * Ao trabalhar com comandos da CLI do AWS no PowerShell, especialmente com este runbook de exemplo, você deve especificar a região do AWS. Caso contrário, os cmdlets falharão. Para obter mais detalhes, consulte o tópico [Specify AWS Region (Especificar região do AWS)](https://docs.aws.amazon.com/powershell/latest/userguide/pstools-installing-specifying-region.html) no documento AWS Tools for PowerShell (Ferramentas do AWS para PowerShell).  

8. Para recuperar uma lista de nomes de imagem de sua assinatura do AWS, inicie o ISE do PowerShell e importe o Módulo do PowerShell do AWS. Faça a autenticação no AWS substituindo `Get-AutomationPSCredential` no ambiente do ISE por `AWScred = Get-Credential`. Essa instrução solicita suas credenciais, e você pode fornecer sua ID de chave de acesso para o nome de usuário e sua chave de acesso secreta para a senha. 

      ```powershell
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
      ```
        
    A seguinte saída será retornada:<br><br>
   ![Obter imagens do AWS](./media/automation-scenario-aws-deployment/powershell-ise-output.png)<br>  
9. Copie e cole um dos nomes de imagem em uma variável de Automação referenciada no runbook como `$InstanceType`. Como, neste exemplo, você está usando a assinatura em camadas gratuita do AWS, use **t2.micro** para seu exemplo de runbook.  
10. Salve o runbook, clique em **Publicar** para publicá-lo e em **Sim** quando solicitado.

### <a name="test-the-aws-vm-runbook"></a>Testar o runbook de VM do AWS

1. Verifique se o runbook cria um ativo chamado `AWScred` para autenticação no AWS ou atualize o script para referenciar o nome do seu ativo de credencial.    
2. Verifique seu novo runbook e certifique-se de que todos os valores de parâmetro tenham sido atualizados.
Verifique se o módulo do PowerShell do AWS foi importado para a Automação do Azure.  
3. Na Automação do Azure, defina **Registros detalhados de log** e, opcionalmente, **Registros de andamento de log** na operação de runbook **Log e rastreamento** para **Ativado**.<br><br> ![Log e rastreamento de runbook](./media/automation-scenario-aws-deployment/runbook-settings-logging-and-tracing.png).  
4. Clique em **Iniciar** para iniciar o runbook e, em seguida, clique em **OK** quando o painel Iniciar Runbook for aberto.
5. No painel Iniciar Runbook, forneça um nome de VM. Aceite os valores padrão para os outros parâmetros pré-configurados no script. Clique em **OK** para iniciar o trabalho de runbook.<br><br> ![Iniciar runbook New-AwsVM](./media/automation-scenario-aws-deployment/runbook-start-job-parameters.png)
6. Um painel Trabalho é aberto para o trabalho de runbook criado. Feche esse painel.
7. Você pode exibir o progresso do trabalho e exibir os fluxos de saída selecionando **Todos os Logs** no painel Trabalho do runbook.<br><br> ![Saída de fluxo](./media/automation-scenario-aws-deployment/runbook-job-streams-output.png)
8. Para confirmar que a VM está sendo provisionada, faça logon no console de gerenciamento do AWS se você não estiver conectado no momento.<br><br> ![VM implantada por console do AWS](./media/automation-scenario-aws-deployment/aws-instances-status.png)

## <a name="next-steps"></a>Próximas etapas
 
* Para descobrir quais runbooks têm suporte, confira [Tipos de runbook da Automação do Azure](automation-runbook-types.md).
* Para se familiarizar com os runbooks, confira [Gerenciar runbooks na Automação do Azure](manage-runbooks.md).
* Para obter detalhes do PowerShell, confira [Documentos do PowerShell](/powershell/scripting/overview).
* Para obter suporte a scripts, confira [Suporte ao script nativo do PowerShell na Automação do Azure](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
* Para obter uma referência de cmdlet do PowerShell, confira [Az.Automation](/powershell/module/az.automation).
