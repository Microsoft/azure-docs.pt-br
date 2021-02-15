---
title: Usar a integração do controle do código-fonte na Automação do Azure
description: Este artigo informa como sincronizar o controle do código-fonte da Automação do Azure com outros repositórios.
services: automation
ms.subservice: process-automation
ms.date: 11/12/2020
ms.topic: conceptual
ms.openlocfilehash: e7a6b6d3e753352820cdcb910dcbfa9362793493
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050763"
---
# <a name="use-source-control-integration"></a>Usar a integração de controle do código-fonte

 A integração de controle do código-fonte na Automação do Azure é compatível com a sincronização unidirecional do repositório do controle do código-fonte. O controle do código-fonte permite que você mantenha os runbooks da sua conta de Automação do Azure atualizados com scripts no repositório de controle do código-fonte do GitHub ou Azure Repos. Esse recurso facilita a promoção de código que foi testado em seu ambiente de desenvolvimento para sua conta de Automação de produção.

 A integração do controle do código-fonte permite que você colabore com facilidade com sua equipe, controle alterações e reverta para versões anteriores dos seus runbooks. Por exemplo, o controle do código-fonte permite sincronizar diferentes branches no controle do código-fonte com suas contas de desenvolvimento, teste ou produção de Automação do Azure.

## <a name="source-control-types"></a>Tipos de controle do código-fonte

A Automação do Azure é compatível com três tipos de controle do código-fonte:

* GitHub
* Azure Repos (Git)
* Azure Repos (TFVC)

## <a name="prerequisites"></a>Pré-requisitos

