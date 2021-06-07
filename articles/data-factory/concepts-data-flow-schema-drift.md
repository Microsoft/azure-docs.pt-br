---
title: Descompasso de esquema no fluxo de dados de mapeamento
description: Construa fluxos de dados resistentes no Azure Data Factory com descompasso de esquema
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/15/2020
ms.openlocfilehash: 11ddb2f40ee56b51c5ecbae11465093abb8e4feb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93027475"
---
# <a name="schema-drift-in-mapping-data-flow"></a>Descompasso de esquema no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A descompasso de esquema é o caso em que suas fontes geralmente alteram metadados. Campos, colunas e tipos podem ser adicionados, removidos ou alterados em tempo real. Sem lidar com descompasso de esquema, seu fluxo de dados se torna vulnerável a alterações de fonte de dados upstream. Padrões de ETL típicos falham quando colunas e campos de entrada são alterados porque tendem a ser vinculados a esses nomes de origem.

Para se proteger contra descompasso de esquema, é importante ter as instalações em uma ferramenta de fluxo de dados para permitir que você, como engenheiro de dados,:

* Definir fontes com nomes de campo mutáveis, tipos de dados, valores e tamanhos
* Defina parâmetros de transformação que funcionam com padrões de dados ao invés de campos e valores codificados
* Defina expressões que compreendam padrões para corresponder aos campos recebidos, ao invés de usar campos nomeados

Azure Data Factory nativamente dá suporte a esquemas flexíveis que mudam da execução para a execução para que você possa criar uma lógica de transformação de dados genérica sem a necessidade de recompilar os fluxos de dados.

Você precisa tomar uma decisão sobre a arquitetura de seu fluxo de dados para aceitar o descompasso de esquema em todo o fluxo. Ao fazer isso, você pode proteger contra alterações de esquema das fontes. No entanto, você perderá a ligação antecipada de suas colunas e tipos em todo o fluxo de dados. Azure Data Factory trata os fluxos de descompasso de esquema como fluxos de ligação tardia, portanto, quando você cria suas transformações, os nomes de coluna desfeitos não estarão disponíveis para você nas exibições de esquema em todo o fluxo.

Este vídeo fornece uma introdução a algumas das soluções complexas que você pode criar facilmente no ADF com o recurso de descompasso de esquema do fluxo de dados. Neste exemplo, criamos padrões reutilizáveis com base em esquemas de banco de dados flexíveis:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tyx7]

## <a name="schema-drift-in-source"></a>Descompasso de esquema na origem

As colunas que entram em seu fluxo de dados da definição de origem são definidas como "descompassos" quando não estão presentes na projeção de origem. Você pode exibir a projeção de origem na guia projeção na transformação origem. Quando você seleciona um conjunto de um DataSet para sua origem, o ADF pegará automaticamente o esquema do conjunto de e criará uma projeção da definição de esquema do conjunto de mesmos.

Em uma transformação de origem, a descompasso de esquema é definida como uma leitura de colunas que não definem seu esquema de conjunto de linhas. Para habilitar a descompasso de esquema, marque **permitir descompasso de esquema** em sua transformação de origem.

![Origem de descompasso de esquema](media/data-flow/schemadrift001.png "Origem de descompasso de esquema")

Quando a descompasso de esquema está habilitada, todos os campos de entrada são lidos de sua origem durante a execução e passados por todo o fluxo para o coletor. Por padrão, todas as colunas recentemente detectadas, conhecidas como *colunas descompassos*, chegam como um tipo de dados de cadeia de caracteres. Se você quiser que o fluxo de dados infira automaticamente os tipos de dados de colunas desfeitas, marque **inferir tipos de coluna** desfeitas em suas configurações de origem.

## <a name="schema-drift-in-sink"></a>Descompasso de esquema no coletor

Em uma transformação de coletor, o descompasso de esquema é quando você grava colunas adicionais sobre o que é definido no esquema de dados do coletor. Para habilitar a descompasso de esquema, marque **permitir descompasso de esquema** na transformação do coletor.

![Coletor de descompasso de esquema](media/data-flow/schemadrift002.png "Coletor de descompasso de esquema")

Se a descompasso de esquema estiver habilitada, verifique se o controle deslizante de **mapeamento automático** na guia mapeamento está ativado. Com esse controle deslizante ativado, todas as colunas de entrada são gravadas no destino. Caso contrário, você deve usar o mapeamento baseado em regras para gravar colunas descompassos.

![Mapeamento automático do coletor](media/data-flow/automap.png "Mapeamento automático do coletor")

## <a name="transforming-drifted-columns"></a>Transformando colunas descompassos

Quando o fluxo de dados tiver colunas descompassos, você poderá acessá-las em suas transformações com os seguintes métodos:

* Use as `byPosition` `byName` expressões e para referenciar explicitamente uma coluna por nome ou número de posição.
* Adicionar um padrão de coluna em uma transformação de agregação ou coluna derivada para corresponder a qualquer combinação de nome, fluxo, posição, origem ou tipo
* Adicionar mapeamento baseado em regra em uma transformação SELECT ou Sink para corresponder colunas perrespondidas a aliases de colunas por meio de um padrão

Para obter mais informações sobre como implementar padrões de coluna, consulte [padrões de coluna no fluxo de dados de mapeamento](concepts-data-flow-column-pattern.md).

### <a name="map-drifted-columns-quick-action"></a>Ação rápida mapear colunas descompassos

Para referenciar explicitamente colunas descompassos, você pode gerar rapidamente mapeamentos para essas colunas por meio de uma ação rápida de visualização de dados. Depois que o [modo de depuração](concepts-data-flow-debug-mode.md) estiver ativado, vá para a guia Visualização de dados e clique em **Atualizar** para buscar uma visualização de dados. Se data factory detectar que as colunas descompassos existem, você poderá clicar em **mapear descompasso** e gerar uma coluna derivada que permite que você referencie todas as colunas descompassos em exibições de esquema downstream.

![Captura de tela mostra a guia Visualização de dados com o mapa desatualizado chamado.](media/data-flow/mapdrifted1.png "Mapa descompasso")

Na transformação coluna derivada gerada, cada coluna descompasso é mapeada para seu nome e tipo de dados detectados. Na visualização de dados acima, a coluna ' MovieID ' é detectada como um inteiro. Depois que o **mapa** é clicado, o MovieID é definido na coluna derivada como `toInteger(byName('movieId'))` e incluído em exibições de esquema em transformações de downstream.

![Captura de tela mostra a guia Configurações da coluna derivada.](media/data-flow/mapdrifted2.png "Mapa descompasso")

## <a name="next-steps"></a>Próximas etapas
Na [linguagem de expressão de fluxo de dados](data-flow-expression-functions.md), você encontrará recursos adicionais para padrões de coluna e descompasso de esquema, incluindo "byName" e "byPosition".
