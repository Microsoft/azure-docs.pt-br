---
title: "Ciência de dados com a Máquina Virtual da Ciência de Dados do Linux no Azure | Microsoft Docs"
description: "Como executar várias tarefas comuns da ciência de dados com a VM da Ciência de Dados do Linux."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 34ef0b10-9270-474f-8800-eecb183bbce4
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: bradsev;paulsh
ms.openlocfilehash: 0decb8918a544114316569720aa5deede692d5f1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-on-azure"></a>Ciência de dados com uma Máquina Virtual da Ciência de Dados do Linux no Azure
Este passo a passo mostra como executar várias tarefas comuns da ciência de dados com a VM da Ciência de Dados do Linux. A Máquina Virtual da Ciência de Dados do Linux (DSVM) é uma imagem da máquina virtual disponível no Azure pré-instalada com uma coleção de ferramentas usadas comumente para a análise de dados e o aprendizado de máquina. Os principais componentes do software são detalhados no tópico [Provisionar a Máquina Virtual da Ciência de Dados do Linux](linux-dsvm-intro.md) . A imagem da VM facilita começar a fazer a ciência de dados em minutos, sem precisar instalar e configurar cada uma das ferramentas individualmente. Você pode dimensionar facilmente a VM, se necessário, e parar quando não estiver em uso. Portanto, esse recurso é elástico e econômico.

