---
title: Explorar o Linux
titleSuffix: Azure Data Science Virtual Machine
description: Saiba como concluir várias tarefas comuns de ciência de dados usando a Máquina Virtual de Ciência de Dados do Linux.
services: machine-learning
ms.service: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 09/17/2020
ms.openlocfilehash: 42136d0d58dbc318aab0e111fcef46f80751ca88
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100517664"
---
# <a name="data-science-with-an-ubuntu-data-science-virtual-machine-in-azure"></a>Ciência de dados com uma Máquina Virtual de Ciência de Dados do Ubuntu no Azure

Este tutorial mostra como concluir várias tarefas comuns de ciência de dados usando o Ubuntu Máquina Virtual de Ciência de Dados (DSVM). O Ubuntu DSVM é uma imagem de máquina virtual disponível no Azure pré-instalado com uma coleção de ferramentas comumente usadas para análise de dados e aprendizado de máquina. Os principais componentes de software são discriminados para [provisionar o máquina virtual de ciência de dados Ubuntu](./dsvm-ubuntu-intro.md). A imagem da DSVM é uma introdução fácil e rápida à ciência de dados, sem precisar instalar e configurar cada uma das ferramentas individualmente. Se necessário, é fácil escalar verticalmente a DSVM e interrompê-lo quando não estiver me uso. Portanto, o recurso de DSVM é elástico e econômico.

