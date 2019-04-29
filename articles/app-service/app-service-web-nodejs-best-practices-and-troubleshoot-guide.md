---
title: Práticas recomendadas e solução de problemas para Node.js - Serviço de Aplicativo do Azure
description: Conheça as práticas recomendadas e as etapas de solução de problemas para aplicativos de nó no Serviço de Aplicativo do Azure.
services: app-service\web
documentationcenter: nodejs
author: ranjithr
manager: wadeh
editor: ''
ms.assetid: 387ea217-7910-4468-8987-9a1022a99bef
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/09/2017
ms.author: ranjithr
ms.custom: seodec18
ms.openlocfilehash: 321dbf891c77007952f01b32bb509a15c2ac3e6f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60853018"
---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-app-service-windows"></a>Guia de solução de problemas e práticas recomendadas para aplicativos de nó no Serviço de Aplicativo do Azure Windows

Neste artigo, você conhecerá as práticas recomendadas e as etapas de solução de problemas para [aplicativos de nó](app-service-web-get-started-nodejs.md) em execução no Serviço de Aplicativo do Azure (com [iisnode](https://github.com/azure/iisnode)).

> [!WARNING]
> Tenha cuidado ao usar as etapas de solução de problemas no site de produção. A recomendação é solucionar problemas do aplicativo em uma configuração que não seja de produção (por exemplo, o slot de preparo) e, quando o problema for corrigido, trocar o slot de preparo pelo slot de produção.
>

## <a name="iisnode-configuration"></a>Configuração de IISNOD

Este [arquivo de esquema](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) mostra todas as configurações que podem ser definidas para iisnode. Algumas das configurações que são úteis para o seu aplicativo:

### <a name="nodeprocesscountperapplication"></a>nodeProcessCountPerApplication

Essa configuração controla o número de processos de nó que são iniciados por aplicativo do IIS. O valor padrão é 1. É possível inicializar quantos node.exes de acordo com a contagem de vCPU de VM, alterando o valor para 0. O valor recomendado é 0 para a maioria dos aplicativos para que você possa utilizar todos os vCPUs do seu computador. Node.exe é de thread único para que um node.exe consuma um máximo de 1 vCPU. Para obter o desempenho máximo do seu aplicativo de nó, você deseja usar todos os vCPUs.

### <a name="nodeprocesscommandline"></a>nodeProcessCommandLine

Essa configuração controla o caminho para node.exe. Você pode definir esse valor para apontar para sua versão de node.exe.

### <a name="maxconcurrentrequestsperprocess"></a>maxConcurrentRequestsPerProcess

Essa configuração controla o número máximo de solicitações simultâneas enviadas por iisnode a cada node.exe. No Serviço de Aplicativo do Azure, o valor padrão é Infinito. É possível configurar o valor, dependendo de quantas solicitações o aplicativo recebe e com que rapidez o aplicativo processa cada solicitação.

### <a name="maxnamedpipeconnectionretry"></a>maxNamedPipeConnectionRetry

Essa configuração controla o número máximo de vezes que o iisnode tenta fazer novamente a conexão no pipe nomeado para enviar as solicitações para o node.exe. Essa configuração, em combinação com namedPipeConnectionRetryDelay, determina o tempo limite total de cada solicitação em iisnode. O valor padrão é 200 no Serviço de Aplicativo do Azure. Total Timeout em segundos = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay)/1000

### <a name="namedpipeconnectionretrydelay"></a>namedPipeConnectionRetryDelay

Essa configuração controla o tempo (em ms) pelo qual iisnode aguardará entre cada tentativa para enviar a solicitação a node.exe no pipe nomeado. O valor padrão é 250 ms.
Total Timeout em segundos = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay)/1000

Por padrão, o tempo limite total no iisnode no Serviço de Aplicativo do Azure é 200 \* 250 ms = 50 segundos.

### <a name="logdirectory"></a>logDirectory

Essa configuração controla o diretório em que o iisnode registrará em log stdout/stderr. O valor padrão é iisnode, que é relativo ao diretório de scripts principal (o diretório em que o server.js principal está presente)

### <a name="debuggerextensiondll"></a>debuggerExtensionDll

Essa configuração controla qual versão de node-inspector o iisnode usará ao depurar o aplicativo de nó. Atualmente, iisnode-inspector-0.7.3.dll e iisnode-inspector.dll são os dois únicos valores válidos para essa configuração. O valor padrão é iisnode-inspector-0.7.3.dll. A versão iisnode-inspector-0.7.3.dll usa o node-inspector-0.7.3 e usa soquetes da Web. Habilite os soquetes da Web no WebApp do Azure para usar essa versão. Consulte <https://ranjithblogs.azurewebsites.net/?p=98> para obter mais detalhes sobre como configurar o iisnode para usar o novo node-inspector.

