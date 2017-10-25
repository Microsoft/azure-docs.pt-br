---
title: "Solução Monitor de Desempenho de Rede no Azure Log Analytics | Microsoft Docs"
description: O Monitor de Desempenho de Rede do Azure Log Analytics ajuda a monitorar o desempenho de suas redes quase em tempo real para detectar e localizar afunilamentos de desempenho de rede.
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: banders
ms.openlocfilehash: c6568e491429f6046ab164ab5eacd0ae5846e201
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="network-performance-monitor-solution-in-log-analytics"></a>Solução Monitor de Desempenho de Rede no Azure Log Analytics

![Símbolo do Monitor de Desempenho de Rede](./media/log-analytics-network-performance-monitor/npm-symbol.png)

Este documento descreve como configurar e usar a solução de Monitor de Desempenho de Rede no Log Analytics, que ajuda a monitorar o desempenho de suas redes quase em tempo real para detectar e localizar afunilamentos de desempenho de rede. Com a solução de Monitor de Desempenho de Rede, você pode monitorar a perda e a latência entre duas redes, servidores ou sub-redes. O Monitor de Desempenho de Rede detecta problemas de tráfego de rede, como blackholing, erros de roteamento e problemas que os métodos de monitoramento de rede convencionais não são capazes de detectar. O Monitor de Desempenho de Rede gera alertas e notifica como e quando um limite é ultrapassado para um link de rede. Esses limites podem ser aprendidos automaticamente pelo sistema ou você pode configurá-los para usar regras de alerta personalizadas. O Monitor de Desempenho de Rede garante a detecção oportuna de problemas de desempenho de rede e localiza a origem do problema para determinado segmento de rede ou dispositivo.

Você pode detectar problemas de rede com o painel de solução, que exibe informações resumidas sobre a rede, incluindo eventos recentes de integridade de rede, links de rede não íntegros e links de sub-rede que estão enfrentando latência e alta perda de pacotes. Você pode executar uma busca detalhada em um link de rede para exibir o status de integridade atual de links de sub-rede, bem como links de nó para nó. Você também pode exibir as tendências históricas de perda e latência no nível de rede, sub-rede e nó para nó. Você pode detectar problemas de rede transitórios exibindo gráficos de tendências históricas de perda de pacotes e latência e localizar afunilamentos de rede em um mapa de topologia. O gráfico de topologia interativo permite que você visualize as rotas de rede salto por salto e determine a origem do problema. Assim como em outras soluções, você pode usar a Pesquisa de Log para vários requisitos de análise para criar relatórios personalizados com base nos dados coletados pelo Monitor de Desempenho de Rede.

A solução usa transações sintéticas como mecanismo principal para detectar falhas na rede. Assim, você pode usá-la sem levar em consideração o fornecedor ou modelo de um dispositivo de rede específico. Ele funciona no local, na nuvem (IaaS) e em ambientes híbridos. A solução detecta automaticamente a topologia de rede e várias rotas em sua rede.

Produtos de monitoramento de rede típicos se concentram em como monitorar a integridade do dispositivo de rede (roteadores, comutadores etc.), mas não fornecem informações sobre a qualidade real de conectividade de rede entre dois pontos, enquanto o Monitor de Desempenho de Rede faz isso.

### <a name="using-the-solution-standalone"></a>Usar a solução autônoma
Para monitorar a qualidade das conexões de rede entre cargas de trabalho críticas, redes, datacenters ou sites do Pffice, e você pode usar a solução de Monitor de Desempenho de Rede por si só para monitorar a integridade da conectividade entre:

* vários datacenters ou sites do Office conectados por meio de uma rede pública ou privada
* cargas de trabalho críticas que estão executando aplicativos de linha de negócios
* Serviços de nuvem pública, como Microsoft Azure ou AWS (Amazon Web Services) e redes locais, se você tiver IaaS (VM) disponível e tiver gateways configurados para permitir a comunicação entre redes locais e de nuvem
* Redes do Azure e locais quando você usar o ExpressRoute

### <a name="using-the-solution-with-other-networking-tools"></a>Usar a solução com outras ferramentas de rede
Se quiser monitorar um aplicativo de linha de negócios, você poderá usar a solução de Monitor de Desempenho de Rede como uma solução complementar para outras ferramentas de rede. Uma rede lenta pode levar a aplicativos lentos. O Monitor de Desempenho de Rede pode ajudá-lo a investigar problemas de desempenho de aplicativos causados por problemas de rede subjacentes. Como a solução não requer acesso a dispositivos de rede, o administrador do aplicativos não precisa contar com uma equipe de rede para fornecer informações sobre como a rede está afetando os aplicativos.

