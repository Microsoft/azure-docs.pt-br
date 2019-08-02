---
title: Console Serial do Azure GRUB e Modo de Usuário Único | Microsoft Docs
description: Usando o Console Serial para grub em máquinas virtuais do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 5ff87e761bbe5243a478a00ca90d4be03fdbc6d4
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706575"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Usar o Console Serial para acessar GRUB e Modo de Usuário Único
O GRUB é o GRand Unified Bootloader. Com o GRUB, você consegue modificar sua configuração de inicialização para inicializar no modo de usuário único, entre outras coisas.

O modo de usuário único é um ambiente mínimo com o mínimo de funcionalidade. Ele pode ser útil para investigar problemas de inicialização, do sistema de arquivos ou de rede. Menos serviços podem ser executados em segundo plano e, dependendo do nível de execução, um sistema de arquivos talvez nem possa ser montado automaticamente.

O modo de usuário único também é útil em situações nas quais a VM só pode ser configurada para aceitar chaves SSH para o logon. Nesse caso, você poderá usar o modo de usuário único para criar uma conta com a autenticação de senha.

Para entrar no modo de usuário único, você precisará inserir o GRUB quando a VM estiver sendo inicializada e modificar a configuração de inicialização no GRUB. Isso pode ser feito com o console serial da VM.

## <a name="general-grub-access"></a>Acesso geral ao GRUB
Para acessar o GRUB, você precisará reiniciar a VM mantendo aberta a folha do console serial. Algumas distribuições exigirão a entrada do teclado para mostrar o GRUB, enquanto outras mostrarão o GRUB automaticamente por alguns segundos e permitirão que a entrada de teclado do usuário cancele o tempo limite. 

Você desejará garantir que o GRUB esteja habilitado na sua VM para que seja possível acessar o modo de usuário único. Dependendo da sua distribuição, pode haver algum trabalho de configuração para garantir que o GRUB esteja habilitado. Informações específicas da distribuição estão disponíveis abaixo.

### <a name="reboot-your-vm-to-access-grub-in-serial-console"></a>Reiniciar a VM para acessar o GRUB no console serial
A reinicialização da VM com a folha do console serial aberta poderá ser feita com um comando SysRq `'b'` se [SysRq](./serial-console-nmi-sysrq.md) estiver habilitado ou clicando no botão Reiniciar na folha Visão geral (abra a VM em uma nova guia do navegador para reinicializar sem fechar a folha do console serial). Siga as instruções específicas da distribuição abaixo para saber o que esperar do GRUB ao reinicializar.

## <a name="general-single-user-mode-access"></a>Acesso geral ao modo de usuário único
O acesso manual ao modo de usuário único poderá ser necessário quando você não configurar uma conta com autenticação de senha. Você precisará modificar a configuração do GRUB para inserir manualmente o modo de usuário único. Depois que fizer isso, confira Usar o modo de usuário único para redefinir ou adicionar uma senha para obter mais instruções.

Quando a VM não puder ser reinicializada, geralmente as distribuições escolherão automaticamente o modo de usuário único ou o modo de emergência. Outras, no entanto, exigirão uma configuração adicional antes de escolher automaticamente o modo de usuário único ou o modo de emergência (como a configuração de uma senha raiz).

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>Usar o modo de usuário único para redefinir ou adicionar uma senha
Quando você estiver no modo de usuário único, faça o seguinte para adicionar um novo usuário com privilégios sudo:
1. Execute `useradd <username>` para adicionar um usuário
1. Execute `sudo usermod -a -G sudo <username>` para conceder privilégios raiz ao novo usuário
1. Use `passwd <username>` para definir a senha do novo usuário. Em seguida, você poderá fazer logon como o novo usuário

## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Acesso para Red Hat Enterprise Linux (RHEL)
O RHEL alternará para o modo de usuário único automaticamente se ele não conseguir inicializar normalmente. No entanto, se não tiver configurado o acesso à raiz para o modo de usuário único, você não terá uma senha raiz e não conseguirá fazer logon. Há uma solução alternativa (consulte "Entrada manual no modo de usuário único" abaixo), mas a sugestão é configurar o acesso à raiz inicialmente.

### <a name="grub-access-in-rhel"></a>Acesso ao GRUB no RHEL
O RHEL vem com o GRUB habilitado pronto para uso. Para acessar o GRUB, reinicialize a VM com `sudo reboot` e pressione qualquer tecla. Você verá a tela GRUB aparecer.

