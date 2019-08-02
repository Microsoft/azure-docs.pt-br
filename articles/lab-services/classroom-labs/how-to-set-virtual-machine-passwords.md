---
title: Definir senhas para as VMs no Azure Lab Services | Microsoft Docs
description: Saiba como definir e redefinir senhas para máquinas virtuais (VMs) em laboratórios de sala de aula do Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: c1564fadef35a20d0d87db8439ae1cc3dc923318
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144093"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs"></a>Defina ou redefina a senha para máquinas virtuais nos laboratórios de sala de aula
Este artigo fornece maneiras diferentes de senhas de configuração e resettings para acessar as VMs nos laboratórios de sala de aula. 

## <a name="lab-owners-teachers"></a>Proprietários de laboratório (professores)
Um proprietário de laboratório (professor) pode definir/redefinir a senha para VMs no momento da criação do laboratório (Assistente de criação do laboratório) ou depois de criar o laboratório (no painel). 

### <a name="set-password-at-the-time-of-lab-creation"></a>Definir a senha no momento da criação de laboratório
Um proprietário de laboratório (professor) pode definir uma senha para VMs no laboratório na **definir credenciais** página do Assistente de criação de laboratório.

![Definir as credenciais](../media/tutorial-setup-classroom-lab/set-credentials.png)

Ao habilitar/desabilitar o **usar a mesma senha para todas as máquinas virtuais** opção nesta página, um professor pode optar por usar a mesma senha para todas as VMs no laboratório ou permitir que os alunos definir as senhas para suas VMs. Por padrão, essa configuração é habilitada para imagens de sistema operacional de todos os Windows e Linux, exceto o Ubuntu. Quando essa configuração estiver desabilitada, os alunos serão solicitados a definir uma senha quando tentam se conectar à VM pela primeira vez. 

O proprietário de laboratório pode redefinir a senha (se necessário) sobre o **configurar modelo** página do Assistente de criação de laboratório. 

![Página de configuração do modelo após a conclusão](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)

O proprietário de laboratório também pode redefinir a senha depois que o laboratório for criado, no painel. 

### <a name="reset-password-on-the-dashboard"></a>Redefinir a senha no painel

1. Selecione o menu de estouro (três pontos verticais) no bloco do laboratório e selecione **redefinição de senha**. 

    ![Redefinição de senha menu na home page](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)
1. Sobre o **definir senha** caixa de diálogo, insira uma senha e selecione **definir senha**.
    
    ![Caixa de diálogo Definir senha](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="lab-users-students"></a>Usuários do laboratório (estudantes)
No momento da criação do laboratório, o proprietário de laboratório pode habilitar ou desabilitar a **usar a mesma senha para todas as máquinas virtuais**. Se essa opção estiver habilitada, os alunos não podem redefinir a senha. Todas as VMs nos laboratórios terá a mesma senha que é definida com o professor. 

Se essa opção estiver desabilitada, os usuários precisarão definir uma senha ao tentar se conectar à VM pela primeira vez. Quando os usuários (estudantes) selecionam os **Connect** botão no bloco do laboratório a **minhas máquinas virtuais** página, o usuário vê a seguinte caixa de diálogo para definir a senha para a VM: 

![Redefinir a senha do aluno](../media/how-to-set-virtual-machine-passwords/student-set-password.png)

Aluno também pode definir a senha clicando no menu de estouro (**três pontos verticais**) no bloco do laboratório e selecionando **Redefinir senha**. 

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre outras opções de uso de alunos você (como um proprietário de laboratório) podem configurar, consulte o seguinte artigo: [Configurar o uso do aluno](how-to-configure-student-usage.md).
