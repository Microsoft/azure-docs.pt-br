---
title: Introdução aos Serviços ML no Azure HDInsight
description: Aprenda a usar os Serviços ML no HDInsight para criar aplicativos para análise de big data.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 06/12/2019
ms.openlocfilehash: a5e4c7acd407cef1bb5fc43d7e40014a6b088a0a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448898"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>O que são os Serviços de ML no Azure HDInsight

O Microsoft Machine Learning Server está disponível como uma opção de implantação quando você cria clusters HDInsight no Azure. O tipo de cluster que fornece essa opção é chamado **Serviços ML**. Essa funcionalidade fornece aos cientistas de dados, estatísticos e programadores de R o acesso sob demanda a métodos escalonáveis e distribuídos de análise no HDInsight.

O ML Services no HDInsight fornece os recursos mais recentes para análise de R em conjuntos de dados de praticamente qualquer tamanho carregados no Blob do Azure ou no armazenamento do Data Lake. Uma vez que o cluster do Microsoft ML Services é criado no R de software livre, os aplicativos baseados em R que você compilar podem aproveitar qualquer um dos mais de 8000 pacotes de R de software livre. As rotinas do ScaleR, o pacote de análise de dados grandes da Microsoft, também estão disponíveis.

O nó de borda de um cluster fornece um local conveniente para se conectar ao cluster e executar os scripts de R. Com um nó do borda, você tem a opção de executar funções distribuídas paralelizadas do ScaleR nos núcleos do servidor do nó de borda. Você também pode executá-los nos nós do cluster usando os contextos de cálculo Hadoop Map Reduce ou Apache Spark do ScaleR.

