---
title: O que é o Azure Lighthouse?
description: O Azure Lighthouse permite que os provedores de serviços forneçam serviços gerenciados para seus clientes com maior automação e eficiência em escala.
ms.date: 12/16/2020
ms.topic: overview
ms.openlocfilehash: b9215f42e3ad3b7517d14fdaee710a31680ce453
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97617146"
---
# <a name="what-is-azure-lighthouse"></a>O que é o Azure Lighthouse?

O Azure Lighthouse permite o gerenciamento entre vários locatários, o que gera maior automação, escalabilidade e governança aprimorada entre recursos e locatários.

Com o Azure Lighthouse, os provedores de serviços podem entregar serviços gerenciados usando ferramentas de gerenciamento abrangentes e robustas integradas na plataforma Azure. Os clientes mantêm controle sobre quem pode acessar o locatário deles, quais recursos as pessoas podem acessar e quais ações podem ser executadas. Essa oferta também pode beneficiar as [organizações de TI empresariais](concepts/enterprise.md) que gerenciam recursos em vários locatários.

![Diagrama de visão geral do Azure Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Benefícios

O Azure Lighthouse ajuda os provedores de serviço a criar e entregar serviços gerenciados de maneira eficiente. Os benefícios incluem:

- **Gerenciamento em escala**: a participação do cliente e as operações de ciclo de vida para gerenciar os recursos do cliente são mais fáceis e escaláveis. APIs, ferramentas de gerenciamento e fluxos de trabalho existentes podem ser usados com recursos delegados, incluindo computadores hospedados fora do Azure, independentemente das regiões onde estão localizados.
- **Maior visibilidade e controle para os clientes**: os clientes têm controle preciso sobre os escopos que delegam para gerenciamento e as permissões que são permitidas. Eles podem auditar ações do provedor de serviços e remover o acesso completamente, se desejado.
- **Ferramentas de plataforma unificadas e abrangentes**: nossa experiência de ferramentas aborda os principais cenários de provedor de serviços, incluindo vários modelos de licenciamento, como EA, CSP e pago conforme o uso. O Azure Lighthouse funciona com ferramentas e APIs existentes, modelos de licenciamento, [Aplicativos Gerenciados do Azure](concepts/managed-applications.md) e programas de parceiros como o [programa CSP (Provedor de Soluções na Nuvem)](/partner-center/csp-overview). Você pode integrar o Azure Lighthouse aos seus fluxos de trabalho e aplicativos existentes e acompanhar seu impacto sobre as participações de clientes [vinculando sua ID de parceiro](./how-to/partner-earned-credit.md).

Não há custos adicionais associados ao uso do Azure Lighthouse para gerenciar os recursos do Azure. Qualquer cliente ou parceiro do Azure pode usar o Azure Lighthouse.

## <a name="capabilities"></a>Funcionalidades

O Azure Lighthouse inclui várias maneiras de ajudar a simplificar a participação e o gerenciamento:

- **Gerenciamento de recursos delegados do Azure**: [gerencie os recursos do Azure dos seus clientes com segurança de dentro do seu locatário](concepts/azure-delegated-resource-management.md) sem precisar alternar os planos de contexto e de controle. As assinaturas e os grupos de recursos de clientes podem ser delegados a usuários e funções especificados no locatário de gerenciamento, com a capacidade de remover o acesso conforme necessário.
- **Novas experiências do portal do Azure**: veja as informações entre locatários na [página **Meus clientes**](how-to/view-manage-customers.md) no portal do Azure. Uma [página **Provedores de serviços**](how-to/view-manage-service-providers.md) correspondente permite que os clientes vejam e gerenciem o acesso do provedor de serviços.
- **Modelos do Azure Resource Manager**: use modelos do ARM para [integrar recursos de cliente delegados](how-to/onboard-customer.md) e [executar tarefas de gerenciamento entre locatários](samples/index.md).
- **Ofertas de serviço gerenciado no Azure Marketplace**: [ofereça seus serviços aos clientes](concepts/managed-services-offers.md) por meio de ofertas privadas ou públicas e integre-os automaticamente ao Azure Lighthouse.

> [!TIP]
> Uma oferta semelhante, o [Microsoft 365 Lighthouse](https://techcommunity.microsoft.com/t5/small-and-medium-business-blog/announcing-microsoft-365-lighthouse-for-managed-service/ba-p/1698181), ajudará os Parceiros de TI a integrar, monitorar e gerenciar os clientes do Microsoft 365 em escala. Atualmente, o Microsoft 365 Lighthouse está em versão prévia privada.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Gerenciamento de recursos delegados do Azure](concepts/azure-delegated-resource-management.md).
- Explore [experiências de gerenciamento entre locatários](concepts/cross-tenant-management-experience.md).
- Veja como [usar o Azure Lighthouse em uma empresa](concepts/enterprise.md).
- Veja detalhes de [disponibilidade](https://azure.microsoft.com/global-infrastructure/services/?products=azure-lighthouse&regions=all) e [FedRAMP e escopo de auditoria SRG do DoD CC](../azure-government/compliance/azure-services-in-fedramp-auditscope.md) para o Azure Lighthouse.
