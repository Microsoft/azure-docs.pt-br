---
title: Treinar um modelo - tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Treinar um modelo é um passo importante na construção de um modelo de tradução. O treinamento acontece com base nos documentos selecionados para esses treinamentos.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 5c17f842b93fb99a6a06b94f84ae26126794b776
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896419"
---
# <a name="train-a-model"></a>Treinar um modelo

Treinar um modelo é a primeira e mais importante etapa para criar um modelo de tradução, caso contrário, o modelo não pode ser criado. O treinamento acontece com base nos documentos selecionados para os treinamentos. Quando você selecionar documentos de tipo de documento de "treinamento", lembre-se do requisito mínimo de 10.000 de sentenças paralelas. À medida que você seleciona documentos, exibimos o número total de sentenças de treinamento para orientá-lo. Esse requisito não se aplica quando você seleciona apenas documentos de tipo de documento de dicionário para treinar um modelo.

Para treinar um modelo:

1. Selecione o projeto no qual você deseja construir um modelo.

2. A guia Dados para o projeto mostrará todos os documentos relevantes para o par de idiomas do projeto. Selecione manualmente os documentos que você deseja usar para treinar seu modelo. Você pode selecionar documentos de treinamento, ajuste e teste nessa tela. Além disso, basta selecionar o conjunto de treinamento e fazer com que o Custom Translator crie os conjuntos de ajustes e testes para você.

    - Nome do documento: nome do documento.

    - Emparelhamento: se este documento for um documento paralelo ou monolíngue. Atualmente, documentos monolíngues não são suportados para treinamento.

    - Tipo de documento: pode ser treinamento, ajuste, teste ou dicionário.

    - Par de idiomas: mostra o idioma de origem e de destino do projeto.

    - Sentenças de origem: mostra o número de frases extraídas do arquivo de origem.

    - Sentenças de destino: mostra o número de frases extraídas do arquivo de destino.

    ![Treinar um modelo](media/how-to/how-to-train-model.png)

3. Clique no botão "criar modelo".

4. Na caixa de diálogo, especifique o nome do seu modelo. Por padrão, "treinar imediatamente" está selecionado para iniciar o pipeline de treinamento ao clicar no botão "criar modelo". Você pode selecionar "salvar como rascunho" para criar os metadados do modelo e colocar o modelo em um estado de rascunho, mas o treinamento do modelo não será iniciado. Posteriormente, você precisa selecionar manualmente os modelos no estado de rascunho para treinar.

5. Clique no botão "criar modelo".

    ![Caixa de diálogo de modelo de treinamento](media/how-to/how-to-train-model-2.png)

6. O tradutor personalizado enviará o treinamento e mostrará o status do treinamento na guia modelos.

    ![Página de modelo de treinamento](media/how-to/how-to-train-model-3.png)

>[!Note]
>O tradutor personalizado dá suporte a 10 treinamentos simultâneos em um espaço de trabalho em qualquer ponto no tempo.

## <a name="modify-a-model-name"></a>Modificar um nome de modelo

Você pode modificar um nome de modelo na página de detalhes do modelo.

1. Na página projetos, clique no nome do projeto no qual o modelo existe.
2. Clique na guia modelo.
3. Clique no nome do modelo para exibir os detalhes do modelo.
4. Clique no ícone do lápis.

    ![Editar modelo](media/how-to/how-to-edit-model.png)

5. Na caixa de diálogo, altere o nome do modelo e dê um nome significativo ao seu modelo.

    ![Mais de caixa de diálogo Editar](media/how-to/how-to-edit-model-dialog.png)

6. Clique em Salvar.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais [como exibir detalhes do modelo](how-to-view-model-details.md).
