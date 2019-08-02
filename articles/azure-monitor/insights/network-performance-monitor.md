---
title: Solução de Monitor de Desempenho de Rede no Azure | Microsoft Docs
description: O Monitor de Desempenho de Rede no Azure ajuda a monitorar o desempenho de suas redes quase em tempo real para detectar e localizar gargalos de desempenho de rede.
services: log-analytics
documentationcenter: ''
author: vinynigam
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/20/2018
ms.author: vinigam
ms.openlocfilehash: c350ca3cd8cbfb5e550fccd0bae0df53168de178
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68312085"
---
# <a name="network-performance-monitor-solution-in-azure"></a>Solução do Monitor de Desempenho de Rede no Azure

![Símbolo do Monitor de Desempenho de Rede](./media/network-performance-monitor/npm-symbol.png)


O Monitor de Desempenho de Rede é uma solução de monitoramento de rede híbrida baseada em nuvem que ajuda a monitorar o desempenho de rede entre vários pontos em sua infraestrutura de rede. Também ajuda a monitorar a conectividade de rede para serviços e pontos de extremidade do aplicativo e a monitorar o desempenho do Azure ExpressRoute. 

O Monitor de Desempenho de Rede detecta problemas de tráfego de rede, como blackholing, erros de roteamento e problemas que os métodos de monitoramento de rede convencionais não são capazes de detectar. A solução gera alertas e notifica como e quando um limite é ultrapassado para um link de rede. Ela também garante a detecção oportuna de problemas de desempenho de rede e localiza a origem do problema para determinado segmento de rede ou dispositivo. 

O Monitor de Desempenho de Rede oferece três recursos abrangentes: 

* [Monitor de Desempenho](network-performance-monitor-performance-monitor.md): você pode monitorar a conectividade de rede através de implantações de nuvem e localizações locais, vários data centers e filiais, vários aplicativos ou microsserviços multiníveis críticos à missão. Com o Monitor de Desempenho, você pode detectar problemas de rede antes de os usuários reclamarem.

* [Monitor de Conectividade de Serviço](network-performance-monitor-service-connectivity.md): você pode monitorar a conectividade dos seus usuários aos serviços importantes, determinar qual infraestrutura está no caminho e identificar onde gargalos ocorrem na rede. Você pode saber sobre as interrupções antes dos usuários e veja a localização exata dos problemas ao longo de seu caminho de rede. 

    Esse recurso ajuda você a executar testes baseados em HTTP, HTTPS, TCP e ICMP para monitorar quase em tempo real ou historicamente a disponibilidade e o tempo de resposta do serviço. Você também pode monitorar a contribuição da rede na perda de pacotes e a latência. Com um mapa de topologia de rede, é possível isolar as lentidões de rede. Você pode identificar os pontos de problema que ocorrem junto com o caminho de rede do nó ao serviço, com os dados de latência em cada salto. Com testes internos, você pode monitorar a conectividade de rede ao Office 365 e ao Dynamics CRM sem nenhuma configuração prévia. Com essa funcionalidade, você pode monitorar a conectividade de rede a qualquer ponto de extremidade compatível com TCP, como sites, aplicativos SaaS, aplicativos PaaS, bancos de dados SQL, etc.

* [Monitor do ExpressRoute](network-performance-monitor-expressroute.md): monitore a conectividade e o desempenho de ponta a ponta entre as suas filiais e o Azure por meio do Azure ExpressRoute.  

