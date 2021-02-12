---
title: Recursos de transação do marketplace comercial da Microsoft
description: Este artigo descreve as considerações de preços, cobrança, faturamento e pagamento para a opção de transação do marketplace comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 59768e4fc04613a5ecac6a76059382499b7aaa86
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986455"
---
# <a name="commercial-marketplace-transact-capabilities"></a>Recursos de transação do marketplace comercial

Este artigo descreve as considerações sobre preços, cobrança, faturamento e pagamento para o Marketplace comercial da Microsoft.

## <a name="transactions-by-listing-option"></a>Transações por opção de listagem

O editor ou a Microsoft é responsável por gerenciar transações de licença de software para ofertas no marketplace comercial. A opção de listagem escolhida para sua oferta determina quem gerencia a transação. Para obter disponibilidade e explicações de cada opção de publicação, consulte [introdução às opções de listagem](determine-your-listing-type.md)

### <a name="contact-me-free-trial-and-byol-options"></a>Contate-me, avaliação gratuita e opções de BYOL

Os editores podem escolher o _contato comigo_ e a _avaliação gratuita_, opções para fins de aquisição promocional e de usuário. Para alguns tipos de oferta, os editores podem escolher a opção BYOL (traga sua própria licença) para permitir que os clientes comprem uma assinatura para sua oferta usando uma licença que adquiriu diretamente de você. Com essas opções, a Microsoft não participa diretamente das transações de licença de software do editor e não há nenhum valor de transação associado.

Os Publicadores são responsáveis por dar suporte a todos os aspectos da transação de licença de software. Isso inclui, mas não se limita a ordem, preenchimento, medição, cobrança, faturamento, pagamento e coleção. Com a opção de listagem entre em contato comigo, os editores mantêm 100% das taxas de licenciamento de software do Publicador coletadas do cliente.

### <a name="transact-publishing-option"></a>Opção de publicação de transação

A escolha de vender pela Microsoft aproveita os recursos do Microsoft Commerce e fornece uma experiência de ponta a ponta da descoberta e da avaliação até a compra e a implementação. Uma oferta que é propagada é aquela na qual a Microsoft facilita a troca de dinheiro por uma licença de software em nome do editor. As ofertas transdesejadas são cobradas em relação a uma assinatura da Microsoft existente ou a um cartão de crédito, permitindo que a Microsoft hospede transações do Marketplace de nuvem em nome do Publicador.

Escolha a opção de transação ao criar uma nova oferta no Partner Center. Esta opção será mostrada somente se a transação estiver disponível para seu tipo de oferta.

## <a name="transact-overview"></a>Visão geral da transação

Ao usar a opção Transact, a Microsoft permite a venda de software de terceiros e a implantação de alguns tipos de oferta para a assinatura do Azure do cliente. O editor deve considerar a cobrança de taxas de infraestrutura e suas próprias tarifas de licenciamento de software ao selecionar um modelo de preços para uma oferta.

Atualmente, a opção de publicação de transação tem suporte para os seguintes tipos de oferta:

- Máquinas virtuais
- Aplicativos do Azure
- Aplicativos SaaS

### <a name="billing-infrastructure-costs"></a>Custos de infra-estrutura de faturamento

Para **máquinas virtuais** e **aplicativos do Azure**, as tarifas de uso da infraestrutura do Azure são cobradas na assinatura do Azure do cliente. Os valores de uso da infraestrutura são precificados e apresentados separadamente dos valores de licenciamento do provedor de software na fatura do cliente.

Para **Aplicativos SaaS**, você como editor deve contabilizar os valores de uso da infraestrutura do Azure e os valores de licenciamento de software como um item de custo único.  Ele é representado como um valor fixo para o cliente. O uso da infraestrutura do Azure é gerenciado e cobrado diretamente no Publicador. Os valores reais de uso da infraestrutura não são vistos pelo cliente. Em geral, os editores optam por incluir os valores de uso da infraestrutura do Azure em seus preços de licença de software. As taxas de licenciamento de software não são limitadas ou baseadas no consumo do usuário.

## <a name="pricing-models"></a>Modelos de preço

Dependendo da opção de transação usada, as cobranças de assinatura são as seguintes:

