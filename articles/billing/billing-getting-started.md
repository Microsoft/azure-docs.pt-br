---
title: Evitar custos inesperados, gerenciar o faturamento no Azure | Microsoft Docs
description: Saiba como evitar encargos inesperados em sua conta do Azure. Use recursos de gerenciamento e controle de custos para uma assinatura do Microsoft Azure.
services: ''
documentationcenter: ''
author: bandersmsft
manager: alherz
editor: ''
tags: billing
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2018
ms.author: banders
ms.openlocfilehash: d215e2ba5d650b532b9d7554ccca9ad5537cf4de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60919211"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Evite cobranças inesperadas com o gerenciamento de custo e a cobrança do Azure

Quando você se inscreve para o Azure, há várias coisas que você pode fazer para obter uma ideia melhor de seus gastos. A [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) pode fornecer uma estimativa de custos antes de você criar um recurso do Azure. O [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) fornece uma previsão e a análise de custos atual de sua assinatura. Se desejar agrupar e entender os custos de diferentes projetos ou equipes, examine a [marcação de recursos](../azure-resource-manager/resource-group-using-tags.md). Se sua organização tiver um sistema de relatórios que você prefira usar, confira as [APIs de cobrança](billing-usage-rate-card-overview.md).

- Se sua assinatura for um EA (Contrato Enterprise), a visualização pública para visualização dos custos no portal do Azure agora estará disponível. Se sua assinatura for feita por meio do CSP (Provedor de Soluções na Nuvem) ou pelo Azure Sponsorship, alguns dos recursos a seguir poderão não se aplicar ao seu caso. Consulte [Recursos adicionais do EA, CSP e Sponsorship](#other-offers) para obter mais informações.

- Se sua assinatura for uma Avaliação Gratuita, do [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), do AIO (Azure via Open) ou BizSpark, ela será desabilitada automaticamente quando todos os créditos forem usados. Saiba mais sobre os [limites de gastos](#spending-limit) para evitar que sua assinatura desabilitada de forma inesperada.

- Se tiver se inscrito na [Conta gratuita do Azure](https://azure.microsoft.com/free/), [você poderá usar alguns dos serviços do Azure mais populares gratuitamente por 12 meses](billing-create-free-services-included-free-account.md). Juntamente com as recomendações listadas abaixo, consulte [Evitar ser cobrado na conta gratuita](billing-avoid-charges-free-account.md).

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

### <a name="spending-limit"></a>Verifique se você tem um limite de gastos ativado

Se você tiver uma assinatura que usa crédito, o limite de gastos é ativado para você por padrão. Dessa forma, quando você gasta todo o seu crédito, não há cobrança a mais no seu cartão de crédito. Consulte a [lista completa de ofertas do Azure e a disponibilidade do limite de gastos](https://azure.microsoft.com/support/legal/offer-details/).

No entanto, se você atingir o limite de gastos, os serviços serão desabilitados. Isso significa que suas VMs serão desalocadas. Para evitar o tempo de inatividade do serviço, você deve desabilitar o limite de gastos. O excedente é cobrado em seu cartão de crédito cadastrado. 

Para ver se você já tem limite de gastos ativado, vá para o [modo de exibição Assinaturas no Centro de Contas](https://account.windowsazure.com/Subscriptions). Uma faixa será exibida se o limite de gastos estiver ativo:

![Captura de tela que mostra um aviso sobre a ativação do limite de gastos no Centro de Contas](./media/billing-getting-started/spending-limit-banner.PNG)

Clique na faixa e siga os prompts para remover o limite de gastos. Se você não inseriu informações de cartão de crédito na inscrição, deverá inseri-las para remover o limite de gastos. Para saber mais, confira [Limite de gastos do Azure – Como funciona e como habilitá-lo ou removê-lo](https://azure.microsoft.com/pricing/spending-limits/).

Você pode usar o serviço [Cloudyn](https://www.cloudyn.com/) para criar alertas que notificam os stakeholders automaticamente sobre anomalias de gastos ou risco de gastos em excesso. Você pode criar alertas usando relatórios que dão suporte para alertas com base no orçamento e nos limites de custo. Para obter mais informações sobre como usar Cloudyn, confira [Tutorial: examinar o uso e os custos](../cost-management/tutorial-review-usage.md).

Este exemplo usa o relatório **Custo real ao longo do tempo** para enviar uma notificação quando os gastos em uma VM do Azure se aproximarem do seu orçamento total. Nesse cenário, você tem um orçamento total de US$ 20.000 e deseja receber uma notificação quando os custos estiverem se aproximando da metade de seu orçamento, US$ 9.000, e um alerta adicional quando os custos atingirem US$ 10.000.

1. No menu na parte superior do portal do Cloudyn, selecione **Custos** > **Análise de custo** > **Custo real ao longo do tempo**. 
2. Defina **Grupos** para **Serviço** e defina **Filtrar no serviço** para **Azure/VM**. 
3. Na parte superior direita do relatório, selecione **Ações** e selecione **Agendar relatório**.
4. Para enviar um email a si mesmo com o relatório a intervalos programados, selecione a guia **Agendamento** no diálogo **Salvar ou Agendar este relatório**. Selecione **Enviar por email**. As marcas, o agrupamento e a filtragem que você usar serão incluídos no relatório por email. 
5. Selecione a guia **Limite** e selecione **Custo real versus Limite**. 
   1. Na caixa de limite **Alerta vermelho**, insira 10000. 
   2. Na caixa de limite **Alerta amarela**, insira 9000. 
   3. Na caixa **Número de alertas consecutivos**, digite o número de alertas consecutivos a receber. Quando você receber o número total de alertas especificado, não será enviado mais nenhum alerta. 
6. Clique em **Salvar**.

    ![Exemplo mostrando alertas amarelos e vermelhos com base nos limites de gastos](./media/billing-getting-started/schedule-alert01.png)

Você também pode escolher a métrica de limite **Percentual de Custo versus Orçamento** para criar alertas. Isso permite que você especifique os limites como porcentagens do orçamento, em vez de valores de moeda.

## <a name="ways-to-monitor-your-costs-when-using-azure-services"></a>Maneiras de monitorar os custos ao usar os serviços do Azure

### <a name="tags"></a>Adicionar marcas aos seus recursos para agrupar os dados de cobrança

Você pode usar marcas para agrupar os dados de cobrança no caso de serviços com suporte. Por exemplo, se você executa várias VMs para equipes diferentes, pode usar marcas para categorizar os custos por centro de custo (RH, marketing, finanças) ou ambiente (pré-produção, produção, teste). 

![Captura de tela que mostra a configuração de marcas no portal](./media/billing-getting-started/tags.PNG)

As marcas aparecem em vários modos de exibição de relatório diferentes. Por exemplo, elas são visíveis logo no [modo de exibição de análise de custo](#costs) e no .csv de uso detalhado após o primeiro período de cobrança.

Para obter mais informações, veja [Usando marcas para organizar os recursos do Azure](../azure-resource-manager/resource-group-using-tags.md).

### <a name="costs"></a>Verifique regularmente no portal o detalhamento de custos e a taxa de gravação

Depois de colocar seus serviços em funcionamento, verifique regularmente quanto eles estão custando. Você pode ver o gasto atual e a taxa de gravação no portal do Azure.

1. Acesse [Assinaturas no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selecione uma assinatura.

2. Se houver suporte para a sua assinatura, você poderá ver o detalhamento de custo e a taxa de gravação.

    ![Captura de tela da taxa de gravação e do detalhamento no portal do Azure](./media/billing-getting-started/burn-rate.PNG)

3. Clique em **Análise de custo** na lista à esquerda para ver a análise de custo por recurso. Aguarde 24 horas depois de adicionar um serviço de dados para preenchê-lo.

    ![Captura de tela do modo de exibição de análise de custo no portal do Azure](./media/billing-getting-started/cost-analysis.PNG)

4. Você pode filtrar por propriedades diferentes, como [marcas](#tags), grupo de recursos e período de tempo. Clique em **Aplicar** para confirmar os filtros e em **Baixar** se quiser exportar o modo de exibição para um arquivo .csv (valores separado por vírgulas).

5. Além disso, clique em um recurso para ver seu histórico de gastos diário e quanto o recurso custa por dia.

    ![Captura de tela da exibição do histórico de gastos no portal do Azure](./media/billing-getting-started/costhistory.PNG)

Recomendamos verificar os custos que você vê com as estimativas que viu ao selecionar os serviços. Se os custos são totalmente diferentes em relação às estimativas, verifique novamente o plano de preços que você selecionou para os seus recursos.

### <a name="consider-enabling-cost-cutting-features-like-auto-shutdown-for-vms"></a>Considere a possibilidade de habilitar recursos de redução de custos, como desligamento automático de VMs

Dependendo do cenário, você pode configurar o desligamento automático para suas VMs no portal do Azure. Para saber mais, confira [Desligamento automático de máquinas virtuais usando o Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Captura de tela da opção de desligamento automático no portal](./media/billing-getting-started/auto-shutdown.PNG)

O desligamento automático não é igual a desligar a VM com as opções de energia padrão. O desligamento automático interrompe e desaloca suas VMs para parar as cobranças adicionais. Para saber mais, confira os estados de VM nas Perguntas frequentes sobre preços de [VMs Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [VMs Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).

Para obter mais recursos de redução de custos para os ambientes de desenvolvimento e teste, confira [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-check-out-azure-advisor-recommendations"></a>Habilitar e conferir as recomendações do Azure Advisor

[O Assistente do Azure](../advisor/advisor-overview.md) é um recurso que ajuda a reduzir os custos identificando recursos com baixa utilização. Visite o assistente no portal do Azure:

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

Se estiver gerenciando TI em uma grande organização, recomendamos a leitura de [Azure enterprise scaffold](/azure/architecture/cloud-adoption-guide/subscription-governance) e [enterprise IT white paper](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (baixe o .pdf, somente em inglês).

#### <a name="EA"></a> Visualizações de custo Contrato Enterprise de Versão Prévia no portal do Azure 

As visualizações de custo Enterprise estão, no momento, em Visualização Pública. Itens a serem observados:

- Os custos de assinatura são baseados no uso e não incluem valores pré-pagos, excedentes, quantidades incluídas, ajustes e impostos. Os encargos reais são calculados no nível de registro.
- Os valores mostrados no portal do Azure podem ser diferentes daqueles no Enterprise Portal. Atualizações no Enterprise Portal podem levar alguns minutos antes de serem mostradas no portal do Azure.
- Se você não está vendo os custos, isso pode ser por um dos seguintes motivos:
    - Você não tem permissões no nível da assinatura. Para ver as exibições de custo empresarial, você precisa ser um Leitor de Cobrança, Leitor, Colaborador ou Proprietário no nível da assinatura.
    - Você é um Proprietário de Conta e seu Administrador de registro desabilitou a configuração “Encargos de exibição do AO”.  Contate o Administrador de registro para obter acesso aos custos. 
    - Você é um Administrador de Departamento e seu Administrador de registro desabilitou a configuração “Encargos de exibição do DA”.  Entre em contato com o Administrador de registro para obter acesso.
    - Você comprou o Azure por meio de um parceiro de canal e o parceiro não liberou informações sobre preços.  
- Se você atualizar as configurações relacionadas ao custo de acesso no Enterprise Portal, haverá um atraso de alguns minutos antes que as alterações sejam mostradas no portal do Azure.
- Limite de gastos e diretrizes de fatura não se aplicam às assinaturas do Contrato Enterprise.

### <a name="check-your-subscription-and-access"></a>Verificar assinatura e acesso

Para exibir os custos, você deve ter [acesso no nível das assinaturas a informações de cobrança](billing-manage-access.md). Somente o Administrador da Conta pode acessar o [Centro de Contas](https://account.azure.com/Subscriptions), alterar informações de cobrança e gerenciar assinaturas. O Administrador da Conta é a pessoa que passou pelo processo de inscrição. Para gerenciar essas funções, consulte [Adicionar ou alterar as funções de administrador do Azure que gerenciam a assinatura ou serviços](billing-add-change-azure-subscription-administrator.md).

Para ver se você é o administrador da Conta, acesse [Assinaturas no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Examine a lista de assinaturas às quais você tem acesso. Procure em **Minha função**. Se estiver indicado *Administrador de conta*, tudo bem. Se estiver indicado *Proprietário*, você não tem todos os privilégios.

![Captura de tela de sua função no modo de exibição Assinaturas no portal do Azure](./media/billing-getting-started/sub-blade-view.PNG)

Se você não for o Administrador da conta, alguém provavelmente lhe deu acesso parcial usando [RBAC (Controle de Acesso Baseado em Função) do Azure Active Directory](../role-based-access-control/role-assignments-portal.md). Para gerenciar assinaturas e alterar informações de cobrança, [localize o Administrador da Conta](billing-subscription-transfer.md#whoisaa). Peça ao Administrador da Conta para executar as tarefas ou [transferir a assinatura para você](billing-subscription-transfer.md).

Se o administrador da sua conta não estiver mais na sua organização e você precisar gerenciar o faturamento, [entre em contato conosco](https://go.microsoft.com/fwlink/?linkid=2083458).


### <a name="how-to-request-a-service-level-agreement-credit-for-a-service-incident"></a>Como solicitar um crédito de contrato de nível de serviço para um incidente de serviço

O SLA (contrato de nível de serviço) descreve os compromissos da Microsoft com relação ao tempo de atividade e à conectividade. Um incidente de serviço é relatado quando os serviços do Azure enfrentar um problema que o tempo de atividade de impactos ou conectividade, geralmente conhecida como uma "interrupção". Se não podemos alcançar e manter os níveis de serviço para cada serviço, conforme descrito no SLA, em seguida, você pode ser elegível para crédito para uma parte das suas taxas de serviço mensal.

Para solicitar um crédito:

1. Entre no [Portal do Azure](https://portal.azure.com/). Se você tiver várias contas, certifique-se de que você use um que foi afetada por tempo de inatividade do Azure. Isso ajuda a suporte automaticamente coletar as informações necessárias em segundo plano e resolver o caso mais rapidamente.
2. Crie uma nova solicitação de suporte.
3. Sob **tipo de problema**, selecione **cobrança**.
4. Sob **tipo de problema**, selecione **solicitar reembolso**.
5. Adicione detalhes para especificar que você está pedindo para uma crédito de SLA, mencione o data/hora/fuso horário, bem como os serviços afetados (máquinas virtuais, Sites da Web, etc.)
6. Verifique os detalhes do contato e selecione o **criar** botão para enviar sua solicitação.

Os limites de SLA variam de acordo com o serviço. Por exemplo, camada da Web de SQL tem um SLA de 99,9%, as VMs têm um SLA de 99,95% e camada Standard do SQL tem um SLA de 99,99%.

Para alguns serviços, há pré-requisitos para o SLA aplicar. Por exemplo, máquinas virtuais deve ter dois ou mais instâncias implantadas no mesmo conjunto de disponibilidade.

Para obter mais informações, consulte o [contratos de nível de serviço](https://azure.microsoft.com/en-us/support/legal/sla/) documentação e o [resumo SLA para serviços do Azure](https://azure.microsoft.com/en-us/support/legal/sla/summary/) documentação.

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de Ajuda, [criar uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
