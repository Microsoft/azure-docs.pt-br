---
title: 'Executar script R: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo executar script R no Azure Machine Learning designer para executar código R personalizado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 12/17/2020
ms.openlocfilehash: bdd7fd8e19bf2de6d0b3c6b2edd4515771fae237
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98118992"
---
# <a name="execute-r-script-module"></a>Executar módulo de script R

Este artigo descreve como usar o módulo executar script R para executar o código R em seu pipeline de Azure Machine Learning designer.

Com o R, você pode executar tarefas que não têm suporte em módulos existentes, como: 
- Criar transformações de dados personalizadas
- Use suas próprias métricas para avaliar previsões
- Crie modelos usando algoritmos que não são implementados como módulos autônomos no designer

## <a name="r-version-support"></a>Suporte à versão do R

O designer de Azure Machine Learning usa a distribuição CRAN (rede de arquivos R abrangente) de R. A versão atualmente usada é CRAN 3.5.1.

## <a name="supported-r-packages"></a>Pacotes de R com suporte

O ambiente do R é pré-instalado com mais de 100 pacotes. Para obter uma lista completa, consulte a seção [pacotes de R pré-instalados](#preinstalled-r-packages).

Você também pode adicionar o código a seguir a qualquer módulo executar script R para ver os pacotes instalados.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```
> [!NOTE]
> Se o pipeline contiver vários módulos executar script R que precisam de pacotes que não estão na lista pré-instalada, instale os pacotes em cada módulo. 

## <a name="installing-r-packages"></a>Instalando pacotes R
Para instalar pacotes de R adicionais, use o `install.packages()` método. Os pacotes são instalados para cada módulo executar script R. Eles não são compartilhados entre outros módulos executar script R.

> [!NOTE]
> **Não** é recomendável instalar o pacote R do grupo de script. É recomendável instalar pacotes diretamente no editor de scripts.
> Especifique o repositório CRAN quando você estiver instalando pacotes, como `install.packages("zoo",repos = "http://cran.us.r-project.org")` .

> [!WARNING]
> O módulo excute R script não dá suporte à instalação de pacotes que exigem compilação nativa, como `qdap` o pacote, que requer Java e `drc` pacote que requer C++. Isso ocorre porque esse módulo é executado em um ambiente pré-instalado com permissão de não administrador.
> Não instale pacotes pré-criados no/para Windows, pois os módulos de designer estão em execução no Ubuntu. Para verificar se um pacote é pré-compilado no Windows, você pode ir para [Cran](https://cran.r-project.org/) e pesquisar seu pacote, baixar um arquivo binário de acordo com seu sistema operacional e verificar a **compilação:** Part no arquivo de **Descrição** . Veja a seguir um exemplo: :::image type="content" source="media/module/r-package-description.png" alt-text="Descrição do pacote R" lightbox="media/module/r-package-page.png":::

Este exemplo mostra como instalar o Zoo:
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Note that functions dependent on the X11 library,
# such as "View," are not supported because the X11 library
# is not preinstalled.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
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
 > [!NOTE]
 > Antes de instalar um pacote, verifique se ele já existe, para que você não repita uma instalação. As instalações repetitivas podem fazer com que as solicitações de serviço Web expirem o tempo limite.     

## <a name="access-to-registered-dataset"></a>Acesso ao DataSet registrado

Você pode consultar o seguinte código de exemplo para acessar os [conjuntos de valores registrados](../how-to-create-register-datasets.md) em seu espaço de trabalho:

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  run = get_current_run()
  ws = run$experiment$workspace
  dataset = azureml$core$dataset$Dataset$get_by_name(ws, "YOUR DATASET NAME")
  dataframe2 <- dataset$to_pandas_dataframe()
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="uploading-files"></a>Carregando arquivos
O módulo executar script R dá suporte ao carregamento de arquivos usando o SDK do R Azure Machine Learning.

O exemplo a seguir mostra como carregar um arquivo de imagem em executar script R:
```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")

  # Generate a jpeg graph
  img_file_name <- "rect.jpg"
  jpeg(file=img_file_name)
  example(rect)
  dev.off()

  upload_files_to_run(names = list(file.path("graphic", img_file_name)), paths=list(img_file_name))


  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

Depois que a execução do pipeline for concluída, você poderá visualizar a imagem no painel direito do módulo.

> [!div class="mx-imgBorder"]
> ![Visualização da imagem carregada](media/module/upload-image-in-r-script.png)

## <a name="how-to-configure-execute-r-script"></a>Como configurar o executar script R

O módulo executar script R contém um código de exemplo como um ponto de partida.

![Diagrama de entradas para um módulo R](media/module/execute-r-script.png)

Os conjuntos de dados armazenados no designer são convertidos automaticamente em um quadro do R data quando carregados com esse módulo.

1.  Adicione o módulo **Executar script R** ao seu pipeline.  

1. Conecte as entradas que o script precisa. As entradas são opcionais e podem incluir dados e código de R adicional.

    * **DataSet1**: referencia a primeira entrada como `dataframe1` . O conjunto de dados de entrada deve ser formatado como um arquivo CSV, TSV ou ARFF. Ou você pode conectar um conjunto de Azure Machine Learning.

    * **Dataset2**: referencia a segunda entrada como `dataframe2` . Esse conjunto de e também deve ser formatado como um arquivo CSV, TSV ou ARFF ou como um conjunto de Azure Machine Learning.

    * **Pacote de script**: a terceira entrada aceita arquivos. zip. Um arquivo compactado pode conter vários arquivos e vários tipos de arquivo.

1. Na caixa de texto **script r** , digite ou cole um script R válido.

    > [!NOTE]
    > Tenha cuidado ao escrever seu script. Verifique se não há erros de sintaxe, como o uso de variáveis não declaradas ou de módulos ou funções não importadas. Preste atenção extra à lista de pacotes pré-instalados no final deste artigo. Para usar pacotes que não estão listados, instale-os em seu script. Um exemplo é `install.packages("zoo",repos = "http://cran.us.r-project.org")`.
    
    Para ajudá-lo a começar, a caixa de texto **script R** é preenchida previamente com o código de exemplo, que você pode editar ou substituir.
    
    ```R
    # R version: 3.5.1
    # The script MUST contain a function named azureml_main,
    # which is the entry point for this module.

    # Note that functions dependent on the X11 library,
    # such as "View," are not supported because the X11 library
    # is not preinstalled.
    
    # The entry point function MUST have two input arguments.
    # If the input port is not connected, the corresponding
    # dataframe argument will be null.
    #   Param<dataframe1>: a R DataFrame
    #   Param<dataframe2>: a R DataFrame
    azureml_main <- function(dataframe1, dataframe2){
    print("R script run.")

    # If a .zip file is connected to the third input port, it's
    # unzipped under "./Script Bundle". This directory is added
    # to sys.path.

    # Return datasets as a Named List
    return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

    A função de ponto de entrada deve ter os argumentos de entrada `Param<dataframe1>` e `Param<dataframe2>` , mesmo quando esses argumentos não são usados na função.

    > [!NOTE]
    > Os dados passados para o módulo executar script R são referenciados como `dataframe1` e `dataframe2` , que é diferente do designer de Azure Machine Learning (a referência de designer como `dataset1` , `dataset2` ). Certifique-se de que os dados de entrada sejam referenciados corretamente em seu script.  
 
    > [!NOTE]
    > O código R existente pode precisar de alterações secundárias para ser executado em um pipeline de designer. Por exemplo, os dados de entrada que você fornecer no formato CSV devem ser explicitamente convertidos em um DataSet antes que você possa usá-lo em seu código. Os tipos de dados e colunas usados na linguagem R também são diferentes de algumas maneiras dos tipos de dados e colunas usados no designer.

1. Se o seu script for maior que 16 KB, use a porta do **pacote de script** para evitar erros como *CommandLine excede o limite de 16597 caracteres*. 
    
    1. Agrupe o script e outros recursos personalizados em um arquivo zip.
    1. Carregue o arquivo zip como um **conjunto** de um arquivo para o estúdio. 
    1. Arraste o módulo DataSet da lista *DataSets* no painel do módulo à esquerda na página criação do designer. 
    1. Conecte o módulo DataSet à porta do **pacote de script** do módulo **Executar script R** .
    
    Veja a seguir o código de exemplo para consumir o script no pacote de script:

    ```R
    azureml_main <- function(dataframe1, dataframe2){
    # Source the custom R script: my_script.R
    source("./Script Bundle/my_script.R")

    # Use the function that defined in my_script.R
    dataframe1 <- my_func(dataframe1)

    sample <- readLines("./Script Bundle/my_sample.txt")
    return (list(dataset1=dataframe1, dataset2=data.frame("Sample"=sample)))
    }
    ```

1.  Para **semente aleatória**, insira um valor a ser usado dentro do ambiente de R como o valor de semente aleatória. Este parâmetro é equivalente a chamar `set.seed(value)` no código de R.  

1. Enviar o pipeline.  

## <a name="results"></a>Resultados

A execução de módulos de script R pode retornar várias saídas, mas elas devem ser fornecidas como quadros de dados do R. O designer converte automaticamente os quadros de dados em conjuntos para obter compatibilidade com outros módulos.

Mensagens e erros padrão do R são retornados para o log do módulo.

Se você precisar imprimir resultados no script R, poderá encontrar os resultados impressos em **70_driver_log** na guia **saídas + logs** no painel direito do módulo.

## <a name="sample-scripts"></a>Scripts de exemplo

Há várias maneiras de estender seu pipeline usando scripts R personalizados. Esta seção fornece código de exemplo para tarefas comuns.


### <a name="add-an-r-script-as-an-input"></a>Adicionar um script R como uma entrada

O módulo executar script R dá suporte a arquivos de script R arbitrários como entradas. Para usá-los, você deve carregá-los em seu espaço de trabalho como parte do arquivo. zip.

1. Para carregar um arquivo. zip que contém o código R para seu espaço de trabalho, vá para a página de ativos de **conjuntos** de arquivos. Selecione **criar conjunto** de texto e, em seguida, selecione **do arquivo local** e a opção tipo de conjunto de **arquivos** .  

1. Verifique se o arquivo compactado aparece em **meus conjuntos** de arquivos na categoria **conjuntos** de arquivos na árvore de módulos à esquerda.

1.  Conecte o conjunto de dados à porta de entrada do **pacote de script** .

1. Todos os arquivos no arquivo. zip estão disponíveis durante o tempo de execução do pipeline. 

    Se o arquivo de pacote de script contiver uma estrutura de diretório, a estrutura será preservada. Mas você deve alterar seu código para preceder o **pacote Directory./script** para o caminho.

### <a name="process-data"></a>Processar dados

O exemplo a seguir mostra como dimensionar e normalizar dados de entrada:

```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Note that functions dependent on the X11 library,
# such as "View," are not supported because the X11 library
# is not preinstalled.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  # If a .zip file is connected to the third input port, it's
  # unzipped under "./Script Bundle". This directory is added
  # to sys.path.
  series <- dataframe1$width
  # Find the maximum and minimum values of the width column in dataframe1
  max_v <- max(series)
  min_v <- min(series)
  # Calculate the scale and bias
  scale <- max_v - min_v
  bias <- min_v / dis
  # Apply min-max normalizing
  dataframe1$width <- dataframe1$width / scale - bias
  dataframe2$width <- dataframe2$width / scale - bias
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
 ```

### <a name="read-a-zip-file-as-input"></a>Ler um arquivo. zip como entrada

Este exemplo mostra como usar um conjunto de dados em um arquivo. zip como uma entrada para o módulo executar script R.

1. Crie o arquivo de dados no formato CSV e nomeie-o **mydatafile.csv**.
1. Crie um arquivo. zip e adicione o arquivo CSV ao arquivo morto.
1. Carregue o arquivo compactado em seu espaço de trabalho Azure Machine Learning. 
1. Conecte o conjunto de dados resultante à entrada **ScriptBundle** do módulo **Executar script R** .
1. Use o código a seguir para ler os dados CSV do arquivo compactado.

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

Você pode passar objetos R entre instâncias do módulo Executar Script R usando o mecanismo de serialização interna. Este exemplo pressupõe que você deseja mover o objeto do R chamado `A` entre dois módulos executar script r.

1. Adicione o primeiro módulo **Executar script R** ao seu pipeline. Em seguida, digite o seguinte código na caixa de texto **script R** para criar um objeto serializado `A` como uma coluna na tabela de dados de saída do módulo:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    A conversão explícita para o tipo de inteiro é feita porque a função de serialização gera dados no `Raw` formato R, ao qual o designer não dá suporte.

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

## <a name="preinstalled-r-packages"></a>Pacotes de R pré-instalados

Os seguintes pacotes de R pré-instalados estão disponíveis no momento:

| Pacote      | Versão    | 
|--------------|------------| 
| askpass      | 1,1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| base         | 3.5.1      | 
| base64enc    | 0.1-3      | 
| BH           | 1.69.0-1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0-6      | 
| boot         | 1.3-22     | 
| broom        | 0.5.2      | 
| callr        | 3.2.0      | 
| sinal de interpolação        | 6.0-84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| classe        | 7.3-15     | 
| cli          | 1.1.0      | 
| clipe        | 0.6.0      | 
| cluster      | 2.0.7-1    | 
| codetools    | 0.2-16     | 
| colorspace   | 1.4-1      | 
| compiler     | 3.5.1      | 
| crayon       | 1.3.4      | 
| curl         | 3.3        | 
| data.table   | 1.12.2     | 
| conjuntos de dados     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| evaluate     | 0,14       | 
| fansi        | 0.4.0      | 
| forcats      | 0.3.0      | 
| foreach      | 1.4.4      | 
| foreign      | 0,8-71     | 
| vinculação           | 1.3.1      | 
| gdata        | 2.18.0     | 
| Genéricos     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0-18     | 
| glue         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| elemento gráfico     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| grade         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| haven        | 2.1.0      | 
| highr        | 0,8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0,9-9      | 
| iterators    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1.23       | 
| labeling     | 0.3        | 
| lattice      | 0,20-38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | -hadoop2      | 
| magrittr     | 1.5        | 
| markdown     | 1          | 
| MASS         | 7.3-51.4   | 
| Matriz       | 1.2-17     | 
| methods      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| mime         | 0,7        | 
| ModelMetrics | 1.2.2      | 
| modelr       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1-140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
| openssl      | 1.4        | 
| parallel     | 3.5.1      | 
| pillar       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| progress     | 1.2.2      | 
| ps           | 1.3.0      | 
| purrr        | 0.3.2      | 
| quadprog     | 1.5-7      | 
| quantmod     | 0.4-15     | 
| R6           | 2.4.0      | 
| randomForest | 4.6-14     | 
| RColorBrewer | 1.1-2      | 
| Rcpp         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| readr        | 1.3.1      | 
| readxl       | 1.3.1      | 
| recipes      | 0.1.5      | 
| rematch      | 1.0.1      | 
| reprex       | 0.3.0      | 
| reshape2     | 1.4.3      | 
| reticulate   | 1.12       | 
| rlang        | 0.4.0      | 
| rmarkdown    | 1.13       | 
| ROCR         | 1.0-7      | 
| rpart        | 4.1-15     | 
| rstudioapi   | 0,1        | 
| rvest        | 0.3.4      | 
| scales       | 1.0.0      | 
| selectr      | 0.4-1      | 
| spatial      | 7.3-11     | 
| splines      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| stats        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringi      | 1.4.3      | 
| stringr      | 1.3.1      | 
| survival     | 2,44-1.1   | 
| sys          | 3.2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| tidyr        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043.102   | 
| tinytex      | 0.13       | 
| tools        | 3.5.1      | 
| tseries      | 0,10 a 47    | 
| TTR          | 0.23-4     | 
| utf8         | 1.1.4      | 
| utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| whisker      | 0.3-2      | 
| withr        | 2.1.2      | 
| xfun         | 0,8        | 
| xml2         | 1.2.0      | 
| xts          | 0.11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| zoo          | 1.8-6      | 

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning.