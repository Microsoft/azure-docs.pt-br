---
title: 'Início Rápido: Criar um aplicativo Web HTML estático'
description: Implante seu primeiro Olá, Mundo em HTML no Serviço de Aplicativo do Azure em minutos. Você fará a implantação usando o Git, que é um dos vários modos de implantação no Serviço de Aplicativo.
author: msangapu-msft
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, cli-validate, seodec18
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-html-uiex
ms.openlocfilehash: 0a33889277c9f6cc60f5e81aff520e27828a1d06
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379614"
---
# <a name="create-a-static-html-web-app-in-azure"></a>Criar um aplicativo Web HTML estático no Azure

O [Serviço de Aplicativo do Azure](overview.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches. Este início rápido mostra como implantar um site HTML+CSS básico no Serviço de Aplicativo do Azure. Você concluirá este início rápido no [Cloud Shell](../cloud-shell/overview.md), mas também pode executar esses comandos localmente com a [CLI do Azure](/cli/azure/install-azure-cli).

![Home page do aplicativo de exemplo](media/quickstart-html/hello-world-in-browser-az.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="download-the-sample"></a>Baixar o exemplo

No Cloud Shell, crie um diretório de início rápido e depois altere-o.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Em seguida, execute o comando a seguir para clonar o repositório de aplicativos de exemplo ao seu diretório de início rápido.

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```

## <a name="create-a-web-app"></a>Criar um aplicativo Web

Altere para o diretório que contém o código de exemplo e execute o comando `az webapp up`. No exemplo a seguir, substitua <app_name> por um nome de aplicativo exclusivo. O conteúdo estático é indicado pelo sinalizador `--html`.

```bash
cd html-docs-hello-world

az webapp up --location westeurope --name <app_name> --html
```

O comando `az webapp up` realiza as seguintes ações:

- Criar um grupo de recursos padrão.

- Criar um plano de serviço de aplicativo padrão.

- Criar um aplicativo com o nome especificado.

- Arquivos [de implantação zip](./deploy-zip.md) do diretório de trabalho atual para o aplicativo Web.

Esse comando pode demorar um pouco para ser executado. Na execução, ele exibe informações semelhantes ao seguinte exemplo:

```output
{
  "app_url": "https://&lt;app_name&gt;.azurewebsites.net",
  "location": "westeurope",
  "name": "&lt;app_name&gt;",
  "os": "Windows",
  "resourcegroup": "appsvc_rg_Windows_westeurope",
  "serverfarm": "appsvc_asp_Windows_westeurope",
  "sku": "FREE",
  "src_path": "/home/&lt;username&gt;/quickstart/html-docs-hello-world ",
  &lt; JSON data removed for brevity. &gt;
}
```

Anote o valor `resourceGroup`. Você precisa dele para a seção [limpar recursos](#clean-up-resources).

## <a name="browse-to-the-app"></a>Navegar até o aplicativo

Em um navegador, acesse a URL do aplicativo: `http://<app_name>.azurewebsites.net`.

A página está sendo executada como um aplicativo Web do Serviço de Aplicativo do Azure.

![Home page do aplicativo de exemplo](media/quickstart-html/hello-world-in-browser-az.png)

**Parabéns!** Você implantou seu primeiro aplicativo HTML no Serviço de Aplicativo.

## <a name="update-and-redeploy-the-app"></a>Atualizar o aplicativo e reimplantar

No Cloud Shell, digite `nano index.html` para abrir o editor de texto nano. Na marca do título `<h1>`, altere "Serviço de Aplicativo do Azure – Exemplo de Site HTML Estático" para "Serviço de Aplicativo do Azure", como mostrado abaixo.

![Nano index.html](media/quickstart-html/nano-index-html.png)

Salve suas alterações e saia do nano. Use o comando `^O` para salvar e `^X` para sair.

Agora você vai reimplantar o aplicativo com o mesmo comando `az webapp up`.

```bash
az webapp up --location westeurope --name <app_name> --html
```

Depois que a implantação for concluída, troque para a janela do navegador aberta na etapa **Navegar até o aplicativo** e atualize a página.

![Home page do aplicativo de exemplo atualizada](media/quickstart-html/hello-azure-in-browser-az.png)

## <a name="manage-your-new-azure-app"></a>Gerenciar seu novo aplicativo do Azure

Para ver o aplicativo Web que você criou, no [portal do Azure](https://portal.azure.com), procure **Serviços de Aplicativos** e selecione essa opção. 

![Selecionar Serviços de Aplicativos no portal do Azure](./media/quickstart-html/portal0.png)

Na página **Serviços de Aplicativos**, selecione o nome do seu aplicativo do Azure.

![Navegação no Portal para o aplicativo do Azure](./media/quickstart-html/portal1.png)

A página Visão Geral do seu aplicativo Web é exibida. Aqui você pode executar tarefas básicas de gerenciamento como procurar, parar, iniciar, reiniciar e excluir.

![Folha Serviço de Aplicativo no portal do Azure](./media/quickstart-html/portal2.png)

O menu à esquerda fornece páginas diferentes para configurar seu aplicativo.

## <a name="clean-up-resources"></a>Limpar os recursos

Nas etapas anteriores, você criou os recursos do Azure em um grupo de recursos. Se você acha que não precisará desses recursos no futuro, exclua o grupo de recursos executando o seguinte comando no Cloud Shell. Lembre-se de que o nome do grupo de recursos foi gerado automaticamente para você na etapa [criar um aplicativo Web](#create-a-web-app).

```bash
az group delete --name appsvc_rg_Windows_westeurope
```

Esse comando pode demorar um pouco para ser executado.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Mapear domínio personalizado](app-service-web-tutorial-custom-domain.md)
