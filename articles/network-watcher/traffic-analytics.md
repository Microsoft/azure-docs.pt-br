---
title: Análise de Tráfego do Azure | Microsoft Docs
description: Saiba como analisar logs de fluxo de grupo de segurança de rede do Azure com a Análise de Tráfego.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/15/2018
ms.author: kumud
ms.reviewer: yagup
ms.openlocfilehash: ca3174ad69185da88bf89c843f641dd2b20d9ac5
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67872484"
---
# <a name="traffic-analytics"></a>Análise de Tráfego

Análise de Tráfego é uma solução baseada em nuvem, que oferece visibilidade sobre atividade de usuário e aplicativo nas redes em nuvem. Análise de Tráfego analisa os logs de fluxo de grupo de segurança da rede (NSG) do Observador de Rede para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Com a Análise de Tráfego, você pode:

- Visualizar a atividade de rede nas assinaturas do Azure e identificar pontos de acesso.
- Identificar ameaças à segurança e proteger sua rede com informações como portas abertas, aplicativos tentando acesso à internet e máquinas virtuais (VM) conectando-se a redes não autorizadas.
- Compreender os padrões de fluxo de tráfego entre regiões do Azure e a internet para otimizar a implantação de rede para desempenho e capacidade.
- Identificar problemas de configuração de rede originando conexões com falha em sua rede.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="why-traffic-analytics"></a>Por que a Análise de Tráfego?

É essencial monitorar, gerenciar e conhecer sua própria rede para um desempenho, conformidade e segurança incomparáveis. Conhecer o seu próprio ambiente é de suma importância para protegê-lo e otimizá-lo. Geralmente é necessário saber o estado atual da rede, quem está se conectando, de onde eles estão se conectando, quais portas estão abertas para a Internet, o comportamento esperado da rede, o comportamento irregular da rede e os aumentos repentinos no tráfego.

Redes de nuvem são diferentes de redes de empresas no local, onde você tem netflow ou protocolo equivalente compatível com roteadores e comutadores, que fornecem a capacidade de coletar o tráfego de rede IP conforme ele entra ou sai de uma interface de rede. Analisando dados de fluxo de tráfego, você pode criar uma análise de volume e fluxo de tráfego de rede.

As redes virtuais do Azure têm logs de fluxo do NSG, que fornecem informações sobre o tráfego de IP de entrada e saída por meio de um Grupo de Segurança de Rede associado a interfaces de rede individuais, VMs ou sub-redes. Analisando o NSG bruto de fluxo de logs e inserção de inteligência de segurança, topologia e geografia, a Análise de Tráfego pode fornecer ideias sobre o fluxo de tráfego em seu ambiente. A Análise de Tráfego fornece informações como hosts com mais comunicação, protocolos de aplicativo com mais comunicação, pares de host com mais conversas, tráfego permitido/bloqueado, tráfego de entrada/saída, portas de internet abertas, regras com mais bloqueio, distribuição de tráfego por datacenter do Azure, rede virtual, sub-redes, ou, redes não autorizadas.

## <a name="key-components"></a>Principais componentes

