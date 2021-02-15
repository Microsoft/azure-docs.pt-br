---
title: 'Copiar e colar de e para uma máquina virtual: bastiões do Azure'
description: Neste artigo, saiba como copiar e colar de e para uma VM do Azure usando a bastiões.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cherylmc
ms.openlocfilehash: 50f7906992aa19daa205a30f71ce21456bafe558
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92079149"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion"></a>Copiar e colar em uma máquina virtual: bastiões do Azure

Este artigo ajuda você a copiar e colar texto de e para máquinas virtuais ao usar a bastiões do Azure. Antes de trabalhar com uma VM, certifique-se de ter seguido as etapas para [criar um host de bastiões](./tutorial-create-host-portal.md). Em seguida, conecte-se à VM com a qual você deseja trabalhar usando o [RDP](bastion-connect-vm-rdp.md) ou [SSH](bastion-connect-vm-ssh.md).

Para navegadores que dão suporte ao acesso à API de área de transferência avançada, você pode copiar e colar o texto entre o dispositivo local e a sessão remota da mesma maneira que copia e cola entre aplicativos em seu dispositivo local. Para outros navegadores, você pode usar a paleta de ferramentas de acesso à área de transferência de bastiões.

>[!NOTE]
>No momento, só há suporte para copiar/colar texto.
>

   ![Permitir área de transferência](./media/bastion-vm-manage/allow.png)

Só há suporte para copiar/colar texto. Para copiar e colar direta, seu navegador pode solicitar acesso à área de transferência quando a sessão de bastiões está sendo inicializada. **Permitir que** a página da Web acesse a área de transferência.

## <a name="copy-to-a-remote-session"></a><a name="to"></a>Copiar para uma sessão remota

Depois de se conectar à máquina virtual usando o [portal do Azure ](https://portal.azure.com), conclua as seguintes etapas:

1. Copie o texto/conteúdo do dispositivo local para a área de transferência local.
1. Durante a sessão remota, inicie a paleta de ferramentas de acesso à área de transferência de bastiões selecionando as duas setas. As setas estão localizadas no centro esquerdo da sessão.

   ![Captura de tela que mostra as setas de inicialização da paleta de ferramentas realçadas no lado esquerdo da janela.](./media/bastion-vm-manage/left.png)

   ![Captura de tela mostra uma área de transferência para texto copiado em bastiões.](./media/bastion-vm-manage/clipboard.png)
1. Normalmente, o texto copiado é mostrado automaticamente na paleta de colagem de cópia de bastiões. Se o texto não estiver lá, Cole o texto na área de texto na paleta.
1. Quando o texto estiver na área de texto, você poderá colá-lo na sessão remota.

   ![Captura de tela que mostra o botão Copiar/colar realçado e uma cadeia de texto de exemplo copiada na sessão remota.](./media/bastion-vm-manage/local.png)

## <a name="copy-from-a-remote-session"></a><a name="from"></a>Copiar de uma sessão remota

Depois de se conectar à máquina virtual usando o [portal do Azure ](https://portal.azure.com), conclua as seguintes etapas:

1. Copie o texto/conteúdo da sessão remota para a área de transferência remota (usando Ctrl-C).

   ![paleta de ferramentas](./media/bastion-vm-manage/remote.png)
1. Durante a sessão remota, inicie a paleta de ferramentas de acesso à área de transferência de bastiões selecionando as duas setas. As setas estão localizadas no centro esquerdo da sessão.

   ![área de transferência](./media/bastion-vm-manage/clipboard2.png)
1. Normalmente, o texto copiado é mostrado automaticamente na paleta de colagem de cópia de bastiões. Se o texto não estiver lá, Cole o texto na área de texto na paleta.
1. Quando o texto estiver na área de texto, você poderá colá-lo no dispositivo local.

   ![colar](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>Próximas etapas

Leia as [perguntas frequentes do Bastion](bastion-faq.md).