### <a name="flushresponse"></a>flushResponse

O comportamento padrão do IIS é que ele armazena em buffer dados de resposta até 4 MB antes de liberá-los ou até o fim da resposta, o que ocorrer primeiro. O iisnode oferece uma configuração para substituir esse comportamento: para liberar um fragmento do corpo da entidade de resposta assim que o iisnode o receba de node.exe, você precisa definir o atributo iisnode/@flushResponse no web.config como 'true':

```xml
<configuration>
    <system.webServer>
        <!-- ... -->
        <iisnode flushResponse="true" />
    </system.webServer>
</configuration>
```

Habilite a liberação de cada fragmento do corpo de entidade de resposta que adiciona uma sobrecarga de desempenho que reduz a taxa de transferência do sistema em aproximadamente 5% (a partir da v0.1.13). O melhor para escopo dessa configuração apenas para nós de extremidade que requerem fluxo de resposta (por exemplo, usando o elemento `<location>` no web.config)

Além disso, para aplicativos de streaming, você também precisará definir responseBufferLimit do manipulador do iisnode como 0.

```xml
<handlers>
    <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>
</handlers>
```

### <a name="watchedfiles"></a>watchedFiles

Uma lista separada por ponto e vírgula de arquivos que são observados para alterações. Qualquer alteração em um arquivo faz com que o aplicativo seja reciclado. Cada entrada consiste em um nome de diretório opcional, bem como um nome de arquivo necessário, que são relativos ao diretório em que o ponto de entrada principal do aplicativo está localizado. Caracteres curinga são permitidos apenas na parte de nome de arquivo. O valor padrão é `*.js;iisnode.yml`

### <a name="recyclesignalenabled"></a>recycleSignalEnabled

O valor padrão é falso. Se habilitado, o aplicativo de nó pode se conectar a um pipe nomeado (variável de ambiente IISNODE\_CONTROL\_PIPE) e enviar uma mensagem de "reciclagem". Isso fará com que w3wp seja reciclado normalmente.

### <a name="idlepageouttimeperiod"></a>idlePageOutTimePeriod

O valor padrão é 0, o que significa que esse recurso está desabilitado. Quando definido como um valor maior que 0, iisnode fará uma remoção de página de todos os seus processos filho a cada 'idlePageOutTimePeriod' em milissegundos. Confira a [documentação](/windows/desktop/api/psapi/nf-psapi-emptyworkingset) para compreender o que a remoção de página significa. Essa configuração é útil para aplicativos que consomem uma grande quantidade de memória e desejam eliminar ocasionalmente a memória do disco para liberar memória RAM.

> [!WARNING]
> Tenha cuidado ao habilitar as definições de configuração a seguir em aplicativos de produção. A recomendação é não as habilitar em aplicativos de produção ativos.
>

### <a name="debugheaderenabled"></a>debugHeaderEnabled

O valor padrão é falso. Se configurado como verdadeiro, o iisnode adicionará um cabeçalho de resposta HTTP `iisnode-debug` a cada resposta HTTP que enviar ao valor do cabeçalho `iisnode-debug` como URL. Peças individuais de informações de diagnóstico podem ser obtidas observando o fragmento de URL, no entanto, uma visualização está disponível ao abrir a URL em um navegador.

### <a name="loggingenabled"></a>loggingEnabled

Essa configuração controla o registro em log de stdout e stderr por iisnode. Iisnode capturará stdout/stderr dos processos de nó que ele iniciar e gravará no diretório especificado na configuração 'logDirectory'. Depois que isso for habilitado, o aplicativo gravará logs no sistema de arquivos e, dependendo da quantidade de logs realizados pelo aplicativo, poderá haver implicações de desempenho.

### <a name="deverrorsenabled"></a>devErrorsEnabled

O valor padrão é falso. Quando definido como true, iisnode exibirá o código de status HTTP e o código de erro Win32 no navegador. O código win32 será útil na depuração de certos tipos de problemas.

### <a name="debuggingenabled-do-not-enable-on-live-production-site"></a>debuggingEnabled (não habilitar em sites de produção ativos)

