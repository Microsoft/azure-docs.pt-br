---
title: Publicar blocos de anotações na Galeria de notebooks Azure Cosmos DB
description: Saiba como baixar blocos de anotações da Galeria pública, editá-los e publicar seus próprios blocos de anotações na galeria.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/02/2021
ms.author: dech
ms.openlocfilehash: 58ae61bc9e1736b13bb1802e2f39d5ada045cb6a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102039222"
---
# <a name="publish-notebooks-to-the-azure-cosmos-db-notebook-gallery"></a>Publicar blocos de anotações na Galeria de notebooks Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB blocos de anotações internos do Jupyter são diretamente integrados às suas contas de Azure Cosmos DB no portal do Azure. Usando esses blocos de anotações, você pode analisar e visualizar seus dados do portal do Azure. Os blocos de anotações internos para Azure Cosmos DB estão disponíveis atualmente em [muitas regiões](https://azure.microsoft.com/global-infrastructure/services/?products=cosmos-db&regions=all). Para usar blocos de anotações, [crie uma nova conta do cosmos](create-cosmosdb-resources-portal.md) ou [habilite blocos de anotações em uma conta existente](enable-notebooks.md) em uma dessas regiões.

O ambiente do notebook na portal do Azure tem alguns exemplos publicados pela equipe de Azure Cosmos DB. Ele também tem uma galeria pública onde você pode publicar e compartilhar seus próprios blocos de anotações. Depois que um bloco de anotações é publicado na Galeria, ele está disponível para todos os Azure Cosmos DB usuários para exibir e usar. Neste artigo, você aprenderá a usar os blocos de anotações da Galeria pública e publicará seu bloco de anotações na galeria.

## <a name="download-a-notebook-from-the-gallery"></a>Baixar um bloco de anotações da Galeria

Use as etapas a seguir para exibir e baixar blocos de anotações da galeria para seu espaço de trabalho de blocos de anotações:

1. Entre no [portal do Azure](https://portal.azure.com/) e navegue até a conta do Azure Cosmos DB que está habilitada com o ambiente do notebook.

1. Navegue até a   >    >    >  guia **Galeria pública** da Galeria de blocos de anotações do data Explorer.

1. Aceite o [código de conduta](https://azure.microsoft.com/support/legal/cosmos-db-public-gallery-code-of-conduct/)  antes de exibir ou publicar blocos de anotações na galeria. O código de conduta é registrado por usuário, de acordo com o nível de assinatura. Quando você alternar para uma assinatura diferente, deverá aceitá-la novamente antes de acessar a galeria. Para aceitar o código de conduta, marque a caixa de seleção e **continue**:

   :::image type="content" source="./media/publish-notebook-gallery/view-public-gallery.png" alt-text="Navegue até a Galeria pública de blocos de anotações e aceite o código de conduta.":::

1. Em seguida, você pode adicionar um bloco de anotações específico à sua guia favoritos ou baixá-lo. Quando você baixa um bloco de anotações, ele é copiado para o espaço de trabalho dos blocos de anotações, onde você pode executá-lo ou editá-lo.

   :::image type="content" source="./media/publish-notebook-gallery/download-notebook-gallery.png" alt-text="Baixe um bloco de anotações da galeria para seu espaço de trabalho.":::

## <a name="publish-a-notebook-to-the-gallery"></a>Publicar um bloco de anotações na Galeria

Ao criar seus próprios blocos de anotações ou editar os blocos de anotações existentes para se ajustar a um cenário diferente, talvez você queira publicá-los na galeria. Depois que um bloco de anotações é publicado na Galeria, outros usuários podem acessá-lo. Você pode [explorar a Galeria de exemplos de bloco de anotações](https://cosmos.azure.com/gallery.html) para exibir os blocos de anotações disponíveis no momento.

Use as seguintes etapas para publicar um bloco de anotações:

1. Entre no [portal do Azure](https://portal.azure.com/) e navegue até a conta do Azure Cosmos DB que está habilitada com o ambiente do notebook.

1. Navegue até a guia **Data Explorer**  >  **blocos** de anotações  >  **meus blocos** de anotações.

1. Vá para o bloco de anotações que você deseja publicar. Selecione o botão **salvar** na barra de comandos e, em seguida, selecione **publicar na Galeria**:

   :::image type="content" source="./media/publish-notebook-gallery/choose-notebook-publish-option-2.png" alt-text="Escolha um bloco de anotações para publicar na galeria.":::

   Você também pode encontrar a opção **publicar na Galeria** selecionando a **...** botão ao lado do nome do bloco de anotações:

   :::image type="content" source="./media/publish-notebook-gallery/choose-notebook-publish.png" alt-text="Outra abordagem para escolher um bloco de anotações para publicar na galeria.":::

1. Preencha o formulário **publicar na Galeria** com os seguintes detalhes:

   * **Nome:** Um nome amigável para identificar seu bloco de anotações.
   * **Descrição:**  Uma breve descrição do que o seu notebook faz.
   * **Marcas:** As marcas são opcionais e são usadas para filtrar os resultados quando pesquisados por uma palavra-chave.
   * **Imagem da capa:** Uma imagem que é usada na página de rosto quando o notebook é publicado. Você pode escolher uma das seguintes opções:
   * **Imagem personalizada** – você pode carregar uma imagem do seu computador. Escolha um arquivo de imagem com taxa de proporção 256x144.
   * **URL** -forneça uma URL acessível publicamente em que a imagem está localizada.
   * **Tirar captura de tela** – uma captura de tela de seu notebook aberto é automaticamente executada e carregada para a versão prévia.
   * **Usar primeira exibição de saída** -saída da primeira célula que tem uma saída de exibição. Células que exibem apenas redução/texto não são contadas como uma saída de exibição.

   :::image type="content" source="./media/publish-notebook-gallery/publish-notebook.png" alt-text="Preencha o formulário publicar na galeria.":::

   > [!NOTE]
   > Se você estiver usando a opção **publicar na Galeria** do **...** ao lado do nome do bloco de anotações, você não verá todas as opções de **imagem de capa** . Isso porque o bloco de anotações pode não estar aberto e Azure Cosmos DB não terá acesso para tirar uma captura de tela ou acessar a primeira saída de exibição.

1. Verifique se a visualização parece correta e selecione **publicar**. Quando publicado, esse bloco de anotações será exibido na Galeria pública do Azure Cosmos DB blocos de anotações. Verifique se você removeu dados confidenciais ou saída antes da publicação. O conteúdo do bloco de anotações é verificado quanto a qualquer violação das políticas da Microsoft antes da publicação. Esse processo geralmente leva alguns segundos para ser concluído. Se quaisquer violações forem encontradas, uma mensagem será exibida na guia notificação. Seu bloco de anotações não será publicado se encontrar qualquer violação, você removerá o texto violado e o publicará novamente.

   > [!NOTE]
   > Depois que os blocos de anotações são publicados na Galeria pública, todos os Azure Cosmos DB usuários podem exibi-los. O acesso não está limitado a por assinatura ou por nível de organização.

1. Depois que o notebook for publicado na Galeria, você poderá vê-lo na guia **meu trabalho publicado** . Você também pode remover ou excluir blocos de anotações publicados da Galeria pública.

1. Você também pode relatar um bloco de anotações que viole o código de conduta. Se uma violação for encontrada, Cosmos DB removerá automaticamente o bloco de anotações da galeria. Se um bloco de anotações for removido, os usuários poderão vê-lo na guia **meu trabalho publicado** com a anotação removida. Para reportar um bloco de anotações , vá para a  >  guia Galeria pública da Galeria de **blocos de anotações** do data Explorer  >    >   . Abra o bloco de anotações que você deseja relatar, focalize o botão **ajuda** no canto direito e selecione **relatar abuso**.

   :::image type="content" source="./media/publish-notebook-gallery/report-notebook-violation.png" alt-text="Relate um bloco de anotações que viole o código de conduta.":::

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre os benefícios do [Azure Cosmos DB notebooks Jupyter](cosmosdb-jupyter-notebooks.md)
* [Usar recursos e comandos de notebook Python](use-python-notebook-features-and-commands.md)
* [Usar recursos e comandos de notebook C#](use-csharp-notebook-features-and-commands.md)
* [Importar notebooks de um repositório GitHub](import-github-notebooks.md)
