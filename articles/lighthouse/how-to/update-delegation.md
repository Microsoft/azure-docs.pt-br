---
title: Atualizar uma delegação
description: Saiba como atualizar uma delegação para um cliente anteriormente integrado ao Azure Lighthouse.
ms.date: 02/16/2021
ms.topic: how-to
ms.openlocfilehash: f0ed5222cdbac3d0e4d193941c2a6f233d15938c
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100555759"
---
# <a name="update-a-delegation"></a>Atualizar uma delegação

Depois que você tiver integrado uma assinatura (ou grupo de recursos) ao Lighthouse do Azure, talvez seja necessário fazer alterações. Por exemplo, o cliente pode desejar que você execute tarefas de gerenciamento adicionais que exijam uma função interna do Azure diferente, ou talvez seja necessário alterar o locatário para o qual uma assinatura de cliente é delegada.

> [!TIP]
> Embora possamos nos referimos a provedores de serviços e clientes neste tópico, as [empresas que gerenciam vários locatários](../concepts/enterprise.md) podem usar o mesmo processo para configurar o Azure Lighthouse e consolidar sua experiência de gerenciamento.

Se você tiver [integrado seu cliente por meio de modelos de Azure Resource Manager (modelos ARM)](onboard-customer.md), uma nova implantação deverá ser executada para esse cliente. Dependendo do que você está alterando, talvez você queira atualizar a oferta original ou remover a oferta original e criar uma nova.

- **Se você estiver alterando somente autorizações**: você pode atualizar sua delegação alterando apenas a seção **autorizações** do modelo ARM.
- **Se você estiver alterando o locatário de gerenciamento**: você deve criar um novo modelo de ARM usando com um **mspOfferName** diferente de sua oferta anterior.

## <a name="update-your-arm-template"></a>Atualizar seu modelo do ARM

Para atualizar sua delegação, você precisará implantar um modelo do ARM que inclui as alterações que você gostaria de fazer.

Se você estiver atualizando somente autorizações (como adicionar um novo grupo de usuários com uma função que você não tenha incluído anteriormente ou alterar a função de um usuário existente), poderá usar o mesmo **mspOfferName** que o [modelo do ARM](onboard-customer.md#create-an-azure-resource-manager-template) que você usou para a delegação anterior. Você pode usar o modelo anterior como um ponto de partida. Em seguida, faça as alterações necessárias, como substituir uma função interna do Azure por outra ou adicionar uma autorização completamente nova ao modelo.

Se você alterar o **mspOfferName**, isso será considerado uma oferta nova e separada. Isso será necessário se você estiver alterando o locatário de gerenciamento.

Não é necessário alterar o **mspOfferName** se o locatário de gerenciamento permanecer o mesmo. Na maioria dos casos, é recomendável ter apenas um **mspOfferName** em uso pelo mesmo cliente e gerenciando o locatário. Se você optar por alterá-lo mesmo assim, certifique-se de que a delegação anterior do cliente seja removida antes de implantar uma nova.

## <a name="remove-the-previous-delegation"></a>Remover a delegação anterior

Antes de executar uma nova implantação, talvez você queira [remover o acesso à delegação anterior](remove-delegation.md). Isso garante que todas as permissões anteriores sejam removidas, permitindo que você inicie a limpeza com os usuários/grupos e funções exatos que devem ser aplicados no futuro.

> [!IMPORTANT]
> Se você usar um novo **mspOfferName** e mantiver qualquer um dos mesmos valores de **PrincipalId** , deverá remover o acesso à delegação anterior antes de implantar a nova oferta. Se você não remover a oferta primeiro, os usuários que receberam permissão anteriormente poderão perder o acesso completamente devido a atribuições conflitantes.

Se você estiver alterando o locatário de gerenciamento, poderá deixar a oferta anterior em vigor se desejar que ambos os locatários continuem a ter acesso. Se você quiser que o novo locatário de gerenciamento tenha acesso, a oferta anterior deverá ser removida. Isso pode ser feito antes ou depois da integração da nova oferta.

Se você estiver atualizando a oferta para ajustar somente autorizações e mantiver o mesmo **mspOfferName**, você não precisará remover a delegação anterior. A nova implantação substituirá a delegação anterior e somente as autorizações no modelo mais recente serão aplicadas.

:::image type="content" source="../media/update-delegation.jpg" alt-text="Diagrama mostrando quando alterar mspOfferName e remover uma delegação anterior.":::

A remoção do acesso à delegação pode ser feita por qualquer usuário no locatário de gerenciamento que recebeu a [função de exclusão de atribuição de registro de serviços gerenciados](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) na delegação original. Se nenhum usuário no seu locatário de gerenciamento tiver essa função, você poderá pedir ao cliente para [remover o acesso à oferta no portal do Azure](view-manage-service-providers.md#add-or-remove-service-provider-offers).

> [!TIP]
> Se você tiver removido a delegação anterior seguindo as etapas acima e ainda não conseguir implantar o novo modelo do ARM, talvez seja necessário [remover completamente a definição de registro](/powershell/module/az.managedservices/remove-azmanagedservicesdefinition). Isso pode ser feito por qualquer usuário com uma função que tenha a `Microsoft.Authorization/roleAssignments/write` permissão, como [proprietário](../../role-based-access-control/built-in-roles.md#owner), no locatário do cliente.  

## <a name="deploy-the-arm-template"></a>Implantar o modelo do ARM

O cliente pode [implantar o modelo atualizado](onboard-customer.md#deploy-the-azure-resource-manager-templates) da mesma maneira que fazia anteriormente: no portal do Azure, usando o PowerShell ou usando CLI do Azure.

Após a conclusão da implantação, [confirme se ela foi bem-sucedida](onboard-customer.md#confirm-successful-onboarding). As autorizações atualizadas estarão em vigor para a assinatura ou os grupos de recursos que o cliente tiver delegado.

## <a name="updating-managed-service-offers"></a>Atualizando ofertas de serviço gerenciado

Se você integra seu cliente por meio de uma oferta de serviço gerenciado publicada no Azure Marketplace e deseja atualizar autorizações, você pode atualizar a delegação [publicando uma nova versão da sua oferta](../../marketplace/partner-center-portal/update-existing-offer.md) com as [autorizações](../../marketplace/plan-managed-service-offer.md) que você deseja usar atualizadas no plano para esse cliente. O cliente será capaz de atualizar para a versão mais recente no portal do Azure.

Se você quiser alterar o locatário de gerenciamento, será necessário [criar e publicar uma nova oferta de serviço gerenciado](../../marketplace/plan-managed-service-offer.md) para o cliente aceitar.

> [!TIP]
> Como mencionado anteriormente, recomendamos que você não use várias ofertas diferentes entre o mesmo cliente e o gerenciamento de locatário. Se você publicar uma nova oferta para o mesmo cliente que usa o mesmo locatário de gerenciamento, certifique-se de que a oferta anterior seja removida antes que o cliente aceite a oferta mais recente.

## <a name="next-steps"></a>Próximas etapas

- [Exiba e gerencie clientes](view-manage-customers.md) acessando **Meus clientes** no portal do Azure.
- Aprenda a [remover o acesso a uma delegação](remove-delegation.md) que foi integrada anteriormente.
