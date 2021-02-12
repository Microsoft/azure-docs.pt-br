---
title: Perguntas frequentes sobre migrações para Azure
description: Obtenha respostas para perguntas comuns sobre o serviço migrações para Azure.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 6396ca4483f5e9782890123a7c39a0cbcdbd30a3
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832565"
---
# <a name="azure-migrate-common-questions"></a>Migrações para Azure: perguntas comuns

Este artigo responde a perguntas comuns sobre as migrações para Azure. Se você tiver dúvidas depois de ler este artigo, poderá lançá-los no [Fórum migrações para Azure](https://aka.ms/AzureMigrateForum). Você também pode examinar estes artigos:

- Perguntas sobre o [dispositivo migrações para Azure](common-questions-appliance.md)
- Perguntas sobre [descoberta, avaliação e visualização de dependência](common-questions-discovery-assessment.md)

## <a name="what-is-azure-migrate"></a>O que são as Migrações para Azure?

As migrações para Azure fornecem um hub central para acompanhar a descoberta, avaliação e migração de seus aplicativos e cargas de trabalho locais e VMs de nuvem pública e privada para o Azure. O Hub fornece ferramentas de migração do Azure para avaliação e migração e ofertas de ISV de terceiros. [Saiba mais](migrate-services-overview.md).

## <a name="what-can-i-do-with-azure-migrate"></a>O que posso fazer com as migrações para Azure?

Use as migrações para Azure para descobrir, avaliar e migrar dados, aplicativos e infraestrutura locais para o Azure. As migrações para Azure dão suporte à avaliação e migração de VMs VMware locais, VMs do Hyper-V, servidores físicos, outras VMs virtualizadas, bancos de dados, aplicativos Web e áreas de trabalho virtuais. 

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Qual é a diferença entre migrações e Azure Site Recovery do Azure?

As [migrações para Azure](migrate-services-overview.md) fornecem um hub centralizado para avaliação e migração para o Azure. 

- Usar as migrações para Azure fornece interoperabilidade e extensibilidade futura com as ferramentas de migração do Azure, outros serviços do Azure e ferramentas de terceiros.
- O migrações para Azure: a ferramenta de migração de servidor é desenvolvida especificamente para a migração de servidor para o Azure. Ele é otimizado para migração. Você não precisa aprender sobre os conceitos e cenários que não são diretamente relevantes para a migração. 
- Não há cobranças de uso de ferramentas para migração por 180 dias, a partir do momento em que a replicação é iniciada para uma VM. Ele dá tempo para concluir a migração. Você paga apenas pelos recursos de armazenamento e de rede usados na replicação e pelos encargos de computação consumidos durante as migrações de teste.
- As migrações para Azure dão suporte a todos os cenários de migração com suporte pelo Site Recovery. Além disso, para VMs VMware, as migrações para Azure fornecem uma opção de migração sem agente.
- Estamos priorizando novos recursos de migração para as migrações para Azure: ferramenta de migração de servidor somente. Esses recursos não são destinados a Site Recovery.

[Azure site Recovery](../site-recovery/site-recovery-overview.md) deve ser usado somente para recuperação de desastre.

A ferramenta migrações para Azure: Server Migration usa algumas funcionalidades de Site Recovery de back-end para a migração de deslocamento e mudança de algumas máquinas locais.

## <a name="i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version"></a>Tenho um projeto com a experiência clássica anterior de migrações para Azure. Como fazer começar a usar a nova versão?

A migração do Azure clássica está se aposentando em fevereiro de 2024. Depois de fevereiro de 2024, a versão clássica de migrações para Azure não terá mais suporte e os metadados de inventário no projeto clássico serão excluídos. Não é possível fazer upgrade de projetos ou componentes na versão anterior para a nova versão. Você precisa [criar um projeto das Migrações para Azure](create-manage-projects.md) e [adicionar ferramentas de avaliação e migração](./create-manage-projects.md) a ele. Use os tutoriais para entender como usar as ferramentas de avaliação e migração disponíveis. Se você tinha um workspace do Log Analytics anexado a um projeto clássico, poderá anexá-lo a um projeto da versão atual depois de excluir o projeto clássico.

## <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Qual é a diferença entre as migrações para Azure: avaliação de servidor e o MAP Toolkit?

A avaliação do servidor fornece avaliação para ajudar com a preparação da migração e a avaliação de cargas de trabalho para migração para o Azure. O [Kit de ferramentas de avaliação e planejamento (MAP) da Microsoft](https://www.microsoft.com/download/details.aspx?id=7826) ajuda com outras tarefas, incluindo o planejamento de migração para versões mais recentes de sistemas operacionais de cliente e servidor do Windows e acompanhamento de uso de software. Para esses cenários, continue a usar o MAP Toolkit.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Qual é a diferença entre a avaliação do servidor e o Site Recovery Planejador de Implantações?

A avaliação do servidor é uma ferramenta de planejamento de migração. O Site Recovery Planejador de Implantações é uma ferramenta de planejamento de recuperação de desastre.

Escolha sua ferramenta com base no que você deseja fazer:

- **Planejar a migração local para o Azure**: se você planeja migrar seus servidores locais para o Azure, use a avaliação do servidor para planejamento de migração. A avaliação do servidor avalia as cargas de trabalho locais e fornece orientações e ferramentas para ajudá-lo a migrar. Depois que o plano de migração estiver em vigor, você poderá usar ferramentas como migrações para Azure: migração de servidor para migrar os computadores para o Azure.
- **Planejar a recuperação de desastre para o Azure**: se você planeja configurar a recuperação de desastre do local para o azure com site Recovery, use o Planejador de Implantações de site Recovery. O Planejador de Implantações fornece uma avaliação profunda e específica Site Recovery do seu ambiente local para fins de recuperação de desastres. Ele fornece recomendações relacionadas à recuperação de desastre, como replicação e failover.

## <a name="how-does-server-migration-work-with-site-recovery"></a>Como funciona a migração de servidor com o Site Recovery?

- Se você usar as migrações para Azure: migração de servidor para executar uma migração *sem agente* de VMs VMware locais, a migração será nativa para migrações para Azure e site Recovery não será usada.
- Se você usar migrações para Azure: migração de servidor para executar uma migração *baseada em agente* de VMs VMware ou se migrar VMs Hyper-V ou servidores físicos, migrações para Azure: a migração de servidor usa o mecanismo de replicação Azure site Recovery.

## <a name="which-geographies-are-supported"></a>Quais regiões têm suporte?

Examine as geografias compatíveis para [nuvens públicas](migrate-support-matrix.md#supported-geographies-public-cloud) e [governamentais](migrate-support-matrix.md#supported-geographies-azure-government).

## <a name="how-do-i-get-started"></a>Como começar?

Identifique a ferramenta de que você precisa e, em seguida, adicione a ferramenta a um projeto de migrações para Azure. 

Para adicionar uma ferramenta ISV ou um movimentador:

1. Comece obtendo uma licença ou Inscreva-se para uma avaliação gratuita, de acordo com a política de ferramentas. O licenciamento de ferramentas está de acordo com o modelo de licenciamento de ferramentas ou de ISV.
2. Em cada ferramenta, há uma opção para se conectar às Migrações para Azure. Siga as instruções e a documentação da ferramenta para conectar a ferramenta com as migrações para Azure.

Você pode acompanhar sua jornada de migração de dentro do projeto de migrações para Azure, entre o Azure e em outras ferramentas.

## <a name="how-do-i-delete-a-project"></a>Como fazer excluir um projeto?

Saiba como [excluir um projeto](how-to-delete-project.md). 

## <a name="next-steps"></a>Próximas etapas

Leia a [visão geral de migrações para Azure](migrate-services-overview.md).