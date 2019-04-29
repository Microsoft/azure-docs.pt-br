---
title: O console serial da máquina virtual do Azure para Linux | Microsoft Docs
description: Console serial bidirecional para máquinas virtuais do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: harijay
ms.openlocfilehash: f407d87249c44ad3a4773b2cd8fc85ee09506ceb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60921353"
---
# <a name="virtual-machine-serial-console-for-linux"></a>Console serial da máquina virtual para Linux

O console serial da máquina virtual (VM) no portal do Azure fornece acesso a um console baseado em texto para máquinas virtuais Linux. Essa conexão serial conecta-se à porta serial COM1 da máquina virtual, fornecendo acesso a ela, independentemente do estado da rede ou sistema operacional da máquina virtual. O acesso ao console serial de uma máquina virtual pode ser feito apenas usando o portal do Azure. É permitido apenas para os usuários que têm uma função de acesso do Virtual Machine Contributor ou superior à máquina virtual.

Para obter a documentação do console serial para máquinas virtuais do Windows, consulte [console serial da máquina Virtual para Windows](../windows/serial-console.md).

> [!NOTE]
> O console serial para máquinas virtuais geralmente está disponível nas regiões globais do Azure. Ainda não está disponível no governo do Azure ou nas nuvens do Azure China.


## <a name="prerequisites"></a>Pré-requisitos

- A VM na qual você está acessando um console serial deve usar o modelo de implantação de gerenciamento de recursos. Implantações clássicas não são suportadas.

