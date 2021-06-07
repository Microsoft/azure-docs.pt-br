---
title: Aplicar classificações em ativos (visualização)
description: Este documento descreve como aplicar classificações em ativos.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: d12a7d52562fe32126e12a844c2d36c14cf01431
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96551580"
---
# <a name="apply-classifications-on-assets-in-azure-purview"></a>Aplicar classificações em ativos no Azure alcance

Este artigo discute como aplicar classificações em ativos.

## <a name="introduction"></a>Introdução

As classificações podem ser de tipos personalizados ou de sistema. As classificações do sistema estão presentes no alcance por padrão. As classificações personalizadas podem ser criadas com base em um padrão de expressão regular. As classificações podem ser aplicadas a ativos de forma automática ou manual.

Este documento explica como aplicar classificações aos seus dados.

## <a name="prerequisites"></a>Pré-requisitos

- Crie classificações personalizadas com base em sua necessidade.
- Configure a verificação em suas fontes de dados.

## <a name="apply-classifications"></a>Aplicar classificações
No Azure alcance, você pode aplicar classificações personalizadas ou do sistema em um ativo de arquivo, tabela ou coluna. Este artigo descreve as etapas para aplicar manualmente as classificações em seus ativos.

### <a name="apply-classification-to-a-file-asset"></a>Aplicar classificação a um ativo de arquivo
O Azure alcance pode examinar e classificar automaticamente os arquivos de documentação. Por exemplo, se você tiver um arquivo chamado *multiple.docx* e ele tiver um número de ID nacional em seu conteúdo, o Azure alcance adicionará o **número de identificação nacional da UE** de classificação à página de detalhes do ativo do arquivo.

Em alguns cenários, talvez você queira adicionar manualmente as classificações ao seu ativo de arquivo. Se você tiver vários arquivos agrupados em um conjunto de recursos, adicione classificações no nível do conjunto de recursos.

Siga estas etapas para adicionar uma classificação personalizada ou de sistema a um conjunto de recursos de partição:

1. Pesquise ou procure a partição e navegue até a página de detalhes do ativo.

    :::image type="content" source="./media/apply-classifications/asset-detail-page.png" alt-text="Captura de tela mostrando a página de detalhes do ativo.":::

1. Na guia **visão geral** , exiba a seção **classificações** para ver se há quaisquer classificações existentes. Selecione **Editar**.

1. Na lista suspensa **classificações** , selecione as classificações específicas nas quais você está interessado. Por exemplo, **número de cartão de crédito**, que é uma classificação de sistema e **CustomerAccountID**, que é uma classificação personalizada.

    :::image type="content" source="./media/apply-classifications/select-classifications.png" alt-text="Captura de tela mostrando como selecionar classificações a serem adicionadas a um ativo.":::

1. Selecione **Salvar**

1. Na guia **visão geral** , confirme se as classificações selecionadas aparecem na seção **classificações** .

    :::image type="content" source="./media/apply-classifications/confirm-classifications.png" alt-text="Captura de tela mostrando como confirmar se as classificações foram adicionadas a um ativo.":::

### <a name="apply-classification-to-a-table-asset"></a>Aplicar classificação a um ativo de tabela

Quando o Azure alcance examina suas fontes de dados, ele não atribui classificações automaticamente a ativos de tabela. Se desejar que o ativo de tabela tenha uma classificação, você deverá adicioná-lo manualmente.

Para adicionar uma classificação a um ativo de tabela:

1. Encontre um ativo de tabela no qual você esteja interessado. Por exemplo, tabela **Customer** .

1. Confirme se nenhuma classificação foi atribuída à tabela. Selecionar **Editar**

    :::image type="content" source="./media/apply-classifications/select-edit-from-table-asset.png" alt-text="Captura de tela mostrando como exibir e editar as classificações de um ativo de tabela.":::

1. Na lista suspensa **classificações** , selecione uma ou mais classificações. Este exemplo usa uma classificação personalizada chamada **CustomerInfo**, mas você pode selecionar quaisquer classificações para esta etapa.

    :::image type="content" source="./media/apply-classifications/select-classifications-in-table.png" alt-text="Captura de tela mostrando como selecionar classificações a serem adicionadas a um ativo de tabela.":::

1. Selecione **salvar** para salvar as classificações.

1. Na página **visão geral** , verifique se o Azure alcance adicionou suas novas classificações.

    :::image type="content" source="./media/apply-classifications/verify-classifications-added-to-table.png" alt-text="Captura de tela mostrando como verificar se as classificações foram adicionadas a um ativo de tabela.":::

### <a name="add-classification-to-a-column-asset"></a>Adicionar classificação a um ativo de coluna

O Azure alcance examina e adiciona classificações automaticamente a todos os ativos de coluna. No entanto, se você quiser alterar a classificação, poderá fazê-lo no nível da coluna.

Para adicionar uma classificação a uma coluna:

1. Localize e selecione o ativo da coluna e, em seguida, selecione **Editar** na guia **visão geral** .

1. Selecione a guia **esquema**

    :::image type="content" source="./media/apply-classifications/edit-column-schema.png" alt-text="Captura de tela mostrando como editar o esquema de uma coluna.":::

1. Identifique as colunas nas quais você está interessado e selecione **Adicionar uma classificação**. Este exemplo adiciona uma classificação de **senhas comuns** à coluna **PasswordHash** .

    :::image type="content" source="./media/apply-classifications/add-classification-to-column.png" alt-text="Captura de tela mostrando como adicionar uma classificação a uma coluna.":::

1. Selecione **Salvar**

1. Selecione a guia **esquema** e confirme se a classificação foi adicionada à coluna.

    :::image type="content" source="./media/apply-classifications/confirm-classification-added.png" alt-text="Captura de tela mostrando como confirmar se uma classificação foi adicionada a um esquema de coluna.":::

## <a name="impact-of-rescanning-on-existing-classifications"></a>Impacto da nova verificação em classificações existentes

As classificações são aplicadas na primeira vez, com base na verificação do conjunto de exemplo em seus dados e na correspondência com o padrão de Regex definido. No momento da nova verificação, se novas classificações se aplicarem, a coluna obterá classificações adicionais. As classificações existentes permanecem na coluna e devem ser removidas manualmente.

## <a name="next-steps"></a>Próximas etapas
Para saber como criar uma classificação personalizada, consulte [criar uma classificação personalizada](create-a-custom-classification-and-classification-rule.md).