---
title: Desenvolver e implantar o WebJobs usando o Visual Studio – Azure
description: Saiba como desenvolver e implantar o Azure WebJobs no Serviço de Aplicativo do Azure usando o Visual Studio.
services: app-service
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.service: app-service
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 02/18/2019
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: e66a2ffa6578ed0c9eb5eb19659adf9ba253bbeb
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67613367"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Desenvolver e implantar o WebJobs usando o Visual Studio – Serviço de Aplicativo do Azure

Este artigo explica como usar o Visual Studio para implantar um projeto de aplicativo de Console em um aplicativo web no [serviço de aplicativo](overview.md) como um [Azure WebJob](https://go.microsoft.com/fwlink/?LinkId=390226). Para obter informações sobre como implantar o WebJobs usando o [portal do Azure](https://portal.azure.com), consulte [Executar tarefas em segundo plano com o WebJobs](webjobs-create.md).

Você pode publicar diversos trabalhos Web em um único aplicativo web. Certifique-se de que cada trabalho Web em um aplicativo web tem um nome exclusivo.

Versão 3.x do [SDK do Azure WebJobs](webjobs-sdk-how-to.md) permite que você desenvolva trabalhos Web que são executados como aplicativos .NET Core ou .NET Framework a aplicativos, enquanto a versão 2.x oferece suporte a .NET Framework. A maneira que você implantar um projeto de trabalhos Web é diferente para projetos do .NET Core em comparação com aqueles do .NET Framework.

## <a name="webjobs-as-net-core-console-apps"></a>Trabalhos Web como aplicativos de console .NET Core

Ao usar a versão 3.x do WebJobs, você pode criar e publicar WebJobs como aplicativos de console .NET Core. Para obter instruções passo a passo criar e publicar um aplicativo de console .NET Core no Azure como um trabalho Web, consulte [Introdução ao SDK de WebJobs do Azure para processamento em segundo plano controlada por evento](webjobs-sdk-get-started.md).

> [!NOTE]
> Trabalhos Web do .NET core não pode ser vinculado com projetos da web. Se você precisar implantar o seu trabalho Web com um aplicativo web, você deve [criar seu WebJob como um aplicativo de console do .NET Framework](#webjobs-as-net-framework-console-apps).  

### <a name="deploy-to-azure-app-service"></a>Implantar o serviço de aplicativo do Azure

Publicando um WebJob do .NET Core no serviço de aplicativo do Visual Studio usa as mesmas ferramentas como publicar um aplicativo ASP.NET Core.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

### <a name="webjob-types"></a>Tipos de WebJob

Por padrão, um trabalho Web publicados a partir de um projeto de console é executado apenas quando disparado do .NET Core ou sob demanda. Você também pode atualizar o projeto para [executada em uma agenda](#scheduled-execution) ou executado continuamente.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

#### <a name="scheduled-execution"></a>Execução agendada

Quando você publica um aplicativo de console .NET Core no Azure, um novo *Settings* arquivo é adicionado ao projeto. Use esse arquivo para definir uma agenda de execução do trabalho Web. Para obter mais informações, consulte [agendar um WebJob disparado](#scheduling-a-triggered-webjob).

#### <a name="continuous-execution"></a>Execução contínua

Você pode usar o Visual Studio para alterar o trabalho Web seja executado continuamente quando Always On está habilitado no Azure.

1. Se você ainda não fez isso, [publicar o projeto no Azure](#deploy-to-azure-app-service).

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no nome do projeto e selecione **Publicar**.

1. No **Publish** guia, escolha **configurações**. 

1. No **configurações de perfil** caixa de diálogo, escolha **Continuous** para **tipo de WebJob**e escolha **salvar**.

    ![Caixa de diálogo Configurações de publicação para um trabalho Web](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. Selecione **publicar** republicar o trabalho Web com as configurações atualizadas.

## <a name="webjobs-as-net-framework-console-apps"></a>Trabalhos Web como aplicativos de console do .NET Framework  

Quando o Visual Studio implanta um projeto habilitado para trabalhos Web o aplicativo de Console do .NET Framework, ele executa duas tarefas:

* Copia os arquivos de tempo de execução para a pasta apropriada no aplicativo web (*App_Data/trabalhos/continuous* para trabalhos Web contínuos e *App_Data/trabalhos/triggered* para trabalhos Web agendados ou sob demanda).
* Configura trabalhos do [Agendador do Microsoft Azure](https://docs.microsoft.com/azure/scheduler/) para WebJobs agendados para execução em determinadas horas. (Isso não é necessário para Trabalhos Web contínuos.)

Um projeto habilitado para Trabalhos Web tem os seguintes itens adicionados:

* O pacote NuGet [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) .
* Um arquivo [webjob-publish-settings.json](#publishsettings) que contém configurações de implantação e agendador. 

![Diagrama que mostra o que é adicionado a um Aplicativo de Console para habilitar implantação como um Trabalho Web](./media/webjobs-dotnet-deploy-vs/convert.png)

É possível adicionar esses itens a um projeto do Aplicativo de Console existente ou usar um modelo para criar um novo projeto do Aplicativo de Console habilitado para Trabalhos Web. 

É possível implantar um projeto como um Trabalho Web propriamente dito ou vinculá-lo a um projeto Web de forma que ele seja implantado automaticamente sempre que você implanta o projeto Web. Para vincular projetos, o Visual Studio inclui o nome do projeto habilitado para Trabalhos Web em um arquivo [webjobs-list.json](#webjobslist) no projeto Web.

![Diagrama que mostra o projeto de Trabalho Web sendo vinculado ao projeto Web](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Pré-requisitos

Se estiver usando o Visual Studio 2015, instale o [SDK do Azure para .NET (Visual Studio 2015)](https://azure.microsoft.com/downloads/).

Se você estiver usando o Visual Studio de 2019, instale o [carga de trabalho de desenvolvimento do Azure](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---choose-workloads).

### <a id="convert"></a> Habilitar a implantação do WebJobs em um projeto existente do Aplicativo de Console

Você tem duas opções:

* [Habilitar implantação automática com um projeto Web](#convertlink).

  Configure um projeto do Aplicativo de Console existente de forma que ele seja implantado automaticamente como um Trabalho Web quando você implanta o projeto Web. Use esta opção quando quiser executar o Trabalho Web no mesmo aplicativo Web em que você executa o aplicativo Web relacionado.

* [Habilitar implantação sem um projeto Web](#convertnolink).

  Configure um projeto do Aplicativo de Console existente a ser implantado como um Trabalho Web propriamente dito, sem link para um projeto Web. Use esta opção quando você quiser executar um Trabalho Web em um aplicativo Web por si só, sem nenhum aplicativo Web em execução no aplicativo Web. Você talvez queira fazer isso para ser capaz de dimensionar os recursos de Trabalho Web independentemente dos recursos do aplicativo Web.

#### <a id="convertlink"></a> Habilitar a implantação de Trabalhos Web automática com um projeto Web

1. Clique com o botão direito do mouse no projeto Web no **Gerenciador de Soluções** e clique em **Adicionar** > **Projeto Existente como Trabalho Web do Azure**.
   
    ![Projeto Existente como Trabalho Web do Azure](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    A caixa de diálogo [Adicionar Trabalho Web do Azure](#configure) é exibida.
2. Na lista suspensa **Nome do projeto** , selecione o projeto do Aplicativo de Console a ser adicionado como um Trabalho Web.
   
    ![Selecionando projeto na caixa de diálogo Adicionar Trabalho Web do Azure](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. Complete a caixa de diálogo [Adicionar Trabalho Web do Azure](#configure) e clique em **OK**. 

#### <a id="convertnolink"></a> Habilitar a implantação de Trabalhos Web sem um projeto Web
1. Clique com o botão direito do mouse no projeto do Aplicativo de Console no **Gerenciador de Soluções** e, depois, clique em **Publicar como Azure WebJob...** . 
   
    ![Publicar como Trabalho Web do Azure](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    A caixa de diálogo [Adicionar Trabalho Web do Azure](#configure) é exibida, com o projeto selecionado na caixa **Nome do projeto** .
2. Complete a caixa de diálogo [Adicionar Trabalho Web do Azure](#configure) e clique em **OK**.
   
   O assistente **Publicar Web** é exibido.  Se você não desejar publicar imediatamente, feche o assistente. As configurações inseridas por você são salvas para quando quiser [implantar o projeto](#deploy).

### <a id="create"></a>Criar um novo projeto habilitado para Trabalhos Web
Para criar um projeto habilitado para Trabalhos Web, é possível usar o modelo de projeto do Aplicativo de Console e habilitar a implantação de Trabalhos Web conforme explicado na [seção anterior](#convert). Também é possível usar o modelo do novo projeto de Trabalhos Web:

* [Usar o novo modelo de projeto de Trabalhos Web para um Trabalho Web independente](#createnolink)
  
    Crie um projeto e o configure para ser implantado propriamente dito como um Trabalho Web, sem link para um projeto Web. Use esta opção quando você quiser executar um Trabalho Web em um aplicativo Web por si só, sem nenhum aplicativo Web em execução no aplicativo Web. Você talvez queira fazer isso para ser capaz de dimensionar os recursos de Trabalho Web independentemente dos recursos do aplicativo Web.
* [Usar o novo modelo de projeto de Trabalhos Web para um Trabalho Web vinculado a um projeto Web](#createlink)
  
    Crie um projeto que seja configurado para ser implantado automaticamente como um Trabalho Web quando um projeto Web na mesma solução for implantado. Use esta opção quando quiser executar o Trabalho Web no mesmo aplicativo Web em que você executa o aplicativo Web relacionado.

> [!NOTE]
> O modelo new-project do WebJobs instala automaticamente pacotes NuGet e inclui o código em *Program.cs* para o [SDK do WebJobs](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Se você não desejar usar o SDK do WebJobs, remova ou altere a instrução `host.RunAndBlock` em *Program.cs*.
> 
> 

#### <a id="createnolink"></a> Usar o novo modelo de projeto de Trabalhos Web para um Trabalho Web independente
1. Clique em **Arquivo** > **Novo Projeto** e, depois, na caixa de diálogo **Novo Projeto**, clique em **Nuvem** > **Azure WebJob (.NET Framework)** .
   
    ![Caixa de diálogo Novo Projeto mostrando o modelo de Trabalho Web](./media/webjobs-dotnet-deploy-vs/np.png)
2. Siga as direções mostradas anteriormente para [tornar o projeto do Aplicativo de Console um projeto de Trabalhos Web independente](#convertnolink).

#### <a id="createlink"></a> Usar o novo modelo de projeto de Trabalhos Web para um Trabalho Web vinculado a um projeto Web
1. Clique com o botão direito do mouse no projeto Web no **Gerenciador de Soluções** e clique em **Adicionar** > **Novo Projeto de Trabalho Web do Azure**.
   
    ![Entrada de menu de Novo Projeto de Trabalho Web do Azure](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    A caixa de diálogo [Adicionar Trabalho Web do Azure](#configure) é exibida.
2. Complete a caixa de diálogo [Adicionar Trabalho Web do Azure](#configure) e clique em **OK**.

### <a id="configure"></a>A caixa de diálogo Adicionar Trabalho Web do Azure
A caixa de diálogo **Adicionar Azure WebJob** permite inserir o nome do WebJob e executar a configuração de modo do WebJob. 

![Caixa de diálogo Adicionar Trabalho Web do Azure](./media/webjobs-dotnet-deploy-vs/aaw2.png)

Os campos dessa caixa de diálogo correspondem aos campos da caixa de diálogo **Adicionar WebJob** do portal do Azure. Para obter mais informações, consulte [Executar tarefas em segundo plano com o Trabalhos Web](webjobs-create.md).

> [!NOTE]
> * Para obter informações sobre a implantação de linha de comando, consulte [Habilitando a entrega de linha de comando ou contínua de Trabalhos Web do Azure](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
> * Se você implantar um WebJob e, em seguida, decidir que deseja alterar o tipo de WebJob e implantá-lo novamente, precisará excluir o arquivo *webjobs-publish-settings.json*. Isso fará com que o Visual Studio exiba novamente as opções de publicação para que você possa alterar o tipo de Trabalho Web.
> * Se você implantar um Trabalho Web e depois alterar o modo de execução de contínuo para não contínuo ou vice-versa, o Visual Studio criará um novo Trabalho Web no Azure quando você o reimplantar. Se você alterar outras configurações de agendamento, mas deixar o modo de execução igual ou alternar Agendado e Sob Demanda, o Visual Studio atualizará o trabalho existente, em vez de criar um novo.
> 
> 

### <a id="publishsettings"></a>webjob-publish-settings.json
Quando você configura um Aplicativo de Console para implantação de Trabalhos Web, o Visual Studio instala o pacote NuGet [Microsoft.Web.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) e armazena informações de agendamento em um arquivo *webjob-publish-settings.json* na pasta *Propriedades* do projeto dos Trabalhos Web. Aqui está um exemplo desse arquivo:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

É possível editar esse arquivo diretamente, e o Visual Studio fornece o IntelliSense. O esquema de arquivo é armazenado em [https://schemastore.org](https://schemastore.org/schemas/json/webjob-publish-settings.json) e pode ser exibido.  

### <a id="webjobslist"></a>webjobs-list.json
Quando você vincula um projeto habilitado para Trabalhos Web a um projeto Web, o Visual Studio armazena o nome do projeto de Trabalhos Web em um arquivo *webjobs-list.json* na pasta *Propriedades* do projeto Web. A lista pode conter vários projetos do WebJobs, conforme mostrado no seguinte exemplo:

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

É possível editar esse arquivo diretamente, e o Visual Studio fornece o IntelliSense. O esquema de arquivo é armazenado em [https://schemastore.org](https://schemastore.org/schemas/json/webjobs-list.json) e pode ser exibido.

### <a id="deploy"></a>Implantar um projeto de Trabalhos Web
Um projeto de Trabalhos Web vinculado a um projeto Web é implantado automaticamente com o projeto Web. Para obter informações sobre a implantação de projetos Web, consulte os **guias de instruções** > **Implantar aplicativo** na navegação à esquerda.

Para implantar um projeto do WebJobs sozinho, clique com o botão direito do mouse no projeto no **Gerenciador de Soluções** e clique em **Publicar como Azure WebJob...** . 

![Publicar como Trabalho Web do Azure](./media/webjobs-dotnet-deploy-vs/paw.png)

Para um Trabalho Web independente, o mesmo assistente **Publicar Web** usado em projetos Web é exibido, mas com menos configurações disponíveis para serem alteradas.

## <a name="scheduling-a-triggered-webjob"></a>Agendar um WebJob disparado

Trabalhos Web usa um *Settings* arquivo para determinar quando um trabalho Web é executada. Use esse arquivo para definir uma agenda de execução do trabalho Web. O exemplo a seguir executa a cada hora entre 9H às 17H:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Esse arquivo deve estar localizado na raiz da pasta de trabalhos Web, ao longo do lado do script do seu WebJob, como `wwwroot\app_data\jobs\triggered\{job name}` ou `wwwroot\app_data\jobs\continuous\{job name}`. Ao implantar um WebJob por meio do Visual Studio, marque as propriedades do arquivo `settings.job` como **Copiar se mais recente**. 

Quando você [criar um trabalho Web do portal do Azure](webjobs-create.md), o arquivo Settings job é criado para você.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="cron-expressions"></a>Expressões CRON

Trabalhos Web usa as mesmas expressões de CRON para o agendamento do gatilho de temporizador no Azure Functions. Para saber mais sobre o suporte CRON, consulte o [artigo de referência de gatilho de temporizador](../azure-functions/functions-bindings-timer.md#cron-expressions).

### <a name="settingjob-reference"></a>referência de Setting.job

As configurações a seguir têm suporte de trabalhos Web:

| **Configuração** | **Tipo**  | **Descrição** |
| ----------- | --------- | --------------- |
| `is_in_place` | Todos | Permite que o trabalho seja executado no local sem primeiro ser copiado para uma pasta temporária. Para obter mais informações, consulte [diretório de trabalho de trabalhos Web](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory). |
| `is_singleton` | Contínuo | Execute apenas os trabalhos Web em uma única instância quando escalado horizontalmente. Para obter mais informações, consulte [definir um trabalho contínuo como singleton](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton). |
| `schedule` | Disparado | Execute o trabalho Web em um agendamento de CRON. Para obter mais informações, consulte o [artigo de referência de gatilho de temporizador](../azure-functions/functions-bindings-timer.md#cron-expressions). |
| `stopping_wait_time`| Todos | Permite o controle do comportamento de desligamento. Para obter mais informações, consulte [desligamento normal](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown). |

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre o SDK do WebJobs](webjobs-sdk-how-to.md)
