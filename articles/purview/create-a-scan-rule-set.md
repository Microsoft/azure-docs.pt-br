---
title: Criar um conjunto de regras de verificação
description: Crie um conjunto de regras de verificação no Azure alcance para examinar rapidamente as fontes de dados em sua organização.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: 9662652a6a40285ad382857975ec0dd04b8ba8be
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551559"
---
# <a name="create-a-scan-rule-set"></a>Criar um conjunto de regras de verificação

Em um catálogo alcance do Azure, você pode criar conjuntos de regras de verificação para permitir que você examine rapidamente as fontes de dados em sua organização.

Um conjunto de regras de verificação é um contêiner para agrupar um conjunto de regras de verificação para que você possa associá-las facilmente a uma verificação. Por exemplo, você pode criar um conjunto de regras de verificação padrão para cada um dos tipos de fonte de dados e, em seguida, usar esses conjuntos de regras de verificação por padrão para todas as verificações em sua empresa. Você também pode desejar que os usuários com as permissões corretas criem outros conjuntos de regras de verificação com configurações diferentes com base na necessidade comercial.

## <a name="steps-to-create-a-scan-rule-set"></a>Etapas para criar um conjunto de regras de verificação

Para criar um conjunto de regras de verificação:

1. No catálogo alcance do Azure, selecione **centro de gerenciamento**.

1. Selecione **verificar conjuntos de regras** no painel esquerdo e selecione **novo**.

1. Na página **novo conjunto de regras de verificação** , selecione as fontes de dados às quais o scanner de catálogo dá suporte na lista suspensa **tipo de origem** . Você pode criar um conjunto de regras de verificação para cada tipo de fonte de dados que pretende verificar.

1. Dê ao seu conjunto de regras de verificação um **nome**. O comprimento máximo é de 63 caracteres, sem espaços permitidos. Opcionalmente, insira uma **Descrição**. O tamanho máximo é de 256 caracteres.

   :::image type="content" source="./media/create-a-scan-rule-set/purview-home-page.png" alt-text="Captura de tela mostrando a página do conjunto de regras de verificação." border="true":::

1. Selecione **Continuar**.

   A página **Selecionar tipos de arquivo** é exibida. Observe que as opções de tipo de arquivo nesta página variam com base no tipo de fonte de dados que você escolheu na página anterior. Todos os tipos de arquivo estão habilitados por padrão.

      :::image type="content" source="./media/create-a-scan-rule-set/select-file-types-page.png" alt-text="Captura de tela mostrando a página Selecionar tipos de arquivo.":::

   A seleção de **tipos de arquivo de documento** nesta página permite que você inclua ou exclua os seguintes tipos de arquivo do Office:. doc,. docm,. docx,. dot,. odp,. ods,. odt,. pdf,. pot,. PPS,. ppsx,. ppt,. pptm,. pptx,. xlc,. xls,. xlsb,. xlsm,. xlsx e. xlt.

1. Habilite ou desabilite um bloco de tipo de arquivo marcando ou desmarcando a caixa de seleção. Se você escolher uma fonte de dados de tipo de Data Lake (por exemplo, Azure Data Lake Storage Gen2 ou BLOB do Azure), habilite os tipos de arquivo para os quais você deseja que o esquema seja extraído e classificado.

