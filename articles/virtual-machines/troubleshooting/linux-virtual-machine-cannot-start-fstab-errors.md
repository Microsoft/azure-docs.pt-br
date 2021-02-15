---
title: Solucionar problemas de inicialização de VM Linux devido a erros de fstab | Microsoft Docs
description: Explica por que a VM do Linux não pode iniciar e como resolver o problema.
services: virtual-machines-linux
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 10/09/2019
ms.author: v-six
ms.openlocfilehash: fd49993e6825c47bbae8f034715c03191e06ab2d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89441656"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-fstab-errors"></a>Solucionar problemas de inicialização de VM Linux devido a erros de fstab

Você não pode se conectar a uma VM (máquina virtual) Linux do Azure usando uma conexão Secure Shell (SSH). Ao executar o recurso de [diagnóstico de inicialização](./boot-diagnostics.md) na [portal do Azure](https://portal.azure.com/), você verá entradas de log que se assemelham aos seguintes exemplos:

## <a name="examples"></a>Exemplos

Veja a seguir exemplos de possíveis erros.

### <a name="example-1-a-disk-is-mounted-by-the-scsi-id-instead-of-the-universally-unique-identifier-uuid"></a>Exemplo 1: um disco é montado pela ID SCSI em vez do UUID (identificador universal exclusivo)

```
[K[[1;31m TIME [0m] Timed out waiting for device dev-incorrect.device.
[[1;33mDEPEND[0m] Dependency failed for /data.
[[1;33mDEPEND[0m] Dependency failed for Local File Systems.
…
Welcome to emergency mode! After logging in, type "journalctl -xb" to viewsystem logs, "systemctl reboot" to reboot, "systemctl default" to try again to boot into default mode.
Give root password for maintenance
(or type Control-D to continue)
```

### <a name="example-2-an-unattached-device-is-missing-on-centos"></a>Exemplo 2: um dispositivo desanexado está ausente no CentOS

```
Checking file systems…
fsck from util-linux 2.19.1
Checking all file systems.
/dev/sdc1: nonexistent device ("nofail" fstab option may be used to skip this device)
/dev/sdd1: nonexistent device ("nofail" fstab option may be used to skip this device)
/dev/sde1: nonexistent device ("nofail" fstab option may be used to skip this device)

[/sbin/fsck.ext3 (1) — /CODE] sck.ext3 -a /dev/sdc1
fsck.ext3: No such file or directory while trying to open /dev/sdc1

/dev/sdc1:
The superblock could not be read or does not describe a correct ext2
filesystem. If the device is valid and it really contains an ext2
filesystem (and not swap or ufs or something else), then the superblock
is corrupt, and you might try running e2fsck with an alternate superblock:

e2fsck -b 8193 <device>

[/sbin/fsck.xfs (1) — /GLUSTERDISK] fsck.xfs -a /dev/sdd1
/sbin/fsck.xfs: /dev/sdd1 does not exist
[/sbin/fsck.ext3 (1) — /DATATEMP] fsck.ext3 -a /dev/sde1 fsck.ext3: No such file or directory while trying to open /dev/sde1
```

### <a name="example-3-a-vm-cannot-start-because-of-an-fstab-misconfiguration-or-because-the-disk-is-no-longer-attached"></a>Exemplo 3: uma VM não pode ser iniciada devido a uma configuração incorreta de fstab ou porque o disco não está mais anexado

```
The disk drive for /var/lib/mysql is not ready yet or not present.
Continue to wait, or Press S to skip mounting or M for manual recovery
```

### <a name="example-4-a-serial-log-entry-shows-an-incorrect-uuid"></a>Exemplo 4: uma entrada de log serial mostra um UUID incorreto

```
Checking filesystems
Checking all file systems.
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 70442/1905008 files, 800094/7608064 blocks
[/sbin/fsck.ext4 (1) — /datadrive] fsck.ext4 -a UUID="<UUID>"
fsck.ext4: Unable to resolve UUID="<UUID>"
[FAILED

*** An error occurred during the file system check.
*** Dropping you to a shell; the system will reboot
*** when you leave the shell.
*** Warning — SELinux is active
*** Disabling security enforcement for system recovery.
*** Run 'setenforce 1' to reenable.
type=1404 audit(1428047455.949:4): enforcing=0 old_enforcing=1 auid=<AUID> ses=4294967295
Give root password for maintenance
(or type Control-D to continue)
```

Esse problema pode ocorrer se a sintaxe da tabela de sistemas de arquivos (fstab) estiver incorreta ou se um disco de dados necessário que esteja mapeado para uma entrada no arquivo "/etc/fstab" não estiver anexado à VM.

## <a name="resolution"></a>Resolução

Para resolver esse problema, inicie a VM no modo de emergência usando o console serial para máquinas virtuais do Azure. Em seguida, use a ferramenta para reparar o sistema de arquivos. Se o console serial não estiver habilitado em sua VM, vá para a seção [reparar a VM offline](#repair-the-vm-offline) .

## <a name="use-the-serial-console"></a>Usar o console serial

### <a name="using-single-user-mode"></a>Usando o modo de usuário único

1. Conecte-se ao [console serial](./serial-console-linux.md).
2. Usar o console serial para escolher o modo de usuário único [modo de usuário](serial-console-grub-single-user-mode.md)
3. Depois que a VM for inicializada no modo de usuário único. Use seu editor de texto favorito para abrir o arquivo fstab. 

   ```
   # nano /etc/fstab
   ```

4. Examine os sistemas de arquivos listados. Cada linha no arquivo fstab indica um sistema de arquivos que é montado quando a VM é iniciada. Para obter mais informações sobre a sintaxe do arquivo fstab, execute o comando Man fstab. Para solucionar uma falha de inicialização, examine cada linha para certificar-se de que ela está correta na estrutura e no conteúdo.

   > [!Note]
   > * Os campos em cada linha são separados por tabulações ou espaços. Linhas em branco são ignoradas. As linhas que têm um sinal numérico (#) como o primeiro caractere são comentários. As linhas comentadas podem permanecer no arquivo fstab, mas não serão processadas. É recomendável que você comente as linhas fstab que você não tem certeza sobre, em vez de remover as linhas.
   > * Para que a VM seja recuperada e iniciada, as partições do sistema de arquivos devem ser as únicas partições necessárias. A VM pode apresentar erros de aplicativo sobre partições comentadas adicionais. No entanto, a VM deve iniciar sem as partições adicionais. Posteriormente, você poderá remover os comentários das linhas comentadas.
   > * Recomendamos que você monte discos de dados em VMs do Azure usando o UUID da partição do sistema de arquivos. Por exemplo, execute o seguinte comando: ``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Para determinar o UUID do sistema de arquivos, execute o comando blkid. Para obter mais informações sobre a sintaxe, execute o comando Man blkid.
   > * A opção nofail ajuda a garantir que a VM seja iniciada mesmo que o sistema de arquivos esteja corrompido ou o sistema de arquivos não exista na inicialização. Recomendamos que você use a opção nofail no arquivo fstab para habilitar a inicialização para continuar após a ocorrência de erros em partições que não são necessárias para que a VM seja iniciada.

5. Altere ou comente todas as linhas incorretas ou desnecessárias no arquivo fstab para permitir que a VM seja iniciada corretamente.

6. Salve as alterações no arquivo fstab.

7. Reinicialize a VM usando o comando abaixo.
   
   ```
   # reboot -f
   ```
> [!Note]
   > Você também pode usar o comando "Ctrl + x" que também reinicializaria a VM.


8. Se o comentário ou correção das entradas for bem-sucedido, o sistema deverá alcançar um prompt de bash no Portal. Verifique se você pode se conectar à VM.

### <a name="using-root-password"></a>Usando a senha raiz

1. Conecte-se ao [console serial](./serial-console-linux.md).
2. Entre no sistema usando um usuário local e senha.

   > [!Note]
   > Você não pode usar uma chave SSH para entrar no sistema no console serial.

3. Procure o erro que indica que o disco não foi montado. No exemplo a seguir, o sistema estava tentando anexar um disco que não estava mais presente:

   ```
   [DEPEND] Dependency failed for /datadisk1.
   [DEPEND] Dependency failed for Local File Systems.
   [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
   [DEPEND] Dependency failed for Migrate local... structure to the new structure.
   Welcome to emergency mode! After logging in, type "journalctl -xb" to view
   system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
   Give root password for maintenance
   (or type Control-D to continue):
   ```

4. Conecte-se à VM usando a senha raiz (VMs baseadas em Red Hat).

5. Use seu editor de texto favorito para abrir o arquivo fstab. Depois que o disco for montado, execute o seguinte comando para o nano:

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Examine os sistemas de arquivos listados. Cada linha no arquivo fstab indica um sistema de arquivos que é montado quando a VM é iniciada. Para obter mais informações sobre a sintaxe do arquivo fstab, execute o comando Man fstab. Para solucionar uma falha de inicialização, examine cada linha para certificar-se de que ela está correta na estrutura e no conteúdo.

   > [!Note]
   > * Os campos em cada linha são separados por tabulações ou espaços. Linhas em branco são ignoradas. As linhas que têm um sinal numérico (#) como o primeiro caractere são comentários. As linhas comentadas podem permanecer no arquivo fstab, mas não serão processadas. É recomendável que você comente as linhas fstab que você não tem certeza sobre, em vez de remover as linhas.
   > * Para que a VM seja recuperada e iniciada, as partições do sistema de arquivos devem ser as únicas partições necessárias. A VM pode apresentar erros de aplicativo sobre partições comentadas adicionais. No entanto, a VM deve iniciar sem as partições adicionais. Posteriormente, você poderá remover os comentários das linhas comentadas.
   > * Recomendamos que você monte discos de dados em VMs do Azure usando o UUID da partição do sistema de arquivos. Por exemplo, execute o seguinte comando: ``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Para determinar o UUID do sistema de arquivos, execute o comando blkid. Para obter mais informações sobre a sintaxe, execute o comando Man blkid.
   > * A opção nofail ajuda a garantir que a VM seja iniciada mesmo que o sistema de arquivos esteja corrompido ou o sistema de arquivos não exista na inicialização. Recomendamos que você use a opção nofail no arquivo fstab para habilitar a inicialização para continuar após a ocorrência de erros em partições que não são necessárias para que a VM seja iniciada.

7. Altere ou comente todas as linhas incorretas ou desnecessárias no arquivo fstab para permitir que a VM seja iniciada corretamente.

8. Salve as alterações no arquivo fstab.

9. Reinicie a máquina virtual.

10. Se o comentário ou correção das entradas for bem-sucedido, o sistema deverá alcançar um prompt de bash no Portal. Verifique se você pode se conectar à VM.

11. Verifique os pontos de montagem ao testar qualquer alteração fstab executando o comando mount – a. Se não houver erros, os pontos de montagem deverão ser bons.

## <a name="repair-the-vm-offline"></a>Repare a VM offline

1. Anexe o disco do sistema da VM como um disco de dados a uma VM de recuperação (qualquer VM Linux em funcionamento). Para fazer isso, é possível usar [comandos da CLI](./troubleshoot-recovery-disks-linux.md) ou automatizar a configuração da VM de recuperação usando os [comandos de reparo da VM](repair-linux-vm-using-azure-virtual-machine-repair-commands.md).

2. Depois de montar o disco do sistema como um disco de dados na VM de recuperação, faça backup do arquivo fstab antes de fazer alterações e siga as próximas etapas para corrigir o arquivo fstab.

3. Procure o erro que indica que o disco não estava montado. No exemplo a seguir, o sistema estava tentando anexar um disco que não estava mais presente:

   ```output
   [DEPEND] Dependency failed for /datadisk1.
   [DEPEND] Dependency failed for Local File Systems.
   [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
   [DEPEND] Dependency failed for Migrate local... structure to the new structure.
   Welcome to emergency mode! After logging in, type "journalctl -xb" to view system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
   Give root password for maintenance (or type Control-D to continue):
   ```

4. Conecte-se à VM usando a senha raiz (VMs baseadas em Red Hat).

5. Use seu editor de texto favorito para abrir o arquivo fstab. Depois que o disco for montado, execute o comando a seguir para o nano. Verifique se você está trabalhando no arquivo fstab que está localizado no disco montado e não no arquivo fstab que está na VM de resgate.

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Examine os sistemas de arquivos listados. Cada linha no arquivo fstab indica um sistema de arquivos que é montado quando a VM é iniciada. Para obter mais informações sobre a sintaxe do arquivo fstab, execute o comando Man fstab. Para solucionar uma falha de inicialização, examine cada linha para certificar-se de que ela está correta na estrutura e no conteúdo.

   > [!Note]
   > * Os campos em cada linha são separados por tabulações ou espaços. Linhas em branco são ignoradas. As linhas que têm um sinal numérico (#) como o primeiro caractere são comentários. As linhas comentadas podem permanecer no arquivo fstab, mas não serão processadas. É recomendável que você comente as linhas fstab que você não tem certeza sobre, em vez de remover as linhas.
   > * Para que a VM seja recuperada e iniciada, as partições do sistema de arquivos devem ser as únicas partições necessárias. A VM pode apresentar erros de aplicativo sobre partições comentadas adicionais. No entanto, a VM deve iniciar sem as partições adicionais. Posteriormente, você poderá remover os comentários das linhas comentadas.
   > * Recomendamos que você monte discos de dados em VMs do Azure usando o UUID da partição do sistema de arquivos. Por exemplo, execute o seguinte comando: ``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Para determinar o UUID do sistema de arquivos, execute o comando blkid. Para obter mais informações sobre a sintaxe, execute o comando Man blkid. Observe que o disco que você deseja recuperar agora está montado em uma nova VM. Embora os UUIDs devam ser consistentes, as IDs de partição de dispositivo (por exemplo, "/dev/sda1") são diferentes nessa VM. As partições do sistema de arquivos da VM com falha original que estão localizadas em um VHD que não são do sistema não estão disponíveis para a VM de recuperação [usando comandos da CLI](./troubleshoot-recovery-disks-linux.md).
   > * A opção nofail ajuda a garantir que a VM seja iniciada mesmo que o sistema de arquivos esteja corrompido ou o sistema de arquivos não exista na inicialização. Recomendamos que você use a opção nofail no arquivo fstab para habilitar a inicialização para continuar após a ocorrência de erros em partições que não são necessárias para que a VM seja iniciada.

7. Altere ou comente todas as linhas incorretas ou desnecessárias no arquivo fstab para permitir que a VM seja iniciada corretamente.

8. Salve as alterações no arquivo fstab.

9. Reinicie a máquina virtual ou reconstrua a VM original.

10. Se o comentário ou correção das entradas for bem-sucedido, o sistema deverá alcançar um prompt de bash no Portal. Verifique se você pode se conectar à VM.

11. Verifique os pontos de montagem ao testar qualquer alteração fstab executando o comando mount – a. Se não houver erros, os pontos de montagem deverão ser bons.

12. Desmonte e desanexe o disco rígido virtual original e, em seguida, crie uma VM a partir do disco do sistema original. Para fazer isso, você pode usar [comandos da CLI](troubleshoot-recovery-disks-linux.md) ou os [comandos de reparo da VM](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) se os usou para criar a VM de recuperação.

13. Depois de criar a VM novamente e você pode se conectar a ela por meio de SSH, execute as seguintes ações:
    * Examine as linhas fstab que foram alteradas ou comentadas durante a recuperação.
    * Verifique se você está usando o UUID e a opção nofail adequadamente.
    * Teste as alterações fstab antes de reiniciar a VM. Para fazer isso, use o seguinte comando: ``$ sudo mount -a``
    * Crie uma cópia adicional do arquivo fstab corrigido para uso em cenários de recuperação futura.

## <a name="next-steps"></a>Próximas etapas

* [Solucionar problemas de uma VM Linux anexando o disco do SO a uma VM de recuperação com a CLI 2.0 do Azure](./troubleshoot-recovery-disks-linux.md)
* [Solucionar problemas de uma VM do Linux anexando o disco do sistema operacional a uma VM de recuperação usando o portal do Azure](./troubleshoot-recovery-disks-portal-linux.md)
