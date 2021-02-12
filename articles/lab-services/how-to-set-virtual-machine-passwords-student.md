---
title: Redefinir senhas para VMs de laboratório de sala de aula no Azure Lab Services | Microsoft Docs
description: Saiba como redefinir senhas para VMs (máquinas virtuais) em laboratórios de Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1b0b13862ca4620da15606138c0a80adeac8056a
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436803"
---
# <a name="set-or-reset-password-for-virtual-machines-in-labs-students"></a>Definir ou redefinir a senha para máquinas virtuais em laboratórios (alunos)
Este artigo mostra como os alunos podem definir/redefinir a senha para suas VMs. 

## <a name="enable-resetting-of-passwords"></a>Habilitar a redefinição de senhas
No momento da criação do laboratório, o proprietário do laboratório pode habilitar ou desabilitar o **uso da mesma senha para todas as máquinas virtuais**. Se essa opção tiver sido habilitada, os alunos não poderão redefinir a senha. Todas as VMs nos laboratórios terão a mesma senha definida pelo instrutor. 

Se essa opção estiver desabilitada, os usuários precisarão definir uma senha ao tentar se conectar à VM pela primeira vez. Os alunos também podem redefinir a senha posteriormente a qualquer momento, conforme mostrado na última seção deste artigo. 

## <a name="reset-password-for-the-first-time"></a>Redefinir a senha pela primeira vez
Se a opção **usar a mesma senha para todas as máquinas virtuais** estiver desabilitada, quando os usuários (alunos) selecionarem o botão **conectar** no bloco do laboratório na página **minhas máquinas virtuais** , o usuário verá a caixa de diálogo a seguir para definir a senha da VM: 

![Redefinir a senha para o aluno](./media/how-to-set-virtual-machine-passwords/student-set-password.png)

## <a name="reset-password-later"></a>Redefinir senha posteriormente
O aluno também pode definir a senha clicando no menu de estouro (**três pontos verticais**) no bloco do laboratório e selecionando **Redefinir senha**. 

![Redefinir senha posteriormente](./media/how-to-set-virtual-machine-passwords/student-set-password-2.png)


## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre outras opções de uso de alunos que um proprietário de laboratório pode configurar, consulte o seguinte artigo: [Configurar o uso de alunos](how-to-configure-student-usage.md).
