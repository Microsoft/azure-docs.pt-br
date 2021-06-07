---
title: Acesso às redes virtuais do Azure
description: Visão geral sobre como ISEs (ambientes de serviço de integração) ajudam os aplicativos lógicos a acessarem as VNETs (redes virtuais) do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: 3070083040424b877159955dc2138f15319f05c8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766381"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Acessar recursos de rede virtual do Azure a partir dos Aplicativos Lógicos do Azure usando ISEs (Ambientes de Serviço de Integração)

Às vezes, os aplicativos lógicos precisam acessar recursos protegidos, como VMs (máquinas virtuais) e outros sistemas ou serviços que estão na [rede virtual do Azure](../virtual-network/virtual-networks-overview.md) ou conectados a ela. Para configurar esse acesso, você pode [criar um *ISE* (Ambiente de Serviço de Integração)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md). Um ISE é uma instância do serviço de Aplicativos Lógicos que usa recursos dedicados e é executado separadamente do serviço de Aplicativos Lógicos de multilocatários "globais". Os dados em um ISE permanecem na [mesma região em que você cria e implanta esse ISE](https://azure.microsoft.com/global-infrastructure/data-residency/).

Por exemplo, algumas redes virtuais do Azure usam pontos de extremidade privados, que podem ser configurados por meio do [Link Privado do Azure](../private-link/private-link-overview.md) para fornecer acesso aos serviços de PaaS do Azure, como Armazenamento do Azure, Azure Cosmos DB ou Banco de Dados SQL do Azure, serviços de parceiro ou serviços de cliente hospedados no Azure. Se os seus aplicativos lógicos precisarem de acesso a redes virtuais que usam pontos de extremidade privados, será necessário criar, implantar e executar esses aplicativos lógicos em um ISE.

Quando você cria um ISE, o Azure *injeta* ou implanta esse ISE em sua rede virtual do Azure. Em seguida, é possível usar esse ISE como o local para os aplicativos lógicos e as contas de integração que precisam de acesso.

![Selecionar o ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Esta visão geral fornece mais informações sobre [por que é desejável usar um ISE](#benefits), as [diferenças entre o serviço de Aplicativos Lógicos dedicado e de multilocatário](#difference) e como você pode acessar diretamente os recursos que estão na sua rede virtual do Azure ou conectados a ela.

<a name="benefits"></a>

## <a name="why-use-an-ise"></a>Por que usar um ISE?

A execução de aplicativos lógicos em sua própria instância dedicada separada ajuda a reduzir o impacto que outros locatários do Azure podem ter sobre o desempenho de seus aplicativos, também conhecido como o [efeito "vizinhos com ruído"](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). Um ISE proporciona estes benefícios:

* Acesso direto a recursos que estão na sua rede virtual ou conectados a ela

  Os aplicativos lógicos que você cria e executa em um ISE podem usar [conectores projetados especificamente que são executados no ISE](../connectors/managed.md#ise-connectors). Se existir um conector do ISE para um sistema ou fonte de dados local, você poderá se conectar diretamente sem precisar usar o [gateway de dados local](../logic-apps/logic-apps-gateway-connection.md). Para obter mais informações, confira [Recursos dedicados versus multilocatário](#difference) e [Acesso a sistemas locais](#on-premises) mais adiante neste tópico.

* Acesso contínuo a recursos que estão fora da sua rede virtual ou não estão conectados a ela

  Os aplicativos lógicos criados e executados em um ISE ainda podem usar conectores que são executados no serviço de Aplicativos Lógicos de multilocatário quando um conector específico do ISE não está disponível. Para obter mais informações, confira [Recursos dedicados versus multilocatário](#difference).

* Seus próprios endereços IP estáticos, que são separados dos endereços IP estáticos compartilhados pelos aplicativos lógicos no serviço multilocatário. Você também pode configurar um único endereço IP de saída público, estático e previsível para se comunicar com os sistemas de destino. Dessa forma, você não precisa configurar aberturas adicionais do firewall nesses sistemas de destino para cada ISE.

* Aumento dos limites de duração da execução, retenção de armazenamento, taxa de transferência, tempos limite de solicitação e resposta HTTP, tamanhos de mensagem e solicitações de conector personalizado. Para obter mais informações, confira [Limites e configuração para Aplicativos Lógicos do Azure](logic-apps-limits-and-config.md).

<a name="difference"></a>

## <a name="dedicated-versus-multi-tenant"></a>Recursos dedicados versus multilocatário

Ao criar e executar aplicativos lógicos em um ISE, você obtém as mesmas experiências de usuário e funcionalidades semelhantes ao serviço de Aplicativos Lógicos de multilocatário. Você pode usar os mesmos gatilhos, ações e conectores gerenciados internos que estão disponíveis no serviço de Aplicativos Lógicos de multilocatário. Alguns conectores gerenciados oferecem versões adicionais do ISE. A diferença entre os conectores do ISE e os conectores não ISE é o local em que eles são executados e os rótulos que eles têm no Designer do Aplicativo Lógico quando você trabalha em um ISE.

![Conectores com e sem rótulos em um ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* Ações e gatilhos internos, como HTTP, exibem o rótulo **CORE** e executam o mesmo ISE que o aplicativo lógico.

* Os conectores gerenciados que exibem o rótulo **ISE** são especialmente projetados para ISEs e *sempre são executados no mesmo ISE que o seu aplicativo lógico*. Por exemplo, veja abaixo alguns [conectores que oferecem versões do ISE](../connectors/managed.md#ise-connectors):<p>

  * Armazenamento de Blobs, Armazenamento de Arquivos e Armazenamento de Tabelas do Azure
  * Barramento de Serviço do Azure, Filas do Azure, Hubs de Eventos do Azure
  * Automação do Azure, Azure Key Vault, Grade de Eventos do Azure e Logs do Azure Monitor
  * FTP, SFTP-SSH, Sistema de Arquivos e SMTP
  * SAP, IBM MQ, IBM DB2 e IBM 3270
  * SQL Server, Azure Synapse Analytics, Azure Cosmos DB
  * AS2, X12 e EDIFACT

  Com exceções raras, se um conector do ISE estiver disponível em um sistema ou fonte de dados local, você poderá se conectar diretamente sem usar o [gateway de dados local](../logic-apps/logic-apps-gateway-connection.md). Para obter mais informações, confira [Acesso a sistemas locais](#on-premises) mais adiante neste tópico.

* Os conectores gerenciados que não exibem o rótulo do **ISE** continuam a funcionar em aplicativos lógicos em um ISE. Esses conectores *sempre são executados no serviço de Aplicativos Lógicos de multilocatário* e não no ISE.

* Os conectores personalizados que você cria *fora de um ISE*, independentemente de eles exigirem o [gateway de dados local](../logic-apps/logic-apps-gateway-connection.md), continuam funcionando nos aplicativos lógicos em um ISE. No entanto, os conectores personalizados criados *dentro de um ISE* não funcionarão com o gateway de dados local. Para obter mais informações, confira [Acesso a sistemas locais](#on-premises).

<a name="on-premises"></a>

## <a name="access-to-on-premises-systems"></a>Acesso a sistemas locais

Os aplicativos lógicos que são executados em um ISE podem acessar diretamente sistemas locais e fontes de dados que estão em uma rede virtual do Azure ou estão conectados a ela usando estes itens:<p>

* O gatilho ou ação HTTP, que exibe o rótulo **CORE**

* O conector do **ISE**, se disponível, para uma fonte de dados ou sistema local

  Se um conector do ISE estiver disponível, você poderá acessar diretamente o sistema ou a fonte de dados sem o [gateway de dados local](../logic-apps/logic-apps-gateway-connection.md). No entanto, se você precisar acessar o SQL Server de um ISE e usar a autenticação do Windows, será necessário usar a versão sem ISE do conector e o gateway de dados local. A versão do ISE do conector não dá suporte à autenticação do Windows. Para obter mais informações, confira [Conectores do ISE](../connectors/managed.md#ise-connectors) e [Conecte-se por meio de um Ambiente de Serviço de Integração](../connectors/managed.md#integration-account-connectors).

* Um conector personalizado

  * Os conectores personalizados que você cria *fora de um ISE*, independentemente de eles exigirem o [gateway de dados local](../logic-apps/logic-apps-gateway-connection.md), continuam funcionando nos aplicativos lógicos em um ISE.

  * Os conectores personalizados criados *dentro de um ISE* não funcionarão com o gateway de dados local. No entanto, esses conectores podem acessar diretamente sistemas e fontes de dados locais que estão na rede virtual que hospeda o ISE ou conectados a ela. Portanto, os aplicativos lógicos que estão dentro de um ISE geralmente não precisam do gateway de dados ao acessar esses recursos.

Para acessar sistemas e fontes de dados locais que não têm conectores do ISE, estão fora da sua rede virtual ou não estão conectados à sua rede virtual, você ainda precisará usar o gateway de dados local. Os aplicativos lógicos em um ISE podem continuar usando conectores que não têm o rótulo **CORE** ou **ISE**. Esses conectores são executados no serviço de Aplicativos Lógicos de multilocatário, em vez de no seu ISE. 

<a name="ise-level"></a>

## <a name="ise-skus"></a>SKUs do ISE

Ao criar o ISE, você pode selecionar o SKU de Desenvolvedor ou o SKU Premium. Essa opção de SKU está disponível somente durante a criação do ISE e não pode ser alterada posteriormente. Veja abaixo as diferenças entre esses SKUs:

* **Desenvolvedor**

  Fornece um ISE de menor custo que você pode usar para exploração, experimentos, desenvolvimento e teste, mas não para teste de produção ou de desempenho. O SKU do Desenvolvedor inclui gatilhos e ações internos, Conectores padrão, Conectores empresariais e uma conta de integração de [Camada gratuita](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) com um [preço mensal fixo](https://azure.microsoft.com/pricing/details/logic-apps). 

  > [!IMPORTANT]
  > Esse SKU não tem SLA (contrato de nível de serviço), capacidade de expansão nem redundância durante a reciclagem, o que significa que você pode enfrentar atrasos ou tempo de inatividade. As atualizações de back-end podem interromper o serviço de modo intermitente.

  Para obter informações sobre capacidade e limites, confira os [Limites do ISE em Aplicativos Lógicos do Azure](logic-apps-limits-and-config.md#integration-service-environment-ise). Para saber como funciona a cobrança dos ISEs, confira o [Modelo de preços dos Aplicativos Lógicos](../logic-apps/logic-apps-pricing.md#fixed-pricing).

* **Premium**

  Fornece um ISE que pode ser usado para teste de produção e desempenho. O SKU Premium inclui suporte a SLA, gatilhos e ações internos, Conectores padrão, Conectores empresariais, uma conta de integração da [camada Standard](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits), capacidade de escalonamento vertical e redundância durante a reciclagem por um [preço mensal fixo](https://azure.microsoft.com/pricing/details/logic-apps).

  Para obter informações sobre capacidade e limites, confira os [Limites do ISE em Aplicativos Lógicos do Azure](logic-apps-limits-and-config.md#integration-service-environment-ise). Para saber como funciona a cobrança dos ISEs, confira o [Modelo de preços dos Aplicativos Lógicos](../logic-apps/logic-apps-pricing.md#fixed-pricing).

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>Acesso ao ponto de extremidade do ISE

Ao criar o ISE, você pode optar por usar pontos de extremidade de acesso internos ou externos. A sua seleção determina se os gatilhos de solicitação ou de webhook em aplicativos lógicos no ISE podem receber chamadas de fora da sua rede virtual. Esses pontos de extremidade também afetam o modo de acesso às entradas e saídas do histórico de execuções dos seus aplicativos lógicos.

> [!IMPORTANT]
> Você pode selecionar o ponto de extremidade de acesso somente durante a criação do ISE e não pode alterar essa opção posteriormente.

* **Interno**: os pontos de extremidade privados permitem chamadas para aplicativos lógicos no ISE, nas quais você pode exibir e acessar entradas e saídas do histórico de execuções dos aplicativos lógicos *somente de dentro da sua rede virtual*.

  > [!IMPORTANT]
  > Se você precisar usar esses gatilhos baseados em webhook, use os pontos de extremidade externos, *não* os pontos de extremidade internos, para criar o ISE:
  > 
  > * Azure DevOps
  > * Grade de Eventos do Azure
  > * Common Data Service
  > * Office 365
  > * SAP (versão do ISE)
  > 
  > Além disso, verifique se você tem conectividade de rede entre os pontos de extremidade privados e o computador no qual você deseja acessar o histórico de execuções. Caso contrário, ao tentar ver o histórico de execuções do aplicativo lógico, você receberá o seguinte erro: "Erro inesperado. Falha ao buscar".
  >
  > ![Erro da ação de Armazenamento do Azure resultante da incapacidade de enviar tráfego por meio do firewall](./media/connect-virtual-network-vnet-isolated-environment-overview/integration-service-environment-error.png)
  >
  > Por exemplo, o computador cliente pode existir dentro da rede virtual do ISE ou dentro de uma rede virtual conectada à rede virtual do ISE por meio de emparelhamento ou de uma rede virtual privada. 

* **Externo**: os pontos de extremidade públicos permitem chamadas para aplicativos lógicos no ISE, nas quais você pode ver e acessar entradas e saídas do histórico de execuções dos aplicativos lógicos *de fora da sua rede virtual*. Se você usar os NSGs (grupos de segurança de rede), verifique se eles estão configurados com regras de entrada para permitir o acesso às entradas e saídas do histórico de execuções. Para obter mais informações, confira [Habilitar o acesso para ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access).

Para determinar se o ISE usa um ponto de extremidade de acesso interno ou externo, no menu do ISE, em **Configurações**, selecione **Propriedades** e localize a propriedade **Ponto de extremidade de acesso**:

![Localizar o ponto de extremidade de acesso do ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/find-ise-access-endpoint.png)

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Modelo de preços

Os aplicativos lógicos, os gatilhos internos, as ações internas e os conectores que são executados no ISE usam um plano de preços fixos diferente do plano baseado em consumo. Para obter mais informações, confira o [Modelo de preços de Aplicativos Lógicos](../logic-apps/logic-apps-pricing.md#fixed-pricing). Para obter os valores, consulte [Preços dos Aplicativos Lógicos](https://azure.microsoft.com/pricing/details/logic-apps/).

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Contas de integração com ISE

É possível usar contas de integração com aplicativos lógicos dentro de um ISE (ambiente de serviço de integração). No entanto, essas contas de integração devem usar o *mesmo ISE* que os aplicativos lógicos vinculados. Os aplicativos lógicos em um ISE podem fazer referência somente às contas de integração que estão no mesmo ISE. Ao criar uma conta de integração, é possível selecionar o ISE como local para a conta de integração. Para saber como funciona o preço e a cobrança das contas de integração com um ISE, confira o [Modelo de preços dos Aplicativos Lógicos](../logic-apps/logic-apps-pricing.md#fixed-pricing). Para obter os valores, consulte [Preços dos Aplicativos Lógicos](https://azure.microsoft.com/pricing/details/logic-apps/). Para obter informações sobre limites, confira [Limites da conta de integração](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="next-steps"></a>Próximas etapas

* [Conectar-se a redes virtuais do Azure a partir do serviço Aplicativos Lógicos do Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Saiba mais sobre a [Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md)
* Saiba mais sobre a [integração de rede virtual para os serviços do Azure](../virtual-network/virtual-network-for-azure-services.md)