- **Obtenha agora (gratuito)** – sem custos para as licenças de software. Os clientes não são cobrados pelas tarifas do Azure Marketplace para usar uma oferta gratuita. Ofertas gratuitas não podem ser convertidas em uma oferta paga. Os clientes devem solicitar uma oferta paga.
- **Traga sua própria licença** (BYOL) – todas as cobranças aplicáveis por licenças de software são gerenciadas diretamente entre o editor e o cliente. A Microsoft só passa pelos valores de uso da infraestrutura do Azure. Se uma oferta estiver listada no mercado comercial, os clientes que obtiverem acesso ou uso da oferta fora do mercado comercial não serão cobrados pelas tarifas do Marketplace comercial.
- **Preços de assinatura** – as taxas de licença de software são apresentadas como uma taxa de assinatura recorrente mensal ou anual, cobrada como uma taxa fixa ou por estação. As taxas de assinatura recorrente não são rateada para cancelamentos de clientes de médio prazo ou serviços não utilizados. As taxas de assinatura recorrentes podem ser rateada se o cliente atualizar ou fizer downgrade de sua assinatura no meio do período da assinatura.
- **Preço com base no uso** – para ofertas de máquina virtual do Azure, os clientes são cobrados com base na extensão do uso da oferta. Para imagens de máquina virtual, os clientes são cobrados uma taxa por hora do Azure Marketplace, conforme definido pelos publicadores, para uso de máquinas virtuais implantadas a partir das imagens de VM. O valor por hora pode ser uniforme ou variado entre vários tamanhos de máquina virtual. As horas parciais são cobradas por minuto. Os planos são cobrados mensalmente.
- **Preços limitados** – para ofertas de aplicativo Azure e ofertas de SaaS, os editores podem usar o [serviço de medição do Marketplace](./partner-center-portal/marketplace-metering-service-apis.md) para cobrar pelo consumo com base nas dimensões de medidor escolhidas. Por exemplo, largura de banda, tíquetes ou emails processados. Os editores podem definir uma ou mais dimensões de medidor para cada plano. Os editores são responsáveis por acompanhar o uso de clientes individuais, com cada medidor definido na oferta. Os eventos devem ser relatados à Microsoft em uma hora. A Microsoft cobra os clientes com base nas informações de uso relatadas pelos editores para o período de cobrança aplicável.
- **Avaliação gratuita** – sem custos para licenças de software que variam de 30 dias até seis meses, dependendo do tipo de oferta. Se os editores fornecerem uma avaliação gratuita em vários planos na mesma oferta, os clientes poderão alternar para uma avaliação gratuita em outro plano, mas o período de avaliação não será reiniciado. Para ofertas de máquina virtual, os clientes são cobrados pelos custos de infraestrutura do Azure para usar a oferta durante um período de avaliação. Após a expiração do período de avaliação, os clientes são cobrados automaticamente pelo último plano que tentaram com base nas taxas padrão, a menos que sejam cancelados antes do final do período de avaliação.

> [!NOTE]
> Ofertas que são cobradas de acordo com o consumo depois que uma solução é usada não se qualificam para reembolsos.

