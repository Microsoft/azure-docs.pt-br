---
title: Recursos e Considerações sobre Transações Comerciais do Marketplace | Azure
description: Este artigo descreve as considerações de preço, faturamento, faturamento e pagamento da transação para um tipo de oferta.
services: Azure, Marketplace, Compute, Storage, Networking, Transact Offer Type
documentationcenter: ''
author: yijenj
manager: nuno costa
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/29/2018
ms.author: yijenj
ms.openlocfilehash: 3c07f7ac2b6407a3edf53fd5d3a4b402714fefbb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61064989"
---
# <a name="azure-marketplace-commercial-transaction-capabilities-and-considerations"></a>Recursos e considerações sobre transações comerciais do Azure Marketplace

As opções de publicação do Azure Marketplace oferecem maneiras exclusivas de conectar o software e os provedores de serviços em nuvem aos clientes. Este artigo aborda os seguintes tópicos relacionados ao comércio no Azure Marketplace:

* Opções de publicação no marketplace
* Visão geral de transações
* Transact modelos de cobrança
* Transact requisitos

## <a name="marketplace-publishing-options"></a>Opções de publicação no marketplace

As seguintes opções de publicação estão disponíveis para os editores do Azure Marketplace.

### <a name="list--trial-publishing-options"></a>Opções de publicação de lista e avaliação

No Azure Marketplace, os editores podem aproveitar a lista e as opções de publicação de avaliação para fins promocionais e de aquisição de usuários. Com as opções de publicação de lista ou de avaliação, a Microsoft não participa diretamente das transações de licença de software do editor e não há valor de transação associada. Os editores são responsáveis por dar suporte a todos os aspectos da transação de licença de software, incluindo, entre outros: pedido, atendimento, medição, faturamento, faturamento, pagamento e cobrança. Com as opções de publicação de lista e avaliação, os editores mantêm 100% dos valores de licenciamento de software do editor coletadas do cliente. 

### <a name="transact-publishing-option"></a>Opção de publicação de transação

Além das opções de publicação de lista e de avaliação, a opção de publicação de transações está disponível para os editores do Azure Marketplace.   Ele tira proveito dos recursos de comércio globalmente disponíveis da Microsoft. Essa opção permite que a Microsoft hospede transações do mercado de nuvem em nome do editor.

## <a name="transact-general-overview"></a>Visão geral de transações

Ao usar a opção de publicação de transação, a Microsoft permite que a venda de software de terceiros e a implantação de alguns tipos de oferta para a assinatura do cliente do Azure. O editor deve considerar o faturamento de valores de infraestrutura do Azure e os próprios valores de licenciamento de software do editor ao selecionar um modelo de faturamento e um tipo de oferta no Azure Marketplace.

A opção de publicação Transact no Azure Marketplace é atualmente compatível com os seguintes tipos de oferta: Máquinas Virtuais, Aplicativos Azure ou Aplicativos SaaS.

![[Transacionar Enterprise lida no Marketplace do Azure]](./media/marketplace-publishers-guide/Transact-enterprise-deals.png)

### <a name="billing-infrastructure-costs"></a>Custos de infra-estrutura de faturamento

**Para máquinas virtuais e aplicativos do Azure**

Para máquinas virtuais e aplicativos do Azure, os valores de uso da infraestrutura do Azure são cobradas da assinatura do Azure do cliente.  Os valores de uso da infraestrutura são precificadas e apresentadas separadamente dos valores de licenciamento do provedor de software na fatura do cliente.

**Para aplicativos SaaS**

Para aplicativos SaaS, o editor deve contabilizar os valores de uso da infraestrutura do Azure e os valores de licenciamento de software como um item de custo único.  Ela é representada como uma taxa fixa para o cliente. O uso da infraestrutura do Azure é gerenciado e faturado diretamente para o parceiro.  Os valores reais de uso da infraestrutura não são vistos pelo cliente.  Em geral, os editores optam por incluir os valores de uso da infraestrutura do Azure em seus preços de licença de software.  Os valores de licenciamento de software não são medidos ou baseados no consumo.

## <a name="transact-billing-models"></a>Transact modelos de cobrança

Dependendo da opção de transação usada, os valores de licença de software do editor podem ser apresentados da seguinte forma:  

* Grátis: Sem custo para licenças de software. 

* Traga sua própria licença (BYOL): Todas as cobranças aplicáveis por licenças de software são gerenciadas diretamente entre o editor e o cliente. A Microsoft só passa pelos valores de uso da infraestrutura do Azure. (Máquinas Virtuais e Aplicativos do Azure somente).

