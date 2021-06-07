---
title: Adicionar destinatários no compartilhamento de dados do Azure
description: Saiba como adicionar destinatários a um compartilhamento de dados existente no compartilhamento de dados do Azure.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/17/2020
ms.openlocfilehash: a8e3dac620873ab11ae24395310066037f6d2df4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97680635"
---
# <a name="how-to-add-a-recipient-to-your-share"></a>Como adicionar um destinatário ao seu compartilhamento

Você pode adicionar o destinatário ao criar um novo compartilhamento ou a um compartilhamento existente. Na interface do usuário do compartilhamento de dados do Azure, você pode adicionar o destinatário usando o email de logon do Azure do usuário.  Na API, você pode usar uma combinação de entidade de usuário/serviço e ID de locatário. Quando uma ID de locatário é especificada, o convite só pode ser aceito nesse locatário. Além disso, da API, você pode criar um convite sem enviar um email para o destinatário. 

## <a name="add-recipient-to-an-existing-share"></a>Adicionar um destinatário a um compartilhamento existente

No compartilhamento de dados do Azure, navegue até o compartilhamento enviado e selecione a guia **convites** . Aqui estão listados todos os destinatários de convites para esse compartilhamento de dados. Para adicionar um novo, clique em **Adicionar destinatário**.

![A captura de tela mostra adicionar destinatário selecionado.](./media/how-to/how-to-add-recipients/add-recipient.png)

Um painel será exibido no lado direito da página. Clique em **Adicionar destinatário** e preencha o email do novo destinatário na linha em branco. Certifique-se de usar o email de logon do Azure do destinatário (usar seu alias de email não funcionará). 

![Captura de tela mostra o painel Adicionar destinatário, no qual você pode adicionar e enviar um convite.](./media/how-to/how-to-add-recipients/add-recipient-side.png)

Clique em **Adicionar e enviar convite**. Os novos destinatários serão enviados emails de convite para esse compartilhamento.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como [excluir um convite para um compartilhamento](how-to-delete-invitation.md).
