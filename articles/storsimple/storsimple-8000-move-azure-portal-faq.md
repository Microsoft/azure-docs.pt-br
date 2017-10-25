---
title: "Perguntas frequentes sobre mudar do portal clássico para o portal do Azure | Microsoft Docs"
description: "Fornece respostas para perguntas frequentes sobre como mover dispositivos do StorSimple do portal clássico para o portal do Azure."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/28/2017
ms.author: alkohli
ms.openlocfilehash: 06e8d99aa2ad4eb11e594a729c6dab39d5cd1eb6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="move-storsimple-device-manager-service-from-classic-to-azure-portal-frequently-asked-questions-faq"></a>Mover o serviço Gerenciador de Dispositivos do StorSimple do portal clássico para o portal do Azure: perguntas frequentes (FAQ)

## <a name="overview"></a>Visão geral

A seguir estão perguntas e respostas que você pode ter ao mover seu serviço Gerenciador de Dispositivos do StorSimple em execução no portal clássico do Azure para o portal do Azure.

As perguntas e respostas são organizadas nas seguintes categorias:

* Movendo o serviço Gerenciador de Dispositivos do StorSimple
* Movendo contas de armazenamento
* Usando cmdlets baseados no Azure Resource Manager
* Usando o serviço Gerenciador de Dados do StorSimple
* Diversos

## <a name="moving-storsimple-device-manager-service"></a>Movendo o serviço Gerenciador de Dispositivos do StorSimple

### <a name="once-i-have-moved-to-azure-portal-can-i-still-create-a-storsimple-manager-service-in-the-classic-portal"></a>Mudei uma vez para o portal do Azure. Posso ainda criar um serviço StorSimple Manager no portal clássico?