* Pago conforme o uso: Os valores da licença de software são apresentadas como uma taxa de preços por hora, por núcleo (vCPU) com base na infraestrutura do Azure usada. Isso se aplica apenas a máquinas virtuais e aplicativos do Azure.

* Preço da assinatura (baseado no site): Os valores de licença de software são apresentados como um valor mensal.  Isso se aplica somente a aplicativos SaaS e aplicativos gerenciados do Azure.

* Avaliação de software gratuita: Sem cobrança de licenças de software por 30 dias ou 90 dias.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Preços gratuitos e traga sua própria licença (BYOL)

Ao publicar uma oferta de transação gratuita ou com licença própria, a Microsoft não desempenha nenhum papel na facilitação da transação de vendas para os valores de licença de software. Como a lista e as opções de publicação de avaliação, o editor mantém 100% dos valores de licença de software. 

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Preço pré-pago e assinatura (site-based)

Ao publicar uma oferta de transação pré-paga ou assinatura, a Microsoft fornece a tecnologia e os serviços para processar compras, devoluções e estornos de licenças de software. Nesse cenário, o editor autoriza a Microsoft a agir como um agente para essas finalidades. A editora permite que a Microsoft facilite a transação de licenciamento de software, mantendo sua designação como vendedor, provedor, distribuidor e licenciador.

A Microsoft permite aos clientes solicitar, licenciar e usar o software do editor, sujeito aos termos e condições do Azure Marketplace e do contrato de licenciamento do usuário final do editor (consulte o Cloud Partner Portal). Os editores devem fornecer seu contrato de licenciamento do usuário final na oferta do mercado.

Os pedidos processados pelo mercado são faturados para a assinatura do Azure do cliente em uma única fatura, o mesmo método de faturamento que os custos de infraestrutura do Azure do cliente. Os clientes podem usar o método de faturamento e o instrumento de pagamento preferidos usados para o faturamento da assinatura do Azure.

### <a name="free-software-trials"></a>Avaliações de software livre

Para cenários de publicação em transações, o editor pode disponibilizar uma licença de software gratuitamente por 30 dias ou 90 dias. Esse recurso de desconto não inclui o custo do uso da infraestrutura do Azure que é impulsionado pelo uso da solução de parceiro.

### <a name="private-offers"></a>Ofertas privadas

Além de usar tipos de oferta e modelos de faturamento para monetizar uma oferta, os editores podem realizar transações de uma versão privada da oferta de solução, completa com preços negociados específicos de transação e configurações personalizadas usando uma imagem personalizada. As ofertas privadas são suportadas por todas as três opções de publicação de transações.

Essa opção de preço pode ser maior ou menor do que o preço exibido publicamente.  Ofertas privadas podem ser usadas para desconto ou adicionar um prêmio para uma oferta. As ofertas privadas podem ser disponibilizadas para um ou mais clientes por meio de white listando sua assinatura do Azure no nível da oferta.

### <a name="examples"></a>Exemplos

**Pré-paga** 

* Se você ativar a opção Pague conforme o uso, terá a seguinte estrutura de custos.

|Custo de sua licença  | US $ 1,00 por hora  |
|---------|---------|
|Custo de uso do Azure (D1/1-Núcleo)    |   US $ 0,14 por hora     |
|*O cliente é cobrado pela Microsoft*    |  *US $ 1,14 por hora*       |

* Nesse cenário, a Microsoft fatura US$ 1,14 por hora pelo uso da sua imagem de VM publicada.

|Faturas da Microsoft  | US$ 1,14 por hora  |
|---------|---------|
|A Microsoft paga para você 80% do seu custo de licença|   US $ 0,80 por hora     |
|A Microsoft mantém 20% do seu custo de licença  |  US $ 0,20 por hora       |
|A Microsoft mantém a 100% do custo de uso do Azure | US $ 0,14 por hora |

**Traga sua própria licença (BYOL)**

* Se você habilitar a opção de BYOL, você tem a seguinte estrutura de custo.

|Custo de sua licença  | Valor de licença negociada e faturada por você  |
|---------|---------|
|Custo de uso do Azure (D1/1-Núcleo)    |   US $ 0,14 por hora     |
|*O cliente é cobrado pela Microsoft*    |  *US $ 0,14 por hora*       |

* Nesse cenário, a Microsoft fatura US$ 0,14 por hora pelo uso da sua imagem de VM publicada.

