---
title: Monitor de conexão no Azure | Microsoft Docs
description: Saiba como usar o monitor de conexão para monitorar a comunicação de rede em um ambiente distribuído.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 7abaae033d2dbdb329a1f99d8f9845e5965d806c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101712310"
---
# <a name="network-connectivity-monitoring-with-connection-monitor"></a>Monitoramento de conectividade de rede com o monitor de conexão

> [!IMPORTANT]
> A partir de 1 de julho de 2021, você não poderá adicionar novos testes em um espaço de trabalho existente ou habilitar um novo espaço de trabalho no Monitor de Desempenho de Rede. Você também não poderá adicionar novos monitores de conexão no monitor de conexão (clássico). Você pode continuar a usar os testes e os monitores de conexão criados antes de 1 de julho de 2021. Para minimizar a interrupção do serviço para suas cargas de trabalho atuais, [migre seus testes de monitor de desempenho de rede ](migrate-to-connection-monitor-from-network-performance-monitor.md) ou  [migre do monitor de conexão (clássico)](migrate-to-connection-monitor-from-connection-monitor-classic.md) para o novo monitor de conexão no observador de rede do Azure antes de 29 de fevereiro de 2024.

O monitor de conexão fornece monitoramento de conexão de ponta a ponta unificado no observador de rede do Azure. O recurso de monitor de conexão dá suporte a implantações de nuvem híbridas e do Azure. O observador de rede fornece ferramentas para monitorar, diagnosticar e exibir métricas relacionadas à conectividade para suas implantações do Azure.

Aqui estão alguns casos de uso para o monitor de conexão:

- A VM do servidor Web front-end se comunica com uma VM do servidor de banco de dados em um aplicativo de várias camadas. Você deseja verificar a conectividade de rede entre as duas VMs.
- Você deseja que as VMs na região leste dos EUA executem ping nas VMs na região EUA Central e você deseja comparar as latências de rede entre regiões.
- Você tem vários sites locais do Office em Seattle, Washington, e no Ashburn, Virgínia. Seus sites do Office se conectam a URLs do Microsoft 365. Para os usuários de URLs de Microsoft 365, compare as latências entre Seattle e Ashburn.
- Seu aplicativo híbrido precisa de conectividade com um ponto de extremidade de armazenamento do Azure. Seu site local e seu aplicativo do Azure se conectam ao mesmo ponto de extremidade de armazenamento do Azure. Você deseja comparar as latências do site local com as latências do aplicativo do Azure.
- Você deseja verificar a conectividade entre suas configurações locais e as VMs do Azure que hospedam seu aplicativo de nuvem.

O monitor de conexão combina o melhor de dois recursos: o recurso de monitor de conexão do observador de rede [(clássico)](./network-watcher-monitoring-overview.md#monitor-communication-between-a-virtual-machine-and-an-endpoint) e o [Monitor de conectividade do serviço](../azure-monitor/insights/network-performance-monitor-service-connectivity.md)de monitor de desempenho de rede (NPM), monitoramento do [ExpressRoute](../expressroute/how-to-npm.md)e recurso de [monitoramento de desempenho](../azure-monitor/insights/network-performance-monitor-performance-monitor.md) .

Aqui estão alguns benefícios do monitor de conexão:

* Experiência unificada e intuitiva para necessidades de monitoramento híbrido e do Azure
* Monitoramento de conectividade entre regiões e entre espaços de trabalho
* Frequências de investigação mais altas e melhor visibilidade do desempenho da rede
* Alertas mais rápidos para suas implantações híbridas
* Suporte para verificações de conectividade baseadas em HTTP, TCP e ICMP 
* Métricas e suporte Log Analytics para configurações de teste do Azure e não Azure

![Diagrama mostrando como o monitor de conexão interage com VMs do Azure, hosts não Azure, pontos de extremidade e locais de armazenamento de dados](./media/connection-monitor-2-preview/hero-graphic.png)

Para começar a usar o monitor de conexão para monitoramento, siga estas etapas: 

1. Instale os agentes de monitoramento.
1. Habilite o observador de rede em sua assinatura.
1. Crie um monitor de conexão.
1. Configure a análise de dados e alertas.
1. Diagnostique problemas em sua rede.

As seções a seguir fornecem detalhes para essas etapas.

## <a name="install-monitoring-agents"></a>Instalar agentes de monitoramento

O monitor de conexão depende de arquivos executáveis leves para executar verificações de conectividade. Ele dá suporte a verificações de conectividade de ambientes do Azure e ambientes locais. O arquivo executável que você usa depende se sua VM está hospedada no Azure ou no local.

### <a name="agents-for-azure-virtual-machines"></a>Agentes para máquinas virtuais do Azure

Para fazer com que o monitor de conexão reconheça suas VMs do Azure como fontes de monitoramento, instale a extensão da máquina virtual do agente do observador de rede neles. Essa extensão também é conhecida como a *extensão do observador de rede*. As máquinas virtuais do Azure exigem a extensão para disparar o monitoramento de ponta a ponta e outras funcionalidades avançadas. 

Você pode instalar a extensão do observador de rede ao [criar uma VM](./connection-monitor.md#create-the-first-vm). Você também pode instalar, configurar e solucionar problemas separadamente da extensão do observador de rede para [Linux](../virtual-machines/extensions/network-watcher-linux.md) e [Windows](../virtual-machines/extensions/network-watcher-windows.md).

As regras para um grupo de segurança de rede (NSG) ou firewall podem bloquear a comunicação entre a origem e o destino. O monitor de conexão detecta esse problema e o mostra como uma mensagem de diagnóstico na topologia. Para habilitar o monitoramento de conexão, verifique se as regras de firewall e NSG permitem pacotes via TCP ou ICMP entre a origem e o destino.

### <a name="agents-for-on-premises-machines"></a>Agentes para computadores locais

Para fazer com que o monitor de conexão reconheça seus computadores locais como fontes de monitoramento, instale o agente de Log Analytics nos computadores.  Em seguida, habilite a solução Monitor de Desempenho de Rede. Esses agentes são vinculados a espaços de trabalho do Log Analytics, portanto, você precisa configurar a ID do espaço de trabalho e a chave primária antes que os agentes possam iniciar o monitoramento.

Para instalar o agente de Log Analytics para computadores Windows, consulte [Azure monitor extensão de máquina virtual para Windows](../virtual-machines/extensions/oms-windows.md).

Se o caminho incluir firewalls ou NVAs (soluções de virtualização de rede), verifique se o destino está acessível.

Para computadores Windows, para abrir a porta, execute o script [EnableRules.ps1](https://aka.ms/npmpowershellscript) PowerShell sem parâmetros em uma janela do PowerShell com privilégios administrativos.

Para computadores Linux, as portNumbers a serem usadas precisam ser alteradas manualmente. 
* Navegue até o caminho:/var/opt/Microsoft/omsagent/npm_state. 
* Abrir arquivo: npmdregistry
* Alterar o valor do número da porta ```“PortNumber:<port of your choice>”```

 Observe que os números de porta que estão sendo usados devem ser iguais em todos os agentes usados em um espaço de trabalho. 

O script cria chaves do registro necessárias pela solução. Ele também cria regras de Firewall do Windows para permitir que os agentes criem conexões TCP entre si. As chaves do Registro criadas pelo script especificam se é preciso registrar os logs de depuração e o caminho para o arquivo de logs. O script também define a porta TCP de agente usada para comunicação. Os valores para essas chaves são definidos automaticamente pelo script. Não altere manualmente essas chaves. A porta aberta por padrão é 8084. Você pode usar uma porta personalizada fornecendo o parâmetro portNumber para o script. Use a mesma em todos os computadores em que o script é executado. [Leia mais](../azure-monitor/agents/log-analytics-agent.md#network-requirements) sobre os requisitos de rede para agentes de log Analytics

O script configura apenas o firewall do windows localmente. Se tiver um firewall de rede, certifique-se que ele permite o tráfego destinado à porta TCP usada pelo Monitor de Desempenho de Rede.

## <a name="enable-network-watcher-on-your-subscription"></a>Habilitar o observador de rede em sua assinatura

Todas as assinaturas que têm uma rede virtual são habilitadas com o observador de rede. Quando você cria uma rede virtual em sua assinatura, o observador de rede é habilitado automaticamente na região e na assinatura da rede virtual. Essa habilitação automática não afeta seus recursos ou incorre em um encargo. Verifique se o observador de rede não está explicitamente desabilitado em sua assinatura. 

Para obter mais informações, consulte [habilitar o observador de rede](./network-watcher-create.md).

## <a name="create-a-connection-monitor"></a>Criar um monitor de conexão 

O monitor de conexão monitora a comunicação em intervalos regulares. Ele informa sobre alterações na acessibilidade e na latência. Você também pode verificar a topologia de rede atual e histórica entre os agentes de origem e os pontos de extremidade de destino.

As fontes podem ser VMs do Azure ou computadores locais que têm um agente de monitoramento instalado. Os pontos de extremidade de destino podem ser Microsoft 365 URLs, URLs do Dynamics 365, URLs personalizadas, IDs de recursos de VM do Azure, IPv4, IPv6, FQDN ou qualquer nome de domínio.

### <a name="access-connection-monitor"></a>Monitor de conexão de acesso

1. Na home page portal do Azure, vá para **observador de rede**.
1. À esquerda, na seção **monitoramento** , selecione Monitor de **conexão**.
1. Você verá todos os monitores de conexão que foram criados no monitor de conexão. Para ver os monitores de conexão que foram criados na experiência clássica do monitor de conexão, vá para a guia **Monitor de conexão** .
    
  :::image type="content" source="./media/connection-monitor-2-preview/cm-resource-view.png" alt-text="Captura de tela mostrando monitores de conexão que foram criados no monitor de conexão" lightbox="./media/connection-monitor-2-preview/cm-resource-view.png":::

### <a name="create-a-connection-monitor"></a>Criar um monitor de conexão

Nos monitores de conexão criados no monitor de conexão, você pode adicionar máquinas locais e VMs do Azure como fontes. Esses monitores de conexão também podem monitorar a conectividade com pontos de extremidade. Os pontos de extremidade podem estar no Azure ou qualquer outra URL ou IP.

O monitor de conexão inclui as seguintes entidades:

* **Recurso de monitor de conexão** – um recurso do Azure específico da região. Todas as entidades a seguir são propriedades de um recurso de monitor de conexão.
* **Ponto de extremidade** – uma origem ou um destino que participa das verificações de conectividade. Os exemplos de pontos de extremidade incluem VMs do Azure, agentes locais, URLs e IPs.
* **Configuração de teste** – uma configuração específica de protocolo para um teste. Com base no protocolo escolhido, você pode definir a porta, os limites, a frequência de teste e outros parâmetros.
* **Grupo de teste** – o grupo que contém pontos de extremidade de origem, pontos de extremidade de destino e configurações de teste. Um monitor de conexão pode conter mais de um grupo de teste.
* **Teste** – a combinação de um ponto de extremidade de origem, ponto de extremidade de destino e configuração de teste. Um teste é o nível mais granular no qual os dados de monitoramento estão disponíveis. Os dados de monitoramento incluem a porcentagem de verificações que falharam e o RTT (tempo de ida e volta).

 ![Diagrama mostrando um monitor de conexão, definindo a relação entre os grupos de teste e os testes](./media/connection-monitor-2-preview/cm-tg-2.png)

Você pode criar um monitor de conexão usando [portal do Azure](./connection-monitor-create-using-portal.md), [ARMClient](./connection-monitor-create-using-template.md) ou [PowerShell](connection-monitor-create-using-powershell.md)

Todas as fontes, destinos e configurações de teste que você adiciona a um grupo de teste são divididos em testes individuais. Veja um exemplo de como as origens e os destinos são divididos:

* Grupo de teste: TG1
* Fontes: 3 (A, B, C)
* Destinos: 2 (D, E)
* Configurações de teste: 2 (config 1, config 2)
* Total de testes criados: 12

| Número do teste | Fonte | Destino | Configuração de teste |
| --- | --- | --- | --- |
| 1 | A | D | Configuração 1 |
| 2 | A | D | Configuração 2 |
| 3 | A | E | Configuração 1 |
| 4 | A | E | Configuração 2 |
| 5 | B | D | Configuração 1 |
| 6 | B | D | Configuração 2 |
| 7 | B | E | Configuração 1 |
| 8 | B | E | Configuração 2 |
| 9 | C | D | Configuração 1 |
| 10 | C | D | Configuração 2 |
| 11 | C | E | Configuração 1 |
| 12 | C | E | Configuração 2 |

### <a name="scale-limits"></a> Limites de escala

Os monitores de conexão têm os seguintes limites de escala:

* Máximo de monitores de conexão por assinatura por região: 100
* Máximo de grupos de teste por monitor de conexão: 20
* Máximo de origens e destinos por monitor de conexão: 100
* Configurações de teste máximas por monitor de conexão: 20

## <a name="analyze-monitoring-data-and-set-alerts"></a>Analisar dados de monitoramento e definir alertas

Depois de criar um monitor de conexão, as origens verificam a conectividade com os destinos com base em sua configuração de teste.

### <a name="checks-in-a-test"></a>Verificações em um teste

Com base no protocolo que você escolheu na configuração de teste, o monitor de conexão executa uma série de verificações para o par origem-destino. As verificações são executadas de acordo com a frequência de teste escolhida.

Se você usar HTTP, o serviço calculará o número de respostas HTTP que retornaram um código de resposta válido. Os códigos de resposta válidos podem ser definidos usando o PowerShell e a CLI. O resultado determina a porcentagem de verificações com falha. Para calcular o RTT, o serviço mede o tempo entre uma chamada HTTP e a resposta.

Se você usar TCP ou ICMP, o serviço calculará a porcentagem de perda de pacotes para determinar a porcentagem de verificações com falha. Para calcular o RTT, o serviço mede o tempo necessário para receber a confirmação (ACK) dos pacotes que foram enviados. Se você habilitou os dados de traceroute para seus testes de rede, poderá ver a perda e a latência de saltos por salto para sua rede local.

### <a name="states-of-a-test"></a>Estados de um teste

Com base nos dados retornados pelas verificações, os testes podem ter os seguintes Estados:

* **Pass** – valores reais para a porcentagem de verificações com falha e RTT estão dentro dos limites especificados.
* **Falha** – valores reais para a porcentagem de verificações com falha ou RTT excedeu os limites especificados. Se nenhum limite for especificado, um teste atingirá o estado de falha quando a porcentagem de verificações com falha for 100.
* **Aviso** – 
     * Se o limite for especificado e o monitor de conexão observar as verificações com falha por cento mais de 80% do limite, o teste será marcado como aviso.
     * Na ausência de limites especificados, o monitor de conexão atribui automaticamente um limite. Quando esse limite for excedido, o status do teste será alterado para aviso.Para o tempo de ida e volta em testes TCP ou ICMP, o limite é 750msec. Para cheques com falha, o limite é de 10%. 
* **Indeterminado**   – Sem dados no espaço de trabalho Log Analytics.Verifique as métricas. 
* **Não está em execução**   – Desabilitado desabilitando o grupo de teste  

### <a name="data-collection-analysis-and-alerts"></a>Coleta de dados, análise e alertas

Os dados coletados pelo monitor de conexão são armazenados no espaço de trabalho Log Analytics. Você configura esse espaço de trabalho quando criou o monitor de conexão. 

Os dados de monitoramento também estão disponíveis em métricas de Azure Monitor. Você pode usar Log Analytics para manter seus dados de monitoramento pelo tempo que desejar. O Azure Monitor armazena métricas por apenas 30 dias por padrão. 

Você pode [definir alertas baseados em métricas nos dados](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/).

#### <a name="monitoring-dashboards"></a>Monitorando painéis

Nos painéis de monitoramento, você verá uma lista dos monitores de conexão que você pode acessar para suas assinaturas, regiões, carimbos de data/hora, fontes e tipos de destino.

Ao ir para o monitor de conexão do observador de rede, você pode exibir os dados por:

* **Monitor de conexão** – lista de todos os monitores de conexão criados para suas assinaturas, regiões, carimbos de data/hora, fontes e tipos de destino. Este modo de exibição é o padrão.
* **Grupos de teste** – lista de todos os grupos de teste criados para suas assinaturas, regiões, carimbos de data/hora, fontes e tipos de destino. Esses grupos de teste não são filtrados por monitores de conexão.
* **Teste** – lista de todos os testes que são executados para suas assinaturas, regiões, carimbos de data/hora, fontes e tipos de destino. Esses testes não são filtrados por monitores de conexão ou grupos de teste.

Na imagem a seguir, as três exibições de dados são indicadas pela seta 1.

No painel, você pode expandir cada monitor de conexão para ver seus grupos de teste. Em seguida, você pode expandir cada grupo de teste para ver os testes que são executados nele. 

Você pode filtrar uma lista com base em:

* **Filtros de nível superior** – lista de pesquisa por texto, tipo de entidade (monitor de conexão, grupo de teste ou teste) carimbo de data/hora e escopo. O escopo inclui assinaturas, regiões, fontes e tipos de destino. Consulte a caixa 1 na imagem a seguir.
* **Filtros baseados em estado** – filtre pelo estado do monitor de conexão, do grupo de teste ou do teste. Consulte a caixa 2 na imagem a seguir.
* **Filtro baseado em alerta** – filtrar por alertas acionados no recurso de monitor de conexão. Consulte a caixa 3 na imagem a seguir.

  :::image type="content" source="./media/connection-monitor-2-preview/cm-view.png" alt-text="Captura de tela mostrando como filtrar exibições de monitores de conexão, grupos de teste e testes no monitor de conexão " lightbox="./media/connection-monitor-2-preview/cm-view.png":::
    
Por exemplo, para examinar todos os testes no monitor de conexão onde o IP de origem é 10.192.64.56:
1. Altere a exibição para **teste**.
1. No campo de pesquisa, digite *10.192.64.56*
1. Em **escopo** no filtro de nível superior, selecione **fontes**.

Para mostrar apenas os testes com falha no monitor de conexão em que o IP de origem é 10.192.64.56:
1. Altere a exibição para **teste**.
1. Para o filtro baseado em estado, selecione **falha**.
1. No campo de pesquisa, digite *10.192.64.56*
1. Em **escopo** no filtro de nível superior, selecione **fontes**.

Para mostrar apenas os testes com falha no monitor de conexão em que o destino é outlook.office365.com:
1. Alterar exibição para **teste**.
1. Para o filtro baseado em estado, selecione **falha**.
1. No campo de pesquisa, digite *Office.Live.com*
1. Em **escopo** no filtro de nível superior, selecione **destinos**.
  
  :::image type="content" source="./media/connection-monitor-2-preview/tests-view.png" alt-text="Captura de tela mostrando uma exibição filtrada para mostrar apenas os testes com falha para o destino Outlook.Office365.com" lightbox="./media/connection-monitor-2-preview/tests-view.png":::

Para saber o motivo da falha de um monitor de conexão ou de um grupo de teste ou teste, clique na coluna denominada razão.  Isso informa qual limite (verificação de falha% ou RTT) foi violado e mensagens de diagnóstico relacionadas
  
  :::image type="content" source="./media/connection-monitor-2-preview/cm-reason-of-failure.png" alt-text="Captura de tela mostrando o motivo da falha para um monitor de conexão, teste ou grupo de teste" lightbox="./media/connection-monitor-2-preview/cm-reason-of-failure.png":::
    
Para exibir as tendências em RTT e a porcentagem de verificações com falha para um monitor de conexão:
1. Selecione o monitor de conexão que você deseja investigar.

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing.png" alt-text="Captura de tela mostrando métricas para um monitor de conexão, exibido pelo grupo de teste" lightbox="./media/connection-monitor-2-preview/cm-drill-landing.png":::

1. Você verá as seções a seguir  
    1. Essentials-propriedades específicas do recurso do monitor de conexão selecionado 
    1. Resumo 
        1. Linhas de tendência agregadas para RTT e percentual de verificações com falha para todos os testes no monitor de conexão. Você pode definir um tempo específico para exibir os detalhes.
        1. 5 principais em grupos de teste, origens e destinos com base no RTT ou percentual de verificações com falha. 
    1. Guias para grupos de teste, fontes, destinos e configurações de teste – lista os grupos de teste, origens ou destinos no monitor de conexão. Falha em testes de verificação, RTT de agregação e verificações de% valores com falha.  Você também pode voltar no tempo para exibir dados. 
    1. Problemas-problemas no nível de salto para cada teste no monitor de conexão. 

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing-2.png" alt-text="Captura de tela mostrando métricas para um monitor de conexão, exibido por grupo de teste parte 2" lightbox="./media/connection-monitor-2-preview/cm-drill-landing-2.png":::

1. É possível
    * Clique em Exibir todos os testes-para exibir todos os testes no monitor de conexão
    * Clique em Exibir todos os grupos de teste, testar configurações, origens e destinos-para exibir detalhes específicos de cada um. 
    * Escolha um grupo de teste, configuração de teste, origem ou destino-para exibir todos os testes na entidade.

1. Na exibição exibir todos os testes, você pode:
    * Selecione testes e clique em comparar.
    
    :::image type="content" source="./media/connection-monitor-2-preview/cm-compare-test.png" alt-text="Captura de tela mostrando a comparação de dois testes" lightbox="./media/connection-monitor-2-preview/cm-compare-test.png":::
    
    * Use o cluster para expandir recursos compostos como VNET, sub-redes para seus recursos filho
    * Exiba a topologia para qualquer teste clicando em topologia.

Para exibir as tendências em RTT e a porcentagem de verificações com falha para um grupo de teste:
1. Selecione o grupo de teste que você deseja investigar. 
1. Você exibirá de forma semelhante ao monitor de conexão-Essentials, resumo, tabela para grupos de teste, fontes, destinos e configurações de teste. Navegue como você faria para um monitor de conexão

Para exibir as tendências em RTT e a porcentagem de verificações com falha para um teste:
1. Selecione o teste que você deseja investigar. Você verá a topologia de rede e os gráficos de tendência de ponta a ponta para cheques com falha% e tempo de viagem de ida e volta. Para ver os problemas identificados, na topologia, selecione qualquer salto no caminho. (Esses saltos são recursos do Azure.) Essa funcionalidade não está disponível atualmente para redes locais

  :::image type="content" source="./media/connection-monitor-2-preview/cm-test-topology.png" alt-text="Captura de tela mostrando a exibição de topologia de um teste" lightbox="./media/connection-monitor-2-preview/cm-test-topology.png":::

#### <a name="log-queries-in-log-analytics"></a>Consultas de log em Log Analytics

Use Log Analytics para criar exibições personalizadas de seus dados de monitoramento. Todos os dados que a interface do usuário exibe são de Log Analytics. Você pode analisar dados interativamente no repositório. Correlacione os dados de Integridade do Agente ou outras soluções baseadas em Log Analytics. Exporte os dados para o Excel ou Power BI, ou crie um link compartilhável.

#### <a name="metrics-in-azure-monitor"></a>Métricas no Azure Monitor

Nos monitores de conexão que foram criados antes da experiência do monitor de conexão, todas as quatro métricas estão disponíveis:% investigações com falha, AverageRoundtripMs, ChecksFailedPercent (versão prévia) e RoundTripTimeMs (versão prévia). Nos monitores de conexão que foram criados na experiência do monitor de conexão, os dados estão disponíveis apenas para as métricas que são marcadas com *(versão prévia)*.

  :::image type="content" source="./media/connection-monitor-2-preview/monitor-metrics.png" alt-text="Captura de tela mostrando métricas no monitor de conexão" lightbox="./media/connection-monitor-2-preview/monitor-metrics.png":::

Ao usar as métricas, defina o tipo de recurso como Microsoft. Network/networkWatchers/connectionMonitors

| Métrica | Nome de exibição | Unidade | Tipo de agregação | Descrição | Dimensões |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent (clássico) | % De investigações com falha (clássico) | Percentual | Média | Falha na porcentagem de investigações de monitoramento de conectividade. | Sem dimensões |
| AverageRoundtripMs (clássico) | Média de tempo de ida e volta (MS) (clássico) | Milissegundos | Média | RTT de rede médio para investigações de monitoramento de conectividade enviadas entre a origem e o destino. |             Sem dimensões |
| ChecksFailedPercent | % De verificações com falha | Percentual | Média | Percentual de verificações com falha para um teste. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocolo <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Região |
| RoundTripTimeMs | Tempo de ida e volta (ms) | Milissegundos | Média | RTT para cheques enviados entre a origem e o destino. Esse valor não é médio. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocolo <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Região |
| Result | Resultado do teste | Contagem | Média | Resultado do teste do monitor de conexão | SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocolo <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>SourceIP <br>DestinationIP <br>SourceSubnet <br>DestinationSubnet |

#### <a name="metric-based-alerts-for-connection-monitor"></a>Alertas baseados em métrica para o monitor de conexão

Você pode criar alertas de métrica em monitores de conexão usando os métodos abaixo 

1. Do monitor de conexão, durante a criação do monitor de conexão [usando portal do Azure](connection-monitor-preview-create-using-portal.md#) 
1. No monitor de conexão, usando "configurar alertas" no painel 
1. De Azure Monitor-para criar um alerta no Azure Monitor: 
    1. Escolha o recurso de monitor de conexão que você criou no monitor de conexão.
    1. Verifique se a **métrica** aparece como tipo de sinal para o monitor de conexão.
    1. Em **Adicionar condição**, para o **nome do sinal**, selecione **ChecksFailedPercent (visualização)** ou **RoundTripTimeMs (versão prévia)**.
    1. Para **tipo de sinal**, escolha **métricas**. Por exemplo, selecione **ChecksFailedPercent (visualização)**.
    1. Todas as dimensões para a métrica são listadas. Escolha o nome da dimensão e o valor da dimensão. Por exemplo, selecione **endereço de origem** e, em seguida, insira o endereço IP de qualquer fonte no monitor de conexão.
    1. Em **lógica de alerta**, preencha os seguintes detalhes:
        * **Tipo de condição**: **estática**.
        * **Condição** e **limite**.
        * **Granularidade de agregação e frequência de avaliação**: o monitor de conexão atualiza os dados a cada minuto.
    1. Em **ações**, escolha o grupo de ações.
    1. Forneça detalhes do alerta.
    1. Criar a regra de alerta.

  :::image type="content" source="./media/connection-monitor-2-preview/mdm-alerts.jpg" alt-text="Captura de tela mostrando a área criar regra no Azure Monitor. O endereço de origem e o nome do ponto de extremidade de origem são" lightbox="./media/connection-monitor-2-preview/mdm-alerts.jpg":::

## <a name="diagnose-issues-in-your-network"></a>Diagnosticar problemas em sua rede

O monitor de conexão ajuda você a diagnosticar problemas no monitor de conexão e na rede. Os problemas em sua rede híbrida são detectados pelos agentes de Log Analytics que você instalou anteriormente. Problemas no Azure são detectados pela extensão do observador de rede. 

Você pode exibir problemas na rede do Azure na topologia de rede.

Para redes cujas fontes são VMs locais, os seguintes problemas podem ser detectados:

* Atingiu tempo limite solicitado.
* Ponto de extremidade não resolvido pelo DNS – temporário ou persistente. URL inválida.
* Nenhum host encontrado.
* A origem não pode se conectar ao destino. Destino não acessível por meio de ICMP.
* Problemas relacionados a certificados: 
    * Certificado de cliente necessário para autenticar o agente. 
    * A lista de realocação de certificado não está acessível. 
    * O nome do host do ponto de extremidade não corresponde ao assunto ou ao nome alternativo da entidade do certificado. 
    * O certificado raiz está ausente no repositório de autoridades de certificação confiáveis do computador local da origem. 
    * O certificado SSL expirou, é inválido, revogado ou é incompatível.

Para redes cujas fontes são VMs do Azure, os seguintes problemas podem ser detectados:

* Problemas do agente:
    * Agente interrompido.
    * Falha na resolução de DNS.
    * Nenhum aplicativo ou ouvinte escuta na porta de destino.
    * Não foi possível abrir o soquete.
* Problemas de estado da VM: 
    * Iniciando
    * Parando
    * Parado
    * Desalocando
    * Desalocada
    * Reinicialização
    * Não alocado
* A entrada da tabela ARP está ausente.
* O tráfego foi bloqueado devido a problemas de firewall local ou regras de NSG.
* Problemas de gateway de rede virtual: 
    * Rotas ausentes.
    * O túnel entre dois gateways está desconectado ou ausente.
    * O segundo gateway não foi encontrado pelo túnel.
    * Nenhuma informação de emparelhamento foi encontrada.
* A rota estava ausente no Microsoft Edge.
* O tráfego parou devido a rotas do sistema ou UDR.
* O BGP não está habilitado na conexão do gateway.
* A investigação DIP está inoperante no balanceador de carga.

## <a name="next-steps"></a>Próximas etapas
    
   * Saiba [como criar um monitor de conexão usando o portal do Azure](./connection-monitor-create-using-portal.md)  
   * Saiba [como criar um monitor de conexão usando o ARMClient](./connection-monitor-create-using-template.md)