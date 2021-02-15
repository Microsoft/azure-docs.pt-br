---
title: Organize sua fatura com base em suas necessidades – Azure
description: Saiba como organizar os custos na sua fatura. Você pode personalizar sua conta de cobrança criando perfis de cobrança e seções na fatura.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 6424fc0ff49566fad949b3fba4718acb2bad4cd3
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362757"
---
# <a name="organize-costs-by-customizing-your-billing-account"></a>Organizar os custos personalizando sua conta de cobrança

Sua conta de cobrança do Contrato de Cliente da Microsoft fornece flexibilidade para organizar seus custos com base em suas necessidades, seja por departamento, projeto ou ambiente de desenvolvimento.

Este artigo descreve como usar o portal do Azure para organizar seus custos. Este artigo aplica-se a uma conta de cobrança para um Contrato de Cliente da Microsoft. [Verifique se você tem acesso a um Contrato de Cliente da Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="structure-your-account-with-billing-profiles-and-invoice-sections"></a>Estruturar sua conta usando perfis de cobrança e seções da fatura

Na conta de cobrança para um Contrato de Cliente da Microsoft, use perfis de cobrança e seções da fatura para organizar seus custos.

![Captura de tela mostrando o Contrato de Cliente da Microsoft na hierarquia de cobrança](./media/mca-section-invoice/mca-hierarchy.png)

### <a name="billing-profile"></a>Perfil de faturamento

Um perfil de cobrança representa uma fatura e as informações de cobrança relacionadas, como métodos de pagamento e endereço para cobrança. Uma fatura mensal é gerada no início do mês para cada perfil de cobrança de sua conta. A fatura contém os encargos de uso do Azure e de outras compras do mês anterior.

Um perfil de cobrança é criado automaticamente junto com sua conta de cobrança quando você se inscreve no Azure. Você pode criar perfis de cobrança adicionais para organizar seus custos em várias faturas mensais.

> [!IMPORTANT]
>
> A criação de perfis de cobrança adicionais pode afetar o custo geral. Para obter mais informações, confira [Coisas a serem consideradas ao adicionar novos perfis de cobrança](#things-to-consider-when-adding-new-billing-profiles).

### <a name="invoice-section"></a>Seção da fatura

Uma seção da fatura representa um agrupamento de custos em sua fatura. Uma seção da fatura é criada automaticamente para cada perfil de cobrança em sua conta. Você pode criar seções adicionais para organizar seus custos com base em suas necessidades. Cada seção da fatura é exibida na fatura com os encargos incorridos no mês.

A imagem abaixo mostra uma fatura com duas seções de fatura – engenharia e marketing. O resumo e os encargos detalhados de cada seção são exibidos na fatura. Os preços mostrados na imagem são apenas para fins de exemplo e não representam os preços reais dos serviços do Azure.

![Imagem mostrando uma fatura com seções](./media/mca-section-invoice/mca-invoice-with-sections.png)

## <a name="billing-account-structure-for-common-scenarios"></a>Estrutura da conta de cobrança para cenários comuns

Esta seção descreve os cenários comuns para organizar os custos e as estruturas de conta de cobrança correspondentes:

|Cenário  |Estrutura  |
|---------|---------|
|João se inscreve no Azure e precisa de uma fatura mensal única. | Um perfil de cobrança e uma seção da fatura. Essa estrutura é configurada automaticamente para João quando ele se inscreve no Azure e não requer etapas adicionais. |

![Gráfico de informações para um cenário de cobrança individual.](./media/mca-section-invoice/organize-billing-scenario1.png)

|Cenário  |Estrutura  |
|---------|---------|
|A Contoso é uma pequena organização que precisa de uma fatura mensal única com custos agrupados por departamentos – marketing e engenharia.  | Um perfil de cobrança para a Contoso e uma seção da fatura para os departamentos de marketing e engenharia. |

![Gráfico de informações para um cenário de cobrança múltipla.](./media/mca-section-invoice/organize-billing-scenario2.png)

|Cenário  |Estrutura  |
|---------|---------|
|A Fabrikam é uma organização de médio porte que precisa de faturas separadas para seus departamentos de engenharia e marketing. Para o departamento de engenharia, eles desejam agrupar os custos por ambientes – produção e desenvolvimento.  | Um perfil de cobrança para os departamentos de marketing e engenharia. Para o departamento de engenharia, uma seção da fatura para o ambiente de produção e outra para o ambiente de desenvolvimento. |

![Gráfico de informações para um cenário de cobrança múltipla, com cobrança separada para ambientes de produção e desenvolvimento.](./media/mca-section-invoice/organize-billing-scenario3.png)

## <a name="create-a-new-invoice-section"></a>Criar uma seção da fatura

Para criar uma seção da fatura, você precisa ser um **proprietário do perfil de cobrança** ou um **colaborador do perfil de cobrança**. Para obter mais informações, confira [Gerenciar seções da fatura para o perfil de cobrança](understand-mca-roles.md#manage-invoice-sections-for-billing-profile).

1. Entre no [portal do Azure](https://portal.azure.com).

2. Pesquise **Gerenciamento de Custos + Cobrança**.

   ![Captura de tela que mostra a pesquisa de Gerenciamento de Custos + Cobrança no portal](./media/mca-section-invoice/search-cmb.png)

3. Selecione **Perfis de cobrança** no painel esquerdo. Na lista, selecione um perfil de cobrança. A nova seção será exibida na fatura do perfil de cobrança selecionado.

   [![Captura de tela que mostra a lista de perfil de cobrança.](./media/mca-section-invoice/mca-select-profile.png)](./media/mca-section-invoice/mca-select-profile-zoomed-in.png#lightbox)

4. Selecione **Seções da fatura** no painel esquerdo e, em seguida, selecione **Adicionar** na parte superior da página.

   [![Captura de tela que mostra a adição de seções da fatura](./media/mca-section-invoice/mca-list-invoice-sections.png)](./media/mca-section-invoice/mca-list-invoice-sections-zoomed-in.png#lightbox)

5. Insira um nome para a seção da fatura.

   [![Captura de tela que mostra a página de criação da seção da fatura](./media/mca-section-invoice/mca-create-invoice-section.png)](./media/mca-section-invoice/mca-create-invoice-section-zoomed-in.png#lightbox)

6. Selecione **Criar**.

## <a name="create-a-new-billing-profile"></a>Criar um perfil de cobrança

Para criar um perfil de cobrança, você precisa ser um **proprietário da conta de cobrança** ou um **colaborador da conta de cobrança**. Para obter mais informações, confira [Gerenciar perfis de cobrança para a conta de cobrança](understand-mca-roles.md#manage-billing-profiles-for-billing-account).

> [!IMPORTANT]
>
> A criação de perfis de cobrança adicionais pode afetar o custo geral. Para obter mais informações, confira [Coisas a serem consideradas ao adicionar novos perfis de cobrança](#things-to-consider-when-adding-new-billing-profiles).

1. Entre no [portal do Azure](https://portal.azure.com).

2. Pesquise **Gerenciamento de Custos + Cobrança**.

   ![Captura de tela que mostra a pesquisa de Gerenciamento de Custos + Cobrança no portal](./media/mca-section-invoice/search-cmb.png)

3. Selecione **Perfis de cobrança** no painel esquerdo e, em seguida, selecione **Adicionar** na parte superior da página.

   [![Captura de tela que mostra a lista de perfil de cobrança com a opção Adicionar selecionada.](./media/mca-section-invoice/mca-list-profiles.png)](./media/mca-section-invoice/mca-list-profiles-zoomed-in.png#lightbox)

    > [!Note]
    >
    > Se o botão Adicionar não é exibido na página Perfil de cobrança, o recurso não está disponível para sua conta. Atualmente, ele só está disponível para contas que foram configuradas enquanto trabalhavam com um representante da Microsoft.

4. Preencha o formulário e clique em **Criar**.

   [![Captura de tela que mostra a página de criação do perfil de cobrança](./media/mca-section-invoice/mca-add-profile.png)](./media/mca-section-invoice/mca-add-profile-zoomed-in.png#lightbox)

    |Campo  |Definição  |
    |---------|---------|
    |Nome     | Um nome de exibição que ajuda você a identificar com facilidade o perfil de cobrança no portal do Azure.  |
    |Número da OC    | Um número de ordem de compra opcional. O número da OC será exibido nas faturas geradas para o perfil de cobrança. |
    |Endereço de cobrança   | O endereço para cobrança será exibido nas faturas geradas para o perfil de cobrança. |
    |Fatura por email   | Marque a caixa Fatura por email para receber as faturas deste perfil de cobrança por email. Se não fizer isso, poderá exibir e baixar as faturas no portal do Azure.|

5. Selecione **Criar**.

## <a name="link-charges-to-invoice-sections-and-billing-profiles"></a>Vincular encargos a seções de fatura e perfis de cobrança

Depois de personalizar sua conta de cobrança com base em suas necessidades, você poderá vincular assinaturas e outros produtos à seção da fatura desejada e ao perfil de cobrança.

### <a name="link-a-new-subscription"></a>Vincular uma nova assinatura

1. Entre no [portal do Azure](https://portal.azure.com).

2. Pesquise **Assinaturas**.

   [![Captura de tela que mostra a pesquisa de assinatura no portal do Azure.](./media/mca-section-invoice/search-subscriptions.png)](./media/mca-section-invoice/search-subscriptions.png#lightbox)

3. Selecione **Adicionar** na parte superior da página.

   ![Captura de tela que mostra o botão Adicionar na exibição Assinaturas para uma nova assinatura.](./media/mca-section-invoice/subscription-add.png)

4. Caso você tenha acesso a várias contas de cobrança, selecione a sua conta de cobrança do Contrato de Cliente da Microsoft.

   ![Captura de tela que mostra o painel Criar assinatura.](./media/mca-section-invoice/mca-create-azure-subscription.png)

5. Selecione o perfil de cobrança que será cobrado pelo uso da assinatura. Os encargos para uso do Azure e outras compras nesta assinatura serão cobrados na fatura do perfil de cobrança selecionado.

6. Selecione a seção Fatura para vincular os encargos da assinatura. Os encargos serão exibidos nesta seção na fatura do perfil de cobrança.

7. Selecione um plano do Azure e insira um nome amigável para sua assinatura.

9. Clique em **Criar**.  

### <a name="link-existing-subscriptions-and-products"></a>Vincular assinaturas e produtos existentes

Se tiver assinaturas do Azure existentes ou outros produtos, como o Azure Marketplace e os recursos de origem do aplicativo, você poderá movê-los de sua seção da fatura existente para outra seção da fatura para reorganizar os custos.

> [!IMPORTANT]
>
> Assinaturas e outros produtos só podem ser movidos entre seções da fatura que pertencem ao mesmo perfil de cobrança. A movimentação de assinaturas e produtos entre seções da fatura em perfis de cobrança diferentes não é compatível.

1. Entre no [portal do Azure](https://portal.azure.com).

2. Pesquise **Gerenciamento de Custos + Cobrança**.

   ![Captura de tela que mostra a pesquisa no portal do Azure para Gerenciamento de Custos e Cobrança.](./media/mca-section-invoice/search-cmb.png)

3. Para vincular uma assinatura a uma nova seção da fatura, selecione **Assinaturas do Azure** do lado esquerdo da tela. Para outros produtos, como o Azure Marketplace e os recursos de origem do aplicativo, selecione **Encargos recorrentes**.

   [![Captura de tela que mostra a opção para alterar a seção da fatura](./media/mca-section-invoice/mca-select-change-invoice-section.png)](./media/mca-section-invoice/mca-select-change-invoice-section.png#lightbox)

4. Na página, clique nas reticências (três pontos) para a assinatura ou o produto que você deseja vincular a uma nova seção da fatura. Selecione **Alterar seção da fatura**.

5. Selecione a nova seção da fatura no menu suspenso. A lista suspensa mostrará apenas seções da fatura associadas ao mesmo perfil de cobrança da seção da fatura existente.

    [![Captura de tela que mostra a seleção de uma nova seção da fatura](./media/mca-section-invoice/mca-select-new-invoice-section.png)](./media/mca-section-invoice/mca-select-new-invoice-section-zoomed-in.png#lightbox)

6. Clique em **Salvar**.

## <a name="things-to-consider-when-adding-new-billing-profiles"></a>Coisas a serem consideradas ao adicionar novos perfis de cobrança

### <a name="azure-usage-charges-may-be-impacted"></a>Os encargos de uso do Azure podem ser afetados

Em sua conta de cobrança para um Contrato de Cliente da Microsoft, o uso do Azure é agregado mensalmente para cada perfil de cobrança. Os preços dos recursos do Azure com preços em níveis são determinados com base no uso de cada perfil de cobrança separadamente. O uso não é agregado nos perfis de cobrança ao calcular o preço. Isso pode afetar o custo geral do uso do Azure para contas com vários perfis de cobrança.

Vejamos um exemplo de como os custos variam para dois cenários. Os preços usados nos cenários são apenas para fins de exemplo e não representam os preços reais dos serviços do Azure.

#### <a name="you-only-have-one-billing-profile"></a>Você tem apenas um perfil de cobrança.

Vamos supor que você esteja usando o Armazenamento de Blobs de Blocos do Azure, que custa USD 0,00184 por GB para os primeiros 50 TB (terabytes) e, em seguida, USD 0,00177 por GB para os próximos 450 TB (terabytes). Você usou 100 TB nas assinaturas que são cobradas no seu perfil de cobrança. Aqui está o quanto você seria cobrado.

|  Preço do nível (USD) |Quantidade | Valor (USD)|
|---------|---------|---------|
|1,84 por TB para os primeiros 50 TB/mês    | 50 TB        | 92,0   |
|1,77 por TB para os próximos 450 TB/mês    |  50 TB         | 88,5   |
|Total     |     100 TB  | 180,5

O total de encargos para usar 100 TB de dados neste cenário é de **180,5**

#### <a name="you-have-multiple-billing-profiles"></a>Você tem vários perfis de cobrança.

Agora, vamos supor que você criou outro perfil de cobrança e usou 50 GB por meio de assinaturas que são cobradas no primeiro perfil de cobrança, bem como 50 GB por meio de assinaturas que são cobradas no segundo perfil de cobrança. Aqui está o quanto você seria cobrado.

`Charges for the first billing profile`

|  Preço do nível (USD) |Quantidade | Valor (USD)|
|---------|---------|---------|
|1,84 por TB para os primeiros 50 TB/mês    | 50 TB        | 92,0  |
|1,77 por TB para os próximos 450 TB/mês    |  0 TB         | 0.0  |
|Total     |     50 TB  | 92,0

`Charges for the second billing profile`

|  Preço do nível (USD) |Quantidade | Valor (USD)|
|---------|---------|---------|
|1,84 por TB para os primeiros 50 TB/mês    | 50 TB        | 92,0  |
|1,77 por TB para os próximos 450 TB/mês    |  0 TB         | 0.0  |
|Total     |     50 TB  | 92,0

O total de encargos para usar 100 TB de dados neste cenário é de **184,0** (92,0 * 2).

### <a name="azure-reservation-benefits-might-not-apply-to-all-subscriptions"></a>Os benefícios de reserva do Azure podem não se aplicar a todas as assinaturas

As reservas do Azure com escopo compartilhado são aplicadas a assinaturas em um perfil de cobrança e não são compartilhadas entre os perfis de cobrança.

![Gráfico de informações para aplicativo de reserva para uma estrutura de conta de cobrança diferente](./media/mca-section-invoice/mca-reservations-benefits-by-bg.png)

Na imagem acima, a Contoso tem duas assinaturas. O benefício de reserva do Azure é aplicado de maneira diferente, dependendo de como a conta de cobrança é estruturada. No cenário à esquerda, o benefício de reserva é aplicado às duas assinaturas que são cobradas no perfil de cobrança de engenharia. No cenário à direita, o benefício de reserva é aplicado à assinatura 1, visto que é a única assinatura que é cobrada no perfil de cobrança de engenharia.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso ao Contrato de Cliente da Microsoft
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se precisar de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

## <a name="next-steps"></a>Próximas etapas

- [Criar uma assinatura adicional do Azure para o Contrato de Cliente da Microsoft](create-subscription.md)
- [Gerenciar as funções de cobrança no portal do Azure](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)
- [Obter a propriedade de cobrança das assinaturas do Azure de usuários em outras contas de cobrança](mca-request-billing-ownership.md)
