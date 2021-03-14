---
title: Exibir e gerenciar provedores de serviços
description: Os clientes podem usar a página Provedores de serviços no portal do Azure para exibir informações sobre provedores de serviços, ofertas de provedor de serviço e recursos delegados.
ms.date: 02/16/2021
ms.topic: how-to
ms.openlocfilehash: f6ee5fb154d75ff715acf99c5184cd1652ccdb80
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100555585"
---
# <a name="view-and-manage-service-providers"></a>Exibir e gerenciar provedores de serviços

A página **provedores de serviço** no [portal do Azure](https://portal.azure.com) fornece aos clientes controle e visibilidade para seus provedores de serviço que usam o [Azure Lighthouse](../overview.md). Os clientes podem exibir detalhes sobre os provedores de serviços, delegar recursos específicos, procurar novas ofertas, remover o acesso do provedor de serviços e muito mais.

Para exibir a página **provedores de serviço** no portal do Azure, selecione **todos os serviços**, em seguida, pesquise provedores de **serviço** e selecione-o. Você também pode encontrar essa página digitando "provedores de serviço" ou "Azure Lighthouse" na caixa de pesquisa próxima à parte superior do portal do Azure.

> [!NOTE]
> Para exibir a página **provedores de serviços** , um usuário no locatário do cliente deve ter a [função interna do leitor](../../role-based-access-control/built-in-roles.md#reader) (ou outra função interna que inclui acesso ao leitor).
>
> Para adicionar ou atualizar ofertas, delegar recursos e remover ofertas, o usuário deve ter uma função com a `Microsoft.Authorization/roleAssignments/write` permissão, como [proprietário](../../role-based-access-control/built-in-roles.md#owner).

Tenha em mente que a página **provedores de serviço** mostra apenas informações sobre os provedores de serviço que têm acesso às assinaturas ou aos grupos de recursos do cliente por meio do Azure Lighthouse. Se um cliente trabalha com provedores de serviço adicionais que não usam o Azure Lighthouse, você não verá informações sobre esses provedores de serviços aqui.

## <a name="view-service-provider-details"></a>Exibir detalhes do provedor de serviços

Para exibir detalhes sobre os provedores de serviço atuais usando o Azure Lighthouse para trabalhar no locatário do cliente, selecione **ofertas de provedor de serviço** no lado esquerdo da página provedores de **serviço** .

Para cada oferta, você verá o nome do provedor de serviços e a oferta associada a ele. Você pode selecionar uma oferta para exibir uma descrição e outros detalhes, incluindo as atribuições de função que o provedor de serviços recebeu.

Na coluna **delegações** , você pode ver quantas assinaturas e/ou grupos de recursos foram delegados ao provedor de serviços para essa oferta. O provedor de serviços poderá acessar e gerenciar essas assinaturas e/ou grupos de recursos de acordo com os níveis de acesso especificados na oferta.

## <a name="add-or-remove-service-provider-offers"></a>Adicionar ou remover ofertas do provedor de serviços

Para adicionar uma nova oferta do provedor de serviços da página **ofertas do provedor de serviços** , selecione **Adicionar oferta**. Selecione **ofertas privadas** para exibir as ofertas que um provedor de serviços publicou para este cliente. Em seguida, você pode selecionar essa oferta na tela de **ofertas particulares** e, em seguida, selecionar **Configurar + assinar**.

Você pode remover uma oferta de provedor de serviços a qualquer momento selecionando o ícone de lixeira na linha dessa oferta. Depois de confirmar a exclusão, esse provedor de serviços não terá mais acesso aos recursos que antes eram delegados para essa oferta.

## <a name="delegate-resources"></a>Delegar recursos

Antes que um provedor de serviços possa acessar e gerenciar recursos do cliente, uma ou mais assinaturas e/ou grupos de recursos específicos devem ser delegados. Se um cliente tiver aceitado uma oferta, mas ainda não tiver delegado nenhum recurso, ele verá uma observação na parte superior da seção **ofertas do provedor de serviços** . Isso permite que o cliente saiba que ele precisa executar uma ação antes que o provedor de serviços possa acessar os recursos de qualquer cliente.

Para delegar assinaturas ou grupos de recursos:

1. Marque a caixa para a linha que contém o provedor de serviços, a oferta e o nome. Em seguida, selecione **Recursos delegados** na parte superior da tela.
1. Na seção **Detalhes da oferta** da página **Recursos delegados**, examine os detalhes sobre o provedor de serviços e a oferta. Para examinar as atribuições de função da oferta, selecione **Clique aqui para ver os detalhes da oferta selecionada**.
1. Na seção **Delegar**, selecione **Delegar assinaturas** ou **Delegar grupos de recursos**.
1. Escolha as assinaturas e/ou grupos de recursos que você gostaria de delegar para essa oferta e selecione **Adicionar**.
1. Marque a caixa de seleção na parte inferior da página para confirmar que você deseja conceder a esse provedor de serviços acesso aos recursos que você selecionou e, em seguida, selecione **Delegar**.

## <a name="update-service-provider-offers"></a>Atualizar as ofertas do provedor de serviços

Após um cliente adicionar uma oferta, um provedor de serviços poderá publicar uma versão atualizada da mesma oferta no Azure Marketplace. Por exemplo, talvez eles queiram adicionar uma nova definição de função. Se uma nova versão da oferta tiver sido publicada, a página de **ofertas do provedor de serviços** mostrará um ícone de "atualização" na linha dessa oferta. Selecione este ícone para ver as diferenças entre a versão atual da oferta e a nova.

 ![Ícone de oferta de atualização](../media/update-offer.jpg)

Depois de revisar as alterações, o cliente pode optar por atualizar para a nova versão. As autorizações e outras configurações especificadas na nova versão serão aplicadas a quaisquer assinaturas e/ou grupos de recursos que foram delegados para essa oferta.

## <a name="view-delegations"></a>Exibir delegações

As delegações representam as atribuições de função que concedem permissões ao provedor de serviços para os recursos que um cliente delegou. Para exibir essas informações, selecione **Delegações** no lado esquerdo da página **Provedores de serviços**.

Os filtros na parte superior da página permitem que você classifique e agrupe suas informações de delegação. Você também pode filtrar por clientes, ofertas ou palavras-chave específicas.

> [!NOTE]
> Os clientes não verão essas atribuições de função ou todos os usuários do locatário do provedor de serviços que receberam essas funções, ao [exibir as atribuições de função para o escopo delegado no portal do Azure](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) ou via APIs.

## <a name="audit-delegations-in-your-environment"></a>Auditar delegações em seu ambiente

Os clientes podem querer obter visibilidade das assinaturas e/ou dos grupos de recursos que foram delegados para o Azure Lighthouse. Isso é especialmente útil para os clientes com um grande número de assinaturas, ou que têm muitos usuários que executam tarefas de gerenciamento.

Fornecemos uma [definição de política interna Azure Policy](../../governance/policy/samples/built-in-policies.md#lighthouse) para [auditar a delegação de escopos para um locatário de gerenciamento](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/Lighthouse_Delegations_Audit.json). Você pode atribuir essa política a um grupo de gerenciamento que inclui todas as assinaturas que você deseja auditar. Quando você verifica a conformidade com essa política, quaisquer assinaturas delegadas e/ou grupos de recursos (dentro do grupo de gerenciamento ao qual a política é atribuída) serão mostrados em um estado não compatível. Em seguida, você pode examinar os resultados e confirmar se não há delegações inesperadas.

Outra [definição de política interna](../../governance/policy/samples/built-in-policies.md#lighthouse) permite restringir as [delegações a locatários de gerenciamento específicos](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/AllowCertainManagingTenantIds_Deny.json). De forma semelhante, essa política pode ser aplicada a um grupo de gerenciamento que inclui todas as assinaturas para as quais você deseja limitar as delegações. Depois que a política for implantada, qualquer tentativa de delegar uma assinatura a um locatário fora daqueles que você especificar será negada.

Para obter mais informações sobre como atribuir uma política e exibir os resultados do estado de conformidade, consulte [início rápido: criar uma atribuição de política](../../governance/policy/assign-policy-portal.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Azure Lighthouse](../overview.md).
- Saiba como [auditar a atividade do provedor de serviços](view-service-provider-activity.md).
- Saiba como os provedores de serviços podem [Exibir e gerenciar clientes](view-manage-customers.md) na página **meus clientes** no portal do Azure.
- Saiba como as [empresas que gerenciam vários locatários](../concepts/enterprise.md) podem usar o Azure Lighthouse para consolidar sua experiência de gerenciamento.

