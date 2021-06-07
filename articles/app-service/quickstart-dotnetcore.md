---
title: 'Início Rápido: Implantar um aplicativo Web ASP.NET'
description: Saiba como executar aplicativos Web no Serviço de Aplicativo do Azure implantando seu primeiro aplicativo ASP.NET.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 03/30/2021
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-ide
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-dotnetcore-uiex
ms.openlocfilehash: 482bf6d29fbc1e982ee4d17099d82915ff3a0241
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762453"
---
<!-- NOTES:

I'm a .NET developer who wants to deploy my web app to App Service. I may develop apps with
Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET SDK/CLI. This article
should be able to guide .NET devs, whether they're app is .NET Core, .NET, or .NET Framework.

As a .NET developer, when choosing an IDE and .NET TFM - you map to various OS requirements.
For example, if you choose Visual Studio - you're developing the app on Windows, but you can still
target cross-platform with .NET Core 3.1 or .NET 5.0.

| .NET / IDE         | Visual Studio | Visual Studio for Mac | Visual Studio Code | Command line   |
|--------------------|---------------|-----------------------|--------------------|----------------|
| .NET Core 3.1      | Windows       | macOS                 | Cross-platform     | Cross-platform |
| .NET 5.0           | Windows       | macOS                 | Cross-platform     | Cross-platform |
| .NET Framework 4.8 | Windows       | N/A                   | Windows            | Windows        |

-->

# <a name="quickstart-deploy-an-aspnet-web-app"></a>Início Rápido: Implantar um aplicativo Web ASP.NET

Neste guia de início rápido, você aprenderá a criar e implantar seu primeiro aplicativo Web ASP.NET no [Serviço de Aplicativo do Azure](overview.md). O Serviço de Aplicativo dá suporte a várias versões de aplicativos .NET e fornece um serviço de hospedagem na Web altamente escalonável e com aplicação de patch automática. Os aplicativos Web ASP.NET são multiplataforma e podem ser hospedados no Linux ou no Windows. Quando terminar, você terá um grupo de recursos do Azure que consistirá em um plano de hospedagem do Serviço de Aplicativo e um Serviço de Aplicativo com um aplicativo Web implantado.

> [!TIP]
> O .NET Core 3.1 é a versão atual do LTS (suporte de longo prazo) do .NET. Para obter mais informações, confira a [política de suporte do .NET](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

## <a name="prerequisites"></a>Pré-requisitos

:::zone target="docs" pivot="development-environment-vs"

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/dotnet).
- <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio 2019</a> com a carga de trabalho de **desenvolvimento Web e do ASP.NET**.

    Se você já instalou o Visual Studio 2019:

    - Instale as atualizações mais recentes no Visual Studio selecionando **Ajuda** > **Verificar Atualizações**.
    - Adicione a carga de trabalho selecionando **Ferramentas** > **Obter Ferramentas e Recursos**.

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/dotnet).
- <a href="https://www.visualstudio.com/downloads" target="_blank">Visual Studio Code</a>.
- A extensão de <a href="https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack" target="_blank">Ferramentas do Azure</a>.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

<a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank"> Instale o SDK do .NET Core 3.1 mais recente. </a>

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Instale o SDK do .NET 5.0 mais recente. </a>

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> Instale o Pacote do Desenvolvedor do .NET Framework 4.8. </a>

> [!NOTE]
> No entanto, o Visual Studio Code é multiplataforma; o .NET Framework não é. Se você estiver desenvolvendo aplicativos .NET Framework com o Visual Studio Code, considere o uso de um computador Windows para satisfazer as dependências de build.

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/dotnet).
- O <a href="/cli/azure/install-azure-cli" target="_blank">CLI do Azure</a>.
- O SDK do .NET (inclui runtime e CLI).

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

<a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank"> Instale o SDK do .NET Core 3.1 mais recente. </a>

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Instale o SDK do .NET 5.0 mais recente. </a>

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

<a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank"> Instale o SDK do .NET 5.0 mais recente. </a> e <a href="https://dotnet.microsoft.com/download/dotnet-framework/net48" target="_blank"> o Pacote do Desenvolvedor do .NET Framework 4.8. </a>

