---
title: Como pesquisar, editar e excluir projetos - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: O tradutor personalizado fornece várias maneiras de gerenciar seus projetos de maneira eficiente. Você pode criar vários projetos, pesquisar com base em seus critérios, editar seus projetos. Exclusão de um projeto também é possível no tradutor de personalizado.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 308ca25d35011c67ded7300177149cd590462952
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896436"
---
# <a name="search-edit-and-delete-projects"></a>Pesquisar, editar e excluir projetos

O tradutor personalizado fornece várias maneiras de gerenciar seus projetos de maneira eficiente. Você pode criar muitos projetos, pesquisar com base em seus critérios e editar seus projetos. Exclusão de um projeto também é possível no tradutor de personalizado.  

## <a name="search-and-filter-projects"></a>Pesquisar e filtrar projetos

A ferramenta de filtragem permite pesquisar projetos por diferentes condições de filtro. Ele filtra como nome do projeto, status, idioma de origem e destino e categoria do projeto.

1. Clique no botão de filtro.

    ![Projeto de pesquisa](media/how-to/how-to-search-project.png)

2. Você pode filtrar por qualquer (ou todos) dos seguintes campos: nome do projeto, idioma de origem, idioma de destino, categoria e disponibilidade do projeto.

3. Clique em Aplicar.

    ![Opções de filtro de projeto de pesquisa](media/how-to/how-to-search-project-filters.png)

4. Limpe o filtro para ver todos os seus projetos tocando em "Limpar".

## <a name="edit-a-project"></a>Editar um projeto

O tradutor personalizado oferece a capacidade de editar o nome e a descrição de um projeto. Outros metadados do projeto como a categoria, o idioma de origem e o idioma de destino não estão disponíveis para edição. As etapas a seguir descrevem como editar um projeto.

1. Clique no ícone de lápis que aparece quando você passa o mouse sobre um projeto.

    ![Editar projeto](media/how-to/how-to-edit-project.png)

2. Na caixa de diálogo, você pode modificar o nome do projeto, a descrição do projeto, a descrição da categoria e o rótulo do projeto se nenhum modelo for implantado. Você não pode modificar o par de categorias ou idiomas depois que o projeto é criado.

    ![Editar caixa de diálogo do projeto](media/how-to/how-to-edit-project-dialog.png)

3. Clique no botão "salvar".

## <a name="delete-a-project"></a>Excluir um projeto

Você pode excluir um projeto quando você não precisa mais dela. Faça com que o projeto não tenha modelos em estado ativo, como implantado, treinamento enviado, processamento de dados, implantação, etc., caso contrário, a operação de exclusão falhará. Abaixo, as etapas descrevem como excluir um projeto.

1. Passe o mouse sobre qualquer registro de projeto e clique no ícone de Lixeira.

   ![Excluir projeto](media/how-to/how-to-delete-project.png)

2. Confirme a exclusão. A exclusão de um projeto excluirá todos os modelos criados nesse projeto. A exclusão do projeto não afetará seus documentos.

   ![Caixa de diálogo de confirmação Excluir](media/how-to/how-to-delete-project-confirm.png)

## <a name="next-steps"></a>Próximas etapas

- [Envie documentos](how-to-upload-document.md) para começar a criar seu modelo de tradução personalizado.
