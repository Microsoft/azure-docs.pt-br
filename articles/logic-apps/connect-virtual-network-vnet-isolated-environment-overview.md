---
title: Acesso às redes virtuais do Azure
description: Visão geral sobre como os ambientes de serviço de integração (ISEs) ajudam os aplicativos lógicos a acessar redes virtuais do Azure (VNETs)
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 01/11/2021
ms.openlocfilehash: 7bb9c8552f673587891fde12e25d4fb899726c22
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108561"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Acessar recursos de rede virtual do Azure a partir dos Aplicativos Lógicos do Azure usando ISEs (Ambientes de Serviço de Integração)

Às vezes, seus aplicativos lógicos precisam acessar recursos protegidos, como VMs (máquinas virtuais) e outros sistemas ou serviços, que estão dentro ou conectados a uma [rede virtual do Azure](../virtual-network/virtual-networks-overview.md). Para configurar esse acesso, você pode [criar um *ambiente do serviço de integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md). Um ISE é uma instância do serviço de aplicativos lógicos que usa recursos dedicados e é executado separadamente do serviço de aplicativos lógicos multilocatários "globais". Os dados em um ISE permanecem na [mesma região em que você cria e implanta esse ISE](https://azure.microsoft.com/global-infrastructure/data-residency/).

Por exemplo, algumas redes virtuais do Azure usam pontos de extremidade privados, que podem ser configurados por meio [do link privado do Azure](../private-link/private-link-overview.md), para fornecer acesso aos serviços de PaaS do Azure, como o armazenamento do azure, Azure Cosmos DB ou banco de dados SQL do Azure, serviços de parceiro ou serviços de cliente hospedados no Azure. Se seus aplicativos lógicos precisarem de acesso a redes virtuais que usam pontos de extremidade privados, você deverá criar, implantar e executar esses aplicativos lógicos dentro de um ISE.

Quando você cria um ISE, o Azure *injeta* ou implanta esse ISE em sua rede virtual do Azure. Em seguida, é possível usar esse ISE como o local para os aplicativos lógicos e as contas de integração que precisam de acesso.

![Selecionar o ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Esta visão geral fornece mais informações sobre [por que você desejaria usar um ISE](#benefits), as [diferenças entre o serviço de aplicativos lógicos dedicado e multilocatário](#difference)e como você pode acessar diretamente os recursos que estão dentro ou conectados à sua rede virtual do Azure.

<a name="benefits"></a>

## <a name="why-use-an-ise"></a>Por que usar um ISE?

A execução de aplicativos lógicos em sua própria instância dedicada separada ajuda a reduzir o impacto que outros locatários do Azure podem ter sobre o desempenho de seus aplicativos, também conhecido como o [efeito "vizinhos com ruído"](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). Um ISE proporciona estes benefícios:

* Acesso direto a recursos que estão dentro ou conectados à sua rede virtual

  Os aplicativos lógicos que você cria e executa em um ISE podem usar [conectores projetados especificamente que são executados no ISE](../connectors/apis-list.md#ise-connectors). Se existir um conector ISE para um sistema local ou fonte de dados, você poderá se conectar diretamente sem precisar usar o [Gateway de dados local](../logic-apps/logic-apps-gateway-connection.md). Para obter mais informações, consulte [dedicado versus multilocatário](#difference) e [acesso a sistemas locais](#on-premises) mais adiante neste tópico.

* Acesso contínuo a recursos que estão fora ou não conectados à sua rede virtual

  Os aplicativos lógicos criados e executados em um ISE ainda podem usar conectores que são executados no serviço de aplicativos lógicos multilocatários quando um conector específico do ISE não está disponível. Para obter mais informações, consulte [dedicado versus multilocatário](#difference).

* Seus próprios endereços IP estáticos, que são separados dos endereços IP estáticos compartilhados pelos aplicativos lógicos no serviço multilocatário. Você também pode configurar um único endereço IP de saída público, estático e previsível para se comunicar com os sistemas de destino. Dessa forma, você não precisa configurar aberturas adicionais do firewall nesses sistemas de destino para cada ISE.

* Aumento dos limites de duração da execução, retenção de armazenamento, taxa de transferência, tempos limite de solicitação e resposta HTTP, tamanhos de mensagem e solicitações de conector personalizado. Para obter mais informações, confira [Limites e configuração para Aplicativos Lógicos do Azure](logic-apps-limits-and-config.md).

<a name="difference"></a>

## <a name="dedicated-versus-multi-tenant"></a>Dedicado versus multilocatário

Ao criar e executar aplicativos lógicos em um ISE, você obtém as mesmas experiências de usuário e recursos semelhantes que o serviço de aplicativos lógicos multilocatários. Você pode usar todos os mesmos gatilhos internos, ações e conectores gerenciados que estão disponíveis no serviço de aplicativos lógicos multilocatários. Alguns conectores gerenciados oferecem versões adicionais do ISE. A diferença entre conectores do ISE e conectores não ISE existe no local em que eles são executados e os rótulos que eles têm no designer de aplicativo lógico quando você trabalha em um ISE.

![Conectores com e sem rótulos em um ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* Gatilhos e ações internos, como HTTP, exibem o rótulo **principal** e são executados no mesmo ISE que seu aplicativo lógico.

* Os conectores gerenciados que exibem o rótulo do **ISE** são especialmente projetados para ISEs e *sempre são executados no mesmo ISE que seu aplicativo lógico*. Por exemplo, aqui estão alguns [conectores que oferecem versões do ISE](../connectors/apis-list.md#ise-connectors):<p>

  * Armazenamento de Blobs, Armazenamento de Arquivos e Armazenamento de Tabelas do Azure
  * Barramento de serviço do Azure, filas do Azure, hubs de eventos do Azure
  * Automação do Azure, Azure Key Vault, grade de eventos do Azure e logs de Azure Monitor
  * FTP, SFTP-SSH, sistema de arquivos e SMTP
  * SAP, IBM MQ, IBM DB2 e IBM 3270
  * SQL Server, Azure Synapse Analytics, Azure Cosmos DB
  * AS2, X12 e EDIFACT

  Com exceções raras, se um conector do ISE estiver disponível para um sistema local ou fonte de dados, você poderá se conectar diretamente sem usar o [Gateway de dados local](../logic-apps/logic-apps-gateway-connection.md). Para obter mais informações, consulte [acesso a sistemas locais](#on-premises) mais adiante neste tópico.

* Os conectores gerenciados que não exibem o rótulo do **ISE** continuam a funcionar para aplicativos lógicos dentro de um ISE. Esses conectores *sempre são executados no serviço de aplicativos lógicos multilocatários*, e não no ISE.

* Conectores personalizados que você cria *fora de um ISE*, independentemente de eles exigirem o [Gateway de dados local](../logic-apps/logic-apps-gateway-connection.md), continuam funcionando para aplicativos lógicos dentro de um ISE. No entanto, os conectores personalizados criados *dentro de um ISE* não funcionarão com o gateway de dados local. Para obter mais informações, consulte [acesso a sistemas locais](#on-premises).

<a name="on-premises"></a>

## <a name="access-to-on-premises-systems"></a>Acesso a sistemas locais

Os aplicativos lógicos que são executados dentro de um ISE podem acessar diretamente sistemas locais e fontes de dados que estão dentro ou conectados a uma rede virtual do Azure usando estes itens:<p>

* O gatilho ou ação HTTP, que exibe o rótulo **principal**

* O conector do **ISE** , se disponível, para um sistema local ou uma fonte de dados

  Se um conector do ISE estiver disponível, você poderá acessar diretamente o sistema ou a fonte de dados sem o [Gateway de dados local](../logic-apps/logic-apps-gateway-connection.md). No entanto, se você precisar acessar SQL Server de um ISE e usar a autenticação do Windows, deverá usar a versão sem ISE do conector e o gateway de dados local. A versão do ISE do conector não dá suporte à autenticação do Windows. Para obter mais informações, consulte [conectores do ISE](../connectors/apis-list.md#ise-connectors) e [Conecte-se de um ambiente do serviço de integração](../connectors/apis-list.md#integration-service-environment).

* Um conector personalizado

  * Conectores personalizados que você cria *fora de um ISE*, independentemente de eles exigirem o [Gateway de dados local](../logic-apps/logic-apps-gateway-connection.md), continuam funcionando para aplicativos lógicos dentro de um ISE.

  * Os conectores personalizados que você cria *dentro de um ISE* não funcionam com o gateway de dados local. No entanto, esses conectores podem acessar diretamente sistemas locais e fontes de dados que estão dentro ou conectadas à rede virtual que hospeda o ISE. Portanto, os aplicativos lógicos que estão dentro de um ISE geralmente não precisam do gateway de dados ao acessar esses recursos.

Para acessar sistemas locais e fontes de dados que não têm conectores do ISE, estão fora da sua rede virtual ou não estão conectados à sua rede virtual, você ainda precisa usar o gateway de dados local. Os aplicativos lógicos em um ISE podem continuar usando conectores que não têm o rótulo **principal** ou **ISE** . Esses conectores são executados no serviço de aplicativos lógicos multilocatários, em vez de em seu ISE. 

<a name="ise-level"></a>

## <a name="ise-skus"></a>SKUs do ISE

Ao criar o ISE, você pode selecionar a SKU do desenvolvedor ou a SKU Premium. Estas são as diferenças entre estas SKUs:

* **Desenvolvedor**

  Fornece um ISE de menor custo que você pode usar para experimentação, desenvolvimento e teste, mas não para teste de produção ou de desempenho. A SKU do desenvolvedor inclui gatilhos e ações internos, conectores padrão, conectores corporativos e uma única conta de integração de [camada gratuita](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) para um preço mensal fixo. No entanto, esse SKU não inclui nenhum SLA (contrato de nível de serviço), opções para escalar verticalmente a capacidade ou redundância durante a reciclagem, o que significa que você pode enfrentar atrasos ou tempo de inatividade.

* **Premium**

  Fornece um ISE que você pode usar para produção e inclui suporte a SLA, gatilhos e ações internos, conectores padrão, conectores corporativos, uma única conta de integração de [camada Standard](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) , opções para escalar verticalmente a capacidade e redundância durante a reciclagem de um preço mensal fixo.

> [!IMPORTANT]
> A opção SKU está disponível apenas na criação do ISE e não pode ser alterada posteriormente.

Para obter os valores, consulte [Preços dos Aplicativos Lógicos](https://azure.microsoft.com/pricing/details/logic-apps/). Para saber como funcionam o preço e a cobrança nos ISEs, consulte o [Modelo de preços dos Aplicativos Lógicos](../logic-apps/logic-apps-pricing.md#fixed-pricing).

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>Acesso ao ponto de extremidade do ISE

Ao criar o ISE, você pode optar por usar pontos de extremidade de acesso internos ou externos. Sua seleção determina se os gatilhos de solicitação ou webhook em aplicativos lógicos no ISE podem receber chamadas de fora de sua rede virtual. Esses pontos de extremidade também afetam a maneira como você pode acessar as entradas e saídas do histórico de execuções de seus aplicativos lógicos.

> [!IMPORTANT]
> Você pode selecionar o ponto de extremidade de acesso somente durante a criação do ISE e não pode alterar essa opção posteriormente.

* **Interno**: pontos de extremidade privados permitem chamadas para aplicativos lógicos no ISE, onde você pode exibir e acessar entradas e saídas do histórico de execuções de aplicativos lógicos *somente de dentro de sua rede virtual*.

  > [!IMPORTANT]
  > Se você precisar usar esses gatilhos baseados em webhook, use pontos de extremidade externos, *não* pontos de extremidade internos, ao criar o ISE:
  > 
  > * Azure DevOps
  > * Grade de Eventos do Azure
  > * Common Data Service
  > * Office 365
  > * SAP (versão do ISE)
  > 
  > Além disso, verifique se você tem conectividade de rede entre os pontos de extremidade privados e o computador do qual você deseja acessar o histórico de execução. Caso contrário, ao tentar exibir o histórico de execução do aplicativo lógico, você receberá um erro que diz "erro inesperado. Falha ao buscar ".
  >
  > ![Erro de ação de armazenamento do Azure resultante da incapacidade de enviar tráfego por meio do firewall](./media/connect-virtual-network-vnet-isolated-environment-overview/integration-service-environment-error.png)
  >
  > Por exemplo, o computador cliente pode existir dentro da rede virtual do ISE ou dentro de uma rede virtual conectada à rede virtual do ISE por meio de emparelhamento ou de uma rede virtual privada. 

* **Externo**: pontos de extremidade públicos permitem chamadas para aplicativos lógicos no ISE, onde você pode exibir e acessar entradas e saídas do histórico de execuções de aplicativos lógicos *de fora da sua rede virtual*. Se você usar NSGs (grupos de segurança de rede), verifique se eles estão configurados com regras de entrada para permitir o acesso às entradas e saídas do histórico de execuções. Para obter mais informações, consulte [habilitar o acesso para ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access).

Para determinar se o ISE usa um ponto de extremidade de acesso interno ou externo, no menu do ISE, em **configurações**, selecione **Propriedades** e localize a propriedade **ponto de extremidade de acesso** :

![Localizar ponto de extremidade de acesso do ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/find-ise-access-endpoint.png)

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Modelo de preços

Os aplicativos lógicos, os gatilhos internos, as ações internas e os conectores executados no ISE usam um plano de preços fixo que difere do plano de preços baseado em consumo. Para obter mais informações, consulte [modelo de preços de aplicativos lógicos](../logic-apps/logic-apps-pricing.md#fixed-pricing). Para obter os valores, consulte [Preços dos Aplicativos Lógicos](https://azure.microsoft.com/pricing/details/logic-apps/).

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Contas de integração com ISE

É possível usar contas de integração com aplicativos lógicos dentro de um ISE (ambiente de serviço de integração). No entanto, essas contas de integração devem usar o *mesmo ISE* que os aplicativos lógicos vinculados. Os aplicativos lógicos em um ISE podem fazer referência somente às contas de integração que estão no mesmo ISE. Ao criar uma conta de integração, é possível selecionar o ISE como local para a conta de integração. Para saber como o preço e a cobrança funcionam para contas de integração com um ISE, consulte o [modelo de preços dos aplicativos lógicos](../logic-apps/logic-apps-pricing.md#fixed-pricing). Para obter os valores, consulte [Preços dos Aplicativos Lógicos](https://azure.microsoft.com/pricing/details/logic-apps/). Para obter informações sobre limites, consulte [limites da conta de integração](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="next-steps"></a>Próximas etapas

* [Conectar-se a redes virtuais do Azure a partir do serviço Aplicativos Lógicos do Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Saiba mais sobre a [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md)
* Saiba mais sobre a [integração de rede virtual para os serviços do Azure](../virtual-network/virtual-network-for-azure-services.md)