- A VM na qual você está acessando um console serial deve ter [diagnósticos de inicialização](boot-diagnostics.md) ativados.

    ![Configurações de diagnóstico de inicialização](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- Uma conta que usa um console serial deve ter a [função de Integrador de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) para a VM e a conta de [diagnóstico de inicialização](boot-diagnostics.md):

- A VM na qual você está acessando um console serial deve ter uma conta baseada em senha. Você pode criar um com a função [reset password](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) da extensão de acesso da VM. Selecione **Redefinir senha** na seção **Suporte + solução de problemas**.

- Para configurações específicas das distribuições do Linux, consulte [Disponibilidade de distribuição Linux do console serial](#serial-console-linux-distribution-availability).



## <a name="get-started-with-the-serial-console"></a>Comece com o console serial
O console serial para máquinas virtuais está acessível somente por meio do portal do Azure:

  1. Abra o [Portal do Azure](https://portal.azure.com).

  1. No menu à esquerda, selecione **Máquinas Virtuais**.

  1. Selecione uma VM na lista. A página de visão geral da VM é aberta.

  1. Role para baixo até a seção **Support + troubleshooting** e selecione **Console serial**. Um novo painel com o console serial abre e inicia a conexão.

     ![Janela do console serial do Linux](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)


> [!NOTE]
> O console serial requer um usuário local com uma senha configurada. As VMs configuradas apenas com uma chave pública SSH não poderão entrar no console serial. Para criar um usuário local com uma senha, use a [Extensão do VMAccess](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension), disponível no portal selecionando **Redefinir senha** no portal do Azure e crie um usuário local com uma senha.
> Você também pode redefinir a senha do administrador em sua conta [usando o GRUB para inicializar no modo de usuário único](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distribution-availability"></a>Disponibilidade de distribuição Linux do console serial
Para o console serial funcionar corretamente, o sistema operacional convidado deve ser configurado para ler e gravar mensagens do console na porta serial. A maioria das distribuições [endossadas do Linux do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) tem o console serial configurado por padrão. A seleção do **console serial** na seção **Support + troubleshooting** do portal do Azure fornece acesso ao console serial.

Distribuição      | Acesso ao console serial
:-----------|:---------------------
Red Hat Enterprise Linux    | Acesso ao console serial habilitado por padrão.
CentOS      | Acesso ao console serial habilitado por padrão.
Ubuntu      | Acesso ao console serial habilitado por padrão.
CoreOS      | Acesso ao console serial habilitado por padrão.
SUSE        | Imagens mais recentes do SLES disponíveis no Azure têm acesso ao console serial ativado por padrão. Se você estiver usando versões mais antigas (10 ou anteriores) do SLES no Azure, consulte o [artigo do KB](https://www.novell.com/support/kb/doc.php?id=3456486) para ativar o console serial.
Oracle Linux        | Acesso ao console serial habilitado por padrão.
Imagens personalizadas do Linux     | Para ativar o console serial para sua imagem customizada da VM Linux, ative o acesso ao console no arquivo */ etc / inittab* para executar um terminal em`ttyS0`. Por exemplo: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Para obter mais informações sobre a criação adequada de imagens personalizadas, consulte [Criar e carregar um VHD do Linux no Azure](https://aka.ms/createuploadvhd). Se você está construindo um kernel personalizado, considere habilitar esses flags do kernel: `CONFIG_SERIAL_8250=y` e `CONFIG_MAGIC_SYSRQ_SERIAL=y`. O arquivo de configuração normalmente está localizado na */boot/* caminho.

> [!NOTE]
> Se você não estiver vendo nada no console serial, verifique se o diagnóstico de inicialização está habilitado na VM.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Cenários comuns para acessar o console serial

Cenário          | Ações no console serial
:------------------|:-----------------------------------------
Arquivo  *FSTAB quebrado* | Pressione a tecla **Enter** para continuar e use um editor de texto para corrigir o arquivo *FSTAB*. Você pode precisar estar no modo de usuário único para fazer isso. Para obter mais informações, consulte a seção console serial [como corrigir problemas de fstab](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) e [console serial de usar para acessar o GRUB e modo de usuário único](serial-console-grub-single-user-mode.md).
Regras de firewall incorretas |  Se você tiver configurado o iptables para bloquear a conectividade SSH, você pode usar o console serial para interagir com sua VM sem a necessidade de SSH. Mais detalhes podem ser encontrados na [página do manual iptables](https://linux.die.net/man/8/iptables). Da mesma forma, se você firewalld está bloqueando o acesso SSH, você pode acessar a VM por meio do console serial e reconfigurar firewalld. Mais detalhes podem ser encontrados na [firewalld documentação](https://firewalld.org/documentation/).
Corrupção de sistema de arquivos/verificação | Consulte a seção do console serial do [VM Linux do Azure não pode iniciar devido a erros de sistema de arquivos](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) para obter mais detalhes sobre como solucionar problemas corrompido sistemas de arquivos usando o console serial.
Problemas de configuração de SSH | Acesse o console serial e altere as configurações. Console serial pode ser usado independentemente da configuração de SSH de uma VM, pois requer a VM tenha conectividade de rede funcione. Um guia de solução de problemas está disponível em [solucionar problemas de conexões SSH a uma VM do Linux do Azure que falha, tem erros, ou é recusada](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection). Mais detalhes estão disponíveis em [detalhadas SSH etapas para problemas para se conectar a uma VM do Linux no Azure para solucionar problemas](./detailed-troubleshoot-ssh-connection.md)
Interagir com o carregador de inicialização | Reinicie a VM na folha do console serial para acessar o GRUB na VM do Linux. Para obter mais detalhes e informações específicas de distribuição, consulte [console serial de usar para acessar o GRUB e modo de usuário único](serial-console-grub-single-user-mode.md).

## <a name="disable-the-serial-console"></a>Desativar o console serial
Por padrão, todas as assinaturas têm acesso de console serial habilitado para todas as VMs. É possível desabilitar o console serial no nível da assinatura ou no nível da VM.

> [!NOTE]
> Para habilitar ou desabilitar o console serial para uma assinatura, você precisa ter permissões de gravação para a assinatura. Essas permissões incluem funções de administrador ou proprietário. Funções personalizadas também podem ter permissões de gravação.

### <a name="subscription-level-disable"></a>Desabilitação no nível da assinatura
O console serial pode ser desativado para uma assinatura inteira por meio da [chamada Disable Console REST API](/rest/api/serialconsole/console/disableconsole). Você pode usar a função **Try It** disponível nesta página de documentação da API para desativar e ativar o console serial para uma assinatura. Digite seu ID de assinatura para **subscriptionId**, insira **padrão** para **padrão** e, em seguida, selecione **Executar**. Comandos da CLI do Azure ainda não estão disponíveis.

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

### <a name="vm-level-disable"></a>Desabilitação no nível da VM
O console serial pode ser desativado para uma VM específica, desativando a configuração de diagnóstico de inicialização dessa VM. Desative o diagnóstico de inicialização do portal do Azure para desativar o console serial da VM.

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
> Isso significa que um usuário desconectado não será desconectado. A capacidade de impor um logout após a desconexão (usando SIGHUP ou mecanismo similar) ainda está no roteiro. Para o Windows, há um tempo limite automático ativado no Console Administrativo Especial (SAC); no entanto, para o Linux, você pode definir a configuração de tempo limite do terminal. Para fazer isso, adicione `export TMOUT=600` no arquivo *.bash_profile* ou *.profile* para o usuário que você usa para fazer login no console. Essa configuração expirará a sessão após 10 minutos.

## <a name="accessibility"></a>Acessibilidade
A acessibilidade é o foco principal do console serial do Azure. Para esse fim, garantimos que o console serial seja totalmente acessível.

### <a name="keyboard-navigation"></a>Navegação de teclado
Use a tecla **Tab** no seu teclado para navegar na interface do console serial do portal do Azure. Sua localização será destacada na tela. Para deixar o foco da janela do console serial, pressione **Ctrl**+**F6** no seu teclado.

### <a name="use-the-serial-console-with-a-screen-reader"></a>Use o console serial com um leitor de tela
O console serial possui suporte ao leitor de tela embutido. Navegar ao redor com um leitor de tela ativado permitirá que o texto alternativo do botão atualmente selecionado seja lido em voz alta pelo leitor de tela.

## <a name="errors"></a>Errors
Como a maioria dos erros é transitória, tentar novamente sua conexão pode corrigi-los. A tabela a seguir mostra uma lista de erros e atenuações.

Erro                            |   Redução
:---------------------------------|:--------------------------------------------|
Não é possível recuperar as configurações de diagnóstico de inicialização para *&lt;VMNAME &gt;*. Para usar o console serial, verifique se o diagnóstico de inicialização está habilitado para essa VM. | Verifique se a VM tem [diagnósticos de inicialização](boot-diagnostics.md) habilitados.
A VM está em estado desalocado interrompido. Inicie a máquina virtual e tente estabelecer novamente a conexão de console serial. | A VM deve estar em um estado iniciado para acessar o console serial.
Você não tem as permissões necessárias para usar esta VM com o console serial. Certifique-se de ter pelo menos permissões de função de Colaborador da Máquina Virtual.| O acesso ao console serial requer determinadas permissões. Para mais informações, consulte [Pré-requisitos](#prerequisites).
Não é possível determinar o grupo de recursos para a conta de armazenamento de diagnósticos de inicialização *&lt;STORAGEACCOUNTNAME&gt;*. Verifique se o diagnóstico de inicialização está habilitado para essa VM e se você tem acesso a essa conta de armazenamento. | O acesso ao console serial requer determinadas permissões. Para mais informações, consulte [Pré-requisitos](#prerequisites).
O soquete da Web está fechado ou não pôde ser aberto. | Talvez seja necessário colocar `*.console.azure.com` na lista de permissões. Uma abordagem mais detalhada, porém mais longa, é colocar na lista de permissões os [Intervalos de IP do Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653), que são alterados regularmente.
Uma resposta "Proibido" foi encontrada ao acessar a conta de armazenamento do diagnóstico de inicialização desta VM. | Certifique-se de que o diagnóstico de inicialização não tem um firewall de conta. Uma conta de armazenamento do diagnóstico de inicialização acessível é necessária para o console serial funcione.

## <a name="known-issues"></a>Problemas conhecidos
Estamos cientes de algumas questões com o console serial. Aqui está uma lista desses problemas e as etapas de mitigação.

Problema                           |   Redução
:---------------------------------|:--------------------------------------------|
Pressionando **Enter** depois que o banner de conexão não faz com que um prompt de login seja exibido. | Para mais informações, consulte [Hitting enter não faz nada](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Esse problema pode ocorrer se você estiver executando uma VM personalizada, um appliance reforçado ou uma configuração do GRUB que faz com que o Linux não consiga se conectar corretamente à porta serial.
O texto do console serial ocupa apenas uma parte do tamanho da tela (geralmente depois de usar um editor de texto). | Os consoles seriais não dão suporte à negociação do tamanho da janela ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)), o que significa que nenhum sinal SIGWINCH será enviado para atualizar o tamanho da tela e a VM não saberá o tamanho do seu terminal. Instale o xterm ou um utilitário semelhante para fornecer o comando `resize` e, em seguida, execute `resize`.
Colar longas cadeias de caracteres não funciona. | O console serial limita o comprimento de cadeias de caracteres colados em terminal a 2048 caracteres para impedir a sobrecarga da largura de banda de porta serial.
O console serial não funciona com um firewall de conta de armazenamento. | Por design, o console serial não pode funcionar com firewalls de conta de armazenamento habilitados na conta de armazenamento do diagnóstico de inicialização.


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

**P. Quem pode acessar o console serial para minha VM?**

a. Você deve ter a função de Colaborador da Máquina Virtual ou superior para que uma VM acesse o console serial da VM.

**P. Meu console serial não está exibindo nada, o que eu faço?**

a. Sua imagem provavelmente não está configurada corretamente para acesso ao console serial. Para obter informações sobre como configurar sua imagem para ativar o console serial, consulte [Disponibilidade de distribuição Linux do console serial](#serial-console-linux-distribution-availability).

**P. O console serial está disponível para conjuntos de escala de máquinas virtuais?**

a. No momento, o acesso ao console serial para instâncias de conjuntos de dimensionamento de máquinas virtuais não é suportado.

**P. If I set up my VM by using only SSH key authentication, can I still use the serial console to connect to my VM?**

a. Sim. Como o console serial não requer chaves SSH, você só precisa configurar uma combinação de nome de usuário / senha. Você pode fazer isso selecionando **Redefinir senha** no portal do Azure e usando essas credenciais para entrar no console serial.

## <a name="next-steps"></a>Próximas etapas
* Use o console serial para [acessar o GRUB e o modo de usuário único](serial-console-grub-single-user-mode.md).
* Usar o console serial para [chamadas NMI e SysRq](serial-console-nmi-sysrq.md).
* Saiba como usar o console serial [habilitar GRUB em várias distribuições](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/).
* O console serial também está disponível para [VMs Windows](../windows/serial-console.md).
* Saiba mais sobre [diagnóstico de inicialização](boot-diagnostics.md).

