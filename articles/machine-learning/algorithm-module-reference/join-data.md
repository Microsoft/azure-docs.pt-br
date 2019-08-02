---
title: 'Dados de junção: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo ingressar dados de ingresso no serviço de Azure Machine Learning para mesclar DataSets.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterlu
ms.date: 06/01/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 1022bdc26a340b6b54ad840d1fe47674509fa865
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67871666"
---
# <a name="join-data"></a>Unir dados

Este artigo descreve como usar o módulo de **dados de junção** na interface visual do serviço de Azure Machine Learning para mesclar dois DataSets usando uma operação de junção de estilo de banco de dados.  

## <a name="how-to-configure-join-data"></a>Como configurar dados de junção

Para executar uma junção em dois conjuntos de valores, eles devem estar relacionados por uma coluna de chave. Também há suporte para chaves compostas usando várias colunas. 

1. Adicione os conjuntos de dados que você deseja combinar e, em seguida, arraste o módulo de **dado de junção** para seu experimento. 

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
  
    * **Junção interna**: Uma *junção interna* é a operação de junção mais comum. Ele retorna as linhas combinadas somente quando os valores das colunas de chave correspondem.  
  
    * **Junção externa esquerda**: Uma *junção externa esquerda* retorna linhas Unidas para todas as linhas da tabela esquerda. Quando uma linha na tabela esquerda não tem linhas correspondentes na tabela direita, a linha retornada contém valores ausentes para todas as colunas que vêm da tabela direita. Você também pode especificar um valor de substituição para valores ausentes.  
  
    * **Junção externa completa**: Uma *junção externa completa* retorna todas as linhas da tabela esquerda (**Table1**) e da tabela direita (**Table2**).  
  
         Para cada uma das linhas em uma das tabelas que não têm linhas correspondentes no outro, o resultado inclui uma linha que contém valores ausentes.  
  
    * **Semijunção à esquerda**: Uma *semijunção à esquerda* retorna somente os valores da tabela esquerda quando os valores das colunas de chave correspondem.  

1. Para a opção, **Mantenha as colunas de chave direita na tabela unida**:

    * Selecione esta opção para exibir as chaves de ambas as tabelas de entrada.
    * Anular seleção para retornar apenas as colunas de chave da entrada à esquerda.

1. Execute o experimento ou selecione o módulo ingressar dados e selecionou a **execução selecionada** para executar a junção.

1. Para exibir os resultados, clique com o botão  > direito do mouse no conjunto > de dados de**resultados da**junção **.**

## <a name="next-steps"></a>Próximas etapas

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning serviço. 