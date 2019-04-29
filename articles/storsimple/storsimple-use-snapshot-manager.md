---
title: Interface do usuário do StorSimple Snapshot Manager | Microsoft Docs
description: Descreve a interface do usuário do StorSimple Snapshot Manager e explica como usá-la para gerenciar trabalhos de backup e o catálogo de backups.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: c7d91892-2881-41a2-a7a2-908dc3646493
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.custom: ''
ms.openlocfilehash: 46225e5a332e035e4d1cc256e71c4b5d8686fd47
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60845064"
---
# <a name="use-storsimple-snapshot-manager-user-interface-to-manage-backup-jobs-and-backup-catalog"></a>Usar a interface de usuário do StorSimple Snapshot Manager para gerenciar trabalhos de backup e catálogo de backups

## <a name="overview"></a>Visão geral
O StorSimple Snapshot Manager traz uma interface do usuário intuitiva que pode ser usada para fazer e gerenciar backups. Este tutorial fornece uma introdução à interface do usuário e explica como usar cada um dos componentes. Para obter uma descrição detalhada do StorSimple Snapshot Manager, confira a seção [O que é o StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="console-description"></a>Descrição do console
Para exibir a interface do usuário, clique no ícone do StorSimple Snapshot Manager na área de trabalho. A janela de console aparece, conforme mostrado na ilustração a seguir.

![Painéis do Gerenciador de instantâneos do StorSimple](./media/storsimple-use-snapshot-manager/HCS_SSM_gui_panes.png)

A janela do console tem cinco elementos principais. Clique no link apropriado para obter uma descrição completa de cada elemento.

* [Barra de menus](#menu-bar) 
* [Barra de ferramentas](#tool-bar) 
* [Painel Escopo](#scope-pane) 
* [Painel Resultados](#results-pane) 
* [Painel Ações](#actions-pane) 

Além disso, o StorSimple Snapshot Manager dá suporte à [navegação por teclado e diversos atalhos](#keyboard-navigation-and-shortcuts).

### <a name="console-accessibility"></a>Acessibilidade do console
A interface do usuário do StorSimple Snapshot Manager dá suporte aos recursos de acessibilidade fornecidos pelo sistema operacional Windows e o MMC (Console de Gerenciamento Microsoft), bem como alguns atalhos de teclado específicos ao StorSimple Snapshot Manager. 

* Para obter uma descrição dos recursos de acessibilidade do Windows, vá até [Atalhos de teclado para o Windows](https://support.microsoft.com/kb/126449). 
* Para obter uma descrição dos recursos de acessibilidade do MMC, vá até [Acessibilidade para o MMC 3.0](https://technet.microsoft.com/library/cc766075.aspx)
* Para obter uma descrição dos recursos de acessibilidade do StorSimple Snapshot Manager, vá até [Navegação por teclado e atalhos](#keyboard-navigation-and-shortcuts).

## <a name="menu-bar"></a>Barra de menus
A barra de menus na parte superior da janela do console contém os menus [Arquivo](#file-menu), [Ação](#action-menu), [Exibição](#view-menu), [Favoritos](#favorites-menu), [Janela](#window-menu) e [Ajuda](#help-menu).

Clique em qualquer item na barra de menus para ver uma lista dos comandos disponíveis nesse menu. O exemplo a seguir mostra o menu **Exibição** selecionado na barra de menus.

![Menu de Exibição selecionado](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

### <a name="file-menu"></a>Menu Arquivo
O menu **Arquivo** contém comandos padrão do MMC (Console de Gerenciamento Microsoft).

#### <a name="menu-access"></a>Acesso ao menu
Para exibir o menu **Arquivo**, clique em **Arquivo** na barra de menus. O seguinte menu será exibido.

![Menu Arquivo do StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_FileMenu.png) 

#### <a name="menu-description"></a>Descrição do menu
A tabela a seguir descreve os itens que aparecem no menu **Arquivo** .

| Item de menu | DESCRIÇÃO |
|:--- |:--- |
| Novo |Clique em **Novo** para criar um novo console com base no StorSimple Snapshot Manager. |
| Aberto |Clique em **Abrir** para abrir um console existente. |
| Salvar |Clique em **Salvar** para salvar o console atual. |
| Salvar como |Clique em **Salvar Como** para criar uma nova instância renomeada do console atual. Use a opção **Salvar como** para personalizar uma exibição e salvá-la para recuperação posterior. Por exemplo, você pode criar snap-ins do StorSimple Snapshot Manager que apontem para servidores específicos. |
| Adicionar/Remover Snap-in |Clique em **Adicionar/Remover Snap-in** para adicionar ou remover snap-ins e organizar nós no painel **Escopo**. Para obter mais informações, vá para [Adicionar, Remover e Organizar Snap-ins e Extensões no MMC 3.0](https://technet.microsoft.com/library/cc722035.aspx). |
| Opções |Clique em **Opções** para alterar o ícone do console, especificar permissões e modos de acesso do usuário ou excluir arquivos de console para aumentar o espaço em disco disponível. |
| Lista de caminhos de arquivo |Clique em um caminho na lista numerada para reabrir um arquivo que você abriu recentemente. |
| Sair |Clique em **Sair** para fechar o menu **Arquivo**. |

### <a name="action-menu"></a>Menu Ação
Use o menu **Ação** para selecionar uma das ações disponíveis. Os itens disponíveis dependem da seleção feita no painel **Escopo** ou no painel **Resultados**.

#### <a name="menu-access"></a>Acesso ao menu
Para exibir o menu **Ação** , siga um destes procedimentos:

* Clique com o botão direito do mouse em um item no painel **Escopo** ou no painel **Resultados**.
* Selecione um item no painel **Escopo** ou no painel **Resultados** e clique em **Ação** na barra de menus. 

Por exemplo, se você selecionar o nó superior do painel **Escopo** e depois clicar com o botão direito do mouse em **Ação** na barra de menus, o seguinte menu aparece.

![Menu Ação do StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_Action_menu.png)

O painel **Ações** (à direita do console) contém a mesma lista de ações que o menu **Ação**. Além disso, o painel **Ações** contém as opções do menu **Exibição**, que permitem criar uma exibição personalizada do painel **Resultados**.

![Painel Ações com o menu Exibição aberto](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

#### <a name="menu-description"></a>Descrição do menu
A tabela a seguir contém uma lista em ordem alfabética das ações do StorSimple Snapshot Manager. 

* A coluna **Ação** lista ações que você pode realizar em nós e resultados. 
* A coluna **Navegação** explica como exibir o menu **Ação** apropriado para que você possa selecionar a ação. Algumas ações aparecem em vários menus **Ação** . Para essas ações, selecione uma opção de **Navegação** da lista com marcadores. 
* A coluna **Descrição** descreve como utilizar cada ação do menu **Ação** ou do painel Ações e explica o que ela faz.

> [!NOTE]
> O painel **Ações** e os menus **Ação** contêm opções adicionais, como **Exibição**, **Nova Janela daqui**, **Atualizar**, **Exportar Lista** e **Ajuda**. Essas opções estão disponíveis como parte do MMC e não são específicas ao StorSimple Snapshot Manager. A tabela inclui descrições dessas opções.
> 
> 

| Ação | Navegação | DESCRIÇÃO |
|:--- |:--- |:--- |
| Autenticar |Clique no nó **Dispositivos** e clique com o botão direito do mouse em um dispositivo no painel **Resultados**. |Clique em **Autenticar** para digitar a senha que você configurou para o dispositivo. |
| Clone |Expanda **Catálogo de Backup**s, expanda **Instantâneos em Nuvem**, clique em um backup com data e selecione um volume no painel **Resultados**. |Clique em **Clone** para criar uma cópia de um instantâneo de nuvem e armazená-la em um local que você designar. |
| Configurar um Dispositivo |Clique com o botão direito do mouse no nó **Dispositivos** . |Clique em **Configurar um Dispositivo** para configurar um ou vários dispositivos para se conectar ao host do Windows. |
| Criar Política de Backup |Faça uma das opções a seguir:<ul><li>Clique com botão direito do mouse em **Políticas de Backup**.</li><li>Clique ou expanda **Grupos de volumes** e clique com o botão direito do mouse em um grupo de volumes.</li><li>Clique ou expanda **Catálogo de Backup** e clique com o botão direito do mouse em um grupo de volumes.</li></ul> |Clique em **Criar Política de Backup** para configurar um backup agendado para um grupo de volumes. |
| Criar Grupo de Volumes |Faça uma das opções a seguir:<ul><li>Clique no nó **Volumes** e, em seguida, clique com o botão direito do mouse em um volume no painel **Resultados**.</li><li>Clique com o botão direito do mouse no nó **Grupos de Volumes** .</li></ul> |Clique em **Criar Grupo de Volumes** para atribuir volumes a um grupo de volumes. |
| Excluir |Clique em um nó ou resultado (esse item aparece em vários menus de **Ação** e nos painéis de **Ações**.) |Clique em **Excluir** para excluir o nó ou o resultado que você selecionou. Quando a caixa de diálogo de confirmação aparecer, confirme ou cancele a exclusão. |
| Detalhes |Clique no nó **Dispositivos** e, em seguida, clique com o botão direito do mouse em um dispositivo no painel **Resultados**. |Clique em **Detalhes** para ver os detalhes da configuração de um dispositivo. |
| Editar |Clique em **Políticas de Backup** e clique com o botão direito do mouse em uma política no painel **Resultados**. |Clique em **Editar** para alterar o agendamento de backup de um grupo de volumes. |
| Exportar Lista |Clique em um nó ou resultado (esse item aparece em todos os menus de **Ação** e nos painéis de **Ações**.) |Clique em **Exportar Lista** para salvar uma lista em um arquivo CSV (valores separados por vírgulas). Em seguida, você pode importar esse arquivo para um aplicativo de planilha para análise. |
| Ajuda |Clique em qualquer nó ou resultado. (Esse item aparece em todos os menus de **Ação** e painéis de **Ações**.) |Clique em **Ajuda** para abrir a Ajuda online em uma janela separada do navegador. |
| Nova Janela a Partir Daqui |Clique em um nó ou resultado (esse item aparece em todos os menus de **Ação** e nos painéis de **Ações**.) |Clique em **Nova Janela a Partir Daqui** para abrir uma nova janela do StorSimple Snapshot Manager. |
| Atualizar |Clique em um nó ou resultado (esse item aparece em todos os menus de **Ação** e nos painéis de **Ações**.) |Clique em **Atualizar** para atualizar a janela StorSimple Snapshot Manager exibida atualmente. |
| Atualizar Dispositivo |Clique no nó **Dispositivos** e clique com o botão direito do mouse em um dispositivo no painel **Resultados**. |Clique em **Atualizar Dispositivo** para sincronizar um dispositivo específico conectado ao StorSimple Snapshot Manager. |
| Atualizar Dispositivos |Clique com o botão direito do mouse no nó **Dispositivos** . |Clique em **Atualizar Dispositivos** para sincronizar sua lista de dispositivos conectados com o StorSimple Snapshot Manager. |
| Examinar volumes novamente |Clique com o botão direito do mouse no nó **Volumes** . |Clique em **Examinar volumes novamente** para atualizar a lista de volumes que aparece no painel **Resultados**. |
| Restaurar |Expanda **Catálogo de Backup**, expanda um grupo de volumes, expanda **Instantâneos Locais** ou **Instantâneos em Nuvem** e clique com o botão direito do mouse em um backup. |Clique em **Restaurar** para substituir os dados do grupo de volumes atual pelos dados do backup selecionado. |
| Fazer Backup |Faça uma das opções a seguir:<ul><li>Expanda **Grupos de volumes** e clique com o botão direito do mouse em um grupo de volumes.</li><li>Expanda **Catálogo de Backup** e clique com o botão direito do mouse em um grupo de volumes.</li></ul> |Clique em **Fazer Backup** para iniciar um trabalho de backup imediatamente. |
| Alternar Exibição de Importações |Clique com o botão direito do mouse no nó superior do painel **Escopo** (o nó **StorSimple Snapshot Manager** nos exemplos). |Clique em **Alternar Exibição de Importações** para mostrar ou ocultar os grupos de volumes e backups associados que foram importados do painel de serviço do Gerenciador de Dispositivos StorSimple. |

### <a name="view-menu"></a>Menu Exibir
Use o menu **Exibir** para criar uma exibição personalizada do conteúdo do painel **Resultados**. O menu **Exibir** contém as opções **Adicionar/Remover Colunas** e **Personalizar**.

#### <a name="menu-access"></a>Acesso ao menu
Você pode acessar o menu **Exibir** na barra de menus ou no painel **Ações**.

![Menu Exibir do StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png) 

#### <a name="menu-description"></a>Descrição do menu
A tabela a seguir descreve os itens que aparecem no menu **Exibir** .

| Item de menu | DESCRIÇÃO |
|:--- |:--- |
| Adicionar/Remover Colunas |Clique em **Adicionar/Remover Colunas** para adicionar ou remover colunas do painel **Resultados**. |
| Personalizar |Clique em **Personalizar** para mostrar ou ocultar itens na janela do console do StorSimple Snapshot Manager. |

### <a name="favorites-menu"></a>Menu Favoritos
Use o menu **Favoritos** para adicionar, remover e organizar exibições de página e tarefas que você usa com frequência. 

#### <a name="menu-access"></a>Acesso ao menu
Você pode acessar o menu **Favoritos** na barra de menus.

![Menu Favoritos do StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_FavoritesMenu.png)

#### <a name="menu-description"></a>Descrição do menu
A tabela a seguir descreve os itens que aparecem no menu **Favoritos** .

| Item de menu | DESCRIÇÃO |
|:--- |:--- |
| Adicionar aos Favoritos |Clique em **Adicionar aos Favoritos** para adicionar a exibição atual à sua lista de favoritos. |
| Organizar Favoritos |Clique em **Organizar Favoritos** para organizar o conteúdo da pasta Favoritos. |

### <a name="window-menu"></a>Menu Janela
Use o menu **Janela** para adicionar e reorganizar as janelas do console do StorSimple Snapshot Manager.

#### <a name="menu-access"></a>Acesso ao menu
Você pode acessar o menu **Janela** na barra de menus.

![Menu Janela do StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_WindowMenu.png)

A lista numerada na parte inferior do menu mostra as janelas que estão abertas. Clique em qualquer janela da lista para trazer a janela ao primeiro plano. 

#### <a name="menu-description"></a>Descrição do menu
A tabela a seguir descreve os itens que aparecem no menu da Janela.

| Item de menu | DESCRIÇÃO |
|:--- |:--- |
| Nova Janela |Clique em **Nova Janela** para abrir uma nova janela do console (além da janela existente). |
| Em cascata |Clique em **Em cascata** para exibir as janelas abertas do console no estilo em cascata. |
| Lado a lado horizontalmente |Clique em **Lado a lado horizontalmente** para exibir as janelas abertas do console lado a lado (ou em grade). |
| Organizar Ícones |Se você tiver várias janelas do console abertas e dispersas em sua área de trabalho, minimize-as e clique em **Organizar Ícones** para organizá-las em uma linha horizontal na parte inferior da tela. |

### <a name="help-menu"></a>Menu Ajuda
Use o menu **Ajuda** para exibir a ajuda online disponível para o MMC e o StorSimple Snapshot Manager. Você também pode exibir informações sobre as versões de software do MMC e do StorSimple Snapshot Manager que estão instaladas no sistema. 

Você pode acessar o menu **Ajuda** na barra de menus. Você também pode acessar os tópicos de ajuda do StorSimple Snapshot Manager no painel **Ações** .

![Menu Ajuda do StorSimple Snapshot Manager](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpMenu.png)

#### <a name="menu-description"></a>Descrição do menu
A tabela a seguir descreve os itens que aparecem no menu Ajuda.

| Item de menu | DESCRIÇÃO |
|:--- |:--- |
| Ajuda no StorSimple Snapshot Manager |Clique em **Ajuda no StorSimple Snapshot Manager** para abrir a ajuda do StorSimple Snapshot Manager em uma janela separada. |
| Tópicos de Ajuda |Clique em **Tópicos de Ajuda** para abrir a ajuda online do MMC em uma janela separada. |
| Site do TechCenter |Clique em **Site da Web do TechCenter** para abrir a home page do Microsoft TechNet Tech Center em uma janela separada. |
| No Console de Gerenciamento Microsoft |Clique em **Sobre o Console de Gerenciamento Microsoft** para ver qual versão do Console de Gerenciamento Microsoft está instalada em seu sistema. |
| Sobre o StorSimple Snapshot Manager |Clique em **Sobre o StorSimple Snapshot Manager** para ver qual versão do snap-in está instalada em seu sistema. |

## <a name="tool-bar"></a>Barra de ferramentas
A barra de ferramentas, localizada abaixo da barra de menus, contém ícones de navegação e tarefas. Cada ícone é um atalho para uma tarefa específica.

### <a name="icon-descriptions"></a>Descrições dos ícones
A tabela a seguir descreve os ícones que aparecem na barra de ferramentas. 

| ícone | DESCRIÇÃO |
|:--- |:--- |
| ![Seta para a esquerda](./media/storsimple-use-snapshot-manager/HCS_SSM_LeftArrow.png) |Clique no ícone de seta para a esquerda para retornar à página anterior. |
| ![Seta para a direita](./media/storsimple-use-snapshot-manager/HCS_SSM_RightArrow.png) |Clique na seta para a direita para ir para a próxima página (se a seta estiver cinza, a ação estará indisponível). |
| ![Ícone para cima](./media/storsimple-use-snapshot-manager/HCS_SSM_Up.png) |Clique no ícone para cima para subir um nível na árvore do console (o painel **Escopo** ). |
| ![Mostrar/ocultar árvore de console](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowConsoleTree.png) |Clique no ícone mostrar/ocultar da árvore do console para mostrar ou ocultar o painel **Escopo** . |
| ![Exportar Lista](./media/storsimple-use-snapshot-manager/HCS_SSM_ExportListIcon.png) |Clique no ícone exportar lista para exportar uma lista para um arquivo CSV que você especificar. |
| ![Ícone de ajuda](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpIcon.png) |Clique no ícone de ajuda para abrir um tópico de ajuda online do MMC. |
| ![Mostrar/ocultar painel de Ações](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowAction.png) |Clique no ícone mostrar/ocultar do painel **Ações** para mostrar ou ocultar o painel **Ações**. |

## <a name="scope-pane"></a>Painel Escopo
O painel **Escopo** é o painel esquerdo da interface do usuário do StorSimple Snapshot Manager. Ele contém a árvore do console (ou nó) e é o principal mecanismo de navegação do StorSimple Snapshot Manager. 

### <a name="scope-pane-structure"></a>Estrutura do painel Escopo
O painel **Escopo** contém uma série de objetos que podem ser clicados (nós), organizados em uma estrutura de árvore. 

![Painel Escopo](./media/storsimple-use-snapshot-manager/HCS_SSM_Scope_pane.png) 

* Para expandir ou recolher um nó, clique no ícone de seta ao lado do nome do nó.
* Para exibir o status ou o conteúdo de um nó, clique no nome do nó. As informações são exibidas no painel **Resultados** . 

O painel **Escopo** contém os seguintes nós: 

* [Nó Dispositivos](#devices-node) 
* [Nó Volumes](#volumes-node) 
* [Nó Grupos de Volumes](#volume-groups-node) 
* [Nó Políticas de Backup](#backup-policies-node) 
* [Nó Catálogo de Backups](#backup-catalog-node) 
* [Nó Trabalhos](#jobs-node) 

### <a name="scope-pane-tasks"></a>Tarefas do painel Escopo
Você pode usar o painel **Escopo** para concluir uma ação em um nó específico. Para selecionar uma tarefa, siga um destes procedimentos:

* Clique com o botão direito no nó e selecione a tarefa no menu que aparece.
* Clique no nó e clique na barra de menus **Ação** . Selecione a tarefa no menu que aparece.
* Clique no nó e selecione a ação no painel **Ações** .

Quando você seleciona um nó e usa qualquer um desses métodos para ver uma lista de tarefas, apenas as ações que podem ser executadas nesse nó são mostradas.

### <a name="devices-node"></a>Nó Dispositivos
O nó **Dispositivos** representa os dispositivos do StorSimple e os dispositivos virtuais do StorSimple que estão conectados ao StorSimple Snapshot Manager. Selecione esse nó para conectar e configurar um dispositivo e importar seus volumes associados, grupos de volumes e cópias de backup existentes. Vários dispositivos podem ser conectados a um único host.

* Para expandir o nó, clique no ícone de seta ao lado de **Dispositivos**.
* Para ver um menu das ações disponíveis, clique com o botão direito no nó **Dispositivos** ou em qualquer um de nós que aparecem na exibição expandida.
* Para ver uma lista dos dispositivos configurados, clique em **Dispositivos** no painel **Escopo**. A lista de dispositivos, com informações sobre cada dispositivo, aparece no painel **Resultados** .

### <a name="volumes-node"></a>Nó Volumes
O nó **Volumes** representa as unidades que correspondem aos volumes montados pelo host, incluindo aqueles descobertos através do iSCSI e os descobertos através de um dispositivo. Use este nó para exibir a lista de volumes disponíveis e atribuir volumes individuais aos grupos de volumes.

* Para expandir o nó, clique no ícone de seta ao lado de **Volumes**.
* Para ver um menu das ações disponíveis, clique com o botão direito no nó **Volumes** ou em qualquer um dos nós que aparecem na exibição expandida.
* Para ver uma lista de volumes, clique em **Volumes** no painel **Escopo**. A lista de volumes, com informações sobre cada volume, aparece no painel **Resultados** .

### <a name="volume-groups-node"></a>Nó Grupos de Volumes
Grupos de volumes também são conhecidos como grupos de consistência. Cada grupo de volumes é um pool de volumes relacionados ao aplicativo que ajuda a garantir a consistência do aplicativo durante operações de backup. Use o nó **Grupos de Volumes** para configurar esses grupos e para fazer backups interativos ou criar agendamentos de backup. 

* Para expandir o nó, clique no ícone de seta ao lado de **Grupos de Volumes**.
* Para ver um menu das ações disponíveis, clique com o botão direito do mouse no nó **Grupos de Volumes** ou em qualquer um dos nós que aparecem na exibição expandida.
* Para ver uma lista de grupos de volumes, clique em **Grupos de Volumes** no painel **Escopo**. A lista de grupos de volumes, com informações sobre cada grupo de volumes, aparece no painel **Resultados** .

### <a name="backup-policies-node"></a>Nó Políticas de Backup
Políticas de backup são agendas de trabalhos para instantâneos locais e na nuvem. Use o nó **Políticas de Backup** para especificar a frequência com que um backup é criado e por quanto tempo ele deve ser mantido. 

* Para expandir o nó, clique no ícone de seta ao lado de **Políticas de Backup**.
* Para ver um menu das ações disponíveis, clique com o botão direito do mouse no nó **Políticas de Backup** ou em qualquer um dos nós que aparecem na exibição expandida.
* Para ver uma lista de políticas de backup, clique em **Políticas de Backup** no painel **Escopo**. A lista de políticas de backup, com informações sobre cada política, aparece no painel **Resultados** .

> [!NOTE]
> Você pode manter um máximo de 64 backups.


### <a name="backup-catalog-node"></a>Nó Catálogo de Backups
O nó **Catálogo de Backups** contém listas de backups locais e externos de volumes do Azure StorSimple. Este nó é organizado por grupo de volumes e cada contêiner do grupo de volumes contém estruturas separadas para instantâneos locais (o nó **Instantâneos Locais**) e instantâneos em nuvem (o nó de **Instantâneos em Nuvem**). Quando expandido, cada contêiner de grupo de volumes lista todos os backups bem-sucedidos que foram feitos interativamente ou por uma política configurada.

* Para expandir o nó, clique no ícone de seta ao lado de **Catálogo de Backups**.
* Para ver um menu das ações disponíveis, clique com o botão direito do mouse no nó **Catálogo de Backups** ou em qualquer um dos nós que aparecem na exibição expandida.
* Para ver uma lista de instantâneos de backup, clique em **Catálogo de Backup** no painel **Escopo**. A lista de instantâneos, com informações sobre cada um deles, aparece no painel **Resultados** .

### <a name="local-snapshots-node"></a>Nó Instantâneos Locais
O nó **Instantâneos Locais** relaciona os instantâneos locais para um grupo de volumes específico. O nó está localizado no nó **Catálogo de Backup** no painel **Escopo**. Instantâneos locais são cópias momentâneas de dados de volume armazenados no dispositivo Azure StorSimple. Normalmente, esse tipo de backup pode ser criado e restaurado rapidamente. Você pode usar um instantâneo local como faria com uma cópia de backup local.

* Para expandir o nó, clique no ícone de seta ao lado de **Instantâneos Locais**.
* Para ver um menu das ações disponíveis, clique com o botão direito no nó **Instantâneos Locais** ou em qualquer um dos nós que aparecem na exibição expandida.
* Para ver uma lista de instantâneos locais, clique em **Instantâneos Locais** no painel **Escopo**. A lista de instantâneos, com informações sobre cada um deles, aparece no painel **Resultados** .

### <a name="cloud-snapshots-node"></a>Nó Instantâneos de Nuvem
O nó **Instantâneos de Nuvem** lista os instantâneos locais para um grupo de volumes específico. O nó está localizado no nó **Catálogo de Backup** no painel **Escopo**. Instantâneos de nuvem são cópias momentâneas de dados de volume armazenados na nuvem. Um instantâneo de nuvem é equivalente a um instantâneo duplicado em um sistema de armazenamento externo diferente. Instantâneos de nuvem são particularmente úteis em cenários de recuperação de desastres.

* Para expandir o nó, clique no ícone de seta ao lado de **Instantâneos de Nuvem**.
* Para ver um menu das ações disponíveis, clique com o botão direito do mouse no nó **Instantâneos de Nuvem** ou em qualquer um dos nós que aparecem na exibição expandida.
* Para ver uma lista de instantâneos de nuvem, clique em **Instantâneos de Nuvem** no painel **Escopo**. A lista de instantâneos, com informações sobre cada um deles, aparece no painel **Resultados** .

### <a name="jobs-node"></a>Nó Trabalhos
O nó **Trabalhos** contém informações sobre trabalhos de backup agendados, em execução e concluídos recentemente. 

* Para expandir o nó, clique no ícone de seta ao lado de **Trabalhos**.
* Para ver um menu das ações disponíveis, clique com o botão direito do mouse no nó **Trabalhos** ou em qualquer um dos nós que aparecem na exibição expandida.
* Para ver uma lista dos trabalhos agendados, expanda o nó **Trabalhos** e clique em **Agendado**. A lista de trabalhos configurados anteriormente e informações sobre cada trabalho aparecem no painel **Resultados** . 
* Para ver uma lista de trabalhos concluídos recentemente, expanda o nó **Trabalhos** e clique em **Últimas 24 horas**. É exibida uma lista de trabalhos que foram concluídos nas últimas 24 horas no painel **Resultados** . O painel **Resultados** também contém informações sobre cada trabalho concluído.
* Para ver uma lista dos trabalhos que estão sendo executados, expanda o nó **Trabalhos** e clique em **Em execução**. A lista de trabalhos em execução e informações sobre cada trabalho aparece no painel **Resultados** .

## <a name="results-pane"></a>Painel Resultados
O painel **Resultados** é o painel central da UI do StorSimple Snapshot Manager. Ele contém listas e informações de status detalhadas para o nó selecionado no painel **Escopo** .

### <a name="example"></a>Exemplo
Para ver o exemplo a seguir, clique no nó **Grupos de Volumes** no painel **Escopo**. O painel **Resultados** exibe uma lista de grupos de volumes com detalhes sobre cada grupo.

![Painel Resultados](./media/storsimple-use-snapshot-manager/HCS_SSM_Results_pane.png) 

Você pode configurar os detalhes mostrados no painel **Resultados**: clique com o botão direito do mouse em um nó no painel **Escopo**, clique em **Exibir** e clique em **Adicionar/Remover Colunas**.

## <a name="actions-pane"></a>Painel Ações
O painel **Ações** é o painel direito da UI do StorSimple Snapshot Manager. Ele contém um menu de operações que podem ser executadas no nó, na exibição ou nos dados que você seleciona no painel **Escopo** ou no painel **Resultados**. O painel **Ações** contém os mesmos comandos que os menus de **Ação** que estão disponíveis para itens no painel **Escopo** e no painel **Resultados**. Para obter uma descrição de cada ação, confira a tabela na seção do menu **Ação** .

### <a name="examples"></a>Exemplos
Para ver o exemplo a seguir, no painel **Escopo**, expanda o nó **Trabalhos** e clique em **Agendado**. O painel **Ações** exibe as ações disponíveis para o nó **Agendado**.

![Exemplo de trabalhos agendados do painel Ações](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane.png) 

Para ver mais opções, no painel **Escopo**, expanda o nó **Trabalhos**, clique em **Agendado** e clique em um trabalho agendado no painel **Resultados**. O painel **Ações** exibe as ações disponíveis para o trabalho agendado, conforme mostrado no exemplo a seguir.

![Exemplo de ações de trabalho do painel Ações](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

## <a name="keyboard-navigation-and-shortcuts"></a>Navegação por teclado e atalhos
O StorSimple Snapshot Manager permite que os recursos de acessibilidade do sistema operacional Windows e do MMC (Console de Gerenciamento Microsoft). Ele também inclui alguns recursos de navegação por teclado e atalhos que são específicos ao StorSimple Snapshot Manager, conforme descrito nas seções a seguir.

* [Teclas de navegação de teclado](#keyboard-navigation-keys) 
* [Teclas de atalho da barra de menus](#menu-bar-shortcut-keys) 
* [Teclas de atalho do painel Escopo](#scope-pane-shortcut-keys) 

### <a name="keyboard-navigation-keys"></a>Teclas de navegação de teclado
A tabela a seguir descreve as teclas que você pode usar para navegar pela interface do usuário do StorSimple Snapshot Manager. 

| Tecla de navegação | Ação |
|:--- |:--- |
| Seta para baixo |Use a tecla de seta para baixo para mover verticalmente para o próximo item em um menu ou painel. |
| Digite |Pressione a tecla Enter para concluir uma ação e ir para a etapa seguinte. Por exemplo, você pode pressionar Enter para selecionar **Avançar**, **OK** ou **Criar** e ir para a etapa seguinte em um assistente. |
| Esc |Pressione a tecla Esc para fechar um menu ou para cancelar e fechar uma página. |
| F1 |Pressione a tecla F1 para exibir um tópico da ajuda para a janela ativa no momento. |
| F5 |Pressione a tecla F5 para atualizar um nó. |
| F6 |Pressione a tecla F6 para mover do painel **Escopo** para o painel **Resultados**. |
| F10 |Pressione a tecla F10 para ir para a barra de menus. |
| Tecla de seta para a esquerda |Use a tecla de seta para a esquerda para mover horizontalmente de uma opção da barra de menus para a opção anterior. Quando você move para o item anterior na barra de menus, aparece o menu de ação (ou contexto) para o item anterior. |
| Tecla de seta para a direita |Use a tecla de seta para a direita para mover horizontalmente de uma opção da barra de menus para a próxima. Quando você move para o próximo item na barra de menus, o menu de ação (ou contexto) para o novo item é exibido. |
| Tecla TAB |Use a tecla Tab para mover para o próximo painel no console ou para a próxima caixa de seleção ou texto em uma página. |
| Tecla de seta para cima |Use a tecla de seta para cima para mover verticalmente para o item anterior em um menu ou painel. |

### <a name="menu-bar-shortcut-keys"></a>Teclas de atalho da barra de menus
A tabela a seguir descreve as combinações de teclas de atalho da barra de menus. Após você pressionar as teclas de atalho e o menu abrir, você pode usar teclas de atalho do menu (as chaves sublinhadas no menu). Para obter mais informações sobre a barra de menus, acesse [Barra de menus](#menu-bar).

| Atalho | Result | Tecla de atalho do menu | Result |
|:--- |:--- |:--- |:--- |
| ALT + F |Abre o menu **Arquivo** . |N |Abre uma nova instância do console. |
|  |O |Abre a página **Ferramentas Administrativas** . | |
|  |S |Salva o console do StorSimple Snapshot Manager. | |
|  |O  |Abre a página **Salvar Como** . | |
|  |M |Abre a página **Adicionar/Remover Snap-in** . | |
|  |P |Abre a página **Opções** . | |
|  |H |Abre a Ajuda online. | |
| ALT+A |Abre o menu **Ação** . |I |Ativa e desativa a opção de exibição de importação. |
|  |W |Abre um novo console do StorSimple Snapshot Manager. | |
|  |F |Atualiza o console do StorSimple Snapshot Manager. | |
|  |L |Abre a página **Exportar Lista** . | |
|  |H |Abre a Ajuda online. | |
| ALT+V |Abre o menu **Exibição** . |O  |Abre a página **Adicionar/Remover Colunas** . |
|  |U |Abre a página **Personalizar Modo de Exibição** . | |
| ALT+O |Abre o menu **Favoritos** . |O  |Abre a página **Adicionar aos Favoritos** . |
|  |O |Abre a página **Organizar Favoritos** . | |
| ALT+W |Abre o menu **Janela** . |N |Abre outra janela do StorSimple Snapshot Manager. |
|  |C |Exibe todas as janelas de console abertas em um estilo em cascata. | |
|  |T |Exibe todas as janelas de console abertas em um padrão de grade. | |
|  |I |Organiza os ícones em uma linha horizontal na parte inferior da tela. | |
| ALT+H |Abre o menu **Ajuda** . |H |Abre a Ajuda online. |
|  |T |Abre a página da Web do Microsoft TechNet Tech Center. | |
|  |O  |Abre a página **Sobre o Console de Gerenciamento Microsoft** . | |

### <a name="scope-pane-shortcut-keys"></a>Teclas de atalho do painel Escopo
As tabelas a seguir mostram as combinações de teclas de atalho para cada nó no painel **Escopo** . 

* [Teclas de atalho do nó Dispositivos](#devices-node-shortcut-keys)
* [Teclas de atalho do nó Volumes](#volumes-node-shortcut-keys)
* [Teclas de atalho do nó Grupos de Volumes](#volume-groups-node-shortcut-keys)
* [Teclas de atalho do nó Políticas de Backup](#backup-policies-node-shortcut-keys)
* [Teclas de atalho do nó Catálogo de Backups](#backup-catalog-node-shortcut-keys)
* [Teclas de atalho do nó Trabalhos](#jobs-node-shortcut-keys)

#### <a name="devices-node-shortcut-keys"></a>Teclas de atalho do nó Dispositivos
| Atalho de menu | Result |
|:--- |:--- |
| C |Abre a página **Configurar um Dispositivo** . |
| D |Atualiza a lista de dispositivos e detalhes do dispositivo. |
| V |Abre o menu **Exibição** . |
| W |Abre um novo console do StorSimple Snapshot Manager voltado para o nó **Detalhes** . |
| F |Atualiza o console do StorSimple Snapshot Manager. |
| L |Abre a página **Exportar Lista** . |
| H |Abre a Ajuda online. |

#### <a name="volumes-node-shortcut-keys"></a>Teclas de atalho do nó Volumes
| Atalho de menu | Result |
|:--- |:--- |
| V |Atualiza a lista de volumes. |
| V (pressione duas vezes) |Abre o menu **Exibição** . |
| W |Abre um novo console do StorSimple Snapshot Manager voltado para o nó **Volumes** . |
| F |Atualiza o console do StorSimple Snapshot Manager. |
| L |Abre a página **Exportar Lista** . |
| H |Abre a Ajuda online. |

#### <a name="volume-groups-node-shortcut-keys"></a>Teclas de atalho do nó Grupos de Volumes
| Atalho de menu | Result |
|:--- |:--- |
| G |Abre a página **Criar um Grupo de Volumes** . |
| V |Abre o menu **Exibição** . |
| W |Abre um novo console do StorSimple Snapshot Manager voltado para o nó **Grupos de Volumes** . |
| F |Atualiza o console do StorSimple Snapshot Manager. |
| L |Abre a página **Exportar Lista** . |
| H |Abre a Ajuda online. |

#### <a name="backup-policies-node-shortcut-keys"></a>Teclas de atalho do nó Políticas de Backup
| Atalho de menu | Result |
|:--- |:--- |
| b |Abre a página **Criar uma Política** . |
| V |Abre o menu **Exibição** . |
| W |Abre um novo console do StorSimple Snapshot Manager voltado para o nó **Grupos de Volumes** . |
| F |Atualiza o console do StorSimple Snapshot Manager. |
| L |Abre a página **Exportar Lista**. |
| H |Abre a Ajuda online. |

#### <a name="backup-catalog-node-shortcut-keys"></a>Teclas de atalho do nó Catálogo de Backups
| Atalho de menu | Result |
|:--- |:--- |
| W |Abre um novo console do StorSimple Snapshot Manager voltado para o nó **Grupos de Volumes** . |
| F |Atualiza o console do StorSimple Snapshot Manager. |
| H |Abre a Ajuda online. |

#### <a name="jobs-node-shortcut-keys"></a>Teclas de atalho do nó Trabalhos
| Atalho de menu | Result |
|:--- |:--- |
| V |Abre o menu **Exibição** . |
| W |Abre um novo console do StorSimple Snapshot Manager voltado para o nó **Trabalhos** . |
| F |Atualiza o console do StorSimple Snapshot Manager. |
| L |Abre a página **Exportar Lista** . |
| H |Abre a Ajuda online |

## <a name="next-steps"></a>Próximas etapas
* Saiba como [usar o StorSimple Snapshot Manager para administrar sua solução do StorSimple](storsimple-snapshot-manager-admin.md).
* Saiba como [usar o StorSimple Snapshot Manager para conectar e gerenciar dispositivos](storsimple-snapshot-manager-manage-devices.md).

