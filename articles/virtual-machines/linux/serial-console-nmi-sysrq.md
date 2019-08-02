---
title: Console Serial do Azure para chamadas SysRq e NMI | Microsoft Docs
description: Usando o Console Serial para chamadas SysRq e NMI em máquinas virtuais do Azure.
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
ms.openlocfilehash: 87db223465c0d6680b8d60807bf90afc81e52554
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708339"
---
# <a name="use-serial-console-for-sysrq-and-nmi-calls"></a>Use o Console Serial para chamadas SysRq e NMI

## <a name="system-request-sysrq"></a>System Request (SysRq)
SysRq é uma sequência de teclas compreendidas pelo kernel do sistema operacional Linux, que pode disparar um conjunto de ações predefinidas. Esses comandos geralmente são usados quando a solução de problemas de máquina virtual ou de recuperação não pode ser executada por meio da administração tradicional (por exemplo, se a VM não está respondendo). Usar o recurso SysRq do Console Serial do Azure irá imitar o pressionamento da tecla SysRq e os caracteres digitados em um teclado físico.

Depois que a sequência SysRq for entregue, a configuração de kernel irá controlar como o sistema responde. Para obter informações sobre como habilitar e desabilitar SysRq, consulte o *Guia do Administrador do SysRq* [texto](https://aka.ms/kernelorgsysreqdoc) | [markdown](https://aka.ms/linuxsysrq).  

O Console Serial do Azure pode ser usado para enviar um SysRq para uma máquina virtual do Azure usando o ícone de teclado na barra de comandos mostrada abaixo.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg)

Escolher "Enviar Comando de SysRq" abrirá uma caixa de diálogo, que fornecerá opções comuns de SysRq ou aceitará uma sequência de comandos de SysRq inseridos na caixa de diálogo.  Isso permite que a série de SysRqs execute uma operação de alto nível, como uma reinicialização segura usando: `REISUB`.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-sysreq_UI.png)

O comando SysRq não pode ser usado em máquinas virtuais que estão paradas ou cujo kernel está em um estado não responsivo. (por exemplo, um pânico de kernel).

### <a name="enable-sysrq"></a>Habilitar SysRq 
Conforme descrito no *Guia do Administrador do SysRq* acima, o SysRq pode ser configurado de modo que todos os comandos, nenhum deles ou apenas determinados comandos estejam disponíveis. Você pode habilitar todos os comandos de SysRq usando a etapa abaixo, mas eles não permanecerão em vigor após uma reinicialização:
```
echo "1" >/proc/sys/kernel/sysrq
```
Para tornar a configuração do SysReq persistente, você pode fazer o seguinte para habilitar todos os comandos de SysRq
1. Adicionar esta linha a */etc/sysctl.conf* <br>
    `kernel.sysrq = 1`
1. Reinicializar ou atualizar sysctl executando <br>
    `sysctl -p`

### <a name="command-keys"></a>Teclas de Comando 
No Guia do Administrador do SysRq acima:

|Comando| Função
| ------| ----------- |
|``b``  |   Reiniciará imediatamente o sistema sem sincronizar ou desmontar os discos.
|``c``  |   Executará uma falha do sistema por meio de uma desreferência de ponteiro NULL. Um despejo de memória será utilizado se configurado.
|``d``  |   Mostra todos os bloqueios mantidos.
|``e``  |   Envie um SIGTERM para todos os processos, exceto para init.
|``f``  |   Chamará o killer oom para encerrar um processo de consumo de memória, mas não acionará o pânico se nada puder ser encerrado.
|``g``  |   Usado por kgdb (depurador de kernel)
|``h``  |   Exibirá a ajuda (qualquer outra chave diferente daquelas listadas aqui também exibirá a ajuda, mas ``h`` é fácil de lembrar :-)
|``i``  |    Envie um SIGKILL para todos os processos, exceto para init.
|``j``  |    Forçar "Apenas descongelá-lo" - sistemas de arquivos congelados por FIFREEZE ioctl.
|``k``  |    A Chave de Acesso Seguro (SAK) encerra todos os programas no console do virtual atual. OBSERVAÇÃO:  confira os comentários importantes a seguir, na seção SAK.
|``l``  |    Mostra um backtrace de pilha para todas as CPUs ativas.
|``m``  |    Despejará as informações da memória atual no console.
|``n``  |    Usado para facilitar as tarefas de RT
|``o``  |    Desativará o sistema (se configurado e com suporte).
|``p``  |    Despejará os registros e sinalizadores atuais para o console.
|``q``  |    Despejará por listas de CPU de todos os hrtimers armados (mas NÃO temporizadores timer_list regulares) e informações detalhadas sobre todos os dispositivos clockevent.
|``r``  |    Desativa o modo bruto do teclado e define-o como XLATE.
|``s``  |    Tentará sincronizar todos os sistemas de arquivos montados.
|``t``  |    Despejará uma lista de tarefas atuais e suas informações no console.
|``u``  |    Tentará montar novamente todos os sistemas de arquivos somente leitura montados.
|``v``  |    Restaura forçadamente o console framebuffer
|``v``  |    Causa despejo do buffer de ETM [específico de ARM]
|``w``  |    Despeja tarefas que estão em estado ininterrupto (bloqueado).
|``x``  |    Usado pela interface xmon em plataformas ppc/powerpc. Mostre os Registros PMU globais em sparc64. Despeje todas as entradas TLB em MIPS.
|``y``  |    Mostrar Registros de CPU globais [específico de SPARC-64]
|``z``  |    Despejar o buffer ftrace
|``0``-``9`` | Define o nível de log de console, controlando quais mensagens de kernel serão impressas no console. (``0``, por exemplo, faria isso, para que apenas mensagens de emergência como PANICs ou OOPSes fizessem isso no console.)

