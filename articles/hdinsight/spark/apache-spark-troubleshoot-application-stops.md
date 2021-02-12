---
title: Apache Spark aplicativo de streaming é interrompido após 24 dias no Azure HDInsight
description: Um aplicativo Apache Spark streaming é interrompido após a execução por 24 dias e não há erros nos arquivos de log.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: b702cbf915e4991df4c202564677ea7e0a02f9c4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929470"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>Cenário: o aplicativo de streaming de Apache Spark é interrompido após a execução por 24 dias no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções de problemas ao usar os componentes do Apache Spark nos clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Um aplicativo Apache Spark streaming é interrompido após a execução por 24 dias e não há erros nos arquivos de log.

## <a name="cause"></a>Causa

O `livy.server.session.timeout` valor controla por quanto tempo o Apache Livy deve aguardar a conclusão de uma sessão. Depois que o comprimento da sessão atingir o `session.timeout` valor, a sessão Livy e o aplicativo serão automaticamente eliminados.

## <a name="resolution"></a>Resolução

Para trabalhos de longa execução, aumente o valor de `livy.server.session.timeout` usando a interface do usuário do amAmbari. Você pode acessar a configuração Livy da interface do usuário do Ambari usando a URL `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs` .

Substitua `<yourclustername>` pelo nome do seu cluster HDInsight, conforme mostrado no Portal.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]