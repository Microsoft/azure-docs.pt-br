---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 05/09/2018
ms.date: 06/04/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 9c4910d9ec1fdf651b1bc29955a4e1de2a775f7a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60576147"
---
1. Clique no botão **Conectar** na página de propriedades da máquina virtual. 
2. Na página **Conectar à máquina virtual**, mantenha selecionadas as opções apropriadas e clique em **Fazer o download do arquivo RDP**.
2. Abra o arquivo RDP baixado e clique em **Conectar** quando solicitado. 
2. Você receberá um aviso de que o arquivo `.rdp` é proveniente de um editor desconhecido. Isso é normal. Na janela de Área de Trabalho Remota, clique em **Conectar** para continuar.

    ![Captura de tela de um aviso sobre um editor desconhecido.](./media/virtual-machines-log-on-win-server/rdp-warn.png)
3. Na janela **Segurança do Windows**, selecione **Mais opções** e **Usar uma conta diferente**. Digite as credenciais de uma conta na máquina virtual e clique em **OK**.

     **Conta local** - normalmente, são o nome de usuário e a senha da conta local especificados por você no momento da criação da máquina virtual. Nesse caso, o domínio é o nome da máquina virtual e é inserido como *nomedavm*&#92;*nome de usuário*.  

    **VM Ingressada no Domínio** – se a VM pertencer a um domínio, digite o nome de usuário no formato *Domínio*&#92;*Nome de usuário*. A conta precisa estar no grupo Administradores ou ter privilégios de acesso remoto concedidos à VM.

    **Controlador de domínio** - se a VM for um controlador de domínio, digite o nome de usuário e a senha da conta de administrador para esse domínio.
4. Clique em **Sim** para verificar a identidade da máquina virtual e concluir o logon.

   ![Captura de tela mostrando uma mensagem sobre como verificar a identidade da VM.](./media/virtual-machines-log-on-win-server/cert-warning.png)
