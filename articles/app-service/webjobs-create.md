---
title: Executar tarefas em segundo plano com trabalhos Web
description: Saiba como usar trabalhos Web para executar tarefas em segundo plano no serviço Azure App. Escolha entre uma variedade de formatos de script e execute-os com expressões CRON.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ms.reviewer: msangapu;suwatch;pbatum;naren.soni
ms.custom: seodec18
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./webjobs-create-ieux
ms.openlocfilehash: 8afc8ca9b0dedb10ecdb30e8abb22a5d0986de5b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723870"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Executar tarefas em segundo plano com trabalhos Web no serviço Azure App

Este artigo mostra como implantar o WebJobs usando o [portal do Azure](https://portal.azure.com) para carregar um executável ou um script. Para obter informações sobre como desenvolver e implantar o WebJobs usando o Visual Studio, consulte [Implantar o WebJobs usando o Visual Studio](webjobs-dotnet-deploy-vs.md).

## <a name="overview"></a>Visão geral
O webjobs é um recurso do [serviço de Azure app](index.yml) que permite executar um programa ou script na mesma instância que um aplicativo Web, aplicativo de API ou aplicativo móvel. Não há nenhum custo adicional para usar Trabalhos Web.

> [!IMPORTANT]
> O WebJobs ainda não é suportado para o Serviço de Aplicativo no Linux.

O SDK do WebJobs do Azure pode ser usado com o WebJobs para simplificar muitas tarefas de programação. Para obter mais informações, consulte [O que é o SDK de Trabalhos Web](https://github.com/Azure/azure-webjobs-sdk/wiki).

O Azure Functions oferece outra maneira de executar programas e scripts. Para obter uma comparação entre o WebJobs e o Functions, consulte [Escolher entre o Flow, Aplicativos Lógicos, Functions e WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>Tipos de WebJob

A tabela a seguir descreve as diferenças entre WebJobs *contínuos* e *disparados*.


|Contínuo  |Disparado  |
|---------|---------|
| É iniciado imediatamente quando o WebJob é criado. Para impedir o encerramento do trabalho, o programa ou o script normalmente faz seu trabalho dentro de um loop infinito. Se o trabalho for encerrado, você poderá reiniciá-lo. | É iniciado apenas quando disparado manualmente ou de acordo com um agendamento. |
| É executado em todas as instâncias nas quais o aplicativo Web é executado. Opcionalmente, você pode restringir o WebJob a uma única instância. |É executado em uma única instância selecionada pelo Azure para balanceamento de carga.|
| Dá suporte à depuração remota. | Não dá suporte à depuração remota.|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="supported-file-types-for-scripts-or-programs"></a><a name="acceptablefiles"></a>Tipos de arquivo com suporte para scripts ou programas

Há suporte para os tipos de arquivo a seguir:

* .cmd, .bat, .exe (usando o cmd do Windows)
* .ps1 (usando o PowerShell)
* .sh (usando o Bash)
* .php (usando o PHP)
* .py (usando o Python)
* .js (usando o Node.js)
* .jar (usando o Java)

## <a name="create-a-continuous-webjob"></a><a name="CreateContinuous"></a> Criar um WebJob contínuo

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

> [!IMPORTANT]
> Se você tiver o controle do código-fonte configurado com seu aplicativo, os trabalhos Web deverão ser implantados como parte da integração do controle do código-fonte. Depois que o controle do código-fonte é configurado com seu aplicativo, um WebJob não pode ser adicionado do portal do Azure.

1. No [portal do Azure](https://portal.azure.com), acesse a página **Serviço de Aplicativo** do aplicativo Web, aplicativo de API ou aplicativo móvel do Serviço de Aplicativo.

2. Selecione **WebJobs**.

   ![Selecionar o WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Na página **WebJobs**, selecione **Adicionar**.

    ![Página do WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Use as configurações **Adicionar WebJob**, conforme especificado na tabela.

   ![Captura de tela que mostra as configurações de adicionar WebJob que você precisa configurar.](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Configuração      | Valor de exemplo   | Descrição  |
   | ------------ | ----------------- | ------------ |
   | **Nome** | myContinuousWebJob | Um nome que seja exclusivo em um aplicativo do Serviço de Aplicativo. Deve começar com uma letra ou um número e não pode conter caracteres especiais além de “-” e “_”. |
   | **Upload de arquivo** | ConsoleApp.zip | Um arquivo *.zip* que contém o executável ou o arquivo de script, bem como os arquivos de suporte necessários para executar o programa ou o script. Os tipos de executável ou arquivo de script com suporte são listados na seção [Tipos de arquivo com suporte](#acceptablefiles). |
   | **Tipo** | Contínuo | Os [tipos do WebJob](#webjob-types) foram descritos anteriormente neste artigo. |
   | **Escala** | Várias instâncias | Disponível somente para WebJobs Contínuos. Determina se o programa ou o script é executado em todas as instâncias ou apenas em uma única instância. A opção de execução em várias instâncias não se aplica aos [tipos de preço](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Gratuito ou Compartilhado. | 

4. Clique em **OK**.

   O novo WebJob é exibido na página **WebJobs**.

   ![Lista de WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. Para parar ou reiniciar um WebJob contínuo, clique com o botão direito do mouse no WebJob na lista e clique em **Parar** ou **Iniciar**.

    ![Interromper um WebJob contínuo](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="create-a-manually-triggered-webjob"></a><a name="CreateOnDemand"></a> Criar um WebJob disparado manualmente

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. No [portal do Azure](https://portal.azure.com), acesse a página **Serviço de Aplicativo** do aplicativo Web, aplicativo de API ou aplicativo móvel do Serviço de Aplicativo.

2. Selecione **WebJobs**.

   ![Selecionar o WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Na página **WebJobs**, selecione **Adicionar**.

    ![Página do WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Use as configurações **Adicionar WebJob**, conforme especificado na tabela.

   ![Captura de tela que mostra as configurações que precisam ser definidas para a criação de um WebJob disparado manualmente.](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Configuração      | Valor de exemplo   | Descrição  |
   | ------------ | ----------------- | ------------ |
   | **Nome** | myTriggeredWebJob | Um nome que seja exclusivo em um aplicativo do Serviço de Aplicativo. Deve começar com uma letra ou um número e não pode conter caracteres especiais além de “-” e “_”.|
   | **Upload de arquivo** | ConsoleApp.zip | Um arquivo *.zip* que contém o executável ou o arquivo de script, bem como os arquivos de suporte necessários para executar o programa ou o script. Os tipos de executável ou arquivo de script com suporte são listados na seção [Tipos de arquivo com suporte](#acceptablefiles). |
   | **Tipo** | Disparado | Os [tipos do WebJob](#webjob-types) foram descritos anteriormente neste artigo. |
   | **Gatilhos** | Manual | |

4. Clique em **OK**.

   O novo WebJob é exibido na página **WebJobs**.

   ![Lista de WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. Para executar o Trabalho Web, clique com o botão direito do mouse em seu nome na lista e clique em **Executar**.
   
    ![Executar o Trabalho Web](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="create-a-scheduled-webjob"></a><a name="CreateScheduledCRON"></a> Criar um WebJob agendado

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. No [portal do Azure](https://portal.azure.com), acesse a página **Serviço de Aplicativo** do aplicativo Web, aplicativo de API ou aplicativo móvel do Serviço de Aplicativo.

2. Selecione **WebJobs**.

   ![Selecionar o WebJobs](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Na página **WebJobs**, selecione **Adicionar**.

   ![Página do WebJob](./media/web-sites-create-web-jobs/wjblade.png)

3. Use as configurações **Adicionar WebJob**, conforme especificado na tabela.

   ![Página Adicionar WebJob](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | Configuração      | Valor de exemplo   | Descrição  |
   | ------------ | ----------------- | ------------ |
   | **Nome** | myScheduledWebJob | Um nome que seja exclusivo em um aplicativo do Serviço de Aplicativo. Deve começar com uma letra ou um número e não pode conter caracteres especiais além de “-” e “_”. |
   | **Upload de arquivo** | ConsoleApp.zip | Um arquivo *.zip* que contém o executável ou o arquivo de script, bem como os arquivos de suporte necessários para executar o programa ou o script. Os tipos de executável ou arquivo de script com suporte são listados na seção [Tipos de arquivo com suporte](#acceptablefiles). |
   | **Tipo** | Disparado | Os [tipos do WebJob](#webjob-types) foram descritos anteriormente neste artigo. |
   | **Gatilhos** | Agendado | Para que o agendamento funcione de modo confiável, habilite o recurso AlwaysOn. O AlwaysOn está disponível apenas nos tipos de preço Básico, Standard e Premium.|
   | **Expressão CRON** | 0 0/20 * * * * | As [expressões CRON](#ncrontab-expressions) são descritas na seção a seguir. |

4. Clique em **OK**.

   O novo WebJob é exibido na página **WebJobs**.

   ![Lista de WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="ncrontab-expressions"></a>Expressões NCRONTAB

Você pode inserir uma [expressão NCRONTAB](../azure-functions/functions-bindings-timer.md#ncrontab-expressions) no portal ou incluir um `settings.job` arquivo na raiz do seu arquivo WebJob *. zip* , como no exemplo a seguir:

```json
{
    "schedule": "0 */15 * * * *"
}
```

Para saber mais, consulte [agendando um WebJob disparado](webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob).

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

## <a name="view-the-job-history"></a><a name="ViewJobHistory"></a> Exibir o histórico de trabalhos

1. Selecione o WebJob do qual você deseja ver o histórico e, em seguida, selecione o botão **Logs**.
   
   ![Botão Logs](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. Na página **Detalhes do WebJob**, selecione uma hora para ver os detalhes de uma execução.
   
   ![Detalhes do WebJob](./media/web-sites-create-web-jobs/webjobdetails.png)

3. Na página **Detalhes da Execução do WebJob**, selecione **Alternar Saída** para ver o texto do conteúdo do log.
   
    ![Detalhes da execução do trabalho Web](./media/web-sites-create-web-jobs/webjobrundetails.png)

   Para ver o texto de saída em uma janela separada do navegador, selecione **Baixar**. Para baixar o texto propriamente dito, clique com o botão direito do mouse em **Baixar** e use as opções do navegador para salvar o conteúdo do arquivo.
   
5. Selecione o link da barra de trilha **WebJobs** na parte superior da página para ir para uma lista de WebJobs.

    ![Barra de trilha do WebJob](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![Lista de Trabalhos Web no painel de histórico](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="next-steps"></a><a name="NextSteps"></a> Próximas etapas

O SDK do WebJobs do Azure pode ser usado com o WebJobs para simplificar muitas tarefas de programação. Para obter mais informações, consulte [O que é o SDK de Trabalhos Web](https://github.com/Azure/azure-webjobs-sdk/wiki).
