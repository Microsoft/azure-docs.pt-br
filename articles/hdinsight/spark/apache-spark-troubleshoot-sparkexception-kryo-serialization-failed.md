---
title: Problemas com JDBC/ODBC e a estrutura do Apache Thrift - Azure HDInsight
description: Não é possível baixar conjuntos de dados grandes usando JDBC/ODBC e a estrutura de software do Apache Thrift no Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 05/14/2020
ms.openlocfilehash: 0b96bc9f6a78d07e091e791b769056859183da0d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945119"
---
# <a name="unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-hdinsight"></a>Não é possível baixar conjuntos de dados grandes usando JDBC/ODBC e a estrutura de software do Apache Thrift no HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções de problemas ao usar os componentes do Apache Spark nos clusters do Azure HDInsight.

## <a name="issue"></a>Problema

Ao tentar baixar grandes conjuntos de dados usando JDBC/ODBC e a estrutura de software do Apache Thrift no Azure HDInsight, você receberá uma mensagem de erro semelhante à seguinte:

```
org.apache.spark.SparkException: Kryo serialization failed:
Buffer overflow. Available: 0, required: 36518. To avoid this, increase spark.kryoserializer.buffer.max value.
```

## <a name="cause"></a>Causa

Essa exceção é causada pelo processo de serialização que está tentando usar mais espaço de buffer do que o permitido. No Spark 2.0.0, a classe `org.apache.spark.serializer.KryoSerializer` é usada para serializar objetos quando os dados são acessados por meio da estrutura de software do Apache Thrift. Uma classe diferente é usada para dados que serão enviados pela rede ou armazenados em cache na forma serializada.

## <a name="resolution"></a>Resolução

Aumente o valor do buffer de `Kryoserializer`. Adicione uma chave chamada `spark.kryoserializer.buffer.max` e defina-a como `2047` no spark2 config em `Custom spark2-thrift-sparkconf`. Reinicie todos os componentes afetados.

> [!IMPORTANT]
> O valor de `spark.kryoserializer.buffer.max` deve ser menor que 2048. Valores fracionados não são compatíveis.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [Suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **Suporte** na barra de menus ou abra o hub **Ajuda + suporte**. Para obter informações mais detalhadas, consulte [Como criar uma solicitação de Suporte do Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso ao Gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [Planos de suporte do Azure](https://azure.microsoft.com/support/plans/).