---
title: Monitoramento de desempenho de aplicativos Web - Azure Application Insights | Microsoft Docs
description: Como o Application Insights se encaixa no ciclo do devOps
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 479522a9-ff5c-471e-a405-b8fa221aedb3
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 03/14/2017
ms.author: bwren
ms.openlocfilehash: de94633cabaa7a1562a5a4839a8a8924da91a283
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deep-diagnostics-for-web-apps-and-services-with-application-insights"></a>Diagnósticos detalhados para aplicativos Web e serviços com o Application Insights
## <a name="why-do-i-need-application-insights"></a>Por que eu preciso do Application Insights?
O Application Insights monitora seu aplicativo Web em execução. Ele informa sobre problemas de desempenho e falhas e ajuda você a analisar como os clientes usam seu aplicativo. Ele funciona para aplicativos executados em várias plataformas (ASP.NET, J2EE, Node.js,...) e é hospedado na nuvem ou localmente. 

![Aspectos da complexidade do fornecimento de aplicativos Web](./media/app-insights-devops/010.png)

É essencial monitorar um aplicativo moderno enquanto ele está em execução. Acima de tudo, você deseja detectar falhas antes que a maioria de seus clientes o faça. Você também deseja descobrir e corrigir problemas de desempenho que, embora não sejam catastróficos, talvez deixem as coisas mais lentas ou causem alguma inconveniência para seus usuários. E quando o sistema estiver funcionando bem, você vai querer saber o que os usuários estão fazendo com ele: eles estão usando o recurso mais recente? Eles estão tendo êxito com ele?

Os aplicativos Web modernos são desenvolvidos em um ciclo de fornecimento contínuo: liberar um novo recurso ou aperfeiçoamento; observar como ele funciona para os usuários; planejar o próximo incremento de desenvolvimento com base nesse conhecimento. Uma parte importante deste ciclo é a fase de observação. O Application Insights fornece as ferramentas para monitorar o desempenho e uso de um aplicativo Web.

O aspecto mais importante deste processo é o diagnóstico e a detecção. Falhas em aplicativos constituem uma indicação de que negócios podem estar sendo perdidos. A função principal de uma estrutura de monitoramento é, portanto, detectar falhas de forma confiável, notificá-lo imediatamente e apresentar a você as informações necessárias para diagnosticar o problema. Isso é exatamente o que o Application Insights faz.

### <a name="where-do-bugs-come-from"></a>De onde vêm os bugs?
Falhas em sistemas da web geralmente surgem de problemas de configuração ou interações ruins entre seus vários componentes. A primeira tarefa ao lidar com um incidente de site ativo é, portanto, identificar o local do problema: qual componente ou relação é a causa?

Alguns de nós, aqueles com cabelos grisalhos, podem se lembrar de uma era mais simples na qual um programa de computador era executado em um computador. Os desenvolvedores o testavam exaustivamente antes de enviá-lo e após o envio, raramente viam ou pensavam sobre ele novamente. Os usuários tinham que lidar com os bugs remanescentes por muitos anos. 

Hoje em dia tudo é diferente. Seu aplicativo é executado em uma infinidade de diferentes dispositivos e é impossível garantir exatamente o mesmo comportamento em todos eles. Hospedar aplicativos na nuvem significa que os bugs podem ser corrigidos rapidamente, mas também significa uma competição contínua e a expectativa de novos recursos em intervalos frequentes. 

Nessas condições, a única maneira de manter um controle firme sobre a contagem de bugs é a realização de testes de unidade automatizados. Seria impossível testar tudo novamente de forma manual em cada entrega. O teste de unidade agora é uma parte comum do processo de build. Ferramentas como o Xamarin Test Cloud ajudam oferecendo o teste de interface do usuário automatizado a várias versões de navegador. Esses regimes de teste nos permitem esperar que a taxa de bugs encontrados dentro de um aplicativo possa ser mantida no mínimo.

Os aplicativos Web típicos têm muitos componentes em tempo real. Além do cliente (em um aplicativo de navegador ou de dispositivo) e o servidor Web, é provável que seja processamento substancial de back-end. Talvez o back-end seja um pipeline de componentes ou uma coleção menos rígida de partes de colaboração. E muitos deles não estarão sob seu controle, são serviços externos dos quais você depende.

Em configurações como essas, pode ser difícil e antieconômico realizar o teste para, ou prever, todos os modos de falha que não o próprio sistema ativo. 

