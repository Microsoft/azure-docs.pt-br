---
title: 'Copiar e colar para e de uma máquina virtual: Bastiões do Azure | Microsoft Docs'
description: Neste artigo, saiba como copiar e colar para e de uma VM do Azure usando bastião.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 9d69d1a9fae258c9546a8c794fc0b0c68b952049
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191805"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion-preview"></a>Copie e cole a uma máquina virtual: Bastiões do Azure (visualização)

Este artigo ajuda você a copiar e colar o texto de e para máquinas virtuais ao usar o Azure bastião. Antes de trabalhar com uma VM, verifique se você tiver seguido as etapas a serem [criar um host bastião](bastion-create-host-portal.md). Em seguida, conecte-se à VM que você deseja trabalhar usando o [RDP](bastion-connect-vm-rdp.md) ou [SSH](bastion-connect-vm-ssh.md).

> [!IMPORTANT]
> Essa versão prévia pública é fornecida sem um SLA e não deve ser usada para cargas de trabalho de produção. Determinados recursos podem não ter suporte, podem ter restrição ou podem não estar disponíveis em todos os locais do Azure. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.
>

Para navegadores que oferecem suporte o acesso avançado da API de área de transferência, você pode copiar e colar o texto entre o dispositivo local e a sessão remota da mesma forma que você copiar e colar entre aplicativos em seus dispositivos locais. Para outros navegadores, você pode usar a paleta da ferramenta acesso bastiões da área de transferência.

  ![Permitir que a área de transferência](./media/bastion-vm-manage/allow.png)

Há suporte para apenas texto copiar/colar. Para cópia direta e colar, seu navegador pode solicitar acesso de área de transferência quando a sessão de bastiões está sendo inicializada. **Permitir** a página da web para acessar a área de transferência.

## <a name="to"></a>Copiar para uma sessão remota

Depois de se conectar à máquina virtual usando o [portal do Azure](https://aka.ms/BastionHost) para a visualização de bastiões:

1. Copie o conteúdo de texto/do dispositivo local na área de transferência local.
1. Durante a sessão remota, inicie a paleta da ferramenta de acesso da área de transferência bastiões selecionando as duas setas. As setas estão localizadas na parte esquerda central da sessão.

    ![paleta da ferramenta](./media/bastion-vm-manage/left.png)

    ![Área de transferência](./media/bastion-vm-manage/clipboard.png)

1. Normalmente, o texto copiado mostra automaticamente na paleta de colar cópia de bastiões. Se o texto não estiver lá, em seguida, cole o texto na área de texto na paleta.
1. Depois que o texto está na área de texto, você pode colá-lo para a sessão remota.

    ![Colar](./media/bastion-vm-manage/local.png)

## <a name="from"></a>Copiar de uma sessão remota

Depois de se conectar à máquina virtual usando o [portal do Azure](https://aka.ms/BastionHost) para a visualização de bastiões:

1. Copie o conteúdo de texto/da sessão remota na área de transferência remota (usando Ctrl-C).

    ![paleta da ferramenta](./media/bastion-vm-manage/remote.png)

1. Durante a sessão remota, inicie a paleta da ferramenta de acesso da área de transferência bastiões selecionando as duas setas. As setas estão localizadas na parte esquerda central da sessão.

    ![Área de transferência](./media/bastion-vm-manage/clipboard2.png)

1. Normalmente, o texto copiado mostra automaticamente na paleta de colar cópia de bastiões. Se o texto não estiver lá, em seguida, cole o texto na área de texto na paleta.
1. Depois que o texto está na área de texto, você pode colá-lo para o dispositivo local.

    ![Colar](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>Próximas etapas

Leia as [perguntas Frequentes de bastiões](bastion-faq.md).