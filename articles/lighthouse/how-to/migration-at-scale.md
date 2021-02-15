---
title: Gerenciar projetos de migrações para Azure em escala
description: Saiba como usar efetivamente as migrações para Azure em recursos de cliente delegados.
ms.date: 01/29/2021
ms.topic: how-to
ms.openlocfilehash: ed2fc8ae1a3b87a3b2501c3a390fe7a1822003e4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393574"
---
# <a name="manage-azure-migrate-projects-at-scale-with-azure-lighthouse"></a>Gerenciar projetos de migrações para Azure em escala com o Azure Lighthouse

Este tópico fornece uma visão geral de como o [Azure Lighthouse](../overview.md) pode ajudá-lo a usar as [migrações para Azure](../../migrate/migrate-services-overview.md) de maneira escalonável entre vários locatários do Azure Active Directory (Azure AD).

O Azure Lighthouse permite que os provedores de serviços realizem operações em escala em vários locatários ao mesmo tempo, tornando as tarefas de gerenciamento mais eficientes.

As Migrações para Azure oferecem um hub centralizado para avaliar e migrar para o Azure os servidores, a infraestrutura, os aplicativos e os dados locais. Normalmente, os parceiros que executam avaliações e migração em escala para vários clientes devem acessar cada assinatura de cliente individualmente usando o [modelo de assinatura CSP (provedor de soluções na nuvem)](/partner-center/customers-revoke-admin-privileges) ou [criando um usuário convidado no locatário do cliente](../../active-directory/external-identities/what-is-b2b.md).

A integração do Azure Lighthouse com o Azure Migrations permite que os provedores de serviços descubram, avaliem e migrem cargas de trabalho para diferentes clientes em escala, permitindo que os clientes tenham visibilidade e controle totais de seus ambientes. Por meio do gerenciamento de recursos delegado do Azure, os provedores de serviços têm uma exibição única de todos os projetos de migrações para Azure que eles gerenciam em vários locatários de clientes.

> [!NOTE]
> Por meio do Azure Lighthouse, os parceiros podem executar descoberta, avaliação e migração para VMs VMware locais, VMs do Hyper-V, servidores físicos e instâncias AWS/GCP. Há duas opções para [migração de VM do VMware](../../migrate/server-migrate-overview.md). Atualmente, somente o método de migração baseado em agente pode ser usado ao trabalhar em um projeto de migração em uma assinatura de cliente delegada; Atualmente, não há suporte para migração usando a replicação sem agente por meio de acesso delegado ao escopo do cliente.

> [!TIP]
> Embora possamos nos referimos a provedores de serviços e clientes neste tópico, essa orientação também se aplica a [empresas que usam o Azure Lighthouse para gerenciar vários locatários](../concepts/enterprise.md).

Dependendo do seu cenário, talvez você queira criar as migrações para Azure no locatário do cliente ou em seu locatário de gerenciamento. Examine as considerações abaixo e determine qual modelo melhor se adapta às necessidades de migração do seu cliente.

## <a name="create-an-azure-migrate-project-in-the-customer-tenant"></a>Criar um projeto de migrações para Azure no locatário do cliente

Uma opção ao usar o Azure Lighthouse é criar o projeto de migrações para Azure no locatário do cliente. Os usuários no locatário de gerenciamento podem selecionar a assinatura do cliente ao criar um projeto de migração. Do locatário de gerenciamento, o provedor de serviços pode executar as operações de migração necessárias. Isso pode incluir a implantação do dispositivo de migrações para Azure para descobrir as cargas de trabalho, avaliar as cargas de trabalho agrupando as VMs e calcular os custos relacionados à nuvem, revisar a preparação da VM e executar a migração.

Nesse cenário, nenhum recurso será criado e armazenado no locatário de gerenciamento, mesmo que as etapas de descoberta e avaliação possam ser iniciadas e executadas a partir desse locatário. Todos os recursos, como projetos de migração, relatórios de avaliação para cargas de trabalho locais e recursos migrados no destino de destino, serão implantados na assinatura do cliente. No entanto, o provedor de serviços pode acessar todos os projetos de clientes de sua própria experiência de locatário e Portal.

Essa abordagem minimiza a alternância de contexto para provedores de serviço que trabalham em vários clientes, ao mesmo tempo que permite que os clientes mantenham todos os seus recursos em seus próprios locatários.

O fluxo de trabalho para esse modelo será semelhante ao seguinte:

