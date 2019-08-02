---
title: Criar um aplicativo PHP no Linux – Serviço de Aplicativo do Azure | Microsoft Docs
description: Implante seu primeiro Olá, Mundo PHP no Serviço de Aplicativo no Linux em minutos.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 7976dcafe8bb3f787465e72b6a61bbc48daf4086
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849546"
---
# <a name="create-a-php-app-in-app-service-on-linux"></a>Criar um aplicativo PHP no Serviço de Aplicativo no Linux

> [!NOTE]
> Este artigo implanta um aplicativo no Serviço de Aplicativo no Linux. Para implantar o Serviço de Aplicativo no _Windows_, confira [Criar um aplicativo PHP no Azure](../app-service-web-get-started-php.md).
>

O [Serviço de Aplicativo no Linux](app-service-linux-intro.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches usando o sistema operacional Linux. Este tutorial de início rápido mostra como implantar um aplicativo PHP no Serviço de Aplicativo do Azure no Linux usando o [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

![Aplicativo de exemplo em execução no Azure](media/quickstart-php/hello-world-in-browser.png)

Você pode seguir as etapas deste artigo usando um computador Mac, Windows ou Linux.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

* <a href="https://git-scm.com/" target="_blank">Instalar o Git</a>
* <a href="https://php.net" target="_blank">Instalar o PHP</a>

## <a name="download-the-sample"></a>Baixar o exemplo

Em uma janela de terminal, execute os comandos a seguir para clonar o aplicativo de exemplo no computador local e navegue para o diretório que contém o código de exemplo.

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

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux.md)]

## <a name="create-a-web-app"></a>Criar um aplicativo Web

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-php-linux-no-h.md)] 

Navegue até o site para ver o aplicativo recém-criado com imagem interna. Substitua _&lt;nome do aplicativo>_ pelo nome de seu aplicativo.

```bash
http://<app_name>.azurewebsites.net
```

Seu novo aplicativo deve ficar assim:

![Página de aplicativo vazia](media/quickstart-php/app-service-web-service-created.png)

[!INCLUDE [Push to Azure](../../../includes/app-service-web-git-push-to-azure.md)] 

```bash
Counting objects: 2, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (2/2), 352 bytes | 0 bytes/s, done.
Total 2 (delta 1), reused 0 (delta 0)
remote: Updating branch 'master'.
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
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
   cc39b1e..25f1805  master -> master
```

## <a name="browse-to-the-app"></a>Navegar até o aplicativo

Navegue até o aplicativo implantado usando o navegador da Web.

```bash
http://<app_name>.azurewebsites.net
```

O código de exemplo PHP está em execução no Serviço de Aplicativo no Linux com imagem interna.

![Aplicativo de exemplo em execução no Azure](media/quickstart-php/hello-world-in-browser.png)

**Parabéns!** Você implantou seu primeiro aplicativo PHP no Serviço de Aplicativo no Linux.

## <a name="update-locally-and-redeploy-the-code"></a>Atualizar localmente e reimplantar o código

No diretório local, abra o arquivo `index.php` no aplicativo do PHP e faça uma pequena alteração no texto dentro da cadeia de caracteres para `echo`:

```php
echo "Hello Azure!";
```

Confirme suas alterações no Git e, em seguida, envie as alterações de código por push para o Azure.

```bash
git commit -am "updated output"
git push azure master
```

Depois que a implantação for concluída, troque para a janela do navegador aberta na etapa **Navegar até o aplicativo** e atualize a página.

![Aplicativo de exemplo atualizado em execução no Azure](media/quickstart-php/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Gerenciar seu novo aplicativo do Azure

Acesse o <a href="https://portal.azure.com" target="_blank">portal do Azure</a> para gerenciar o aplicativo que você criou.

No menu à esquerda, clique em **Serviços de Aplicativos** e, em seguida, clique no nome do aplicativo do Azure.

![Navegação no Portal para o aplicativo do Azure](./media/quickstart-php/php-docs-hello-world-app-service-list.png)

Você verá a página Visão geral do aplicativo. Aqui você pode executar tarefas básicas de gerenciamento como procurar, parar, iniciar, reiniciar e excluir.

![Página Serviço de Aplicativo no portal do Azure](media/quickstart-php/php-docs-hello-world-app-service-detail.png)

O menu à esquerda fornece páginas diferentes para configurar seu aplicativo. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Aplicativo PHP com o MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Configurar o aplicativo PHP](configure-language-php.md)
