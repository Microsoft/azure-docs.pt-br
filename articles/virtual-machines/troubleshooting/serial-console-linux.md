---
title: Console Serial do Azure para Linux | Microsoft Docs
description: Bi-direcional Console Serial para máquinas virtuais do Azure e conjuntos de dimensionamento de máquina Virtual.
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
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: f3fa646fcca99d5762f480b3fd12c5e249eabaf8
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710576"
---
# <a name="azure-serial-console-for-linux"></a>Console Serial do Azure para Linux

O Console Serial no portal do Azure fornece acesso a um console baseado em texto para máquinas virtuais do Linux (VMs) e instâncias de conjunto de dimensionamento de máquinas virtuais. Essa conexão serial conecta-se à porta serial ttys0 da VM ou instância do conjunto de dimensionamento de máquina virtual, fornecendo acesso a ele, independentemente do estado do sistema operacional ou rede. O console serial só pode ser acessado por meio do portal do Azure e é permitido apenas para os usuários que tenham uma função de acesso de Colaborador ou superior para o conjunto de dimensionamento VM ou máquina virtual.

Console Serial funciona da mesma maneira para VMs e instâncias de conjunto de dimensionamento de máquinas virtuais. Neste documento, todos os menções em VMs implicitamente inclui instâncias de conjunto de dimensionamento de máquina virtual, a menos que indicado de outra forma.

Para obter a documentação do Console Serial para Windows, consulte [Console Serial para Windows](../windows/serial-console.md).

> [!NOTE]
> O Console Serial está disponível em regiões globais do Azure. Ainda não está disponível no governo do Azure ou nas nuvens do Azure China.


## <a name="prerequisites"></a>Pré-requisitos

- A instância de conjunto de dimensionamento VM ou máquina virtual deve usar o modelo de implantação do resource Manager. Implantações clássicas não são suportadas.

- Sua conta que usa o console serial deve ter o [função de Colaborador de máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) para a VM e o [diagnóstico de inicialização](boot-diagnostics.md) conta de armazenamento

- A instância de conjunto de dimensionamento VM ou máquina virtual deve ter um usuário baseado em senha. Você pode criar um com a função [reset password](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) da extensão de acesso da VM. Selecione **Redefinir senha** na seção **Suporte + solução de problemas**.