As tarefas da ciência de dados demonstradas neste passo a passo seguem as etapas descritas no [Processo da Ciência de Dados de Equipe](https://azure.microsoft.com/documentation/learning-paths/data-science-process/). Esse processo fornece uma abordagem sistemática para a ciência de dados que permite às equipes de cientistas de dados colaborarem de maneira eficiente no ciclo de vida da criação de aplicativos inteligentes. O processo da ciência de dados também fornece uma estrutura iterativa para a ciência de dados que pode ser seguida por uma pessoa.

Analisamos o conjunto de dados [baseado em spam](https://archive.ics.uci.edu/ml/datasets/spambase) neste passo a passo. Este é um conjunto de emails marcados como spam ou ham (ou seja, não são spam) e também contém algumas estatísticas sobre o conteúdo dos emails. As estatísticas incluídas serão analisadas na próxima seção, exceto uma.

## <a name="prerequisites"></a>Pré-requisitos
Antes de criar uma Máquina Virtual da Ciência de Dados do Linux, você deve ter o seguinte:

* Uma **assinatura do Azure**. Se você não tiver uma, consulte [Criar sua conta gratuita do Azure hoje](https://azure.microsoft.com/free/).
* Uma [**VM da ciência de dados do Linux**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm). Para obter informações sobre como provisionar essa VM, consulte [Provisionar a Máquina Virtual da Ciência de Dados do Linux](linux-dsvm-intro.md).
* [X2Go](http://wiki.x2go.org/doku.php) instalado em seu computador e aberto em uma sessão XFCE. Para obter informações sobre como instalar e configurar um **cliente X2Go**, confira [Instalando e configurando o cliente X2Go](linux-dsvm-intro.md#installing-and-configuring-x2go-client). 
* Uma **conta do AzureML**. Se você ainda não tiver, inscreva-se para ter uma nova na [home page do AzureML](https://studio.azureml.net/). Há uma camada de uso gratuita para ajudá-lo a começar.

## <a name="download-the-spambase-dataset"></a>Baixar o conjunto de dados baseado em spam
O conjunto de dados [baseado em spam](https://archive.ics.uci.edu/ml/datasets/spambase) é um conjunto de dados relativamente pequeno que contém somente 4.601 exemplos. Esse é um tamanho conveniente usado ao demonstrar alguns dos principais recursos da VM da Ciência de Dados, quando ela mantém os requisitos de recursos modestos.

> [!NOTE]
> Este passo a passo foi criado em Máquina Virtual da Ciência de Dados do Linux de tamanho D2 v2. Esse tamanho da DSVM é capaz de lidar com os procedimentos neste passo a passo.
>
>

Se você precisar de mais espaço de armazenamento, poderá criar discos adicionais e anexá-los à sua VM. Esses discos usam o armazenamento persistente do Azure para que seus dados sejam preservados mesmo quando o servidor é provisionado novamente devido ao redimensionamento ou é desligado. Para adicionar um disco e anexá-lo à sua VM, siga as instruções em [Adicionar um disco a uma VM do Linux](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Essas etapas usam a Interface de Linha de Comando do Azure (Azure CLI), que já está instalada na DSVM. Então, esses procedimentos podem ser feitos inteiramente na própria VM. Outra opção para aumentar o armazenamento é usar os [arquivos do Azure](../../storage/files/storage-how-to-use-files-linux.md).

Para baixar os dados, abra uma janela do terminal e execute este comando:

    wget http://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

O arquivo baixado não tem uma linha de cabeçalho, portamos, iremos criar outro arquivo com um cabeçalho. Execute este comando para criar um arquivo com os devidos cabeçalhos:

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

Então, concatene os dois arquivos com o comando:

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

O conjunto de dados tem vários tipos de estatísticas em cada email:

* Colunas como ***word\_freq\_WORD*** indicam a porcentagem de palavras no email que correspondem a *WORD*. Por exemplo, se *word\_freq\_make* for 1, 1% de todas as palavras no email foi *make*.
* Colunas como ***char\_freq\_CHAR*** indicam a porcentagem de todos os caracteres no email que foram *CHAR*.
* ***capital\_run\_length\_longest*** é o maior comprimento de uma sequência de letras maiúsculas.
* ***capital\_run\_length\_average*** é a duração média de todas as sequências de letras maiúsculas.
* ***capital\_run\_length\_total*** é o comprimento total de todas as sequências de letras maiúsculas.
* ***spam*** indica se o email foi considerado spam ou não (1 = spam, 0 = não spam).

## <a name="explore-the-dataset-with-microsoft-r-open"></a>Explorar o conjunto de dados com o Microsoft R Open
Vamos examinar os dados e fazer um aprendizado de máquina básico com R. A VM da Ciência de Dados vem com o [Microsoft R Open](https://mran.revolutionanalytics.com/open/) pré-instalado. As bibliotecas matemáticas multithread nesta versão do R oferecem melhor desempenho que diversas versões de thread único. O Microsoft R Open também fornece a capacidade de reprodução usando um instantâneo do repositório de pacotes CRAN.

Para obter cópias dos exemplos de código usados neste passo a passo, clone o repositório **Azure-Machine-Learning-Data-Science** usando o git, que é pré-instalado na VM. Na linha de comando do git, execute:

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Abra uma janela de terminal e inicie uma nova sessão de R com o console interativo de R.

> [!NOTE]
> Você também pode usar o RStudio para os procedimentos a seguir. Para instalar o RStudio, execute este comando em um terminal: `./Desktop/DSVM\ tools/installRStudio.sh`
>
>

Para importar os dados e configurar o ambiente, execute:

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

Para ver as estatísticas de resumo sobre cada coluna:

    summary(data)

Para obter uma exibição diferente dos dados:

    str(data)

Isso mostra o tipo de cada variável e os primeiros valores no conjunto de dados.

A coluna *spam* foi lida como um número inteiro, mas é realmente uma variável categórica (ou fator). Para definir seu tipo:

    data$spam <- as.factor(data$spam)

Para fazer algumas análises exploratórias, use o pacote [ggplot2](http://ggplot2.org/) , uma biblioteca de gráficos popular para R já está instalada na VM. Observe, dos dados de resumo exibidos anteriormente, temos as estatísticas de resumo sobre a frequência do caractere de ponto de exclamação. Iremos plotar as frequências aqui com os seguintes comandos:

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Como a barra zero está distorcendo a plotagem, iremos nos livrar dela:

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Há uma densidade não trivial acima de 1 parece interessante. Vejamos apenas os dados:

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Em seguida, divida por ham vs. spam:

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

Esses exemplos devem permitir fazer plotagens semelhante das outras colunas para explorar os dados contidos nelas.

## <a name="train-and-test-an-ml-model"></a>Treinar e testar um modelo de AM
Agora, iremos treinar alguns modelos de aprendizado de máquina para classificar os emails no conjunto de dados como contendo span ou ham. Treinamos um modelo da árvore de decisão e um modelo de floresta aleatória nesta seção, então, testamos sua precisão das previsões.

> [!NOTE]
> O pacote rpart (particionamento recursivo e árvores de regressão) usado no código a seguir já está instalado na VM da Ciência de Dados.
>
>

Primeiro, dividiremos o conjunto de dados em conjuntos de treinamento e teste:

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

Em seguida, crie uma árvore de decisão para classificar os emails.

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

Aqui está o resultado:

![1](./media/linux-dsvm-walkthrough/decision-tree.png)

Para determinar a execução no conjunto de treinamento, use o seguinte código:

    trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
    t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Para determinar a execução no conjunto de teste:

    testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Também tentaremos um modelo de floresta aleatória. As florestas aleatórias treinam uma infinidade de árvores de decisão e geram uma classe que é o modo das classificações a partir de todas as árvores de decisão individuais. Fornecem uma abordagem do aprendizado de máquina mais potente à medida que corrigem a tendência de um modelo de árvore de decisão de sobreajustar um conjunto de dados de treinamento.

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-ml"></a>Implantar um modelo para o AzureML
[Azure Machine Learning Studio](https://studio.azureml.net/) (AzureML) é um serviço de nuvem que facilita criar e implantar modelos de análise preditiva. Um dos recursos interessantes do AzureML é sua capacidade de publicar qualquer função R como um serviço da Web. O pacote AzureML R facilita a implantação correta a partir de nossa sessão R na DSVM.

Para implantar o código da árvore de decisão da seção anterior, você precisa entrar no Azure Machine Learning Studio. Você precisa de sua ID do espaço de trabalho e de um token de autorização para entrar. Para localizar esses valores e inicializar as variáveis do AzureML com eles:

Selecione **Configurações** no menu à esquerda. Observe a **ID do ESPAÇO DE TRABALHO**. ![2](./media/linux-dsvm-walkthrough/workspace-id.png)

Selecione **Tokens de Autorização** no menu de sobrecarga e observe o **Token de Autorização Primário**.![3](./media/linux-dsvm-walkthrough/workspace-token.png)

Carregue o pacote **AzureML** e defina os valores das variáveis com seu token e ID do espaço de trabalho na sessão R da DSVM:

    require(AzureML)
    wsAuth = "<authorization-token>"
    wsID = "<workspace-id>"


Iremos simplificar o modelo para tornar esta demonstração mais fácil de implementar. Selecione as três variáveis na árvore de decisão mais próximas da raiz e compile uma nova árvore usando apenas essas três variáveis:

    colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
    smallTrainSet <- trainSet[, colNames]
    smallTestSet <- testSet[, colNames]
    model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

Precisamos de uma função de previsão que usa os recursos como uma entrada e retorna os valores previstos:

    predictSpam <- function(char_freq_dollar, word_freq_remove, word_freq_hp) {
        predictDF <- predict(model.rpart, data.frame("char_freq_dollar" = char_freq_dollar,
        "word_freq_remove" = word_freq_remove, "word_freq_hp" = word_freq_hp))
        return(colnames(predictDF)[apply(predictDF, 1, which.max)])
    }

Publique a função predictSpam para o AzureML usando a função **publishWebService** :

    spamWebService <- publishWebService("predictSpam",
        "spamWebService",
        list("char_freq_dollar"="float", "word_freq_remove"="float","word_freq_hp"="float"),
        list("spam"="int"),
        wsID, wsAuth)

Essa função usa a **predictSpam**, cria um serviço Web denominado **spamWebService** com entradas e saídas definidas e retorna informações sobre o novo ponto de extremidade.

Exiba os detalhes do serviço Web publicado, incluindo seu ponto de extremidade de API e acesse as chaves com o comando:

    spamWebService[[2]]

Para experimentar nas primeiras 10 linhas do conjunto de teste:

    consumeDataframe(spamWebService$endpoints[[1]]$PrimaryKey, spamWebService$endpoints[[1]]$ApiLocation, smallTestSet[1:10, 1:3])


## <a name="use-other-tools-available"></a>Usar outras ferramentas disponíveis
As seções restantes mostram como usar algumas das ferramentas instaladas na VM da Ciência de Dados do Linux. Aqui está a lista das ferramentas analisadas:

* XGBoost
* Python
* Jupyterhub
* Rattle
* PostgreSQL e Squirrel SQL
* SQL Server Data Warehouse

## <a name="xgboost"></a>XGBoost
[XGBoost](https://xgboost.readthedocs.org/en/latest/) : uma ferramenta que fornece implementação de árvore aumentada rápida e precisa.

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

O XGBoost também pode chamar a partir do python ou de uma linha de comando.

## <a name="python"></a>Python
Para o desenvolvimento com o Python, as distribuições do Anaconda Python 2.7 e 3.5 foram instaladas na DSVM.

> [!NOTE]
> A distribuição Anaconda inclui o [Condas](http://conda.pydata.org/docs/index.html), que pode ser usado para criar ambientes personalizados para o Python com versões diferentes e/ou pacotes instalados.
>
>

Iremos transmitir o conjunto de dados baseado em spam e classificar os emails com máquinas do vetor de suporte no scikit-learn:

    import pandas
    from sklearn import svm    
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Para fazer previsões:

    clf.predict(X.ix[0:20, :])

Para mostrar como publicar um ponto de extremidade do AzureML, criaremos um modelo mais simples das três variáveis como fizemos quando publicamos o modelo R anteriormente.

    X = data.ix[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Para publicar o modelo para o AzureML:

    # Publish the model.
    workspace_id = "<workspace-id>"
    workspace_token = "<workspace-token>"
    from azureml import services
    @services.publish(workspace_id, workspace_token)
    @services.types(char_freq_dollar = float, word_freq_remove = float, word_freq_hp = float)
    @services.returns(int) # 0 or 1
    def predictSpam(char_freq_dollar, word_freq_remove, word_freq_hp):
        inputArray = [char_freq_dollar, word_freq_remove, word_freq_hp]
        return clf.predict(inputArray)

    # Get some info about the resulting model.
    predictSpam.service.url
    predictSpam.service.api_key

    # Call the model
    predictSpam.service(1, 1, 1)

> [!NOTE]
> Isso só está disponível para o python 2.7 e ainda não é suportado na versão 3.5. Execute com **/anaconda/bin/python2.7**.
>
>

## <a name="jupyterhub"></a>Jupyterhub
A distribuição Anaconda na DSVM vem com um bloco de anotações do Jupyter, um ambiente de plataforma cruzada para compartilhar o Python, R ou o código Julia e a análise. O notebook Jupyter é acessado com o JupyterHub. Você entra usando seu nome de usuário local do Linux e a senha em ***https://\<nome DNS da VM ou Endereço IP\>:8000/***. Todos os arquivos de configuração para o JupyterHub são encontrados no diretório **/etc/jupyterhub**.

Vários blocos de anotações de exemplo já estão instalados na VM:

* Consulte o [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb) para ver um bloco de anotações do Python de exemplo.
* Consulte o [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) para obter um bloco de anotações do **R** de exemplo.
* Consulte o [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb) para obter outro bloco de anotações do **Python** de exemplo.

> [!NOTE]
> O idioma Julia também está disponível na linha de comando na VM da Ciência de Dados do Linux.
>
>

## <a name="rattle"></a>Rattle
[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (a Ferramenta Analítica do R Fácil de Aprender) é uma ferramenta gráfica do R para a mineração de dados. Ela tem uma interface simples que facilita carregar, explorar e transformar os dados, compilar e avaliar os modelos.  O artigo [Rattle: Uma GUI da Mineração de Dados para o R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) fornece um passo a passo que demonstre seus recursos.

Instale e inicie o Rattle com os seguintes comandos:

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

> [!NOTE]
> A instalação não é necessária na DSVM. Mas o Rattle pode solicitar que você instale pacotes adicionais ao ser carregado.
>
>

O Rattle usa uma interface baseada em guias. A maioria das guias corresponde às etapas no [Processo da Ciência de Dados](https://azure.microsoft.com/documentation/learning-paths/data-science-process/), como carregar os dados ou explorá-los. O processo da ciência de dados flui da esquerda para a direita nas guias. Mas a última guia contém um log dos comandos do R executados pelo Rattle.

Para carregar e configurar o conjunto de dados:

* Para carregar o arquivo, selecione a guia **Dados** , em seguida,
* Escolha o seletor ao lado do **Nome de arquivo** e escolha **spambaseHeaders.data**.
* Para carregar o arquivo. selecione **Executar** na linha superior de botões. Você deve ver um resumo de cada coluna, incluindo o tipo de dados identificado, se é uma entrada, um destino ou outro tipo de variável, e o número de valores exclusivos.
* O Rattle identifica corretamente a coluna de **spam** como o destino. Selecione a coluna de spam e defina o **Tipo de Dados de Destino** para **Categórico**.

Para explorar os dados:

* Selecione a guia **Explorar** .
* Clique em **Resumo** e em **Executar** para ver algumas informações sobre os tipos de variáveis e algumas estatísticas resumidas.
* Para exibir os outros tipos de estatísticas sobre cada variável, selecione outras opções, como **Descrever** ou **Básico**.

A guia **Explorar** também permite gerar várias plotagens criteriosas. Para plotar um histograma dos dados:

* Selecione **Distribuições**.
* Verifique o **Histograma** quanto a **word_freq_remove** e **word_freq_you**.
* Selecione **Executar**. Você deverá ver duas plotagens de densidade em uma janela de gráfico, na qual fica claro que a palavra "you" aparece com muito mais frequência nos emails que "remove".

As plotagens de correlação também são interessantes. Para criar uma:

* Escolha **Correlação** como o **Tipo** e
* Selecione **Executar**.
* O Rattle avisa que ele recomenda um máximo de 40 variáveis. Selecione **Sim** para exibir a plotagem.

Há algumas correlações interessantes que surgem: "tecnologia" é muito correlacionado a "HP" e "laboratórios", por exemplo. Também está muito correlacionado a "650", porque o código de área dos doadores do conjunto de dados é 650.

Os valores numéricos para as correlações entre as palavras estão disponíveis na janela Explorar. É interessante observar, por exemplo, que "tecnologia" está negativamente correlacionado a "seu" e "dinheiro".

O Rattle pode transformar o conjunto de dados para lidar com alguns problemas comuns. Por exemplo, ele permite que você redimensione os recursos, atribua os valores ausentes, lide com os valores atípicos e remova as variáveis ou observações sem dados. O Rattle também pode identificar regras de associação entre as observações e/ou variáveis. Essas guias estão fora do escopo deste passo a passo de introdução.

O Rattle também pode executar a análise de cluster. Iremos excluir alguns recursos para facilitar a leitura da saída. Na guia **Dados**, escolha **Ignorar** ao lado de cada uma das variáveis, exceto esses 10 itens:

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

Em seguida, vá para a guia **Cluster**, escolha **KMeans** e defina o *Número de clusters* para 4. Então, **Executar**. Os resultados são exibidos na janela de saída. Um cluster tem alta frequência de "george" e "hp", e provavelmente é um email corporativo legítimo.

Para compilar um modelo de aprendizado de máquina da árvore de decisão simples:

* Selecione a guia **Modelo** ,
* Escolha **Árvore** como o **Tipo**.
* Selecione **Executar** para exibir a árvore em formato de texto na janela de saída.
* Selecione o botão **Desenhar** para exibir uma versão gráfica. Isto é bastante semelhante à árvore obtida anteriormente usando *rpart*.

Um dos recursos interessantes do Rattle é sua capacidade de executar vários métodos de aprendizado de máquina e avaliá-los rapidamente. Este é o procedimento:

* Escolha **Todos** para o **Tipo**.
* Selecione **Executar**.
* Depois de concluir, você pode clicar em qualquer **Tipo**, como **SVM**, e exibir os resultados.
* Você também pode comparar o desempenho dos modelos no conjunto de validação usando a guia **Avaliar** . Por exemplo, a seleção **Matriz do Erro** mostra a matriz de confusão, erro geral e erro de classe média para cada modelo no conjunto de validação.
* Você também pode plotar as curvas ROC, executar a análise de sensibilidade e fazer outros tipos de avaliações do modelo.

Após terminar de compilar os modelos, selecione a guia **Log** para exibir o código do R executado pelo Rattle durante a sessão. Você pode selecionar o botão **Exportar** para salvá-lo.

> [!NOTE]
> Há um bug na versão atual do Rattle. Para modificar o script ou usá-lo para repetir as etapas posteriormente, você deve inserir um caractere # na frente de *Exportar este log... * no texto do log.
>
>

## <a name="postgresql--squirrel-sql"></a>PostgreSQL e Squirrel SQL
A DSVM vem com o PostgreSQL instalado. O PostgreSQL é um banco de dados relacional sofisticado de fonte aberta. Esta seção mostra como carregar nosso conjunto de dados de spam no PostgreSQL e consultá-lo.

Antes de carregar os dados, você precisa permitir a autenticação de senha a partir do host local. Em um prompt de comando:

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

Na parte inferior do arquivo de configuração, há várias linhas que detalham as conexões permitidas:

    # "local" is for Unix domain socket connections only
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

Altere a linha "conexões locais IPv4" para usar md5 em vez de ident, para que possamos fazer logon usando um nome de usuário e senha:

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

E reinicie o serviço postgres:

    sudo systemctl restart postgresql

Para iniciar o psql, um terminal interativo do PostgreSQL, como o usuário postgres interno, execute o seguinte comando em um prompt:

    sudo -u postgres psql

Crie uma nova conta de usuário, usando o mesmo nome de usuário da conta do Linux com a qual você está conectado no momento, e atribua uma senha:

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

Então, faça logon no psql como seu usuário:

    psql

E importe os dados para um novo banco de dados:

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

Agora, iremos explorar os dados e executar algumas consultas usando o **Squirrel SQL**, uma ferramenta gráfica que permite interagir com os bancos de dados por meio de um driver JDBC.

Para começar, inicie o Squirrel SQL no menu Aplicativos. Para configurar o driver:

* Selecione **Windows** e **Exibir Drivers**.
* Clique com o botão direito do mouse em **PostgreSQL** e selecione **Modificar Driver**.
* Selecione **Caminho de classe Extra** e **Adicionar**.
* Digite ***/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar*** para o **Nome de Arquivo** e
* Selecione **Abrir**.
* Escolha Listar Drivers e selecione **org.postgresql.Driver** no **Nome da Classe** e **OK**.

Para configurar a conexão com o servidor local:

* Selecione **Windows** e **Exibir Aliases.**
* Escolha o botão **+** para criar um novo alias.
* Nomeie-o como *Banco de dados de spam* e escolha **PostgreSQL** na lista suspensa **Driver**.
* Define a URL como *jdbc:postgresql://localhost/spam*.
* Insira seu *nome de usuário* e *senha*.
* Clique em **OK**.
* Para abrir a janela **Conexão**, clique duas vezes no alias do ***Banco de dados de spam***.
* Selecione **Conectar**.

Para executar algumas consultas:

* Selecione a guia **SQL** .
* Insira uma consulta simples, como `SELECT * from data;` , na caixa de texto da consulta na parte superior da guia SQL.
* Pressione **Ctrl-Enter** para executá-la. Por padrão, o Squirrel SQL retorna as primeiras 100 linhas de sua consulta.

Há muito mais consultas que você pode executar para explorar esses dados. Por exemplo, como a frequência da palavra *make* difere entre o spam e o ham?

    SELECT avg(word_freq_make), spam from data group by spam;

Ou quais são as características de email que geralmente contêm *3º*?

    SELECT * from data order by word_freq_3d desc;

A maioria dos emails com uma alta ocorrência de *3º* aparentemente é spam, portanto, pode ser um recurso útil para compilar um modelo de previsão para classificar os emails.

Se você quiser executar o aprendizado de máquina com os dados armazenados em um banco de dados PostgreSQL, considere usar o [MADlib](http://madlib.incubator.apache.org/).

## <a name="sql-server-data-warehouse"></a>SQL Server Data Warehouse
O SQL Data Warehouse do Azure é um banco de dados baseado em nuvem e expansível com capacidade de processar volumes imensos de dados, relacionais e não relacionais. Para obter mais informações, consulte [O que é Azure SQL Data Warehouse?](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Para conectar o data warehouse e criar a tabela, execute o seguinte comando em um prompt de comando:

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

Em seguida, no prompt do sqlcmd:

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Copie os dados com o bcp:

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

> [!NOTE]
> As terminações de linha no arquivo baixado estão no estilo Windows, mas o bcp espera o estilo UNIX, portanto, precisamos informar isso ao bcp com o sinalizador -r.
>
>

E consulte com o sqlcmd:

    select top 10 spam, char_freq_dollar from spam;
    GO

Você também pode consultar com o Squirrel SQL. Siga as etapas semelhantes para o PostgreSQL, usando o Driver JDBC do Microsoft MSSQL Server, que pode ser encontrado em ***/usr/share/java/jdbcdrivers/sqljdbc42.jar***.

## <a name="next-steps"></a>Próximas etapas
Para obter uma visão geral dos tópicos que explicam as tarefas que compõem o processo de Ciência de dados no Azure, confira [Processo de Ciência de Dados de Equipe](http://aka.ms/datascienceprocess).

Para obter uma descrição de outras orientações de ponta a ponta que demonstram as etapas no Processo da Ciência de Dados de Equipe para cenários específicos, consulte [Explicações Passo a Passo do Processo da Ciência de Dados de Equipe](../team-data-science-process/walkthroughs.md). As orientações também mostram como combinar ferramentas e serviços de nuvem e locais em um fluxo de trabalho ou pipeline para criar um aplicativo inteligente.