---
title: Usar o Purview Studio
description: Este artigo conceitual descreve como usar o Azure alcance Studio.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: d8e6c4b2addf9745b2ddabe8f6fdad9d82dce59f
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97503943"
---
# <a name="use-purview-studio"></a>Usar o Purview Studio

Este artigo fornece uma visão geral de alguns dos principais recursos do Azure alcance.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta ativa do alcance já foi criada no portal do Azure e o usuário tem permissões para acessar o alcance Studio.

## <a name="launch-purview-account"></a>Iniciar conta do alcance

* Para iniciar sua conta do alcance, vá para contas do alcance em portal do Azure, selecione a conta que você deseja iniciar e inicie a conta.

   :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="Captura de tela da seleção para iniciar o catálogo de contas do Azure Purview.":::

* Outra maneira de iniciar a conta do alcance é ir para `https://web.purview.azure.com` , selecionar **Azure Active Directory** e um nome de conta para iniciar a conta.

## <a name="home-page"></a>Página inicial

**Home** é a página inicial para o cliente alcance do Azure.

 :::image type="content" source="./media/use-purview-studio/purview-homepage.png" alt-text="Captura de tela da Home Page.":::

A lista a seguir resume os principais recursos da **Home Page**. Cada número na lista corresponde a um número realçado na captura de tela anterior.

1. Nome amigável do catálogo. Você pode definir o nome do catálogo no **centro de gerenciamento** > **informações da conta*.

2. A análise de catálogo mostra o número de:
    - Usuários, grupos e aplicativos
    - Fontes de dados
    - Ativos
    - Termos do glossário

3. A caixa de pesquisa permite pesquisar ativos de dados no catálogo de dados.

4. Os botões de acesso rápido fornecem acesso às funções usadas com frequência do aplicativo. Os botões apresentados dependem da função atribuída à sua conta de usuário.

    - Para o *curador de dados*, os botões são **centro de conhecimento**, **procurar ativos**, **gerenciar Glossário** e **Exibir informações**.
    - Para o *leitor de dados*, os botões em destaque são centro de **conhecimento**, **procurar ativos**, **Exibir Glossário** e **Exibir informações**.
    - Para o curador de dados do *administrador de fonte de dados*  +  , os botões em destaque são **centro de conhecimento**, **registrar fontes de dados**, **procurar ativos** e **gerenciar Glossário**.
    - Para o leitor de dados do *administrador de fonte de dados*  +  , os botões em destaque são **centro de conhecimento**, **registrar fontes de dados**, **procurar ativos** e **Exibir Glossário**.

5. A barra de navegação à esquerda ajuda a localizar as páginas principais do aplicativo. Os botões apresentados dependem da função atribuída à sua conta de usuário.

    - Para o *curador de dados*, os botões são **página inicial**, **Glossário**, **ideias** e **centro de gerenciamento**.
    - Para o *leitor de dados*, os botões são **página inicial**, **Glossário**, **ideias** e **centro de gerenciamento**.
    - Para   +  o *leitor/curador de dados* do administrador de fonte de dados, os botões são **página inicial**, **fontes**, **Glossário**, **ideias** e **centro de gerenciamento**.
  
6. A guia **acessado recentemente** mostra uma lista de ativos de dados acessados recentemente. Para obter informações sobre como acessar ativos, consulte [Pesquisar o catálogo de dados](how-to-search-catalog.md) e [procurar por tipo de ativo](how-to-browse-catalog.md#browse-experience).  A guia **meus itens** é uma lista de ativos de dados pertencentes ao usuário conectado.
7. **Links úteis** contêm links para status de região, documentação, preço, visão geral e status de alcance
8. A barra de navegação superior contém informações sobre as notas de versão/atualizações, as seções conta do alcance, notificações, ajuda e comentários.

## <a name="knowledge-center"></a>Centro de conhecimento

O centro de conhecimento é onde você pode encontrar todos os vídeos e tutoriais relacionados ao alcance.

## <a name="guided-tours"></a>Passeios guiados

Cada UX no Azure alcance Studio terá Tours guiados para dar uma visão geral da página. Para iniciar o tour guiado, selecione **ajuda** na barra superior e selecione **Tours guiados**.

:::image type="content" source="./media/use-purview-studio/guided-tour.png" alt-text="Captura de tela do tour guiado.":::

> [!Important]
   > A função de administrador de fonte de dados por si só não tem acesso ao alcance Studio.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar uma entidade de segurança](tutorial-scan-data.md)