* Um repositório de controle do código-fonte (GitHub ou Azure Repos)
* Uma [conta Executar como](automation-security-overview.md#run-as-accounts)
* Os [módulos mais recentes do Azure](automation-update-azure-modules.md) na sua conta de Automação do Azure, incluindo o módulo `Az.Accounts` (módulo Az equivalente do `AzureRM.Profile`)

> [!NOTE]
> Os trabalhos de sincronização de controle do código-fonte são executados na conta de Automação do usuário e são cobrados seguindo mesma taxa que os outros trabalhos de Automação do Azure.

## <a name="configure-source-control"></a>Configurar o controle de origem

Esta seção informa como configurar o controle do código-fonte em sua conta de Automação do Azure. Você pode usar o portal do Azure ou o PowerShell.

### <a name="configure-source-control-in-azure-portal"></a>Configurar controle do código-fonte no portal do Azure

Use este procedimento para configurar o controle do código-fonte usando o portal do Azure.

1. Na sua conta de Automação do Azure, selecione **Controle do código-fonte** e clique em **Adicionar**.

    ![Selecionar controle do código-fonte](./media/source-control-integration/select-source-control.png)

2. Escolha **Tipo de controle do código-fonte** e clique em **Autenticar**.

3. Uma janela do navegador é aberta e solicita suas credenciais de acesso. Siga as instruções para concluir a autenticação.

4. Na página Resumo do controle do código-fonte, use os campos para preencher as propriedades de controle do código-fonte definidas abaixo. Clique em **Salvar** ao terminar.

    |Propriedade  |Descrição  |
    |---------|---------|
    |Nome do controle do código-fonte     | Um nome amigável para o controle do código-fonte. O nome deve conter apenas letras e números.        |
    |Tipo de controle do código-fonte     | O tipo do mecanismo de controle do código-fonte. As opções disponíveis são:</br> * GitHub</br>* Azure Repos (Git)</br> * Azure Repos (TFVC)        |
    |Repositório     | Nome do repositório ou projeto. Os primeiros 200 repositórios são recuperados. Para pesquisar um repositório, digite o nome no campo e clique em **Pesquisar no GitHub**.|
    |Branch     | Branch do qual os arquivos de origem são extraídos. O direcionamento de branch não está disponível para o tipo de controle do código-fonte TFVC.          |
    |Caminho da pasta     | Pasta que contém os runbooks a serem sincronizados; por exemplo, **/Runbooks**. Somente runbooks na pasta especificada são sincronizados. Não há suporte para recursão.        |
    |Sincronização automática<sup>1</sup>     | Opção que ativa ou desativa a sincronização automática quando uma confirmação é feita no repositório de controle do código-fonte.        |
    |Publicar runbook     | Opção definida como ativada (On) se os runbooks são publicados automaticamente após a sincronização do controle do código-fonte. Caso contrário, é definida como desativada (Off).           |
    |Descrição     | Texto que especifica detalhes adicionais sobre o controle do código-fonte.        |

    <sup>1</sup> Para habilitar a sincronização automática ao configurar a integração do controle do código-fonte com o Azure Repos, você deve ser um administrador de projeto.

   ![Resumo do controle do código-fonte](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> As credenciais de acesso do seu repositório do controle do código-fonte podem ser diferentes das suas credenciais de acesso do portal do Azure. Verifique se você está conectado com a conta correta do seu repositório de controle do código-fonte ao configurar o controle do código-fonte. Se estiver em dúvida, abra uma nova guia no navegador, saia de **dev.azure.com**, **visualstudio.com** ou **github.com** e tente se reconectar ao controle do código-fonte.

### <a name="configure-source-control-in-powershell"></a>Configurar controle do código-fonte no PowerShell

Você também pode usar o PowerShell para configurar o controle do código-fonte na Automação do Azure. Para usar os cmdlets do PowerShell nesta operação, você precisa de um PAT (token de acesso pessoal). Use o cmdlet [New-AzAutomationSourceControl](/powershell/module/az.automation/new-azautomationsourcecontrol) para criar a conexão de controle do código-fonte. Este cmdlet requer uma cadeia de caracteres segura para o PAT. Para saber como criar uma cadeia de caracteres segura, confira [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring).

As subseções a seguir ilustram a criação do PowerShell da conexão de controle do código-fonte para GitHub, Azure Repos (Git) e Azure Repos (TFVC).

#### <a name="create-source-control-connection-for-github"></a>Criar conexão de controle do código-fonte para GitHub

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>Criar conexão de controle do código-fonte para Azure Repos (Git)

> [!NOTE]
> O Azure Repos (Git) usa uma URL que acessa **dev.azure.com** no lugar de **visualstudio.com**, usado nos formatos anteriores. O formato mais antigo da URL `https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname>` foi preterido, mas ainda é compatível. O novo formato é preferencial.


```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>Criar conexão de controle do código-fonte para Azure Repos (TFVC)

> [!NOTE]
> O Azure Repos (TFVC) usa uma URL que acessa **dev.azure.com** no lugar de **visualstudio.com**, usado nos formatos anteriores. O formato mais antigo da URL `https://<accountname>.visualstudio.com/<projectname>/_versionControl` foi preterido, mas ainda é compatível. O novo formato é preferencial.

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>Permissões do token de acesso pessoal (PAT)

O controle do código-fonte requer algumas permissões mínimas para PATs. As sub-seções a seguir contêm as permissões mínimas necessárias para o GitHub e o Azure Repos.

##### <a name="minimum-pat-permissions-for-github"></a>Permissões mínimas do PAT para GitHub

A tabela a seguir contêm as permissões mínimas necessárias do PAT para o GitHub. Para obter mais informações sobre como criar um PAT no GitHub, consulte [criar um token de acesso pessoal para a linha de comando](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Escopo  |Descrição  |
|---------|---------|
|**`repo`**     |         |
|`repo:status`     | Acessar status de confirmação         |
|`repo_deployment`      | Acessar status de implantação         |
|`public_repo`     | Repositórios públicos de acesso         |
|`repo:invite` | Convites de repositório de acesso |
|`security_events` | Ler e gravar eventos de segurança |
|**`admin:repo_hook`**     |         |
|`write:repo_hook`     | Escrever ganchos de repositório         |
|`read:repo_hook`|Ler ganchos de repositório|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Permissões mínimas do PAT para Azure Repos

A lista a seguir contêm as permissões mínimas necessárias do PAT para o Azure Repos. Para obter mais informações sobre como criar um PAT no Azure Repos, confira [Autenticar o acesso com tokens de acesso pessoal](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

| Escopo  |  Tipo de acesso  |
|---------| ----------|
| `Code`      | Ler  |
| `Project and team` | Ler |
| `Identity` | Ler     |
| `User profile` | Ler     |
| `Work items` | Ler    |
| `Service connections` | Ler, consulta e gerenciar<sup>1</sup>    |

<sup>1</sup> A permissão `Service connections` só será necessário se você tiver a sincronização automática habilitada.

## <a name="synchronize-with-source-control"></a>Sincronizar com controle do código-fonte

Siga estas etapas para sincronizar com o controle do código-fonte.

1. Selecione o código-fonte da tabela da página de controle do código-fonte.

2. Clique em **Iniciar sincronização** para iniciar o processo de sincronização.

3. Exiba o status do trabalho de sincronização atual ou anteriores clicando na guia **Trabalhos de sincronização**.

4. No menu suspenso **Controle do código-fonte**, selecione um mecanismo de controle do código-fonte.

    ![Status da sincronização](./media/source-control-integration/sync-status.png)

5. Clicar em um trabalho permite exibir a saída do trabalho. O exemplo a seguir é a saída de um trabalho de sincronização do controle do código-fonte.

    ```output
    ===================================================================

    Azure Automation Source Control.
    Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

    Setting AzEnvironment.

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

    ==================================================================

    ```

6. O registro em log adicional está disponível selecionando **Todos os logs** na página Resumo do trabalho de sincronização de controle do código-fonte. Essas entradas adicionais no podem ajudá-lo a solucionar problemas que venham a surgir durante o uso do controle do código-fonte.

## <a name="disconnect-source-control"></a>Desconectar controle do código-fonte

Para se desconectar de um repositório de controle do código-fonte:

1. Abra **Controle do código-fonte** em **Configurações da Conta** da sua conta de Automação do Azure.

2. Selecione o mecanismo de controle do código-fonte que você deseja remover.

3. Na página de resumo do controle do código-fonte, clique em **Excluir**.

## <a name="handle-encoding-issues"></a>Tratar problemas de codificação

Se várias pessoas estiverem editando runbooks em seu repositório de controle do código-fonte usando diferentes editores, poderão ocorrer problemas de codificação. Para saber mais sobre essa situação, confira [Causas comuns de problemas de codificação](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues).

## <a name="update-the-pat"></a>Atualizar o PAT

No momento, você não pode usar o portal do Azure para atualizar o PAT no controle do código-fonte. Quando seu PAT vencer ou for revogado, você poderá atualizar o controle do código-fonte com um novo token de acesso de uma das seguintes maneiras:

* Usar a [REST API](/rest/api/automation/sourcecontrol/update).
* Usar o cmdlet [Update-AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol).

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre integração do controle do código-fonte no Automação do Azure, veja [Automação do Azure: Integração do Controle do Código-Fonte na Automação do Azure](https://azure.microsoft.com/blog/azure-automation-source-control-13/).  
* Para integrar o controle de origem do runbook com o Visual Studio Codespaces, confira [automação do Azure: integrando o controle de origem do runbook usando o Visual Studio Codespaces](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/).
