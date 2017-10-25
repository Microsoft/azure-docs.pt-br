---
title: "Notas de versão da extensão do Visual Studio para análise do desenvolvedor"
description: "As atualizações mais recentes das ferramentas do Visual Studio para análise do desenvolvedor"
services: application-insights
documentationcenter: 
author: acearun
manager: carmonm
ms.assetid: 2001db30-efc5-417a-a413-93c1b218975f
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: bwren
ms.openlocfilehash: 2e6987d30b56407559f54c02817ee94eff8df46b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="release-notes-for-developer-analytics-tools"></a>Notas de versão para ferramentas de análise do desenvolvedor

## <a name="version-718-visual-studio-2015"></a>Versão 7.18 (Visual Studio 2015)

* Notificações do sistema reformuladas.
* Filtros “Não” na exibição Detalhes de eventos na Pesquisa do Application Insights.
* Correções de bug

## <a name="version-86-visual-studio-2017-rtw-and-rc4-and-version-717-visual-studio-2015"></a>Versão 8.6 (Visual Studio 2017 RTW e RC4) e versão 7.17 (Visual Studio 2015)

* Agora são feitas anotações nos dados, no Metrics Explorer no portal do Azure, que marcam quando o aplicativo é publicado do Visual Studio
* Agora foram adicionados marcadores às barras de rolagem de arquivos de código, correspondentes aos avisos do CodeLens em vermelho e amarelo do Application Insights
* Atualização de informações sobre preço na janela Configuração
* Correções de bug

