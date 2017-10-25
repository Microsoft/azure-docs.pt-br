---
title: "Introdução ao Servidor R no Azure HDInsight | Microsoft Docs"
description: "Saiba como usar o R Server no HDInsight para criar aplicativos para análise de Big Data."
services: hdinsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 6dc21bf5-4429-435f-a0fb-eea856e0ea96
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/19/2017
ms.author: bradsev
ms.openlocfilehash: a9f61ec7c051ccdd867aec94121347db19b059b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
#<a name="introduction-to-r-server-and-open-source-r-capabilities-on-hdinsight"></a>Introdução ao Servidor R e recursos de R de software livre no HDInsight

O Microsoft R Server está disponível como uma opção de implantação quando você cria clusters HDInsight no Azure. Essa nova funcionalidade fornece aos cientistas de dados, estatísticos e programadores de R o acesso sob demanda a métodos escalonáveis e distribuídos de análise no HDInsight.

Os clusters podem ser dimensionados adequadamente para os projetos e as tarefas em questão e interrompidos quando não são mais necessários. Já que fazem parte do Azure HDInsight, esses clusters recebem suporte de nível empresarial 24 horas por dia e sete dias por semana, apresentam um SLA de 99,9% de tempo de ativação e a capacidade de se integrarem a outros componentes no ecossistema do Azure.

O R Server no HDInsight fornece os recursos mais recentes para análise de R em conjuntos de dados de praticamente qualquer tamanho carregados no Blob do Azure ou no armazenamento do Data Lake. Uma vez que o Servidor R é criado no R de software livre, os aplicativos baseados em R que você compilar podem aproveitar qualquer um dos mais de 8000 pacotes de R de software livre. As rotinas no ScaleR, o pacote de análise de Big Data da Microsoft que está incluído no R Server, também estão disponíveis.

O nó de borda de um cluster fornece um local conveniente para se conectar ao cluster e executar os scripts de R. Com um nó do borda, você tem a opção de executar funções distribuídas paralelizadas do ScaleR nos núcleos do servidor do nó de borda. Você também pode executá-las em todos os nós do cluster usando o Hadoop Map Reduce do ScaleR ou os contextos de computação do Spark.

