---
title: Definir módulos R personalizados
titleSuffix: Azure Machine Learning Studio
description: Este tópico descreve como criar e implantar um módulo R personalizado no Azure Machine Learning Studio. Ele explica o que são módulos R personalizados e arquivos que são usados para defini-los.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: 0dec86eff9b9df70514be6f32f3aad60bfb311ca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60751189"
---
# <a name="define-custom-r-modules-for-azure-machine-learning-studio"></a>Definir os módulos R personalizados para o Azure Machine Learning Studio

Este tópico descreve como criar e implantar um módulo R personalizado no Azure Machine Learning Studio. Ele explica o que são módulos R personalizados e arquivos que são usados para defini-los. Ilustra como criar os arquivos que definem um módulo e como registrar o módulo para implantação em um workspace de Machine Learning. Os elementos e atributos usados na definição de módulo personalizado, em seguida, são descritos mais detalhadamente. Também é discutido como usar a funcionalidades e arquivos auxiliares, bem como diversas saídas. 



## <a name="what-is-a-custom-r-module"></a>O que é um módulo R personalizado?
Um **módulo personalizado** é um módulo definido pelo usuário que pode ser carregado em seu workspace e executado como parte de um experimento do Azure Machine Learning Studio. Um **módulo R personalizado** é um módulo personalizado que executa uma função R definida pelo usuário. **R** é uma linguagem de programação para a computação estatística e gráficos que é amplamente usada por cientistas estatísticos e para implementar algoritmos estatísticos. Atualmente, R é a única linguagem com suporte em módulos personalizados, mas o suporte para idiomas adicionais está agendado para futuras versões.

Os módulos personalizados têm o **status de primeira classe** no Azure Machine Learning Studio no sentido de que podem ser usados como qualquer outro módulo. Eles podem ser executados com outros módulos, incluídos em visualizações ou em experimentos publicados. Você tem controle sobre o algoritmo implementado pelo módulo, as portas de entrada e saída a ser usadas, os parâmetros de modelagem e outros vários comportamentos de tempo de execução. Uma experiência contendo módulos personalizados também pode ser publicada na Galeria de IA do Azure para fácil compartilhamento.

## <a name="files-in-a-custom-r-module"></a>Arquivos em um módulo R personalizado
Um módulo R personalizado é definido por um arquivo .zip que contém, no mínimo, dois arquivos:

* Um **arquivo fonte** que implementa a função R exposta pelo módulo
* Um **arquivo de definição XML** que descreve a interface do módulo personalizado

Os arquivos auxiliares adicionais também podem ser incluídos no arquivo .zip que fornecem funcionalidades que podem ser acessadas por meio do módulo personalizado. Essa opção é discutida na parte **Argumentos** da seção de referência **Elementos no arquivo de definição XML** após o exemplo de início rápido.

## <a name="quickstart-example-define-package-and-register-a-custom-r-module"></a>Exemplo de início rápido: definir, empacotar e registrar um módulo R personalizado
Este exemplo ilustra como construir os arquivos necessários para um módulo R personalizado, empacotá-los em um arquivo zip e, em seguida, registrar o módulo no workspace de Machine Learning. Os arquivos de exemplo e pacote de zip exemplo podem ser baixados de [Baixar arquivo CustomAddRows.zip](https://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409).

## <a name="the-source-file"></a>O arquivo de origem
Considere o exemplo de um módulo **Adicionar Linhas personalizado** que modifica a implementação padrão do módulo **Adicionar Linhas** usado para concatenar linhas (observações) de dois conjuntos de dados (quadros de dados). O módulo **Adicionar Linhas** padrão acrescenta as linhas do segundo conjunto de dados de entrada ao final do primeiro usando o algoritmo `rbind`. A função `CustomAddRows` personalizada similarmente aceita dois conjuntos de dados, mas também aceita um parâmetro booliano de troca como entrada adicional. Se o parâmetro de troca for definido como **FALSE**, ele retornará o mesmo conjunto de dados que a implementação padrão. Porém, se o parâmetro de troca for **TRUE**, a função acrescentará linhas do primeiro conjunto de dados de entrada ao final do segundo conjunto de dados. O arquivo CustomAddRows.R que contém a implementação da função `CustomAddRows` R exposta pelo módulo **Adicionar Linhas Personalizadas** contém o código R a seguir.

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) 
    {
        if (swap)
        {
            return (rbind(dataset2, dataset1));
        }
        else
        {
            return (rbind(dataset1, dataset2));
        } 
    } 