- A instância de conjunto de dimensionamento VM ou máquina virtual deve ter [diagnósticos de inicialização](boot-diagnostics.md) habilitado.

    ![Configurações de diagnóstico de inicialização](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- Para configurações específicas das distribuições do Linux, consulte [Disponibilidade de distribuição Linux do console serial](#serial-console-linux-distribution-availability).

- A instância de conjunto de dimensionamento VM ou máquina virtual deve ser configurada para a saída serial em `ttys0`. Esse é o padrão para as imagens do Azure, mas convém Verifique isso em imagens personalizadas. Detalhes [abaixo](#custom-linux-images).


## <a name="get-started-with-the-serial-console"></a>Introdução ao Console Serial
O Console Serial para máquinas virtuais e o conjunto de dimensionamento de máquina virtual é acessível apenas por meio do portal do Azure:

### <a name="serial-console-for-virtual-machines"></a>Console Serial para máquinas virtuais
Console Serial para máquinas virtuais é tão simples como clicar em **console Serial** dentro de **suporte + solução de problemas** seção no portal do Azure.
  1. Abra o [Portal do Azure](https://portal.azure.com).

  1. Navegue até **todos os recursos** e selecione uma máquina Virtual. A página de visão geral da VM é aberta.

  1. Role para baixo até a seção **Support + troubleshooting** e selecione **Console serial**. Um novo painel com o console serial abre e inicia a conexão.

     ![Janela do Console Serial do Linux](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Console Serial para conjuntos de dimensionamento de máquina Virtual
Console serial está disponível em uma base por instância para conjuntos de dimensionamento de máquina virtual. Você precisará navegar até a instância individual de um conjunto de dimensionamento de máquina virtual antes de ver os **console Serial** botão. Se seu conjunto de dimensionamento de máquina virtual não tiver habilitado o diagnóstico de inicialização, certifique-se de que atualizar o seu modelo de conjunto de dimensionamento de máquina virtual para habilitar o diagnóstico de inicialização e, em seguida, atualize todas as instâncias para o novo modelo para acessar o console serial.
  1. Abra o [Portal do Azure](https://portal.azure.com).

  1. Navegue até **todos os recursos** e selecione um conjunto de dimensionamento de máquina Virtual. A página de visão geral de dimensionamento de máquinas virtuais definido é aberta.

  1. Navegue até **instâncias**

  1. Selecione uma instância de conjunto de dimensionamento de máquina virtual

  1. Dos **suporte + solução de problemas** seção, selecione **console Serial**. Um novo painel com o console serial abre e inicia a conexão.

     ![Console Serial do conjunto de dimensionamento de máquina virtual Linux](./media/virtual-machines-serial-console/vmss-start-console.gif)


> [!NOTE]
> O console serial requer um usuário local com uma senha configurada. Máquinas virtuais ou conjuntos de dimensionamento de máquina virtual configurados somente com uma chave pública SSH não conseguirá entrar no console serial. Para criar um usuário local com uma senha, use a [Extensão do VMAccess](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension), disponível no portal selecionando **Redefinir senha** no portal do Azure e crie um usuário local com uma senha.
> Você também pode redefinir a senha do administrador em sua conta [usando o GRUB para inicializar no modo de usuário único](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distribution-availability"></a>Disponibilidade de distribuição de Linux do Console Serial
Para o console serial funcionar corretamente, o sistema operacional convidado deve ser configurado para ler e gravar mensagens do console na porta serial. A maioria das distribuições [endossadas do Linux do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) tem o console serial configurado por padrão. A seleção do **console serial** na seção **Support + troubleshooting** do portal do Azure fornece acesso ao console serial.

> [!NOTE]
> Se você não estiver vendo nada no console serial, verifique se o diagnóstico de inicialização está habilitado na VM. Atingindo **Enter** geralmente será corrigir problemas em que nada está aparecendo no console serial.

Distribuição      | Acesso ao console serial
:-----------|:---------------------
Red Hat Enterprise Linux    | Acesso ao console serial habilitado por padrão.
CentOS      | Acesso ao console serial habilitado por padrão.
Ubuntu      | Acesso ao console serial habilitado por padrão.
CoreOS      | Acesso ao console serial habilitado por padrão.
SUSE        | Imagens mais recentes do SLES disponíveis no Azure têm acesso ao console serial ativado por padrão. Se você estiver usando versões mais antigas (10 ou anteriores) do SLES no Azure, consulte o [artigo do KB](https://www.novell.com/support/kb/doc.php?id=3456486) para ativar o console serial.
Oracle Linux        | Acesso ao console serial habilitado por padrão.

### <a name="custom-linux-images"></a>Imagens personalizadas do Linux
Para ativar o console serial para sua imagem customizada da VM Linux, ative o acesso ao console no arquivo */ etc / inittab* para executar um terminal em`ttyS0`. Por exemplo: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`.

Você também desejará adicionar ttys0 como o destino para a saída serial. Para obter mais informações sobre como configurar uma imagem personalizada para trabalhar com o console serial, consulte os requisitos gerais do sistema em [criar e carregar um VHD do Linux no Azure](https://aka.ms/createuploadvhd#general-linux-system-requirements).

Se você está construindo um kernel personalizado, considere habilitar esses flags do kernel: `CONFIG_SERIAL_8250=y` e `CONFIG_MAGIC_SYSRQ_SERIAL=y`. O arquivo de configuração normalmente está localizado na */boot/* caminho. |

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Cenários comuns para acessar o Console Serial

Cenário          | Ações no Console Serial
:------------------|:-----------------------------------------
Arquivo  *FSTAB quebrado* | Pressione a tecla **Enter** para continuar e use um editor de texto para corrigir o arquivo *FSTAB*. Você pode precisar estar no modo de usuário único para fazer isso. Para obter mais informações, consulte a seção console serial [como corrigir problemas de fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) e [console serial de usar para acessar o GRUB e modo de usuário único](serial-console-grub-single-user-mode.md).
Regras de firewall incorretas |  Se você tiver configurado o iptables para bloquear a conectividade SSH, você pode usar o console serial para interagir com sua VM sem a necessidade de SSH. Mais detalhes podem ser encontrados na [página do manual iptables](https://linux.die.net/man/8/iptables).<br>Da mesma forma, se seu firewalld está bloqueando o acesso SSH, você pode acessar a VM por meio do console serial e reconfigurar firewalld. Mais detalhes podem ser encontrados na [firewalld documentação](https://firewalld.org/documentation/).
Corrupção de sistema de arquivos/verificação | Consulte a seção do console serial do [VM Linux do Azure não pode iniciar devido a erros de sistema de arquivos](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) para obter mais detalhes sobre como solucionar problemas corrompido sistemas de arquivos usando o console serial.
Problemas de configuração de SSH | Acesse o console serial e altere as configurações. Console serial pode ser usado independentemente da configuração de SSH de uma VM, pois requer a VM tenha conectividade de rede funcione. Um guia de solução de problemas está disponível em [solucionar problemas de conexões SSH a uma VM do Linux do Azure que falha, tem erros, ou é recusada](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection). Mais detalhes estão disponíveis em [detalhadas SSH etapas para problemas para se conectar a uma VM do Linux no Azure para solucionar problemas](./detailed-troubleshoot-ssh-connection.md)
Interagir com o carregador de inicialização | Reinicie a VM na folha do console serial para acessar o GRUB na VM do Linux. Para obter mais detalhes e informações específicas de distribuição, consulte [console serial de usar para acessar o GRUB e modo de usuário único](serial-console-grub-single-user-mode.md).

## <a name="disable-the-serial-console"></a>Desabilitar o Console Serial
Por padrão, todas as assinaturas têm acesso ao console serial habilitado. Você pode desabilitar o console serial no nível de assinatura ou nível de conjunto de escala de máquina virtual/de VM. Observe que o diagnóstico de inicialização deve ser habilitado em uma VM para que o console serial funcione.

### <a name="vmvirtual-machine-scale-set-level-disable"></a>Desabilitar o nível de conjunto de dimensionamento máquina virtual/de VM
O console serial pode ser desativado para uma escala VM ou máquina virtual específica, definida pela desabilitação da configuração de diagnóstico de inicialização. Desative o diagnóstico de inicialização do portal do Azure para desabilitar o console serial para a VM ou o conjunto de dimensionamento de máquina virtual. Se você estiver usando o console serial em um conjunto de dimensionamento de máquina virtual, certifique-se de que atualizar suas instâncias de conjunto de dimensionamento de máquina virtual para o modelo mais recente.

> [!NOTE]
> Para habilitar ou desabilitar o console serial para uma assinatura, você precisa ter permissões de gravação para a assinatura. Essas permissões incluem funções de administrador ou proprietário. Funções personalizadas também podem ter permissões de gravação.

### <a name="subscription-level-disable"></a>Desabilitação no nível da assinatura
O console serial pode ser desativado para uma assinatura inteira por meio da [chamada Disable Console REST API](/rest/api/serialconsole/console/disableconsole). Essa ação requer acesso de nível de Colaborador ou superior para a assinatura. Você pode usar a função **Try It** disponível nesta página de documentação da API para desativar e ativar o console serial para uma assinatura. Digite seu ID de assinatura para **subscriptionId**, insira **padrão** para **padrão** e, em seguida, selecione **Executar**. Comandos da CLI do Azure ainda não estão disponíveis.

Para habilitar novamente o console serial para uma assinatura, use o [chamada à API de REST de Console habilitar](/rest/api/serialconsole/console/enableconsole).

![Experimente o API REST](./media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

Como alternativa, você pode usar o seguinte conjunto de comandos bash no Cloud Shell para habilitar, desabilitar e exibir o status desabilitado do console serial para uma assinatura:

* Para obter o status desabilitado do console serial para uma assinatura:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s | jq .properties
    ```
* Para desabilitar o console serial para uma assinatura:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/disableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```
* Para habilitar o console serial para uma assinatura:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/enableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```

## <a name="serial-console-security"></a>Segurança de console serial

### <a name="access-security"></a>Segurança de acesso
O acesso ao console serial é limitado aos usuários que têm uma função de acesso de [Colaborador da Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) ou superior à máquina virtual. Se o locatário do Azure Active Directory exigir autenticação de vários fatores (MFA), o acesso ao console serial também precisará do MFA, pois o acesso ao console serial é feito através do [Portal do Azure](https://portal.azure.com).

### <a name="channel-security"></a>Segurança de canal
Todos os dados enviados são criptografados na rede.

### <a name="audit-logs"></a>Logs de auditoria
Todo o acesso ao console serial está conectado a logs de [diagnósticos de inicialização](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) da máquina virtual. O acesso a esses logs pertence e é controlado pelo administrador da máquina virtual do Azure.

> [!CAUTION]
> Nenhuma senha de acesso para o console seja registrada. No entanto, se comandos executados dentro do console contiverem ou gerarem senhas, segredos, nomes de usuário ou qualquer outra forma de informações de identificação pessoal (PII), eles serão gravados nos logs de diagnóstico de inicialização VM. Eles serão gravados juntamente com todos os outro texto visível, como parte da implementação de scrollback do console serial função. Esses logs são circulares e somente indivíduos com permissões de leitura para a conta de armazenamento de diagnósticos têm acesso a eles. No entanto, recomendamos seguir a melhor prática de usar a Área de Trabalho Remota para qualquer coisa que possa envolver segredos e / ou PII.

### <a name="concurrent-usage"></a>Uso simultâneo
Se um usuário estiver conectado ao console serial e outro usuário solicitar com êxito acesso à mesma máquina virtual, o primeiro usuário será desconectado e o segundo usuário será conectado à mesma sessão.

> [!CAUTION]
> Isso significa que um usuário desconectado não será desconectado. A capacidade de impor um logoff após a desconexão (usando SIGHUP ou um mecanismo semelhante) ainda está previsto. Para o Windows, há um tempo limite automático ativado no Console Administrativo Especial (SAC); no entanto, para o Linux, você pode definir a configuração de tempo limite do terminal. Para fazer isso, adicione `export TMOUT=600` no arquivo *.bash_profile* ou *.profile* para o usuário que você usa para fazer login no console. Essa configuração expirará a sessão após 10 minutos.

## <a name="accessibility"></a>Acessibilidade
A acessibilidade é dos principais focos do console Serial do Azure. Para esse fim, garantimos que o console serial seja totalmente acessível.

### <a name="keyboard-navigation"></a>Navegação de teclado
Use a tecla **Tab** no seu teclado para navegar na interface do console serial do portal do Azure. Sua localização será destacada na tela. Para deixar o foco da janela do console serial, pressione **Ctrl**+**F6** no seu teclado.

### <a name="use-serial-console-with-a-screen-reader"></a>Usar o Console Serial com um leitor de tela
O console serial possui suporte ao leitor de tela embutido. Navegar ao redor com um leitor de tela ativado permitirá que o texto alternativo do botão atualmente selecionado seja lido em voz alta pelo leitor de tela.

## <a name="errors"></a>Errors
Como a maioria dos erros é transitória, tentar novamente sua conexão pode corrigi-los. A tabela a seguir mostra uma lista de erros e atenuações. Esses erros e mitigações se aplicam a ambas as VMs e instâncias de conjunto de dimensionamento de máquinas virtuais.

Erro                            |   Redução
:---------------------------------|:--------------------------------------------|
Não é possível recuperar as configurações de diagnóstico de inicialização para *&lt;VMNAME &gt;* . Para usar o console serial, verifique se o diagnóstico de inicialização está habilitado para essa VM. | Verifique se a VM tem [diagnósticos de inicialização](boot-diagnostics.md) habilitados.
A VM está em estado desalocado interrompido. Inicie a máquina virtual e tente estabelecer novamente a conexão de console serial. | A VM deve estar em um estado iniciado para acessar o console serial.
Você não tem as permissões necessárias para usar esta VM com o console serial. Certifique-se de ter pelo menos permissões de função de Colaborador da Máquina Virtual.| O acesso ao console serial requer determinadas permissões. Para mais informações, consulte [Pré-requisitos](#prerequisites).
Não é possível determinar o grupo de recursos para a conta de armazenamento de diagnósticos de inicialização *&lt;STORAGEACCOUNTNAME&gt;* . Verifique se o diagnóstico de inicialização está habilitado para essa VM e se você tem acesso a essa conta de armazenamento. | O acesso ao console serial requer determinadas permissões. Para mais informações, consulte [Pré-requisitos](#prerequisites).
O soquete da Web está fechado ou não pôde ser aberto. | Talvez seja necessário colocar `*.console.azure.com` na lista de permissões. Uma abordagem mais detalhada, porém mais longa, é colocar na lista de permissões os [Intervalos de IP do Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653), que são alterados regularmente.
Uma resposta "Proibido" foi encontrada ao acessar a conta de armazenamento do diagnóstico de inicialização desta VM. | Certifique-se de que o diagnóstico de inicialização não tem um firewall de conta. Uma conta de armazenamento do diagnóstico de inicialização acessível é necessária para o console serial funcione.

## <a name="known-issues"></a>Problemas conhecidos
Estamos cientes de algumas questões com o console serial. Aqui está uma lista desses problemas e as etapas de mitigação. Esses problemas e atenuações se aplicam a ambas as VMs e instâncias de conjunto de dimensionamento de máquinas virtuais.

Problema                           |   Redução
:---------------------------------|:--------------------------------------------|
Pressionando **Enter** depois que o banner de conexão não faz com que um prompt de login seja exibido. | Para mais informações, consulte [Hitting enter não faz nada](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Esse problema pode ocorrer se você estiver executando uma VM personalizada, o dispositivo avançado ou a configuração do GRUB que faz com que o Linux para conseguir se conectar à porta serial.
O texto do console serial ocupa apenas uma parte do tamanho da tela (geralmente depois de usar um editor de texto). | Os consoles seriais não dão suporte à negociação do tamanho da janela ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)), o que significa que nenhum sinal SIGWINCH será enviado para atualizar o tamanho da tela e a VM não saberá o tamanho do seu terminal. Instale o xterm ou um utilitário semelhante para fornecer o comando `resize` e, em seguida, execute `resize`.
Colar longas cadeias de caracteres não funciona. | O console serial limita o comprimento de cadeias de caracteres colados em terminal a 2048 caracteres para impedir a sobrecarga da largura de banda de porta serial.
O console serial não funciona com um firewall de conta de armazenamento. | Por design, o console serial não pode funcionar com firewalls de conta de armazenamento habilitados na conta de armazenamento do diagnóstico de inicialização.
Console serial não funciona com uma conta de armazenamento usando o Azure Data Lake armazenamento Gen2 com namespaces hierárquicos. | Esse é um problema conhecido com namespaces hierárquicos. Para atenuar, certifique-se de que a conta de armazenamento de diagnóstico de inicialização da VM não é criada usando o Azure Data Lake armazenamento Gen2. Essa opção só pode ser definida no momento da criação da conta de armazenamento. Talvez você precise criar conta de armazenamento de um diagnóstico de inicialização separado sem o Azure Data Lake armazenamento Gen2 habilitado para atenuar esse problema.
Entrada nas imagens SLES BYOS do teclado irregular. Entrada do teclado apenas esporadicamente é reconhecida. | Isso é um problema com o pacote Plymouth. Plymouth não deve ser executado no Azure, pois você não precisa de uma tela inicial e Plymouth interfere com a capacidade de plataforma de usar o Console Serial. Remover Plymouth com `sudo zypper remove plymouth` e, em seguida, reinicialize. Como alternativa, modifique a linha de kernel de sua configuração do GRUB acrescentando `plymouth.enable=0` até o final da linha. Você pode fazer isso [editando a entrada de inicialização no momento da inicialização](https://aka.ms/serialconsolegrub#single-user-mode-in-suse-sles), ou ao editar a linha GRUB_CMDLINE_LINUX `/etc/default/grub`, recompilação GRUB com `grub2-mkconfig -o /boot/grub2/grub.cfg`e, em seguida, reinicializar.


## <a name="frequently-asked-questions"></a>Perguntas frequentes

**P. Como posso enviar comentários?**

a. Fornecer comentários com a criação de um problema do GitHub em https://aka.ms/serialconsolefeedback. Como alternativa (menos preferencial), você pode enviar comentários por meio azserialhelp@microsoft.com ou na categoria da máquina virtual https://feedback.azure.com.

**P. O console serial dá suporte para copiar/colar?**

a. Sim. Use **Ctrl**+**Shift**+**C** e **Ctrl**+**Shift** + **V** para copiar e colar no terminal.

**P. É possível usar o console serial, em vez de uma conexão SSH?**

a. Embora esse uso possa parecer tecnicamente possível, o console serial deve ser usado principalmente como uma ferramenta de solução de problemas em situações em que a conectividade via SSH não é possível. Recomendamos não usar o console serial como substituto de SSH pelos seguintes motivos:

- O console serial não possui muita largura de banda como o SSH. Como é uma conexão somente de texto, mais interações com GUI são difíceis.
- O acesso ao console serial é atualmente possível apenas usando um nome de usuário e senha. Como as chaves SSH são muito mais seguras do que combinações de nome de usuário / senha, do ponto de vista da segurança de login, recomendamos o SSH no console serial.

**P. Quem pode habilitar ou desabilitar o console serial da minha assinatura?**

a. Para ativar ou desativar o console serial em um nível de assinatura, você deve ter permissões de gravação para a assinatura. As funções que têm permissão de gravação incluem funções de administrador ou proprietário. Funções personalizadas também podem ter permissões de gravação.

**P. Quem pode acessar o console serial para meu conjunto de dimensionamento de máquina virtual/de VM?**

a. Você deve ter a função de Colaborador de máquina Virtual ou superior para uma escala VM ou máquina virtual configurada para acessar o console serial.

**P. Meu console serial não está exibindo nada, o que eu faço?**

a. Sua imagem provavelmente não está configurada corretamente para acesso ao console serial. Para obter informações sobre como configurar sua imagem para ativar o console serial, consulte [Disponibilidade de distribuição Linux do console serial](#serial-console-linux-distribution-availability).

**P. O console serial está disponível para conjuntos de escala de máquinas virtuais?**

a. Sim, é! Consulte [Console Serial para conjuntos de dimensionamento de máquina Virtual](#serial-console-for-virtual-machine-scale-sets)

**P. Se configurei minha VM ou um conjunto usando apenas autenticação de chave SSH de dimensionamento de máquina virtual, ainda posso usar o console serial para se conectar à minha instância de conjunto de dimensionamento de máquinas virtuais/VM?**

a. Sim. Como o console serial não requer chaves SSH, você só precisa configurar uma combinação de nome de usuário / senha. Você pode fazer isso selecionando **Redefinir senha** no portal do Azure e usando essas credenciais para entrar no console serial.

## <a name="next-steps"></a>Próximas etapas
* Use o console serial para [acessar o GRUB e o modo de usuário único](serial-console-grub-single-user-mode.md).
* Usar o console serial para [chamadas NMI e SysRq](serial-console-nmi-sysrq.md).
* Saiba como usar o console serial [habilitar GRUB em várias distribuições](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/).
* O console serial também está disponível para [VMs Windows](../windows/serial-console.md).
* Saiba mais sobre [diagnóstico de inicialização](boot-diagnostics.md).

