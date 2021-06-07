---
title: Planejar o gerenciamento de custos para o serviço de aplicativo
description: Saiba como planejar e gerenciar custos para Azure App serviço usando a análise de custo na portal do Azure.
ms.custom: subject-cost-optimization
ms.service: app-service
ms.topic: how-to
ms.date: 01/01/2021
ms.openlocfilehash: ada4c1991a57c8252247c9617e097dc82cb3b4a9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100593978"
---
# <a name="plan-and-manage-costs-for-azure-app-service"></a>Planejar e gerenciar custos para Azure App serviço

<!-- Check out the following published examples:
- [https://docs.microsoft.com/azure/cosmos-db/plan-manage-costs](../cosmos-db/plan-manage-costs.md)
- [https://docs.microsoft.com/azure/storage/common/storage-plan-manage-costs](../storage/common/storage-plan-manage-costs.md)
- [https://docs.microsoft.com/azure/machine-learning/concept-plan-manage-cost](../machine-learning/concept-plan-manage-cost.md)
-->

<!-- Note for Azure service writer: Links to Cost Management articles are full URLS with the ?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn campaign suffix. Leave those URLs intact. They're used to measure traffic to Cost Management articles.
-->

<!-- Note for Azure service writer: Modify the following for your service. -->

Este artigo descreve como planejar e gerenciar custos para Azure App serviço. Primeiro, você usa a calculadora de preços do Azure para ajudar a planejar os custos do serviço de aplicativo antes de adicionar qualquer recurso ao serviço para estimar os custos. Em seguida, ao adicionar recursos do Azure, examine os custos estimados. Depois de começar a usar os recursos do serviço de aplicativo, use os recursos de [Gerenciamento de custos](../cost-management-billing/index.yml?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para definir orçamentos e monitorar custos. Você também pode examinar os custos previstos e identificar as tendências de gastos para identificar as áreas em que talvez queira agir. Os custos para Azure App serviço são apenas uma parte dos custos mensais em sua fatura do Azure. Embora este artigo explique como planejar e gerenciar os custos do serviço de aplicativo, você é cobrado por todos os serviços e recursos do Azure usados em sua assinatura do Azure, incluindo os serviços de terceiros.

## <a name="relevant-costs-for-app-service"></a>Custos relevantes para o serviço de aplicativo

O serviço de aplicativo é executado na infraestrutura do Azure que acumula o custo. É importante entender que a infraestrutura adicional pode acumular o custo. Você deve gerenciar esse custo ao fazer alterações em recursos implantados.

### <a name="costs-that-accrue-with-azure-app-service"></a>Custos que se acumulam com o serviço Azure App

Dependendo do recurso que você usa no serviço de aplicativo, os seguintes recursos de acumulação de custo podem ser criados:

- **Plano do serviço de aplicativo**  Necessário para hospedar um aplicativo do serviço de aplicativo.
- **Camada isolada**  Uma [rede virtual](../virtual-network/index.yml) é necessária para um ambiente do serviço de aplicativo.
- **Backup**  do  Uma [conta de armazenamento](../storage/index.yml) é necessária para fazer backups.
- **Logs de diagnóstico**  Você pode selecionar a [conta de armazenamento](../storage/index.yml) como a opção de log ou integrá-la ao [Azure log Analytics](../azure-monitor/logs/log-analytics-tutorial.md).
- **Certificados do serviço de aplicativo**  Os certificados comprados no Azure devem ser mantidos em [Azure Key Vault](../key-vault/index.yml).

Outros recursos de custo para o serviço de aplicativo são (consulte [preços do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/) para obter detalhes):

- [Domínios do serviço de aplicativo](manage-custom-dns-buy-domain.md)  Sua assinatura será cobrada pelo registro de domínio anualmente, se você habilitar a renovação automática.
- [Certificados do serviço de aplicativo](configure-ssl-certificate.md#import-an-app-service-certificate)  Encargo único no momento da compra. Se você tiver vários subdomínios para proteger, poderá reduzir o custo adquirindo um certificado curinga em vez de vários certificados padrão.
- [Associações de certificado baseadas em IP](configure-ssl-bindings.md#create-binding)  A associação é configurada em um certificado no nível do aplicativo. Os custos são acumulados para cada associação. Para a camada **Standard** e superior, a primeira associação baseada em IP não é cobrada.

### <a name="costs-that-might-accrue-after-resource-deletion"></a>Custos que podem ser acumulados após a exclusão do recurso

Quando você exclui todos os aplicativos em um plano do serviço de aplicativo, o plano continua a acumular encargos com base em seu tipo de preço e número de instâncias configurados. Para evitar encargos indesejados, exclua o plano ou dimensione-o para a camada **gratuita** .

Depois de excluir Azure App recursos de serviço, os recursos dos serviços do Azure relacionados podem continuar existindo. Eles continuam acumulando os custos até que você os exclua. Por exemplo:

- A rede virtual que você criou para um plano do serviço de aplicativo de camada **isolada**
- Contas de armazenamento que você criou para armazenar backups ou logs de diagnóstico
- Key Vault criado para armazenar certificados do serviço de aplicativo
- Namespaces analíticos de log que você criou para enviar logs de diagnóstico
- [Reservas de instância ou carimbo](#azure-reservations) para o serviço de aplicativo que ainda não expiraram

### <a name="using-monetary-credit-with-azure-app-service"></a>Usando crédito monetário com o serviço Azure App

Você pode pagar por Azure App cobranças de serviço com o crédito antecipado do Azure (anteriormente chamado de compromisso monetário). No entanto, você não pode usar o crédito de pagamento antecipado do Azure para pagar por cobranças de produtos e serviços de terceiros, incluindo aqueles do Azure Marketplace.

## <a name="estimate-costs"></a>Estimar custos

Uma maneira fácil de estimar e otimizar o custo do serviço de aplicativo com antecedência é usar a [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/).

Para usar a calculadora de preços, clique em **serviço de aplicativo** na guia **produtos** . Em seguida, role para baixo para trabalhar com a calculadora. A captura de tela a seguir é um exemplo e não reflete os preços atuais.

![Exemplo mostrando o custo estimado na calculadora de preços do Azure](media/overview-manage-costs/pricing-calculator.png)

### <a name="review-estimated-costs-in-the-azure-portal"></a>Examinar os custos estimados no portal do Azure

Ao criar um aplicativo do serviço de aplicativo ou um plano do serviço de aplicativo, você pode ver os custos estimados.

Para criar um aplicativo e exibir o preço estimado:

1. Na página criar, role para baixo até **plano do serviço de aplicativo** e clique em **criar novo**.
1. Especifique um nome e clique em **OK**.
1. Ao lado de **SKU e tamanho**, clique em **alterar tamanho**.
1. Examine o preço estimado mostrado no resumo. A captura de tela a seguir é um exemplo e não reflete os preços atuais.

    ![Examinar o custo estimado de cada tipo de preço no portal](media/overview-manage-costs/pricing-estimates.png)

Se sua assinatura do Azure tiver um limite de gastos, o Azure impedirá que você gaste em seu valor de crédito. Conforme você cria e usa os recursos do Azure, seus créditos são usados. Quando você atingir seu limite de crédito, os recursos implantados serão desabilitados para o restante desse período de cobrança. Você não pode alterar seu limite de crédito, mas pode removê-lo. Para obter mais informações sobre limites de gastos, consulte [limite de gastos do Azure](../cost-management-billing/manage/spending-limit.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="optimize-costs"></a>Otimizar custos

Em um nível básico, os aplicativos do serviço de aplicativo são cobrados pelo plano do serviço de aplicativo que os hospeda. Os custos associados à implantação do serviço de aplicativo dependem de alguns fatores principais:

- **Tipo de preço**  Caso contrário, conhecido como SKU do plano do serviço de aplicativo. As camadas mais altas fornecem mais núcleos de CPU, memória, armazenamento ou recursos, ou combinações deles.
- As camadas dedicadas de **contagem de instâncias** (Basic e acima) podem ser escaladas horizontalmente e cada instância dimensionada horizontalmente acumula os custos.
- **Taxa de selo**  Na camada isolada, uma taxa fixa é acumulada no ambiente do serviço de aplicativo, independentemente de quantos aplicativos ou instâncias de trabalho estão hospedados.

Um plano do serviço de aplicativo pode hospedar mais de um aplicativo. Dependendo da sua implantação, você pode economizar custos que hospedam mais aplicativos em um plano do serviço de aplicativo (ou seja, hospedando seus aplicativos em menos planos do serviço de aplicativo).

Para obter detalhes, consulte [visão geral do plano do serviço de aplicativo](overview-hosting-plans.md)

### <a name="non-production-workloads"></a>Cargas de trabalho de não produção

Para testar o serviço de aplicativo ou sua solução ao acumular custos baixos ou mínimos, você pode começar usando os dois tipos de preço de nível de entrada, **gratuito** e **compartilhado**, que são hospedados em instâncias compartilhadas. Para testar seu aplicativo em instâncias dedicadas com melhor desempenho, você pode atualizar para a camada **básica** , que dá suporte a aplicativos do Windows e do Linux. 

> [!NOTE]
> **Preços de desenvolvimento/teste do Azure**  Para testar as cargas de trabalho de pré-produção que exigem camadas mais altas (todas as camadas, exceto para **isoladas**), os assinantes do Visual Studio também podem aproveitar os [preços de desenvolvimento/teste do Azure](https://azure.microsoft.com/pricing/dev-test/), que oferece descontos significativos.
>
> As camadas **gratuita** e **compartilhada** , bem como os descontos de preço de desenvolvimento/teste do Azure, não têm um SLA com suporte financeiro.

### <a name="production-workloads"></a>Cargas de trabalho de produção

As cargas de trabalho de produção são fornecidas com a recomendação do tipo de preço **padrão** dedicado ou acima. Embora o preço vá para níveis mais altos, ele também oferece mais memória e armazenamento e hardware de maior desempenho, proporcionando maior densidade de aplicativo por instância de computação. Isso se traduz em uma contagem de instâncias inferior para o mesmo número de aplicativos e, portanto, menor custo. Na verdade, o **Premium v3** (a camada mais alta não **isolada** ) é a maneira mais econômica de servir seu aplicativo em escala. Para adicionar à economia, você pode obter descontos profundos em [reservas Premium v3](#azure-reservations).

> [!NOTE]
> O **Premium v3** dá suporte a contêineres do Windows e contêineres do Linux. 

Depois de escolher o tipo de preço desejado, você deve minimizar as instâncias ociosas. Em uma implantação em expansão, você pode desperdiçar dinheiro em instâncias de computação subutilizadas. Você deve [Configurar o dimensionamento](../azure-monitor/autoscale/autoscale-get-started.md)automático, disponível na camada **Standard** e superior. Ao criar agendamentos de expansão, bem como as regras de expansão com base em métricas, você paga apenas pelas instâncias de que realmente precisa em um determinado momento.

### <a name="azure-reservations"></a>Reservas do Azure

Se você planeja utilizar um número mínimo conhecido de instâncias de computação por um ano ou mais, você deve aproveitar a camada **Premium v3** e reduzir drasticamente o custo da instância ao reservar essas instâncias em incrementos de 1 ano ou 3 anos. A economia de custo mensal pode ser de até 55% por instância. São possíveis dois tipos de reservas:

- **Windows (ou independente de plataforma)**  Pode se aplicar a instâncias do Windows ou Linux em sua assinatura.
- **Específico do Linux**  Aplica-se somente a instâncias do Linux em sua assinatura.

O preço da instância reservada se aplica às instâncias aplicáveis em sua assinatura, até o número de instâncias que você reserva. As instâncias reservadas são uma questão de cobrança e não estão ligadas a instâncias de computação específicas. Se você executar menos instâncias do que reservar a qualquer momento durante o período de reserva, você ainda pagará pelas instâncias reservadas. Se você executar mais instâncias do que reservar a qualquer momento durante o período de reserva, você pagará o custo acumulado normal das instâncias adicionais.

A camada **isolada** (ambiente do serviço de aplicativo) também dá suporte a reservas de 1 e 3 anos a preços reduzidos. Para obter mais informações, consulte [como os descontos de reserva se aplicam ao serviço Azure app](../cost-management-billing/reservations/reservation-discount-app-service.md).

## <a name="monitor-costs"></a>Monitorar custos

Ao usar os recursos do Azure com o serviço de aplicativo, você incorre em custos. Os custos da unidade de uso de recursos do Azure variam de acordo com os intervalos de tempo (segundos, minutos, horas e dias). Assim que o serviço de aplicativo for iniciado, os custos serão incorridos e você poderá ver os custos na [análise de custo](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

Ao usar a análise de custo, você exibe os custos do serviço de aplicativo em gráficos e tabelas para intervalos de tempo diferentes. Alguns exemplos são por dia, mês atual e anterior e ano. Você também exibe os custos em relação a orçamentos e custos previstos. Alternar para exibições mais longas ao longo do tempo pode ajudá-lo a identificar tendências de gastos. E você verá onde o excesso de gastos pode ter ocorrido. Se você tiver criado orçamentos, também poderá ver facilmente onde eles foram excedidos.
    
Para exibir os custos do serviço de aplicativo na análise de custo:

1. Entre no portal do Azure.
2. Abra o escopo no portal do Azure e selecione **análise de custo** no menu. Por exemplo, vá para **assinaturas**, selecione uma assinatura na lista e, em seguida, selecione  **análise de custo** no menu. Selecione o **escopo** para alternar para um escopo diferente na análise de custo.
3. Por padrão, o custo dos serviços é mostrado no primeiro gráfico de rosca. Selecione a área no gráfico rotulada serviço de aplicativo.

Os custos mensais reais são mostrados quando a análise de custo é aberta inicialmente. Veja um exemplo que mostra todos os custos de uso mensal.

![Exemplo mostrando os custos acumulados de uma assinatura](media/overview-manage-costs/all-costs.png)

Para restringir os custos de um único serviço, como o serviço de aplicativo, selecione **Adicionar filtro** e, em seguida, selecione **nome do serviço**. Em seguida, selecione **serviço de aplicativo**.

Aqui está um exemplo mostrando os custos apenas para o serviço de aplicativo.

![Exemplo mostrando os custos acumulados para ServiceName](media/overview-manage-costs/service-specific-costs.png)

No exemplo anterior, você verá o custo atual do serviço. Os custos pelas regiões do Azure (locais) e os custos do serviço de aplicativo por grupo de recursos também são mostrados. A partir daqui, você pode explorar os custos por conta própria.

## <a name="create-budgets"></a>Criar orçamentos

<!-- Note to Azure service writer: Modify the following as needed for your service. -->

Você pode criar [orçamentos](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para gerenciar custos e criar [alertas](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) que notifiquem automaticamente as partes interessadas de despesas de gastos e que estejam gastando riscos. Os alertas são baseados nos gastos comparados com os limites de orçamento e de custo. Orçamentos e alertas são criados para assinaturas e grupos de recursos do Azure, para que eles sejam úteis como parte de uma estratégia de monitoramento de custo geral. 

Os orçamentos podem ser criados com filtros para recursos ou serviços específicos no Azure se você quiser mais granularidade presente no monitoramento. Os filtros ajudam a garantir que você não crie acidentalmente novos recursos que custam dinheiro extra. Para obter mais informações sobre as opções de filtro disponíveis quando você cria um orçamento, consulte [Opções de grupo e filtro](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportar dados de custo

Você também pode [exportar seus dados de custo](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para uma conta de armazenamento. Isso é útil quando você precisa ou outros para fazer mais análises de dados para custos. Por exemplo, uma equipe de Finanças pode analisar os dados usando o Excel ou Power BI. Você pode exportar seus custos em uma agenda diária, semanal ou mensal e definir um intervalo de datas personalizado. A exportação de dados de custo é a maneira recomendada para recuperar conjuntos de dado de custo.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como os preços funcionam com o armazenamento do Azure. Consulte [preços do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/).
- Saiba [como otimizar seu investimento em nuvem com o gerenciamento de custos do Azure](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Saiba mais sobre como gerenciar custos com [análise de custo](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Saiba mais sobre como [evitar custos inesperados](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Faça o curso de aprendizado guiado de [Gerenciamento de custos](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) .

<!-- Insert links to other articles that might help users save and manage costs for you service here.

Create a table of contents entry for the article in the How-to guides section where appropriate. -->