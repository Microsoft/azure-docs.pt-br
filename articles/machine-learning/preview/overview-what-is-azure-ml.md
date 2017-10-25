---
title: "O que é o Azure Machine Learning? | Microsoft Docs"
description: "Visão geral do Gerenciamento de Modelos e Experimentação do Azure Machine Learning, uma solução integrada de ciência de dados de ponta a ponta para cientistas profissionais desenvolverem, experimentarem e implantarem aplicativos de análise avançada em escala de nuvem."
services: machine-learning
author: haining
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: get-started-article
ms.date: 09/21/2017
ms.openlocfilehash: 3bf9227a7ee432f036c57dd9d1c3807c7a867f3f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-machine-learning"></a>O que é o Azure Machine Learning?

O Azure Machine Learning é uma solução integrada de análise avançada e de ciência de dados de ponta a ponta. Ele permite que os cientistas de dados preparem dados, desenvolvam experimentos e implantem modelos em escala de nuvem.

Os principais componentes do Azure Machine Learning são:
- Azure Machine Learning Workbench
- Serviço de Experimentação do Azure Machine Learning
- Serviço de Gerenciamento de Modelos do Azure Machine Learning
- Bibliotecas do Microsoft Machine Learning para o Apache Spark (Biblioteca MMLSpark)
- Ferramentas do Visual Studio Code para IA

Juntos, esses aplicativos e serviços ajudam a acelerar significativamente o desenvolvimento e a implantação de projetos de ciência de dados. 

![Conceitos de Azure Machine Learning](media/overview-what-is-azure-ml/aml-concepts.png)

## <a name="open-source-compatible"></a>Compatível com software livre

O Azure Machine Learning dá suporte a tecnologias de software livre. Você pode usar dezenas de milhares de pacotes do Python de software livre, como as seguintes estruturas de aprendizado de máquina:

