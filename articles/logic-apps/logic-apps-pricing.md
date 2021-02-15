---
title: Modelos de preços de & de cobrança
description: Visão geral de como os modelos de preços e cobrança funcionam nos aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 103855748c4b5d998dfc81eeb4044f5f53dae9e5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100372001"
---
# <a name="pricing-and-billing-models-for-azure-logic-apps"></a>Modelos de preços e cobrança para aplicativos lógicos do Azure

Os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) ajudam a criar e executar fluxos de trabalho de integração automatizados que podem ser dimensionados na nuvem. Este artigo descreve como os modelos de cobrança e preços funcionam para o serviço de aplicativos lógicos e recursos relacionados. Para obter taxas de preços específicas, consulte [preços dos aplicativos lógicos](https://azure.microsoft.com/pricing/details/logic-apps). Para saber como você pode planejar, gerenciar e monitorar custos, consulte [planejar e gerenciar custos para aplicativos lógicos do Azure](plan-manage-costs.md).

<a name="consumption-pricing"></a>

## <a name="multi-tenant-pricing"></a>Preços de vários locatários

Um modelo de preços de consumo de pagamento por uso se aplica a aplicativos lógicos que são executados no serviço de aplicativos lógicos multilocatários públicos, "globais". Todas as execuções bem-sucedidas e malsucedidas são medidas e cobradas.

Por exemplo, uma solicitação que um gatilho de sondagem faz ainda é medida como uma execução, mesmo que o gatilho seja ignorado, e nenhuma instância de fluxo de trabalho do aplicativo lógico é criada.

| Itens | Descrição |
|-------|-------------|
| Gatilhos e ações [internas](../connectors/apis-list.md#built-in) | Executar nativamente no serviço de aplicativos lógicos e é medido usando o [preço de **ações**](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>Por exemplo, o gatilho HTTP e o gatilho de solicitação são gatilhos internos, enquanto que a ação HTTP e a ação de resposta são ações internas. Operações de dados, operações em lote, operações de variáveis e [ações de controle de fluxo de trabalho](../connectors/apis-list.md#control-workflow), como loops, condições, comutador, ramificações paralelas e assim por diante, também são ações internas. |
| Ações e gatilhos de [conector padrão](../connectors/apis-list.md#managed-connectors) <p><p>Gatilhos e ações de [conector personalizado](../connectors/apis-list.md#custom) | Medido usando o [preço do conector padrão](https://azure.microsoft.com/pricing/details/logic-apps/). |
| Gatilhos e ações do [Enterprise Connector](../connectors/apis-list.md#managed-connectors) | Medido usando o [preço do conector Enterprise](https://azure.microsoft.com/pricing/details/logic-apps/). No entanto, durante a visualização pública, os conectores Enterprise são medidos usando o [preço do conector *padrão*](https://azure.microsoft.com/pricing/details/logic-apps/). |
| Ações dentro de [loops](logic-apps-control-flow-loops.md) | Cada ação executada em um loop é medida para cada ciclo de loop executado. <p><p>Por exemplo, suponha que você tenha um loop "for each" que inclui ações que processam uma lista. O serviço de aplicativos lógicos mede cada ação que é executada nesse loop multiplicando o número de itens de lista pelo número de ações no loop e adiciona a ação que inicia o loop. Portanto, o cálculo para uma lista de 10 itens é (10 * 1) + 1, o que resulta em 11 execuções de ação. |
| Tentativas de repetição | Para lidar com as exceções e erros mais básicos, você pode configurar uma [política de repetição](logic-apps-exception-handling.md#retry-policies) em gatilhos e ações quando houver suporte. Essas repetições junto com a solicitação original são cobradas a taxas com base em se o gatilho ou a ação tem tipo interno, padrão ou empresarial. Por exemplo, uma ação que é executada com 2 tentativas é cobrada por 3 execuções de ação. |
| [Retenção de dados e consumo de armazenamento](#data-retention) | Medido usando o preço de retenção de dados, que pode ser encontrado na [página de preços dos aplicativos lógicos](https://azure.microsoft.com/pricing/details/logic-apps/), na tabela **detalhes de preços** . |
|||

Para saber mais, consulte o seguinte:

* [Exibir métricas para execuções e consumo de armazenamento](plan-manage-costs.md#monitor-billing-metrics)
* [Limites em aplicativos lógicos do Azure](logic-apps-limits-and-config.md)

### <a name="not-metered"></a>Não medido

* Gatilhos que são ignorados devido a condições não atendidas
* Ações que não foram executadas porque o aplicativo lógico parou antes de concluir
* [Aplicativos lógicos desabilitados](#disabled-apps)

### <a name="other-related-resources"></a>Outros recursos relacionados

Os aplicativos lógicos funcionam com outros recursos relacionados, como contas de integração, gateways de dados locais e ambientes de serviço de integração (ISEs). Para saber mais sobre os preços desses recursos, examine estas seções mais adiante neste tópico:

* [On-premises data gateway (Gateway de dados local)](#data-gateway)
* [Modelo de preços da conta de integração](#integration-accounts)
* [Modelo de preços do ISE](#fixed-pricing)

### <a name="tips-for-estimating-consumption-costs"></a>Dicas para estimar os custos de consumo

Para ajudá-lo a estimar custos de consumo mais precisos, leia estas dicas:

* Considere o número possível de mensagens ou eventos que podem chegar em um determinado dia, em vez de basear seus cálculos apenas no intervalo de sondagem.

* Quando um evento ou mensagem atende aos critérios do gatilho, muitos disparadores imediatamente tentam ler todos e quaisquer outros eventos ou mensagens em espera que atendam aos critérios. Esse comportamento significa que, mesmo quando você seleciona um intervalo de sondagem mais longo, o gatilho dispara com base no número de eventos ou mensagens em espera que qualificam-se para iniciar os fluxos de trabalho. Os disparadores que seguem esse comportamento incluem o Barramento de Serviço do Azure e o Hub de Eventos do Azure.

  Por exemplo, suponha que você configure o gatilho que verifica um ponto de extremidade todos os dias. Quando o gatilho verifica o ponto de extremidade e encontra 15 eventos que atendem aos critérios, o gatilho dispara e executa o fluxo de trabalho correspondente 15 vezes. O serviço de aplicativos lógicos mede todas as ações que esses 15 fluxos de trabalho executam, incluindo as solicitações de gatilho.

<a name="fixed-pricing"></a>

## <a name="ise-pricing"></a>Preços do ISE

Um modelo de preço fixo se aplica a aplicativos lógicos executados em um [ *ambiente do serviço de integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Um ISE é cobrado usando o [preço de ambiente de serviço de integração](https://azure.microsoft.com/pricing/details/logic-apps), que depende do [nível do ISE ou da *SKU*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) que você criar. Esse preço é diferente do preço de multilocatário, pois você está pagando pela capacidade reservada e pelos recursos dedicados, independentemente de usá-los ou não.

| SKU do ISE | Descrição |
|---------|-------------|
| **Premium** | A unidade base tem [capacidade fixa](logic-apps-limits-and-config.md#integration-service-environment-ise) e é [cobrada a uma taxa por hora para o SKU Premium](https://azure.microsoft.com/pricing/details/logic-apps). Se precisar de mais taxa de transferência, você poderá [adicionar mais unidades de escala](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) ao criar o ISE ou posteriormente. Cada unidade de escala é cobrada a uma [taxa por hora que é aproximadamente metade da taxa de unidade base](https://azure.microsoft.com/pricing/details/logic-apps). <p><p>Para obter informações sobre capacidade e limites, consulte [limites do ISE em aplicativos lógicos do Azure](logic-apps-limits-and-config.md#integration-service-environment-ise). |
| **Desenvolvedor** | A unidade base tem [capacidade fixa](logic-apps-limits-and-config.md#integration-service-environment-ise) e é [cobrada a uma taxa por hora para a SKU do desenvolvedor](https://azure.microsoft.com/pricing/details/logic-apps). No entanto, essa SKU não tem nenhum SLA (contrato de nível de serviço), capacidade de expansão ou redundância durante a reciclagem, o que significa que você pode enfrentar atrasos ou tempo de inatividade. As atualizações de back-end podem interromper o serviço intermitentemente. <p><p>**Importante**: Certifique-se de usar essa SKU somente para exploração, experimentos, desenvolvimento e teste-não para teste de produção ou de desempenho. <p><p>Para obter informações sobre capacidade e limites, consulte [limites do ISE em aplicativos lógicos do Azure](logic-apps-limits-and-config.md#integration-service-environment-ise). |
|||

### <a name="included-at-no-extra-cost"></a>Incluído sem custo extra

| Itens | Descrição |
|-------|-------------|
| Gatilhos e ações [internas](../connectors/apis-list.md#built-in) | Exiba o rótulo **principal** e execute no mesmo ISE que seus aplicativos lógicos. |
| [Conectores padrão](../connectors/apis-list.md#managed-connectors) <p><p>[Conectores empresariais](../connectors/apis-list.md#enterprise-connectors) | -Conectores gerenciados que exibem o rótulo do **ISE** são especialmente projetados para funcionar sem o gateway de dados local e são executados no mesmo ISE que seus aplicativos lógicos. Os preços do ISE incluem quantas conexões corporativas desejar. <p><p>-Conectores que não exibem a execução do rótulo do ISE no serviço de aplicativos lógicos multilocatário. No entanto, o preço do ISE inclui essas execuções para aplicativos lógicos que são executados em um ISE. |
| Ações dentro de [loops](logic-apps-control-flow-loops.md) | Os preços do ISE incluem cada ação executada em um loop para cada ciclo de loop executado. <p><p>Por exemplo, suponha que você tenha um loop "for each" que inclui ações que processam uma lista. Para obter o número total de execuções de ação, multiplique o número de itens de lista com o número de ações no loop e adicione a ação que inicia o loop. Portanto, o cálculo para uma lista de 10 itens é (10 * 1) + 1, o que resulta em 11 execuções de ação. |
| Tentativas de repetição | Para lidar com as exceções e erros mais básicos, você pode configurar uma [política de repetição](logic-apps-exception-handling.md#retry-policies) em gatilhos e ações quando houver suporte. O preço do ISE inclui novas tentativas junto com a solicitação original. |
| [Retenção de dados e consumo de armazenamento](#data-retention) | Os aplicativos lógicos em um ISE não incorrem em custos de retenção e armazenamento. |
| [Contas de integração](#integration-accounts) | Inclui o uso de uma única camada de conta de integração, com base na SKU do ISE, sem custo adicional. |
|||

Para obter informações sobre limites, consulte [limites do ISE em aplicativos lógicos do Azure](logic-apps-limits-and-config.md#integration-service-environment-ise).

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Contas de integração

Uma [conta de integração](../logic-apps/logic-apps-pricing.md#integration-accounts) é um recurso separado que você cria e vincula a aplicativos lógicos para que você possa explorar, compilar e testar soluções de integração B2B que usam recursos de processamento [EDI](logic-apps-enterprise-integration-b2b.md) e [XML](logic-apps-enterprise-integration-xml.md) . O aplicativo lógico do Azure oferece esses níveis de conta de integração ou camadas:

| Camada | Descrição |
|------|-------------|
| **Basic** | Para cenários em que você deseja apenas manipulação de mensagens ou atue como um parceiro comercial pequeno que tenha uma relação de parceiro comercial com uma entidade de negócios maior. <p><p>Compatível com o SLA dos aplicativos lógicos. |
| **Standard** | Para cenários em que você tem relações B2B mais complexas e um número maior de entidades que você deve gerenciar. <p><p>Compatível com o SLA dos aplicativos lógicos. |
| **Gratuito** | Para cenários exploratórios, não cenários de produção. Essa camada tem limites de disponibilidade, taxa de transferência e uso da região. Por exemplo, a camada gratuita está disponível somente para regiões públicas no Azure, por exemplo, oeste dos EUA ou sudeste asiático, mas não para o [Azure China 21vianet](/azure/china/overview-operations) ou [Azure governamental](../azure-government/documentation-government-welcome.md). <p><p>**Observação**: não há suporte para o SLA dos aplicativos lógicos. |
|||

Para obter informações sobre limites de conta de integração, consulte [limites e configuração para aplicativos lógicos do Azure](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits), como:

* [Limites em contas de integração por assinatura do Azure](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

* [Limites em vários artefatos por conta de integração](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits). Os artefatos incluem parceiros comerciais, contratos, mapas, esquemas, assemblies, certificados, configurações de lote e assim por diante.

### <a name="integration-accounts-for-consumption-based-logic-apps"></a>Contas de integração para aplicativos lógicos baseados em consumo

As contas de integração são cobradas usando um preço fixo da [conta de integração](https://azure.microsoft.com/pricing/details/logic-apps/) baseado na camada de conta que você usa.

### <a name="ise-based-logic-apps"></a>Aplicativos lógicos baseados no ISE

Sem nenhum custo extra, o ISE inclui uma única conta de integração, com base em sua SKU do ISE. Para obter um custo extra, você pode criar mais contas de integração para o ISE usar até o [limite total do ISE](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). Saiba mais sobre o [modelo de preços do ISE](#fixed-pricing) anteriormente neste tópico.

| SKU do ISE | Conta de integração incluída | Custo adicional |
|---------|------------------------------|-----------------|
| **Premium** | Uma única conta de integração [padrão](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) | Até 19 contas padrão. Nenhuma conta Gratuita ou Básica é permitida. |
| **Desenvolvedor** | Uma única conta de integração [gratuita](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) | Até 19 contas padrão, se você já tiver uma conta gratuita ou 20 contas padrão, se não tiver uma conta gratuita. Nenhuma conta Básica é permitida. |
||||

<a name="data-retention"></a>

## <a name="data-retention-and-storage-consumption"></a>Retenção de dados e consumo de armazenamento

Todas as entradas e saídas no histórico de execução do aplicativo lógico são armazenadas e limitadas com base na duração de [execução](logic-apps-limits-and-config.md#run-duration-retention-limits)do aplicativo e no período de retenção do histórico.

* Para aplicativos lógicos no serviço de aplicativos lógicos multilocatários, o consumo de armazenamento é cobrado a um preço fixo, que pode ser encontrado na [página de preços dos aplicativos lógicos](https://azure.microsoft.com/pricing/details/logic-apps), na tabela **detalhes de preços** .

* Para aplicativos lógicos no ISEs, o consumo de armazenamento não incorre em custos de retenção de dados.

Para monitorar o uso do consumo de armazenamento, consulte [Exibir métricas para execuções e consumo de armazenamento](plan-manage-costs.md#monitor-billing-metrics).

<a name="data-gateway"></a>

## <a name="on-premises-data-gateway"></a>Gateway de dados local

Um [Gateway de dados local](../logic-apps/logic-apps-gateway-install.md) é um recurso separado que você cria para que seus aplicativos lógicos possam acessar dados locais usando conectores específicos com suporte de gateway. As operações de conectores que são executadas pelo gateway incorrem em encargos, mas o próprio gateway não provoca cobranças.

<a name="disabled-apps"></a>

## <a name="disabled-logic-apps"></a>Aplicativos lógicos desabilitados

Os aplicativos lógicos desabilitados não são cobrados porque não podem criar novas instâncias enquanto estão desabilitados. Depois de desativar um aplicativo lógico, qualquer instância em execução no momento pode demorar algum tempo antes de parar completamente.

## <a name="next-steps"></a>Próximas etapas

* [Planejar e gerenciar custos para aplicativos lógicos do Azure](plan-manage-costs.md)
