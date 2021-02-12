---
title: A interface do usuário do Apache Ambari mostra hosts e serviços no Azure HDInsight
description: Solução de problemas de um problema da interface do usuário do Apache Ambari quando ele mostra hosts e serviços no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/02/2019
ms.openlocfilehash: 0d3a393dc1cbd51b83edc0b6989bb5e7a5670a29
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943269"
---
# <a name="scenario-apache-ambari-ui-shows-down-hosts-and-services-in-azure-hdinsight"></a>Cenário: a interface do usuário do Apache Ambari mostra hosts e serviços no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

A interface do usuário do Apache Ambari é acessível, mas a interface do usuário mostra quase todos os serviços estão inativos, todos os hosts mostrando a pulsação perdida

## <a name="cause"></a>Causa

Na maioria dos cenários, isso é um problema com o Ambari Server não estar em execução no Active cabeçalho. Verifique qual cabeçalho é o cabeçalho ativo e certifique-se de que o ambari-Server seja executado à direita. Não inicie manualmente o ambari-Server, deixe que o serviço do controlador de failover seja responsável por iniciar o ambari-Server no cabeçalho correto. Reinicialize o cabeçalho ativo para forçar um failover.

Problemas de rede também podem causar esse problema. Em cada nó de cluster, consulte se você pode executar ping `headnodehost` . Há uma situação rara em que nenhum nó de cluster pode se conectar a `headnodehost` :

```
$>telnet headnodehost 8440
... No route to host
```

## <a name="resolution"></a>Resolução

Geralmente, reinicializar o cabeçalho ativo atenuará esse problema. Caso contrário, entre em contato com a equipe de suporte do HDInsight.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]