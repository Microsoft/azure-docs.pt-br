---
title: "Retenção de dados e armazenamento no Azure Application Insights | Microsoft Docs"
description: "Declaração de política de privacidade e retenção"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: a6268811-c8df-42b5-8b1b-1d5a7e94cbca
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: bwren
ms.openlocfilehash: ddb9fa516da66da0484619439848583a29e1f5c1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Coleta, retenção e armazenamento de dados no Application Insights


Quando você instala o SDK do [Azure Application Insights][start] em seu aplicativo, ele envia telemetria sobre seu aplicativo para a nuvem. Naturalmente, os desenvolvedores responsáveis querem saber exatamente quais dados são enviados, o que acontece com os dados e como eles podem manter o controle sobre esses dados. Em particular, poderiam ser enviados dados confidenciais, onde são armazenados e qual é o nível de segurança? 

Primeiro, a resposta curta:

* Os módulos de telemetria padrão que executam "prontos de fábrica" têm pouca probabilidade de enviar dados confidenciais para o serviço. A telemetria está relacionada a carga, a métricas de desempenho e uso, a relatórios de exceção e a outros dados de diagnóstico. Os principais dados de usuário visíveis nos relatórios de diagnóstico são as URLs; mas seu aplicativo, em qualquer caso, não deve colocar dados confidenciais em texto sem formatação em uma URL.
* Você pode escrever código que envie a telemetria personalizada adicional para ajudá-lo com o uso de monitoramento e diagnóstico. (Essa extensibilidade é um ótimo recurso do Application Insights.) Seria possível, por engano, escrever esse código de modo que ele incluísse dados pessoais e outros dados confidenciais. Se seu aplicativo trabalhar com esses dados, você deverá aplicar processos de revisão completos para todo o código que escrever.
* Ao desenvolver e testar seu aplicativo, é fácil inspecionar o que está sendo enviado pelo SDK. Os dados aparecem nas janelas de saída de depuração do IDE e do navegador. 
* Os dados são mantidos em servidores do [Microsoft Azure](http://azure.com) nos EUA ou na Europa. (Mas seu aplicativo pode ser executado em qualquer lugar). O Azure tem [fortes processos de segurança e cumpre uma ampla gama de padrões de conformidade](https://azure.microsoft.com/support/trust-center/). Somente você e a sua equipe designada têm acesso aos seus dados. A equipe da Microsoft pode ter acesso restrito a eles apenas em circunstâncias limitadas específicas e com o seu conhecimento. Eles são criptografados em trânsito, embora não nos servidores.

O restante deste artigo aborda mais detalhadamente essas respostas. Ele foi projetado para ser independente, para que possa mostrá-lo aos colegas que não fazem parte de sua equipe.

## <a name="what-is-application-insights"></a>O que é o Application Insights?
O [Azure Application Insights][start] é um serviço fornecido pela Microsoft que ajuda a melhorar o desempenho e a usabilidade do seu aplicativo ativo. Ele monitora seus aplicativos em todo o tempo de execução, tanto durante o teste quanto depois de publicado ou implantado. O Application Insights cria gráficos e tabelas que mostram, por exemplo, em que horas do dia você tem mais usuários, o nível de capacidade de resposta do aplicativo e quão bem ele é atendido por quaisquer serviços externos dos quais depende. Se houver travamentos, falhas ou problemas de desempenho, você pode pesquisar os dados de telemetria em detalhes para diagnosticar a causa. E o serviço lhe enviará emails se houver alterações na disponibilidade e no desempenho do seu aplicativo.

Para obter essa funcionalidade, você instala um SDK do Application Insights em seu aplicativo, que passa a fazer parte do código. Quando o aplicativo é executado, o SDK monitora a operação e envia a telemetria para o serviço do Application Insights. Este é um serviço de nuvem hospedado pelo [Microsoft Azure](http://azure.com). (Mas o Application Insights funciona para qualquer aplicativo, não apenas aqueles hospedados no Azure.)

![O SDK em seu aplicativo envia telemetria ao serviço Application Insights.](./media/app-insights-data-retention-privacy/01-scheme.png)

O serviço Application Insights armazena e analisa a telemetria. Para ver a análise ou a pesquisa por meio de telemetria armazenada, você entra na sua conta do Azure e abre o recurso Application Insights para seu aplicativo. Você também pode compartilhar o acesso aos dados com outros membros da equipe ou com assinantes do Azure especificados.

Você pode ter dados exportados do serviço Application Insights, por exemplo, para um banco de dados ou para ferramentas externas. Você fornece a cada ferramenta uma chave especial obtida do serviço. A chave poderá ser revogada, se necessário. 

Application Insights SDKs estão disponíveis para uma variedade de tipos de aplicativos: serviços Web hospedados em seus próprios servidores J2EE ou ASP.NET ou no Azure; clientes Web – ou seja, o código em execução em uma página da Web; aplicativos da área de trabalho e serviços; aplicativos de dispositivo, como Windows Phone, iOS e Android. Todos enviam telemetria para o mesmo serviço.

## <a name="what-data-does-it-collect"></a>Quais dados são coletados?
### <a name="how-is-the-data-is-collected"></a>Como os dados são coletados?
Há três fontes de dados:

* O SDK, que se integra com o seu aplicativo [no desenvolvimento](app-insights-asp-net.md) ou no [tempo de execução](app-insights-monitor-performance-live-website-now.md). Há diferentes SDKs para diferentes tipos de aplicativos. Também há um [SDK para páginas da web](app-insights-javascript.md), que são carregadas no navegador do usuário final junto com a página.
  
  * Cada SDK tem vários [módulos](app-insights-configuration-with-applicationinsights-config.md), que usam diferentes técnicas para coletar diferentes tipos de telemetria.
  * Se você instalar o SDK no desenvolvimento, poderá usar sua API para enviar sua própria telemetria, além dos módulos padrão. Essa telemetria personalizada pode incluir quaisquer dados que você deseje enviar.
* Em alguns servidores web, também existem agentes que são executados juntamente com o aplicativo e que enviam telemetria sobre CPU, memória e ocupação de rede. Por exemplo, as VMs do Azure, hosts de Docker e [servidores J2EE](app-insights-java-agent.md) podem ter esses agentes.
* [Testes de disponibilidade](app-insights-monitor-web-app-availability.md) são processos executados pela Microsoft que enviam solicitações para o aplicativo web a intervalos regulares. Os resultados são enviados para o serviço Application Insights.

### <a name="what-kinds-of-data-are-collected"></a>Quais tipos de dados são coletados?
As principais categorias são:

* [Telemetria do servidor Web](app-insights-asp-net.md) - solicitações HTTP.  URI, tempo necessário para processar a solicitação, código de resposta, endereço IP do cliente. ID da sessão
* [Páginas da Web](app-insights-javascript.md) - contagens de página, usuário e sessão. Tempos de carregamento de página. Exceções. Chamadas Ajax.
* Contadores de desempenho - memória, CPU, E/S, ocupação de rede.
* Contexto de cliente e servidor - sistema operacional, localidade, tipo de dispositivo, navegador, resolução da tela.
* [Exceções](app-insights-asp-net-exceptions.md) e falhas - **despejos de pilha**, id da compilação, tipo de CPU. 
* [Dependências](app-insights-asp-net-dependencies.md) - chamadas a serviços externos, como REST, SQL, AJAX. Cadeia de conexão ou URI, duração, sucesso, comando.
* [Testes de disponibilidade](app-insights-monitor-web-app-availability.md) - duração do teste e etapas, respostas.
* [Logs de rastreamento](app-insights-asp-net-trace-logs.md) e [telemetria personalizada](app-insights-api-custom-events-metrics.md) - **qualquer elemento que você codifique nos seus logs ou telemetria**.

[Mais detalhes](#data-sent-by-application-insights).

## <a name="how-can-i-verify-whats-being-collected"></a>Como verificar o que está sendo coletado?
Se você estiver desenvolvendo o aplicativo usando o Visual Studio, execute o aplicativo no modo de depuração (F5). A telemetria é exibida na janela Saída. A partir dali, é possível copiá-la e formatá-la como JSON para fácil inspeção. 

![](./media/app-insights-data-retention-privacy/06-vs.png)

Também há uma exibição mais legível na janela Diagnóstico.

Para páginas da Web, abra a janela de depuração do navegador.

![Pressione F12 e abra a guia Rede.](./media/app-insights-data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>Posso escrever código para filtrar a telemetria antes de ela ser enviada?
Isso seria possível escrevendo um [plug-in de processador de telemetria](app-insights-api-filtering-sampling.md).

## <a name="how-long-is-the-data-kept"></a>Por quanto tempo os dados são mantidos?
Os pontos de dados brutos (ou seja, os itens que você pode consultar na Análise e inspecionar na Pesquisa) são mantidos por até 90 dias. Se precisar manter os dados por mais tempo, você poderá usar a [exportação contínua](app-insights-export-telemetry.md) para copiá-los para uma conta de armazenamento.

Os dados agregados (ou seja, contagens, médias e outros dados estatísticos que você vê no Gerenciador de Métricas) são mantidos em um detalhamento de um minuto por 90 dias.

## <a name="who-can-access-the-data"></a>Quem pode acessar os dados?
Os dados são visíveis para você e, se você tiver uma conta de organização, para os membros de sua equipe. 

Eles podem ser exportados por você e pelos membros da equipe e podem ser copiados para outros locais e passados para outras pessoas.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>O que a Microsoft faz com as informações que meu aplicativo envia ao Application Insights?
A Microsoft usa os dados apenas para fornecer o serviço a você.

## <a name="where-is-the-data-held"></a>Onde os dados são mantidos?
* Nos EUA ou na Europa. Quando você cria um novo recurso do Application Insights, você pode selecionar o local. 


#### <a name="does-that-mean-my-app-has-to-be-hosted-in-the-usa-or-europe"></a>Isso significa que o aplicativo deve ser hospedado nos EUA ou na Europa?
* Não. Seu aplicativo pode ser executado em qualquer lugar, em seus próprios hosts locais ou na Nuvem.

## <a name="how-secure-is-my-data"></a>Quão seguros meus dados estão?
O Application Insights é um serviço do Azure. Políticas de segurança são descritas no [white paper de segurança, privacidade e conformidade do Azure](http://go.microsoft.com/fwlink/?linkid=392408).

Os dados são armazenados em servidores do Microsoft Azure. Para contas no Portal do Azure, as restrições de conta são descritas no documento sobre [Segurança, privacidade e Conformidade do Azure](http://go.microsoft.com/fwlink/?linkid=392408).

É restrito o acesso aos seus dados por funcionários da Microsoft. Podemos acessar seus dados com a sua permissão e se isso for necessário para o seu uso do Application Insights. 

Dados agregados em aplicativos de todos os nossos clientes (como taxas de dados e tamanho médio dos rastreamentos) são usados para melhorar o Application Insights.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>A telemetria de outra pessoa poderia interferir meus dados do Application Insights?
Eles poderiam enviar telemetria adicional para sua conta usando a chave de instrumentação, que pode ser encontrada no código de suas páginas da Web. Com dados suficientes adicionais, suas métricas não representariam corretamente o uso e o desempenho do seu aplicativo.

Se você compartilhar código com outros projetos, lembre-se de remover sua chave de instrumentação.

## <a name="is-the-data-encrypted"></a>Os dados são criptografados?
Não dentro dos servidores no momento.

Todos os dados são criptografados conforme se movem entre datacenters.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>Os dados são criptografados em trânsito do meu aplicativo para servidores do Application Insights?
Sim, podemos usar https para enviar dados para o portal de quase todos os SDKs, incluindo servidores Web, dispositivos e páginas da Web em HTTPS. A única exceção é que os dados enviados de páginas da Web em HTTP simples. 

## <a name="personally-identifiable-information"></a>Informações pessoalmente identificáveis
#### <a name="could-personally-identifiable-information-pii-be-sent-to-application-insights"></a>Podem ser enviadas informações pessoalmente identificáveis (PII) para o Application Insights?
Sim, é possível. 

Como orientação geral:

* A telemetria mais padrão (ou seja, telemetria enviada sem que você escreva nenhum código) não inclui PII explícitas. No entanto, é possível identificar indivíduos por inferência de uma coleção de eventos.
* Mensagens de exceção e rastreamento podem conter informações de identificação pessoal
* Telemetria personalizada - ou seja, chamadas como TrackEvent que você escreve em código usando a API ou os rastreamentos de log - podem conter qualquer dado que você escolher.

A tabela ao final deste documento contém descrições mais detalhadas dos dados coletados.

#### <a name="am-i-responsible-for-complying-with-laws-and-regulations-in-regard-to-pii"></a>Eu sou responsável por manter a conformidade com as leis e regulamentações relativas às informações de identificação pessoal?
Sim. É sua responsabilidade assegurar que a coleta e uso de dados estejam em conformidade com as leis e normas e também com os termos do Microsoft Online Services.

Você deve informar os clientes adequadamente quanto aos dados que seu aplicativo coleta e como tais dados são usados.

#### <a name="can-my-users-turn-off-application-insights"></a>Meus usuários podem desativar o Application Insights?
Não diretamente. Não fornecemos um comutador que os usuários possam operar para desativar o Application Insights.

No entanto, você pode implementar tal recurso em seu aplicativo. Todos os SDKs incluem uma configuração de API que desativa a coleta da telemetria. 

#### <a name="my-application-is-unintentionally-collecting-sensitive-information-can-application-insights-scrub-this-data-so-it-isnt-retained"></a>Meu aplicativo está coletando informações confidenciais acidentalmente. O Application Insights pode eliminar esses dados para que eles não sejam retidos?
O Application Insights não filtra nem exclui seus dados. Você deve gerenciar os dados adequadamente e evitar enviar esses dados para o Application Insights.

## <a name="data-sent-by-application-insights"></a>Dados enviados pelo Application Insights
Os SDKs variam entre diferentes plataformas, e há vários componentes que você pode instalar. (Consulte [Application Insights - visão geral][start].) Cada componente envia dados diferentes.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>Classes de dados enviados em cenários diferentes
| Sua ação | Classes de dados coletados (consulte a tabela a seguir) |
| --- | --- |
| [Adicionar o Application Insights SDK a um projeto Web .NET][greenbrown] |ServerContext<br/>Inferido<br/>Contadores de desempenho<br/>Solicitações<br/>**Exceções**<br/>Session<br/>users |
| [Instalar o Monitor de Status no IIS][redfield] |Dependências<br/>ServerContext<br/>Inferido<br/>Contadores de desempenho |
| [Adicionar o Application Insights SDK a um aplicativo Web Java][java] |ServerContext<br/>Inferido<br/>Solicitação<br/>Session<br/>users |
| [Adicionar SDK do JavaScript à página da Web][client] |ClientContext  <br/>Inferido<br/>Página<br/>ClientPerf<br/>Ajax |
| [Definir propriedades padrão][apiproperties] |**Propriedades** em todos os eventos padrão e personalizados |
| [Chamar TrackMetric][api] |Valores numéricos<br/>**Propriedades** |
| [Chamar Track*][api] |Nome do evento<br/>**Propriedades** |
| [Chamar TrackException][api] |**Exceções**<br/>Despejo da pilha<br/>**Propriedades** |
| O SDK não é capaz de coletar dados. Por exemplo: <br/> - não é possível acessar os contadores de desempenho<br/> - exceção no inicializador de telemetria |Diagnóstico do SDK |

Para [SDKs para outras plataformas][platforms], consulte seus respectivos documentos.

#### <a name="the-classes-of-collected-data"></a>As classes dos dados coletados
| Classe de dados coletados | Inclui (não é uma lista completa) |
| --- | --- |
| **Propriedades** |**Quaisquer dados - determinados pelo seu código** |
| DeviceContext |ID, IP, localidade, modelo de dispositivo, rede, tipo de rede, nome OEM, resolução de tela, instância de função, nome da função, tipo de dispositivo |
| ClientContext  |Sistema operacional, localidade, linguagem, rede, resolução da janela |
| Session |ID da sessão |
| ServerContext |Nome do computador, localidade, sistema operacional, dispositivo, sessão de usuário, contexto de usuário, operação |
| Inferido |localização geográfica do endereço IP, carimbo de data/hora, sistema operacional, navegador |
| Métricas |Valor e nome da métrica |
| Eventos |Valor e nome do evento |
| PageViews |URL e nome da página ou o nome de tela |
| Desempenho do cliente |URL/nome de página, tempo de carregamento do navegador |
| Ajax |Chamadas HTTP da página da Web para o servidor |
| Solicitações |URL, duração, código de resposta |
| Dependências |Tipo (SQL, HTTP,...), cadeia de conexão ou URI, síncrono/assíncrono, duração, sucesso, instrução SQL (com Monitor de Status) |
| **Exceções** |Tipo, **mensagem**, pilhas de chamadas, arquivo-fonte e número de linha, ID do thread |
| Falhas |ID do processo, ID do processo-pai, ID de thread de falha; patch do aplicativo, ID de compilação; tipo de exceção, endereço, motivo; símbolos e registros ofuscados, endereços binários de início e término, nome e caminho binários, tipo de CPU |
| Rastreamento |**Mensagem** e nível de severidade |
| Contadores de desempenho |Tempo do processador, memória disponível, taxa de solicitação, taxa de exceções, bytes particulares do processo, taxa de E/S, duração da solicitação, comprimento da fila de solicitações |
| Disponibilidade |Código de resposta de teste da Web, duração de cada etapa de teste, nome do teste, carimbo de data/hora, sucesso, tempo de resposta, local de teste |
| Diagnóstico do SDK |Mensagem de rastreamento ou exceção |

Você pode [desativar alguns dos dados editando ApplicationInsights.config][config]

## <a name="credits"></a>Credits
Este produto inclui dados GeoLite2 criados pelo MaxMind, disponíveis em [http://www.maxmind.com](http://www.maxmind.com).



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[platforms]: app-insights-platforms.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

