---
title: Como criar planos para sua oferta de SaaS no Microsoft Partner Center
description: Como criar planos para uma nova oferta de SaaS (software como serviço) usando o portal do Microsoft Commercial Marketplace no Partner Center.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 238ef9ec80b01470e28535d0eb42dbb3a377b005
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746430"
---
# <a name="how-to-create-plans-for-your-saas-offer"></a>Como criar planos para sua oferta de SaaS

As ofertas vendidas por meio do Microsoft Commercial Marketplace devem ter pelo menos um plano. Você pode criar uma variedade de planos com diferentes opções na mesma oferta. Esses planos (às vezes chamados de SKUs) podem diferir em termos de versão, monetização ou camadas de serviço. Para obter orientações detalhadas sobre planos, consulte [planos e preços para ofertas de Marketplace comercial](plans-pricing.md).

> [!NOTE]
> Se você optar por processar as transações de forma independente, essa opção não será exibida. Em vez disso, pule para [como vender sua oferta de SaaS](create-new-saas-offer-marketing.md).

## <a name="create-a-plan"></a>Criar um plano

1. Próximo à parte superior da guia **visão geral do plano** , selecione **+ criar novo plano**.

1. Na caixa de diálogo exibida, na caixa **ID do plano** , insira uma ID de plano exclusiva. Use até 50 caracteres alfanuméricos minúsculos, traços ou sublinhados. Você não pode modificar a ID do plano depois de selecionar **criar**.

1. Na caixa **nome do plano** , insira um nome exclusivo para este plano. Use no máximo 50 caracteres.

1. Selecione **Criar**.

## <a name="define-the-plan-listing"></a>Definir a listagem do plano

Na guia **lista de planos** , você pode definir o nome e a descrição do plano, pois você deseja que eles apareçam no Marketplace comercial.

1. Na caixa **nome do plano** , o nome que você forneceu anteriormente para esse plano aparece aqui. Você pode alterá-lo a qualquer momento. Esse nome aparecerá no Marketplace comercial como o título do plano de software da sua oferta.

1. Na caixa **Descrição do plano** , explique o que torna este plano de software exclusivo e quaisquer diferenças de outros planos dentro de sua oferta. Esta descrição pode conter até 500 caracteres.
1. Selecione **salvar rascunho** antes de continuar para a próxima guia: **preços e disponibilidade**.

## <a name="define-markets-pricing-and-availability"></a>Definir mercados, preços e disponibilidade

Cada plano precisa estar disponível em pelo menos um mercado. Na guia **preços e disponibilidade** , você pode configurar os mercados em que esse plano estará disponível, o modelo de monetização, o preço e os termos de cobrança desejados. Além disso, você pode indicar se deseja tornar o plano visível para todos ou apenas para clientes específicos (também chamado de plano privado).

1. Em **mercados**, selecione o link **Editar mercados** .
1. Na caixa de diálogo que aparece, selecione os locais de mercado onde você deseja disponibilizar seu plano. Você deve selecionar no mínimo um e no máximo 141 mercados.

   > [!NOTE]
   > Essa caixa de diálogo inclui uma caixa de pesquisa e uma opção para filtrar somente os países "impostos remetidos", no qual a Microsoft remete as vendas e o imposto sobre o uso em seu nome.

1. Selecione **salvar** para fechar a caixa de diálogo.

## <a name="define-a-pricing-model"></a>Definir um modelo de preços

