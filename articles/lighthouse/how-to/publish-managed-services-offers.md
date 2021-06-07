---
title: Publicar uma oferta de serviços gerenciados no Azure Marketplace
description: Saiba como publicar uma oferta de serviço gerenciado que integre os clientes ao Azure Lighthouse.
ms.date: 03/31/2021
ms.topic: how-to
ms.openlocfilehash: 3f790c37a36fa1c5092a50c02429260825158c72
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491348"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Publicar uma oferta de serviços gerenciados no Azure Marketplace

Neste artigo, você aprenderá a publicar uma oferta de serviço gerenciado pública ou privada no [Azure Marketplace](https://azuremarketplace.microsoft.com), usando o programa [Marketplace Comercial](../../marketplace/overview.md) do Partner Center. Os clientes que compram a oferta delegam assinaturas ou grupos de recursos, permitindo que você os gerencie por meio do [Azure Lighthouse](../overview.md).

## <a name="publishing-requirements"></a>Publicação de requisitos

Você precisa ter uma conta [válida no Partner Center](../../marketplace/partner-center-portal/create-account.md) para criar e publicar ofertas. Se você ainda não tem uma conta, o [processo de inscrição](https://aka.ms/joinmarketplace) explica as etapas de criação da conta no Partner Center e do registro no programa do Marketplace Comercial.

De acordo com os [requisitos de certificação de ofertas de serviço gerenciado](/legal/marketplace/certification-policies#700-managed-services), você deve ter o [nível de competência de plataforma de nuvem Silver ou Gold](/partner-center/learn-about-competencies) ou ser [MSP Especialista em Azure](https://partner.microsoft.com/membership/azure-expert-msp) para publicar uma oferta de serviço gerenciado. Você também deve [inserir um destino de cliente potencial, que cria um registro no sistema CRM](../../marketplace/plan-managed-service-offer.md#customer-leads) sempre que um cliente implanta sua oferta.

Se não quiser publicar uma oferta no Azure Marketplace ou não preencher todos os requisitos, você poderá integrar os clientes manualmente usando modelos do Azure Resource Manager. Veja mais informações em [Integrar um cliente ao Azure Lighthouse](onboard-customer.md).

A tabela a seguir ajuda a determinar se os clientes devem ser integrados por meio da publicação da oferta de serviço gerenciado ou com modelos do Azure Resource Manager.

|**Consideração**  |**Oferta de serviço gerenciado**  |**Modelos de ARM**  |
|---------|---------|---------|
|Requer uma [conta do Partner Center](../../marketplace/partner-center-portal/create-account.md)   |Sim         |Não        |
|Requer o [nível de competência de plataforma de nuvem Silver ou Gold](/partner-center/learn-about-competencies) ou [MSP Especialista em Azure](https://partner.microsoft.com/membership/azure-expert-msp)      |Sim         |Não         |
|Disponível para novos clientes por meio do Azure Marketplace     |Sim     |Não       |
|Pode limitar a oferta a clientes específicos     |Sim, somente com ofertas privadas, que não podem ser usadas com assinaturas estabelecidas por meio de um revendedor do programa CSP (Provedor de Soluções na Nuvem)         |Yes         |
|Requer a aceitação do cliente no portal do Azure     |Sim     |Não   |
|Pode usar a automação para integrar várias assinaturas, grupos de recursos ou clientes |Não     |Sim    |
|Acesso imediato a novas funções internas e recursos do Azure Lighthouse     |Nem sempre (geralmente disponível após algum atraso)         |Yes         |
|Os clientes podem ver e aceitar ofertas atualizadas no portal do Azure | Sim | Não |

> [!NOTE]
> As ofertas de serviço gerenciado podem não estar disponíveis no Azure Government e em outras nuvens nacionais.

## <a name="create-your-offer"></a>Criar sua oferta

Veja instruções detalhadas para criação de ofertas, incluindo todas as informações e ativos que você precisa fornecer, em [Criar uma oferta de serviço gerenciado](../../marketplace/create-managed-service-offer.md).

Para saber mais sobre o processo de publicação geral, examine a [documentação do Marketplace Comercial](../../marketplace/overview.md). Você também deve examinar as [políticas de certificação do mercado comercial](/legal/marketplace/certification-policies), especialmente a seção [Serviços Gerenciados](/legal/marketplace/certification-policies#700-managed-services).

Ao adicionar a oferta, o cliente pode delegar uma ou mais assinaturas ou grupos de recursos, que serão [integrados ao Azure Lighthouse](#the-customer-onboarding-process).

> [!IMPORTANT]
> Cada plano em uma oferta de serviços gerenciados inclui a seção **Detalhes do Manifesto**, na qual você define as entidades do Azure AD (Azure Active Directory) do locatário que terão acesso aos grupos de recursos delegados e/ou às assinaturas para os clientes que comprarem o plano. É importante estar ciente de que qualquer grupo (ou entidade de serviço ou usuário) que você incluir terá as mesmas permissões para cada cliente que comprar o plano. Para atribuir grupos diferentes ao trabalho com cada cliente, você pode publicar um [plano privado](../../marketplace/private-offers.md) separado que seja exclusivo de cada cliente. Não é possível usar os planos privados com assinaturas estabelecidas por meio de revendedores do programa CSP (Provedor de Soluções na Nuvem).

## <a name="publish-your-offer"></a>Publicar sua oferta

Depois de concluir todas as seções, a próxima etapa é publicar a oferta no Azure Marketplace. Selecione o botão **Publicar** para iniciar o processo de ativação da oferta. Veja mais informações sobre esse processo [aqui](../../marketplace/review-publish-offer.md).

Você pode [publicar uma versão atualizada de sua oferta](../..//marketplace/partner-center-portal/update-existing-offer.md) a qualquer momento. Por exemplo, talvez você queira adicionar uma nova definição de função a uma oferta publicada anteriormente. Quando você fizer isso, os clientes que já tiverem adicionado a oferta verão um ícone na página [**Provedores de serviço**](view-manage-service-providers.md) no portal do Azure avisando que há uma atualização disponível. Cada cliente poderá [examinar as alterações](view-manage-service-providers.md#update-service-provider-offers) e decidir se querem atualizar para a nova versão. 

## <a name="the-customer-onboarding-process"></a>O processo de integração do cliente

Após adicionar a oferta, o cliente pode [delegar uma ou mais assinaturas ou grupos de recursos](view-manage-service-providers.md#delegate-resources), que serão integrados ao Azure Lighthouse. Se um cliente tiver aceitado uma oferta, mas ainda não tiver delegado nenhum recurso, ele verá uma observação na parte superior da seção **Ofertas de provedores** da página [**Provedores de serviço**](view-manage-service-providers.md) no portal do Azure.

> [!IMPORTANT]
> A delegação deve ser feita por uma conta do locatário do cliente que não seja convidada e que tenha a permissão `Microsoft.Authorization/roleAssignments/write` para a assinatura a ser integrada, como [Proprietário](../../role-based-access-control/built-in-roles.md#owner) (ou que contenha os grupos de recursos que serão integrados). Para ver todos os usuários que podem delegar a assinatura, um usuário do locatário do cliente poderá selecionar a assinatura no portal do Azure, abrir o **IAM (Controle de acesso)** e [exibir todos os usuários com a função Proprietário](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

Depois que o cliente delega uma assinatura (ou um ou mais grupos de recursos em uma assinatura), o provedor de recursos **Microsoft.ManagedServices** é registrado para essa assinatura, e os usuários do locatário podem acessar os recursos delegados de acordo com as autorizações da oferta.

Se você publicar uma versão atualizada da oferta, o cliente poderá [examinar as alterações no portal do Azure e aceitar a nova versão](view-manage-service-providers.md#update-service-provider-offers).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Marketplace Comercial](../../marketplace/overview.md).
- [Vincule sua ID de parceiro](partner-earned-credit.md) para acompanhar seu impacto nas participações do cliente.
- Saiba mais sobre as [experiências de gerenciamento entre locatários](../concepts/cross-tenant-management-experience.md).
- [Exiba e gerencie clientes](view-manage-customers.md) acessando **Meus clientes** no portal do Azure.
