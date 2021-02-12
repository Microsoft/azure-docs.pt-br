---
title: CPU vinculada no cluster apache HBase – Azure HDInsight
description: Solucionar problemas de CPU vinculada no servidor de região no cluster do Apache HBase no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/01/2019
ms.openlocfilehash: 7d0cf139f06bb296b486d2932d95b53fc1167a5d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937015"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>Cenário: CPU vinculada no servidor de região no cluster apache HBase no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

## <a name="issue"></a>Problema

O processo do servidor de região do Apache HBase começa aproximadamente 200% da CPU, fazendo com que os alertas sejam acionados em HBase Master processo e o cluster não funcionem com capacidade total.

## <a name="cause"></a>Causa

Se você estiver executando o cluster HBase v 3.4, talvez tenha sido atingido por um possível bug causado pela atualização do JDK para a versão 1.7.0 _151. O sintoma que vemos é que o processo do servidor de região começa aproximadamente 200% da CPU (para verificar isso `top` , execute o comando; se houver um processo ocupando perto de 200% da CPU, obtenha seu PID e confirme se ele é um processo do servidor de região executando `ps -aux | grep` ).

## <a name="resolution"></a>Resolução

1. Instale o JDK 1,8 em todos os nós do cluster, como mostrado abaixo:

    * Execute a ação de script `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh` . Certifique-se de selecionar a opção para executar em todos os nós.

    * Como alternativa, você pode entrar em cada nó individual e executar o comando `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk` .

1. Acesse Ambari UI- `https://<clusterdnsname>.azurehdinsight.net` .

1. Navegue até o **HBase->configurações->avançado->avançado** `hbase-env configs` e altere a variável `JAVA_HOME` para `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64` . Salve a alteração de configuração.

1. [Opcional, mas recomendado] [Libera Todas as tabelas no cluster](/archive/blogs/azuredatalake/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables).

1. Na interface do usuário do Ambari novamente, reinicie todos os serviços HBase que precisam ser reiniciados.

1. Dependendo dos dados no cluster, pode levar alguns minutos até uma hora para o cluster alcançar o estado estável. A maneira como você confirma que o cluster atinge o estado estável é verificando a interface do usuário do HMaster (todos os servidores de região devem estar ativos) do Ambari (atualizar) ou do cabeçalho executar o Shell do HBase e executar o comando status.

Para verificar se a atualização foi bem-sucedida, verifique se os processos relevantes do HBase foram iniciados usando a versão do Java apropriada-por exemplo, para servidor de região, verifique como:

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]