> [!NOTE]
> No entanto, a [CLI do .NET](/dotnet/core/tools) é multiplataforma; o .NET Framework não é. Se você estiver desenvolvendo aplicativos .NET Framework com a CLI do .NET, considere o uso de um computador Windows para satisfazer as dependências de build.

---

:::zone-end

## <a name="create-an-aspnet-web-app"></a>Criar um aplicativo Web ASP .NET

:::zone target="docs" pivot="development-environment-vs"

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

1. Abra o Visual Studio e selecione **Criar novo projeto**.
1. Em **Criar um projeto**, localize e escolha **Aplicativo Web ASP .NET Core** e selecione **Avançar**.
1. Em **Configurar seu novo projeto**, dê ao aplicativo o nome _MyFirstAzureWebApp_ e selecione, **Avançar**.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-net.png" alt-text="Configurar o aplicativo Web ASP.NET Core 3.1" border="true":::

1. Selecione **.NET Core 3.1 (suporte de longo prazo)** .
1. Verifique se o **Tipo de Autenticação** está definido como **Nenhum**. Selecione **Criar**.

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-netcoreapp31.png" alt-text="Visual Studio – selecione .NET Core 3.1 e Nenhum para Tipo de Autenticação." border="true":::

1. No menu do Visual Studio, selecione **Depurar** > **Iniciar sem Depuração** para executar o aplicativo Web localmente.

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio – .NET Core 3.1 procurar localmente" lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

1. Abra o Visual Studio e selecione **Criar novo projeto**.
1. Em **Criar um projeto**, localize e escolha **Aplicativo Web ASP .NET Core** e selecione **Avançar**.
1. Em **Configurar seu novo projeto**, dê ao aplicativo o nome _MyFirstAzureWebApp_ e selecione, **Avançar**.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-net.png" alt-text="Visual Studio – configure o aplicativo Web ASP.NET 5.0." border="true":::

1. Selecione **.NET Core 5.0 (atual)** .
1. Verifique se o **Tipo de Autenticação** está definido como **Nenhum**. Selecione **Criar**.

   :::image type="content" source="media/quickstart-dotnet/vs-additional-info-net50.png" alt-text="Visual Studio – informações adicionais ao selecionar o .NET Core 5.0." border="true":::

1. No menu do Visual Studio, selecione **Depurar** > **Iniciar sem Depuração** para executar o aplicativo Web localmente.

   :::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio – ASP.NET Core 5.0 em execução localmente." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

1. Abra o Visual Studio e selecione **Criar novo projeto**.
1. Em **Criar um projeto**, localize e escolha **Aplicativo Web ASP.NET (.NET Framework)** e selecione **Avançar**.
1. Em **Configurar seu novo projeto**, dê ao aplicativo o nome _MyFirstAzureWebApp_ e selecione **Criar**.

   :::image type="content" source="media/quickstart-dotnet/configure-webapp-netframework48.png" alt-text="Visual Studio – configure o aplicativo Web ASP.NET Framework 4.8." border="true":::

1. Selecione o modelo **MVC** .
1. Verifique se a **Autenticação** está definida como **Sem Autenticação**. Selecione **Criar**.

   :::image type="content" source="media/quickstart-dotnet/vs-mvc-no-auth-netframework48.png" alt-text="Visual Studio – selecione o modelo MVC." border="true":::

1. No menu do Visual Studio, selecione **Depurar** > **Iniciar sem Depuração** para executar o aplicativo Web localmente.

   :::image type="content" source="media/quickstart-dotnet/vs-local-webapp-netframework48.png" alt-text="Visual Studio – ASP.NET Framework 4.8 em execução localmente." lightbox="media/quickstart-dotnet/vs-local-webapp-netframework48.png" border="true":::

---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

Crie uma pasta chamada _MyFirstAzureWebApp_ e abra-a no Visual Studio Code. Abra a janela do <a href="https://code.visualstudio.com/docs/editor/integrated-terminal" target="_blank">Terminal</a> e crie um aplicativo Web .NET usando o comando [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options).

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

```dotnetcli
dotnet new webapp -f netcoreapp3.1
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

```dotnetcli
dotnet new webapp -f net5.0
```

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

```dotnetcli
dotnet new webapp --target-framework-override net48
```

> [!IMPORTANT]
> O sinalizador `--target-framework-override` é uma substituição de texto com forma livre do TFM (Moniker da Estrutura de Destino) para o projeto e *não garante* que o modelo de suporte exista ou compile. Você só pode compilar e executar aplicativos .NET Framework no Windows.

---

No **Terminal** no Visual Studio Code, execute o aplicativo localmente usando o comando [`dotnet run`](/dotnet/core/tools/dotnet-run).

```dotnetcli
dotnet run
```

Abra um navegador da Web e navegue até o aplicativo em `https://localhost:5001`.


### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Você verá o modelo do aplicativo Web ASP.NET Core 3.1 exibido na página.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code – execute o .NET Core 3.1 no navegador localmente." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Você verá o modelo do aplicativo Web ASP.NET Core 5.0 exibido na página.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code – execute o .NET 5.0 no navegador localmente." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

Você verá o modelo do aplicativo Web ASP.NET Framework 4.8 exibido na página.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code – execute o .NET 4.8 no navegador localmente." lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

Abra uma janela do terminal em seu computador para um diretório de trabalho. Crie um aplicativo Web .NET usando o comando [`dotnet new webapp`](/dotnet/core/tools/dotnet-new#web-options) e altere os diretórios para o aplicativo recém-criado.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp -f netcoreapp3.1 && cd MyFirstAzureWebApp
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp -f net5.0 && cd MyFirstAzureWebApp
```

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

```dotnetcli
dotnet new webapp -n MyFirstAzureWebApp --target-framework-override net48 && cd MyFirstAzureWebApp
```

> [!IMPORTANT]
> O sinalizador `--target-framework-override` é uma substituição de texto com forma livre do TFM (Moniker da Estrutura de Destino) para o projeto e *não garante* que o modelo de suporte exista ou compile. Você só pode compilar aplicativos .NET Framework no Windows.

---

Na mesma sessão do terminal, execute o aplicativo localmente usando o comando [`dotnet run`](/dotnet/core/tools/dotnet-run).

```dotnetcli
dotnet run
```

Abra um navegador da Web e navegue até o aplicativo em `https://localhost:5001`.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Você verá o modelo do aplicativo Web ASP.NET Core 3.1 exibido na página.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code – ASP.NET Core 3.1 no navegador local." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Você verá o modelo do aplicativo Web ASP.NET Core 5.0 exibido na página.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net.png" alt-text="Visual Studio Code – ASP.NET Core 5.0 no navegador local." lightbox="media/quickstart-dotnet/local-webapp-net.png" border="true":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

Você verá o modelo do aplicativo Web ASP.NET Framework 4.8 exibido na página.

:::image type="content" source="media/quickstart-dotnet/local-webapp-net48.png" alt-text="Visual Studio Code – ASP.NET Framework 4.8 no navegador local." lightbox="media/quickstart-dotnet/local-webapp-net48.png" border="true":::

---

:::zone-end

## <a name="publish-your-web-app"></a>Publicar seu aplicativo Web

Para publicar o aplicativo Web, você deve primeiro criar e configurar um novo Serviço de Aplicativo no qual você possa publicá-lo.

Como parte da configuração do Serviço de Aplicativo, você criará:

- Um novo [grupo de recursos](../azure-resource-manager/management/overview.md#terminology) para conter todos os recursos do Azure para o serviço.
- Um novo [Plano de Hospedagem](overview-hosting-plans.md) que especifica o local, o tamanho e os recursos do farm de servidores Web que hospeda o aplicativo.

Siga estas etapas para criar seu Serviço de Aplicativo e publicar o aplicativo Web:

:::zone target="docs" pivot="development-environment-vs"

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **MyFirstAzureWebApp** e selecione **Publicar**.
1. Em **Publicar**, selecione **Azure** e **Avançar**.

    :::image type="content" source="media/quickstart-dotnet/vs-publish-target-Azure.png" alt-text="Visual Studio – publique o aplicativo Web e o Azure de destino." border="true":::

1. As opções dependem de você já ter entrado no Azure e de ter uma conta do Visual Studio vinculada a uma conta do Azure. Selecione **Adicionar uma conta** ou **Entrar** para entrar em sua assinatura do Azure. Se você já estiver conectado, selecione a conta que deseja.

    :::image type="content" source="media/quickstart-dotnetcore/sign-in-Azure-vs2019.png" border="true" alt-text="Visual Studio – selecione entrar na caixa de diálogo do Azure.":::

1. Escolha o **Destino específico**: **Serviço de Aplicativo do Azure (Linux)** ou **Serviço de Aplicativo do Azure (Windows)** .

    > [!IMPORTANT]
    > Ao direcionar o ASP.NET Framework 4.8, você usará o **Serviço de Aplicativo do Azure (Windows)** .

1. À direita de **Instâncias do Serviço de Aplicativo**, selecione **+** .

    :::image type="content" source="media/quickstart-dotnetcore/publish-new-app-service.png" border="true" alt-text="Visual Studio – caixa de diálogo Novo aplicativo do Serviço de Aplicativo.":::

1. Em **Assinatura**, aceite a assinatura que está listada ou selecione uma nova na lista suspensa.
1. Para **Grupo de recursos**, selecione **Novo**. Em **Nome do novo grupo de recursos**, insira *myResourceGroup* e selecione **OK**.
1. Em **Plano de Hospedagem**, selecione **Novo**.
1. Na caixa de diálogo **Plano de Hospedagem: Criar novo**, insira os valores especificados na tabela a seguir:

    | Configuração          | Valor sugerido          | Descrição                                                           |
    |------------------|--------------------------|-----------------------------------------------------------------------|
    | **Plano de hospedagem** | *MyFirstAzureWebAppPlan* | O nome do plano do Serviço de Aplicativo.                                         |
    | **Localidade**     | *Oeste da Europa*            | O datacenter onde o aplicativo Web está hospedado.                           |
    | **Tamanho**         | *Gratuito*                   | O [Tipo de preço][app-service-pricing-tier] determina os recursos de hospedagem. |

    :::image type="content" source="media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png" border="true" alt-text="Criar um Plano de Hospedagem":::

1. Em **Nome**, insira um nome de aplicativo exclusivo que inclua somente os caracteres válidos `a-z`, `A-Z`, `0-9` e `-`. Você pode aceitar o nome exclusivo gerado automaticamente. A URL do aplicativo Web é `http://<app-name>.azurewebsites.net`, em que `<app-name>` é o nome do aplicativo.
1. Selecione **Criar** para criar os recursos do Azure.

    :::image type="content" source="media/quickstart-dotnetcore/web-app-name-vs2019.png" border="true" alt-text="Visual Studio – caixa de diálogo Criar recursos de aplicativo.":::

   Quando o assistente for concluído, os recursos do Azure serão criados para você e você estará pronto para publicar.

1. Escolha **Concluir** para fechar o assistente.
1. Na página de **Publicação**, selecione **Publicar**. O Visual Studio compila, empacota e publica o aplicativo no Azure e, em seguida, inicia o aplicativo no navegador padrão.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Você verá o aplicativo Web ASP.NET Core 3.1 exibido na página.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio – aplicativo Web ASP.NET Core 3.1 no Azure.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Você verá o aplicativo Web ASP.NET Core 5.0 exibido na página.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio – aplicativo Web ASP.NET Core 5.0 no Azure.":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    Você verá o aplicativo Web ASP.NET Framework 4.8 exibido na página.

    :::image type="content" source="media/quickstart-dotnet/vs-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Visual Studio – aplicativo Web ASP.NET Framework 4.8 no Azure.":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

Para implantar seu aplicativo Web usando a extensão de Ferramentas do Azure do Visual Studio:

1. No Visual Studio Code, abra a [**Paleta de Comandos**](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette), <kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>P</kbd>.
1. Pesquise e selecione "Serviço de Aplicativo do Azure: Implantar no aplicativo Web".
1. Responda aos prompts da seguinte maneira:

    - Selecione *MyFirstAzureWebApp* como a pasta a ser implantada.
    - Selecione **Adicionar Configuração** quando solicitado.
    - Se solicitado, entre em sua conta existente do Azure.

    :::image type="content" source="media/quickstart-dotnet/vscode-sign-in-to-Azure.png" alt-text="Visual Studio Code – entre no Azure." border="true":::

    - Selecione sua **Assinatura**.
    - Selecione **Criar Aplicativo Web... Avançado**.
    - Para **Inserir um nome globalmente exclusivo**, use um nome que seja exclusivo em todo o Azure (os *caracteres válidos são `a-z`, `0-9` e `-`* ). Um bom padrão é usar uma combinação do nome da empresa e um identificador de aplicativo.
    - Selecione **Criar grupo de recursos** e forneça um nome como `myResourceGroup`.
    - Quando for solicitado a **Selecionar uma pilha de runtime**:
      - No *.NET Core 3.1*, selecione **.NET Core 3.1 (LTS)**
      - No *.NET 5.0*, selecione **.NET 5**
      - No *.NET Framework 4.8*, selecione **ASP.NET V4.8**
    - Selecione um sistema operacional (Windows ou Linux).
        - No *.NET Framework 4.8*, o Windows será selecionado implicitamente.
    - Selecione **Criar um plano do Serviço de Aplicativo**, forneça um nome e selecione o [tipo de preço][app-service-pricing-tier] **F1 Gratuito**.
    - Selecione **Ignorar por enquanto** para o recurso do Application Insights.
    - Selecione uma localização próxima de você.

1. Quando a publicação for concluída, selecione **Procurar Site** na notificação e selecione **Abrir** quando solicitado.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Você verá o aplicativo Web ASP.NET Core 3.1 exibido na página.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio Code – aplicativo Web ASP.NET Core 3.1 no Azure.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Você verá o aplicativo Web ASP.NET Core 5.0 exibido na página.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="Visual Studio Code – aplicativo Web ASP.NET Core 5.0 no Azure.":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    Você verá o aplicativo Web ASP.NET Framework 4.8 exibido na página.

    :::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-Azure-webapp-net48.png" border="true" alt-text="Visual Studio Code – aplicativo Web ASP.NET Framework 4.8 no Azure.":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

Implante o código no seu diretório *MyFirstAzureWebApp* local usando o comando [`az webapp up`](/cli/azure/webapp#az_webapp_up):

```azurecli
az webapp up --sku F1 --name <app-name> --os-type <os>
```

- Se o comando `az` não for reconhecido, verifique se você tem a CLI do Azure instalada conforme descrito em [Pré-requisitos](#prerequisites).
- Substitua `<app-name>` por um nome que seja exclusivo em todo o Azure (*os caracteres válidos são `a-z`, `0-9` e `-`* ). Um bom padrão é usar uma combinação do nome da empresa e um identificador de aplicativo.
- O argumento `--sku F1` cria o aplicativo Web no [tipo de preço][app-service-pricing-tier] **Gratuito**. Omita esse argumento para usar um nível Premium mais rápido, o que incorre em um custo por hora.
- Substitua `<os>` por `linux` ou `windows`. Você precisa usar `windows` ao direcionar o *ASP.NET Framework 4.8*.
- Opcionalmente, você pode incluir o argumento `--location <location-name>`, em que `<location-name>` é uma região do Azure disponível. Você pode recuperar uma lista de regiões permitidas para sua conta do Azure executando o comando [`az account list-locations`](/cli/azure/appservice#az_appservice_list_locations).

O comando pode demorar um pouco para ser concluído. Durante a execução, ele fornece mensagens sobre como criar o grupo de recursos, o plano do Serviço de Aplicativo e o aplicativo de hospedagem, configurar o registro em log e executar a implantação ZIP. Em seguida, ele gera uma mensagem com a URL do aplicativo:

```azurecli
You can launch the app at http://<app-name>.azurewebsites.net
```

Abra um navegador da Web e navegue até a URL:

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Você verá o aplicativo Web ASP.NET Core 3.1 exibido na página.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="CLI – aplicativo Web ASP.NET Core 3.1 no Azure.":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Você verá o aplicativo Web ASP.NET Core 5.0 exibido na página.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net.png" lightbox="media/quickstart-dotnet/Azure-webapp-net.png" border="true" alt-text="CLI – aplicativo Web ASP.NET Core 5.0 no Azure.":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

Você verá o aplicativo Web ASP.NET Framework 4.8 exibido na página.

:::image type="content" source="media/quickstart-dotnet/Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/Azure-webapp-net48.png" border="true" alt-text="CLI – aplicativo Web ASP.NET Framework 4.8 no Azure.":::

---

:::zone-end

## <a name="update-the-app-and-redeploy"></a>Atualizar o aplicativo e reimplantar

Siga estas etapas para atualizar e reimplantar seu aplicativo Web:

:::zone target="docs" pivot="development-environment-vs"

1. No **Gerenciador de Soluções**, no seu projeto, abra *Index.cshtml*.
1. Substitua o primeiro elemento `<div>` pelo seguinte código:

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   Salve suas alterações.

1. Para implantar novamente no Azure, clique com o botão direito do mouse no projeto **MyFirstAzureWebApp** no **Gerenciador de Soluções** e selecione **Publicar**.
1. Na página de resumo **Publicar**, selecione **Publicar**.

    Quando a publicação está concluída, o Visual Studio inicia um navegador para a URL do aplicativo Web.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Você verá o aplicativo Web ASP.NET Core 3.1 atualizado exibido na página.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio – aplicativo Web ASP.NET Core 3.1 atualizado no Azure.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Você verá o aplicativo Web ASP.NET Core 5.0 atualizado exibido na página.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio – aplicativo Web ASP.NET Core 5.0 atualizado no Azure.":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    Você verá o aplicativo Web ASP.NET Framework 4.8 atualizado exibido na página.

    :::image type="content" source="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/vs-updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio – aplicativo Web ASP.NET Framework 4.8 atualizado no Azure.":::

    ---

:::zone-end

:::zone target="docs" pivot="development-environment-vscode"

1. Abra *index.cshtml*.
1. Substitua o primeiro elemento `<div>` pelo seguinte código:

    ```razor
    <div class="jumbotron">
        <h1>.NET 💜 Azure</h1>
        <p class="lead">Example .NET app to Azure App Service.</p>
    </div>
    ```

   Salve suas alterações.

1. Abra a **Barra Lateral** do Visual Studio Code e selecione o ícone do **Azure** para expandir as opções.
1. No nó **SERVIÇO DE APLICATIVO**, expanda sua assinatura e clique com o botão direito do mouse em **MyFirstAzureWebApp**.
1. Selecione a opção **Implantar no Aplicativo Web...** .
1. Selecione **Implantar** quando solicitado.
1. Quando a publicação for concluída, selecione **Procurar Site** na notificação e selecione **Abrir** quando solicitado.

    ### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

    Você verá o aplicativo Web ASP.NET Core 3.1 atualizado exibido na página.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio Code – aplicativo Web ASP.NET Core 3.1 atualizado no Azure.":::

    ### <a name="net-50"></a>[.NET 5.0](#tab/net50)

    Você verá o aplicativo Web ASP.NET Core 5.0 atualizado exibido na página.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="Visual Studio Code – aplicativo Web ASP.NET Core 5.0 atualizado no Azure.":::

    ### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

    Você verá o aplicativo Web ASP.NET Framework 4.8 atualizado exibido na página.

    :::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="Visual Studio Code – aplicativo Web ASP.NET Framework 4.8 atualizado no Azure.":::

    ---

:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->

No diretório local, abra o arquivo *Index.cshtml*. Substitua o primeiro elemento `<div>`:

```razor
<div class="jumbotron">
    <h1>.NET 💜 Azure</h1>
    <p class="lead">Example .NET app to Azure App Service.</p>
</div>
```

Salve suas alterações e reimplante o aplicativo usando o comando `az webapp up` novamente:

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

O ASP.NET Core 3.1 é multiplataforma. Com base em sua implantação anterior, substitua `<os>` por `linux` ou `windows`.

```azurecli
az webapp up --os-type <os>
```

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

O ASP.NET Core 5.0 é multiplataforma. Com base em sua implantação anterior, substitua `<os>` por `linux` ou `windows`.

```azurecli
az webapp up --os-type <os>
```

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

O ASP.NET Framework 4.8 tem dependências de estrutura e precisa ser hospedado no Windows.

```azurecli
az webapp up --os-type windows
```

> [!TIP]
> Se você tiver interesse em hospedar seus aplicativos .NET no Linux, considere fazer a migração do [ASP.NET Framework para o ASP.NET Core](/aspnet/core/migration/proper-to-2x).

---

Esse comando usa valores armazenados em cache localmente no arquivo *.azure/config*, incluindo o nome do aplicativo, o grupo de recursos e o plano do Serviço de Aplicativo.

Depois que a implantação for concluída, mude de volta para a janela do navegador aberta na etapa **Navegar até o aplicativo** e clique em Atualizar.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Você verá o aplicativo Web ASP.NET Core 3.1 atualizado exibido na página.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="CLI – aplicativo Web ASP.NET Core 3.1 atualizado no Azure.":::

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Você verá o aplicativo Web ASP.NET Core 5.0 atualizado exibido na página.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net.png" border="true" alt-text="CLI – aplicativo Web ASP.NET Core 5.0 atualizado no Azure.":::

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

Você verá o aplicativo Web ASP.NET Framework 4.8 atualizado exibido na página.

:::image type="content" source="media/quickstart-dotnet/updated-Azure-webapp-net48.png" lightbox="media/quickstart-dotnet/updated-Azure-webapp-net48.png" border="true" alt-text="CLI – aplicativo Web ASP.NET Framework 4.8 atualizado no Azure.":::

---

:::zone-end

## <a name="manage-the-azure-app"></a>Gerenciar o aplicativo do Azure

Para gerenciar o aplicativo Web, acesse o [portal do Azure](https://portal.azure.com) e procure e selecione **Serviços de Aplicativos**.

:::image type="content" source="media/quickstart-dotnetcore/app-services.png" alt-text="Portal do Azure – selecione a opção Serviços de Aplicativos.":::

Na página **Serviços de Aplicativos**, selecione o nome do seu aplicativo Web.

:::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Portal do Azure – Página Serviços de Aplicativos com um aplicativo Web de exemplo selecionado.":::

A página **Visão Geral** do aplicativo Web contém opções para gerenciamento básico, como procurar, parar, iniciar, reiniciar e excluir. O menu à esquerda fornece páginas adicionais para configurar o aplicativo.

:::image type="content" source="media/quickstart-dotnetcore/web-app-overview-page.png" alt-text="Portal do Azure – Página de visão geral do Serviço de Aplicativo.":::

<!--
## Clean up resources - H2 added from the next three includes
-->
:::zone target="docs" pivot="development-environment-vs"
[!INCLUDE [Clean-up Portal web app resources](../../includes/clean-up-section-portal-web-app.md)]
:::zone-end

:::zone target="docs" pivot="development-environment-vscode"
[!INCLUDE [Clean-up Portal web app resources](../../includes/clean-up-section-portal-web-app.md)]
:::zone-end

<!-- markdownlint-disable MD044 -->
:::zone target="docs" pivot="development-environment-cli"
<!-- markdownlint-enable MD044 -->
[!INCLUDE [Clean-up CLI resources](../../includes/cli-samples-clean-up.md)]
:::zone-end

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou e implantou um aplicativo Web ASP.NET para o Serviço de Aplicativo do Azure.

### <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

Avance para o próximo artigo para saber como criar um aplicativo .NET Core e conectá-lo a um Banco de Dados SQL:

> [!div class="nextstepaction"]
> [Tutorial: Aplicativo ASP.NET Core com o Banco de Dados SQL](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Configurar o aplicativo ASP.NET Core 3.1](configure-language-dotnetcore.md)

### <a name="net-50"></a>[.NET 5.0](#tab/net50)

Avance para o próximo artigo para saber como criar um aplicativo .NET Core e conectá-lo a um Banco de Dados SQL:

> [!div class="nextstepaction"]
> [Tutorial: Aplicativo ASP.NET Core com o Banco de Dados SQL](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [Configurar o aplicativo ASP.NET Core 5.0](configure-language-dotnetcore.md)

### <a name="net-framework-48"></a>[.NET Framework 4.8](#tab/netframework48)

Avance para o próximo artigo para saber como criar um aplicativo .NET Framework e conectá-lo a um Banco de Dados SQL:

> [!div class="nextstepaction"]
> [Tutorial: Aplicativo ASP.NET com o banco de dados SQL](app-service-web-tutorial-dotnet-sqldatabase.md)

> [!div class="nextstepaction"]
> [Configurar o aplicativo ASP.NET Framework](configure-language-dotnet-framework.md)

---

[app-service-pricing-tier]: https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
