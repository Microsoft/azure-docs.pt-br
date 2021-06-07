---
title: Como criar um projeto-Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Este artigo explica como criar e gerenciar um projeto no Tradutor personalizado de serviços cognitivas do Azure.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 9e3aa52323f44e6c1407fe2a542e40ee06370043
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98895791"
---
# <a name="create-a-project"></a>Criar um projeto

Um projeto é um contêiner para modelos, documentos e testes. Cada projeto inclui automaticamente todos os documentos que são carregados nesse workspace que tenham o par de idiomas correto.

Criar um projeto é o primeiro passo para construir um modelo.

## <a name="create-a-project"></a>Crie um projeto:

1.  No portal [conversor personalizado](https://portal.customtranslator.azure.ai), clique em Criar projeto.

    ![Criar projeto](media/how-to/how-to-create-project.png)

2.  Digite os seguintes detalhes sobre o seu projeto na caixa de diálogo:

    a.  Nome do projeto (obrigatório): dê ao seu projeto um nome exclusivo e significativo. Não é necessário mencionar os idiomas dentro do título.

    b.  Descrição: Um breve resumo sobre o projeto. Essa descrição não tem influência sobre o comportamento do Custom Translator ou de seu sistema personalizado resultante, mas pode ajudá-lo a diferenciar entre diferentes projetos.

    c.  Par de idiomas (obrigatório): selecione o idioma para o qual você está traduzindo.

    d.  Categoria (obrigatório): selecione a categoria mais apropriada para o seu projeto. A categoria descreve a terminologia e o estilo dos documentos que você pretende traduzir.

    e.  Descrição da categoria: use este campo para descrever melhor o campo ou setor específico em que você está trabalhando. Por exemplo, se a sua categoria for medicina, você poderá adicionar um documento específico, como uma cirurgia ou pediatria. A descrição não tem influência sobre o comportamento do Custom Translator ou do sistema personalizado resultante.

    f.  Rótulo do projeto: o [rótulo do projeto](workspace-and-project.md#project-labels) faz distinção entre projetos com o mesmo par de idiomas e categoria. Como prática recomendada, use um rótulo *somente* se você estiver planejando criar vários projetos para o mesmo par de idiomas e mesma categoria e quiser acessar esses projetos com um CategoryID diferente. Não use esse campo se você estiver criando sistemas apenas para uma categoria. Um rótulo de projeto não é necessário e não é útil para distinguir entre pares de idiomas. Você pode usar o mesmo rótulo para vários projetos.

    ![Criar caixa de diálogo do projeto](media/how-to/how-to-create-project-dialog.png)

3.  Clique em Criar. 

## <a name="view-project-details"></a>Exibir detalhes do projeto

A página de entrada do Custom Translator mostra os primeiros 10 projetos em seu workspace. Ele exibe o nome do projeto, o par de idiomas, a categoria, o status e a pontuação da BLEU.

Depois de selecionar um projeto, você verá o seguinte na página do projeto:

- CategoryID: Um CategoryID é criado pela concatenação do WorkspaceID, rótulo do projeto e código da categoria. Você usa o CategoryID com o Text Translator API para obter traduções personalizadas. Para copiar, clique no ícone de cópia.

- Botão Train: Use este botão para iniciar um [treinamento de um modelo](how-to-train-model.md).

- Botão Adicionar documentos: use este botão para [fazer upload de documentos](how-to-upload-document.md).

- Botão Filtrar documentos: use este botão para filtrar e pesquisar documentos específicos.

    ![Exibir detalhes do projeto](media/how-to/how-to-view-project.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais [como pesquisar, editar, excluir projeto](how-to-search-edit-delete-projects.md).
- Saiba mais [como carregar documento](how-to-upload-document.md) para criar modelos de tradução.
