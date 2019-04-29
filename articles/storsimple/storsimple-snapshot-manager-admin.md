---
title: Administração do StorSimple Snapshot Manager | Microsoft Docs
description: Oferece uma visão geral e links para mais informações sobre tarefas e fluxos de trabalho da solução StorSimple Snapshot Manager.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 1cdbb61d-bd16-4be4-ade2-ceab11508acb
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2016
ms.author: v-sharos
ms.openlocfilehash: bc72da98800ef85ef14be0882ba856fbf01386b9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60630012"
---
# <a name="use-storsimple-snapshot-manager-to-administer-your-storsimple-solution"></a>Use o StorSimple Snapshot Manager para administrar sua solução de StorSimple

## <a name="overview"></a>Visão geral
O StorSimple Snapshot Manager é um snap-in do Microsoft Management Console (MMC) que simplifica a proteção de dados e o gerenciamento de backup em um ambiente Microsoft Azure StorSimple. Com o StorSimple Snapshot Manager, você pode gerenciar dados do Microsoft Azure StorSimple no data center e na nuvem como uma solução única de armazenamento integrado, simplificando os processos de backup e reduzindo os custos.

O console de gerenciamento central do StorSimple Snapshot Manager permite criar cópias de backup consistentes e pontuais de local e dados na nuvem. Por exemplo, você pode usar o console para:

* Configurar, fazer backup e excluir volumes.
* Configurar grupos de volumes para garantir que o backup dos dados seja consistente com o aplicativo.
* Gerencie políticas de backup para que os dados sejam copiados em um agendamento predeterminado.
* Crie cópias independentes dos dados, que podem ser armazenados na nuvem e usados para a recuperação de desastres.

Este artigo fornece links para tutoriais que descrevem o StorSimple Snapshot Manager e como usá-lo para concluir a fluxos de trabalho e tarefas de administração do sistema.

* Para obter mais informações sobre os componentes e arquitetura do StorSimple Snapshot Manager, consulte [O que é o StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) 
* Para baixar o StorSimple Snapshot Manager, acesse [a página de download do StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).
* Para ver procedimentos de implantação do StorSimple Snapshot Manager, vá para [Implantar o StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).

> [!NOTE]
> Você não pode usar o StorSimple Snapshot Manager para gerenciar as Matrizes Virtuais do Microsoft Azure StorSimple (também conhecido como dispositivos virtuais locais StorSimple).


## <a name="storsimple-snapshot-manager-tasks-and-workflows"></a>Fluxos de trabalho e tarefas do StorSimple Snapshot Manager
Você pode usar o StorSimple Snapshot Manager para monitorar e gerenciar trabalhos de backup atuais, agendados e concluídos. Além disso, o StorSimple Snapshot Manager fornece um catálogo de até 64 backups concluídos. Você pode usar o catálogo para localizar e restaurar volumes ou arquivos individuais. 

| SE VOCÊ QUISER FAZER ISSO... | USE ESTE TUTORIAL... |
|:--- |:--- |
| Saiba mais sobre o StorSimple Snapshot Manager |[O que é o StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) |
| Instalar o StorSimple Snapshot Manager<br>Reinstalar o StorSimple Snapshot Manager<br>Remover o StorSimple Snapshot Manager |[Implantar o StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md) |
| Use os menus e recursos do StorSimple Snapshot Manager:<ul><li>Barra de menus</li><li>Barra de ferramentas</li><li>Painel Escopo</li><li>Painel Resultados</li><li>Painel Ações</li><li>Navegação por teclado e atalhos</li></ul> |[Interface do usuário do StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md) |
| Use os recursos comuns do MMC incluídos no StorSimple Snapshot Manager:<ul><li>Visualizar</li><li>Nova Janela a Partir Daqui</li><li>Atualizar</li><li>Exportar Lista</li><li>Ajuda</li></ul> |[Usar as ações de menu do MMC no StorSimple Snapshot Manager](storsimple-snapshot-manager-mmc-menu.md) |
| Adicionar ou substituir um dispositivo<br>Conectar um dispositivo<br>Verificar grupos de volume importados<br>Atualizar os dispositivos conectados<br>Autenticar um dispositivo<br>Exibir detalhes do dispositivo<br>Excluir uma configuração de dispositivo<br>Alterar uma senha de dispositivo<br>Substituir um dispositivo com falha<br> |[Usar o StorSimple Snapshot Manager para conectar e gerenciar dispositivos StorSimple](storsimple-snapshot-manager-manage-devices.md) |
| Montar volumes<br>Exibir informações sobre volumes<br>Excluir um volume<br>Examinar volumes novamente<br>Configurar e fazer backup de um volume básico<br>Configurar e fazer backup de um volume espelhado dinâmico |[Usar o StorSimple Snapshot Manager para exibir e gerenciar volumes](storsimple-snapshot-manager-manage-volumes.md) |
| Exibir grupos de volumes<br>Criar um grupo de volumes<br>Fazer backup de um grupo de volumes<br>Editar um grupo de volumes<br>Excluir um grupo de volumes |[Usar o StorSimple Snapshot Manager para criar e gerenciar grupos de volumes](storsimple-snapshot-manager-manage-volume-groups.md) |
| Criar uma política de backup <br>Editar uma política de backup<br>Excluir uma política de backup |[Usar o StorSimple Snapshot Manager para criar e gerenciar políticas de backup](storsimple-snapshot-manager-manage-backup-policies.md) |
| Exibir e gerenciar trabalhos de backup agendados<br>Exibir e gerenciar trabalhos de backup recentes<br>Exibir e gerenciar trabalhos de backup em execução |[Usar o StorSimple Snapshot Manager para exibir e gerenciar trabalhos de backup](storsimple-snapshot-manager-manage-backup-jobs.md) |
| Restaurar um volume<br>Clonar um volume ou grupo de volumes<br>Excluir um conjunto de backups<br>Recuperar um arquivo<br>Restaurar o banco de dados do StorSimple Snapshot Manager |[Usar o StorSimple Snapshot Manager para gerenciar o catálogo de backup](storsimple-snapshot-manager-manage-backup-catalog.md) |

## <a name="next-steps"></a>Próximas etapas
[Baixar o StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).