### <a name="the-xml-definition-file"></a>O arquivo de definição XML
Para expor essa função `CustomAddRows` como um módulo de Azure Machine Learning Studio, um arquivo de definição XML deve ser criado para especificar qual deverá ser a aparência e o comportamento do módulo **Adicionar Linhas Personalizadas**. 

    <!-- Defined a module using an R Script -->
    <Module name="Custom Add Rows">
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another. Dataset 2 is concatenated to Dataset 1 when Swap is FALSE, and vice versa when Swap is TRUE.</Description>

    <!-- Specify the base language, script file and R function to use for this module. -->        
        <Language name="R" 
         sourceFile="CustomAddRows.R" 
         entryPoint="CustomAddRows" />  

    <!-- Define module input and output ports -->
    <!-- Note: The values of the id attributes in the Input and Arg elements must match the parameter names in the R Function CustomAddRows defined in CustomAddRows.R. -->
        <Ports>
            <Input id="dataset1" name="Dataset 1" type="DataTable">
                <Description>First input dataset</Description>
            </Input>
            <Input id="dataset2" name="Dataset 2" type="DataTable">
                <Description>Second input dataset</Description>
            </Input>
            <Output id="dataset" name="Dataset" type="DataTable">
                <Description>The combined dataset</Description>
            </Output>
        </Ports>

    <!-- Define module parameters -->
        <Arguments>
            <Arg id="swap" name="Swap" type="bool" >
                <Description>Swap input datasets.</Description>
            </Arg>
        </Arguments>
    </Module>


É fundamental observar que o valor dos atributos **id** dos elementos **Input** e **Arg** no arquivo XML deve corresponder aos nomes de parâmetro de função do código R no arquivo CustomAddRows.R COM EXATIDÃO: (*dataset1*, *dataset2* e *swap*, no exemplo). Da mesma forma, o valor do atributo **entryPoint** do elemento **Language** deve corresponder EXATAMENTE ao nome da função no script R: (*CustomAddRows* no exemplo). 

Por outro lado, o atributo **id** para o elemento de **Output** não corresponde a todas as variáveis no script R. Quando mais de uma saída for necessária, basta retornar uma lista da função R com resultados colocados *na mesma ordem* em que os elementos **Outputs** são declarados no arquivo XML.

### <a name="package-and-register-the-module"></a>Empacotar e registrar o módulo
Salve esses dois arquivos como *CustomAddRows.R* e *CustomAddRows.xml* e compacte-os em um arquivo *CustomAddRows.zip*.

Para registrá-los no seu workspace do Machine Learning, vá para seu workspace no Machine Learning Studio, clique no botão **+NOVO** na parte inferior e escolha **MÓDULO -&gt; DO PACOTE ZIP** para carregar o novo módulo **Adicionar Linhas personalizado**.

![Carregar Zip](./media/custom-r-modules/upload-from-zip-package.png)

O módulo **Adicionar Linhas personalizado** agora está pronto para ser acessado pelos seus testes de Machine Learning.

## <a name="elements-in-the-xml-definition-file"></a>Elementos no arquivo de definição XML
### <a name="module-elements"></a>Elementos de módulo
O elemento **Módulo** é usado para definir um módulo personalizado no arquivo XML. Vários módulos podem ser definidos em um arquivo XML usando vários elementos de **módulo** . Cada módulo no workspace deve ter um nome exclusivo. Registre um módulo personalizado com o mesmo nome de um módulo personalizado existente e o módulo existente será substituído pelo novo. Os módulos personalizados, no entanto, podem ser registrados com o mesmo nome que um módulo existente do Azure Machine Learning Studio. Nesse caso, eles aparecerão na categoria **Personalizado** da paleta do módulo.

    <Module name="Custom Add Rows" isDeterministic="false"> 
        <Owner>Microsoft Corporation</Owner>
        <Description>Appends one dataset to another...</Description>/> 


