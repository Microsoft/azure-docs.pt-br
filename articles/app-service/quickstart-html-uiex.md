---
title: 'Início Rápido: Criar um aplicativo Web HTML estático'
description: Implante seu primeiro Olá, Mundo em HTML no Serviço de Aplicativo do Azure em minutos. Você fará a implantação usando o Git, que é um dos vários modos de implantação no Serviço de Aplicativo.
author: msangapu-msft
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, cli-validate, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 603d8e642cd2e88beec6ae34094a2c6c43d179ee
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768889"
---
# <a name="create-a-static-html-web-app-in-azure"></a>Criar um aplicativo Web HTML estático no Azure

Este início rápido mostra como implantar um site HTML+CSS básico no <abbr title="Um serviço baseado em HTTP para hospedagem de aplicativos Web, APIs REST e aplicativos de back-end móveis.">Serviço de aplicativo do Azure</abbr>. Você concluirá este início rápido no [Cloud Shell](../cloud-shell/overview.md), mas também pode executar esses comandos localmente com a [CLI do Azure](/cli/azure/install-azure-cli).

## <a name="1-prepare-your-environment"></a>1. Prepare o seu ambiente

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

No [Cloud Shell](../cloud-shell/overview.md), crie um diretório de início rápido e mude para ele.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Em seguida, execute o comando a seguir para clonar o repositório de aplicativos de exemplo ao seu diretório de início rápido.

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```
<hr/>

## <a name="2-create-a-web-app"></a>2. Criar um aplicativo Web

Altere para o diretório que contém o código de exemplo e execute o comando `az webapp up`. **Substitua** `<app-name>` por um nome globalmente exclusivo.

```bash
cd html-docs-hello-world

az webapp up --location westeurope --name <app_name> --html
```

<details>
<summary>Solução de problemas</summary>
<ul>
<li>Se o comando <code>az</code> não for reconhecido, verifique se você tem a CLI do Azure instalada conforme descrito em <a href="#1-prepare-your-environment">Preparar o ambiente</a>.</li>
<li>Substitua <code>&lt;app-name&gt;</code> por um nome que seja exclusivo em todo o Azure (<em>os caracteres válidos são <code>a-z</code>, <code>0-9</code> e <code>-</code></em>). Um bom padrão é usar uma combinação do nome da empresa e um identificador de aplicativo.</li>
<li>O argumento <code>--sku F1</code> cria o aplicativo Web no tipo de preço Gratuito. Omita esse argumento para usar um nível Premium mais rápido, o que incorre em um custo por hora.</li>
<li>O <code>--html</code> argumento instrui a tratar todo o conteúdo da pasta como estático e desabilitar a automação de build.</li>
<li>Opcionalmente, você pode incluir o argumento <code>--location &lt;location-name&gt;</code>, em que <code>&lt;location-name&gt;</code> é uma região do Azure disponível. Você pode recuperar uma lista de regiões permitidas para sua conta do Azure executando o comando <a href="/cli/azure/appservice#az_appservice_list_locations"><code>az account list-locations</code></a>.</li>
</ul>
</details>

O comando pode demorar um pouco para ser concluído. 

<details>
<summary>O que <code>az webapp up</code> está fazendo?</summary>
<p>O comando <code>az webapp up</code> realiza as seguintes ações:</p>
<ul>
<li>Criar um grupo de recursos padrão.</li>
<li>Criar um plano do Serviço de Aplicativo padrão.</li>
<li><a href="/cli/azure/webapp#az_webapp_create">Criar um aplicativo do Serviço de Aplicativo</a> com o nome especificado.</li>
<li>Faz a <a href="/azure/app-service/deploy-zip">implantação de Zip</a> dos arquivos do diretório de trabalho atual para o aplicativo.</li>
<li>Durante a execução, ele fornece mensagens sobre a criação de recursos, o registro em log e a implantação do ZIP.</li>
</ul>

Quando termina, ele exibe informações semelhantes ao seguinte exemplo:

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

</details>

Você precisará do valor `resourceGroup` para [limpar os recursos](#6-clean-up-resources) posteriormente.

<hr/>

## <a name="3-browse-to-the-app"></a>3. Navegar até o aplicativo

Em um navegador, acesse a URL do aplicativo: `http://<app_name>.azurewebsites.net`.

A página está sendo executada como um aplicativo Web do Serviço de Aplicativo do Azure.

![Home page do aplicativo de exemplo](media/quickstart-html/hello-world-in-browser-az.png)

<hr/>

## <a name="4-update-and-redeploy-the-app"></a>4. Atualizar o aplicativo e reimplantar

No Cloud Shell, **digite** `nano index.html` para abrir o editor de texto nano. 

Na marca do título `<h1>`, altere "Serviço de Aplicativo do Azure – Exemplo de Site HTML Estático" para "Serviço de Aplicativo do Azure".

![Nano index.html](media/quickstart-html/nano-index-html.png)

**Salve** as alterações usando o comando `^O`.

**Saia** do nano usando o comando `^X`.

Reimplante o aplicativo com o comando `az webapp up`.

```bash
az webapp up --html
```

Volte para a janela do navegador aberta na etapa **Navegar até o aplicativo**.

**Atualize** a página.

![Home page do aplicativo de exemplo atualizada](media/quickstart-html/hello-azure-in-browser-az.png)

<hr/>

## <a name="5-manage-your-new-azure-app"></a>5. Gerenciar seu novo aplicativo do Azure

**Navegue** até o [portal do Azure](https://portal.azure.com). 

**Pesquise** pelos **Serviços de Aplicativos** e **selecione** essa opção.

![Selecionar Serviços de Aplicativos no portal do Azure](./media/quickstart-html/portal0.png)

**Selecione** o nome de seu aplicativo do Azure.

![Navegação no Portal para o aplicativo do Azure](./media/quickstart-html/portal1.png)

A página Visão Geral do seu aplicativo Web é exibida. Aqui você pode executar tarefas básicas de gerenciamento como procurar, parar, iniciar, reiniciar e excluir.

![Folha Serviço de Aplicativo no portal do Azure](./media/quickstart-html/portal2.png)

O menu à esquerda fornece páginas diferentes para configurar seu aplicativo.

<hr/>

## <a name="6-clean-up-resources"></a>6. Limpar os recursos

Nas etapas anteriores, você criou os recursos do Azure em um grupo de recursos. Se você acha que não precisará desses recursos no futuro, exclua o grupo de recursos executando o seguinte comando no Cloud Shell. Lembre-se de que o nome do grupo de recursos foi gerado automaticamente para você na etapa [criar um aplicativo Web](#2-create-a-web-app).

```bash
az group delete --name appsvc_rg_Windows_westeurope
```

Esse comando pode demorar um pouco para ser executado.

<hr/>

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Mapear domínio personalizado](app-service-web-tutorial-custom-domain-uiex.md)
