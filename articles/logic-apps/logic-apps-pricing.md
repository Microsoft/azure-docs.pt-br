---
title: Modelo de cobrança de & de preços
description: Visão geral sobre como o preço e a cobrança funcionam para aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
author: jonfancey
ms.author: jonfan
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 9243d089b4a000066ec03dbeeccd046db374f558
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673103"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Modelo de preços para os Aplicativos Lógicos do Azure

Os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) ajudam a criar e executar fluxos de trabalho de integração automatizados que podem ser dimensionados na nuvem. Este artigo descreve como a cobrança e os preços funcionam para os aplicativos lógicos do Azure. Para obter taxas de preços, consulte [preços dos aplicativos lógicos](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Modelo de preço por consumo

Para novos aplicativos lógicos executados no serviço público, "global", multilocatário aplicativos lógicos do Azure, você paga apenas pelo que usar. Esses aplicativos lógicos usam um plano baseado em consumo e modelo de preços. Em seu aplicativo lógico, cada etapa é uma ação, e os aplicativos lógicos do Azure medem todas as ações executadas em seu aplicativo lógico.

Por exemplo, as ações incluem:

* [Gatilhos](#triggers), que são ações especiais. Todos os aplicativos lógicos exigem um gatilho como a primeira etapa.

* [Ações "internas" ou nativas](../connectors/apis-list.md#built-in) como http, chamadas para Azure Functions e gerenciamento de API e assim por diante

* Chamadas para [conectores gerenciados](../connectors/apis-list.md#managed-connectors) , como o Outlook 365, Dropbox e assim por diante

* [Controlar ações de fluxo de trabalho](../connectors/apis-list.md#control-workflow) como loops, instruções condicionais e assim por diante

Os [conectores padrão](../connectors/apis-list.md#managed-connectors) são cobrados com o [preço do conector padrão](https://azure.microsoft.com/pricing/details/logic-apps). Os [conectores empresariais](../connectors/apis-list.md#managed-connectors) geralmente disponíveis são cobrados pelo [preço do conector corporativo](https://azure.microsoft.com/pricing/details/logic-apps), enquanto os conectores corporativos de visualização pública são cobrados com o [preço do conector padrão](https://azure.microsoft.com/pricing/details/logic-apps).

Saiba mais sobre como a cobrança funciona nos níveis de [gatilhos](#triggers) e [ações](#actions) . Ou, para obter informações sobre limites, consulte [limites e configuração para aplicativos lógicos do Azure](logic-apps-limits-and-config.md).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Modelo de preços fixo

Um [ISE ( *ambiente do serviço de integração* )](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) fornece uma maneira isolada para criar e executar aplicativos lógicos que podem acessar recursos em uma rede virtual do Azure. Os aplicativos lógicos executados em um ISE não incorrem em custos de retenção de dados. Ao criar um ISE e somente durante a criação, você pode escolher um [nível de ISE ou "SKU"](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level), que têm [taxas de preços](https://azure.microsoft.com/pricing/details/logic-apps)diferentes:

* **Premium** ISE: a unidade base deste SKU tem capacidade fixa, mas se você precisar de mais taxa de transferência, poderá [adicionar mais unidades de escala](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) durante a criação do ISE ou posteriormente. Para limites do ISE, consulte [limites e configuração para aplicativos lógicos do Azure](logic-apps-limits-and-config.md#integration-service-environment-ise).

* **Desenvolvedor** ISE: essa SKU não tem capacidade de escalar verticalmente, nenhum SLA (contrato de nível de serviço) e nenhum limite publicado. Use esse SKU somente para experimentar, desenvolver e testar, não para produção ou teste de desempenho.

Para aplicativos lógicos que você cria e executa em um ISE, você paga um [preço fixo](https://azure.microsoft.com/pricing/details/logic-apps) (versus pagamento por uso) para esses recursos:

* Gatilhos e ações [internas](../connectors/apis-list.md#built-in)

  Em um ISE, gatilhos e ações internas exibem o rótulo **principal** e são executados no mesmo ISE que seus aplicativos lógicos.

* Conectores [padrão](../connectors/apis-list.md#managed-connectors) e conectores [corporativos](../connectors/apis-list.md#enterprise-connectors) , que permitem que você tenha tantas conexões corporativas quanto desejar

   Conectores Standard e Enterprise que exibem o rótulo do **ISE** executados no mesmo ISE que seus aplicativos lógicos. Os conectores que não exibem a etiqueta do ISE são executados no serviço Public, "global", de aplicativos lógicos multilocatários. O preço fixo também se aplica aos conectores que são executados no serviço multilocatário ao usá-los com aplicativos lógicos executados em um ISE.

* Uso da [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) sem custo adicional, com base em seu [SKU do ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level):

  * **Premium** SKU do ISE: uma única conta de integração de [camada Standard](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

  * **Desenvolvedor** SKU do ISE: uma única conta de integração de [camada gratuita](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)

  Para obter um custo adicional, você pode criar mais contas de integração para o ISE [até o limite total](logic-apps-limits-and-config.md#integration-account-limits). 

  * **Premium** SKU do ISE: até 19 mais contas padrão. Nenhuma conta Gratuita ou Básica é permitida.

  * **Desenvolvedor** SKU do ISE: até 19 mais contas padrão se você já tiver uma conta gratuita, ou 20 contas padrão, se não tiver uma conta gratuita. Nenhuma conta Básica é permitida.

  Para obter mais informações sobre limites de conta de integração, consulte [limites e configuração para aplicativos lógicos do Azure](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). Você pode aprender mais sobre [as camadas de conta de integração e seu modelo de preços](#integration-accounts) posteriormente neste tópico.

<a name="connectors"></a>

## <a name="connectors"></a>Conectores

Os conectores de aplicativos lógicos do Azure ajudam seu aplicativo lógico a acessar aplicativos, serviços e sistemas na nuvem ou localmente, fornecendo [gatilhos](#triggers), [ações](#actions)ou ambos. Os conectores são classificados como Standard ou Enterprise. Para obter uma visão geral sobre esses conectores, consulte [conectores para aplicativos lógicos do Azure](../connectors/apis-list.md). Se nenhum conector predefinido estiver disponível para as APIs REST que você deseja usar em seus aplicativos lógicos, você poderá criar [conectores personalizados](/connectors/custom-connectors), que são apenas wrappers em volta dessas APIs REST. Os conectores personalizados são cobrados como conectores padrão. As seções a seguir fornecem mais informações sobre como a cobrança de gatilhos e ações funcionam.

<a name="triggers"></a>

## <a name="triggers"></a>Gatilhos

Um gatilho é sempre a primeira etapa em um fluxo de trabalho de aplicativo lógico e é uma ação especial que cria e executa uma instância de aplicativo lógico quando critérios específicos são atendidos ou um evento específico ocorre. Dispara ações de diferentes formas, o que afeta o modo como o aplicativo lógico é monitorado. Aqui estão os vários tipos de gatilhos que existem nos aplicativos lógicos do Azure:

* **Gatilho de recorrência**: você pode usar esse gatilho genérico, que não é específico de nenhum serviço ou sistema, para iniciar qualquer fluxo de trabalho de aplicativo lógico e criar uma instância de aplicativo lógico que é executada com base no intervalo de recorrência que você configurou no gatilho. Por exemplo, você pode configurar um gatilho de recorrência que é executado a cada três dias ou em uma agenda mais complexa.

* **Gatilho de sondagem**: você pode usar esse gatilho de recorrência mais especializado, que é geralmente associado ao conector gerenciado para um serviço ou sistema específico, para verificar se há eventos ou mensagens que atendam aos critérios para criar e executar a instância do aplicativo lógico com base no intervalo de recorrência que você configurou no gatilho. Mesmo quando nenhuma instância de aplicativo lógico é criada, por exemplo, quando os gatilhos são ignorados, o serviço de aplicativos lógicos mede cada solicitação de sondagem como uma execução. Para especificar o intervalo de sondagem, configure o gatilho no Designer de Aplicativos Lógicos.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Gatilho de webhook**: em vez de usar um gatilho de sondagem, você pode usar um gatilho de webhook para aguardar que o cliente envie uma solicitação para seu aplicativo lógico em uma URL de ponto de extremidade específica. Cada solicitação enviada para o ponto de extremidade do webhook conta como uma execução de ação. Por exemplo, a solicitação e o gatilho de webhook HTTP são gatilhos de webhook genéricos. Alguns conectores para serviços ou sistemas também têm gatilhos de webhook.

<a name="actions"></a>

## <a name="actions"></a>Ações

O aplicativo lógico do Azure monitora as ações "internas", como HTTP, como ações nativas. Por exemplo, ações internas incluem chamadas HTTP, chamadas de Azure Functions ou gerenciamento de API e etapas de fluxo de controle, como condições, loops e instruções switch. Cada ação tem seu próprio tipo de ação. Por exemplo, ações que chamam [conectores](/connectors) têm o tipo "ApiConnection". Esses conectores são classificados como conectores Standard ou Enterprise, que são medidos com base em seus respectivos [preços](https://azure.microsoft.com/pricing/details/logic-apps). Conectores empresariais na versão *prévia* são cobrados como conectores padrão.

Os aplicativos lógicos do Azure medem todas as ações bem-sucedidas e malsucedidas como execuções. No entanto, os aplicativos lógicos não medim essas ações:

* Ações que são ignoradas devido a condições não atendidas
* Ações que não são executadas porque o aplicativo lógico parou antes de terminar

Para ações executadas dentro de loops, os aplicativos lógicos do Azure contam cada ação para cada ciclo no loop. Por exemplo, suponha que você tenha um loop "para cada" que processe uma lista. Os aplicativos lógicos medem uma ação nesse loop multiplicando o número de itens da lista pelo número de ações no loop e adiciona a ação que inicia o loop. Portanto, o cálculo para uma lista de 10 itens é (10 * 1) + 1, o que resulta em 11 execuções de ação.

## <a name="disabled-logic-apps"></a>Aplicativos lógicos desabilitados

Os aplicativos lógicos desabilitados não são cobrados porque não podem criar novas instâncias enquanto estão desabilitados. Depois de desativar um aplicativo lógico, qualquer instância em execução no momento pode demorar algum tempo antes de parar completamente.

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>Contas de integração

Um [modelo de preço fixo](https://azure.microsoft.com/pricing/details/logic-apps) se aplica a [contas de integração](logic-apps-enterprise-integration-create-integration-account.md) em que você pode explorar, desenvolver e testar os recursos [B2B e](logic-apps-enterprise-integration-b2b.md) de [processamento XML](logic-apps-enterprise-integration-xml.md) no aplicativo lógico do Azure sem custo adicional. Cada assinatura do Azure pode ter até um [limite específico de contas de integração](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits). Cada conta de integração pode armazenar até um [limite específico de artefatos](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits), que incluem parceiros comerciais, contratos, mapas, esquemas, assemblies, certificados, configurações de lote e assim por diante.

O aplicativo lógico do Azure oferece contas de integração gratuitas, básicas e padrão. As camadas básica e Standard têm suporte do SLA (contrato de nível de serviço) dos aplicativos lógicos, enquanto a camada gratuita não tem suporte de um SLA e tem limites de disponibilidade, taxa de transferência e uso da região. Exceto para contas de integração de camada gratuita, você pode ter mais de uma conta de integração em cada região do Azure. Para obter os valores, consulte [Preços dos Aplicativos Lógicos](https://azure.microsoft.com/pricing/details/logic-apps/).

Se você tiver um [ISE ( *ambiente do serviço de integração* )](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), o ISE poderá usar uma única conta de integração sem custo adicional, embora o tipo de conta incluído varie de acordo com a SKU do [ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level). Para obter um [custo adicional](#fixed-pricing), você pode criar mais contas de integração para o ISE até o [limite total em contas de integração](logic-apps-limits-and-config.md#integration-account-limits). Para saber como o modelo de preços fixo funciona para um ISE, consulte a seção [modelo de preços fixos](#fixed-pricing) anteriores neste tópico. Para obter os valores, consulte [Preços dos Aplicativos Lógicos](https://azure.microsoft.com/pricing/details/logic-apps).

Para escolher entre uma conta de integração gratuita, básica ou padrão, examine estas descrições de caso de uso:

* **Gratuito**: para quando você quiser experimentar cenários exploratórios, não cenários de produção. Essa camada está disponível somente para regiões públicas no Azure, por exemplo, oeste dos EUA ou sudeste asiático, mas não para o [Azure China 21vianet](/azure/china/overview-operations) ou [Azure governamental](../azure-government/documentation-government-welcome.md).

* **Básico**: para quando você desejar apenas a manipulação de mensagens ou para atuar como um pequeno parceiro comercial que tenha uma relação de parceiro comercial com uma entidade de negócios maior

* **Standard**: para quando você tem relações B2B mais complexas e um número maior de entidades que você deve gerenciar

<a name="data-retention"></a>

## <a name="data-retention"></a>Retenção de dados

Exceto para aplicativos lógicos que são executados em um ambiente do serviço de integração (ISE), todas as entradas e saídas armazenadas no histórico de execução do aplicativo lógico são cobradas com base em um [período de retenção de execução](logic-apps-limits-and-config.md#run-duration-retention-limits)do aplicativo lógico. Os aplicativos lógicos executados em um ISE não incorrem em custos de retenção de dados. Para obter os valores, consulte [Preços dos Aplicativos Lógicos](https://azure.microsoft.com/pricing/details/logic-apps).

Para ajudá-lo a monitorar o consumo de armazenamento do aplicativo lógico, você pode:

* Exiba o número de unidades de armazenamento em GB que seu aplicativo lógico usa mensalmente.

* Exiba os tamanhos das entradas e saídas de uma ação específica no histórico de execução do aplicativo lógico.

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>Exibir consumo de armazenamento do aplicativo lógico

1. No portal do Azure, encontre e abra seu aplicativo lógico.

1. No menu do aplicativo lógico, em **monitoramento**, selecione **métricas**.

1. No painel direito, em **título do gráfico**, na lista **métrica** , selecione **uso de cobrança para execuções de consumo de armazenamento**.

   Essa métrica fornece o número de unidades de consumo de armazenamento em GB por mês que estão sendo cobradas.

   > [!NOTE]
   > As execuções que consomem menos de 500 MB no armazenamento podem não aparecer no modo de exibição monitoramento, mas ainda são cobradas.

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>Exibir os tamanhos de entrada e saída da ação

1. No portal do Azure, encontre e abra seu aplicativo lógico.

1. No menu do aplicativo lógico, selecione **visão geral**.

1. No painel direito, em **histórico de execuções**, selecione a execução que tem as entradas e saídas que você deseja verificar.

1. Em **execução do aplicativo lógico**, escolha **detalhes da execução**.

1. No painel **detalhes de execução do aplicativo lógico** , na tabela ações, que lista o status e a duração de cada ação, selecione a ação que você deseja exibir.

1. No painel de **ação do aplicativo lógico** , localize os tamanhos das entradas e saídas da ação. Em link de **entradas** e **link de saídas**, localize os links para essas entradas e saídas.

   > [!NOTE]
   > Para loops, somente as ações de nível superior mostram tamanhos para suas entradas e saídas. Para ações dentro de loops aninhados, entradas e saídas mostram tamanho zero e nenhum link.

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre os aplicativos lógicos do Azure](logic-apps-overview.md)
* [Criar seu primeiro aplicativo lógico](quickstart-create-first-logic-app-workflow.md)
