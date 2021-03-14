---
title: Solucionando problemas de falha de carregamento do SDK para aplicativos Web do JavaScript-Aplicativo Azure insights
description: Como solucionar problemas de falha de carregamento do SDK para aplicativos Web do JavaScript
ms.topic: conceptual
author: MSNev
ms.author: newylie
ms.date: 06/05/2020
ms.custom: devx-track-js
ms.openlocfilehash: 6295a56abbf3466c68b968c935936dbc10e22fb5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711409"
---
# <a name="troubleshooting-sdk-load-failure-for-javascript-web-apps"></a>Solucionando problemas de falha de carregamento do SDK para aplicativos Web JavaScript

<!-- 
Editor Note: This link name above "SDK Load Failure" has a direct references by https://go.microsoft.com/fwlink/?linkid=2128109 which is embedded in the snippet and from the JavaScript Page. If you change this text you MUST also update these references.
-->

A exceção de falha de carregamento do SDK é criada e relatada pelo trecho de código JavaScript (v3 ou posterior) quando detecta que o script do SDK falhou ao baixar ou inicializar. De forma simples, o cliente (navegador) dos usuários finais não conseguiu baixar o SDK do Application Insights ou inicializar na página de hospedagem identificada e, portanto, nenhuma telemetria ou evento será relatado.

![Visão geral da falha do navegador de portal do Azure](./media/javascript-sdk-load-failure/overview.png)

> [!NOTE]
> Essa exceção tem suporte em todos os principais navegadores que dão suporte à API Fetch () ou XMLHttpRequest. Isso exclui o IE 8 e versões anteriores, portanto, você não obterá esse tipo de exceção relatada a partir desses navegadores (a menos que seu ambiente inclua um polifill de busca).

![detalhe de exceção do navegador](./media/javascript-sdk-load-failure/exception.png)

Os detalhes da pilha incluem as informações básicas com as URLs que estão sendo usadas pelo usuário final.

| Name                      | Descrição                                                                                                  |
|---------------------------|--------------------------------------------------------------------------------------------------------------|
| &lt;&nbsp;Ponto de extremidade CDN&gt; | A URL que foi usada (e falhou) para baixar o SDK.                                                      |
| &lt;Link de ajuda &nbsp;&gt;    | Uma URL que vincula à documentação de solução de problemas (esta página).                                              |
| &lt;URL do host &nbsp;&gt;     | A URL completa da página que o usuário final estava usando.                                                    |
| &lt;URL do ponto de extremidade &nbsp;&gt; | A URL que foi usada para relatar a exceção, esse valor pode ser útil para identificar se a página de hospedagem foi acessada da Internet pública ou de uma nuvem privada.

Os motivos mais comuns para essa exceção ocorrerem:

- Falha de conectividade de rede intermitente.
- Application Insights interrupção da CDN.
- Falha na inicialização do SDK após o carregamento do script.
- Application Insights a CDN do JavaScript foi bloqueada.

