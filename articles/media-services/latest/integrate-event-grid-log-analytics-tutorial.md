---
title: Armazenar eventos dos Serviços de Mídia no Azure Log Analytics
description: Saiba como armazenar eventos dos Serviços de Mídia do Azure no Azure Log Analytics.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.date: 08/24/2020
ms.author: inhenkel
ms.openlocfilehash: a10625f6f5bec18528e56bf8f607f5c1b25aa6e3
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281061"
---
# <a name="tutorial-store-azure-media-services-events-in-azure-log-analytics"></a>Tutorial: Armazenar eventos dos Serviços de Mídia do Azure no Azure Log Analytics

## <a name="azure-media-services-events"></a>Eventos dos Serviços de Mídia do Azure

Os Serviços de Mídia do Azure v3 emitem eventos na [Grade de Eventos do Azure](monitoring/media-services-event-schemas.md). Você pode assinar eventos de várias maneiras e armazená-los em armazenamentos de dados. Neste tutorial, você assinará os eventos dos Serviços de Mídia usando um [Fluxo de Aplicativos Lógicos](https://azure.microsoft.com/services/logic-apps/). O Aplicativo Lógico será disparado para cada evento e armazenará o corpo do evento no Azure Log Analytics. Depois que os eventos estiverem no Azure Log Analytics, você poderá usar outros serviços do Azure para criar um painel, para monitor eventos e gerar alertas sobre eles, embora não abordaremos isso neste tutorial.

> [!NOTE]
> Seria útil se você já estivesse familiarizado com o uso de FFmpeg como seu codificador local.  Caso contrário, não tem problema. A linha de comando e as instruções para transmitir um vídeo estão incluídas abaixo.

Você saberá como:

> [!div class="checklist"]
> * Criar um fluxo de Aplicativo Lógico sem código
> * Assinar tópicos de eventos dos Serviços de Mídia do Azure
> * Analisar eventos e armazená-los no Azure Log Analytics
> * Consultar eventos por meio do Azure Log Analytics

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

> * Uma [assinatura do Azure](setup-azure-subscription-how-to.md)
> * Uma conta e um grupo de recursos dos [Serviços de Mídia](account-create-how-to.md).
> * Uma instalação do [FFmpeg](https://ffmpeg.org/download.html) no seu sistema operacional.
> * Um workspace do [Log Analytics](../../azure-monitor/logs/quick-create-workspace.md)

## <a name="subscribe-to-a-media-services-event-with-logic-app"></a>Assinar um evento dos Serviços de Mídia com o Aplicativo Lógico

1. No portal do Azure, crie um workspace do [Log Analytics](../../azure-monitor/logs/quick-create-workspace.md) caso ainda não o tenha feito. Você precisará da ID do Workspace e de uma das chaves, portanto, mantenha a janela do navegador aberta. Em seguida, abra o portal em outra guia ou janela.

1. Navegue até sua conta dos Serviços de Mídia do Azure e selecione **Eventos**. Isso mostrará todos os métodos para assinar os eventos dos Serviços de Mídia do Azure.
    > [!div class="mx-imgBorder"]
    > ![Portal dos Serviços de Mídia do Azure](media/tutorial-events-log-analytics/select-events-01a.png)

1. Selecione o **ícone de Aplicativos Lógicos** para criar um Aplicativo Lógico. Isso abrirá o Designer de Aplicativo Lógico, no qual você pode criar o fluxo para capturar os eventos e enviá-los por push para o Log Analytics. 
    > [!div class="mx-imgBorder"]
    > ![Criar Aplicativo Lógico](media/tutorial-events-log-analytics/select-logic-app-02.png)

1. Selecione o **ícone +** e o locatário que você deseja usar e clique em Entrar. Você verá um prompt de entrada da Microsoft.
    > [!div class="mx-imgBorder"]
    > ![Conectar-se à Grade de Eventos do Azure](media/tutorial-events-log-analytics/select-event-add-grid-03.png)
![Selecionar o locatário](media/tutorial-events-log-analytics/select-tenant-03a.png)

1. Selecione **Continuar** para assinar os Eventos dos Serviços de Mídia.
    > [!div class="mx-imgBorder"]
    > ![Conectado à Grade de Eventos do Azure](media/tutorial-events-log-analytics/select-continue-04.png)

1. Na lista **Tipo de Recurso**, localize "Microsoft.Media.MediaServices".
    > [!div class="mx-imgBorder"]
    >![Eventos de Recurso dos Serviços de Mídia do Azure](media/tutorial-events-log-analytics/locate-azure-media-services-events-05.png)

1. Selecione o **item do Tipo de Evento**. Haverá uma lista de todos os eventos emitidos pelos Serviços de Mídia do Azure. Selecione os eventos que deseja acompanhar. Adicionar vários tipos de evento. Posteriormente, você fará uma pequena alteração no fluxo do Aplicativo Lógico para armazenar cada tipo de evento em um log do Log Analytics separado e propagará o nome do Tipo de Evento para o nome do Log Analytics dinamicamente.
    > [!div class="mx-imgBorder"]
    > ![Tipo de Evento dos Serviços de Mídia do Azure](media/tutorial-events-log-analytics/select-azure-media-services-event-type-06.png)

1. Selecione **Salvar como**.

1. Dê um nome para o seu Aplicativo Lógico.  O grupo de recursos é selecionado por padrão. Deixe as outras configurações como estão e selecione **Criar**.  Você será retornado à tela inicial do Azure.
    > [!div class="mx-imgBorder"]
    > ![Interface de nomenclatura do aplicativo lógico](media/tutorial-events-log-analytics/give-logic-app-name-06a.png)

## <a name="create-an-action"></a>Criar uma ação

Agora que você se inscreveu nos eventos, crie uma ação.

1. Se o portal tiver levado você de volta à tela inicial, volte para o Aplicativo Lógico que você acabou de criar pesquisando Todos os recursos para o nome do aplicativo.

1. Selecione seu aplicativo e escolha **Designer de aplicativo lógico**. O painel do designer será aberto.

1. Selecione **+ Nova Etapa**.

1. Como você deseja enviar os eventos por push para o serviço do Azure Log Analytics, pesquise "Azure Log Analytics" e selecione o "Coletor de Dados do Azure Log Analytics".
    > [!div class="mx-imgBorder"]
    > ![Azure Log Analytics Data Collector](media/tutorial-events-log-analytics/select-azure-log-analytics-data-collector-07.png)

1. Para se conectar ao workspace do Log Analytics, você precisa da ID do Workspace e de uma Chave de Agente. Abra o portal do Azure em uma nova guia ou janela, navegue até o workspace do Log Analytics criado antes do início deste tutorial.
    > [!div class="mx-imgBorder"]
    > ![ID do workspace do Azure Log Analytics](media/tutorial-events-log-analytics/log-analytics-workspace-id-08.png)

1. No menu à esquerda, localize **Gerenciamento de Agentes** e selecione-o. Isso mostrará as chaves de agente geradas.
    > [!div class="mx-imgBorder"]
    > ![Gerenciamento de Agentes do Azure Log Analytics](media/tutorial-events-log-analytics/select-agents-management-09.png)

1. Copie a *ID do Workspace*.
    > [!div class="mx-imgBorder"]
    > ![Copiar a ID do Workspace](media/tutorial-events-log-analytics/copy-workspace-id.png)

1. Na outra guia ou janela do navegador, no Coletor de Dados do Azure Log Analytics, selecione **Enviar Dados**, forneça um nome para a conexão e cole a *ID do Workspace* no campo **ID do Workspace**.

1. Retorne à guia ou à janela do navegador do Workspace e copie a *Chave do Workspace*.
    > [!div class="mx-imgBorder"]
    > ![Chave primária de gerenciamento de agentes](media/tutorial-events-log-analytics/agents-management-primary-key-10.png)

1. Na outra guia ou janela do navegador, cole a *Chave do Workspace* no campo **Chave do Workspace**.

1. Selecione **Criar**. Agora, você criará o corpo da solicitação JSON e o Nome de Log Personalizado.

1. Selecione o campo **Corpo da solicitação JSON**.  Será exibido um link para **Adicionar conteúdo dinâmico**.

1. Selecione **Adicionar conteúdo dinâmico** e escolha **Tópico**.

1. Faça o mesmo para **Nome de Log Personalizado**.
    > [!div class="mx-imgBorder"]
    > ![Tópico selecionado](media/tutorial-events-log-analytics/topic-selected.png)

1. Selecione **Exibição de Código** do Aplicativo Lógico. Procure as linhas Entradas e Tipo de Log.
    > [!div class="mx-imgBorder"]
    > ![Exibição de código de duas linhas](media/tutorial-events-log-analytics/code-view-two-lines.png)

1. Altere o valor `body` de `"@triggerBody()?['topic']"` para `"@{triggerBody()}"`. Isso é para analisar a mensagem inteira para o Log Analytics.

1. Altere o `Log-Type` de `"@triggerBody()?['topic']"` para `"@replace(triggerBody()?['eventType'],'.','')"`. Isso substituirá "." porque eles não são permitidos nos Nomes de Log do Log Analytics.
    > [!div class="mx-imgBorder"]
    > ![JSON do Aplicativo Lógico após a alteração](media/tutorial-events-log-analytics/changed-lines.png)

1. Selecione **Salvar**.

1. Para verificar, selecione **Designer de aplicativo lógico**.
    > [!div class="mx-imgBorder"]
    > ![Etapas Verificar Corpo e Função](media/tutorial-events-log-analytics/verify-changes-to-json.png)

1. Quando você examinar todos os recursos no grupo de recursos, haverá um Aplicativo Lógico e dois conectores de API do Aplicativo Lógico listados, um para os Eventos e outro para o Log Analytics. Para obter mais informações sobre tópicos do sistema da Grade de Eventos, leia [Tópicos do Sistema da Grade de Eventos](../../event-grid/system-topics.md).
    > [!div class="mx-imgBorder"]
    > ![Confira todos os novos recursos no Grupo de Recursos](media/tutorial-events-log-analytics/contoso-rg-listing.png)

## <a name="test"></a>Teste

Para testar como realmente funciona, crie um Evento ao Vivo nos Serviços de Mídia do Azure. Crie um Evento ao Vivo RTMP e use ffmpeg para enviar por push um fluxo "ao vivo" com base em um arquivo de exemplo .mp4. Depois que o evento for criado, obtenha a URL de ingestão RTMP.

1. Em sua conta dos Serviços de Mídia, selecione **Transmissão ao vivo**.
    > [!div class="mx-imgBorder"]
    > ![Criar um Evento ao Vivo dos Serviços de Mídia do Azure](media/tutorial-events-log-analytics/live-event.png)

1. Selecione **Adicionar evento ao vivo**.

1. Insira um nome no campo **Nome do evento ao vivo**. O campo **Descrição** é opcional.

1. Selecione a codificação de nuvem **Standard**.

1. Selecione **Padrão 720p** para a predefinição de codificação.

1. Selecione o protocolo de entrada **RTMP**.

1. Selecione **Sim** para a URL de entrada persistente.

1. Selecione **Sim** para iniciar o evento quando o evento for criado.

    > [!WARNING]
    > A cobrança será iniciada se você selecionar Sim.  Se você quiser esperar para iniciar o fluxo até *pouco antes de* iniciar o streaming com o FFmpeg, selecione **Não** e lembre-se de iniciar o evento ao vivo.

    > [!div class="mx-imgBorder"]
    > ![Configurações de evento ao vivo](media/tutorial-events-log-analytics/live-event-settings.png)

1. Marque a caixa de seleção **Eu tenho todos os direitos para usar o conteúdo/arquivo...** .

1. Selecione **Examinar + criar**.

1. Examine as configurações e selecione **Criar**.  A listagem de eventos ao vivo será exibida e a URL de Ingestão de eventos ao vivo será mostrada.

1. Copie a **URL de Ingestão** para a área de transferência.

1. Selecione o **evento ao vivo** na listagem para ver o Modo de exibição do produtor.

### <a name="stream-with-ffmpeg-cli"></a>Fazer streaming com a CLI do FFmpeg

1. Use a linha de comando a seguir.

    ```AzureCLI
    ffmpeg -i <localpathtovideo> -map 0 -c:v libx264 -c:a copy -f flv <ingestURL>/mystream
    ```

1. Altere `<ingestURL>` para a URL de Ingestão que você copiou para a área de transferência.
1. Altere `<localpathtovideo>` para o caminho local do arquivo que você deseja transmitir de FFmpeg.
1. Adicione um nome exclusivo ao final, por exemplo, `mystream`.
1. Ajuste a linha de comando para refletir o arquivo de origem de teste e qualquer outra variável do sistema.
1. Execute o comando. Após alguns segundos, o streaming do player de "Modo de exibição do produtor" deverá ser iniciado. Atualize o player se o vídeo não for exibido automaticamente.

    > [!div class="mx-imgBorder"]
    > ![Verificar a ingestão adequada do vídeo no Player de Visualização do Produtor](media/tutorial-events-log-analytics/live-event-producer-view.png)

## <a name="verify-the-events"></a>Verificar os eventos

Com a transmissão ao vivo, os Serviços de Mídia do Azure estão emitindo vários eventos que estão disparando o fluxo do Aplicativo Lógico. Para verificar, navegue até o Aplicativo Lógico e determine se há gatilhos sendo acionados pelos eventos dos Serviços de Mídia.

1. Navegue até a página Visão Geral do Aplicativo Lógico. Você deverá ver o "Histórico de Execuções" listando trabalhos que foram concluídos com êxito.
    > [!div class="mx-imgBorder"]
    > ![Verificar a execução bem-sucedida do trabalho no Aplicativo Lógico](media/tutorial-events-log-analytics/run-history.png)

1. Selecione um trabalho bem-sucedido. Os detalhes do trabalho durante o runtime são mostrados.
1. Selecione **Enviar Dados** para expandi-lo. Nesse caso, o evento `MicrosoftMediaLiveEventEncoderConnected` mostra que eles foram capturados e que o corpo foi analisado. Isso é o que é enviado por push para o workspace do Azure Log Analytics.
    > [!div class="mx-imgBorder"]
    > ![Confira Enviar Dados](media/tutorial-events-log-analytics/verify-send-data.png)

## <a name="verify-the-logs"></a>Verificar os logs

1. Navegue até o workspace do Log Analytics criado anteriormente.

1. Selecione **Logs**.
1. Feche a pop-up Consultas de exemplo.
1. Haverá uma listagem de Logs Personalizados. Selecione a seta para baixo para expandi-la. Lá, você verá o nome do evento `MicrosoftMediaLiveEventEncoderConnected`.
1. Selecione o nome do evento para expandi-lo.
1. Quando você selecionar o ícone "olho", ele mostrará uma visualização do resultado da consulta.
1. Selecione **Ver no editor de consultas** e escolha o item na listagem **UTC TimeGenerated** para expandi-la e ver os dados brutos.

![Confira a saída detalhada do evento no Log Analytics](media/tutorial-events-log-analytics/raw-data.png)

## <a name="delete-resources"></a>Excluir recursos

Se você não quiser continuar a usar os recursos criados durante este tutorial, exclua todos os recursos no grupo de recursos ou a cobrança continuará ocorrendo.

## <a name="next-steps"></a>Próximas etapas

Você pode criar consultas diferentes e salvá-las. Elas podem ser adicionadas ao [Painel do Azure](../../azure-monitor/visualize/tutorial-logs-dashboards.md).