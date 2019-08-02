---
title: Criar um aplicativo Python no Linux – Serviço de Aplicativo do Azure | Microsoft Docs
description: Implante seu primeiro aplicativo Python Olá, Mundo no Serviço de Aplicativo do Azure no Linux em minutos.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: ea247907aebc241fb8f1b266ad55bc2fc983607f
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67853983"
---
# <a name="create-a-python-app-in-azure-app-service-on-linux-preview"></a>Criar um aplicativo Python no Serviço de Aplicativo do Azure no Linux (Versão Prévia)

O [Serviço de Aplicativo no Linux](app-service-linux-intro.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches usando o sistema operacional Linux. Este Início Rápido mostra como implantar um aplicativo do Python com base na imagem interna do Python (versão prévia) no Serviço de Aplicativo no Linux usando o [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

Você pode seguir as etapas deste artigo usando um computador Mac, Windows ou Linux.

![Aplicativo de exemplo em execução no Azure](media/quickstart-python/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Baixar o exemplo

No Cloud Shell, crie um diretório de início rápido e depois altere-o.

```bash
mkdir quickstart

cd quickstart
```

Em seguida, execute o comando a seguir para clonar o repositório de aplicativos de exemplo ao seu diretório de início rápido.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Na execução, ele exibe informações semelhantes ao seguinte exemplo:

```bash
Cloning into 'python-docs-hello-world'...
remote: Enumerating objects: 43, done.
remote: Total 43 (delta 0), reused 0 (delta 0), pack-reused 43
Unpacking objects: 100% (43/43), done.
Checking connectivity... done.
```

## <a name="create-a-web-app"></a>Criar um aplicativo Web

Altere para o diretório que contém o código de exemplo e execute o comando `az webapp up`.

No exemplo a seguir, substitua *\<app_name>* por um nome do aplicativo exclusivo globalmente (os caracteres válidos são `a-z`, `0-9` e `-`).

```bash
cd python-docs-hello-world

az webapp up -n <app_name>
```

Esse comando pode demorar um pouco para ser executado. Na execução, ele exibe informações semelhantes ao seguinte exemplo:

```json
The behavior of this command has been altered by the following extension: webapp
Creating Resource group 'appsvc_rg_Linux_CentralUS' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_CentralUS' ...
App service plan creation complete
Creating app '<app_name>' ....
Webapp creation complete
Creating zip with contents of dir /home/username/quickstart/python-docs-hello-world ...
Preparing to deploy contents to app.
All done.
{
  "app_url": "https:/<app_name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app_name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_CentralUS ",
  "serverfarm": "appsvc_asp_Linux_CentralUS",
  "sku": "BASIC",
  "src_path": "/home/username/quickstart/python-docs-hello-world ",
  "version_detected": "-",
  "version_to_create": "python|3.7"
}
```

O comando `az webapp up` realiza as seguintes ações:

- Criar um grupo de recursos padrão.

- Criar um plano de serviço de aplicativo padrão.

- Criar um aplicativo com o nome especificado.

- Faz a [implantação de Zip](https://docs.microsoft.com/azure/app-service/deploy-zip) dos arquivos do diretório de trabalho atual para o aplicativo.

## <a name="browse-to-the-app"></a>Navegar até o aplicativo

Navegue até o aplicativo implantado usando o navegador da Web.

```bash
http://<app_name>.azurewebsites.net
```

O código de exemplo Python está em execução no Serviço de Aplicativo no Linux com uma imagem interna.

![Aplicativo de exemplo em execução no Azure](media/quickstart-python/hello-world-in-browser.png)

**Parabéns!** Você implantou seu primeiro aplicativo Python no Serviço de Aplicativo no Linux.

## <a name="update-locally-and-redeploy-the-code"></a>Atualizar localmente e reimplantar o código

No Cloud Shell, digite `code application.py` para abrir o editor do Cloud Shell.

![Código application.py](media/quickstart-python/code-applicationpy.png)

 Faça uma pequena alteração no texto na chamada para `return`:

```python
return "Hello Azure!"
```

Salve suas alterações e saia do editor. Use o comando `^S` para salvar e `^Q` para sair.

Agora você vai reimplantar o aplicativo. Substitua `<app_name>` pelo seu aplicativo.

```bash
az webapp up -n <app_name>
```

Depois que a implantação for concluída, troque para a janela do navegador aberta na etapa **Navegar até o aplicativo** e atualize a página.

![Aplicativo de exemplo atualizado em execução no Azure](media/quickstart-python/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Gerenciar seu novo aplicativo do Azure

Acesse o <a href="https://portal.azure.com" target="_blank">portal do Azure</a> para gerenciar o aplicativo que você criou.

No menu à esquerda, clique em **Serviços de Aplicativos** e, em seguida, clique no nome do aplicativo do Azure.

![Navegação no Portal para o aplicativo do Azure](./media/quickstart-python/app-service-list.png)

Você verá a página Visão geral do aplicativo. Aqui você pode executar tarefas básicas de gerenciamento como procurar, parar, iniciar, reiniciar e excluir.

![Página Serviço de Aplicativo no portal do Azure](media/quickstart-python/app-service-detail.png)

O menu à esquerda fornece páginas diferentes para configurar seu aplicativo. 

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Próximas etapas

A imagem interna do Python no Serviço de Aplicativo no Linux está atualmente em versão prévia, sendo possível personalizar o comando usado para iniciar o aplicativo. Nesse caso, também é possível criar aplicativos Python de produção usando um contêiner personalizado.

> [!div class="nextstepaction"]
> [Tutorial: Aplicativo Python com o PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Configurar o aplicativo Python](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Tutorial: Implantação por meio de um repositório de contêiner particular](tutorial-custom-docker-image.md)
