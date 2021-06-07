---
title: Introdução aos Serviços ML no Azure HDInsight
description: Aprenda a usar os Serviços ML no HDInsight para criar aplicativos para análise de big data.
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 87f4181e820b1c6ecdeb0fda85a88e80db248dd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943920"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>O que são os Serviços de ML no Azure HDInsight

O Microsoft Machine Learning Server está disponível como uma opção de implantação quando você cria clusters HDInsight no Azure. O tipo de cluster que fornece essa opção é chamado **Serviços ML**. Essa funcionalidade fornece acesso sob demanda a métodos adaptáveis e distribuídos de análise no HDInsight.

Os Serviços de ML no HDInsight fornecem as funcionalidades mais recentes para análise de R em conjuntos de dados de praticamente qualquer tamanho. Os conjuntos de valores podem ser carregados no Armazenamento de Blobs do Azure ou no Data Lake Storage. Seus aplicativos baseados em R podem usar os mais de 8 mil pacotes R de software livre. As rotinas do ScaleR, o pacote de análise de Big Data da Microsoft, também estão disponíveis.

O nó de borda fornece um local conveniente para se conectar ao cluster e executar os scripts R. O nó de borda permite a execução das funções distribuídas em paralelo do ScaleR nos núcleos do servidor. Você também pode executá-las em todos os nós do cluster usando o Hadoop Map Reduce do ScaleR. Você também pode usar os contextos de computação do Apache Spark.