> Observação: Red Hat também fornece documentação para inicializar no Modo de Recuperação, Modo de Emergência, Modo de Depuração e redefinindo a senha raiz. [Clique aqui para acessá-la](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Configurar o acesso à raiz para o modo de usuário único no RHEL
O modo de usuário único no RHEL requer que o usuário raiz seja habilitado, pois ele está desabilitado por padrão. Se você precisar habilitar o modo de usuário único, siga estas instruções:

1. Faça logon no sistema Red Hat via SSH
1. Alternar para raiz
1. Habilitar a senha para o usuário raiz 
    * `passwd root` (definir uma senha forte raiz)
1. Verifique se o usuário raiz só pode fazer logon por meio de ttyS0
    * `edit /etc/ssh/sshd_config` e verifique se PermitRootLogIn está definido como não
    * `edit /etc/securetty file` para permitir somente o logon por meio de ttyS0 

Agora, se o sistema for inicializado no modo de usuário único, você poderá se conectar por meio da senha raiz.

Como alternativa para RHEL 7.4 + ou 6.9 +, você pode habilitar o modo de usuário único nos prompts do GRUB. Confira as instruções [aqui](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)

### <a name="manually-enter-single-user-mode-in-rhel"></a>Acessar manualmente o modo de usuário único no RHEL
Se tiver configurado o GRUB e o acesso à raiz com as instruções acima, você poderá entrar no modo de usuário único com as instruções a seguir:

1. Pressione 'Esc' ao reiniciar a VM para acessar o GRUB
1. No GRUB, pressione 'e' para editar o sistema operacional selecionado no qual você deseja inicializar (normalmente, a primeira linha)
1. Localize a linha de kernel - no Azure, ela começará com `linux16`
1. Pressione Ctrl + E para ir até o final da linha
1. Adicione o seguinte ao final da linha: `systemd.unit=rescue.target`
    * Isso resultará na inicialização no modo de usuário único. Se você quiser usar o modo de emergência, adicione `systemd.unit=emergency.target` ao final da linha em vez de `systemd.unit=rescue.target`
1. Pressione Ctrl + X para sair e reinicializar com as configurações aplicadas
1. Será solicitada a senha de administrador antes de poder entrar no modo de usuário único - essa é a mesma senha que você criou nas instruções acima    

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Entrar no modo de usuário único sem conta raiz habilitada no RHEL
Se não percorrer as etapas acima para habilitar que o usuário raiz, você poderá ainda redefinir a senha raiz. Use as instruções a seguir:

> Observação: Se você estiver usando o SELinux, verifique se seguiu as etapas adicionais descritas na documentação do Red Hat [aqui](https://aka.ms/rhel7grubterminal) ao redefinir a senha raiz.

1. Pressione 'Esc' ao reiniciar a VM para acessar o GRUB
1. No GRUB, pressione 'e' para editar o sistema operacional selecionado no qual você deseja inicializar (normalmente, a primeira linha)
1. Localize a linha de kernel - no Azure, ela começará com `linux16`
1. Adicione `rd.break` ao final da linha, garantindo que exista um espaço antes de `rd.break` (consulte o exemplo a seguir)
    - Isso interromperá o processo de inicialização antes de o controle ser passado de `initramfs` a `systemd`, conforme descrito na documentação do Red Hat [aqui](https://aka.ms/rhel7rootpassword).
1. Pressione Ctrl + X para sair e reinicializar com as configurações aplicadas
1. Depois que você inicializar, ocorrerá a alternância para o modo de emergência com um sistema de arquivos somente leitura. Insira `mount -o remount,rw /sysroot` no shell para montar novamente o sistema de arquivos raiz com permissões de leitura/gravação
1. Depois que você inicializar no modo de usuário único, digite `chroot /sysroot` para alternar para o `sysroot` jail
1. Você agora é a raiz. Você pode redefinir sua senha raiz com `passwd` e, em seguida, usar as instruções acima para entrar no modo de usuário único. Digite `reboot -f` para reinicializar quando terminar.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> Observação: Executar as instruções acima o direcionará para o shell de emergência, portanto, você também poderá executar tarefas como editar `fstab`. No entanto, a sugestão geralmente aceita é redefinir sua senha raiz e usá-la para entrar no modo de usuário único. 


## <a name="access-for-centos"></a>Acesso para o CentOS
Muito semelhante ao Red Hat Enterprise Linux, o modo de usuário único no CentOS requer que o GRUB e o usuário raiz estejam habilitados. 

### <a name="grub-access-in-centos"></a>Acesso ao GRUB no CentOS
O CentOS vem com o GRUB habilitado pronto para uso. Para acessar o GRUB, reinicialize a VM com `sudo reboot` e pressione qualquer tecla. Você verá a tela GRUB aparecer.

### <a name="single-user-mode-in-centos"></a>Modo de usuário único no CentOS
Siga as instruções para RHEL acima para habilitar o modo de usuário único no CentOS.

## <a name="access-for-ubuntu"></a>Acesso para Ubuntu 
As imagens do Ubuntu não exigem uma senha raiz. Se o sistema for inicializado no modo de usuário único, você terá acesso sem credenciais adicionais. 

### <a name="grub-access-in-ubuntu"></a>Acesso ao GRUB no Ubuntu
Para acessar o GRUB, pressione e segure 'Esc' enquanto a VM está inicializando. 

Por padrão, as imagens do Ubuntu não mostram automaticamente a tela do GRUB. Isso pode ser alterado com as instruções a seguir:
1. Abra `/etc/default/grub.d/50-cloudimg-settings.cfg` em um editor de texto da sua escolha
1. Altere o valor `GRUB_TIMEOUT` para um valor diferente de zero
1. Abra `/etc/default/grub` em um editor de texto da sua escolha
1. Comente na linha `GRUB_HIDDEN_TIMEOUT=1`
1. Execute `sudo update-grub`

### <a name="single-user-mode-in-ubuntu"></a>Modo de usuário único no Ubuntu
O Ubuntu alternará para o modo de usuário único automaticamente se ele não conseguir inicializar normalmente. Para entrar manualmente no modo de usuário único, siga estas instruções:

1. No GRUB, pressione 'e' para editar sua entrada de inicialização (a entrada do Ubuntu)
1. Procure a linha que começa com `linux`, em seguida, procure `ro`
1. Adicione `single` após `ro`, garantindo que haja um espaço antes e depois de `single`
1. Pressione Ctrl + X para reinicializar com essas configurações e entrar no modo de usuário único

## <a name="access-for-coreos"></a>Acesso para CoreOS
O modo de usuário único no CoreOS requer que o GRUB esteja habilitado. 

### <a name="grub-access-in-coreos"></a>Acesso ao GRUB no CoreOS
Para acessar o GRUB, pressione qualquer tecla quando sua VM estiver inicializando.

### <a name="single-user-mode-in-coreos"></a>Modo de usuário único no CoreOS
O CoreOS alternará para o modo de usuário único automaticamente se ele não conseguir inicializar normalmente. Para entrar manualmente no modo de usuário único, siga estas instruções:
1. No GRUB, pressione 'e' para editar sua entrada de inicialização
1. Procure a linha que começa com `linux$`. Deve haver 2, encapsulados em diferentes cláusulas if/else
1. Anexe `coreos.autologin=ttyS0` ao final de ambas as `linux$` linhas
1. Pressione Ctrl + X para reinicializar com essas configurações e entrar no modo de usuário único

## <a name="access-for-suse-sles"></a>Acesso para SUSE SLES
As imagens mais recentes de SLES 12 SP3+ permitem o acesso por meio do console serial, caso o sistema seja inicializado no modo de emergência. 

### <a name="grub-access-in-suse-sles"></a>Acesso ao GRUB no SUSE SLES
O acesso ao GRUB no SLES requer a configuração do carregador de inicialização por meio do YaST. Para fazer isso, siga as instruções abaixo:

1. ssh na VM do SLES e execute `sudo yast bootloader`. Use a tecla `tab`, a tecla `enter` e as teclas de direção para navegar pelo menu. 
1. Navegue até `Kernel Parameters`e marque `Use serial console`. 
1. Adicionar `serial --unit=0 --speed=9600 --parity=no` aos argumentos de Console

1. Pressione F10 para salvar suas configurações e sair
1. Para acessar o GRUB, reinicie sua VM e pressione qualquer tecla durante a sequência de inicialização para fazer com que o GRUB permaneça na tela
    - O tempo limite padrão para o GRUB é 1 s. Você pode modificar isso alterando a variável `GRUB_TIMEOUT` em `/etc/default/grub`

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Modo de usuário único no SUSE SLES
Você será automaticamente direcionado para o shell de emergência se o SLES não puder ser inicializado normalmente. Para entrar manualmente no shell de emergência, siga estas instruções:

1. No GRUB, pressione 'e' para editar sua entrada de inicialização (a entrada do SLES)
1. Procure a linha de kernel que começa com `linux`
1. Acrescente `systemd.unit=emergency.target` ao fim da linha
1. Pressione Ctrl + X para reinicializar com essas configurações e entrar no shell de emergência
   > Observe que ocorrerá a alternância para o shell de emergência com um sistema de arquivos _somente leitura_. Se quiser fazer todas as edições em todos os arquivos, você precisará montar novamente o sistema de arquivos com permissões de leitura-gravação. Para fazer isso, insira `mount -o remount,rw /` no shell

## <a name="access-for-oracle-linux"></a>Acesso para o Oracle Linux
Muito semelhante ao Red Hat Enterprise Linux, o modo de usuário único no Oracle Linux requer que o GRUB e o usuário raiz estejam habilitados. 

### <a name="grub-access-in-oracle-linux"></a>Acesso ao GRUB no Oracle Linux
O Oracle Linux vem com o GRUB habilitado pronto para uso. Para acessar o GRUB, reinicialize a VM com `sudo reboot` e pressione “Esc”. Você verá a tela GRUB aparecer.

### <a name="single-user-mode-in-oracle-linux"></a>Modo de usuário único no Oracle Linux
Siga as instruções para RHEL acima para habilitar o modo de usuário único no Oracle Linux.

## <a name="next-steps"></a>Próximas etapas
* A página principal da documentação do Linux do console serial está localizada [aqui](serial-console.md).
* Use o Console Serial para [chamadas NMI e SysRq](serial-console-nmi-sysrq.md)
* O Console Serial também está disponível para VMs do [Windows](../windows/serial-console.md)
* Saiba mais sobre [diagnóstico de inicialização](boot-diagnostics.md)