### <a name="distribution-specific-documentation"></a>Documentação específica de distribuição ###
Para obter a documentação específica de distribuição em SysRq e as etapas para configurar o Linux para criar um despejo de memória no recebimento de um comando "Crash" de SysRq, consulte os links abaixo:

#### <a name="ubuntu"></a>Ubuntu ####
 - [Despejo de Memória do Kernel](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

#### <a name="red-hat"></a>Red Hat ####
- [O que é o Recurso de SysRq e como usá-lo?](https://access.redhat.com/articles/231663)
- [Como usar o recurso de SysRq para coletar informações de um servidor RHEL](https://access.redhat.com/solutions/2023)

#### <a name="suse"></a>SUSE ####
- [Configurar a captura de despejo de núcleo de kernel](https://www.suse.com/support/kb/doc/?id=3374462)

#### <a name="coreos"></a>CoreOS ####
- [Coletar logs de falha](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="non-maskable-interrupt-nmi"></a>Interrupção Não Mascarável (NMI) 
Uma interrupção não mascarável (NMI) foi projetada para criar um sinal que o software em uma máquina virtual não irá ignorar. Historicamente, as NMIs são usadas para monitorar os problemas de hardware em sistemas que necessitam de tempos de resposta específicos.  Administradores de sistema e de hoje, os programadores geralmente usam NMI como um mecanismo para depurar ou solucionar problemas de sistemas que não estão respondendo.

O Console Serial pode ser usado para enviar uma NMI a uma máquina virtual do Azure usando o ícone de teclado na barra de comandos mostrada abaixo. Depois que a NMI for entregue, a configuração de máquina virtual irá controlar como o sistema responde.  Os sistemas operacionais Linux poderão ser configurados para parar e criar um despejo de memória quando o sistema operacional receber uma NMI.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg) <br>

Para sistemas Linux que dão suporte a sysctl para configurar parâmetros de kernel, você pode habilitar um pânico ao receber essa NMI usando o seguinte:
1. Adicionar esta linha a */etc/sysctl.conf* <br>
    `kernel.panic_on_unrecovered_nmi=1`
1. Reinicializar ou atualizar sysctl executando <br>
    `sysctl -p`

Para obter mais informações sobre configurações de kernel do Linux, incluindo `unknown_nmi_panic`, `panic_on_io_nmi` e `panic_on_unrecovered_nmi`, confira: [Documentação para /proc/sys/kernel/*](https://www.kernel.org/doc/Documentation/sysctl/kernel.txt). Para obter a documentação específica de distribuição em NMI e as etapas para configurar o Linux para criar um despejo de memória no recebimento de uma NMI, consulte os links abaixo:
 
### <a name="ubuntu"></a>Ubuntu 
 - [Despejo de Memória do Kernel](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

### <a name="red-hat"></a>Red Hat 
 - [O que é uma NMI e para qual finalidade posso usá-la?](https://access.redhat.com/solutions/4127)
 - [Como posso configurar meu sistema para parar quando for efetuado o push do comutador NMI?](https://access.redhat.com/solutions/125103)
 - [Guia do Administrador de Despejo de Memória](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/pdf/kernel_crash_dump_guide/kernel-crash-dump-guide.pdf)

### <a name="suse"></a>SUSE 
- [Configurar a captura de despejo de núcleo de kernel](https://www.suse.com/support/kb/doc/?id=3374462)

### <a name="coreos"></a>CoreOS 
- [Coletar logs de falha](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="next-steps"></a>Próximas etapas
* A página principal da documentação do Linux do Console Serial está localizada [aqui](serial-console.md).
* Usar o Console Serial para inicializar em [GRUB e entrar no modo de usuário único](serial-console-grub-single-user-mode.md)
* O Console Serial também está disponível para VMs do [Windows](../windows/serial-console.md)
* Saiba mais sobre [diagnóstico de inicialização](boot-diagnostics.md)