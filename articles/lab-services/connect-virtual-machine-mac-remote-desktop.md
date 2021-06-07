---
title: Como se conectar a uma VM dos Azure Lab Services no Mac | Microsoft Docs
description: Saiba como se conectar a partir de um Mac a uma máquina virtual nos Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 674bea13093e185ae991148c2cf96599a273d0bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85444617"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-mac"></a>Conecte-se a uma VM usando o Protocolo de Área de Trabalho Remota em um Mac
Esta seção mostra como um aluno pode se conectar a uma VM de laboratório de sala de aula a partir de um Mac usando a RDP.

## <a name="install-microsoft-remote-desktop-on-a-mac"></a>Instalar a Área de Trabalho Remota da Microsoft em um Mac
1. Abra a App Store em seu Mac e procure pela **Área de Trabalho Remota da Microsoft**.

    ![Área de Trabalho Remota da Microsoft](./media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. Instale a versão mais recente da Área de Trabalho Remota da Microsoft. 

## <a name="access-the-vm-from-your-mac-using-rdp"></a>Acessar a VM de seu Mac usando RDP
1. Abra o arquivo **RDP** que é baixado em seu computador com a **Área de Trabalho Remota da Microsoft** instalada. Ele deve começar a se conectar à VM. 

    ![Conectar-se a uma VM](./media/how-to-use-classroom-lab/connect-linux-vm.png)
1. Selecione **Continuar** se você receber o aviso a seguir. 

    ![Aviso de certificado](./media/how-to-use-classroom-lab/certificate-error.png)
1. Você deverá ver a VM. 

    > [!NOTE]
    > O exemplo a seguir é referente a uma VM CentOS do Linux. 

    ![VM](./media/how-to-use-classroom-lab/vm-ui.png)


## <a name="next-steps"></a>Próximas etapas
Para saber como se conectar a VMs do Linux usando RDP, consulte [Usar área de trabalho remota para máquinas virtuais do Linux](how-to-use-remote-desktop-linux-student.md)


