---
title: Gerenciar fontes de dados no Azure alcance (versão prévia)
description: Saiba como registrar novas fontes de dados, gerenciar coleções de fontes de dados e exibir fontes no Azure alcance (versão prévia).
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: 90a873b7de6ccc1ba21a05bf4c0e288ed668cac0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694454"
---
# <a name="manage-data-sources-in-azure-purview-preview"></a>Gerenciar fontes de dados no Azure alcance (versão prévia)

Neste artigo, você aprende a registrar novas fontes de dados, gerenciar coleções de fontes de dados e exibir fontes no Azure alcance (versão prévia)

## <a name="register-a-new-source"></a>Registrar uma nova fonte

Use as etapas a seguir para registrar uma nova fonte.

1. Abra o alcance Studio e selecione o bloco **registrar fontes** .

   :::image type="content" source="media/manage-data-sources/purview-studio.png" alt-text="Azure alcance Studio":::

1. Selecione **registrar** e, em seguida, selecione um tipo de fonte. Este exemplo usa o armazenamento de BLOBs do Azure. Selecione **Continuar**.

   :::image type="content" source="media/manage-data-sources/select-source-type.png" alt-text="Selecione um tipo de fonte de dados na página registrar fontes":::

1. Preencha o formulário na página **registrar fontes** . Selecione um nome para a origem e insira as informações relevantes. Se você escolher **da assinatura do Azure** como seu método de seleção de conta, as fontes em sua assinatura aparecerão em uma lista suspensa. Como alternativa, você pode inserir suas informações de origem manualmente.

   :::image type="content" source="media/manage-data-sources/register-sources-form.png" alt-text="Formulário para informações da fonte de dados":::

1. Selecione **Concluir**.

## <a name="view-sources"></a>Exibir fontes

Você pode exibir todas as fontes registradas na guia **fontes** do Azure alcance Studio. Há dois tipos de modos de exibição: exibição de mapa e exibição de lista.

### <a name="map-view"></a>Exibição do mapa

No modo de exibição de mapa, você pode ver todas as suas fontes e coleções. Na imagem a seguir, há uma fonte de armazenamento de BLOBs do Azure. Em cada bloco de origem, você pode editar a origem, iniciar uma nova verificação ou exibir detalhes de origem.

:::image type="content" source="media/manage-data-sources/map-view.png" alt-text="Exibição de mapa da fonte de dados do Azure alcance":::

### <a name="list-view"></a>Modo de exibição de lista

No modo de exibição de lista, você pode ver uma lista classificável de fontes. Passe o mouse sobre a fonte para obter as opções para editar, iniciar uma nova verificação ou excluir.

:::image type="content" source="media/manage-data-sources/list-view.png" alt-text="Exibição de lista de fontes de dados do Azure alcance":::

## <a name="manage-collections"></a>Gerenciar coleções

Você pode agrupar suas fontes de dados em coleções. Para criar uma nova coleção, selecione **+ nova coleção** na página *fontes* do Azure alcance Studio. Dê um nome à coleção e selecione *nenhuma* como pai. A nova coleção aparece na exibição de mapa.

Para adicionar fontes a uma coleção, selecione o lápis de **edição** na origem e escolha uma coleção no menu suspenso **selecionar uma coleção** .

Para criar uma hierarquia de coleções, atribua coleções de nível superior como um pai a coleções de nível inferior. Na imagem a seguir, a *Fabrikam* é um pai da coleção *Finance* , que contém uma fonte de dados de armazenamento de BLOBs do Azure. Você pode recolher ou expandir coleções clicando no círculo anexado à seta entre níveis.

:::image type="content" source="media/manage-data-sources/collections.png" alt-text="Uma hierarquia de coleções no Azure alcance Studio":::

Você pode remover fontes de uma hierarquia selecionando *nenhuma* para o pai. As fontes não pai são agrupadas em uma caixa pontilhada no modo de exibição de mapa sem setas vinculando-as aos pais.

## <a name="next-steps"></a>Próximas etapas

Saiba como registrar e verificar várias fontes de dados:

* [Azure Data Lake Storage Gen 2](register-scan-adls-gen2.md)
* [Power BI locatário](register-scan-power-bi-tenant.md)
* [Banco de Dados SQL do Azure](register-scan-azure-sql-database.md)
