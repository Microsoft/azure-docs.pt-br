---
title: A inicialização VM do Azure está presa no Windows Update | Microsoft Docs
description: Saiba como solucionar o problema quando uma inicialização de VM do Azure fica presa no Windows Update.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: genli
ms.openlocfilehash: 3090b7b889d914fc0cdb598b8bf29a73c81f50cb
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98631996"
---
# <a name="azure-vm-startup-is-stuck-at-windows-update"></a>Inicialização de VM do Azure presa no Windows Update

Este artigo ajuda a resolver o problema de quando sua VM (Máquina Virtual) fica presa no estágio do Windows Update durante a inicialização. 


## <a name="symptom"></a>Sintoma

 Uma VM do Windows não inicia. Quando você verificar as capturas de tela na janela [Diagnósticos de inicialização](../troubleshooting/boot-diagnostics.md), verá que a inicialização está presa no processo de atualização. A seguir estão exemplos de mensagens que você pode receber:

- Instalando o Windows ##%. Não desligue o computador. Isso vai demorar um pouco enquanto seu computador será reiniciado várias vezes
- Mantenha seu computador ligado até que isso seja concluído. Instalando a atualização # de #... 
- Não foi possível concluir as atualizações. Desfazendo alterações, não desligue o computador
- Falha ao configurar atualizações do Windows. Revertendo alterações. Não desligue o computador
- Erro < código de erro > ao aplicar operações de atualização ##### de ##### (\Regist...)
- Erro fatal < código de erro > aplicar operações de atualização de ##### de ##### ($$...)


## <a name="solution"></a>Solução
> [!TIP]
> Se você tiver um backup recente da VM, poderá tentar [restaurar a VM do backup](../../backup/backup-azure-arm-restore-vms.md) para corrigir o problema de inicialização.

Dependendo do número de atualizações que estão sendo instaladas ou revertidas, o processo de atualização pode levar algum tempo. Deixe a VM nesse estado por 8 horas. Se a VM ainda estiver nesse estado após esse período, reinicie-a pelo portal do Azure e veja se ela consegue iniciar normalmente. Se essa etapa não funcionar, tente a solução a seguir.

### <a name="remove-the-update-that-causes-the-problem"></a>Remover a atualização que causou o problema

1. Tire um instantâneo do disco do SO da VM afetada como um backup. Para obter mais informações, consulte [Instantâneo de um disco](../windows/snapshot-copy-managed-disk.md). 
2. [Anexe o disco do sistema operacional a uma VM de recuperação](troubleshoot-recovery-disks-portal-windows.md).
3. Depois que o disco do SO está conectado à VM de recuperação, execute **diskmgmt.msc** para abrir o Gerenciamento de Disco e verifique se o disco anexado está **ONLINE**. Anote a letra da unidade atribuída ao disco do sistema operacional anexado contendo a pasta \Windows. Se o disco estiver criptografado, descriptografe-o antes de continuar com as próximas etapas neste documento.

4. Abra uma instância de prompt de comandos com privilégios elevados (Executar como administrador). Insira o comando a seguir para obter a lista dos pacotes de atualização que estão no disco do SO anexado:

    ```console
    dism /image:<Attached OS disk>:\ /get-packages > c:\temp\Patch_level.txt
    ```

    Por exemplo, se o disco do sistema operacional anexado for a unidade F, execute o seguinte comando:

    ```console
    dism /image:F:\ /get-packages > c:\temp\Patch_level.txt
    ```

5. Abra o arquivo C:\temp\Patch_level.txt e, em seguida, leia-o de baixo para cima. Localize a atualização que está no estado **Instalação Pendente** ou **Desinstalação Pendente**.  A seguir está um exemplo do status de atualização:

    ```
    Package Identity : Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    State : Install Pending
    Release Type : Security Update
    Install Time :
    ```
6. Remova a atualização que causou o problema:
    
    ```
    dism /Image:<Attached OS disk>:\ /Remove-Package /PackageName:<PACKAGE NAME TO DELETE>
    ```
    Exemplo: 

    ```
    dism /Image:F:\ /Remove-Package /PackageName:Package_for_RollupFix~31bf3856ad364e35~amd64~~17134.345.1.5
    ```

    > [!NOTE] 
    > Dependendo do tamanho do pacote, a ferramenta DISM levará algum tempo para processar a desinstalação. Normalmente, o processo será concluído em 16 minutos.

7. [Desanexe o disco do SO e recrie a VM](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-the-original-virtual-hard-disk). Em seguida, verifique se o problema for resolvido.
