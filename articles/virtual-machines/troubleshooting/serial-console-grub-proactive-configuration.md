---
title: Configuração do GRUB proativo do console serial do Azure | Microsoft Docs
description: Configure o GRUB em várias distribuições, permitindo o acesso no modo de usuário único e de recuperação em máquinas virtuais do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/10/2019
ms.author: mimckitt
ms.openlocfilehash: aba47500400004c1d6a7044a266bad6f20d5d9c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91360541"
---
# <a name="proactively-ensuring-you-have-access-to-grub-and-sysrq-could-save-you-lots-of-down-time"></a>Garantir de maneira proativa que você tenha acesso ao GRUB e ao SysRq pode economizar muito tempo de inatividade

Ter acesso ao console serial e ao GRUB melhora os tempos de recuperação de sua máquina virtual Linux IaaS na maioria dos casos. O GRUB oferece opções de recuperação que, de outra forma, demorariam mais para recuperar a VM. 


Há muitos motivos para executar uma recuperação de VM e eles podem ser atribuídos a cenários como:

   - Sistemas de arquivos corrompidos/kernel/MBR (registro mestre de inicialização)
   - Upgrades de kernel com falha
   - Parâmetros incorretos de kernel do GRUB
   - Configurações incorretas de fstab
   - Configurações de firewall
   - Senha perdida
   - Arquivos de configuração de sshd desconfigurados
   - Configurações de rede

 Muitos outros cenários como especificado [aqui](./serial-console-linux.md#common-scenarios-for-accessing-the-serial-console).

Verifique se você pode acessar o GRUB e o Console serial em suas VMs implantadas no Azure. 

Se você ainda não conhece o console serial, confira [este link](./serial-console-linux.md).

> [!TIP]
> Certifique-se de fazer backups de arquivos antes de fazer alterações.

Assista ao vídeo abaixo para ver como você pode recuperar rapidamente uma VM do Linux quando tiver acesso ao GRUB.

[Vídeo de recuperação da VM do Linux](https://youtu.be/KevOc3d_SG4)

Há vários métodos para ajudar na recuperação de VMs do Linux. Em um ambiente de nuvem, este processo costuma ser complexo.
Há um progresso contínuo para a criação de ferramentas e recursos a fim de garantir que os serviços sejam recuperados rapidamente.

Com o console serial do Azure, você pode interagir com sua VM do Linux como se estivesse no console de um sistema.

Você pode manipular muitos arquivos de configuração, incluindo como o kernel será inicializado. 

Os administradores de sistemas Linux/Unix mais experientes apreciarão os **modos de usuário único** e **de emergência**, que podem ser acessados no console serial do Azure, tornando a troca de disco e a exclusão de VM redundante para muitos cenários de recuperação.

O método de recuperação depende do problema ocorrido, por exemplo, uma senha perdida ou esquecida pode ser redefinida por meio das opções do portal do Azure -> **Redefinir senha**. O recurso para **Redefinir senha** é conhecido como uma extensão e se comunica com o agente convidado do Linux.

Outras extensões, como o script personalizado, estão disponíveis. No entanto essas opções exigem que o **waagent** do Linux esteja ativo e em um estado íntegro, o que nem sempre é o caso.

![status do agente](./media/virtual-machines-serial-console/agent-status.png)


Garantir que você tenha acesso ao console serial do Azure e GRUB significa que uma alteração de senha ou uma configuração incorreta pode ser corrigida em questão de minutos em vez de horas. Você poderia até mesmo forçar a VM a inicializar de um kernel alternativo caso tivesse vários kernels em disco no cenário em que o kernel primário estava corrompido.

![vários kernels](./media/virtual-machines-serial-console/more-kernel.png)

## <a name="suggested-order-of-recovery-methods"></a>Ordem sugerida de métodos de recuperação:

- Console serial do Azure

- Troca de disco – Pode ser automatizada usando:

   - [Scripts de recuperação do PowerShell](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
   - [Scripts de recuperação de bash](https://github.com/sribs/azure-support-scripts)

- Método herdado

## <a name="disk-swap-video"></a>Vídeo de troca de disco:

Caso não tenha acesso ao GRUB assista [este](https://youtu.be/m5t0GZ5oGAc) vídeo e veja como é possível automatizar facilmente o procedimento de troca de disco para recuperar a VM.

## <a name="challenges"></a>Desafios:

Nem todas as VMs do Azure para Linux são configuradas por padrão para o acesso de GRUB e nenhuma delas está configurada para ser interrompida com os comandos sysrq. Algumas distribuições mais antigas, como SLES 11, não estão configuradas para exibir o prompt de logon no console serial do Azure.

Neste artigo, vamos analisar várias distribuições do Linux e configurações de documentos sobre como disponibilizar o GRUB.




## <a name="how-to-configure-linux-vm-to-accept-sysrq-keys"></a>Como configurar a VM do Linux para aceitar chaves de SysRq
A chave sysrq é habilitada em algumas distribuições mais recentes do Linux por padrão, embora em outras possa ser configurada para aceitar valores apenas para algumas funções de sysrq.
Em distribuições mais antigas, ela pode ser desabilitada por completo.

O recurso SysRq é útil para reinicializar uma VM com falha ou que não esteja respondendo diretamente pelo console serial do Azure. Também é útil para obter acesso ao menu GRUB, reiniciando, como alternativa, uma VM de outra janela do portal; caso contrário, a sessão de ssh poderia descartar a conexão do console atual, expirando os tempos limite do GRUB, que são usados para exibir o menu do GRUB.
A VM deve ser configurada para aceitar um valor de 1 para o parâmetro de kernel, que habilita todas as funções de sysrq ou 128, o que permite reinicializar/desligar.


[Habilitar vídeo de sysrq](https://youtu.be/0doqFRrHz_Mc)


Para configurar a VM para aceitar uma reinicialização por meio de comandos de SysRq no portal do Azure, você precisará definir um valor de 1 para o parâmetro de kernel kernel.sysrq.

Para que essa configuração persista uma reinicialização, adicione uma entrada ao arquivo **sysctl.conf**:

`echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Para configurar o parâmetro de kernel dinamicamente:

`sysctl -w kernel.sysrq=1`

Se você não tiver acesso à **raiz** ou o sudo for interrompido, não será possível configurar o sysrq a partir de um prompt do shell.

Você pode habilitar o sysrq neste cenário usando o portal do Azure. Esse método pode ser benéfico se o arquivo **sudoers.d/waagent** estiver quebrado ou caso seja excluído.

O uso do recurso Operações-> Executar comando-> RunShellScript do portal do Azure exige que o processo waagent seja íntegro. Em seguida, você pode injetar este comando para habilitar o sysrq:

`sysctl -w kernel.sysrq=1 ; echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Conforme mostrado aqui: ![enable sysrq2](./media/virtual-machines-serial-console/enabling-sysrq-2.png)

Depois de concluído, você poderá tentar acessar o **sysrq** e deverá ver que é possível fazer uma reinicialização.

![enable sysrq3](./media/virtual-machines-serial-console/enabling-sysrq-3.png)

Selecione **Reinicialização** e o comando **Send SysRq**.

![enable sysrq4](./media/virtual-machines-serial-console/enabling-sysrq-4.png)

O sistema deve registrar uma mensagem de redefinição como esta:

![enable sysrq5](./media/virtual-machines-serial-console/enabling-sysrq-5.png)


## <a name="ubuntu-grub-configuration"></a>Configuração de GRUB do Ubuntu

Por padrão, você deve ser capaz de acessar o GRUB mantendo a tecla **ESC** pressionada durante a inicialização da VM. Se o menu GRUB não aparecer, você poderá forçar e manter o menu GRUB na tela no console serial do Azure usando uma das opções abaixo.

**Opção 1**: força o GRUB a ser exibido na tela. 

Atualize o arquivo/etc/default/grub.d/50-cloudimg-settings.cfg para manter o menu do GRUB na tela durante o tempo limite especificado.
Você não precisa pressionar **ESC**, pois o GRUB será exibido imediatamente.

```
GRUB_TIMEOUT=0

change to

GRUB_TIMEOUT=5
```

**Opção 2**: permite que a tecla **ESC** seja pressionada antes da inicialização.

Um comportamento semelhante pode ocorrer ao fazer alterações no arquivo/etc/default/grub e observar um tempo limite de três segundos ao pressionar **Esc**.


Comente estas duas linhas:

```
#GRUB_HIDDEN_TIMEOUT=0
#GRUB_HIDDEN_TIMEOUT_QUIET=true
```
e adicione esta:

```
GRUB_TIMEOUT_STYLE=countdown
```


## <a name="ubuntu-1204"></a>Ubuntu 12\.04

O Ubuntu 12.04 permitirá o acesso ao console serial, mas não oferece a capacidade de interagir. Um prompt de **logon:** não é exibido.


Para 12.04, para obter um prompt de **logon:**
1. Crie um arquivo chamado /etc/init/ttyS0.conf contendo o seguinte texto:

    ```
    # ttyS0 - getty
    #
    # This service maintains a getty on ttyS0 from the point the system is
    # started until it is shut down again.
    start on stopped rc RUNLEVEL=[12345]
    stop on runlevel [!12345]
    
    respawn
    exec /sbin/getty -L 115200 ttyS0 vt102
    ```    

2. Peça para começar a iniciar o getty.     
    ```
    sudo start ttyS0
    ```
 
As configurações necessárias para definir o console serial nas versões do Ubuntu podem ser encontradas [aqui](https://help.ubuntu.com/community/SerialConsoleHowto).

## <a name="ubuntu-recovery-mode"></a>Modo de recuperação do Ubuntu

As opções adicionais de recuperação e limpeza estão disponíveis para o Ubuntu pelo GRUB. No entanto, essas configurações só estarão acessíveis se você configurar os parâmetros do kernel adequadamente.
Deixar de configurar esse parâmetro de inicialização do kernel forçaria o envio do menu de recuperação para o Diagnóstico do Azure e não para o console serial do Azure.
Você pode acessar o menu de recuperação do Ubuntu seguindo estas etapas:

Interrompa o processo de inicialização e acessar o menu do GRUB.

Selecione opções avançadas para Ubuntu e pressione Enter.

![Captura de tela mostra o Console serial com opções avançadas para Ubuntu selecionado.](./media/virtual-machines-serial-console/ubunturec1.png)

Selecione a linha que exibe *(modo de recuperação)* e não pressione Enter, mas "e".

![Captura de tela mostra a Console serial com uma versão do modo de recuperação selecionada.](./media/virtual-machines-serial-console/ubunturec2.png)

Localize a linha que carregará o kernel e substitua o último parâmetro **nomodeset** com destino como **console = ttyS0**.

```
linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery nomodeset

change to

linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery console=ttyS0
```

![Captura de tela mostra a Console serial com o valor alterado.](./media/virtual-machines-serial-console/ubunturec3.png)

Pressione **Ctrl-x** para iniciar e carregar o kernel.
Se tudo correr bem, você verá essas opções adicionais, que podem ajudar a executar outras opções de recuperação.

![Captura de tela mostra a Console serial no menu recuperação, que oferece opções de recuperação adicionais.](./media/virtual-machines-serial-console/ubunturec4.png)


## <a name="red-hat-grub-configuration"></a>Configuração do GRUB para Red Hat

## <a name="red-hat-74-grub-configuration"></a>Configuração do GRUB para Red Hat 7\.4\+
A configuração padrão /etc/default/grub nessas versões está configurada adequadamente.

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_SERIAL_COMMAND="serial"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

Habilitar a chave SysRq

```
sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq
```

## <a name="red-hat-72-and-73-grub-configuration"></a>Configuração de GRUB para Red Hat 7\.2 e 7\.3
O arquivo a ser modificado é /etc/default/grub, uma configuração padrão é parecida com este exemplo:

```
GRUB_TIMEOUT=1
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

Altere as linhas a seguir em /etc/default/grub.

```
GRUB_TIMEOUT=1 

to

GRUB_TIMEOUT=5
```


```
GRUB_TERMINAL_OUTPUT="console"

to

GRUB_TERMINAL="serial console"
```

Adicione também esta linha:

```
GRUB_SERIAL_COMMAND=”serial –speed=115200 –unit=0 –word=8 –parity=no –stop=1″
```

/etc/default/grub agora deve ser semelhante a este exemplo:

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```
 
Conclua e atualize a configuração do grub usando

`grub2-mkconfig -o /boot/grub2/grub.cfg`

Defina o parâmetro de kernel de SysRq:

`sysctl -w kernel.sysrq = 1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq`

Como alternativa, você pode configurar o GRUB e o SysRq usando uma única linha no shell ou por meio de Executar Comando. Faça backup de seus arquivos antes de executar este comando:


`cp /etc/default/grub /etc/default/grub.bak; sed -i 's/GRUB_TIMEOUT=1/GRUB_TIMEOUT=5/g' /etc/default/grub; sed -i 's/GRUB_TERMINAL_OUTPUT="console"/GRUB_TERMINAL="serial console"/g' /etc/default/grub; echo "GRUB_SERIAL_COMMAND=\"serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1\"" >> /etc/default/grub;grub2-mkconfig -o /boot/grub2/grub.cfg;sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 /etc/sysctl.conf;sysctl -a | grep -i sysrq`


## <a name="red-hat-6x-grub-configuration"></a>Configuração de GRUB para Red Hat 6\.x
O arquivo a ser modificado é /boot/grub/grub.conf. O valor `timeout` determinará por quanto tempo o GRUB é mostrado.

```
#boot=/dev/vda
default=0
timeout=15
splashimage=(hd0,0)/grub/splash.xpm.gz
#hiddenmenu
serial --unit=0 --speed=9600
terminal serial
terminal --timeout=5 serial console
```


A última linha *terminal – timeout = 5 serial console* aumentará o tempo limite do **GRUB** adicionando um prompt de cinco segundos que exibe **Pressione qualquer tecla para continuar.**

![Captura de tela mostra um console com saída.](./media/virtual-machines-serial-console/rh6-1.png)

O menu GRUB deve aparecer na tela para o tempo limite configurado = 15 sem a necessidade de pressionar Esc. Certifique-se de clicar no console no navegador para ativar o menu e selecionar o kernel necessário.

![Captura de tela mostra um console com duas opções do Linux.](./media/virtual-machines-serial-console/rh6-2.png)

## <a name="suse"></a>SuSE

## <a name="sles-12-sp1"></a>SLES 12 sp1
Use o carregador de inicialização de YaST de acordo com os [documentos](./serial-console-grub-single-user-mode.md#grub-access-in-suse-sles) oficiais.

Ou adicione/altere para /etc/default/grub os seguintes parâmetros:

```
GRUB_TERMINAL=serial
GRUB_TIMEOUT=5
GRUB_SERIAL_COMMAND="serial --unit=0 --speed=9600 --parity=no"

```
Verifique se ttyS0 é usado no GRUB_CMDLINE_LINUX ou GRUB_CMDLINE_LINUX_DEFAULT.

```
GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0,9600n"
```

Recriar o grub.cfg:

`grub2-mkconfig -o /boot/grub2/grub.cfg`


## <a name="sles-11-sp4"></a>SLES 11 SP4 
O console serial aparece e exibe mensagens de inicialização, mas não exibe um prompt de **logon:**

Abra uma sessão de ssh na VM e atualize o arquivo **/etc/inittab** ao remover o comentário desta linha:

```
#S0:12345:respawn:/sbin/agetty -L 9600 ttyS0 vt102
```

Execute o comando: 

`telinit q`

Para habilitar o GRUB, as seguintes alterações devem ser feitas em /boot/grub/menu.lst. 

```
timeout 5
serial --unit=0 --speed=9600 --parity=no
terminal --timeout=5 serial console

root (hd0,0)
kernel /boot/vmlinuz-3.0.101-108.74-default root=/dev/disk/by-uuid/ab6b62bb--
1a8c-45eb-96b1-1fbc535b9265 disk=/dev/sda  USE_BY_UUID_DEVICE_NAMES=1 earlyprinttk=ttyS0 console=ttyS0 rootdelay=300  showopts vga=0x314
```

 Essa configuração permitirá que a mensagem **Pressione qualquer tecla para continuar** apareça no console por cinco segundos. 

Ela exibirá o menu GRUB por mais cinco segundos. Ao pressionar a seta para baixo, você interromperá o contador e selecionará um kernel que deseja inicializar para acrescentar a palavra-chave **único** para o modo de usuário único que requer a definição da senha raiz.

Acrescentar o comando **init=/bin/bash** carregará o kernel, mas garantirá que o programa init seja substituído por um shell de bash.

Você poderá acessar um shell sem precisar inserir uma senha. Proceda para atualizar a senha para contas do Linux ou fazer outras alterações de configuração.


## <a name="force-the-kernel-to-a-bash-prompt"></a>Forçar o kernel a um prompt de bash
O acesso ao GRUB permite interromper o processo de inicialização. Essa interação é útil para muitos procedimentos de recuperação.
Caso não tenha a senha raiz e o usuário único exigir que você tenha uma, é possível inicializar o kernel substituindo o programa init por um prompt de bash. Essa interrupção pode ser obtida acrescentando init=/bin/bash à linha de inicialização do kernel.

![Captura de tela mostra um console com a linha de inicialização atualizada.](./media/virtual-machines-serial-console/bash1.png)

Remonte o sistema de arquivos /(raiz) RW usando o comando:

`mount -o remount,rw /`

![Captura de tela mostra um console com uma ação de remontagem.](./media/virtual-machines-serial-console/bash2.png)


Agora você pode alterar a senha raiz ou fazer muitas outras alterações de configuração do Linux.

![Captura de tela mostra um console em que você pode alterar a senha raiz e outras configurações.](./media/virtual-machines-serial-console/bash3.png)

Reinicie a VM com: 

`/sbin/reboot -f`




## <a name="single-user-mode"></a>Modo de usuário único

Como alternativa, talvez seja necessário acessar a VM no modo de usuário único ou de emergência. Selecione o kernel que você deseja inicializar ou interromper usando as teclas de direção.
Insira o modo desejado acrescentando a palavra-chave **único** ou **1** à linha de inicialização do kernel. Em sistemas RHEL, você também pode acrescentar **rd.break**.

Para obter mais informações sobre como acessar o modo de usuário único, consulte [este documento](./serial-console-grub-single-user-mode.md#general-single-user-mode-access). 


![single_user_ubuntu](./media/virtual-machines-serial-console/single-user-ubuntu.png)


## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre o [Console serial do Azure]( ./serial-console-linux.md).