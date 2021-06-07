---
title: Criar uma oferta de teste
description: Como criar uma oferta de desenvolvimento separada para testar sua oferta de produção no programa do Marketplace comercial no Microsoft Partner Center.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 893d38d7dcf2ef0910bc46d3e9bfd168c2a89162
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543356"
---
# <a name="create-a-test-offer"></a>Criar uma oferta de teste

Para desenvolver em um ambiente separado de sua oferta de produção, você criará uma oferta de desenvolvimento e teste (DEV) separada e uma oferta de produção (PROD) separada. Para obter informações sobre os benefícios de usar uma oferta de desenvolvimento separada, consulte [planejar uma oferta de SaaS](plan-saas-offer.md#test-offer).

Você configurará a maioria das configurações nas ofertas de desenvolvimento e produção. Por exemplo, a linguagem de marketing oficial e os ativos, como capturas de tela e logotipos, devem ser iguais. Nos casos em que a configuração é a mesma, você pode copiar e colar campos dos planos na oferta de desenvolvimento para os planos na oferta de produção.

As seções a seguir descrevem as diferenças de configuração entre as ofertas de desenvolvimento e produção.

## <a name="offer-setup-page"></a>Página Configuração da oferta

Recomendamos que você use o mesmo alias na caixa **alias** de ambas as ofertas, mas acrescente "_test" ao alias da oferta de desenvolvimento. Por exemplo, se o alias da sua oferta de PROD for "contososolution", o alias da oferta de desenvolvimento deverá ser "contososolution_test". Dessa forma, você pode identificar facilmente qual sua oferta de desenvolvimento de sua oferta de produção.

Na seção **clientes potenciais do cliente** , use uma tabela do Azure ou um ambiente de teste do CRM para a oferta de desenvolvimento. Use o sistema de gerenciamento de leads pretendido para a oferta de produção.

## <a name="properties-page"></a>Página Propriedades

Configure essa página da mesma nas ofertas de desenvolvimento e de produção.

## <a name="offer-listing-page"></a>Página Listagem de ofertas

Configure essa página da mesma nas ofertas de desenvolvimento e de produção.

## <a name="preview-audience"></a>Público-alvo de versão prévia

Na oferta de desenvolvimento, inclua os nomes da entidade de usuário do Azure Active Directory (AAD) ou os endereços de email do conta Microsoft (MSA) de desenvolvedores e testadores, incluindo você mesmo. O nome principal de usuário de um usuário no AAD pode ser diferente do email desse usuário. Por exemplo, jane.doe@contoso.com não funcionará, mas janedoe@contoso.com vai. Os usuários designados terão acesso à oferta de desenvolvimento quando você compartilhar o link de **Visualização** durante a fase de desenvolvimento e teste.

Na oferta de produção, inclua o nome da entidade de usuário do Azure AD ou o email da conta da Microsoft dos usuários que irão validar a oferta antes de selecionar o botão Ativar ao **vivo** para publicar a oferta ao vivo.

## <a name="technical-configuration-page"></a>Página Configuração técnica

Esta tabela descreve as diferenças entre as configurações para ofertas de desenvolvimento e ofertas de produção.

***Tabela 1: diferenças de configuração técnica***

| Setting | Oferta de desenvolvimento | Oferta de PROD |
| ------------ | ------------- | ------------- |
| URL da página de aterrissagem | Insira seu ponto de extremidade de desenvolvimento/teste. | Insira seu ponto de extremidade de produção. |
| Webhook de conexão | Insira seu ponto de extremidade de desenvolvimento/teste. | Insira seu ponto de extremidade de produção. |
| ID de locatário Azure Active Directory | Insira sua ID de locatário de registro do aplicativo de teste (ID do diretório do AAD). | Insira sua ID de locatário de registro do aplicativo de produção. |
| ID do aplicativo do Azure Active Directory | Insira a ID do aplicativo de registro do aplicativo de teste (ID do cliente). | Insira a ID do aplicativo de registro do aplicativo de produção. |
||||

## <a name="plan-overview-page"></a>Página Visão geral do plano

Ao criar seus planos, recomendamos que você use a mesma _ID de plano_ e _nome do plano_ nas ofertas de desenvolvimento e de produção, exceto acrescentar a ID do plano na oferta de desenvolvimento com **_test**. Por exemplo, se a ID do plano na oferta de PROD for "Enterprise", a ID do plano na oferta de desenvolvimento deverá ser "enterprise_test". Dessa forma, você pode identificar facilmente qual sua oferta de desenvolvimento de sua oferta de produção. Você criará planos na oferta de produção com os modelos de preços e os preços que você decidir são os melhores para sua oferta.

### <a name="plan-listing"></a>Listagem de planos

Na guia de listagem **visão geral do plano**  >   , insira a mesma descrição do plano nos planos de desenvolvimento e de produção.

### <a name="pricing-and-availability-page"></a>Página de preços e disponibilidade

Esta seção fornece diretrizes para concluir a   >  página de **preços e a disponibilidade** da visão geral do plano.

#### <a name="markets"></a>Mercados

Selecione os mesmos mercados para as ofertas de desenvolvimento e produção.

#### <a name="pricing"></a>Preços

Use a oferta de desenvolvimento para experimentar os modelos de preços. Depois de verificar quais modelos ou modelo de preços funcionam melhor, você criará os planos na oferta de produção com os modelos de preços e os preços desejados.

A oferta de desenvolvimento deve ter planos com preço zero ou baixo nos planos. A oferta de PROD terá os preços que você deseja cobrar aos clientes.

> [!IMPORTANT]
> As compras feitas na visualização serão processadas para as ofertas de desenvolvimento e de produção. Se uma oferta tiver um preço de $100/mo, sua empresa será cobrada $100. Se isso acontecer, você poderá abrir um [tíquete de suporte](support.md) e emitirei um pagamento pelo valor total (e não levará nenhuma taxa de serviço de armazenamento).

#### <a name="pricing-model"></a>Modelo de preços

Use o mesmo modelo de preços nos planos das ofertas de desenvolvimento e produção. Por exemplo, se o plano na oferta de PROD for uma taxa fixa, com um termo de cobrança mensal, configure o plano na oferta de desenvolvimento usando o mesmo modelo.

Para reduzir o custo de teste dos modelos de preços, incluindo dimensões de medidor personalizado do Marketplace, recomendamos que você configure a seção de **preços** da guia **preços e disponibilidade** , na oferta de desenvolvimento com preços mais baixos do que a oferta de produção. Aqui estão algumas diretrizes que você pode seguir ao definir preços para planos na oferta de desenvolvimento.

***Tabela 2: diretrizes de preços***

| Preço | Comentário |
| ------------ | ------------- |
| $0.00 | Defina um custo de transação total de zero como sem impacto financeiro. Use esse preço ao fazer chamadas para as APIs de medição ou para testar planos de compra em sua oferta ao desenvolver sua solução. |
| $0.01-$49.99 | Use este intervalo de preços para testar análises, relatórios e o processo de compra. |
| $50 e acima | Use este intervalo de preços para testar o pagamento. Para obter informações sobre nossa programação de pagamento, consulte [agendas e processos](/partner-center/payout-policy-details)de pagamento. |
|||

Para evitar a cobrança de uma taxa de serviço de armazenamento em seu teste, abra um [tíquete de suporte](support.md).

#### <a name="free-trial"></a>Avaliação gratuita

Não habilite uma avaliação gratuita para a oferta de desenvolvimento.

## <a name="co-sell-with-microsoft-page"></a>Página de vendas conjuntas com a Microsoft

Não configure a guia de **venda conjunta com a Microsoft** da oferta de desenvolvimento.

## <a name="resell-through-csps"></a>Revenda por meio de CSPs

Não configure a guia **revender por meio de CSPs** da oferta de desenvolvimento.

## <a name="next-steps"></a>Próximas etapas

- [Testar e publicar uma oferta de SaaS](test-publish-saas-offer.md)
