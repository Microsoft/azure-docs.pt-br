---
title: Transformação não dinâmica no fluxo de dados de mapeamento
description: Transformação não dinâmica do fluxo de dados de mapeamento de Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/14/2020
ms.openlocfilehash: ef861cdf394716a70d85e43ce9c60f46af2cc2e4
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040208"
---
# <a name="unpivot-transformation-in-mapping-data-flow"></a>Transformação não dinâmica no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Use o fluxo de dados de mapeamento do AAD no ADF como uma maneira de transformar um conjunto de dado não normalizado em uma versão mais normalizada expandindo valores de várias colunas em um único registro em vários registros com os mesmos valores em uma única coluna.

![Captura de tela mostra não dinâmico selecionado no menu.](media/data-flow/unpivot1.png "Opções não dinâmicas 1")

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4B1RR]

## <a name="ungroup-by"></a>Desagrupar por

![Captura de tela mostra as configurações não dinâmicas com a guia desagrupar por selecionada.](media/data-flow/unpivot5.png "Opções não dinâmicas 2")

Primeiro, defina as colunas que você deseja desagrupar por para a agregação não dinâmica. Defina uma ou mais colunas para desagrupamento com o sinal + ao lado da lista de colunas.

## <a name="unpivot-key"></a>Chave não dinâmica

![Captura de tela mostra as configurações não dinâmicas com a guia de chave não dinâmica selecionada.](media/data-flow/unpivot6.png "Opções não dinâmicas 3")

A chave não dinâmica é a coluna que o ADF dinamizará da coluna para a linha. Por padrão, cada valor exclusivo no conjunto de valores para esse campo será dinamizado para uma linha. No entanto, opcionalmente, você pode inserir os valores do conjunto de um que você deseja dinamizar para os valores de linha.

## <a name="unpivoted-columns"></a>Colunas não dinâmicas

![Captura de tela mostra as configurações não dinâmicas com a guia Visualização de dados selecionada.](media/data-flow//unpivot7.png "Opções de UNPIVOT 4")

Por fim, escolha o nome da coluna para armazenar os valores de colunas não dinâmicas que são transformadas em linhas.

Adicional Você pode remover linhas com valores nulos.

Por exemplo, SumCost é o nome da coluna que é escolhido no exemplo compartilhado acima.

![Imagem mostrando as colunas PO, Vendor e fruta antes e depois de uma transformação unipivot usando a coluna fruta como a chave unipivot.](media/data-flow/unpivot3.png)

Definir a organização da coluna como "normal" agrupará todas as novas colunas não dinâmicas de um único valor. Definir a organização das colunas como "lateral" agrupará novas colunas não dinâmicas geradas a partir de uma coluna existente.

![Captura de tela mostra o resultado da transformação.](media/data-flow//unpivot7.png "Opções de UNPIVOT 5")

O conjunto final de resultados de dados não dinâmicos mostra os totais de colunas agora transformadas em valores de linha separados.

## <a name="next-steps"></a>Próximas etapas

Use a [transformação dinâmica](data-flow-pivot.md) para dinamizar linhas para colunas.
