---
title: "Introdução à Máquina Virtual de Ciência de Dados do Azure para Linux e Windows | Microsoft Docs"
description: "Principais cenários de análise e componentes para Máquinas Virtuais de Ciência de Dados do Windows e Linux."
keywords: "ferramentas de ciência de dados, máquina virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: 8398af10d4826373e16420be3309c6ae8246ca4b
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-data-science-virtual-machine-for-linux-and-windows"></a>Introdução à Máquina Virtual de Ciência de Dados do Azure para Linux e Windows

A DSVM (Máquina Virtual de Ciência de Dados) é uma imagem de VM personalizada na nuvem do Microsoft Azure especificamente criada para ciência de dados. Ela tem muitas ferramentas conhecidas de ciência de dados, entre outras, pré-instaladas e pré-configuradas que ajudam a começar a criar rapidamente aplicativos inteligentes para análise avançada. Ela está disponível no Windows Server e no Linux. Oferecemos a edição do Windows do DSVM no Server 2016 e no Server 2012. Oferecemos uma edição Linux do DSVM nas distribuições Ubuntu 16.04 LTS e OpenLogic 7.2 CentOS baseadas em Linux. 

Este tópico explica o que você pode fazer com a VM de Ciência de Dados, descreve alguns dos principais cenários de uso da VM, lista os principais recursos nas versões Windows e Linux, além de fornecer instruções sobre como começar a usá-los.


## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>O que é possível fazer com a Máquina Virtual de Ciência de Dados?
O objetivo da Máquina Virtual de Ciência de Dados é fornecer aos profissionais de dados em todos os níveis de habilidade e funções um ambiente descomplicado de ciência de dados. Com essa VM, você economiza tempo considerável que gastaria se tivesse distribuído um ambiente comparável por conta própria. Em vez disso, inicie seu projeto de ciência de dados imediatamente em uma instância de VM recentemente criada. 

A VM de Ciência de Dados foi desenvolvida e configurada para trabalhar com amplos cenários de uso. Você pode reduzir ou aumentar o ambiente de acordo com as necessidades do seu projeto. É possível usar seu idioma preferido para programar as tarefas de ciência de dados. Você pode instalar outras ferramentas e personalizar o sistema para suas necessidades exatas.

## <a name="key-scenarios"></a>Principais cenários
Esta seção sugere alguns cenários importantes para os quais a VM de Ciência de Dados pode ser implantada.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Área de trabalho de análise pré-configurada na nuvem
A VM de Ciência de Dados fornece uma configuração de linha de base para equipes de ciência de dados que buscam substituir as respectivas áreas de trabalho locais por uma área de trabalho de nuvem gerenciada. Essa linha de base garante que todos os cientistas de dados em uma equipe tenham uma configuração consistente com a qual podem verificar experiências e promover colaboração. Ela também diminui os custos reduzindo a carga de administração de sistema e poupando o tempo necessário para avaliar, instalar e manter os vários pacotes de software necessários à análise avançada.  

### <a name="data-science-training-and-education"></a>Educação e treinamento de ciência de dados
Os treinadores e educadores corporativos que ensinam sobre ciência de dados geralmente fornecem uma imagem de máquina virtual para garantir que seus alunos tenham uma configuração consistente e que as amostras funcionem de maneira previsível. A VM de Ciência de Dados cria um ambiente sob demanda com uma configuração consistente que facilita o suporte e os desafios de incompatibilidade. Nos casos em que esses ambientes precisam ser criados com frequência, especialmente para aulas rápidas de treinamento, os alunos são consideravelmente beneficiados.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Capacidade elástica sob demanda para projetos em larga escala
As maratonas/competições de ciência de dados ou modelagem e exploração de dados em larga escala exigem que a capacidade de hardware seja escalada horizontalmente, geralmente por um curto período. A VM de Ciência de Dados pode ajudar a replicar o ambiente de ciência de dados rapidamente em servidores escalados horizontalmente sob demanda, o que permite a realização de testes que exigem a execução de recursos de computação altamente potentes.

