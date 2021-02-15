---
title: Solucionando problemas de erros de conexão de saída intermitente no serviço Azure App
description: Solucionar problemas de erros de conexão intermitente e problemas de desempenho relacionados no serviço Azure App
author: v-miegge
manager: barbkess
ms.topic: troubleshooting
ms.date: 11/19/2020
ms.author: ramakoni
ms.custom: security-recommendations,fasttrack-edit
ms.openlocfilehash: 989f47c0ff60865a8e8be15e089cdcf96ab2550c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968291"
---
# <a name="troubleshooting-intermittent-outbound-connection-errors-in-azure-app-service"></a>Solucionando problemas de erros de conexão de saída intermitente no serviço Azure App

Este artigo ajuda você a solucionar problemas de erros de conexão intermitente e problemas de desempenho relacionados no [serviço Azure app](./overview.md). Este tópico fornecerá mais informações e metodologias de solução de problemas para o esgotamento de portas de SNAT (conversão de rede de endereço de origem). Se você precisar de mais ajuda a qualquer momento neste artigo, entre em contato com os especialistas do Azure no [msdn do Azure e os fóruns de Stack Overflow](https://azure.microsoft.com/support/forums/). Como alternativa, arquivo de um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.

## <a name="symptoms"></a>Sintomas

Os aplicativos e funções hospedados no serviço Azure App podem apresentar um ou mais dos seguintes sintomas:

* Tempos de resposta lentos em todas ou algumas das instâncias em um plano de serviço.
* Erros intermitentes de gateway ou 5xx **inválidos**
* Mensagens de erro de tempo limite
* Não foi possível conectar-se a pontos de extremidade externos (como SQLDB, Service Fabric, outros serviços de aplicativos, etc.)

## <a name="cause"></a>Causa

A principal causa de problemas intermitentes de conexão é atingir um limite ao fazer novas conexões de saída. Os limites que você pode pressionar incluem:

* Conexões TCP: há um limite no número de conexões de saída que podem ser feitas. O limite de conexões de saída é associado ao tamanho do trabalho usado.
* Portas SNAT: [conexões de saída no Azure](../load-balancer/load-balancer-outbound-connections.md) descreve as restrições de porta SNAT e como elas afetam as conexões de saída. O Azure usa SNAT (conversão de endereço de rede de origem) e balanceadores de carga (não expostos aos clientes) para se comunicar com endereços IP públicos. Inicialmente, cada instância no serviço de Azure App recebe um número pré-alocado de portas SNAT **128** . O limite de porta SNAT afeta a abertura de conexões com a mesma combinação de endereço e porta. Se seu aplicativo cria conexões com uma combinação de combinações de endereços e portas, você não usará as portas SNAT. As portas SNAT são usadas quando você tem chamadas repetidas para a mesma combinação de endereço e porta. Depois que uma porta for liberada, a porta estará disponível para reutilização conforme necessário. O balanceador de carga de rede do Azure recupera a porta SNAT de conexões fechadas somente depois de aguardar por 4 minutos.

Quando aplicativos ou funções abrem rapidamente uma nova conexão, eles podem esgotar rapidamente a cota alocada das portas 128. Eles são bloqueados até que uma nova porta SNAT fique disponível, seja por meio da alocação dinâmica de portas SNAT adicionais ou pela reutilização de uma porta SNAT recuperada. Se seu aplicativo ficar sem portas SNAT, ele terá problemas intermitentes de conectividade de saída. 

## <a name="avoiding-the-problem"></a>Evitando o problema

Há algumas soluções que permitem evitar limitações de porta SNAT. Entre elas estão:

* pools de conexão: ao agrupar suas conexões, você evita abrir novas conexões de rede para chamadas para o mesmo endereço e porta.
* pontos de extremidade de serviço: você não tem uma restrição de porta SNAT para os serviços protegidos com pontos de extremidade de serviço.
* pontos de extremidade privados: você não tem uma restrição de porta SNAT para serviços protegidos com pontos de extremidade privados.
* Gateway NAT: com um gateway NAT, você tem 64K portas SNAT de saída que são utilizáveis pelos recursos que enviam tráfego por meio dela.

Evitar o problema de porta SNAT significa evitar a criação de novas conexões repetidamente para o mesmo host e porta. Os pools de conexão são uma das maneiras mais óbvias de resolver esse problema.

Se o destino for um serviço do Azure que dá suporte a pontos de extremidade de serviço, você poderá evitar problemas de esgotamento de porta SNAT usando a [integração VNet regional](./web-sites-integrate-with-vnet.md) e pontos de extremidade de serviço ou pontos de extremidade privados. Quando você usa a integração VNet regional e coloca pontos de extremidade de serviço na sub-rede de integração, o tráfego de saída do aplicativo para esses serviços não terá restrições de porta SNAT de saída. Da mesma forma, se você usar a integração VNet regional e pontos de extremidade privados, não terá nenhum problema de porta SNAT de saída para esse destino. 

Se o destino for um ponto de extremidade externo fora do Azure, o uso de um gateway de NAT fornecerá 64K portas de saída de SNAT. Ele também fornece um endereço de saída dedicado que você não compartilha com ninguém. 

Se possível, melhore seu código para usar pools de conexão e evitar a situação inteira. Nem sempre é possível alterar o código rápido o suficiente para atenuar essa situação. Para os casos em que você não pode alterar seu código no tempo, aproveite as outras soluções. A melhor solução para o problema é combinar todas as soluções da melhor maneira possível. Tente usar pontos de extremidade de serviço e pontos de extremidade privados para os serviços do Azure e o gateway de NAT para o restante. 

As estratégias gerais para mitigar o esgotamento de porta SNAT são discutidas na [seção solução de problemas](../load-balancer/load-balancer-outbound-connections.md) das **conexões de saída da** documentação do Azure. Dessas estratégias, as seguintes são aplicáveis a aplicativos e funções hospedados no serviço Azure App.

### <a name="modify-the-application-to-use-connection-pooling"></a>Modificar o aplicativo para usar o pooling de conexão

* Para conexões HTTP de pool, examine [conexões http do pool com HttpClientFactory](/aspnet/core/performance/performance-best-practices#pool-http-connections-with-httpclientfactory).
* Para obter informações sobre o pool de conexões SQL Server, examine [SQL Server pooling de conexão (ADO.net)](/dotnet/framework/data/adonet/sql-server-connection-pooling).
* Para implementar o pooling com aplicativos do Entity Framework, examine [pooling de DbContext](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling).

Aqui está uma coleção de links para implementar o pool de conexões por uma pilha de solução diferente.

#### <a name="node"></a>Nó

Por padrão, as conexões para NodeJS não são mantidas ativas. Abaixo estão os bancos de dados populares e pacotes para o pool de conexões que contêm exemplos de como implementá-los.

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools)

Keep-Alive de HTTP

* [agentkeepalive](https://www.npmjs.com/package/agentkeepalive)
* [ Documentação doNode.js v 13.9.0](https://nodejs.org/api/http.html)

#### <a name="java"></a>Java

Abaixo estão as bibliotecas populares usadas para o pool de conexões JDBC, que contêm exemplos de como implementá-las: pool de conexões JDBC.

* [Tomcat 8](https://tomcat.apache.org/tomcat-8.0-doc/jdbc-pool.html)
* [C3p0](https://github.com/swaldman/c3p0)
* [HikariCP](https://github.com/brettwooldridge/HikariCP)
* [Apache DBCP](https://commons.apache.org/proper/commons-dbcp/)

Pool de conexões HTTP

* [Gerenciamento de conexão do Apache](https://hc.apache.org/httpcomponents-client-ga/tutorial/html/connmgmt.html)
* [PoolingHttpClientConnectionManager de classe](http://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/apache/http/impl/conn/PoolingHttpClientConnectionManager.html)

#### <a name="php"></a>PHP

Embora o PHP não ofereça suporte ao pool de conexões, você pode tentar usar conexões de banco de dados persistentes para o servidor back-end.
 
* Servidor MySQL

   * [Conexões mysqli](https://www.php.net/manual/mysqli.quickstart.connections.php) para versões mais recentes
   * [mysql_pconnect](https://www.php.net/manual/function.mysql-pconnect.php) para versões mais antigas do php

* Outras fontes de dados

   * [Gerenciamento de conexão PHP](https://www.php.net/manual/en/pdo.connections.php)

### <a name="modify-the-application-to-reuse-connections"></a>Modificar o aplicativo para reutilizar conexões

*  Para obter ponteiros adicionais e exemplos sobre como gerenciar conexões no Azure functions, examine [gerenciar conexões no Azure Functions](../azure-functions/manage-connections.md).

### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a>Modificar o aplicativo para usar uma lógica de repetição menos agressiva

* Para obter orientações e exemplos adicionais, reveja o [padrão de repetição](/azure/architecture/patterns/retry).

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a>Usar keepalives para redefinir o tempo limite ocioso de saída

* Para implementar keepalives para Node.js aplicativos, examine [meu aplicativo de nó fazendo chamadas de saída excessivas](./app-service-web-nodejs-best-practices-and-troubleshoot-guide.md#my-node-application-is-making-excessive-outbound-calls).

### <a name="additional-guidance-specific-to-app-service"></a>Diretrizes adicionais específicas para o serviço de aplicativo:

* Um [teste de carga](/azure/devops/test/load-test/app-service-web-app-performance-test) deve simular dados do mundo real em uma velocidade de alimentação contínua. O teste de aplicativos e funções sob estresse do mundo real pode identificar e resolver os problemas de esgotamento da porta SNAT antes do tempo.
* Verifique se os serviços de back-end podem retornar respostas rapidamente. Para solucionar problemas de desempenho com o banco de dados SQL do Azure, examine [solucionar problemas de desempenho do banco de dados SQL do Azure com Intelligent insights](../azure-sql/database/intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow).
* Escalar horizontalmente o plano do serviço de aplicativo para mais instâncias. Para saber mais sobre como dimensionar, consulte [Escalar um aplicativo no Serviço de Aplicativo do Azure](./manage-scale-up.md). Cada instância de trabalho em um plano do serviço de aplicativo é alocada a um número de portas SNAT. Se você distribuir seu uso em mais instâncias, poderá obter o uso da porta SNAT por instância abaixo do limite recomendado de 100 conexões de saída, por ponto de extremidade remoto exclusivo.
* Considere a mudança para [ambiente do serviço de aplicativo (ase)](./environment/using-an-ase.md), em que você está alocado para um único endereço IP de saída, e os limites para conexões e portas SNAT são muito maiores. Em um ASE, o número de portas SNAT por instância é baseado na [tabela de prealocação do balanceador de carga do Azure](../load-balancer/load-balancer-outbound-connections.md#snatporttable) . por exemplo, um ase com 1-50 instâncias de trabalho tem 1024 portas prealocadas por instância, enquanto um ase com instâncias de trabalho 51-100 tem 512 portas alocadas por instância.

Evitar os limites de TCP de saída é mais fácil de resolver, pois os limites são definidos pelo tamanho do seu operador. Você pode ver os limites em [área restrita limite de VM entre VMs-conexões TCP](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Nome do limite|Descrição|Pequeno (a1)|Média (a2)|Grande (a3)|Camada isolada (ASE)|
|---|---|---|---|---|---|
|conexões|Número de conexões em toda a VM|1920|3968|8064|16.000|

Para evitar limites de TCP de saída, você pode aumentar o tamanho de seus trabalhadores ou escalar horizontalmente.

## <a name="troubleshooting"></a>Solução de problemas

Conhecer os dois tipos de limites de conexão de saída e o que seu aplicativo faz é facilitar a solução de problemas. Se você souber que seu aplicativo faz muitas chamadas para a mesma conta de armazenamento, você pode suspeitar de um limite de SNAT. Se seu aplicativo cria muitas chamadas para pontos de extremidade pela Internet, você suspeita que está atingindo o limite da VM.

Se você não souber o comportamento do aplicativo o suficiente para determinar a causa rapidamente, há algumas ferramentas e técnicas disponíveis no serviço de aplicativo para ajudar com essa determinação.

### <a name="find-snat-port-allocation-information"></a>Localizar informações de alocação de porta SNAT

Você pode usar o [diagnóstico do serviço de aplicativo](./overview-diagnostics.md) para localizar informações de alocação de porta SNAT e observar a métrica de alocação de portas SNAT de um site do serviço de aplicativo. Para localizar informações de alocação de porta SNAT, siga as seguintes etapas:

1. Para acessar o diagnóstico do serviço de aplicativo, navegue até o aplicativo Web do serviço de aplicativo ou Ambiente do Serviço de Aplicativo na [portal do Azure](https://portal.azure.com/). No painel de navegação esquerdo, selecione **diagnosticar e resolver problemas**.
2. Selecionar categoria de desempenho e disponibilidade
3. Selecione bloco de esgotamento de porta SNAT na lista de blocos disponíveis na categoria. A prática é mantê-lo abaixo de 128.
Se você precisar dele, ainda poderá abrir um tíquete de suporte e o engenheiro de suporte obterá a métrica do back-end para você.

Como o uso da porta SNAT não está disponível como uma métrica, não é possível fazer a autoescala com base no uso da porta SNAT ou configurar o dimensionamento automático com base na métrica de alocação de portas SNAT.

### <a name="tcp-connections-and-snat-ports"></a>Conexões TCP e portas SNAT

As conexões TCP e SNAT não estão diretamente relacionadas. Um detector de uso de conexões TCP está incluído na folha diagnosticar e resolver problemas de qualquer site do serviço de aplicativo. Procure a frase "conexões TCP" para encontrá-la.

* As portas SNAT são usadas somente para fluxos de rede externos, enquanto o total de conexões TCP inclui conexões de loopback locais.
* Uma porta SNAT pode ser compartilhada por fluxos diferentes, se os fluxos forem diferentes em qualquer protocolo, endereço IP ou porta. A métrica de conexões TCP conta cada conexão TCP.
* O limite de conexões TCP ocorre no nível de instância de trabalho. O balanceamento de carga de saída de rede do Azure não usa a métrica de conexões TCP para limitação de porta SNAT.
* Os limites de conexões TCP são descritos em [área restrita áreas limites numéricos de VM-conexões TCP](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Nome do limite|Descrição|Pequeno (a1)|Média (a2)|Grande (a3)|Camada isolada (ASE)|
|---|---|---|---|---|---|
|conexões|Número de conexões em toda a VM|1920|3968|8064|16.000|

### <a name="webjobs-and-database-connections"></a>Trabalhos Web e conexões de banco de dados
 
Se as portas SNAT esgotarem, em que os trabalhos Web não conseguem se conectar ao banco de dados SQL, não há nenhuma métrica para mostrar quantas conexões são abertas por cada processo de aplicativo Web individual. Para encontrar o WebJob problemático, mova vários trabalhos Web para outro plano do serviço de aplicativo para ver se a situação melhora ou se um problema permanece em um dos planos. Repita o processo até encontrar o WebJob problemático.

### <a name="using-snat-ports-sooner"></a>Usando portas SNAT mais cedo

Você não pode alterar as configurações do Azure para liberar as portas SNAT usadas mais cedo, pois todas as portas SNAT serão liberadas de acordo com as condições abaixo e o comportamento será por design.
 
* Se o servidor ou o cliente enviar FINACK, a [porta SNAT será liberada](../load-balancer/load-balancer-outbound-connections.md) após 240 segundos.
* Se um RST for visto, a porta SNAT será liberada após 15 segundos.
* Se o tempo limite de ociosidade tiver sido atingido, a porta será liberada.
 
## <a name="additional-information"></a>Informações adicionais

* [SNAT com serviço de aplicativo](https://4lowtherabbit.github.io/blogs/2019/10/SNAT/)
* [Solucionar problemas de desempenho de aplicativo lento no Serviço de Aplicativo do Azure](./troubleshoot-performance-degradation.md)