|Faturas da Microsoft  | US $ 0,14 por hora  |
|---------|---------|
|A Microsoft mantém o custo de uso do Azure    |   US $ 0,14 por hora     |
|A Microsoft mantém 0% do seu custo de licença   |  US $ 0.00 por hora       |

**Assinatura do aplicativo SaaS (venda pelo Azure)**

Essa opção deve ser configurada para vender através da Microsoft e pode ser precificada usando um ou mais planos mensais de taxa fixa definidos no nível da oferta.

* Se você ativar a opção Vender por meio do Azure, terá a seguinte estrutura de custos.

|Custo de sua licença       | US $100,00 por mês  |
|--------------|---------|
|Custo de uso do Azure (D1/1-Núcleo)    | Cobrados diretamente para o publicador, não o cliente |
|*O cliente é cobrado pela Microsoft*    |  *US $ 100,00 por mês (observação: o editor deve contabilizar quaisquer custos incorridos ou de infraestrutura de passagem no valor da licença)*  |

* Nesse cenário, a Microsoft cobra US $ 100,00 pela sua licença de software e paga US $ 80,00 ao editor.

|Faturas da Microsoft  | US $100,00 por mês  |
|---------|---------|
|A Microsoft paga para você 80% do seu custo de licença    |   US $80,00 por mês     |
|A Microsoft mantém 20% do seu custo de licença   |  US $20,00 por mês       |

### <a name="customer-invoicing-payment-billing-and-collections"></a>Faturamento, pagamento, faturamento e cobrança do cliente

**Faturamento e pagamento**

O editor pode usar o método de faturamento preferido do cliente para fornecer valores de licença de software de assinatura ou PAYGO.

**Contrato Enterprise** 

Se o método de faturamento preferido do cliente for o Microsoft Enterprise Agreement, os valores de licença de software serão cobrados usando esse método de faturamento como um custo discriminado, separado dos custos de uso específicos do Azure.

**Cartões de crédito e a fatura mensal** 

Os clientes também podem pagar usando um cartão de crédito e uma fatura mensal. Nesse caso, os valores de licença de software serão cobrados da mesma forma que o cenário do Enterprise Agreement, como um custo discriminado, separado dos custos de uso específicos do Azure.

Por exemplo, se o cliente compra usando um cartão de crédito:

|DESCRIÇÃO    |    Data  |
|----------|----------|
|Período de ordem   | 15 de agosto de 2018 – 30 de agosto de 2018 |
|Prazo final (mês)   | 30 de agosto de 2018 |
|Data de cobrança | 1 de setembro de 2018 |
|Data de pagamento do cliente | 1 de setembro de 2018 |
|Efetue a caução de período (cartões de crédito, 30 dias) | 1 de setembro de 2018 – 30 de setembro de 2018 |
|Início do período de coleta | 1 de setembro de 2018 |
|Término do período de coleta (máximo, 30 dias) | 30 de setembro de 2018 |
|Data de cálculo de pagamento (mensalmente no dia 15) | 1 de outubro de 2018 |
|Data de pagamento | 15 de outubro de 2018 |

Se o cliente comprar usando um Enterprise Agreement:

| DESCRIÇÃO |    Data  |
|----------|----------|
|Período de ordem | 15 de agosto de 2018 – 30 de agosto de 2018 |
|Prazo final (trimestre) | 30 de setembro de 2018 |
|Data de cobrança | 15 de outubro de 2018 |
|Efetue a caução de período (cartões de crédito, 30 dias) | n/d |
|Início do período de coleta | 15 de outubro de 2018 |
|Término do período de coleta (máximo, 90 dias) | 15 de janeiro de 2019 |
|Data de pagamento do cliente | 30 de dezembro de 2018 |
|Data de cálculo de pagamento (mensalmente no dia 15) | 15 de janeiro de 2019 |
|Data de pagamento | 15 de fevereiro de 2019 |

**Créditos gratuitos e compromisso monetário** 

Alguns clientes optam por pagar antecipadamente o Azure com um compromisso monetário no Enterprise Agreement ou receberam créditos gratuitos para uso com o Azure. Embora esses créditos possam ser usados para pagar pelo uso do Azure, eles não podem ser usados para pagar valores de licença de software do editor.

**Cobrança e coleções** 

O faturamento da licença do software do editor é apresentado usando o método de faturamento selecionado pelo cliente e segue o cronograma do faturamento. Clientes sem um Contrato Enterprise em vigor são cobrados mensalmente pelas licenças de software do mercado. Os clientes com um Contrato Enterprise são faturados mensalmente por meio de uma fatura apresentada trimestralmente.

