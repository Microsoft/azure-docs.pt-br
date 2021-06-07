---
title: 'Início Rápido: Criar um aplicativo Web PHP'
description: Implante seu primeiro Olá, Mundo em PHP no Serviço de Aplicativo do Azure em minutos. Você fará a implantação usando o Git, que é um dos vários modos de implantação no Serviço de Aplicativo.
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.topic: quickstart
ms.date: 08/01/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: c04e1b33233abebde746fd7fb1ef8d761dba7e4e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788197"
---
# <a name="create-a-php-web-app-in-azure-app-service"></a>Criar um aplicativo Web PHP no Serviço de Aplicativo do Azure

::: zone pivot="platform-windows"  
O [Serviço de Aplicativo do Azure](overview.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches.  Este tutorial de início rápido mostra como implantar um aplicativo PHP no Serviço de Aplicativo do Azure no Windows.
::: zone-end  

::: zone pivot="platform-linux"
O [Serviço de Aplicativo do Azure](overview.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches.  Este tutorial de guia de início rápido mostra como implantar um aplicativo PHP no Serviço de Aplicativo do Azure no Linux.
::: zone-end  

Crie o aplicativo Web usando a [CLI do Azure](/cli/azure/get-started-with-azure-cli) no Cloud Shell e use o Git para implantar o código PHP de exemplo para o aplicativo Web.

![Aplicativo de exemplo em execução no Azure](media/quickstart-php/hello-world-in-browser.png)

Você pode seguir as etapas aqui usando um computador Mac, Windows ou Linux. A conclusão das etapas demora cerca de cinco minutos assim que os pré-requisitos são instalados.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

* <a href="https://git-scm.com/" target="_blank">Instalar o Git</a>
* <a href="https://php.net/manual/install.php" target="_blank">Instalar o PHP</a>

## <a name="download-the-sample-locally"></a>Baixar o exemplo localmente

Em uma janela de terminal, execute os comandos a seguir. Isso clonará o aplicativo de exemplo no computador local e fará com que você navegue até o diretório que contém o código de exemplo. 

```bash
git clone https://github.com/Azure-Samples/php-docs-hello-world
cd php-docs-hello-world
```

## <a name="run-the-app-locally"></a>Executar o aplicativo localmente

Execute o aplicativo no local para ver como ele deve ficar quando o implantar no Azure. Abra uma janela do terminal e use o comando `php` para iniciar o servidor Web do PHP interno.

```bash
php -S localhost:8080
```

Abra um navegador da Web e navegue até o aplicativo de exemplo em `http://localhost:8080`.

Você verá o **Olá, Mundo!** mensagem do aplicativo de exemplo exibida na página.

![Aplicativo de exemplo em execução local](media/quickstart-php/localhost-hello-world-in-browser.png)

Na janela do terminal, pressione **Ctrl+C** para sair do servidor Web.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

::: zone pivot="platform-windows"  
[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]
::: zone-end  

::: zone pivot="platform-linux"
[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux.md)]
::: zone-end

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan.md)]

## <a name="create-a-web-app"></a>Criar um aplicativo Web

No Cloud Shell, crie um aplicativo Web no plano do Serviço de Aplicativo do `myAppServicePlan` com o comando [`az webapp create`](/cli/azure/webapp#az_webapp_create). 

No exemplo a seguir, substitua `<app-name>` por um nome do aplicativo exclusivo globalmente (os caracteres válidos são `a-z`, `0-9` e `-`). A execução é predefinida para `PHP|7.4`. Para ver todos os runtimes com suporte, execute [`az webapp list-runtimes`](/cli/azure/webapp#az_webapp_list_runtimes). 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.4" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.4" --deployment-local-git
```

> [!NOTE]
> O símbolo de análise de parada `(--%)`, introduzido no PowerShell 3.0, direciona o PowerShell para impedir a entrada de interpretação como comandos ou expressões do PowerShell.
>

Quando o aplicativo Web for criado, a CLI do Azure mostrará um resultado semelhante ao seguinte exemplo:

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app-name&gt;.azurewebsites.net",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

Você criou um novo aplicativo Web vazio com a implantação do Git habilitada.

> [!NOTE]
> A URL do Git remoto é mostrada na propriedade `deploymentLocalGitUrl` com o formato `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Salve essa URL, pois você precisará dela mais tarde.
>

Navegue até o aplicativo Web recém-criado. Substitua _&lt;app-name>_ pelo nome do aplicativo exclusivo criado na etapa anterior.

```bash
http://<app-name>.azurewebsites.net
```

Seu novo aplicativo Web deve ficar assim:

![Página de aplicativo Web vazia](media/quickstart-php/app-service-web-service-created.png)

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure.md)] 

<pre>
Counting objects: 2, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (2/2), 352 bytes | 0 bytes/s, done.
Total 2 (delta 1), reused 0 (delta 0)
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id '25f18051e9'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Copying file: 'index.php'
remote: Ignoring: .git
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
   cc39b1e..25f1805  main -> main
</pre>

## <a name="browse-to-the-app"></a>Navegar até o aplicativo

Navegue até o aplicativo implantado usando o navegador da Web.

```
http://<app-name>.azurewebsites.net
```

O código de exemplo do PHP está em execução em um aplicativo Web do Serviço de Aplicativo do Azure.

![Aplicativo de exemplo em execução no Azure](media/quickstart-php/hello-world-in-browser.png)

**Parabéns!** Você implantou seu primeiro aplicativo PHP no Serviço de Aplicativo.

## <a name="update-locally-and-redeploy-the-code"></a>Atualizar localmente e reimplantar o código

Usando um editor de texto local, abra o arquivo `index.php` no aplicativo do PHP e faça uma pequena alteração no texto dentro da cadeia de caracteres para `echo`:

```php
echo "Hello Azure!";
```

Na janela do terminal local, confirme suas alterações no Git e então envie por push as alterações do código para o Azure.

```bash
git commit -am "updated output"
git push azure main
```

Depois que a implantação for concluída, retorne para a janela do navegador que foi aberta durante a etapa **Navegar até o aplicativo** e atualize a página.

![Aplicativo de exemplo atualizado em execução no Azure](media/quickstart-php/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Gerenciar seu novo aplicativo do Azure

1. Vá para o <a href="https://portal.azure.com" target="_blank">portal do Azure</a> para gerenciar o aplicativo Web que você criou. Pesquise e selecione **Serviços de Aplicativos**.

    ![Pesquisar Serviços de Aplicativos, portal do Azure, criar aplicativo Web PHP](media/quickstart-php/navigate-to-app-services-in-the-azure-portal.png)

2. Selecione o nome do seu aplicativo do Azure.

    ![Navegação no Portal para o aplicativo do Azure](./media/quickstart-php/php-docs-hello-world-app-service-list.png)

    A página **Visão Geral** do seu aplicativo Web será exibida. Aqui você pode executar tarefas básicas de gerenciamento como **Procurar**, **Parar**, **Reiniciar** e **Excluir**.

    ![Página Serviço de Aplicativo no portal do Azure](media/quickstart-php/php-docs-hello-world-app-service-detail.png)

    O menu do aplicativo Web fornece opções diferentes para configurar seu aplicativo. 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [PHP com MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Configurar o aplicativo PHP](configure-language-php.md)
