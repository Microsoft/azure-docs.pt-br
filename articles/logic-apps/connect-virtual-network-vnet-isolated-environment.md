---
title: Conectar-se a redes virtuais do Azure com um ISE
description: Criar um ISE (ambiente de serviço de integração) que pode acessar redes virtuais do Azure virtual (VNETs) por meio dos Aplicativos Lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: 315de18539bf083515658b40fa70f3c214d7c909
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97739732"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Conectar redes virtuais do Azure a partir dos Aplicativos Lógicos do Azure, usando um ISE (Ambiente de Serviço de Integração)

Para cenários em que seus aplicativos lógicos e contas de integração precisam de acesso a uma [Rede virtual do Azure](../virtual-network/virtual-networks-overview.md), crie um [*ISE* (Ambiente de Serviço de Integração)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Um ISE é um ambiente dedicado que usa armazenamento dedicado e outros recursos que são mantidos separadamente do serviço "global" multilocatário dos Aplicativos Lógicos. Essa separação também reduz os impactos que outros locatários do Azure podem ter no desempenho de seus aplicativos. Um ISE também fornece a você seus endereços IP estáticos. Esses endereços IP são separados dos endereços IP estáticos compartilhados pelos aplicativos lógicos no serviço público e multilocatário.

Quando você cria um ISE, o Azure o *injeta* na rede virtual do Azure, que, em seguida, implanta o serviço de Aplicativos Lógicos em sua rede virtual. Ao criar aplicativos lógicos e contas de integração, você seleciona o ISE como sua localização. Sua conta de integração ou de aplicativo lógico, em seguida, pode acessar diretamente os recursos, como máquinas virtuais (VMs), servidores, sistemas e serviços em sua rede virtual.

![Selecionar o ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> Para que os aplicativos lógicos e as contas de integração funcionem juntos em um ISE, ambos devem usar o *mesmo ISE* como local.

Um ISE aumentou os limites de duração da execução, retenção de armazenamento, taxa de transferência, tempos limite de solicitação e resposta HTTP, tamanhos de mensagem e solicitações de conector personalizado. Para obter mais informações, confira [Limites e configuração para Aplicativos Lógicos do Azure](../logic-apps/logic-apps-limits-and-config.md). Para saber mais sobre ISEs, confira [Acesso aos recursos da Rede Virtual do Azure dos Aplicativos Lógicos do Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

Este artigo mostra como concluir estas tarefas usando o portal do Azure:

* Habilitar o acesso para seu ISE.
* Criar seu ISE.
* Adicionar capacidade extra ao ISE.

Você também pode criar um ISE usando o [exemplo de modelo de início rápido do Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) ou a API REST de Aplicativos Lógicos, incluindo a configuração de chaves gerenciadas pelo cliente:

* [Criar um ambiente do serviço de integração (ISE) usando a API REST de Aplicativos Lógicos](../logic-apps/create-integration-service-environment-rest-api.md)
* [Configurar chaves gerenciadas pelo cliente para criptografar dados em repouso para ISEs](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

  > [!IMPORTANT]
  > Os aplicativos lógicos, os gatilhos internos, as ações internas e os conectores executados no ISE usam um plano de preços diferente do plano baseado em consumo. Para saber como funcionam o preço e a cobrança nos ISEs, confira o [Modelo de preços de Aplicativos Lógicos](../logic-apps/logic-apps-pricing.md#fixed-pricing). Para obter os valores, consulte [Preços dos Aplicativos Lógicos](../logic-apps/logic-apps-pricing.md).

* Uma [rede virtual do Azure](../virtual-network/virtual-networks-overview.md) que tem quatro sub-redes *vazias* , que são necessárias para criar e implantar recursos em seu Ise e são usadas por esses componentes internos e ocultos:

  * Computação de aplicativos lógicos
  * Ambiente do Serviço de Aplicativo internos (conectores)
  * Gerenciamento de API interno (conectores)
  * Redis interno para cache e desempenho
  
  Você pode criar as sub-redes antecipadamente ou ao criar o ISE para que possa criar as sub-redes ao mesmo tempo. No entanto, antes de criar suas sub-redes, certifique-se de examinar os [requisitos de sub-rede](#create-subnet).

  * Verifique se a sua rede virtual [habilita o acesso ao ISE](#enable-access) para que o ISE possa funcionar corretamente e permanecer acessível.

  * Se você usar ou desejar usar o [ExpressRoute](../expressroute/expressroute-introduction.md) juntamente com o [túnel forçado](../firewall/forced-tunneling.md), deverá [criar uma tabela de rotas](../virtual-network/manage-route-table.md) com a rota específica a seguir e vincular a tabela de rotas a cada sub-rede usada pelo ISE:

    **Nome**: <*nome da rota*><br>
    **Prefixo de endereço**: 0.0.0.0/0<br>
    **Próximo salto**: Internet
    
    Essa tabela de rotas específica é necessária para que os componentes dos aplicativos lógicos possam se comunicar com outros serviços dependentes do Azure, como o armazenamento do Azure e o BD SQL do Azure. Para obter mais informações sobre essa rota, consulte [prefixo de endereço 0.0.0.0/0](../virtual-network/virtual-networks-udr-overview.md#default-route). Se você não usar o túnel forçado com o ExpressRoute, não precisará dessa tabela de rotas específica.
    
    O ExpressRoute permite que você estenda suas redes locais para a nuvem da Microsoft e conecte-se aos serviços de nuvem da Microsoft por meio de uma conexão privada que é facilitada pelo provedor de conectividade. Especificamente, o ExpressRoute é uma rede virtual privada que roteia o tráfego por uma rede privada, em vez de através da Internet pública. Seus aplicativos lógicos podem se conectar a recursos locais que estão na mesma rede virtual quando se conectam por meio do ExpressRoute ou de uma rede virtual privada.
   
  * Se você usar uma [NVA (solução de virtualização de rede)](../virtual-network/virtual-networks-udr-overview.md#user-defined), certifique-se de não habilitar o encerramento de TLS/SSL ou alterar o tráfego de TLS/SSL de saída. Além disso, certifique-se de não habilitar a inspeção para o tráfego originado da sub-rede do ISE. Para obter mais informações, consulte [Roteamento de tráfego de rede virtual](../virtual-network/virtual-networks-udr-overview.md).

  * Se quiser usar servidores DNS personalizados para sua rede virtual do Azure, [configure esses servidores seguindo estas etapas](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) antes de implantar o ISE na rede virtual. Para saber mais informações sobre como gerenciar as configurações de servidor DNS, confira [Criar, alterar ou excluir uma rede virtual](../virtual-network/manage-virtual-network.md#change-dns-servers).

    > [!NOTE]
    > Se você alterar o servidor DNS ou as configurações do servidor DNS, deverá reiniciar o ISE para que o ISE possa pegar essas alterações. Para obter mais informações, confira [Reiniciar o ISE](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE).

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>Habilitar o acesso para o ISE

Quando você usa um ISE com uma rede virtual do Azure, um problema comum de configuração é ter uma ou mais portas bloqueadas. Os conectores que você usa para criar conexões entre seu ISE e os sistemas de destino também poderão ter requisitos de porta próprios. Quando você se comunica com um sistema FTP usando o conector FTP, a porta usada nesse sistema FTP precisa estar disponível, por exemplo, a porta 21 para enviar comandos.

Para garantir que o ISE esteja acessível e que os aplicativos lógicos nesse ISE possam se comunicar em cada sub-rede em sua rede virtual, [abra as portas descritas nesta tabela em cada sub-rede](#network-ports-for-ise). Se alguma porta necessária estiver indisponível, o ISE não funcionará corretamente.

* Se você tiver várias instâncias do ISE que precisam de acesso a outros pontos de extremidade com restrições de IP, implante um [Firewall do Azure](../firewall/overview.md) ou uma [solução de virtualização de rede](../virtual-network/virtual-networks-overview.md#filter-network-traffic) em sua rede virtual e roteie o tráfego de saída por meio do firewall ou da solução de virtualização de rede. Você pode [configurar um endereço IP único, de saída, público, estático e previsível](connect-virtual-network-vnet-set-up-single-ip-address.md) que todas as instâncias do ISE em sua rede virtual possam usar para se comunicar com os sistemas de destino. Dessa forma, você não precisa configurar aberturas adicionais do firewall nesses sistemas de destino para cada ISE.

   > [!NOTE]
   > Você poderá usar essa abordagem para um único ISE quando seu cenário exigir a limitação do número de endereços IP que precisam de acesso. Considere se os custos adicionais para o firewall ou solução de virtualização de rede fazem sentido para o seu cenário. Saiba mais sobre os [preços do Firewall do Azure](https://azure.microsoft.com/pricing/details/azure-firewall/).

* Se você tiver criado uma rede virtual do Azure e sub-redes sem nenhuma restrição, não precisará configurar [NSGs (grupos de segurança de rede)](../virtual-network/network-security-groups-overview.md#network-security-groups) em sua rede virtual para controlar o tráfego entre sub-redes.

* Para uma rede virtual existente, você *tem a opção* de configurar [NSGs (grupos de segurança de rede)](../virtual-network/network-security-groups-overview.md#network-security-groups) para [filtrar o tráfego de rede entre sub-redes](../virtual-network/tutorial-filter-network-traffic.md). Se você quiser seguir essa rota ou se já estiver usando NSGs, [abra as portas descritas nesta tabela](#network-ports-for-ise) para esses NSGs.

  Ao configurar [regras de segurança de NSG](../virtual-network/network-security-groups-overview.md#security-rules), você precisará usar *ambos* os protocolos **TCP** e **UDP** ou poderá selecionar **Qualquer um** em vez disso, para não precisar criar regras separadas para cada protocolo. As regras de segurança de NSG descrevem as portas que você deve abrir para os endereços IP que precisam de acesso a essas portas. Todos os firewalls, roteadores ou outros itens existentes entre esses pontos de extremidade também devem manter essas portas acessíveis a esses endereços IP.

* Se você configurar o túnel forçado por meio do firewall para redirecionar o tráfego de entrada na Internet, examine os [requisitos de túnel forçado adicionais](#forced-tunneling).

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>Portas de rede usadas pelo ISE

Esta tabela descreve as portas que o ISE exige que estejam acessíveis e a finalidade dessas portas. Para ajudar a reduzir a complexidade quando você configura regras de segurança, a tabela usa [marcas de serviço](../virtual-network/service-tags-overview.md) que representam grupos de prefixos de endereço IP para um serviço específico do Azure. Onde indicado, o *ISE interno* e o *ISE externo* referem-se ao [ponto de extremidade de acesso selecionado durante a criação do ISE](connect-virtual-network-vnet-isolated-environment.md#create-environment). Para obter mais informações, confira [Acesso do ponto de extremidade](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access).

> [!IMPORTANT]
> Para todas as regras, defina as portas de origem como `*` porque elas são efêmeras.

#### <a name="inbound-security-rules"></a>Regras de segurança de entrada

| Finalidade | Endereços IP ou marca de serviço de origem | Portas de origem | Endereços IP ou marca de serviço de destino | Portas de destino | Observações |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| Comunicação entre sub-redes na rede virtual | Espaço de endereço da rede virtual com sub-redes do ISE | * | Espaço de endereço da rede virtual com sub-redes do ISE | * | Necessário para que o tráfego flua *entre* as sub-redes de sua rede virtual. <p><p>**Importante**: Para que o tráfego flua entre os *componentes* em cada sub-rede, abra todas as portas em cada sub-rede. |
| Ambos: <p>Comunicação com o aplicativo lógico <p><p>Executa o histórico do aplicativo lógico| ISE interno: <br>**VirtualNetwork** <p><p>ISE externo: **Internet** ou confira as **Notas** | * | **VirtualNetwork** | 443 | Em vez de usar a marca de serviço de **Internet**, você pode especificar o endereço IP de origem para estes itens: <p><p>– O computador ou serviço que chama os gatilhos de solicitação ou webhooks em seu aplicativo lógico <p>– O computador ou serviço do qual você deseja acessar o histórico de execuções do aplicativo lógico <p><p>**Importante**: Fechar ou bloquear essa porta impede chamadas para aplicativos lógicos que têm gatilhos de solicitação ou webhooks. Você também é impedido de acessar entradas e saídas para cada etapa no histórico de execuções. No entanto, você não é impedido de acessar o histórico de execuções do aplicativo lógico.|
| Designer de Aplicativos Lógicos – propriedades dinâmicas | **LogicAppsManagement** | * | **VirtualNetwork** | 454 | As solicitações são provenientes dos [endereços IP de entrada](../logic-apps/logic-apps-limits-and-config.md#inbound) do ponto de extremidade de acesso dos Aplicativos Lógicos para essa região. |
| Implantação do conector | **AzureConnectors** | * | **VirtualNetwork** | 454 | Necessário para implantar e atualizar conectores. Fechar ou bloquear essa porta causa falha nas implantações do ISE e impedem atualizações e correções do conector. |
| Verificação da integridade da rede | **LogicApps** | * | **VirtualNetwork** | 454 | As solicitações são provenientes dos [endereços IP de entrada](../logic-apps/logic-apps-limits-and-config.md#inbound) e dos [endereços IP de saída](../logic-apps/logic-apps-limits-and-config.md#outbound) do ponto de extremidade de acesso dos Aplicativos Lógicos para essa região. |
| Dependência de Gerenciamento de Serviço de Aplicativo | **AppServiceManagement** | * | **VirtualNetwork** | 454, 455 ||
| Comunicação do Gerenciador de Tráfego do Azure | **AzureTrafficManager** | * | **VirtualNetwork** | ISE interno: 454 <p><p>ISE externo: 443 ||
| Ambos: <p>Implantação de política do conector <p>Gerenciamento de API - ponto de extremidade de gerenciamento | **APIManagement** | * | **VirtualNetwork** | 3443 | Na implantação da política de conector, o acesso à porta é necessário para implantar e atualizar conectores. Fechar ou bloquear essa porta causa falha nas implantações do ISE e impedem atualizações e correções do conector. |
| Acessar Instâncias do Cache do Azure para Redis entre Instâncias de Função | **VirtualNetwork** | * | **VirtualNetwork** | 6379 – 6383, confira também **Notas**| Para que o ISE funcione com o Cache do Azure para Redis, você deverá abrir essas [portas de entrada e saída descritas pelas perguntas frequentes do Cache do Azure para o Redis](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
|||||||

#### <a name="outbound-security-rules"></a>Regras de segurança de saída

| Finalidade | Endereços IP ou marca de serviço de origem | Portas de origem | Endereços IP ou marca de serviço de destino | Portas de destino | Observações |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| Comunicação entre sub-redes na rede virtual | Espaço de endereço da rede virtual com sub-redes do ISE | * | Espaço de endereço da rede virtual com sub-redes do ISE | * | Necessário para que o tráfego flua *entre* as sub-redes de sua rede virtual. <p><p>**Importante**: Para que o tráfego flua entre os *componentes* em cada sub-rede, abra todas as portas em cada sub-rede. |
| Comunicação a partir do aplicativo lógico | **VirtualNetwork** | * | Varia com base no destino | 80, 443 | O destino varia de acordo com os pontos de extremidade do serviço externo com o qual seu aplicativo lógico precisa se comunicar. |
| Azure Active Directory | **VirtualNetwork** | * | **AzureActiveDirectory** | 80, 443 ||
| Dependência de Armazenamento do Azure | **VirtualNetwork** | * | **Storage** | 80, 443, 445 ||
| Gerenciamento de Conexão | **VirtualNetwork** | * | **AppService** | 443 ||
| Publicar métricas e logs de diagnóstico | **VirtualNetwork** | * | **AzureMonitor** | 443 ||
| Dependência SQL Azure | **VirtualNetwork** | * | **SQL** | 1433 ||
| Azure Resource Health | **VirtualNetwork** | * | **AzureMonitor** | 1886 | Necessário para publicar o status da integridade no Resource Health. |
| Dependência do Log para agente de monitoramento e política do Hub de Eventos | **VirtualNetwork** | * | **EventHub** | 5672 ||
| Acessar Instâncias do Cache do Azure para Redis entre Instâncias de Função | **VirtualNetwork** | * | **VirtualNetwork** | 6379 – 6383, confira também **Notas**| Para que o ISE funcione com o Cache do Azure para Redis, você deverá abrir estas [portas de entrada e saída descritas pelas perguntas frequentes do Cache do Azure para o Redis](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
| Resolução de nomes DNS | **VirtualNetwork** | * | Endereços IP para qualquer servidor de DNS (sistema de nomes de domínio) personalizado em sua rede virtual | 53 | Necessário somente quando você usa servidores DNS personalizados em sua rede virtual |
|||||||

Além disso, você precisa adicionar regras de saída para [ambiente do serviço de aplicativo (ase)](../app-service/environment/intro.md):

* Se você usar o Firewall do Azure, precisará configurar o firewall com a [marca de FQDN (nome de domínio totalmente qualificado](../firewall/fqdn-tags.md#current-fqdn-tags)) do ambiente do serviço de aplicativo (ase), que permite o acesso de saída ao tráfego da plataforma ASE.

* Se você usar um dispositivo de firewall diferente do firewall do Azure, precisará configurar o firewall com *todas* as regras listadas nas [dependências de integração do firewall](../app-service/environment/firewall-integration.md#dependencies) necessárias para ambiente do serviço de aplicativo.

<a name="forced-tunneling"></a>

#### <a name="forced-tunneling-requirements"></a>Requisitos de túnel forçado

Se você configurar ou usar o [túnel forçado](../firewall/forced-tunneling.md) por meio do firewall, precisará permitir dependências externas adicionais para o ISE. O túnel forçado permite redirecionar o tráfego de entrada pela Internet para um próximo salto designado, como sua VPN (rede virtual privada) ou para um dispositivo virtual, em vez de para a Internet para que você possa inspecionar e auditar o tráfego de rede de saída.

Se você não permitir o acesso a essas dependências, a implantação do ISE falhará e o ISE implantado deixará de funcionar.

* rotas definidas pelo usuário

  Para evitar o roteamento assimétrico, você deve definir uma rota para cada endereço IP listado abaixo com a **Internet** como o próximo salto.
  
  * [Endereços de gerenciamento de Ambiente de Serviço de Aplicativo](../app-service/environment/management-addresses.md)
  * [Endereços IP do Azure para conectores na região do ISE, disponíveis neste arquivo de download](https://www.microsoft.com/download/details.aspx?id=56519)
  * [Endereços de gerenciamento do Gerenciador de tráfego do Azure](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json)
  * [Endereços de entrada e saída dos aplicativos lógicos para a região do ISE](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses-and-service-tags)
  * [Endereços IP do Azure para conectores na região do ISE, que estão neste arquivo de download](https://www.microsoft.com/download/details.aspx?id=56519)

* Pontos de extremidade de serviço

  Você precisa habilitar pontos de extremidade de serviço para SQL do Azure, armazenamento, barramento de serviço, keyvault e hubs de eventos, pois não é possível enviar tráfego por meio de um firewall para esses serviços.

*  Outras dependências de entrada e de saída

   O firewall *deve* permitir as seguintes dependências de entrada e saída:
   
   * [Dependências do serviço Azure App](../app-service/environment/firewall-integration.md#deploying-your-ase-behind-a-firewall)
   * [Dependências do serviço de cache do Azure](../azure-cache-for-redis/cache-how-to-premium-vnet.md#what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-virtual-networks)
   * [Dependências do gerenciamento de API do Azure](../api-management/api-management-using-with-vnet.md#-common-network-configuration-issues)

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Criar seu ISE

1. No [portal do Azure](https://portal.azure.com), na caixa principal de pesquisa, digite `integration service environments` como filtro e selecione **Ambientes do Serviço de Integração**.

   ![Localize e selecione "Ambientes de Serviço de Integração"](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. No painel **Ambiente de Serviço de Integração**, selecione **Adicionar**.

   ![Selecione "Adicionar" para criar o ambiente do serviço de integração](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. Forneça esses detalhes para seu ambiente e, em seguida, selecione **Revisar + criar**, por exemplo:

   ![Fornecer detalhes do ambiente](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Assinatura** | Sim | <*Azure-subscription-name*> | A assinatura do Azure a ser usada para o ambiente |
   | **Grupo de recursos** | Sim | <*Azure-resource-group-name*> | Um grupo de recursos novo ou existente do Azure no qual deseja criar seu ambiente |
   | **Nome do ambiente de serviço de integração** | Sim | <*environment-name*> | O nome do ISE, que pode conter apenas letras, números, hifens (`-`), sublinhados (`_`) e pontos (`.`). |
   | **Localidade** | Sim | <*Azure-datacenter-region*> | A região do datacenter do Azure na qual o ambiente será implantado |
   | **SKU** | Sim | **Premium** ou **Desenvolvedor (sem SLA)** | A SKU do ISE a ser criada e usada. Para diferenças entre essas SKUs, confira [SKUs do ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level). <p><p>**Importante**: Essa opção está disponível somente na criação do ISE e não pode ser alterada posteriormente. |
   | **Capacidade adicional** | Premium: <br>Sim <p><p>Desenvolvedor: <br>Não aplicável | Premium: <br>0 a 10 <p><p>Desenvolvedor: <br>Não aplicável | O número de unidades de processamento adicionais a serem usadas no recurso do ISE. Para adicionar capacidade após a criação, confira [Adicionar capacidade do ISE](../logic-apps/ise-manage-integration-service-environment.md#add-capacity). |
   | **Ponto de extremidade de acesso** | Sim | **Interno** ou **Externo** | O tipo dos pontos de extremidade de acesso a serem usados no ISE. Esses pontos de extremidade determinam se os gatilhos de solicitação ou webhook em Aplicativos Lógicos no ISE podem receber chamadas de fora de sua rede virtual. <p><p>Por exemplo, se você quiser usar os seguintes gatilhos baseados em webhook, certifique-se de selecionar **externo**: <p><p>-DevOps do Azure <br>-Grade de eventos do Azure <br>-Common Data Service <br>-Office 365 <br>-SAP (versão do ISE) <p><p>Sua seleção também afeta a maneira como você pode exibir e acessar entradas e saídas em seu histórico de execuções de aplicativo lógico. Para obter mais informações, confira [Acesso do ponto de extremidade do ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). <p><p>**Importante**: Você pode selecionar o ponto de extremidade de acesso somente durante a criação do ISE e não pode alterar essa opção posteriormente. |
   | **Rede virtual** | Sim | <*Azure-virtual-network-name*> | A rede virtual do Azure na qual você deseja injetar seu ambiente para que os aplicativos lógicos no ambiente possam acessar sua rede virtual. Se não tiver uma rede, [primeiro crie uma rede virtual do Azure](../virtual-network/quick-create-portal.md). <p><p>**Importante**: Você pode executar essa injeção *apenas* quando cria seu ISE. |
   | **Sub-redes** | Sim | <*subnet-resource-list*> | Um ISE requer quatro sub-redes *vazias* , que são necessárias para criar e implantar recursos no ISE e são usadas por componentes de aplicativos lógicos internos, como conectores e cache para desempenho. <p>**Importante**: Certifique-se [de examinar os requisitos de sub-rede antes de continuar com estas etapas para criar suas sub-redes](#create-subnet). |
   |||||

   <a name="create-subnet"></a>

   **Criar sub-redes**

   O ISE requer quatro sub-redes *vazias* , que são necessárias para criar e implantar recursos no ISE e são usadas por componentes de aplicativos lógicos internos, como conectores e cache para desempenho. Você *não pode* alterar esses endereços de sub-rede depois de criar seu ambiente. Se você criar e implantar o ISE por meio do portal do Azure, certifique-se de não delegar essas sub-redes a nenhum serviço do Azure. No entanto, se você criar e implantar o ISE por meio da API REST, Azure PowerShell ou um modelo de Azure Resource Manager, será necessário [delegar](../virtual-network/manage-subnet-delegation.md) uma sub-rede vazia para o `Microsoft.integrationServiceEnvironment` . Para obter mais informações, consulte [Adicionar uma delegação de sub-rede](../virtual-network/manage-subnet-delegation.md).

   Cada sub-rede precisa atender a estes requisitos:

   * Usa um nome que começa com um caractere alfabético ou um sublinhado (sem números) e não usa estes caracteres:,,,,, `<` `>` `%` `&` `\\` `?` , `/` .

   * Usa o [formato CIDR (roteamento sem classe Inter-Domain)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).
   
     > [!IMPORTANT]
     >
     > Não use os espaços de endereço IP a seguir para sua rede virtual ou sub-redes porque eles não podem ser resolvidos pelos aplicativos lógicos do Azure:<p>
     > 
     > * 0.0.0.0/8
     > * 100.64.0.0/10
     > * 127.0.0.0/8
     > * 168.63.129.16/32
     > * 169.254.169.254/32

   * Usa um `/27` no espaço de endereço porque cada sub-rede requer 32 endereços. Por exemplo, `10.0.0.0/27` tem 32 endereços porque 2<sup>(32-27)</sup> é 2<sup>5</sup> ou 32. Mais endereços não fornecerão benefícios adicionais. Para saber mais sobre como calcular endereços, confira [blocos CIDR IPv4](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Se você usar o [ExpressRoute](../expressroute/expressroute-introduction.md), deverá [criar uma tabela de rotas](../virtual-network/manage-route-table.md) que tenha a seguinte rota e vincular essa tabela a cada sub-rede usada pelo ISE:

     **Nome**: <*nome da rota*><br>
     **Prefixo de endereço**: 0.0.0.0/0<br>
     **Próximo salto**: Internet

   1. Na lista **Sub-redes**, selecione **Gerenciar configuração de sub-rede**.

      ![Gerenciar configuração de sub-rede](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. No painel **Sub-redes**, selecione **Sub-rede**.

      ![Adicionar quatro sub-redes vazias](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. No painel **Adicionar sub-rede**, forneça estas informações.

      * **Name**: o nome da sub-rede
      * **Intervalo de endereços (bloco CIDR)** : o intervalo da sub-rede em sua rede virtual e no formato CIDR

      ![Adicionar detalhes da sub-rede](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. Quando terminar, selecione **OK**.

   1. Repita essas etapas para mais três sub-redes.

      > [!NOTE]
      > Se as sub-redes que você tentar criar não forem válidas, o portal do Azure mostrará uma mensagem, mas não bloqueará seu progresso.

   Para saber mais sobre como criar sub-redes, confira [Adicionar uma sub-rede de rede virtual](../virtual-network/virtual-network-manage-subnet.md).

1. Depois que o Azure validar com êxito suas informações do ISE, selecione **Criar**, por exemplo:

   ![Após a validação bem-sucedida, selecione "Criar"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   O Azure inicia a implantação de seu ambiente, que geralmente leva cerca de duas horas para ser concluída. Em alguns casos, a implantação pode levar até quatro horas. Para verificar o status da implantação, na barra de ferramentas do Azure, selecione o ícone de notificações, o que abre o painel de notificações.

   ![Verifique o status da implantação](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Se a implantação for concluída com êxito, o Azure mostrará esta notificação:

   ![Implantação bem-sucedida](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   Caso contrário, siga as instruções do portal do Azure para solucionar problemas de implantação.

   > [!NOTE]
   > Se a implantação falhar ou você excluir o ISE, o Azure poderá levar até uma hora ou possivelmente mais em casos raros, antes de liberar suas sub-redes. Portanto, talvez seja necessário aguardar antes de poder reutilizar essas sub-redes em outro ISE.
   >
   > Se você excluir sua rede virtual, o Azure geralmente levará até duas horas antes de lançar suas sub-redes, mas essa operação poderá levar mais tempo. 
   > Ao excluir redes virtuais, verifique se algum recurso ainda está conectado. 
   > Confira [Excluir rede virtual](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Para exibir seu ambiente, selecione **Ir para o recurso** se o Azure não for automaticamente para seu ambiente após a conclusão da implantação.

1. Para um ISE com acesso de ponto de extremidade *externo* , você precisa criar um grupo de segurança de rede, se ainda não tiver um, e adicionar uma regra de segurança de entrada para permitir o tráfego de endereços IP de saída do conector gerenciado. Para configurar essa regra, siga estas etapas:

   1. No menu do ISE, em **configurações**, selecione **Propriedades**.

   1. Em **endereços IP de saída do conector**, copie os intervalos de endereços IP públicos, que também aparecem neste artigo, [limites e endereços IP de saída de configuração](../logic-apps/logic-apps-limits-and-config.md#outbound).

   1. Crie um grupo de segurança de rede, se você ainda não tiver um.
   
   1. Com base nas informações a seguir, adicione uma regra de segurança de entrada para os endereços IP de saída públicos que você copiou. Para obter mais informações, consulte [tutorial: filtrar o tráfego de rede com um grupo de segurança de rede usando o portal do Azure](../virtual-network/tutorial-filter-network-traffic.md#create-a-network-security-group).

      | Finalidade | Endereços IP ou marca de serviço de origem | Portas de origem | Endereços IP ou marca de serviço de destino | Portas de destino | Observações |
      |---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
      | Permitir o tráfego de endereços IP de saída do conector | <*Connector-Public-Outbound-IP-addresses*> | * | Espaço de endereço da rede virtual com sub-redes do ISE | * | |
      |||||||

1. Para verificar a integridade da rede do ISE, confira [Gerenciar seu ambiente do serviço de integração](../logic-apps/ise-manage-integration-service-environment.md#check-network-health).

   > [!CAUTION]
   > Se a rede do ISE se tornar não íntegra, o Ambiente do Serviço de Aplicativo interno (ASE) que é usado pelo ISE também pode se tornar não íntegro. Se o ASE não estiver íntegro por mais de sete dias, o ASE será suspenso. Para resolver esse Estado, verifique sua configuração de rede virtual. Resolva os problemas encontrados e reinicie o ISE. Caso contrário, após 90 dias, o ASE suspenso será excluído e o ISE se tornará inutilizável. Portanto, certifique-se de manter seu ISE íntegro para permitir o tráfego necessário.
   > 
   > Para saber mais, consulte esses tópicos:
   >
   > * [Visão geral de diagnóstico do Serviço de Aplicativo do Azure](../app-service/overview-diagnostics.md)
   > * [Log de mensagens para Ambiente do Serviço de Aplicativo do Azure](../app-service/environment/using-an-ase.md#logging)

1. Para começar a criar aplicativos lógicos e outros artefatos no ISE, confira [Adicionar recursos aos ambientes de serviço de integração](../logic-apps/add-artifacts-integration-service-environment-ise.md).

   > [!IMPORTANT]
   > Depois de criar o ISE, conectores do ISE gerenciados ficam disponíveis para uso, mas eles não aparecem automaticamente no seletor de conector no designer de aplicativo lógico. Antes de poder usar esses conectores do ISE, você precisa [Adicionar e implantar manualmente esses conectores no ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment) para que eles apareçam no designer do aplicativo lógico.

## <a name="next-steps"></a>Próximas etapas

* [Adicionar recursos a ambientes de serviço de integração](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Gerenciar ambientes de serviço de integração](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* Saiba mais sobre a [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md)
* Saiba mais sobre a [integração de rede virtual para os serviços do Azure](../virtual-network/virtual-network-for-azure-services.md)
