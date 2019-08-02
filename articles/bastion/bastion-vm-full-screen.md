---
title: Alterar o modo de exibição de sessão de máquina virtual para a tela inteira no Azure bastiões | Microsoft Docs
description: Neste artigo, saiba como alterar o modo de exibição para tela inteira.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 6a063d6c8891133126924bfb934770f7818db71a
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191545"
---
# <a name="change-to-full-screen-view-for-a-vm-session-azure-bastion-preview"></a>Altere para o modo de tela inteira de uma sessão de vm: Bastiões do Azure (visualização)

Este artigo ajuda você a alterar o modo de exibição de máquina virtual para tela inteira e voltar no seu navegador. Antes de trabalhar com uma VM, verifique se você tiver seguido as etapas a serem [criar um host bastião](bastion-create-host-portal.md). Em seguida, conecte-se à VM que você deseja trabalhar usando o [RDP](bastion-connect-vm-rdp.md) ou [SSH](bastion-connect-vm-ssh.md).

> [!IMPORTANT]
> Essa versão prévia pública é fornecida sem um SLA e não deve ser usada para cargas de trabalho de produção. Determinados recursos podem não ter suporte, podem ter restrição ou podem não estar disponíveis em todos os locais do Azure. Veja os [Termos de Uso Adicionais para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter detalhes.
>

## <a name="launch-the-clipboard-tool"></a>Inicie a ferramenta da área de transferência

Durante a sessão remota, inicie a paleta da ferramenta de acesso da área de transferência bastiões selecionando as duas setas, localizada na parte esquerda central da sessão.

![ferramentas](./media/bastion-vm-manage/left.png)

## <a name="select-full-screen"></a>Selecione a tela inteira

Selecione o **Fullscreen** botão para alternar a sessão para uma experiência de tela inteira. Depois de mudar, a sessão será reinicializado para tela inteira.

![tela inteira](./media/bastion-vm-manage/full-screen.png)
 
## <a name="next-steps"></a>Próximas etapas

Leia as [perguntas Frequentes de bastiões](bastion-faq.md).
Saiba como [copiar e colar](bastion-vm-copy-paste.md) de e para uma VM do Azure.