1. Para determinados tipos de fonte de dados, você também pode [criar um tipo de arquivo personalizado](#create-a-custom-file-type).

1. Selecione **Continuar**.

   A página **selecionar regras de classificação** é exibida. Esta página exibe as regras de **sistema** e as **regras personalizadas** selecionadas e o número total de regras de classificação selecionadas. Por padrão, todas as caixas de seleção de **regras do sistema** estão marcadas

1. Para as regras que você deseja incluir ou excluir, você pode marcar ou desmarcar as caixas de seleção regra de classificação de **regras do sistema** globalmente por categoria.

   :::image type="content" source="./media/create-a-scan-rule-set/select-classification-rules.png" alt-text="Captura de tela mostrando a página Selecionar regras de classificação.":::

1. Você pode expandir o nó categoria e marcar ou desmarcar as caixas de seleção individuais. Por exemplo, se a regra para o **número Argentina. DNI** tiver altos falsos positivos, você poderá desmarcar essa caixa de seleção específica.

   :::image type="content" source="./media/create-a-scan-rule-set/select-system-rules.png" alt-text="Captura de tela mostrando como selecionar regras do sistema.":::

1. Selecione **criar** para concluir a criação do conjunto de regras de verificação.

### <a name="create-a-custom-file-type"></a>Criar um tipo de arquivo personalizado

O Azure alcance dá suporte à adição de uma extensão personalizada e à definição de um delimitador de coluna personalizado em um conjunto de regras de verificação.

Para criar um tipo de arquivo personalizado:

1. Siga as etapas 1 a 5 em [etapas para criar um conjunto de regras de verificação](#steps-to-create-a-scan-rule-set) ou editar um conjunto de regras de verificação existente.

1. Na página **Selecionar tipos de arquivo** , selecione **novo tipo de arquivo** para criar um novo tipo de arquivo personalizado.

   :::image type="content" source="./media/create-a-scan-rule-set/select-new-file-type.png" alt-text="Captura de tela mostrando como selecionar novo tipo de arquivo na página Selecionar tipos de arquivo.":::

1. Insira uma **extensão de arquivo** e uma **Descrição** opcional.

   :::image type="content" source="./media/create-a-scan-rule-set/new-custom-file-type-page.png" alt-text="Captura de tela mostrando a nova página de tipo de arquivo personalizado." border="true":::

1. Faça uma das seguintes seleções para o **conteúdo do arquivo no** para especificar o tipo de conteúdo do arquivo em seu arquivo:

   - Selecione **delimitador personalizado** e insira seu próprio **delimitador personalizado** (somente caractere único).

   - Selecione **tipo de arquivo do sistema** e escolha um tipo de arquivo do sistema (por exemplo, XML) na lista suspensa **tipo de arquivo do sistema** .

1. Selecione **criar** para salvar o arquivo personalizado.

   O sistema retorna para a página **Selecionar tipos de arquivo** e insere o novo tipo de arquivo personalizado como um novo bloco.

   :::image type="content" source="./media/create-a-scan-rule-set/new-custom-file-type-tile.png" alt-text="Captura de tela mostrando o novo bloco de tipo de arquivo personalizado na página Selecionar tipos de arquivo.":::

1. Selecione **Editar** no bloco novo tipo de arquivo se desejar alterá-lo ou excluí-lo.

1. Selecione **continuar** para concluir a configuração do conjunto de regras de verificação.

## <a name="system-scan-rule-sets"></a>Conjuntos de regras de verificação do sistema

Os conjuntos de regras de verificação do sistema são conjuntos de regras de verificação definidos pela Microsoft que são criados automaticamente para cada catálogo alcance do Azure. Cada conjunto de regras de verificação do sistema é associado a um tipo de fonte de dados específico. Ao criar uma verificação, você pode associá-la a um conjunto de regras de verificação do sistema. Sempre que a Microsoft faz uma atualização para esses conjuntos de regras do sistema, você pode atualizá-los em seu catálogo e aplicar a atualização a todas as verificações associadas.

1. Para exibir a lista de conjuntos de regras de verificação do sistema, selecione **verificar conjuntos de regras** no **centro de gerenciamento** e escolha a guia **sistema** .

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-sets.jpg" alt-text="Captura de tela mostrando a lista de conjuntos de regras de verificação do sistema." lightbox="./media/create-a-scan-rule-set/system-scan-rule-sets.jpg":::

1. Cada conjunto de regras de verificação do sistema tem um **nome**, um **tipo de origem** e uma **versão**. Se você selecionar o número de versão de um conjunto de regras de verificação na coluna **versão** , verá as regras associadas à versão atual e as versões anteriores (se houver).

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-set-page.jpg" alt-text="Captura de tela mostrando uma página de conjunto de regras de verificação do sistema." lightbox="./media/create-a-scan-rule-set/system-scan-rule-set-page.jpg":::

1. Se uma atualização estiver disponível para um conjunto de regras de verificação do sistema, você poderá selecionar **Atualizar** na coluna **versão** . Na página regra de verificação do sistema, escolha de uma versão na lista suspensa **selecionar uma nova versão a ser atualizada** . A página fornece uma lista de regras de classificação do sistema associadas à nova versão e à versão atual.

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-set-version.jpg" alt-text="Captura de tela mostrando como alterar a versão de um conjunto de regras de verificação do sistema." lightbox="./media/create-a-scan-rule-set/system-scan-rule-set-version.jpg":::

### <a name="associate-a-scan-with-a-system-scan-rule-set"></a>Associar uma verificação a um conjunto de regras de verificação do sistema

Ao [criar uma verificação](tutorial-scan-data.md#scan-data-into-the-catalog), você pode optar por associá-la a um conjunto de regras de verificação do sistema da seguinte maneira:

1. Na página **selecionar um conjunto de regras de verificação** , selecione o conjunto de regras de verificação do sistema.

   :::image type="content" source="./media/create-a-scan-rule-set/set-system-scan-rule-set.jpg" alt-text="Captura de tela mostrando como selecionar um conjunto de regras de verificação do sistema para uma verificação." lightbox="./media/create-a-scan-rule-set/set-system-scan-rule-set.jpg":::

1. Selecione **continuar** e, em seguida, selecione **salvar e executar**.
