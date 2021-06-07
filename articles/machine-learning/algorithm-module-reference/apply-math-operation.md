---
title: Aplicar operação matemática
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo aplicar operação matemática em Azure Machine Learning para aplicar uma operação matemática a valores de coluna em um pipeline.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: cc2afb2b29c69e9687dbd3b194b6a1626d4131b3
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625187"
---
# <a name="apply-math-operation"></a>Aplicar operação matemática

Este artigo descreve um módulo do designer de Azure Machine Learning.

Use a operação aplicar matemática para criar cálculos que são aplicados a colunas numéricas no conjunto de dados de entrada. 

As operações matemáticas incluem funções aritméticas, funções trigonométricas, funções de arredondamento e funções especiais usadas em ciência de dados, como gama e funções de erro.  

Depois de definir uma operação e executar o pipeline, os valores são adicionados ao conjunto de seus conjuntos de seus. Dependendo de como você configura o módulo, você pode:

+ Acrescente os resultados ao seu conjunto de um (útil ao verificar o resultado da operação).
+ Substitua os valores de colunas pelos novos valores calculados.
+ Gerar uma nova coluna para resultados e não mostrar os dados originais. 

Procure a operação que você precisa nessas categorias:  

- [Basic](#basic-math-operations)  
  
     As funções na categoria **básica** podem ser usadas para manipular um único valor ou coluna de valores. Por exemplo, você pode obter o valor absoluto de todos os números em uma coluna ou calcular a raiz quadrada de cada valor em uma coluna.  
  
-   [Comparar](#comparison-operations)  
  
      As funções na categoria **comparar** são todas usadas para comparação: você pode fazer uma comparação por pares dos valores em duas colunas ou pode comparar cada valor em uma coluna a uma constante especificada. Por exemplo, você pode comparar colunas para determinar se os valores eram os mesmos em dois conjuntos de valores. Ou, você pode usar uma constante, como um valor máximo permitido, para localizar exceções em uma coluna numérica.  
  
-   [Operações](#arithmetic-operations)  
  
     A categoria de **operações** inclui funções matemáticas básicas: adição, subtração, multiplicação e divisão. Você pode trabalhar com colunas ou constantes. Por exemplo, você pode adicionar o valor na coluna A ao valor na coluna B. Ou, você pode subtrair uma constante, como uma média calculada anteriormente, de cada valor na coluna A.  
  
-   [Arredondamento](#rounding-operations)  
  
     A categoria de **arredondamento** inclui uma variedade de funções para executar operações como arredondamento, teto, piso e truncamento para vários níveis de precisão. Você pode especificar o nível de precisão para números decimais e inteiros.  
  
-   [Especial](#special-math-functions)  
  
     A categoria **especial** inclui funções matemáticas que são especialmente usadas em ciência de dados, como integrals elípticas e a função de erro gaussiano.  
  
-   [Trigonométricas](#trigonometric-functions)  
  
     A categoria **trigonométrica** inclui todas as funções trigonométricas padrão. Por exemplo, você pode converter radianos em graus ou calcular funções como tangentes em radianos ou graus.
     Essas funções são unários, o que significa que elas usam uma única coluna de valores como entrada, aplicam a função trigonométrica e retornam uma coluna de valores como resultado. Verifique se a coluna de entrada é o tipo apropriado e se contém o tipo correto de valores para a operação especificada.   

## <a name="how-to-configure-apply-math-operation"></a>Como configurar aplicar operação matemática  

O módulo **aplicar operação matemática** requer um conjunto de módulos que contenha pelo menos uma coluna contendo apenas números. Os números podem ser discretos ou contínuos, mas devem ser de um tipo de dados numérico, não uma cadeia de caracteres.

Você pode aplicar a mesma operação a várias colunas numéricas, mas todas as colunas devem estar no mesmo conjunto de mesmos. 

Cada instância desse módulo pode executar apenas um tipo de operação de cada vez. Para executar operações matemáticas complexas, talvez seja necessário encadear várias instâncias do módulo **aplicar operação matemática** .  
  
1.  Adicione o módulo **aplicar operação matemática** ao seu pipeline.

1. Conecte um conjunto de um DataSet que contenha pelo menos uma coluna numérica.  

1.  Selecione uma ou mais colunas de origem nas quais executar o cálculo.   
  
    - Qualquer coluna escolhida deve ser um tipo de dados numérico. 
    - O intervalo de dados deve ser válido para a operação matemática selecionada. Caso contrário, poderá ocorrer um erro ou o resultado NaN (não um número). Por exemplo, ln (-1,0) é uma operação inválida e resulta em um valor de `NaN` .
  
1.  Selecione **categoria** para selecionar o **tipo** de operação matemática a ser executada.
    
1. Escolha uma operação específica da lista nessa categoria.
  
1.  Defina parâmetros adicionais necessários para cada tipo de operação.  
  
1.  Use a opção **modo de saída** para indicar como você deseja que a operação matemática seja gerada: 

    - **Append**. Todas as colunas usadas como entradas são incluídas no conjunto de informações de saída, mais uma coluna adicional é anexada que contém os resultados da operação matemática.
    - **Inplace**. Os valores nas colunas usadas como entradas são substituídos pelos novos valores calculados. 
    - **ResultOnly**. Uma única coluna é retornada contendo os resultados da operação matemática.
  
1.  Enviar o pipeline.  
  
## <a name="results"></a>Resultados

Se você gerar os resultados usando as opções **Append** ou **ResultOnly** , os cabeçalhos de coluna do conjunto de resultados retornado indicarão a operação e as colunas que foram usadas. Por exemplo, se você comparar duas colunas usando o operador **Equals** , os resultados teriam a seguinte aparência:  
  
-   **Equals (Col2_Col1)**, indicando que você testou Col2 em col1.  
-   **Equals (Col2_ $10)**, indicando que você comparou a coluna 2 com a constante 10.  

Mesmo que você use a opção **in loco** , os dados de origem não são excluídos ou alterados; a coluna no DataSet original ainda está disponível no designer. Para exibir os dados originais, você pode conectar o módulo [adicionar colunas](add-columns.md) e associá-lo à saída de **aplicar operação matemática**.  
    
## <a name="basic-math-operations"></a>Operações matemáticas básicas 

As funções na categoria **básica** geralmente usam um único valor de uma coluna, executam a operação predefinida e retornam um único valor. Para algumas funções, você pode especificar uma constante ou um conjunto de colunas como um segundo argumento.  
  
 O Azure Machine Learning dá suporte às seguintes funções na categoria **básica** :  

### <a name="abs"></a>Abs

Retorna o valor absoluto das colunas selecionadas.  
  
### <a name="atan2"></a>Atan2

Retorna uma tangente inversa de quatro quadrantes.  

Selecione as colunas que contêm as coordenadas de ponto. Para o segundo argumento, que corresponde à coordenada x, você também pode especificar uma constante.  

Corresponde à função ATAN2 no MATLAB.  

### <a name="conj"></a>Conj

Retorna o conjugado para os valores na coluna selecionada.  

### <a name="cuberoot"></a>CubeRoot

Calcula a raiz do cubo para os valores na coluna selecionada.  

### <a name="doublefactorial"></a>DoubleFactorial  
 Calcula o fatorial duplo para valores na coluna selecionada. O fatorial duplo é uma extensão da função fatorial normal e é denotado como `x!!`.  

### <a name="eps"></a>Eps

Retorna o tamanho do espaço entre o valor atual e o próximo número mais alto de precisão dupla. Corresponde à função EPS no MATLAB.  
  
### <a name="exp"></a>Exp

Retorna e elevado à potência do valor na coluna selecionada. Essa função é igual à função EXP do Excel.  

### <a name="exp2"></a>Exp2

Retorna o exponencial de base 2 dos argumentos, resolvendo para y = x * 2<sup>t</sup> , em que t é uma coluna de valores que contém expoentes.  

Em  **conjunto de colunas**, selecione a coluna que contém os valores de expoente t.

Para **Exp2** , você pode especificar um segundo argumento x, que pode ser uma constante ou outra coluna de valores. No **segundo tipo de argumento**, indique se você fornecerá o multiplicador x como uma constante ou um valor em uma coluna.  

Por exemplo, se você selecionar uma coluna com os valores {0,1,2,3,4,5} para multiplicador e expoente, a função retornará {0, 2, 8, 24, 64 160).  

### <a name="expminus1"></a>ExpMinus1 

Retorna o expoente negativo para valores na coluna selecionada.  

### <a name="factorial"></a>Fatorial
Retorna o fatorial para valores na coluna selecionada.  

### <a name="hypotenuse"></a>Hipotenusa
Calcula o hipotenusa para um triângulo no qual o comprimento de um lado é especificado como uma coluna de valores, e o comprimento do segundo lado é especificado como uma constante ou como duas colunas.  

### <a name="ln"></a>Ln

Retorna o logaritmo natural dos valores na coluna selecionada.  

### <a name="lnplus1"></a>LnPlus1

Retorna o logaritmo natural mais um para os valores na coluna selecionada.  

### <a name="log"></a>Log

Retorna o log dos valores na coluna selecionada, dada a base especificada.  

Você pode especificar a base (o segundo argumento) como uma constante ou selecionando outra coluna de valores.  

### <a name="log10"></a>Log10

Retorna os valores de logaritmo de base 10 para a coluna selecionada.  

### <a name="log2"></a>Log2

Retorna os valores de logaritmo de base 2 para a coluna selecionada.  

### <a name="nthroot"></a>NthRoot
Retorna a enésima raiz do valor usando um n que você especificar.  

Selecione as colunas para as quais você deseja calcular a raiz usando a opção **ColumnSet** .  

No **segundo tipo de argumento**, selecione outra coluna que contenha a raiz ou especifique uma constante a ser usada como raiz.  

Se o segundo argumento for uma coluna, cada valor na coluna será usado como o valor de n para a linha correspondente. Se o segundo argumento for uma constante, digite o valor para n na caixa de texto do **segundo argumento** .
### <a name="pow"></a>Pow

Calcula X elevado à potência de Y para cada um dos valores na coluna selecionada.  

Primeiro, selecione as colunas que contêm a **base**, que deve ser um float, usando a opção **ColumnSet** .  

No **segundo tipo de argumento**, selecione a coluna que contém o expoente ou especifique uma constante a ser usada como expoente.  

Se o segundo argumento é uma coluna, cada valor na coluna é usado como o exponente para a linha correspondente. Se o segundo argumento for uma constante, digite o valor para o expoente na caixa de texto do **segundo argumento** .  

### <a name="sqrt"></a>Sqrt

Retorna a raiz quadrada dos valores na coluna selecionada.  

### <a name="sqrtpi"></a>SqrtPi

Para cada valor na coluna selecionada, multiplica o valor por PI e, em seguida, retorna a raiz quadrada do resultado.  

### <a name="square"></a>Square

Quadrados os valores na coluna selecionada.  

## <a name="comparison-operations"></a>Operadores de comparação  

Use as funções de comparação no Azure Machine Learning designer a qualquer momento que você precisar testar dois conjuntos de valores entre si. Por exemplo, em um pipeline, talvez seja necessário fazer essas operações de comparação:  

- Avaliar uma coluna do modelo de Pontuação de probabilidade em relação a um valor limite.
- Determine se dois conjuntos de resultados são os mesmos. Para cada linha diferente, adicione um sinalizador falso que pode ser usado para processamento adicional ou filtragem.  

### <a name="equalto"></a>EqualTo

Retornará true se os valores forem iguais.  

### <a name="greaterthan"></a>GreaterThan

Retornará true se os valores na **coluna definida** forem maiores que a constante especificada ou maior que os valores correspondentes na coluna de comparação.  

### <a name="greaterthanorequalto"></a>GreaterThanOrEqualTo

Retornará true se os valores na **coluna definida** forem maiores ou iguais à constante especificada, ou maior ou igual aos valores correspondentes na coluna de comparação.  

### <a name="lessthan"></a>LessThan

Retornará true se os valores no **conjunto de colunas** forem menores que a constante especificada ou menores que os valores correspondentes na coluna de comparação.  
  
### <a name="lessthanorequalto"></a>LessThanOrEqualTo

Retornará true se os valores no **conjunto de colunas** forem menores ou iguais à constante especificada, ou menor ou igual aos valores correspondentes na coluna de comparação.  

### <a name="notequalto"></a>NotEqualTo

Retorna True se os valores em **Coluna definida** não forem iguais à constante ou coluna de comparação e retorna False se forem iguais.  

### <a name="pairmax"></a>PairMax

Retorna o valor maior; o valor na **Coluna definida** ou o valor na coluna constante ou comparação.  

### <a name="pairmin"></a>PairMin

Retorna o valor menor — o valor em **coluna definida** ou o valor na coluna constante ou comparação  
  
##  <a name="arithmetic-operations"></a>Arithmetic operations   

Inclui as operações aritméticas básicas: adição e subtração, divisão e multiplicação.  Como a maioria das operações é binária, exigindo dois números, você primeiro escolhe a operação e, em seguida, escolhe a coluna ou os números a serem usados no primeiro e segundo argumentos.

A ordem de divisão e subtração são as seguintes: 
- Subtrair (Arg1_Arg2) = arg1-ARG 2
- Dividir (Arg1_Arg2) = arg1/ARG 2

A tabela a seguir mostra alguns exemplos

Operação|Núm1|Núm2|Coluna de resultado|Valor do resultado|
----|----|----|----|----
|Adição|1|5|Adicionar (Num2_Num1)| 6|
|Multiplicação|1|5|Vários (Num2_Num1)|5|
|Subtração|5|1|Subtrair (Num2_Num1)|4|
|Subtração|0|1|Subtrair (Num2_Num1)|-1|
|Divisão|5|1|Dividir (Num2_Num1)|5|
|Divisão|1|0|Dividir (Num2_Num1)|Infinito|

### <a name="add"></a>Adicionar

Especifique as colunas de origem usando o **conjunto de colunas** e, em seguida, adicione a esses valores um número especificado no **segundo argumento**.  

Para adicionar os valores em duas colunas, escolha uma coluna ou colunas usando o **conjunto de colunas** e escolha uma segunda coluna usando o **segundo argumento**.  

### <a name="divide"></a>Dividir

Divide os valores na **coluna definida** por uma constante ou pelos valores de coluna definidos no **segundo argumento**.  Em outras palavras, você escolhe o divisor primeiro e, em seguida, o dividendo. O valor de saída é o quociente.

### <a name="multiply"></a>Multiplicar

Multiplica os valores na **Coluna definida** pelos valores de constante ou coluna especificados.  

### <a name="subtract"></a>Subtrair

Especifique a coluna de valores para operar (o *minuendo*), escolhendo uma coluna diferente, usando a opção **conjunto de colunas** . Em seguida, especifique o número a ser subtraído (o *subtraendo*) usando a segunda lista suspensa de **argumentos** . Você pode escolher uma constante ou coluna de valores.

##  <a name="rounding-operations"></a>Operações de arredondamento 

O designer de Azure Machine Learning dá suporte a uma variedade de operações de arredondamento. Para muitas operações, você deve especificar a quantidade de precisão a ser usada ao arredondar. Você pode usar um nível de precisão estática, especificado como uma constante, ou pode aplicar um valor de precisão dinâmica obtido de uma coluna de valores.  

- Se você usar uma constante, defina **tipo de precisão** como **constante** e digite o número de dígitos como um número inteiro na caixa de texto **precisão constante** . Se você digitar um não inteiro, o módulo não gerará um erro, mas os resultados poderão ser inesperados.  

- Para usar um valor de precisão diferente para cada linha em seu conjunto de linhas, defina **tipo de precisão** como **ColumnSet** e escolha a coluna que contém os valores de precisão apropriados.  

### <a name="ceiling"></a>Ceiling

Retorna o teto para os valores na **Coluna definida**.  

### <a name="ceilingpower2"></a>CeilingPower2

Retorna o teto quadrado para os valores na **Coluna definida**.  

### <a name="floor"></a>Piso

Retorna a base para os valores na **Coluna definida**, até a precisão especificada.  

### <a name="mod"></a>Mod

Retorna a parte fracionária dos valores na **Coluna definida**, até a precisão especificada.  

### <a name="quotient"></a>Quociente

Retorna a parte fracionária dos valores na **Coluna definida**, até a precisão especificada.  

### <a name="remainder"></a>Resto

Retorna o restante para os valores na **Coluna definida**.  

### <a name="rounddigits"></a>RoundDigits

Retorna os valores na **Coluna definida**, arredondado pela regra de 4/5 para o número de dígitos especificado.  

### <a name="rounddown"></a>RoundDown

Retorna os valores na **Coluna definida**, arredondado para baixo até o número especificado de dígitos.  

### <a name="roundup"></a>RoundUp

Retorna os valores na **Coluna definida**, arredondado para cima até o número especificado de dígitos.  

### <a name="toeven"></a>ToEven

Retorna os valores na **Coluna definida**, arredondados para o próximo número inteiro par.  

### <a name="toodd"></a>ToOdd

Retorna os valores na **Coluna definida**, arredondados para o próximo número inteiro ímpar.  

### <a name="truncate"></a>Truncate

Trunca os valores na **Coluna definida** removendo todos os dígitos não permitidos pela precisão especificada.  
  
## <a name="special-math-functions"></a>Funções matemáticas especiais

Essa categoria inclui funções matemáticas especializadas geralmente usadas em ciência de dados. Salvo indicação em contrário, a função é unário e retorna o cálculo especificado para cada valor na coluna ou colunas selecionadas.  

### <a name="beta"></a>Beta

Retorna o valor da função beta de Euler.  

### <a name="ellipticintegrale"></a>EllipticIntegralE
Retorna o valor do integral elíptica incompleto.  
  

### <a name="ellipticintegralk"></a>EllipticIntegralK

Retorna o valor da integral elíptica completa (K).  

### <a name="erf"></a>Erf

Retorna o valor da função de erro.  

A função de erro (também chamada de função de erro Gauss) é uma função especial de forma sigmoide que é usada na probabilidade para descrever a difusão.  

### <a name="erfc"></a>Erfc

Retorna o valor da função de erro complementar.  

`Erfc` é definido como 1 – ERF (x).  

### <a name="erfscaled"></a>ErfScaled

Retorna o valor da função de erro dimensionada.  

Uma versão dimensionada da função de erro pode ser usada para evitar estouro aritmético.  

### <a name="erfinverse"></a>ErfInverse

Retorna o valor da função inversa `erf` .  

### <a name="exponentialintegralein"></a>ExponentialIntegralEin

Retorna o valor da Ei integral exponencial.  

### <a name="gamma"></a>Gama

Retorna o valor da função gama.  

### <a name="gammaln"></a>GammaLn

Retorna o logaritmo natural da função gama.  

### <a name="gammaregularizedp"></a>GammaRegularizedP

Retorna o valor da função gama incompleta regularizada.  

Essa função usa um segundo argumento, que pode ser fornecido como uma constante ou uma coluna de valores.  

### <a name="gammaregularizedpinverse"></a>GammaRegularizedPInverse

Retorna o valor da função gama incompleta regularizada inversa.  

Essa função usa um segundo argumento, que pode ser fornecido como uma constante ou uma coluna de valores.  

### <a name="gammaregularizedq"></a>GammaRegularizedQ  

Retorna o valor da função gama incompleta regularizada.  

Essa função usa um segundo argumento, que pode ser fornecido como uma constante ou uma coluna de valores.  

### <a name="gammaregularizedqinverse"></a>GammaRegularizedQInverse

Retorna o valor da função gama incompleta regularizada generalizada inversa.

Essa função usa um segundo argumento, que pode ser fornecido como uma constante ou uma coluna de valores.  

### <a name="polygamma"></a>PolyGamma

Retorna o valor da função poligama.  

Essa função usa um segundo argumento, que pode ser fornecido como uma constante ou uma coluna de valores.

##  <a name="trigonometric-functions"></a>Funções trigonométricas 

Essa categoria iIncludes a maioria das funções trigonométricas trigonométricas e inversas importantes. Todas as funções trigonométricas são unárias e não requerem nenhum argumento adicional.  

### <a name="acos"></a>Acos

Calcula o arco cosseno para os valores de coluna.  

### <a name="acosdegree"></a>AcosDegree

Calcula o arco cosseno dos valores de coluna, em graus.  

### <a name="acosh"></a>Acosh

Calcula o arco cosseno hiperbólico dos valores de coluna.  

### <a name="acot"></a>Acot

Calcula o arco cotangente dos valores da coluna.  

### <a name="acotdegrees"></a>AcotDegrees

Calcula o arco cotangente dos valores de coluna, em graus.  

### <a name="acoth"></a>Acoth

Calcula o arco cotangente hiperbólico dos valores de coluna.  

### <a name="acsc"></a>Acsc

Calcula o arccosecant dos valores da coluna.  

### <a name="acscdegrees"></a>AcscDegrees

Calcula o arccosecant dos valores de coluna, em graus.  
### <a name="asec"></a>Asec

Calcula o arcosecante dos valores da coluna.  

### <a name="asecdegrees"></a>AsecDegrees

Calcula o arcosecante dos valores de coluna, em graus.  

### <a name="asech"></a>Asech

Calcula o arcosecante hiperbólico dos valores de coluna.  

### <a name="asin"></a>Asin

Calcula o arco seno dos valores da coluna.  

### <a name="asindegrees"></a>AsinDegrees

Calcula o arco seno dos valores de coluna, em graus.  

### <a name="asinh"></a>Asinh

Calcula o arco seno hiperbólico para os valores de coluna.  

### <a name="atan"></a>Atan

Calcula o arco tangente dos valores de coluna.  

### <a name="atandegrees"></a>AtanDegrees

Calcula o arco tangente dos valores de coluna, em graus.  

### <a name="atanh"></a>Atanh

Calcula o arco tangente hiperbólico dos valores de coluna.  

### <a name="cos"></a>Cos

Calcula o cosseno dos valores de coluna.  

### <a name="cosdegrees"></a>CosDegrees

Calcula o cosseno para os valores de coluna, em graus.  

### <a name="cosh"></a>Cosh

Calcula o cosseno hiperbólico para os valores de coluna.  

### <a name="cot"></a>Cot

Calcula a cotangente para os valores de coluna.  

### <a name="cotdegrees"></a>CotDegrees

Calcula a cotangente para os valores de coluna, em graus.  

### <a name="coth"></a>Coth
Calcula a cotangente hiperbólica para os valores de coluna.  

### <a name="csc"></a>Csc

Calcula a cossecante para os valores de coluna.  

### <a name="cscdegrees"></a>CscDegrees

Calcula a cossecante para os valores de coluna, em graus.  

### <a name="csch"></a>Csch

Calcula a cossecante hiperbólica para os valores de coluna.  

### <a name="degreestoradians"></a>DegreesToRadians

Converte graus em radianos.  

### <a name="sec"></a>Sec

Calcula a secante dos valores da coluna.  

### <a name="asecdegrees"></a>aSecDegrees

Calcula a secante para os valores de coluna, em graus.  

### <a name="asech"></a>aSech

Calcula a secante Hiperbólica dos valores de coluna.  

### <a name="sign"></a>Assinar

Retorna o sinal dos valores da coluna.  

### <a name="sin"></a>Sin

Calcula o seno dos valores da coluna.  

### <a name="sinc"></a>Sinc

Calcula o valor seno-cosseno dos valores de coluna.  

### <a name="sindegrees"></a>SinDegrees

Calcula o seno para os valores de coluna, em graus.  

### <a name="sinh"></a>Sinh

Calcula o seno hiperbólico dos valores de coluna.  

### <a name="tan"></a>Tan

Calcula a tangente dos valores da coluna.  

### <a name="tandegrees"></a>TanDegrees

Calcula a tangente do argumento, em graus.  

### <a name="tanh"></a>Tanh

Calcula a tangente hiperbólica dos valores de coluna.  
  
## <a name="technical-notes"></a>Observações técnicas

Tenha cuidado ao selecionar mais de uma coluna como o segundo operador. Os resultados são fáceis de entender se a operação for simples, como adicionar uma constante a todas as colunas. 

Suponha que o conjunto de seus conjuntos de linhas tenha várias colunas e você adicione o conjunto de um a ele mesmo. Nos resultados, cada coluna é adicionada a si mesma, da seguinte maneira:  
  
|Núm1|Núm2|Num3|Adicionar (Num1_Num1)|Adicionar (Num2_Num2)|Adicionar (Num3_Num3)|
|----|----|----|----|----|----|
|1|5|2|2|10|4|
|2|3|-1|4|6|-2|
|0|1|-1|0|2|-2|

Se você precisar executar cálculos mais complexos, poderá encadear várias instâncias de **aplicar operação matemática**. Por exemplo, você pode adicionar duas colunas usando uma instância de **Aplicar Operação Matemática** e usar outra instância de **Aplicar Operação Matemática** para dividir a soma por uma constante para obter a média.  
  
Como alternativa, use um dos seguintes módulos para fazer todos os cálculos de uma só vez, usando o script SQL, R ou Python:
 
+ [Executar Script R](execute-r-script.md)
+ [Executar Script do Python](execute-python-script.md)
+ [Aplicar Transformação SQL](apply-sql-transformation.md)   
  
## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 
