---
title: 'Tutorial: Criar e importar termos de glossário no Azure Purview (versão prévia)'
description: Este tutorial descreve como criar termos de glossário, adicionar termos de glossário a um ativo e importar termos de glossário.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 9443c6fbaca16cf075745972a1655a2b4b2ea43c
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077526"
---
# <a name="tutorial-create-and-import-glossary-terms-in-azure-purview-preview"></a>Tutorial: Criar e importar termos de glossário no Azure Purview (versão prévia)

> [!IMPORTANT]
> Atualmente, o Azure Purview está em VERSÃO PRÉVIA. Os [Termos de Uso Complementares para Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluem termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, versão prévia ou que, de outra forma, ainda não foram lançados em disponibilidade geral.

Um glossário é uma ferramenta importante para manter e organizar seu catálogo. Você cria seu glossário definindo novos termos ou importando uma lista de termos e aplicando esses termos aos seus ativos.

Este tutorial é a *parte 5 de uma série de cinco tutoriais* em que você aprenderá os conceitos básicos de como gerenciar a governança de dados em um acervo de dados usando o Azure Purview. Este tutorial se baseia no trabalho que você concluiu na [Parte 4: Explorar conjuntos de recursos, detalhes, esquemas e classificações no Azure Purview (versão prévia)](tutorial-schemas-and-classifications.md).

Neste tutorial, você aprende a:

> [!div class="checklist"]
>
> * Criar termos de glossário.
> * Adicionar termos de glossário a um ativo.
> * Importar termos de glossário.

## <a name="prerequisites"></a>Pré-requisitos

* [Tutorial completo: Explorar conjuntos de recursos, detalhes, esquemas e classificações no Azure Purview (versão prévia)](tutorial-schemas-and-classifications.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="create-glossary-terms"></a>Criar termos do glossário

Você pode criar termos empresariais ou técnicos no glossário. Você também pode anotar seus ativos aplicando termos a eles.

Aqui está um resumo de alguns dos campos mais comuns na página **Termo de glossário**:

* **Status**: atribua um status ao termo (**Rascunho**, **Aprovado**, **Expirado** ou **Alerta**).

* **Definição**: insira uma definição do termo.

* **Acrônimo**: insira uma versão abreviada do termo usando as letras iniciais de cada palavra.

* **Sinônimos**: selecione outros termos com definições iguais ou semelhantes.

* **Termos Relacionados**: selecione outros termos no glossário que estão relacionados a este, mas que têm definições diferentes. Os exemplos são termos técnicos relacionados a um termo empresarial, um nome de código ou outros termos que devem ser associados ao termo.

Crie um termo de glossário seguindo estas etapas:

1. Navegue até o recurso do Azure Purview no portal do Azure e selecione **Abrir o Purview Studio**. Você é direcionado automaticamente para a home page do Purview Studio.

1. Selecione **Glossário** no painel esquerdo para abrir a página **Termos de glossário**.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/glossary-terms-page.png" alt-text="Captura de tela mostrando a página Termos de glossário.":::

1. Selecione **Novo termo** > **Padrão do sistema** > **Continuar**.

1. Na guia **Visão geral** da página **Novo termo**, insira o **Nome** do novo termo: *Financeiro*.

1. Insira a **Definição**: *Este termo representa informações financeiras para a Contoso Inc.*

1. Na lista suspensa **Status**, selecione **Aprovado**.

1. Ao terminar, selecione **Criar**.

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/enter-new-glossary-term.png" alt-text="Captura de tela mostrando como criar um termo de glossário.":::

## <a name="add-glossary-terms-to-an-asset"></a>Adicionar termos de glossário a um ativo

1. Use as etapas que você aprendeu na [parte 1 desta série de tutoriais](tutorial-scan-data.md) para encontrar um ativo. Por exemplo, **Contoso_CashFlow_{N}.csv**.

1. Na página de detalhes do ativo, selecione **Editar**.

1. Na lista suspensa **Termos de glossário** na guia **Visão geral**, selecione **Financeiro** e escolha **Salvar**.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-glossary-term-to-asset.png" alt-text="Captura de tela mostrando como adicionar um termo de glossário a um ativo.":::

1. Acesse a seção **Termos de glossário** na guia **Visão geral** e observe que o termo *Financeiro* foi aplicado ao ativo.

## <a name="import-glossary-terms"></a>Importar termos de glossário

Para importar termos ou atualizar os termos existentes em massa, carregue um modelo preenchido previamente em seu glossário.

Neste procedimento, você importará os termos de glossário por meio de um arquivo .csv:

1. Anote o local em que você armazenou o arquivo chamado *StarterKitTerms.csv*, que faz parte do kit de início que você baixou na [parte 1 desta série de tutoriais](tutorial-scan-data.md).

   Esse arquivo contém uma lista de termos preenchidos previamente que são relevantes para seu acervo de dados.

1. Para começar a importação, selecione **Glossário** e escolha **Importar termos**.

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/import-glossary-terms-select.png" alt-text="Captura de tela mostrando como importar termos de glossário.":::

1. Na página **Importar termos**, selecione **Padrão do sistema** e escolha **Continuar**.

1. Selecione **Procurar**, acesse a localização em que você baixou *StarterKitTerms.csv* e selecione **Abrir**.

1. Selecione **OK** para começar a importar os termos.

   Depois que a importação for concluída, os novos termos de glossário serão exibidos na página **Termos de glossário**.

1. Veja cada um dos termos recém-importados para ver como eles são definidos.

## <a name="create-custom-term-templates"></a>Criar modelos de termo personalizados

Nesta seção, você aprenderá a criar um modelo de termo personalizado com atributos personalizados e a importar dados usando um arquivo csv de modelo.

Há vários modelos de termos do sistema existentes, que podem ser exibidos selecionando **Glossário** > **Gerenciar modelos de termo** > **Sistema**.

:::image type="content" source="./media/tutorial-import-create-glossary-terms/system-term-templates.png" alt-text="modelos de termo do sistema.":::

Para criar um modelo de termo personalizado, execute as seguintes etapas:

1. Selecione **Glossário** no menu do lado esquerdo.
1. Selecione **Gerenciar modelos de termo** > **Personalizado** > **Novo modelo de termo**

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-new-custom-term-template.png" alt-text="criar um modelo de termo personalizado.":::

Na tela **Novo modelo de termo**, execute as seguintes etapas:

1. Insira o **Nome do modelo**: `Sensitivity level`.
1. Insira sua descrição desejada, como `Indicates the level of sensitivity for this data.`
1. Selecione **+ Novo atributo** para abrir uma caixa de diálogo para adicionar atributos.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/new-term-template-screen-start.png" alt-text="início da tela do novo modelo de termo.":::

1. Na tela **Novo atributo**, insira os seguintes parâmetros:

   |Configuração|Valor sugerido|
   |---------|-----------|
   |Nome do atributo |is sensitive information|
   |Tipo de campo | Opção única|
   |Marcar como obrigatório | Marque essa caixa.|
   |+ Adicionar uma opção | Adicione duas opções. "Sim" e "Não".|

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-new-attribute.png" alt-text="adicionar um novo atributo.":::

1. Repita as etapas anteriores para adicionar outro atributo com o nome `can be shared externally`. Depois que ambos os atributos forem adicionados, selecione **Criar**.

## <a name="import-terms-from-a-template"></a>Importar termos de um modelo

Em seguida, importe um novo termo usando o modelo **Nível de confidencialidade** criado. 

1. Na tela **Termos de glossário**, selecione **Importar termos**.

1. Selecione **Nível de confidencialidade** na tela **Importar termos**. Selecione **Continuar**.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/select-sensitivity-level.png" alt-text="Selecione o nível de confidencialidade.":::

1. O modelo de termo para o **Nível de confidencialidade** contém atributos do sistema padrão, bem como os novos atributos que você adicionou: `can be shared externally` e `is sensitive information`. Selecione **Baixar um arquivo .CSV de exemplo**.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/download-sample-csv-file.png" alt-text="Baixar arquivo csv de exemplo.":::

1. Um arquivo de modelo é baixado para você editar e carregar o novo termo de glossário com atributos de cliente. Abra o arquivo CSV que você baixou. Adicione novos termos e os valores apropriados deles como novas linhas no arquivo CSV.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-term-in-csv.png" alt-text="Crie o termo no arquivo csv.":::

   Os termos listados na coluna **Termos Relacionados** ou **Sinônimos** que ainda não existe serão criados quando o arquivo for carregado. Eles serão criados usando o modelo **Padrão do sistema**.

1. Para carregar o arquivo, retorne à tela **Importar termos** e selecione **Procurar** ao lado da caixa **Selecione o arquivo .CSV concluído para upload**. Selecione o arquivo na caixa de diálogo aberta e escolha **OK**.

1. Os novos termos agora estão listados na tela **Termos de glossário**. Você pode Ver detalhes sobre os novos termos clicando no nome do termo na lista.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
>
> * Criar termos de glossário.
> * Adicionar termos de glossário a um ativo.
> * Importar termos de glossário.

Avance para o próximo artigo para saber mais sobre os diferentes insights de catálogo.

> [!div class="nextstepaction"]
> [Noções básicas de Insights no Azure Purview](concept-insights.md)