- [scikit-learn](http://scikit-learn.org/)
- [TensorFlow](https://www.tensorflow.org/)
- [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)
- [ML do Spark](https://spark.apache.org/docs/2.1.1/ml-pipeline.html)

Você pode executar seus experimentos em ambientes gerenciados como contêineres do Docker e clusters do Spark. Você também pode usar hardware avançado como [máquinas virtuais habilitadas para GPU no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-gpu) para acelerar a execução.

O Azure Machine Learning é criado sobre as seguintes tecnologias de software livre:

- [Jupyter Notebook](http://jupyter.org/)
- [Apache Spark](https://spark.apache.org/)
- [Docker](https://www.docker.com/)
- [Kubernetes](https://kubernetes.io/)
- [Python](https://www.python.org/)
- [Conda](https://conda.io/docs/)

Ele também inclui tecnologias de software livre da Microsoft, como a [Biblioteca de Machine Learning da Microsoft para o Apache Spark](https://github.com/Azure/mmlspark) e o Kit de Ferramentas Cognitivas.

Além disso, você também se beneficiar de algumas das tecnologias de aprendizado de máquina mais avançadas, testadas e aprovadas desenvolvidas pela Microsoft para resolver problemas em larga escala. Elas são testadas em vários produtos da Microsoft, tais como:

- Windows
- Bing
- Xbox
- Office
- SQL Server

A seguir estão algumas das tecnologias de aprendizado de máquina da Microsoft incluídas com o Azure Machine Learning:

- [PROSE](https://microsoft.github.io/prose/) (Program Synthesis using Examples)
- [microsoftml](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)
- [revoscalepy](https://docs.microsoft.com/r-server/python-reference/revoscalepy/revoscalepy-package)

## <a name="azure-machine-learning-workbench"></a>Azure Machine Learning Workbench
O Azure Machine Learning Workbench é um aplicativo da área de trabalho mais ferramentas de linha de comando, com suporte no Windows e no macOS. Ele permite que você gerencie soluções de aprendizado de máquina por todo o ciclo de vida de ciência de dados:

- Preparação e ingestão de dados
- Modelo de desenvolvimento e gerenciamento de experimentos
- Implantação de modelos em ambientes de destino diversos

Aqui estão as funcionalidades principais oferecidas pelo Azure Machine Learning Workbench:

- Ferramenta de preparação de dados que pode aprender a lógica de transformação de dados por exemplo.
- Abstração de fonte de dados acessível por meio de código Python e UX.
- SDK do Python para invocar pacotes de preparação de dados construídos visualmente.
- Serviço de Bloco de anotações do Jupyter interno e UX cliente.
- Monitoramento e gerenciamento de experimentos por meio de exibições de histórico de execuções.
- Controle de acesso baseado em função que permite o compartilhamento e colaboração por meio do Azure Active Directory.
- Instantâneos de projeto automáticos para cada execução e controle de versão explícito habilitado pela integração nativa do Git. 
- Integração com IDEs populares do Python.

Para obter mais informações, faça referência aos seguintes artigos:
- [Guia do Usuário de Preparação de Dados](data-prep-user-guide.md)
- [Usando o Git com o Azure Machine Learning](using-git-ml-project.md)
- [Usando o Bloco de anotações do Jupyter no Azure Machine Learning](how-to-use-jupyter-notebooks.md)
- Roaming e Compartilhamento
- [Lista do Histórico de Execuções](how-to-use-run-history-model-metrics.md)
- [Integração de IDE](how-to-configure-your-IDE.md)

## <a name="azure-machine-learning-experimentation-service"></a>Serviço de Experimentação do Azure Machine Learning
O Serviço de Experimentação lida com a execução de experimentos de aprendizado de máquina. Ele também dá suporte ao Workbench, fornecendo gerenciamento de projetos, integração de Git, controle de acesso, roaming e compartilhamento. 

Por meio da configuração fácil, você pode executar suas experiências em uma variedade de opções de ambiente de computação:

- Local nativo
- Contêiner do Docker local
- Contêiner do Docker em uma VM remota
- Expandir o cluster Spark no Azure

O serviço de experimentação constrói ambientes virtuais para garantir que o script possa ser executado em isolamento com resultados reproduzíveis. Ele registra as informações de histórico de execuções e apresenta o histórico para você visualmente. Você pode selecionar facilmente o melhor modelo dentre as suas execuções de experimento. 

Para obter mais informações, consulte [Configuração de Execução de Experimentação](experiment-execution-configuration.md).

## <a name="azure-machine-learning-model-management-service"></a>Serviço de Gerenciamento de Modelos do Azure Machine Learning

O Serviço de Gerenciamento de Modelos permite que cientistas de dados e equipes de operações de desenvolvimento implantem modelos preditivos em uma ampla variedade de ambientes. Versões de modelo e linhagem são acompanhados desde as execuções de treinamento até as implantações. Os modelos são armazenados, registrados e gerenciados na nuvem. 

Usando comandos CLI simples, você pode dividir seu modelo, seus scripts de pontuação e suas dependências em contêineres de imagens do Docker. Essas imagens são registradas no seu próprio registro do Docker hospedado no Azure (Registro de Contêiner do Azure). Eles podem ser implantados com segurança para os destinos a seguir:

- Computadores locais
- Servidores locais
- A nuvem
- Dispositivos de borda IoT

O Kubernetes em execução no ACS (Serviço de Contêiner do Azure) é usado para a implantação de expansão de nuvem. A telemetria de execução do modelo é capturada no AppInsights para análise visual. 

Para obter mais informações sobre o Serviço de Gerenciamento de Modelos, consulte [Visão geral do Gerenciamento de Modelos](model-management-overview.md)


## <a name="microsoft-machine-learning-library-for-apache-spark"></a>Biblioteca do Microsoft Machine Learning para o Apache Spark

O [MMLSpark](https://github.com/Azure/mmlspark) (Biblioteca do Microsoft Machine Learning para o Apache Spark) é um pacote de Spark de software livre que fornece ferramentas de aprendizagem profunda e de ciência de dados para o Apache Spark. Ele integra [Pipelines de Machine Learning do Spark](https://spark.apache.org/docs/2.1.1/ml-pipeline.html) com o [Kit de Ferramentas Cognitivas da Microsoft](https://www.microsoft.com/en-us/cognitive-toolkit/) e a biblioteca [OpenCV](http://opencv.org/). Ele permite que você crie com rapidez modelos analíticos e preditivos altamente escalonáveis e eficientes para grandes conjuntos de dados de texto e de imagens. Alguns dos destaques incluem:

- Ingerir facilmente imagens do HDFS no DataFrame do Spark
- Pré-processar dados de imagem usando transformações do OpenCV
- Personalizar imagens usando redes neurais profundas previamente treinadas usando o Kit de Ferramentas Cognitivas da Microsoft 
- Usar LSTMs bidirecionais previamente treinados do Keras para extração de entidade médica
- Treinar modelos de classificação de imagem baseados em DNN em VMs de GPU série N no Azure
- Personalizar dados de texto de forma livre usando APIs convenientes sobre primitivos em SparkML via um único transformador
- Treinar modelos de classificação e regressão facilmente por meio de personalização implícita de dados
- Computar um conjunto rico de métricas de avaliação, incluindo métricas por instância

Para obter mais informações, consulte [Usando MMLSpark no Azure Machine Learning](how-to-use-mmlspark.md).

## <a name="visual-studio-code-tools-for-ai"></a>Ferramentas do Visual Studio Code para IA
Ferramentas do Visual Studio para IA é uma extensão no Visual Studio Code para criar, testar e implantar soluções de IA e de aprendizagem profunda. Ele apresenta muitos pontos de integração com o Azure Machine Learning, incluindo:
- Uma exibição de histórico de execução mostrando o desempenho das execuções de treinamento e métricas registradas.
- Um modo de exibição de galeria permitindo aos usuários procurar e inicializar novos projetos com o Kit de Ferramentas Cognitivas da Microsoft, o TensorFlow e muitas outras estruturas de aprendizagem profunda. 
- Uma exibição do gerenciador para seleção de destinos de computação para seus scripts executarem.
 

## <a name="what-are-the-machine-learning-options-from-microsoft"></a>Quais são as opções de aprendizado de máquina da Microsoft?
Além do Azure Machine Learning, há uma ampla variedade de opções no Azure para criar, implantar e gerenciar modelos de aprendizado de máquina. 
* Serviços de Machine Learning da Microsoft no SQL Server
* Microsoft Machine Learning Server
* Máquina Virtual de Ciência de Dados
* MLLib Spark no HDInsight
* Serviço de Treinamento de IA do Lote
* Kit de Ferramentas Cognitivas da Microsoft
* Serviços Cognitivos da Microsoft


### <a name="microsoft-machine-learning-services-in-sql-server"></a>Serviços de Machine Learning da Microsoft no SQL Server
[Os Serviços de Machine Learning da Microsoft](https://docs.microsoft.com/sql/advanced-analytics/r/r-services) permitem que você execute, treine e implante modelos de aprendizado de máquina usando o R ou Python. Você pode usar dados situados localmente e em Bancos de Dados do SQL Server. 

Use os Serviços de Machine Learning da Microsoft quando você precisar treinar ou implantar modelos localmente ou dentro do Microsoft SQL Server. Modelos criados com os Serviços de Machine Learning podem ser implantados usando o Gerenciamento de Modelos do Azure Machine Learning. 

### <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server 
[O Microsoft Machine Learning Server](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone) é um servidor empresarial para hospedagem e gerenciamento de cargas de trabalho paralelas e distribuídas de processos do R e do Python. O Microsoft Machine Learning Server é executado no Windows, Linux, Hadoop e Apache Spark. Ele também está disponível em [HDInsight](https://azure.microsoft.com/services/hdinsight/r-server/). Ele fornece um mecanismo de execução para soluções criadas usando [pacotes do Microsoft Machine Learning](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package) e estende o R e o Python de software livre com suporte para os seguintes cenários:

- análise de alto desempenho
- análise estatística
- aprendizado de máquina
- conjuntos de dados realmente grandes

A funcionalidade de valor agregado é fornecida por meio de pacotes de proprietários que são instalados com o servidor. Para desenvolvimento, você pode usar IDEs como [Ferramentas do R para Visual Studio](https://www.visualstudio.com/vs/rtvs/) e [Ferramentas Python para Visual Studio](https://www.visualstudio.com/vs/python/).

Use o Microsoft Machine Learning Server quando você precisar:

- Compilar e implantar modelos criados com R e Python em um servidor
- Distribuir o treinamento de R e Python em grande escala em um cluster Spark ou Hadoop

### <a name="data-science-virtual-machine"></a>Máquina Virtual de Ciência de Dados
A [DSVM (Máquina Virtual de Ciência de Dados)](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview) é uma imagem de VM personalizada na nuvem do Microsoft Azure especificamente criada para ciência de dados. Ela tem muitas ferramentas conhecidas de ciência de dados, entre outras, pré-instaladas e pré-configuradas que ajudam a começar a criar rapidamente aplicativos inteligentes para análise avançada. Ela está disponível no Windows Server e no Linux. Oferecemos a edição do Windows do DSVM no Server 2016 e no Server 2012. Oferecemos uma edição Linux do DSVM nas distribuições Ubuntu 16.04 LTS e OpenLogic 7.2 CentOS baseadas em Linux. 

Use a Máquina Virtual de Ciência de Dados quando você precisar executar ou hospedar seus trabalhos em um único nó. Ou então, se você precisar expandir remotamente o processamento em um único computador. A Máquina Virtual de Ciência de Dados tem suporte como um destino para Experimentação do Azure Machine Learning e Gerenciamento de Modelos do Azure Machine Learning. 

### <a name="spark-mllib-in-hdinsight"></a>MLLib Spark no HDInsight
O [MLLib Spark no HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-ipython-notebook-machine-learning) permite que você crie modelos como parte de trabalhos Spark que estão em execução em Big Data. O Spark permite que você transforme e prepare dados com facilidade e, em seguida, expanda a criação de modelos em um único trabalho. Modelos criados por meio do MLLib Spark podem ser implantados, gerenciados e monitorados por meio do Gerenciamento de Modelos do Azure Machine Learning. Execuções de treinamento podem ser distribuídas e gerenciadas com a Experimentação do Azure Machine Learning. O Spark também pode ser usado para expandir trabalhos de preparação de dados criados no Machine Learning Workbench. 

Use o Spark quando você precisar expandir o processamento de dados e criar modelos como parte de um pipeline de dados. Você pode criar trabalhos Spark no Scala, Java, Python ou R. 

### <a name="batch-ai-training"></a>Treinamento de IA do Lote 
O [Treinamento de IA do Lote do Azure](https://aka.ms/batchaitraining) ajuda a fazer experiências em paralelo com seus modelos de IA usando qualquer estrutura, então treina-os em grande escala em GPUs clusterizadas. Descreva sua configuração e requisitos de trabalho para a execução e nós cuidaremos do restante. 

O Treinamento de IA do Lote permite que você expanda trabalhos de aprendizagem profunda entre GPUs clusterizadas usando estruturas como:

- Kit de Ferramentas Cognitivas
- Caffe
- Encadeador
- TensorFlow

O Gerenciamento de Modelos do Azure Machine Learning pode ser usado para implantar, gerenciar e monitorar modelos tomados do Treinamento de IA do Lote.  O Treinamento de IA do Lote será integrado à Experimentação do Azure Machine Learning no futuro. 

### <a name="microsoft-cognitive-toolkit"></a>Kit de Ferramentas Cognitivas da Microsoft
O [Kit de Ferramentas Cognitivas da Microsoft](https://www.microsoft.com/en-us/cognitive-toolkit/) é um kit de ferramentas de aprendizagem profunda unificado que descreve as redes neurais como etapas computacionais em um gráfico direcionado. Nesse gráfico direcionado, nós de folha representam valores de entrada ou parâmetros de rede, enquanto outros nós representam as operações de matriz sobre as respectivas entradas. O Kit de Ferramentas Cognitivas permite facilmente perceber e combinar tipos de modelo populares como DNNs de encaminhamento do feed, CNNs (redes convolucionais) e RNNs/LSTMs (redes recorrentes). Ele implementa o SGD (gradiente descendente estocástico, erro backpropagation) com diferenciação automática e paralelização entre vários GPUs e servidores.

Use o Kit de Ferramentas Cognitivas quando você desejar criar um modelo usando aprendizagem profunda.  O Kit de Ferramentas Cognitivas pode ser usado em qualquer um dos serviços acima.

### <a name="microsoft-cognitive-services"></a>Serviços Cognitivos da Microsoft
Os Serviços Cognitivos da Microsoft são um conjunto de 30 APIs que permitem que você crie aplicativos que usam métodos naturais de comunicação. Essas APIs permitem que seus aplicativos vejam, ouçam, falem, entendam e interpretem nossas necessidades com apenas algumas linhas de código. Adicione facilmente recursos inteligentes para seus aplicativos, tais como: 

- Detecção de emoção e sentimento
- Visão e reconhecimento de fala
- Reconhecimento vocal
- Conhecimento e pesquisa

Os Serviços Cognitivos da Microsoft podem ser usados para desenvolver aplicativos entre dispositivos e plataformas. As APIs estão em constante aperfeiçoamento e são fáceis de configurar. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="next-steps"></a>Próximas etapas
* [Instalar e criar o Azure Machine Learning](quickstart-installation.md)
