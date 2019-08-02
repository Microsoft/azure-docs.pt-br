---
title: Migrar seus alertas clássicos no Azure Monitor, usando a ferramenta de migração voluntária
description: Saiba como usar a ferramenta de migração voluntária para migrar suas regras de alerta clássicas.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 0c8aa00d069ae54584d8e828dab35c22048f1876
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295540"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Use a ferramenta de migração voluntária para migrar suas regras de alerta clássicas

Como [anunciado anteriormente](monitoring-classic-retirement.md), alertas clássicos no Azure Monitor estão sendo desativadas em setembro de 2019 (foi originalmente de 2019 de julho). Uma ferramenta de migração está disponível no portal do Azure para clientes que usam regras de alerta clássicas e que queiram disparar a migração em si. Este artigo explica como usar a ferramenta de migração para migrar voluntariamente suas regras de alerta clássicas antes do início de migração automática em setembro de 2019.

> [!NOTE]
> Devido a atraso na disponibilização da ferramenta de migração, a data de desativação para a migração de alertas clássicos tiver sido [estendido até 31 de agosto de 2019](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) da data do dia 30 de junho de 2019 originalmente anunciada.

## <a name="benefits-of-new-alerts"></a>Benefícios dos novos alertas

Alertas clássicos estão sendo substituídas pela nova e unificada de alertas no Azure Monitor. A nova plataforma alertas tem os seguintes benefícios:

- Você pode alertar sobre uma variedade de métricas multidimensionais para [muitos serviços do Azure mais](alerts-metric-near-real-time.md#metrics-and-dimensions-supported).
- Suporte de alertas de métrica novos [regras de alerta com vários recursos](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) que reduzir significativamente a sobrecarga de gerenciar várias regras.
- O mecanismo de notificação unificada, que dá suporte a:
  - [Grupos de ação](action-groups.md), um mecanismo de notificação modular que funciona com todos os novos tipos de alertas (métrica, log e log de atividades).
  - Novos mecanismos de notificação, como SMS, voz e o conector de ITSM.
- O [alerta experiência unificada](alerts-overview.md) traz todos os alertas em sinais diferentes (métrica, log e log de atividades) em um único lugar.

## <a name="before-you-migrate"></a>Antes de migrar

O processo de migração converte as regras de alerta clássicas equivalentes, novas regras de alerta e cria grupos de ação. Durante a preparação, lembre-se dos seguintes pontos:

- O formato de carga de notificação e as APIs para criar e gerenciar novas regras de alerta são diferentes das regras de alerta clássicas porque eles oferecem suporte a mais recursos. [Saiba como preparar a migração](alerts-prepare-migration.md).

- Algumas regras de alerta clássicas não podem ser migradas usando a ferramenta. [Saiba quais regras não podem ser migradas e o que fazer com eles](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated).

    > [!NOTE]
    > O processo de migração não afetarão a avaliação de suas regras de alerta clássicas. Eles também continuarão a executar e enviar alertas até que eles são migrados e as novas regras de alerta em vigor.

## <a name="how-to-use-the-migration-tool"></a>Como usar a ferramenta de migração

Para disparar a migração de suas regras de alerta clássicas no portal do Azure, siga estas etapas:

1. Na [portal do Azure](https://portal.azure.com), selecione **Monitor**.

1. Selecione **alertas**e, em seguida, selecione **gerenciar regras de alerta** ou **exibir alertas clássicos**.

1. Selecione **migrar para novas regras** para ir para a página de aterrissagem de migração. Esta página mostra uma lista de todas as suas assinaturas e seus status de migração:

    ![migração de aterrissagem](media/alerts-migration/migration-landing.png "migre regras")

    Todas as assinaturas que podem ser migradas por meio da ferramenta são marcadas como **pronto para migrar**.

    > [!NOTE]
    > A ferramenta de migração está distribuindo em fases para todas as assinaturas que usam regras de alerta clássicas. Nas fases iniciais da distribuição, você poderá ver algumas assinaturas marcadas como não prontos para a migração.

1. Selecione uma ou mais assinaturas e, em seguida, selecione **visualizar migração**.

    A página resultante mostra os detalhes de regras de alerta clássicos que serão migrados para uma assinatura por vez. Você também pode selecionar **baixar os detalhes de migração para esta assinatura** para obter os detalhes em um formato CSV.

    ![visualização da migração](media/alerts-migration/migration-preview.png "migração de visualização")

1. Especifique um ou mais endereços de email para ser notificado sobre o status de migração. Você receberá o email quando a migração for concluída ou se nenhuma ação é necessária.

1. Selecione **Iniciar migração**. Leia as informações mostradas na caixa de diálogo de confirmação e confirme que você está pronto para iniciar o processo de migração.

    > [!IMPORTANT]
    > Depois de você inicia a migração para uma assinatura, você não será capaz de editar ou criar regras de alerta clássicas para essa assinatura. Essa restrição garante que nenhuma alteração para suas regras de alerta clássicas é perdidas durante a migração para as novas regras. Embora você não poderá alterar suas regras de alerta clássicas, eles continuarão ainda para executar e para fornecer alertas até que eles já foram migrados. Após a migração ser concluída para a sua assinatura, você não pode mais usar regras de alerta clássicas.

    ![Confirmar a migração](media/alerts-migration/migration-confirm.png "confirmar Iniciar migração")

1. Quando a migração for concluída, ou se a ação não é necessária, você receberá um email os endereços que você forneceu anteriormente. Periodicamente, você pode verificar o status na página de aterrissagem a migração no portal.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>Por que minha assinatura está listada como não pronto para a migração?

A ferramenta de migração está distribuindo para os clientes em fases. Nas fases iniciais, a maioria ou todas as suas assinaturas podem ser marcadas como **não está pronto para a migração**. 

Quando uma assinatura fica pronta para a migração, o proprietário da assinatura receberá uma mensagem de email informando que a ferramenta está disponível. Fique atento para esta mensagem.

### <a name="who-can-trigger-the-migration"></a>Quem pode disparar a migração?

Os usuários que têm a função de Colaborador de monitoramento atribuída a eles no nível da assinatura são capazes de disparar a migração. [Saiba mais sobre o controle de acesso baseado em função para o processo de migração](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-will-the-migration-take"></a>Quanto tempo levará a migração?

A migração é concluída para a maioria das assinaturas em menos de uma hora. Você pode manter controle do andamento da migração na página de aterrissagem de migração. Durante a migração, ter certeza de que os alertas são ainda em execução no sistema de alertas clássicos ou em um novo.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>O que fazer se eu encontrar um problema durante a migração?

Consulte a [guia de solução](alerts-understand-migration.md#common-problems-and-remedies) para obter ajuda com problemas que você pode enfrentar durante a migração. Se nenhuma ação é necessária para concluir a migração, você será notificado em endereços de email que você forneceu ao configurar a ferramenta.

## <a name="next-steps"></a>Próximas etapas

- [Preparar para a migração](alerts-prepare-migration.md)
- [Entender como funciona a ferramenta de migração](alerts-understand-migration.md)
