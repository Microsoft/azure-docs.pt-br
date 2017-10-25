---
title: Diretrizes de ajuste de desempenho para MapReduce do Azure Data Lake Store | Microsoft Docs
description: Diretrizes de ajuste de desempenho para MapReduce do Azure Data Lake Store
services: data-lake-store
documentationcenter: 
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: 9528148792f083cb0e48d356e61cf61762ee954f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-store"></a>Diretrizes de ajuste do desempenho para o MapReduce no HDInsight e Azure Data Lake Store


## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do repositório Azure Data Lake**. Para obter instruções sobre como criar uma, consulte [Introdução ao repositório Azure Data Lake](data-lake-store-get-started-portal.md)
* **Cluster HDInsight do Azure** com acesso a uma conta do Repositório Data Lake. Confira [Criar um cluster HDInsight com o Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Certifique-se de habilitar a área de trabalho remota para o cluster.
* **Usando o MapReduce no HDInsight**.  Para obter mais informações, consulte [Usar o MapReduce no Hadoop no HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-use-mapreduce)  
* **Diretrizes de ajuste de desempenho no ADLS**.  Para ver os conceitos gerais de desempenho, confira [Diretrizes de ajuste de desempenho do Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-performance-tuning-guidance)  

## <a name="parameters"></a>parâmetros

Ao executar trabalhos do MapReduce, aqui estão os parâmetros mais importantes que você pode configurar para aumentar o desempenho no ADLS:

* **Mapreduce.map.memory.mb** – a quantidade de memória para alocar para cada mapeador
* **Mapreduce.job.maps** – o número de tarefas de mapeamento por trabalho
* **Mapreduce.reduce.memory.mb** – a quantidade de memória para alocar para cada redutor
* **Mapreduce.job.reduces** – o número de tarefas de redução por trabalho

**Mapreduce.map.memory / Mapreduce.reduce.memory** Esse número deve ser ajustado com base na quantidade de memória que é necessária para a tarefa de mapeamento e/ou redução.  Os valores padrão de mapreduce.map.memory e mapreduce.reduce.memory podem ser exibidos no Ambari por meio da configuração do Yarn.  No Ambari, navegue até YARN e exiba a guia Configurações.  A memória YARN será exibida.  

**Mapreduce.job.maps / Mapreduce.job.reduces** Isso determinará o número máximo de mapeadores ou redutores a serem criados.  O número de divisões determinará quantas mapeadores serão criados para o trabalho MapReduce.  Portanto, você poderá obter menos mapeadores que o solicitado se houver menos divisões do que o número de mapeadores solicitado.       

## <a name="guidance"></a>Diretrizes

**Etapa 1: determinar o número de trabalhos em execução** – por padrão, o MapReduce usará todo o cluster para o seu trabalho.  Você pode usar uma parte menor do cluster ao usar um número de mapeadores menor do que o número de contêineres disponíveis existentes.  As diretrizes neste documento pressupõem que seu aplicativo é o único aplicativo em execução no cluster.      

**Etapa 2: Definir mapreduce.map.memory/mapreduce.reduce.memory** – o tamanho da memória para tarefas de mapeamento e redução dependerá do seu trabalho específico.  Caso deseje aumentar a simultaneidade, você poderá reduzir o tamanho da memória.  O número de tarefas em execução simultânea depende do número de contêineres.  Diminuindo a quantidade de memória por mapeador ou redutor, mais contêineres podem ser criados, o que permite a execução simultânea de mais mapeadores ou redutores.  Diminuir muito a quantidade de memória pode fazer com que alguns processos fiquem sem memória.  Se você receber um erro de heap quando executar seu trabalho, você deverá aumentar a memória por mapeador ou redutor.  Considere que adicionar mais contêineres adicionará sobrecarga extra para cada contêiner adicional, o que pode degradar o desempenho.  Outra alternativa é de obter mais memória pelo uso de um cluster com maiores quantidades de memória ou pelo aumento do número de nós no cluster.  Mais memória permitirá o uso de mais contêineres, o que significa mais simultaneidade.  

**Etapa 3: determinar a memória YARN total** – para ajustar mapreduce.job.maps/mapreduce.job.reduces, você deve considerar a quantidade de memória YARN total disponível para uso.  Essas informações estão disponíveis no Ambari.  Navegue até YARN e exiba a guia Configurações.  A memória YARN é exibida nessa janela.  Para obter a memória YARN total, você deve multiplicar a memória YARN por nó pelo número de nós em seu cluster.

    Total YARN memory = nodes * YARN memory per node
Se você estiver usando um cluster vazio, a memória poderá ser a memória YARN total para seu cluster.  Se outros aplicativos estiverem usando memória, você poderá usar apenas uma parte da memória do cluster, reduzindo o número de mapeadores ou redutores para o número de contêineres que você deseja usar.  

**Etapa 4: calcular o número de contêineres YARN** – contêineres YARN determinam a quantidade de simultaneidade disponível para o trabalho.  Pegar a memória YARN total e divida-a por mapreduce.map.memory.  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**Etapa 5: definir mapreduce.job.maps/mapreduce.job.reduces** Defina mapreduce.job.maps/mapreduce.job.reduces para, no mínimo, o número de contêineres disponíveis.  Você pode experimentar ainda mais aumentando o número de mapeadores e redutores para ver se obtém um melhor desempenho.  Tenha em mente que mais mapeadores terão uma sobrecarga adicional, então ter um número excessivo de mapeadores pode degradar o desempenho.  

O isolamento de CPU e agendamento de CPU são desligados por padrão para que o número de contêineres YARN seja restrito pela memória.

## <a name="example-calculation"></a>Exemplo de cálculo

Suponhamos que você tenha um cluster composto de oito nós D14 e deseje executar um trabalho com uso intensivo de E/S.  Aqui estão os cálculos que você deve fazer:

**Etapa 1: determinar o número de trabalhos em execução** – em nosso exemplo, vamos supor que nosso trabalho seja o único em execução.  

**Etapa 2: definir mapreduce.map.memory/mapreduce.reduce.memory** – em nosso exemplo, você está executando um trabalho com uso intensivo de E/S e decide que 3 GB de memória para tarefas de mapeamento serão suficientes.

    mapreduce.map.memory = 3GB
**Etapa 3: determinar o total de memória YARN**

    total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**Etapa 4: calcular o número de contêineres YARN**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**Etapa 5: definir mapreduce.job.maps/mapreduce.job.reduces**

    mapreduce.map.jobs = 256

## <a name="limitations"></a>Limitações

**Limitação do ADLS**

Como um serviço multilocatário, o ADLS define limites de largura de banda de nível de conta.  Se você atingir esses limites, começará a ver falhas de tarefas. Isso pode ser identificado observando os erros de limitação nos logs de tarefa.  Se precisar de mais largura de banda para seu trabalho, entre em contato conosco.   

Para verificar se há problemas de limitação, você precisa habilitar o log de depuração no lado do cliente. Veja como fazer isso:

1. Coloque a seguinte propriedade nas propriedades de log4j em Ambari > YARN > Configuração > Avançado yarn-log4j: log4j.logger.com.microsoft.azure.datalake.store=DEBUG

2. Reinicie todos os nós/o serviço para que a configuração entre em vigor.

3. Se o problema for de limitação, você verá o código de erro HTTP 429 no arquivo de log do YARN. O arquivo de log do YARN está em /tmp/&lt;usuário&gt;/yarn.log

## <a name="examples-to-run"></a>Exemplos para execução

Para demonstrar como o MapReduce é executado no Azure Data Lake Store, temos abaixo um código de exemplo que foi executado em um cluster com as seguintes configurações:

* 16 nós D14v2
* Cluster Hadoop executando HDI 3.6

Para um ponto de partida, aqui estão alguns comandos de exemplo para executar o MapReduce Teragen, Terasort e Teravalidate.  Você pode ajustar esses comandos com base em seus recursos.

**Teragen**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input

**Terasort**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output

**Teravalidate**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
