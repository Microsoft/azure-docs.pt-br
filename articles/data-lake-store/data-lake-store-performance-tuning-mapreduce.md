---
title: Diretrizes de Ajuste de Desempenho do Azure Data Lake Storage Gen1 MapReduce | Microsoft Docs
description: Diretrizes de Ajuste de Desempenho do Azure Data Lake Storage Gen1 MapReduce
services: data-lake-store
documentationcenter: ''
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: b9e5d034db4711384d2ac8a1083da5c93ea11900
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437235"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Orientação de ajuste de desempenho para o MapReduce no HDInsight e no Armazenamento de Data Lake do Azure Gen1

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do Azure Data Lake Storage Gen1**. Para obter instruções sobre como criar uma, confira [Introdução ao Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Cluster Azure HDInsight** com acesso a uma conta do Azure Data Lake Storage Gen1. Veja [Criar um cluster HDInsight com Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de habilitar a área de trabalho remota para o cluster.
* **Usando o MapReduce no HDInsight**.  Para obter mais informações, consulte [Usar o MapReduce no Hadoop no HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Diretrizes de ajuste de desempenho no Data Lake Storage Gen1**.  Para obter os conceitos gerais de desempenho, consulte [Diretrizes de ajuste de desempenho do Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>parâmetros

Ao executar trabalhos do MapReduce, aqui estão os parâmetros mais importantes que você pode configurar para aumentar o desempenho no Data Lake armazenamento Gen1:

* **Mapreduce.map.memory.mb** – a quantidade de memória para alocar para cada mapeador
* **Mapreduce.job.maps** – o número de tarefas de mapeamento por trabalho
* **Mapreduce.reduce.memory.mb** – a quantidade de memória para alocar para cada redutor
* **Mapreduce.job.reduces** – o número de tarefas de redução por trabalho

**Mapreduce.map.memory / Mapreduce.reduce.memory** Esse número deve ser ajustado com base na quantidade de memória que é necessária para a tarefa de mapeamento e/ou redução.  Os valores padrão de mapreduce.map.memory e mapreduce.reduce.memory podem ser exibidos no Ambari por meio da configuração do Yarn.  No Ambari, navegue até YARN e exiba a guia Configurações.  A memória YARN será exibida.  

**Mapreduce.job.maps / Mapreduce.job.reduces** Isso determinará o número máximo de mapeadores ou redutores a serem criados.  O número de divisões determinará quantas mapeadores serão criados para o trabalho MapReduce.  Portanto, você poderá obter menos mapeadores que o solicitado se houver menos divisões do que o número de mapeadores solicitado.       

## <a name="guidance"></a>Diretrizes

**Etapa 1: Determinar o número de trabalhos em execução** -por padrão, o MapReduce usará todo o cluster para o seu trabalho.  Você pode usar uma parte menor do cluster ao usar um número de mapeadores menor do que o número de contêineres disponíveis existentes.  As diretrizes neste documento pressupõem que seu aplicativo é o único aplicativo em execução no cluster.      

**Etapa 2: Definir mapreduce.map.memory/mapreduce.reduce.memory** – o tamanho da memória para o mapa e reduzir as tarefas sejam dependentes em seu trabalho específico.  Caso deseje aumentar a simultaneidade, você poderá reduzir o tamanho da memória.  O número de tarefas em execução simultânea depende do número de contêineres.  Diminuindo a quantidade de memória por mapeador ou redutor, mais contêineres podem ser criados, o que permite a execução simultânea de mais mapeadores ou redutores.  Diminuir muito a quantidade de memória pode fazer com que alguns processos fiquem sem memória.  Se você receber um erro de heap quando executar seu trabalho, você deverá aumentar a memória por mapeador ou redutor.  Considere que adicionar mais contêineres adicionará sobrecarga extra para cada contêiner adicional, o que pode degradar o desempenho.  Outra alternativa é de obter mais memória pelo uso de um cluster com maiores quantidades de memória ou pelo aumento do número de nós no cluster.  Mais memória permitirá o uso de mais contêineres, o que significa mais simultaneidade.  

**Etapa 3: Determinar a memória YARN Total** – para ajustar mapreduce.job.maps/mapreduce.job.reduces, você deve considerar a quantidade de memória YARN total disponível para uso.  Essas informações estão disponíveis no Ambari.  Navegue até YARN e exiba a guia Configurações.  A memória YARN é exibida nessa janela.  Para obter a memória YARN total, você deve multiplicar a memória YARN por nó pelo número de nós em seu cluster.

    Total YARN memory = nodes * YARN memory per node
Se você estiver usando um cluster vazio, a memória poderá ser a memória YARN total para seu cluster.  Se outros aplicativos estiverem usando memória, você poderá usar apenas uma parte da memória do cluster, reduzindo o número de mapeadores ou redutores para o número de contêineres que você deseja usar.  

**Etapa 4: Calcular o número de contêineres YARN** – contêineres YARN determinam a quantidade de simultaneidade disponível para o trabalho.  Pegar a memória YARN total e divida-a por mapreduce.map.memory.  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**Etapa 5: Definir mapreduce.job.maps/mapreduce.job.reduces** definido mapreduce.job.maps/mapreduce.job.reduces para pelo menos o número de contêineres disponíveis.  Você pode experimentar ainda mais aumentando o número de mapeadores e redutores para ver se obtém um melhor desempenho.  Tenha em mente que mais mapeadores terão uma sobrecarga adicional, então ter um número excessivo de mapeadores pode degradar o desempenho.  

O isolamento de CPU e agendamento de CPU são desligados por padrão para que o número de contêineres YARN seja restrito pela memória.

## <a name="example-calculation"></a>Exemplo de cálculo

Suponhamos que você tenha um cluster composto de oito nós D14 e deseje executar um trabalho com uso intensivo de E/S.  Aqui estão os cálculos que você deve fazer:

**Etapa 1: Determinar o número de trabalhos em execução** -para nosso exemplo, vamos supor que nosso trabalho seja o único em execução.  

**Etapa 2: Definir mapreduce.map.memory/mapreduce.reduce.memory** – para nosso exemplo, você está executando um trabalho com uso intensivo de e/s e decide que 3 GB de memória para tarefas de mapeamento serão suficiente.

    mapreduce.map.memory = 3GB
**Etapa 3: Determinar a memória YARN Total**

    total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**Etapa 4: Calcular o número de contêineres YARN**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**Etapa 5: Definir mapreduce.job.maps/mapreduce.job.reduces**

    mapreduce.map.jobs = 256

## <a name="limitations"></a>Limitações

**Limitação do Data Lake Storage Gen1**

Como um serviço multilocatário, o Data Lake Storage Gen1 define limites de largura de banda de nível de conta.  Se você atingir esses limites, começará a ver falhas de tarefas. Isso pode ser identificado observando os erros de limitação nos logs de tarefa.  Se precisar de mais largura de banda para seu trabalho, entre em contato conosco.   

Para verificar se há problemas de limitação, você precisa habilitar o log de depuração no lado do cliente. Veja como fazer isso:

1. Coloque a seguinte propriedade nas propriedades de log4j em Ambari > YARN > Configuração > Avançado yarn-log4j: log4j.logger.com.microsoft.azure.datalake.store=DEBUG

2. Reinicie todos os nós/o serviço para que a configuração entre em vigor.

3. Se o problema for de limitação, você verá o código de erro HTTP 429 no arquivo de log do YARN. O arquivo de log do YARN está em /tmp/&lt;usuário&gt;/yarn.log

## <a name="examples-to-run"></a>Exemplos para execução

Para demonstrar como o MapReduce é executado no Data Lake Storage Gen1, abaixo está um código de exemplo que foi executado em um cluster com as seguintes configurações:

* 16 nós D14v2
* Cluster Hadoop executando HDI 3.6

Para um ponto de partida, aqui estão alguns comandos de exemplo para executar o MapReduce Teragen, Terasort e Teravalidate.  Você pode ajustar esses comandos com base em seus recursos.

**Teragen**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input

**Terasort**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output

**Teravalidate**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
