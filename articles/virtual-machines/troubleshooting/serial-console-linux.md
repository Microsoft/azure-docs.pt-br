---
title: Console serial do Azure para Linux | Microsoft Docs
description: Bi-Directional console serial para máquinas virtuais do Azure e conjuntos de dimensionamento de máquinas virtuais usando um exemplo do Linux.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: 25e3a9cb363ae4e64b953aeb7a6da4e2e66c9fc7
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977091"
---
# <a name="azure-serial-console-for-linux"></a>Console serial do Azure para Linux

O console serial no portal do Azure fornece acesso a um console baseado em texto para VMs (máquinas virtuais) do Linux e instâncias do conjunto de dimensionamento de máquinas virtuais. Essa conexão serial conecta-se à porta serial ttyS0 da instância da VM ou do conjunto de dimensionamento de máquinas virtuais, fornecendo acesso a ela independentemente da rede ou do estado do sistema operacional. O console serial só pode ser acessado pelo portal do Azure, e apenas os usuários que têm a função de acesso de Colaborador ou superior à VM ou ao conjunto de dimensionamento de máquinas virtuais podem usá-lo.

O console serial funciona da mesma maneira nas VMs e nas instâncias do conjunto de dimensionamento de máquinas virtuais. Neste documento, todas as menções às VMs incluirão implicitamente as instâncias do conjunto de dimensionamento de máquinas virtuais, salvo indicação em contrário.

O console serial está geralmente disponível em regiões globais do Azure e em visualização pública no Azure governamental. Ele ainda não está disponível na nuvem do Azure China.

Para obter a documentação do console serial para Windows, consulte [console serial para Windows](./serial-console-windows.md).

> [!NOTE]
> O console serial é incompatível atualmente com uma conta de armazenamento de diagnóstico de inicialização gerenciada. Para usar o console serial, verifique se você está usando uma conta de armazenamento personalizada.

## <a name="prerequisites"></a>Pré-requisitos

- A VM ou a instância do conjunto de dimensionamento de máquinas virtuais precisa usar o modelo de implantação do gerenciamento de recursos. Implantações clássicas não são suportadas.

- Sua conta que usa o console serial precisa ter a [função Colaborador da Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) na VM e a conta de armazenamento de [diagnóstico de inicialização](boot-diagnostics.md)

- Sua VM ou a instância do conjunto de dimensionamento de máquinas virtuais precisa ter um usuário baseado em senha. Você pode criar um com a função [reset password](../extensions/vmaccess.md#reset-password) da extensão de acesso da VM. Selecione **Redefinir senha** na seção **Suporte + solução de problemas**.

- Sua VM ou instância do conjunto de dimensionamento de máquinas virtuais deve ter o [diagnóstico de inicialização](boot-diagnostics.md) habilitado.

    ![Configurações de diagnóstico de inicialização](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- Para configurações específicas das distribuições do Linux, consulte [Disponibilidade de distribuição Linux do console serial](#serial-console-linux-distribution-availability).

- A instância do conjunto de dimensionamento de máquinas virtuais ou VM deve ser configurada para a saída serial em `ttys0` . Esse é o padrão para as imagens do Azure, mas você desejará fazer uma verificação dupla em imagens personalizadas. Detalhes [abaixo](#custom-linux-images).


> [!NOTE]
> O console serial requer um usuário local com uma senha configurada. VMs ou conjuntos de dimensionamento de máquinas virtuais configurados somente com uma chave pública SSH não poderão entrar no console serial. Para criar um usuário local com uma senha, use a [Extensão do VMAccess](../extensions/vmaccess.md), disponível no portal selecionando **Redefinir senha** no portal do Azure e crie um usuário local com uma senha.
> Você também pode redefinir a senha do administrador em sua conta [usando o GRUB para inicializar no modo de usuário único](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distribution-availability"></a>Disponibilidade de distribuição do Linux do console serial
Para o console serial funcionar corretamente, o sistema operacional convidado deve ser configurado para ler e gravar mensagens do console na porta serial. As [distribuições mais endossadas do Linux do Azure](../linux/endorsed-distros.md) têm o console serial configurado por padrão. A seleção do **console serial** na seção **Support + troubleshooting** do portal do Azure fornece acesso ao console serial.

> [!NOTE]
> Se você não estiver vendo nada no console serial, verifique se o diagnóstico de inicialização está habilitado na VM. Pressionar **Enter** geralmente corrigirá problemas em que nada está aparecendo no console serial.

Distribuição      | Acesso ao console serial
:-----------|:---------------------
Red Hat Enterprise Linux    | Acesso ao console serial habilitado por padrão.
CentOS      | Acesso ao console serial habilitado por padrão.
Debian      | Acesso ao console serial habilitado por padrão.
Ubuntu      | Acesso ao console serial habilitado por padrão.
CoreOS      | Acesso ao console serial habilitado por padrão.
SUSE        | Imagens mais recentes do SLES disponíveis no Azure têm acesso ao console serial ativado por padrão. Se você estiver usando versões mais antigas (10 ou anteriores) do SLES no Azure, consulte o [artigo do KB](https://www.novell.com/support/kb/doc.php?id=3456486) para ativar o console serial.
Oracle Linux        | Acesso ao console serial habilitado por padrão.

### <a name="custom-linux-images"></a>Imagens personalizadas do Linux
Para ativar o console serial para sua imagem customizada da VM Linux, ative o acesso ao console no arquivo */ etc / inittab* para executar um terminal em`ttyS0`. Por exemplo: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Talvez você também precise gerar um Getty em ttyS0. Isso pode ser feito com `systemctl start serial-getty@ttyS0.service` .

Você também vai querer adicionar ttyS0 como o destino para a saída serial. Para obter mais informações sobre como configurar uma imagem personalizada para trabalhar com o console serial, consulte os requisitos gerais do sistema em [criar e carregar um VHD do Linux no Azure](../linux/create-upload-generic.md#general-linux-system-requirements).

Se você está construindo um kernel personalizado, considere habilitar esses flags do kernel: `CONFIG_SERIAL_8250=y` e `CONFIG_MAGIC_SYSRQ_SERIAL=y`. O arquivo de configuração normalmente está localizado na */boot/* caminho.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Cenários comuns para acessar o console serial

Cenário          | Ações no console serial
:------------------|:-----------------------------------------
Arquivo *fstab* danificado | Pressione a tecla **Enter** para continuar e use um editor de texto para corrigir o arquivo *FSTAB*. Você pode precisar estar no modo de usuário único para fazer isso. Para obter mais informações, consulte a seção do console serial de [como corrigir problemas do fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) e [usar o console serial para acessar o grub e o modo de usuário único](serial-console-grub-single-user-mode.md).
Regras de firewall incorretas |  Se você tiver configurado o iptables para bloquear a conectividade SSH, poderá usar o console serial para interagir com sua VM sem precisar de SSH. Mais detalhes podem ser encontrados na [página do iptables Man](https://linux.die.net/man/8/iptables).<br>Da mesma forma, se o firewall estiver bloqueando o acesso SSH, você poderá acessar a VM por meio do console serial e reconfigurar o firewall. Mais detalhes podem ser encontrados na [documentação do firewall](https://firewalld.org/documentation/).
Corrupção de sistema de arquivos/verificação | Consulte a seção do console serial da [VM Linux do Azure não pode ser iniciada devido a erros do sistema de arquivos](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) para obter mais detalhes sobre como solucionar problemas em sistemas de arquivos corrompidos usando o console serial.
Problemas de configuração de SSH | Acesse o console serial e altere as configurações. Console serial pode ser usada independentemente da configuração de SSH de uma VM, pois ela não requer que a VM tenha conectividade de rede para funcionar. Um guia de solução de problemas está disponível em [solucionar problemas de conexões SSH com uma VM Linux do Azure que falha, com erros ou é recusada](./troubleshoot-ssh-connection.md). Mais detalhes estão disponíveis em [etapas detalhadas de solução de problemas de SSH para problemas de conexão a uma VM do Linux no Azure](./detailed-troubleshoot-ssh-connection.md)
Interagir com o carregador de inicialização | Reinicie a VM na folha do console serial para acessar o GRUB na VM do Linux. Para obter mais detalhes e informações específicas do distribuição, consulte [usar o console serial para acessar o grub e o modo de usuário único](serial-console-grub-single-user-mode.md).

## <a name="disable-the-serial-console"></a>Desabilitar o Console serial

Por padrão, todas as assinaturas têm o acesso ao console serial habilitado. Desabilite o console serial no nível da assinatura ou no nível da VM/do conjunto de dimensionamento de máquinas virtuais. Para obter instruções detalhadas, acesse [Habilitar e desabilitar o Console serial do Azure](./serial-console-enable-disable.md).

## <a name="serial-console-security"></a>Segurança de console serial

### <a name="access-security"></a>Segurança de acesso
O acesso ao console serial é limitado aos usuários que têm uma função de acesso de [Colaborador da Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) ou superior à máquina virtual. Se o locatário do Azure Active Directory exigir autenticação de vários fatores (MFA), o acesso ao console serial também precisará do MFA, pois o acesso ao console serial é feito através do [Portal do Azure](https://portal.azure.com).

### <a name="channel-security"></a>Segurança de canal
Todos os dados enviados são criptografados na rede.

### <a name="audit-logs"></a>Logs de auditoria
Todo o acesso ao console serial está conectado a logs de [diagnósticos de inicialização](./boot-diagnostics.md) da máquina virtual. O acesso a esses logs pertence e é controlado pelo administrador da máquina virtual do Azure.

> [!CAUTION]
> Nenhuma senha de acesso para o console seja registrada. No entanto, se comandos executados dentro do console contiverem ou gerarem senhas, segredos, nomes de usuário ou qualquer outra forma de informações de identificação pessoal (PII), eles serão gravados nos logs de diagnóstico de inicialização VM. Eles serão gravados juntamente com todos os outro texto visível, como parte da implementação de scrollback do console serial função. Esses logs são circulares e somente indivíduos com permissões de leitura para a conta de armazenamento de diagnósticos têm acesso a eles. Se você estiver inserindo qualquer comando de dados que contenha segredos ou PII, recomendamos o uso do SSH, a menos que o console serial seja absolutamente necessário.

### <a name="concurrent-usage"></a>Uso simultâneo
Se um usuário estiver conectado ao console serial e outro usuário solicitar com êxito acesso à mesma máquina virtual, o primeiro usuário será desconectado e o segundo usuário será conectado à mesma sessão.

> [!CAUTION]
> Isso significa que um usuário que está desconectado não será desconectado. A capacidade de impor um logout após a desconexão (usando SIGHUP ou mecanismo semelhante) ainda está no roteiro. Para o Windows, há um tempo limite automático ativado no Console Administrativo Especial (SAC); no entanto, para o Linux, você pode definir a configuração de tempo limite do terminal. Para fazer isso, adicione `export TMOUT=600` no arquivo * .bash_profile * ou * .profile * para o usuário que você usa para fazer login no console. Essa configuração expirará a sessão após 10 minutos.

## <a name="accessibility"></a>Acessibilidade
Acessibilidade é um foco importante para o console serial do Azure. Para esse fim, garantimos que o console serial seja totalmente acessível.

### <a name="keyboard-navigation"></a>Navegação de teclado
Use a tecla **Tab** no seu teclado para navegar na interface do console serial do portal do Azure. Sua localização será destacada na tela. Para deixar o foco da janela do console serial, pressione **Ctrl**+**F6** no seu teclado.

### <a name="use-serial-console-with-a-screen-reader"></a>Usar o console serial com um leitor de tela
O console serial possui suporte ao leitor de tela embutido. Navegar ao redor com um leitor de tela ativado permitirá que o texto alternativo do botão atualmente selecionado seja lido em voz alta pelo leitor de tela.

## <a name="known-issues"></a>Problemas conhecidos
Estamos cientes de alguns problemas no console serial e no sistema operacional da VM. Aqui está uma lista desses problemas e etapas para mitigação para VMs Linux. Esses problemas e as mitigações se aplicam às VMs e às instâncias do conjunto de dimensionamento de máquinas virtuais. Se eles não corresponderem ao erro que você está vendo, confira os erros comuns do serviço Console serial em [Erros comuns do Console serial](./serial-console-errors.md).

Problema                           |   Atenuação
:---------------------------------|:--------------------------------------------|
Pressionando **Enter** depois que o banner de conexão não faz com que um prompt de login seja exibido. | O GRUB pode não estar configurado corretamente. Execute os seguintes comandos: `grub2-mkconfig -o /etc/grub2-efi.cfg` e/ou `grub2-mkconfig -o /etc/grub2.cfg` . Para mais informações, consulte [Hitting enter não faz nada](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Esse problema pode ocorrer se você estiver executando uma VM personalizada, um dispositivo protegido ou uma configuração do GRUB que faz com que o Linux falhe na conexão com a porta serial.
O texto do console serial ocupa apenas uma parte do tamanho da tela (geralmente depois de usar um editor de texto). | Os consoles seriais não dão suporte à negociação do tamanho da janela ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)), o que significa que nenhum sinal SIGWINCH será enviado para atualizar o tamanho da tela e a VM não saberá o tamanho do seu terminal. Instale o xterm ou um utilitário semelhante para fornecer o comando `resize` e, em seguida, execute `resize`.
Colar longas cadeias de caracteres não funciona. | O console serial limita o comprimento de cadeias de caracteres colados em terminal a 2048 caracteres para impedir a sobrecarga da largura de banda de porta serial.
Entrada de teclado irregular em imagens SLES BYOS. A entrada do teclado é reconhecida apenas esporadicamente. | Isso é um problema com o pacote Plymouth. Plymouth não deve ser executado no Azure porque você não precisa de uma tela inicial e Plymouth interfere na capacidade da plataforma de usar o console serial. Remova Plymouth com `sudo zypper remove plymouth` e reinicialize. Como alternativa, modifique a linha de kernel da configuração do GRUB anexando `plymouth.enable=0` ao final da linha. Você pode fazer isso [editando a entrada de inicialização no momento da inicialização](./serial-console-grub-single-user-mode.md#single-user-mode-in-suse-sles)ou editando a linha de GRUB_CMDLINE_LINUX no `/etc/default/grub` , recriando o grub com `grub2-mkconfig -o /boot/grub2/grub.cfg` e reinicializando.


## <a name="frequently-asked-questions"></a>Perguntas frequentes

**P. Como posso enviar comentários?**

a. Fornecer comentários com a criação de um problema do GitHub em https://aka.ms/serialconsolefeedback. Como alternativa (menos preferencial), você pode enviar comentários por meio azserialhelp@microsoft.com ou na categoria da máquina virtual https://feedback.azure.com.

**P. O console serial dá suporte para copiar/colar?**

a. Sim. Use **Ctrl**+**Shift**+**C** e **Ctrl**+**Shift**+**V** para copiar e colar no terminal.

**Perguntas. Posso usar o console serial em vez de uma conexão SSH?**

a. Embora esse uso possa parecer tecnicamente possível, o console serial deve ser usado principalmente como uma ferramenta de solução de problemas em situações em que a conectividade via SSH não é possível. Recomendamos não usar o console serial como substituto de SSH pelos seguintes motivos:

- O console serial não possui muita largura de banda como o SSH. Como é uma conexão somente de texto, mais interações com GUI são difíceis.
- O acesso ao console serial é atualmente possível apenas usando um nome de usuário e senha. Como as chaves SSH são muito mais seguras do que combinações de nome de usuário / senha, do ponto de vista da segurança de login, recomendamos o SSH no console serial.

**Perguntas. Quem pode habilitar ou desabilitar o console serial para minha assinatura?**

a. Para ativar ou desativar o console serial em um nível de assinatura, você deve ter permissões de gravação para a assinatura. As funções que têm permissão de gravação incluem funções de administrador ou proprietário. Funções personalizadas também podem ter permissões de gravação.

**Perguntas. Quem pode acessar o console serial do meu conjunto de dimensionamento de máquinas virtuais/VM?**

a. Você deve ter a função colaborador da máquina virtual ou superior para uma VM ou conjunto de dimensionamento de máquinas virtuais para acessar o console serial.

**P. Meu console serial não está exibindo nada, o que eu faço?**

a. Sua imagem provavelmente não está configurada corretamente para acesso ao console serial. Para obter informações sobre como configurar sua imagem para ativar o console serial, consulte [Disponibilidade de distribuição Linux do console serial](#serial-console-linux-distribution-availability).

**P. O console serial está disponível para conjuntos de escala de máquinas virtuais?**

a. Sim. Confira [Console serial para Conjuntos de Dimensionamento de Máquinas Virtuais](serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

**Perguntas. Se eu configurar minha VM ou conjunto de dimensionamento de máquinas virtuais usando apenas a autenticação de chave SSH, ainda poderei usar o console serial para se conectar à minha instância de VM/conjunto de dimensionamento de máquinas virtuais?**

a. Sim. Como o console serial não requer chaves SSH, você só precisa configurar uma combinação de nome de usuário / senha. Você pode fazer isso selecionando **Redefinir senha** no portal do Azure e usando essas credenciais para entrar no console serial.

## <a name="next-steps"></a>Próximas etapas
* Use o console serial para [acessar o GRUB e o modo de usuário único](serial-console-grub-single-user-mode.md).
* Usar o console serial para [chamadas NMI e SysRq](serial-console-nmi-sysrq.md).
* Saiba como usar o console serial para [habilitar o grub em vários distribuições](serial-console-grub-proactive-configuration.md)
* O console serial também está disponível para [VMs do Windows](./serial-console-windows.md).
* Saiba mais sobre [diagnóstico de inicialização](boot-diagnostics.md).