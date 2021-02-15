---
title: 'Dividir dados: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo dividir dados em Azure Machine Learning para dividir um conjunto de dado em dois conjuntos distintos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: a4c93b12ad654e54a7f3c7ee0e75507d5cb45e90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907821"
---
# <a name="split-data-module"></a>Módulo dividir dados

Este artigo descreve um módulo no designer de Azure Machine Learning.

Use o módulo dividir dados para dividir um conjunto de dado em dois conjuntos distintos.

Esse módulo é útil quando você precisa separar dados em conjuntos de treinamento e teste. Você também pode personalizar a maneira como os dados são divididos. Algumas opções dão suporte à randomização de dados. Outras são adaptadas para um determinado tipo de dados ou tipo de modelo.

## <a name="configure-the-module"></a>Configurar o módulo

> [!TIP]
> Antes de escolher o modo de divisão, Leia todas as opções para determinar o tipo de divisão de que você precisa.
> Se você alterar o modo de divisão, todas as outras opções poderão ser redefinidas.

1. Adicione o módulo **dividir dados** ao seu pipeline no designer. Você pode encontrar esse módulo em **transformação de dados**, na categoria **exemplo e dividir** .

1. **Modo de divisão**: escolha um dos seguintes modos, dependendo do tipo de dados que você tem e de como deseja dividi-lo. Cada modo de divisão tem opções diferentes.

   - **Dividir linhas**: Use essa opção se desejar apenas dividir os dados em duas partes. Você pode especificar a porcentagem de dados a serem colocados em cada divisão. Por padrão, os dados são divididos 50/50.

     Você também pode tornar aleatória a seleção de linhas em cada grupo e usar a amostragem de sobreratificação. Em amostragem de sobreratificação, você deve selecionar uma única coluna de dados para a qual você deseja que os valores sejam distribuídos igualmente entre os dois conjuntos de dado de resultado.  

   - **Divisão de expressão regular**: escolha esta opção quando desejar dividir o conjunto de um testando uma única coluna para um valor.

     Por exemplo, se você estiver analisando sentimentos, poderá verificar a presença de um nome de produto específico em um campo de texto. Em seguida, você pode dividir o conjunto de registros em linhas com o nome e as linhas do produto de destino sem o nome do produto de destino.

   - **Divisão de expressão relativa**: Use essa opção sempre que desejar aplicar uma condição a uma coluna de número. O número pode ser um campo de data/hora, uma coluna que contém valores de idade ou moeda, ou até mesmo uma porcentagem. Por exemplo, talvez você queira dividir seu conjunto de dados com base no custo dos itens, agrupar pessoas por intervalos de idade ou separar os dados por uma data de calendário.

### <a name="split-rows"></a>Dividir linhas

1. Adicione o módulo [dividir dados](./split-data.md) ao seu pipeline no designer e conecte o conjunto que você deseja dividir.
  
1. Para o **modo de divisão**, selecione **dividir linhas**. 

1. **Fração de linhas no primeiro conjunto de resultados de saída**: Use esta opção para determinar quantas linhas entrarão na primeira saída (lado esquerdo). Todas as outras linhas entrarão na segunda saída (lado direito).

   A taxa representa a porcentagem de linhas enviadas para o primeiro conjunto de resultados de saída, portanto, você deve inserir um número decimal entre 0 e 1.
     
   Por exemplo, se você inserir **0,75** como o valor, o conjunto de valores será dividido 75/25. Nessa divisão, 75% das linhas serão enviadas para o primeiro conjunto de resultados de saída. Os 25% restantes serão enviados para o segundo conjunto de resultados de saída.
  
1. Selecione a opção **divisão aleatória** se desejar tornar aleatória a seleção de dados nos dois grupos. Essa é a opção preferida quando você está criando conjuntos de testes de treinamento e de teste.

1. **Semente aleatória**: Insira um valor inteiro não negativo para iniciar a sequência pseudoaleatória de instâncias a serem usadas. Essa propagação padrão é usada em todos os módulos que geram números aleatórios. 

   A especificação de uma semente torna os resultados reproduzíveis. Se você precisar repetir os resultados de uma operação de divisão, deverá especificar uma semente para o gerador de número aleatório. Caso contrário, a semente aleatória é definida por padrão como **0**, o que significa que o valor de semente inicial é obtido do relógio do sistema. Como resultado, a distribuição de dados pode ser ligeiramente diferente cada vez que você executar uma divisão. 