Mais informações sobre os diversos recursos de suporte do [Monitor de Desempenho de Rede](https://docs.microsoft.com/azure/networking/network-monitoring-overview) estão disponíveis online.
 
## <a name="supported-regions"></a>Regiões com Suporte
O NPM pode monitorar a conectividade entre redes e aplicativos em qualquer parte do mundo, de um workspace que está hospedado em uma das seguintes regiões:
* Europa Ocidental
* Centro-Oeste dos EUA
* East US
* Leste do Japão
* Sudeste da Ásia
* Sudeste da Austrália
* Sul do Reino Unido
* Índia Central
* Virgínia, Governo dos EUA


A lista de regiões de suporte para Monitor do ExpressRoute está disponível na [documentação](https://docs.microsoft.com/azure/expressroute/how-to-npm?utm_swu=8117).


## <a name="set-up-and-configure"></a>Instalar e configurar

### <a name="install-and-configure-agents"></a>Instalar e configurar agentes 

Use os processos básicos para instalar agentes em [conectar computadores Windows para Azure monitor](../platform/agent-windows.md) e [conectar Operations Manager ao Azure monitor](../platform/om-agents.md).

### <a name="where-to-install-the-agents"></a>Onde instalar os agentes 

* **Monitor de Desempenho**: instale os agentes do Log Analytics em pelo menos um nó conectado a cada sub-rede por meio da qual você deseja monitorar a conectividade de rede com outras sub-redes.

    Para monitorar um link de rede, instale os agentes em ambas as extremidades do link. Se você não tiver certeza sobre a topologia da rede, instale os agentes em servidores com cargas de trabalho críticas entre os quais você deseja monitorar o desempenho da rede. Por exemplo, se você quiser monitorar a conexão de rede entre um servidor Web e um servidor que executa o SQL, instale um agente em ambos os servidores. Os agentes monitoram a conectividade de rede (links) entre os hosts, não os próprios hosts. 

* **Monitor de Conectividade de Serviço**: instale um agente do Log Analytics em cada nó por meio do qual você deseja monitorar a conectividade de rede com o ponto de extremidade de serviço. Um exemplo é se você deseja monitorar a conectividade de rede para o Office 365 em seus sites do office rotulado O1, O2 e O3. Instale o agente Log Analytics em pelo menos um nó em O1, O2 e O3. 

* **Monitor do ExpressRoute**: instale pelo menos um agente do Log Analytics na sua rede virtual do Azure. Instale também pelo menos um agente em sua sub-rede local, que é conectada por meio de emparelhamento privado do ExpressRoute.  

### <a name="configure-log-analytics-agents-for-monitoring"></a>Configurar agentes do Log Analytics para monitoramento 

O Monitor de Desempenho de Rede usa transações sintéticas para monitorar o desempenho da rede entre os agentes de origem e de destino. Você pode escolher entre TCP e ICMP como o protocolo para o monitoramento nas funcionalidades do Monitor de Desempenho e do Monitor de Conectividade de Serviço. Apenas o TCP está disponível como o protocolo de monitoramento para o Monitor do ExpressRoute. Certifique-se de que o firewall permita a comunicação entre os agentes do Log Analytics usados para monitoramento no protocolo escolhido. 

* **Protocolo TCP**: se você tiver escolhido TCP como protocolo para monitoramento, abra a porta de firewall nos agentes que está sendo usada para o Monitor de Desempenho de Rede e Monitor do ExpressRoute, para garantir que os agentes possam se conectar entre si. Para abrir a porta, execute o script do PowerShell [EnableRules.ps1](https://aka.ms/npmpowershellscript) sem parâmetros em uma janela do PowerShell com privilégios administrativos.

    O script cria chaves do registro necessárias pela solução. Ele também cria regras de Firewall do Windows para permitir que os agentes criem conexões TCP entre si. As chaves do Registro criadas pelo script especificam se é preciso registrar os logs de depuração e o caminho para o arquivo de logs. O script também define a porta TCP de agente usada para comunicação. Os valores para essas chaves são definidos automaticamente pelo script. Não altere manualmente essas chaves. A porta aberta por padrão é 8084. Você pode usar uma porta personalizada fornecendo o parâmetro portNumber para o script. Use a mesma em todos os computadores em que o script é executado. 

    >[!NOTE]
    > O script configura apenas o firewall do windows localmente. Se tiver um firewall de rede, certifique-se que ele permite o tráfego destinado à porta TCP usada pelo Monitor de Desempenho de Rede.

    >[!NOTE]
    > Você não precisa executar o script do PowerShell [EnableRules. ps1](https://aka.ms/npmpowershellscript ) para o monitor de conectividade de serviço.

    

* **Protocolo ICMP**: se você escolheu ICMP como protocolo para monitoramento, habilite as seguintes regras de firewall para usar de forma confiável o ICMP:
    
   ```
   netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
   ```
 

### <a name="configure-the-solution"></a>Configurar a solução 

1. Adicionar a solução de Monitor de Desempenho de Rede ao workspace [do Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Você também pode usar o processo descrito em [Adicionar soluções de Azure monitor do Galeria de soluções](../../azure-monitor/insights/solutions.md). 
2. Abra seu espaço de trabalho do Log Analytics e selecione o bloco **Visão geral**. 
3. Selecione o bloco **Monitor de Desempenho de Rede** com a mensagem *A solução requer configuração adicional*.

   ![Bloco do Monitor de Desempenho de Rede](media/network-performance-monitor/npm-config.png)

4. Na página **Configuração**, você vê a opção de instalar agentes do Log Analytics e configurar os agentes para monitoramento na visualização **Configurações Comuns**. Como explicado anteriormente, se você instalou e configurou os agentes do Log Analytics, selecione a exibição **Configuração** para configurar o recurso que você deseja usar. 

   **Monitor de Desempenho**: escolha qual protocolo usar para transações sintéticas na **regra de monitor de desempenho Padrão** e clique em **Salvar e continuar**. Esta seleção de protocolo só mantém a regra padrão gerada pelo sistema. Você precisa escolher o protocolo sempre que criar uma regra de Monitor de Desempenho explicitamente. Você sempre pode mover para as configurações de regra **Padrão** na guia do **Monitor de Desempenho** (que aparece depois de concluir a configuração do dia 0) e alterar o protocolo mais tarde. Se você não deseja usar a funcionalidade de Monitor de Desempenho, você pode desabilitar a regra padrão nas configurações de regra **Padrão** na guia do **Monitor de Desempenho**.

   ![Execução do Monitor de Desempenho](media/network-performance-monitor/npm-synthetic-transactions.png)
    
   **Monitor de Conectividade de Serviço**: a funcionalidade fornece testes internos pré-configurados para monitorar a conectividade de rede dos seus agentes ao Office 365 e ao Dynamics 365. Escolha os serviços do Office 365 e Dynamics 365 que você deseja monitorar, marcando as caixas de seleção ao lado deles. Escolha os agentes que deseja monitorar clicando no botão **Adicionar Agentes**. Se você não quiser usar esse recurso ou desejar configurá-lo mais tarde, não selecione nada e selecione **Salvar e Continuar**.

   ![Exibição do monitor de conectividade de serviço](media/network-performance-monitor/npm-service-endpoint-monitor.png)

   **Monitor do ExpressRoute**: Selecione **Descobrir Agora** para descobrir todos os emparelhamentos privados do ExpressRoute que estão conectados às redes virtuais na assinatura do Azure vinculada a este espaço de trabalho do Log Analytics. 

   ![Exibição do Monitor ExpressRoute](media/network-performance-monitor/npm-express-route.png)

   Após a conclusão da descoberta, os circuitos e emparelhamentos descobertos são listados em uma tabela. 

   ![Configuração do Monitor de Desempenho de Rede](media/network-performance-monitor/npm-private-peerings.png)
    
O monitoramento para esses circuitos e emparelhamentos está inicialmente em um estado desabilitado. Selecione cada recurso que você deseja monitorar e configure o monitoramento para eles no modo de exibição de detalhes à direita. Selecione **Salvar** para salvar a configuração. Para saber mais, consulte o artigo “Configurar monitoramento do ExpressRoute”. 

Quando a instalação estiver concluída, demora entre 30 minutos e uma hora para que os dados sejam preenchidos. Enquanto a solução agrega os dados de sua rede, você verá a mensagem *A solução requer configuração adicional* no bloco de **visão geral** do Monitor de Desempenho de Rede. Depois que os dados são coletados e indexados, o bloco de **Visão geral** muda e informa o resumo da integridade de sua rede. Em seguida, você pode editar o monitoramento dos nós nos quais os agentes do Log Analytics estão instalados, bem como as sub-redes descobertas em seu ambiente.

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Editar configurações de monitoramento para sub-redes e nós 

Todas as sub-redes com pelo menos um agente instalado são listadas na guia  **Sub-redes**  na página de configuração. 


Para habilitar ou desabilitar o monitoramento de sub-redes específicas:

1. Marque ou desmarque a caixa de seleção ao lado do **ID da sub-rede**. Em seguida, certifique-se de que **usar para monitoramento** está marcado ou desmarcado, conforme apropriado. Você pode marcar ou desmarcar várias sub-redes. Quando desabilitado, as sub-redes não são monitoradas e os agentes serão atualizados para parar de executar o ping para outros agentes. 
2. Escolha os nós que você deseja monitorar em uma sub-rede específica. Selecione a sub-rede na lista e mova os nós necessários entre as listas que contêm nós não monitorados e monitorados. Você pode adicionar uma descrição personalizada à sub-rede.
3. Selecione **Salvar** para salvar a configuração. 

#### <a name="choose-nodes-to-monitor"></a>Escolher nós para monitorar

Todos os nós que têm um agente instalado neles estão listados na guia **Nós**. 

1. Marque ou desmarque os nós que você deseja monitorar ou parar de monitorar. 
2. Selecione **Usar para Monitoramento** ou desmarque essa opção, conforme apropriado. 
3. Clique em **Salvar**. 


Configure os recursos desejados:

- [Monitorar Desempenho](network-performance-monitor-performance-monitor.md#configuration)
- [Monitor de conectividade de serviço](network-performance-monitor-performance-monitor.md#configuration)
- [ExpressRoute Monitor](network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Detalhes da coleta de dados
Para coletar as informações de perda e latência, o Monitor de Desempenho de Rede usa pacotes de handshake TCP SYN-SYNACK-ACK ao escolher TCP como protocolo. O Monitor de Desempenho de Rede usa a RESPOSTA DE ECO ICMP DE ECO ICMP ao escolher ICMP como protocolo. O rastreamento de rota também é usado para obter informações sobre a topologia.

A tabela a seguir mostra os métodos de coleta de dados e outros detalhes sobre como os dados são coletados para o Monitor de Desempenho de Rede.

| Plataforma | Agente direto | Agente do System Center Operations Manager | Armazenamento do Azure | Operations Manager necessário? | Dados de agente do Operations Manager enviados por meio do grupo de gerenciamento | Frequência de coleta |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |Mensagens de handshakes TCP/ECO ICMP a cada 5 segundos, dados enviados a cada 3 minutos |
 

 
A solução usa transações sintéticas para avaliar a integridade da rede. Agentes do Log Analytics instalados em vários pontos da rede trocam pacotes TCP ou ICMP Echo uns com os outros. Se os agentes usam pacotes TCP ou protocolo ICMP, dependerá do protocolo selecionado para monitoramento. No processo, os agentes aprendem qual é o tempo de ida e volta e a perda de pacotes, se houver. Periodicamente, cada agente também realiza uma rota de rastreamento para outros agentes para localizar todas as várias rotas da rede que deve ser testadas. Usando esses dados, os agentes podem deduzir a latência de rede e os valores de perda de pacotes. Os testes são repetidos a cada cinco segundos. Os dados são agregados por cerca de três minutos pelos agentes antes de serem carregados no espaço de trabalho Log Analytics em Azure Monitor.



>[!NOTE]
> Embora os agentes se comuniquem uns com os outros com frequência, não geram tráfego de rede significativo durante a realização de testes. Os agentes utilizam somente pacotes de handshake TCP SYN-SYNACK-ACK para determinar a perda e latência. Não são trocados pacotes de dados. Durante esse processo, os agentes se comunicam entre si somente quando necessário. A topologia de comunicação do agente é otimizada para reduzir o tráfego de rede.

## <a name="use-the-solution"></a>Usar a solução 

### <a name="network-performance-monitor-overview-tile"></a>Bloco do Monitor de Desempenho de Rede 

Depois que você habilita a solução de Monitor de Desempenho de Rede, o bloco da solução na página de **Visão Geral** fornece uma visão geral da integridade da rede. 

 ![Bloco do Monitor de Desempenho de Rede](media/network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>Painel do Monitor de Desempenho de Rede 

* **Eventos de Integridade de Rede Mais Importantes**: esta página fornece uma lista de alertas e eventos de integridade mais recentes no sistema e o tempo desde que o evento esteve ativo. Um alerta ou evento de integridade é gerado sempre que o valor da métrica escolhida (perda, latência, tempo de resposta ou utilização de largura de banda) para a regra de monitoramento excede o limite. 

* **Monitor do ExpressRoute**: esta página fornece resumos de integridade para as várias conexões de emparelhamento do ExpressRoute os monitores de solução. O bloco **Topologia** informa o número de caminhos de rede através dos circuitos do ExpressRoute que estão sendo monitorados em sua rede. Selecione este bloco para ir para a exibição **Topologia**.

* **Monitor de Conectividade de Serviço**: esta página fornece resumos de integridade para os diferentes testes que você criou. O bloco **Topologia** exibe o número de pontos de extremidade que são monitorados. Selecione este bloco para ir para a exibição **Topologia**.

* **Monitor de Desempenho**: esta página fornece resumos de integridade para os links de **Rede** e links **Sub-rede** links que monitoram a solução. O bloco **Topologia** informa o número de caminhos de rede que estão sendo monitorados em sua rede. Selecione este bloco para ir para a exibição **Topologia**. 

* **Consultas Comuns**: essa página contém um conjunto de consultas de pesquisa que buscam diretamente os dados de monitoramento de rede brutos. Você pode usar essas consultas como um ponto de partida para criar suas próprias consultas para relatórios personalizados. 

   ![Painel do Monitor de Desempenho de Rede](media/network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>Fazer busca detalhada para profundidade 

Você pode selecionar vários links do painel da solução para fazer drill down em qualquer área de interesse. Por exemplo, ao ver um alerta ou um link de rede não íntegro no painel, selecione para investigar mais. A página lista todos os links de sub-rede do link de rede específico. Você pode ver o status de integridade, perda e latência de cada link de sub-rede. Você pode descobrir rapidamente qual link de sub-rede causa problemas. Selecione **Exibir links de nó** para ver todos os links de nó do link de sub-rede não íntegro. Em seguida, você pode ver links de nós individuais e encontrar os links de nó não íntegros. 

Selecione  **Exibir topologia** para exibir a topologia de salto a salto das rotas entre os nós de origem e de destino. As rotas não íntegras aparecem em vermelho. Você pode exibir a latência que cada sal acrescentou para que você possa identificar rapidamente o problema de uma parte específica da rede.

 

### <a name="network-state-recorder-control"></a>Controle do Gravador de Estado da Rede

Cada modo de exibição exibe um instantâneo da integridade da rede em um ponto específico no tempo. Por padrão, o estado mais recente é mostrado. A barra na parte superior da página mostra o ponto no tempo para que o estado seja exibido. Para exibir um instantâneo de sua integridade de rede em um momento anterior, selecione **Ações**. Você também pode optar por habilitar ou desabilitar a atualização automática para qualquer página enquanto exibe o estado mais recente. 

 ![Gravador de Estado da Rede](media/network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Gráficos de tendência 

Em cada nível em que faz o drill down, você pode ver a tendência de métrica aplicável. Pode ser perda, latência, tempo de resposta ou utilização de largura de banda. Para alterar o intervalo de tempo da tendência usando o controle de tempo na parte superior do gráfico. 

Os gráficos de tendência mostram uma perspectiva histórica da métrica de desempenho. Alguns problemas de rede são temporários por natureza e são difíceis de detectar apenas examinando o estado atual da rede. Problemas podem surgir rapidamente e desaparecem antes que alguém perceba, apenas para aparecer em um momento posterior. Esses problemas transitórios também podem ser difíceis para os administradores de aplicativos. Os problemas geralmente aparecem como aumentos inexplicáveis no tempo de resposta do aplicativo, mesmo quando todos os componentes do aplicativo aparecem sem problemas. 

Você pode facilmente detectar esses tipos de problemas, observando um gráfico de tendência. O problema aparece como um aumento repentino na latência de rede ou perda de pacotes. Para investigar o problema use o gravador de estado da rede para exibir o instantâneo de rede e a topologia referentes a esse ponto no tempo quando o problema ocorreu.

 
![Gráficos de tendência](media/network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Mapa de topologia 

O Monitor de Desempenho de Rede mostra a topologia de salto a salto de rotas entre o ponto de extremidade de origem e o de destino, em um mapa de topologia interativo. Para exibir o mapa de topologia, selecione o bloco **Topologia** no painel da solução. Você também pode selecionar o link **Exibição de topologia** nas páginas de detalhamento. 

O mapa de topologia exibe quantas rotas existem entre a origem e o destino e quais caminhos os pacotes de dados seguem. A latência que cada salto de rede acrescentou também pode ser vista. Todos os caminhos para os quais a latência total do caminho está acima do limite (definido na regra de monitoramento correspondente) são mostrados em vermelho. 

Ao clicar em um nó ou passar o mouse sobre ele no mapa de topologia, você verá as propriedades do nó, como o FQDN e o endereço IP. Selecione um salto para ver seu endereço IP. Você pode identificar o salto de rede problemático observando a latência acrescentada por ele. Para filtrar rotas específicas, use os filtros no painel de ação recolhível. Para simplificar as topologias de rede, oculte os nós intermediários usando o controle deslizante no painel de ações. Você pode ampliar ou reduzir o mapa de topologia usando a roda do mouse. 

A topologia mostrada no mapa é a topologia de camada 3 e não contém conexões e dispositivos de camada 2. 

 
![Mapa de topologia](media/network-performance-monitor/topology-map.png)
 

## <a name="log-queries-in-azure-monitor"></a>Consultas de log em Azure Monitor

Todos os dados expostos graficamente por meio do painel de Monitor de Desempenho de Rede e das páginas de busca detalhada também estão disponíveis nativamente em [consultas de log](../log-query/log-query-overview.md). Você pode executar análises interativas de dados no repositório e correlacionar dados de origens diferentes. Você também pode criar alertas personalizados e exibições e exportar os dados para Excer, Power BI ou um link compartilhável. A área  **Consultas Comuns**  no painel tem algumas consultas úteis que você pode usar como ponto de partida para criar suas próprias consultas e relatórios. 

## <a name="alerts"></a>Alertas

O Monitor de Desempenho de Rede usa os recursos de alertas do [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts).

Isso significa que todas as notificações são gerenciadas usando [grupos de ação](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).  

Se você é um usuário do NPM criando um alerta por meio do Log Analytics: 
1. Você verá um link que o redirecionará para portal do Azure. Clique nele para acessar o portal.
2. Clique no bloco de solução do Monitor de Desempenho de Rede. 
3. Navegue para Configurar.  
4. Selecione o teste sobre o qual deseja criar um alerta e siga as etapas mencionadas a seguir.

Se você for um usuário do NPM criando um alerta por meio do portal do Azure:  
1. Você pode optar por inserir seu email diretamente ou criar alertas por meio de grupos de ação.
2. Se você optar por inserir seu email diretamente, um grupo de ação com o nome **ActionGroup de Email NPM** é criado e a ID de email é adicionada a esse grupo de ação.
3. Se optar por usar grupos de ação, você precisará selecionar um grupo criado anteriormente. Você pode aprender a criar um grupo de ação [aqui.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups#create-an-action-group-by-using-the-azure-portal) 
4. Quando o alerta for criado com êxito, você poderá usar o link Gerenciar Alertas para gerenciar seus alertas. 

Cada vez que você cria um alerta, o NPM cria uma regra de alerta de log de consulta com base no Azure Monitor. Essa consulta é disparada a cada 5 minutos por padrão. O monitor do Azure não cobra pelas primeiras 250 regras de alerta criadas de log e quaisquer regras de alerta acima limitam de 250 regras de alerta de log serão cobradas de acordo com os [alertas de preços na página de preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).
As notificações são cobradas separadamente de acordo com as [notificações de preços na página de preço do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).


## <a name="pricing"></a>Preços

Informações sobre preços estão disponíveis [online](network-performance-monitor-pricing-faq.md).

## <a name="provide-feedback"></a>Fornecer comentários 

* **UserVoice:** você pode postar suas ideias para recursos do Monitor de Desempenho de Rede nas quais você deseja trabalhar. Visite a [página UserVoice](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring). 

* **Junte-se ao nosso coorte:** estamos sempre interessados em novos clientes para a nossa coorte. Como parte do coorte, você consegue acesso antecipado a novos recursos e uma oportunidade de ajudar a melhorar o Monitor de Desempenho de Rede. Se estiver interessado em participar, preencha esta  [pesquisa rápida](https://aka.ms/npmcohort). 

## <a name="next-steps"></a>Próximas etapas 
Saiba mais sobre o [Monitor de Desempenho](network-performance-monitor-performance-monitor.md), o [Monitor de Conectividade do Serviço](network-performance-monitor-performance-monitor.md) e o [Monitor do ExpressRoute](network-performance-monitor-expressroute.md). 
