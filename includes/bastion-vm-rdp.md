---
title: incluir arquivo
description: incluir arquivo
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/21/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 083ab61d5a20bfb8e38747ae0694b1176c0a0fd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92521519"
---
1. Abra o [Portal do Azure](https://portal.azure.com). Navegue até a máquina virtual à qual você deseja se conectar e selecione **Conectar**. Selecione **Bastion** no menu suspenso.

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm.png" alt-text="Selecionar Bastion":::

1. Depois de selecionar o Bastion no menu suspenso, uma barra lateral será exibida com três guias: RDP, SSH e Bastion. Como o Bastion foi provisionado para a rede virtual, a guia Bastion está ativa por padrão. Selecione **Usar Bastion**.

   :::image type="content" source="./media/bastion-vm-rdp/select-use-bastion.png" alt-text="Selecione Usar o Bastion":::

1. Na página **Conectar-se usando o Azure Bastion**, insira o nome de usuário e a senha para sua máquina virtual e selecione **Conectar**.

   :::image type="content" source="./media/bastion-vm-rdp/connect-vm-host.png" alt-text="Connect":::

1. A conexão RDP com essa máquina virtual via Bastion será aberta diretamente no portal do Azure (via HTML5) usando a porta 443 e o serviço Bastion.

   :::image type="content" source="./media/bastion-vm-rdp/connection.png" alt-text="Conectar usando a porta 443":::
