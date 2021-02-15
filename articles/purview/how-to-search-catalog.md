---
title: Como pesquisar o catálogo de dados
description: Este artigo fornece uma visão geral de como pesquisar um catálogo de dados.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/15/2020
ms.openlocfilehash: 87ca3c49d6a96f083cc50f01f5b9a4a739d688a1
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551459"
---
# <a name="search-the-azure-purview-data-catalog"></a>Pesquisar no catálogo de dados do Azure alcance

Este artigo descreve como usar os vários recursos de pesquisa no catálogo de dados do Azure alcance.

## <a name="search-the-catalog-for-assets"></a>Pesquisar ativos no catálogo

As etapas para conduzir uma pesquisa de ativo são:

1. [Abra a caixa de diálogo de pesquisa de ativos](#open-the-asset-search-dialog) selecionando **Pesquisar catálogo**.
1. [Insira os termos de pesquisa](#enter-search-terms) para localizar ativos com características que correspondam aos termos.
1. [Defina filtros rápidos](#set-quick-filters) para restringir a pesquisa.
1. [Inicie a pesquisa](#start-the-search) e vá para os resultados da pesquisa.

Não importa se você definiu filtros rápidos antes ou depois de inserir os termos de pesquisa.

Se não houver nenhum termo de pesquisa e nenhum filtro, os resultados da pesquisa incluirão todos os ativos.

### <a name="open-the-asset-search-dialog"></a>Abrir a caixa de diálogo de pesquisa de ativos

Abra a caixa de diálogo de pesquisa de ativos selecionando **Pesquisar catálogo**.

:::image type="content" source="./media/how-to-search-catalog/search-catalog.png" alt-text="Abaixo de ' Catálogo de pesquisa ', há um painel esquerdo com filtros de pesquisa e um painel direito com pesquisas recentes." border="true":::

A caixa de diálogo de pesquisa mostra filtros rápidos, histórico de pesquisa e uma lista de ativos acessados recentemente.

:::image type="content" source="./media/how-to-search-catalog/asset-search-dialog.png" alt-text="A lista de pesquisa está no painel direito, abaixo de ' Catálogo de pesquisa '." border="true":::

### <a name="enter-search-terms"></a>Inserir termos de pesquisa

Insira um ou mais termos de pesquisa no **Catálogo de pesquisa**. Conforme você digita, os termos de pesquisa correspondentes de pesquisas recentes são listados em **suas pesquisas recentes, os** termos de pesquisa de correspondência sugeridos são listados em **sugestões de pesquisa** e os ativos de dados correspondentes são listados em sugestões de **ativos**.

:::image type="content" source="./media/how-to-search-catalog/enter-search-terms.png" alt-text="Captura de tela mostrando os resultados de uma pesquisa que está sendo inserida na caixa Pesquisar catálogo":::

Os resultados da pesquisa incluem somente ativos com uma ou mais características que correspondam aos termos de pesquisa. Essas características incluem nome do ativo, tipo de ativo, classificações e contatos.

#### <a name="types-of-search-criteria"></a>Tipos de critérios de pesquisa

O Azure alcance dá suporte aos seguintes tipos de critérios de pesquisa.

> [!Note]
> Sempre especifique operadores boolianos (**e**, **ou**, **não**) em maiúsculas. Caso contrário, não importa o caso nem espaços extras.

- **Hive**: localizar documentos que contenham **Hive**.
- **banco de dados do hive**: localizar documentos que contêm exatamente o **banco de dados do hive**.
- **Hive ou banco de dados**: encontre documentos que contenham **Hive** ou **banco de dados**, ou ambos.
- **Hive e banco de dados**, **Hive && Database**: localizar documentos que contenham **Hive** e **banco de dados**.
- **Hive e (banco de dados ou depósito)**: localizar documentos que contenham **Hive** e **banco de dados** ou **depósito**, ou ambos.
- **Hive não banco de dados**: localizar documentos que contenham **Hive**, mas não **banco de dados**.
- **HIV**: encontre documentos que contenham uma palavra que comece com o **HIV**. Por exemplo, **HIV**, **Hive**, **hivbar** (* é um curinga que corresponde a qualquer número de caracteres).

### <a name="set-quick-filters"></a>Definir filtros rápidos

A lista de resultados da pesquisa é baseada nos termos de pesquisa que você insere no **Catálogo de pesquisa** e nos valores selecionados para os filtros rápidos.

Um filtro rápido limita a lista de resultados da pesquisa aos ativos que têm um valor selecionado de uma característica. O filtro tem uma lista suspensa e uma caixa de texto. A lista suspensa mostra os valores da característica que estão nos resultados da pesquisa *atual* . Ao lado de cada valor na lista, há uma contagem do número de ativos nos resultados da pesquisa atual que têm esse valor. Se você selecionar um valor na lista, os resultados da pesquisa serão restritos aos ativos que têm esse valor. Você só pode selecionar um valor.

Os resultados da pesquisa atual usados na formação da lista suspensa são determinados por:

- Os termos de pesquisa que são inseridos no **Catálogo de pesquisa**. 
- Os valores que são selecionados nos filtros rápidos.

Aqui está um exemplo do filtro rápido "tipo de ativo".

:::image type="content" source="./media/how-to-search-catalog/asset-type-quick-filter.png" alt-text="O exemplo de filtro rápido do tipo de ativo." border="true":::

Você pode inserir o texto na caixa de texto para restringir os valores na lista suspensa a valores que correspondem ou correspondem parcialmente ao texto. Para obter exemplos de como usar a caixa de texto, consulte [Filtro rápido de pesquisa: filtrar por tipo de ativo](#search-quick-filter-filter-by-asset-type)e [filtro de pesquisa rápida: filtrar por classificação](#search-quick-filter-filter-by-classification).

#### <a name="search-quick-filter-filter-by-asset-type"></a>Filtro rápido de pesquisa: filtrar por tipo de ativo

Para filtrar por tipo de ativo, use o filtro rápido **tipo de ativo** . A lista suspensa mostra os tipos de ativo encontrados nos resultados da pesquisa atual, conforme determinado pelos termos de pesquisa e os filtros rápidos. Para cada tipo, o número de ativos desse tipo é mostrado.

:::image type="content" source="./media/how-to-search-catalog/asset-type-quick-filter.png" alt-text="O tipo de ativo filtros rápidos é realçado. Ele mostra os tipos de ativo e uma contagem de cada um." border="true":::

Selecione um tipo de ativo para restringir os resultados da pesquisa aos ativos desse tipo. Você só pode selecionar um tipo.

Para mostrar apenas os tipos de ativo cujos nomes correspondem a uma cadeia de caracteres, insira a cadeia de caracteres na caixa de texto. Por exemplo, para mostrar apenas os tipos de ativo com **SQL** em seus nomes, insira **SQL**.

:::image type="content" source="./media/how-to-search-catalog/filter-asset-types.png" alt-text="O painel filtros rápidos tem SQL para ' tipo de ativo '. A lista de ativos que contêm o SQL mostra três entradas." border="true":::

Selecione um tipo de ativo para restringir os resultados da pesquisa aos ativos desse tipo. Você pode selecionar apenas um tipo.

#### <a name="search-quick-filter-filter-by-classification"></a>Filtro rápido de pesquisa: filtrar por classificação

Para filtrar por classificação de ativos, use o filtro rápido **classificação** . A lista suspensa mostra as classificações que foram atribuídas a um ou mais ativos nos resultados da pesquisa atual, conforme determinado pelos termos de pesquisa e os filtros rápidos. Para cada classificação, o número de ativos atribuídos a essa classificação é mostrado.

:::image type="content" source="./media/how-to-search-catalog/classification-quick-filter.png" alt-text="Os filtros rápidos de classificação são realçados." border="true":::

Selecione uma classificação para restringir os resultados da pesquisa aos ativos atribuídos a essa classificação. Você só pode selecionar uma classificação.

Para mostrar somente as classificações cujos nomes correspondem a uma cadeia de caracteres, insira a cadeia de caracteres na caixa de texto. Por exemplo, para mostrar somente as classificações com o **número** em seus nomes, insira **número**.

:::image type="content" source="./media/how-to-search-catalog/filter-classifications.png" alt-text="No painel filtros rápidos, a classificação é ' Banco ' e as classificações listadas contêm esse valor." border="true":::

Selecione uma classificação para restringir os resultados da pesquisa aos ativos que foram atribuídos a essa classificação. Não é possível selecionar mais de uma classificação.

#### <a name="search-quick-filter-filter-by-contacts"></a>Filtro rápido de pesquisa: filtrar por contatos

Um *contato* é uma pessoa que está atribuída a um ativo como um proprietário ou especialista. Quando você exibe detalhes do ativo, os contatos são mostrados na guia **contatos** .

Há duas maneiras de Pesquisar ativos que tenham um contato específico atribuído a eles.

- Insira todo ou parte do nome do contato no **Catálogo de pesquisa** e faça uma pesquisa. Os resultados da pesquisa incluirão ativos que têm contatos cujos nomes correspondem aos seus termos de pesquisa.
- Selecione o contato de interesse no filtro rápido de **contato** e faça uma pesquisa.

:::image type="content" source="./media/how-to-search-catalog/contact-quick-filter.png" alt-text="O valor de Person no painel de filtros rápidos é ' Darren '. Há três sugestões no painel de sugestões." border="true":::

## <a name="search-example"></a>Exemplo de pesquisa

Vamos considerar um exemplo hipotético para ver como os termos de pesquisa e os filtros rápidos interagem para determinar os resultados da pesquisa. Em particular, Monitoraremos a contagem do tipo de ativo **armazenamento de BLOBs do Azure**.

- Fazemos a primeira pesquisa sem termos de pesquisa inseridos e nenhum valor selecionado nos filtros rápidos. A pesquisa localiza todos os ativos no catálogo. A lista de resultados da pesquisa e o **tipo de ativo** filtro rápido revela:

    - A lista de resultados da pesquisa tem 164.230 ativos, que são todos os ativos no catálogo.
    - A lista suspensa **tipo de ativo** tem 43 entradas. Esses são todos os tipos de ativo no catálogo. Como cada ativo é de um e apenas um tipo, a soma das contagens dos tipos de ativo 43 é 164.230.
    - O tipo de ativo de **armazenamento de BLOBs do Azure** é a primeira entrada na lista suspensa do filtro rápido de **tipo de ativo** . Os valores são ordenados por Count, o maior primeiro, portanto, o **armazenamento de BLOBs do Azure** é o tipo de ativo mais comum. Sua contagem é 118.174.

- Agora, inserimos **parquet** no **Catálogo de pesquisa** e fazemos outra pesquisa. Os resultados da pesquisa incluem apenas ativos com características que correspondem a **parquet**. Isso reduz todas as contagens, da seguinte maneira:

    - A lista de resultados da pesquisa tem 493 ativos. Somente 493 dos ativos 164.230 no catálogo têm características que correspondem a "parquet".
    - A lista suspensa **tipo de ativo** tem 15 entradas. Cada um dos ativos 493 é de um desses 15 tipos, e a soma das contagens dos 15 tipos é 493.
    - Há 456 ativos do tipo **armazenamento de BLOBs do Azure**. Os outros ativos 37 (493 menos 456) são de um dos outros 14 tipos.

- Agora, veremos a lista suspensa de um filtro rápido diferente, **classificação**:

    - Há 12 classificações para os ativos 493 na lista de resultados da pesquisa. As contagens para os ativos 493 não somam 493, já que qualquer número de classificações pode ser atribuído a um ativo.
    - A classificação de **nome da pessoa** é atribuída a 36 ativos, mais do que qualquer outra classificação.

- Selecionamos a classificação de **nome da pessoa** . A lista de resultados da pesquisa cai para os 36 ativos, como esperado, pois a contagem do **nome da pessoa** era 36. Nenhum desses resultados são do tipo **armazenamento de BLOBs do Azure**.

Podemos concluir que não há nenhum ativo cujo tipo é o **armazenamento de BLOBs do Azure** que corresponde a **parquet** e que tem uma classificação do **nome da pessoa**.

## <a name="start-the-search"></a>Iniciar a pesquisa

Quando você pesquisa, os termos de pesquisa que você insere no **Catálogo de pesquisa** são correspondidos em relação às características do ativo. Essas características incluem nome, tipo, classificação e contatos. Os ativos com características de correspondência aparecem na lista de resultados da pesquisa, a menos que sejam excluídos por um filtro rápido.

Depois de inserir os termos de pesquisa e definir os filtros rápidos, inicie a pesquisa de uma das seguintes maneiras:

- Para pesquisar com base nos termos inseridos, selecione o ícone de pesquisa ( :::image type="icon" source="./media/how-to-search-catalog/search-icon.png"::: ), pressione **Enter** ou selecione **exibir resultados da pesquisa**.
- Para pesquisar usando os termos de uma pesquisa anterior, selecione-os em **suas pesquisas recentes**.
- Para pesquisar usando os termos sugeridos, selecione-os nas **sugestões de pesquisa**.

Selecione um ativo das **sugestões de ativos** para ir diretamente para a página de detalhes do ativo. Nenhuma pesquisa é feita.

A lista de resultados para sugestões e pesquisas de usuários pode ser ligeiramente diferente. Os resultados na lista de sugestões são baseados em correspondências difusas, enquanto os resultados de pesquisa iniciados pelo usuário são baseados em correspondências exatas.

Quando você pesquisa, a página **resultados da pesquisa** é exibida e lista os ativos encontrados pela pesquisa.

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="Captura de tela mostrando os resultados da pesquisa para um valor de pesquisa de contoso.":::

Para ver os detalhes do ativo, selecione um nome de ativo.

Use os controles na parte inferior de uma página de resultados da pesquisa para navegar para outras páginas de resultados da pesquisa.

:::image type="content" source="./media/how-to-search-catalog/page-navigation.png" alt-text="Captura de tela mostrando como navegar pelas páginas de resultados da pesquisa.":::

### <a name="sort-search-results"></a>Classificar resultados da pesquisa

Use **classificar por** para classificar os resultados da pesquisa por **relevância** ou por **nome**.

:::image type="content" source="./media/how-to-search-catalog/sort-by.png" alt-text="Captura de tela mostrando como classificar os resultados da pesquisa. Para este exemplo, a lista suspensa classificar por é definida como relevância.":::

### <a name="search-results-dynamic-filters"></a>Filtros dinâmicos de resultados da pesquisa

O painel de **filtro** na página de **resultados da pesquisa** tem filtros que fornecem filtragem dinâmica dos ativos na lista de resultados da pesquisa. A filtragem é dinâmica no que filtros adicionais podem aparecer com base nas seleções de filtro.

Os filtros dinâmicos têm uma caixa de seleção para cada valor na lista suspensa. Use essas caixas de seleção para filtrar quantos valores desejar.

#### <a name="search-results-dynamic-filter-filter-by-asset-type"></a>Filtro dinâmico de resultados da pesquisa: filtrar por tipo de ativo

Se você selecionar um tipo de ativo na lista suspensa **tipo de ativo** , serão exibidos filtros dinâmicos que fornecerão maneiras adicionais de restringir os resultados da pesquisa. Os filtros variam dependendo do tipo de ativo selecionado. Por exemplo, se você selecionar **banco de dados SQL do Azure**, filtros dinâmicos aparecerão para servidor, banco de dados e esquema. Os valores nesses filtros são dos ativos nos resultados da pesquisa do tipo selecionado.

:::image type="content" source="./media/how-to-search-catalog/asset-type-dynamic-filter.png" alt-text="O item de filtro do banco de dados SQL do Azure é o único item ' tipo de ativo ' selecionado. Um resultado de pesquisa desse tipo de ativo também é realçado." border="true":::

#### <a name="search-results-dynamic-filter-filter-by-classification"></a>Filtro dinâmico de resultados da pesquisa: filtrar por classificação

Cada classificação na lista de **classificação** aplica-se a pelo menos um item na lista de resultados da pesquisa. Selecione uma ou mais classificações para restringir os resultados da pesquisa aos ativos das classificações selecionadas.

:::image type="content" source="./media/how-to-search-catalog/classification-dynamic-filter.png" alt-text="O filtro de classificação de ' resultados da pesquisa ' é realçado." border="true":::

#### <a name="edit-and-delete-search-results-filters"></a>Editar e excluir filtros de resultados de pesquisa

Para remover um filtro, desmarque a caixa de seleção ao lado do nome do filtro.

### <a name="recently-accessed-assets"></a>Ativos acessados recentemente

A seção **acessada recentemente** da caixa de pesquisa expandida exibe os ativos acessados mais recentemente, se houver.

- Selecione **Exibir tudo** na seção **acessado recentemente** para ver a lista completa de ativos acessados recentemente.

   :::image type="content" source="./media/how-to-search-catalog/get-to-recent-view.png" alt-text="Captura de tela mostrando a seção acessada recentemente da caixa de pesquisa expandida.":::

   É exibida uma lista de ativos acessados recentemente.

   :::image type="content" source="./media/how-to-search-catalog/recent.png" alt-text="Captura de tela mostrando uma lista de ativos acessados recentemente.":::

- Para filtrar por nome, insira uma cadeia de caracteres de pesquisa em **Filtrar por nome**.

- Para remover itens da lista, selecione-os com suas caixas de seleção e, em seguida, selecione **remover**.

   :::image type="content" source="./media/how-to-search-catalog/remove-from-recent-view.png" alt-text="Captura de tela mostrando como remover itens de uma lista de ativos acessados recentemente.":::

- Para limpar a lista inteira, selecione **limpar**.

   :::image type="content" source="./media/how-to-search-catalog/clear-recent-view-selections.png" alt-text="Captura de tela mostrando como limpar uma lista de ativos acessados recentemente":::

### <a name="search-assets"></a>Pesquisar ativos

Muitas páginas diferentes de **página inicial** têm uma caixa **Pesquisar ativos** na parte superior. Por exemplo, aqui está uma página de detalhes de ativos, com os **ativos de pesquisa** realçados.

:::image type="content" source="./media/how-to-search-catalog/search-assets.png" alt-text="Captura de tela mostrando uma página de detalhes do ativo com ativos de pesquisa realçados":::

Selecione **Pesquisar ativos** para iniciar uma caixa de pesquisa como aquela que você obtém do **Catálogo de pesquisa** em **casa**, com os mesmos recursos.

:::image type="content" source="./media/how-to-search-catalog/search-assets-dialog.png" alt-text="Captura de tela mostrando uma caixa de ativos de pesquisa expandida.":::

## <a name="next-steps"></a>Próximas etapas

- [Como criar, importar e exportar os termos do glossário](how-to-create-import-export-glossary.md)
- [Como gerenciar modelos de termo para o Glossário de negócios](how-to-manage-term-templates.md)