[Consulte as observações detalhadas aqui](https://www.visualstudio.com/news/releasenotes/vs2017-relnotes#devanalytics)

## <a name="version-716-visual-studio-2015"></a>Versão 7.16 (Visual Studio 2015)

* Correções de bug

## <a name="version-85-visual-studio-2017-rc3-and-version-715-visual-studio-2015"></a>Versão 8.5 (Visual Studio 2017 RC3) e versão 7.15 (Visual Studio 2015)

* O CodeLens agora mostra dados de telemetria em tempo real e dados de depuração em projetos que enviam dados para um recurso do Application Insights
* Agora, as informações sobre preço do Application Insights são mostradas na janela Configuração
* Agora, o CodeLens para solicitações e exceções dá suporte a projetos do ASP.NET escritos em Visual Basic
* Agora, a Pesquisa do Application Insights mostra contagens de eventos não amostrados de eventos que foram amostrados
* Correções de bug

## <a name="version-714-visual-studio-2015"></a>Versão 7.14 (Visual Studio 2015)

* Suporte de pesquisa para disponibilidade (teste na Web) e eventos de exibição de página
* Suporte de tendências para disponibilidade (teste na Web) e eventos de exibição de página
* Ferramentas de Diagnóstico e rótulo de detalhes do evento para Amostragem Adaptável do SDK
* Correções de bug

## <a name="version-712-visual-studio-2015"></a>Versão 7.12 (Visual Studio 2015)

* Novo formato de notificação de publicação
* Correções de bug

## <a name="version-84-visual-studio-2017-rc2-and-version-711-visual-studio-2015"></a>Versão 8.4 (Visual Studio 2017 RC2) e versão 7.11 (Visual Studio 2015)

* O CodeLens mostra solicitações de sessões de depuração local para projetos com o SDK do Application Insights
* O CodeLens pode levá-lo diretamente ao Application Analytics para que você veja o impacto do usuário
* Inserção de um JavaScript para coletar as exibições de página
* Correções de bug

## <a name="version-710-visual-studio-2015"></a>Versão 7.10 (Visual Studio 2015)

* Novo design da janela Configuração do Application Insights
* Correções de bug

## <a name="version-79-visual-studio-2015"></a>Versão 7.9 (Visual Studio 2015)

* O CodeLens mostra as exceções ocorridas durante as sessões de depuração local para projetos com o SDK do Application Insights
* Correções de bug

## <a name="version-83-visual-studio-2017-rc-and-version-78-visual-studio-2015"></a>Versão 8.3 (Visual Studio 2017 RC) e versão 7.8 (Visual Studio 2015)

* Nova experiência de adição do Application Insights à janela Configuração
* Correções de bug

## <a name="version-77-visual-studio-2015"></a>Versão 7.7 (Visual Studio 2015)

* Mapeamentos mais precisos de eventos de telemetria para métodos usando o roteamento personalizado do ASP.NET
* Correções de bug

## <a name="version-76-visual-studio-2015"></a>Versão 7.6 (Visual Studio 2015)

* Análise dos eventos envolvidos em uma operação na nova guia Acompanhar Operação em eventos na ferramenta Pesquisa
* Correções de bug

## <a name="version-75-visual-studio-2015"></a>Versão 7.5 (Visual Studio 2015)

* Informações de telemetria de produção para solicitações nas Ferramentas de Diagnóstico
* Criação de Item de Trabalho em Itens Relacionados na ferramenta de Pesquisa
* Correções de bug

## <a name="version-74-visual-studio-2015"></a>Versão 7.4 (Visual Studio 2015)

* Agora o painel de filtro em Tendências é redimensionável
* Correções de bug

## <a name="version-73-visual-studio-2015"></a>Versão 7.3 (Visual Studio 2015)

* Solicitações no CodeLens
* Janela Configuração
* Atualização do SDK do HockeyApp para v4.2.2
* Correções de bug

## <a name="version-72-visual-studio-2015"></a>Versão 7.2 (Visual Studio 2015)

* Correções de bug

## <a name="version-71-visual-studio-2015"></a>Versão 7.1 (Visual Studio 2015)

* Indicador Preparação de Telemetria em Tendências do Application Insights
* Correções de bug

## <a name="version-70"></a>Versão 7.0
### <a name="azure-application-insights-trends"></a>Tendências do Azure Application Insights
O Azure Application Insights é uma nova ferramenta no Visual Studio que você pode usar para ajudá-lo a analisar como seu aplicativo funciona ao longo do tempo. Para começar, no botão da barra de ferramentas do **Application Insights** ou na janela Pesquisar do Application Insights, escolha **Explorar Tendências de Telemetria**. Ou, no menu **Exibição**, clique em **Outras Janelas** e em **Tendências do Application Insights**. Escolha uma das cinco consultas comuns para começar. Você pode analisar conjuntos de dados diferentes com base em tipos de telemetria, intervalos de tempo e outras propriedades. Para encontrar anomalias em seus dados, escolha uma das opções de anomalias na lista suspensa **Tipo de Exibição** . As opções de filtragem na parte inferior da janela facilitam o aprimoramento de subconjuntos específicos de sua telemetria.

![Tendências do Application Insights](./media/app-insights-release-notes-vsix/Trends.png)

### <a name="exceptions-in-codelens"></a>Exceções em CodeLens
A telemetria de exceção agora é exibida no CodeLens. Se você conectou seu projeto ao serviço Application Insights, verá o número de exceções que ocorreram em cada método na produção nas últimas 24 horas. No CodeLens, você pode ir para Pesquisar ou Tendências a fim de investigar as exceções mais detalhadamente.

![Exceções em CodeLens](./media/app-insights-release-notes-vsix/ExceptionsCodeLens.png)

### <a name="aspnet-core-support"></a>Suporte do ASP.NET Core
O Application Insights agora dá suporte a projetos do ASP.NET Core RC2 no Visual Studio. Você pode adicionar o Application Insights a novos projetos do ASP.NET Core RC2 da caixa de diálogo **Novo Projeto** , como mostra a captura de tela a seguir. Ou você pode adicioná-lo a um projeto existente clicando com o botão direito do mouse no projeto no Gerenciador de Soluções e clicando em **Adicionar Application Insights Telemetry**.

![Suporte do ASP.NET Core](./media/app-insights-release-notes-vsix/NetCoreSupport.png)

Também há novo suporte a projetos ASP.NET 5 RC1 e ASP.NET Core RC2 na janela Ferramentas de Diagnóstico. Você verá eventos do Application Insights como solicitações e exceções do seu aplicativo ASP.NET ao depurar localmente em seu computador. Em cada evento, clique em **Pesquisa** para fazer busca detalhada e saber mais.

![Suporte a Ferramentas de Diagnóstico](./media/app-insights-release-notes-vsix/DiagnosticTools.png)

### <a name="hockeyapp-for-universal-windows-apps"></a>HockeyApp para aplicativos universais do Windows
Além dos comentários de usuário e da distribuição beta, o HockeyApp fornece relatórios de falha simbolizados para seus aplicativos Universais do Windows. Facilitamos ainda mais a adição do SDK do HockeyApp: clique com o botão direito do mouse no projeto Universal do Windows e clique em **Hockey App – Habilitar Análise de Falhas**. Isso instala o SDK, configura a coleta de falhas e provisiona um recurso HockeyApp na nuvem sem carregar seu aplicativo no serviço HockeyApp.

Outros novos recursos:

* Fizemos a experiência da Pesquisa do Application Insights mais rápida e mais intuitiva. Agora, intervalos de tempo e filtros de detalhe são aplicados automaticamente conforme você os seleciona.
* Além disso, também na Pesquisa do Application Insights, agora há uma opção para ir para o código diretamente da telemetria de solicitação.
* Fizemos aprimoramentos à experiência de entrada do HockeyApp.
* Nas Ferramentas de Diagnóstico, as informações de telemetria de produção para exceções são exibidas.

## <a name="version-52"></a>Versão 5.2
Estamos felizes em anunciar a introdução de cenários do HockeyApp no Visual Studio. A primeira integração está em distribuição beta dos aplicativos Universais do Windows e do Windows Forms no Visual Studio.

Com a distribuição beta, você pode carregar versões anteriores dos aplicativos no HockeyApp para distribuição a um subconjunto seleto de clientes ou testadores. Essa distribuição, combinada com os recursos de comentários dos usuário e coleção de falhas do HockeyApp, pode fornecer a você informações valiosas sobre o aplicativo antes de fazer um amplo lançamento. Você pode usar essas informações para solucionar problemas com seu aplicativo para poder evitar ou minimizar problemas futuros, como classificações de aplicativo baixas, comentários negativos e assim por diante.

Confira como é simples carregar compilações na distribuição beta no Visual Studio.

### <a name="universal-windows-apps"></a>Aplicativos universais Windows
O menu de contexto do nó de um projeto de aplicativo Universal do Windows agora inclui uma opção para carregar sua compilação no HockeyApp.

![Menu de contexto do projeto para Aplicativos Universais do Windows](./media/app-insights-release-notes-vsix/UniversalContextMenu.png)

Escolha o item e a caixa de diálogo de carregamento do HockeyApp será exibida. Você precisará de uma conta do HockeyApp para carregar sua compilação. Se você é um novo usuário, não se preocupe. Criar uma conta é um processo simples.

Ao se conectar, você verá o formulário de carregamento no diálogo.

![Carregar caixa de diálogo para aplicativos Universais do Windows](./media/app-insights-release-notes-vsix/UniversalUploadDialog.png)

Escolha o conteúdo a ser carregado (um arquivo appxbundle ou appx) e escolha as opções de versão no assistente. Se preferir, você poderá adicionar notas de versão na página seguinte. Escolha **Concluir** para começar a carregar.

Quando o carregamento estiver concluído, uma notificação do sistema para o HockeyApp com confirmação e um link para o aplicativo no portal do HockeyApp serão exibidos.

![Carregar a notificação de conclusão](./media/app-insights-release-notes-vsix/UploadComplete.png)

É isso! Você acabou de carregar uma compilação para distribuição Beta com apenas alguns cliques.

Você pode gerenciar seu aplicativo de várias maneiras no portal do HockeyApp. Isso inclui convidar usuários, exibir relatórios de falha e comentários, alterar os detalhes e assim por diante.

![Portal do HockeyApp](./media/app-insights-release-notes-vsix/HockeyAppPortal.png)

Confira a [Base de Dados de Conhecimento do HockeyApp](http://support.hockeyapp.net/kb/app-management-2) para obter mais detalhes sobre o gerenciamento de aplicativo.

### <a name="windows-forms-apps"></a>Aplicativos do Windows Forms
O menu de contexto do nó de um projeto do Windows Forms agora inclui uma opção para carregar sua compilação no HockeyApp.

![Menu de contexto do projeto para aplicativos do Windows Forms](./media/app-insights-release-notes-vsix/WinFormContextMenu.png)

Isso abre o diálogo de carregamento do HockeyApp, que é semelhante ao de um aplicativo universal do Windows.

![Caixa de diálogo Upload para aplicativos do Windows Forms](./media/app-insights-release-notes-vsix/WinFormsUploadDialog.png)

Observe um novo campo nesse assistente para especificar a versão do aplicativo. Para aplicativos universais do Windows, as informações são preenchidas com o manifesto. Aplicativos do Windows Forms, infelizmente, não têm um equivalente desse recurso. Você precisará especificá-los manualmente.

O restante do fluxo é semelhante aos Aplicativos Universais do Windows: escolha opções de versão e lançamento, adição de notas de versão, carregamento e gerenciamento no portal do HockeyApp.

É simples assim. Experimente e conte-nos o que você acha.

## <a name="version-43"></a>Versão 4.3
### <a name="search-telemetry-from-local-debug-sessions"></a>Pesquisar a telemetria nas sessões de depuração local
Com esta versão, você pode pesquisar a telemetria do Application Insights gerada na sessão de depuração do Visual Studio. Antes, você só podia usar a pesquisa se tivesse registrado seu aplicativo no Application Insights. Agora, seu aplicativo precisa apenas ter o SDK do Application Insights instalado para pesquisar a telemetria local.

Se você tem um aplicativo ASP.NET com o SDK do Application Insights, execute as etapas a seguir para usar a pesquisa.

1. Depure o aplicativo.
2. Abra a Pesquisa do Application Insights com uma das seguintes maneiras:
   * Ou, no menu **Exibição**, clique em **Outras Janelas** e em **Pesquisa do Application Insights**.
   * Clicar no botão de barra de ferramentas do **Application Insights** .
   * No Gerenciador de Soluções, expanda **ApplicationInsights.config** e clique em **Pesquisar telemetria da sessão de depuração**.
3. Se ainda não tiver se inscrito no Application Insights, a janela Pesquisar será aberta no modo de telemetria da sessão de depuração.
4. Clique no ícone **Pesquisar** para ver a telemetria local.

![Upload concluído](./media/app-insights-release-notes-vsix/LocalSearch.png)

## <a name="version-42"></a>Versão 4.2
Nesta versão, adicionamos recursos para facilitar a pesquisa de dados no contexto de eventos, com a capacidade de saltar para o código de mais eventos de dados e uma experiência simples para enviar dados de log para o Application Insights. Essa extensão é atualizada mensalmente. Caso tenha comentários ou solicitações de recurso, envie-os para aidevtools@microsoft.com.

### <a name="no-click-logging-experience"></a>Experiência de registro em log sem cliques
Se você já estiver usando System.Diagnostics.Tracing, log4net ou NLog, não precisará se preocupar com mover todos os rastreamentos para o Application Insights. Nesta versão, integramos os adaptadores de registro em log do Application Insights à experiência de configuração normal.
Se você já tiver uma dessas estruturas de registros de log configuradas, a seção a seguir descreverá como obtê-la.
**Se já tiver adicionado o Application Insights:**

1. Clique com botão direito do mouse no nó do projeto e clique em **Application Insights**. Em seguida, clique em **Configurar Application Insights**. Verifique se aparece a opção de adicionar o adaptador correto à janela de configuração.
2. Com alternativa, quando você compilar a solução, observe a janela pop-up que aparece no canto superior direito da tela e clique em **Configurar**.

![Notificação de registro em log](./media/app-insights-release-notes-vsix/LoggingToast.png)

Depois de instalar o adaptador de log, execute o aplicativo e verificar se visualiza os dados na guia de ferramentas de diagnóstico, da seguinte forma:

![Rastreamentos](./media/app-insights-release-notes-vsix/Traces.png)

### <a name="jump-to-or-find-the-code-where-the-telemetry-event-property-is-emitted"></a>Salte até ou localize o código em que a propriedade de evento de telemetria é emitida
Com a nova versão, o usuário pode clicar em qualquer valor nos detalhes do evento e isso pesquisará uma cadeia de caracteres correspondente na solução aberta atual. Os resultados serão exibidos na lista "Encontrar Resultados" do Visual Studio, conforme mostrado abaixo:

![Localizar correspondência](./media/app-insights-release-notes-vsix/FindMatch.png)

### <a name="new-search-window-for-when-you-are-not-signed-in"></a>Nova janela de pesquisa para quando você não está conectado
Melhoramos a aparência da janela da Pesquisa do Application Insights para ajudá-lo a procurar os dados enquanto o aplicativo está em produção.

![Janela de pesquisa](./media/app-insights-release-notes-vsix/SearchWindow.png)

### <a name="see-all-telemetry-events-associated-with-the-event"></a>Veja todos os eventos de telemetria associados ao evento
Adicionamos uma nova guia com consultas predefinidas para todos os dados relacionados ao evento de telemetria que o usuário está exibindo, ao lado da guia para obter detalhes do evento. Por exemplo, uma solicitação tem um campo chamado **ID da Operação**. Todos os eventos associados a essa solicitação têm o mesmo valor para **ID da Operação**. Se ocorrer uma exceção enquanto a operação está processando a solicitação, ela recebe a mesma ID de operação que a solicitação para facilitar a localização. Se você estiver vendo uma solicitação, clique em **Toda a telemetria para esta operação** para abrir uma nova guia que exibe os novos resultados da pesquisa.

![Itens relacionados](./media/app-insights-release-notes-vsix/RelatedItems.png)

### <a name="forward-and-back-history-in-search"></a>Avançar e Voltar histórico na Pesquisa
Agora você pode ir e voltar entre os resultados da pesquisa.

![Voltar](./media/app-insights-release-notes-vsix/GoBAck.png)

## <a name="version-41"></a>Versão 4.1
Esta versão vem com uma quantidade de novos recursos e atualizações. Você precisa ter a Atualização 1 instalada para instalar esta versão.

### <a name="jump-from-an-exception-to-method-in-source-code"></a>Saltar de uma exceção para o método no código-fonte
Agora, se você exibir as exceções do seu aplicativo de produção na janela da Pesquisa do Application Insights, poderá ir para o método em seu código onde a exceção está ocorrendo. Você só precisa ter o projeto correto carregado e o Application Insights cuida do resto! (Para saber mais sobre a janela da Pesquisa do Application Insights, confira as notas de versão para a versão 4.0 nas seções a seguir.)

Como ele funciona? Você poderá usar a Pesquisa do Applications Insights mesmo quando uma solução não estiver aberta. A área de rastreamento de pilha exibe uma mensagem informativa e muitos dos itens no rastreamento da pilha não ficam disponíveis.

Se as informações de arquivo estiverem disponíveis, alguns itens poderão ser links, mas o item de informação de solução ainda estará visível.

Se você clicar no hiperlink, poderá saltar para o local do método selecionado em seu código. Pode haver uma diferença no número de versão, mas o recurso, para ir para a versão correta do código, será apresentado em versões posteriores.

![Clicar em detalhes da exceção](./media/app-insights-release-notes-vsix/jumptocode.png)

### <a name="new-entry-points-to-the-search-experience-in-solution-explorer"></a>Novos pontos de entrada para a experiência de pesquisa no Gerenciador de Soluções
Agora você pode acessar a Pesquisa usando o Gerenciador de Soluções.

![Pesquisar no Gerenciador de Soluções](./media/app-insights-release-notes-vsix/searchentry.png)

### <a name="displays-a-notification-when-publish-is-completed"></a>Exibe uma notificação quando a publicação é concluída
Uma caixa de diálogo pop-up será exibida depois que o projeto for publicado online para que você possa exibir os dados do Application Insights em produção.

![Publicar notificação de conclusão](./media/app-insights-release-notes-vsix/publishtoast.png)

## <a name="version-40"></a>Versão 4.0
### <a name="search-application-insights-data-from-within-visual-studio"></a>Pesquise dados do Application Insights no Visual Studio
Assim como a função de pesquisa no portal do Application Insights, agora no Visual Studio você pode filtrar e pesquisar tipos de eventos, valores de propriedade e texto e inspecionar eventos individuais.

![Janela de pesquisa](./media/app-insights-release-notes-vsix/search.png)

### <a name="see-data-coming-from-your-local-computer-in-diagnostic-tools"></a>Confira dados provenientes do computador local nas Ferramentas de Diagnóstico
Você pode exibir sua telemetria, além de outros dados de depuração, na página Ferramentas de Diagnóstico do Visual Studio. Há suporte apenas para ASP.NET 4.5.

![Página Ferramentas de Diagnóstico](./media/app-insights-release-notes-vsix/diagtools.png)

### <a name="add-the-sdk-to-your-project-without-signing-in-to-azure"></a>Adicione o SDK ao seu projeto sem entrar no Azure
Você não precisa mais entrar no Azure para adicionar pacotes do Application Insights ao seu projeto, seja na caixa de diálogo **Novo Projeto** ou no menu de contexto do projeto. Se você entrar, o SDK será instalado e configurado para enviar telemetria ao portal, como antes. Se você não entrar, o SDK será adicionado ao seu projeto e irá gerar telemetria para o hub de diagnóstico. Você pode configurá-lo mais tarde, se desejar.

![Caixa de diálogo Novo Projeto](./media/app-insights-release-notes-vsix/newproject.png)

### <a name="device-support"></a>Suporte a dispositivos
Em *Connect();* 2015, [anunciamos](https://azure.microsoft.com/blog/deep-diagnostics-for-web-apps-with-application-insights/) que a nossa experiência de desenvolvedores para dispositivos móveis é o HockeyApp. O HockeyApp ajuda a distribuir suas versões beta para testadores, a coletar e a analisar todas as falhas do aplicativo e a coletar comentários diretamente de seus clientes.
O HockeyApp dá suporte a seu aplicativo em qualquer plataforma que você escolher para compilá-lo, seja iOS, Android ou Windows, ou uma solução de plataforma cruzada, como Unity, Cordova ou Xamarin.

Em versões futuras da extensão Application Insights, vamos introduzir uma experiência mais integrada entre o HockeyApp e o Visual Studio. Por enquanto, você pode começar a usar o HockeyApp simplesmente adicionando a referência do NuGet. Para saber mais, confira a [documentação](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone) .