A [falha de conectividade de rede intermitente](#intermittent-network-connectivity-failure) é o motivo mais comum para essa exceção, especialmente em cenários móveis móvel em que os usuários perdem a conectividade de rede intermitentemente.

As seções a seguir descrevem como solucionar cada possível causa raiz desse erro.

> [!NOTE]
> Várias das etapas de solução de problemas pressupõem que seu aplicativo tem controle direto da &lt; marca/script de trecho de código &gt; e é a configuração retornada como parte da página HTML de hospedagem. Se você não fizer isso, as etapas identificadas não se aplicarão ao seu cenário.

## <a name="intermittent-network-connectivity-failure"></a>Falha de conectividade de rede intermitente

**Se o usuário estiver enfrentando falhas intermitentes de conectividade de rede, haverá menos soluções possíveis e elas geralmente serão resolvidas por um curto período de tempo.** Por exemplo, se o usuário recarregar seu site (atualiza a página), os arquivos (eventualmente) serão baixados e armazenados em cache localmente até que uma versão atualizada seja liberada.

> [!NOTE]
> Se essa exceção for persistente e estiver ocorrendo em muitos de seus usuários (diagnosticado por um nível rápido e sustentado dessa exceção sendo relatado) junto com uma redução na telemetria normal do cliente, os problemas intermitentes de conectividade de rede não serão, _provavelmente_ , a causa do problema e você deverá continuar a diagnosticar com os outros possíveis problemas conhecidos.

Com essa situação, é improvável que a hospedagem do SDK em sua própria CDN forneça ou reduza as ocorrências dessa exceção, pois sua própria CDN será afetada pelo mesmo problema.

O mesmo também é verdadeiro ao usar o SDK por meio da solução de pacotes NPM. No entanto, da perspectiva dos usuários finais, quando isso ocorre, o aplicativo inteiro falha ao carregar/inicializar (em vez de _apenas_ ao SDK de telemetria, que não vê visualmente), portanto, provavelmente, atualizará seu site até que o seja totalmente carregado.

Você também pode tentar usar [pacotes NPM](#use-npm-packages-to-embed-the-application-insight-sdk) para inserir o SDK do Application insights.

Para minimizar a falha de conectividade de rede intermitente, implementamos Cache-Control cabeçalhos em todos os arquivos CDN para que, depois que o navegador do usuário final tenha baixado a versão atual do SDK, ele não precise baixar novamente e o navegador reutilizará a cópia obtida anteriormente (veja [como funciona o Caching](../../cdn/cdn-how-caching-works.md)). Se a verificação de cache falhar ou tiver havido uma nova versão, o navegador do usuário final precisará baixar a versão atualizada. Portanto, você pode ver um nível de plano de fundo de _"ruído"_ no cenário de falha de verificação ou um pico temporário quando uma nova versão ocorre e é disponibilizada de forma geral (implantada na CDN).
 
## <a name="application-insights-cdn-outage"></a>Interrupção Application Insights CDN

Você pode confirmar se há uma interrupção Application Insights CDN ao tentar acessar o ponto de extremidade da CDN diretamente do navegador (por exemplo, https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js ou https://js.monitor.azure.com/scripts/b/ai.2.min.js) de um local diferente dos seus usuários finais, provavelmente do seu próprio computador de desenvolvimento (pressupondo que sua organização não bloqueou esse domínio).

Se você confirmar que há uma interrupção, poderá [criar um novo tíquete de suporte](https://azure.microsoft.com/support/create-ticket/) ou tentar alterar a URL usada para baixar o SDK.

### <a name="change-the-cdn-endpoint"></a>Alterar o ponto de extremidade da CDN
  
Como o trecho de código e sua configuração são retornados por seu aplicativo como parte de cada página gerada, você pode alterar a configuração de trecho `src` para usar uma URL diferente para o SDK. Ao usar essa abordagem, você pode ignorar o problema bloqueado da CDN, pois a nova URL não deve ser bloqueada.

Pontos de extremidade CDN atuais do Application Insights SDK do JavaScript
- `https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js`
- `https://js.monitor.azure.com/scripts/b/ai.2.min.js`

> [!NOTE]
> O `https://js.monitor.azure.com/` ponto de extremidade é um alias que nos permite alternar entre os provedores de CDN em aproximadamente 5 minutos, sem a necessidade de alterar qualquer configuração. Isso é para nos permitir corrigir problemas de CDN detectados mais rapidamente se um provedor CDN estiver tendo problemas regionais ou globais sem exigir que todos ajustem suas configurações.

## <a name="sdk-failed-to-initialize-after-loading-the-script"></a>Falha na inicialização do SDK após o carregamento do script

Quando o SDK não é inicializado, o &lt; script/ &gt; foi baixado com êxito da CDN, mas falha durante a inicialização. Isso pode ser devido a dependências ausentes ou inválidas ou alguma forma de exceção de JavaScript.

A primeira coisa a verificar é se o SDK foi baixado com êxito, se o script não tiver sido baixado, esse cenário __não__ é a causa da exceção de falha de carregamento do SDK.

Verificação rápida: usando um navegador que dá suporte a ferramentas de desenvolvedor (F12), valide na guia rede que o script definido na ```src``` configuração de trecho foi baixado com um código de resposta de 200 (êxito) ou um 304 (não alterado). Você também pode usar uma ferramenta como o Fiddler para examinar o tráfego de rede.

As seções a seguir incluem opções de relatório diferentes, que recomendará a criação de um tíquete de suporte ou a geração de um problema no GitHub.

 Regras básicas de relatórios:

- Se o problema estiver afetando apenas um pequeno número de usuários e uma versão específica ou um subconjunto de versões do navegador (verifique os detalhes na exceção relatada), é provável que seja um problema somente de usuário final ou ambiente, o que provavelmente exigirá que seu aplicativo forneça `polyfill` implementações adicionais. Para isso, [Execute um problema no GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues).
- Se esse problema estiver afetando todo o seu aplicativo e todos os seus usuários forem afetados, provavelmente será um problema relacionado à versão e, portanto, você deverá [criar um novo tíquete de suporte](https://azure.microsoft.com/support/create-ticket/).

### <a name="javascript-exceptions"></a>Exceções de JavaScript

Primeiro, vamos verificar as exceções de JavaScript, usando um navegador que dá suporte a ferramentas de desenvolvedor (F12) carregar a página e examinar se alguma exceção ocorreu.

Se houver exceções sendo relatadas no script do SDK (por exemplo ai.2.min.js), isso pode indicar que a configuração passada para o SDK contém uma configuração necessária inesperada ou ausente ou uma versão defeituosa foi implantada na CDN.

Para verificar a configuração com falha, altere a configuração passada para o trecho de código (se ainda não estiver) para que ela inclua apenas sua chave de instrumentação como um valor de cadeia de caracteres.

> src: " https://js.monitor.azure.com/scripts/b/ai.2.min.js ",<br />
> cfg: {<br />
> instrumentationKey: "INSTRUMENTATION_KEY"<br />
> }});<br />

Se, ao usar essa configuração mínima, você ainda estiver vendo uma exceção de JavaScript no script do SDK, [crie um novo tíquete de suporte](https://azure.microsoft.com/support/create-ticket/) , pois isso exigirá que a compilação com falha seja revertida, pois provavelmente é um problema com uma versão implantada recentemente.

Se a exceção desaparecer, o problema provavelmente será causado por um valor incompatível ou inesperado de tipo. Comece a adicionar suas opções de configuração de volta uma a uma e teste até que a exceção ocorra novamente. Em seguida, verifique a documentação do item que está causando o problema. Se a documentação não estiver clara ou se você precisar de assistência, [Execute um problema no GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues).

Se sua configuração foi implantada anteriormente e estava funcionando, mas acabou de começar a relatar essa exceção, talvez seja um problema com uma versão implantada recentemente, verifique se ela está afetando apenas um pequeno conjunto de usuários/navegador e se ele está [relacionado ao GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues) ou  [cria um novo tíquete de suporte](https://azure.microsoft.com/support/create-ticket/).

### <a name="enable-console-debugging"></a>Habilitar a depuração do console

Supondo que não haja exceções sendo lançadas, a próxima etapa é habilitar a depuração do console adicionando a `loggingLevelConsole` configuração à configuração. isso enviará todos os erros e avisos de inicialização para o console do navegador (normalmente disponível por meio das ferramentas de desenvolvedor (F12)). Os erros relatados devem ser autoexplicativos e se você precisar de assistência adicional, [um problema no GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues).

> cfg: {<br />
> instrumentationKey: "INSTRUMENTATION_KEY",<br />
> loggingLevelConsole: 2<br />
> }});<br />

> [!NOTE]
> Durante a inicialização, o SDK executa algumas verificações básicas para as principais dependências conhecidas. Se eles não forem fornecidos pelo tempo de execução atual, ele relatará as falhas como mensagens de aviso ao console, mas somente se o `loggingLevelConsole` for maior que zero.

Se ainda não for possível inicializar, tente habilitar a ```enableDebug``` definição de configuração. Isso fará com que todos os erros internos sejam lançados como uma exceção (o que fará com que a telemetria seja perdida). Como se trata de uma configuração somente para desenvolvedores, ela provavelmente ficará ruidosa com exceções sendo lançadas como parte de algumas verificações internas, portanto, você precisará examinar cada exceção para determinar qual problema está causando a falha do SDK. Use a versão não reduzidos do script (Observe a extensão abaixo de ". js" e não ".min.js") caso contrário, as exceções ficarão ilegíveis.

> [!WARNING]
> Essa é uma configuração somente para desenvolvedores e nunca deve ser habilitada em um ambiente de produção completo, pois você perderá a telemetria.

> src: " https://js.monitor.azure.com/scripts/b/ai.2.min.js ",<br />
> cfg: {<br />
> instrumentationKey: "INSTRUMENTATION_KEY",<br />
> enableDebug: true<br />
> }});<br />

Se isso ainda não fornecer informações, você deverá [arquivar um problema no GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues) com os detalhes e um site de exemplo, se tiver um. Inclua a versão do navegador, o sistema operacional e os detalhes da estrutura JS para ajudar a identificar o problema.

## <a name="the-application-insights-javascript-cdn-has-been-blocked"></a>O Application Insights CDN do JavaScript foi bloqueado

A CDN sendo bloqueada é possível quando um Application Insights ponto de extremidade da CDN do SDK do JavaScript foi relatado e/ou identificado como não seguro. Quando isso ocorrer, isso fará com que o ponto de extremidade seja publicamente bloqueado e os consumidores dessas listas começarão a bloquear todo o acesso.

Para resolver, é necessário que o proprietário do ponto de extremidade da CDN funcione com a entidade de listagem de blocos que marcou o ponto de extremidade como não seguro para que ele possa ser removido da lista relevante.

Verifique se o ponto de extremidade da CDN foi identificado como não seguro.
- [Relatório de transparência do Google](https://transparencyreport.google.com/safe-browsing/search)
- [VirusTotal](https://www.virustotal.com/gui/home/url)
- [Sucuri SiteCheck](https://sitecheck.sucuri.net/)

Dependendo da frequência em que o aplicativo, o firewall ou o ambiente atualizam suas cópias locais dessas listas, pode levar um tempo considerável e/ou exigir intervenção manual pelos usuários finais ou departamentos de ti corporativos para forçar uma atualização ou permitir explicitamente que os pontos de extremidade da CDN resolvam o problema.

Se o ponto de extremidade da CDN for identificado como não seguro, [crie um tíquete de suporte](https://azure.microsoft.com/support/create-ticket/) para garantir que o problema seja resolvido assim que possível.

Para *potencialmente* ignorar esse problema mais rapidamente, você pode [alterar o ponto de extremidade da CDN do SDK](#change-the-cdn-endpoint).

### <a name="application-insights-javascript-cdn-is-blocked-by-end-user---blocked-by-browser-installed-blocker-personal-firewall"></a>Application Insights a CDN do JavaScript está bloqueada (pelo usuário final bloqueado pelo navegador; bloqueador instalado; firewall pessoal)

Verifique se os usuários finais têm:

- Instalado um plug-in de navegador (normalmente alguma forma de anúncio, malware ou bloqueador de pop-ups).
- Bloqueado (ou não permitido) os pontos de extremidade da CDN Application Insights em seu navegador ou proxy.
- Configurou uma regra de firewall que está fazendo com que o domínio CDN para o SDK seja bloqueado (ou a entrada DNS não seja resolvida).

Se tiverem configurado qualquer uma dessas opções, você precisará trabalhar com elas (ou fornecer documentação) para permitir os pontos de extremidade da CDN.

É possível que o plug-in instalado esteja usando a blocklist pública. Se esse não for o caso, é mais provável que alguma outra solução configurada manualmente ou que ele esteja usando umalist de domínio privada.

#### <a name="add-exceptions-for-cdn-endpoints"></a>Adicionar exceções para pontos de extremidade CDN

Trabalhe com seus usuários finais ou forneça documentação informando-os de que eles devem permitir que os scripts dos pontos de extremidade Application Insights CDN sejam baixados incluindo-os na lista de exceções de regra de firewall ou plug-in do navegador (irão variar de acordo com o ambiente do usuário final).

Aqui está um exemplo de como configurar o [Chrome para permitir ou bloquear o acesso aos sites.](https://support.google.com/chrome/a/answer/7532419?hl=en)

### <a name="application-insights-cdn-is-blocked-by-corporate-firewall"></a>Application Insights CDN está bloqueada (por firewall corporativo)

Se os usuários finais estiverem em uma rede corporativa, é mais provável que sua solução de firewall e que seu departamento de ti tenha implementado alguma forma de sistema de filtragem da Internet. Nesse caso, você precisará trabalhar com eles para permitir as regras necessárias para os usuários finais.

#### <a name="add-exceptions-for-cdn-endpoints-for-corporations"></a>Adicionar exceções para pontos de extremidade CDN para corporações

  Isso é semelhante à [adição de exceções para usuários finais](#add-exceptions-for-cdn-endpoints), mas você precisará trabalhar com o departamento de ti da empresa para que eles configurem os pontos de extremidade Application insights CDN a serem baixados, incluindo (ou removendo)-os em qualquer lista de bloqueios de domínio ou serviços de listagem de permissão.

  > [!WARNING]
  > Se o usuário corporativo estiver usando uma [nuvem privada](https://azure.microsoft.com/overview/what-is-a-private-cloud/) e não puder habilitar qualquer forma de exceção para fornecer aos usuários internos acesso público aos pontos de extremidade da CDN, você precisará usar os [pacotes Application insights NPM](https://www.npmjs.com/package/applicationinsights) ou hospedar o SDK do Application insights em sua própria CDN.  

### <a name="additional-troubleshooting-for-blocked-cdn"></a>Solução de problemas adicional para CDN bloqueada

> [!NOTE]
> Se os usuários estiverem usando uma [nuvem privada](https://azure.microsoft.com/overview/what-is-a-private-cloud/) e não tiverem acesso à Internet pública, você precisará hospedar o SDK em sua própria CDN ou usar pacotes NPM.

#### <a name="host-the-sdk-on-your-own-cdn"></a>Hospede o SDK em sua própria CDN

 Em vez de seus usuários finais baixarem o SDK do Application Insights da CDN pública, você poderia hospedar o SDK do Application Insights de seu próprio ponto de extremidade da CDN. É recomendável que você use uma versão específica (ia. 2. #. # .min.js) para que seja mais fácil identificar qual versão você está usando. Também atualizá-lo regularmente para a versão atual (ai.2.min.js) para que você possa aproveitar quaisquer correções de bugs e novos recursos que se tornem disponíveis.

#### <a name="use-npm-packages-to-embed-the-application-insight-sdk"></a>Usar pacotes NPM para inserir o SDK do Application insights

Em vez de usar o trecho de código e os pontos de extremidade da CDN pública, você pode usar os [pacotes NPM](https://www.npmjs.com/package/applicationinsights) para incluir o SDK como parte de seus próprios arquivos JavaScript. O SDK se tornará apenas outro pacote dentro de seus próprios scripts.

> [!NOTE]
> É recomendável que, ao usar pacotes NPM, você também use alguma forma de [conjunto de JavaScript](https://www.bing.com/search?q=javascript+bundler) para auxiliar na divisão de código e minificação.

Assim como no trecho de código, também é possível que seus próprios scripts (com ou sem usar os pacotes NPM do SDK) possam ser afetados pelos mesmos problemas de bloqueio listados aqui, de modo que, dependendo do aplicativo, dos usuários e da estrutura, talvez você queira considerar a implementação de algo semelhante à lógica no trecho para detectar e relatar esses problemas.


## <a name="next-steps"></a>Próximas etapas 
- [Obtenha ajuda adicional ao arquivar um problema no GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues)
- [Monitorar o uso da página da Web](javascript.md)
