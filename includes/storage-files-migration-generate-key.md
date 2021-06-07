---
title: incluir arquivo
description: incluir arquivo
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: a142de62f1ab7046d47c29753863b16864d97536
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075539"
---
As chaves de criptografia de dados de serviço são usadas para criptografar dados confidenciais, como credenciais de conta de armazenamento, que são enviadas de seu serviço StorSimple Manager ao dispositivo StorSimple. Você precisará alterar essas chaves periodicamente se sua organização de TI tiver uma política de rotação de chave nos dispositivos de armazenamento. O processo de alteração de chave pode ser ligeiramente diferente dependendo se há um só ou vários dispositivos gerenciados pelo serviço StorSimple Manager. Para obter mais informações, acesse [Segurança e proteção de dados do StorSimple](../articles/storsimple/storsimple-8000-security.md).

A alteração da chave de criptografia de dados de serviço é um processo de três etapas:

1. Usando scripts do Windows PowerShell para o Azure Resource Manager, autorize um dispositivo a alterar a chave de criptografia de dados de serviço.
2. Usar o Windows PowerShell para StorSimple para iniciar a alteração da chave de criptografia de dados de serviço.
3. Se você tiver mais de um dispositivo StorSimple, atualize a chave de criptografia de dados de serviço nos outros dispositivos.

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>Etapa 1: use um script do Windows PowerShell para autorizar um dispositivo a alterar a chave de criptografia de dados de serviço
Normalmente, o administrador do dispositivo solicita que o administrador do serviço autorize um dispositivo a alterar as chaves de criptografia de dados de serviço. O administrador do serviço então autoriza o dispositivo a alterar a chave.

Esta etapa é executada usando o script baseado no Azure Resource Manager. O administrador de serviços pode selecionar um dispositivo qualificado para receber a autorização. O dispositivo é então autorizado a iniciar o processo de alteração da chave de criptografia de dados de serviço. 

Para obter mais informações sobre como usar o script, acesse [Authorize-ServiceEncryptionRollover.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1)

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Quais dispositivos podem ser autorizados a alterar as chaves de criptografia de dados de serviço?
Um dispositivo deve atender aos seguintes critérios para que possa ser autorizado a iniciar as alterações da chave de criptografia de dados de serviço:

* O dispositivo deve estar online para ser qualificado para autorização da alteração da chave de criptografia de dados de serviço.
* Você pode autorizar o mesmo dispositivo novamente após 30 minutos, caso a alteração de chave não tenha sido iniciada.
* Você pode autorizar um dispositivo diferente, desde que a alteração de chave não tenha sido iniciada pelo dispositivo autorizado anteriormente. Depois que o novo dispositivo tiver sido autorizado, o dispositivo antigo não poderá iniciar a alteração.
* Não é possível autorizar um dispositivo enquanto a substituição da chave de criptografia de dados de serviço estiver em andamento.
* Você pode autorizar um dispositivo quando alguns dos dispositivos registrados no serviço tiverem substituído a criptografia, enquanto outros não. 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>Etapa 2: Usar o Windows PowerShell para StorSimple para iniciar a alteração da chave de criptografia de dados de serviço
Essa etapa é executada na interface do Windows PowerShell para StorSimple no dispositivo StorSimple autorizado.

> [!NOTE]
> Nenhuma operação poderá ser executada no Portal do Azure do serviço do StorSimple Manager até que a substituição de chave esteja concluída.


Se você estiver usando o console serial do dispositivo para se conectar à interface do Windows PowerShell, execute as etapas a seguir.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>Para iniciar a alteração da chave de criptografia de dados de serviço
1. Selecione a Opção 1 para fazer logon com acesso completo.
2. No prompt de comando, digite:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. Depois que o cmdlet tiver sido concluído com êxito, você receberá uma nova chave de criptografia de dados de serviço. Copie e salve essa chave para uso na etapa 3 deste processo. Essa chave será usada para atualizar todos os dispositivos restantes registrados no serviço StorSimple Manager.
   
   > [!NOTE]
   > Esse processo deve ser iniciado em quatro horas, a contar da autorização de um dispositivo StorSimple.
   > 
   > 
   
   Essa nova chave é então enviada ao serviço para ser enviada por push a todos os dispositivos que são registrados no serviço. Um alerta aparecerá no painel de serviço. O serviço desabilitará todas as operações nos dispositivos registrados e o administrador do dispositivo precisará atualizar a chave de criptografia de dados de serviço nos outros dispositivos. No entanto, as E/Ss (hosts que enviam dados à nuvem) não serão interrompidas.
   
   Se você tiver um único dispositivo registrado no serviço, o processo de substituição agora está concluído e a próxima etapa poderá ser ignorada. Se você tiver vários dispositivos registrados em seu serviço, passe para a etapa 3.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>Etapa 3: Atualizar a chave de criptografia de dados de serviço em outros dispositivos StorSimple
Essas etapas devem ser executadas na interface do Windows PowerShell de seu dispositivo StorSimple, caso você tenha vários dispositivos registrados no serviço StorSimple Manager. A chave que você obteve na Etapa 2 deve ser usada para atualizar todos os demais dispositivos StorSimple registrados com o serviço do StorSimple Manager.

Execute as etapas a seguir para atualizar a criptografia de dados de serviço no seu dispositivo.

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>Para atualizar a chave de criptografia de dados de serviço em dispositivos físicos
1. Use o Windows PowerShell para StorSimple para se conectar ao console. Selecione a Opção 1 para fazer logon com acesso completo.
2. No prompt de comando, digite: `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. Forneça a chave de criptografia de dados de serviço que você obteve na [Etapa 2: Usar o Windows PowerShell para StorSimple para iniciar a alteração da chave de criptografia de dados de serviço](#to-initiate-the-service-data-encryption-key-change).

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>Para atualizar a chave de criptografia de dados de serviço em todos os dispositivos de nuvem 8010/8020
1. Baixe e instale o script do PowerShell [Update-CloudApplianceServiceEncryptionKey.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1). 
2. Abra o PowerShell e, no prompt de comando, digite: `Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

Esse script garantirá que essa chave de criptografia de dados de serviço seja definida em todos os dispositivos de nuvem 8010/8020 no gerenciador de dispositivos.