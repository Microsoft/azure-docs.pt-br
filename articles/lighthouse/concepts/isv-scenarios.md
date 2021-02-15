---
title: Lighthouse do Azure em cenários de ISV
description: Os recursos do Azure Lighthouse podem ser usados por ISVs para obter mais flexibilidade com ofertas de clientes.
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: d6a12a51d360ad236563b871dbd94cc442ade434
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2020
ms.locfileid: "97696323"
---
# <a name="azure-lighthouse-in-isv-scenarios"></a>Lighthouse do Azure em cenários de ISV

Um cenário comum para o [Azure Lighthouse](../overview.md) é um provedor de serviços que gerencia recursos em seus locatários do Azure Active Directory (Azure AD) de seus clientes. Os recursos do Azure Lighthouse também podem ser usados por ISVs (fornecedores independentes de software) usando ofertas baseadas em SaaS com seus clientes. O Azure Lighthouse pode ser especialmente útil para ISVs que estão oferecendo serviços gerenciados ou suporte que exigem acesso ao escopo da assinatura.

## <a name="managed-service-offers-in-azure-marketplace"></a>Ofertas de serviço gerenciado no Azure Marketplace

Como um ISV, talvez você já tenha publicado soluções para o Azure Marketplace. Se você oferecer serviços gerenciados aos seus clientes, poderá fazer isso publicando uma oferta de serviço gerenciado. Essas ofertas simplificam o processo de integração e tornam seus serviços mais escalonáveis para quantos clientes forem necessários. O Azure Lighthouse dá suporte a uma ampla gama de [tarefas e cenários de gerenciamento](cross-tenant-management-experience.md#enhanced-services-and-scenarios) que podem ser usados para fornecer valor aos seus clientes.

Para obter mais informações, consulte [publicar uma oferta de serviço gerenciado no Azure Marketplace](../how-to/publish-managed-services-offers.md).

## <a name="using-azure-lighthouse-with-azure-managed-applications"></a>Usando o Azure Lighthouse com aplicativos gerenciados do Azure

[Os aplicativos gerenciados do Azure](../../azure-resource-manager/managed-applications/overview.md) são outra maneira que os ISVs podem fornecer serviços aos seus clientes. Você pode usar o Azure Lighthouse junto com seus aplicativos gerenciados do Azure para habilitar cenários avançados.

Para obter mais informações, consulte [Azure Lighthouse e aplicativos gerenciados do Azure](managed-applications.md).

## <a name="saas-based-multi-tenant-offerings"></a>Ofertas multilocatário baseadas em SaaS

Um cenário adicional é onde o ISV hospeda os recursos em uma assinatura em seu próprio locatário e, em seguida, usa o Azure Lighthouse para permitir que os clientes acessem esses recursos. O cliente pode fazer logon em seu próprio locatário e acessar esses recursos conforme necessário. Os ISVs mantêm seu IP em seu próprio locatário e podem usar seus próprios planos de suporte para gerar tíquetes relacionados à solução hospedada em seu locatário, em vez de usar o plano do cliente. Como os recursos estão no locatário do ISV, todas as ações podem ser executadas diretamente pelo ISV, como o logon em VMs, a instalação de aplicativos e a execução de tarefas de manutenção.

Nesse cenário, os usuários no locatário do cliente são essencialmente concedidos acesso como um "gerenciamento de locatário", mesmo que o cliente não esteja gerenciando os recursos do ISV. Como eles estão acessando o locatário do ISV diretamente, é importante conceder apenas as permissões mínimas necessárias, para que os clientes não possam fazer alterações inadvertidamente na solução ou outros recursos de ISV.

Para habilitar essa arquitetura, o ISV precisa obter a ID de objeto para um grupo de usuários no locatário do Azure AD do cliente, juntamente com sua ID de locatário. Em seguida, o ISV cria um modelo do ARM concedendo a esse grupo de usuários as permissões apropriadas e o [implanta na assinatura do ISV](../how-to/onboard-customer.md) que contém os recursos que o cliente acessará.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as [experiências de gerenciamento entre locatários](cross-tenant-management-experience.md).
- Saiba mais sobre o [Gerenciamento de recursos delegados do Azure](azure-delegated-resource-management.md).