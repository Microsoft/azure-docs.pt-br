---
title: Implantação por meio do repositório Git local
description: Saiba como habilitar a implantação do Git local no Serviço de Aplicativo do Azure. Uma das maneiras mais simples de implantar o código de seu computador local.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 02/16/2021
ms.reviewer: dariac
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 5dd6183bf88c167adb2f084c319cd90b94351dfb
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560456"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Implantação do Git local no Serviço de Aplicativo do Azure

Este guia de instruções mostra como implantar seu aplicativo no [serviço Azure app](overview.md) de um repositório git em seu computador local.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir as etapas neste guia de instruções:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Instalar o Git](https://www.git-scm.com/downloads).

- Ter um repositório git local com código que você deseja implantar. Para baixar um repositório de exemplo, execute o seguinte comando na janela do seu terminal local:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-a-deployment-user"></a>Configurar um usuário de implantação

Consulte [configurar credenciais de implantação para o serviço de Azure app](deploy-configure-credentials.md). Você pode usar credenciais de escopo de usuário ou credenciais de escopo de aplicativo.

## <a name="create-a-git-enabled-app"></a>Criar um aplicativo habilitado para git

Se você já tiver um aplicativo do serviço de aplicativo e quiser configurar a implantação do git local para ele, consulte [configurar um aplicativo existente](#configure-an-existing-app) em vez disso.

# <a name="azure-cli"></a>[CLI do Azure](#tab/cli)

Execute [`az webapp create`](/cli/azure/webapp#az_webapp_create) com a `--deployment-local-git` opção. Por exemplo:

```azurecli-interactive
az webapp create --resource-group <group-name> --plan <plan-name> --name <app-name> --runtime "<runtime-flag>" --deployment-local-git
```

A saída contém uma URL como: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` . Use essa URL para implantar seu aplicativo na próxima etapa.

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/powershell)

Execute [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) da raiz do seu repositório git. Por exemplo:

```azurepowershell-interactive
New-AzWebApp -Name <app-name>
```

Quando você executar esse cmdlet de um diretório que é um repositório git, ele criará automaticamente um git remoto para seu aplicativo do serviço de aplicativo para você, chamado `azure` .

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

No portal, você precisa criar um aplicativo primeiro e, em seguida, configurar a implantação para ele. Consulte [configurar um aplicativo existente](#configure-an-existing-app).

-----

## <a name="configure-an-existing-app"></a>Configurar um aplicativo existente

Se você ainda não criou um aplicativo, consulte [criar um aplicativo habilitado para git](#create-a-git-enabled-app) em vez disso.

# <a name="azure-cli"></a>[CLI do Azure](#tab/cli)

Executar [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-local-git) . Por exemplo:

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

A saída contém uma URL como: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` . Use essa URL para implantar seu aplicativo na próxima etapa.

> [!TIP]
> Essa URL contém o nome de usuário de implantação de escopo de usuários. Se desejar, você pode [usar as credenciais de escopo do aplicativo](deploy-configure-credentials.md#appscope) . 

# <a name="azure-powershell"></a>[PowerShell do Azure](#tab/powershell)

Defina o `scmType` do seu aplicativo executando o cmdlet [set-AzResource](/powershell/module/az.resources/set-azresource) .

```powershell-interactive
$PropertiesObject = @{
    scmType = "LocalGit";
}

Set-AzResource -PropertyObject $PropertiesObject -ResourceGroupName <group-name> `
-ResourceType Microsoft.Web/sites/config -ResourceName <app-name>/web `
-ApiVersion 2015-08-01 -Force
```

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

1. Na [portal do Azure](https://portal.azure.com), navegue até a página de gerenciamento do aplicativo.

1. No menu à esquerda, selecione configurações da **central de implantação**  >  . Selecione **git local** na **origem** e, em seguida, clique em **salvar**.

    ![Mostra como habilitar a implantação do git local para o serviço de aplicativo no portal do Azure](./media/deploy-local-git/enable-portal.png)

1. Na seção git local, copie o **URI de git clone** para mais tarde. Esse URI não contém nenhuma credencial.

-----

## <a name="deploy-the-web-app"></a>Implantar o aplicativo Web

1. Em uma janela de terminal local, altere o diretório para a raiz do repositório git e adicione um git remoto usando a URL obtida do seu aplicativo. Se o método escolhido não fornecer uma URL, use `https://<app-name>.scm.azurewebsites.net/<app-name>.git` com o nome do aplicativo no `<app-name>` .
   
   ```bash
   git remote add azure <url>
   ```

    > [!NOTE]
    > Se você [criou um aplicativo habilitado para git no PowerShell usando New-AzWebApp](#create-a-git-enabled-app), o remoto já será criado para você.
   
1. Envie por push para o Azure remoto com `git push azure master` . 
   
1. Na janela **Gerenciador de credenciais do git** , insira suas credenciais de escopo de [usuário ou de escopo de aplicativo](#configure-a-deployment-user), não suas credenciais de logon do Azure.

    Se a URL remota do git já contiver o nome de usuário e a senha, você não será solicitado. 
   
1. Examine a saída. Você pode ver a automação específica do tempo de execução, como o MSBuild para ASP.NET, `npm install` para Node.js e `pip install` para Python. 
   
1. Navegue até seu aplicativo no portal do Azure para verificar se o conteúdo está implantado.

## <a name="troubleshoot-deployment"></a>Solucionar problemas de implantação

Você poderá ver as seguintes mensagens de erro comuns ao usar o Git para publicar em um aplicativo do serviço de aplicativo no Azure:

|Mensagem|Causa|Resolução
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|O aplicativo não está em funcionamento.|inicie o aplicativo no portal do Azure. A implantação do git não está disponível quando o aplicativo Web é interrompido.|
|`Couldn't resolve host 'hostname'`|As informações de endereço para o remoto ' Azure ' estão incorretas.|use o comando `git remote -v` para listar todos os remotes (remotos), juntamente com a URL associada. Verifique se a URL do remote do 'azure' está correta. Se necessário, remova e recrie esse remoto usando a URL correta.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'main'.`|Você não especificou uma ramificação durante `git push` ou não definiu o `push.default` valor em `.gitconfig` .|Execute `git push` novamente, especificando o Branch principal: `git push azure main` .|
|`src refspec [branchname] does not match any.`|Você tentou enviar por push para um Branch que não seja o principal no remoto ' Azure '.|Execute `git push` novamente, especificando o Branch principal: `git push azure main` .|
|`RPC failed; result=22, HTTP code = 5xx.`|esse erro poderá ocorrer se você tentar efetuar push de um repositório Git de grande porte por HTTPS.|Altere a configuração do git no computador local para torná-la `postBuffer` maior. Por exemplo: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|Você implantou um aplicativo Node.js com um _package.jsno_ arquivo que especifica módulos adicionais necessários.|Examine as `npm ERR!` mensagens de erro antes desse erro para obter mais contexto sobre a falha. Veja a seguir as causas conhecidas desse erro e as `npm ERR!` mensagens correspondentes:<br /><br />**package.jsmalformados no arquivo**: `npm ERR! Couldn't read dependencies.`<br /><br />**O módulo nativo não tem uma distribuição binária para o Windows**:<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />ou <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>Recursos adicionais

- [Servidor de Build do serviço de aplicativo (documentação do Project kudu)](https://github.com/projectkudu/kudu/wiki)
- [Implantação contínua no Serviço de Aplicativo do Azure](deploy-continuous-deployment.md)
- [Exemplo: criar um aplicativo Web e implantar o código de um repositório git local (CLI do Azure)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Exemplo: criar um aplicativo Web e implantar o código de um repositório git local (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
