---
title: "Evitar custos inesperados e gerenciar a cobrança – Azure | Microsoft Docs"
description: Saiba como evitar encargos inesperados em sua conta do Azure. Use recursos de gerenciamento e controle de custos para uma assinatura do Microsoft Azure.
services: 
documentationcenter: 
author: tonguyen10
manager: tonguyen
editor: 
tags: billing
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: tonguyen
ms.openlocfilehash: d74f649a8b5f35ffe16479576959e5ebb2857bf1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Evite cobranças inesperadas com o gerenciamento de custo e a cobrança do Azure

Ao se inscrever no Azure, há várias coisas que podem ser feitas para você ter uma ideia melhor de seus gastos. A [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) pode fornecer uma estimativa de custos antes de você criar um recurso do Azure. O [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) fornece uma previsão e a análise de custos atual de sua assinatura. Se desejar agrupar e entender os custos de diferentes projetos ou equipes, examine a [marcação de recursos](../azure-resource-manager/resource-group-using-tags.md). Se sua organização tiver um sistema de relatórios que você prefira usar, confira as [APIs de cobrança](billing-usage-rate-card-overview.md). 

- Se sua assinatura for um EA (Contrato Enterprise), a visualização pública para visualização dos custos no portal do Azure agora estará disponível. Se sua assinatura for feita por meio do CSP (Provedor de Soluções na Nuvem) ou pelo Azure Sponsorship, alguns dos recursos a seguir poderão não se aplicar ao seu caso. Consulte [Recursos adicionais do EA, CSP e Sponsorship](#other-offers) para obter mais informações.

- Se sua assinatura for uma Avaliação Gratuita, do [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), do AIO (Azure via Open) ou BizSpark, ela será desabilitada automaticamente quando todos os créditos forem usados. Saiba mais sobre os [limites de gastos](#spending-limit) para evitar que sua assinatura desabilitada de forma inesperada.

## <a name="get-estimated-costs-before-adding-azure-services"></a>Obter custos estimados antes de adicionar serviços do Azure

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Estimar custo online usando a calculadora de preços

Confira a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) e para obter uma estimativa do custo mensal do serviço em que você está interessado. Você pode adicionar qualquer recurso do Azure para obter um estimativa de custo.

![Captura de tela do menu de calculadora de preços](./media/billing-getting-started/pricing-calc.png)

Por exemplo, uma VM Windows A1 é calculada ao custo de US$ 66,96/mês em horas de computação se você deixá-la em execução o tempo todo:

![Captura de tela da calculadora de preços mostrando que uma VM Windows A1 é estimada ao custo de US$ 66,96 por mês](./media/billing-getting-started/pricing-calcVM.png)

Para obter mais informações sobre preços, consulte estas [Perguntas frequentes](https://azure.microsoft.com/pricing/faq/). Ou, se quiser falar com um vendedor do Azure, entre em contato com 1-800-867-1389.

### <a name="review-the-estimated-cost-in-the-azure-portal"></a>Examine o custo estimado no portal do Azure

Normalmente, quando você adiciona um serviço no portal do Azure, há um modo de exibição que mostra um custo estimado semelhante por mês. Por exemplo, quando você escolhe o tamanho de sua VM do Windows, vê o custo mensal estimado para as horas de computação:

![Exemplo: uma VM Windows A1 é estimada ao custo de US$ 66,96 por mês](./media/billing-getting-started/vm-size-cost.PNG)

### <a name="set-up-billing-alerts"></a>Configurar alertas de cobrança

Configure alertas de cobrança para receber emails quando os custos de uso excederem um valor especificado. Se você tiver créditos mensais, configure alertas para o uso de um valor especificado. Para obter mais informações, veja [Configurar alertas de cobrança para suas assinaturas do Microsoft Azure](billing-set-up-alerts.md).

![Captura de tela de um email de alerta de cobrança](./media/billing-getting-started/billing-alert.png)

> [!NOTE]
> Este recurso ainda está em visualização e,portanto, verifique seu uso regularmente.

Talvez seja melhor usar a estimativa de custo da calculadora de preços como uma diretriz para seu primeiro alerta.

### <a name="spending-limit"></a>Verifique se você tem um limite de gastos ativado

Se você tiver uma assinatura que usa crédito, o limite de gastos é ativado para você por padrão. Dessa forma, quando você gasta todo o seu crédito, não há cobrança a mais no seu cartão de crédito. Consulte a [lista completa de ofertas do Azure e a disponibilidade do limite de gastos](https://azure.microsoft.com/support/legal/offer-details/).

No entanto, se você atingir o limite de gastos, os serviços serão desabilitados. Isso significa que suas VMs serão desalocadas. Para evitar o tempo de inatividade do serviço, você deve desabilitar o limite de gastos. O excedente é cobrado em seu cartão de crédito cadastrado. 

Para ver se você já tem limite de gastos ativado, vá para o [modo de exibição Assinaturas no Centro de Contas](https://account.windowsazure.com/Subscriptions). Uma faixa será exibida se o limite de gastos estiver ativo:

![Captura de tela que mostra um aviso sobre a ativação do limite de gastos no Centro de Contas](./media/billing-getting-started/spending-limit-banner.PNG)

Clique na faixa e siga os prompts para remover o limite de gastos. Se você não inseriu informações de cartão de crédito na inscrição, deverá inseri-las para remover o limite de gastos. Para saber mais, confira [Limite de gastos do Azure – Como funciona e como habilitá-lo ou removê-lo](https://azure.microsoft.com/pricing/spending-limits/).

## <a name="ways-to-monitor-your-costs-when-using-azure-services"></a>Maneiras de monitorar os custos ao usar os serviços do Azure

### <a name="tags"></a>Adicionar marcas aos seus recursos para agrupar os dados de cobrança

Você pode usar marcas para agrupar os dados de cobrança no caso de serviços com suporte. Por exemplo, se você executa várias VMs para equipes diferentes, pode usar marcas para categorizar os custos por centro de custo (RH, marketing, finanças) ou ambiente (pré-produção, produção, teste). 

![Captura de tela que mostra a configuração de marcas no portal](./media/billing-getting-started/tags.PNG)

As marcas aparecem em vários modos de exibição de relatório diferentes. Por exemplo, elas são visíveis logo no [modo de exibição de análise de custo](#costs) e no [. csv de uso detalhado](#invoice-and-usage) após o primeiro período de cobrança.

Para obter mais informações, veja [Usando marcas para organizar os recursos do Azure](../azure-resource-manager/resource-group-using-tags.md).

### <a name="costs"></a>Verifique regularmente no portal o detalhamento de custos e a taxa de gravação

Depois de colocar seus serviços em funcionamento, verifique regularmente quanto eles estão custando. Você pode ver o gasto atual e a taxa de gravação no portal do Azure. 

1. Visite a [folha de Assinaturas no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selecione uma assinatura.

2. Você deve ver o detalhamento de custo e a taxa de gravação na folha pop-up. Ele pode não ter suporte da sua oferta (um aviso seria exibido na parte superior).

    ![Captura de tela da taxa de gravação e do detalhamento no portal do Azure](./media/billing-getting-started/burn-rate.PNG)

3. Clique em **Análise de custo** na lista à esquerda para ver a análise de custo por recurso. Aguarde 24 horas depois de adicionar um serviço de dados para preenchê-lo.

    ![Captura de tela do modo de exibição de análise de custo no portal do Azure](./media/billing-getting-started/cost-analysis.PNG)

4. Você pode filtrar por propriedades diferentes, como [marcas](#tags), grupo de recursos e período de tempo. Clique em **Aplicar** para confirmar os filtros e em **Baixar** se quiser exportar o modo de exibição para um arquivo .csv (valores separado por vírgulas).

5. Além disso, clique em um recurso para ver o histórico de gastos diário e quanto o recurso custa por dia.

    ![Captura de tela da exibição do histórico de gastos no portal do Azure](./media/billing-getting-started/costhistory.PNG)

Recomendamos que você verifique os custos, consulte as estimativas vistas quando selecionou os serviços. Se os custos diferem bastante das estimativas, verifique novamente o plano de preços (VMs A1 x A0, por exemplo) que você selecionou para os seus recursos. 

### <a name="consider-enabling-cost-cutting-features-like-auto-shutdown-for-vms"></a>Considere a possibilidade de habilitar recursos de redução de custos, como desligamento automático de VMs

Dependendo do cenário, você pode configurar o desligamento automático para suas VMs no portal do Azure. Para saber mais, confira [Desligamento automático de máquinas virtuais usando o Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Captura de tela da opção de desligamento automático no portal](./media/billing-getting-started/auto-shutdown.PNG)

O desligamento automático não é igual a desligar a VM com as opções de energia padrão. O desligamento automático interrompe e desaloca suas VMs para parar as cobranças adicionais. Para saber mais, confira os estados de VM nas Perguntas frequentes sobre preços de [VMs Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [VMs Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).

Para obter mais recursos de redução de custos para os ambientes de desenvolvimento e teste, confira [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-check-out-azure-advisor-recommendations"></a>Habilitar e conferir as recomendações do Azure Advisor

[O Azure Advisor](../advisor/advisor-overview.md) é um recurso de visualização que ajuda a reduzir os custos identificando recursos com baixa utilização. Habilite-o no portal do Azure:

![Captura de tela do botão Azure Advisor no portal do Azure](./media/billing-getting-started/advisor-button.PNG)

Em seguida, você pode obter recomendações práticas na guia **Custo** no painel do Advisor:

![Captura de tela de exemplo de recomendação de custo do Advisor](./media/billing-getting-started/advisor-action.PNG)

Para saber mais, veja [Advisor Cost recommendations](../advisor/advisor-cost-recommendations.md) (Recomendações de custo do Advisor).

## <a name="reviewing-costs-at-the-end-of-your-billing-cycle"></a>Examinando os custos ao final do ciclo de cobrança

Após o término do ciclo de cobrança, sua fatura estará disponível. Você também pode [baixar faturas anteriores e arquivos de detalhes de uso](billing-download-azure-invoice-daily-usage-date.md) para confirmar se foi cobrado corretamente. Para obter mais informações sobre como comparar seu uso diário com sua fatura, consulte [Entenda sua fatura do Microsoft Azure](billing-understand-your-bill.md).

### <a name="billing-api"></a>API de Cobrança

Use nossa API de Cobrança para obter dados de uso programaticamente. Use a API RateCard junto com a API de Uso para obter seu uso cobrado. Para saber mais, confira [Obtenha informações sobre o consumo de recursos do Microsoft Azure](billing-usage-rate-card-overview.md).

## <a name="other-offers"></a> Casos especiais e recursos adicionais

### <a name="ea-csp-and-sponsorship-customers"></a>Clientes do EA, CSP e Sponsorship
Fale com seu gerente de conta ou parceiro do Azure para começar.

| Oferta | Recursos |
|-------------------------------|-----------------------------------------------------------------------------------|
| EA (Enterprise Agreement) | [Portal EA](https://ea.azure.com/), [documentos de ajuda](https://ea.azure.com/helpdocs) e [relatório do Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Programa do CSP (Provedor de Soluções na Nuvem) | Fale com seu provedor |
| Azure Sponsorship | [Portal do Sponsorship](https://www.microsoftazuresponsorships.com/) |

Se estiver gerenciando TI em uma grande organização, recomendamos a leitura de [Azure enterprise scaffold](../azure-resource-manager/resource-manager-subscription-governance.md) e [enterprise IT white paper](http://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (baixe o .pdf, somente em inglês).

#### <a name="EA"></a> Visualizações de custo Contrato Enterprise de Versão Prévia no Portal do Azure 

As visualizações de custo Enterprise estão, no momento, em Visualização Pública. Itens a serem observados:
- Os custos de assinatura são baseados no uso e não contam para valores pré-pagos, excedentes, quantidades incluídas, ajustes e impostos. Os encargos reais são calculados no nível de registro. 
- As quantidades exibidas no Portal do Azure podem ser atrasadas em comparação com os valores no Enterprise Portal.  
- Se você não estiver vendo os custos, pode ser devido a um dos seguintes motivos:
    - Você é um Proprietário de Conta e seu Administrador de registro desabilitou a configuração “Encargos de exibição do AO”.  Contate o Administrador de registro para obter acesso aos custos. 
    - Você é um Administrador de Departamento e seu Administrador de registro desabilitou a configuração “Encargos de exibição do DA”.  Contate o Administrador de registro para obter acesso. 
    - Você adquiriu o Azure por meio de um parceiro de canal e o parceiro não liberou as informações de preços.  
- Quando as configurações relacionadas ao acesso de custo são atualizadas no Enterprise Portal, há um atraso de alguns minutos antes que as alterações sejam refletidas no Portal do Azure.
- Limite de gastos, alertas de cobrança e diretrizes de fatura não pertencem às Assinaturas EA.

### <a name="check-your-subscription-and-access"></a>Verificar assinatura e acesso

A exibição dos custos exige [acesso no nível das assinaturas a informações de cobrança](billing-manage-access.md), mas somente o Administrador da conta pode acessar o [Centro de Contas](https://account.azure.com/Subscriptions), alterar informações de cobrança e gerenciar assinaturas. O Administrador da conta é a pessoa que passou pelo processo de inscrição. Para gerenciar essas funções, consulte [Adicionar ou alterar as funções de administrador do Azure que gerenciam a assinatura ou serviços](billing-add-change-azure-subscription-administrator.md).

Para ver se você é o Administrador da conta, vá para a [folha Assinaturas no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e examine a lista de assinaturas a que você tem acesso. Procure em **Minha função**. Se estiver indicado *Administrador de conta*, tudo bem. Se estiver indicado *Proprietário*, você não tem todos os privilégios.

![Captura de tela de sua função no modo de exibição Assinaturas no portal do Azure](./media/billing-getting-started/sub-blade-view.PNG)

Se você não for o Administrador da conta, alguém provavelmente lhe deu acesso parcial pelo [RBAC (Controle de Acesso Baseado em Função) do Azure Active Directory](../active-directory/role-based-access-control-configure.md). Para gerenciar assinaturas e alterar informações de cobrança, [localize o Administrador da conta](billing-subscription-transfer.md#whoisaa) e peça-lhe para executar as tarefas ou [transferir a assinatura para você](billing-subscription-transfer.md).

Se o Administrador da conta não está mais na sua organização e você precisa gerenciar a cobrança, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). 
## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se precisar de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