1. O cliente está [integrado ao Azure Lighthouse](onboard-customer.md). A função interna colaborador é necessária para a identidade que será usada com as migrações para Azure. Consulte o modelo de exemplo [delegado-Resource-Management-azmigrate](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate) para obter um exemplo usando essa função.
1. O usuário designado entra no locatário de gerenciamento na portal do Azure e, em seguida, vai para migrações para Azure. Este usuário [cria um projeto de migrações para Azure](../../migrate/create-manage-projects.md), selecionando a assinatura de cliente delegada apropriada.
1. Em seguida, o usuário [executa as etapas para descoberta e avaliação](../../migrate/tutorial-discover-vmware.md).

   Para VMs VMware, antes de configurar o dispositivo, você pode limitar a descoberta para vCenter Server data centers, clusters, uma pasta de clusters, hosts, uma pasta de hosts ou VMs individuais. Para definir o escopo, atribua permissões na conta que o dispositivo usa para acessar o vCenter Server. Isso será útil se as VMs de vários clientes estiverem hospedadas no hipervisor. Não é possível limitar o escopo de descoberta do Hyper-V.

    > [!NOTE]
    > Você pode descobrir e avaliar as máquinas virtuais VMware para migração usando as migrações para Azure por meio do acesso delegado fornecido pelo Azure Lighthouse. Para a migração de máquinas virtuais VMware, somente o método baseado em agente tem suporte no momento ao trabalhar em um projeto de migração em uma assinatura de cliente delegada.

1. Quando a assinatura do cliente de destino estiver pronta, continue com a migração por meio do acesso concedido pelo Azure Lighthouse. O projeto de migração que contém os resultados da avaliação e os recursos migrados será criado no locatário do cliente sob a assinatura de destino.

> [!TIP]
> Antes da migração, uma zona de aterrissagem precisará ser implantada para provisionar os recursos de infraestrutura de fundação e preparar a assinatura para a qual as máquinas virtuais serão migradas. Para acessar ou criar alguns recursos nessa zona de aterrissagem, a função interna do proprietário pode ser necessária, o que não tem suporte no momento no Azure Lighthouse. Para esses cenários, o cliente pode precisar fornecer a função de acesso de convidado ou delegar acesso de administrador por meio do modelo de assinatura do CSP. Para obter uma abordagem para criar zonas de aterrissagem multilocatário, consulte a [solução de demonstração de zonas de aterrissagem de vários locatários](https://github.com/Azure/Multi-tenant-Landing-Zones) no github.

## <a name="create-an-azure-migrate-project-in-the-managing-tenant"></a>Criar um projeto de migrações para Azure no locatário de gerenciamento

Nesse cenário, as operações relacionadas à migração, como descoberta e avaliação, ainda são realizadas por usuários no locatário de gerenciamento. No entanto, o projeto de migração e todos os recursos relevantes residirão no locatário do parceiro, e o cliente não terá visibilidade direta do projeto (embora as avaliações possam ser compartilhadas com os clientes, se desejado). O destino de migração para cada cliente será a assinatura do cliente.

Essa abordagem permite que os provedores de serviços iniciem projetos de avaliação e descoberta de migração rapidamente, abstraindo as etapas iniciais das assinaturas e dos locatários do cliente.

O fluxo de trabalho para esse modelo será semelhante ao seguinte:

1. O cliente está [integrado ao Azure Lighthouse](onboard-customer.md). A função interna colaborador é necessária para a identidade que será usada com as migrações para Azure. Consulte o modelo de exemplo [delegado-Resource-Management-azmigrate](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate) para obter um exemplo usando essa função.
1. O usuário designado entra no locatário de gerenciamento na portal do Azure e, em seguida, vai para migrações para Azure. Este usuário [cria um projeto de migrações para Azure](../../migrate/create-manage-projects.md) em uma assinatura que pertence ao locatário de gerenciamento.
1. Em seguida, o usuário [executa as etapas para descoberta e avaliação](../../migrate/tutorial-discover-vmware.md). As VMs locais serão descobertas e avaliadas no projeto de migração criado no locatário de gerenciamento e, em seguida, migradas a partir daí.

   Se você estiver gerenciando vários clientes no mesmo host do Hyper-V, poderá descobrir todas as cargas de trabalho ao mesmo tempo. As VMs específicas do cliente podem ser selecionadas no mesmo grupo, então uma avaliação pode ser criada e a migração pode ser executada selecionando a assinatura do cliente apropriada como destino de destino. Não é necessário limitar o escopo de descoberta e você pode manter uma visão geral completa de todas as cargas de trabalho do cliente em um projeto de migração.

1. Quando estiver pronto, continue com a migração selecionando a assinatura de cliente delegada como destino de destino para replicar e migrar as cargas de trabalho. Os recursos recém-criados existirão na assinatura do cliente, enquanto os dados de avaliação e os recursos pertencentes ao projeto de migração permanecerão no locatário de gerenciamento.

Observação: você deve modificar o arquivo de parâmetro para refletir seu ambiente antes de implantar https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate

## <a name="partner-recognition-for-customer-migrations"></a>Reconhecimento de parceiros para migrações de clientes

Como membro do [Microsoft Partner Network](https://partner.microsoft.com), você pode vincular sua ID de parceiro com as credenciais usadas para gerenciar recursos de cliente delegados. Isso permite que a Microsoft inclua a influência de atributos e a receita consumida pelo Azure para sua organização com base nas tarefas que você executa para os clientes, incluindo projetos de migração.

Para obter mais informações, confira [Vincular a ID de parceiro para acompanhar seu impacto em recursos delegados](partner-earned-credit.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as [migrações para Azure](../../migrate/migrate-services-overview.md).
- Saiba mais sobre outras [experiências de gerenciamento entre locatários](../concepts/cross-tenant-management-experience.md) com suporte do Azure Lighthouse.
