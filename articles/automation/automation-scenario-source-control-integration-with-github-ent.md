---
title: "Integração de controle de origem da Automação do Azure com o GitHub Enterprise | Microsoft Docs"
description: "Descreve os detalhes de como configurar a integração com o GitHub Enterprise para controle de origem de runbooks da Automação."
services: automation
documentationCenter: 
authors: eslesar
manager: jwhit
editor: 
ms.assetid: e01d817c-7d38-421c-adf5-647a4b526eb4
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: magoedte
ms.openlocfilehash: 2b3ca90382a1c4fb6446799ad539ab8ee603aefc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-scenario---automation-source-control-integration-with-github-enterprise"></a>Cenário da Automação do Azure - Integração de controle de origem da Automação com o GitHub Enterprise

No momento, a Automação oferece suporte à integração de controle de origem, o que permite que você associe runbooks em sua conta de Automação a um repositório de controle de origem do GitHub.  No entanto, os clientes que implantaram o [GitHub Enterprise](https://enterprise.github.com/home) para dar suporte a suas práticas de DevOps também querem usá-lo para gerenciar o ciclo de vida de runbooks que são desenvolvidos para automatizar processos de negócios e operações de gerenciamento de serviço.  

Nesse cenário, você terá um computador com Windows em seu data center configurado como um Hybrid Runbook Worker com os módulos do Azure Resource Manager e as ferramentas Git instalados.  O computador Hybrid Worker possui um clone do repositório Git local.  Quando o runbook for executado no Hybrid Worker, o diretório do Git será sincronizado e o conteúdo do arquivo de runbook será importado para a conta de Automação.

Este artigo descreve como definir essa configuração em seu ambiente de Automação do Azure. Começaremos configurando a Automação com as credenciais de segurança, com os runbooks necessários para oferecer suporte a esse cenário e com a implantação de um Hybrid Runbook Worker em seu data center, a fim de executar runbooks e acessar seu repositório do GitHub Enterprise para sincronizar runbooks com sua conta de Automação.  


## <a name="getting-the-scenario"></a>Obtendo o cenário

Este cenário é composto por dois runbooks do PowerShell que você pode importar diretamente da [Galeria de Runbooks](automation-runbook-gallery.md) no Portal do Azure, ou baixar da [Galeria do PowerShell](https://www.powershellgallery.com).

### <a name="runbooks"></a>Runbooks

Runbook | Descrição| 
--------|------------|
Export-RunAsCertificateToHybridWorker | O Runbook exporta um certificado RunAs de uma conta de Automação para um Hybrid Worker, de modo que os runbooks no worker possam autenticar com Azure a fim de importar runbooks para a conta de Automação.| 
Sync-LocalGitFolderToAutomationAccount | O Runbook sincroniza a pasta Git local no computador híbrido e, em seguida, importa os arquivos de runbook (*.ps1) para a conta de Automação.|

### <a name="credentials"></a>Credenciais

Credencial | Descrição|
-----------|------------|
GitHRWCredential | Ativo de credencial que você cria contendo o nome de usuário e a senha para um usuário com permissões no Hybrid Worker.|

## <a name="installing-and-configuring-this-scenario"></a>Instalando e configurando esse cenário

### <a name="prerequisites"></a>Pré-requisitos

1. O runbook Sync-LocalGitFolderToAutomationAccount autentica usando a conta [Executar como do Azure](automation-sec-configure-azure-runas-account.md). 

2. Também é necessário ter um espaço de trabalho do Microsoft OMS (Operations Management Suite) com a solução de Automação do Azure habilitada e configurada.  Se você não tiver um associado à conta de Automação usada para instalar e configurar esse cenário, ele é criado e configurado para você durante a execução do script **New-OnPremiseHybridWorker.ps1** no Hybrid Runbook Worker.        

    > [!NOTE]
    > Atualmente, as regiões a seguir apenas oferecem suporte para integração de automação com OMS: **Sudeste da Austrália**, **Leste dos EUA 2**, **Sudeste Asiático** e **Europa Ocidental**. 

3. Um computador que pode servir como um Hybrid Runbook Worker dedicado e que também hospedará o software do GitHub e manterá os arquivos de runbook (*runbook*.ps1) em um diretório de origem no sistema de arquivos para sincronização entre o GitHub e sua conta de Automação.

### <a name="import-and-publish-the-runbooks"></a>Importar e publicar os runbooks

Para importar os runbooks *Export-RunAsCertificateToHybridWorker* e *Sync-LocalGitFolderToAutomationAccount* da Galeria de Runbooks de sua conta de Automação no Portal do Azure, siga os procedimentos em [Importar runbook da Galeria de Runbooks](automation-runbook-gallery.md#to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal). Publique os runbooks depois que eles forem importados com êxito em sua conta de Automação.

### <a name="deploy-and-configure-hybrid-runbook-worker"></a>Implantar e configurar o Hybrid Runbook Worker

Se você ainda não tiver um Hybrid Runbook Worker já implantado em seu data center, revise os requisitos e siga as etapas de instalação automatizada usando o procedimento em [Hybrid Runbook Worker da Automação do Azure - Automatizar a instalação e a configuração](automation-hybrid-runbook-worker.md#automated-deployment).  Após a instalação bem-sucedida do Hybrid Worker em um computador, execute as etapas a seguir para concluir a configuração do suporte a esse cenário.

1. Entre no computador que hospeda a função Hybrid Runbook Worker com uma conta que tenha direitos administrativos locais e crie um diretório para armazenar os arquivos de runbook do Git.  Clone o repositório Git interno no diretório.
2. Se você ainda não tiver uma conta de Executar como, ou se quiser criar uma nova dedicada a essa finalidade, no Portal do Azure navegue até as contas de Automação, selecione sua conta de Automação e crie um [ativo de credencial](automation-credentials.md) contendo o nome de usuário e a senha de um usuário com permissões para o Hybrid Worker.  
3. Em sua conta de Automação, [edite o runbook](automation-edit-textual-runbook.md) **Export-RunAsCertificateToHybridWorker** e modifique o valor da variável *$Password* com uma senha forte.  Depois de modificar o valor, clique em **Publicar** para publicar a versão de rascunho do runbook. 
5. Inicie o runbook **Export-RunAsCertificateToHybridWorker** e, na folha **Iniciar Runbook**, sob a opção **Configurações de execução**, selecione a opção **Hybrid Worker** e, na lista suspensa, selecione o grupo do Hybrid Worker criado anteriormente para esse cenário.  

    Esse procedimento exporta um certificado para o Hybrid Worker para que os runbooks no worker possam autenticar com o Azure usando a conexão Executar como a fim de gerenciar recursos do Azure (particularmente para esse cenário - importar runbooks para a conta de Automação).

4. Em sua conta de Automação, selecione o grupo do Hybrid Worker criado anteriormente e [especifique uma conta Executar como](automation-hrw-run-runbooks.md#runas-account) para o grupo de Hybrid Worker e escolha o ativo de credencial criado.  Isso garante que o runbook de sincronização possa executar comandos Git. 
5. Inicie o runbook **Sync-LocalGitFolderToAutomationAccount**, forneça os seguintes valores de parâmetro de entrada necessários e na folha **Iniciar Runbook**, sob a opção **Configurações de execução**, selecione a opção **Hybrid Worker** e, na lista suspensa, selecione o grupo do Hybrid Worker criado anteriormente para esse cenário:
    * *ResourceGroup* ‑ o nome do grupo de recursos associado à conta de Automação
    * *AutomationAccountName* - o nome de sua conta de Automação
    * *GitPath* - a pasta ou arquivo local no Hybrid Runbook Worker no qual o Git está configurado para buscar as alterações mais recentes

    Isso sincronizará a pasta local do Git no computador do Hybrid Worker e importará os arquivos .ps1 do diretório de origem para a conta de Automação.

    ![Inicie Sync-LocalGitFolderToAutomationAccount Runbook](media/automation-scenario-source-control-integration-with-github-ent/start-runbook-synclocalgitfoldertoautoacct.png)<br>

7. Exiba detalhes de resumo do trabalho para o runbook selecionando-o na folha **Runbooks** de sua conta de Automação e depois selecione o bloco **Trabalhos**.  Confirme a conclusão bem-sucedida selecionando o bloco **Todos os logs** e examinando o fluxo de logs detalhado.  

## <a name="next-steps"></a>Próximas etapas

-  Para saber mais sobre os tipos de runbook, suas vantagens e limitações, veja [Tipos de runbook da Automação do Azure](automation-runbook-types.md)
-  Para saber mais sobre o recurso de suporte de script do PowerShell, veja [Native PowerShell script support in Azure Automation (Suporte a scripts nativos do PowerShell na Automação do Azure)](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
