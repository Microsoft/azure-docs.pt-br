---
title: Escalonar privilégios de nuvem privada – solução CloudSimple do VMware do Azure
description: Descreve como escalonar privilégios em sua nuvem privada para funções administrativas no vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d11c88b91b13cca13120a9203e376fdc2c3d6d8d
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332803"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>Escalonar privilégios do vCenter de nuvem privada a partir do portal CloudSimple 

Para obter acesso administrativo ao vCenter sua nuvem privada, você pode escalar temporariamente seus privilégios CloudSimple.  Usando privilégios elevados, você pode instalar as soluções de VMware, adicionar fontes de identidade e gerenciar usuários.

Novos usuários podem ser criados no domínio do SSO do vCenter e receber acesso ao vCenter.  Quando você cria novos usuários, adicioná-los para os grupos de builtin CloudSimple para acessar o vCenter.  Para obter mais informações, consulte [modelo de permissão de nuvem privada CloudSimple do VMware vCenter](https://docs.azure.cloudsimple.com/learn-private-cloud-permissions/).

> [!CAUTION]
> Não faça nenhuma alteração de configuração para os componentes de gerenciamento. As ações executadas durante o estado com privilégios escalonado podem afetar negativamente o seu sistema ou podem fazer com que o sistema fique indisponível.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="escalate-privileges"></a>Escalonar privilégios

1. Acesso a [CloudSimple portal](access-cloudsimple-portal.md).

2. Abra o **recursos** , selecione a nuvem privada para o qual você deseja escalonar privilégios.

3. Próximo à parte inferior da página de resumo sob **alterar privilégios vSphere**, clique em **escalonar**.

    ![Alterar o privilégio de vSphere](media/escalate-private-cloud-privilege.png)

4. Selecione o tipo de usuário do vSphere.  Somente **CloudOwner@cloudsimple.local** usuário local pode ser escalonado.

5. Selecione o intervalo de tempo de escalonar na lista suspensa. Escolha o período mais curto que permitirá que você conclua a tarefa.

6. Marque a caixa de seleção para confirmar que você compreende os riscos.

    ![Escalonar a caixa de diálogo de privilégio](media/escalate-private-cloud-privilege-dialog.png)

7. Clique em **OK**.

8. O processo de escalonamento de bloqueios pode levar alguns minutos. Ao concluir, clique em **OK**.

O escalonamento de privilégio começa e dura até o término do intervalo selecionado.  Você pode entrar no seu vCenter de nuvem privada para realizar tarefas administrativas.

> [!IMPORTANT]
> Apenas um usuário pode ter escalonou privilégios.  Você desprovisionar deve escalonar os privilégios do usuário antes de você pode escalonar privilégios de outro usuário.

## <a name="extend-privilege-escalation"></a>Estender a elevação de privilégios

Se você precisar de mais tempo para concluir suas tarefas, você pode estender o período de escalonamento de privilégio.  Escolha que o adicionais escalonar o intervalo de tempo que permite que você conclua as tarefas administrativas.

1. Sobre o **recursos** > **nuvens privadas** no portal do CloudSimple, selecione a nuvem privada para o qual você deseja estender o escalonamento de privilégio.

2. Na parte inferior da guia Resumo, clique em **estender a elevação de privilégios**.

    ![Estender a elevação de privilégios](media/de-escalate-private-cloud-privilege.png)

3. Selecione um intervalo de tempo de escalonar na lista suspensa. Examine a nova hora de término de escalonamento de bloqueios.

4. Clique em **salvar** para estender o intervalo.

## <a name="de-escalate-privileges"></a>Desprovisionar escalonar privilégios

Depois que as tarefas administrativas forem concluídas, você deve escalar desprovisionar seus privilégios.  

1. Sobre o **recursos** > **nuvens privadas** no portal do CloudSimple, selecione a nuvem privada para o qual você deseja desprovisionar escalonar privilégios.

2. Clique em **desprovisionar escalonar**.

3. Clique em **OK**.

> [!IMPORTANT]
> Para evitar erros, saia do vCenter e entrar novamente depois de desprovisionar escalonar privilégios.

## <a name="next-steps"></a>Próximas etapas

* [Configurar fontes de identidade do vCenter para usar o Active Directory](https://docs.azure.cloudsimple.com/set-vcenter-identity/)
* Instalar a solução de backup para [as máquinas virtuais de carga de trabalho de backup](https://docs.azure.cloudsimple.com/backup-workloads-veeam/)