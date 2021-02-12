---
title: Criar monitor de conexão-portal do Azure
titleSuffix: Azure Network Watcher
description: Este artigo descreve como criar um monitor no monitor de conexão usando o portal do Azure.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2020
ms.author: vinigam
ms.openlocfilehash: bd13712d137ec5a1fdfa6dec8e6f6d1e0a7432cb
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833160"
---
# <a name="create-a-monitor-in-connection-monitor-by-using-the-azure-portal"></a>Criar um monitor no monitor de conexão usando o portal do Azure

> [!IMPORTANT]
> A partir de 1 de julho de 2021, você não poderá adicionar novos testes em um espaço de trabalho existente ou habilitar um novo espaço de trabalho no Monitor de Desempenho de Rede. Você também não poderá adicionar novos monitores de conexão no monitor de conexão (clássico). Você pode continuar a usar os testes e os monitores de conexão criados antes de 1 de julho de 2021. Para minimizar a interrupção do serviço para suas cargas de trabalho atuais, [migre seus testes de monitor de desempenho de rede ](migrate-to-connection-monitor-from-network-performance-monitor.md) ou  [migre do monitor de conexão (clássico)](migrate-to-connection-monitor-from-connection-monitor-classic.md) para o novo monitor de conexão no observador de rede do Azure antes de 29 de fevereiro de 2024.

Saiba como usar o monitor de conexão para monitorar a comunicação entre seus recursos. Este artigo descreve como criar um monitor usando o portal do Azure. O monitor de conexão dá suporte a implantações de nuvem híbridas e do Azure.


## <a name="before-you-begin"></a>Antes de começar 

Nos monitores de conexão que você cria usando o monitor de conexão, você pode adicionar máquinas locais e VMs do Azure como fontes. Esses monitores de conexão também podem monitorar a conectividade com pontos de extremidade. Os pontos de extremidade podem estar no Azure ou em qualquer outra URL ou IP.

Aqui estão algumas definições para você começar:

* **Recurso de monitor de conexão**. Um recurso do Azure específico da região. Todas as entidades a seguir são propriedades de um recurso de monitor de conexão.
* **Ponto de extremidade**. Uma origem ou um destino que participa de verificações de conectividade. Exemplos de pontos de extremidade incluem:
    * VMs do Azure.
    * Redes virtuais do Azure.
    * Sub-redes do Azure.
    * Agentes locais.
    * Sub-redes locais.
    * Redes personalizadas locais que incluem várias sub-redes.
    * URLs e IPs.
* **Configuração de teste**. Uma configuração específica de protocolo para um teste. Com base no protocolo escolhido, você pode definir a porta, os limites, a frequência de teste e outros parâmetros.
* **Grupo de teste**. O grupo que contém pontos de extremidade de origem, pontos de extremidade de destino e configurações de teste. Um monitor de conexão pode conter mais de um grupo de teste.
* **Test**. A combinação de um ponto de extremidade de origem, ponto de extremidade de destino e configuração de teste. Um teste é o nível mais granular no qual os dados de monitoramento estão disponíveis. Os dados de monitoramento incluem a porcentagem de verificações que falharam e o RTT (tempo de ida e volta).

:::image type="content" source="./media/connection-monitor-2-preview/cm-tg-2.png" alt-text="Diagrama que mostra um monitor de conexão e define a relação entre os grupos de teste e os testes.":::


## <a name="create-a-connection-monitor"></a>Criar um monitor de conexão

Para criar um monitor no monitor de conexão usando o portal do Azure:

1. Na home page portal do Azure, vá para **observador de rede**.
1. No painel esquerdo, na seção **monitoramento** , selecione Monitor de **conexão**.

   Você verá todos os monitores de conexão que foram criados no monitor de conexão. Para ver os monitores de conexão que foram criados no monitor de conexão clássica, vá para a guia **Monitor de conexão** .

   :::image type="content" source="./media/connection-monitor-2-preview/cm-resource-view.png" alt-text="Captura de tela que mostra os monitores de conexão criados no monitor de conexão.":::
   
    
1. No painel do **Monitor de conexão** , no canto superior esquerdo, selecione **criar**.

   

