---
title: "Ações do menu MMC do StorSimple Snapshot Manager | Microsoft Docs"
description: "Descreve como usar as ações de menu padrão do Console de Gerenciamento Microsoft (MMC) no StorSimple Snapshot Manager."
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: 78ef81af-0d3a-4802-be54-ad192f9ac8a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 48f439a566a8067e153aab4fb789937d2f91268d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>Usar as ações de menu do MMC no StorSimple Snapshot Manager

## <a name="overview"></a>Visão geral
No StorSimple Snapshot Manager, você verá as seguintes ações listadas em todos os menus de ação e todas as variações do painel de **Ações** .

* Visualizar
* Nova Janela a Partir Daqui 
* Atualizar 
* Exportar Lista 
* Ajuda 

Essas ações fazem parte do MMC (Console de Gerenciamento Microsoft) e não são específicas ao StorSimple Snapshot Manager. Este tutorial descreve essas ações e explica como usar cada uma delas no StorSimple Snapshot Manager.

## <a name="view"></a>Visualizar
Você pode usar a opção **Exibir** para alterar a exibição do painel **Resultados** e alterar a exibição da janela do console. 

#### <a name="to-change-the-results-pane-view"></a>Para alterar o modo de exibição de painel de Resultados
1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Escopo**, clique com o botão direito em qualquer nó ou expanda o nó e clique com o botão direito em um item no painel **Resultados**, em seguida, clique na opção **Exibir**. 
3. Para adicionar ou remover as colunas que aparecem no painel **Resultados**, clique em **Adicionar/Remover Colunas**. A caixa de diálogo **Adicionar/Remover Colunas** é exibida.
   
    ![Adicionar ou remover colunas do painel de Resultados](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 
4. Preencha o formulário da seguinte maneira:
   
   * Selecione os itens na lista de colunas **Disponível** e clique em **Adicionar** para adicioná-los à lista **Colunas exibidas**. 
   * Clique nos itens na lista **Colunas exibidas** e em **Remover** para removê-los da lista. 
   * Selecione um item na lista de colunas **Exibidas** e clique em **Mover para cima** ou **Mover para baixo** para mover o item para cima ou para baixo na lista. 
   * Clique em **Restaurar Padrões** para retornar para a configuração do painel **Resultados** padrão. 
5. Quando tiver concluído suas seleções, clique em **OK**. 

#### <a name="to-change-the-console-window-view"></a>Para alterar a exibição da janela de console
1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Escopo**, clique com o botão direito em qualquer nó, clique em **Exibir**, em seguida, em **Personalizar**. A caixa de diálogo **Personalizar** é exibida.
   
    ![Personalizar a janela do console](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 
3. Marque ou desmarque as caixas de seleção para exibir ou ocultar itens na janela do console. Quando tiver concluído suas seleções, clique em **OK**.

## <a name="new-window-from-here"></a>Nova Janela a Partir Daqui
Você pode usar a opção **Nova Janela a Partir Daqui** para abrir uma nova janela do console.

#### <a name="to-open-a-new-console-window"></a>Para abrir uma nova janela do console
1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Escopo**, clique com o botão direito em qualquer nó, em seguida, clique em **Nova Janela Daqui**. 
   
    Uma nova janela aparece, mostrando apenas o escopo que você selecionou. Por exemplo, se você clicar com o botão direito no nó **Políticas de Backup**, a nova janela mostrará apenas o nó **Políticas de Backup** no painel **Escopo** painel e uma lista das políticas de backup definidas no painel **Resultados**. Veja os exemplos a seguir.
   
    ![Nova Janela a Partir Daqui](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 

## <a name="refresh"></a>Atualizar
Você pode usar a ação **Atualizar** para atualizar a janela do console.

#### <a name="to-update-the-console-window"></a>Para atualizar a janela do console
1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Escopo**, clique com o botão direito em qualquer nó ou expanda o nó e clique com o botão direito em um item no painel **Resultados**, em seguida, clique em **Atualizar**. 

## <a name="export-list"></a>Exportar Lista
Você pode usar a ação **Exportar Lista** para salvar uma lista em um arquivo CSV (valores separados por vírgulas). Por exemplo, você pode exportar a lista de políticas de backup ou o catálogo de backups. Em seguida, você pode importar o arquivo CSV em um aplicativo de planilha para análise.

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>Para salvar uma lista em um arquivo CSV (valores separados por vírgulas)
1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager. 
2. No painel **Escopo**, clique com o botão direito em qualquer nó ou expanda o nó e clique com o botão direito em um item no painel **Resultados**, em seguida, clique em **Exportar Lista**. 
3. A caixa de diálogo **Exportar Lista** é exibida. Preencha o formulário da seguinte maneira: 
   
   1. Na caixa **Nome de arquivo** , digite um nome para o arquivo CSV ou clique na seta para selecionar na lista suspensa.
   2. Na caixa **Salvar como tipo** , clique na seta e selecione um tipo de arquivo na lista suspensa.
   3. Para salvar apenas os itens selecionados, selecione as linhas e marque a caixa de seleção **Salvar apenas linhas selecionadas** . Para salvar todas as listas exportadas, desmarque a caixa de seleção **Salvar apenas linhas selecionadas** .
   4. Clique em **Salvar**.
      
      ![Exportar lista como um arquivo de valores separados por vírgula](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 

## <a name="help"></a>Ajuda
Você pode usar o menu **Ajuda** para exibir a ajuda online disponível para o MMC e o StorSimple Snapshot Manager.

#### <a name="to-view-available-online-help"></a>Para exibir a ajuda online disponível
1. Clique no ícone da área de trabalho para iniciar o StorSimple Snapshot Manager.
2. No painel **Escopo**, clique com o botão direito em qualquer nó ou expanda o nó e clique com o botão direito em um item no painel **Resultados**, em seguida, clique em **Ajuda**. 

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre [a interface do usuário do StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md).
* Saiba mais sobre [como usar o StorSimple Snapshot Manager para administrar sua solução do StorSimple](storsimple-snapshot-manager-admin.md).