Essa configuração controla o recurso de depuração. Iisnode é integrado a node-inspector. Habilitando essa configuração, você habilita a depuração do aplicativo de nó. Ao habilitar essa configuração, o iisnode cria arquivos node-inspector no diretório "debuggerVirtualDir" na primeira solicitação de depuração para o aplicativo de nó. É possível carregar o node-inspector enviando uma solicitação para `http://yoursite/server.js/debug`. Você pode controlar o segmento de URL de depuração com a configuração 'debuggerPathSegment'. Por padrão, debuggerPathSegment='debug'. É possível definir `debuggerPathSegment` para um GUID, por exemplo, para que seja mais difícil ser descoberto por outras pessoas.

Leia [Depurar aplicativos node.js no Windows](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) para obter mais detalhes sobre a depuração.

## <a name="scenarios-and-recommendationstroubleshooting"></a>Cenários e recomendações/solução de problemas

### <a name="my-node-application-is-making-excessive-outbound-calls"></a>O aplicativo de nó está fazendo chamadas de saída excessivas

Muitos aplicativos desejam fazer conexões de saída como parte de suas operações normais. Por exemplo, quando uma solicitação chega, o aplicativo de nó deseja contatar uma API REST em outro lugar e obter algumas informações para processar a solicitação. Convém usar um agente keep alive ao fazer chamadas http ou https. Você poderia usar o módulo agentkeepalive como o agente keep alive ao fazer essas chamadas de saída.

O módulo agentkeepalive garante que os soquetes sejam reutilizados na VM do WebApp do Azure. Criar um novo soquete em cada solicitação de saída adiciona sobrecarga ao aplicativo. O aplicativo reutilizar soquetes para solicitações de saída garante que o aplicativo não exceda os maxSockets alocados por VM. A recomendação no Serviço de Aplicativo do Azure é definir o valor de maxSockets do agentKeepAlive como um total de (4 instâncias de node.exe \* 40 maxSockets/instância) 160 soquetes por VM.

