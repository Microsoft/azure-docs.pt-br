---
title: Entenda como funciona o processo de migração automática para seus alertas Azure Monitor clássicos
description: Saiba como funciona o processo de migração automática.
ms.topic: conceptual
ms.date: 08/19/2019
ms.subservice: alerts
ms.openlocfilehash: 9ce3550dcef174a4d970917fabfda3c4fd2a8df4
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526544"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>Entender o processo de migração automática para suas regras de alerta clássicas

Como [anunciado anteriormente](monitoring-classic-retirement.md), os alertas clássicos no Azure monitor são desativados para usuários de nuvem pública, embora ainda estejam em uso limitado para recursos que ainda não dão suporte aos novos alertas. Como parte do processo de aposentadoria, [uma ferramenta de migração](alerts-using-migration-tool.md) está disponível no portal do Azure para que os clientes disparem a migração em si.
Este artigo orienta você pelo processo de migração automática e ajuda você a resolver quaisquer problemas que você possa encontrar.

  > [!NOTE]
  > Este artigo se aplica somente à nuvem pública do Azure. O processo de aposentadoria para alertas clássicos Azure Monitor na nuvem do Azure governamental e no Azure China 21Vianet será anunciado em data futura.

## <a name="what-will-happen-during-the-automatic-migration-process"></a>O que acontecerá durante o processo de migração automática?

- A partir de **1º de setembro de 2019**, os clientes não poderão criar novas regras de alerta clássicas, exceto em [determinadas métricas](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts).
- Para as exceções, o cliente pode continuar a criar novas regras de alerta clássicas e usar seus alertas clássicos até o anúncio posterior.
- A partir de **1º de setembro de 2019**, a migração de alertas clássicos será disparada em lotes para qualquer cliente que tenha alertas clássicos.
- Assim como acontece com a ferramenta de migração voluntária, certas regras de alerta clássicas que não são migrada serão deixadas como estão. Essas regras de alerta clássicas continuarão a ter suporte até um anúncio adicional. No entanto, qualquer regra de alerta clássica inválida será excluída, pois elas não são funcionais.
Todas as regras de alerta clássicas que estão monitorando recursos de destino excluídos ou em [métricas que não são mais suportadas](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) são consideradas inválidas.
- Depois que a migração para sua assinatura for iniciada, a menos que haja problemas, a migração deve ser concluída em uma hora. Os clientes podem monitorar o status da migração na [folha de migração no Azure monitor](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel).
- Os proprietários da assinatura receberão um email sobre a conclusão bem-sucedida da migração.
- Se houver problemas durante a migração, os proprietários da assinatura também receberão um email informando que eles são os mesmos. Os clientes podem usar a folha de migração para ver os detalhes completos do problema.
- Caso uma ação seja necessária para os clientes, como desabilitar temporariamente um bloqueio de recurso ou alterar uma atribuição de política, os clientes precisarão resolver esses problemas. Se os problemas não forem resolvidos por enquanto, a migração bem-sucedida de seus alertas clássicos não poderá ser garantida.

    > [!NOTE]
    > Se você não quiser aguardar a inicialização do processo de migração automática, ainda poderá disparar a migração voluntariamente usando a ferramenta de migração.

## <a name="important-things-to-note"></a>Coisas importantes a observar

O processo de migração converte as regras de alerta clássicas em regras de alerta novas e equivalentes e cria grupos de ações. Em preparação, lembre-se dos seguintes pontos:

- Os formatos de carga de notificação para novas regras de alerta são diferentes daqueles das regras de alerta clássicas, pois oferecem suporte a mais recursos. Se você tiver aplicativos lógicos, runbooks ou WebHooks disparados pela regra de alerta clássica, eles poderão parar de funcionar conforme o esperado quando a migração for concluída devido a diferenças nas cargas de notificação. [Saiba como se preparar para a migração](alerts-prepare-migration.md).

- Algumas regras de alerta clássicas não podem ser migradas usando a ferramenta. [Saiba quais regras não podem ser migradas e o que fazer com elas](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts).

    > [!NOTE]
    > O processo de migração não afetará a avaliação de suas regras de alerta clássicas. Eles continuarão a executar e enviar alertas até que eles sejam migrados e as novas regras de alerta entrem em vigor.

## <a name="what-if-the-automatic-migration-fails"></a>E se a migração automática falhar?

Quando o processo de migração automática falhar, os proprietários da assinatura receberão um email notificando-os sobre o problema. Você pode usar a folha migração no Azure Monitor para ver os detalhes completos do problema.

Consulte o [Guia de solução de problemas](alerts-understand-migration.md#common-problems-and-remedies) para obter ajuda com problemas que você pode enfrentar durante a migração.

  > [!NOTE]
  > Caso uma ação seja necessária para os clientes, como desabilitar temporariamente um bloqueio de recurso ou alterar uma atribuição de política, os clientes precisarão resolver esses problemas. Se os problemas não forem resolvidos por enquanto, a migração bem-sucedida de seus alertas clássicos não poderá ser garantida.

## <a name="next-steps"></a>Próximas etapas

- [Preparar para a migração](alerts-prepare-migration.md)
- [Entender como a ferramenta de migração funciona](alerts-understand-migration.md)
