---
title: Entenda como funciona o processo de migração automática para seus alertas Azure Monitor clássicos
description: Saiba como funciona o processo de migração automática.
ms.topic: conceptual
ms.date: 02/14/2021
ms.openlocfilehash: 92aaffcea7a7c96cd77aade318520b093eed3e14
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102045455"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>Entender o processo de migração automática para suas regras de alerta clássicas

Como [anunciado anteriormente](monitoring-classic-retirement.md), os alertas clássicos no Azure monitor são desativados para usuários de nuvem pública, embora ainda estejam em uso limitado até **31 de maio de 2021**. Os alertas clássicos para a nuvem do Azure governamental e o Azure China 21Vianet serão desativados em **29 de fevereiro de 2024**.

[Uma ferramenta de migração](alerts-using-migration-tool.md) está disponível no portal do Azure para que os clientes disparem a migração por conta própria. Este artigo explica o processo de migração automática na nuvem pública, que será iniciado após 31 de maio de 2021. Ele também detalha problemas e soluções que você pode encontrar.

## <a name="important-things-to-note"></a>Coisas importantes a observar

O processo de migração converte as regras de alerta clássicas em regras de alerta novas e equivalentes e cria grupos de ações. Em preparação, lembre-se dos seguintes pontos:

- Os formatos de carga de notificação para novas regras de alerta são diferentes dos conteúdos das regras de alerta clássicas, pois oferecem suporte a mais recursos. Se você tiver uma regra de alerta clássica com aplicativos lógicos, runbooks ou WebHooks, eles poderão parar de funcionar conforme o esperado após a migração, devido às diferenças na carga. [Saiba como se preparar para a migração](alerts-prepare-migration.md).

- Algumas regras de alerta clássicas não podem ser migradas usando a ferramenta. [Saiba quais regras não podem ser migradas e o que fazer com elas](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts).

## <a name="what-will-happen-during-the-automatic-migration-process-in-public-cloud"></a>O que acontecerá durante o processo de migração automática na nuvem pública?

- A partir de 31 de maio de 2021, você não poderá criar novas regras de alerta clássicas e a migração de alertas clássicos será disparada em lotes.
- Todas as regras de alerta clássicas que estão monitorando recursos de destino excluídos ou em [métricas que não são mais suportadas](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) são consideradas inválidas.
- As regras de alerta clássico que forem inválidas serão removidas em um dia após 31 de maio de 2021.
- Depois que a migração para sua assinatura for iniciada, ela deverá ser concluída dentro de uma hora. Os clientes podem monitorar o status da migração na [ferramenta de migração no Azure monitor](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel).
- Os proprietários da assinatura receberão um email sobre o êxito ou a falha da migração.

    > [!NOTE]
    > Se você não quiser aguardar a inicialização do processo de migração automática, ainda poderá disparar a migração voluntariamente usando a ferramenta de migração.

## <a name="what-if-the-automatic-migration-fails"></a>E se a migração automática falhar?

Quando o processo de migração automática falhar, os proprietários da assinatura receberão um email notificando-os sobre o problema. Você pode usar a ferramenta de migração no Azure Monitor para ver os detalhes completos do problema. Consulte o [Guia de solução de problemas](alerts-understand-migration.md#common-problems-and-remedies) para obter ajuda com problemas que você pode enfrentar durante a migração.

  > [!NOTE]
  > Caso uma ação seja necessária para os clientes, como desabilitar temporariamente um bloqueio de recurso ou alterar uma atribuição de política, os clientes precisarão resolver esses problemas. Se os problemas não forem resolvidos por enquanto, a migração bem-sucedida de seus alertas clássicos não poderá ser garantida.

## <a name="next-steps"></a>Próximas etapas

- [Preparar para a migração](alerts-prepare-migration.md)
- [Entender como a ferramenta de migração funciona](alerts-understand-migration.md)