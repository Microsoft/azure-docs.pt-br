---
title: Integração do controle do código-fonte na Automação do Azure
description: Este artigo descreve a integração de controle de origem com o GitHub na Automação do Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 81602f1a30fb753d7a8fcfccace581cd8c7b2f0c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60880302"
---
# <a name="source-control-integration-in-azure-automation"></a>Integração de controle de origem na Automação do Azure

Controle de origem permite que você mantenha seus runbooks em sua automação de conta são atualizados com seus scripts em seu repositório de controle de origem do GitHub ou repositórios do Azure. O controle de origem permite que você colabore com facilidade com sua equipe, controle alterações e reverta para versões anteriores de seus runbooks. Por exemplo, o controle do código-fonte permite sincronizar diferentes branches no controle do código-fonte com suas contas de Automação de desenvolvimento, teste ou produção. Isso facilita a promoção de código que foi testado em seu ambiente de desenvolvimento para sua conta de Automação de produção. Integração de controle do código-fonte com a automação dá suporte à sincronização de direção única do seu repositório de controle do código-fonte.

A automação do Azure dá suporte a três tipos de controle de origem:

* GitHub
* Repositórios do Azure (Git)
* Azure Repos (TFVC)

## <a name="pre-requisites"></a>Pré-requisitos

* Um repositório de controle do código-fonte (GitHub ou repositórios do Azure)
* Um [conta executar como](manage-runas-account.md)
* Verifique se você tem o [módulos do Azure mais recente](automation-update-azure-modules.md) em sua conta de automação

> [!NOTE]
> Trabalhos de sincronização de controle do código-fonte são executados na Conta de Automação do usuário e são cobrados conforme a mesma taxa que outros trabalhos de Automação.

## <a name="configure-source-control---azure-portal"></a>Configurar o controle do código-fonte - portal do Azure

Dentro de sua conta de automação, selecione **controle de origem** e clique em **+ adicionar**

![Selecionar controle do código-fonte](./media/source-control-integration/select-source-control.png)

Escolha **Tipo de controle do código-fonte** e clique em **Autenticar**. Uma janela do navegador é aberta e solicita que você entrar, siga os prompts para concluir a autenticação.

Na página **Resumo de controle do código-fonte**, preencha as informações e clique em **Salvar**. A tabela a seguir fornece uma descrição dos campos disponíveis.

|Propriedade  |DESCRIÇÃO  |
|---------|---------|
|Nome do controle do código-fonte     | Um nome amigável para o controle do código-fonte. *Esse nome deve conter apenas letras e números.*        |
|Tipo de controle do código-fonte     | O tipo de origem do controle do código-fonte. As opções disponíveis são:</br> GitHub</br>Repositórios do Azure (Git)</br> Azure Repos (TFVC)        |
|Repositório     | O nome do repositório ou projeto. Os repositórios de 200 primeiros são retornados. Para procurar por um repositório, digite o nome no campo e clique em **pesquisa no GitHub**.|
|Branch     | O branch do qual o pull dos arquivos de origem é efetuado. Direcionamento de ramificação não está disponível para o tipo de controle de origem TFVC.          |
|Caminho da pasta     | A pasta que contém os runbooks a serem sincronizados. Exemplo: /Runbooks </br>*Somente runbooks na pasta especificada são sincronizados. Não há suporte para recursão.*        |
|Sincronização automática     | Ativa ou desativa a sincronização automática quando uma confirmação é feita no repositório de controle do código-fonte         |
|Publicar runbook     | Se definido como **em**, depois de runbooks são sincronizados do controle de origem que serão publicados automaticamente.         |
|DESCRIÇÃO     | Um campo de texto para fornecer detalhes adicionais        |

