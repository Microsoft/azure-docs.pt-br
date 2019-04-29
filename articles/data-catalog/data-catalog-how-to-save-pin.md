---
title: Salva pesquisas e fixa ativos de dados no Catálogo de Dados do Azure
description: Artigo de instruções que destaca recursos do Catálogo de Dados do Azure para salvar fontes de dados e ativos de dados para uso posterior.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 6bd00a81-820d-4b7c-91fa-ab09e575474c
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: df3220ddb80ebc329ed6b0024ca4eefd2bdfb321
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61001957"
---
# <a name="save-searches-and-pin-data-assets-in-azure-data-catalog"></a>Salva pesquisas e fixa ativos de dados no Catálogo de Dados do Azure
## <a name="introduction"></a>Introdução
O Catálogo de Dados do Azure fornece recursos para descoberta de fontes de dados. Você pode rapidamente pesquisar e filtrar o catálogo para localizar fontes de dados e entender a finalidade pretendida, facilitando a localização dos dados corretos para o trabalho em questão.

Porém e se você precisar trabalhar regularmente com os mesmos dados? E se você e outros usuários regularmente contribuírem com conhecimento para as mesmas fontes de dados no catálogo? Nessas situações, precisar emitir repetidamente as mesmas pesquisas pode ser ineficiente. É aqui que pesquisas salvas e ativos de dados fixos podem ajudar.

## <a name="saved-searches"></a>Pesquisas salvas
Uma pesquisa salva no Catálogo de Dados é uma definição de pesquisa reutilizável por usuário. Você pode definir uma pesquisa, inclusive os termos de pesquisa, as marcas e outros filtros e, em seguida, salvá-la. Você pode executar novamente a definição de pesquisa salva posteriormente para retornar quaisquer ativos de dados que correspondem aos seus critérios de pesquisa.

### <a name="create-a-saved-search"></a>Criar uma pesquisa salva
Para criar uma pesquisa salva, faça o seguinte:
1. No portal do Catálogo de Dados do Azure, na janela **Pesquisa Atual**, clique em **Salvar**. 

    ![Configurações da Pesquisa Atual, link Salvar](./media/data-catalog-how-to-save-pin/01-save-option.png) 

2. Insira os critérios de pesquisa que você deseja reutilizar e, em seguida, clique em **Salvar**.

    ![Configurações da Pesquisa Atual, nome da pesquisa salva](./media/data-catalog-how-to-save-pin/02-name.png)

3. Quando solicitado, insira um nome para a pesquisa salva. Escolha um nome que seja significativo e descritivo dos ativos de dados que serão retornados pela pesquisa.

### <a name="manage-saved-searches"></a>Gerenciar pesquisas salvas
Depois de salvar uma ou mais pesquisas, uma opção **Pesquisas Salvas** é exibida abaixo da caixa **Pesquisa Atual**. Quando a lista é expandida, todas as pesquisas salvas são exibidas.

 ![Lista de pesquisas salvas](./media/data-catalog-how-to-save-pin/03-list.png)

Execute um destes procedimentos:

* Para executar uma pesquisa, selecione uma pesquisa salva na lista.

* Para exibir uma lista de opções de gerenciamento para uma pesquisa salva, clique na seta para baixo ao lado do nome da pesquisa.

    ![Opções para gerenciar as pesquisas salvas](./media/data-catalog-how-to-save-pin/04-managing.png)

* Para inserir um novo nome para a pesquisa salva, selecione **Renomear**. A definição de pesquisa não é alterada.

* Para remover a pesquisa salva de sua lista, selecione **Excluir** e, em seguida, confirme a exclusão.

* Para marcar a pesquisa salva como sua pesquisa padrão, selecione **Salvar como Padrão**. Se você executar uma pesquisa "vazia" na home page do Catálogo de Dados do Azure, a pesquisa padrão será executada. Além disso, a pesquisa marcada como padrão será exibida na parte superior da lista **Pesquisas Salvas**.

### <a name="organizational-saved-searches"></a>Pesquisas organizacionais salvas
Todos os usuários na sua organização podem salvar pesquisas para uso próprio. Os administradores do Catálogo de Dados também podem salvar pesquisas para todos os usuários dentro da organização. Quando os administradores salvam uma pesquisa, é apresentada a eles uma opção para **Compartilhar dentro da empresa**. Selecionar essa opção compartilha a pesquisa salva com todos os usuários na organização.

 ![Pesquisas organizacionais salvas](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)

## <a name="pinned-data-assets"></a>Ativos de dados fixos
Com pesquisas salvas, você pode salvar e reutilizar as definições de pesquisa. Os ativos de dados que são retornados pelas pesquisas podem mudar ao longo do tempo conforme o conteúdo do catálogo muda. Quando você fixa ativos de dados, pode identificar explicitamente ativos de dados específicos para facilitar o acesso a eles sem necessidade de usar uma pesquisa.

A fixação de um ativo de dados é simples. Para adicionar o ativo de dados à sua lista fixada, simplesmente clique no ícone de **fixar**. O ícone é exibido no canto do bloco ativo, no modo de exibição de bloco e na coluna mais à esquerda, no modo de exibição de lista, no portal do Catálogo de Dados do Azure.

![O ícone de fixar ativo de dados](./media/data-catalog-how-to-save-pin/05-pinning.png)

Desafixar um ativo de dados é igualmente simples. Simplesmente clique no ícone **desafixar** para alternar a configuração para o ativo selecionado.

![Ícone de desafixar o ativo de dados](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="the-my-assets-section"></a>A seção Meus Ativos
A home page do portal do Catálogo de Dados inclui uma seção **Meus Ativos** que exibe os ativos de interesse do usuário atual. Essa seção inclui os ativos fixos e as pesquisas salvas.

![A seção Meus ativos na home page](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>Resumo
O Catálogo de Dados do Azure fornece recursos que tornam mais fácil de descobrir as fontes de dados de que você precisa para que você e outros membros da organização possam gastar menos tempo procurando dados e mais tempo trabalhando com eles. As pesquisas salvas e os ativos de dados fixos aprimoram esses recursos principais para que os usuários possam facilmente identificar fontes de dados com as quais eles trabalham repetidamente.
