---
title: Sintaxe de consulta de pesquisa de grafo
titleSuffix: Azure Machine Learning
description: Saiba como usar a sintaxe de consulta de pesquisa no designer de Azure Machine Learning para procurar nós no grafo de pipeline.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 8/24/2020
ms.openlocfilehash: 762581ea5b3183d62913e9ea6935bf7e4c4ae67f
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420760"
---
# <a name="graph-search-query-syntax"></a>Sintaxe de consulta de pesquisa de grafo

Neste artigo, você aprenderá sobre a sintaxe de consulta de pesquisa de grafo no Azure Machine Learning. O recurso de pesquisa de grafo permite pesquisar um nó por seu nome e suas propriedades. 

 ![Captura de tela animada mostrando uma experiência de pesquisa de gráfico de exemplo](media/search/graph-search.gif)

A pesquisa de grafo dá suporte à pesquisa de palavra-chave de texto completo no nome do nó e comentários. Você também pode filtrar a propriedade de nó como runStatus, Duration, computeTarget. A pesquisa de palavra-chave é baseada na consulta Lucene. Uma consulta de pesquisa completa tem esta aparência:  

**[consulta Lucene | [consulta de filtro]** 

Você pode usar a consulta Lucene ou a consulta de filtro. Para usar ambos, use o **|** separador. A sintaxe da consulta de filtro é mais estrita do que a consulta Lucene. Portanto, se a entrada do cliente puder ser analisada como ambos, a consulta de filtro será aplicada.

 

## <a name="lucene-query"></a>Consulta Lucene

A pesquisa de grafo usa a consulta simples Lucene como sintaxe de pesquisa de texto completo no nó "nome" e "comentário". Há suporte para os seguintes operadores Lucene:

 
- E/OU
- Correspondência de curinga com **?** e * *\** _ operadores.

### <a name="examples"></a>Exemplos

- Pesquisa simples: `JSON Data`

- E/OU: `JSON AND Validation`

- Vários e/ou: `(JSON AND Validation) OR (TSV AND Training)`

 
- Correspondência de curinga: 
    - `machi?e learning`
    - `mach_ing`
 
>[!NOTE]
> Não é possível iniciar uma consulta Lucene com um caractere "*".

##  <a name="filter-query"></a>Consulta de filtro

 
As consultas de filtro usam o seguinte padrão:
 
`**[key1] [operator1] [value1]; [key2] [operator1] [value2];**`

 
Você pode usar as seguintes propriedades de nó como chaves:

- runStatus
- computação
- duration
- posterior

E use os seguintes operadores:

- Maior ou igual a: `>=`
- Menor ou igual a: `<=`
- Grande `>`
- Inferiores `<`
- Iguais `==`
- Contêm `=`
- NotEqual `!=`
- No `in`

 
 

### <a name="example"></a>Exemplo

- duração > 100;
- status em {Failed, não foi iniciado}
- computação em {GPU-cluster}; runStatus em {Completed}

## <a name="technical-notes"></a>Observações técnicas

- A relação entre vários filtros é "e"
- Se `>=,  >,  <, or  <=` for escolhido, os valores serão automaticamente convertidos em tipo de número. Caso contrário, os tipos de cadeia de caracteres são usados para comparação.
- Para todos os valores de tipo de cadeia de caracteres, Case não diferencia em comparação.
- O operador "in" espera uma coleção como valor, a sintaxe da coleção é `{name1, name2, name3}`
- O espaço será ignorado entre palavras-chave