Os modelos ou previsões que resultam de análises podem ser baixados para uso no local. Elas também podem ser operacionalizadas em qualquer lugar no Azure, como por meio de um [serviço Web](../machine-learning/studio/publish-a-machine-learning-web-service.md) no [Azure Machine Learning Studio](http://studio.azureml.net).

## <a name="get-started-with-r-on-hdinsight"></a>Introdução ao R no HDInsight
Para incluir o Servidor R em um cluster HDInsight, selecione o tipo de cluster do Servidor de R ao criar um cluster HDInsight usando o Portal do Azure. O tipo de cluster do R Server inclui o R Server nos mesmos nós de dados do cluster e em um nó de borda, que serve como uma zona de aterrissagem para análise com base no R Server. Veja [Introdução ao uso do R Server no HDInsight](hdinsight-hadoop-r-server-get-started.md) para ver um passo a passo sobre como criar o cluster.

## <a name="learn-about-data-storage-options"></a>Saiba mais sobre as opções de armazenamento de dados
O armazenamento padrão para o sistema de arquivos HDFS de clusters HDInsight pode ser associado a uma conta de Armazenamento do Azure ou ao Azure Data Lake Store. Essa associação assegura que os dados carregados para o armazenamento de cluster durante a análise sejam persistentes. Há várias ferramentas para lidar com a transferência de dados para a opção de armazenamento selecionada por você, incluindo o recurso de upload com base no portal da conta de armazenamento e o utilitário [AzCopy](../storage/common/storage-use-azcopy.md).

Independentemente da opção de armazenamento primário em uso, você tem a opção de adicionar acesso ao Blob adicional e aos Data Lake Stores durante o processo de provisionamento do cluster. Consulte [Guia de Introdução ao R Server no HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-get-started) para obter informações sobre como adicionar acesso a contas adicionais. Consulte o artigo suplementar [Opções de Armazenamento do Azure para o R Server no HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage) para saber mais sobre como usar várias contas de armazenamento.

Também é possível usar os [Arquivos do Azure](../storage/files/storage-how-to-use-files-linux.md) como uma opção de armazenamento para uso no nó de borda. Os Arquivos do Azure permitem montar um compartilhamento de arquivos que foi criado em um Armazenamento do Azure para o sistema de arquivos do Linux. Para obter mais informações sobre essas opções de armazenamento de dados para R Server no cluster HDInsight, confira [Opções de Armazenamento do Azure para o R Server em clusters HDInsight](hdinsight-hadoop-r-server-storage.md).

## <a name="access-r-server-on-the-cluster"></a>Acessar o Servidor R no cluster
Você pode se conectar ao R Server no nó de borda usando um navegador, desde que você tenha optado por incluir o RStudio Server durante o processo de provisionamento. Se você não o instalou ao provisionar o cluster, você pode adicioná-lo mais tarde. Para obter informações sobre como instalar o RStudio Server após a criação de um cluster, veja [Instalando o RStudio Server em clusters HDInsight](hdinsight-hadoop-r-server-install-r-studio.md). Você também pode se conectar ao R Server usando PuTTY/SSH para acessar o console do R. 

## <a name="develop-and-run-r-scripts"></a>Desenvolver e executar scripts R
Os scripts R que você criar e executar podem usar qualquer um dos mais de 8000 pacotes de software livre do R, além das rotinas paralelizadas e distribuídas disponíveis na biblioteca do ScaleR. Em geral, um script que é executado com o R Server no nó de borda é executado no interpretador de R nesse nó. A exceção são essas etapas que precisam chamar uma função do ScaleR com um contexto de computação que é definido para o Map Reduce do Hadoop (RxHadoopMR) ou Spark (RxSpark). Nesse caso, a função é executada de maneira distribuída por esses nós de dados (tarefa) do cluster que são associados aos dados referenciados. Para saber mais sobre as diferentes opções de contexto de computação, confira [Opções de contexto de computação para o Servidor R no HDInsight](hdinsight-hadoop-r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Operacionalizar um modelo
Quando sua modelagem de dados estiver concluída, você poderá operacionalizar o modelo para fazer previsões sobre novos dados, no Azure ou então local. Esse processo é conhecido como pontuação. A pontuação pode ser feita em HDInsight, no Azure Machine Learning ou local.

### <a name="score-in-hdinsight"></a>Pontuação no HDInsight
Para pontuar no HDInsight, escreva uma função R que chame seu modelo para fazer previsões para um novo arquivo de dados que você carregou na conta de armazenamento. Em seguida, salve as previsões na conta de armazenamento. Você pode executar a rotina sob demanda no nó de borda do seu cluster ou usando um trabalho agendado.  

### <a name="score-in-azure-machine-learning-aml"></a>Pontuação no AML (Azure Machine Learning)
Para pontuar usando um serviço Web do AML, use o pacote R do Azure Machine Learning de software livre conhecido como [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) para publicar seu modelo como um serviço Web do Azure. Para sua conveniência, este pacote é pré-instalado no nó de borda. Em seguida, use os recursos no Machine Learning para criar uma interface de usuário para o serviço Web e chame o serviço Web conforme necessidade para pontuação.

Se você escolher essa opção, será preciso converter qualquer objeto de modelo do ScaleR em objeto de modelo de software livre equivalente para uso com o serviço Web. Use as funções de coerção do ScaleR, tais como `as.randomForest()` para modelos baseados em combinação, para essa conversão.

### <a name="score-on-premises"></a>Pontuação local
Para pontuar no local após criar o modelo, você pode serializar o modelo em R, baixá-lo, desserializá-lo e usá-lo para pontuação de novos dados. Você pode pontuar novos dados usando a abordagem descrita anteriormente em [Pontuação no HDInsight](#scoring-in-hdinsight) ou [usando o DeployR](https://deployr.revolutionanalytics.com/).

## <a name="maintain-the-cluster"></a>Manter o cluster
### <a name="install-and-maintain-r-packages"></a>Instalar e manter pacotes R
A maioria dos pacotes R que você usa será exigida no nó de borda, uma vez que a maioria das etapas dos scripts R será executada lá. Para instalar pacotes R adicionais no nó de borda, você pode usar o método `install.packages()` usual em R.

Se estiver usando apenas rotinas da biblioteca do ScaleR pelo cluster, você geralmente não precisará instalar pacotes R adicionais nos nós de dados. No entanto, talvez você precise de pacotes adicionais para dar suporte ao uso da execução de **rxExec** ou **RxDataStep** nos nós de dados.

Nesses casos, os pacotes adicionais podem ser instalados com uma ação de script após a criação do cluster. Para saber mais, confira [Criando um cluster HDInsight com o R Server](hdinsight-hadoop-r-server-get-started.md).   

### <a name="change-hadoop-map-reduce-memory-settings"></a>Alterar as configurações de memória do Hadoop Map Reduce
Um cluster pode ser modificado para alterar a quantidade de memória disponível para o Servidor R quando ele está executando um trabalho do Map Reduce. Para modificar um cluster, use a interface de usuário do Apache Ambari que está disponível por meio da folha Portal do Azure do seu cluster. Para obter instruções sobre como acessar a interface de usuário do Ambari para seu cluster, confira [Gerenciar clusters HDInsight usando a interface de usuário do Ambari Web](hdinsight-hadoop-manage-ambari.md).

Também é possível alterar a quantidade de memória disponível para o R Server usando opções do Hadoop na chamada para **RxHadoopMR** , como se segue:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Ajustar escala do cluster
Um cluster existente pode ser escalado e reduzido verticalmente por meio do portal. Ao ajustar a escala, você pode ganhar a capacidade adicional que pode precisar para tarefas de processamento mais amplo, ou pode escalar um cluster de volta quando ele estiver ocioso. Para obter instruções sobre como escalar um cluster, confira [Gerenciar clusters HDInsight](hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Manter o sistema
A manutenção para aplicar patches do sistema operacional e outras atualizações é executada nas VMs do Linux subjacentes em um cluster HDInsight fora do horário comercial. Normalmente, a manutenção é feita às 3:30 (com base na hora local da VM) todas as segundas e quintas-feiras. As atualizações são executadas de forma que não afetem mais de um quarto do cluster por vez.  

Como os nós de cabeçalho são redundantes e nem todos os nós de dados são afetados, todos os trabalhos que estiverem em execução durante esse tempo podem ficar lentos. No entanto, todos eles devem continuar em execução até a conclusão. Todos os dados locais ou de software personalizados que você tem são preservados nesses eventos de manutenção, a menos que uma falha catastrófica ocorra e exija uma recriação do cluster.

## <a name="learn-about-ide-options-for-r-server-on-an-hdinsight-cluster"></a>Saiba mais sobre opções de IDE para o Servidor R em um cluster HDInsight
O nó de borda do Linux de um cluster HDInsight é a zona de aterrissagem para análises baseadas em R. Versões recentes do HDInsight fornecem uma opção padrão para instalação da versão de comunidade do [RStudio Server](https://www.rstudio.com/products/rstudio-server/) no nó de borda como um IDE baseado em navegador. O uso do RStudio Server como um IDE para o desenvolvimento e execução de scripts de R pode ser consideravelmente mais produtivo do que apenas usar o console de R. Se você optar por não adicionar o RStudio Server ao criar o cluster, mas quiser adicioná-lo mais tarde, veja [Instalar o R Studio Server em clusters HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-install-r-studio).

Outra opção de IDE completa é instalar um IDE de área de trabalho e usá-lo para acessar o cluster por meio do uso de um contexto de computação do Spark ou do Map Reduce remoto. As opções incluem a Microsoft RTVS ([Ferramentas do R para Visual Studio](https://www.visualstudio.com/features/rtvs-vs.aspx)), RStudio e o [StatET](http://www.walware.de/goto/statet) baseado em Eclipse da Walware.

Por fim, você pode acessar o console do R Server no nó de borda digitando **R** no prompt de comando do Linux após a conexão via SSH ou PuTY. Ao usar a interface do console, é prático executar um editor de texto para o desenvolvimento de scripts R em outra janela e recortar e colar seções do seu script no console R, conforme necessário.

## <a name="learn-about-pricing"></a>Saiba mais sobre preços
As taxas associadas a um cluster HDInsight com servidor R são estruturadas de forma semelhante às taxas para os clusters HDInsight padrão. Elas são baseadas no dimensionamento das VMs subjacentes por nome, data e nós de borda com adição de uma elevação de hora por núcleo. Para saber mais sobre os preços do HDInsight e a disponibilidade de uma avaliação gratuita de 30 dias, confira o [Preço do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Próximas etapas
Para aprender mais sobre como usar o R Server com clusters HDInsight, veja os tópicos a seguir:

* [Introdução ao uso do Servidor R no HDInsight](hdinsight-hadoop-r-server-get-started.md)
* [Adicionar Servidor do RStudio ao HDInsight (se não for instalado durante a criação do cluster)](hdinsight-hadoop-r-server-install-r-studio.md)
* [Opções de contexto de computação para o Servidor R no HDInsight](hdinsight-hadoop-r-server-compute-contexts.md)
* [Opções de Armazenamento do Azure para o Servidor R no HDInsight](hdinsight-hadoop-r-server-storage.md)
