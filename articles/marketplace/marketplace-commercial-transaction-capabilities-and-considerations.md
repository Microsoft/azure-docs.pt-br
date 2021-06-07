---
title: Recursos de transação do marketplace comercial da Microsoft
description: Este artigo descreve as considerações de preços, cobrança, faturamento e pagamento para a opção de transação do marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2021
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 0a25e1b50455cad5bdbe5b76b2a291f2a1c11940
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106999"
---
# <a name="commercial-marketplace-transact-capabilities"></a>Recursos de transação do marketplace comercial

Este artigo descreve as considerações de preços, cobrança, faturamento e pagamento do marketplace comercial da Microsoft.

## <a name="transactions-by-listing-option"></a>Opção de transações por listagem

O editor ou a Microsoft é responsável por gerenciar transações de licença de software para ofertas no marketplace comercial. A opção de publicação que você escolhe para a oferta determina quem gerencia a transação. Veja a disponibilidade e as explicações de cada opção de publicação em [Introdução às opções de listagem](determine-your-listing-type.md)

### <a name="contact-me-free-trial-and-byol-options"></a>Entrar em contato comigo, avaliação gratuita e opções de BYOL

Os editores podem escolher as opções _Entrar em contato comigo_ e _Avaliação gratuita_ para fins de promoções e aquisição de usuários. Em alguns tipos de oferta, os editores podem escolher a opção _BYOL (traga sua própria licença)_ , permitindo que os clientes adquiram uma assinatura da oferta por meio de uma licença comprada diretamente de você. Com essas opções, a Microsoft não participa diretamente das transações de licença de software do editor e não há nenhum valor de transação associado, portanto, os editores ficam com a receita.

Nesse caso, os editores são responsáveis por dar suporte para todos os aspectos da transação de licença de software. Isso inclui pedido, cumprimento, medição, cobrança, faturamento e pagamento, entre outros. Com a opção de listagem Entre em contato comigo, os editores mantêm 100% dos valores de licenciamento de software cobrados aos clientes.

### <a name="transact-publishing-option"></a>Opção de publicação de transação

Ao escolher vender por meio da Microsoft, você aproveita os recursos comerciais da nossa empresa e tem uma experiência completa, desde a descoberta e a avaliação até a compra e a implementação. Uma oferta _transacionável_ é aquela na qual a Microsoft facilita a troca de dinheiro por uma licença de software em nome do editor. As ofertas de transação são cobradas por meio de uma assinatura da Microsoft ou um cartão de crédito, permitindo que a Microsoft hospede transações do marketplace na nuvem em nome do editor.

Escolha a opção de transação ao criar uma nova oferta no Partner Center. Esta opção será mostrada somente se a transação estiver disponível para o tipo de oferta.

## <a name="transact-overview"></a>Visão geral da transação

Com a opção de transação, a Microsoft permite a venda de software de terceiros e a implantação de alguns tipos de oferta na assinatura do Azure do cliente. O editor deve considerar a cobrança de valores de infraestrutura e seus próprios valores de licenciamento de software ao selecionar um modelo de preço para a oferta.

Atualmente, a opção de publicação de transação tem suporte para os seguintes tipos de oferta:

| Tipo de oferta | Cadência de cobrança | Cobrança medida | Modelo de preços |
| ------------ | ------------- | ------------- | ------------- |
| Aplicativo do Azure<br>(Aplicativo gerenciado) | Mensal | Sim | Baseado em uso |
| Máquina Virtual do Azure | Mensalmente * | Não | Baseado em uso, BYOL |
| SaaS (software como serviço) | Mensal e anual | Sim | Preço fixo, por usuário, baseado no uso. |
|||||

`*` A Máquina Virtual do Azure oferece suporte a planos de cobrança com base no uso. A cobrança desses planos é feita mensalmente pelas horas de uso da assinatura, baseado no uso por núcleo, por tamanho dos núcleos ou por mercado e tamanho dos núcleos.

### <a name="metered-billing"></a>Cobrança medida