Os modelos ou previsões que resultam de análises podem ser baixados para uso local. Eles também podem ser `operationalized` em outro lugar no Azure. Em particular, por meio de [Azure Machine Learning Studio (clássico)](https://studio.azureml.net)e do [serviço Web](../../machine-learning/classic/deploy-a-machine-learning-web-service.md).

## <a name="get-started-with-ml-services-on-hdinsight"></a>Introdução ML Services no HDInsight

Para criar um cluster dos Serviços de ML no HDInsight, selecione o tipo de cluster do **ML Services**. O tipo de cluster dos Serviços de ML inclui o ML Server nos nós de dados e no nó de borda. O nó de borda funciona como uma zona de destino para análise baseada nos Serviços de ML. Consulte [Criar clusters do Apache Hadoop usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) para ver um passo a passo sobre como criar o cluster.

## <a name="why-choose-ml-services-in-hdinsight"></a>Por que escolher os Serviços ML no HDInsight?

Os Serviços ML no HDInsight fornecem os seguintes benefícios:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>Inovação AI da Microsoft e open-source

  Os Serviços de ML incluem um conjunto distribuído altamente adaptável de algoritmos, como [RevoscaleR](/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](/machine-learning-server/python-reference/revoscalepy/revoscalepy-package) e [microsoftML](/machine-learning-server/python-reference/microsoftml/microsoftml-package). Esses algoritmos podem funcionar em tamanhos de dados maiores que o tamanho da memória física. Eles também são executados em uma ampla variedade de plataformas de maneira distribuída. Saiba mais sobre a coleção de [pacotes R](/machine-learning-server/r-reference/introducing-r-server-r-package-reference) e [pacotes Python](/machine-learning-server/python-reference/introducing-python-package-reference) personalizada da Microsoft incluída no produto.
  
  Os Serviços de ML unem essas inovações e contribuições da Microsoft provenientes da comunidade de software livre (kits de ferramentas do R, do Python e de IA). Tudo isso com base em uma plataforma única de nível empresarial. Qualquer pacote de aprendizado de máquina de código aberto R ou Python pode trabalhar lado a lado com qualquer inovação proprietária da Microsoft.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Operação e administração simples, segura e em grande escala

  As empresas que dependem de paradigmas e ambientes tradicionais investem muito tempo e esforço em direção à operacionalização. Essa ação resulta em custos e atrasos inflacionados, incluindo o tempo de conversão de: modelos, iterações para mantê-los válidos e atualizados, aprovação regulamentar e gerenciamento de permissões.

  Os Serviços de ML oferecem [operacionalização](/machine-learning-server/what-is-operationalization) de nível empresarial. Após a conclusão de um modelo de machine learning, serão necessários apenas alguns cliques para gerar APIs de serviços Web. Esses [serviços da Web](/machine-learning-server/operationalize/concept-what-are-web-services) são hospedados em uma grade de servidores na nuvem e podem ser integrados a aplicativos de linha de negócios. A capacidade de implantar um permite a grade Elástico você dimensionar perfeitamente com as necessidades dos negócios, tanto para o lote e pontuação em tempo real. Para obter instruções, consulte [Operar os Serviços ML no HDInsight](r-server-operationalize.md).

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

> [!NOTE]  
> Há suporte para o tipo de cluster dos Serviços de ML no HDInsight apenas no HDInsight 3.6. O HDInsight 3.6 está agendado para ser desativado em 31 de dezembro de 2020.

## <a name="key-features-of-ml-services-on-hdinsight"></a>Principais recursos dos Serviços ML no HDInsight

Os seguintes recursos estão incluídos nos Serviços ML no HDInsight.

| Categoria da funcionalidade | Descrição |
|------------------|-------------|
| R habilitado | [Pacotes R](/machine-learning-server/r-reference/introducing-r-server-r-package-reference) para soluções escritas em R, com uma distribuição de software livre de R e infraestrutura em tempo de execução para execução de scripts. |
| Python habilitado | [Módulos do Python](/machine-learning-server/python-reference/introducing-python-package-reference) para soluções escritas em Python, com uma distribuição de software livre do Python e infraestrutura em tempo de execução para execução de scripts.
| [Modelos previamente treinados](/machine-learning-server/install/microsoftml-install-pretrained-models) | Para análise visual e análise de sentimento de texto, pronto para classificar os dados que você fornecer. |
| [Implantar e consumir modelos](r-server-operationalize.md) | `Operationalize` seu servidor e implante soluções como um serviço Web. |
| [Execução Remota](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Inicie sessões remotas no cluster ML Services na sua rede a partir da estação de trabalho do seu cliente. |

## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Opções de armazenamento de dados para serviços ML no HDInsight

O armazenamento padrão do sistema de arquivos HDFS pode ser uma conta de Armazenamento do Microsoft Azure ou o Azure Data Lake Storage. Os dados carregados no armazenamento de cluster durante a análise tornam-se persistentes. Os dados estarão disponíveis mesmo depois que o cluster for excluído. Várias ferramentas podem lidar com a transferência de dados para o armazenamento. As ferramentas incluem o recurso de carregamento baseado em portal da conta de armazenamento e o utilitário AzCopy.

Você pode habilitar o acesso aos armazenamentos de Blob e do Data Lake durante a criação. Você não está limitado pela opção de armazenamento primário em uso.  Consulte o artigo [ Opções de armazenamento do Azure para serviços ML no artigo HDInsight ](./r-server-storage.md) para saber mais sobre o uso de várias contas de armazenamento.

Também é possível usar os Arquivos do Azure como uma opção de armazenamento para uso no nó de borda. Os Arquivos do Azure habilitam compartilhamentos de arquivos criados no Armazenamento do Azure para o sistema de arquivos do Linux. Para obter mais informações, confira [Opções de Armazenamento do Microsoft Azure para Serviços de ML no HDInsight](r-server-storage.md).

## <a name="access-ml-services-edge-node"></a>Acesse o nó de borda do ML Services

Você pode se conectar-se ao Microsoft ML Server no nó de borda usando um navegador ou SSH/PuTTY. O console do R é instalado por padrão durante a criação do cluster.  

## <a name="develop-and-run-r-scripts"></a>Desenvolver e executar scripts R

Seus scripts R podem usar qualquer um dos mais de 8 mil pacotes R de software livre. Você também pode usar as rotinas paralelizadas e distribuídas da biblioteca ScaleR. Os scripts executados no nó de borda são executados dentro do interpretador do R nesse nó. Com exceção das etapas que chamam funções ScaleR com um contexto computacional do Map Reduce (RxHadoopMR) ou Spark (RxSpark). As funções são executadas de maneira distribuída nos nós de dados associados aos dados. Para obter mais informações sobre as opções de contexto, confira [Opções de contexto de computação para Serviços de ML no HDInsight](r-server-compute-contexts.md).

## <a name="operationalize-a-model"></a>`Operationalize` um modelo

Quando a modelagem de dados estiver concluída, `operationalize` o modelo para fazer previsões de novos dados no Azure ou no local. Esse processo é conhecido como pontuação. A pontuação pode ser feita em HDInsight, no Azure Machine Learning ou local.

### <a name="score-in-hdinsight"></a>Pontuação no HDInsight

Para pontuar no HDInsight, escreva uma função do R. A função chama seu modelo para fazer previsões para um novo arquivo de dados que você carregou na conta de armazenamento. Em seguida, salve as previsões na conta de armazenamento. Você pode executar a rotina sob demanda no nó de borda do seu cluster ou usando um trabalho agendado.

### <a name="score-in-azure-machine-learning-aml"></a>Pontuação no AML (Azure Machine Learning)

Para pontuar usando um Azure Machine Learning, use o pacote R do Azure Machine Learning de software livre conhecido como [AzureML](https://cran.r-project.org/src/contrib/Archive/AzureML/) para publicar seu modelo como um serviço Web do Azure. Para sua conveniência, este pacote é pré-instalado no nó de borda. Em seguida, use os recursos no Azure Machine Learning para criar uma interface de usuário para o serviço Web e chame o serviço Web conforme necessidade para pontuação. Em seguida, converta objetos de modelo do ScaleR em objetos de modelo de software livre equivalentes para uso com o serviço Web. Use as funções de coerção do ScaleR, tais como `as.randomForest()` para modelos baseados em combinação, para essa conversão.

### <a name="score-on-premises"></a>Pontuação local

Para pontuar no local após criar o modelo: serialize o modelo em R, baixe-o, desserialize-o e use-o para pontuar novos dados. Você pode marcar novos dados usando a abordagem descrita anteriormente em Pontuação no HDInsight ou usando [serviços da Web](/machine-learning-server/operationalize/concept-what-are-web-services).

## <a name="maintain-the-cluster"></a>Manter o cluster

### <a name="install-and-maintain-r-packages"></a>Instalar e manter pacotes R

A maioria dos pacotes R que você usa será exigida no nó de borda, uma vez que a maioria das etapas dos scripts R será executada lá. Para instalar pacotes R adicionais no nó de borda, você pode usar o método `install.packages()` em R.

Se você estiver apenas usando rotinas de biblioteca ScaleR, você normalmente não precisará de pacotes R adicionais. Talvez você precise de pacotes adicionais para a execução de **rxExec** ou **RxDataStep** nos nós de dados.

Os pacotes adicionais podem ser instalados com uma ação de script após a criação do cluster. Para obter mais informações, consulte [ Gerenciar serviços ML no cluster do HDInsight ](r-server-hdinsight-manage.md).

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Alterar as configurações de memória do Apache Hadoop MapReduce

A memória disponível para os Serviços de ML pode ser modificada quando está executando um trabalho MapReduce. Para modificar um cluster, use a interface do usuário do Apache Ambari para o cluster. Para obter instruções sobre a interface do usuário do Ambari, confira [Gerenciar clusters HDInsight usando a interface do usuário do Ambari Web](../hdinsight-hadoop-manage-ambari.md).

A memória disponível para os Serviços de ML pode ser alterada usando os comutadores do Hadoop na chamada para **RxHadoopMR**:

```r
hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"
```

### <a name="scale-your-cluster"></a>Ajustar escala do cluster

Um cluster de serviços ML existente no HDInsight pode ser ampliado ou reduzido pelo portal. Escalando verticalmente, você obtém capacidade adicional para tarefas de processamento maiores. Você pode dimensionar de volta um cluster quando ele está ocioso. Para obter instruções sobre como escalar um cluster, confira [Gerenciar clusters HDInsight](../hdinsight-administer-use-portal-linux.md).

### <a name="maintain-the-system"></a>Manter o sistema

A manutenção do SO é executada nas VMs Linux subjacentes em um cluster HDInsight fora do horário comercial. Normalmente, a manutenção é feita às 3h30 (hora local da VM) todas as segundas e quintas-feiras. As atualizações não afetam mais do que um quarto do cluster por vez.

Os trabalhos em execução podem ficar lentos durante a manutenção. No entanto, todos eles devem continuar em execução até a conclusão. Todos os dados locais ou de software personalizados que você tem são preservados nesses eventos de manutenção, a menos que ocorra uma falha catastrófica que exija a recompilação do cluster.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>Opções de IDE para serviços ML no HDInsight

O nó de borda do Linux de um cluster HDInsight é a zona de aterrissagem para análises baseadas em R. Versões recentes do HDInsight fornecem um IDE do RStudio Server baseado em navegador no nó de borda. O RStudio Server é mais produtivo do que o console do R para desenvolvimento e execução.

Um IDE de desktop pode acessar o cluster por meio de um contexto de computação do MapReduce ou Spark remoto. As opções incluem: [RTVS](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (Ferramentas do R para Visual Studio) da Microsoft, o RStudio e o StatET de malware baseado no Eclipse.

Acesse o console do R no nó de borda digitando **R** no prompt de comando. Ao usar a interface do console, é conveniente desenvolver o script R em um editor de texto. Em seguida, recorte e cole as seções do seu script no console do R, conforme necessário.

## <a name="pricing"></a>Preços

Os preços associados a um cluster HDInsight dos Serviços de ML são estruturados de maneira semelhante a outros tipos de cluster HDInsight. Eles são baseados no dimensionamento das VMs subjacentes por nome, data e nós de borda. As elevações de hora por núcleo também. Para obter mais informações, consulte [Preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar os Serviços de ML em clusters HDInsight, confira os seguintes artigos:

* [Executar um script de R em um cluster de serviços de ML no Azure HDInsight do usando o RStudio Server](machine-learning-services-quickstart-job-rstudio.md)
* [Opções de contexto de computação para cluster do ML Services no HDInsight](r-server-compute-contexts.md)
* [ Opções de armazenamento para o cluster dos Serviços ML no HDInsight ](r-server-storage.md)