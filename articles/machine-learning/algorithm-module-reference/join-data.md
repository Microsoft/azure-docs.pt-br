---
title: 'Dados de junção: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo ingressar dados no designer de Azure Machine Learning para mesclar dois conjuntos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: c23dca40d50c5837bd9ff45bc3c3d7fb2581685b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420743"
---
# <a name="join-data"></a>Unir dados

Este artigo descreve como usar o módulo **ingressar dados** no designer de Azure Machine Learning para mesclar dois DataSets usando uma operação de junção de estilo de banco de dado.  

## <a name="how-to-configure-join-data"></a>Como configurar dados de junção

Para executar uma junção em dois conjuntos de valores, eles devem estar relacionados por uma coluna de chave. Também há suporte para chaves compostas usando várias colunas. 

1. Adicione os conjuntos de dados que você deseja combinar e, em seguida, arraste o módulo de **dado de junção** para seu pipeline. 

    Você pode encontrar o módulo na categoria **transformação de dados** , em **manipulação**.

1. Conecte os conjuntos de dados ao módulo **unir data** . 
 
1. Selecione **Iniciar seletor de coluna** para escolher coluna (s) de chave. Lembre-se de escolher colunas para as entradas à esquerda e à direita.

    Para uma única chave:

    Selecione uma única coluna de chave para ambas as entradas.
    
    Para uma chave composta:

    Selecione todas as colunas de chave da entrada esquerda e a entrada direita na mesma ordem. O módulo de **dados de junção** unirá as tabelas quando todas as colunas de chave forem correspondentes. Marque a opção **permitir duplicatas e preservar a ordem das colunas na seleção** se a ordem da coluna não for igual à tabela original. 

    ![column-selector](media/module/join-data-column-selector.png)


1. Selecione a opção **corresponder caso** para preservar a diferenciação de maiúsculas e minúsculas em uma junção de coluna de texto. 
   
1. Use a lista suspensa **tipo de junção** para especificar como os conjuntos de valores devem ser combinados.  
  
    * **Junção interna**: uma *junção interna* é a operação de junção mais comum. Retorna as linhas combinadas somente quando os valores das colunas de chave correspondem.  
  
    * **Junção externa esquerda**: uma *junção externa esquerda* retorna linhas Unidas para todas as linhas da tabela esquerda. Quando uma linha na tabela esquerda não tem linhas correspondentes na tabela direita, a linha retornada contém valores ausentes para todas as colunas que vêm da tabela direita. Você também pode especificar um valor de substituição para valores ausentes.  
  
    * **Junção externa completa**: uma *junção externa completa* retorna todas as linhas da tabela esquerda (**Table1**) e da tabela direita (**Table2**).  
  
         Para cada uma das linhas em uma das tabelas que não têm linhas correspondentes no outro, o resultado inclui uma linha que contém valores ausentes.  
  
    * **Semijunção à esquerda**: uma *semijunção à esquerda* retorna somente os valores da tabela esquerda quando os valores das colunas de chave correspondem.  

1. Para a opção, **Mantenha as colunas de chave direita na tabela unida**:

    * Selecione esta opção para exibir as chaves de ambas as tabelas de entrada.
    * Anular seleção para retornar apenas as colunas de chave da entrada à esquerda.

1. Envie o pipeline.

1. Para exibir os resultados, clique com o botão direito do mouse nos **dados de junção** e selecione **Visualizar**.

## <a name="next-steps"></a>Próximas etapas

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning. 