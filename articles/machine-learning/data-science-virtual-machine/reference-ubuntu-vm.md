---
title: 'Referência: Ubuntu Máquina Virtual de Ciência de Dados'
titleSuffix: Azure Data Science Virtual Machine
description: Detalhes sobre as ferramentas incluídas no Ubuntu Máquina Virtual de Ciência de Dados
author: gvashishtha
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: e9a55f72718d6ed5991f3d0f16323409bb0f699f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101661062"
---
# <a name="reference-ubuntu-linux-data-science-virtual-machine"></a>Referência: Ubuntu (Linux) Máquina Virtual de Ciência de Dados

Veja abaixo uma lista de ferramentas disponíveis em sua Máquina Virtual de Ciência de Dados do Ubuntu. 

## <a name="deep-learning-libraries"></a>Bibliotecas de aprendizado profundo

### <a name="cntk"></a>CNTK

O Microsoft Cognitive Toolkit é um kit de ferramentas de aprendizado profundo open-source. As associações do Python estão disponíveis nos ambientes raiz e py35 do Conda. Ele também tem uma ferramenta de linha de comando (CNTK) que já está no caminho.

Os blocos de anotações de amostra de Python estão disponíveis no JupyterHub. Para executar um exemplo básico na linha de comando, execute os comandos a seguir no shell:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Para obter mais informações, consulte a seção CNTK do [GitHub](https://github.com/Microsoft/CNTK) e o [wiki do CNTK](https://github.com/Microsoft/CNTK/wiki).

### <a name="caffe"></a>Caffe

Caffe é uma estrutura de aprendizado aprofundado da Berkeley Vision and Learning Center. Ele está disponível em /opt/caffe. Você pode encontrar exemplos em /opt/caffe/examples.

### <a name="caffe2"></a>Caffe2

Caffe2 é uma estrutura de aprendizado do Facebook que se baseia no Caffe. Ele está disponível no Python 2.7 no ambiente raiz do Conda. Para ativá-lo, execute o seguinte comando no shell:

```bash
source /anaconda/bin/activate root
```

Alguns blocos de anotações de amostra também estão disponíveis no JupyterHub.

### <a name="h2o"></a>H2O

H2O é uma plataforma de análise preditiva e aprendizado de máquina rápido, na memória e distribuído. Um pacote do Python é instalado nos ambientes raiz e py35 do Anaconda. Um pacote R também é instalado. 

Para abrir o H2O na linha de comando, execute `java -jar /dsvm/tools/h2o/current/h2o.jar`. Há várias [opções de linha de comando](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line) que você pode querer configurar. Você pode acessar a interface do usuário Web do Flow navegando até `http://localhost:54321` para começar. Os blocos de anotações de amostra também estão disponíveis no JupyterHub.

### <a name="keras"></a>Keras

O Keras é uma API de rede neural de alto nível em Python. Ele pode ser executado com base no TensorFlow, no Microsoft Cognitive Toolkit ou no Theano. Ele está disponível nos ambientes raiz e py35 do Python.

### <a name="mxnet"></a>MXNet

MXNet é uma estrutura de aprendizado profunda criada para eficiência e flexibilidade. Ela tem associações R e Python incluídas no DSVM. Os blocos de anotações de amostra estão incluído no JupyterHub e o código de exemplo está disponível em /dsvm/samples/mxnet.

### <a name="nvidia-digits"></a>NVIDIA DIGITS

O NVIDIA Deep Learning GPU Training System, conhecido como DIGITS, é um sistema para simplificar tarefas comuns de aprendizado profundo. As tarefas incluem gerenciamento de dados, design e treinamento de redes neurais em sistemas GPU e monitoramento de desempenho em tempo real com visualização avançada.

Os dígitos estão disponíveis como um serviço chamado *dígitos*. Inicie o serviço e navegue até `http://localhost:5000` para começar.

DIGITS também é instalado como um módulo do Python no ambiente raiz Conda.

### <a name="tensorflow"></a>TensorFlow

TensorFlow é a biblioteca de aprendizado aprofundado do Google. É uma biblioteca de software open-source para computação numérica usando grafos de fluxo de dados. O TensorFlow está disponível no ambiente de py35 do Python e alguns blocos de anotações de amostra estão incluídos no JupyterHub.

### <a name="theano"></a>Theano

Theano é uma biblioteca do Python para computação numérica eficiente. Ele está disponível nos ambientes raiz e py35 do Python. 

### <a name="torch"></a>Torch

Tocha é uma estrutura de computação científica com amplo suporte para algoritmos de aprendizado de máquina. Ela está disponível em /dsvm/tools/torch e a **enésima** sessão interativa e o gerenciador de pacotes LuaRocks estão disponíveis na linha de comando. Os exemplos estão disponíveis em /dsvm/samples/torch.

PyTorch também está disponível no ambiente raiz do Anaconda. Os exemplos estão em /dsvm/samples/pytorch.

## <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server

R é uma das linguagens mais populares para análise de dados e aprendizado de máquina. Se deseja usar o R para sua análise, a VM tem o Microsoft Machine Learning Server com o Microsoft R Open e a Math Kernel Library. A Math Kernel Library otimiza as operações matemáticas frequentes em algoritmos analíticos. O Microsoft R Open é 100% compatível com CRAN R e qualquer uma das bibliotecas R publicadas em CRAN pode ser instalada no Microsoft R Open. 

O Machine Learning Server fornece dimensionamento e operacionalização de modelos R em serviços Web. Edite seus programas R em um dos editores padrão como RStudio, vi ou Emacs. Se você preferir usar o editor de Emacs, ele estará pré-instalado. O pacote de Emacs ESS (Emacs Speaks Statistics) simplifica o trabalho com arquivos R no editor Emacs.

Para abrir o console R, insira **R** no shell. Esse comando leva você para um ambiente interativo. Para desenvolver seu programa R, você normalmente usa um editor como vi ou Emacs e, em seguida, executa os scripts no R. Com o RStudio, você tem um IDE gráfico completo para desenvolver o seu programa R.

Também há um script de R para você instalar os [20 melhores pacotes do R](https://www.kdnuggets.com/2015/06/top-20-r-packages.html), caso queira. Você pode executar esse script depois de estar na interface do R Interativo. Conforme mencionado anteriormente, você pode abrir essa interface inserindo **R** no shell.  

## <a name="python"></a>Python

O Anaconda Python é instalado com os ambientes Python 2.7 e 3.5. O ambiente 2.7 é chamado _raiz_ e o ambiente 3.5 é chamado _py35_. Essa distribuição contém o Python base com aproximadamente 300 dos mais populares pacotes de matemática, engenharia e análise de dados.

O ambiente py35 é o padrão. Para ativar o ambiente raiz (2.7), use este comando:

```bash
source activate root
```

Para ativar o ambiente py35 novamente, use este comando:

```bash
source activate py35
```

Para invocar uma sessão interativa do Python, insira **python** no shell. 

Instale outras bibliotecas Python usando Conda ou pip. Para pip, ative o ambiente correto primeiro se você não quiser o padrão:

```bash
source activate root
pip install <package>
```

Ou especifique o caminho completo até o pip:

```bash
/anaconda/bin/pip install <package>
```

Para Conda, você deve sempre especificar o nome do ambiente (py35 ou raiz):

```bash
conda install <package> -n py35
```

Se estiver em uma interface gráfica ou tiver a configuração do encaminhamento X11, você poderá inserir o comando **pycharm** para abrir o IDE do PyCharm Python. Você pode usar os editores de texto padrão. Além disso, você pode usar o Spyder, um IDE do Python que é fornecido com distribuições do Anaconda Python. O Spyder precisa de uma área de trabalho gráfica ou de encaminhamento X11. A área de trabalho gráfica tem um atalho para o Spyder.

## <a name="jupyter-notebook"></a>Notebook Jupyter

A distribuição do Anaconda também acompanha um notebook Jupyter, um ambiente de compartilhamento de código e de análise. O notebook Jupyter é acessado com o JupyterHub. Entre usando seu nome de usuário e senha locais do Linux.

O servidor do notebook Jupyter foi previamente configurado com os kernels do Python 2, do Python 3 e do R. Use o ícone de área de trabalho do **Jupyter Notebook** para abrir o navegador e acessar o servidor de notebook. Se você estiver na VM via cliente SSH ou X2Go, também poderá acessar o servidor do Jupyter Notebook em `https://localhost:8000/`.

> [!NOTE]
> Continue se você obtiver quaisquer avisos de certificado.

Você pode acessar o servidor de bloco de anotações do Jupyter por meio de qualquer host. Insira **https:// \<VM DNS name or IP address\> : 8000/**.

> [!NOTE]
> A porta 8000 é aberta no firewall por padrão quando a VM é provisionada. 

Empacotamos exemplos de notebooks, um em Python em outro em R. Você pode ver o link para os exemplos na home page do notebook após a autenticação no Jupyter Notebook usando a senha e o nome de usuário Linux locais. Você pode criar um novo notebook selecionando **Novo** e selecionando o kernel de linguagem apropriado. Caso não veja o botão **Novo**, selecione o ícone do **Jupyter** na parte superior esquerda para acessar a home page do servidor de notebook.

## <a name="apache-spark-standalone"></a>Apache Spark autônomo

Uma instância autônomo do Apache Spark é pré-instalada na DSVM Linux para ajudar você a desenvolver aplicativos Spark localmente antes de testá-los e implantá-los em clusters grandes. 

Execute programas PySpark através do kernel de Jupyter. Ao abrir o Jupyter, selecione o botão **Novo** e você verá uma lista de kernels disponíveis. O **Spark – Python** é o kernel PySpark que permite a criação de aplicativos Spark usando a linguagem Python. Também é possível usar um IDE Python como PyCharm ou Spyder para criar seu programa em Spark. 

Nessa instância autônoma, a pilha do Spark é executada dentro do programa de chamada cliente. Esse recurso torna mais rápido e fácil solucionar problemas, em comparação com o desenvolvimento em um cluster Spark.

O Jupyter oferece um notebook PySpark de exemplo. Você pode encontrá-lo no diretório SparkML no diretório base do Jupyter ($HOME/notebooks/SparkML/pySpark). 

Se você estiver programando em R para Spark, use o Microsoft Machine Learning Server, SparkR ou sparklyr. 

Antes de executar em um contexto do Spark no Microsoft Machine Learning Server, execute uma etapa de configuração única para habilitar uma instância local de HDFS Hadoop e YARN de nó único. Por padrão, os serviços do Hadoop serão instalados, mas desabilitados no DSVM. Para habilitá-los, execute os seguintes comandos como raiz na primeira vez:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Será possível interromper os serviços relacionados ao Hadoop relacionados quando você não precisar deles executando ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```.

O diretório /dsvm/samples/MRS oferece um exemplo que demonstra como desenvolver e testar o Microsoft Machine Learning Server em um contexto de Spark remoto (a instância de Spark autônoma na DSVM).

## <a name="ides-and-editors"></a>IDEs e editores

Você tem a opção de vários editores de código, incluindo vi/Vim, Emacs, PyCharm, RStudio e IntelliJ. 

PyCharm, RStudio e IntelliJ são editores gráficos. Para usá-los, você precisa estar conectado a uma área de trabalho gráfica. Você os abre usando os atalhos de menu de área de trabalho e de aplicativo.

Vim e Emacs são editores baseados em texto. No Emacs, o pacote complementar do ESS facilita o trabalho com R no editor Emacs. Você pode encontrar mais informações no [site do ESS](https://ess.r-project.org/).

O LaTex é instalado por meio do pacote TeXlive, junto com um pacote de complemento Emacs chamado [AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html). Esse pacote simplifica a criação de seus documentos do LaTex no Emacs.  

## <a name="databases"></a>Bancos de dados

### <a name="graphical-sql-client"></a>Cliente gráfico do SQL

SQuirrel SQL, um cliente gráfico do SQL, pode conectar-se a vários bancos de dados (como o Microsoft SQL Server e MySQL) e executar consultas SQL. É possível executar o SQuirreL SQL em uma sessão de área de trabalho gráfica (usando o cliente X2Go, por exemplo) usando um ícone de área de trabalho. Ou você pode executar o cliente usando o seguinte comando no shell:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Antes do primeiro uso, configure os drivers e aliases de banco de dados. Os drivers JDBC estão localizados em /usr/share/java/jdbcdrivers.

Para obter mais informações, consulte [SQUIRREL SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Ferramentas de linha de comando para acessar o Microsoft SQL Server

O pacote de driver ODBC do SQL Server também vem com duas ferramentas de linha de comando:

- **bcp**: a ferramenta bcp copia dados em massa entre uma instância do Microsoft SQL Server e um arquivo de dados em um formato especificado pelo usuário. Você pode usá-la para importar grandes números de novas linhas para tabelas do SQL Server ou para exportar dados de tabelas para arquivos de dados. Para importar dados para uma tabela, você deve usar um arquivo de formato criado para essa tabela. Ou você deve entender a estrutura da tabela e os tipos de dados que são válidos para suas colunas.

  Para obter mais informações, consulte [conectando-se com o bcp](/sql/connect/odbc/linux-mac/connecting-with-bcp).

- **sqlcmd**: você pode inserir instruções TRANSACT-SQL usando a ferramenta sqlcmd. Você também pode inserir procedimentos do sistema e arquivos de script no prompt de comando. Essa ferramenta usa o ODBC para executar lotes do Transact-SQL.

  Para saber mais, confira [Conectando-se com o sqlcmd](/sql/connect/odbc/linux-mac/connecting-with-sqlcmd).

  > [!NOTE]
  > Há algumas diferenças nessa ferramenta entre as plataformas Linux e Windows. Consulte a documentação para obter detalhes.

### <a name="database-access-libraries"></a>Bibliotecas de acesso do banco de dados

As bibliotecas estão disponíveis em R e Python para acesso do banco de dados:

* No R, você pode usar o pacote RODBC ou o dplyr para consultar ou executar instruções SQL no servidor de banco de dados.
* No Python, a biblioteca pyodbc fornece acesso ao banco de dados com o ODBC como a camada subjacente.  

## <a name="azure-tools"></a>Ferramentas do Azure

As ferramentas do Azure a seguir são instaladas na VM:

* **CLI do Azure**: você pode usar a interface de linha de comando no Azure para criar e gerenciar recursos do Azure por meio de comandos do Shell. Para abrir as ferramentas do Azure, insira **ajuda do azure**. Para saber mais, confira a [página de documentação da CLI do Azure](/cli/azure/get-started-with-az-cli2).
* **Gerenciador de armazenamento do Azure**: Gerenciador de armazenamento do Azure é uma ferramenta gráfica que você pode usar para navegar pelos objetos que você armazenou em sua conta de armazenamento do Azure e para carregar e baixar dados de e para BLOBs do Azure. Você pode acessar o Gerenciador de Armazenamento do ícone de atalho da área de trabalho. Você também pode abri-lo em um prompt do shell inserindo **StorageExplorer**. É necessário estar conectado em um cliente X2Go ou ter a configuração de encaminhamento X11.
* **Bibliotecas do Azure**: Veja a seguir algumas das bibliotecas pré-instaladas.
  
  * **Python**: as bibliotecas relacionadas ao Azure no Python são *Azure*, *azureml*, *pydocumentdb* e *pyodbc*. Com as três primeiras bibliotecas, você pode acessar os serviços de armazenamento do Azure, o Azure Machine Learning e o Azure Cosmos DB (um banco de dados NoSQL no Azure). A quarta biblioteca, pyodbc (juntamente com o Microsoft ODBC driver for SQL Server), permite o acesso a SQL Server, banco de dados SQL do Azure e análise de Synapse do Azure do Python usando uma interface ODBC. Insira **pip list** para ver todas as bibliotecas listadas. Certifique-se de executar este comando nos ambientes do Python 2.7 e 3.5.
  * **R**: as bibliotecas relacionadas ao Azure em R são AZUREML e RODBC.
  * **Java**: a lista de bibliotecas Java do Azure pode ser encontrada no diretório /dsvm/sdk/AzureSDKJava na VM. As bibliotecas principais são as APIs de armazenamento e gerenciamento do Azure, o Azure Cosmos DB e os drivers JDBC para SQL Server.  

Você pode acessar o [portal do Azure](https://portal.azure.com) do navegador Firefox pré-instalado. No portal do Azure, você pode criar, gerenciar e monitorar recursos do Azure.

## <a name="azure-machine-learning"></a>Azure Machine Learning

O Azure Machine Learning é um serviço de nuvem totalmente gerenciado que habilita você a compilar, implantar e compartilhar soluções de análise preditiva. Você pode criar seus experimentos e modelos no Azure Machine Learning Studio (versão prévia). Você pode acessá-lo de um navegador da Web na Máquina Virtual de Ciência de Dados acessando o [Microsoft Azure Machine Learning](https://ml.azure.com).

Depois de entrar no Azure Machine Learning Studio, você pode usar uma tela de experimentação para criar um fluxo lógico para os algoritmos de aprendizado de máquina. Você também tem acesso a um Jupyter notebook que está hospedado no Azure Machine Learning e pode trabalhar de forma direta com os experimentos no Azure Machine Learning Studio. 

Coloque em operação os modelos de aprendizado de máquina compilados encapsulando-os em uma interface de serviço Web. A operacionalização de modelos de machine learning permite que clientes escritos em qualquer linguagem invoquem previsões desses modelos. Para saber mais, confira a [Documentação do Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Você também pode criar seus modelos em R ou Python na VM e, em seguida, implantá-los em produção no Azure Machine Learning. Instalamos bibliotecas em R (**AzureML**) e Python (**azureml**) para habilitar essa funcionalidade.

> [!NOTE]
> Essas instruções foram escritas para a versão do Windows da Máquina Virtual de Ciência de Dados. Mas as informações fornecidas sobre a implantação de modelos para o Azure Machine Learning são aplicáveis à VM Linux.

## <a name="machine-learning-tools"></a>Ferramentas de Machine Learning

A VM vem com ferramentas e algoritmos de aprendizado de máquina que foram pré-compiladas e pré-instaladas localmente. Elas incluem:

* **Vowpal Wabbit**: um algoritmo de aprendizado rápido online.
* **xgboost**: uma ferramenta que fornece algoritmos de árvore aumentados e otimizados.
* **Rattle**: uma ferramenta gráfica baseada em R para facilitar a exploração de dados e a modelagem.
* **Python**: o Anaconda Python é fornecido com os algoritmos de aprendizado de máquina com bibliotecas como Scikit-learn. Você pode instalar outras bibliotecas usando o comando `pip install` .
* **LightGBM**: um gradiente rápido, distribuído e de alto desempenho aumentando a estrutura com base em algoritmos de árvore de decisão.
* **R**: uma biblioteca avançada de funções de aprendizado de máquina está disponível para R. as bibliotecas pré-instaladas incluem LM, GLM, randomForest e rpart. Você pode instalar outras bibliotecas executando este comando:

    ```r
    install.packages(<lib name>)
    ```

Veja algumas informações adicionais sobre as três primeiras ferramentas de aprendizado de máquina na lista.

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit é um sistema de aprendizado de máquina rápido que usa técnicas como online, hash, allreduce, reduções, learning2search, ativo e aprendizado interativo.

Para executar a ferramenta em um exemplo básico, use os seguintes comandos:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Há outras demonstrações maiores nesse diretório. Para saber mais sobre Vowpal Wabbit, confira [esta seção do GitHub](https://github.com/JohnLangford/vowpal_wabbit) e o [wiki do Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki).

### <a name="xgboost"></a>xgboost

A biblioteca xgboost foi projetada e otimizada para algoritmos aumentados (de árvore). O objetivo dessa biblioteca é estender os limites de computação de máquinas para os extremos necessários de modo a fornecer aumento de árvore de grande escala escalonável, portátil e preciso.

Ele é fornecido como uma linha de comando e uma biblioteca do R. Para usar esta biblioteca em R, você pode iniciar a sessão interativa do R (inserindo **R** no shell) e carregar a biblioteca.

Veja um exemplo simples que você pode executar em um prompt do R:

```R
library(xgboost)

data(agaricus.train, package='xgboost')
data(agaricus.test, package='xgboost')
train <- agaricus.train
test <- agaricus.test
bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
pred <- predict(bst, test$data)
```

Para executar a linha de comando do xgboost, veja os comandos a serem executados no shell:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

Um arquivo .model é gravado no diretório especificado. Você pode encontrar informações sobre esse exemplo de demonstração [no GitHub](https://github.com/dmlc/xgboost/tree/master/demo/CLI/binary_classification).

Para saber mais sobre o xgboost, confira a [página de documentação do xgboost](https://xgboost.readthedocs.org/en/latest/) e seu [repositório GitHub](https://github.com/dmlc/xgboost).

### <a name="rattle"></a>Rattle

Rattle (the **R** **A** nalytical **T** ool **T** o **L** earn **E** asily – Ferramenta Analítica do R para Aprender com Facilidade) usa exploração e modelagem de dados com base em GUI. Ele apresenta resumos estatísticos e visuais dos dados, transforma os dados que podem ser modelados prontamente, compila modelos de dados supervisionados e sem supervisão, apresenta o desempenho dos modelos graficamente e calcula as pontuações de novos conjuntos de dados. Ele também gera código R, replicando as operações na interface do usuário que pode ser executado diretamente em R ou usado como ponto de partida para análise posterior.

Para executar o Rattle, você precisa estar em uma sessão de logon da área de trabalho gráfica. No terminal, insira **R** para abrir o ambiente R. No prompt do R, digite os seguintes comandos:

```R
library(rattle)
rattle()
```

Agora, uma interface gráfica é aberta com um conjunto de guias. Use as seguintes etapas de início rápido no Rattle para usar um conjunto de dados meteorológicos de exemplo e criar um modelo. Em algumas das etapas, você deve instalar e carregar automaticamente alguns pacotes do R que ainda não estão no sistema.

> [!NOTE]
> Se não tiver acesso para instalar o pacote no diretório do sistema (o padrão), você poderá ver uma solicitação na janela do console do R para instalar pacotes na sua biblioteca pessoal. Caso veja essas solicitações, responda **s** .

1. Selecione **Executar**.
1. Uma caixa de diálogo é exibida perguntando se você deseja usar o conjunto de dados meteorológicos de exemplo. Selecione **Sim** para carregar o exemplo.
1. Selecione a guia **modelo** .
1. Selecione **Executar** para criar uma árvore de decisão.
1. Selecione **Desenhar** para exibir a árvore de decisão.
1. Selecione a opção **Floresta** e **Executar** para criar uma floresta aleatória.
1. Selecione a guia **Avaliar**.
1. Selecione a opção **Risco** e **Executar** para exibir dois gráficos de desempenho de **Risco (Cumulativo)**.
1. Selecione a guia **Log** para mostrar o código R gerado para as operações anteriores.
   (Devido a um bug na versão atual do Rattle, você precisa inserir um **#** caractere na frente da **exportação desse log** no texto do log.)
1. Selecione o botão **Exportar** para salvar o arquivo de script do R chamado *weather_script.R* na pasta base.

Você pode sair do Rattle e do R. Agora, você pode modificar o script de R gerado. Ou você pode usá-lo no estado em que se encontra e executá-lo em qualquer momento, para repetir tudo o que foi feito na interface do usuário do Rattle. Essa é uma maneira, especialmente para iniciantes em R, de fazer análise e aprendizado de máquina rapidamente em uma interface gráfica e, ao mesmo tempo, gerar código em R automaticamente para modificar ou aprender.

## <a name="next-steps"></a>Próximas etapas

Tem mais dúvidas? Considere criar um [tíquete de suporte](https://azure.microsoft.com/support/create-ticket/).