- **NSG (Grupo de Segurança de Rede)** : Contém uma lista de regras de segurança que permitem ou negam o tráfego de rede a recursos conectados a uma Rede Virtual do Azure. Os NSGs podem ser associados a sub-redes, VMs individuais (clássicas) ou interfaces de rede individuais (NIC) anexadas a VMs (Resource Manager). Para obter mais informações, confira [Visão geral do Grupo de Segurança de Rede](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Logs de fluxo do NSG (Grupo de Segurança de Rede)** : Permitem que você exiba informações sobre o tráfego IP de entrada e saída por meio de um grupo de segurança de rede. Os logs de fluxo do NSG são gravados no formato json e mostram os fluxos de entrada e de saída por regra, a NIC à qual o fluxo se aplica, as informações de cinco tuplas sobre o fluxo (IP de origem/destino, porta de origem/destino, e protocolo) e se o tráfego foi permitido ou negado. Para obter mais informações sobre os logs de fluxo do NSG, consulte [logs de fluxo do NSG](network-watcher-nsg-flow-logging-overview.md).
- **Log Analytics**: Um serviço do Azure que coleta dados de monitoramento e armazena os dados em um repositório central. Esses dados podem incluir eventos, dados de desempenho ou dados personalizados fornecidos pela API do Azure. Depois de coletados, os dados ficam disponíveis para alertas, análise e exportação. Os aplicativos de monitoramento, como o monitor de desempenho de rede e a análise de tráfego, são criados usando logs de Azure Monitor como base. Para obter mais informações, consulte [Azure monitor logs](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Espaço de Trabalho do Log Analytics**: Uma instância de logs de Azure Monitor, onde os dados pertencentes a uma conta do Azure são armazenados. Para obter mais informações sobre espaços de trabalho do Log Analytics, consulte [criar um espaço de trabalho do log Analytics](../azure-monitor/learn/quick-create-workspace.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- **Observador de Rede**: Um serviço regional que permite monitorar e diagnosticar condições em um nível de cenário de rede no Azure. Você pode ativar e desativar os logs de fluxo do NSG com o Observador de Rede. Para obter mais informações, consulte [Observador de Rede](network-watcher-monitoring-overview.md).

## <a name="how-traffic-analytics-works"></a>Como funciona a Análise de Tráfego

A Análise de Tráfego examina os logs de fluxo NSG brutos e captura logs reduzidos agregando fluxos comuns entre o mesmo endereço IP de origem, o endereço IP de destino, a porta de destino e o protocolo. Por exemplo, o Host 1 (endereço IP: 10.10.10.10) em comunicação com Host 2 (endereço IP: 10.10.20.10), 100 vezes durante um período de 1 hora usando porta (por exemplo, 80) e protocolo (por exemplo, http). O log reduzido tem uma entrada, que o Host 1 e o Host 2 se comunicaram 100 vezes em um período de 1 hora usando a porta *80* e o protocolo *HTTP*, em vez de ter 100 entradas. Logs reduzidos são aprimorados com informações de região, segurança e topologia e armazenados em um espaço de trabalho do Log Analytics. A figura a seguir mostra o fluxo de dados:

![Fluxo de dados para processamento de logs de fluxo NSG](./media/traffic-analytics/data-flow-for-nsg-flow-log-processing.png)

## <a name="supported-regions"></a>Regiões com suporte

Você pode usar a análise de tráfego para NSGs em qualquer uma das seguintes regiões com suporte:

* Canadá Central
* Centro-Oeste dos EUA
* East US
* Leste dos EUA 2
* Centro-Norte dos EUA
* Centro-Sul dos Estados Unidos
* Centro dos EUA
* Oeste dos EUA
* Oeste dos EUA 2
* França Central
* Europa Ocidental
* Norte da Europa
* Sul do Brasil
* Oeste do Reino Unido
* Sul do Reino Unido
* Leste da Austrália
* Sudeste da Austrália
* Ásia Oriental
* Sudeste Asiático
* Coreia Central
* Índia Central
* Sul da Índia
* Leste do Japão 
* Oeste do Japão
* Gov. dos EUA – Virgínia

O espaço de trabalho do Log Analytics deve existir nas seguintes regiões:
* Canadá Central
* Centro-Oeste dos EUA
* East US
* Leste dos EUA 2
* Centro-Sul dos Estados Unidos
* Oeste dos EUA
* Oeste dos EUA 2
* Centro dos EUA
* França Central
* Norte da Europa
* Europa Ocidental
* Sul do Reino Unido
* Leste da Austrália
* Sudeste da Austrália
* Ásia Oriental
* Sudeste Asiático
* Coreia Central
* Índia Central
* Leste do Japão
* Gov. dos EUA – Virgínia

## <a name="prerequisites"></a>Pré-requisitos

### <a name="user-access-requirements"></a>Requisitos de acesso do usuário

A conta deve ser um membro de uma das [funções internas](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) a seguir do Azure:

|Modelo de implantação   | Role                   |
|---------          |---------               |
|Gerenciador de Recursos   | Proprietário                  |
|                   | Colaborador            |
|                   | Leitor                 |
|                   | Colaborador de rede    |

Se a conta não estiver atribuída a uma das funções internas, ela deverá ser atribuída a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) à qual são atribuídas as ações a seguir, no nível da assinatura:

- "Microsoft.Network/applicationGateways/read"
- "Microsoft.Network/connections/read"
- "Microsoft.Network/loadBalancers/read"
- "Microsoft.Network/localNetworkGateways/read"
- "Microsoft.Network/networkInterfaces/read"
- "Microsoft.Network/networkSecurityGroups/read"
- "Microsoft.Network/publicIPAddresses/read"
- "Microsoft.Network/routeTables/read"
- "Microsoft.Network/virtualNetworkGateways/read"
- "Microsoft.Network/virtualNetworks/read"

Para obter informações sobre como verificar as permissões de acesso do usuário, consulte [Perguntas frequentes sobre análise de Tráfego](traffic-analytics-faq.md).

### <a name="enable-network-watcher"></a>Habilitar o Observador de Rede

Para analisar o tráfego, você precisa ter um observador de rede existente, ou [habilitar um observador de rede](network-watcher-create.md) em cada região cujos NSGs você deseja analisar o tráfego. A Análise de Tráfego pode ser habilitada para os NSGs hospedados em qualquer uma das [regiões com suporte](#supported-regions).

### <a name="select-a-network-security-group"></a>Selecionar Grupo de Segurança de Rede

Antes de habilitar o log de fluxo do NSG, você deve ter um grupo de segurança de rede para registrar os fluxos. Se você não tiver um grupo de segurança de rede, consulte [Criar grupos de segurança de rede](../virtual-network/manage-network-security-group.md#create-a-network-security-group) para criar um.

No lado esquerdo do portal do Azure, selecione **Monitor**, em seguida, **Observador de Rede**e, em seguida, selecione **logs de fluxos do NSG**. Selecione o grupo de segurança de rede que você deseja habilitar um log de fluxo do NSG, conforme mostrado na figura a seguir:

![Seleção de NSGs que exigem a habilitação do log de fluxo do NSG](./media/traffic-analytics/selection-of-nsgs-that-require-enablement-of-nsg-flow-logging.png)

Se você tentar habilitar a Análise de Tráfego para um NSG que está hospedado em qualquer região que não seja [regiões com suporte](#supported-regions), você receberá um erro "Não encontrado".

## <a name="enable-flow-log-settings"></a>Habilitar configurações de log de fluxos

Antes de habilitar as configurações de log de fluxos, você deve concluir as seguintes tarefas:

Registre o provedor do Azure Insights, se ainda não estiver registrado para a sua assinatura:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

Se você ainda não tiver uma conta de armazenamento do Azure para armazenar os logs de fluxo do NSG, você deve criar uma conta de armazenamento. Você pode criar uma conta de armazenamento com o comando a seguir. Antes de executar o comando, substitua `<replace-with-your-unique-storage-account-name>` com um nome que seja exclusivo em todos os locais do Azure, entre 3 a 24 caracteres de comprimento, usando apenas números e letras minúsculas. Você também pode alterar o nome do grupo de recursos, se necessário.

```azurepowershell-interactive
New-AzStorageAccount `
  -Location westcentralus `
  -Name <replace-with-your-unique-storage-account-name> `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

Selecione as opções a seguir, conforme mostrado na imagem:

1. Selecione *Ativado* para **Status**
2. Selecione a *versão 2* para a **versão dos logs de fluxo**. A versão 2 contém estatísticas de sessão de fluxo (bytes e pacotes)
3. Selecione uma conta de armazenamento existente na qual armazenar os logs de fluxo. Se você deseja armazenar os dados indefinidamente, defina o valor como *0*. Incorrem em taxas de armazenamento do Azure para a conta de armazenamento.
4. Defina a **Retenção** para o número de dias que você deseja armazenar os dados.
5. Selecione *Ativado* para **Status de Análise de Tráfego**.
6. Selecione um workspace do Log Analytics (OMS) existente ou selecione **Criar novo workspace** para criar um novo. Um espaço de trabalho do Log Analytics é usado pela Análise de Tráfego para armazenar os dados agregados e indexados que são usados para gerar a análise. Se você selecionar um workspace existente, ele deve existir em uma das [regiões com suporte](#supported-regions) e ter sido atualizado para a nova linguagem de consulta. Se você não desejar atualizar um workspace existente ou não tem um workspace em uma região com suporte, crie um novo. Para obter mais informações sobre linguagens de consulta, consulte [Atualização do Azure Log Analytics para a nova pesquisa de logs ](../log-analytics/log-analytics-log-search-upgrade.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

    O espaço de trabalho do Log Analytics que hospeda a solução de Análise de Tráfego e os NSGs não precisa estar na mesma região. Por exemplo, você pode ter Análise de Tráfego em um workspace na região Europa Ocidental e ter os NSGs no Leste dos EUA e Oeste dos EUA. Podem ser configurados vários NSGs no mesmo workspace.
7. Clique em **Salvar**.

    ![Seleção de conta de armazenamento, espaço de trabalho do Log Analytics e habilitação da Análise de Tráfego](./media/traffic-analytics/selection-of-storage-account-log-analytics-workspace-and-traffic-analytics-enablement-nsg-flowlogs-v2.png)

Repita as etapas anteriores para quaisquer outros NSGs para os quais você deseja habilitar a Análise de Tráfego. Os dados dos logs de fluxo são enviados para o espaço de trabalho, portanto, verifique se as leis e regulamentos locais em seu país/região permitem o armazenamento de dados na região onde o espaço de trabalho existe.

Você também pode configurar a análise de tráfego usando o cmdlet do PowerShell [set-AzNetworkWatcherConfigFlowLog](/powershell/module/az.network/set-aznetworkwatcherconfigflowlog) no Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="view-traffic-analytics"></a>Exibir Análise de Tráfego

No lado esquerdo do portal, selecione **Todos os serviços**, em seguida, digite *Monitorar* na caixa **Filtro**. Selecione **Monitorar** quando aparecer nos resultados da pesquisa. Para começar a explorar a análise de tráfego e seus recursos, selecione **Observador de Rede**, em seguida, **Análise de Tráfego**.

![Acessar o painel de Análise de Tráfego](./media/traffic-analytics/accessing-the-traffic-analytics-dashboard.png)

O painel pode levar até 30 minutos para aparecer pela primeira vez, pois a Análise de Tráfego deve primeiro agregar dados suficientes para derivar informações significativas, antes que possa gerar qualquer relatório.

## <a name="usage-scenarios"></a>Cenários de uso

Algumas das informações que talvez você pode querer obter depois que a Análise de Tráfego estiver totalmente configurada são as seguintes:

### <a name="find-traffic-hotspots"></a>Localizar pontos de acesso de tráfego

**Procurar**

- Quais hosts, sub-redes e redes virtuais estão enviando ou recebendo mais tráfego, percorrendo o máximo tráfego mal-intencionado e bloqueando fluxos significativos?
    - Verifique o gráfico comparativo de host, sub-rede e rede virtual. Reconhecer quais hosts, sub-redes e redes virtuais estão enviando ou recebendo mais tráfego pode ajudar a identificar os hosts que estão processando mais tráfego e se a distribuição de tráfego é feita corretamente.
    - Você pode avaliar se o volume de tráfego é apropriado para um host. O volume de tráfego é um comportamento normal ou merece mais investigação?
- Quanto tráfego de entrada/saída existe?
    -   O host deve receber mais tráfego de entrada que de saída, ou vice-versa?
- Estatísticas do tráfego bloqueado.
    - Por que um host está bloqueando um volume significativo de tráfego benigno? Esse comportamento requer investigação adicional e provavelmente otimização da configuração
- Estatísticas de tráfego mal-intencionado permitido/bloqueado
  - Por que um host está recebendo tráfego mal-intencionado e por que fluxos de origem mal-intencionada são permitidos? Esse comportamento requer investigação adicional e provavelmente otimização da configuração.

    Selecione **Ver todos**, em **Host**, conforme mostrado na figura a seguir:

    ![Painel de apresentação de host com a maioria dos detalhes de tráfego](media/traffic-analytics/dashboard-showcasing-host-with-most-traffic-details.png)

- A imagem a seguir mostra a tendência de tempo para os cinco hosts que mais se comunicam e os detalhes relacionados ao fluxo (fluxos de entrada/saída permitidos e entrada/saída negados) para um host:

    ![Tendência dos cinco hosts que mais se comunicam](media/traffic-analytics/top-five-most-talking-host-trend.png)

**Procurar**

- Quais são os pares de hosts com mais conversas?
    - Comportamento esperado, como comunicação front-end ou back-end, ou comportamento irregular, como tráfego de internet back-end.
- Estatísticas de tráfego permitido/bloqueado
    - Por que um host está permitindo ou bloqueando volume de tráfego significativo
- Protocolo de aplicativo usado com mais frequência entre os pares de host com mais conversas:
    - Esses aplicativos são permitidos nesta rede?
    - Os aplicativos estão configurados corretamente? Eles estão usando o protocolo apropriado para a comunicação? Selecione **Ver todos** em **Conversa frequente**, conforme mostrado na figura a seguir:

        ![Painel de apresentação de conversa mais frequente](./media/traffic-analytics/dashboard-showcasing-most-frequent-conversation.png)

- A imagem a seguir mostra a tendência de tempo das cinco conversas principais e os detalhes relacionados ao fluxo como fluxos de entrada e saída permitidos e negados para um par de conversas:

    ![Tendência e detalhes das cinco conversas principais](./media/traffic-analytics/top-five-chatty-conversation-details-and-trend.png)

**Procurar**

- Qual protocolo de aplicativo é mais usado em seu ambiente e quais pares de host que conversam estão usando mais o protocolo de aplicativo?
    - Esses aplicativos são permitidos nesta rede?
    - Os aplicativos estão configurados corretamente? Eles estão usando o protocolo apropriado para a comunicação? O comportamento esperado é portas comuns, como 80 e 443. Para comunicação padrão, se todas as portas incomum são exibidas, eles podem exigir uma alteração de configuração. Selecione **Ver todos** em **Porta do aplicativo**, na figura a seguir:

        ![Painel apresentando os principais protocolos de aplicativos](./media/traffic-analytics/dashboard-showcasing-top-application-protocols.png)

- As imagens a seguir mostram a tendência de tempo para os cinco principais protocolos L7 e os detalhes relacionados ao fluxo (por exemplo, fluxos permitidos e negados) para um protocolo L7:

    ![Tendências e detalhes dos cinco principais protocolos de camada 7](./media/traffic-analytics/top-five-layer-seven-protocols-details-and-trend.png)

    ![Detalhes de fluxos para protocolo de aplicativo na pesquisa de logs](./media/traffic-analytics/flow-details-for-application-protocol-in-log-search.png)

**Procurar**

- Tendências de utilização de capacidade de um gateway de VPN em seu ambiente.
    - Cada SKU VPN permite que uma determinada quantidade de largura de banda. Os gateways de VPN estão subutilizados?
    - Seus gateways estão atingindo a capacidade? Você deve atualizar para o próximo SKU superior?
- Quais são os hosts com mais conversas, por meio de qual gateway VPN, por qual porta?
    - Esse padrão é normal? Selecione **Ver todos** em **Gateway de VPN**, conforme mostrado na figura a seguir:

        ![Painel apresentando as principais conexões VPN ativas](./media/traffic-analytics/dashboard-showcasing-top-active-vpn-connections.png)

- A imagem a seguir mostra a tendência de tempo de utilização da capacidade de um Gateway de VPN do Azure e os detalhes relacionados ao fluxo (por exemplo, portas e fluxos permitidos):

    ![Detalhes de fluxo e a tendência de utilização do gateway de VPN](./media/traffic-analytics/vpn-gateway-utilization-trend-and-flow-details.png)

### <a name="visualize-traffic-distribution-by-geography"></a>Visualizar a distribuição de tráfego por região

**Procurar**

- Distribuição de tráfego por data center como principais fontes de tráfego para um data center, principais redes não autorizadas que conversam com o data center e principais protocolos de aplicativo que conversam.
  - Se você observar mais carga em um data center, você pode planejar para a distribuição de tráfego eficiente.
  - Se redes não autorizadas estiverem conversando no data center, corrija as regras NSG para bloqueá-las.

    Selecione **Exibir mapa** em **Seu ambiente**, conforme mostrado na figura a seguir:

    ![Painel apresentando distribuição de tráfego](./media/traffic-analytics/dashboard-showcasing-traffic-distribution.png)

- O mapa geográfico mostra a faixa de opção superior para seleção de parâmetros, como data centers (implantado/não-implantação/ativo/inativo/Análise de Tráfego habilitado/Análise de Tráfego não habilitado) e países/regiões que contribuem com tráfego benigno/mal-intencionado para o ativo planta

    ![Exibição de mapa de área de geográfica mostrando implantação ativa](./media/traffic-analytics/geo-map-view-showcasing-active-deployment.png)

- O mapa geográfico mostra a distribuição de tráfego para um data center de países/regiões e continentes que se comunicam com ele em linhas azuis (tráfego benigno) e vermelho (tráfego mal-intencionado):

    ![Exibição de mapa geográfico mostrando a distribuição de tráfego para países/regiões e continentes](./media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png)

    ![Detalhes de fluxo para distribuição de tráfego na pesquisa de log](./media/traffic-analytics/flow-details-for-traffic-distribution-in-log-search.png)

### <a name="visualize-traffic-distribution-by-virtual-networks"></a>Visualizar a distribuição de tráfego por redes virtuais

**Procurar**

- Distribuição de tráfego por rede virtual, topologia, principais fontes de tráfego na rede virtual, principais redes não autorizadas que conversam com a rede virtual e principais protocolos de aplicativo que conversam.
  - Saber qual rede virtual está conversando com qual rede virtual. Se a conversa não é esperada, ela pode ser corrigido.
  - Se redes não autorizadas estão conversando com uma rede virtual, você pode corrigir as regras NSG para bloquear as redes não autorizadas.
 
    Selecione **Exibir VNets** em **Seu ambiente**, conforme mostrado na figura a seguir:

    ![Painel mostrando a distribuição de rede virtual](./media/traffic-analytics/dashboard-showcasing-virtual-network-distribution.png)

- A Topologia de Rede Virtual mostra a faixa de opções para seleção de parâmetros, como de uma rede virtual (rede virtual entre conexões/ativa/inativa), conexões externas, fluxos ativos e fluxos mal-intencionado da rede virtual.
- É possível filtrar a Topologia de Rede Virtual com base em assinaturas, workspaces, grupos de recursos e intervalo de tempo. Filtros adicionais que ajudam a reconhecer o fluxo são: Tipo de fluxo (InterVNet, IntraVNET e assim por diante), direção do fluxo (entrada, saída), status do fluxo (permitido, bloqueado), VNETs (direcionado e conectado), tipo de conexão (emparelhamento ou gateway-P2S e S2S) e NSG. Use esses filtros para concentrar-se nas VNets que você quer examinar em detalhes.
- A Topologia de Rede Virtual mostra a distribuição de tráfego a uma rede virtual com relação aos fluxos (permitidos/bloqueados/entrada/saída/benignos/mal-intencionado), protocolo de aplicativo e grupos de segurança de rede, por exemplo:

    ![Topologia de rede virtual mostrando a distribuição de tráfego e detalhes de fluxo](./media/traffic-analytics/virtual-network-topology-showcasing-traffic-distribution-and-flow-details.png)
    
    ![Topologia de rede virtual mostrando o nível superior e mais filtros](./media/traffic-analytics/virtual-network-filters.png)

    ![Detalhes de fluxo para distribuição de tráfego de rede virtual na pesquisa de log](./media/traffic-analytics/flow-details-for-virtual-network-traffic-distribution-in-log-search.png)

**Procurar**

- Distribuição de tráfego por sub-rede, topologia, principais fontes de tráfego na sub-rede, principais redes não autorizadas que conversam com a sub-rede e principais protocolos de aplicativo que conversam.
    - Saber qual sub-rede está conversando com qual sub-rede. Se você vir conversas inesperadas, você pode corrigir sua configuração.
    - Se redes não autorizadas estão conversando com uma sub-rede, você será capaz de corrigi-lo ao configurar as regras NSG para bloquear as redes não autorizadas.
- A topologia de sub-redes mostra a faixa de opções para seleção de parâmetros como sub-redes ativas/inativas, conexões externas, fluxos ativos e fluxos mal-intencionado da sub-rede.
- A Topologia de Sub-Rede mostra a distribuição de tráfego a uma rede virtual com relação aos fluxos (permitidos/bloqueados/entrada/saída/benignos/mal-intencionado), protocolo de aplicativo e NSGs, por exemplo:

    ![Topologia de sub-rede distribuição de tráfego de uma sub-rede da rede virtual em relação a fluxos](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-virtual-subnet-with-regards-to-flows.png)

**Procurar**

Distribuição de tráfego por gateway de Aplicativo e Load Balancer, topologia, principais fontes de tráfego, redes não autorizadas que conversam com o gateway de Aplicativo e Load Balancer e principais protocolos de aplicativo de conversam. 
    
 - Saber qual sub-rede está conversando com qual gateway de Aplicativo ou Load Balancer. Se você observar conversas inesperadas, poderá corrigir a configuração.
 - Se redes não autorizadas estiverem conversando com um gateway de Aplicativo ou Load Balancer, você poderá corrigi-lo, configurando as regras NSG para bloquear as redes não autorizadas. 

    ![subnet-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows.png)

### <a name="view-ports-and-virtual-machines-receiving-traffic-from-the-internet"></a>Exibir as portas e máquinas virtuais que recebem tráfego da internet

**Procurar**

- Quais portas abertas estão conversando pela internet?
  - Se portas inesperadas encontram-se abertas, você poderá corrigir sua configuração:

    ![Painel mostrando portas que recebem e enviam tráfego para a internet](./media/traffic-analytics/dashboard-showcasing-ports-receiving-and-sending-traffic-to-the-internet.png)

    ![Detalhes dos hosts e portas de destino do Azure](./media/traffic-analytics/details-of-azure-destination-ports-and-hosts.png)

**Procurar**

Você tem o tráfego mal-intencionado no seu ambiente? Onde ele é originado? Para onde ele é destinado?

![Detalhes de fluxos de tráfego mal-intencionado na pesquisa de log](./media/traffic-analytics/malicious-traffic-flows-detail-in-log-search.png)


### <a name="visualize-the-trends-in-nsgnsg-rules-hits"></a>Visualizar as tendências nas ocorrências de regras NSG/NSG

**Procurar**

- Quais regras NSG/NSG têm mais ocorrências no gráfico comparativo com distribuição de fluxos?
- Quais são os principais pares de conversas de origem e destino por regras NSG/NSG?

    ![Painel mostrando estatísticas de acessos de NSG](./media/traffic-analytics/dashboard-showcasing-nsg-hits-statistics.png)

- As imagens a seguir mostram tendências de tempo para acessos de regras NSG e detalhes de fluxo de origem-destino para um grupo de segurança de rede:

  - Detecte rapidamente quais regras NSGs e NSG estão percorrendo fluxos mal-intencionados e quais são os principais endereços IP mal-intencionados que acessam o ambiente de nuvem
  - Identificar quais regras NSG/NSG estão permitindo/bloqueando tráfego de rede significativo
  - Selecione os filtros principais para inspeção granular de regras NSG ou NSG

    ![Apresentação de tendências de tempo para acessos de regras NSG e as principais regras NSG](./media/traffic-analytics/showcasing-time-trending-for-nsg-rule-hits-and-top-nsg-rules.png)

    ![Detalhes de estatísticas das principais regras NSG na pesquisa de log](./media/traffic-analytics/top-nsg-rules-statistics-details-in-log-search.png)

## <a name="frequently-asked-questions"></a>Perguntas frequentes

Para obter respostas para perguntas frequentes, confira [Perguntas Frequentes sobre Análise de Tráfego](traffic-analytics-faq.md).

## <a name="next-steps"></a>Próximas etapas

- Para saber como habilitar os logs de fluxo, consulte [Habilitar o log de fluxo NSG](network-watcher-nsg-flow-logging-portal.md).
- Para entender o esquema e os detalhes de processamento de Análise de Tráfego, consulte [esquema de análise de tráfego](traffic-analytics-schema.md).