1. **Divisão de desratificação**: defina essa opção como **true** para garantir que os dois conjuntos de resultados de saída contenham uma amostra representativa dos valores na coluna *Strata* ou na *coluna de chave estratificação*. 

   Com a amostragem de sobreratificação, os dados são divididos de forma que cada conjunto de dado de saída Obtém aproximadamente a mesma porcentagem de cada valor de destino. Por exemplo, talvez você queira garantir que seus conjuntos de treinamento e teste sejam aproximadamente equilibrados em relação ao resultado ou a alguma outra coluna (como sexo).

1. Envie o pipeline.


## <a name="select-a-regular-expression"></a>Selecionar uma expressão regular

1. Adicione o módulo [dividir dados](./split-data.md) ao seu pipeline e conecte-o como entrada para o DataSet que você deseja dividir.  
  
1. Para o **modo de divisão**, selecione divisão de **expressão regular**.

1. Na caixa **expressão regular** , insira uma expressão regular válida. 
  
   A expressão regular deve seguir a sintaxe do Python para expressões regulares.

1. Envie o pipeline.

   Com base na expressão regular que você fornece, o conjunto de registros é dividido em dois conjuntos de linhas: linhas com valores que correspondem à expressão e todas as linhas restantes. 

Os exemplos a seguir demonstram como dividir um conjunto de um DataSet usando a opção de **expressão regular** . 

### <a name="single-whole-word"></a>Única palavra inteira 

Este exemplo coloca no primeiro conjunto de todos os linhas que contêm o texto `Gryphon` na coluna `Text` . Ele coloca outras linhas na segunda saída de **dados divididos**.

```text
    \"Text" Gryphon  
```

### <a name="substring"></a>Subcadeia de caracteres

Este exemplo procura a cadeia de caracteres especificada em qualquer posição dentro da segunda coluna do conjunto de um. A posição é indicada aqui pelo valor de índice de 1. A correspondência diferencia maiúsculas e minúsculas.

```text
(\1) ^[a-f]
```

O primeiro conjunto de resultados contém todas as linhas em que a coluna de índice começa com um destes caracteres:,,,, `a` `b` `c` `d` `e` , `f` . Todas as outras linhas são direcionadas para a segunda saída.

## <a name="select-a-relative-expression"></a>Selecionar uma expressão relativa

1. Adicione o módulo [dividir dados](./split-data.md) ao seu pipeline e conecte-o como entrada para o DataSet que você deseja dividir.
  
1. Para o **modo de divisão**, selecione **expressão relativa**.
  
1. Na caixa **expressão relacional** , insira uma expressão que executa uma operação de comparação em uma única coluna.

   Para **coluna numérica**:
   - A coluna contém números de qualquer tipo de dados numéricos, incluindo tipos de dados de data e hora.
   - A expressão pode fazer referência a um máximo de um nome de coluna.
   - Use o caractere de e comercial, `&` , para a operação e. Use o caractere de pipe, `|` , para a operação ou.
   - Há suporte para os seguintes operadores:,,,, `<` `>` `<=` `>=` `==` , `!=` .
   - Não é possível agrupar operações usando o `(` e o `)` .
   
   Para **coluna de cadeia de caracteres**:
   - Há suporte para os seguintes operadores: `==` , `!=` .

1. Envie o pipeline.

   A expressão divide o conjunto de um em dois conjuntos de linhas: linhas com valores que atendem à condição e todas as linhas restantes.

Os exemplos a seguir demonstram como dividir um DataSet usando a opção de **expressão relativa** no módulo **dividir dados** .  

### <a name="calendar-year"></a>Ano calendário

Um cenário comum é dividir um conjunto de dados por anos. A expressão a seguir seleciona todas as linhas em que os valores na coluna `Year` são maiores que `2010` .

```text
\"Year" > 2010
```

A expressão de data deve considerar todas as partes de data incluídas na coluna de dados. O formato das datas na coluna de dados deve ser consistente. 

Por exemplo, em uma coluna de data que usa o formato `mmddyyyy` , a expressão deve ser algo assim:

```text
\"Date" > 1/1/2010
```

### <a name="column-index"></a>Índice de coluna

A expressão a seguir demonstra como você pode usar o índice da coluna para selecionar todas as linhas na primeira coluna do conjunto de dados que contém valores menores ou iguais a 30, mas não iguais a 20.

```text
(\0)<=30 & !=20
```


## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 