Quando modelos de preços de assinatura ou de pagamento conforme o uso são selecionados, a Microsoft atua como agente do editor e é responsável por todos os aspectos de faturamento, pagamento e cobrança.

### <a name="publisher-payout-and-reporting"></a>Pagamento e relatórios do editor

* Quaisquer valores de licenciamento de software coletados pela Microsoft como um agente estão sujeitas a um valor de transação de 20%, a menos que seja especificado de outra forma e sejam deduzidas no momento do pagamento do editor.

* Os clientes normalmente compram usando o Enterprise Agreement ou um contrato de pagamento por utilização habilitado para cartão de crédito. O tipo de contrato determina o tempo de cobrança, faturamento, cobrança e pagamento.

>[!NOTE] 
>Todos os relatórios e insights da opção de publicação de transações estão disponíveis na seção Insights do Portal do Cloud Partner.

#### <a name="billing-questions-and-support"></a>Perguntas sobre cobrança e suporte

Para obter mais informações e políticas legais, consulte o [Contrato do editor](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf) (disponível no Portal do Cloud Partner).

Para receber ajuda sobre questões de faturamento, [crie um incidente de suporte](https://support.microsoft.com/getsupport?wf=0&tenant=classiccommercial&oaspworkflow=start_1.0.0.0&pesid=16230&forceorigin=esmc&ccsid=636764613233453423) e escolha Máquinas virtuais ou Aplicativos da web (também conhecidos como SaaS Apps), dependendo do tipo de oferta usado.

## <a name="transact-requirements"></a>Transact requisitos

Os requisitos de transação para diferentes tipos de oferta são abordados nesta seção.

### <a name="requirements-for-all-offer-types"></a>Requisitos para todos os tipos de oferta

**Conta do Centro de desenvolvimento e a Microsoft** 

* Tanto um Centro de Desenvolvimento quanto uma conta da Microsoft são necessários para a opção de publicação de transações, independentemente do modelo de preços da oferta.
* A conta do Centro de Desenvolvimento contém todos os detalhes financeiros relevantes necessários para que a Microsoft colete valores do cliente em nome do editor e pague o editor.
* Embora você possa usar a mesma organizacional ou detalhes de entrada da Microsoft em ambas as contas, o Centro de desenvolvimento é uma conta separada da conta do Portal do Cloud Partner. Para usar a opção de publicação transacionar, o editor deve concluir o processo de inscrição na conta do Centro de Desenvolvimento, além de se inscrever para acessar o Portal do Cloud Partner.

*Para obter mais informações sobre como configurar essas contas, consulte [Torne-se um editor do Cloud Marketplace](https://docs.microsoft.com/azure/marketplace/become-publisher).* 

### <a name="requirements-for-specific-offer-types"></a>Requisitos para tipos de oferta específica

A opção de publicação de transações está disponível apenas para uso com os seguintes tipos de oferta de mercado: 

**Máquina Virtual** 

Selecione modelos gratuitos, traga sua licença própria ou pague conforme o preço e apresente-os como SKUs definidos no nível da oferta. Na fatura do cliente do Azure, a Microsoft apresenta os valores de licença de software do editor separadamente dos valores de infraestrutura subjacentes do Azure. Os valores de infraestrutura do Azure são impulsionadas pelo uso do software do editor.

**Aplicativos do Azure: Modelo de solução ou aplicativo gerenciado** 

Deve provisionar uma ou mais máquinas virtuais e pulls por meio da soma dos preços de máquina virtual. Para aplicativos gerenciados em um único plano, uma assinatura mensal de taxa fixa pode ser selecionada como o modelo de preço, em vez do preço da máquina virtual. Em ambos os casos, os valores de uso da infraestrutura do Azure são passadas para o cliente separadamente dos valores de licença de software, mas no mesmo extrato de faturamento.

## <a name="next-steps"></a>Próximas etapas

* Examine os requisitos de qualificação nas opções de publicação pela seção de tipo de oferta para finalizar a seleção e a configuração de sua oferta.
* Examine os padrões de publicação por vitrine eletrônica para obter exemplos de como sua solução é mapeada para uma configuração e tipo de oferta.
* Torne-se um editor do Marketplace e faça login no [Portal do Cloud Partner](https://cloudpartner.azure.com) para criar e configurar sua oferta.
