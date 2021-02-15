---
title: Como se conectar a uma VM dos Azure Lab Services no Chromebook | Microsoft Docs
description: Saiba como se conectar a partir de um Chromebook a uma máquina virtual nos Azure Lab Services.
services: devtest-lab, lab-services, virtual-machines
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 74135c0b36f533ebfbba6422bc79af47825a1a3b
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/30/2020
ms.locfileid: "97813217"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-chromebook"></a>Conecte-se a uma VM usando o Protocolo de Área de Trabalho Remota em um Chromebook

Esta seção mostra como um aluno pode se conectar a uma VM de laboratório de sala de aula a partir de um Chromebook usando a RDP.

## <a name="install-microsoft-remote-desktop-on-a-chromebook"></a>Instalar a Área de Trabalho Remota da Microsoft em um Chromebook

1. Abra a App Store em seu Chromebook e procure pela **Área de Trabalho Remota da Microsoft**.

    ![Área de Trabalho Remota da Microsoft](./media/how-to-use-classroom-lab/install-ms-remote-desktop-chromebook.png)
    
1. Instale a versão mais recente da Área de Trabalho Remota da Microsoft. 

## <a name="access-the-vm-from-your-chromebook-using-rdp"></a>Acessar a VM a partir do seu Chromebook usando a RDP

1. Abra o arquivo **RDP** que é baixado em seu computador com a **Área de Trabalho Remota da Microsoft** instalada. Ele deve começar a se conectar à VM. 

    ![Conectar-se a uma VM](./media/how-to-use-classroom-lab/connect-vm-chromebook.png)

1. Quando solicitado, insira sua senha.

    ![Captura de tela que mostra as telas de logon onde você insere seu nome de usuário e senha.](./media/how-to-use-classroom-lab/password-chromebook.png)

1. Selecione **Continuar** se você receber o aviso a seguir. 

    ![Aviso de certificado](./media/how-to-use-classroom-lab/certificate-error-chromebook.png)

1. Você deve ver a área de trabalho da VM à qual você está se conectando.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como se conectar às VMs do Linux, consulte [Conectar-se às máquinas virtuais do Linux](how-to-use-remote-desktop-linux-student.md)

