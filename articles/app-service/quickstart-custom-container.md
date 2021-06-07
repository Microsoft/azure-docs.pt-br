---
title: 'Início Rápido: Executar um contêiner personalizado no Serviço de Aplicativo'
description: Comece a usar contêineres no Serviço de Aplicativo do Azure implantando seu primeiro contêiner personalizado.
author: msangapu-msft
ms.author: msangapu
ms.date: 10/21/2019
ms.topic: quickstart
ms.custom: devx-track-csharp
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 360da015f012822593dbb6390cb7df0017ba85b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745070"
---
# <a name="run-a-custom-container-in-azure"></a>Executar um contêiner personalizado no Azure

::: zone pivot="container-windows"
[O Serviço de Aplicativo do Azure](overview.md) fornece pilhas de aplicativos predefinidos em Windows, como ASP.NET ou Node.js, em execução no IIS. O ambiente de contêiner do Windows pré-configurado impede o sistema operacional de executar o acesso administrativo, instalações de software e alterações do cache de assembly global e assim por diante. Para saber mais, confira [Funcionalidade do sistema operacional no Serviço de Aplicativo do Azure](operating-system-functionality.md). Se o aplicativo exigir mais acesso que o permitido pelo ambiente pré-configurado, você pode implantar um contêiner personalizado do Windows como alternativa.