Exemplo de configuração [agentKeepALive](https://www.npmjs.com/package/agentkeepalive):

```nodejs
let keepaliveAgent = new Agent({
    maxSockets: 40,
    maxFreeSockets: 10,
    timeout: 60000,
    keepAliveTimeout: 300000
});
```

> [!IMPORTANT]
> Este exemplo pressupõe que haja quatro node.exe em execução na VM. Se houver um número diferente de node.exe em execução na VM, você precisará modificar a configuração maxSockets de forma adequada.
>

#### <a name="my-node-application-is-consuming-too-much-cpu"></a>O aplicativo de nó está consumindo muita CPU

Você pode receber uma recomendação do Serviço de Aplicativo do Azure no portal sobre o alto consumo de CPU. Você também pode configurar monitores para observar determinadas [métricas](web-sites-monitor.md). Ao conferir o uso de CPU no [Painel do Portal do Azure](../azure-monitor/app/web-monitor-performance.md), verifique os valores máximo de CPU para que você não deixe de ver os valores de pico.
Se você achar que o aplicativo está consumindo muita CPU e não consegue determinar o motivo, você poderá analisar o aplicativo de nó para descobrir.

#### <a name="profiling-your-node-application-on-azure-app-service-with-v8-profiler"></a>Criar o perfil do aplicativo de nó no Serviço de Aplicativo do Azure com o V8-Profiler

Por exemplo, digamos que você tenha um aplicativo hello world para o qual deseje criar o perfil, conforme mostrado abaixo:

```nodejs
const http = require('http');
function WriteConsoleLog() {
    for(let i=0;i<99999;++i) {
        console.log('hello world');
    }
}

function HandleRequest() {
    WriteConsoleLog();
}

http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/html'});
    HandleRequest();
    res.end('Hello world!');
}).listen(process.env.PORT);
```

Vá para o site do Console de Depuração `https://yoursite.scm.azurewebsites.net/DebugConsole`

Vá para o diretório site/wwwroot. Você verá um prompt de comando conforme mostrado no exemplo a seguir:

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Execute o comando `npm install v8-profiler`.

Esse comando instala o v8-profiler no diretório de módulos de nó \_e todas as dependências.
Agora, edite o server.js para criar o perfil do aplicativo.

```nodejs
const http = require('http');
const profiler = require('v8-profiler');
const fs = require('fs');

function WriteConsoleLog() {
    for(let i=0;i<99999;++i) {
        console.log('hello world');
    }
}

function HandleRequest() {
    profiler.startProfiling('HandleRequest');
    WriteConsoleLog();
    fs.writeFileSync('profile.cpuprofile', JSON.stringify(profiler.stopProfiling('HandleRequest')));
}

http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/html'});
    HandleRequest();
    res.end('Hello world!');
}).listen(process.env.PORT);
```

As alterações anteriores criarão o perfil da função WriteConsoleLog e gravarão a saída do perfil no arquivo 'profile.cpuprofile' no site wwwroot. Envie uma solicitação para o aplicativo. Você verá um arquivo 'profile.cpuprofile' criado no site wwwroot.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Fazer o download do arquivo e abri-lo com as ferramentas do Chrome F12. Pressione F12 no Chrome e clique na guia **Perfis**. Clique no botão **Carregar**. Selecione o arquivo profile.cpuprofile que você baixou. Clique no perfil que você acabou de carregar.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Você verá que 95% do tempo foi consumido pela função WriteConsoleLog. A saída também mostra os números de linha e os arquivos de origem exatos que causaram o problema.

### <a name="my-node-application-is-consuming-too-much-memory"></a>Meu aplicativo de nó está consumindo muita memória

Se o seu aplicativo estiver consumindo muita memória, você verá um aviso do Serviço de Aplicativo do Azure em seu portal sobre o alto consumo de memória. Você pode configurar monitores para observar determinadas [métricas](web-sites-monitor.md). Ao conferir o uso de CPU no [Painel do Portal do Azure](../azure-monitor/app/web-monitor-performance.md), verifique os valores máximo de CPU para a memória para que você não deixe de ver os valores de pico.

#### <a name="leak-detection-and-heap-diff-for-nodejs"></a>Detecção de vazamento e Comparação de Heap para node.js

Você pode usar [node-memwatch](https://github.com/lloyd/node-memwatch) para ajudá-lo a identificar perdas de memória.
Você pode instalar `memwatch` como v8-profiler e editar o código para capturar e comparar heaps para identificar os vazamentos de memória no aplicativo.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>Os arquivos node.exe estão sendo encerrados aleatoriamente

Há algumas razões pelas quais o node.exe é desligado aleatoriamente:

1. Seu aplicativo está lançando exceções não identificadas – confira o arquivo d:\\home\\LogFiles\\Application\\logging-errors.txt para obter detalhes sobre a exceção lançada. Esse arquivo tem o rastreamento de pilha para ajudar a depurar e corrigir o aplicativo.
2. O aplicativo está consumindo muita memória, o que está afetando outros processos desde o início. Se a memória total da VM estiver próxima de 100%, os node.exe poderão ser encerrados pelo gerenciador de processos. O gerenciador de processos encerra alguns processos para permitir que outros processos tenham a oportunidade de realizar algum trabalho. Para corrigir esse problema, analise o aplicativo para perda de memória. Se o aplicativo exigir grandes quantidades de memória, dimensione para uma VM maior (o que aumenta a RAM disponível para a VM).

### <a name="my-node-application-does-not-start"></a>O aplicativo de nó não é iniciado

Se o aplicativo estiver retornando Erros 500 na inicialização, pode haver alguns motivos:

1. Node.exe não está presente no local correto. Verifique a configuração nodeProcessCommandLine.
2. O arquivo de script principal não está presente no local correto. Verifique web.config e confira se o nome do arquivo de script principal na seção de manipuladores corresponde ao arquivo de script principal.
3. A configuração de Web.config não está correta. Verifique os nomes/valores das configurações.
4. Inicialização a Frio – o aplicativo está demorando muito para ser inicializado. Se o aplicativo demorar mais do que (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay)/1000 segundos, iisnode retornará um erro 500. Aumente os valores dessas configurações para corresponder à hora de início do aplicativo e impedir que iisnode atinja o tempo limite e retorne o erro 500.

### <a name="my-node-application-crashed"></a>Meu aplicativo de nó falhou

O aplicativo está lançando exceções não identificadas – Verifique o arquivo `d:\\home\\LogFiles\\Application\\logging-errors.txt` para obter detalhes sobre a exceção gerada. Esse arquivo tem o rastreamento de pilha para ajudar a diagnosticar e corrigir o aplicativo.

### <a name="my-node-application-takes-too-much-time-to-start-cold-start"></a>O aplicativo de nó demora muito para ser inicializado (Inicialização a Frio)

A causa comum para os tempos de início longos do aplicativo é um número alto de arquivos nos módulos do nó\_. O aplicativo tenta carregar a maioria desses arquivos ao iniciar. Por padrão, como os arquivos estão armazenados no compartilhamento de rede no Serviço de Aplicativo do Azure, o carregamento de muitos arquivos pode demorar algum tempo.
Algumas soluções para tornar esse processo mais rápido são:

1. Verifique se você tem uma estrutura de dependência simples e nenhuma dependência duplicada usando npm3 para instalar os módulos.
2. Tente carregar lentamente os node\_modules e não carregue todos os módulos na inicialização do aplicativo. Para módulos de carregamento Lento, a chamada para exigir (‘módulo’) deve ser feita quando você realmente precisar do módulo dentro da função antes da primeira execução do código do módulo.
3. O Serviço de Aplicativo do Azure oferece um recurso chamado cache local. Esse recurso copia o conteúdo do compartilhamento de rede para o disco local na VM. Como os arquivos são locais, o tempo de carregamento do nó\_módulos é muito mais rápido.

## <a name="iisnode-http-status-and-substatus"></a>Substatus e status http de IISNODE

O `cnodeconstants` [arquivo de origem](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) lista todas as combinações possíveis de status/substatus que o iisnode pode retornar devido a um erro.

Habilite FREB para que o aplicativo veja o código de erro win32 (habilite FREB apenas em sites que não sejam produção, por motivos de desempenho).

| Status Http | Substatus Http | Razão Possível? |
| --- | --- | --- |
| 500 |1000 |Houve algum problema ao expedir a solicitação para IISNODE – Verifique se node.exe foi iniciado. Node.exe pode ter falhado ao inicializar. Verifique se há erros na configuração de web.config. |
| 500 |1001 |- Win32Error 0x2 - o aplicativo não está respondendo à URL. Verifique as regras de regravação de URL ou se o aplicativo expresso tem as rotas corretas definidas. -Win32Error 0x6d – o pipe nomeado está ocupado – Node.exe não está aceitando solicitações porque o pipe está ocupado. Verifique o alto uso da cpu. - Outros erros – verifique se node.exe falhou. |
| 500 |1002 |Falha de node.exe – confira d:\\home\\LogFiles\\logging-errors.txt para o rastreamento de pilha. |
| 500 |1003 |Problema na configuração de pipe – A configuração de pipe nomeado está incorreta. |
| 500 |1004-1018 |Erro ao enviar a solicitação ou ao processar a resposta de/para node.exe. Verifique se node.exe falhou. verifique d:\\home\\LogFiles\\logging-errors.txt para rastrear a pilha. |
| 503 |1000 |Não há memória suficiente para alocar mais conexões de pipe nomeado. Verifique por que o aplicativo está consumindo tanta memória. Verifique o valor da configuração maxConcurrentRequestsPerProcess. Se não estiver definido como infinito e houver muitas solicitações, aumente o valor para evitar o erro. |
| 503 |1001 |Não foi possível expedir a solicitação para node.exe porque o aplicativo está sendo reciclado. Depois que o aplicativo for reciclado, as solicitações deverão ser atendidas normalmente. |
| 503 |1002 |Verifique o código de erro win32 para obter o motivo real – não foi possível distribuir a solicitação para um node.exe. |
| 503 |1003 |O pipe nomeado é muito Ocupado – Verifique se o node.exe está consumindo CPU excessiva |

NODE.exe tem uma configuração chamada `NODE_PENDING_PIPE_INSTANCES`. Em Serviço de Aplicativo do Azure, esse valor é definido como 5000. Isso significa que node.exe pode aceitar 5.000 solicitações por vez no pipe nomeado. Esse valor deve ser suficiente para a maioria dos aplicativos de nó em execução no Serviço de Aplicativo do Azure. Você não deve ver 503.1003 no Serviço de Aplicativo do Azure devido ao alto valor para `NODE_PENDING_PIPE_INSTANCES`

## <a name="more-resources"></a>Mais recursos

Siga estes links para saber mais sobre aplicativos do node.js no Serviço de Aplicativo do Azure.

* [Get started with Node.js web apps in Azure App Service (Introdução aos aplicativos Web do Node.js no Serviço de Aplicativo do Azure)](app-service-web-get-started-nodejs.md)
* [Como depurar um aplicativo Web Node.js no Serviço de Aplicativo do Azure](app-service-web-tutorial-nodejs-mongodb-app.md)
* [Usando Módulos no Node.js com aplicativos do Microsoft Azure](../nodejs-use-node-modules-azure-apps.md)
* [Aplicativos Web do Serviço de Aplicativo do Azure: Node.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Centro de desenvolvedores do Node. js](../nodejs-use-node-modules-azure-apps.md)
* [Explorar o Console de Depuração Super Secret Kudu](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)
