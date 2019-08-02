---
title: 'Executar script R: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo executar script R no serviço de Azure Machine Learning para executar o código R.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterlu
ms.date: 06/01/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 710d64b445953ae3124830931c8cbb9315d32b83
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875723"
---
# <a name="execute-r-script"></a>Executar script R

Este artigo descreve como usar o módulo **Executar script r** para executar o código r em seu experimento de interface visual.

Com o R, você pode executar tarefas que atualmente não são suportadas por módulos existentes, como: 
- Criar transformações de dados personalizadas
- Use suas próprias métricas para avaliar previsões
- Criar modelos usando algoritmos que não são implementados como módulos autônomos na interface visual

## <a name="r-version-support"></a>Suporte à versão do R

A interface visual do serviço de Azure Machine Learning usa a distribuição CRAN (ampla rede de arquivos R) de R. A versão atualmente usada é CRAN 3.5.1.

## <a name="supported-r-packages"></a>Pacotes de R com suporte

O ambiente de R é pré-instalado com mais de 100 pacotes. Para obter uma lista completa, consulte a seção [pacotes de R pré-instalados](#pre-installed-r-packages).

Você também pode adicionar o código a seguir a qualquer módulo **Executar script R** e ver os pacotes instalados.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>Instalando pacotes R
Para instalar pacotes de R adicionais, use `install.packages()` o método. Certifique-se de especificar o repositório CRAN. Os pacotes são instalados para cada módulo **Executar script r** e não são compartilhados entre outros módulos **Executar script r** .

Este exemplo mostra como instalar o Zoo:
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  
  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")
  library(zoo)
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="how-to-configure-execute-r-script"></a>Como configurar o executar script R

O módulo **Executar script R** contém um código de exemplo que você pode usar como ponto de partida. Para configurar o módulo **Executar script R** , forneça um conjunto de entradas e código a ser executado.

![Módulo R](media/module/execute-r-script.png)

Os conjuntos de dados armazenados na interface visual são automaticamente convertidos em um quadro de data do R quando carregados com esse módulo.

1.  Adicione o módulo **Executar script R** ao seu experimento.

    > [!NOTE]
    > Todos os dados passados para o módulo **Executar script R** são convertidos no `data.frame` formato R.

1. Conecte as entradas necessárias ao script. As entradas são opcionais e podem incluir dados e código de R adicional.

    * **DataSet1**: Referencie a primeira entrada como `dataframe1`. O conjunto de dados de entrada deve ser formatado como CSV, TSV, ARFF ou você pode conectar um conjunto de dados Azure Machine Learning.

    * **Dataset2**: Referencie a segunda entrada como `dataframe2`. Esse conjunto de e também deve ser formatado como um arquivo CSV, TSV, ARFF ou como um conjunto de Azure Machine Learning.

    * **Pacote de script**: A terceira entrada aceita arquivos ZIP. O arquivo compactado pode conter vários arquivos e vários tipos de arquivo.

1. Na caixa de texto **script r** , digite ou cole um script R válido.

    Para ajudá-lo a começar, a caixa de texto **script R** é preenchida previamente com o código de exemplo, que você pode editar ou substituir.

    * O script deve conter uma função chamada `azureml_main`, que é o ponto de entrada para esse módulo.

    * A função de ponto de entrada pode conter até dois argumentos de `Param<dataframe1>` entrada: e`Param<dataframe2>`
 
    > [!NOTE]
    >  O código R existente pode precisar de alterações secundárias para ser executado em um experimento de interface visual. Por exemplo, os dados de entrada que você fornecer no formato CSV devem ser explicitamente convertidos em um DataSet antes que você possa usá-lo em seu código. Os tipos de dados e colunas usados na linguagem R também são diferentes de algumas maneiras dos tipos de dados e colunas usados na interface visual.

1.  **Semente aleatória**: Digite um valor a ser usado dentro do ambiente de R como o valor de semente aleatória. Esse parâmetro é equivalente a chamar `set.seed(value)` no código R.  

1. Execute o experimento.  

## <a name="results"></a>Resultados

Os módulos **Executar script r** podem retornar várias saídas, mas devem ser fornecidas como quadros de dados do r. Os quadros de dados são convertidos automaticamente em conjuntos de elementos de interface visual para compatibilidade com outros módulos.

Mensagens e erros padrão do R são retornados para o log do módulo.

## <a name="sample-scripts"></a>Scripts de exemplo

Há várias maneiras de estender seu experimento usando o script R personalizado.  Esta seção fornece código de exemplo para tarefas comuns.


### <a name="add-r-script-as-an-input"></a>Adicionar script R como uma entrada

O módulo **Executar script r** dá suporte a arquivos de script r arbitrários como entradas. Para fazer isso, eles devem ser carregados em seu espaço de trabalho como parte do arquivo ZIP.

1. Para carregar um arquivo ZIP que contém o código R para seu espaço de trabalho, clique em **novo**, clique em **conjunto**de e, em seguida, selecione **do arquivo local** e a opção **arquivo zip** .  

1. Verifique se o arquivo compactado está disponível na lista **DataSets salvos** .

1.  Conecte o conjunto de dados à porta de entrada do **pacote de script** .

1. Todos os arquivos contidos no arquivo ZIP estão disponíveis durante o tempo de execução do experimento. 

    Se o arquivo de pacote de script contiver uma estrutura de diretório, a estrutura será preservada. No entanto, você deve alterar seu código para preceder o **pacote Directory./script** para o caminho.

### <a name="process-data"></a>Processar dados

O exemplo a seguir mostra como dimensionar e normalizar dados de entrada:

```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.
# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  # If a zip file is connected to the third input port, it is
  # unzipped under "./Script Bundle". This directory is added
  # to sys.path.
  series <- dataframe1$width
  # find the maximum and minimum values of width column in dataframe1
  max_v <- max(series)
  min_v <- min(series)
  # calculate the scale and bias
  scale <- max_v - min_v
  bias <- min_v / dis
  # apply min-max normalizing
  dataframe1$width <- dataframe1$width / scale - bias
  dataframe2$width <- dataframe2$width / scale - bias
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
 ```

### <a name="read-a-zip-file-as-input"></a>Ler um arquivo ZIP como entrada

Este exemplo mostra como usar um conjunto de dados em um arquivo ZIP como uma entrada para o módulo **Executar script R** .

1. Crie o arquivo de dados no formato CSV e nomeie-o como "mydatafile. csv".
1. Crie um arquivo ZIP e adicione o arquivo CSV ao arquivo morto.
1. Carregue o arquivo compactado em seu espaço de trabalho Azure Machine Learning. 
1. Conecte o conjunto de dados resultante à entrada **ScriptBundle** do módulo **Executar script R** .
1. Usando o código a seguir para ler os dados CSV do arquivo compactado.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>Replicar linhas

Este exemplo mostra como replicar registros positivos em um DataSet para balancear o exemplo:

```R
azureml_main <- function(dataframe1, dataframe2){
  data.set <- dataframe1[dataframe1[,1]==-1,]  
  # positions of the positive samples
  pos <- dataframe1[dataframe1[,1]==1,]
  # replicate the positive samples to balance the sample  
  for (i in 1:20) data.set <- rbind(data.set,pos)  
  row.names(data.set) <- NULL
  # Return datasets as a Named List
  return(list(dataset1=data.set, dataset2=dataframe2))
}
```

### <a name="pass-r-objects-between-execute-r-script-modules"></a>Passar objetos R entre executar módulos de script R

Você pode passar objetos R entre instâncias do módulo **Executar script r** usando o mecanismo de serialização interna. Este exemplo pressupõe que você deseja mover o objeto do R chamado `A` entre dois módulos **Executar script r** .

1. Adicione o primeiro módulo **Executar script r** ao seu experimento e digite o seguinte código na caixa de texto **script r** para criar um objeto `A` serializado como uma coluna na tabela de dados de saída do módulo:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    A conversão explícita para o tipo de inteiro é feita porque a função de serialização gera dados `Raw` no formato R, que não tem suporte na interface visual.

1. Adicione uma segunda instância do módulo **Executar script R** e conecte-a à porta de saída do módulo anterior.

1. Digite o código a seguir na caixa de texto **script R** para extrair `A` o objeto da tabela de dados de entrada. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>Pacotes de R pré-instalados

A lista atual de pacotes de R pré-instalados disponíveis para uso:

|              |            | 
|--------------|------------| 
| Pacote      | Versão    | 
| askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| polybase         | 3.5.1      | 
| base64enc    | 0,1-3      | 
| BH           | 1.69.0-1   | 
| associador        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1,0-6      | 
| ciar         | 1.3-22     | 
| Broom        | 0.5.2      | 
| chamador        | 3.2.0      | 
| acento        | 6.0-84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| classe        | 7.3-15     | 
| CLI          | 1.1.0      | 
| clipe        | 0.6.0      | 
| cluster      | 2.0.7-1    | 
| codetools    | 0.2-16     | 
| colorspace   | 1.4-1      | 
| Compiler     | 3.5.1      | 
| lápis       | 1.3.4      | 
| curl         | 3.3        | 
| Data. tabela   | 1.12.2     | 
| datasets     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| avaliar     | 0,14       | 
| fansi        | 0.4.0      | 
| forcats      | 0.3.0      | 
| foreach      | 1.4.4      | 
| intercâmbio      | 0,8-71     | 
| vinculação           | 1.3.1      | 
| gdata        | 2.18.0     | 
| genéricos     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0-18     | 
| cola         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| gráficos     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| grade         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| foram        | 2.1.0      | 
| mais alto        | 0.8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0,9-9      | 
| iteradores    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | -2, 23E-15    | 
| knitr        | 1.23       | 
| rotulagem     | 0.3        | 
| malha      | 0,20-38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1.5        | 
| Markdown     | 1          | 
| EM MASSA         | 7.3-51.4   | 
| Matriz       | 1.2-17     | 
| Maneiras      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| MIME         | 0,7        | 
| ModelMetrics | 1.2.2      | 
| modelador       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1-140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
| openssl      | 1.4        | 
| completa     | 3.5.1      | 
| pilares       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| progresso     | 1.2.2      | 
| ps           | 1.3.0      | 
| purrr        | 0.3.2      | 
| quadprog     | 1,5-7      | 
| quantmod     | 0.4-15     | 
| R6           | 2.4.0      | 
| randomForest | 4.6-14     | 
| RColorBrewer | 1.1-2      | 
| Rcpp         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| leitura        | 1.3.1      | 
| readxl       | 1.3.1      | 
| receitas      | 0.1.5      | 
| recorrespondência      | 1.0.1      | 
| reprex       | 0.3.0      | 
| reshape2     | 1.4.3      | 
| reticulate   | 1,12       | 
| rlang        | 0.4.0      | 
| rmarkdown    | 1,13       | 
| ROCR         | 1.0-7      | 
| rpart        | 4.1-15     | 
| rstudioapi   | 0,1        | 
| rvest        | 0.3.4      | 
| reduz       | 1.0.0      | 
| Selecione      | 0.4-1      | 
| espacial      | 7.3-11     | 
| linhas      | 3.5.1      | 
| QUADRADO      | 2017.10-1  | 
| stats        | 3.5.1      | 
| stats4       | 3.5.1      | 
| Cadeia de caracteres      | 1.4.3      | 
| Cadeia de caracteres      | 1.3.1      | 
| sobrevivência     | 2,44-1.1   | 
| sys          | 3.2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| limpar        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| Data de início     | 3043.102   | 
| tinytex      | 0,13       | 
| ferramentas        | 3.5.1      | 
| tseries      | 0,10 a 47    | 
| TTR          | 0.23-4     | 
| utf8         | 1.1.4      | 
| utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| caixa estreita      | 0.3-2      | 
| com        | 2.1.2      | 
| xfun         | 0.8        | 
| xml2         | 1.2.0      | 
| XTS          | 0.11-2     | 
| YAML         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| zoológico          | 1.8-6      | 

## <a name="next-steps"></a>Próximas etapas

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning serviço. 