Os editores que desejam alterar as tarifas de uso associadas a uma oferta, devem primeiro remover a oferta (ou o plano específico dentro da oferta) do Marketplace comercial. A remoção deve ser feita de acordo com os requisitos do [contrato do Microsoft Publisher](https://go.microsoft.com/fwlink/?LinkID=699560). Em seguida, o Publicador pode publicar uma nova oferta (ou um plano dentro de uma oferta) que inclui as novas tarifas de uso. Para obter informações sobre como remover uma oferta ou um plano, consulte [parar de vender uma oferta ou um plano](./partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).

### <a name="free-contact-me-and-bring-your-own-license-byol-pricing"></a>Os preços gratuitos, entre em contato comigo e BYOL (traga sua própria licença)

Ao publicar uma oferta com a opção obter agora (gratuito), entrar em contato comigo ou BYOL, a Microsoft não desempenha uma função na facilitação da transação de vendas para suas taxas de licença de software. Assim como a lista e as opções de publicação de avaliação gratuita, o Publicador mantém 100% das taxas de licença de software.

### <a name="usage-based-and-subscription-pricing"></a>Preço com base no uso e assinatura

Ao publicar uma oferta de uma transação baseada em usuário ou assinatura, a Microsoft fornece a tecnologia e os serviços para processar compras, Devoluções e estorno de licenças de software. Nesse cenário, o editor autoriza a Microsoft a agir como um agente para essas finalidades. A editora permite que a Microsoft facilite a transação de licenciamento de software, mantendo sua designação como vendedor, provedor, distribuidor e licenciador.

A Microsoft permite aos clientes solicitar, licenciar e usar seu software, sujeito aos termos e condições do marketplace comercial da Microsoft e do seu contrato de licenciamento de usuário final. Você deve fornecer seu próprio contrato de licenciamento de usuário final ou selecionar o [Contrato Standard](./standard-contract.md) ao criar a oferta.

### <a name="free-software-trials"></a>Avaliações de software livre

Para cenários de publicação Transact, você pode disponibilizar uma licença de software gratuita por 30 a 120 dias, dependendo da assinatura. Esse recurso de aplicação de desconto não inclui o custo do uso de infraestrutura do Azure orientado pelo uso da solução do parceiro.

### <a name="private-offers"></a>Ofertas privadas

Além de usar tipos de oferta e modelos de cobrança para monetizar uma oferta, você pode realizar uma oferta privada e completa com preços negociados, específicos do negócio ou configurações personalizadas. As ofertas privadas têm suporte para todas as três opções de publicação de transações.

Essa opção permite um estabelecer um preço mais alto ou mais baixo do que a oferta publicamente disponível. Você pode usar ofertas privadas para desconto ou adicionar uma Premium a uma oferta. Você pode tornar as ofertas privadas disponíveis para um ou mais clientes allowlistingndo sua assinatura do Azure no nível da oferta.

### <a name="commercial-marketplace-service-fees"></a>Tarifas de serviço do Marketplace comercial

Cobramos uma taxa de serviço de loja padrão de 20% quando os clientes compram sua oferta de transação do mercado comercial. Para obter detalhes dessa taxa, consulte a seção 5C do [contrato do Microsoft Publisher](https://go.microsoft.com/fwlink/?LinkID=699560).

Para determinadas ofertas que você publica no Marketplace comercial, você pode se qualificar para uma taxa de serviço de armazenamento reduzida de 10%. Para que uma oferta seja qualificada, ela deve ter sido designada pela Microsoft como o Azure IP co-revenda incentivados. A qualificação deve ser atendida pelo menos cinco dias úteis antes do término de cada mês do calendário para receber a taxa de serviço do Marketplace reduzida. Depois que a qualificação for atendida, a taxa de serviço reduzida será concedida a todas as transações efetivas no primeiro dia do mês seguinte até que o status do incentivados de venda de IP do Azure seja perdido. Para obter detalhes sobre a qualificação de venda de IP, consulte [requisitos para o status de venda](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status).

A taxa reduzida de serviço do Marketplace se aplica ao IP do Azure covenda de incentivados SaaS, VMs, aplicativos gerenciados e quaisquer outras soluções de IaaS pagas qualificadas disponibilizadas por meio do Marketplace comercial. As ofertas de SaaS pagas associadas a um aplicativo Microsoft Teams ou pelo menos dois suplementos Microsoft 365 (Excel, PowerPoint, Word, Outlook e SharePoint) e publicadas em Microsoft AppSource também recebem esse desconto.

### <a name="examples"></a>Exemplos

**Com base no uso** 

O preço com base no uso tem a seguinte estrutura de custo:

|Custo de sua licença  | US $ 1,00 por hora   |
|---------|---------|
|Custo de uso do Azure (D1/1-Núcleo)    |   US $ 0,14 por hora     |
|*O cliente é cobrado pela Microsoft*    |  *US $ 1,14 por hora*       |
||

Nesse cenário, a Microsoft fatura US$ 1,14 por hora pelo uso da sua imagem de VM publicada.

|Faturas da Microsoft  | US$ 1,14 por hora  |
|---------|---------|
|A Microsoft paga para você 80% do seu custo de licença|   US $ 0,80 por hora     |
|A Microsoft mantém 20% do seu custo de licença  |  US $ 0,20 por hora       |
|A Microsoft mantém a 100% do custo de uso do Azure | US $ 0,14 por hora |
||

**Traga sua própria licença (BYOL)**

BYOL tem a seguinte estrutura:

|Custo de sua licença  | Valor de licença negociada e faturada por você  |
|---------|---------|
|Custo de uso do Azure (D1/1-Núcleo)    |   US $ 0,14 por hora     |
|*O cliente é cobrado pela Microsoft*    |  *US $ 0,14 por hora*       |
||

Nesse cenário, a Microsoft fatura US$ 0,14 por hora pelo uso da sua imagem de VM publicada.

|Faturas da Microsoft  | US $ 0,14 por hora  |
|---------|---------|
|A Microsoft mantém o custo de uso do Azure    |   US $ 0,14 por hora     |
|A Microsoft mantém 0% do seu custo de licença   |  US $ 0.00 por hora       |
||

**Assinatura de aplicativo SaaS**

Essa opção deve ser configurada para ser vendida pela Microsoft e pode ser cobrada a uma taxa fixa ou por usuário em uma base mensal ou anual. Se você habilitar a opção **Vender pela Microsoft** para uma oferta de SaaS, você terá a seguinte estrutura de custo:

| Custo de sua licença       | US $100,00 por mês  |
|--------------|---------|
| Custo de uso do Azure (D1/1-Núcleo)    | Cobrados diretamente para o publicador, não o cliente |
| *O cliente é cobrado pela Microsoft*    |  *US$ 100,00 por mês (o editor precisa contabilizar os custos incorridos ou de infraestrutura de passagem no valor da licença)*  |
||

Nesse cenário, a Microsoft cobra US $ 100,00 pela sua licença de software e paga US $ 80,00 ao editor.

Nesse cenário, a Microsoft cobra US$ 100,00 pela sua licença de software e paga US$ 90,00 ao editor:

|Faturas da Microsoft  | US $100,00 por mês  |
|---------|---------|
|A Microsoft paga para você 80% do seu custo de licença <br> \* A Microsoft paga 90% do seu custo de licença para qualquer aplicativo SaaS qualificado   |   US $80,00 por mês <br> \* US $90,00 por mês    |
|A Microsoft mantém 20% do seu custo de licença <br> \* A Microsoft mantém 10% do seu custo de licença para qualquer aplicativo SaaS qualificado.  |  US $20,00 por mês <br> \* US $10,00     |

### <a name="customer-invoicing-payment-billing-and-collections"></a>Faturamento, pagamento, faturamento e cobrança do cliente

**Faturamento e pagamento** – Você pode usar o método de faturamento preferencial do cliente para entregar os valores de licença de software de assinatura ou PAYGO.

**Enterprise Agreement** – Se o método de faturamento preferido do cliente for o Microsoft Enterprise Agreement, os valores de licença de software serão cobrados usando esse método de faturamento como um custo discriminado, separado dos custos de uso específicos do Azure.

**Cartões de crédito e fatura mensal** – Os clientes também podem pagar usando um cartão de crédito e uma nota fiscal mensal. Nesse caso, os valores de licença de software serão cobrados da mesma forma que o cenário do Enterprise Agreement, como um custo discriminado, separado dos custos de uso específicos do Azure.

**Créditos gratuitos e pagamentos antecipados do Azure** – alguns clientes optam por pagar o Azure com o pagamento antecipado do Azure (anteriormente chamado de compromisso monetário) no contrato Enterprise ou foram fornecidos créditos gratuitos para uso com o Azure. Embora esses créditos possam ser usados para pagar pelo uso do Azure, eles não podem ser usados para pagar valores de licença de software do editor.

**Cobrança e coleções** – a cobrança de licença de software do Publicador é apresentada usando o método de faturamento selecionado pelo cliente e segue a linha do tempo de faturamento. Clientes sem um Contrato Enterprise em vigor são cobrados mensalmente pelas licenças de software do mercado. Os clientes com um Contrato Enterprise são faturados mensalmente por meio de uma fatura apresentada trimestralmente.

Quando modelos de preços de assinatura ou de pagamento conforme o uso são selecionados, a Microsoft atua como agente do editor e é responsável por todos os aspectos de faturamento, pagamento e cobrança.

### <a name="publisher-payout-and-reporting"></a>Pagamento e relatórios do editor

Quaisquer valores de licenciamento de software coletados pela Microsoft como um agente estão sujeitas a um valor de transação de 20%, a menos que seja especificado de outra forma e sejam deduzidas no momento do pagamento do editor.

Os clientes normalmente compram usando o Enterprise Agreement ou um contrato de pagamento por utilização habilitado para cartão de crédito. O tipo de contrato determina o tempo de cobrança, faturamento, cobrança e pagamento.

>[!NOTE]
>Todos os relatórios e insights sobre a opção de publicação da transação estão disponíveis na seção Análise do Partner Center.

#### <a name="billing-questions-and-support"></a>Perguntas sobre cobrança e suporte

Para obter mais informações e políticas legais, consulte o [contrato do Microsoft Publisher](https://go.microsoft.com/fwlink/?LinkID=699560) (disponível no Partner Center).

Para obter ajuda sobre dúvidas de cobrança, entre em contato com o [suporte do editor do marketplace comercial](https://aka.ms/marketplacepublishersupport).

## <a name="transact-requirements"></a>Transact requisitos

Esta seção aborda os requisitos de transação para diferentes tipos de oferta.

### <a name="requirements-for-all-offer-types"></a>Requisitos para todos os tipos de oferta

- Uma conta da Microsoft e informações financeiras são necessárias para a opção de publicação de transações, independentemente do modelo de preços da oferta.
- As informações financeiras obrigatórias incluem a conta de pagamento e o perfil fiscal.
- O Publicador deve residir em um [país ou região com suporte](sell-from-countries.md).

Para obter mais informações sobre como configurar essas contas, consulte [gerenciar sua conta do Marketplace comercial no Partner Center](partner-center-portal/manage-account.md).

### <a name="requirements-for-specific-offer-types"></a>Requisitos para tipos de oferta específica

A opção de publicação de transações está disponível apenas para uso com os seguintes tipos de oferta de mercado:

- **Máquina virtual do Azure** – selecione dentre os modelos de preços gratuitos, traga sua própria licença ou com base no uso e apresente os planos definidos no nível da oferta. Na fatura do cliente do Azure, a Microsoft apresenta os valores de licença de software do editor separadamente dos valores de infraestrutura subjacentes do Azure. Os valores de infraestrutura do Azure são impulsionadas pelo uso do software do editor.

- **Aplicativo do Azure: modelo de solução ou aplicativo gerenciado** – deve provisionar uma ou mais máquinas virtuais e efetuar pull da soma dos preços da máquina virtual. Para aplicativos gerenciados em um único plano, uma assinatura mensal de taxa fixa pode ser selecionada como o modelo de preço, em vez do preço da máquina virtual. Em alguns os casos, os valores de uso da infraestrutura do Azure são passados para o cliente separadamente dos valores de licença de software, mas no mesmo extrato de faturamento. No entanto, se você configurar uma oferta de aplicativo gerenciado para encargos de infraestrutura de ISV, os recursos do Azure serão cobrados para o Publicador e o cliente receberá uma tarifa fixa que inclui o custo de infraestrutura, licenças de software e serviços de gerenciamento.

- **Aplicativo SaaS** -deve ser uma solução multilocatário, usar [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) para autenticação e integrá-lo com as [APIs de cumprimento de SaaS](partner-center-portal/pc-saas-fulfillment-api-v2.md). O uso da infraestrutura do Azure é gerenciado e cobrado diretamente para você (o parceiro), portanto, você deve considerar as taxas de uso da infraestrutura do Azure e as taxas de licenciamento de software como um único item de custo. Para obter diretrizes detalhadas, consulte [criar uma nova oferta de SaaS no Marketplace comercial](./create-new-saas-offer.md).

## <a name="next-steps"></a>Próximas etapas

- Examine os requisitos de qualificação nas opções de publicação pela seção de tipo de oferta para finalizar a seleção e a configuração de sua oferta.
- Examine os padrões de publicação por loja online para obter exemplos de como sua solução é mapeada para um tipo de oferta e configuração.