---
title: Estamos movendo imagens de máquina virtual (VM) para o armazenamento em disco gerenciado no Azure Marketplace
description: Para fornecer armazenamento mais rápido e confiável e suporte para novos recursos e funcionalidades do Marketplace, estamos movendo as imagens de VM do Marketplace para o armazenamento em disco gerenciado.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/22/2019
author: kriti-ms
ms.author: krsh
ms.openlocfilehash: e3d20b5e9206cf05d749f75982a07937a8caf078
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102201083"
---
# <a name="were-moving-virtual-machine-vm-images-on-azure-marketplace-to-managed-disk-storage"></a>Estamos movendo as imagens da VM (máquina virtual) no Azure Marketplace para o armazenamento em disco gerenciado

Para fornecer armazenamento mais rápido e confiável e suporte para novos recursos e funcionalidades do Marketplace, estamos movendo as imagens de VM do Marketplace para o armazenamento em disco gerenciado.

A partir de 2 de janeiro de 2020, moveremos as imagens da VM para o armazenamento em disco gerenciado em fases. Na primeira fase, moveremos apenas imagens sem novas implantações ou máquinas virtuais em execução nos últimos 90 dias. Antes de movermos qualquer uma das imagens, enviaremos um email para permitir que o Publicador saiba quais imagens serão movidas e quando elas serão transferidas.

Os editores ou consumidores não precisam realizar nenhuma ação e os usuários não serão afetados. As ofertas do Marketplace permanecerão disponíveis e os clientes ainda poderão implantar VMs gerenciadas dessas imagens, durante e após a movimentação.

Se você tiver alguma dúvida, [entre em contato conosco](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff).

## <a name="faqs"></a>Perguntas frequentes

### <a name="would-the-users-of-my-vm-images-experience-an-outage"></a>Os usuários de minhas imagens de VM sofreram uma interrupção?

Os usuários das imagens de VM não sofrerão uma interrupção. 

### <a name="how-long-does-it-take-for-the-process-to-complete"></a>Quanto tempo leva para que o processo seja concluído?

Pode levar até 24 horas para concluir a migração.

### <a name="do-i-need-to-take-any-action"></a>Preciso realizar qualquer ação?

Não. Os editores ou consumidores não precisam realizar nenhuma ação.

### <a name="do-i-have-to-update-my-system-to-call-the-cloud-portal-apis-in-a-different-way-after-they-are-moved-to-managed-disk-storage"></a>É necessário atualizar meu sistema para chamar as APIs do portal de nuvem de uma maneira diferente depois que elas são movidas para o armazenamento de disco gerenciado?

Não. Suas chamadas à API existentes continuarão a funcionar.

### <a name="would-all-my-vm-images-be-moved-to-managed-disk-at-the-same-time"></a>Todas as imagens de minha VM seriam movidas para o disco gerenciado ao mesmo tempo?

Iremos mover todas as suas imagens de VM no mesmo dia. Você será notificado quando eles forem movidos.

### <a name="can-i-request-to-schedule-the-move-of-my-vm-images-to-a-later-time"></a>Posso solicitar para agendar a movimentação de minhas imagens de VM para um momento posterior?

Recomendamos mover as imagens na data agendada. No entanto, se você tiver preocupações, entre em contato conosco para reagendar a movimentação.

### <a name="can-i-publish-updates-to-my-vm-images-during-the-move"></a>Posso publicar atualizações em minhas imagens de VM durante a movimentação?

As atualizações para as imagens de VM não podem ser feitas durante a movimentação.

### <a name="will-the-publishing-process-change-after-my-vm-image-is-moved-to-managed-disk"></a>O processo de publicação será alterado depois que minha imagem VM for movida para o disco gerenciado?

Não, o processo de publicação permanecerá o mesmo. 

### <a name="can-the-publisher-move-their-offers-to-managed-disk"></a>O Publicador pode mover suas ofertas para o disco gerenciado?

Não, os Publicadores não podem mover suas ofertas para o disco gerenciado. Eles precisarão aguardar e suas imagens serão movidas automaticamente. Enviaremos notificações ao Publicador antes de fazermos alterações.
