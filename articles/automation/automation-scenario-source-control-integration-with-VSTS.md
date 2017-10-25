---
title: "Integrar a automação do Azure com o controle do código-fonte do Visual Studio Team Services | Microsoft Docs"
description: "O cenário guia você pela configuração de integração com uma conta de Automação do Azure e o controle do código-fonte do Visual Studio Team Services."
services: automation
documentationcenter: 
author: eamono
manager: 
editor: 
keywords: "Azure PowerShell, VSTS, controle do código-fonte, automação"
ms.assetid: a43b395a-e740-41a3-ae62-40eac9d0ec00
ms.service: automation
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.openlocfilehash: 01f9c01c9e04e02dbb548b68cf99684ba6ddd57e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-scenario---automation-source-control-integration-with-visual-studio-team-services"></a>Cenário da Automação do Azure – Integração de controle do código-fonte da Automação com o Visual Studio Team Services

Nesse cenário, você tem um projeto do Visual Studio Team Services que você está usando para gerenciar runbooks da Automação do Azure ou configurações do DSC sob o controle do código-fonte.
Este artigo descreve como integrar o VSTS em seu ambiente de Automação do Azure para que ocorra a integração contínua ocorra para cada check-in.

## <a name="getting-the-scenario"></a>Obtendo o cenário

Este cenário é composto por dois runbooks do PowerShell que você pode importar diretamente da [Galeria de Runbooks](automation-runbook-gallery.md) no Portal do Azure, ou baixar da [Galeria do PowerShell](https://www.powershellgallery.com).

### <a name="runbooks"></a>Runbooks

Runbook | Descrição| 
--------|------------|
Sync-VSTS | Importar runbooks ou configurações do controle do código-fonte do VSTS quando é feito um check-in. Se executado manualmente, ele importará e publicará todos os runbooks ou configurações para a conta de Automação.| 
Sync-VSTSGit | Importar runbooks ou configurações do controle do código-fonte do VSTS sob o Git quando é feito um check-in. Se executado manualmente, ele importará e publicará todos os runbooks ou configurações para a conta de Automação.|

### <a name="variables"></a>variáveis

Variável | Descrição|
-----------|------------|
VSToken | Ativo de variável segura que você criará e que contém o token de acesso pessoal do VSTS. Você pode aprender como criar um token de acesso pessoal do VSTS na [página de autenticação do VSTS](https://www.visualstudio.com/en-us/docs/integrate/get-started/auth/overview). 
## <a name="installing-and-configuring-this-scenario"></a>Instalando e configurando esse cenário

Criar um [token de acesso pessoal](https://www.visualstudio.com/en-us/docs/integrate/get-started/auth/overview) no VSTS que será usado para sincronizar runbooks ou configurações em sua conta de automação.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPersonalToken.png) 

Criar uma [variável segura](automation-variables.md) na sua conta de automação para armazenar o token de acesso pessoal para que o runbook possa autenticar o VSTS e sincronizar os runbooks ou configurações na conta de Automação. Você pode nomear esse VSToken. 

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSTokenVariable.png)

Importe o runbook que sincronizará seus runbooks ou configurações para a conta de Automação. Você pode usar o [runbook de exemplo do VSTS](https://www.powershellgallery.com/packages/Sync-VSTS/1.0/DisplayScript) ou o [runbook de exemplo do VSTS com Git] (https://www.powershellgallery.com/packages/Sync-VSTSGit/1.0/DisplayScript) from the PowerShellGallery.com dependendo de você usar o controle do código-fonte do VSTS ou VSTS com Git e implantar a sua conta de automação.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPowerShellGallery.png)

Agora você pode [publicar](automation-creating-importing-runbook.md#publishing-a-runbook) esse runbook para que você possa criar um webhook. 
![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPublishRunbook.png)

Criar um [webhook](automation-webhooks.md) para este runbook Sync-VSTS e preencha os parâmetros conforme mostrado abaixo. Certifique-se de copiar a URL do webhook, pois você precisará dela para um gancho de serviço no VSTS. O VSAccessTokenVariableName é o nome (VSToken) da variável segura que você criou anteriormente para conter o token de acesso pessoal. 

A integração com o VSTS (Sync-VSTS.ps1) utilizará os parâmetros a seguir.
### <a name="sync-vsts-parameters"></a>Parâmetros do Sync-VSTS

Parâmetro | Descrição| 
--------|------------|
WebhookData | Isso conterá as informações de check-in enviadas do gancho de serviço do VSTS. Você deve deixar esse parâmetro em branco.| 
ResourceGroup | Esse é o nome do grupo de recursos em que a conta de automação está contida.|
AutomationAccountName | O nome da conta de automação que será sincronizada com o VSTS.|
VSFolder | O nome da pasta do VSTS em que os runbooks e as configurações existem.|
VSAccount | O nome da conta do Visual Studio Team Services.| 
VSAccessTokenVariableName | O nome da variável segura (VSToken) que contém o token de acesso pessoal do VSTS.| 


![](media/automation-scenario-source-control-integration-with-VSTS/VSTSWebhook.png)

Se você estiver usando o VSTS com GIT (Sync-VSTSGit.ps1), ele utilizará os parâmetros a seguir.

Parâmetro | Descrição|
--------|------------|
WebhookData | Isso conterá as informações de check-in enviadas do gancho de serviço do VSTS. Você deve deixar esse parâmetro em branco.| ResourceGroup | Esse é o nome do grupo de recursos em que a conta de automação está contida.|
AutomationAccountName | O nome da conta de automação que será sincronizada com o VSTS.|
VSAccount | O nome da conta do Visual Studio Team Services.|
VSProject | O nome do projeto no VSTS em que os runbooks e as configurações existem.|
GitRepo | O nome do repositório Git.|
GitBranch | O nome da ramificação no repositório Git do VSTS.|
Pasta | O nome da pasta na ramificação Git do VSTS.|
VSAccessTokenVariableName | O nome da variável segura (VSToken) que contém o token de acesso pessoal do VSTS.|

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSGitWebhook.png)

Crie um gancho de serviço no VSTS para check-ins para a pasta que dispara esse webhook no check-in de código. Selecione WebHooks como o serviço com o qual se integrar ao criar uma nova assinatura. Você pode aprender mais sobre os ganchos de serviço na [documentação de ganchos de serviço do VSTS](https://www.visualstudio.com/en-us/docs/marketplace/integrate/service-hooks/get-started).
![](media/automation-scenario-source-control-integration-with-VSTS/VSTSServiceHook.png)

Agora você deve ser capaz de fazer todos os check-ins de runbooks e configurações no VSTS e sincronizá-los automaticamente em sua conta de automação.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSSyncRunbookOutput.png)

Se você executar esse runbook manualmente sem ser disparado pelo VSTS, você poderá deixar o parâmetro webhookdata vazio e ele fará uma sincronização completa da pasta do VSTS especificada.

Se você deseja desinstalar o cenário, remova o gancho de serviço do VSTS, exclua o runbook e a variável VSToken.