No elemento **Módulo** , você pode especificar dois elementos opcionais adicionais:

* um elemento **Proprietário** que está incorporado no módulo  
* um elemento **Descrição** que contém o texto exibido na ajuda rápida para o módulo e quando você passa o mouse sobre o módulo na interface de usuário do Machine Learning.

Regras para limites de caracteres nos elementos de Módulo:

* O valor do atributo **nome** no elemento **Módulo** não deve exceder 64 caracteres de comprimento. 
* O conteúdo do elemento **Descrição** não deve exceder 128 caracteres de comprimento.
* O conteúdo do elemento **Proprietário** não deve exceder 32 caracteres de comprimento.

Os resultados de um módulo podem ser determinísticos ou não determinísticos.** Por padrão, todos os módulos são considerados determinísticos. Ou seja, dado um conjunto inalterável de parâmetros de entrada e de dados, o módulo retornarão os mesmos resultados RAND ou uma hora de função que é executado. Devido a esse comportamento, o Studio de Azure Machine Learning só executará novamente os módulos marcados como determinísticos se um parâmetro ou os dados de entrada tenham mudado. Retornar os resultados em cache também oferece uma execução muito mais rápida dos experimentos.

Há funções não determinísticas, como RAND ou uma função que retorna a data ou a hora atual. Se o módulo usar uma função não determinística, você poderá especificar que o módulo é não determinístico ao definir o atributo **isDeterministic** opcional como **FALSE**. Isso garante que o módulo será executado novamente sempre que o experimento for executado, mesmo que o módulo de entrada e os parâmetros não tenham mudado. 

### <a name="language-definition"></a>Definição de linguagem
O elemento **Idioma** em seu arquivo de definição XML é usado para especificar o idioma do módulo personalizado. Atualmente, R é a única linguagem com suporte. O valor do atributo **sourceFilede** deve ser o nome do arquivo R que contém a função a ser chamada quando o módulo é executado. Esse arquivo deve fazer parte do pacote de zip. O valor do atributo **entryPoint** é o nome da função que está sendo chamada e deve corresponder a uma função válida definida no arquivo de origem.

    <Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />


### <a name="ports"></a>Portas
As portas de entrada e saída de um módulo personalizado são especificadas nos elementos filho da seção **Portas** do arquivo de definição XML. A ordem desses elementos determina o layout visualizado (UX) pelos usuários. A primeira **entrada** ou **saída** filha listada no elemento **Portas** do arquivo XML se tornará a porta de entrada mais à esquerda no UX do Machine Learning.
Cada porta de entrada e saída pode ter um elemento filho de **Descrição** opcional que especifica o texto mostrado quando você passar o cursor do mouse sobre a porta na interface de usuário de Machine Learning.

**Regras de portas**:

* O número máximo de **portas de entrada e saída** é de 8 para cada.

### <a name="input-elements"></a>Elementos de entrada
As portas de entrada permitem que você passe dados para a função R e o workspace. Os **tipos de dados** com suporte para portas de entrada são os seguintes: 

**DataTable:** Esse tipo é passado para a função R como um data.frame. Na verdade, qualquer tipo (por exemplo, arquivos CSV ou arquivos ARFF) têm suporte no Machine Learning e que é compatível com **DataTable** é convertido para um data.frame automaticamente. 

        <Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
            <Description>Input Dataset 1</Description>
           </Input>

O atributo **id** associado a cada porta de entrada **DataTable** deve ter um valor exclusivo e esse valor deve combinar com o seu parâmetro de nome correspondente na função R.
As portas **DataTable** opcionais que não forem passadas como entrada em um experimento terão o valor **NULL** passado para a função R e as portas zip opcionais serão ignoradas se a entrada não estiver conectada. O atributo **isOptional** é opcional para os tipos **DataTable** e **Zip** e *false* por padrão.

**ZIP:** Os módulos personalizados podem aceitar um arquivo zip como entrada. Essa entrada é descompactada para o diretório de trabalho R da sua função

        <Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
            <Description>Zip files to be extracted to the R working directory.</Description>
           </Input>