Os modelos ou previsões que resultam de análises podem ser baixados para uso local. Elas também podem ser operacionalizadas em qualquer lugar no Azure, como por meio de um [serviço Web](../../machine-learning/studio/publish-a-machine-learning-web-service.md) no [Azure Machine Learning Studio](https://studio.azureml.net).

## <a name="get-started-with-ml-services-on-hdinsight"></a>Introdução ML Services no HDInsight

Para criar um cluster de Serviços ML no Azure HDInsight, selecione o tipo de cluster **ML Services** ao criar um cluster HDInsight usando o portal do Azure. O tipo de cluster ML Services inclui o ML Server nos nós de dados do cluster e em um nó de borda, que serve como uma zona de aterrissagem para análises baseadas no ML Services. Consulte [Criar clusters do Apache Hadoop usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) para ver um passo a passo sobre como criar o cluster.

## <a name="why-choose-ml-services-in-hdinsight"></a>Por que escolher os Serviços ML no HDInsight?

Os Serviços ML no HDInsight fornecem os seguintes benefícios:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>Inovação AI da Microsoft e open-source

  O ML Services inclui um conjunto distribuído de algoritmos altamente escalonáveis como [RevoscaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), [ revoscalepy](https://docs.microsoft.com/machine-learning-server/python-reference/revoscalepy/revoscalepy-package) e [microsoftML](https://docs.microsoft.com/machine-learning-server/python-reference/microsoftml/microsoftml-package) que podem funcionar em tamanhos de dados maiores que o tamanho da memória física, e executar em uma ampla variedade de plataformas de maneira distribuída. Saiba mais sobre a coleção de [pacotes R](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) e [pacotes Python](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) personalizada da Microsoft incluída no produto.
  
  O ML Services une essas inovações e contribuições da Microsoft provenientes da comunidade de código aberto (kits de ferramentas R, Python e AI), tudo sobre uma única plataforma de nível corporativo. Qualquer pacote de aprendizado de máquina de código aberto R ou Python pode trabalhar lado a lado com qualquer inovação proprietária da Microsoft.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Operação e administração simples, segura e em grande escala

  As empresas que dependem de paradigmas e ambientes tradicionais investem muito tempo e esforço em direção à operacionalização. Isso resulta em custos e atrasos inflacionados, incluindo o tempo de conversão de modelos, iterações para mantê-los válidos e atualizados, aprovação regulamentar e gerenciamento de permissões por meio da operacionalização.

  O ML Services oferece operacionalização [de nível empresarial](https://docs.microsoft.com/machine-learning-server/what-is-operationalization), pois, depois que um modelo de aprendizado de máquina é concluído, são necessários apenas alguns cliques para gerar APIs de serviços da web. Esses [serviços da Web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) são hospedados em uma grade de servidores na nuvem e podem ser integrados a aplicativos de linha de negócios. A capacidade de implantar um permite a grade Elástico você dimensionar perfeitamente com as necessidades dos negócios, tanto para o lote e pontuação em tempo real. Para obter instruções, consulte [Operar os Serviços ML no HDInsight](r-server-operationalize.md).

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

## <a name="key-features-of-ml-services-on-hdinsight"></a>Principais recursos dos Serviços ML no HDInsight

Os seguintes recursos estão incluídos nos Serviços ML no HDInsight.

| Categoria da funcionalidade | DESCRIÇÃO |
|------------------|-------------|
| R habilitado | [pacotes R](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference) para soluções escritas em R, com uma distribuição de código aberto de R e infraestrutura de tempo de execução para execução de scripts. |
| Python habilitado | [Módulos Python](https://docs.microsoft.com/machine-learning-server/python-reference/introducing-python-package-reference) para soluções escritas em Python, com uma distribuição de software livre do Python e infraestrutura em tempo de execução para execução de scripts.
| [Modelos previamente treinados](https://docs.microsoft.com/machine-learning-server/install/microsoftml-install-pretrained-models) | Para análise visual e análise de sentimento de texto, pronto para classificar os dados que você fornecer. |
| [Implantar e consumir modelos](r-server-operationalize.md) | Operacionalizar seu servidor e implantar soluções como um serviço web. |
| [Execução Remota](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Inicie sessões remotas no cluster ML Services na sua rede a partir da estação de trabalho do seu cliente. |


## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Opções de armazenamento de dados para serviços ML no HDInsight

O armazenamento padrão para o sistema de arquivos HDFS de clusters HDInsight pode ser associado a uma conta de Armazenamento do Microsoft Azure ou ao Azure Data Lake Store. Essa associação assegura que os dados carregados para o armazenamento de cluster durante a análise sejam persistentes e os dados estão disponíveis mesmo depois que o cluster é excluído. Há várias ferramentas para lidar com a transferência de dados para a opção de armazenamento selecionada por você, incluindo o recurso de upload com base no portal da conta de armazenamento e o utilitário [AzCopy](../../storage/common/storage-use-azcopy.md).

Você tem a opção de ativar o acesso a armazenamentos de Blob e Data Lake adicionais durante o processo de provisionamento de cluster, independentemente da opção de armazenamento principal em uso.  Consulte o artigo [ Opções de armazenamento do Azure para serviços ML no artigo HDInsight ](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-r-server-storage) para saber mais sobre o uso de várias contas de armazenamento.

Também é possível usar os [Arquivos do Azure](../../storage/files/storage-how-to-use-files-linux.md) como uma opção de armazenamento para uso no nó de borda. Os Arquivos do Azure permitem montar um compartilhamento de arquivos que foi criado em um Armazenamento do Azure para o sistema de arquivos do Linux. Para obter mais informações sobre essas opções de armazenamento de dados para os Serviços ML no cluster HDInsight, consulte [ Opções de Armazenamento do Azure para os Serviços ML no HDInsight ](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>Acesse o nó de borda do ML Services

Você pode se conectar ao Microsoft ML Server no nó de borda usando um navegador. Ele é instalado por padrão durante a criação do cluster.  Você também pode se conectar ao nó de borda do cluster a partir da linha de comando usando o SSH / PuTTY para acessar o console R.

## <a name="develop-and-run-r-scripts"></a>Desenvolver e executar scripts R

Os scripts R que você criar e executar podem usar qualquer um dos mais de 8000 pacotes de software livre do R, além das rotinas paralelizadas e distribuídas disponíveis na biblioteca do ScaleR. Em geral, um script executado com o ML Services no nó de borda é executado no interpretador R nesse nó. A exceção são essas etapas que precisam chamar uma função do ScaleR com um contexto de computação que é definido para o Map Reduce do Hadoop (RxHadoopMR) ou Spark (RxSpark). Nesse caso, a função é executada de maneira distribuída por esses nós de dados (tarefa) do cluster que são associados aos dados referenciados. Para obter mais informações sobre as diferentes opções de contexto de computação, consulte [Opções de contexto de computação para serviços do ML no HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>Operacionalizar um modelo

Quando a modelagem de dados estiver concluída, você poderá operacionalizar o modelo para fazer previsões de novos dados no Azure ou no local. Esse processo é conhecido como pontuação. A pontuação pode ser feita em HDInsight, no Azure Machine Learning ou local.

### <a name="score-in-hdinsight"></a>Pontuação no HDInsight

Para pontuar no HDInsight, escreva uma função R que chame seu modelo para fazer previsões para um novo arquivo de dados que você carregou na conta de armazenamento. Em seguida, salve as previsões na conta de armazenamento. Você pode executar a rotina sob demanda no nó de borda do seu cluster ou usando um trabalho agendado.

### <a name="score-in-azure-machine-learning-aml"></a>Pontuação no AML (Azure Machine Learning)

Para pontuar usando um Azure Machine Learning, use o pacote R do Azure Machine Learning de software livre conhecido como [AzureML](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) para publicar seu modelo como um serviço Web do Azure. Para sua conveniência, este pacote é pré-instalado no nó de borda. Em seguida, use os recursos no Azure Machine Learning para criar uma interface de usuário para o serviço Web e chame o serviço Web conforme necessidade para pontuação.

Se você escolher essa opção, será preciso converter qualquer objeto de modelo do ScaleR em objeto de modelo de software livre equivalente para uso com o serviço Web. Use as funções de coerção do ScaleR, tais como `as.randomForest()` para modelos baseados em combinação, para essa conversão.

### <a name="score-on-premises"></a>Pontuação local

Para pontuar no local após criar o modelo, você pode serializar o modelo em R, baixá-lo, desserializá-lo e usá-lo para pontuação de novos dados. Você pode marcar novos dados usando a abordagem descrita anteriormente em [Pontuação no HDInsight](#score-in-hdinsight) ou usando [serviços da Web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services).

## <a name="maintain-the-cluster"></a>Manter o cluster

### <a name="install-and-maintain-r-packages"></a>Instalar e manter pacotes R

A maioria dos pacotes R que você usa será exigida no nó de borda, uma vez que a maioria das etapas dos scripts R será executada lá. Para instalar pacotes R adicionais no nó de borda, você pode usar o método `install.packages()` em R.

Se estiver usando apenas rotinas da biblioteca do ScaleR pelo cluster, você geralmente não precisará instalar pacotes R adicionais nos nós de dados. No entanto, talvez você precise de pacotes adicionais para dar suporte ao uso da execução de **rxExec** ou **RxDataStep** nos nós de dados.

Nesses casos, os pacotes adicionais podem ser instalados com uma ação de script após a criação do cluster. Para obter mais informações, consulte [ Gerenciar serviços ML no cluster do HDInsight ](r-server-hdinsight-manage.md).

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Alterar as configurações de memória do Apache Hadoop MapReduce

Um cluster pode ser modificado para alterar a quantidade de memória disponível para os Serviços ML quando estiver executando um trabalho MapReduce. Para modificar um cluster, use a interface de usuário do Apache Ambari que está disponível por meio da folha Portal do Azure do seu cluster. Para obter instruções sobre como acessar a interface de usuário do Ambari para seu cluster, confira [Gerenciar clusters HDInsight usando a interface de usuário do Ambari Web](../hdinsight-hadoop-manage-ambari.md).

Também é possível alterar a quantidade de memória disponível para os Serviços ML usando os switches do Hadoop na chamada para **RxHadoopMR** da seguinte maneira:

    hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"  

### <a name="scale-your-cluster"></a>Ajustar escala do cluster

Um cluster de serviços ML existente no HDInsight pode ser ampliado ou reduzido pelo portal. Ao ajustar a escala, você pode ganhar a capacidade adicional que pode precisar para tarefas de processamento mais amplo, ou pode escalar um cluster de volta quando ele estiver ocioso. Para obter instruções sobre como escalar um cluster, confira [Gerenciar clusters HDInsight](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Manter o sistema

A manutenção para aplicar patches do sistema operacional e outras atualizações é executada nas VMs do Linux subjacentes em um cluster HDInsight fora do horário comercial. Normalmente, a manutenção é feita às 3:30 (com base na hora local da VM) todas as segundas e quintas-feiras. As atualizações são executadas de forma que não afetem mais de um quarto do cluster por vez.

Como os nós de cabeçalho são redundantes e nem todos os nós de dados são afetados, todos os trabalhos que estiverem em execução durante esse tempo podem ficar lentos. No entanto, todos eles devem continuar em execução até a conclusão. Todos os dados locais ou de software personalizados que você tem são preservados nesses eventos de manutenção, a menos que uma falha catastrófica ocorra e exija uma recriação do cluster.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>Opções de IDE para serviços ML no HDInsight

O nó de borda do Linux de um cluster HDInsight é a zona de aterrissagem para análises baseadas em R. Versões recentes do HDInsight fornecem uma opção padrão para instalação do RStudio Server no nó de borda como um IDE baseado em navegador. O uso do RStudio Server como um IDE para o desenvolvimento e execução de scripts de R pode ser consideravelmente mais produtivo do que apenas usar o console de R.

Você pode instalar um IDE de área de trabalho e usá-lo para acessar o cluster por meio do uso de um contexto de computação do Spark ou do MapReduce remoto. As opções incluem a Microsoft RTVS ([Ferramentas do R para Visual Studio](https://www.visualstudio.com/features/rtvs-vs.aspx)), RStudio e o [StatET](http://www.walware.de/goto/statet) baseado em Eclipse da Walware.

Além disso, você pode acessar o console R no nó de borda digitando **R** no prompt de comandos do Linux depois de conectar via SSH ou PuTTY. Ao usar a interface do console, é prático executar um editor de texto para o desenvolvimento de scripts R em outra janela e recortar e colar seções do seu script no console R, conforme necessário.

## <a name="pricing"></a>Preços

Os preços associados a um cluster do HDInsight do ML Services são estruturados de forma semelhante aos preços de outros tipos de cluster do HDInsight. Elas são baseadas no dimensionamento das VMs subjacentes por nome, data e nós de borda com adição de uma elevação de hora por núcleo. Para obter mais informações, consulte [Preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar os Serviços ML em clusters HDInsight, consulte os seguintes tópicos:

* [Executar um script de R em um cluster de serviços de ML no Azure HDInsight do usando o RStudio Server](machine-learning-services-quickstart-job-rstudio.md)
* [Opções de contexto de computação para cluster do ML Services no HDInsight](r-server-compute-contexts.md)
* [ Opções de armazenamento para o cluster dos Serviços ML no HDInsight ](r-server-storage.md)