1. Na guia **noções básicas** , insira informações para o monitor de conexão: 
   * **Nome do monitor de conexão**: Insira um nome para o monitor de conexão. Use as regras de nomenclatura padrão para recursos do Azure.
   * **Assinatura**: selecione uma assinatura para o monitor de conexão.
   * **Região**: selecione uma região para o monitor de conexão. Você pode selecionar apenas as VMs de origem que são criadas nessa região.
   * **Configuração do espaço de trabalho**: escolha um espaço de trabalho personalizado ou o espaço de trabalho padrão. Seu espaço de trabalho mantém os dados de monitoramento.
       * Para usar o espaço de trabalho padrão, marque a caixa de seleção. 
       * Para escolher um espaço de trabalho personalizado, desmarque a caixa de seleção. Em seguida, selecione a assinatura e a região para seu espaço de trabalho personalizado. 

   :::image type="content" source="./media/connection-monitor-2-preview/create-cm-basics.png" alt-text="Captura de tela que mostra a guia noções básicas no monitor de conexão.":::
   
1. Na parte inferior da guia, selecione **Avançar: grupos de teste**.

1. Adicione fontes, destinos e configurações de teste em seus grupos de teste. Para saber mais sobre como configurar seus grupos de teste, confira [criar grupos de teste no monitor de conexão](#create-test-groups-in-a-connection-monitor). 

   :::image type="content" source="./media/connection-monitor-2-preview/create-tg.png" alt-text="Captura de tela que mostra a guia grupos de teste no monitor de conexão.":::

1. Na parte inferior da guia, selecione **Avançar: criar alertas**. Para saber mais sobre a criação de alertas, consulte [criar alertas no monitor de conexão](#create-alerts-in-connection-monitor).

   :::image type="content" source="./media/connection-monitor-2-preview/create-alert.png" alt-text="Captura de tela que mostra a guia criar alerta.":::

1. Na parte inferior da guia, selecione **Avançar: revisar + criar**.

1. Na guia **revisar + criar** , examine as informações básicas e os grupos de teste antes de criar o monitor de conexão. Se você precisar editar o monitor de conexão, poderá fazer isso voltando para as respectivas guias. 
   :::image type="content" source="./media/connection-monitor-2-preview/review-create-cm.png" alt-text="Captura de tela que mostra a guia revisar + criar no monitor de conexão.":::
   > [!NOTE] 
   > A guia **revisar + criar** mostra o custo por mês durante o estágio do monitor de conexão. Atualmente, a coluna **custo/mês atual** não mostra nenhum encargo. Quando o monitor de conexão se tornar disponível de forma geral, essa coluna mostrará um encargo mensal. 
   > 
   > Mesmo durante o estágio do monitor de conexão, Log Analytics se aplicam encargos de ingestão.

1. Quando você estiver pronto para criar o monitor de conexão, na parte inferior da guia **revisar + criar** , selecione **criar**.

O monitor de conexão cria o recurso de monitor de conexão em segundo plano.

## <a name="create-test-groups-in-a-connection-monitor"></a>Criar grupos de teste em um monitor de conexão

Cada grupo de teste em um monitor de conexão inclui origens e destinos que são testados em parâmetros de rede. Eles são testados para a porcentagem de verificações que falham e o RTT sobre as configurações de teste.

No portal do Azure, para criar um grupo de teste em um monitor de conexão, especifique valores para os seguintes campos:

* **Desabilitar grupo de teste**: você pode marcar essa caixa de seleção para desabilitar o monitoramento de todas as origens e destinos que o grupo de teste especifica. Essa seleção é desmarcada por padrão.
* **Nome**: nomeie seu grupo de teste.
* **Fontes**: você pode especificar VMs do Azure e máquinas locais como fontes se os agentes estiverem instalados neles. Para saber mais sobre como instalar um agente para sua fonte, consulte [instalar agentes de monitoramento](./connection-monitor-overview.md#install-monitoring-agents).
   * Para escolher os agentes do Azure, selecione a guia **pontos de extremidade do Azure** . Aqui você vê somente as VMs que estão associadas à região que você especificou quando criou o monitor de conexão. Por padrão, as VMs são agrupadas na assinatura à qual pertencem. Esses grupos são recolhidos. 
   
       Você pode fazer uma busca detalhada do nível de **assinatura** para outros níveis na hierarquia:

      **Assinatura**  >  do **Grupo**  >  de recursos **VNET**  >  **Sub-rede**  >  **VMs com agentes**

      Você também pode alterar o seletor **Agrupar por** para iniciar a árvore de qualquer outro nível. Por exemplo, se você agrupar por rede virtual, verá as VMs que têm agentes nas VMs de sub-rede de **VNET** de hierarquia  >    >  **com agentes**.

       Quando você seleciona uma VNET, uma sub-rede ou uma única VM, a ID de recurso correspondente é definida como o ponto de extremidade. Por padrão, todas as VMs na VNET ou sub-rede selecionada que têm a extensão do observador de rede do Azure participam do monitoramento. Para reduzir o escopo, selecione sub-redes ou agentes específicos ou altere o valor da propriedade escopo. 

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-sources.png" alt-text="Captura de tela que mostra o painel adicionar fontes e a guia pontos de extremidade do Azure no monitor de conexão.":::

   * Para escolher os agentes locais, selecione a guia **não-pontos de extremidade do Azure** . Por padrão, os agentes são agrupados em espaços de trabalho por região. Todos esses espaços de trabalho têm o Monitor de Desempenho de Rede configurado. 
   
       Se você precisar adicionar Monitor de Desempenho de Rede ao seu espaço de trabalho, obtenha-o no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Para obter informações sobre como adicionar Monitor de Desempenho de Rede, consulte [Monitoring Solutions in Azure monitor](../azure-monitor/insights/solutions.md). 
   
       Em **criar monitor de conexão**, na guia **noções básicas** , a região padrão é selecionada. Se você alterar a região, poderá escolher agentes de espaços de trabalho na nova região. Você pode selecionar um ou mais agentes ou sub-redes. Na exibição de **sub-rede** , você pode selecionar IPS específicos para monitoramento. Se você adicionar várias sub-redes, uma rede local personalizada chamada **OnPremises_Network_1** será criada. Você também pode alterar o seletor **Group by** para agrupar por agentes.

      :::image type="content" source="./media/connection-monitor-2-preview/add-non-azure-sources.png" alt-text="Captura de tela que mostra o painel adicionar fontes e a guia pontos de extremidade não Azure no monitor de conexão.":::

   * Para escolher os pontos de extremidade usados recentemente, você pode usar a guia **ponto de extremidades recente** 
   
   * Quando terminar de configurar as fontes, selecione **concluído** na parte inferior da guia. Você ainda pode editar propriedades básicas, como o nome do ponto de extremidade, selecionando o ponto de extremidade na exibição **Criar grupo de teste** . 

* **Destinos**: você pode monitorar a conectividade com uma VM do Azure, um computador local ou qualquer ponto de extremidade (um IP público, uma URL ou um FQDN) especificando-o como um destino. Em um único grupo de teste, você pode adicionar VMs do Azure, máquinas locais, URLs do Office 365, URLs do Dynamics 365 e pontos de extremidade personalizados.

    * Para escolher VMs do Azure como destinos, selecione a guia **pontos de extremidade do Azure** . Por padrão, as VMs do Azure são agrupadas em uma hierarquia de assinatura que está na região que você selecionou em **criar monitor de conexão** na guia **noções básicas** . Você pode alterar a região e escolher VMs do Azure na nova região. Em seguida, você pode fazer uma busca detalhada do nível de **assinatura** para outros níveis na hierarquia, assim como é possível quando você define os pontos de extremidade do Azure de origem.

      Você pode selecionar VNETs, sub-redes ou VMs únicas, como é possível quando você define os pontos de extremidade do Azure de origem. Quando você seleciona uma VNET, uma sub-rede ou uma única VM, a ID de recurso correspondente é definida como o ponto de extremidade. Por padrão, todas as VMs na VNET ou sub-rede selecionada que têm a extensão do observador de rede participam do monitoramento. Para reduzir o escopo, selecione sub-redes ou agentes específicos ou altere o valor da propriedade escopo. 

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-dests1.png" alt-text="<captura de tela que mostra o painel Adicionar destinos e a guia pontos de extremidade do Azure. >":::

      :::image type="content" source="./media/connection-monitor-2-preview/add-azure-dests2.png" alt-text="<captura de tela que mostra o painel Adicionar destinos no nível da assinatura. >":::
       
    
    * Para escolher os agentes não Azure como destinos, selecione a guia **pontos de extremidade não Azure** . Por padrão, os agentes são agrupados em espaços de trabalho por região. Todos esses espaços de trabalho têm Monitor de Desempenho de Rede configurados. 
    
      Se você precisar adicionar Monitor de Desempenho de Rede ao seu espaço de trabalho, obtenha-o no Azure Marketplace. Para obter informações sobre como adicionar Monitor de Desempenho de Rede, consulte [Monitoring Solutions in Azure monitor](../azure-monitor/insights/solutions.md). 

      Em **criar monitor de conexão**, na guia **noções básicas**   , a região padrão é selecionada. Se você alterar a região, poderá escolher agentes de espaços de trabalho na nova região. Você pode selecionar um ou mais agentes ou sub-redes. Na exibição de **sub-rede** , você pode selecionar IPS específicos para monitoramento. Se você adicionar várias sub-redes, uma rede local personalizada chamada **OnPremises_Network_1** será criada.  

      :::image type="content" source="./media/connection-monitor-2-preview/add-non-azure-dest.png" alt-text="Captura de tela que mostra o painel Adicionar destinos e a guia pontos de extremidade não Azure.":::
    
    * Para escolher pontos de extremidade públicos como destinos, selecione a guia **endereços externos** . A lista de pontos de extremidade inclui URLs de teste do Office 365 e URLs de teste do Dynamics 365, agrupadas por nome. Você também pode escolher os pontos de extremidade que foram criados em outros grupos de teste no mesmo monitor de conexão. 
    
        Para adicionar um ponto de extremidade, no canto superior direito, selecione **Adicionar ponto de extremidade**. Em seguida, forneça um nome de ponto de extremidade e URL, IP ou FQDN.

       :::image type="content" source="./media/connection-monitor-2-preview/add-endpoints.png" alt-text="Captura de tela que mostra onde adicionar pontos de extremidade públicos como destinos no monitor de conexão.":::

    * Para escolher os pontos de extremidade usados recentemente, vá para a guia **ponto de extremidades recente**   .
    * Quando terminar de escolher destinos, selecione **concluído**. Você ainda pode editar propriedades básicas, como o nome do ponto de extremidade, selecionando o ponto de extremidade na exibição **Criar grupo de teste** . 

* **Configurações de teste**: você pode adicionar uma ou mais configurações de teste a um grupo de teste. Crie uma nova configuração de teste usando a **nova** guia de configuração. Ou adicione uma configuração de teste de outro grupo de teste no mesmo monitor de conexão da guia **escolher existente** .

    * **Nome da configuração de teste**: nomeie a configuração de teste.
    * **Protocolo**: selecione **TCP**, **ICMP** ou **http**. Para alterar HTTP para HTTPS, selecione **http** como o protocolo e, em seguida, selecione **443** como a porta.
        * **Criar configuração de teste TCP**: essa caixa de seleção será exibida somente se você selecionar **http** na lista **protocolo** . Marque essa caixa de seleção para criar outra configuração de teste que use as mesmas origens e destinos que você especificou em outro lugar em sua configuração. A nova configuração de teste é denominada **\<name of test configuration> _networkTestConfig**.
        * **Desabilitar traceroute**: essa caixa de seleção se aplica quando o protocolo é TCP ou ICMP. Selecione esta caixa para impedir que as fontes descubram a topologia e o RTT de salto a salto.
    * **Porta de destino**: você pode fornecer uma porta de destino de sua escolha.
        * **Escutar na porta**: essa caixa de seleção se aplica quando o protocolo é TCP. Marque essa caixa de seleção para abrir a porta TCP escolhida se ela ainda não estiver aberta. 
    * **Frequência de teste**: nessa lista, especifique com que frequência as fontes executarão ping nos destinos no protocolo e na porta que você especificou. Você pode escolher 30 segundos, 1 minuto, 5 minutos, 15 minutos ou 30 minutos. Selecione **personalizado** para inserir outra frequência entre 30 segundos e 30 minutos. As fontes testarão a conectividade com destinos com base no valor que você escolher. Por exemplo, se você selecionar 30 segundos, as fontes verificarão a conectividade com o destino pelo menos uma vez em cada período de 30 segundos.
    * **Limite de êxito**: você pode definir limites nos seguintes parâmetros de rede:
       * **Falha nas verificações**: defina a porcentagem de verificações que podem falhar quando as origens verificarem a conectividade com os destinos usando os critérios que você especificou. Para o protocolo TCP ou ICMP, a porcentagem de verificações com falha pode ser igual à porcentagem de perda de pacotes. Para o protocolo HTTP, esse valor representa a porcentagem de solicitações HTTP que não receberam resposta.
       * **Tempo de ida** e volta: defina o RTT, em milissegundos, por quanto tempo as fontes podem levar para se conectar ao destino pela configuração de teste.
       
   :::image type="content" source="./media/connection-monitor-2-preview/add-test-config.png" alt-text="Captura de tela que mostra onde configurar uma configuração de teste no monitor de conexão.":::
       
## <a name="create-alerts-in-connection-monitor"></a>Criar alertas no monitor de conexão

Você pode configurar alertas em testes que estão falhando com base nos limites definidos em configurações de teste.

No portal do Azure, para criar alertas para um monitor de conexão, especifique valores para esses campos: 

- **Criar alerta**: você pode marcar essa caixa de seleção para criar um alerta de métrica em Azure monitor. Quando você marcar essa caixa de seleção, os outros campos serão habilitados para edição. Encargos adicionais para o alerta serão aplicáveis, com base nos [preços dos alertas](https://azure.microsoft.com/pricing/details/monitor/). 

- **Escopo**  >  **Recurso**  >  do **Hierarquia**: esses valores são preenchidos automaticamente, com base nos valores especificados na guia **noções básicas** .

- **Nome da condição**: o alerta é criado na `Test Result(preview)` métrica. Quando o resultado do teste do monitor de conexão for um resultado com falha, a regra de alerta será acionada. 

- **Nome do grupo de ações**: você pode inserir seu email diretamente ou pode criar alertas por meio de grupos de ação. Se você inserir seu email diretamente, será criado um grupo de ação com o nome **NPM email** Group. A ID de email é adicionada a esse grupo de ações. Se você optar por usar grupos de ações, será necessário selecionar um grupo de ações criado anteriormente. Para saber como criar um grupo de ações, consulte [criar grupos de ações na portal do Azure](../azure-monitor/platform/action-groups.md). Depois que o alerta for criado, você poderá [gerenciar seus alertas](../azure-monitor/platform/alerts-metric.md#view-and-manage-with-azure-portal). 

- **Nome da regra de alerta**: o nome do monitor de conexão.

- **Habilitar Regra após a criação**: Marque esta caixa de seleção para habilitar a regra de alerta com base na condição. Desabilite essa caixa de seleção se desejar criar a regra sem habilitá-la. 

:::image type="content" source="./media/connection-monitor-2-preview/create-alert-filled.png" alt-text="Captura de tela que mostra a guia criar alerta no monitor de conexão.":::

## <a name="scale-limits"></a> Limites de escala

Os monitores de conexão têm estes limites de escala:

* Máximo de monitores de conexão por assinatura por região: 100
* Máximo de grupos de teste por monitor de conexão: 20
* Máximo de origens e destinos por monitor de conexão: 100
* Configurações de teste máximas por monitor de conexão: 2 por meio do portal do Azure

## <a name="next-steps"></a>Próximas etapas

* Saiba [como analisar dados de monitoramento e definir alertas](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts).
* Saiba [como diagnosticar problemas em sua rede](./connection-monitor-overview.md#diagnose-issues-in-your-network).