Para os módulos R personalizados, a id de uma porta Zip não precisa corresponder a nenhum parâmetro da função R. Isso ocorre porque o arquivo zip é extraído automaticamente para o diretório de trabalho do R.

**Regras de entrada:**

* O valor do atributo **id** do elemento **Entrada** deve ser um nome de variável R válido.
* O valor do atributo **id** do elemento **Entrada** não deve ter mais de 64 caracteres de comprimento.
* O valor do atributo **nome** do elemento **Entrada** não deve ter mais de 64 caracteres de comprimento.
* O conteúdo do elemento **Descrição** não deve ter mais de 128 caracteres de comprimento
* O valor do atributo **tipo** do elemento **Entrada** deve ser *Zip* ou *DataTable*.
* O valor do atributo **isOptional** do elemento **Entrada** não é necessário (e é *false* por padrão quando não especificado); mas, se for especificado, deve ser *true* ou *false*.

### <a name="output-elements"></a>Elementos de saída
**Portas de saída padrão:** As portas de saída são mapeadas para os valores de retorno da função R, que pode ser usada pelos módulos subsequentes. *DataTable* é o único tipo de porta de saída padrão com suporte no momento. (O suporte para *Aprendizes* e *Transformações* estará disponível em breve.) Uma saída *DataTable* é definida como:

    <Output id="dataset" name="Dataset" type="DataTable">
        <Description>Combined dataset</Description>
    </Output>

Para saídas em módulos R personalizados, o valor do atributo **id** não precisa corresponder a nada no script R, mas deve ser exclusivo. Para uma única saída de módulo, o valor retornado da função R deve ser um *data.frame*. Para mais de um objeto de um tipo de dados com suporte de saída, as portas de saída apropriada precisam ser especificadas no arquivo de definição XML e os objetos precisam ser retornados como uma lista. Os objetos de saída são atribuídos a portas da esquerda para a direita, refletindo a ordem na qual os objetos são colocados na lista retornada de saída.

Por exemplo, se você deseja modificar o módulo **Adicionar Linhas personalizado** para produzir os dois conjuntos de dados originais, *dataset1* e *dataset2*, além do conjunto de dados *dataset* recém-incorporado (em ordem, da esquerda para a direita, como: *dataset*, *dataset1*, *dataset2*), então defina as portas de saída no arquivo CustomAddRows.xml da seguinte maneira:

    <Ports> 
        <Output id="dataset" name="Dataset Out" type="DataTable"> 
            <Description>New Dataset</Description> 
        </Output> 
        <Output id="dataset1_out" name="Dataset 1 Out" type="DataTable"> 
            <Description>First Dataset</Description> 
        </Output> 
        <Output id="dataset2_out" name="Dataset 2 Out" type="DataTable"> 
            <Description>Second Dataset</Description> 
        </Output> 
        <Input id="dataset1" name="Dataset 1" type="DataTable"> 
            <Description>First Input Table</Description>
        </Input> 
        <Input id="dataset2" name="Dataset 2" type="DataTable"> 
            <Description>Second Input Table</Description> 
        </Input> 
    </Ports> 


E retorne a lista de objetos em uma lista na ordem correta em “CustomAddRows.R”:

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) { 
        if (swap) { dataset <- rbind(dataset2, dataset1)) } 
        else { dataset <- rbind(dataset1, dataset2)) 
        } 
    return (list(dataset, dataset1, dataset2)) 
    } 

**Saída de visualização:** Também é possível especificar uma porta de saída do tipo *Visualização*, que exibe a saída do dispositivo gráfico R e a saída do console. Essa porta não faz parte da saída da função R e não interfere na ordem de outros tipos de porta de saída. Para adicionar uma porta de visualização aos módulos personalizados, adicione um elemento de **saída** com um valor de *Visualização* para seu atributo **tipo**:

    <Output id="deviceOutput" name="View Port" type="Visualization">
      <Description>View the R console graphics device output.</Description>
    </Output>

**Regras de saída:**