Além disso, se você já investiu em outras ferramentas de monitoramento de rede, a solução pode complementá-las, pois soluções de monitoramento de rede mais tradicionais não fornecem informações sobre métricas de desempenho de rede de ponta a ponta, como perda e latência.  A solução de Monitor de Desempenho de Rede pode ajudar a preencher essa lacuna.

## <a name="installing-and-configuring-agents-for-the-solution"></a>Instalar e configurar agentes para a solução
Use os processos básicos para instalar agentes em [Conectar computadores com Windows à Análise de Log](log-analytics-windows-agents.md) e [Conectar o Operations Manager à Análise de Log](log-analytics-om-agents.md).

> [!NOTE]
> Você precisará instalar pelo menos dois agentes para ter dados suficientes para descobrir e monitorar os recursos de rede. Caso contrário, a solução permanecerá em um estado de configuração até você instalar e configurar agentes adicionais.
>
>

### <a name="where-to-install-the-agents"></a>Onde instalar os agentes
Antes de instalar agentes, considere a topologia da rede e quais partes da rede você deseja monitorar. Recomendamos que você instale mais de um agente para cada sub-rede que deseja monitorar. Em outras palavras, para cada sub-rede que você deseja monitorar, escolha dois ou mais servidores ou máquinas virtuais e instale o agente neles.

Se você não tiver certeza sobre a topologia da rede, instale os agentes em servidores com cargas de trabalho críticas em que você deseja monitorar o desempenho da rede. Por exemplo, convém manter o controle de uma conexão de rede entre um servidor Web e um servidor que executa o SQL Server. Neste exemplo, você deve instalar um agente em ambos os servidores.

Os agentes monitoram a conectividade de rede (links) entre os hosts, não os próprios hosts. Portanto, para monitorar um link de rede, você deve instalar agentes em ambas as extremidades do link.

### <a name="configure-agents"></a>Configurar agentes

