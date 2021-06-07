---
title: 'Tutorial: Navegar até a home page do Azure Purview e pesquisar um ativo'
description: Este tutorial descreve como usar recursos na home page do Azure Purview e pesquisar no catálogo.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 02/22/2020
ms.openlocfilehash: 5285d5b2c7cbe6845fb6043fa1096f94254602e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101677844"
---
# <a name="tutorial-navigate-the-azure-purview-preview-home-page-and-search-for-an-asset"></a>Tutorial: Navegar até a home page do Azure Purview (versão prévia) e pesquisar um ativo

> [!IMPORTANT]
> Atualmente, o Azure Purview está em VERSÃO PRÉVIA. Os [Termos de Uso Complementares para Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluem termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, versão prévia ou que, de outra forma, ainda não foram lançados em disponibilidade geral.

Neste tutorial, você se familiarizará com o Azure Purview navegando nos recursos da home page e procurando um ativo no catálogo.

Este artigo é a *parte 2 de uma série de cinco tutoriais* em que você aprenderá os conceitos básicos de como gerenciar a governança de dados em um acervo de dados usando o Azure Purview. Este tutorial se baseia no trabalho que você concluiu na [Parte 1: Examinar dados com o Azure Purview](tutorial-scan-data.md)

Neste tutorial, você irá:

> [!div class="checklist"]
>
> * Navegue até a home page do Azure Purview.
> * Pesquise um ativo.
> * Adicione um proprietário de ativos.

## <a name="prerequisites"></a>Pré-requisitos

* [Tutorial completo: Examinar dados com o Azure Purview](tutorial-scan-data.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="navigate-the-azure-purview-home-page"></a>Navegar até a home page do Azure Purview

As etapas a seguir percorrerão a home page do Azure Purview.

1. Navegue até o recurso do Azure Purview no portal do Azure e selecione **Abrir o Purview Studio**. Você é direcionado automaticamente para a home page do Purview Studio.

   A parte superior da home page exibe o nome do seu catálogo e um conjunto de análises de catálogo. Estão incluídos o número de fontes, os ativos de dados e os termos do glossário. No resumo, você pode ver que há mais de 200 ativos no total e 113 termos de glossário. Esse número é atualizado à medida que sua organização examina e adiciona mais termos ao Azure Purview.

   :::image type="content" source="./media/tutorial-asset-search/purview-home-page.png" alt-text="Captura de tela mostrando a home page do Azure Purview.":::

1. Selecione **Procurar ativos** para ver todos os seus ativos.

## <a name="search-for-an-asset"></a>Procurar um ativo

Antes de começar, aqui está uma rápida atualização sobre um pouco de terminologia fundamental:

* **Ativo**: um objeto no catálogo que é conciso e contém a definição de qualquer entidade no acervo de dados. Exemplos de entidades incluem uma tabela SQL, um relatório do Power BI e sua atividade de data factory.
  
* **Esquema**: também conhecido como coluna ou atributo, um esquema representa a estrutura de um ativo ou um conjunto de recursos.

* **Conjunto de recursos**: um objeto no catálogo que representa muitos objetos físicos no armazenamento. Esses objetos geralmente compartilham um esquema comum e, na maioria dos casos, uma convenção de nomenclatura ou estrutura de pastas. Por exemplo, o formato de data padrão é *aaaa/mm/dd*. Para obter mais informações, confira [Noções básicas de conjuntos de recursos](concept-resource-sets.md).

* **Tipo de ativo**: um agrupamento de ativos e conjuntos de recursos sob um nome lógico, que geralmente é mapeado para o nome da plataforma de dados.

Siga estas etapas para procurar um ativo e marcar-se como proprietário:

1. Na caixa **Pesquisar catálogo** em sua home page, insira o nome do grupo de recursos que contém seu acervo de dados (o grupo de recursos criado no tutorial anterior). Uma lista de sugestões será exibida.

1. Selecione **Exibir resultados da pesquisa**. Como todos os seus ativos começam com o nome do seu grupo de recursos, todos eles aparecem nos resultados da pesquisa. Fora deste tutorial, você procuraria nomes de ativos específicos, não grupos de recursos.

    :::image type="content" source="./media/tutorial-asset-search/search-suggestions.png" alt-text="Captura de tela mostrando a lista de sugestões de pesquisa de catálogo.":::

1. Você pode usar os filtros no painel de navegação esquerdo para filtrar por tipo de ativo, classificação, contato, rótulo e termo de glossário.

1. Para procurar um conjunto de recursos, comece digitando o nome do conjunto. Uma lista de sugestões de pesquisa com as palavras-chave corretas será exibida. Você também pode pesquisar nomes absolutos colocando-os entre aspas.

   :::image type="content" source="media/tutorial-asset-search/keyword-search.png" alt-text="Pesquisa de ativos de palavra-chave do Azure Purview":::

## <a name="edit-an-asset"></a>Editar um ativo

1. Selecione um dos ativos dos resultados da pesquisa. Depois selecione **Editar**

1. Na guia **Visão geral**, você pode adicionar uma descrição.

    :::image type="content" source="./media/tutorial-asset-search/overview-tab.png" alt-text="Captura de tela mostrando o campo Descrição na guia Visão geral.":::

1. Selecione a guia **Contatos**. Ao lado de **Proprietários**, em **Selecionar usuário ou grupo**, comece digitando seu endereço de email corporativo.

    :::image type="content" source="./media/tutorial-asset-search/contacts-tab.png" alt-text="Captura de tela mostrando campos preenchidos.":::

    Uma sugestão de nome é exibida automaticamente.

1. Ao lado de **Especialistas**, em **Selecionar usuário ou grupo**, insira um nome (por exemplo, o nome do seu gerente) e selecione **Salvar**.

    Os campos descrição, nome do proprietário e nome do especialista agora estão populados.

## <a name="next-steps"></a>Próximas etapas

Antes de prosseguir para o próximo tutorial desta série, reserve algum tempo adicional para explorar a home page do Azure Purview por conta própria. Neste tutorial, você aprendeu a:

> [!div class="checklist"]
>
> * Navegue até a home page do Azure Purview.
> * Pesquise um ativo.
> * Adicione um proprietário de ativos.

Passe para o próximo tutorial para saber como procurar ativos no Azure Purview e descobrir a linhagem de ativos.

> [!div class="nextstepaction"]
> [Procurar os ativos e ver a linhagem deles](tutorial-browse-and-view-lineage.md)