* O valor do atributo **id** do elemento **Saída** deve ser um nome de variável R válido.
* O valor do atributo **id** do elemento **Saída** não deve ter mais de 32 caracteres de comprimento.
* O valor do atributo **nome** do elemento **Saída** não deve ter mais de 64 caracteres de comprimento.
* O valor do atributo **tipo** do elemento **Saída** deve ser *Visualização*.

### <a name="arguments"></a>Argumentos
Dados adicionais podem ser passados para a função R por meio de parâmetros do módulo definidos no elemento **Argumentos** . Esses parâmetros são exibidos no canto direito do painel mais à direita da interface do usuário do Machine Learning quando o módulo está selecionado. Os argumentos podem ser qualquer um dos tipos com suporte, ou você pode criar uma enumeração personalizada quando necessário. De modo similar aos elementos **Portas**, os elementos **Argumentos** podem ter um elemento **Descrição** opcional que especifica o texto que aparece quando você passa o cursor do mouse sobre o nome do parâmetro.
Propriedades opcionais de um módulo, como o defaultValue, minValue e maxValue, podem ser adicionadas a qualquer argumento como atributos a um elemento **Propriedades** . As propriedades válidas para o elemento **Propriedades** dependem do tipo de argumento e são descritas com os tipos de argumento com suporte na próxima seção. Argumentos com a propriedade **isOptional** definida como **"true"** não exigem que o usuário insira um valor. Se um valor não for fornecido para o argumento, o argumento não será passado para a função de ponto de entrada. Argumentos da função de ponto de entrada que são opcionais precisam ser manipulados explicitamente pela função, por exemplo, atribuído um valor padrão NULL na definição da função de ponto de entrada. Um argumento opcional somente irá impor as outras restrições de argumento, ou seja, mín. ou máx., se um valor for fornecido pelo usuário.
Assim como acontece com entradas e saídas, é essencial que cada um dos parâmetros tenha valores de ID exclusivos associados. Em nosso exemplo de início rápido, a ID/parâmetro associado era *troca*.

### <a name="arg-element"></a>Elemento arg
Um parâmetro de módulo é definido usando o elemento filho **Arg** da seção **Argumentos** do arquivo de definição XML. Assim como acontece com os elementos filho na seção **Portas**, a ordem dos parâmetros na seção **Argumentos** define o layout encontrado na UX. Os parâmetros aparecem de cima para baixo na interface do usuário na mesma ordem em que são definidos no arquivo XML. Os tipos com suporte no Machine Learning para parâmetros são listados aqui. 

**int** – um parâmetro de tipo de número inteiro (32 bits).

    <Arg id="intValue1" name="Int Param" type="int">
        <Properties min="0" max="100" default="0" />
        <Description>Integer Parameter</Description>
    </Arg>


* *Propriedades opcionais*: **mín.**, **máx.**, **padrão** e **isOptional**

**double** – um parâmetro de tipo duplo.

    <Arg id="doubleValue1" name="Double Param" type="double">
        <Properties min="0.000" max="0.999" default="0.3" />
        <Description>Double Parameter</Description>
    </Arg>


* *Propriedades opcionais*: **mín.**, **máx.**, **padrão** e **isOptional**

**bool** – um parâmetro booliano que é representado por uma caixa de seleção no UX.

    <Arg id="boolValue1" name="Boolean Param" type="bool">
        <Properties default="true" />
        <Description>Boolean Parameter</Description>
    </Arg>



* *Propriedades opcionais*: **padrão** -falso se não definido

**string**: uma cadeia de caracteres padrão

    <Arg id="stringValue1" name="My string Param" type="string">
        <Properties isOptional="true" />
        <Description>String Parameter 1</Description>
    </Arg>    

* *Propriedades opcionais*: **padrão** e **isOptional**

**ColumnPickerFor**: um parâmetro de seleção de coluna. Esse tipo é renderizado como um seletor de coluna no UX. O elemento **Propriedade** é usado aqui para especificar a ID da porta da qual as colunas são selecionadas, em que o tipo de porta de destino deve ser *DataTable*. O resultado da seleção de coluna é passado para a função R como uma lista de cadeias de caracteres contendo os nomes da coluna selecionada. 

        <Arg id="colset" name="Column set" type="ColumnPicker">      
          <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
          <Description>Column set</Description>
        </Arg>