Não. Depois de migrar seu serviço StorSimple Manager para o portal do Azure, você não poderá criar um novo serviço no portal clássico. Além disso, você não pode gerenciar seu dispositivo no portal clássico. Para saber mais, vá para [Mover o serviço para o portal do Azure](storsimple-8000-manage-service.md#move-a-service-to-azure-portal).

### <a name="i-have-multiple-storsimple-managers-running-in-the-classic-portal-can-i-choose-which-ones-to-move-to-the-azure-portal"></a>Tenho vários Gerenciadores do StorSimple em execução no portal clássico. Posso escolher quais quero mover para o portal do Azure?

Não. Você não pode escolher quais Gerenciadores do StorSimple podem ser movidos para o portal do Azure. Todos os Gerenciadores do StorSimple na assinatura são movidos.


### <a name="i-initiated-the-migration-of-my-service-to-the-new-azure-portal-should-i-delete-the-storsimple-manager-from-the-classic-portal"></a>Eu iniciei a migração do meu serviço para o novo portal do Azure. Devo excluir o Gerenciador do StorSimple no portal clássico? 

Não. Não tente excluir os serviços que você moveu no portal clássico. Aguarde a conclusão da migração; depois que todos os Gerenciadores do StorSimple se mudarem para o novo portal, você não precisará excluir os serviços no portal clássico. Eventualmente, o portal clássico será preterido.

### <a name="can-i-rename-my-storsimple-device-manager-service-in-the-azure-portal"></a>Posso renomear meu serviço Gerenciador de Dispositivos do StorSimple no portal do Azure?

Não. O nome do serviço não pode ser alterado depois que o serviço é criado no portal do Azure. O mesmo comportamento também ocorre para outras entidades, como dispositivos, volumes, contêineres de volume e políticas de backup.

### <a name="can-i-create-the-80108020-storsimple-cloud-appliances-with-azure-resource-manager-deployment-model"></a>Posso criar Dispositivos de Nuvem StorSimple 8010/8020 com o modelo de implantação do Azure Resource Manager?

Sim. Você pode criar novos Dispositivos de Nuvem StorSimple 8010/8020 no modelo de implantação do Azure Resource Manager. As VMs subjacentes para esses dispositivos de nuvem também estão no modelo de implantação do Gerenciador de Recursos.

### <a name="when-we-migrate-subscriptions-to-the-azure-portal-will-the-underlying-vms-for-the-storsimple-cloud-appliances-also-migrate-to-azure-resource-management-deployment-model"></a>Quando migramos assinaturas para o portal do Azure, as VMs subjacentes para os Dispositivos de Nuvem StorSimple também migram para o modelo de implantação do Azure Resource Manager?

A mudança do serviço StorSimple independe do gerenciamento das VMs para os Dispositivos de Nuvem StorSimple. Você pode gerenciar totalmente as VMs para Dispositivos de Nuvem StorSimple no portal do Azure e no clássico ainda hoje.

### <a name="can-i-manage-existing-classic-80108020-storsimple-cloud-appliance-from-the-azure-portal"></a>Posso gerenciar o Dispositivo de Nuvem StorSimple 8010/8020 clássico existente no portal do Azure?

Sim. As máquinas virtuais associadas a dispositivos de nuvem 8010/8020 existentes podem ser gerenciadas no portal do Azure.

Se você criou dispositivos de nuvem StorSimple modelo 8010/8020 com a Atualização 3.0 ou posterior em execução, não sofrerá impacto pela mudança do serviço para o novo portal do Azure. Você deve conseguir gerenciar totalmente seus dispositivos de nuvem sem problemas. 

Se você tiver dispositivos de nuvem com versões anteriores da Atualização 3.0 em execução no portal clássico, só terá algumas funcionalidades disponíveis. Para saber mais, vá para a [lista de operações sem suporte para dispositivos que executam versões anteriores à Atualização 3](storsimple-8000-manage-service.md#move-a-service-to-azure-portal).

Você não pode atualizar um dispositivo de nuvem. Use a versão mais recente do software para criar um novo dispositivo de nuvem e faça failover dos contêineres de volume existentes para o novo dispositivo de nuvem criado. Para saber mais, vá para [Fazer failover para o dispositivo de nuvem](storsimple-8000-cloud-appliance-u2.md#fail-over-to-the-cloud-appliance)


### <a name="my-storsimple-8000-series-device-is-running-update-20-i-migrated-my-service-to-new-azure-portal-my-device-connected-successfully-but-it-seems-that-i-am-not-able-to-fully-manage-my-device-how-do-i-resolve-this-behavior"></a>Meu dispositivo da série StorSimple 8000 está executando a Atualização 2.0. Eu migrei meu serviço para o novo portal do Azure. Meu dispositivo foi conectado com êxito, mas não estou conseguindo gerenciar totalmente o meu dispositivo. Como posso resolver esse comportamento?

O novo portal do Azure tem suporte somente para dispositivos StorSimple que executam a Atualização 3.0 e superior. Se o dispositivo estava executando a Atualização 2.0, você só tem algumas funcionalidades disponíveis para o dispositivo. Para saber mais, vá para a [lista de operações sem suporte para dispositivos que executam versões anteriores à Atualização 3](storsimple-8000-manage-service.md#move-a-service-to-azure-portal).

Para gerenciar totalmente o seu dispositivo, instale a atualização mais recente nele. Para saber mais, vá para [Instalar a Atualização 5](storsimple-8000-install-update-5.md).

### <a name="i-just-moved-my-storsimple-manager-service-to-the-azure-portal-i-am-seeing-some-alerts-related-to-my-device-is-this-behavior-related-to-the-move"></a>Acabei de mover meu serviço StorSimple Manager para o portal do Azure. Estou recebendo alguns alertas relacionados ao meu dispositivo. Esse comportamento está relacionado com a mudança?

Não. A mudança em si não deve resultar em erros ou alertas. Siga as recomendações de alerta para resolver os alertas.

### <a name="i-am-using-a-storsimple-50007000-series-device-are-these-also-supported-in-the-azure-portal"></a>Estou usando um dispositivo da série StorSimple 5000/7000. Eles também têm suporte no portal do Azure?

Não. Não há suporte para os dispositivos StorSimple da série 5000/7000 no portal do Azure.

### <a name="i-am-planning-to-migrate-data-from-storsimple-50007000-series-device-to-a-storsimple-8000-series-device-how-does-moving-a-service-to-azure-portal-impact-my-data-migration"></a>Estou planejando migrar dados de dispositivo StorSimple da série 5000/7000 para um dispositivo StorSimple da série 8000. Como a mudança de um serviço para o portal do Azure afeta a migração de dados? 

Você pode migrar dados de seu dispositivo StorSimple da série 5000/7000 para um dispositivo StorSimple da série 8000 em execução no portal do Azure. Para permitir a migração de dados da série 5000/7000 para a série 8000, você deve [Registrar um tíquete de suporte no Suporte da Microsoft](storsimple-8000-contact-microsoft-support.md).


## <a name="moving-subscriptions-storage-accounts-resource-groups"></a>Movendo assinaturas, contas de armazenamento, grupos de recursos

### <a name="can-i-move-storsimple-device-manager-from-one-subscription-to-another-subscription-in-the-azure-portal"></a>Posso mover o Gerenciador de Dispositivos do StorSimple de uma assinatura para outra no portal do Azure?

Não. Não é possível mover o serviço Gerenciador de Dispositivos do StorSimple de uma assinatura para outra. Você pode executar um processo manual que consiste nas seguintes etapas:

* Migre dados para fora do dispositivo StorSimple.
* Execute uma redefinição de fábrica do dispositivo. Isso excluirá todos os dados locais do dispositivo.
* Registre o dispositivo com a nova assinatura para um serviço Gerenciador de Dispositivos do StorSimple.
* Migre os dados novamente para o dispositivo.

### <a name="do-i-have-to-migrate-the-storage-account-to-azure-resource-manager-deployment-model"></a>É necessário migrar a conta de armazenamento para o Modelo de implantação do Azure Resource Manager?

Não. Suas contas de armazenamento do clássico podem ser totalmente gerenciadas no portal do Azure. Quando você move o serviço StorSimple no portal do Azure, sua conta de armazenamento continua a funcionar como anteriormente.

### <a name="what-happens-to-the-storage-account-after-the-service-is-migrated-to-the-azure-portal"></a>O que acontece com a conta de armazenamento depois que o serviço é migrado para o portal do Azure?

A mudança do serviço não está relacionada ao gerenciamento da conta de armazenamento. Tanto as contas de armazenamento clássicas quanto as do Azure Resource Manager podem ser totalmente gerenciadas no portal do Azure. Quando você move o seu serviço para o portal do Azure, o dispositivo deve continuar a ser executado com essa conta de armazenamento e não deve haver nenhum impacto nos seus dados.

### <a name="can-i-migrate-storsimple-device-manager-from-one-resource-group-to-another"></a>Posso migrar o Gerenciador de Dispositivos do StorSimple de um grupo de recursos para outro?

Não. Você não pode mover um Gerenciador de Dispositivos do StorSimple criado com um grupo de recursos para outro grupo de recursos.

## <a name="using-azure-resource-manager-based-cmdlets"></a>Usando cmdlets baseados no Azure Resource Manager

### <a name="i-moved-to-the-new-azure-portal-now-my-scripts-based-on-azure-classic-deployment-model-powershell-cmdlets-are-not-working-what-do-i-need-to-do"></a>Mudei-me para o novo portal do Azure. Agora, meus scripts com base nos cmdlets do PowerShell do modelo de implantação clássica do Azure não estão funcionando? O que preciso fazer?

Não há suporte para os cmdlets do PowerShell existentes do modelo Implantação clássica do Azure no portal do Azure. Atualize os scripts para gerenciar seus dispositivos por meio do Azure Resource Manager. Para saber mais sobre como atualizar seus scripts, vá para [Exemplos para o novo portal do Azure](https://github.com/anoobbacker/storsimpledevicemgmttools).

### <a name="i-just-moved-to-the-azure-portal-and-needed-to-roll-over-the-service-data-encryption-key-where-is-this-option-in-the-azure-portal"></a>Acabei de me mudar para o portal do Azure e preciso sobrepor a chave de criptografia de dados de serviço. Onde está essa opção no portal do Azure?

A opção de sobrepor a chave de criptografia de dados de serviço não está no portal do Azure. Para saber mais sobre como fazer essa sobreposição no portal do Azure, vá para [Alterar a chave de criptografia de dados de serviço](storsimple-8000-manage-service.md#change-the-service-data-encryption-key).

### <a name="i-am-using-windows-powershell-for-storsimple-cmdlets-on-the-storsimple-device-to-perform-operations-such-extract-a-support-package-are-these-cmdlets-affected-when-i-move-to-the-new-azure-portal"></a>Estou usando o Windows PowerShell para cmdlets do StorSimple no dispositivo StorSimple a fim de executar operações como extrair um pacote de suporte. Esses cmdlets são afetados quando eu me mudo para o novo portal do Azure?

Não. Com a mudança do serviço para o novo portal do Azure, não deve haver nenhum impacto sobre os cmdlets do Windows PowerShell para StorSimple associados ao dispositivo StorSimple local (que por si só não será afetado pela mudança). Você pode continuar a usar esses cmdlets para criar um pacote de suporte sem problemas até mesmo no portal do Azure. Somente os cmdlets do PowerShell no modelo de implantação clássica são afetados por essa mudança.

## <a name="moving-storsimple-data-manager-service"></a>Movendo o serviço Gerenciador de Dados do StorSimple

### <a name="i-am-using-storsimple-data-manager-service-how-should-i-proceed-with-this-move"></a>Estou usando o serviço Gerenciador de Dados do StorSimple. Como devo prosseguir com a mudança?

Se você estiver usando o serviço Gerenciador de Dados do StorSimple, precisará mover os Gerenciadores de Dispositivo do StorSimple pela primeira vez para o portal do Azure. Quando a migração for concluída, crie novos Gerenciadores de Dados do StorSimple no portal do Azure. Os Gerenciadores de Dados do StorSimple criados antes da mudança não funcionam.

Para saber mais sobre migração do serviço Gerenciador de Dispositivos de StorSimple, vá para [Mover seu serviço para o portal do Azure](storsimple-8000-manage-service.md#move-a-service-to-azure-portal). Para saber mais sobre a criação do Gerenciador de Dados do StorSimple, vá para [Criar um serviço Gerenciador de Dados do StorSimple](storsimple-data-manager-ui.md).

## <a name="miscellaneous"></a>Diversos

### <a name="i-am-running-storsimple-snapshot-manager-for-my-8000-series-device-is-there-any-impact-on-storsimple-snapshot-manager-when-i-move-to-azure-portal"></a>Estou executando o StorSimple Snapshot Manager em meu dispositivo da série 8000. Haverá algum impacto no StorSimple Snapshot Manager quando eu me mudar para o portal do Azure?

Não. Não haverá nenhum impacto no StorSimple Snapshot Manager quando você mudar o serviço para o portal do Azure. O dispositivo e o StorSimple Snapshot Manager continuam a operar como antes.

### <a name="can-i-rename-my-storsimple-device-volume-containers-or-volumes"></a>Posso renomear o dispositivo StorSimple, os contêineres de volume ou os volumes?

Não. Você não pode renomear os dispositivos, os volumes, os contêineres de volume ou as políticas de backup no portal do Azure.

## <a name="next-steps"></a>Próximas etapas

Aprenda passo a passo a [mover seu serviço Gerenciador de Dispositivos do StorSimple para o portal do Azure](storsimple-8000-manage-service.md#move-a-service-to-azure-portal).



