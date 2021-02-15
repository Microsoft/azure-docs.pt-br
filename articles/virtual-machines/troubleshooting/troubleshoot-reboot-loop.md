---
title: Loop de reinicialização do Windows em uma VM do Azure | Microsoft Docs
description: Saiba como solucionar problemas de loop de reinicialização do Windows | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/15/2018
ms.author: genli
ms.openlocfilehash: ad0ed7e9619f0b789bf8949fe398aa27bc36b9e0
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629633"
---
# <a name="windows-reboot-loop-on-an-azure-vm"></a>Loop de reinicialização do Windows em uma VM do Azure
Este artigo descreve o loop de reinicialização pode ocorrer em uma VM (Máquina Virtual) do Windows no Microsoft Azure.

## <a name="symptom"></a>Sintoma

Quando você usa [Diagnósticos de inicialização](./boot-diagnostics.md) para obter as capturas de tela de uma VM, vê que a máquina virtual está sendo inicializada, mas que o processo de inicialização está sendo interrompido e reiniciando.

![Tela inicial 1](./media/troubleshoot-reboot-loop/start-screen-1.png)

## <a name="cause"></a>Causa

O loop de reinicialização ocorre devido às causas a seguir:

### <a name="cause-1"></a>Causa 1

Há um serviço de terceiros sinalizado como crítico e não é possível iniciá-lo. Isso faz provoca a reinicialização do sistema operacional.

### <a name="cause-2"></a>Causa 2

Algumas alterações foram feitas ao sistema operacional. Geralmente, elas estão relacionadas a uma instalação de atualização, à instalação de aplicativo ou a uma nova política. Talvez você precise verificar os seguintes logs para obter detalhes adicionais:

- Logs de eventos
- CBS.logWindows
- Update.log

### <a name="cause-3"></a>Causa 3

Um dano no sistema de arquivos pode causar esse erro. No entanto, é difícil diagnosticar e identificar a alteração que causa o dano no sistema operacional.

## <a name="solution"></a>Solução

> [!TIP]
> Se você tiver um backup recente da VM, poderá tentar [restaurar a VM do backup](../../backup/backup-azure-arm-restore-vms.md) para corrigir o problema de inicialização.

Para resolver esse problema, [faça backup do disco do sistema operacional](../windows/snapshot-copy-managed-disk.md) e [anexe o disco do sistema operacional a uma VM de resgate](./troubleshoot-recovery-disks-portal-windows.md), então siga as opções de solução adequadamente ou experimente as soluções individualmente.

### <a name="solution-for-cause-1"></a>Solução para a causa 1

1. Depois que o disco do sistema operacional tiver sido anexado a uma VM em funcionamento, garanta que esse disco esteja sinalizado como **Online** no console Gerenciamento de Disco e observe a letra da unidade da partição que contém a pasta **\Windows**.

2. Se o disco estiver definido como **Offline**, defina-o como **Online**.

3. Criar uma cópia da pasta **\Windows\System32\config** caso uma reversão de alterações seja necessária.

4. Na VM de resgate, abra o Editor do Registro do Windows (regedit).

5. Selecione a chave **HKEY_LOCAL_MACHINE** e, em seguida, **Arquivo** > **Carregar Hive** no menu.

6. Navegue até o arquivo SYSTEM na pasta **\Windows\System32\config**.

7. Selecione **Abrir**, digite **BROKENSYSTEM** para o nome, expanda a chave **HKEY_LOCAL_MACHINE** e, em seguida, você verá uma chave adicional chamada **BROKENSYSTEM**.

8. Verifique de qual ControlSet o computador está inicializando. Você verá o número de chaves na seguinte chave do Registro.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\Select\Current`

9. Verifique qual é a importância do serviço de agente da VM por meio da seguinte chave do Registro.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\RDAgent\ErrorControl`

10. Se o valor da chave do Registro não estiver definido como **2**, vá para a atenuação seguinte.

11. Se o valor da chave do Registro estiver definido como **2**, altere-o de **2** para **1**.

12. Se existir alguma das seguintes chaves e elas tiverem o valor **2** ou **3**, altere esses valores para **1** adequadamente:

    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupCoordinatorSvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupInquirySvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupPluginSvc\ErrorControl`

13. Selecione a chave **BROKENSYSTEM** e, em seguida, selecione **arquivo**  >  **Descarregar Hive** no menu.

14. Desanexe o disco do sistema operacional da VM de solução de problemas.

15. Remova o disco da VM para solução de problemas e aguarde cerca de 2 minutos para o Azure liberar esse disco.

16. [Crie uma nova VM usando o disco do sistema operacional](../windows/create-vm-specialized.md).

17. Se o problema for corrigido, talvez você precisará reinstalar o [RDAgent](/archive/blogs/mast/install-the-vm-agent-on-an-existing-azure-vm) (WaAppAgent.exe).

### <a name="solution-for-cause-2"></a>Solução para a causa 2

Restaure a VM para a última configuração válida conhecida, siga as etapas em [Como iniciar a VM do Windows do Azure com a última configuração válida conhecida](https://support.microsoft.com/help/4016731/).

### <a name="solution-for-cause-3"></a>Solução para a causa 3
>[!NOTE]
>O procedimento a seguir deve ser usado apenas como último recurso. Embora restaurar de regback possa restaurar o acesso ao computador, o sistema operacional não será considerado estável, pois haverá dados perdidos no Registro entre o carimbo de data/hora do hive e o dia atual. Você precisa criar uma nova VM e fazer planos para migrar os dados.

1. Depois que o disco estiver anexado a uma VM de solução de problemas, ele deve ser sinalizado como **Online** no console de Gerenciamento de Disco.

2. Criar uma cópia da pasta **\Windows\System32\config** caso uma reversão de alterações seja necessária.

3. Copie os arquivos na pasta **\Windows\System32\config\regback** e substitua-os na pasta **\Windows\System32\config**.

4. Remova o disco da VM para solução de problemas e aguarde cerca de 2 minutos para o Azure liberar esse disco.

5. [Crie uma nova VM usando o disco do sistema operacional](../windows/create-vm-specialized.md).