* *Propriedades obrigatórias*: **portId** – corresponde à ID de um elemento de entrada com o tipo *DataTable*.
* *Propriedades opcionais*:
  
  * **allowedTypes** - filtra os tipos de coluna dos quais você pode escolher. Os valores válidos incluem: 
    
    * Numeric
    * Boolean
    * Categóricos
    * Cadeia de caracteres
    * Rótulo
    * Recurso
    * Pontuação
    * Todos
  * **padrão** – seleções padrão válidas para o seletor de coluna incluem: 
    
    * Nenhum
    * NumericFeature
    * NumericLabel
    * NumericScore
    * NumericAll
    * BooleanFeature
    * BooleanLabel
    * BooleanScore
    * BooleanAll
    * CategoricalFeature
    * CategoricalLabel
    * CategoricalScore
    * CategoricalAll
    * StringFeature
    * StringLabel
    * StringScore
    * StringAll
    * AllLabel
    * AllFeature
    * AllScore
    * Todos

**Suspensa**: uma lista (suspensa) enumerada especifica pelo usuário. Os itens de lista suspensa são especificados dentro do elemento **Propriedades** usando um elemento de **Item**. A **ID** de cada **Item** deve ser exclusiva e uma variável R válida. O valor do **nome** de um **Item** serve como o texto que você vê e o valor que é passado para a função R.

    <Arg id="color" name="Color" type="DropDown">
      <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
      </Properties>
      <Description>Select a color.</Description>
    </Arg>    

* *Propriedades opcionais*:
  * **padrão** – o valor da propriedade padrão deve corresponder a um valor de ID de um dos elementos de **Item**.

### <a name="auxiliary-files"></a>Arquivos auxiliares
Qualquer arquivo que é colocado no arquivo ZIP do módulo personalizado estará disponível para uso durante o momento de execução. Qualquer estrutura de diretório presente é preservada. Isso significa que o fornecimento de arquivos funciona da mesma forma localmente e na execução do Azure Machine Learning Studio. 

> [!NOTE]
> Observe que todos os arquivos são descompactados para o diretório “src”, de modo que todos os caminhos devem ter o prefixo “src/”.
> 
> 

Por exemplo, digamos que você queira remover quaisquer linhas com NAs do conjunto de dados e também remover quaisquer linhas duplicadas antes de produzir CustomAddRows, e você já escreveu uma função R que faz isso em um arquivo RemoveDupNARows.R:

    RemoveDupNARows <- function(dataFrame) {
        #Remove Duplicate Rows:
        dataFrame <- unique(dataFrame)
        #Remove Rows with NAs:
        finalDataFrame <- dataFrame[complete.cases(dataFrame),]
        return(finalDataFrame)
    }
Você pode usar o arquivo auxiliar RemoveDupNARows.R na função CustomAddRows como fonte:

    CustomAddRows <- function(dataset1, dataset2, swap=FALSE) {
        source("src/RemoveDupNARows.R")
            if (swap) { 
                dataset <- rbind(dataset2, dataset1))
             } else { 
                  dataset <- rbind(dataset1, dataset2)) 
             } 
        dataset <- removeDupNARows(dataset)
        return (dataset)
    }

Em seguida, carregue um arquivo zip contendo “CustomAddRows.R”, “CustomAddRows.xml” e “RemoveDupNARows.R” como um módulo R personalizado.

## <a name="execution-environment"></a>Ambiente de execução
O ambiente de execução para o script R usa a mesma versão do R que o módulo **Executar Script R** e pode usar os mesmos pacotes padrão. Você também pode adicionar pacotes R adicionais ao módulo personalizado incluindo-os no pacote zip do módulo personalizado. Basta carregá-los no seu script R como faria em seu próprio ambiente R. 

**Limitações do ambiente de execução** incluem:

* Sistema de arquivos não persistentes: Os arquivos gravados quando o módulo personalizado é executado não persistem entre várias execuções do mesmo módulo.
* Sem acesso à rede