### <a name="questions-"></a>Perguntas...
Algumas perguntas que fazemos quando estamos desenvolvendo um sistema Web:

* Meu aplicativo está falhando? 
* O que aconteceu exatamente? Se ocorreu uma falha em uma solicitação, desejo saber como isso aconteceu. Precisamos de um rastreamento de eventos...
* Meu aplicativo é rápido o suficiente? Quanto tempo ele demora para responder a solicitações típicas?
* O servidor pode lidar com a carga? Ao aumentar a taxa de solicitações, o tempo de resposta se mantém constante?
* Qual o nível de resposta das minhas dependências, como as APIs REST, os bancos de dados e outros componentes que o aplicativo chama. Especificamente, a lentidão do sistema é resultante do meu componente ou estou recebendo respostas lentas de outra pessoa?
* Meu aplicativo está ativo ou inativo? Ele pode ser visto em todo o mundo? Avise-me se ele parar...
* Qual é a causa raiz? A falha foi no meu componente ou em uma dependência? É um problema de comunicação?
* Quantos usuários foram afetados? Se tiver mais de um problema para resolver, qual será o mais importante?

## <a name="what-is-application-insights"></a>O que é o Application Insights?
![Fluxo de trabalho básico do Application Insights](./media/app-insights-devops/020.png)

1. O Application Insights instrumentaliza seu aplicativo e envia a telemetria sobre ele enquanto o aplicativo está em execução. Você pode compilar o SDK do Application Insights no aplicativo ou você pode aplicar a instrumentação no tempo de execução. O primeiro método é mais flexível, uma vez que você pode adicionar sua própria telemetria aos módulos regulares.
2. A telemetria é enviada para o portal do Application Insights, no qual é armazenada e processada. (Embora o Application Insights esteja hospedado no Microsoft Azure, ele pode monitorar quaisquer aplicativos Web, não apenas os do Azure.)
3. A telemetria é apresentada na forma de gráficos e tabelas de eventos.

Há dois tipos principais de telemetria: instâncias agregadas e brutas. 

* Os dados de instância incluem, por exemplo, um relatório de uma solicitação que foi recebida pelo seu aplicativo Web. Você pode localizar e inspecionar os detalhes de uma solicitação usando a ferramenta Pesquisar no portal do Application Insights. A instância pode incluir dados como quanto tempo levou para seu aplicativo responder à solicitação, bem como a URL solicitada, a localização aproximada do cliente e outros dados.
* Os dados agregados incluem o número de eventos por unidade de tempo, para que você possa comparar a taxa de solicitações com os tempos de resposta. Ele também inclui as médias de métricas, como tempos de resposta de solicitação.

As principais categorias de dados são:

* Solicitações para seu aplicativo (normalmente solicitações HTTP), com os dados na URL, o tempo de resposta e o êxito ou falha.
* Dependências, chamadas REST e SQL feitas pelo seu aplicativo, também com o URI, tempos de resposta e êxito
* Exceções, incluindo os rastreamentos de pilha.
* Dados de exibição de página, que vêm de navegadores dos usuários.
* As métricas, como contadores de desempenho, bem como as métricas que você escreve. 
* Eventos personalizados que você pode usar para controlar os eventos de negócios
* Rastreamentos de log usados para depuração.

