---
title: Como solucionar problemas de falha de carregamento do SDK para aplicativos Web JavaScript – Application Insights do Azure
description: Como solucionar problemas de falha de carregamento do SDK para aplicativos Web JavaScript
ms.topic: conceptual
author: MSNev
ms.author: newylie
ms.date: 06/05/2020
ms.custom: devx-track-js
ms.openlocfilehash: 6295a56abbf3466c68b968c935936dbc10e22fb5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711409"
---
# <a name="troubleshooting-sdk-load-failure-for-javascript-web-apps"></a>Como solucionar problemas de falha de carregamento do SDK para aplicativos Web JavaScript

<!-- 
Editor Note: This link name above "SDK Load Failure" has a direct references by https://go.microsoft.com/fwlink/?linkid=2128109 which is embedded in the snippet and from the JavaScript Page. If you change this text you MUST also update these references.
-->

A exceção de falha de carregamento do SDK é criada e relatada pelo trecho JavaScript (v3 ou posterior) quando detecta falha no download ou na inicialização do script do SDK. De maneira simples, o cliente (navegador) dos usuários finais não conseguiu baixar o SDK do Application Insights ou inicializar na página de hospedagem identificada e, portanto, não há relato de telemetria ou de eventos.

![Visão geral da falha do navegador do portal do Azure](./media/javascript-sdk-load-failure/overview.png)

> [!NOTE]
> Essa exceção tem suporte em todos os principais navegadores que dão suporte para a API fetch() ou XMLHttpRequest. Isso exclui o IE 8 e versões anteriores, portanto, esse tipo de exceção não é relatado nesses navegadores (a menos que o ambiente inclua um polyfill de busca).

![detalhe de exceção do navegador](./media/javascript-sdk-load-failure/exception.png)

Os detalhes da pilha incluem as informações básicas com as URLs usadas pelo usuário final.

| Nome                      | Descrição                                                                                                  |
|---------------------------|--------------------------------------------------------------------------------------------------------------|
| &lt;CDN&nbsp;Endpoint&gt; | A URL que foi usada (e falhou) para baixar o SDK.                                                      |
| &lt;Help&nbsp;Link&gt;    | Uma URL que aponta para a documentação de solução de problemas (esta página).                                              |
| &lt;Host&nbsp;URL&gt;     | A URL completa da página que o usuário final estava usando.                                                    |
| &lt;Endpoint&nbsp;URL&gt; | A URL que foi usada para relatar a exceção, um valor que pode ser útil para identificar se a página de hospedagem foi acessada por meio da Internet pública ou de uma nuvem privada.

Os motivos mais comuns para essa exceção ocorrer:

- Falha de conectividade de rede intermitente.
- Interrupção da CDN do Application Insights.
- Falha na inicialização do SDK após o carregamento do script.
- Bloqueio da CDN de JavaScript do Application Insights.

