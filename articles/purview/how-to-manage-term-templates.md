---
title: Como gerenciar modelos de termo para o Glossário de negócios
description: Saiba como gerenciar modelos de termo para o Glossário de negócios em um catálogo de dados do Azure alcance.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/04/2020
ms.openlocfilehash: b1b811d0817d5e23adc208da14719d64d53830dd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96551846"
---
# <a name="how-to-manage-term-templates-for-business-glossary"></a>Como gerenciar modelos de termo para o Glossário de negócios

O Azure alcance permite que você crie um glossário de termos que são importantes para enriquecer seus dados. Cada novo termo adicionado ao seu Glossário do catálogo de dados do alcance é baseado em um modelo de termo que determina os campos para o termo. Este artigo descreve como criar um modelo de termo e atributos personalizados que podem ser associados aos termos do glossário.

## <a name="manage-term-templates-and-custom-attributes"></a>Gerenciar modelos de termo e atributos personalizados

Usando modelos de termo, você pode criar atributos personalizados, agrupá-los e aplicar um modelo ao criar termos. Depois que um termo é criado, o modelo associado ao termo não pode ser alterado.

1. Na página **termos do glossário** , selecione **gerenciar modelos de termo**.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/manage-term-templates.png" alt-text="Captura de tela dos termos do glossário > botão gerenciar modelos de termo.":::

2. A página mostra os atributos de sistema e personalizados. Selecione a guia **personalizado** para criar ou editar modelos de termo.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/manage-term-custom.png" alt-text="Captura de tela dos termos do glossário > página Gerenciar modelos de termo.":::

3. Selecione **+ novo termo modelo** e insira um nome de modelo e uma descrição.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/new-term-template.png" alt-text="Captura de tela dos termos do glossário > gerenciar modelos de termo > modelos de novos termos":::

4. Selecione **+ novo atributo** para criar um novo atributo personalizado para o modelo de termo. Insira um nome de atributo, uma descrição. O nome do atributo personalizado deve ser exclusivo dentro de um modelo de termo, mas pode ser o mesmo nome pode ser reutilizado em todos os modelos.

   Escolha o tipo de campo na lista de opções **texto**, **escolha única**, seleção **múltipla** ou  **Data**. Você também pode fornecer uma cadeia de caracteres de valor padrão para tipos de campo de texto.  O atributo também pode ser marcado como **obrigatório**.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/new-attribute.png" alt-text="Captura de tela dos termos do glossário > nova página de atributo.":::

5. Depois que todos os atributos personalizados forem criados, selecione **Visualizar** para organizar a sequência de atributos personalizados. Você pode arrastar e soltar atributos personalizados na sequência desejada.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/preview-term-template.png" alt-text="Captura de tela dos termos do glossário > modelo de termo de visualização.":::

6. Depois que todos os atributos personalizados forem definidos, selecione **criar** para criar um modelo de termo com atributos personalizados.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/create-term-template.png" alt-text="Captura de tela dos termos do glossário > novo termo modelo – criar botão.":::

7. Os atributos personalizados existentes podem ser marcados como expirados marcando **Marcar como expirado**. Depois de expirar, o atributo não pode ser reativado. O atributo expirado ficará esmaecido para os termos existentes. Novos termos futuros criados com este modelo de termo não mostrarão mais o atributo que foi marcado como expirado.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/expired-attribute.png" alt-text="Captura de tela dos termos do glossário > atributo editar para marcá-lo como expirado.":::

## <a name="next-steps"></a>Próximas etapas

Siga o [tutorial: criar e importar os termos do glossário](tutorial-import-create-glossary-terms.md) para saber mais.
