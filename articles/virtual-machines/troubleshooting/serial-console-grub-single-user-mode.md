---
title: Console serial do Azure para GRUB e modo de usuário único | Microsoft Docs
description: Este artigo descreve como usar o console serial para GRUB em máquinas virtuais do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: alsin
ms.openlocfilehash: 5341cc62a7d02c3072df90becf893dec18427ac2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87439536"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Usar o console serial para acessar o GRUB e o modo de usuário único
O carregador de inicialização global unificado (GRUB) é provavelmente a primeira coisa que você vê ao inicializar uma VM (máquina virtual). Como ele é exibido antes do sistema operacional ser iniciado, o GRUB não é acessível via SSH. No GRUB, você pode modificar a configuração de inicialização para inicializar no modo de usuário único, entre outras coisas.

O modo de usuário único é um ambiente mínimo com funcionalidade mínima. Pode ser útil para investigar problemas de inicialização, problemas do sistema de arquivos ou problemas de rede. Menos serviços podem ser executados em segundo plano e, dependendo do runlevel, um sistema de arquivos pode nem mesmo ser montado automaticamente.

O modo de usuário único também é útil em situações em que sua VM pode ser configurada para aceitar somente chaves SSH para entrada. Nesse caso, você poderá usar o modo de usuário único para criar uma conta com autenticação de senha.

> [!NOTE]
> O serviço de console serial permite que apenas usuários com nível de *colaborador* ou permissões mais altas acessem o console serial de uma VM.

Para entrar no modo de usuário único, digite GRUB quando sua VM estiver sendo inicializada e modifique a configuração de inicialização no GRUB. Consulte instruções detalhadas para inserir o GRUB na próxima seção. Em geral, se a VM tiver sido configurada para exibir GRUB, você poderá usar o botão reiniciar no console serial da VM para reiniciar a VM e exibir o GRUB.

![O botão de reinicialização do console serial do Linux](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-bar.png)

## <a name="general-grub-access"></a>Acesso geral ao GRUB
Para acessar o GRUB, reinicialize sua VM enquanto o painel do console serial estiver aberto. Algumas distribuições exigem entrada de teclado para mostrar o GRUB e outras automaticamente mostram o GRUB por alguns segundos para permitir que a entrada do teclado do usuário cancele o tempo limite.

Para poder acessar o modo de usuário único, você deseja garantir que o GRUB esteja habilitado em sua VM. Dependendo da sua distribuição, pode ser necessário algum trabalho de configuração para garantir que o GRUB esteja habilitado. Para obter informações específicas de distribuição, consulte a próxima seção.

### <a name="restart-your-vm-to-access-grub-in-serial-console"></a>Reiniciar a VM para acessar o GRUB no Console Serial
Você pode reiniciar a VM no console serial passando o mouse sobre o botão **reiniciar** e, em seguida, selecionando **reiniciar VM**. Uma notificação sobre a reinicialização é exibida na parte inferior do painel.

Você também pode reiniciar a VM executando um comando SysRq "b" se [SysRq](./serial-console-nmi-sysrq.md) estiver habilitado. Para saber o que esperar do GRUB ao reinicializar, consulte as instruções específicas de distribuição nas próximas seções.

![Reinicialização do console serial do Linux](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-ubuntu.gif)

## <a name="general-single-user-mode-access"></a>Acesso geral ao modo de usuário único
Talvez seja necessário acesso manual ao modo de usuário único quando você não tiver configurado uma conta com autenticação de senha. Modifique a configuração do GRUB para entrar manualmente no modo de usuário único. Depois de fazer isso, consulte a seção "usar o modo de usuário único para redefinir ou adicionar uma senha" para obter mais instruções.

Se a VM não puder ser inicializada, as distribuições geralmente o descartarão automaticamente no modo de usuário único ou no modo de emergência. Outras distribuições, no entanto, exigem configuração adicional, como a configuração de uma senha raiz, antes que elas possam descartá-lo no modo de usuário único ou de emergência automaticamente.

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>Usar o modo de usuário único para redefinir ou adicionar uma senha
Depois que você estiver no modo de usuário único, adicione um novo usuário com privilégios sudo fazendo o seguinte:
1. Execute `useradd <username>` para adicionar um usuário.
1. Execute `sudo usermod -a -G sudo <username>` para conceder os novos privilégios de raiz de usuário.
1. Use `passwd <username>` para definir a senha do novo usuário. Em seguida, você pode entrar como o novo usuário.


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Acesso para Red Hat Enterprise Linux (RHEL)
Se o RHEL não puder ser inicializado normalmente, ele o descartará automaticamente no modo de usuário único. No entanto, se você não tiver configurado o acesso de raiz para o modo de usuário único, você não terá uma senha raiz e não poderá entrar. Há uma solução alternativa (consulte a seção "inserir manualmente o modo de usuário único no RHEL"), mas sugerimos que você configure o acesso à raiz inicialmente.

