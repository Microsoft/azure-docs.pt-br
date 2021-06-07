---
title: Visão geral do Gerenciamento do Azure - Governança do Azure
description: Visão geral das áreas de gerenciamento de aplicativos do Azure e recursos com links para conteúdo sobre ferramentas de gerenciamento do Azure.
ms.date: 02/05/2021
ms.topic: overview
ms.openlocfilehash: 754fe496b790a7ee32eb840109a60a04ee66ef97
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99593918"
---
# <a name="what-are-the-azure-management-areas"></a>Quais são as áreas de Gerenciamento do Azure?

A Governança no Azure é um aspecto do Gerenciamento do Azure. Este artigo aborda as diferentes áreas de gerenciamento para implantar e manter os recursos no Azure.

Gerenciamento refere-se às tarefas e processos necessários para manter seus aplicativos de negócios e os recursos que dão suporte a eles. O Azure tem muitos serviços e ferramentas que trabalham em conjunto para fornecer gerenciamento completo. Esses serviços não são apenas para recursos no Azure, mas também em outras nuvens e locais. Reconhecer as diferentes ferramentas e entender como elas funcionam juntas é a primeira etapa na criação de um ambiente de gerenciamento completo.

O diagrama a seguir ilustra as diferentes áreas de gerenciamento que são necessárias para manter qualquer aplicativo ou recurso. Essas diferentes áreas podem ser pensadas como um ciclo de vida. Cada área é necessária em sequência contínua durante o tempo de vida de um recurso. Esse ciclo de vida do recurso começa com a implantação inicial, passando pela operação contínua e, finalmente, chegando na desativação.

:::image type="complex" source="../monitoring/media/management-overview/management-capabilities.png" alt-text="Diagrama das disciplinas de gerenciamento no Azure." border="false":::
   Diagrama que mostra os elementos Migrar, Defender, Proteger, Monitorar, Configurar e Controlar da roda de serviços que dão suporte a Gerenciamento e Governança no Azure. Defender tem o Gerenciamento de segurança e a Proteção contra ameaças como subitens. Proteger tem Backup e Recuperação de desastre como subitens. Monitorar tem Aplicativo, Monitoramento de rede e infra e Log Analytics e Diagnóstico como subitens. Configurar tem Configuração, Gerenciamento de atualizações, Automação e Scripts como subitens. E Controlar tem Gerenciamento de políticas e Gerenciamento de custos como subitens.
:::image-end:::

Nenhum serviço único do Azure preenche completamente os requisitos de uma área de gerenciamento específica. Em vez disso, cada qual é realizado por vários serviços que trabalham em conjunto. Alguns serviços, como o Application Insights, oferecem funcionalidade de monitoramento segmentado para aplicativos Web. Outros, como os logs do Azure Monitor, armazenam dados de gerenciamento para outros serviços. Esse recurso permite que você analise diferentes tipos de dados coletados por diversificados serviços.

As seções a seguir descrevem resumidamente as diferentes áreas de gerenciamento e fornecem links para conteúdo detalhado sobre os principais serviços do Azure destinados a abordá-las.

## <a name="monitor"></a>Monitoramento

Monitoramento é o ato de coletar e analisar dados para auditar o desempenho, a integridade e a disponibilidade dos recursos. Uma estratégia de monitoramento eficaz ajuda a compreender a operação dos componentes e aumentar o tempo de atividade com notificações. Leia uma visão geral do monitoramento que identifica os diferentes serviços usados no [Monitoramento de aplicativos e recursos do Azure](../azure-monitor/overview.md).

## <a name="configure"></a>Configurar

Configurar refere-se à implantação inicial e à configuração de recursos e manutenção contínua.
A automação dessas tarefas permite eliminar a redundância, minimizando o tempo e esforço e aumentando a precisão e a eficiência. A [Automação do Azure](../automation/automation-intro.md) fornece a maior parte dos serviços para automatizar tarefas de configuração. Enquanto os runbooks lidam com a automação de processos, o gerenciamento de atualizações e configurações ajuda a gerenciar a configuração.

## <a name="govern"></a>Administrar

Governança fornece mecanismos e processos para manter controle sobre seus aplicativos e recursos no Azure. Ela envolve planejar suas iniciativas e estabelecer prioridades estratégicas.
Governança no Azure é implementada principalmente com dois serviços. O [Azure Policy](./policy/overview.md) permite criar, atribuir e gerenciar definições de política para impor regras aos recursos.
Esse recurso mantém os recursos em conformidade com os padrões corporativos.
O [Gerenciamento de custos do Azure](../cost-management-billing/cost-management-billing-overview.md) permite acompanhar o uso da nuvem e as despesas dos recursos do Azure e de outros provedores de nuvem.

## <a name="secure"></a>Seguro

Gerencie a segurança dos seus recursos e dados. Um programa de segurança envolve a avaliação de ameaças, coletando e analisando dados, e de conformidade dos aplicativos e recursos. A [Central de Segurança do Azure](../security-center/security-center-introduction.md) fornece monitoramento de segurança e análise de ameaças, o que inclui o gerenciamento unificado de segurança e proteção avançada contra ameaças em cargas de trabalho de nuvem híbrida. Consulte [Introdução à segurança do Azure](../security/fundamentals/overview.md) para obter informações abrangentes e orientações sobre como proteger os recursos do Azure.

## <a name="protect"></a>Proteger

Proteção refere-se a manter os aplicativos e dados disponíveis, mesmo com interrupções que estão além do seu controle. A proteção no Azure é fornecida por dois serviços. O [Backup do Azure](../backup/backup-overview.md) fornece backup e recuperação de seus dados, tanto na nuvem como localmente. O [Azure Site Recovery](../site-recovery/site-recovery-overview.md) fornece continuidade dos negócios e recuperação imediata durante um desastre.

## <a name="migrate"></a>Migrar

Migração refere-se à transição de cargas de trabalho atualmente em execução localmente para a nuvem do Azure.
[Migrações para Azure](../migrate/migrate-services-overview.md) é um serviço que ajuda a avaliar a adequação de migração de máquinas virtuais locais para o Azure. O Azure Site Recovery migra máquinas virtuais [do local](../site-recovery/migrate-tutorial-on-premises-azure.md) ou [do Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md). A [Migração de Banco de Dados Azure](../dms/dms-overview.md) ajuda você na migração de fontes de banco de dados para plataformas do Azure Data.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a Governança do Azure, leia estes artigos:

- Confira o [Hub de Governança do Azure](./index.yml).
- Confira [Governança no Cloud Adoption Framework para o Azure](/azure/cloud-adoption-framework/govern/)