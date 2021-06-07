---
title: Use a rotação para exportar dados com o Apache Sqoop no Azure HDInsight
description: Saiba como enviar remotamente trabalhos do Apache Sqoop para o Azure HDInsight usando a rotação.
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/06/2020
ms.openlocfilehash: 4de42bf30824fd71228aa27cc478a54ec3741da9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98928354"
---
# <a name="run-apache-sqoop-jobs-in-hdinsight-with-curl"></a>Executar trabalhos do Apache Sqoop no HDInsight com ondulação

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Aprenda a usar o Curl para executar tarefas do Apache Sqoop em um cluster do Apache Hadoop no HDInsight. Este artigo demonstra como exportar dados do armazenamento do Azure e importá-los para um banco de dado SQL Server usando a ondulação. Este artigo é uma continuação do [Usar o Apache Sqoop com o Hadoop no HDInsight](./hdinsight-use-sqoop.md).

Curl é usado para demonstrar como você pode interagir com o HDInsight usando solicitações HTTP brutas para executar, monitorar e recuperar os resultados de trabalhos do Sqoop. Isso funciona usando a API REST do WebHCat (anteriormente conhecido como Templeton) fornecida pelo seu cluster HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão de [Configurar o ambiente de teste](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) de [Usar o Apache Sqoop com o Hadoop no HDInsight](./hdinsight-use-sqoop.md).

* Um cliente para consultar o banco de dados SQL do Azure. Considere usar [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) ou [Visual Studio Code](../../azure-sql/database/connect-query-vscode.md).

* [Ondulação](https://curl.haxx.se/). Curl é uma ferramenta para transferir dados de ou um cluster HDInsight ou para ele.

* [JQ](https://stedolan.github.io/jq/). O utilitário jq é usado para processar os dados JSON retornados de solicitações REST.

* Familiaridade com o Sqoop. Para obter mais informações, consulte [Guia do usuário do Sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>Enviar trabalhos do Apache Sqoop usando o cURL

Use a rotação para exportar dados usando os trabalhos do Apache Sqoop do armazenamento do Azure para SQL Server.

> [!NOTE]  
> Ao usar o Curl ou qualquer outra comunicação do REST com WebHCat, você deve autenticar as solicitações, fornecendo o nome de usuário e a senha para o administrador do cluster HDInsight. Você também deve usar o nome do cluster como parte do URI (Uniform Resource Identifier) usado para enviar as solicitações ao servidor.

Para os comandos nesta seção, substitua `USERNAME` pelo usuário para autenticar no cluster e substitua `PASSWORD` pela senha da conta de usuário. Substitua `CLUSTERNAME` pelo nome do cluster.

A API REST é protegida por meio de [autenticação básica](https://en.wikipedia.org/wiki/Basic_access_authentication). Você deve sempre fazer solicitações usando HTTPS (HTTP seguro) para ajudar a garantir que suas credenciais sejam enviadas com segurança para o servidor.

1. Para facilitar o uso, defina as variáveis abaixo. Este exemplo se baseia em um ambiente Windows, revise conforme necessário para seu ambiente.

    ```cmd
    set CLUSTERNAME=
    set USERNAME=admin
    set PASSWORD=
    set SQLDATABASESERVERNAME=
    set SQLDATABASENAME=
    set SQLPASSWORD=
    set SQLUSER=sqluser
    ```

1. De uma linha de comando, use o seguinte comando para verificar se você pode se conectar ao cluster HDInsight:

    ```cmd
    curl -u %USERNAME%:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    Você deve receber uma resposta com esta aparência:

    ```json
    {"status":"ok","version":"v1"}
    ```

1. Use o seguinte para enviar um trabalho do sqoop:

    ```cmd
    curl -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% -d command="export --connect jdbc:sqlserver://%SQLDATABASESERVERNAME%.database.windows.net;user=%SQLUSER%@%SQLDATABASESERVERNAME%;password=%PASSWORD%;database=%SQLDATABASENAME% --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/sqoop
    ```

    Os parâmetros usados nesse comando são os seguintes:

   * **-d** -como `-G` não é usado, a solicitação assume como padrão o método post. `-d` especifica os valores de dados que são enviados com a solicitação.

       * **user.name** - o usuário que está executando o comando.

       * **command** - o comando Sqoop a ser executado.

       * **statusdir** - o diretório no qual o status deste trabalho será gravado.

     Esse comando retornará uma ID de trabalho que pode ser usada para verificar o status do trabalho.

       ```json
       {"id":"job_1415651640909_0026"}
       ```

1. Para verificar o status do trabalho, use o comando a seguir. Substitua `JOBID` pelo valor retornado na etapa anterior. Por exemplo, se o valor de retorno era `{"id":"job_1415651640909_0026"}` , `JOBID` seria `job_1415651640909_0026` . Revisar local de `jq` conforme necessário.

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | C:\HDI\jq-win64.exe .status.state
    ```

    Se o trabalho foi concluído, o estado será **SUCCEEDED**.

   > [!NOTE]  
   > Essa solicitação de Curl retorna um documento JSON (JavaScript Object Notation) com informações sobre o trabalho; jq é usado para recuperar o valor de estado.

1. Depois que o estado do trabalho for alterado para **SUCCEEDED**, você poderá recuperar os resultados do trabalho no Armazenamento de Blobs do Azure. O parâmetro `statusdir` transmitido com a consulta contém a localização do arquivo de saída; nesse caso, `wasb:///example/data/sqoop/curl`. Esse endereço armazena a saída do trabalho no `example/data/sqoop/curl` diretório no contêiner de armazenamento padrão usado pelo seu cluster HDInsight.

    É possível usar o Portal do Azure para acessar os blobs stderr e stdout.

1. Para verificar se os dados foram exportados, use as seguintes consultas do seu cliente SQL para exibir os dados exportados:

    ```sql
    SELECT COUNT(*) FROM [dbo].[log4jlogs] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[log4jlogs] WITH (NOLOCK);
    ```

## <a name="limitations"></a>Limitações

* Exportação em massa-com o HDInsight baseado em Linux, o conector do Sqoop usado para exportar dados para o Microsoft SQL Server ou o Azure SQL Database não oferece suporte a inserções em massa no momento.
* Envio em lote — com HDInsight baseado em Linux, ao usar o comutador `-batch` na execução de inserções, Sqoop executará várias inserções em vez de operações de inserção em lotes.

## <a name="summary"></a>Resumo

Conforme demonstrado neste documento, você pode usar uma solicitação HTTP bruta para executar, monitorar e exibir os resultados de trabalhos do Sqoop no cluster HDInsight.

Para obter mais informações sobre a interface REST usada neste artigo, confira o <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Guia da API REST do Apache Sqoop</a>.

## <a name="next-steps"></a>Próximas etapas

[Usar o Apache Sqoop com o Apache Hadoop no HDInsight](hdinsight-use-sqoop.md)

Para outros artigos do HDInsight que envolvem curl:

* [Criar clusters do Apache Hadoop usando a API REST do Azure](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Executar consultas do Apache Hive com o Apache Hadoop no HDInsight usando a REST](apache-hadoop-use-hive-curl.md)
* [Executar tarefas do MapReduce com o Apache Hadoop no HDInsight usando o REST](apache-hadoop-use-mapreduce-curl.md)
