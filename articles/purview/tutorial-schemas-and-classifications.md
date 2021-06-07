---
title: 'Tutorial: Explorar conjuntos de recursos, detalhes, esquemas e classificações no Azure Purview (versão prévia)'
description: Este tutorial descreve como usar conjuntos de recursos, detalhes de ativos, esquemas e classificações.
author: animukherjee
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: c74324ebeeefeed361c0557c45a280a411effa22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97693330"
---
# <a name="tutorial-explore-resource-sets-details-schemas-and-classifications-in-azure-purview-preview"></a>Tutorial: Explorar conjuntos de recursos, detalhes, esquemas e classificações no Azure Purview (versão prévia)

> [!IMPORTANT]
> Atualmente, o Azure Purview está em VERSÃO PRÉVIA. Os [Termos de Uso Complementares para Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluem termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, versão prévia ou que, de outra forma, ainda não foram lançados em disponibilidade geral.

Neste tutorial, você explorará os principais componentes do catálogo: conjuntos de recursos, detalhes de ativos, esquemas e classificações.

Este tutorial é a *parte 4 de uma série de cinco tutoriais* em que você aprenderá os conceitos básicos de como gerenciar a governança de dados em um acervo de dados usando o Azure Purview. Este tutorial se baseia no trabalho que você concluiu na [Parte 3: Procurar ativos no Azure Purview (versão prévia) e ver a linhagem deles](tutorial-browse-and-view-lineage.md).

Neste tutorial, você aprende a:

> [!div class="checklist"]
>
> * Exibir conjuntos de recursos.
> * Exibir detalhes do ativo.
> * Editar o esquema e adicionar classificações.

## <a name="prerequisites"></a>Pré-requisitos

* [Tutorial completo:  Procurar ativos no Azure Purview (versão prévia) e ver a linhagem deles](tutorial-browse-and-view-lineage.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="view-resource-sets"></a>Exibir conjuntos de recursos

Um conjunto de recursos é um objeto no catálogo que representa muitos objetos físicos no armazenamento. Os objetos geralmente compartilham um esquema comum e, na maioria dos casos, uma convenção de nomenclatura ou estrutura de pastas. Por exemplo, o formato de data padrão é *aaaa/mm/dd*. Para obter mais informações sobre conjuntos de recursos, confira [Noções básicas de conjuntos de recursos](concept-resource-sets.md).

1. Navegue até o recurso do Azure Purview no portal do Azure e selecione **Abrir o Purview Studio**. Você é direcionado automaticamente para a home page do Purview Studio.

2. Insira **contoso_staging_positivecashflow_ n** na caixa **Pesquisar ativos** e selecione **Contoso_Staging_PositiveCashFlow_{N}.csv** nos resultados da pesquisa.

## <a name="view-asset-details"></a>Exibir detalhes do ativo

1. A guia **Visão geral** exibe a **Descrição**, **Termos do glossário** e **Propriedades**, como **qualifiedName**.

   :::image type="content" source="./media/tutorial-schemas-and-classifications/overview-tab.png" alt-text="Captura de tela mostrando a guia Visão geral de uma página de ativos do conjunto de recursos.":::

1. Em **Propriedades**, observe os dois campos abaixo:

   * **partitionCount**: indica o número de arquivos físicos associados a esse conjunto de recursos.
   * **schemaCount**: indica o número de variações do esquema que foram encontradas nesse conjunto de recursos.

   O Azure Purview popula essas propriedades em até 24 horas após a conclusão da verificação na [parte 1 desta série de tutoriais](tutorial-scan-data.md).

1. Selecione a guia **Contatos** para examinar os valores **Especialistas** e **Proprietários**.

## <a name="edit-the-schema-and-add-classifications"></a>Editar o esquema e adicionar classificações

1. Selecione a guia **Esquema**. Observe os nomes de coluna e as classificações associadas a eles. Observe que o exame preencheu as propriedades automaticamente.

   :::image type="content" source="./media/tutorial-schemas-and-classifications/schema-tab.png" alt-text="Captura de tela mostrando a guia Esquema.":::

1. Para editar o ativo, selecione o botão **Editar** na parte superior esquerda. Em seguida, selecione a guia **Esquema**.

1. Adicione uma **Descrição** para uma coluna ou renomeie a coluna para um nome mais amigável.

1. Adicione uma classificação no nível do ativo selecionando o menu suspenso **Classificação de nível de coluna** para um nome de coluna que não tenha uma classificação de conjunto.

   :::image type="content" source="./media/tutorial-schemas-and-classifications/edit-schema.png" alt-text="Página de edição de esquema":::

1. Quando terminar de fazer alterações, selecione **Salvar**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
>
> * Exibir conjuntos de recursos.
> * Exibir detalhes do ativo.
> * Editar o esquema e adicionar classificações.

Prossiga para o próximo tutorial para saber mais sobre o glossário e como definir e importar novos termos para ativos.

> [!div class="nextstepaction"]
> [Criar e importar termos do glossário](tutorial-import-create-glossary-terms.md)