## <a name="case-study-real-madrid-fc"></a>Estudo de caso: Real Madrid F.C.
O serviço Web do [Clube de Futebol Real Madrid](http://www.realmadrid.com/) atende a aproximadamente 450 milhões de fãs em todo o mundo. Os fãs o acessam por navegadores da Web e os aplicativos móveis do Clube. Os fãs podem não apenas podem reservar ingressos, mas também acessar informações e videoclipes sobre os resultados, jogadores e próximos jogos. Eles podem pesquisar com filtros como os números de gols marcados. Também há links para mídia social. A experiência do usuário altamente personalizada e é projetada como uma comunicação bidirecional para envolver os fãs.

A solução [é um sistema de serviços e aplicativos no Microsoft Azure](https://www.microsoft.com/en-us/enterprise/microsoftcloud/realmadrid.aspx). A escalabilidade é um requisito fundamental: o tráfego é variável e pode atingir volumes muito grandes durante e próximo das partidas.

Para o Real Madrid, é vital monitorar o desempenho do sistema. O Azure Application Insights fornece uma exibição abrangente de todo o sistema, garantindo um nível de serviço elevado e confiável. 

O Clube obtém ainda uma compreensão detalhada dos fãs: onde estão (apenas 3% estão na Espanha), que interesse têm nos jogadores, resultados históricos e jogos futuros e como eles respondem aos resultados das partidas.

A maioria desses dados de telemetria é coletada automaticamente sem nenhum código adicionado, o que simplificou a solução e reduziu a complexidade operacional.  Para o Real Madrid, o Application Insights lida com 3,8 bilhões de pontos de telemetria por mês.

O Real Madrid usa o módulo do Power BI para exibir sua telemetria.

![Exibição do Power BI de telemetria do Application Insights](./media/app-insights-devops/080.png)

## <a name="smart-detection"></a>Detecção inteligente
O [diagnóstico proativo](app-insights-proactive-diagnostics.md) é um recurso recente. Sem qualquer configuração especial determinada por você, o Application Insights detecta e o alerta automaticamente sobre aumentos incomuns nas taxas de falhas do aplicativo. Ele é inteligente o suficiente para ignorar um histórico de falhas eventuais e aumentos que são proporcionais a um aumento das solicitações. Por exemplo, se houver uma falha em um dos serviços dos quais você depende ou se o novo build que acabou de ser implantado não estiver funcionando muito bem, você saberá assim que consultar seu email. (E há webhooks para que você possa disparar outros aplicativos.)

Outro aspecto desse recurso executa uma análise aprofundada diária da sua telemetria, procurando padrões incomuns de desempenho que são difíceis de descobrir. Por exemplo, ela pode localizar lentidão no desempenho associada a uma determinada área geográfica ou com a versão de navegador específico.

Em ambos os casos, o alerta não apenas informa os sintomas descobertos, mas também fornece dados que serão necessários para ajudar a diagnosticar o problema, como relatórios de exceção relevantes.

![Email do diagnóstico proativo](./media/app-insights-devops/030.png)

O cliente Samtec disse: "Durante uma migração de recurso recente, encontramos um banco de dados subdimensionado que estava atingindo seus limites de recursos e causando tempos limite. Os alertas de detecção proativos foram emitidos enquanto realizávamos a triagem do problema, quase em tempo real, conforme anunciado. Esse alerta juntamente com os alertas da plataforma Azure nos ajudou a corrigir o problema quase instantaneamente. O tempo de inatividade total foi menor que 10 minutos."

## <a name="live-metrics-stream"></a>Live Metrics Stream
A implantação do build mais recente pode causar certa ansiedade. Se houver algum problema, você desejará saber sobre ele imediatamente para que possa recuar, se necessário. O Live Metrics Stream fornece métricas importantes com uma latência de aproximadamente um segundo.

![Métricas em tempo real](./media/app-insights-devops/040.png)

E permite que você inspecione imediatamente uma amostra de quaisquer falhas ou exceções.

![Eventos de falha em tempo real](./media/app-insights-devops/live-stream-failures.png)

## <a name="application-map"></a>Mapa de aplicativo
O mapa de aplicativo detecta automaticamente a topologia do aplicativo, colocando as informações de desempenho sobre ela para permitir que você identifique facilmente os afunilamentos de desempenho e fluxos problemáticos em ambientes distribuídos. Ele permite que você descubra dependências de aplicativos nos Serviços do Azure. Você pode fazer a triagem do problema entendendo se ele é relacionado ao código ou à dependência e, de um único lugar, detalhar a experiência de diagnóstico relacionada. Por exemplo, seu aplicativo pode estar falhando devido à degradação do desempenho na camada SQL. Com o mapa de aplicativo, você pode vê-lo imediatamente e detalhar a experiência do SQL Index Advisor ou Insights de Consulta.

![Mapa de aplicativo](./media/app-insights-devops/050.png)

## <a name="application-insights-analytics"></a>Análise do Application Insights
Com a [Análise](app-insights-analytics.md), você pode escrever consultas arbitrárias em uma poderosa linguagem semelhante a SQL.  O diagnóstico em toda a pilha do aplicativo fica fácil, conforme várias perspectivas são conectadas e você pode fazer as perguntas certas para correlacionar o desempenho do serviço às métricas de negócios e à experiência do cliente. 

Você pode consultar todos os dados brutos de métrica e instância de telemetria armazenados no portal. A linguagem inclui as operações de filtragem, adição, agregação e outras. Você pode calcular campos e realizar a análise estatística. Há visualizações de tabela e gráficas.

![Gráfico de consulta e os resultados da análise](./media/app-insights-devops/025.png)

Por exemplo, é fácil:

* Segmentar os dados de desempenho de solicitação do aplicativo pelas camadas do cliente para entender sua experiência.
* Pesquisar códigos de erro específicos ou nomes de evento personalizados durante investigações do site ativo.
* Analisar o uso do aplicativo de clientes específicos para entender como os recursos são adquiridos e adotados.
* Controlar as sessões e os tempos de resposta para usuários específicos para permitir que as equipes de operações e suporte forneçam suporte ao cliente instantâneo.
* Determinar os recursos de aplicativo usados com frequência para responder a perguntas de priorização de recurso.

Um cliente DNN afirma: "O Application Insights nos forneceu a parte que faltava para que pudéssemos combinar, classificar, consultar e filtrar os dados, conforme necessário. Permitindo que a nossa equipe usasse sua própria experiência e criatividade para localizar dados, com uma linguagem de consulta eficiente, nos permitiu ter insights e resolver problemas que nem mesmo sabíamos que tínhamos. Muitas respostas interessantes surgem das perguntas começando com *'Eu me pergunto se...'.*"

## <a name="development-tools-integration"></a>Integração de ferramentas de desenvolvimento
### <a name="configuring-application-insights"></a>Configurando o Application Insights
O Visual Studio e o Eclipse têm ferramentas para configurar os pacotes SDK corretos para o projeto que você está desenvolvendo. Há um comando de menu adicionar Application Insights.

Caso você esteja usando uma estrutura de registros de rastreamento, como o Log4N, NLog ou System.Diagnostics.Trace, receberá a opção de enviar os logs para o Application Insights juntamente com a telemetria, para que você possa correlacionar facilmente os rastreamentos com solicitações, chamadas de dependência e exceções.

### <a name="search-telemetry-in-visual-studio"></a>Pesquisar telemetria no Visual Studio
Durante o desenvolvimento e depuração de um recurso, você pode exibir e pesquisar a telemetria diretamente no Visual Studio, usando as mesmas funcionalidades de pesquisa que no portal da Web.

E quando o Application Insights registra uma exceção, você pode exibir o ponto de dados no Visual Studio e ir diretamente para o código relevante.

![Pesquisa do Visual Studio](./media/app-insights-devops/060.png)

Durante a depuração, você tem a opção de manter a telemetria no computador de desenvolvimento, exibindo-a no Visual Studio, mas sem enviá-la para o portal. Essa opção local evita a mistura da telemetria de produção com a de depuração.

### <a name="build-annotations"></a>Anotações do build
Se você usar o Visual Studio Team Services para compilar e implantar seu aplicativo, as anotações de implantação aparecerão em gráficos no portal. Se a versão mais recente tiver qualquer efeito sobre as métricas, isso se tornará óbvio.

![Anotações do build](./media/app-insights-devops/070.png)

### <a name="work-items"></a>Itens de trabalho
Quando um alerta é gerado, o Application Insights pode criar automaticamente um item de trabalho no seu sistema de controle de trabalho.

## <a name="but-what-about"></a>Mas e...?
* [Privacidade e armazenamento](app-insights-data-retention-privacy.md) : sua telemetria é mantida em servidores seguros do Azure.
* Desempenho: o impacto é muito baixo. A telemetria é feita em lotes.
* [Preço](app-insights-pricing.md) - você pode começar a usar gratuitamente e isso continua enquanto estiver no volume baixo.


## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Próximas etapas
É fácil começar a usar o Application Insights. As principais opções são:

* Instrumentar um aplicativo Web já em execução. Isso lhe fornece toda a telemetria de desempenho interna. Isso está disponível para [Java](app-insights-java-live.md) e [servidores IIS](app-insights-monitor-performance-live-website-now.md) e também para [aplicativos Web do Azure](app-insights-azure.md).
* Instrumentar seu projeto durante o desenvolvimento. Você pode fazer isso para aplicativos [ASP.NET](app-insights-asp-net.md) ou [Java](app-insights-java-get-started.md), bem como [Node.js](app-insights-nodejs.md) e um host de [outros tipos](app-insights-platforms.md). 
* Instrumentar [qualquer página da Web](app-insights-javascript.md) adicionando um trecho de código curto.

