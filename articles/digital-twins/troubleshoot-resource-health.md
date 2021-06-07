---
title: Entender a integridade do recurso
titleSuffix: Azure Digital Twins
description: Consulte como usar Azure Resource Health para verificar a integridade da instância do gêmeos digital do Azure.
author: baanders
ms.author: baanders
ms.date: 10/6/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: b64ee4d1538276bbbcab3fe09054f399ad20c162
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94616542"
---
# <a name="troubleshooting-azure-digital-twins-resource-health"></a>Solução de problemas do Azure digital gêmeos: Resource Health

[Azure Resource Health](../service-health/resource-health-overview.md) ajuda a diagnosticar e obter suporte para problemas de serviço que afetam os recursos do Azure. Ele relata a integridade atual e passada de seus recursos.

Este artigo mostra como obter informações sobre o **Resource Health** para suas instâncias de gêmeos digitais do Azure.

## <a name="use-azure-resource-health"></a>Usar o Azure Resource Health

Azure Resource Health pode ajudá-lo a monitorar se sua instância do gêmeos digital do Azure está em execução. Você também pode usá-lo para saber se uma interrupção regional está afetando a integridade da sua instância.

Para verificar a integridade da sua instância, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até sua instância do gêmeos digital do Azure. Você pode encontrá-lo digitando seu nome na barra de pesquisa do Portal. 

2. No menu da sua instância, selecione _**Resource Health**_ em *suporte + solução de problemas*. Isso levará você à página para exibir o histórico de integridade do recurso. 

    :::image type="content" source="media/troubleshoot-resource-health/resource-health.png" alt-text="Captura de tela mostrando a página ' integridade do recurso '. Há uma seção ' histórico de integridade ' mostrando um relatório diário dos últimos nove dias. Cada dia mostra o status ' disponível. '":::

Na imagem acima, essa instância é mostrada como *disponível* e tem sido para os últimos nove dias. Para saber mais sobre o status *disponível* e os outros tipos de status que podem aparecer, consulte [*visão geral do Azure Resource Health*](../service-health/resource-health-overview.md).

Você também pode saber mais sobre as diferentes verificações que entram no Resource Health para diferentes tipos de recursos do Azure em [*tipos de recursos e verificações de integridade no Azure Resource Health*](../service-health/resource-health-checks-resource-types.md).

## <a name="next-steps"></a>Próximas etapas

Leia sobre outras maneiras de monitorar sua instância do gêmeos digital do Azure nos seguintes artigos:
* [*Solução de problemas: Exibir métricas com Azure Monitor*](troubleshoot-metrics.md)
* [*Solução de problemas: configurar o diagnóstico*](troubleshoot-diagnostics.md).
* [*Solução de problemas: configurar alertas*](troubleshoot-alerts.md)
