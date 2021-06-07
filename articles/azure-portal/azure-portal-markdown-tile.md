---
title: Usar um bloco markdown personalizado nos painéis do Azure
description: Saiba como adicionar um bloco markdown a um painel do Azure para exibir conteúdo estático
ms.date: 03/19/2021
ms.topic: how-to
ms.custom: devx-track-js
ms.openlocfilehash: 8324b736565cfa353e48cf49b76e2784866f47f7
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104774449"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Usar um bloco markdown em painéis do Azure para mostrar conteúdo personalizado

É possível adicionar um bloco markdown a painéis do Azure para exibir conteúdo estático personalizado. Por exemplo, você pode mostrar instruções básicas, uma imagem ou um conjunto de hiperlinks em um bloco de redução.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Adicionar um bloco markdown ao seu painel

1. Selecione **Painel** na barra lateral do portal do Azure.

   ![Captura de tela mostrando a barra lateral do portal](./media/azure-portal-markdown-tile/azure-portal-nav.png)

1. Na exibição painel, selecione o painel em que o bloco de redução personalizado deve aparecer e, em seguida, selecione **Editar**.

   ![Captura de tela mostrando o modo de exibição de edição do painel](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

1. Na **Galeria de blocos**, localize o bloco chamado **redução** e selecione **Adicionar**. O bloco é adicionado ao painel e a janela **Editar Markdown** é exibida.

1. Insira valores para **título** e **subtítulo**, que são exibidos no bloco depois de você passar para outro campo.

   ![Captura de tela mostrando os resultados da inserção do título e do subtítulo](./media/azure-portal-markdown-tile/azure-portal-dashboard-enter-title.png)

1. Selecione uma das opções para incluir o conteúdo de redução: **edição embutida** ou **Inserir conteúdo usando a URL**.

   - Selecione **edição embutida** se desejar inserir a redução diretamente.

      ![Captura de tela mostrando a inserção de conteúdo embutido](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-inline-content.png)

   - Selecione **Inserir conteúdo usando a URL** se você quiser usar o conteúdo de redução existente que está hospedado online.

      ![Captura de tela mostrando a inserção da URL](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-url.png)

      > [!NOTE]
      > Para maior segurança, você pode criar um arquivo de redução e armazená-lo em um [BLOB da conta de armazenamento do Azure em que a criptografia está habilitada](../storage/common/storage-service-encryption.md)e, em seguida, apontar para o arquivo usando a opção URL. O conteúdo de redução é criptografado por meio das opções de criptografia da conta de armazenamento. Somente os usuários com permissões para o arquivo podem ver o conteúdo de redução no painel. Talvez seja necessário definir uma regra de [compartilhamento de recursos entre origens (CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) na conta de armazenamento para que o portal do Azure ( _https://portal.azure.com/_ ) possa acessar o arquivo de redução no BLOB.

1. Selecione **Concluído** para ignorar o painel **Editar Markdown**. Seu conteúdo aparece no bloco de redução, que você pode redimensionar arrastando a alça no canto inferior direito.

   ![Captura de tela mostrando o bloco markdown personalizado](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Funcionalidades e limitações de conteúdo do markdown

É possível usar qualquer combinação de texto sem formatação, sintaxe de Markdown e conteúdo HTML no bloco markdown. O portal do Azure usa uma biblioteca de software livre chamada _marked_ para transformar seu conteúdo no HTML que é mostrado no bloco. O HTML produzido por _marked_ é pré-processado pelo portal antes de ser renderizado. Essa etapa ajuda a garantir que sua personalização não afete a segurança ou o layout do portal. Durante esse pré-processamento, qualquer parte do HTML que represente uma ameaça em potencial é removida. Os seguintes tipos de conteúdo não são permitidos pelo portal:

* JavaScript – as `<script>` marcas e as avaliações de JavaScript embutidas são removidas.
* iframes-as `<iframe>` marcas são removidas.
* Marcas de estilo `<style>` são removidas. Oficialmente, não há suporte para atributos de estilo embutido em elementos HTML. Você pode achar que alguns elementos de estilo embutido são ideais para você, porém, se eles interferirem no layout do portal, poderão parar de funcionar a qualquer momento. O bloco Markdown destina-se a conteúdo básico e estático que usa os estilos padrão do portal.

## <a name="next-steps"></a>Próximas etapas

* Para criar um painel personalizado, confira [Criar e compartilhar painéis no portal do Azure](../azure-portal/azure-portal-dashboards.md)