Você deve associar um modelo de preço a cada plano: uma _taxa fixa_ ou _por usuário_. Todos os planos na mesma oferta devem usar o mesmo modelo de preços. Por exemplo, uma oferta não pode ter um plano que seja uma taxa fixa e outro plano por usuário. Para obter mais informações, consulte [modelos de preços do SaaS](plan-saas-offer.md#saas-pricing-models).

> [!IMPORTANT]
> Depois que sua oferta for publicada, você não poderá alterar o modelo de preços. Além disso, todos os planos para a mesma oferta precisam compartilhar o mesmo modelo de preços.

### <a name="configure-flat-rate-pricing"></a>Configurar preços de taxa fixa

1. Na guia **preços e disponibilidade** , em **preços**, selecione **taxa fixa**.
1. Marque a caixa de seleção **mensal** ou **anual** , ou ambos, e insira o preço.

### <a name="add-a-custom-meter-dimension"></a>Adicionar uma dimensão de medidor personalizado

Essa opção só estará disponível se você tiver selecionado preços de taxa simples. Para obter mais informações, consulte [cobrança limitada para SaaS usando o serviço de medição do Marketplace comercial](./partner-center-portal/saas-metered-billing.md).

1. Em **dimensões do serviço de medição do Marketplace**, selecione o link **Adicionar uma dimensão de medidor personalizado (máx. 30)** .
1. Na caixa **ID** , insira a referência de identificador imutável ao emitir eventos de uso.
1. Na caixa **nome de exibição** , insira o nome de exibição associado à dimensão. Por exemplo, "mensagens de texto enviadas".
1. Na caixa **unidade de medida** , insira a descrição da unidade de cobrança. Por exemplo, "mensagem por texto" ou "por email 100".
1. No **preço por unidade na caixa USD** , insira o preço de uma unidade da dimensão.
1. Na caixa de **dados quantidade mensal incluída na base** , insira a quantidade (como um número inteiro) da dimensão que é incluída todos os meses para os clientes que pagam a taxa mensal recorrente. Para definir uma quantidade ilimitada, marque a caixa de seleção em vez disso.
1. Na caixa de **dados quantidade anual incluída na base** , insira a quantidade da dimensão (como um número inteiro) que é incluída todos os meses para os clientes que pagam a taxa anual recorrente. Para definir uma quantidade ilimitada, marque a caixa de seleção em vez disso.
1. Para adicionar outra dimensão de medidor personalizada, selecione o link **adicionar outra dimensão** e repita as etapas de 1 a 7.

### <a name="configure-per-user-pricing"></a>Configurar preços por usuário

1. Na guia **preços e disponibilidade** , em **preços**, selecione **por usuário**.
2. Se aplicável, em **limites de usuário**, especifique o número mínimo e máximo de usuários para este plano.
3. Em **termo de cobrança**, especifique um preço mensal, preço anual ou ambos.

### <a name="validate-custom-prices"></a>Validar preços personalizados

Para definir preços personalizados em um mercado individual, exporte, modifique e importe a planilha de preços. Você é responsável por validar esse preço e possuir essas configurações. Para obter informações detalhadas, consulte [preços personalizados](plans-pricing.md#custom-prices).

1. É necessário primeiro salvar as alterações de preços para habilitar a exportação de dados sobre preços. Próximo à parte inferior da guia **preços e disponibilidade** , selecione **salvar rascunho**.
1. Em **preços**, selecione o link **exportar dados de preços** .
1. Abra o arquivo exportedPrice.xlsx no Microsoft Excel.
1. Na planilha, faça as atualizações desejadas para suas informações de preço e, em seguida, salve o. Arquivo CSV.<br> Talvez seja necessário habilitar a edição no Excel antes que você possa atualizar o arquivo.
2. Na guia **preços e disponibilidade** , em **preços**, selecione o link **importar dados de preços** .
3. Na caixa de diálogo exibida, clique em **Sim**.
4. Selecione o arquivo de exportedPrice.xlsx que você atualizou e clique em **abrir**.

### <a name="enable-a-free-trial"></a>Habilitar uma avaliação gratuita

Você pode configurar uma avaliação gratuita para cada plano em sua oferta. Marque a caixa de seleção para permitir uma avaliação gratuita de um mês. Essa caixa de seleção não está disponível para planos que usam o serviço de medição do Marketplace. Para obter mais informações, consulte [avaliações gratuitas](plans-pricing.md#free-trials).

> [!IMPORTANT]
> Depois que sua oferta de transação for publicada com uma avaliação gratuita, ela não poderá ser desabilitada para esse plano. Verifique se essa configuração está correta antes de publicar a oferta para evitar a recriação do plano.

- Em **avaliação gratuita**, marque a caixa de seleção **permitir uma avaliação gratuita de um mês** .

## <a name="choose-who-can-see-your-plan"></a>Escolha quem pode ver seu plano

Você pode configurar cada plano para ser visível para todos ou apenas para um público específico. Você concede acesso a um plano privado usando IDs de locatário com a opção de incluir uma descrição de cada ID de locatário que você atribui. Você pode adicionar no máximo 10 IDs de locatário manualmente ou até 20.000 IDs de locatário usando um. Arquivo CSV.

> [!NOTE]
> Se você publicar um plano privado, poderá alterar sua visibilidade para o público posteriormente. No entanto, depois de publicar um plano público, você não pode alterar sua visibilidade para particular.

### <a name="make-your-plan-public"></a>Torne seu plano público

1. Em **visibilidade do plano**, selecione a caixa **público** .
1. Selecione **salvar rascunho** e, no canto superior esquerdo da guia, selecione **visão geral do plano** para retornar à guia **visão geral do plano** .
1. Para criar outro plano para esta oferta, próximo à parte superior da guia **visão geral do plano** , selecione **+ criar novo plano**. Em seguida, repita as etapas na seção [criar um plano](#create-a-plan) . Caso contrário, vá para [exibir seus planos](#view-your-plans).

### <a name="manually-add-tenant-ids-for-a-private-plan"></a>Adicionar manualmente IDs de locatário para um plano privado 

1. Em **visibilidade do plano**, selecione a caixa **este é um plano privado** .
1. Na caixa **ID do locatário** exibida, insira a ID do locatário do Azure AD do público que você deseja conceder acesso a este plano privado. É necessário um mínimo de uma ID de locatário.
1. Adicional Insira uma descrição desse público na caixa **Descrição** .
1. Para adicionar outra ID de locatário, repita as etapas 2 e 3.
1. Quando você terminar de adicionar IDs de locatário, selecione **salvar rascunho** e, no canto superior esquerdo da guia, selecione **planejar visão geral** para retornar à guia **visão geral do plano** .
1. Para criar outro plano para esta oferta, próximo à parte superior da guia **visão geral do plano** , selecione **+ criar novo plano**. Em seguida, repita as etapas na seção [criar um plano](#create-a-plan) . Caso contrário, vá para [exibir seus planos](#view-your-plans).

### <a name="use-a-csv-file-for-a-private-plan"></a>Use um. Arquivo CSV para um plano privado

1. Em **visibilidade do plano**, selecione a caixa **este é um plano privado** .
2. Selecione o link **Exportar audiência (CSV)** .
3. Abra o. Arquivo CSV e adicione as IDs do Azure às quais você deseja conceder acesso à oferta privada para a coluna **ID** .
4. Opcionalmente, insira uma descrição para cada público na coluna **Descrição** .
5. Adicione "Tenantid" na coluna **tipo** para cada linha com uma ID do Azure.
6. Salve o. Arquivo CSV.
7. Na guia **preço e disponibilidade** , em **visibilidade do plano**, selecione o link **importar audiência (CSV)** .
8. Na caixa de diálogo exibida, selecione **Sim**.
9. Selecione o. Arquivo CSV e, em seguida, selecione **abrir**.
10. Selecione **salvar rascunho** e, no canto superior esquerdo da guia, selecione **visão geral do plano** para retornar à guia **visão geral do plano** .
11. Para criar outro plano para esta oferta, na parte superior da guia **visão geral do plano** , selecione **+ criar novo plano**. Em seguida, repita as etapas na seção [criar um plano](#create-a-plan) . Caso contrário, se você terminar de criar planos, vá para a próxima seção: **exibir seus planos**.

## <a name="view-your-plans"></a>Exibir seus planos

Depois de criar um ou mais planos, você verá o nome do plano, a ID do plano, o modelo de preços, a disponibilidade (pública ou privada), o status de publicação atual e as ações disponíveis na guia **visão geral do plano** .

As ações disponíveis na coluna **ação** da guia **visão geral do plano** variam de acordo com o status do plano e podem incluir o seguinte:

- Se o status do plano for **rascunho**, o link na coluna **ação** dirá **excluir rascunho**.
- Se o status do plano for **dinâmico**, o link na coluna **ação** será parar de **vender plano** ou **sincronizar público privado**. O link **sincronizar audiência privada** publicará apenas as alterações em seus públicos privados, sem publicar outras atualizações que você tenha feito na oferta.

## <a name="next-steps"></a>Próximas etapas

- Saiba [como vender sua oferta de SaaS](create-new-saas-offer-marketing.md) por meio da **venda conjunta com a Microsoft** e **revender por meio** de programas de CSPs.
- [Como testar e publicar uma oferta de SaaS no Marketplace comercial](test-publish-saas-offer.md).
