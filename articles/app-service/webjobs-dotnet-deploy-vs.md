---
title: Desenvolver e implantar trabalhos Web usando o Visual Studio
description: Saiba como desenvolver Azure WebJobs no Visual Studio e implantá-los no serviço Azure App, incluindo a criação de uma tarefa agendada.
author: ggailey777
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.topic: conceptual
ms.custom: devx-track-csharp, vs-azure
ms.date: 07/30/2020
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: de10903be86b52b3415b57a53be81e7fd1661f63
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89226022"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio"></a>Desenvolver e implantar trabalhos Web usando o Visual Studio

Este artigo explica como usar o Visual Studio para implantar um projeto de aplicativo de console em um aplicativo Web no [serviço Azure app](overview.md) como um [WebJob do Azure](https://go.microsoft.com/fwlink/?LinkId=390226). Para obter informações sobre como implantar trabalhos Web usando o [portal do Azure](https://portal.azure.com), consulte [executar tarefas em segundo plano com trabalhos web no serviço Azure app](webjobs-create.md).

Você pode optar por desenvolver um WebJob que seja executado como um [aplicativo .NET Core](#webjobs-as-net-core-console-apps) ou um [aplicativo .NET Framework](#webjobs-as-net-framework-console-apps). A versão 3. x do [SDK do Azure WebJobs](webjobs-sdk-how-to.md) permite que você desenvolva trabalhos Web que são executados como aplicativos .NET Core ou .NET Framework aplicativos, enquanto a versão 2. x dá suporte apenas à .NET Framework. A maneira como você implanta um projeto de trabalhos Web é diferente para projetos do .NET Core do que para projetos .NET Framework.

Você pode publicar vários webjobs em um único aplicativo Web, desde que cada WebJob em um aplicativo Web tenha um nome exclusivo.

## <a name="webjobs-as-net-core-console-apps"></a>Trabalhos Web como aplicativos de console do .NET Core

Com a versão 3. x do SDK do Azure WebJobs, você pode criar e publicar trabalhos Web como aplicativos de console do .NET Core. Para obter as instruções passo a passo para criar e publicar um aplicativo de console do .NET Core no Azure como um WebJob, consulte Introdução [ao SDK do Azure WebJobs para processamento em segundo plano orientado a eventos](webjobs-sdk-get-started.md).

> [!NOTE]
> Os webjobs do .NET Core não podem ser vinculados a projetos da Web. Se você precisar implantar seu WebJob com um aplicativo Web, [crie seus webjobs como um aplicativo de console .NET Framework](#webjobs-as-net-framework-console-apps).  

### <a name="deploy-to-azure-app-service"></a>Implantar no Serviço de Aplicativo do Azure

A publicação de um WebJob do .NET Core em Azure App serviço do Visual Studio usa as mesmas ferramentas que a publicação de um aplicativo ASP.NET Core.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

## <a name="webjobs-as-net-framework-console-apps"></a>Trabalhos Web como aplicativos de console .NET Framework  

Se você usar o Visual Studio para implantar um projeto de aplicativo de console .NET Framework habilitado para trabalhos Web, ele copiará os arquivos de tempo de execução para a pasta apropriada no aplicativo (*App_Data/Jobs/Continuous* para webjobs contínuos e *App_Data/Jobs/Triggered* para trabalhos Web agendados ou sob demanda).

O Visual Studio adiciona os seguintes itens a um projeto habilitado para trabalhos Web:

* O pacote NuGet [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) .
* Um arquivo [webjob-publish-settings.json](#publishsettings) que contém configurações de implantação e agendador. 

![Diagrama mostrando o que é adicionado a um aplicativo de console para habilitar a implantação como um WebJob](./media/webjobs-dotnet-deploy-vs/convert.png)

Você pode adicionar esses itens a um projeto de aplicativo de console existente ou usar um modelo para criar um novo projeto de aplicativo de console habilitado para trabalhos Web. 

Implante um projeto como um WebJob ou vincule-o a um projeto Web para que ele seja implantado automaticamente sempre que você implantar o projeto Web. Para vincular projetos, o Visual Studio inclui o nome do projeto habilitado para Trabalhos Web em um arquivo [webjobs-list.json](#webjobslist) no projeto Web.

![Diagrama que mostra o projeto de Trabalho Web sendo vinculado ao projeto Web](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Pré-requisitos

Instale o Visual Studio 2017 ou o Visual Studio 2019 com a [carga de trabalho de desenvolvimento do Azure](/visualstudio/install/install-visual-studio#step-4---choose-workloads).

### <a name="enable-webjobs-deployment-for-an-existing-console-app-project"></a><a id="convert"></a> Habilitar a implantação de trabalhos Web para um projeto de aplicativo de console existente

Você tem duas opções:

* [Habilitar implantação automática com um projeto Web](#convertlink).

  Configure um projeto de aplicativo de console existente para que ele seja implantado automaticamente como um WebJob quando você implantar um projeto Web. Use esta opção quando quiser executar o Trabalho Web no mesmo aplicativo Web em que você executa o aplicativo Web relacionado.

* [Habilitar implantação sem um projeto Web](#convertnolink).

  Configure um projeto de aplicativo de console existente para implantar como um WebJob por si só, sem um link para um projeto Web. Use esta opção quando você quiser executar um Trabalho Web em um aplicativo Web por si só, sem nenhum aplicativo Web em execução no aplicativo Web. Talvez você queira fazer isso para dimensionar os recursos do WebJob independentemente dos recursos do aplicativo Web.

#### <a name="enable-automatic-webjobs-deployment-with-a-web-project"></a><a id="convertlink"></a> Habilitar a implantação de Trabalhos Web automática com um projeto Web

1. Clique com o botão direito do mouse no projeto Web em **Gerenciador de soluções** e, em seguida, selecione **Adicionar**  >  **projeto existente como WebJob do Azure**.
   
    ![Projeto Existente como Trabalho Web do Azure](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    A caixa de diálogo [Adicionar Trabalho Web do Azure](#configure) é exibida.
2. Na lista suspensa **nome do projeto** , selecione o projeto de aplicativo de console a ser adicionado como um WebJob.
   
    ![Selecionando projeto na caixa de diálogo Adicionar trabalho Web do Azure](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. Conclua a caixa de diálogo [Adicionar trabalho Web do Azure](#configure) e selecione **OK**. 

#### <a name="enable-webjobs-deployment-without-a-web-project"></a><a id="convertnolink"></a> Habilitar a implantação de Trabalhos Web sem um projeto Web
1. Clique com o botão direito do mouse no projeto de aplicativo de console no **Gerenciador de soluções** e, em seguida, selecione **publicar como Azure WebJob**. 
   
    ![Publicar como Trabalho Web do Azure](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    A caixa de diálogo [Adicionar Trabalho Web do Azure](#configure) é exibida, com o projeto selecionado na caixa **Nome do projeto** .
2. Conclua a caixa de diálogo [Adicionar trabalho Web do Azure](#configure) e selecione **OK**.
   
   O assistente **Publicar Web** é exibido. Se você não quiser publicar imediatamente, feche o assistente. As configurações inseridas por você são salvas para quando quiser [implantar o projeto](#deploy).

### <a name="create-a-new-webjobs-enabled-project"></a><a id="create"></a>Criar um novo projeto habilitado para Trabalhos Web
Para criar um novo projeto habilitado para trabalhos Web, use o modelo de projeto de aplicativo de console e habilite a implantação de trabalhos Web conforme explicado na [seção anterior](#convert). Também é possível usar o modelo do novo projeto de Trabalhos Web:

* [Usar o modelo de novo projeto de trabalhos Web para um WebJob independente](#createnolink)
  
    Crie um projeto e o configure para ser implantado propriamente dito como um Trabalho Web, sem link para um projeto Web. Use esta opção quando você quiser executar um Trabalho Web em um aplicativo Web por si só, sem nenhum aplicativo Web em execução no aplicativo Web. Talvez você queira fazer isso para dimensionar os recursos do WebJob independentemente dos recursos do aplicativo Web.
* [Usar o modelo de novo projeto de trabalhos Web para um WebJob vinculado a um projeto](#createlink)
  
    Crie um projeto que esteja configurado para ser implantado automaticamente como um WebJob quando você implantar um projeto Web na mesma solução. Use esta opção quando quiser executar o Trabalho Web no mesmo aplicativo Web em que você executa o aplicativo Web relacionado.

> [!NOTE]
> O modelo new-project do WebJobs instala automaticamente pacotes NuGet e inclui o código em *Program.cs* para o [SDK do WebJobs](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Se você não desejar usar o SDK do WebJobs, remova ou altere a instrução `host.RunAndBlock` em *Program.cs*.
> 
> 

#### <a name="use-the-webjobs-new-project-template-for-an-independent-webjob"></a><a id="createnolink"></a> Usar o novo modelo de projeto de Trabalhos Web para um Trabalho Web independente
1. Selecione **Arquivo** > **Novo** > **Projeto**. Na caixa de diálogo **criar um novo projeto** , procure e selecione **Azure WebJob (.NET Framework)** para C#.
   
2. Siga as instruções anteriores para [tornar o projeto de aplicativo de console um projeto de trabalhos Web independente](#convertnolink).

#### <a name="use-the-webjobs-new-project-template-for-a-webjob-linked-to-a-web-project"></a><a id="createlink"></a> Usar o novo modelo de projeto de Trabalhos Web para um Trabalho Web vinculado a um projeto Web
1. Clique com o botão direito do mouse no projeto Web no **Gerenciador de soluções** e selecione **Adicionar**  >  **novo projeto do Azure WebJob**.
   
    ![Entrada de menu de Novo Projeto de Trabalho Web do Azure](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    A caixa de diálogo [Adicionar Trabalho Web do Azure](#configure) é exibida.
2. Conclua a caixa de diálogo [Adicionar trabalho Web do Azure](#configure) e selecione **OK**.


### <a name="webjob-publish-settingsjson-file"></a><a id="publishsettings"></a>webjob-publish-settings.jsno arquivo
Quando você configura um aplicativo de console para a implantação de trabalhos Web, o Visual Studio instala o pacote NuGet [Microsoft. Web. webjobs. publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) e armazena informações de agendamento em um *webjob-publish-settings.jsno* arquivo na pasta *Propriedades* do projeto do projeto trabalhos Web. Aqui está um exemplo desse arquivo:

```json
{
  "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
  "webJobName": "WebJob1",
  "startTime": "null",
  "endTime": "null",
  "jobRecurrenceFrequency": "null",
  "interval": null,
  "runMode": "Continuous"
}
```

É possível editar esse arquivo diretamente, e o Visual Studio fornece o IntelliSense. O esquema de arquivo é armazenado em [https://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) e pode ser exibido lá.  

### <a name="webjobs-listjson-file"></a><a id="webjobslist"></a>webjobs-list.jsno arquivo
Quando você vincula um projeto habilitado para Trabalhos Web a um projeto Web, o Visual Studio armazena o nome do projeto de Trabalhos Web em um arquivo *webjobs-list.json* na pasta *Propriedades* do projeto Web. A lista pode conter vários projetos do WebJobs, conforme mostrado no seguinte exemplo:

```json
{
  "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
  "WebJobs": [
    {
      "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
    },
    {
      "filePath": "../WebJob1/WebJob1.csproj"
    }
  ]
}
```

Você pode editar esse arquivo diretamente no Visual Studio, com o IntelliSense. O esquema de arquivo é armazenado em [https://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) .

### <a name="deploy-a-webjobs-project"></a><a id="deploy"></a>Implantar um projeto de Trabalhos Web
Um projeto webjobs que você vinculou a um projeto Web é implantado automaticamente com o projeto Web. Para obter informações sobre a implantação de projeto Web, consulte os **guias de instruções para**  >  **implantar o aplicativo** no painel de navegação esquerdo.

Para implantar um projeto de trabalhos Web por si só, clique com o botão direito do mouse no projeto em **Gerenciador de soluções** e selecione **publicar como Azure WebJob**. 

![Publicar como Trabalho Web do Azure](./media/webjobs-dotnet-deploy-vs/paw.png)

Para um Trabalho Web independente, o mesmo assistente **Publicar Web** usado em projetos Web é exibido, mas com menos configurações disponíveis para serem alteradas.

### <a name="add-azure-webjob-dialog-box"></a><a id="configure"></a>Caixa de diálogo Adicionar trabalho Web do Azure
A caixa de diálogo **Adicionar trabalho Web do Azure** permite que você insira o nome do webjob e a configuração do modo de execução para seu WebJob. 

![Caixa de diálogo Adicionar trabalho Web do Azure](./media/webjobs-dotnet-deploy-vs/aaw2.png)

Alguns dos campos nessa caixa de diálogo correspondem aos campos na caixa de diálogo **Adicionar WebJob** da portal do Azure. Para obter mais informações, consulte [executar tarefas em segundo plano com trabalhos Web no serviço Azure app](webjobs-create.md).

Informações de implantação do WebJob:

* Para obter informações sobre a implantação de linha de comando, consulte [Habilitando a entrega de linha de comando ou contínua de Trabalhos Web do Azure](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).

* Se você implantar um WebJob e, em seguida, decidir que deseja alterar o tipo de trabalho Web e reimplantar, exclua o *webjobs-publish-settings.jsno* arquivo. Isso faz com que o Visual Studio reexiba as opções de publicação, para que você possa alterar o tipo de trabalho Web.

* Se você implantar um Trabalho Web e depois alterar o modo de execução de contínuo para não contínuo ou vice-versa, o Visual Studio criará um novo Trabalho Web no Azure quando você o reimplantar. Se você alterar outras configurações de agendamento, mas deixar o modo de execução igual ou alternar entre agendado e sob demanda, o Visual Studio atualizará o trabalho existente em vez de criar um novo.

## <a name="webjob-types"></a>Tipos de WebJob

O tipo de um WebJob pode ser *disparado* ou *contínuo*:

- Disparado (padrão): um WebJob disparado começa com base em um evento de associação, em um [agendamento](#scheduling-a-triggered-webjob), ou quando você o dispara manualmente (sob demanda). Ele é executado em uma única instância em que o aplicativo Web é executado.

- Contínuo: um WebJob [contínuo](#continuous-execution) é iniciado imediatamente quando o webjob é criado. Ele é executado em todas as instâncias escaladas do aplicativo Web por padrão, mas pode ser configurado para ser executado como uma única instância por meio de *Settings. Job*.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="scheduling-a-triggered-webjob"></a>Agendando um WebJob disparado

Quando você publica um aplicativo de console no Azure, o Visual Studio define o tipo de WebJob como **disparado por padrão** e adiciona um novo arquivo *Settings. Job* ao projeto. Para tipos de WebJob disparados, você pode usar esse arquivo para definir um agendamento de execução para seu WebJob.

Use o arquivo *Settings. Job* para definir um agendamento de execução para seu WebJob. O exemplo a seguir é executado a cada hora das 9h às 17:00:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Esse arquivo está localizado na raiz da pasta webjobs com o script do WebJob, como `wwwroot\app_data\jobs\triggered\{job name}` ou `wwwroot\app_data\jobs\continuous\{job name}` . Quando você implanta um WebJob do Visual Studio, marque suas *configurações.* Propriedades do arquivo de trabalho no Visual Studio como **copiar se mais recente**.

Se você [criar um WebJob na portal do Azure](webjobs-create.md), o arquivo *Settings. Job* será criado para você.

#### <a name="cron-expressions"></a>Expressões CRON

O webjobs usa as mesmas expressões CRON para agendamento como o gatilho de temporizador no Azure Functions. Para saber mais sobre o suporte a CRON, consulte [gatilho de temporizador para Azure Functions](../azure-functions/functions-bindings-timer.md#ncrontab-expressions).

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

#### <a name="settingsjob-reference"></a>referência de Settings. Job

As configurações a seguir são suportadas pelos trabalhos Web:

| **Configuração** | **Tipo**  | **Descrição** |
| ----------- | --------- | --------------- |
| `is_in_place` | Todos | Permite que o WebJob seja executado no local sem primeiro ser copiado para uma pasta temporária. Para obter mais informações, consulte [diretório de trabalho do WebJob](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory). |
| `is_singleton` | Contínuo | Só execute o WebJob em uma única instância quando expandido. Para obter mais informações, consulte [definir um trabalho contínuo como singleton](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton). |
| `schedule` | Disparado | Execute o WebJob em uma agenda baseada em CRON. Para obter mais informações, consulte [NCRONTAB Expressions](../azure-functions/functions-bindings-timer.md#ncrontab-expressions). |
| `stopping_wait_time`| Todos | Permite o controle do comportamento de desligamento. Para obter mais informações, consulte [Desligamento normal](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown). |

### <a name="continuous-execution"></a>Execução contínua

Se você habilitar o **Always on** no Azure, poderá usar o Visual Studio para alterar o trabalho Web para ser executado continuamente:

1. Se você ainda não tiver feito isso, [publique o projeto no Azure](#deploy-to-azure-app-service).

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no nome do projeto e selecione **Publicar**.

1. Na guia **publicar** , escolha **Editar**. 

1. Na caixa de diálogo **configurações de perfil** , escolha **contínuo** para o **tipo WebJob** e, em seguida, escolha **salvar**.

    ![Caixa de diálogo Configurações de publicação para um WebJob](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. Selecione **publicar** na guia **publicar** para republicar o WebJob com as configurações atualizadas.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre o SDK do WebJobs](webjobs-sdk-how-to.md)