O _serviço de medição do Marketplace_ permite especificar preços pagos conforme o uso (por consumo) além dos preços mensais ou anuais incluídos no contrato (direito). Você pode basear a cobrança em dimensões do serviço de medição do marketplace especificadas por você, como largura de banda, tíquetes ou emails processados. Veja mais informações sobre a cobrança limitada para ofertas de SaaS em [Cobrança limitada para SaaS usando o serviço de medição do marketplace comercial](./partner-center-portal/saas-metered-billing.md). Veja mais informações sobre a cobrança limitada para ofertas de Aplicativo Azure em [Cobrança limitada de aplicativo gerenciado](./partner-center-portal/azure-app-metered-billing.md).

### <a name="billing-infrastructure-costs"></a>Custos de infra-estrutura de faturamento

Para **máquinas virtuais** e **aplicativos do Azure,** os valores de uso de infraestrutura do Azure são cobrados na assinatura do Azure do cliente. Os valores de uso da infraestrutura são precificados e apresentados separadamente dos valores de licenciamento do provedor de software na fatura do cliente.

Para **aplicativos SaaS**, o editor deve contabilizar os valores de uso da infraestrutura do Azure e os valores de licenciamento de software como um item de custo único. Ele é representado como um valor fixo para o cliente. O uso da infraestrutura do Azure é gerenciado e faturado diretamente para o editor. Os valores reais de uso da infraestrutura não são vistos pelo cliente. Em geral, os editores optam por incluir os valores de uso da infraestrutura do Azure em seus preços de licença de software. Os valores de licenciamento de software não são medidos ou baseados no consumo do usuário.

## <a name="pricing-models"></a>Modelos de preço

Dependendo da opção de transação usada, os preços da assinatura são os seguintes:

- **Obtenha agora (gratuito)** : sem custo para as licenças de software. Não é possível converter ofertas gratuitas em ofertas pagas. Os clientes devem solicitar uma oferta paga.
- **BYOL (Traga sua própria licença)** : se uma oferta for listada no marketplace comercial, os preços aplicáveis das licenças de software serão gerenciados diretamente entre o editor e o cliente. A Microsoft cobra apenas o valor aplicável do uso da infraestrutura do Azure na conta da assinatura do Azure do cliente.
- **Preços de assinatura**: os valores de licença de software são apresentados como uma taxa de recorrência mensal ou anual, cobrada como uma assinatura com valor fixo ou por estação. Não há cobrança proporcional dos valores mensais recorrentes de assinatura caso o cliente cancele durante o período ou não use os serviços. Pode haver cobrança proporcional dos valores recorrentes caso o cliente faça upgrade ou downgrade da assinatura durante o período.
- **Preços baseados no uso**: para ofertas de máquina virtual do Azure, a cobrança é feita com base na extensão do uso da oferta pelo cliente. Para imagens de máquina virtual, há a cobrança de um valor do Azure Marketplace por hora, definido pelo editor, pelo uso das máquinas virtuais implantadas usando as imagens de VM. O valor por hora pode ser uniforme ou variado entre vários tamanhos de máquina virtual. As horas parciais são cobradas por minuto. A cobrança dos planos é mensal.
- **Preços limitados**: para ofertas de Aplicativo Azure e de SaaS, os editores podem usar o [serviço de medição do Marketplace](./partner-center-portal/marketplace-metering-service-apis.md) para cobrar pelo consumo com base nas dimensões de medidor personalizadas que eles configuram. Essas alterações são além dos preços mensais ou anuais incluídos no contrato (direito). Exemplos de dimensões de medidor personalizadas são largura de banda, tíquetes ou emails processados. Os editores podem definir uma ou mais dimensões limitadas para cada plano, com o máximo de 30 por oferta. Os editores são responsáveis por acompanhar o uso de cada cliente, com cada medidor definido pela oferta. Os eventos devem ser comunicados à Microsoft em uma hora. A Microsoft cobra os clientes com base nas informações de uso relatadas pelos editores para o período de cobrança aplicável.
- **Avaliação gratuita**: nenhum preço por licenças de software que variam de 30 dias até seis meses, dependendo do tipo de oferta. Quando os editores oferecem uma avaliação gratuita em mais de um plano na mesma oferta, os clientes podem alternar para uma avaliação gratuita em outro plano, mas o período de avaliação não é reiniciado. Para ofertas de máquina virtual, há cobrança aos clientes pelos custos de infraestrutura do Azure para o uso da oferta durante o período de avaliação. Ao fim do período de avaliação, os clientes recebem automaticamente uma cobrança referente ao valor padrão do último plano que eles avaliaram, a menos que cancelem antes do fim da avaliação.