![Resumo do controle do código-fonte](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Verifique se você está conectado com a conta correta ao configurar o controle do código-fonte. Se houver alguma dúvida, abra uma nova guia no navegador e faça logoff do visualstudio.com ou do github.com e tente conectar o controle do código-fonte novamente.

## <a name="configure-source-control---powershell"></a>Configurar o controle do código-fonte - PowerShell

Você também pode usar o PowerShell para configurar o controle de origem na automação do Azure. Para configurar o controle de origem com os cmdlets do PowerShell, um token de acesso pessoal (PAT) é necessária. Você usa o [New-AzureRmAutomationSourceControl](/powershell/module/AzureRM.Automation/New-AzureRmAutomationSourceControl) para criar a conexão de controle do código-fonte. O cmdlet requer uma cadeia de caracteres de Token de acesso pessoal, para aprender a criar uma cadeia de caracteres segura, consulte [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

### <a name="azure-repos-git"></a>Repositórios do Azure (Git)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="azure-repos-tfvc"></a>Azure Repos (TFVC)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="github"></a>GitHub

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="personal-access-token-permissions"></a>Permissões do token de acesso pessoal

O controle do código-fonte requer algumas permissões mínimas para tokens de acesso pessoal. As tabelas a seguir contêm as permissões mínimas necessárias para o GitHub e repositórios do Azure.

#### <a name="github"></a>GitHub

Para obter mais informações sobre como criar um token de acesso pessoal no GitHub, visite [criar um token de acesso pessoal para a linha de comando](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Escopo  |DESCRIÇÃO  |
|---------|---------|
|**repositório**     |         |
|repo:status     | Acessar status de confirmação         |
|repo_deployment      | Acessar status de implantação         |
|public_repo     | Repositórios públicos de acesso         |
|**admin:repo_hook**     |         |
|write:repo_hook     | Escrever ganchos de repositório         |
|read:repo_hook|Ler ganchos de repositório|

#### <a name="azure-repos"></a>Azure Repos

Para obter mais informações sobre como criar um token de acesso pessoal em repositórios do Azure, visite [autenticar o acesso com tokens de acesso pessoal](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

|Escopo  |
|---------|
|Código (leitura)     |
|Projeto e equipe (leitura)|
|Identidade (leitura)      |
|Perfil do usuário (leitura)     |
|Itens de trabalho (leitura)    |
|Conexões de serviço (ler, consultar e gerenciar)<sup>1</sup>    |

<sup>1</sup> permissão a conexões de serviço só é necessária se você tiver habilitado a sincronização automática.

## <a name="syncing"></a>Sincronização

Selecione a origem da tabela sobre o **controle de fonte de** página. Clique em **Iniciar sincronização** para iniciar o processo de sincronização.

É possível exibir o status do trabalho de sincronização atual ou anteriores clicando na guia **Trabalho de sincronização**. Na lista suspensa **Controle do código-fonte**, selecione um controle do código-fonte.

![Status da sincronização](./media/source-control-integration/sync-status.png)

Clicar em um trabalho permite exibir a saída do trabalho. O exemplo a seguir é a saída de um trabalho de sincronização do controle do código-fonte.

```output
========================================================================================================

Azure Automation Source Control.
Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

Setting AzureRmEnvironment.

Getting AzureRunAsConnection.

Logging in to Azure...

Source control information for syncing:

[Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

Connecting to VSTS...


Source Control Sync Summary:


2 files synced:
 - ExampleRunbook1.ps1
 - ExampleRunbook2.ps1



========================================================================================================
```

Log adicional está disponível selecionando **todos os Logs** sobre o **resumo do trabalho de sincronização de controle de origem** página. Essas entradas de log adicionais podem ajudar você a solucionar problemas que podem surgir ao usar o controle do código-fonte.

## <a name="disconnecting-source-control"></a>Desconectando o controle de origem

Para desconectar de um repositório de controle do código-fonte, abra **controle de origem** sob **configurações de conta** em sua conta de automação.

Selecione o controle do código-fonte que você deseja remover. Na página **Resumo do controle do código-fonte**, clique em **Excluir**.

## <a name="encoding"></a>Codificação

Se várias pessoas estão editando runbooks em seu repositório de controle do código-fonte com diferentes editores, há uma chance de ser executado em problemas de codificação. Essa situação pode levar a caracteres incorretos no seu runbook. Para saber mais sobre isso, consulte [causas comuns de problemas de codificação](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)

## <a name="next-steps"></a>Próximas etapas

Para aprender mais sobre os tipos de runbook, suas vantagens e limitações, veja [Tipos de runbook da Automação do Azure](automation-runbook-types.md)