### <a name="grub-access-in-rhel"></a>Acesso ao GRUB no RHEL
O RHEL vem com o GRUB habilitado pronto para uso. Para entrar no GRUB, reinicialize sua VM executando o `sudo reboot` e pressione qualquer tecla. O painel GRUB deve ser exibido. Se não estiver, verifique se as seguintes linhas estão presentes no arquivo GRUB ( `/etc/default/grub` ):

**Para RHEL 8**

>[!NOTE]
> O Red Hat recomenda o uso de GruBBy para configurar parâmetros de linha de comando do kernel no RHEL 8 +. No momento, não é possível atualizar o tempo limite do grub e os parâmetros do terminal usando GruBBy. Para modificar a atualização do argumento GRUB_CMDLINE_LINUX para todas as entradas de inicialização, execute `grubby --update-kernel=ALL --args="console=ttyS0,115200 console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"` . Mais detalhes estão disponíveis [aqui](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/managing_monitoring_and_updating_the_kernel/configuring-kernel-command-line-parameters_managing-monitoring-and-updating-the-kernel).

```
GRUB_TIMEOUT=5
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
```

**Para RHEL 7**

```
GRUB_TIMEOUT=5
GRUB_TERMINAL_OUTPUT="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 net.ifnames=0"
```

> [!NOTE]
> A Red Hat também fornece documentação para inicialização no modo de resgate, modo de emergência ou modo de depuração e para redefinir a senha raiz. Para obter instruções, consulte [edição de menu do terminal durante a inicialização](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Configurar o acesso à raiz para o modo de usuário único no RHEL
O usuário raiz está desabilitado por padrão. O modo de usuário único no RHEL requer que o usuário raiz esteja habilitado. Se você precisar habilitar o modo de usuário único, use as seguintes instruções:

1. Entre no sistema Red Hat via SSH.
1. Mude para raiz.
1. Habilite a senha para o usuário raiz fazendo o seguinte:
    * Execute `passwd root` (defina uma senha raiz forte).
1. Verifique se o usuário raiz pode entrar somente via ttyS0 fazendo o seguinte: a. Execute `edit /etc/ssh/sshd_config` e verifique se PermitRootLogIn está definido como `no` .
    b. Execute `edit /etc/securetty file` para permitir a entrada somente via ttyS0.

Agora, se o sistema for inicializado no modo de usuário único, você poderá entrar com a senha raiz.

Como alternativa, para o RHEL 7.4 + ou 6,9 +, para habilitar o modo de usuário único nos prompts do GRUB, consulte [inicializando no modo de usuário único](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single).

### <a name="manually-enter-single-user-mode-in-rhel"></a>Inserir manualmente o modo de usuário único no RHEL
Se você configurou o acesso de GRUB e raiz usando as instruções anteriores, você pode inserir o modo de usuário único fazendo o seguinte:

1. Para entrar no GRUB, pressione ESC ao reiniciar a VM.
1. No GRUB, pressione E para editar o sistema operacional no qual você deseja inicializar. O sistema operacional geralmente é listado na primeira linha.
1. Localize a linha de kernel. No Azure, ele começa com *linux16*.
1. Pressione Ctrl + E para ir para o final da linha.
1. No final da linha, adicione *Systemed. Unit = resgate. Target*.

    Esta ação o inicia no modo de usuário único. Se você quiser usar o modo de emergência, adicione *Systemed. Unit = emergência. Target* ao final da linha (em vez de *systemd. Unit = resgate. Target*).

1. Pressione CTRL + X para sair e reinicializar com as configurações aplicadas.

   Você será solicitado a fornecer a senha de administrador antes de poder entrar no modo de usuário único. Essa senha é aquela que você criou nas instruções anteriores.

    ![Imagem animada mostrando uma interface de linha de comando. O usuário seleciona um servidor, localiza o final da linha de kernel e, em seguida, insere o texto especificado.](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Entrar no modo de usuário único sem a conta raiz habilitada no RHEL
Se você não tiver habilitado o usuário raiz seguindo as instruções anteriores, você ainda poderá redefinir sua senha raiz fazendo o seguinte:

> [!NOTE]
> Se você estiver usando o SELinux, ao redefinir a senha raiz, certifique-se de seguir as etapas adicionais descritas na [documentação do Red Hat](https://aka.ms/rhel7grubterminal).

1. Para entrar no GRUB, pressione ESC ao reiniciar a VM.

1. No GRUB, pressione E para editar o sistema operacional no qual você deseja inicializar. O sistema operacional geralmente é listado na primeira linha.
1. Localize a linha de kernel. No Azure, ele começa com *linux16*.
1. No final da linha, adicione *Rd. Break* ao final da linha. Deixe um espaço entre a linha de kernel e *Rd. Break*.

    Essa ação interrompe o processo de inicialização antes de o controle ser passado de `initramfs` para `systemd` , conforme descrito na [documentação do Red Hat](https://aka.ms/rhel7rootpassword).
1. Pressione CTRL + X para sair e reinicializar com as configurações aplicadas.

   Após a reinicialização, você será colocado no modo de emergência com um sistema de arquivos somente leitura.

1. No Shell, digite `mount -o remount,rw /sysroot` para remontar o sistema de arquivos raiz com permissões de leitura/gravação.
1. Depois de inicializar o modo de usuário único, digite `chroot /sysroot` para alternar para o `sysroot` com Jail Break.
1. Agora você está na raiz. Você pode redefinir sua senha raiz digitando `passwd` e, em seguida, usar as instruções anteriores para entrar no modo de usuário único.
1. Depois de terminar, digite `reboot -f` para reinicializar.

![Imagem animada mostrando uma interface de linha de comando. O usuário seleciona um servidor, localiza o final da linha de kernel e insere os comandos especificados.](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> [!NOTE]
> Executar as instruções anteriores o descartará no Shell de emergência para que você também possa executar tarefas como edição `fstab` . No entanto, normalmente sugerimos que você Redefina sua senha raiz e use-a para entrar no modo de usuário único.

## <a name="access-for-centos"></a>Acesso para o CentOS
Assim como Red Hat Enterprise Linux, o modo de usuário único no CentOS requer GRUB e o usuário raiz a ser habilitado.

### <a name="grub-access-in-centos"></a>Acesso ao GRUB no CentOS
O CentOS vem com o GRUB habilitado pronto para uso. Para entrar no GRUB, reinicialize sua VM digitando `sudo reboot` e pressione qualquer tecla. Essa ação exibe o painel GRUB.

### <a name="single-user-mode-in-centos"></a>Modo de usuário único no CentOS
Para habilitar o modo de usuário único no CentOS, siga as instruções anteriores para RHEL.

## <a name="access-for-ubuntu"></a>Acesso para Ubuntu
As imagens do Ubuntu não exigem uma senha raiz. Se o sistema for inicializado no modo de usuário único, você poderá usá-lo sem credenciais adicionais.

### <a name="grub-access-in-ubuntu"></a>Acesso ao GRUB no Ubuntu
Para acessar o GRUB, pressione e segure ESC enquanto a VM estiver sendo inicializada.

Por padrão, imagens do Ubuntu podem não exibir automaticamente o painel GRUB. Você pode alterar a configuração fazendo o seguinte:
1. Em um editor de texto, abra o arquivo */etc/default/grub.d/50-cloudimg-Settings.cfg* .

1. Altere o `GRUB_TIMEOUT` valor para um valor diferente de zero.
1. Em um editor de texto, abra */etc/default/grub*.
1. Comente a `GRUB_HIDDEN_TIMEOUT=1` linha.
1. Verifique se há uma `GRUB_TIMEOUT_STYLE=menu` linha.
1. Execute `sudo update-grub`.

### <a name="single-user-mode-in-ubuntu"></a>Modo de usuário único no Ubuntu
Se o Ubuntu não puder ser inicializado normalmente, ele o descartará automaticamente no modo de usuário único. Para entrar no modo de usuário único manualmente, faça o seguinte:

1. No GRUB, pressione E para editar sua entrada de inicialização (a entrada do Ubuntu).
1. Procure a linha que começa com o *Linux*e procure *ro*.
1. Adicione *single* depois de *ro*, garantindo que há um espaço antes e depois de *único*.
1. Pressione CTRL + X para reinicializar com essas configurações e insira o modo de usuário único.

### <a name="use-grub-to-invoke-bash-in-ubuntu"></a>Usar GRUB para invocar o bash no Ubuntu
Depois de tentar as instruções anteriores, pode haver uma situação (como uma senha raiz esquecida) em que você ainda não consegue acessar o modo de usuário único em sua VM do Ubuntu. Você também pode instruir o kernel a ser executado `/bin/bash` como init, em vez da inicialização do sistema. Essa ação fornece um shell bash e permite a manutenção do sistema. Use as instruções a seguir:

1. No GRUB, pressione E para editar sua entrada de inicialização (a entrada do Ubuntu).

1. Procure a linha que começa com o *Linux*e procure *ro*.
1. Substitua *ro* por *RW init =/bin/bash*.

    Essa ação monta o sistema de arquivos como leitura e gravação e usa `/bin/bash` como o processo de inicialização.
1. Pressione CTRL + X para reinicializar com essas configurações.

## <a name="access-for-coreos"></a>Acesso para CoreOS
O modo de usuário único no CoreOS requer que o GRUB esteja habilitado.

### <a name="grub-access-in-coreos"></a>Acesso ao GRUB no CoreOS
Para acessar o GRUB, pressione qualquer tecla enquanto sua VM estiver inicializando.

### <a name="single-user-mode-in-coreos"></a>Modo de usuário único no CoreOS
Se o CoreOS não puder ser inicializado normalmente, ele o descartará automaticamente no modo de usuário único. Para entrar no modo de usuário único manualmente, faça o seguinte:

1. No GRUB, pressione E para editar sua entrada de inicialização.

1. Procure a linha que começa com *Linux $*. Deve haver duas instâncias da linha, cada uma encapsulada em uma diferente *se... cláusula else* .
1. Acrescente *Coreos. AutoLogin = ttyS0* ao final de cada linha do *Linux $* .
1. Pressione CTRL + X para reinicializar com essas configurações e insira o modo de usuário único.

## <a name="access-for-suse-sles"></a>Acesso para SUSE SLES
Imagens mais recentes do SLES 12 SP3 + permitir o acesso por meio do console serial se o sistema for inicializado no modo de emergência.

### <a name="grub-access-in-suse-sles"></a>Acesso ao GRUB no SUSE SLES
O acesso de GRUB no SLES requer uma configuração de carregador de configurações via YaST. Para criar a configuração, faça o seguinte:

1. Use o SSH para entrar em sua VM SLES e, em seguida, execute `sudo yast bootloader` . Pressione Tab, pressione Enter e, em seguida, use as teclas de direção para navegar pelo menu.

1. Vá para **parâmetros de kernel**e marque a caixa de seleção **usar console serial** .
1. Adicione `serial --unit=0 --speed=9600 --parity=no` aos argumentos do **console** .
1. Pressione F10 para salvar as configurações e sair.
1. Para entrar no GRUB, reinicialize sua VM e pressione qualquer tecla durante a sequência de inicialização para manter o painel GRUB exibido.

    O tempo limite padrão para GRUB é **1s**. Você pode modificar essa configuração alterando a `GRUB_TIMEOUT` variável no arquivo */etc/default/grub* .

![Inicializando a configuração do carregador de inicialização](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Modo de usuário único no SUSE SLES
Se o SLES não puder ser inicializado normalmente, você será automaticamente Descartado no Shell de emergência. Para inserir o Shell de emergência manualmente, faça o seguinte:

1. No GRUB, pressione E para editar sua entrada de inicialização (a entrada SLES).

1. Procure a linha de kernel que começa com o *Linux*.
1. Acrescentar *Systemed. Unit = emergência. Target* ao final da linha de kernel.
1. Pressione CTRL + X para reinicializar com essas configurações e insira o Shell de emergência.

   > [!NOTE]
   > Essa ação o descartará no Shell de emergência com um sistema de arquivos somente leitura. Para editar todos os arquivos, monte novamente o sistema de arquivos com permissões de leitura/gravação. Para fazer isso, insira `mount -o remount,rw /` no Shell.

## <a name="access-for-oracle-linux"></a>Acesso para o Oracle Linux
Assim como Red Hat Enterprise Linux, o modo de usuário único no Oracle Linux requer GRUB e o usuário raiz a ser habilitado.

### <a name="grub-access-in-oracle-linux"></a>Acesso ao GRUB no Oracle Linux
O Oracle Linux vem com o GRUB habilitado pronto para uso. Para entrar no GRUB, reinicialize sua VM executando o `sudo reboot` e pressione ESC. Essa ação exibe o painel GRUB. Se o painel GRUB não for exibido, verifique se o valor da `GRUB_TERMINAL` linha contém o *console serial* (ou seja, `GRUB_TERMINAL="serial console"` ). Recompile o GRUB com `grub2-mkconfig -o /boot/grub/grub.cfg` .

### <a name="single-user-mode-in-oracle-linux"></a>Modo de usuário único no Oracle Linux
Para habilitar o modo de usuário único no Oracle Linux, siga as instruções anteriores para RHEL.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o console serial, consulte:
* [Documentação do console serial do Linux](serial-console-linux.md)
* [Usar o console serial para habilitar o GRUB em várias distribuições](http://linuxonazure.azurewebsites.net/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)
* [Usar o console serial para chamadas NMI e SysRq](serial-console-nmi-sysrq.md)
* [Console serial para VMs do Windows](serial-console-windows.md)
* [Diagnóstico de inicialização](boot-diagnostics.md)