> [!NOTE]
> Ofertas que são cobradas de acordo com o consumo depois que uma solução é usada não se qualificam para reembolsos.

Para alterar o valor de uso de uma oferta, os editores devem primeiro remover a oferta (ou um plano dentro dela) do marketplace comercial. A remoção deve ser feita de acordo com os requisitos do [Contrato de Distribuidor da Microsoft](https://go.microsoft.com/fwlink/?LinkID=699560). Depois o editor pode publicar uma nova oferta (ou um plano dentro dela) com os novos valores de uso. Veja mais informações sobre a remoção de ofertas ou planos em [Parar de vender uma oferta ou um plano](./partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).

### <a name="free-contact-me-and-bring-your-own-license-byol-pricing"></a>Preços gratuitos, de Entre em contato comigo e de BYOL (traga sua própria licença)

Quando o editor publica uma oferta com a opção Obter agora (gratuito), Entrar em contato comigo ou BYOL, a Microsoft não atua na intermediação da venda dos valores de licença de software. O editor mantém 100% dos valores de licença de software.

### <a name="usage-based-and-subscription-pricing"></a>Preço com base no uso e assinatura

Quando o editor publica a oferta como uma transação baseada no uso ou uma assinatura, a Microsoft oferece a tecnologia e os serviços para processar compras, devoluções e estornos de licenças de software. Nesse cenário, o editor autoriza a Microsoft a agir como um agente para essas finalidades. O editor autoriza a Microsoft a intermediar a transação de licenciamento de software. O editor mantém a designação de vendedor, provedor, distribuidor e licenciante.

A Microsoft permite aos clientes solicitar, licenciar e usar seu software, sujeito aos termos e condições do marketplace comercial da Microsoft e do seu contrato de licenciamento de usuário final. Você deve incluir um contrato próprio de licenciamento de usuário final ou selecionar o [Contrato Standard](./standard-contract.md) ao criar a oferta.

### <a name="free-software-trials"></a>Avaliações de software livre

Em cenários de publicação de transações, você pode disponibilizar uma licença de software gratuitamente por 30 ou 120 dias, dependendo da assinatura. Os clientes deverão pagar pelo uso da infraestrutura do Azure em questão.

### <a name="examples-of-pricing-and-store-fees"></a>Exemplos de preços e valores de armazenamento

**Baseado em uso**

Os preços baseados no uso têm a seguinte estrutura de custo:

| **Custo de sua licença** | **US $ 1,00 por hora** |
|---------|---------|
| Custo de uso do Azure (D1/1-Núcleo) | US $ 0,14 por hora |
| _O cliente é cobrado pela Microsoft_ | _US $ 1,14 por hora_ |
||

Nesse cenário, a Microsoft fatura US$ 1,14 por hora pelo uso da sua imagem de VM publicada.

| **Faturas da Microsoft** | **US $ 1,14 por hora**  |
|---------|---------|
| A Microsoft paga para você 80% do seu custo de licença | US $ 0,80 por hora |
| A Microsoft mantém 20% do seu custo de licença  |  US $ 0,20 por hora |
| A Microsoft mantém a 100% do custo de uso do Azure | US $ 0,14 por hora |
||

**Traga sua própria licença (BYOL)**

BYOL tem a seguinte estrutura:

| **Custo de sua licença** | **Valor de licença negociada e faturada por você** |
|---------|---------|
|Custo de uso do Azure (D1/1-Núcleo)    |   US $ 0,14 por hora     |
| _O cliente é cobrado pela Microsoft_ | _US $ 0,14 por hora_ |
||

Nesse cenário, a Microsoft fatura US$ 0,14 por hora pelo uso da sua imagem de VM publicada.

| **Faturas da Microsoft** | **US $ 0,14 por hora** |
|---------|---------|
| A Microsoft mantém o custo de uso do Azure | US $ 0,14 por hora |
| A Microsoft mantém 0% do seu custo de licença | US $ 0.00 por hora |
||

**Assinatura do aplicativo SaaS**

O preço das assinaturas SaaS pode ser fixo ou por usuário, mensal ou anual. Se você habilitar a opção **Vender pela Microsoft** para uma oferta de SaaS, terá a seguinte estrutura de custo:

| **Custo de sua licença** | **US $100,00 por mês** |
|--------------|---------|
| Custo de uso do Azure (D1/1-Núcleo) | Cobrados diretamente para o publicador, não o cliente |
| _O cliente é cobrado pela Microsoft_ | _US$ 100,00 por mês (o editor precisa contabilizar os custos incorridos ou de infraestrutura de passagem no valor da licença)_ |
||

Nessa situação, a Microsoft cobra US$ 100,00 pela sua licença de software e paga US$ 80,00 ou US$ 90,00 a você, dependendo de a oferta ser qualificada para redução do valor do serviço de armazenamento.

| **Faturas da Microsoft** | **US $100,00 por mês** |
|---------|---------|
| A Microsoft paga para você 80% do seu custo de licença <br> \* A Microsoft paga 90% do seu custo de licença para qualquer aplicativo SaaS qualificado | US $80,00 por mês <br> \* US $90,00 por mês |
| A Microsoft mantém 20% do seu custo de licença <br> \* A Microsoft mantém 10% do seu custo de licença para qualquer aplicativo SaaS qualificado. | US $20,00 por mês <br> \* US $10,00 |

### <a name="commercial-marketplace-service-fees"></a>Valores de serviço do marketplace comercial

Cobramos o valor de serviço de armazenamento padrão, 20%, quando os clientes compram uma oferta de transação no marketplace comercial. Veja mais detalhes sobre o valor na Seção 5C do [Contrato de Distribuidor da Microsoft](https://go.microsoft.com/fwlink/?LinkID=699560).

Para determinadas ofertas de transação que publica no marketplace comercial, você pode se qualificar para receber uma redução de 10% no valor do serviço de armazenamento. Para receber o desconto, a oferta precisa ser designada pela Microsoft como _Incentivo à venda conjunta de IP do Azure_. É necessário cumprir os requisitos ao menos cinco dias úteis antes do fim do mês calendário para receber a redução no valor do serviço do marketplace. Após o preenchimento dos requisitos, todas as transações receberão a redução no valor do serviço a partir do primeiro dia do mês seguinte, até a perda do status de _Incentivo à venda conjunta de IP do Azure_. Veja mais detalhes sobre a qualificação para venda conjunta de IP em [Requisitos para o status de venda conjunta](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status).

A redução no valor do serviço do marketplace vale para SaaS, VMs, aplicativos gerenciados e outras soluções de IaaS transacionáveis com incentivo para venda conjunta de IP do Azure disponibilizadas por meio do marketplace comercial. As ofertas de SaaS pagas associadas a um aplicativo Microsoft Teams ou pelo menos dois suplementos Microsoft 365 (Excel, PowerPoint, Word, Outlook e SharePoint) e publicadas no Microsoft AppSource também podem receber o desconto.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Faturamento, pagamento, faturamento e cobrança do cliente

**Faturamento e pagamento**: você pode usar o método de faturamento preferencial do cliente para entregar os valores de licença de software de assinatura ou [PAYGO](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/).

**Contrato Enterprise**: se o método de faturamento preferencial do cliente for o Contrato Enterprise da Microsoft, os valores de licença de software serão cobrados usando esse método de faturamento como um custo discriminado, separado dos custos de uso específicos do Azure.

**Cartões de crédito e fatura mensal**: os clientes podem pagar usando um cartão de crédito e uma fatura mensal. Nesse caso, os valores de licença de software serão cobrados da mesma forma que o cenário do Enterprise Agreement, como um custo discriminado, separado dos custos de uso específicos do Azure.

**Créditos gratuitos e compromisso monetário**: alguns clientes optam por pagar antecipadamente o Azure com um compromisso monetário no Contrato Enterprise ou receberam créditos gratuitos para uso com a utilização do Azure. Embora esses créditos possam ser usados para pagar pelo uso do Azure, eles não podem ser usados para pagar valores de licença de software do editor.

**Faturamento e coleções**: o faturamento da licença do software do editor é apresentado usando o método de faturamento selecionado pelo cliente e segue a linha do tempo do faturamento. Clientes sem um Contrato Enterprise em vigor são cobrados mensalmente pelas licenças de software do mercado. Os clientes com um Contrato Enterprise são faturados mensalmente por meio de uma fatura apresentada trimestralmente.

Quando modelos de preços de assinatura ou de pagamento conforme o uso (também chamado de baseado no uso) são selecionados, a Microsoft atua como agente do editor e é responsável por todos os aspectos de faturamento, pagamento e cobrança.

### <a name="publisher-payout-and-reporting"></a>Pagamento e relatórios do editor

Quaisquer valores de licenciamento de software coletados pela Microsoft como um agente estão sujeitas a um valor de transação de 20%, a menos que seja especificado de outra forma e sejam deduzidas no momento do pagamento do editor.

Os clientes normalmente compram usando o Enterprise Agreement ou um contrato de pagamento por utilização habilitado para cartão de crédito. O tipo de contrato determina o tempo de cobrança, faturamento, cobrança e pagamento.

>[!NOTE]
>Todos os relatórios e insights sobre a opção de publicação da transação estão disponíveis na seção Análise do Partner Center.

#### <a name="billing-questions-and-support"></a>Perguntas sobre cobrança e suporte

Veja mais informações e políticas legais no [Contrato de Distribuidor da Microsoft](https://go.microsoft.com/fwlink/?LinkID=699560). Para obter ajuda sobre dúvidas de cobrança, entre em contato com o [suporte do editor do marketplace comercial](https://aka.ms/marketplacepublishersupport).

## <a name="transact-requirements"></a>Transact requisitos

Esta seção aborda os requisitos de transação para diferentes tipos de oferta.

### <a name="requirements-for-all-offer-types"></a>Requisitos para todos os tipos de oferta

- Uma conta da Microsoft e informações financeiras são necessárias para a opção de publicação de transações, independentemente do modelo de preços da oferta.
- As informações financeiras obrigatórias incluem a conta de pagamento e o perfil fiscal.

Para obter mais informações sobre como configurar essas contas, consulte [Gerenciar sua conta do marketplace comercial no Partner Center](manage-account.md).

### <a name="requirements-for-specific-offer-types"></a>Requisitos para tipos de oferta específica

A transação por meio da Microsoft está disponível apenas para os seguintes tipos de ofertas no marketplace comercial. Esta lista contém os requisitos para tornar esses tipos de oferta transacionáveis no marketplace comercial.

- **Aplicativo do Azure (planos de modelo de solução e de aplicativo gerenciado)** : em alguns casos, os valores de uso da infraestrutura do Azure são passados ao cliente separadamente dos valores de licença de software, mas no mesmo demonstrativo de faturamento. No entanto, se você configurar um plano de aplicativo gerenciado para preços de infraestrutura do ISV, os recursos do Azure serão cobrados ao editor, e o cliente receberá um valor fixo que inclui o custo de infraestrutura, as licenças de software e os serviços de gerenciamento.

- **Máquina virtual do Azure**: selecione entre os modelos de preços gratuito, BYOL ou baseado no uso. Na fatura do cliente do Azure, a Microsoft apresenta os valores de licença de software do editor separadamente dos valores de infraestrutura subjacentes do Azure. Os valores de infraestrutura do Azure são determinados pelo uso do software do editor.

- **Aplicativo SaaS**: deve ser uma solução multilocatário, usar o [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) para autenticação e integrar-se com as [APIs de cumprimento de SaaS](partner-center-portal/pc-saas-fulfillment-api-v2.md). O uso da infraestrutura do Azure é gerenciado e cobrado diretamente a você (o editor); portanto, você deve considerar os valores de uso da infraestrutura do Azure e os valores de licenciamento de software como um único item de custo. Veja as diretrizes detalhadas em [Como planejar uma oferta de SaaS para o marketplace comercial](plan-saas-offer.md#plans).

## <a name="private-plans"></a>Planos privados

Você pode criar um plano privado para uma oferta, incluindo preços negociados específicos do negócio ou configurações personalizadas.

Com os planos privados, você pode oferecer preços maiores ou menores que o da oferta pública para determinados clientes. Os planos privados podem ser usados para desconto ou adição de uma oferta premium. Para disponibilizar os planos privados a um ou mais clientes, liste a assinatura do Azure deles no nível do plano.

## <a name="next-steps"></a>Próximas etapas

- Examine os padrões de publicação por loja online para ver exemplos da associação entre sua solução e uma configuração e tipo de oferta.
- [Guia de publicação por tipo de oferta](publisher-guide-by-offer-type.md).