Neste passo a passo, analisamos o conjunto de dados [baseado em spam](https://archive.ics.uci.edu/ml/datasets/spambase). Baseado em spam é um conjunto de emails que são marcados como spam ou ham (não spam). Baseado em spam também contém algumas estatísticas sobre o conteúdo dos emails. Falaremos sobre as estatísticas a seguir, no passo a passo.

## <a name="prerequisites"></a>Pré-requisitos

Para poder usar uma DSVM do Linux, você precisa cumprir os pré-requisitos a seguir:

* **Assinatura do Azure**. Para obter uma assinatura do Azure, confira [Criar sua conta do Azure hoje mesmo](https://azure.microsoft.com/free/).

* [**Ubuntu máquina virtual de ciência de dados**](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804). Para obter informações sobre como provisionar a máquina virtual, consulte [provisionar o Ubuntu máquina virtual de ciência de dados](./release-notes.md).
* O [**X2Go**](https://wiki.x2go.org/doku.php) instalado em seu computação com a sessão aberta do XFCE. Para obter mais informações, consulte [Instalar e configurar o cliente X2Go](dsvm-ubuntu-intro.md#x2go).
* Para uma experiência de rolagem com menos problemas, no navegador da Web Firefox da DSVM, ative o sinalizador `gfx.xrender.enabled` em `about:config`. [Saiba mais](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). Considere também configurar `mousewheel.enable_pixel_scrolling` como `False`. [Saiba mais](https://support.mozilla.org/questions/981140).

## <a name="download-the-spambase-dataset"></a>Baixar o conjunto de dados baseado em spam

O conjunto de dados [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) é um conjunto de dados relativamente pequeno que contém 4.601 exemplos. O conjunto de dados é um tamanho conveniente para demonstrar alguns dos principais recursos da DSVM, pois mantém requisitos de recursos modestos.

> [!NOTE]
> Este passo a passo foi criado com o uso de uma DSVM do Linux com tamanho D2 v2 (Ubuntu 18.04 Edition). Você pode usar uma DSVM desse tamanho para concluir os procedimentos demonstrados neste passo a passo.

Se você precisar de mais espaço de armazenamento, poderá criar discos adicionais e anexá-los à sua DSVM. Os discos usam o armazenamento persistente do Azure para que seus dados sejam preservados mesmo se o servidor for provisionado novamente devido ao redimensionamento ou é desligado. Para adicionar um disco e anexá-lo à sua DSVM, conclua as etapas em [Adicionar um disco a uma VM do Linux](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). As etapas para adicionar um disco usam a CLI do Azure que já está instalada na DSVM. Você pode concluir as etapas inteiramente na própria DSVM. Outra opção para aumentar o armazenamento é usar os [Arquivos do Azure](../../storage/files/storage-how-to-use-files-linux.md).

Para baixar os dados, abra uma janela do terminal e depois execute este comando:

```bash
wget --no-check-certificate https://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data
```

O arquivo baixado não tem linha de cabeçalho. Vamos criar outro arquivo que tenha um cabeçalho. Execute este comando para criar um arquivo com os devidos cabeçalhos:

```bash
echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers
```

Em seguida, concatene os dois arquivos:

```bash
cat spambase.data >> headers
mv headers spambaseHeaders.data
```

O conjunto de dados tem vários tipos de estatísticas para cada email:

* Colunas como **word\_freq\__WORD_** indicam a porcentagem de palavras no email que correspondem a *WORD*. Por exemplo se **word\_freq\_make** is **1**, então 1% de todas as palavras no email eram *make*.
* Colunas como **char\_freq\__CHAR_** indicam a porcentagem de todos os caracteres no email que são *CHAR*.
* **capital\_run\_length\_longest** é o maior comprimento de uma sequência de letras maiúsculas.
* **capital\_run\_length\_average** é a duração média de todas as sequências de letras maiúsculas.
* **capital\_run\_length\_total** é o comprimento total de todas as sequências de letras maiúsculas.
* **spam** indica se o email foi considerado spam ou não (1 = spam, 0 = não spam).

## <a name="explore-the-dataset-by-using-r-open"></a>Explorar o conjunto de dados um usando o R Open

Vamos examinar os dados e executar um aprendizado de máquina básico usando R. A DSVM vem com o [Microsoft R Open](https://mran.revolutionanalytics.com/open/) pré-instalado. As bibliotecas matemáticas multithread na versão pré-instalada do R oferecem melhor desempenho que versões de thread único. O R Open também fornece a capacidade de reprodução por meio de um instantâneo do repositório de pacotes CRAN.

Para obter cópias dos exemplos de código usados neste passo a passo, use o Git para clonar o repositório Azure-Machine-Learning-Data-Science. O Git é pré-instalado na DSVM. Na linha de comando do git, execute:

```bash
git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git
```

Abra uma janela de terminal e inicie uma nova sessão de R no console interativo de R. Você também pode usar o RStudio, que é pré-instalado na DSVM.

Para importar os dados e configurar o ambiente:

```R
data <- read.csv("spambaseHeaders.data")
set.seed(123)
```

Para ver as estatísticas de resumo sobre cada coluna:

```R
summary(data)
```

Para obter uma exibição diferente dos dados:

```R
str(data)
```

Essa exibição mostra o tipo de cada variável e os primeiros valores no conjunto de dados.

A coluna **spam** foi lida como um número inteiro, mas é realmente uma variável categórica (ou fator). Para definir seu tipo:

```R
data$spam <- as.factor(data$spam)
```

Para fazer algumas análises exploratórias, use o pacote [ggplot2](https://ggplot2.tidyverse.org/), uma biblioteca de gráficos popular para R que está instalada na DSVM. Com base nos dados de resumo exibidos anteriormente, temos as estatísticas de resumo sobre a frequência do caractere de ponto de exclamação. Vamos criar gráficos com as frequências aqui com os seguintes comandos:

```R
library(ggplot2)
ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

Como a barra zero está distorcendo o gráfico, vamos eliminá-la:

```R
email_with_exclamation = data[data$char_freq_exclamation > 0, ]
ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

Há uma densidade não trivial acima de 1 parece interessante. Vamos examinar apenas estes dados:

```R
ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

Em seguida, divida-os em spam e ham:

```R
ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
geom_density(lty=3) +
geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
xlab("spam") +
ggtitle("Distribution of spam \nby frequency of !") +
labs(fill="spam", y="Density")
```

Estes exemplos devem ajudá-lo a criar gráficos semelhantes e explorar os dados nas outras colunas.

## <a name="train-and-test-a-machine-learning-model"></a>Treinar e testar um modelo de machine learning

Vamos treinar alguns modelos de aprendizado de máquina para classificar os emails no conjunto de dados como contendo spam ou ham. Nesta seção, treinamos um modelo de árvore de decisão e um modelo de floresta aleatória. Em seguida, testamos a precisão das previsões.

> [!NOTE]
> O pacote *rpart* (particionamento recursivo e árvores de regressão) usado no código a seguir já está instalado na DSVM.

Primeiro, dividiremos o conjunto de dados em conjuntos de treinamento e de teste:

```R
rnd <- runif(dim(data)[1])
trainSet = subset(data, rnd <= 0.7)
testSet = subset(data, rnd > 0.7)
```

Depois, crie uma árvore de decisão para classificar os emails:

```R
require(rpart)
model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
plot(model.rpart)
text(model.rpart)
```

Eis o resultado:

![Um diagrama da árvore de decisão que é criada](./media/linux-dsvm-walkthrough/decision-tree.png)

Para determinar a execução no conjunto de treinamento, use o seguinte código:

```R
trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

Para determinar a execução no conjunto de teste:

```R
testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

Também tentaremos um modelo de floresta aleatória. As florestas aleatórias treinam uma infinidade de árvores de decisão e geram uma classe que é o modo das classificações a partir de todas as árvores de decisão individuais. Fornecem uma abordagem do aprendizado de máquina mais potente à medida que corrigem a tendência de um modelo de árvore de decisão de sobreajustar um conjunto de dados de treinamento.

```R
require(randomForest)
trainVars <- setdiff(colnames(data), 'spam')
model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

<a name="deep-learning"></a>

## <a name="deep-learning-tutorials-and-walkthroughs"></a>Tutoriais e passo a passo sobre aprendizado profundo

Além dos exemplos baseados em estrutura, fornecemos também um conjunto abrangente de orientações. Essas orientações passo a passo ajudam a iniciar o desenvolvimento de aplicativos de aprendizagem profunda em domínios como reconhecimento vocal/texto e imagem.

- [Executando redes neurais em estruturas diferentes](https://github.com/ilkarman/DeepLearningFrameworks): Um passo a passo completo que mostra como migrar o código de uma estrutura para outra. Ele também demonstra como comparar o desempenho do modelo e do runtime entre estruturas. 

- [Um guia de instruções para criação de uma solução de ponta a ponta para detectar produtos em imagens](https://github.com/Azure/cortana-intelligence-product-detection-from-images): a detecção de imagens é uma técnica que pode localizar e classificar objetos em imagens. A tecnologia tem o potencial de trazer grande recompensa em vários domínios de negócios da vida real. Por exemplo, os varejistas podem usar essa técnica para determinar qual produto um cliente retirou da prateleira. Por sua vez, essas informações ajudam as lojas a gerenciar o estoque do produtos. 

- [Aprendizado profundo para áudio](/archive/blogs/machinelearning/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure): Este tutorial mostra como treinar um modelo de aprendizado profundo para detecção de eventos no [conjunto de dados de sons urbanos](https://urbansounddataset.weebly.com/). O tutorial fornece uma visão geral de como trabalhar com dados de áudio.

- [Classificação de documentos de texto](https://github.com/anargyri/lstm_han): Este passo a passo demonstra como criar e treinar duas arquiteturas de rede neural diferentes: Rede de Atenção Hierárquica e LSTM (memória de longo-curto prazo). Essas redes neurais usam a API Keras para aprendizagem profunda para classificar documentos de texto. Keras é um front-end para três das estruturas de aprendizado profundo mais populares: Microsoft Cognitive Toolkit, TensorFlow e Theano.

## <a name="other-tools"></a>Outras ferramentas

As seções restantes mostram como usar algumas das ferramentas instaladas na DSVM do Linux. Discutimos estas ferramentas:

* XGBoost
* Python
* JupyterHub
* Rattle
* PostgreSQL e SQuirreL SQL
* Azure Synapse Analytics (antigo SQL DW)

### <a name="xgboost"></a>XGBoost

[XGBoost](https://xgboost.readthedocs.org/en/latest/) fornece uma implementação de árvore aumentada rápida e precisa.

```R
require(xgboost)
data <- read.csv("spambaseHeaders.data")
set.seed(123)

rnd <- runif(dim(data)[1])
trainSet = subset(data, rnd <= 0.7)
testSet = subset(data, rnd > 0.7)

bst <- xgboost(data = data.matrix(trainSet[,0:57]), label = trainSet$spam, nthread = 2, nrounds = 2, objective = "binary:logistic")

pred <- predict(bst, data.matrix(testSet[, 0:57]))
accuracy <- 1.0 - mean(as.numeric(pred > 0.5) != testSet$spam)
print(paste("test accuracy = ", accuracy))
```

O XGBoost também pode chamar a partir do Python ou de uma linha de comando.

### <a name="python"></a>Python

Para o desenvolvimento em Python, as distribuições Anaconda Python 3.5 e 2.7 estão instaladas na DSVM.

> [!NOTE]
> A distribuição Anaconda inclui [Conda](https://conda.pydata.org/docs/index.html). Você pode usar o Conda para criar ambientes de Python personalizados com diferentes versões ou pacotes instalados.

Vamos transmitir o conjunto de dados baseado em spam e classificar os emails com máquinas do vetor de suporte no Scikit-learn:

```Python
import pandas
from sklearn import svm
data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
X = data.ix[:, 0:57]
y = data.ix[:, 57]
clf = svm.SVC()
clf.fit(X, y)
```

Para fazer previsões:

```Python
clf.predict(X.ix[0:20, :])
```

Para demonstrar como publicar um ponto de extremidade do Azure Machine Learning, vamos criar um modelo mais básico. Usaremos as três variáveis usadas ao publicar o modelo de R anteriormente:

```Python
X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
y = data.ix[:, 57]
clf = svm.SVC()
clf.fit(X, y)
```

### <a name="jupyterhub"></a>JupyterHub

A distribuição Anaconda na DSVM vem com um Jupyter Notebook, um ambiente de plataforma cruzada para compartilhamento do Python, R ou do código Julia e da análise. O Jupyter Notebook é acessado com o JupyterHub. Você entra usando seu nome de usuário e senha do Linux local em https:// \<DSVM DNS name or IP address\> : 8000/. Todos os arquivos de configuração para o JupyterHub são encontrados em /etc/jupyterhub.

> [!NOTE]
> Para usar o gerenciador de pacotes do Python (por meio do comando `pip`) de um Jupyter Notebook no kernel atual, use este comando na célula de código:
>
>   ```Python
>    import sys
>    ! {sys.executable} -m pip install numpy -y
>   ```
> 
> Para usar o instalador Conda (por meio do comando `conda`) de um Jupyter Notebook no kernel atual, use este comando em uma célula de código:
>
>   ```Python
>    import sys
>    ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
>   ```

Vários blocos de anotações de exemplo já estão instalados na DSVM:

* Exemplo de notebooks Python:
  * [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb)
* Exemplo de notebook R:
  * [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) 

> [!NOTE]
> A linguagem Julia também está disponível na linha de comando na DSVM do Linux.

### <a name="rattle"></a>Rattle

[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (*R* *A* nalytical *T* ool *T* o *L* earn *E* asily) é uma ferramenta R gráfica para mineração de dados. O Rattle tem uma interface simples que facilita carregar, explorar e transformar os dados, compilar e avaliar os modelos. [Rattle: uma GUI da mineração de dados para o R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) fornece um passo a passo que demonstra os recursos do Rattle.

Instale e inicie o Rattle com estes comandos:

```R
if(!require("rattle")) install.packages("rattle")
require(rattle)
rattle()
```

> [!NOTE]
> Você não precisa instalar o Rattle na DSVM. No entanto, você pode ser solicitado a instalar pacotes adicionais quando o Rattle abrir.

O Rattle usa uma interface baseada em guias. A maioria das guias corresponde às etapas no [Processo de ciência de dados de equipe](../team-data-science-process/index.yml), como carregar os dados ou explorá-los. O processo da ciência de dados flui da esquerda para a direita nas guias. A última guia contém um log dos comandos do R executados pelo Rattle.

Para carregar e configurar o conjunto de dados:

1. Para carregar o arquivo, selecione a guia **Dados**.
1. Escolha o seletor ao lado do **Nome de arquivo** e depois escolha **spambaseHeaders.data**.
1. Para carregar o arquivo. Selecione **Executar**. Você deve ver um resumo de cada coluna, incluindo o tipo de dados identificado, se é uma entrada, um destino ou outro tipo de variável, e o número de valores exclusivos.
1. O Rattle identifica corretamente a coluna de **spam** como o destino. Selecione a coluna **spam** e, em seguida, defina o **Tipo de dados de destino** como **Categórico**.

Para explorar os dados:

1. Selecione a guia **Explorar** .
1. Para ver informações sobre os tipos de variáveis e algumas estatísticas de resumo, selecione **Resumo** > **Executar**.
1. Para exibir os outros tipos de estatísticas sobre cada variável, selecione outras opções, como **Descrever** ou **Básico**.

Você também pode usar a guia **Explorar** para gerar gráficos reveladores. Para criar gráficos com um histograma dos dados:

1. Selecione **Distribuições**.
1. Para **word_freq_remove** e **word_freq_you**, selecione **Histograma**.
1. Selecione **Executar**. Você deverá ver dois gráficos de densidade em uma janela de grafo, na qual fica claro que a palavra _você_ aparece com muito mais frequência nos emails que _remover_.

Os gráficos de **correlação** também são interessantes. Para criar um gráfico:

1. Em **Tipo**, selecione **Correlação**.
1. Selecione **Executar**.
1. O Rattle avisa que ele recomenda um máximo de 40 variáveis. Selecione **Sim** para exibir a criação de gráficos.

Há algumas correlações interessantes que surgem: _tecnologia_ é muito correlacionado a _HP_ e _laboratórios_, por exemplo. Também está muito correlacionado a _650_, porque o código de área dos doadores do conjunto de dados é 650.

Os valores numéricos para as correlações entre as palavras estão disponíveis na janela **Explorar**. É interessante observar, por exemplo, que _tecnologia_ está negativamente correlacionado a _seu_ e _dinheiro_.

O Rattle pode transformar o conjunto de dados para lidar com alguns problemas comuns. Por exemplo, ele pode redimensionar os recursos, atribuir os valores ausentes, lidar com os valores atípicos e remover as variáveis ou observações com dados faltando. O Rattle também pode identificar regras de associação entre as observações e variáveis. Essas guias não são abordadas neste passo a passos introdutório.

O Rattle também pode executar análise de cluster. Iremos excluir alguns recursos para facilitar a leitura da saída. Na guia **Dados**, selecione **Ignorar** ao lado de cada uma das variáveis, exceto estes 10 itens:

* word_freq_hp
* word_freq_technology
* word_freq_george
* word_freq_remove
* word_freq_your
* word_freq_dollar
* word_freq_money
* capital_run_length_longest
* word_freq_business
* spam

Retorne à guia **Cluster**. Selecione **KMeans** e, em seguida, defina o **Número de clusters** como **4**. Selecione **Executar**. Os resultados são exibidos na janela de saída. Um cluster tem alta frequência de _george_ e _hp_ e provavelmente é um email corporativo legítimo.

Para compilar um modelo de aprendizado de máquina da árvore de decisão básica:

1. Selecione a guia **Modelo** ,
1. Para o **Tipo**, selecione **Árvore**.
1. Selecione **Executar** para exibir a árvore em formato de texto na janela de saída.
1. Selecione o botão **Desenhar** para exibir uma versão gráfica. A árvore de decisão é semelhante à árvore obtida anteriormente usando rpart.

Um dos recursos úteis do Rattle é sua capacidade de executar vários métodos de aprendizado de máquina e avaliá-los rapidamente. Estas são as etapas:

1. Para **Tipo**, selecione **Todos**.
1. Selecione **Executar**.
1. Quando o Rattle terminar de ser executado, você poderá selecionar qualquer valor para **Tipo**, como **SVM** e exibir os resultados.
1. Você também pode comparar o desempenho dos modelos no conjunto de validação usando a guia **Avaliar**. Por exemplo, a seleção **Matriz do Erro** mostra a matriz de confusão, erro geral e erro de classe média para cada modelo no conjunto de validação. Você também pode criar gráficos com as curvas ROC, executar a análise de sensibilidade e fazer outros tipos de avaliações do modelo.

Ao terminar de compilar os modelos, selecione a guia **Log** para exibir o código do R que foi executado pelo Rattle durante a sessão. Você pode selecionar o botão **Exportar** para salvá-lo.

> [!NOTE]
> A versão atual do Rattle contém um bug. Para modificar o script ou usá-lo para repetir as etapas posteriormente, você deve inserir um caractere **#** na frente de *Exportar este log…* no texto do log.

### <a name="postgresql-and-squirrel-sql"></a>PostgreSQL e SQuirreL SQL

A DSVM vem com o PostgreSQL instalado. O PostgreSQL é um banco de dados relacional sofisticado de fonte aberta. Esta seção mostra como carregar o conjunto de dados baseado em spam no PostgreSQL e, em seguida, consultá-lo.

Antes de carregar os dados, você precisa permitir a autenticação de senha a partir do host local. Em um prompt de comando, execute:

```Bash
sudo gedit /var/lib/pgsql/data/pg_hba.conf
```

Na parte inferior do arquivo de configuração, há várias linhas que detalham as conexões permitidas:

```
# "local" is only for Unix domain socket connections:
local   all             all                                     trust
# IPv4 local connections:
host    all             all             127.0.0.1/32            ident
# IPv6 local connections:
host    all             all             ::1/128                 ident
```

Altere a linha **conexões locais IPv4** para usar **md5** em vez de **ident**, para que possamos fazer logon usando um nome de usuário e senha:

```
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
```

Em seguida, reinicie o serviço PostgreSQL:

```Bash
sudo systemctl restart postgresql
```

Para iniciar o *psql* (um terminal interativo do PostgreSQL) como o usuário postgres interno, execute este comando:

```Bash
sudo -u postgres psql
```

Crie uma nova conta de usuário usando o nome de usuário da conta do Linux usada para fazer logon. Crie uma senha:

```Bash
CREATE USER <username> WITH CREATEDB;
CREATE DATABASE <username>;
ALTER USER <username> password '<password>';
\quit
```

Faça logon no psql:

```Bash
psql
```

Importe os dados para um novo banco de dados:

```SQL
CREATE DATABASE spam;
\c spam
CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
\copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
\quit
```

Agora, vamos explorar os dados e executar algumas consultas usando o SQuirreL SQL, uma ferramenta gráfica usada para interagir com os bancos de dados por meio de um driver JDBC.

Para começar, no menu **Aplicativos**, abra o SQuirreL SQL. Para configurar o driver:

1. Selecione **Windows** > **Exibir drivers**.
1. Clique com o botão direito do mouse em **PostgreSQL** e selecione **Modificar driver**.
1. Selecione **Caminho da classe extra** > **Adicionar**.
1. Em **Nome do arquivo**, digite **/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar**.
1. Selecione **Abrir**.
1. Selecione **Listar drivers**. Em **Nome da classe**, selecione **org.postgresql.Driver** e, em seguida, **OK**.

Para configurar a conexão com o servidor local:

1. Selecione **Windows** > **Exibir aliases.**
1. Selecione o botão **+** para criar um novo alias. No novo nome do alias, insira **Banco de dados de spam**. 
1. Em **Driver**, selecione **PostgreSQL**.
1. Define a URL como **jdbc:postgresql://localhost/spam**.
1. Insira seu nome de usuário e sua senha.
1. Selecione **OK**.
1. Para abrir a janela **Conexão**, clique duas vezes no alias do **Banco de dados de spam**.
1. Selecione **Conectar**.

Para executar algumas consultas:

1. Selecione a guia **SQL** .
1. Na caixa de consulta acima da guia **SQL**, insira uma consulta básica, como `SELECT * from data;`.
1. Pressione Ctrl+Enter para executar a consulta. Por padrão, o SQuirreL SQL retorna as primeiras 100 linhas de sua consulta.

Há muito mais consultas que podem ser executadas para explorar esses dados. Por exemplo, como a frequência da palavra *make* difere entre o spam e o ham?

```SQL
SELECT avg(word_freq_make), spam from data group by spam;
```

Ou, quais são as características de email que geralmente contêm *3º*?

```SQL
SELECT * from data order by word_freq_3d desc;
```

A maioria dos emails que têm uma grande ocorrência de *3d* aparentemente é spam. Essas informações podem ser úteis para criar um modelo de previsão para classificar emails.

Se você quiser usar dados armazenados em um banco de dados PostgreSQL para aprendizado de máquina, considere usar o [MADlib](https://madlib.incubator.apache.org/).

### <a name="azure-synapse-analytics-formerly-sql-dw"></a>Azure Synapse Analytics (antigo SQL DW)

O Azure Synapse Analytics é um banco de dados baseado em nuvem e escalável que pode processar volumes maciços e não relacionais. Para obter mais informações, consulte [o que é o Azure Synapse Analytics?](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Para conectar o data warehouse e criar a tabela, execute o seguinte comando em um prompt de comando:

```Bash
sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I
```

No prompt do sqlcmd, execute este comando:

```SQL
CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
GO
```

Copie os dados usando bcp:

```bash
bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"
```

> [!NOTE]
> O arquivo baixado contém términos de linha no estilo do Windows. A ferramenta bcp espera términos de linha no estilo do Unix. Use o sinalizador -r para informar o bcp.

Em seguida, consulte usando sqlcmd:

```sql
select top 10 spam, char_freq_dollar from spam;
GO
```

Você também pode consultar com o SSQuirreL SQL. Siga as etapas semelhantes ao PostgreSQL usando o driver JDBC do SQL Server. O driver JDBC está na pasta /usr/share/java/jdbcdrivers/sqljdbc42.jar.