Se você pretende usar o protocolo ICMP para transações sintéticas, você precisa habilitar regras de firewall de forma confiável utilizando o ICMP:

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow
```


Se você pretende usar o protocolo TCP, precisará abrir portas de firewall para esses computadores para garantir que os agentes possam se comunicar. Você precisa baixar e executar o [script do PowerShell EnableRules.ps1](https://gallery.technet.microsoft.com/OMS-Network-Performance-04a66634) sem parâmetros em uma janela do PowerShell com privilégios administrativos.

O script cria chaves do Registro necessárias para o Monitor de Desempenho de Rede e cria regras de firewall do Windows para permitir que os agentes criem conexões TCP entre si. As chaves do Registro criadas pelo script também podem especificar se é preciso registrar os logs de depuração e o caminho para o arquivo de log. Também é definida a porta TCP de agente usada para comunicação. Os valores dessas chaves são definidos automaticamente pelo script. Portanto, você não deve alterar manualmente as chaves.

A porta aberta por padrão é 8084. Você pode usar uma porta personalizada fornecendo o parâmetro `portNumber` para o script. No entanto, a mesma porta deve ser usada em todos os computadores em que o script é executado.

> [!NOTE]
> O script EnableRules.ps1 configura regras de firewall do Windows somente no computador em que o script é executado. Se tiver um firewall de rede, você deverá verificar se ele permite o tráfego destinado à porta TCP usada pelo Monitor de Desempenho de Rede.
>
>

## <a name="configuring-the-solution"></a>Configurar a solução
Use as informações a seguir para instalar e configurar a solução.

1. A solução de Monitor de Desempenho de Rede obtém dados de computadores que executam o Windows Server 2008 SP 1 ou posterior ou o Windows 7 SP1 ou posterior, que são os mesmos requisitos do MMA (Microsoft Monitoring Agent). Agentes NPM também podem executar na área de trabalho/sistemas operacionais Windows (Windows 10, Windows 8.1, Windows 8 e Windows 7).
    >[!NOTE]
    >Os agentes para sistemas operacionais Windows Server oferecem suporte a TCP e ICMP como os protocolos para transação sintética. No entanto, os agentes para sistemas operacionais Windows oferecem suporte somente a ICMP como o protocolo para transação sintética.

2. Adicione a solução de Monitor de Desempenho de Rede ao seu espaço de trabalho do [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview) usando o processo descrito em [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md).  
   ![Símbolo do Monitor de Desempenho de Rede](./media/log-analytics-network-performance-monitor/npm-symbol.png)
3. No portal do OMS, você verá um novo bloco intitulado **Monitor de Desempenho de Rede** com a mensagem *A solução requer configuração adicional*. Você precisará configurar a solução para adicionar redes com base em sub-redes e nós que são detectados pelos agentes. Clique em **Monitor de Desempenho de Rede** para começar a configurar a rede padrão.  
   ![A solução requer configuração adicional](./media/log-analytics-network-performance-monitor/npm-config.png)

### <a name="configure-the-solution-with-a-default-network"></a>Configurar a solução com uma rede padrão
Na página de configuração, você verá uma única rede chamada **Padrão**. Se você ainda não definiu redes, todas as sub-redes descobertas automaticamente são colocadas na rede padrão.

Sempre que você criar uma rede, adicione uma sub-rede para ela. Essa sub-rede será removida da rede padrão. Se você excluir uma rede, todas as suas sub-redes serão retornadas automaticamente para a rede padrão.

Em outras palavras, a rede padrão é o contêiner para todas as sub-redes que não estão contidas em qualquer rede definida pelo usuário. Você não pode editar nem excluir a rede padrão. Ela sempre permanece no sistema. No entanto, você poderá criar tantas redes quantas forem necessárias.

Na maioria dos casos, as sub-redes na organização serão organizadas em mais de uma rede, e você deve criar uma ou mais redes para agrupar logicamente as sub-redes.

### <a name="create-new-networks"></a>Criar novas redes
Uma rede no Monitor de Desempenho de Rede é um contêiner de sub-redes. Você pode criar uma rede com qualquer nome que deseje e adicionar as sub-redes à rede. Por exemplo, você pode criar uma rede chamada *Building1* e adicionar sub-redes ou pode criar uma rede chamada *DMZ* e adicionar todas as sub-redes que pertencem à zona desmilitarizada para essa rede.

#### <a name="to-create-a-new-network"></a>Para criar uma nova rede
1. Clique em **Adicionar rede** e digite o nome e a descrição da rede.
2. Selecione uma ou mais sub-redes e clique em **Adicionar**.
3. Clique em **Salvar** para salvar a configuração.  
   ![adicionar rede](./media/log-analytics-network-performance-monitor/npm-add-network.png)

### <a name="wait-for-data-aggregation"></a>Aguardar a agregação de dados
Depois que você salvar a configuração pela primeira vez, a solução iniciará a coleta de informações de perda e latência de pacotes de rede entre os nós em que os agentes estão instalados. Esse processo pode levar algum tempo, às vezes, 30 minutos. Durante esse estado, o bloco de Monitor de Desempenho de Rede na página Visão geral exibe uma mensagem que indica *agregação de dados em andamento*.

![agregação de dados em andamento](./media/log-analytics-network-performance-monitor/npm-aggregation.png)

Quando os dados forem carregados, você verá o bloco atualizado do Monitor de Desempenho de Rede mostrando dados.

![Bloco do Monitor de Desempenho de Rede](./media/log-analytics-network-performance-monitor/npm-tile.png)

Clique no bloco para exibir o painel do Monitor de Desempenho de Rede.

![Painel do Monitor de Desempenho de Rede](./media/log-analytics-network-performance-monitor/npm-dash01.png)

### <a name="edit-monitoring-settings-for-subnets"></a>Editar configurações de monitoramento para sub-redes
Todas as sub-redes em que pelo menos um agente foi instalado são listadas na guia **Sub-redes** na página de configuração.

#### <a name="to-enable-or-disable-monitoring-for-particular-subnetworks"></a>Para habilitar ou desabilitar o monitoramento de sub-redes específicas
1. Marque ou desmarque a caixa ao lado de **ID da sub-rede** e verifique se **Usar para monitoramento** está marcado ou desmarcado, conforme apropriado. Você pode marcar ou desmarcar várias sub-redes. Quando desabilitado, as sub-redes não são monitoradas, pois os agentes serão atualizados para parar de executar o ping para outros agentes.
2. Escolha os nós que você deseja monitorar para uma sub-rede específica, selecionando a sub-rede na lista e movendo os nós necessários entre as listas que contêm nós não monitorados e monitorados.
   Você pode adicionar uma **Descrição** personalizada para a sub-rede, se desejar.
3. Clique em **Salvar** para salvar a configuração.  
   ![editar sub-rede](./media/log-analytics-network-performance-monitor/npm-edit-subnet.png)

### <a name="choose-nodes-to-monitor"></a>Escolher nós para monitorar
Todos os nós que têm um agente instalado neles estão listados na guia **Nós**.

#### <a name="to-enable-or-disable-monitoring-for-nodes"></a>Para habilitar ou desabilitar o monitoramento de nós
1. Marque ou desmarque os nós que você deseja monitorar ou parar de monitorar.
2. Clique em **Usar para Monitoramento** ou desmarque essa opção, conforme apropriado.
3. Clique em **Salvar**.  
   ![habilitar o monitoramento de nós](./media/log-analytics-network-performance-monitor/npm-enable-node-monitor.png)

### <a name="set-monitoring-rules"></a>Definir regras de monitoramento
O Monitor de Desempenho de Rede gera eventos de integridade sobre a conectividade entre um par de nós ou links de rede ou sub-rede quando um limite é ultrapassado. Esses limites podem ser aprendidos automaticamente pelo sistema ou você pode configurar regras de alerta personalizadas.

A *Regra padrão* é criada pelo sistema e cria um evento de integridade sempre que a perda ou a latência entre qualquer par de redes ou links de sub-rede viola o limite aprendido pelo sistema. Você pode optar por desabilitar a regra padrão e criar regras de monitoramento personalizadas

#### <a name="to-create-custom-monitoring-rules"></a>Para criar regras de monitoramento personalizadas
1. Clique em **Adicionar Regra** na guia **Monitor** e digite o nome da regra e uma descrição.
2. Selecione nas listas o par de links de rede ou sub-rede para monitorar.
3. Primeiro, selecione a rede na qual a(s) primeira(s) sub-rede(s) de seu interesse está(ão) contida(s), no menu suspenso de rede, e selecione a(s) sub-rede(s) no menu suspenso da sub-rede correspondente.
   Selecione **Todas as sub-redes** se desejar monitorar todas as sub-redes em um link de rede. Da mesma forma, selecione outra(s) sub-rede(s) de seu interesse. Você pode clicar em **Adicionar Exceção** para excluir o monitoramento de links de sub-rede específicos da seleção feita.
4. Escolha entre os protocolos TCP e ICMP para executar transações sintéticas.
5. Se não quiser criar eventos de integridade para os itens que você selecionou, desmarque **Ativar o monitoramento de integridade nos links cobertos por essa regra**.
6. Escolha as condições de monitoramento.
   Você pode definir limites personalizados para geração de eventos de integridade digitando os valores de limite. Sempre que o valor da condição ultrapassar o limite selecionado para o par de rede/sub-rede selecionado, será gerado um evento de integridade.
7. Clique em **Salvar** para salvar a configuração.  
   ![criar regra de monitoramento personalizada](./media/log-analytics-network-performance-monitor/npm-monitor-rule.png)

Depois de salvar uma regra de monitoramento, você pode integrar essa regra ao Gerenciamento de Alertas clicando em **Criar Alerta**. Uma regra de alerta é criada automaticamente com a consulta de pesquisa e outros parâmetros necessários são preenchidos automaticamente. Usando uma regra de alerta, você pode receber alertas baseados em email, além de alertas existentes em NPM. Os alertas também podem disparar ações corretivas com runbooks ou podem integrar soluções de gerenciamento de serviço existentes usando webhooks. Você pode clicar em **Gerenciar Alerta** para editar as configurações de alerta.

### <a name="choose-the-right-protocol-icmp-or-tcp"></a>Escolher o protocolo certo – ICMP ou TCP

O Monitor de Desempenho de Rede (NPM) usa transações sintéticas para calcular métricas de desempenho de rede como perda de pacote e latência de link. Para entender isso melhor, considere um agente NPM conectado a uma extremidade de um link de rede. Esse agente NPM envia pacotes de teste para um segundo agente NPM conectado a outra extremidade da rede. O segundo agente responde com pacotes de resposta. Esse processo é repetido algumas vezes. Medindo o número de respostas e o tempo necessário para receber cada resposta, o primeiro agente NPM avalia a latência de link e os descartes de pacotes.

O formato, o tamanho e a sequência desses pacotes são determinados pelo protocolo que você escolheu ao criar regras de monitoramento. Com base no protocolo dos pacotes, os dispositivos de rede intermediários (roteadores, comutadores etc.) podem processar esses pacotes de maneira diferente. Consequentemente, a opção de protocolo afeta a precisão dos resultados. E a escolha de protocolo também determina se será necessário executar alguma etapa manual após implantar a solução NPM.

O NPM permite que você escolha entre os protocolos TCP e ICMP para executar transações sintéticas.
Se você escolher o ICMP quando criar uma regra de transação sintética, os agentes NPM usarão mensagens de eco ICMP para calcular a latência de rede e a perda de pacote. O Eco ICMP usa a mesma mensagem enviada pelo utilitário Ping convencional. Quando você usa o TCP como protocolo, os agentes NPM enviam um pacote TCP SYN pela rede. Isso é seguido pela conclusão de um handshake TCP e, em seguida, a remoção da conexão usando pacotes RST.

#### <a name="points-to-consider-before-choosing-the-protocol"></a>Pontos a serem considerados antes de escolher o protocolo
Antes de escolher um protocolo para uso, considere as seguintes informações:

##### <a name="discovering-multiple-network-routes"></a>Descobrindo várias rotas de rede
O TCP é mais preciso ao descobrir várias rotas e exige menos agentes em cada sub-rede. Por exemplo, um ou dois agentes que usem TCP podem descobrir todos os caminhos redundantes entre sub-redes. Entretanto, você precisa de vários agentes usando o ICMP para alcançar resultados semelhantes. Usando o ICMP e, se você tiver *N* número de rotas entre duas sub-redes, precisará de mais de 5*N* agentes na sub-rede de origem ou de destino.

##### <a name="accuracy-of-results"></a>Precisão dos resultados
Roteadores e comutadores tendem a atribuir a prioridade mais baixa para pacotes de eco ICMP em comparação com pacotes TCP. Em determinadas situações, quando os dispositivos de rede estão sobrecarregados, os dados obtidos pelo TCP refletem mais a perda e a latência apresentadas pelos aplicativos. Isso ocorre porque a maioria do tráfego do aplicativo flui pelo TCP. Nesses casos, o ICMP fornece resultados menos precisos em comparação ao TCP.

##### <a name="firewall-configuration"></a>Configuração do firewall
O protocolo TCP exige que os pacotes TCP sejam enviados a uma porta de destino. A porta padrão usada por agentes NPM é 8084, mas você pode alterar isso quando configurar agentes. Portanto, você precisa garantir que seus firewalls de rede ou regras de NSG (no Azure) estejam permitindo o tráfego na porta. Você também precisa certificar-se de que o firewall local nos computadores em que os agentes são instalados esteja configurado para permitir o tráfego nesta porta.

Você pode usar scripts do PowerShell para configurar regras de firewall em computadores que executam o Windows, no entanto, você precisa configurar manualmente o firewall da rede.

Por outro lado, o ICMP não funciona usando a porta. Na maioria dos cenários de negócios, o tráfego do ICMP é permitido por meio de firewalls para que você use ferramentas de diagnóstico de rede como o utilitário Ping. Portanto, se você puder executar Ping de um computador para o outro, poderá usar o protocolo ICMP sem ter que configurar firewalls manualmente.

> [!NOTE]
> Caso você não tenha certeza de qual protocolo usar, escolha o ICMP para começar. Se você não estiver satisfeito com os resultados, poderá sempre mudar para TCP mais tarde.


#### <a name="how-to-switch-the-protocol"></a>Como mudar o protocolo

Se você optar por usar o ICMP durante a implantação, poderá mudar para TCP a qualquer momento, editando a regra de monitoramento padrão.

##### <a name="to-edit-the-default-monitoring-rule"></a>Para editar a regra de monitoramento padrão
1.  Navegue até **Desempenho da Rede** > **Monitorar** > **Configurar** > **Monitorar** e, em seguida, clique em **Regra padrão**.
2.  Role até a seção **Protocolo** e selecione o protocolo que você deseja usar.
3.  Clique em **Salvar** para aplicar a configuração.

Mesmo se a regra padrão for usar um protocolo específico, você poderá criar novas regras com um protocolo diferente. Você pode até mesmo criar uma combinação de regras em que algumas das regras usem ICMP e a outra use TCP.




## <a name="data-collection-details"></a>Detalhes da coleta de dados
O Monitor de Desempenho de Rede usa pacotes de handshake TCP SYN-SYNACK-ACK quando TCP é escolhido e rICMP ECHO ICMP ECHO REPLY quando ICMP é escolhido como o protocolo para coletar informações de latência e de perda. O traceroute também é usado para obter informações sobre a topologia.

A tabela a seguir mostra os métodos de coleta de dados e outros detalhes sobre como os dados são coletados para o Monitor de Desempenho de Rede.

| plataforma | Agente direto | Agente SCOM | Armazenamento do Azure | SCOM necessário? | Os dados do agente SCOM enviados por meio do grupo de gerenciamento | frequência de coleta |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |Mensagens de handshakes TCP/ECO ICMP a cada 5 segundos, dados enviados a cada 3 minutos |

A solução usa transações sintéticas para avaliar a integridade da rede. Os agentes do OMS instalados em vários pontos da rede trocam pacotes TCP ou Eco ICMP (dependendo do protocolo selecionado para monitoramento) uns com os outros. No processo, os agentes aprendem qual é o tempo de ida e volta e a perda de pacotes, se houver. Periodicamente, cada agente também realiza uma rota de rastreamento para outros agentes para localizar todas as várias rotas da rede que deve ser testadas. Usando esses dados, os agentes podem deduzir a latência de rede e os valores de perda de pacotes. Os testes são repetidos a cada cinco segundos, e os dados são agregados por um período de três minutos pelos agentes antes de serem carregados no serviço Log Analytics.

> [!NOTE]
> Embora os agentes se comuniquem uns com os outros com frequência, não geram muito tráfego de rede durante a realização de testes. Os agentes utilizam somente pacotes de handshake TCP SYN-SYNACK-ACK para determinar a perda e latência. Não são trocados pacotes de dados. Durante esse processo, os agentes se comunicam entre si somente quando necessário, e a topologia de comunicação de agente é otimizada para reduzir o tráfego de rede.
>
>

## <a name="using-the-solution"></a>Usando a solução
Esta seção explica todas as funções do painel e como usá-las.

### <a name="solution-overview-tile"></a>Bloco de Visão Geral da Solução
Depois que você habilita a solução de Monitor de Desempenho de Rede, o bloco da solução na página de Visão Geral do OMS fornece uma visão geral da integridade da rede. Ele exibe um gráfico de rosca que mostra o número de links de sub-rede íntegros e não íntegros. Quando você clica no bloco, ele abre o painel da solução.

![Bloco do Monitor de Desempenho de Rede](./media/log-analytics-network-performance-monitor/npm-tile.png)

### <a name="network-performance-monitor-solution-dashboard"></a>Painel de solução do Monitor de Desempenho de Rede
A folha **Resumo da Rede** mostra um resumo das redes, juntamente com seu tamanho relativo. Isso é seguido por blocos que mostram o número total de conexões de rede, links de sub-rede e caminhos no sistema (um caminho consiste nos endereços IP dos dois hosts com agentes e todos os saltos entre eles).

A folha **Eventos de Integridade de Rede Principais** fornece uma lista de alertas e eventos de integridade mais recentes no sistema e o tempo desde que o evento esteve ativo. Um alerta ou evento de integridade é gerado sempre que a perda de pacotes ou a latência de um link de rede ou sub-rede excede um limite.

A folha **Principais Links de Rede Não Íntegros** mostra uma lista de links de rede não íntegros. Esses são os links de rede que têm um ou mais eventos de integridade negativos para eles no momento.

As folhas **Principais Links de Sub-rede com Mais Perda** e **Links de Sub-rede com Mais Latência** mostram os principais links de sub-rede por perda de pacotes e os principais links de sub-rede por latência, respectivamente. Alta latência ou alguma perda de pacotes podem ser esperadas em determinados links de rede. Esses links aparecem na lista dos dez principais, mas não são marcados como não íntegros.

A folha **Consultas Comuns** contém um conjunto de consultas de pesquisa que buscam diretamente os dados de monitoramento de rede brutos. Você pode usar essas consultas como um ponto de partida para criar suas próprias consultas para relatórios personalizados.

![Painel do Monitor de Desempenho de Rede](./media/log-analytics-network-performance-monitor/npm-dash01.png)

### <a name="drill-down-for-depth"></a>Busca detalhada para profundidade
Você pode clicar em vários links do painel da solução para fazer uma busca detalhada mais aprofundada em qualquer área de interesse. Por exemplo, ao ver um alerta ou um link de rede não íntegro no painel, você pode clicar para investigar mais. Você será levado a uma página que lista todos os links de sub-rede do link de rede específico. Você poderá ver o status de integridade, latência e perda de cada link de sub-rede e descobrir rapidamente quais links de sub-rede estão causando o problema. Você pode clicar em **Exibir links de nó** para ver todos os links de nó do link de sub-rede não íntegro. Em seguida, você pode ver links de nós individuais e encontrar os links de nó não íntegros.

Você pode clicar em **Exibir topologia** para exibir a topologia de salto a salto das rotas entre os nós de origem e de destino. As rotas ou os saltos não íntegros são mostrados em vermelho para que você possa identificar rapidamente o problema de uma parte específica da rede.

![dados de busca detalhada](./media/log-analytics-network-performance-monitor/npm-drill.png)

### <a name="network-state-recorder"></a>Gravador de Estado da Rede

Cada modo de exibição exibe um instantâneo da integridade da rede em um ponto específico no tempo. Por padrão, o estado mais recente é mostrado. A barra na parte superior da página mostra o ponto no tempo para que o estado está sendo exibido. Você pode optar por voltar no tempo e exibir o instantâneo da integridade da rede, clicando na barra em **Ações**. Você também pode optar por habilitar ou desabilitar a atualização automática para qualquer página enquanto exibe o estado mais recente.

![estado da rede](./media/log-analytics-network-performance-monitor/network-state.png)

#### <a name="trend-charts"></a>Gráficos de tendência
Em cada nível em que faz a busca detalhada, você pode ver a tendência de perda e latência de um link de rede. Gráficos de tendência também estão disponíveis para links de sub-rede e nó. Você pode alterar o intervalo de tempo para plotar o gráfico usando o controle de tempo na parte superior do gráfico.

Os gráficos de tendência mostram uma perspectiva histórica do desempenho de um link de rede. Alguns problemas de rede são temporários por natureza e seriam difíceis de detectar apenas examinando o estado atual da rede. Isso ocorre porque problemas podem surgir rapidamente e desaparecem antes que alguém perceba, apenas para aparecer em um momento posterior. Esses problemas transitórios também podem ser difíceis para os administradores de aplicativos, pois os problemas geralmente surgem como aumentos inexplicáveis de tempo de resposta, mesmo quando todos os componentes do aplicativo parecem ser executados normalmente.

Você pode facilmente detectar esses tipos de problemas examinando um gráfico de tendência, em que o problema aparecerá como um aumento repentino na latência de rede ou perda de pacotes.

![gráfico de tendência](./media/log-analytics-network-performance-monitor/npm-trend.png)

#### <a name="hop-by-hop-topology-map"></a>Mapa de topologia de salto a salto
O Monitor de Desempenho de Rede mostra a topologia de salto a salto de rotas entre dois nós em um mapa de topologia interativo. Você pode exibir o mapa de topologia selecionando um link de nó e clicando em **Exibir topologia**. Além disso, você pode exibir o mapa de topologia clicando no bloco **Caminhos** no painel. Ao clicar em **Caminhos** no painel, você precisará selecionar os nós de origem e de destino no painel esquerdo e clicar em **Plotar** para plotar rotas entre os dois nós.

O mapa de topologia exibe quantas rotas existem entre os dois nós e quais caminhos os pacotes de dados seguem. Os afunilamentos de desempenho de rede são marcados em vermelho no mapa de topologia. Você pode localizar uma conexão de rede ou um dispositivo de rede com defeito observando elementos em vermelho no mapa de topologia.

Ao clicar em um nó ou passar o mouse sobre ele no mapa de topologia, você verá as propriedades do nó, como o FQDN e o endereço IP. Clique em um salto para ver seu endereço IP. Você pode optar por filtrar rotas específicas usando os filtros no painel de ação recolhível. E também pode simplificar as topologias de rede, ocultando os nós intermediários usando o controle deslizante no painel de ações. Você pode ampliar ou reduzir o mapa de topologia usando a roda do mouse.

Observe que a topologia mostrada no mapa é a topologia de camada 3 e não contém conexões e dispositivos de camada 2.

![mapa de topologia de salto a salto](./media/log-analytics-network-performance-monitor/npm-topology.png)

#### <a name="fault-localization"></a>Localização de falhas
O Monitor de Desempenho de Rede é capaz de encontrar afunilamentos de rede sem se conectar aos dispositivos de rede. Com base nos dados coletados da rede e aplicando algoritmos avançados ao gráfico de rede, o Monitor de Desempenho de Rede faz uma estimativa probabilística das partes da rede que provavelmente são a origem do problema.

Essa abordagem é útil para determinar os afunilamentos de rede quando o acesso a saltos não está disponível, pois não requer que dados sejam coletados de dispositivos de rede como roteadores ou comutadores. Isso também é útil quando os saltos entre dois nós não estão em seu controle administrativo. Por exemplo, os saltos podem ser roteadores do ISP.

### <a name="log-analytics-search"></a>Pesquisa de Análise de Log
Todos os dados que são graficamente expostos por meio do painel do Monitor de Desempenho de Rede e das páginas de busca detalhada também estão disponíveis de forma nativa na pesquisa de Análise de Log. Você pode consultar os dados usando a linguagem de consulta de pesquisa e criar relatórios personalizados exportando os dados para o Excel ou o Power BI. A folha **Consultas Comuns** no painel tem algumas consultas úteis que você pode usar como ponto de partida para criar suas próprias consultas e relatórios.

![consultas de pesquisa](./media/log-analytics-network-performance-monitor/npm-queries.png)

## <a name="investigate-the-root-cause-of-a-health-alert"></a>Investigar a causa raiz de um alerta de integridade
Agora que você leu sobre o Monitor de Desempenho de Rede, vejamos uma investigação simples sobre a causa raiz de um evento de integridade.

1. Na página de Visão Geral, você obterá um instantâneo rápido da integridade da rede, observando o bloco do **Monitor de Desempenho de Rede**. Observe que, dos seis links de sub-redes que estão sendo monitorados, dois não estão íntegros. Isso requer investigação. Clique no bloco para exibir o painel de solução.  
   ![Bloco do Monitor de Desempenho de Rede](./media/log-analytics-network-performance-monitor/npm-investigation01.png)
2. Na imagem de exemplo abaixo, você observará que há um evento de integridade em um link de rede que não está íntegro. Você decide investigar o problema e clica no link de rede **DMZ2-DMZ1** para descobrir a raiz do problema.  
   ![exemplo de link de rede não íntegro](./media/log-analytics-network-performance-monitor/npm-investigation02.png)
3. A página de busca detalhada mostra todos os links de sub-rede no link de rede **DMZ2-DMZ1**. Você observará que, para ambos os links de sub-rede, a latência ultrapassou o limite, tornando o link de rede não íntegro. Você também pode ver as tendências de latência de ambos os links de sub-rede. Você pode usar o controle de seleção de tempo do gráfico para se concentrar no intervalo de tempo necessário. Você pode ver a hora do dia em que a latência atingiu seu pico. Você pode pesquisar mais tarde os logs desse período de tempo para investigar o problema. Clique em **Exibir links do nó** para fazer uma busca ainda mais detalhada.  
   ![exemplo de links de sub-rede não íntegros](./media/log-analytics-network-performance-monitor/npm-investigation03.png)
4. De forma semelhante à página anterior, a página de busca detalhada do link de sub-rede específico lista seus links de nós constituintes. Você pode executar ações semelhantes aqui, como fez na etapa anterior. Clique em **Exibir topologia** para exibir a topologia entre os dois nós.  
   ![exemplo de links de nó não íntegro](./media/log-analytics-network-performance-monitor/npm-investigation04.png)
5. Todos os caminhos entre os dois nós selecionados são plotados no mapa de topologia. Você pode visualizar a topologia de salto a salto das rotas entre dois nós no mapa de topologia. Isso lhe dá uma visão clara de quantas rotas existem entre os dois nós e quais caminhos os pacotes de dados estão adotando. Os afunilamentos de desempenho de rede são marcados em vermelho. Você pode localizar uma conexão de rede ou um dispositivo de rede com defeito observando elementos em vermelho no mapa de topologia.  
   ![exemplo de modo de exibição de topologia não íntegra](./media/log-analytics-network-performance-monitor/npm-investigation05.png)
6. A perda, a latência e o número de saltos em cada caminho podem ser analisados no painel **Ação**. Use a barra de rolagem para exibir os detalhes dos caminhos não íntegros.  Use os filtros para selecionar os caminhos com o nó íntegro, de forma que somente a topologia para os caminhos selecionados seja plotada. Você pode usar a roda do mouse para ampliar ou reduzir o mapa de topologia.

   Na imagem abaixo, você pode ver claramente a causa das áreas problemáticas da seção específica da rede, observando os caminhos e saltos na cor vermelha. Clicar em um nó no mapa de topologia revela as propriedades do nó, incluindo o FQDN e o endereço IP. Clicar em um salto mostra o endereço IP do nó.  
   ![topologia não íntegra - exemplo de detalhes do caminho](./media/log-analytics-network-performance-monitor/npm-investigation06.png)

## <a name="provide-feedback"></a>Fornecer comentários

- **UserVoice** - Você pode postar suas ideias para recursos do Monitor de Desempenho de Rede nas quais você deseja trabalhar. Visite nossa [página UserVoice](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring).
- **Junte-se ao nosso coorte** - Estamos sempre interessados em novos clientes para o nosso coorte. Como parte do coorte, você consegue acesso antecipado a novos recursos e ajuda a melhorar o Monitor de Desempenho de Rede. Se estiver interessado em participar, preencha essa [pesquisa rápida](https://aka.ms/npmcohort).

## <a name="next-steps"></a>Próximas etapas
* [Pesquisar logs](log-analytics-log-searches.md) para exibir registros de dados de desempenho de rede detalhados.
