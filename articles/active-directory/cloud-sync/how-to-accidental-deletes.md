---
title: Azure AD Connect exclusões acidentais de sincronização de nuvem
description: Este tópico descreve como usar o recurso de exclusão acidental para evitar exclusões.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/25/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0da54bd28c1d9ea933e88b6c86cf6092c10d036a
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98785152"
---
# <a name="accidental-delete-prevention"></a>Prevenção acidental de exclusão

O documento a seguir descreve o recurso de exclusão acidental para Azure AD Connect a sincronização de nuvem.  O recurso de exclusão acidental foi projetado para protegê-lo contra alterações de configuração acidentais e alterações em seu diretório local que afetariam muitos usuários e grupos.  Esse recurso permite que você:

- Configure a capacidade de impedir exclusões acidentais automaticamente. 
- Definir o número de objetos (limite) Além do qual a configuração entrará em vigor 
- Configure um endereço de email de notificação para que eles possam receber uma notificação por email depois que o trabalho de sincronização em questão for colocado em quarentena para esse cenário 

Para usar esse recurso, defina o limite para o número de objetos que, se forem excluídos, a sincronização deverá parar.  Portanto, se esse número for atingido, a sincronização será interrompida e uma notificação será enviada para o email especificado.  Essa notificação permitirá que você investigue o que está acontecendo.


## <a name="configure-accidental-delete-prevention"></a>Configurar prevenção acidental de exclusão
Para usar o novo recurso, siga as etapas abaixo.


1.  No portal do Azure, selecione **Azure Active Directory**.
2.  Selecione **Azure AD Connect**.
3.  Selecione **gerenciar sincronização de nuvem**.
4. Em **configuração**, selecione sua configuração.
5. Em **configurações** , preencha o seguinte:
    - **Email de notificação** -email usado para notificações
    - **Impedir exclusões acidentais** -Marque esta caixa para habilitar o recurso
    - **Limite de exclusão acidental** – Insira o número de objetos para interromper a sincronização e enviar uma notificação

![Exclusões acidentais](media/how-to-accidental-deletes/accident-1.png)

## <a name="recovering-from-an-accidental-delete-instance"></a>Recuperando de uma instância de exclusão acidental
Se você encontrar uma exclusão acidental, verá isso sobre o status da configuração do agente de provisionamento.  Ele dirá que o **limite de exclusão foi excedido**.
 
![Status de exclusão acidental](media/how-to-accidental-deletes/delete-1.png)

Ao clicar em **excluir limite excedido**, você verá as informações de status de sincronização.  Isso fornecerá detalhes adicionais. 
 
 ![Status da sincronização](media/how-to-accidental-deletes/delete-2.png)

Ao clicar com o botão direito do mouse nas reticências, você receberá as seguintes opções:
 - Exibir log de provisionamento
 - Exibir o agente
 - Permitir exclusões

 ![Clicar com o botão direito do mouse](media/how-to-accidental-deletes/delete-3.png)

Usando **Exibir log de provisionamento**, você pode ver as entradas **StagedDelete** e examinar as informações fornecidas nos usuários que foram excluídos.
 
 ![Provisionando logs](media/how-to-accidental-deletes/delete-7.png)

### <a name="allowing-deletes"></a>Permitindo exclusões

A ação **permitir exclusões** excluirá os objetos que dispararam o limite de exclusão acidental.  Use o procedimento a seguir para aceitar as exclusões.  

1. Clique com o botão direito do mouse nas reticências e selecione **permitir exclusões**.
2. Clique em **Sim** na confirmação para permitir as exclusões.
 
 ![Sim ao confirmar](media/how-to-accidental-deletes/delete-4.png)

3. Você verá a confirmação de que as exclusões foram aceitas e o status voltará a ser íntegro com o próximo ciclo. 
 
 ![Aceitar exclusões](media/how-to-accidental-deletes/delete-8.png)

### <a name="rejecting-deletions"></a>Rejeitando exclusões

Se não quiser permitir as exclusões, você precisará fazer o seguinte:
- investigar a origem das exclusões
- Corrija o problema (por exemplo, a UO foi movida do escopo acidentalmente e você agora a adicionou novamente ao escopo)
- Executar **reinicialização da sincronização** na configuração do agente

## <a name="next-steps"></a>Próximas etapas 

- [Solução de problemas de sincronização de nuvem Azure AD Connect?](how-to-troubleshoot.md)
- [Azure AD Connect códigos de erro de sincronização de nuvem](reference-error-codes.md)
 

