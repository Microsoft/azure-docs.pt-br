---
title: Usar um bloco markdown personalizado nos painéis do Azure
description: Saiba como adicionar um bloco markdown a um painel do Azure para exibir conteúdo estático
services: azure-portal
keywords: ''
author: kfollis
ms.author: v-biyu
origin.date: 01/25/2019
ms.date: 03/04/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: ec8cbddda4137656a53fd4968c451cd413959274
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60551545"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Usar um bloco markdown em painéis do Azure para mostrar conteúdo personalizado

É possível adicionar um bloco markdown a painéis do Azure para exibir conteúdo estático personalizado. Por exemplo, você pode mostrar instruções básicas, uma imagem ou um conjunto de hiperlinks com um bloco markdown.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Adicionar um bloco markdown ao seu painel

1. Selecione **Painel** na barra lateral do portal do Azure. Se você criou painéis personalizados, na exibição de painel, use a lista suspensa para selecionar o painel em que o bloco markdown personalizado deve aparecer. Selecione o ícone de edição para abrir a **Galeria de Blocos**.

   ![Captura de tela mostrando o modo de exibição de edição do painel](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

2. Na **Galeria de Blocos**, localize o bloco chamado **Markdown** e clique em **Adicionar**. O bloco é adicionado ao painel e a janela **Editar Markdown** é exibida.

3. Edite os campos **Título**, **Subtítulo** e **Conteúdo** para personalizar o bloco. No exemplo mostrado aqui, o bloco markdown foi editado para exibir informações personalizadas de suporte técnico.

   ![Captura de tela mostrando o modo de exibição de edição do bloco markdown](./media/azure-portal-markdown-tile/azure-portal-edit-markdown-tile.png)

4. Selecione **Concluído** para ignorar o painel **Editar Markdown**. Seu conteúdo é exibido no bloco Markdown, que pode ser redimensionado arrastando a alça no canto inferior direito.

   ![Captura de tela mostrando o bloco markdown personalizado](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Funcionalidades e limitações de conteúdo do markdown

É possível usar qualquer combinação de texto sem formatação, sintaxe de Markdown e conteúdo HTML no bloco markdown. O portal do Azure usa uma biblioteca de software livre chamada _marked_ para transformar seu conteúdo no HTML que é mostrado no bloco. O HTML produzido por _marked_ é pré-processado pelo portal antes de ser renderizado. Essa etapa ajuda a garantir que sua personalização não afete a segurança ou o layout do portal. Durante esse pré-processamento, qualquer parte do HTML que represente uma ameaça em potencial é removida. Os seguintes tipos de conteúdo não são permitidos pelo portal:

* JavaScript: marcas `<script>` e avaliações embutidas de JavaScript serão removidas.
* iframes: marcas `<iframe>` serão removidas.
* Estilo: marcas `<style>` serão removidas. Oficialmente, não há suporte para atributos de estilo embutido em elementos HTML. Você pode achar que alguns elementos de estilo embutido são ideais para você, porém, se eles interferirem no layout do portal, poderão parar de funcionar a qualquer momento. O bloco Markdown destina-se a conteúdo básico e estático que usa os estilos padrão do portal.

## <a name="next-steps"></a>Próximas etapas

* Para criar um painel personalizado, confira [Criar e compartilhar painéis no portal do Azure](../azure-portal/azure-portal-dashboards.md)
