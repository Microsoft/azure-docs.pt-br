---
title: Use o Curl para exportar dados com o Apache Sqoop no HDInsight do Azure
description: Aprenda a enviar tarefas do Apache Sqoop remotamente para o HDInsight usando o Curl.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: ede0538e90e9f35797546f34bfed757c2727b194
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508883"
---
# <a name="run-apache-sqoop-jobs-in-hdinsight-with-curl"></a>Executar trabalhos do Apache Sqoop no HDInsight com Curl
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Aprenda a usar o Curl para executar tarefas do Apache Sqoop em um cluster do Apache Hadoop no HDInsight. Este artigo demonstra como exportar dados do armazenamento do Azure e importe-o para um banco de dados do SQL Server usando o Curl. Este artigo é uma continuação da [usar o Apache Sqoop com Hadoop no HDInsight](./hdinsight-use-sqoop.md).

Curl é usado para demonstrar como você pode interagir com o HDInsight usando solicitações HTTP brutas para executar, monitorar e recuperar os resultados de trabalhos do Sqoop. Isso funciona usando a API REST do WebHCat (anteriormente conhecido como Templeton) fornecida pelo seu cluster HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão da [configurar o ambiente de teste](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) partir [usar o Apache Sqoop com Hadoop no HDInsight](./hdinsight-use-sqoop.md).

* Um cliente para consultar o banco de dados SQL do Azure. Considere o uso de [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) ou [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md).

* [Curl](https://curl.haxx.se/). Curl é uma ferramenta para transferir dados de ou um cluster HDInsight ou para ele.

* [jq](https://stedolan.github.io/jq/). O utilitário jq é usado para processar os dados JSON retornados de solicitações REST.

## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>Enviar trabalhos do Apache Sqoop usando o cURL

Use o Curl para exportar dados usando trabalhos do Apache Sqoop do armazenamento do Azure para o SQL Server.

> [!NOTE]  
> Ao usar o Curl ou qualquer outra comunicação do REST com WebHCat, você deve autenticar as solicitações, fornecendo o nome de usuário e a senha para o administrador do cluster HDInsight. Você também deve usar o nome do cluster como parte do URI (Uniform Resource Identifier) usado para enviar as solicitações ao servidor.

Para os comandos nesta seção, substitua `USERNAME` com o usuário para autenticar o cluster e substitua `PASSWORD` com a senha da conta de usuário. Substitua `CLUSTERNAME` pelo nome do cluster.
 
A API REST é protegida por meio de [autenticação básica](https://en.wikipedia.org/wiki/Basic_access_authentication). Você deve sempre fazer solicitações usando HTTPS (HTTP seguro) para ajudar a garantir que suas credenciais sejam enviadas com segurança para o servidor.

1. De uma linha de comando, use o seguinte comando para verificar se você pode se conectar ao cluster HDInsight:

    ```cmd
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Você deve receber uma resposta com esta aparência:

    ```json
    {"status":"ok","version":"v1"}
    ```

2. Substitua `SQLDATABASESERVERNAME`, `USERNAME@SQLDATABASESERVERNAME`, `PASSWORD`, `SQLDATABASENAME` com os valores apropriados dos pré-requisitos. Use o seguinte para enviar um trabalho do sqoop:

    ```cmd
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop
    ```

    Os parâmetros usados nesse comando são os seguintes:

   * **-d** – uma vez que `-G` não é usado; a solicitação padrão é o método POST. `-d` especifica os valores de dados que são enviados com a solicitação.

       * **user.name** - o usuário que está executando o comando.

       * **command** - o comando Sqoop a ser executado.

       * **statusdir** - o diretório no qual o status deste trabalho será gravado.

     Esse comando deverá retornar uma ID de trabalho que pode ser usada para verificar o status do trabalho.

       ```json
       {"id":"job_1415651640909_0026"}
       ```

3. Para verificar o status do trabalho, use o comando a seguir. Substitua `JOBID` pelo valor retornado na etapa anterior. Por exemplo, se o valor de retorno foi `{"id":"job_1415651640909_0026"}`, em seguida, `JOBID` seria `job_1415651640909_0026`.

    ```cmd
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Se o trabalho foi concluído, o estado será **SUCCEEDED**.
   
   > [!NOTE]  
   > Essa solicitação de Curl retorna um documento JSON (JavaScript Object Notation) com informações sobre o trabalho; jq é usado para recuperar o valor de estado.

4. Depois que o estado do trabalho for alterado para **SUCCEEDED**, você poderá recuperar os resultados do trabalho no Armazenamento de Blobs do Azure. O parâmetro `statusdir` transmitido com a consulta contém a localização do arquivo de saída; nesse caso, `wasb:///example/data/sqoop/curl`. Esse endereço armazena a saída do trabalho no `example/data/sqoop/curl` diretório no contêiner de armazenamento padrão usado pelo seu cluster HDInsight.

    É possível usar o Portal do Azure para acessar os blobs stderr e stdout.

5. Para verificar se os dados foram exportados, use as seguintes consultas de seu cliente SQL para exibir os dados exportados:

    ```sql
    SELECT COUNT(*) FROM [dbo].[log4jlogs] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[log4jlogs] WITH (NOLOCK);
    ```

## <a name="limitations"></a>Limitações
* Exportação em massa — com HDInsight baseado em Linux, o conector Sqoop usado para exportar dados no Microsoft SQL Server ou no Banco de Dados SQL do Azure, atualmente, não permite inserções em massa.
* Envio em lote — com HDInsight baseado em Linux, ao usar o comutador `-batch` na execução de inserções, Sqoop executará várias inserções em vez de operações de inserção em lotes.

## <a name="summary"></a>Resumo
Conforme demonstrado neste documento, você pode usar uma solicitação HTTP bruta para executar, monitorar e exibir os resultados de trabalhos do Sqoop no cluster HDInsight.

Para obter mais informações sobre a interface REST usada neste artigo, confira o <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Guia da API REST do Apache Sqoop</a>.

## <a name="next-steps"></a>Próximas etapas
[Usar o Apache Sqoop com o Apache Hadoop no HDInsight](hdinsight-use-sqoop.md)

Para outros artigos do HDInsight que envolvem curl:
 
* [Criar clusters Apache Hadoop usando a API REST do Azure](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Executar consultas do Apache Hive com o Apache Hadoop no HDInsight usando a REST](apache-hadoop-use-hive-curl.md)
* [Executar trabalhos do MapReduce com o Apache Hadoop no HDInsight usando a REST](apache-hadoop-use-mapreduce-curl.md)

