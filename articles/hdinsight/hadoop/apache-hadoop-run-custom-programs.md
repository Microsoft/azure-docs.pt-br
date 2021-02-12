---
title: Executar programas MapReduce personalizados - HDInsight do Azure
description: Quando e como executar programas do Apache MapReduce personalizados em clusters do Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 0f7a3676db14dbf61d3976690c8f9c71a660f889
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943233"
---
# <a name="run-custom-mapreduce-programs"></a>Executar programas de MapReduce personalizados

Sistemas de big data baseados no Apache Hadoop, como HDInsight, permitem o processamento de dados usando uma ampla variedade de ferramentas e tecnologias. A tabela a seguir descreve as principais vantagens e considerações para cada um.

| Mecanismo de consulta | Vantagens | Considerações |
| --- | --- | --- |
| **Apache Hive usando HiveQL** | <ul><li>Uma solução excelente para processamento em lotes e análise de grandes quantidades de dados imutáveis, resumo de dados e consulta sob demanda. Ele usa uma sintaxe SQL familiar.</li><li>Ele pode ser usado para produzir tabelas de dados permanentes que podem ser facilmente dividas e indexadas.</li><li>Várias tabelas e visualizações externas podem ser criadas com os mesmos dados.</li><li>Ele dá suporte a um simples data warehouse que fornece recursos de grande expansão e tolerância a falhas para processamento e armazenamento de dados.</li></ul> | <ul><li>Ele requer que os dados de origem tenham pelo menos algumas estruturas de identificação.</li><li>Ele não é adequado para consultas em tempo real e atualizações em nível de linha. Ele é mais bem usado para trabalhos em lotes em grandes conjuntos de dados.</li><li>Ele poderá não executar alguns tipos de tarefas de processamento complexo.</li></ul> |
| **Apache Pig usando Pig Latin** | <ul><li>Uma solução excelente para manipulação de dados como conjuntos, mesclagem e filtragem de conjuntos de dados, para aplicação de funções a registros ou grupos de registros e para reestruturação de dados com a definição de colunas, pelo agrupamento de valores ou pela conversão de colunas em linhas.</li><li>Ele pode usar uma abordagem baseada em fluxo de trabalho como uma sequência de operações nos dados.</li></ul> | <ul><li>Os usuários do SQL podem achar o Pig Latin menos familiar e mais difícil de usar do que o HiveQL.</li><li>A saída padrão é geralmente um arquivo de texto e portanto pode ser mais difícil de usar com as ferramentas de visualização como o Excel. Normalmente, você criará uma camada de tabela do hive na saída.</li></ul> |
| **Mapa personalizado/redução** | <ul><li>Ele fornece controle total sobre o mapa e as fases de redução e execução.</li><li>Ele permite que as consultas sejam otimizadas para alcançar desempenho máximo do cluster, ou para reduzir a carga nos servidores e na rede.</li><li>Os componentes podem ser gravados em uma variedade de linguagens conhecidas.</li></ul> | <ul><li>É mais difícil do que usar o Pig ou o hive porque você deve criar seu próprio mapa e reduzir os componentes.</li><li>Os processos que exigem a união de conjuntos de dados são mais difíceis de implementar.</li><li>Embora estruturas de teste estejam disponíveis, depurar códigos é mais complexo do que um aplicativo normal porque o código é executado como um trabalho em lotes sob o controle do Agendador de trabalho do Hadoop.</li></ul> |
| **Apache HCatalog** | <ul><li>Ele abstrai os detalhes do caminho de armazenamento, facilitando a administração e removendo a necessidade dos usuários de saber onde os dados estão armazenados.</li><li>Isso possibilita a notificação de eventos como a disponibilidade de dados, permitindo que outras ferramentas, como o Oozie, detectem quando operações tiverem ocorrido.</li><li>Ele apresenta uma exibição relacional dos dados, incluindo o particionamento por chave e facilita o acesso de dados.</li></ul> | <ul><li>Ele dá suporte por padrão a formatos de arquivo RCFile, texto CSV, texto JSON, SequenceFile e ORC, mas talvez seja necessário gravar um SerDe personalizado para outros formatos.</li><li>HCatalog não é thread-safe.</li><li>Há algumas restrições sobre os tipos de dados para colunas ao usar o carregador do HCatalog em scripts do Pig. Para obter mais informações, consulte [Tipos de Dados do HCatLoader](https://cwiki.apache.org/confluence/display/Hive/HCatalog%20LoadStore#HCatalogLoadStore-HCatLoaderDataTypes) na documentação do Apache HCatalog.</li></ul> |

Normalmente, você usa a mais simples dessas soluções que podem fornecer os resultados que você precisa. Por exemplo, você poderá obter esses resultados usando apenas o Hive, mas para cenários mais complexos pode precisar usar o Pig, ou até mesmo gravar seu próprio mapa e reduzir os componentes. Você também pode decidir, depois de testar o Hive ou o Pig, que criar um mapa personalizado e reduzir componentes podem oferecer melhor desempenho, permitindo que você ajuste e otimize o processamento.

## <a name="custom-mapreduce-components"></a>Mapa personalizado/reduzir componentes

Código de mapeamento/reduzir consiste em duas funções separadas, implementadas como **mapa** e **reduzir** componentes. O componente **mapa** é executado em paralelo em vários nós do cluster, cada nó aplicando o mapeamento para o subconjunto do nó de dados. O componente **redução** agrupa e resume os resultados de todas as funções de mapa. Para obter mais informações, consulte [Usar o MapReduce no Hadoop no HDInsight](hdinsight-use-mapreduce.md).

Na maioria dos cenários de processamento do HDInsight, é mais simples e mais eficiente usar uma abstração de nível superior, como Pig ou Hive. Você também pode criar o mapa personalizado e a redução de componentes para uso em scripts do Hive para executar um processamento mais sofisticado.

Mapa personalizado/redução de componentes geralmente são gravados em Java. O Hadoop fornece uma interface de streaming que também permite que os componentes usados sejam desenvolvidos em outras linguagens, como C#, F#, Visual Basic, Python e JavaScript.

* Para uma apresentação sobre como desenvolver programas personalizados MapReduce no Java, consulte [Desenvolver programas MapReduce em Java para Hadoop no HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md).

Considere a criação de seu próprio mapa e a redução de componentes nas seguintes condições:

* Você precisa processar dados que são completamente não estruturados analisando os dados e usando lógica personalizada para obter informações estruturadas deles.
* Você deseja realizar tarefas complexas que são difíceis (ou impossíveis) de expressar no Pig ou no Hive sem recorrer à criação de um UDF. Por exemplo, você talvez precise criar usar um serviço externo de geocodificação para converter coordenadas de latitude e longitude ou endereços IP na fonte de dados em nomes de localização geográfica.
* Você deseja reutilizar o seu código existente do .NET, do Python ou do JavaScript em mapeamento/redução de componentes usando a interface de transmissão do Hadoop.

## <a name="upload-and-run-your-custom-mapreduce-program"></a>Carregar e executar o programa MapReduce personalizado

Os programas de MapReduce mais comuns são gravados em Java e compilados em um arquivo jar.

1. Depois de desenvolver, compilar e testar seu programa MapReduce, use o `scp` comando para carregar o arquivo JAR para o cabeçalho.

    ```cmd
    scp mycustomprogram.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Substitua CLUSTERNAME pelo nome do cluster. Se você tiver usado uma senha para proteger a conta SSH, será solicitado a inserir a senha. Se você tiver usado um certificado, talvez precise usar o parâmetro `-i` para especificar o arquivo da chave privada.

1. Use o [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao cluster. Edite o comando abaixo substituindo CLUSTERNAME pelo nome do cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Da sessão SSH, execute o programa MapReduce por meio do YARN.

    ```bash
    yarn jar mycustomprogram.jar mynamespace.myclass /example/data/sample.log /example/data/logoutput
    ```

    Esse comando envia o trabalho de MapReduce para o YARN. O arquivo de entrada é `/example/data/sample.log`, e o diretório de saída é `/example/data/logoutput`. Os arquivos de entrada e saída são armazenados no armazenamento padrão do cluster.

## <a name="next-steps"></a>Próximas etapas

* [Use C# com fluxo de MapReduce no Apache Hadoop no HDInsight](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Desenvolver programas Java MapReduce para o Apache Hadoop no HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Use o Azure Toolkit for Eclipse para criar aplicativos do Apache Spark para um cluster do HDInsight](../spark/apache-spark-eclipse-tool-plugin.md)
* [Usar funções definidas pelo usuário (UDF) do Python com o Apache Hive e o Apache Pig no HDInsight](python-udf-hdinsight.md)
