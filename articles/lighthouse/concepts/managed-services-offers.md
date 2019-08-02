---
title: Ofertas de Serviços Gerenciados no Azure Marketplace
description: As ofertas de serviços gerenciados permitem que os provedores de serviços vendam ofertas de gerenciamento de recursos a clientes no Azure Marketplace.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: a6fcf5f1d0ac194d60f834fb8d26db019c538410
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67810870"
---
# <a name="managed-services-offers-in-azure-marketplace"></a>Ofertas de Serviços Gerenciados no Azure Marketplace

Este artigo descreve o novo tipo de oferta **Serviços Gerenciados** no [Azure Marketplace](https://azuremarketplace.microsoft.com). As ofertas de serviços gerenciados permitem que você ofereça serviços de gerenciamento de recursos a clientes com o gerenciamento de recursos delegados do Azure. É possível disponibilizar essas ofertas a todos os clientes potenciais ou a apenas um ou clientes mais específicos. Como você cobra os clientes diretamente por custos relacionados a esses serviços gerenciados, não há valores cobrados pela Microsoft.

## <a name="understand-managed-services-offers"></a>Noções básicas sobre ofertas de serviços gerenciados

As ofertas de serviços gerenciados simplificam o processo de integrar clientes para o gerenciamento de recursos delegados do Azure. Depois que um cliente adquire uma oferta no Azure Marketplace, ele poderá especificar quais assinaturas e/ou grupos de recursos devem ser integrados para que os usuários especificados em sua organização possam executar tarefas de administração para o cliente de dentro do locatário da sua organização.

Depois disso, nenhuma ação adicional é exigida pelo cliente ou pelo provedor de serviços para que o cliente seja integrado. Isso ocorre porque, ao definir a oferta no [Portal do Cloud Partner](https://cloudpartner.azure.com/), você cria um manifesto que especifica os usuários, grupos e princípios de serviço do Azure AD que terão acesso aos recursos do cliente usando o gerenciamento de recursos delegados do Azure. junto com funções que definem o nível de acesso dele. Ao atribuir permissões a um grupo do Azure AD em vez de uma série de contas individuais de usuário ou de aplicativo, é possível adicionar ou remover usuários individuais quando seus requisitos de acesso mudam.

## <a name="public-and-private-offers"></a>Ofertas públicas e privadas

Cada oferta de serviços gerenciados inclui um ou mais planos. Esses planos podem ser privados ou públicos.

Se desejar limitar sua oferta a clientes específicos, será possível publicar um plano privado. Quando fizer isso, o plano só poderá ser adquirido para IDs da assinatura específicas que você fornecer. Para saber mais, confira [Ofertas privadas](https://docs.microsoft.com/azure/marketplace/private-offers).

Planos públicos permitem que você promova serviços para novos clientes. Normalmente, eles são mais adequados quando você só requer acesso limitado ao locatário do cliente. Depois de estabelecer uma relação com um cliente, se decidir conceder acesso adicional à sua organização, você poderá fazer isso publicando um novo plano privado somente para esse cliente ou [integrando-os para obter acesso adicional usando os modelos do Azure Resource Manager](../how-to/onboard-customer.md).

Tenha em mente que depois que um plano tiver sido publicado como público, você não poderá alterá-lo para privado. Além disso, não é possível restringir a disponibilidade de um plano público a determinados clientes ou até mesmo a um determinado número de clientes, embora você possa parar de vender o plano completamente caso opte por fazê-lo.

Se apropriado, você pode incluir planos públicos e privados na mesma oferta.

## <a name="publish-managed-service-offers"></a>Publicar ofertas de serviços gerenciados

Para saber como publicar uma oferta de serviços gerenciados, confira [Publicar uma oferta de Serviços Gerenciados no Azure Marketplace](../how-to/publish-managed-services-offers.md). Para obter informações gerais sobre como publicar no Azure Marketplace usando o Portal do Cloud Partner, confira [Guia de publicação do Azure Marketplace e AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) e [Gerenciar ofertas do Azure e do AppSource Marketplace](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [gerenciamento de recursos delegados do Azure](azure-delegated-resource-management.md) e [experiências de gerenciamento entre locatários](cross-tenant-management-experience.md).
- [Publique ofertas de serviços gerenciados](../how-to/publish-managed-services-offers.md) no Azure Marketplace.
