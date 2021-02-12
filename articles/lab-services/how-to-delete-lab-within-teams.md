---
title: Excluir um laboratório de Azure Lab Services das equipes
description: Saiba como excluir um laboratório de Azure Lab Services das equipes.
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: 8d1e20f8f676eb9863187b550a3c0400871d670c
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433947"
---
# <a name="delete-labs-within-teams"></a>Excluir laboratórios dentro de equipes

Este artigo mostra como excluir um laboratório do aplicativo **Azure Lab Services** .

## <a name="prerequisites"></a>Pré-requisitos

* [Crie uma conta dos serviços de laboratório](tutorial-setup-lab-account.md#create-a-lab-account) no portal do Azure.
* Comece [e crie um laboratório de serviços de laboratório dentro das equipes](how-to-get-started-create-lab-within-teams.md).

## <a name="delete-labs"></a>Excluir laboratórios

Um laboratório criado em equipes pode ser excluído no [site do Lab Services](https://labs.azure.com) , excluindo o laboratório diretamente, conforme descrito em [gerenciar laboratórios no Azure Lab Services](how-to-manage-classroom-labs.md). 

A exclusão de laboratório também é disparada quando a equipe é excluída. Se a equipe na qual o laboratório foi criado for excluída, o laboratório será excluído automaticamente 24 horas depois que a sincronização da lista de usuários automática for disparada. 

> [!IMPORTANT]
> A exclusão da guia ou a desinstalação do aplicativo não resultará na exclusão do laboratório. 

Se a guia for excluída, os usuários na lista de membros da equipe ainda poderão acessar as VMs no [site do Lab Services](https://labs.azure.com) , a menos que a exclusão do laboratório seja disparada explicitamente excluindo o laboratório no site ou excluindo a equipe. 

## <a name="next-steps"></a>Próximas etapas

- [Visão geral do uso de Azure Lab Services nas equipes](lab-services-within-teams-overview.md)
- [Gerenciar listas de usuários do laboratório dentro das equipes](how-to-manage-user-lists-within-teams.md)
- [Gerenciar o pool de VMs do laboratório dentro das equipes](how-to-manage-vm-pool-within-teams.md)
- [Criar e gerenciar agendas de laboratório dentro das equipes](how-to-create-schedules-within-teams.md)
- [Acessar uma VM nas equipes – exibição de aluno](how-to-access-vm-for-students-within-teams.md)

