---
title: Locatários, usuários e funções em cenários de Lighthouse do Azure
description: Entenda os conceitos de locatários, usuários e funções do Azure Active Directory, além de como eles podem ser usados em cenários do Azure Lighthouse.
ms.date: 01/14/2021
ms.topic: conceptual
ms.openlocfilehash: d78828cc739030f8e456c64885d77ddf59dd13fb
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2021
ms.locfileid: "98233909"
---
# <a name="tenants-users-and-roles-in-azure-lighthouse-scenarios"></a>Locatários, usuários e funções em cenários de Lighthouse do Azure

Antes de integrar os clientes do [Azure Lighthouse](../overview.md), é importante entender como os locatários, os usuários e as funções do Azure Active Directory (Azure AD) funcionam, além de como eles podem ser usados em cenários de Lighthouse do Azure.

Um *locatário* é uma instância dedicada e confiável do Azure AD. Normalmente, cada locatário representa uma única organização. O [Gerenciamento de recursos delegado do Azure](azure-delegated-resource-management.md) permite a projeção lógica de recursos de um locatário para outro locatário. Isso permite que os usuários no locatário de gerenciamento (como um que pertença a um provedor de serviços) acessem recursos delegados no locatário de um cliente ou permite que [empresas com vários locatários centralizem suas operações de gerenciamento](enterprise.md).

Para alcançar essa projeção lógica, uma assinatura (ou um ou mais grupos de recursos em uma assinatura) no locatário do cliente deve ser *integrada* ao Azure Lighthouse. Esse processo de integração pode ser feito [por meio de modelos do Azure Resource Manager](../how-to/onboard-customer.md) ou ao [publicar uma oferta pública ou privada no Azure Marketplace](../how-to/publish-managed-services-offers.md).

Seja qual for o método de integração que você escolher, será necessário definir as *autorizações*. Cada autorização especifica um **PrincipalId** que terá acesso aos recursos delegados e uma função interna que define as permissões que cada um desses usuários terá para esses recursos. Essa **PrincipalId** define um usuário, grupo ou entidade de serviço do Azure AD no locatário de gerenciamento.

> [!NOTE]
> A menos que especificado explicitamente, as referências a um "usuário" na documentação do Azure Lighthouse podem ser aplicadas a um usuário, grupo ou entidade de serviço do Azure AD em uma autorização.

## <a name="best-practices-for-defining-users-and-roles"></a>Práticas recomendadas para definir usuários e funções

Ao criar suas autorizações, recomendamos as melhores práticas abaixo:

- Na maioria dos casos, é melhor atribuir permissões a um grupo de usuários ou entidade de serviço do Azure AD, em vez de a uma série de contas de usuário individuais. Assim você pode adicionar ou remover o acesso de usuários individuais sem precisar atualizar e publicar o plano novamente quando os requisitos de acesso forem alterados.
- Siga o princípio de privilégios mínimos para que os usuários tenham apenas as permissões necessárias para concluir seu trabalho, ajudando a reduzir a chance de erros acidentais. Para obter mais informações, consulte [práticas recomendadas de segurança](../concepts/recommended-security-practices.md).
- Inclua um usuário com a [Função Excluir Atribuição de Registro de Serviços Gerenciados](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) para que você possa [remover o acesso à delegação](../how-to/remove-delegation.md) posteriormente, se necessário. Se essa função não for atribuída, os recursos delegados só poderão ser removidos por um usuário no locatário do cliente.
- Certifique-se de que qualquer usuário que precise [exibir a página Meus clientes no portal do Azure](../how-to/view-manage-customers.md) tenha a função [Leitor](../../role-based-access-control/built-in-roles.md#reader) (ou outra função interna que inclua acesso de leitura).

> [!IMPORTANT]
> Para adicionar permissões para um grupo do Azure AD, o **tipo de grupo** deve ser definido como **segurança**. Essa opção é selecionada quando o grupo é criado. Para obter mais informações, consulte [Criar um grupo básico e adicionar membros usando o Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="role-support-for-azure-lighthouse"></a>Suporte de função para Lighthouse do Azure

Ao definir uma autorização, cada conta de usuário deve ser atribuída a uma das [funções internas do Azure](../../role-based-access-control/built-in-roles.md). As funções personalizadas e as [funções de administrador de assinatura clássica](../../role-based-access-control/classic-administrators.md) não têm suporte.

Atualmente, todas as [funções internas](../../role-based-access-control/built-in-roles.md) têm suporte no Azure Lighthouse, com as seguintes exceções:

- Não há suporte para a função [Proprietário](../../role-based-access-control/built-in-roles.md#owner).
- Não há suporte para nenhuma função interna com permissão [DataActions](../../role-based-access-control/role-definitions.md#dataactions).
- A função interna [Administrador de Acesso de Usuário](../../role-based-access-control/built-in-roles.md#user-access-administrator) tem suporte, mas apenas para a finalidade limitada de [atribuir funções a uma identidade gerenciada no locatário do cliente](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant). Nenhuma outra permissão normalmente concedida por essa função será aplicada. Se você definir um usuário com essa função, também deverá especificar as funções internas que esse usuário pode atribuir às identidades gerenciadas.

> [!NOTE]
> Depois que uma nova função interna aplicável for adicionada ao Azure, ela poderá ser atribuída durante a [integração de um cliente usando modelos de Azure Resource Manager](../how-to/onboard-customer.md). Pode haver um atraso antes que a função recém-adicionada fique disponível no Partner Center ao [publicar uma oferta de serviço gerenciado](../how-to/publish-managed-services-offers.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as [práticas recomendadas de segurança para o Azure Lighthouse](recommended-security-practices.md).
- Integre seus clientes ao Azure Lighthouse, [usando modelos de Azure Resource Manager](../how-to/onboard-customer.md) ou [publicando uma oferta privada ou pública de serviços gerenciados para o Azure Marketplace](../how-to/publish-managed-services-offers.md).
