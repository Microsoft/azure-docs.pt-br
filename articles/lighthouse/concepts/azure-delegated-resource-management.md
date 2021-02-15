---
title: Gerenciamento de recursos delegados do Azure
description: O gerenciamento de recursos delegado do Azure é uma parte fundamental do Azure Lighthouse, permitindo que os provedores de serviços gerenciem recursos delegados em escala com agilidade e precisão.
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: d484e61fc4ab3714eb362b26d64d449890065888
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92203850"
---
# <a name="azure-delegated-resource-management"></a>Gerenciamento de recursos delegados do Azure

O gerenciamento de recursos delegado do Azure é um dos principais componentes do [Azure Lighthouse](../overview.md). Com o gerenciamento de recursos delegados do Azure, os provedores de serviços podem simplificar as experiências de envolvimento e de integração do cliente e gerenciar recursos delegados em escala com agilidade e precisão. Os clientes mantêm o controle sobre quais provedores de serviço podem acessar seus locatários, os clientes mantêm o controle sobre quem pode acessar seu locatário, quais recursos eles podem acessar e quais ações podem ser executadas.

## <a name="what-is-azure-delegated-resource-management"></a>O que é gerenciamento de recursos delegados do Azure?

O gerenciamento de recursos delegados do Azure permite a projeção lógica de recursos de um locatário para outro. Isso permite que usuários autorizados em um locatário do Azure AD (Active Directory) realizem operações de gerenciamento em diferentes locatários do Azure AD que pertencem a seus clientes. Os provedores de serviços podem entrar em seu próprio locatário do Azure AD e ter autorização para trabalhar em grupos de recursos e assinaturas de clientes delegados. Isso permite que eles realizem operações de gerenciamento em nome de seus clientes, sem precisar entrar em cada locatário individual do cliente.

> [!TIP]
> O gerenciamento de recursos delegado do Azure também pode ser usado [em uma empresa que tem vários locatários do Azure ad próprios](enterprise.md) para simplificar o gerenciamento entre locatários.

Com o gerenciamento de recursos delegados do Azure, os usuários autorizados podem trabalhar diretamente no contexto de uma assinatura de cliente sem ter uma conta no locatário do cliente ou ser um coproprietário do locatário do cliente.

A [experiência de gerenciamento entre locatários](cross-tenant-management-experience.md) permite que você trabalhe com mais eficiência com os serviços de gerenciamento do Azure, como Azure Policy, a central de segurança do Azure e muito mais. Todas as atividades do provedor de serviços são controladas no log de atividades, que é armazenado no locatário do cliente (e pode ser exibido por usuários no locatário de gerenciamento). Isso significa que os usuários no gerenciamento e no locatário gerenciado podem identificar facilmente o usuário associado a quaisquer alterações.

Você pode [publicar o novo tipo de oferta de serviço gerenciado no Azure Marketplace](../how-to/publish-managed-services-offers.md) para integrar facilmente os clientes ao Azure Lighthouse. Ou será possível [concluir o processo de integração implantando modelos do Azure Resource Manager](../how-to/onboard-customer.md).

## <a name="how-azure-delegated-resource-management-works"></a>Como o gerenciamento de recursos delegados do Azure funciona

Em um alto nível, veja como o gerenciamento de recursos delegados do Azure funciona:

1. Primeiro, você identifica o acesso (funções) que seus grupos, entidades de serviço ou usuários precisarão gerenciar os recursos do Azure do cliente. A definição de acesso contém a ID de locatário de gerenciamento junto com as identidades de **PrincipalId** do seu locatário mapeado para [valores internos de **RoleDefinition** ](../../role-based-access-control/built-in-roles.md) (colaborador, colaborador de VM, leitor, etc.).
2. Você especifica esse acesso e integra o cliente ao Azure Lighthouse de uma destas duas maneiras:
   - [Publicar uma oferta de serviço gerenciado do Azure Marketplace](../how-to/publish-managed-services-offers.md) (privada ou pública) que o cliente aceitará
   - [Implantar um modelo de Azure Resource Manager no locatário do cliente](../how-to/onboard-customer.md) para uma ou mais assinaturas ou grupos de recursos específicos

3. Depois que o cliente tiver sido integrado, os usuários autorizados poderão entrar no seu locatário de gerenciamento e executar tarefas no escopo determinado do cliente, com base no acesso que você definiu. Os clientes podem examinar as ações do provedor de serviços e ter a opção de remover o acesso, se necessário.

> [!NOTE]
> Você pode gerenciar recursos delegados que estão localizados em [regiões](../../availability-zones/az-overview.md#regions)diferentes. No entanto, a delegação de assinaturas em uma [nuvem nacional](../../active-directory/develop/authentication-national-cloud.md) e na nuvem pública do Azure ou em duas nuvens nacionais separadas não tem suporte.

## <a name="support-for-azure-delegated-resource-management"></a>Suporte para gerenciamento de recursos delegados do Azure

Se precisar de ajuda com relação ao gerenciamento de recursos delegados do Azure, abra uma solicitação de suporte no portal do Azure. Para **Tipo de problema**, escolha **Técnico**. Selecione uma assinatura e, em seguida, selecione **Lighthouse** (em **monitoramento & gerenciamento**).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as [experiências de gerenciamento entre locatários](cross-tenant-management-experience.md).
- Saiba mais sobre [ofertas de serviços gerenciados no Azure Marketplace](managed-services-offers.md).
