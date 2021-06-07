---
title: Aprofundamento e práticas recomendadas do gateway para Apache Hive no Azure HDInsight
description: Saiba como navegar pelas práticas recomendadas para executar consultas de Hive no gateway do Azure HDInsight
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 549eab1547b75eb9461b23df2c157290943b4ed9
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869779"
---
# <a name="gateway-deep-dive-and-best-practices-for-apache-hive-in-azure-hdinsight"></a>Aprofundamento e práticas recomendadas do gateway para Apache Hive no Azure HDInsight

O gateway do Azure HDInsight (gateway) é o front-end HTTPS para clusters HDInsight. O gateway é responsável por: autenticação, resolução de host, descoberta de serviço e outros recursos úteis necessários para um sistema distribuído moderno. Os recursos fornecidos pelo gateway resultam em alguma sobrecarga para a qual este documento descreverá as práticas recomendadas para navegar. Técnicas de solução de problemas de gateway também são discutidas.

## <a name="the-hdinsight-gateway"></a>O gateway do HDInsight

O gateway do HDInsight é a única parte de um cluster HDInsight que pode ser acessado publicamente pela Internet. O serviço de gateway pode ser acessado sem passar pela Internet pública usando o `clustername-int.azurehdinsight.net` ponto de extremidade do gateway interno. O ponto de extremidade do gateway interno permite que as conexões sejam estabelecidas com o serviço de gateway sem sair da rede virtual do cluster. O gateway trata todas as solicitações enviadas ao cluster e encaminha essas solicitações para os componentes e hosts de cluster corretos.

O diagrama a seguir fornece uma ilustração aproximada de como o Gateway fornece uma abstração na frente de todas as diferentes possibilidades de resolução do host no HDInsight.

:::image type="content" source="./media/gateway-best-practices/host-resolution-diagram.png " alt-text="Diagrama de resolução do host" border="true":::

## <a name="motivation"></a>Motivação

A motivação para colocar um gateway na frente dos clusters HDInsight é fornecer uma interface para descoberta de serviço e autenticação de usuário. Os mecanismos de autenticação fornecidos pelo gateway são especialmente relevantes para clusters habilitados para ESP.

Para a descoberta de serviço, a vantagem do gateway é que cada componente dentro do cluster pode ser acessado como ponto de extremidade diferente no site do gateway ( `clustername.azurehdinsight.net/hive2` ), em oposição a uma infinidade de `host:port` emparelhamentos.

Para autenticação, o Gateway permite que os usuários se autentiquem usando um `username:password` par de credenciais. Para clusters habilitados para ESP, essa credencial seria o nome de usuário e a senha do domínio A autenticação para clusters HDInsight por meio do gateway não exige que o cliente adquira um tíquete Kerberos. Como o gateway aceita `username:password` credenciais e adquire o tíquete Kerberos do usuário em nome do usuário, as conexões seguras podem ser feitas no gateway a partir de qualquer host do cliente, incluindo clientes ingressados em diferentes domínios AA-DDS do que o (ESP).

## <a name="best-practices"></a>Práticas recomendadas

O gateway é um único serviço (com balanceamento de carga entre dois hosts) responsável pelo encaminhamento e autenticação de solicitações. O gateway pode se tornar um afunilamento de taxa de transferência para consultas de Hive que excedem um determinado tamanho. A degradação do desempenho da consulta pode ser observada quando consultas **Select** muito grandes são executadas no gateway via ODBC ou JDBC. "Muito grande" significa consultas que compõem mais de 5 GB de dados em linhas ou colunas. Essa consulta pode incluir uma longa lista de linhas e, ou uma contagem de colunas larga.

A degradação do desempenho do gateway em relação a consultas de um grande tamanho é porque os dados devem ser transferidos do armazenamento de dados subjacente (ADLS Gen2) para: o servidor hive do HDInsight, o gateway e, por fim, por meio dos drivers JDBC ou ODBC para o host do cliente.

O diagrama a seguir ilustra as etapas envolvidas em uma consulta SELECT.

:::image type="content" source="./media/gateway-best-practices/result-retrieval-diagram.png " alt-text="Diagrama de resultados" border="true":::

Apache Hive é uma abstração relacional sobre um sistema de arquivos compatível com HDFS. Essa abstração significa que as instruções **Select** no hive correspondem às operações de **leitura** no sistema de arquivos. As operações de **leitura** são convertidas no esquema apropriado antes de serem relatadas ao usuário. A latência desse processo aumenta com o tamanho dos dados e o total de saltos necessários para alcançar o usuário final.

