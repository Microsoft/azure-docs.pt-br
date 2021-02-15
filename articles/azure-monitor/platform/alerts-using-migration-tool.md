---
title: Migrar Azure Monitor regras de alerta
description: Saiba como usar a ferramenta de migração voluntária para migrar suas regras de alerta clássicas.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: 4f17afc6fb5001b4bbee16fe84697b58d062b292
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526917"
---
# <a name="use-the-voluntary-migration-tool-to-migrate-your-classic-alert-rules"></a>Use a ferramenta de migração voluntária para migrar suas regras de alerta clássicas

Como [anunciado anteriormente](monitoring-classic-retirement.md), os alertas clássicos no Azure monitor são desativados para usuários de nuvem pública, embora ainda estejam em uso limitado para recursos que ainda não dão suporte aos novos alertas. Uma ferramenta de migração estava disponível no portal do Azure aos clientes que usaram regras de alerta clássicas e que desejam disparar a migração por conta própria. Este artigo explica como usar a ferramenta de migração, que também será usada para que os alertas restantes tenham um anúncio adicional pendente.

## <a name="benefits-of-new-alerts"></a>Benefícios de novos alertas

Os alertas clássicos estão sendo substituídos por alertas novos e unificados no Azure Monitor. A nova plataforma de alertas tem os seguintes benefícios:

- Você pode alertar sobre uma variedade de métricas multidimensionais para [muitos outros serviços do Azure](alerts-metric-near-real-time.md#metrics-and-dimensions-supported).
- Os novos alertas de métricas dão suporte a [regras de alerta de vários recursos](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) que reduzem muito a sobrecarga do gerenciamento de muitas regras.
- O mecanismo de notificação unificada, que dá suporte a:
  - [Grupos de ações](action-groups.md), um mecanismo de notificação modular que funciona com todos os novos tipos de alertas (métrica, log e log de atividades).
  - Novos mecanismos de notificação, como SMS, voz e Conector ITSM.
- A [experiência de alerta unificada](alerts-overview.md) traz todos os alertas sobre diferentes sinais (métrica, log e log de atividades) em um único local.

## <a name="before-you-migrate"></a>Antes de migrar

O processo de migração converte as regras de alerta clássicas em regras de alerta novas e equivalentes e cria grupos de ações. Em preparação, lembre-se dos seguintes pontos:

- O formato da carga de notificação e as APIs para criar e gerenciar novas regras de alerta são diferentes das regras de alerta clássicas, pois oferecem suporte a mais recursos. [Saiba como se preparar para a migração](alerts-prepare-migration.md).

- Algumas regras de alerta clássicas não podem ser migradas usando a ferramenta. [Saiba quais regras não podem ser migradas e o que fazer com elas](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts).

    > [!NOTE]
    > O processo de migração não afetará a avaliação de suas regras de alerta clássicas. Eles continuarão a executar e enviar alertas até que eles sejam migrados e as novas regras de alerta entrem em vigor.

## <a name="how-to-use-the-migration-tool"></a>Como usar a ferramenta de migração

Para disparar a migração de suas regras de alerta clássicas no portal do Azure, siga estas etapas:

1. Em [portal do Azure](https://portal.azure.com), selecione **Monitor**.

1. Selecione **alertas** e, em seguida, selecione **gerenciar regras de alerta** ou **exibir alertas clássicos**.

1. Selecione **migrar para novas regras** para ir para a página de aterrissagem de migração. Esta página mostra uma lista de todas as suas assinaturas e seu status de migração:

    ![Captura de tela mostra a página migrar regras de alerta.](media/alerts-migration/migration-landing.png "Migrar regras")

    Todas as assinaturas que podem ser migradas usando a ferramenta são marcadas como **prontas para migrar**.

    > [!NOTE]
    > A ferramenta de migração é distribuída em fases para todas as assinaturas que usam regras de alerta clássicas. Nas primeiras fases da distribuição, você poderá ver algumas assinaturas marcadas como não prontas para a migração.

1. Selecione uma ou mais assinaturas e, em seguida, selecione a **migração de visualização**.

    A página resultante mostra os detalhes das regras de alerta clássicas que serão migradas para uma assinatura de cada vez. Você também pode selecionar **baixar os detalhes de migração para esta assinatura** para obter os detalhes em um formato CSV.

    ![Captura de tela mostra a página migrar regras de alerta com um link para baixar os detalhes de migração para essa assinatura e você pode especificar o email para a notificação de migração.](media/alerts-migration/migration-preview.png "Migração de visualização")

1. Especifique um ou mais endereços de email a serem notificados sobre o status da migração. Você receberá um email quando a migração for concluída ou se alguma ação for necessária.

1. Selecione **Iniciar migração**. Leia as informações mostradas na caixa de diálogo de confirmação e confirme se você está pronto para iniciar o processo de migração.

    > [!IMPORTANT]
    > Depois de iniciar a migração para uma assinatura, você não poderá editar ou criar regras de alerta clássicas para essa assinatura. Essa restrição garante que nenhuma alteração nas regras de alerta clássicas seja perdida durante a migração para as novas regras. Embora você não consiga alterar suas regras de alerta clássicas, elas ainda continuarão sendo executadas e fornecerão alertas até que tenham sido migrados. Depois que a migração for concluída para sua assinatura, você não poderá mais usar as regras de alerta clássicas.

    ![Captura de tela mostra um prompt de confirmação para sua migração, incluindo informações importantes com links para saber mais antes de continuar.](media/alerts-migration/migration-confirm.png "Confirmar início da migração")

1. Quando a migração for concluída ou se a ação for necessária, você receberá um email nos endereços que você forneceu anteriormente. Você também pode verificar periodicamente o status na página de aterrissagem de migração no Portal.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="why-is-my-subscription-listed-as-not-ready-for-migration"></a>Por que minha assinatura está listada como não pronta para migração?

A ferramenta de migração está distribuindo para os clientes em fases. Nas primeiras fases, a maioria ou todas as suas assinaturas podem ser marcadas como **não prontas para a migração**. 

Quando uma assinatura se tornar pronta para migração, o proprietário da assinatura receberá uma mensagem de email informando que a ferramenta está disponível. Fique atento à mensagem.

### <a name="who-can-trigger-the-migration"></a>Quem pode disparar a migração?

Os usuários que têm a função de colaborador de monitoramento atribuída a eles no nível de assinatura são capazes de disparar a migração. [Saiba mais sobre o controle de acesso baseado em função do Azure para o processo de migração](alerts-understand-migration.md#who-can-trigger-the-migration).

### <a name="how-long-will-the-migration-take"></a>Quanto tempo a migração levará?

A migração foi concluída para a maioria das assinaturas em menos de uma hora. Você pode acompanhar o progresso da migração na página de aterrissagem de migração. Durante a migração, tenha certeza de que os alertas ainda estão sendo executados no sistema de alertas clássicos ou no novo.

### <a name="what-can-i-do-if-i-run-into-a-problem-during-migration"></a>O que posso fazer se encontrar um problema durante a migração?

Consulte o [Guia de solução de problemas](alerts-understand-migration.md#common-problems-and-remedies) para obter ajuda com problemas que você pode enfrentar durante a migração. Se alguma ação for necessária para concluir a migração, você será notificado sobre os endereços de email que forneceu ao configurar a ferramenta.

## <a name="next-steps"></a>Próximas etapas

- [Preparar para a migração](alerts-prepare-migration.md)
- [Entender como a ferramenta de migração funciona](alerts-understand-migration.md)
