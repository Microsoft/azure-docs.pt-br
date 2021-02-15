---
title: Adicionar um público de visualização para uma oferta de máquina virtual no Azure Marketplace
description: Saiba como adicionar um público de visualização para uma oferta de máquina virtual no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 854d0fa9b34d495f03a0c3c6203ceb227e4712d2
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629505"
---
# <a name="how-to-add-a-preview-audience-for-a-virtual-machine-offer"></a>Como adicionar um público de visualização para uma oferta de máquina virtual

Na página **Visualização** (selecione no menu do nav esquerdo no Partner Center), selecione um **público-alvo** limitado para validar sua oferta antes de publicá-lo em tempo real para o público comercial mais amplo.

> [!IMPORTANT]
> Depois de verificar sua oferta no painel **Versão prévia** , selecione **Ativar** para publicar sua oferta para o público-alvo do marketplace comercial.

Seu público-alvo de visualização é identificado por GUIDs de **ID de assinatura do Azure** , juntamente com uma **Descrição** opcional para cada um. Nenhum desses campos pode ser visto pelos clientes. É possível encontrar a ID da assinatura na página **Assinaturas** no portal do Azure.

Adicione pelo menos uma ID da assinatura do Azure, seja individualmente (até dez IDs) ou carregando um arquivo CSV (até 100 IDs). Ao adicionar essas IDs de assinatura, você define quem pode ter acesso à versão prévia de sua oferta antes que ela seja publicada como ativa. Se a oferta já estiver ativa, você ainda poderá definir um público-alvo de versão prévia para as alterações de oferta de teste ou atualizações para sua oferta.

> [!NOTE]
> Um público-alvo de versão prévia difere de um público-alvo privado. Um público-alvo de versão prévia pode acessar sua oferta *antes* de ela ser publicada como ativa no Azure Marketplace. O público-alvo de versão prévia pode ver e validar todos os planos, incluindo aqueles que estarão disponíveis somente para um público-alvo privado depois que sua oferta for publicada integralmente no Azure Marketplace. Um público-alvo privado (definido no painel do plano **Preços e Disponibilidade** ) tem acesso exclusivo a um plano específico.

Se você fez alterações, selecione **salvar rascunho** antes de continuar para a próxima guia no menu do nav esquerdo, **visão geral do plano**.

## <a name="next-steps"></a>Próximas etapas

- [Criar planos](azure-vm-create-plans.md)