Um comportamento semelhante pode ocorrer ao executar instruções **Create** ou **Insert** de dados grandes, pois esses comandos corresponderão às operações de **gravação** no sistema de arquivos subjacente. Considere gravar dados, como ORC brutos, no sistema de arquivos/datalake, em vez de carregá-los usando **Insert** ou **Load**.

Em clusters habilitados para o Enterprise Security Pack, as políticas do Apache Ranger suficientemente complexas podem causar uma lentidão no tempo de compilação da consulta, o que pode levar a um tempo limite de gateway. Se um tempo limite de gateway for notado em um cluster ESP, considere a possibilidade de reduzir ou combinar o número de políticas de Ranger.

## <a name="troubleshooting-techniques"></a>Técnicas de solução de problemas

Há vários locais para mitigar e entender os problemas de desempenho atendidos como parte do comportamento acima. Use a seguinte lista de verificação ao experimentar a degradação do desempenho de consulta no gateway do HDInsight:

* Use a cláusula **Limit** ao executar consultas de **seleção** grande. A cláusula **Limit** reduzirá o total de linhas relatadas para o host cliente. A cláusula **Limit** afeta apenas a geração de resultados e não altera o plano de consulta. Para aplicar a cláusula **Limit** ao plano de consulta, use a configuração `hive.limit.optimize.enable` . O **limite** pode ser combinado com um deslocamento usando o limite de formulário de argumento **x, y**.

* Nomeie suas colunas de interesse ao executar consultas **Select** em vez de **usar \* Select**. Selecionar menos colunas reduzirá a quantidade de dados lidos.

* Tente executar a consulta de interesse por meio do Apache beeline. Se a recuperação de resultado via Apache beeline levar um longo período de tempo, espere atrasos ao recuperar os mesmos resultados por meio de ferramentas externas.

* Teste uma consulta de Hive básica para garantir que uma conexão com o gateway do HDInsight possa ser estabelecida. Tente executar uma consulta básica de duas ou mais ferramentas externas para garantir que nenhuma ferramenta individual esteja sendo executada em problemas.

* Examine todos os alertas do Apache Ambari para garantir que os serviços do HDInsight estejam íntegros. Os alertas do Ambari podem ser integrados com Azure Monitor para relatório e análise.

* Se você estiver usando um metastore do hive externo, verifique se o DTU do BD SQL do Azure para o metastore do hive não atingiu seu limite. Se o DTU estiver se aproximando de seu limite, você precisará aumentar o tamanho do banco de dados.

* Certifique-se de que qualquer ferramenta de terceiros, como PBI ou tableau, esteja usando paginação para exibir tabelas ou bancos de dados. Consulte seus parceiros de suporte para essas ferramentas para obter assistência na paginação. A ferramenta principal usada para paginação é o `fetchSize` parâmetro JDBC. Um tamanho de busca pequeno pode resultar em degradação do desempenho do gateway, mas um tamanho de busca muito grande pode resultar em um tempo limite de gateway. O ajuste do tamanho de busca deve ser feito de acordo com a carga de trabalho.

* Se o pipeline de dados envolver a leitura de uma grande quantidade de dados do armazenamento subjacente do cluster HDInsight, considere usar uma ferramenta que faça interface diretamente com o armazenamento do Azure, como Azure Data Factory

* Considere o uso de Apache Hive LLAP ao executar cargas de trabalho interativas, uma vez que o LLAP pode fornecer uma experiência mais suave para retornar rapidamente os resultados da consulta

* Considere aumentar o número de threads disponíveis para o serviço de metastore do hive usando `hive.server2.thrift.max.worker.threads` . Essa configuração é especialmente relevante quando um grande número de usuários simultâneos está enviando consultas para o cluster

* Reduza o número de repetições usadas para acessar o gateway de qualquer ferramenta externa. Se várias repetições forem usadas, considere seguir uma política de repetição de retirada exponencial

* Considere habilitar o hive de compactação usando as configurações `hive.exec.compress.output` e `hive.exec.compress.intermediate` .

## <a name="next-steps"></a>Próximas etapas

* [Apache beeline no HDInsight](../hadoop/apache-hadoop-use-hive-beeline.md)
* [Etapas de solução de problemas de tempo limite do gateway HDInsight](./troubleshoot-gateway-timeout.md)
* [Redes virtuais para HDInsight](../hdinsight-plan-virtual-network-deployment.md)
* [HDInsight com rota expressa](../connect-on-premises-network.md)