### <a name="short-term-experimentation-and-evaluation"></a>Avaliação e experimento de curto prazo
A VM de Ciência de Dados pode ser usada para avaliar ferramentas, ou aprender sobre elas, como o Microsoft R Server, SQL Server, Visual Studio, Jupyter, kits de ferramentas de machine learning/aprendizado profundo, além de novas ferramentas conhecidas na comunidade com mínimo esforço de configuração. Uma vez que a VM de Ciência de Dados pode ser configurada rapidamente, ela pode ser aplicada em outros cenários de uso de curto prazo, como na replicação de testes publicados, na execução de demonstrações, depois de passo a passos em sessões online ou em tutoriais de conferência.

### <a name="deep-learning"></a>Aprendizado
A VM de ciência de dados pode ser usada como modelo de treinamento usando algoritmos de aprendizado em hardware de GPU (unidades de processamento gráfico). Utilizando as funcionalidades de colocação em escala de VM da nuvem do Azure, a DSVM ajuda você a usar hardware baseado em GPU na nuvem de acordo com a necessidade. É possível mudar para uma VM baseada em GPU durante o treinamento de modelos grandes ou quando houver necessidade de cálculos em alta velocidade mantendo o mesmo disco do SO.  A edição do Windows Server 2016 do DSVM vem pré-instalada com drivers de GPU, estruturas e versão de GPU dos algoritmos de aprendizagem profunda. No Linux, o aprendizado na GPU é habilitado apenas na [edição de Máquina Virtual de Ciência de Dados para Linux (Ubuntu)](http://aka.ms/dsvm/ubuntu). Você pode implantar a edição do Ubuntu/Windows-2016 da VM de Ciência de Dados em máquinas virtuais do Azure não baseadas em GPU, quando, então, todas as estruturas de aprendizado aprofundado usarão o modo de CPU. Anteriormente, para o Windows Server 2012, publicamos um [Kit de ferramentas de Aprendizado Aprofundado](http://aka.ms/dsvm/deeplearning), mas agora é recomendável usar o Windows Server 2016 para cargas de trabalho de aprendizado aprofundado baseadas em Windows. A edição do Linux baseada em CentOS da DSVM contém apenas os builds de CPU de algumas ferramentas de aprendizado aprofundado (Kit de Ferramentas de Serviços Cognitivos da Microsoft, TensorFlow, MXNet), mas não é fornecida com drivers e estruturas de GPU pré-instalados. 

## <a name="whats-included-in-the-data-science-vm"></a>O que está incluído na VM de Ciência de Dados?
A Máquina Virtual de Ciência de Dados tem muitas ferramentas conhecidas de ciência de dados e de aprendizado já instaladas e configuradas. Ela também inclui ferramentas que facilitam o trabalho com vários produtos de análise e dados do Azure. Você pode explorar e criar modelos preditivos em conjuntos de dados de larga escala usando o Microsoft R Server ou SQL Server 2016. Também está incluído um conjunto de outras ferramentas da comunidade de software livre e da Microsoft, bem como código de exemplo e notebooks. A tabela a seguir relaciona e compara os principais componentes incluídos nas edições do Windows e Linux da Máquina Virtual de Ciência de Dados.


| **Ferramenta**                                                           | **Edição do Windows** | **Edição do Linux** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) com pacotes populares pré-instalados   |S                      | S             |
| O [Microsoft R Server](https://msdn.microsoft.com/microsoft-r/) Developer Edition inclui <br />  Estrutura R de alto desempenho, distribuída e paralela de &nbsp;&nbsp;&nbsp;&nbsp;* [ScaleR](https://msdn.microsoft.com/microsoft-r/scaler-getting-started)<br />  &nbsp;&nbsp;&nbsp;&nbsp;* [MicrosoftML](https://msdn.microsoft.com/microsoft-r/microsoftml-introduction) – Novos algoritmos de AM de última geração da Microsoft <br />  &nbsp;&nbsp;&nbsp;&nbsp;* [Operacionalização de R](https://msdn.microsoft.com/microsoft-r/operationalize/about)                                            |S                      | S <br/> (MicrosoftML ainda não disponível)|
| [Microsoft Office](https://products.office.com/en-us/business/office-365-proplus-business-software) Pro Plus com ativação compartilhada – Excel, Word e PowerPoint   |S                      |N              |
| [Anaconda Python](https://www.continuum.io/) 2.7, 3.5 com pacotes populares pré-instalados    |S                      |S              |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) com pacotes populares para linguagem Julia pré-instalados                         |S                      |S              |
| Bancos de dados relacionais                                                            | [SQL Server 2016 SP1](https://www.microsoft.com/sql-server/sql-server-2016) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/)(somente CentOS) |
| Ferramentas de Banco de Dados                                                       | * SQL Server Management Studio <br/>* SQL Server Integration Services<br/>* [bcp, sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br /> Drivers * ODBC/JDBC| * [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (ferramenta de consultas), <br /> * bcp, sqlcmd <br /> Drivers * ODBC/JDBC|
| Análise no banco de dados escalonável com o SQL Server R Services | S     |N              |
| **[Jupyter Notebook Server](http://jupyter.org/) com os kernels a seguir,**                                  | S     | S |
|     &nbsp;&nbsp;&nbsp;&nbsp;* R | S | S |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Python 2.7 e 3.5 | S | S |
|     &nbsp;&nbsp;&nbsp;&nbsp;* Julia | S | S |
|     &nbsp;&nbsp;&nbsp;&nbsp;* PySpark | N | S |
|     &nbsp;&nbsp;&nbsp;&nbsp;* [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | N | Y (somente Ubuntu) |
|     &nbsp;&nbsp;&nbsp;&nbsp;* SparkR     | N | S |
| JupyterHub (Servidor com vários notebooks)| N | S |
| **Ferramentas de desenvolvimento, IDEs e editores de código**| | |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio 2017 (Community Edition)](https://www.visualstudio.com/community/) > com Git Plugin, Azure HDInsight (Hadoop), Data Lake, SQL Server Data Tools, [Node.js](https://github.com/Microsoft/nodejstools), [Python](http://aka.ms/ptvs) e [RTVS (Ferramentas do R para Visual Studio)](http://microsoft.github.io/RTVS-docs/) | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Visual Studio Code](https://code.visualstudio.com/) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [PyCharm](https://www.jetbrains.com/pycharm/) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Atom](https://atom.io/) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Juno (IDE de Julia)](http://junolab.org/)| S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* Vim e Emacs | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* Git e GitBash | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* OpenJDK | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* .Net Framework | S | N |
| PowerBI Desktop | S | N |
| SDKs para acessar o Azure e o pacote de serviços Cortana Intelligence | S | S |
| **Ferramentas de movimentação de dados e gerenciamento** | | |
| &nbsp;&nbsp;&nbsp;&nbsp;* Gerenciador de Armazenamento do Azure | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CLI do Azure](https://docs.microsoft.com/cli/azure/overview) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* Azure PowerShell | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Adlcopy(Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Ferramenta de Migração de Dados do DocDB](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Gateway de Gerenciamento de Dados da Microsoft](https://msdn.microsoft.com/library/dn879362.aspx): mover dados entre OnPrem e a nuvem | S | N |
| &nbsp;&nbsp;&nbsp;&nbsp;* Utilitários de linha de comando Unix/Linux | S | S |
| [Apache Drill](http://drill.apache.org) para Exploração de dados | S | S |
| **Machine Learning Tools** |||
| &nbsp;&nbsp;&nbsp;&nbsp;* Integração com [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (R, Python) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Xgboost](https://github.com/dmlc/xgboost) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Weka](http://www.cs.waikato.ac.nz/ml/weka/) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Rattle](http://rattle.togaware.com/) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [LightGBM](https://github.com/Microsoft/LightGBM) | N | Y (somente Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp;* [H2O](https://www.h2o.ai/h2o/) | N | Y (somente Ubuntu) |
| **Ferramentas de Aprendizado Aprofundado baseadas em GPU** |Edição do Windows Server 2016  |Edição do Ubuntu |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Kit de Ferramentas de Serviços Cognitivos da Microsoft (anteriormente conhecido como CNTK)](https://www.microsoft.com/en-us/cognitive-toolkit/) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [TensorFlow](https://www.tensorflow.org/) | S | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [MXNet](http://mxnet.io/) | S | S|
| &nbsp;&nbsp;&nbsp;&nbsp;* [Caffe e Caffe2](https://github.com/caffe2/caffe2) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Torch](http://torch.ch/) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Theano](https://github.com/Theano/Theano) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Keras](https://keras.io/)| N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [NVidia Digits](https://github.com/NVIDIA/DIGITS) | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [CUDA, CUDNN, Nvidia Driver](https://developer.nvidia.com/cuda-toolkit) | S | S |
| **Plataforma Big Data (somente Devtest)**|||
| &nbsp;&nbsp;&nbsp;&nbsp;* [Spark](http://spark.apache.org/) autônomo local | N | S |
| &nbsp;&nbsp;&nbsp;&nbsp;* [Hadoop](http://hadoop.apache.org/) local (HDFS, YARN) | N | S |



## <a name="get-started-with-the-windows-data-science-vm"></a>Comece a usar a VM de Ciência de Dados do Windows
* Crie uma instância da edição do Windows DSVM desejada navegando para
  * [DSVM baseado em Windows Server 2016](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.windows-data-science-vm)
  
  ou o 
  * [DSVM baseado em Windows Server 2012](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) 
* Clique no botão **OBTER AGORA**.
* Entre na VM da sua área de trabalho remota usando as credenciais que você especificou quando criou a VM.
* Para descobrir e iniciar as ferramentas disponíveis, clique no menu **Iniciar**.

## <a name="get-started-with-the-linux-data-science-vm"></a>Começar a usar a VM de Ciência de Dados do Linux
* Crie uma instância da edição do Linux DSVM desejada navegando para 
  * [DSVM baseado em Ubuntu](http://aka.ms/dsvm/ubuntu)

  ou o

  * [DSVM baseado em OpenLogic CentOS](http://aka.ms/dsvm/centos)

  
* Clique no botão **Obter agora**.
* Entre na VM de um cliente SSH, como Putty ou Comando SSH, usando as credenciais que você especificou quando criou a VM.
* No prompt do shell, insira dsvm-more-info.
* Para obter uma área de trabalho gráfica, baixe [aqui](http://wiki.x2go.org/doku.php/doc:installation:x2goclient) o cliente X2Go para sua plataforma e siga as instruções no documento da VM de Ciência de Dados para Linux [Provisionar a Máquina Virtual de Ciência de Dados Linux](linux-dsvm-intro.md#installing-and-configuring-x2go-client).

## <a name="next-steps"></a>Próximas etapas
### <a name="for-the-windows-data-science-vm"></a>Para a VM de Ciência de Dados do Windows
* Para saber mais sobre como executar ferramentas específicas disponíveis na versão do Windows, confira [Provisionar uma Máquina Virtual de Ciência de Dados da Microsoft](provision-vm.md) e
* Para saber mais sobre como executar várias tarefas necessárias para o seu projeto de ciência de dados na VM do Windows, confira [Dez coisas que você pode fazer na Máquina Virtual de Ciência de Dados](vm-do-ten-things.md).

### <a name="for-the-linux-data-science-vm"></a>Para a VM de Ciência de Dados do Linux
* Para saber mais sobre como executar ferramentas específicas disponíveis na versão do Linux, confira [Provisionar a Máquina Virtual de Ciência de Dados Linux](linux-dsvm-intro.md).
* Para obter um passo a passo que mostre como executar várias tarefas comuns de ciência de dados com o VM Linux, confira [Ciência de dados na Máquina Virtual da Ciência de Dados do Linux](linux-dsvm-walkthrough.md).

