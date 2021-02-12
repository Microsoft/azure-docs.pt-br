---
title: Usar R com Machine Learning Studio (clássico) – Azure
description: Use este tutorial de programação R para começar a usar o Azure Machine Learning Studio (clássico) em R para criar uma solução de previsão.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2019
ms.openlocfilehash: bca2f0229a15f44ff8f3589a9c1e80032036b97c
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95507209"
---
# <a name="get-started-with-azure-machine-learning-studio-classic-in-r"></a>Introdução ao Azure Machine Learning Studio (clássico) em R

**aplica-se a:** ![ Essa é uma marca de seleção, o que significa que este artigo se aplica a Machine Learning Studio (clássico). ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) ![ é um X, o que significa que este artigo se aplica a Azure Machine Learning ](../../../includes/media/aml-applies-to-skus/no.png)[ . Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

<!-- Stephen F Elston, Ph.D. -->
Neste tutorial, você aprenderá a usar o Azure Machine Learning Studio (clássico) para criar, testar e executar o código R. No final, você terá uma solução de previsão completa.

> [!div class="checklist"]
> * Criar código para limpeza e transformação de dados.
> * Analise as correlações entre várias das variáveis em nosso conjunto de nossos.
> * Crie um modelo de previsão de série temporal sazonal para leite de produção.


Machine Learning Studio (clássico) contém muitos módulos poderosos de aprendizado de máquina e de manipulação de dados. Com a linguagem de programação R, essa combinação fornece a escalabilidade e a facilidade de implantação do Machine Learning Studio (clássico) com a flexibilidade e análise profunda de R.

A previsão é um método analítico amplamente empregado e útil. Os usos comuns variam de prever as vendas de itens sazonais e determinar os níveis de inventário ideais para prever as variáveis macroeconômicas. A previsão normalmente é feita com modelos de série de tempo. Dados de série temporal são dados em que os valores têm um índice de tempo. O índice de tempo pode ser regular, por exemplo, todos os meses ou a cada minuto. O índice de tempo também pode ser irregular. Um modelo de série de tempo se baseia em dados de série de tempo. A linguagem de programação de R contém uma estrutura flexível e uma análise abrangente para dados de série temporal.

## <a name="get-the-data"></a>Obter os dados

Neste tutorial, você usa dados de preço e de produção de laticínios da Califórnia, que incluem informações mensais sobre a produção de vários produtos de laticínios e o preço do leite gordura, que é uma mercadoria de benchmark.

Os dados usados neste artigo, juntamente com os scripts do R, podem ser baixados de [MachineLearningSamples-notebooks/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples). Os dados no arquivo `cadairydata.csv` foram originalmente sintetizados a partir das informações disponíveis no site Universidade de [sites](https://dairymarkets.com)de Wisconsin.

## <a name="interact-with-r-language-in-machine-learning-studio-classic"></a><a id="mlstudio"></a>Interagir com a linguagem R no Machine Learning Studio (clássico)

Esta seção orienta você por algumas noções básicas de interação com a linguagem de programação R no ambiente Machine Learning Studio (clássico). A linguagem R fornece uma poderosa ferramenta para criar módulos de análise e manipulação de dados personalizados dentro do ambiente Machine Learning Studio (clássico).

Usaremos o RStudio para desenvolver, testar e depurar o código R em uma pequena escala. Esse código é então recortado e colado em um módulo [Executar script R][execute-r-script] pronto para ser executado no Machine Learning Studio (clássico).

### <a name="the-execute-r-script-module"></a>O módulo Executar Script R

Dentro de Machine Learning Studio (clássico), os scripts do R são executados no módulo [Executar script R][execute-r-script] . Um exemplo do módulo [Executar script R][execute-r-script] no Machine Learning Studio (clássico) é mostrado aqui.

 ![Captura de tela que mostra a linguagem de programação R: o módulo executar script R selecionado em Machine Learning Studio (clássico).](./media/r-quickstart/fig1.png)

A imagem anterior mostra algumas das principais partes do ambiente Machine Learning Studio (clássico) para trabalhar com o módulo [Executar script R][execute-r-script] :

* Os módulos do teste são mostrados no painel central.
* A parte superior do painel direito contém uma janela que você pode usar para exibir e editar seus scripts do R.
* A parte inferior do painel direito mostra algumas propriedades do [Executar script R][execute-r-script]. Você pode exibir os logs de erro e saída selecionando as áreas apropriadas deste painel.

Abordaremos o [script R execute][execute-r-script] com mais detalhes no restante deste artigo.

Quando você trabalha com funções de R complexas, recomendamos que você edite, teste e depure em RStudio. Assim como acontece com qualquer desenvolvimento de software, estenda o código incrementalmente e teste-o em casos de teste pequenos e simples. Em seguida, recorte e cole suas funções na janela de script R do módulo [Executar Script R][execute-r-script]. Essa abordagem permite que você aproveite o IDE (ambiente de desenvolvimento integrado) do RStudio e o poder do Machine Learning Studio (clássico).

#### <a name="execute-r-code"></a>Executar código R

Qualquer código R no módulo [Executar script r][execute-r-script] será executado quando você executar o experimento selecionando o botão **executar** . Quando a execução for concluída, uma marca de seleção aparecerá no ícone [Executar script R][execute-r-script] .

#### <a name="defensive-r-coding-for-machine-learning-studio-classic"></a>Codificação R defensiva para Machine Learning Studio (clássico)

Se você estiver desenvolvendo código R para, digamos, um serviço Web usando Machine Learning Studio (clássico), você deve, definitivamente, planejar como seu código lidará com uma entrada de dados inesperada e exceções. Para manter a clareza, não incluímos muito no modo de verificação ou tratamento de exceção na maioria dos exemplos de código mostrados. Conforme continuarmos, daremos vários exemplos de funções usando a funcionalidade de manipulação de exceção do R.

Se você precisar de um tratamento mais completo da manipulação de exceção de R, leia as seções aplicáveis do livro por Wickham listadas em [leituras adicionais](#appendixb).

#### <a name="debug-and-test-r-in-machine-learning-studio-classic"></a>Depurar e testar R em Machine Learning Studio (clássico)

Teste e depure seu código R em uma pequena escala em RStudio. Também há casos em que você precisará rastrear problemas de código R no mesmo [script r execute][execute-r-script] . Além disso, é uma boa prática verificar os resultados em Machine Learning Studio (clássico).

A saída da execução do código R e da plataforma Machine Learning Studio (clássica) é encontrada principalmente em Output. log. Algumas informações adicionais estão em Error. log.

Se ocorrer um erro no Machine Learning Studio (clássico) enquanto você executa o código R, seu primeiro curso de ação deve ser examinar Error. log. Esse arquivo pode conter mensagens de erro úteis para ajudá-lo a entender e corrigir o erro. Para exibir Error. log, selecione **Exibir log de erros** no painel Propriedades para [executar o script R][execute-r-script] que contém o erro.

Por exemplo, executamos o seguinte código R, com uma variável y indefinida, em um módulo [Executar script r][execute-r-script] .

```r
x <- 1.0
z <- x + y
```

Esse código não é executado, o que resulta em uma condição de erro. Selecionar **Exibir log de erros** no painel Propriedades produz a exibição a seguir.

  ![Captura de tela que mostra uma mensagem de erro pop-up.](./media/r-quickstart/fig2.png)

Parece que precisamos examinar output.g para ver a mensagem de erro de R. Selecione o módulo [Executar script R][execute-r-script] e, em seguida, selecione o item **Exibir saída. log** no painel Propriedades à direita. Uma nova janela do navegador é aberta e a seguinte mensagem de erro é exibida.

```output
[Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
---------- Start of error message from R ----------
object 'y' not found


object 'y' not found
----------- End of error message from R -----------
```

Essa mensagem de erro contém surpresas e claramente identifica o problema.

Para inspecionar o valor de qualquer objeto em R, você pode imprimir esses valores no arquivo output.log. As regras para examinar os valores de objeto são essencialmente as mesmas de uma sessão interativa de R. Por exemplo, se você inserir um nome de variável em uma linha, o valor do objeto será impresso no arquivo output. log.

#### <a name="packages-in-machine-learning-studio-classic"></a>Pacotes no Machine Learning Studio (clássico)

Machine Learning Studio (clássico) vem com mais de 350 pacotes de linguagem R pré-instalados. Você pode usar o código a seguir no módulo [Executar Script R][execute-r-script] para recuperar uma lista dos pacotes pré-instalados.

```r
data.set <- data.frame(installed.packages())
maml.mapOutputPort("data.set")
```

Se você não entender a última linha do código no momento, continue lendo. No restante deste artigo, discutiremos extensivamente o uso do R no ambiente Machine Learning Studio (clássico).

### <a name="introduction-to-rstudio"></a>Introdução ao RStudio

RStudio é um IDE amplamente usado para R. Usaremos o RStudio para editar, testar e depurar parte do código R usado neste guia. Depois que o código R é testado e pronto, você pode recortar e colar do editor RStudio em um módulo de [execução de script R][execute-r-script] Machine Learning Studio (clássico).

Se você não tiver a linguagem de programação R instalada em seu computador desktop, faça isso agora. Downloads gratuitos de linguagem R de software livre estão disponíveis na [Cran (rede de arquivos de r) abrangente](https://www.r-project.org/). Os downloads estão disponíveis para Windows, macOS e Linux/UNIX. Escolha um espelho próximo e siga as instruções de download. Além disso, o CRAN contém muitos pacotes úteis de análise e manipulação de dados.

Se você for novo no RStudio, baixe e instale a versão da área de trabalho. Você pode encontrar os downloads do RStudio para Windows, macOS e Linux/UNIX em [RStudio](http://www.rstudio.com/products/RStudio/). Siga as instruções fornecidas para instalar o RStudio em seu computador desktop.

Um tutorial de introdução ao RStudio está disponível em [usando o IDE do RStudio](https://support.rstudio.com/hc/sections/200107586-Using-RStudio).

Para obter mais informações sobre como usar o RStudio, consulte o [guia para a documentação do RStudio](#appendixa).

## <a name="get-data-in-and-out-of-the-execute-r-script-module"></a><a id="scriptmodule"></a>Obter dados de entrada e saída no módulo Executar Script R

Nesta seção, discutiremos como você obtém dados dentro e fora do módulo [Executar script R][execute-r-script] . Também examinaremos como lidar com vários tipos de dados lidos dentro e fora do módulo [Executar script R][execute-r-script] .

O código completo para esta seção está em [MachineLearningSamples-notebooks/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="load-and-check-data"></a>Carregar e verificar dados

#### <a name="load-the-dataset"></a><a id="loading"></a>Carregar o conjunto de dados

Vamos começar carregando o arquivo de **csdairydata.csv** em Machine Learning Studio (clássico).

1. Inicie seu ambiente de Machine Learning Studio (clássico).
1. Selecione **+ novo** na parte inferior esquerda da tela e selecione **conjunto** de espaço.
1. Selecione **do arquivo local** e, em seguida, selecione **procurar** para selecionar o arquivo.
1. Verifique se você selecionou o **arquivo CSV genérico com o cabeçalho (. csv)** como o tipo do conjunto de um.
1. Selecione a marca de seleção.
1. Depois que o conjunto de um for carregado, você deverá ver o novo conjunto de novos ao selecionar a guia conjuntos de banco de **valores** .

#### <a name="create-an-experiment"></a>Criar uma experiência

Agora que temos alguns dados em Machine Learning Studio (clássico), precisamos criar um experimento para fazer a análise.  

1. Selecione **+ novo** na parte inferior esquerda da tela **e selecione experimentar**  >  **experimento em branco**.
1. Nomeie seu experimento selecionando e modificando o **experimento criado no** título na parte superior da página. Por exemplo, altere-o para **análise de laticínios de CA**.
1. À esquerda da página experimento, selecione **DataSets Saved**  >  **My Datasets**. Você deve ver o arquivo de **cadairydata.csv** que você carregou anteriormente.
1. Arraste **csdairydata.csv DataSet** para o experimento.
1. Na caixa **Pesquisar itens de teste** na parte superior do painel esquerdo, digite [Executar script R][execute-r-script]. O módulo aparece na lista de pesquisa.
1. Arraste o módulo [Executar script R][execute-r-script] para o seu palete.
1. Conecte a saída do **conjunto** de dadoscsdairydata.csv à entrada mais à esquerda (**dataSet1**) de [Executar script R][execute-r-script].
1. Selecione **Salvar**.

Neste ponto, seu experimento deve ser semelhante a este exemplo.

![Diagrama que mostra o teste de análise de laticínios de autoridade de certificação com o conjunto de módulos e o módulo executar script R.](./media/r-quickstart/fig3.png)


#### <a name="check-on-the-data"></a>Verificar os dados

Vamos dar uma olhada nos dados que carregamos em nosso experimento. No experimento, selecione a saída do conjunto de **cadairydata.csv** e selecione **Visualizar**. Você deve ver algo como este resumo.

![Captura de tela que mostra um resumo do conjunto de cadairydata.csv DataSet.](./media/r-quickstart/fig4.png)

Essa exibição mostra muitas informações úteis. Podemos ver as primeiras várias linhas do conjunto de registros. Se selecionarmos uma coluna, a seção **estatísticas** mostrará mais informações sobre a coluna. Por exemplo, a linha **tipo de recurso** mostra quais tipos de dados Machine Learning Studio (clássico) atribuídos à coluna. Marque esta exibição antes de começar a fazer qualquer trabalho sério.

### <a name="first-r-script"></a>Primeiro script R

Vamos criar um primeiro script R simples para fazer experimentos em Machine Learning Studio (clássico). Criamos e testamos o script a seguir em RStudio.

```r
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
str(cadairydata)
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

Agora, precisamos transferir esse script para Machine Learning Studio (clássico). Você pode recortar e colar, mas, nesse caso, transferir o script R por meio de um arquivo zip.

### <a name="data-input-to-the-execute-r-script-module"></a>Entrada de dados para o módulo Executar Script R

Vejamos as entradas do módulo [Executar Script R][execute-r-script]. Neste exemplo, vamos ler os dados de laticínios da Califórnia no módulo [Executar script R][execute-r-script] .

Há três entradas possíveis para o módulo [Executar Script R][execute-r-script]. Você pode usar qualquer uma ou todas essas entradas, dependendo do seu aplicativo. Você também pode usar um script R que não usa nenhuma entrada.

Vamos examinar cada uma dessa entradas, da esquerda para a direita. Você pode ver os nomes de cada uma das entradas colocando o cursor sobre a entrada e lendo a dica de ferramenta.

#### <a name="script-bundle"></a>Pacote de script

A entrada do pacote de script permite que você passe o conteúdo de um arquivo zip para o módulo [Executar script R][execute-r-script] . Você pode usar um dos comandos a seguir para ler o conteúdo do arquivo zip em seu código R.

```r
source("src/yourfile.R") # Reads a zipped R script
load("src/yourData.rdata") # Reads a zipped R data file
```

> [!NOTE]
> Machine Learning Studio (clássico) trata os arquivos no arquivo zip como se eles estivessem no diretório src/, portanto, você precisa prefixar os nomes de arquivo com esse nome de diretório. Por exemplo, se o arquivo zip contiver os arquivos `yourfile.R` e `yourData.rdata` na raiz do arquivo zip, você endereçará esses arquivos como `src/yourfile.R` e `src/yourData.rdata` quando usar `source` e `load` .

Já discutimos o carregamento de DataSets em [Load the DataSet](#loading). Depois de criar e testar o script R mostrado na seção anterior, execute as etapas a seguir.

1. Salve o script R em um arquivo .R. Chamamos o arquivo de script **simpleplot. R**. Aqui está o que está no arquivo:

   ```r
   ## Only one of the following two lines should be used
   ## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
   ## If in RStudio, use the second line with read.csv()
   cadairydata <- maml.mapInputPort(1)
   # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
   str(cadairydata)
   pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
   ## The following line should be executed only when running in
   ## Azure Machine Learning Studio (classic)
   maml.mapOutputPort('cadairydata')
   ```

1. Crie um arquivo zip e copie o script para esse arquivo zip. No Windows, você pode clicar com o botão direito do mouse no arquivo e selecionar **Enviar para a**  >  **pasta compactada**. Essa ação cria um novo arquivo zip que contém o **simpleplot.** Arquivo do R.

1. Adicione o arquivo aos **conjuntos de valores** em Machine Learning Studio (clássico) e especifique o tipo como **zip**. Agora você deve ver o arquivo zip em seus conjuntos de dados.

1. Arraste o arquivo zip de **conjuntos de valores** para a **tela ml Studio (clássica)**.

1. Conecte a saída do ícone **dados de zip** à entrada do **Pacote de Scripts** do módulo [Executar Script R][execute-r-script].

1. Insira a `source()` função com o nome do arquivo zip na janela de código do módulo [Executar script R][execute-r-script] . Nesse caso, inserimos `source("src/simpleplot.R")` .

1. Selecione **Salvar**.

Depois que essas etapas forem concluídas, o módulo [Executar script r][execute-r-script] executará o script r no arquivo zip quando o experimento for executado. Neste ponto, seu experimento deve ser semelhante a este exemplo.

![Diagrama que mostra um experimento usando um script R compactado.](./media/r-quickstart/fig6.png)

#### <a name="dataset1"></a>Dataset1

Você pode passar uma tabela retangular de dados para seu código R usando a entrada Dataset1. Em nosso script simples, a `maml.mapInputPort(1)` função lê os dados da porta 1. Esses dados são então atribuídos a um nome de variável de quadro de dados em seu código. Em nosso script simples, a primeira linha de código executa a atribuição.

```r
cadairydata <- maml.mapInputPort(1)
```

Execute o experimento selecionando o botão **executar** . Quando a execução for concluída, selecione o módulo [Executar script R][execute-r-script] e, em seguida, selecione **Exibir log de saída** no painel Propriedades. Uma nova página deve aparecer em seu navegador, exibindo o conteúdo do arquivo output.log. Ao rolar para baixo, você verá algo semelhante à saída a seguir.

```output
[ModuleOutput] InputDataStructure
[ModuleOutput]
[ModuleOutput] {
[ModuleOutput]  "InputName":Dataset1
[ModuleOutput]  "Rows":228
[ModuleOutput]  "Cols":9
[ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
[ModuleOutput] }
```

Mais adiante a página é uma informação mais detalhada sobre as colunas, que será semelhante à saída a seguir.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput]
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput]
[ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput]
[ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
[ModuleOutput]
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput]
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput]
[ModuleOutput]  $ Month            : chr  "Jan" "Feb" "Mar" "Apr" ...
[ModuleOutput]
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput]
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput]
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput]
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
```

Esses resultados são como o esperado, com 228 observações e 9 colunas no dataframe. Podemos ver os nomes de coluna, o tipo de dados do R e um exemplo de cada coluna.

> [!NOTE]
> Essa mesma saída impressa está convenientemente disponível na saída do Dispositivo R do módulo [Executar Script R][execute-r-script]. Discutiremos as saídas do módulo [Executar script R][execute-r-script] na próxima seção.  

#### <a name="dataset2"></a>Dataset2

O comportamento da entrada do Dataset2 é idêntico ao do Dataset1. Usando essa entrada, você pode passar uma segunda tabela retangular de dados para o código R. A função `maml.mapInputPort(2)`, com o argumento 2, é usada para passar esses dados.  

### <a name="execute-r-script-outputs"></a>Execute as saídas do Script R

#### <a name="output-a-dataframe"></a>Exibir um dataframe

Você pode exibir o conteúdo de um dataframe R como tabela retangular por meio da porta de resultados do Dataset1 usando a função `maml.mapOutputPort()` . Em nosso script R simples, essa ação é executada pela linha a seguir.

```r
maml.mapOutputPort('cadairydata')
```

Depois de executar o experimento, selecione a porta de saída dataSet1 do resultado e, em seguida, selecione **Visualizar**. Você deve ver algo como este exemplo.

![Captura de tela que mostra a visualização da saída dos dados de laticínios da Califórnia.](./media/r-quickstart/fig7.png)

Esta saída parece idêntica à entrada, exatamente como se esperava.

### <a name="r-device-output"></a>Saída do Dispositivo R

A saída do Dispositivo do módulo [Executar Script R][execute-r-script] contém mensagens e saída de gráficos. As duas mensagens de erro padrão e de saída padrão de R são enviadas para a porta de saída do dispositivo R.

Para exibir a saída do dispositivo R, selecione a porta e, em seguida, selecione **Visualizar**. Vemos a saída padrão e o erro padrão do script R aqui.

![Captura de tela que mostra a saída padrão e o erro padrão da porta do dispositivo R.](./media/r-quickstart/fig8.png)

Rolando para baixo, vemos a saída de gráficos do nosso script R.

![Captura de tela que mostra a saída de gráficos da porta do dispositivo R.](./media/r-quickstart/fig9.png)

## <a name="data-filtering-and-transformation"></a><a id="filtering"></a>Filtragem de dados e transformação

Nesta seção, executaremos algumas operações básicas de filtragem e transformação de dados nos dados de laticínios da Califórnia. Ao final desta seção, teremos dados em um formato adequado para criar um modelo analítico.

Mais especificamente, nesta seção, executaremos várias tarefas comuns de limpeza e transformação de dados: transformação de tipo, filtragem em dataframes, adição de novas colunas computadas e transformações de valor. Este histórioco deve ajudá-lo a lidar com as diversas variações encontradas em problemas do mundo real.

O código R completo para esta seção está disponível em [MachineLearningSamples-notebooks/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="type-transformations"></a>Transformações de tipo

Agora que podemos ler os dados dos laticínios da Califórnia no código R no módulo [Executar Script R][execute-r-script], precisamos garantir que os dados nas colunas tenham o tipo e o formato desejados.

R é uma linguagem tipificada dinamicamente, o que significa que os tipos de dados são forçados entre si quando necessário. Os tipos de dados atômicos em R incluem Numeric, Logical e Character. O fator de tipo é usado para o armazenamento de dados categóricos de forma compacta. Para obter mais informações sobre tipos de dados, consulte as referências em [leituras adicionais](#appendixb).

Quando dados tabulares são lidos em R de uma fonte externa, sempre é uma boa ideia verificar os tipos resultantes nas colunas. Talvez você queira uma coluna do tipo caractere, mas em muitos casos a coluna será mostrada como fator ou vice-versa. Em outros casos, uma coluna que você imagina deve ser numérica é representada por dados de caractere, por exemplo, "1,23" em vez de 1,23 como um número de ponto flutuante.

Felizmente, é fácil converter um tipo em outro, desde que o mapeamento seja possível. Por exemplo, não é possível converter "Nevada" em um valor numérico, mas você pode convertê-lo em um fator (variável categórica). Como outro exemplo, você pode converter um numérico 1 em um caractere "1" ou um fator.

A sintaxe para qualquer uma dessas conversões é simples: `as.datatype()`. Essas funções de conversão de tipo incluem as seguintes funções:

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

Observando os tipos de dados das colunas que inserimos na seção anterior, todas as colunas são do tipo numeric. A exceção é a coluna rotulada "month", que é do tipo caractere. Vamos converter esse tipo em um fator e testar os resultados.

Excluímos a linha que criou a matriz de gráfico de dispersão e adicionei uma linha para converter a coluna de mês em um fator. Neste experimento, vamos recortar e colar o código R na janela de código do módulo [Executar script r][execute-r-script] . Você também pode atualizar o arquivo zip e carregá-lo para Machine Learning Studio (clássico), mas essa opção usa várias etapas.

```r
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(cadairydata$Month)
str(cadairydata) # Check the result
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

Vamos executar esse código e examinar o log de saída para o script R. Os dados relevantes do log são mostrados aqui.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 14 levels "Apr","April",..: 6 5 9 1 11 8 7 3 14 13 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```

O tipo de mês agora deve indicar **os níveis de fator/14**. Esse tipo é um problema porque há apenas 12 meses no ano. Você também pode verificar para ver que o tipo em **Visualização** da porta do conjunto de resultados de resultado é **categórico**.

O problema é que a coluna de mês não foi codificada sistematicamente. Em alguns casos, um mês é chamado de abril e, em outros, é abreviado como abr. Podemos resolver esse problema cortando a cadeia de caracteres para três caracteres. A linha de código agora é semelhante ao exemplo a seguir.

```r
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))
```

Execute novamente o experimento e exiba o log de saída. Os resultados esperados são mostrados aqui.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```


Nossa variável fator agora tem os 12 níveis desejados.

### <a name="basic-dataframe-filtering"></a>Filtragem de dataframe básica

Os dataframes R oferecem suporte a recursos avançados de filtragem. Conjuntos de dados podem ser subdivididos usando filtros lógicos em linhas ou colunas. Em muitos casos, serão necessários critérios complexos de filtro. Para obter exemplos extensos de filtragem de quadros de informações, consulte as referências em [leituras adicionais](#appendixb).

Há um pouco de filtragem que devemos fazer em nosso conjunto de nós. Se você examinar as colunas no dataframe do cadairydata, verá duas colunas desnecessárias. A primeira coluna contém um número de linha, que não é muito útil. A segunda coluna, Year.Month, contém informações redundantes. Podemos facilmente excluir essas colunas usando o código R a seguir.

> [!NOTE]
> De agora em diante nesta seção, mostraremos o código adicional que estamos adicionando no módulo [Executar script R][execute-r-script] . Adicionaremos cada nova linha *antes* da `str()` função. Usamos essa função para verificar os resultados em Machine Learning Studio (clássico).

Adicionamos a seguinte linha ao código R no módulo [Executar script r][execute-r-script] .

```r
# Remove two columns we do not need
cadairydata <- cadairydata[, c(-1, -2)]
```

Execute este código em seu experimento e verifique o resultado do log de saída. Esses resultados são mostrados aqui.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  7 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```

Agora, obtemos os resultados esperados.

### <a name="add-a-new-column"></a>Adicionar uma nova coluna

Para criar modelos de série temporal, será conveniente ter uma coluna que contenha os meses desde o início da série temporal. Criaremos a nova coluna mês. contagem.

Para ajudar a organizar o código, criaremos nossa primeira função simples, `num.month()` . Em seguida, aplicaremos essa função para criar uma nova coluna no dataframe. O novo código é o seguinte:

```r
## Create a new column with the month count
## Function to find the number of months from the first
## month of the time series
num.month <- function(Year, Month) {
  ## Find the starting year
  min.year  <- min(Year)

  ## Compute the number of months from the start of the time series
  12 * (Year - min.year) + Month - 1
}

## Compute the new column for the dataframe
cadairydata$Month.Count <- num.month(cadairydata$Year, cadairydata$Month.Number)
```

Agora, execute o experimento atualizado e use o log de saída para exibir os resultados. Esses resultados são mostrados aqui.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  8 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```


Parece que tudo está funcionando. Temos a nova coluna com os valores esperados em nosso dataframe

### <a name="value-transformations"></a>Transformações de valor

Nesta seção, executaremos algumas transformações simples nos valores de algumas das colunas do dataframe. A linguagem R suporta transformações de valor quase que de forma arbitrária. Para obter mais exemplos, consulte as referências em [leituras adicionais](#appendixb).

Se você examinar os valores nos resumos do dataframe, verá algo estranho aqui. Há mais sorvetes do que leite produzido na Califórnia? Não, claro que não. O problema é que as unidades são diferentes. O preço está em unidades de libras dos EUA, leite está em unidades de 1 milhão libras dos EUA, sorvete é em unidades de 1.000 galões dos EUA e Cottage queijo está em unidades de 1.000 libras dos EUA. Supondo que os sorvetes de sorvete pesam cerca de 6,5 libras por galão, podemos facilmente fazer a multiplicação para converter esses valores de forma que eles fiquem todos em unidades iguais de 1.000 libras.

Para nosso modelo de previsão, usamos um modelo de multiplicativa para tendência e ajuste sazonal desses dados. Uma transformação de log nos permite usar um modelo linear, o que simplifica esse processo. Podemos aplicar a transformação de log na mesma função em que o multiplicador será aplicado.

No código a seguir, definimos uma nova função, `log.transform()` e a aplicamos às linhas que contêm os valores numéricos. A função R `Map()` é usada para aplicar a função `log.transform()` às colunas selecionadas do dataframe. A `Map()` função é semelhante a `apply()` , mas permite mais de uma lista de argumentos para a função. Observe que uma lista de multiplicadores fornece o segundo argumento para a função `log.transform()` . A `na.omit()` função é usada como um pouco de limpeza para garantir que não haja valores ausentes ou indefinidos no dataframe.

```r
log.transform <- function(invec, multiplier = 1) {
  ## Function for the transformation, which is the log
  ## of the input value times a multiplier

  warningmessages <- c("ERROR: Non-numeric argument encountered in function log.transform",
                       "ERROR: Arguments to function log.transform must be greate than zero",
                       "ERROR: Aggurment multiplier to funcition log.transform must be a scaler",
                       "ERROR: Invalid time seies value encountered in function log.transform"
                       )

  ## Check the input arguments
  if(!is.numeric(invec) | !is.numeric(multiplier)) {warning(warningmessages[1]); return(NA)}  
  if(any(invec < 0.0) | any(multiplier < 0.0)) {warning(warningmessages[2]); return(NA)}
  if(length(multiplier) != 1) {{warning(warningmessages[3]); return(NA)}}

  ## Wrap the multiplication in tryCatch
  ## If there is an exception, print the warning message to
  ## standard error and return NA
  tryCatch(log(multiplier * invec),
           error = function(e){warning(warningmessages[4]); NA})
}


## Apply the transformation function to the 4 columns
## of the dataframe with production data
multipliers  <- list(1.0, 6.5, 1000.0, 1000.0)
cadairydata[, 4:7] <- Map(log.transform, cadairydata[, 4:7], multipliers)

## Get rid of any rows with NA values
cadairydata <- na.omit(cadairydata)  
```

Há um pouco de coisa acontecendo na `log.transform()` função. A maior parte do código verifica se há possíveis problemas com os argumentos ou lida com exceções, que ainda podem surgir durante os cálculos. Apenas algumas linhas desse código fazem os cálculos.

O objetivo da programação defensiva é evitar a falha de uma única função que impeça o processamento de continuar. Uma falha abrupta de uma análise de execução longa pode ser frustrante para os usuários. Para evitar essa situação, devem ser escolhidos valores de retorno padrão que limitem os danos ao processamento downstream. Uma mensagem também é emitida para alertar os usuários que algo deu errado.

Se você não for usado para a programação defensiva em R, todo esse código poderá ser muito difícil. Vamos examinar as principais etapas:

1. Um vetor de quatro mensagens é definido. Essas mensagens são usadas para comunicar informações sobre alguns dos possíveis erros e exceções que podem ocorrer com esse código.
1. Retornamos um valor de NA para cada caso. Há muitas outras possibilidades que podem ter menos efeitos colaterais. Poderíamos retornar um vetor de zeros ou o vetor de entrada original, por exemplo.
1. As verificações são executadas nos argumentos para a função. Em cada caso, se for detectado um erro, será retornado um valor padrão, e uma mensagem será produzida pela função `warning()`. Estamos usando `warning()` em vez de `stop()` porque o último terminará a execução, que é o que estamos tentando evitar. Esse código é escrito em um estilo de procedimento, porque, nesse caso, uma abordagem funcional parecia complexa e obscura.
1. Os cálculos de log são encapsulados `tryCatch()` para que as exceções não causem uma interrupção abrupta no processamento. Sem `tryCatch()` , a maioria dos erros gerados pelas funções do R resulta em um sinal de parada, o que faz exatamente isso.

Executar esse código R em seu teste e veja a saída impressa no output.log. Agora, você verá os valores transformados das quatro colunas no log, como mostrado aqui.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  8 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```

Vemos que os valores foram transformados. A produção de leite agora excede muito a todos os outros laticínios de produção de produtos, recuperando que agora estamos examinando uma escala de log.

Neste ponto, nossos dados são limpos e estamos prontos para a modelagem. Se você examinar o resumo de visualização da saída do conjunto de resultados de resultado do nosso módulo [Executar script R][execute-r-script] , verá que a coluna mês é categórica com 12 valores exclusivos, o que é exatamente o que desejamos.

## <a name="time-series-objects-and-correlation-analysis"></a><a id="timeseries"></a>Análise de correlação e objetos de série temporal

Nesta seção, exploraremos alguns objetos básicos de série temporal do R e analisaremos as correlações entre algumas das variáveis. Nossa meta é gerar um dataframe que contenha as informações de correlação emparelhadas a vários atrasos.

O código R completo para esta seção está em [MachineLearningSamples-notebooks/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="time-series-objects-in-r"></a>Objetos de série temporal em R

Como já mencionado, as série de tempo são uma série de valores de dados indexados por tempo. Objetos de série de tempo R são usados para criar e gerenciar o índice de tempo. Há diversas vantagens em usar objetos de série de tempo. Os objetos de série temporal evitam que você tenha que lidar com os vários detalhes do gerenciamento dos valores de índice da série temporal que são encapsulados no objeto. Além disso, os objetos de série temporal permitem que você use os vários métodos de série temporal para plotar, imprimir, modelar e assim por diante.

A classe de série de tempo POSIXct é comumente usada e é relativamente simples. Essa classe de série de tempo mede o tempo a partir do início da época, 1º de janeiro de 1970. Usaremos os objetos de série temporal POSIXct neste exemplo. Outras classes de objeto de série temporal de R amplamente usadas incluem Zoo e XTS (série temporal extensível).

### <a name="time-series-object-example"></a>Exemplo de objeto de série temporal

Vamos começar com o nosso exemplo. Arraste um novo módulo [Executar script R][execute-r-script] para seu experimento. Conecte a porta de saída do Conjunto de Dados de Resultado 1 do módulo [Executar Script R][execute-r-script] existente à porta de entrada do Conjunto de Dados de Resultado 1 do novo módulo [Executar Script R][execute-r-script].

Como fizemos nos primeiros exemplos, vamos progredir pelo exemplo. Em alguns pontos, mostraremos apenas as linhas adicionais incrementais do código R em cada etapa.

#### <a name="read-the-dataframe"></a>Ler o dataframe

Como primeira etapa, vamos ler um dataframe e verificar se obtemos os resultados esperados. O código a seguir deve ser suficiente.

```r
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)
str(cadairydata) # Check the results
```

Agora, execute o teste. O log da nova forma executar script R deve ser semelhante a este exemplo.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  8 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
```

Esses dados são dos tipos e formato esperados. Agora, a coluna month é do tipo factor e tem o número esperado de níveis.

#### <a name="create-a-time-series-object"></a>Criar um objeto de série temporal

Precisamos adicionar um objeto de série de tempo ao nosso dataframe. Substitua o código atual pelo código a seguir, que adiciona uma nova coluna da classe POSIXct.

```r
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata) # Check the results
```

Agora, verifique o log. Ele deve ser semelhante a este exemplo.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...
```

Podemos ver o resumo a nova coluna é de fato da classe POSIXct.

### <a name="explore-and-transform-the-data"></a>Explorar e transformar os dados

Vamos explorar algumas das variáveis deste conjunto de dados. Uma matriz de gráfico de dispersão é uma boa maneira de produzir uma visão rápida. Vamos substituir a `str()` função no código R anterior pela linha a seguir.

```r
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")
```

Execute este código e veja o que acontece. A plotagem produzida na porta do dispositivo R deve ser parecida com este exemplo.

![Captura de tela que mostra a matriz de gráfico de dispersão das variáveis selecionadas.](./media/r-quickstart/fig17.png)

Há uma estrutura de aparência estranha nas relações entre essas variáveis. Talvez essa estrutura surja de tendências nos dados e do fato de não ter padronizado as variáveis.

### <a name="correlation-analysis"></a>Análise de correlação

Para executar a análise de correlação, precisamos de uma tendência e de padronizar as variáveis. Poderíamos simplesmente usar a função `scale()` de R, que centraliza e dimensiona variáveis. Essa função também pode ser executada mais rapidamente. Mas vamos examinar um exemplo de programação defensiva em R.

A `ts.detrend()` função a seguir executa essas duas operações. As duas linhas de código a seguir eliminam os dados de tendência e, em seguida, padronizam os valores.

```r
ts.detrend <- function(ts, Time, min.length = 3){
  ## Function to de-trend and standardize a time series

  ## Define some messages if they are NULL  
  messages <- c('ERROR: ts.detrend requires arguments ts and Time to have the same length',
                'ERROR: ts.detrend requires argument ts to be of type numeric',
                paste('WARNING: ts.detrend has encountered a time series with length less than', as.character(min.length)),
                'ERROR: ts.detrend has encountered a Time argument not of class POSIXct',
                'ERROR: Detrend regression has failed in ts.detrend',
                'ERROR: Exception occurred in ts.detrend while standardizing time series in function ts.detrend'
  )
  # Create a vector of zeros to return as a default in some cases
  zerovec  <- rep(length(ts), 0.0)

  # The input arguments are not of the same length, return ts and quit
  if(length(Time) != length(ts)) {warning(messages[1]); return(ts)}

  # If the ts is not numeric, just return a zero vector and quit
  if(!is.numeric(ts)) {warning(messages[2]); return(zerovec)}

  # If the ts is too short, just return it and quit
  if((ts.length <- length(ts)) < min.length) {warning(messages[3]); return(ts)}

  ## Check that the Time variable is of class POSIXct
  if(class(cadairydata$Time)[[1]] != "POSIXct") {warning(messages[4]); return(ts)}

  ## De-trend the time series by using a linear model
  ts.frame  <- data.frame(ts = ts, Time = Time)
  tryCatch({ts <- ts - fitted(lm(ts ~ Time, data = ts.frame))},
           error = function(e){warning(messages[5]); zerovec})

  tryCatch( {stdev <- sqrt(sum((ts - mean(ts))^2))/(ts.length - 1)
             ts <- ts/stdev},
            error = function(e){warning(messages[6]); zerovec})

  ts
}  
## Apply the detrend.ts function to the variables of interest
df.detrend <- data.frame(lapply(cadairydata[, 4:7], ts.detrend, cadairydata$Time))

## Plot the results to look at the relationships
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = df.detrend, main = "Pairwise Scatterplots of detrended standardized time series")
```

Há um pouco de coisa acontecendo na `ts.detrend()` função. A maior parte do código verifica se há possíveis problemas com os argumentos ou lida com exceções, que ainda podem surgir durante os cálculos. Apenas algumas linhas desse código realmente fazem os cálculos.

Já discutimos um exemplo de programação defensiva em transformações de valor. Os dois blocos de computação estão encapsulados em `tryCatch()`. Para alguns erros, faz sentido retornar o vetor de entrada original. Em outros casos, retornamos um vetor de zeros.

Observe que a regressão linear usada para a eliminação de tendência é uma regressão de uma série de tempo. A variável preditora é um objeto de série de tempo.

Depois de `ts.detrend()` definido, aplicamos-o às variáveis de interesse em nosso dataframe. Devemos forçar a lista resultante criada pelo para os `lapply()` dados no dataframe usando `as.data.frame()` . Devido aos aspectos defensivas do `ts.detrend()` , a falha ao processar uma das variáveis não impedirá o processamento correto dos outros.

A linha final do código cria um gráfico de dispersão emparelhada. Depois de executar o código R, os resultados do gráfico de dispersão são mostrados aqui.

![Captura de tela que mostra o gráfico de dispersão emparelhado das séries temporais de tendência e padronizadas.](./media/r-quickstart/fig18.png)

Você pode comparar esses resultados com os mostrados no exemplo anterior. Com a tendência removida e as variáveis padronizadas, vemos muito menos estrutura nas relações entre essas variáveis.

O código para calcular as correlações como objetos ccf R é indicado a seguir.

```r
## A function to compute pairwise correlations from a
## list of time series value vectors
pair.cor <- function(pair.ind, ts.list, lag.max = 1, plot = FALSE){
  ccf(ts.list[[pair.ind[1]]], ts.list[[pair.ind[2]]], lag.max = lag.max, plot = plot)
}

## A list of the pairwise indices
corpairs <- list(c(1,2), c(1,3), c(1,4), c(2,3), c(2,4), c(3,4))

## Compute the list of ccf objects
cadairycorrelations <- lapply(corpairs, pair.cor, df.detrend)  

cadairycorrelations
```

A execução desse código produz o log mostrado aqui.

```output
[ModuleOutput] Loading objects:
[ModuleOutput]   port1
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] [[1]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]    -1     0     1 
[ModuleOutput] 0.148 0.358 0.317 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[2]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]     -1      0      1 
[ModuleOutput] -0.395 -0.186 -0.238 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[3]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]     -1      0      1 
[ModuleOutput] -0.059 -0.089 -0.127 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[4]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]    -1     0     1 
[ModuleOutput] 0.140 0.294 0.293 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[5]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]     -1      0      1 
[ModuleOutput] -0.002 -0.074 -0.124 
```

Há um valor de correlação para cada retardo. Nenhum desses valores de correlação é grande o suficiente para ser significativo. Podemos concluir que podemos modelar cada variável de forma independente.

### <a name="output-a-dataframe"></a>Exibir um dataframe
Computamos as correlações emparelhadas como uma lista de objetos CCF do R. Isso apresenta um pequeno problema, pois a porta de saída do conjunto de dados resultado exige um dataframe. Além disso, o objeto CCF é, em si, uma lista e queremos apenas os valores no primeiro elemento dessa lista, as correlações em vários atrasos.

O código a seguir extrai os valores de intervalo da lista de objetos ccf, que também são listas.

```r
df.correlations <- data.frame(do.call(rbind, lapply(cadairycorrelations, '[[', 1)))

c.names <- c("correlation pair", "-1 lag", "0 lag", "+1 lag")
r.names  <- c("Corr Cot Cheese - Ice Cream",
              "Corr Cot Cheese - Milk Prod",
              "Corr Cot Cheese - Fat Price",
              "Corr Ice Cream - Mik Prod",
              "Corr Ice Cream - Fat Price",
              "Corr Milk Prod - Fat Price")

## Build a dataframe with the row names column and the
## correlation dataframe and assign the column names
outframe <- cbind(r.names, df.correlations)
colnames(outframe) <- c.names
outframe


## WARNING!
## The following line works only in Azure Machine Learning Studio (classic)
## When running in RStudio, this code will result in an error
#maml.mapOutputPort('outframe')
```

A primeira linha de código é um pouco complicada e algumas explicações podem ajudá-lo a entender isso. Trabalhando de dentro para fora, temos:

1. O operador **[[** com o argumento **1** seleciona o vetor de correlações no retardo do primeiro elemento da lista de objetos CCF.
1. A função `do.call()` aplica-se à função `rbind()` nos elementos de lista retornados por `lapply()`.
1. A função `data.frame()` converte o resultado produzido por `do.call()` em um dataframe.

Observe que os nomes das linhas estão na coluna do dataframe. Isso preserva os nomes de linha quando eles são gerados a partir do [script R de execução][execute-r-script].

A execução do código produz a saída mostrada aqui quando selecionamos **Visualizar** para exibir a saída na porta do conjunto de resultados. Os nomes de linha estão na primeira coluna, conforme o esperado.

![Captura de tela que mostra a saída dos resultados da análise de correlação.](./media/r-quickstart/fig20.png)

## <a name="time-series-example-seasonal-forecasting"></a><a id="seasonalforecasting"></a>Exemplo de série temporal: previsão sazonal

Agora, nossos dados estão em um formato adequado para análise e determinamos que não há correlações significativas entre as variáveis. Vamos continuar e criar um modelo de previsão de série de tempo. Usando esse modelo, vamos prever a produção de leite da Califórnia pelos 12 meses de 2013.

Nosso modelo de previsão terá dois componentes, um componente de tendência e um componente sazonal. A previsão concluída é o produto desses dois componentes. Esse tipo de modelo é conhecido como modelo de multiplicação. A alternativa é um modelo de adição. Já aplicamos uma transformação de log às variáveis de interesse, o que torna essa análise manejável.

O código R completo para esta seção está em [MachineLearningSamples-notebooks/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="create-the-dataframe-for-analysis"></a>Criar o dataframe para análise

Comece adicionando um novo módulo [Executar script R][execute-r-script] ao seu experimento. Conecte a saída do Conjunto de Dados de Resultado do módulo [Executar Script R][execute-r-script] existente à entrada do **Conjunto de Dados 1** do novo módulo. O resultado deve ser semelhante a este exemplo.

![Diagrama que mostra o experimento com o novo módulo executar script R adicionado.](./media/r-quickstart/fig21.png)

Como a análise de correlação que acabamos de concluir, precisamos adicionar uma coluna com um objeto de série de tempo POSIXct. O código a seguir adicionará a coluna.

```r
# If running in Machine Learning Studio (classic), uncomment the first line with maml.mapInputPort()
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata)
```

Execute este código e examine o log. O resultado deve ser semelhante a este exemplo.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...
```

Com esse resultado, estamos prontos para iniciar nossa análise.

### <a name="create-a-training-dataset"></a>Criar um conjunto de dados de treinamento

Com o dataframe construído, precisamos criar um conjunto de um DataSet de treinamento. Esses dados incluirão todas as observações exceto as últimas 12, do ano de 2013, as quais são o nosso conjunto de dados de teste. O seguinte código subagrupa o dataframe e cria gráficos de produção de derivados de leite e de as variáveis de preço. Em seguida, criamos plotagens das quatro variáveis de produção e preço. Uma função anônima é usada para definir alguns argumentos para a criação de gráficos e, em seguida, para iterar a lista dos outros dois argumentos com `Map()`. Se estiver pensando que um loop for já funcionou bem, você estará correto. Mas, como R é uma linguagem funcional, estamos examinando uma abordagem funcional.

```r
cadairytrain <- cadairydata[1:216, ]

Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
               "Log CA Ice Cream Production, 1000s lb",
               "Log CA Milk Production 1000s lb",
               "Log North CA Milk Milk Fat Price per 1000 lb")

Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)
```

A execução do código produz a série de plotagens de série temporal da saída do dispositivo R mostrada aqui. O eixo de tempo está em unidades de datas, que é uma boa vantagem do método gráfico de série temporal.

![Primeiro gráfico de série temporal de dados de produção e de preço de laticínios da Califórnia.](./media/r-quickstart/unnamed-chunk-161.png)

![Segundo gráfico de série temporal de dados de produção e de preço de laticínios da Califórnia.](./media/r-quickstart/unnamed-chunk-162.png)

![Terceiro gráfico de série temporal de dados de produção e de preço de laticínios da Califórnia.](./media/r-quickstart/unnamed-chunk-163.png).

![Quarta série temporal de gráficos de produção de laticínios da Califórnia e dados de preço](./media/r-quickstart/unnamed-chunk-164.png)

### <a name="a-trend-model"></a>Um modelo de tendência

Agora que criamos um objeto de série temporal e examinamos os dados, vamos começar a construir um modelo de tendência para os dados de produção do leite da Califórnia. Podemos usar uma regressão de série temporal. Fica claro da plotagem que precisaremos de mais de uma inclinação e contrataremos para modelar com precisão a tendência observada nos dados de treinamento.

Considerando a pequena escala dos dados, criaremos o modelo para tendência em RStudio e, em seguida, recortamos e colamos o modelo resultante em Machine Learning Studio (clássico). O RStudio fornece um ambiente adequado para esse tipo de análise interativa.

Como primeira tentativa, tentaremos uma regressão polinomial com a potência de até três. Há um perigo real de sobreajuste desses tipos de modelos. É melhor evitar termos de ordem superior. A `I()` função inibe a interpretação do conteúdo (interpreta o conteúdo como está) e permite que você escreva uma função literalmente interpretada em uma equação de regressão.

```r
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
summary(milk.lm)
```

Essa função gera a saída a seguir.

```output
##
## Call:
## lm(formula = Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3),
##     data = cadairytrain)
##
## Residuals:
##      Min       1Q   Median       3Q      Max
## -0.12667 -0.02730  0.00236  0.02943  0.10586
##
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)
## (Intercept)       6.33e+00   1.45e-01   43.60   <2e-16 **_
## Time              1.63e-09   1.72e-10    9.47   <2e-16 _*_
## I(Month.Count^2) -1.71e-06   4.89e-06   -0.35    0.726
## I(Month.Count^3) -3.24e-08   1.49e-08   -2.17    0.031 _  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0418 on 212 degrees of freedom
## Multiple R-squared:  0.941,    Adjusted R-squared:  0.94
## F-statistic: 1.12e+03 on 3 and 212 DF,  p-value: <2e-16
```

A partir de valores P ( `Pr(>|t|)` ) nessa saída, podemos ver que o termo quadrado pode não ser significativo. Usaremos a `update()` função para modificar esse modelo descartando o termo quadrado.

```r
milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
summary(milk.lm)
```

Essa função gera a saída a seguir.

```output
##
## Call:
## lm(formula = Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)
##
## Residuals:
##      Min       1Q   Median       3Q      Max
## -0.12597 -0.02659  0.00185  0.02963  0.10696
##
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)
## (Intercept)       6.38e+00   4.07e-02   156.6   <2e-16 ***
## Time              1.57e-09   4.32e-11    36.3   <2e-16 **_
## I(Month.Count^3) -3.76e-08   2.50e-09   -15.1   <2e-16 _*_
## ---
## Signif. codes:  0 '_*_' 0.001 '_*' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0417 on 213 degrees of freedom
## Multiple R-squared:  0.941,  Adjusted R-squared:  0.94
## F-statistic: 1.69e+03 on 2 and 213 DF,  p-value: <2e-16
```

Essa saída parece melhor. Todos os termos são significativos. O valor de 2e-16 é um valor padrão e não deve ser levado muito a sério.  

Para teste de sensatez, vamos criar um gráfico de série de tempo dos dados da produção de derivados de leite da Califórnia com a curva de tendência mostrada. Adicionamos o seguinte código no Machine Learning Studio (clássico) modelo [Executar script R][execute-r-script] (não RStudio) para criar o modelo e fazer um gráfico. O resultado é mostrado no exemplo a seguir.

```r
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)
```

![Dados de produção leite da Califórnia com o modelo de tendência mostrado.](./media/r-quickstart/unnamed-chunk-18.png)

Parece que o modelo de tendência se ajusta aos dados muito bem. Além disso, parece que não há evidências de sobreajuste, como ondulações estranhas na curva de modelo.

### <a name="seasonal-model"></a>Modelo sazonal

Com um modelo de tendência em mãos, precisamos continuar e incluir efeitos sazonais. Usaremos o mês do ano como uma variável fictícia no modelo linear para capturar o efeito de mês a mês. Quando você introduz variáveis de fator em um modelo, a interceptação não deve ser computada. Se você não fizer isso, a fórmula será mais especificada e o R removerá um dos fatores desejados, mas manterá o termo de interceptação.

Como temos um modelo de tendência satisfatório, podemos usar a `update()` função para adicionar os novos termos ao modelo existente. -1 na atualização da fórmula descarta o termo de interceptação. Continuando em RStudio:

```r
milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
summary(milk.lm2)
```

Essa função gera a saída a seguir.

```output
##
## Call:
## lm(formula = Milk.Prod ~ Time + I(Month.Count^3) + Month - 1,
##     data = cadairytrain)
##
## Residuals:
##      Min       1Q   Median       3Q      Max
## -0.06879 -0.01693  0.00346  0.01543  0.08726
##
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)
## Time              1.57e-09   2.72e-11    57.7   <2e-16 ***
## I(Month.Count^3) -3.74e-08   1.57e-09   -23.8   <2e-16 **_
## MonthApr          6.40e+00   2.63e-02   243.3   <2e-16 _*_
## MonthAug          6.38e+00   2.63e-02   242.2   <2e-16 _*_
## MonthDec          6.38e+00   2.64e-02   241.9   <2e-16 _*_
## MonthFeb          6.31e+00   2.63e-02   240.1   <2e-16 _*_
## MonthJan          6.39e+00   2.63e-02   243.1   <2e-16 _*_
## MonthJul          6.39e+00   2.63e-02   242.6   <2e-16 _*_
## MonthJun          6.38e+00   2.63e-02   242.4   <2e-16 _*_
## MonthMar          6.42e+00   2.63e-02   244.2   <2e-16 _*_
## MonthMay          6.43e+00   2.63e-02   244.3   <2e-16 _*_
## MonthNov          6.34e+00   2.63e-02   240.6   <2e-16 _*_
## MonthOct          6.37e+00   2.63e-02   241.8   <2e-16 _*_
## MonthSep          6.34e+00   2.63e-02   240.6   <2e-16 _*_
## ---
## Signif. codes:  0 '_*_' 0.001 '_*' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0263 on 202 degrees of freedom
## Multiple R-squared:     1,    Adjusted R-squared:     1
## F-statistic: 1.42e+06 on 14 and 202 DF,  p-value: <2e-16
```

Vemos que o modelo não tem um termo de interceptação e tem 12 fatores mês significativos. Esse resultado é exatamente o que queríamos ver.

Vamos fazer outro gráfico de série de tempo dos dados de produção de derivados de leite da Califórnia para ver até que ponto o modelo sazonal está funcionando. Adicionamos o código a seguir no Machine Learning Studio (clássico) [Executar script R][execute-r-script] para criar o modelo e fazer uma plotagem.

```r
milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)
```

A execução desse código no Machine Learning Studio (clássico) produz a plotagem mostrada aqui.

![Produção de leite na Califórnia com o modelo dos efeitos sazonais.](./media/r-quickstart/unnamed-chunk-20.png)

O ajuste aos dados mostrados neste exemplo é, em vez disso, incentivando. A tendência e o efeito sazonal (variação mensal) parecem razoáveis.

Como outra verificação em nosso modelo, vamos dar uma olhada nos resíduos. O código a seguir calcula os valores previstos de nossos dois modelos, calcula os resíduos para o modelo sazonal e, então, cria gráficos com esses resíduos nos dados de treinamento.

```r
## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute and plot the residuals
residuals <- cadairydata$Milk.Prod - predict2
plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")
```

O gráfico residual é mostrado aqui.

![Resíduos do modelo sazonal para os dados de treinamento.](./media/r-quickstart/unnamed-chunk-21.png)

Esses resíduos parecem razoáveis. Não há nenhuma estrutura específica, exceto o efeito do 2008-2009 crise econômica, que nosso modelo não conta muito bem.

A plotagem mostrada neste exemplo é útil para detectar quaisquer padrões dependentes de tempo nos resíduos. A abordagem explícita de computação e de plotagem dos resíduos que usamos coloca os resíduos na ordem de tempo no gráfico. Se tivéssemos plotado `milk.lm$residuals` , o gráfico não estaria em ordem de tempo.

Também é possível usar `plot.lm()` para produzir uma série de gráficos de diagnóstico:

```r
## Show the diagnostic plots for the model
plot(milk.lm2, ask = FALSE)
```

Esse código produz uma série de gráficos de diagnósticos mostrados nos exemplos a seguir.

![Primeiro gráfico de diagnóstico para o modelo sazonal.](./media/r-quickstart/unnamed-chunk-221.png)

![Segundo gráfico de diagnóstico para o modelo sazonal.](./media/r-quickstart/unnamed-chunk-222.png)

![Terceiro gráfico de diagnóstico para o modelo sazonal.](./media/r-quickstart/unnamed-chunk-223.png)

![Quarto diagnóstico de gráfico para o modelo sazonal.](./media/r-quickstart/unnamed-chunk-224.png)

Há alguns pontos de grande influencia identificados nessas representações gráficas, mas eles não causam muita preocupação. Além disso, podemos ver no gráfico Q-Q normal que os resíduos estão próximos de serem distribuídos normalmente, o que é uma suposição importante para modelos lineares.

### <a name="forecasting-and-model-evaluation"></a>Avaliação de previsão e modelo

Há apenas mais uma coisa a fazer para concluir nosso exemplo. Precisamos calcular as previsões e medir o erro em relação aos dados reais. Nossa previsão será para os 12 meses de 2013. Podemos computar uma medida de erro para essa previsão para os dados reais que não fazem parte do nosso conjunto de dado de treinamento. Além disso, podemos comparar o desempenho em 18 anos de dados de treinamento com os 12 meses de dados de teste.

Um número de métricas é usado para medir o desempenho dos modelos de série de tempo. Em nosso caso, usaremos o erro de RMS (quadrado raiz). A função a seguir calcula o erro RMS entre duas séries.

```r
RMS.error <- function(series1, series2, is.log = TRUE, min.length = 2){
  ## Function to compute the RMS error or difference between two
  ## series or vectors

  messages <- c("ERROR: Input arguments to function RMS.error of wrong type encountered",
                "ERROR: Input vector to function RMS.error is too short",
                "ERROR: Input vectors to function RMS.error must be of same length",
                "WARNING: Funtion rms.error has received invald input time series.")

  ## Check the arguments
  if(!is.numeric(series1) | !is.numeric(series2) | !is.logical(is.log) | !is.numeric(min.length)) {
    warning(messages[1])
    return(NA)}

  if(length(series1) < min.length) {
    warning(messages[2])
    return(NA)}

  if((length(series1) != length(series2))) {
       warning(messages[3])
    return(NA)}

  ## If is.log is TRUE exponentiate the values, else just copy
  if(is.log) {
    tryCatch( {
      temp1 <- exp(series1)
      temp2 <- exp(series2) },
      error = function(e){warning(messages[4]); NA}
    )
  } else {
    temp1 <- series1
    temp2 <- series2
  }

 ## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute the RMS error in a dataframe
  tryCatch( {
    sqrt(sum((temp1 - temp2)^2) / length(temp1))},
    error = function(e){warning(messages[4]); NA})
}
```

Assim como acontece com a `log.transform()` função que discutimos na seção "transformações de valor", há muita verificação de erro e código de recuperação de exceção nessa função. Os princípios empregados são os mesmos. O trabalho é feito em dois lugares ajustados em `tryCatch()`. Primeiro, a série temporal é exponenciação, já que estamos trabalhando com os logs dos valores. Em segundo lugar, o erro real do RMS é computado.

Equipado com uma função para medir o erro do RMS, vamos criar e gerar um dataframe que contenha os erros do RMS. Incluiremos termos apenas para o modelo de tendência e o modelo completo com fatores sazonais. O código a seguir faz o trabalho usando os dois modelos lineares que construímos.

```r
## Compute the RMS error in a dataframe
## Include the row names in the first column so they will
## appear in the output of the Execute R Script
RMS.df  <-  data.frame(
rowNames = c("Trend Model", "Seasonal Model"),
  Traing = c(
  RMS.error(predict1[1:216], cadairydata$Milk.Prod[1:216]),
  RMS.error(predict2[1:216], cadairydata$Milk.Prod[1:216])),
  Forecast = c(
    RMS.error(predict1[217:228], cadairydata$Milk.Prod[217:228]),
    RMS.error(predict2[217:228], cadairydata$Milk.Prod[217:228]))
)
RMS.df

## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('RMS.df')
```

A execução desse código produz a saída mostrada aqui na porta de saída do conjunto de resultados.

![Captura de tela que mostra a comparação de erros do RMS para os modelos.](./media/r-quickstart/fig26.png)

Com base nesses resultados, podemos ver que a adição dos fatores sazonais ao modelo reduz o erro RMS significativamente. Não é muito surpreendente, o erro RMS para os dados de treinamento é um pouco menor que para a previsão.

## <a name="guide-to-rstudio-documentation"></a><a id="appendixa"></a>Guia para a documentação do RStudio

RStudio está bem documentado. Aqui estão alguns links para as seções principais da documentação do RStudio para ajudá-lo a começar.

* **Criar projetos**: você pode organizar e gerenciar seu código R em projetos usando o RStudio. Para obter mais informações, consulte [usando projetos](https://support.rstudio.com/hc/articles/200526207-Using-Projects). Siga estas instruções e crie um projeto para os exemplos de código R neste artigo.
* **Editar e executar o código r**: o RStudio fornece um ambiente integrado para edição e execução do código r. Para obter mais informações, consulte [editando e executando código](https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code).
* **Debug**: o RStudio inclui recursos avançados de depuração. Para obter mais informações sobre esses recursos, consulte [Depurando com RStudio](https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio). Para obter informações sobre os recursos de solução de problemas de ponto de interrupção, consulte [pontos de solução de problemas](https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting)

## <a name="further-reading"></a><a id="appendixb"></a>Leitura adicional

Este tutorial de programação R aborda as noções básicas do que você precisa para usar a linguagem R com Machine Learning Studio (clássico). Se você não estiver familiarizado com o R, duas introduções estarão disponíveis em CRAN:

* [R para iniciantes](https://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf) de Emmanuel Paradis é um bom lugar para começar.
* [Uma introdução ao R](https://cran.r-project.org/doc/manuals/R-intro.html) por W. N. Venables et al. apresenta mais detalhes.

Há muitos livros sobre R que podem ajudá-lo a começar:

* **Arte de programação de R: um tour pelo design de software estatístico** por Norman Matloff é uma excelente introdução à programação em R.
* O **r Cookbook** de Paul Teetor fornece uma abordagem de problema e solução para o uso do R.
* **R em ação** por Robert Kabacoff é outro livro introdutório útil. O [site de R rápido](https://www.statmethods.net/) do Companion é um recurso útil.
* **O R inferno** by Patrick queimadura é um livro surpreendentemente bem-humorada que lida com vários tópicos complicados e difíceis que podem ser encontrados quando você programa em R. O livro está disponível gratuitamente no [inferno do R](https://www.burns-stat.com/documents/books/the-r-inferno/).
* O **r avançado** da Hadley Wickham fornece um aprofundamento nos tópicos avançados em R. A versão online deste livro está disponível gratuitamente em [R avançado](http://adv-r.had.co.nz/).
* A **série de tempo introdutório com r** por Paul Cowpertwait e Andrew Metcalfe fornece uma introdução ao uso do R para análise de série temporal. Muitos textos mais teóricos fornecem exemplos de R.

Aqui estão alguns ótimos recursos da Internet:

* [Exibição de tarefa Cran: a análise de série temporal](https://cran.r-project.org/web/views/TimeSeries.html) tem um catálogo de pacotes de série temporal do R. Para obter informações sobre pacotes de objeto de série temporal específicos, consulte a documentação para esse pacote.
* [A introdução ao r](https://www.datacamp.com/courses/introduction-to-r) é um curso interativo gratuito do datacamp que ensina o R no conforto do seu navegador com lições em vídeo e exercícios de código. Há tutoriais interativos sobre as técnicas de R e os pacotes mais recentes.
* [Aprenda a programação de R, o guia definitivo](https://www.datamentor.io/r-programming/) do datamentor.
* O [codificador r](https://r-coder.com/) tem tutoriais de r detalhados e um curso de r gratuito para iniciantes.
* O [tutorial do R](https://www.cyclismo.org/tutorial/R/) por Kelly Black da Clarkson University é um tutorial rápido.
* [Os principais recursos da linguagem R para melhorar suas habilidades de dados](https://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html) listam mais de 60 de recursos de r.

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