Este início rápido mostra como implantar um aplicativo ASP.NET em uma imagem do Windows para o [Docker Hub](https://hub.docker.com/) do Visual Studio. O aplicativo é executado em um contêiner personalizado no Serviço de Aplicativo do Azure.

> [!NOTE]
> Os Contêineres do Windows são limitados aos Arquivos do Azure e não dão suporte ao Blob do Azure no momento.


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- <a href="https://hub.docker.com/" target="_blank">Inscrever-se em uma conta do Hub do Docker</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Instalar o Docker for Windows</a>.
- <a href="/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Mudar o Docker para executar contêineres do Windows</a>.
- <a href="https://www.visualstudio.com/downloads/" target="_blank">Instale o Visual Studio 2019</a> com as cargas de trabalho de **desenvolvimento do ASP.NET e para a Web** e **desenvolvimento do Azure**. Se você já instalou o Visual Studio 2019:

    - Instale as atualizações mais recentes no Visual Studio selecionando **Ajuda** > **Verificar Atualizações**.
    - Adicione as cargas de trabalho no Visual Studio selecionando **Ferramentas** > **Obter Ferramentas e Recursos**.

## <a name="create-an-aspnet-web-app"></a>Criar um aplicativo Web ASP .NET

Crie um aplicativo Web ASP.NET seguindo estas etapas:

1. Abra o Visual Studio e selecione **Criar novo projeto**.

1. Em **Criar novo projeto**, localize e escolha **Aplicativo Web ASP.NET (.NET Framework)** para C# e, em seguida, selecione **Avançar**.

1. Em **Configurar seu novo projeto**, dê ao aplicativo o nome _myfirstazurewebapp_ e, em seguida, selecione, **Criar**.

   ![Configurar seu projeto de aplicativo Web](./media/quickstart-custom-container/configure-web-app-project-container.png)

1. Você pode implantar qualquer tipo de aplicativo Web ASP.NET no Azure. Para este início rápido, escolha o modelo **MVC**.

1. Selecione **Suporte para Docker** e verifique se a autenticação está definida como **Sem Autenticação**. Selecione **Criar**.

   ![Criar aplicativo Web ASP.NET](./media/quickstart-custom-container/select-mvc-template-for-container.png)

1. Se o arquivo _Dockerfile_ não for aberto automaticamente, abra-o no **Gerenciador de Soluções**.

1. Você precisa de uma [imagem pai com suporte](configure-custom-container.md#supported-parent-images). Altere a imagem pai, substituindo a linha `FROM` pelo código a seguir e salve o arquivo:

   ```dockerfile
   FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
   ```

1. No menu do Visual Studio, selecione **Depurar** > **Iniciar sem Depuração** para executar o aplicativo Web localmente.

   ![Executar o aplicativo localmente](./media/quickstart-custom-container/local-web-app.png)

## <a name="publish-to-docker-hub"></a>Publicar no Docker Hub

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **myfirstazurewebapp** e selecione **Publicar**.

1. Escolha **Serviço de Aplicativo** e selecione **Publicar**.

1. Em **Escolher um destino de publicação**, selecione **Registro de Contêiner** e **Docker Hub** e clique em **Publicar**.

   ![Publicar na página de visão geral do projeto](./media/quickstart-custom-container/publish-to-docker-vs2019.png)

1. Forneça suas credenciais da conta do Docker Hub e selecione **Salvar**.

   Aguarde até que a implantação seja concluída. A página **Publicar** agora mostra o nome do repositório que você usará mais tarde.

   ![Captura de tela que realça o nome do repositório.](./media/quickstart-custom-container/published-docker-repository-vs2019.png)

1. Copie esse nome de repositório para uso posterior.

## <a name="create-a-windows-container-app"></a>Criar um aplicativo de contêiner do Windows

1. Entre no [portal do Azure]( https://portal.azure.com).

1. Escolha **Criar um recurso** no canto superior esquerdo do portal do Azure.

1. Na caixa de pesquisa acima da lista de recursos do Azure Marketplace, pesquise **Aplicativo Web para Contêineres** e selecione **Criar**.

1. Em **Criação de Aplicativo Web**, escolha sua assinatura e um **Grupo de Recursos**. Você pode criar um novo grupo de recursos, se necessário.

1. Forneça um nome de aplicativo, como *win-container-demo*, e escolha **Windows** como o **Sistema Operacional**. Selecione **Avançar: Docker** para continuar.

   ![Criar um Aplicativo Web para Contêineres](media/quickstart-custom-container/create-web-app-continer.png)

1. Para **Origem da Imagem**, escolha **Docker Hub** e, para **Imagem e tag**, insira o nome do repositório que você copiou em [Publicar no Docker Hub](#publish-to-docker-hub).

   ![Configurar seu Aplicativo Web para Contêineres](media/quickstart-custom-container/configure-web-app-continer.png)

    Se você tiver uma imagem personalizada em outro lugar para seu aplicativo Web, como no [Registro de Contêiner do Azure](../container-registry/index.yml) ou em outro repositório privado, configure-a aqui.

1. Selecione **Examinar e Criar** e, em seguida, **Criar** e espere o Azure criar os recursos necessários.

## <a name="browse-to-the-container-app"></a>Navegar até o aplicativo de contêiner

Quando a operação do Azure for concluída, uma caixa de notificação será exibida.

![Implantação bem-sucedida](media/quickstart-custom-container/portal-create-finished.png)

1. Clique em **Ir para o recurso**.

1. Na visão geral desse recurso, siga o link ao lado de **URL**.

Uma nova página do navegador é aberta na seguinte página:

![Aplicativo de Contêiner do Windows Iniciando](media/quickstart-custom-container/app-starting.png)

Aguarde alguns minutos e tente novamente, até chegar à home page padrão do ASP.NET:

![Aplicativo de Contêiner do Windows em execução](media/quickstart-custom-container/app-running-vs.png)

**Parabéns!** Você está executando seu primeiro contêiner personalizado do Windows no Serviço de Aplicativo do Azure.

## <a name="see-container-start-up-logs"></a>Conferir logs de inicialização do contêiner

Pode levar algum tempo para o contêiner do Windows ser carregado. Para ver o progresso, navegue até a URL a seguir, substituindo *\<app_name>* pelo nome do aplicativo.
```
https://<app_name>.scm.azurewebsites.net/api/logstream
```

Os logs transmitidos têm esta aparência:

```
2018-07-27T12:03:11  Welcome, you are now connected to log-streaming service.
27/07/2018 12:04:10.978 INFO - Site: win-container-demo - Start container succeeded. Container: facbf6cb214de86e58557a6d073396f640bbe2fdec88f8368695c8d1331fc94b
27/07/2018 12:04:16.767 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.017 INFO - Site: win-container-demo - Container start complete
27/07/2018 12:05:05.020 INFO - Site: win-container-demo - Container started successfully
```

## <a name="update-locally-and-redeploy"></a>Atualizar localmente e reimplantar

1. No Visual Studio, em **Gerenciador de Soluções**, abra **Exibições** > **Início** > **Index.cshtml**.

1. Encontre o rótulo HTML `<div class="jumbotron">` próximo à parte superior e substitua o elemento inteiro pelo seguinte código:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Para implantar novamente no Azure, clique com o botão direito do mouse no projeto **myfirstazurewebapp** no **Gerenciador de Soluções** e escolha **Publicar**.

1. Na página de publicação, selecione **Publicar** e aguarde até que a publicação seja concluída.

1. Para informar ao Serviço de Aplicativo que ele deve obter a nova imagem do Docker Hub, reinicie o aplicativo. Na página do aplicativo no portal, clique em **Reiniciar** > **Sim**.

   ![Reinicie o aplicativo Web no Azure](./media/quickstart-custom-container/portal-restart-app.png)

[Navegue até o aplicativo de contêiner](#browse-to-the-container-app) novamente. Conforme você atualiza a página da Web, o aplicativo é revertido para a página "Iniciando" no início e, em seguida, exibe a página da Web atualizada novamente após alguns minutos.

![Aplicativo Web atualizado no Azure](./media/quickstart-custom-container/azure-web-app-updated.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Migrar para o contêiner do Windows no Azure](tutorial-custom-container.md)

Se preferir, confira outros recursos:

> [!div class="nextstepaction"]
> [Configurar um contêiner personalizado](configure-custom-container.md)

::: zone-end  

::: zone pivot="container-linux"
O Serviço de Aplicativo no Linux fornece pilhas de aplicativos predefinidos em Linux com suporte para linguagens como .NET, PHP, Node.js e outras. Também é possível usar uma imagem personalizada do Docker para executar seu aplicativo Web em uma pilha de aplicativos que ainda não foi definida no Azure. Este guia de início rápido mostra como implantar uma imagem de um [ACR (Registro de Contêiner do Azure)](../container-registry/index.yml) para o Serviço de Aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

* Uma [conta do Azure](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)
* [Docker](https://www.docker.com/community-edition)
* [Visual Studio Code](https://code.visualstudio.com/)
* A [extensão do Serviço de Aplicativo do Azure para VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Você pode usar essa extensão para criar, gerenciar e implantar aplicativos Web Linux na PaaS (plataforma como serviço) do Azure.
* A [extensão do Docker para VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker). Você pode usar essa extensão para simplificar o gerenciamento de imagens e comandos do Docker local e para implantar imagens de aplicativo criadas no Azure.

## <a name="create-an-image"></a>Criar uma imagem

Para concluir este guia de início rápido, você precisará de uma imagem de aplicativo Web adequada armazenada em um [Registro de Contêiner do Azure](../container-registry/index.yml). Siga as instruções em [Início rápido: Criar um registro de contêiner privado usando o portal do Azure](../container-registry/container-registry-get-started-portal.md), mas use a imagem `mcr.microsoft.com/azuredocs/go` em vez da imagem `hello-world`. Para referência, o [Dockerfile de exemplo é encontrado no repositório de Exemplos do Azure](https://github.com/Azure-Samples/go-docs-hello-world).

> [!IMPORTANT]
> Certifique-se de definir a opção de **Usuário Administrador** como **Habilitar** ao criar o registro de contêiner. Você também pode defini-la na seção **Chaves de acesso** da página do registro no portal do Azure. Essa configuração é necessária para o acesso ao Serviço de Aplicativo.

## <a name="sign-in"></a>Entrar

Em seguida, inicie o VS Code e faça logon em sua conta do Azure usando a extensão do Serviço de Aplicativo. Para fazer isso, selecione o logotipo do Azure na Barra de Atividades, navegue até o gerenciador do **SERVIÇO DE APLICATIVO**, selecione **Entrar no Azure** e siga as instruções.

![entrar no Azure](./media/quickstart-docker/sign-in.png)

## <a name="check-prerequisites"></a>Verificar pré-requisitos

Agora, você pode verificar se tem todos os pré-requisitos instalados e configurados adequadamente.

No VS Code, você deverá ver o endereço de email do Azure na Barra de Status e sua assinatura no gerenciador do **SERVIÇO DE APLICATIVO**.

Em seguida, verifique se você tem o Docker instalado e em execução. O comando a seguir exibirá a versão do Docker se ela estiver em execução.

```bash
docker --version
```

Por fim, verifique se o Registro de Contêiner do Azure está conectado. Para fazer isso, selecione o logotipo do Docker na Barra de Atividade e, em seguida, navegue até **REGISTROS**.

![A captura de tela mostra o valor de Registros com o Azure expandido e um arquivo com a extensão de nome de arquivo .io.](./media/quickstart-docker/registries.png)

## <a name="deploy-the-image-to-azure-app-service"></a>Implantar a imagem no Serviço de Aplicativo do Azure

Agora que tudo está configurado, você pode implantar sua imagem no [Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/) diretamente do gerenciador de extensões do Docker.

Localize a imagem no nó **Registros** no gerenciador do **DOCKER** e expanda para mostrar suas marcações. Clique com o botão direito do mouse em uma tag e selecione **Implantar Imagem no Serviço de Aplicativo do Azure**.

Aqui, siga os prompts para escolher uma assinatura, um nome de aplicativo exclusivo globalmente, um Grupo de Recursos e um Plano do Serviço de Aplicativo. Escolha **B1 Básico** para o tipo de preço e uma região.

Após a implantação, seu aplicativo estará disponível em `http://<app name>.azurewebsites.net`.

Um **Grupo de Recursos** é uma coleção nomeada de todos os recursos do seu aplicativo no Azure. Por exemplo, um grupo de recursos pode conter uma referência a um site, um banco de dados e uma Função do Azure.

Um **Plano do Serviço de Aplicativo** define os recursos físicos que serão usados para hospedar seu site. Este guia de início rápido usa um plano de hospedagem **Básico** na infraestrutura **Linux**, o que significa que o site será hospedado em um computador Linux junto com outros sites. Se você começar com o plano **Básico**, poderá usar o portal do Azure para aumentá-lo para que o seu seja o único site em execução em um computador.

## <a name="browse-the-website"></a>Procurar no site

O painel **Saída** será aberto durante a implantação para indicar o status da operação. Quando a operação for concluída, localize o aplicativo criado no gerenciador do **SERVIÇO DE APLICATIVO**, clique nele com o botão direito do mouse e selecione **Procurar Site** para abrir o site no navegador.

> [!div class="nextstepaction"]
> [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=quickstart-docker&step=deploy-app)

## <a name="next-steps"></a>Próximas etapas

Parabéns, você concluiu com êxito este início rápido!

Em seguida, confira as outras extensões do Azure.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Ferramentas da CLI do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Ferramentas do Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Ou obtenha todas elas instalando o pacote de extensão [Ferramenta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack).

Confira outros recursos:

> [!div class="nextstepaction"]
> [Configurar um contêiner personalizado](configure-custom-container.md)

::: zone-end
