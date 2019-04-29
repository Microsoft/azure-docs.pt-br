---
title: Migrar contas de armazenamento e assinaturas do serviço Gerenciador de Dispositivos do StorSimple | Microsoft Docs
description: Saiba como migrar assinaturas, contas de armazenamento do serviço Gerenciador de Dispositivos do StorSimple 8000.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: 3cce18fa1890fc9e518294e294cc43e0e55065aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60631525"
---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>Migrar assinaturas e contas de armazenamento associadas ao serviço Gerenciador de Dispositivos do StorSimple

Talvez seja necessário mover o serviço StorSimple para um novo registro ou uma nova assinatura. Esses cenários de migração são alterações na conta ou no datacenter. Use a tabela a seguir para entender para qual desses cenários há suporte, incluindo as etapas detalhadas para a movimentação.

## <a name="account-changes"></a>Alterações na conta

| Você pode mover...| Com suporte| Tempo de inatividade| Processo de Suporte do Azure| Abordagem|
|-----|-----|-----|-----|-----|
| Uma assinatura inteira (inclui o serviço StorSimple e contas de armazenamento) para outro registro? | Sim       | Não        | **Transferência de Registro**<br>Use:<li>Quando você adquire um novo compromisso do Azure nos termos de um novo contrato.</li><li>Você deseja migrar todas as contas e assinaturas do registro antigo para o novo. Isso inclui todos os serviços do Azure na assinatura antiga.</li> | **Etapa 1: Abra um tíquete de suporte de operação do Azure Enterprise.**<li>Vá para [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).</li><li> Selecione **Administração do Registro** e, em seguida, selecione **Transferir de um registro para um novo registro**.<br>**Etapa 2: Forneça as informações solicitadas**<br>Inclua:<li>número de registro de origem</li><li> número de registro de destino</li><li>data de efetivação de transferência|
| Um serviço StorSimple de uma conta existente para um novo registro?    | Sim       | Não        | **Transferência de Conta**<br>Use:<li>Quando você não deseja fazer uma transferência completa de registro.</li><li>Você deseja apenas mover contas específicas para um novo registro.</li>| **Etapa 1: Abra um tíquete de suporte de operação do Azure Enterprise.**<li>Vá para [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).</li><li>Selecione **Administração do Registro** e, em seguida, selecione **Transferir uma Conta do EA para um novo registro**.<br>**Etapa 2: Forneça as informações solicitadas**<br>Inclua:<li>número de registro de origem</li><li> número de registro de destino</li><li>data de efetivação de transferência|
| Um serviço StorSimple de uma assinatura para outra assinatura?      | Não         |    Sim         | Nenhum, processo manual|<li>Migre dados para fora do dispositivo StorSimple.</li><li>Execute uma redefinição de fábrica do dispositivo. Isso excluirá todos os dados locais do dispositivo.</li><li>Registre o dispositivo com a nova assinatura para um serviço Gerenciador de Dispositivos do StorSimple.</li><li>Migre os dados novamente para o dispositivo.|
|Posso transferir a propriedade de uma assinatura do Azure para outro diretório? | Sim       | Não        | Associar uma assinatura existente ao diretório do Azure AD | Veja [Para associar uma assinatura existente ao seu diretório do Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md). Pode levar até 10 minutos para que tudo seja mostrado corretamente.|
| Um dispositivo StorSimple de um serviço Gerenciador de Dispositivos do StorSimple para outro serviço em outra região?      | Não         | Sim            | Nenhum, processo manual |Mesmo que acima.|
| Conta de armazenamento para uma nova assinatura ou grupo de recursos?     | Sim        | Não              |Mova a conta de armazenamento para uma assinatura ou grupo de recursos diferente |Após movê-la, se as chaves de acesso da conta de armazenamento forem atualizadas, o usuário precisará configurar as chaves de acesso manualmente para a conta de armazenamento migrada por meio do serviço do Gerenciador de Dispositivos do StorSimple.|
| Conta de armazenamento clássico para uma conta de armazenamento do Azure Resource Manager      | Sim        | Não              |Migrar de Clássica para o Azure Resource Manager |<li>Para obter instruções detalhadas sobre como migrar uma conta de armazenamento de clássico para Azure Resource Manager, vá para [Migrar uma conta de armazenamento clássica](../virtual-machines/windows/migration-classic-resource-manager-ps.md#step-62-migrate-a-storage-account).</li><li> Se as chaves de acesso da conta de armazenamento forem atualizadas após a migração, o usuário precisará sincronizar as chaves de acesso para a conta de armazenamento migrada por meio do serviço do Gerenciador de Dispositivos do StorSimple. Isso é para garantir que os dispositivos do StorSimple continuem a funcionar normalmente e sejam capazes de armazenar camadas de dados principais/backup para o Azure. Para obter instruções detalhadas sobre como sincronizar as chaves de acesso, acesse [Fluxo de trabalho de rotação](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts).</li><li> No caso de um dispositivo de nuvem do StorSimple, se a conta de armazenamento clássico for migrada, mas a máquina virtual subjacente permanecer no modo clássico, o dispositivo deve funcionar corretamente. Se a máquina virtual subjacente para o dispositivo de nuvem for migrada, a funcionalidade de desativar e excluir não funcionará.</li><li> Você deve criar um novo Dispositivo de Nuvem do StorSimple no portal do Azure e, em seguida, fazer o failover dos dispositivos de nuvem mais antigos. Não é possível criar um Dispositivo de Nuvem do StorSimple no novo portal do Azure usando uma conta de armazenamento clássico, eles precisam ter uma conta de armazenamento do Azure Resource Manager. Para obter mais informações, acesse [Implantar e gerenciar um Dispositivo de Nuvem StorSimple](storsimple-8000-cloud-appliance-u2.md).</li>|

## <a name="datacenter-changes"></a>Alterações no datacenter

| Você pode mover...| Com suporte|Tempo de inatividade| Processo de Suporte do Azure| Abordagem|
|-----|-----|-----|-----|-----|
| Um serviço StorSimple de um datacenter do Azure para outro? | Não  | Sim |Nenhum, processo manual  |<li>Migre dados para fora do dispositivo StorSimple.</li><li>Execute uma redefinição de fábrica do dispositivo. Isso excluirá todos os dados locais do dispositivo.</li><li>Registre o dispositivo com a nova assinatura para um novo serviço Gerenciador de Dispositivos do StorSimple.</li><li>Migre os dados novamente para o dispositivo.|
| Uma conta de armazenamento de um datacenter do Azure para outro? | Não  |Sim  |Nenhum, processo manual  | Mesmo que acima.|

## <a name="next-steps"></a>Próximas etapas

* [Implantar um serviço do Gerenciador de Dispositivos do StorSimple](storsimple-8000-manage-service.md)
* [Implantar um dispositivo StorSimple da série 8000 no portal do Azure](storsimple-8000-deployment-walkthrough-u2.md)