A [falha de conectividade de rede intermitente](#intermittent-network-connectivity-failure) é o motivo mais comum para essa exceção, especialmente durante o uso de perfis móveis, em que os usuários perdem a conectividade de rede intermitentemente.

As seções a seguir descrevem como solucionar cada possível causa raiz do erro.

> [!NOTE]
> Para várias das etapas da solução, o aplicativo precisa ter controle direto sobre a marca &lt;script/&gt; do trecho de código e sobre a configuração dela, que é retornada na página de HTML da hospedagem. As etapas identificadas não se aplicam aos cenários em que isso não ocorre.

## <a name="intermittent-network-connectivity-failure"></a>Falha de conectividade de rede intermitente

**Quando o usuário enfrenta falhas intermitentes de conectividade de rede, há menos soluções possíveis, e elas geralmente são resolvidas após um curto período de tempo.** Por exemplo, se o usuário recarregar o site (atualizar a página), os arquivos (em algum momento) serão baixados e armazenados no cache local até o lançamento de uma versão atualizada.

> [!NOTE]
> Quando a exceção é persistente e ocorre com vários dos usuários (diagnosticado por um nível rápido e sustentado de registros da exceção) junto com a redução na telemetria normal do cliente, _provavelmente_ a conectividade de rede intermitente não é a verdadeira causa, e você deve continuar a diagnosticar com os outros possíveis problemas conhecidos.

Nessa situação, a hospedagem do SDK em uma CDN própria provavelmente não reduziria as ocorrências da exceção, pois a CDN também estaria sujeita ao mesmo problema.

Isso também é verdadeiro ao usar o SDK por meio da solução de pacotes NPM. No entanto, quando isso ocorre, todo o aplicativo deixa de carregar ou inicializar na perspectiva dos usuários finais (e não _apenas_ o SDK de telemetria, que não é exibido aos usuários). Por isso, os usuários provavelmente atualizarão o site até o carregamento completo.

Você também pode tentar usar [pacotes NPM](#use-npm-packages-to-embed-the-application-insight-sdk) para inserir o SDK do Application Insights.

Para minimizar a falha de conectividade de rede intermitente, implementamos cabeçalhos Cache-Control em todos os arquivos da CDN. Dessa forma, após baixar a versão atual do SDK, o navegador dos usuários finais usa a cópia já obtida sem precisar baixar o SDK novamente (veja [como o cache funciona](../../cdn/cdn-how-caching-works.md)). Se a verificação de cache falhar ou houver uma nova versão, o navegador do usuário final precisará baixar a versão atualizada. Portanto, talvez haja um nível de _"ruído"_ em segundo plano no cenário de falha de verificação ou um pico temporário quando houver o lançamento e a disponibilização de uma nova versão (implantada na CDN).
 
## <a name="application-insights-cdn-outage"></a>Interrupção da CDN do Application Insights

Para verificar se houve interrupção da CDN do Application Insights, tente acessar o ponto de extremidade da CDN diretamente no navegador (por exemplo, https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js ou https://js.monitor.azure.com/scripts/b/ai.2.min.js) ) ou em um local diferente dos usuários finais, provavelmente no seu próprio computador de desenvolvimento (contanto que sua organização não tenha bloqueado o domínio).

Se você confirmar que há uma interrupção, [crie um tíquete de suporte](https://azure.microsoft.com/support/create-ticket/) ou altere a URL usada para baixar o SDK.

### <a name="change-the-cdn-endpoint"></a>Alterar o ponto de extremidade da CDN
  
Como o trecho de código e a configuração dele são retornados pelo aplicativo como parte de cada página gerada, você pode alterar a configuração do trecho `src` para usar outra URL para o SDK. Com essa abordagem, você pode ignorar o problema de bloqueio da CDN, pois a nova URL não deve ser bloqueada.

Pontos de extremidade atuais da CDN do SDK de JavaScript do Application Insights
- `https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js`
- `https://js.monitor.azure.com/scripts/b/ai.2.min.js`

> [!NOTE]
> O ponto de extremidade `https://js.monitor.azure.com/` é um alias que permite trocar os provedores de CDN em aproximadamente 5 minutos, sem que você precise alterar qualquer configuração. Assim, podemos corrigir os problemas detectados mais rapidamente quando um provedor de CDN apresenta problemas regionais ou globais, sem exigir que todos ajustem suas configurações.

## <a name="sdk-failed-to-initialize-after-loading-the-script"></a>Falha na inicialização do SDK após o carregamento do script

Quando o SDK não é inicializado, o &lt;script /&gt; foi baixado da CDN, mas a inicialização dele falhou. A causa pode ser ausência ou invalidade de dependências ou alguma forma de exceção de JavaScript.

A primeira coisa a verificar é se o SDK foi baixado. Se o script não tiver sido baixado, esse cenário __não__ será a causa da exceção de falha de carregamento do SDK.

Verificação rápida: usando um navegador que dá suporte para ferramentas para desenvolvedores (F12), verifique na guia de rede que o script definido na configuração de trecho de código de ```src``` foi baixado com o código de resposta 200 (êxito) ou 304 (não alterado). Você também pode usar uma ferramenta como o Fiddler para examinar o tráfego de rede.

As seções a seguir incluem opções de relatório diferentes, que recomendam a criação de um tíquete de suporte ou a geração de um problema no GitHub.

 Regras básicas de relatório:

- Se o problema está afetando apenas um pequeno número de usuários e uma versão específica ou um subconjunto de versões do navegador (verifique os detalhes na exceção relatada), é provável que seja um problema somente do usuário final ou do ambiente, o que provavelmente exigirá que o aplicativo forneça implementações adicionais de `polyfill`. Para isso, [registre um problema no GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues).
- Quando o problema afeta todo o aplicativo e todos os usuários, provavelmente está relacionado à versão, e você deve [criar um tíquete de suporte](https://azure.microsoft.com/support/create-ticket/).

### <a name="javascript-exceptions"></a>Exceções de JavaScript

Primeiro, vamos verificar se há exceções de JavaScript. Usando um navegador que dá suporte para ferramentas para desenvolvedores (F12), carregue a página e verifique se alguma exceção ocorreu.

Se houver registro de exceções no script do SDK (por exemplo, ai.2.min.js), talvez a configuração passada ao SDK seja inesperada ou não contenha uma configuração obrigatória; outra possibilidade é a implantação de uma versão com falha na CDN.

Para verificar se há falhas na configuração, altere a configuração passada ao trecho de código (se não o tiver feito) para que ela inclua apenas a chave de instrumentação como um valor de cadeia de caracteres.

> src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js ",<br />
> cfg:{<br />
> instrumentationKey: "INSTRUMENTATION_KEY"<br />
> }});<br />

Se a exceção do JavaScript continuar ocorrendo no script do SDK mesmo com a configuração mínima, [crie um tíquete de suporte](https://azure.microsoft.com/support/create-ticket/), pois será necessário reverter a compilação com falha, já que provavelmente o problema está na versão recém-implantada.

Se a exceção desaparecer, o problema provavelmente terá sido causado por tipos incompatíveis ou um valor inesperado. Comece a adicionar as opções de configuração de volta uma a uma e teste até que a exceção ocorra novamente. Em seguida, verifique a documentação do item que está causando o problema. Se a documentação não estiver clara ou se você precisar de assistência, [registre um problema no GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues).

Se a configuração foi implantada anteriormente e estava funcionando, mas a exceção começou a ocorrer recentemente, talvez haja um problema com uma versão implantada recentemente. Verifique se ela está afetando apenas um pequeno conjunto de usuários/navegador e [registre um problema no GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues) ou [crie um tíquete de suporte](https://azure.microsoft.com/support/create-ticket/).

### <a name="enable-console-debugging"></a>Habilitar a depuração do console

Quando não há registro de exceções, a próxima etapa é habilitar a depuração do console. Para isso, adicione `loggingLevelConsole` à configuração para que todos os erros e os avisos de inicialização sejam enviados ao console do navegador (geralmente disponível por meio das ferramentas para desenvolvedores, tecla F12). Os erros relatados devem ser autoexplicativos, e, se você precisar de assistência adicional, [registre um problema no GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues).

> cfg:{<br />
> instrumentationKey: "INSTRUMENTATION_KEY",<br />
> loggingLevelConsole: 2<br />
> }});<br />

> [!NOTE]
> Durante a inicialização, o SDK verifica a presença das principais dependências conhecidas. Se elas não forem disponibilizadas pelo runtime atual, ele registrará as falhas como mensagens de aviso no console, mas somente se `loggingLevelConsole` for maior que zero.

Se ainda não for possível inicializar, habilite a configuração ```enableDebug```. Isso fará com que todos os erros internos sejam registrados como uma exceção (o que causará perda da telemetria). Como se trata de uma configuração somente para desenvolvedores, provavelmente haverá muito ruído devido ao registro de exceções como parte de algumas verificações internas, portanto, você precisará examinar cada exceção para determinar qual problema está causando a falha do SDK. Use a versão não minimizada do script (a extensão abaixo é ". js" e não ".min.js"), caso contrário, as exceções ficarão ilegíveis.

> [!WARNING]
> Essa é uma configuração somente para desenvolvedores e NUNCA deve ser habilitada em um ambiente de produção completo, pois você perderá a telemetria.

> src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js ",<br />
> cfg:{<br />
> instrumentationKey: "INSTRUMENTATION_KEY",<br />
> enableDebug: true<br />
> }});<br />

Se isso ainda não revelar informações, você deverá [registrar um problema no GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues) com os detalhes e um site de exemplo, se tiver um. Inclua a versão do navegador, o sistema operacional e os detalhes da estrutura de JS para ajudar a identificar o problema.

## <a name="the-application-insights-javascript-cdn-has-been-blocked"></a>Bloqueio da CDN de JavaScript do Application Insights

O bloqueio da CDN pode ocorrer quando um ponto de extremidade da CDN do SDK de JavaScript do Application Insights é denunciado e/ou identificado como não seguro. Quando isso ocorre, o ponto de extremidade é bloqueado publicamente, e os consumidores dessas listas começam a bloquear todo o acesso.

Para resolver, é necessário que o proprietário do ponto de extremidade da CDN entre em contato com a entidade de bloqueio que marcou o ponto de extremidade como não seguro para removê-lo da lista relevante.

Verifique se o ponto de extremidade da CDN foi identificado como não seguro.
- [Relatório de transparência do Google](https://transparencyreport.google.com/safe-browsing/search)
- [VirusTotal](https://www.virustotal.com/gui/home/url)
- [Sucuri SiteCheck](https://sitecheck.sucuri.net/)

Dependendo da frequência com que o aplicativo, o firewall ou o ambiente atualizam as cópias locais das listas, pode levar um tempo considerável e/ou exigir intervenção manual dos usuários finais ou departamentos de TI corporativos para forçar a atualização ou permitir explicitamente que os pontos de extremidade da CDN resolvam o problema.

Se o ponto de extremidade da CDN for identificado como não seguro, [crie um tíquete de suporte](https://azure.microsoft.com/support/create-ticket/) para resolver o problema o mais rápido possível.

Para *potencialmente* ignorar esse problema mais rapidamente, você pode [alterar o ponto de extremidade da CDN do SDK](#change-the-cdn-endpoint).

### <a name="application-insights-javascript-cdn-is-blocked-by-end-user---blocked-by-browser-installed-blocker-personal-firewall"></a>Bloqueio da CDN de JavaScript do Application Insights (pelo usuário final, bloqueado por navegador, bloqueador instalado, firewall pessoal)

Verifique se os usuários finais fizeram estas ações:

- Instalaram um plug-in para navegador (normalmente alguma forma de anúncio, malware ou bloqueador de pop-ups).
- Bloquearam (ou não autorizaram) os pontos de extremidade da CDN do Application Insights no navegador ou proxy.
- Configuraram uma regra de firewall que bloqueou o domínio da CDN do SDK (ou impediu a resolução da entrada DNS).

Se eles fizerem uma dessas ações, você precisará trabalhar com eles (ou fornecer documentação) para permitir os pontos de extremidade da CDN.

É possível que o plug-in instalado esteja usando a lista de bloqueados pública. Caso contrário, é mais provável que seja alguma outra solução configurada manualmente ou que os usuários usem uma lista de domínios bloqueados privada.

#### <a name="add-exceptions-for-cdn-endpoints"></a>Adicionar exceções para pontos de extremidade da CDN

Entre em contato com os usuários finais ou forneça documentação para orientá-los a incluir os pontos de extremidade da CDN do Application Insights na lista de exceção do plug-in para navegador ou da regra de firewall (varia dependendo do ambiente do usuário), a fim de permitir o download dos scripts.

Aqui está um exemplo de como configurar o [Chrome para permitir ou bloquear o acesso aos sites.](https://support.google.com/chrome/a/answer/7532419?hl=en)

### <a name="application-insights-cdn-is-blocked-by-corporate-firewall"></a>Bloqueio da CDN do Application Insights (pelo firewall corporativo)

Quando os usuários finais estão em uma rede corporativa, é mais provável que a solução de firewall e o departamento de TI tenha implementado alguma forma de sistema de filtragem da Internet. Nesse caso, você precisa entrar em contato com eles para permitir as regras necessárias para os usuários finais.

#### <a name="add-exceptions-for-cdn-endpoints-for-corporations"></a>Adicionar exceções para pontos de extremidade da CDN em corporações

  A operação é semelhante à [adição de exceções para os usuários finais](#add-exceptions-for-cdn-endpoints), mas você deve solicitar ao departamento de TI da empresa que inclua os pontos de extremidade da CDN do Application Insights na lista de bloqueio de domínio ou nos serviços de lista de autorizados, a fim de permitir o download.

  > [!WARNING]
  > Se o usuário corporativo estiver usando uma [nuvem privada](https://azure.microsoft.com/overview/what-is-a-private-cloud/) e não puder habilitar qualquer forma de exceção para conceder aos usuários internos acesso público aos pontos de extremidade da CDN, você precisará usar os [pacotes NPM do Application Insights](https://www.npmjs.com/package/applicationinsights) ou hospedar o SDK do Application insights em sua própria CDN.  

### <a name="additional-troubleshooting-for-blocked-cdn"></a>Outros métodos de solução de bloqueio de CDN

> [!NOTE]
> Se os usuários estiverem usando uma [nuvem privada](https://azure.microsoft.com/overview/what-is-a-private-cloud/) e não tiverem acesso à Internet pública, você precisará hospedar o SDK em sua própria CDN ou usar os pacotes NPM.

#### <a name="host-the-sdk-on-your-own-cdn"></a>Hospedar o SDK em uma CDN própria

 Em vez de os usuários finais baixarem o SDK do Application Insights da CDN pública, você poderia hospedar o SDK do Application Insights no seu próprio ponto de extremidade de CDN. Recomendamos usar uma versão específica (ai.2.#.#.min.js) para facilitar a identificação. Também atualize regularmente para a versão atual (ai.2.min.js) para aproveitar as correções de bugs e os novos recursos disponíveis.

#### <a name="use-npm-packages-to-embed-the-application-insight-sdk"></a>Usar pacotes NPM para inserir o SDK do Application insights

Em vez de usar o trecho de código e os pontos de extremidade da CDN pública, você pode usar os [pacotes NPM](https://www.npmjs.com/package/applicationinsights) para incluir o SDK como parte de seus próprios arquivos JavaScript. O SDK se tornará apenas outro pacote dentro de seus próprios scripts.

> [!NOTE]
> Ao usar pacotes NPM, recomendamos usar também alguma forma de [empacotador de JavaScript](https://www.bing.com/search?q=javascript+bundler) para auxiliar na divisão e minificação do código.

Assim como no trecho de código, também é possível que seus próprios scripts (usando ou não os pacotes NPM do SDK) sejam afetados pelos mesmos problemas de bloqueio mencionados aqui. Portanto, dependendo do aplicativo, dos usuários e da estrutura, pode ser melhor implementar algo semelhante à lógica do trecho de código para detectar e relatar esses problemas.


## <a name="next-steps"></a>Próximas etapas 
- [Para obter mais ajuda, registre um problema no GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues)
- [Monitorar o uso da página da Web](javascript.md)
