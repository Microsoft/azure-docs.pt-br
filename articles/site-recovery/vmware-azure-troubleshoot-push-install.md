---
title: Solucionar problemas de instalação por push do serviço de mobilidade com o Azure Site Recovery
description: Solucionar erros de instalação de serviços de mobilidade ao habilitar a replicação para recuperação de desastre com Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 04/03/2020
ms.openlocfilehash: 62c8240a4d2e50aa3b584f322baf7d2ee217c6d3
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127865"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>Solucionar problemas de instalação por push do serviço de mobilidade

A instalação do serviço de mobilidade é uma etapa fundamental para habilitar a replicação. O sucesso desta etapa depende da reunião de pré-requisitos e do trabalho com configurações com suporte. As falhas mais comuns que você pode enfrentar durante a instalação do serviço de mobilidade são devido a:

* [Erros de credencial/privilégio](#credentials-check-errorid-95107--95108)
* [Falhas de logon](#login-failures-errorid-95519-95520-95521-95522)
* [Erros de conectividade](#connectivity-failure-errorid-95117--97118)
* [Erros de arquivo e de compartilhamento de impressora](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [Falhas de Instrumentação de Gerenciamento do Windows (WMI)](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Sistemas operacionais sem suporte](#unsupported-operating-systems)
* [Configurações de inicialização sem suporte](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [Falhas de instalação do VSS (serviço de cópias de sombra de volume)](#vss-installation-failures)
* [Nome do dispositivo na configuração do GRUB, em vez do UUID do dispositivo](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [Volume do Gerenciador de volumes lógicos (LVM)](#lvm-support-from-920-version)
* [Avisos de reinicialização](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

Quando você habilita a replicação, o Azure Site Recovery tenta instalar o agente do serviço de mobilidade em sua VM (máquina virtual). Como parte desse processo, o servidor de configuração tenta se conectar à máquina virtual e copiar o agente. Para habilitar a instalação com êxito, siga as diretrizes passo a passo de solução de problemas.

## <a name="credentials-check-errorid-95107--95108"></a>Verificação de credenciais (ErrorID: 95107 & 95108)

Verifique se a conta de usuário escolhida durante a habilitação da replicação é válida e precisa. Azure Site Recovery requer que a conta **raiz** ou a conta de usuário com **privilégios de administrador** execute uma instalação por push. Caso contrário, a instalação por push será bloqueada no computador de origem.

Para Windows (**erro 95107**), verifique se a conta de usuário tem acesso administrativo no computador de origem, com uma conta local ou conta de domínio. Se você não estiver usando uma conta de domínio, será necessário desabilitar o controle de acesso de usuário remoto no computador local.

* Para adicionar manualmente uma chave do registro que desabilita o controle de acesso de usuário remoto:

  * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
  * Adicione um novo `DWORD` : `LocalAccountTokenFilterPolicy`
  * Defina o valor como `1`

* Para adicionar a chave do registro, em um prompt de comando, execute o seguinte comando:

  `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

Para o Linux (**erro 95108**), você deve escolher a conta **raiz** para a instalação bem-sucedida do agente de serviço de mobilidade. Além disso, os serviços de protocolo FTP do SSH (SFTP) devem estar em execução. Para habilitar o subsistema SFTP e a autenticação de senha no arquivo _sshd_config_ :

1. Entre como **raiz**.
1. Vá para _/etc/ssh/sshd_config arquivo_, localize a linha que começa com `PasswordAuthentication` .
1. Remova a marca de comentário da linha e altere o valor para `yes` .
1. Localize a linha que começa com `Subsystem` e remova a marca de comentário da linha.
1. Reinicie o serviço `sshd` .

Se você quiser modificar as credenciais da conta de usuário escolhida, siga [estas instruções](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95517"></a>Falha de privilégios insuficientes (errorID: 95517)

Quando o usuário escolhido para instalar o agente de mobilidade não tem privilégios de administrador, o servidor de processo de expansão/servidor de configuração não terá permissão para copiar o software do agente de mobilidade para o computador de origem. Esse erro é resultado de uma falha de acesso negado. Verifique se a conta de usuário tem privilégios de administrador.

Se você quiser modificar as credenciais da conta de usuário escolhida, siga [estas instruções](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="insufficient-privileges-failure-errorid-95518"></a>Falha de privilégios insuficientes (errorID: 95518)

Quando o estabelecimento da relação de confiança de domínio entre o domínio primário e a estação de trabalho falha ao tentar entrar no computador de origem, a instalação do agente de mobilidade falha com a ID de erro 95518. Verifique se a conta de usuário usada para instalar o agente de mobilidade tem privilégios administrativos para entrar por meio do domínio primário do computador de origem.

Se você quiser modificar as credenciais da conta de usuário escolhida, siga as [instruções](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)a seguir.

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Falhas de logon (errorID: 95519, 95520, 95521, 95522)

Esta seção descreve as mensagens de erro de logon e credenciais.

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>As credenciais da conta de usuário foram desabilitadas (errorID: 95519)

A conta de usuário escolhida durante a habilitação da replicação foi desabilitada. Para habilitar a conta de usuário, consulte [Este artigo](/powershell/module/Microsoft.PowerShell.LocalAccounts/Enable-LocalUser) ou execute o comando a seguir, substituindo _o texto nome de usuário pelo nome_ real.

`net user 'username' /active:yes`

### <a name="credentials-locked-out-because-of-multiple-failed-login-attempts-errorid-95520"></a>Credenciais bloqueadas devido a várias tentativas de logon com falha (errorID: 95520)

Uma série de esforços de repetição com falha para acessar um computador bloqueará a conta de usuário. A falha pode ser devido a:

* As credenciais fornecidas durante a configuração estão incorretas.
* A conta de usuário escolhida durante a habilitação da replicação está errada.

Modifique as credenciais escolhidas seguindo [estas instruções](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) e repita a operação.

### <a name="logon-servers-arent-available-on-the-source-machine-errorid-95521"></a>Os servidores de logon não estão disponíveis no computador de origem (errorID: 95521)

Esse erro ocorre quando os servidores de logon não estão disponíveis no computador de origem. Se os servidores de logon não estiverem disponíveis, as solicitações de logon falharão e o agente de mobilidade não poderá ser instalado. Para um logon bem-sucedido, verifique se os servidores de logon estão disponíveis no computador de origem e inicie o serviço Netlogon. Para obter mais informações, consulte [cenários de logon do Windows](/windows-server/security/windows-authentication/windows-logon-scenarios).

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>O serviço de logon não está em execução no computador de origem (errorID: 95522)

O serviço de logon não está em execução no computador de origem e causou falha na solicitação de logon. Não é possível instalar o agente de mobilidade. Para resolver o erro, use um dos seguintes métodos para iniciar o `Netlogon` serviço no computador de origem:

* Para iniciar o `Netlogon` serviço a partir de um prompt de comando, execute o comando `net start Netlogon` .
* No Gerenciador de tarefas, inicie o `Netlogon` serviço.

## <a name="connectivity-failure-errorid-95117--97118"></a>Falha de conectividade (errorID: 95117 & 97118)

Servidor de configuração/servidor de processo de expansão tenta se conectar à VM de origem para instalar o agente de mobilidade. Esse erro ocorre quando o computador de origem não está acessível porque há problemas de conectividade de rede.

Para resolver o erro:

* Verifique se a conta de usuário tem acesso administrativo no computador de origem, com uma conta local ou uma conta de domínio. Se você não estiver usando uma conta de domínio, será necessário desabilitar o controle de acesso de usuário remoto no computador local.
  * Para adicionar manualmente uma chave do registro que desabilita o controle de acesso de usuário remoto:
    * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
    * Adicione um novo `DWORD` : `LocalAccountTokenFilterPolicy`
    * Defina o valor como `1`
  * Para adicionar a chave do registro, em um prompt de comando, execute o seguinte comando:

    `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

* Verifique se você pode executar o ping no computador de origem do servidor de configuração. Se você tiver escolhido o servidor de processo de expansão durante a habilitação da replicação, verifique se você pode executar o ping do computador de origem no servidor de processo.

* Verifique se o serviço de compartilhamento de arquivos e impressoras está habilitado em sua máquina virtual. Verifique as etapas [aqui](vmware-azure-troubleshoot-push-install.md#file-and-printer-sharing-services-check-errorid-95105--95106).

* Verifique se o serviço WMI está habilitado em sua máquina virtual. Verifique as etapas [aqui](vmware-azure-troubleshoot-push-install.md#windows-management-instrumentation-wmi-configuration-check-error-code-95103).

* Verifique se as pastas de rede compartilhadas na sua máquina virtual estão acessíveis no servidor de processo. Verifique as etapas [aqui](vmware-azure-troubleshoot-push-install.md#check-access-for-network-shared-folders-on-source-machine-errorid-9510595523).

* Na linha de comando do computador do servidor de origem, use `Telnet` para executar o ping no servidor de configuração ou no servidor de processo de expansão na porta HTTPS 135, conforme mostrado no comando a seguir. Esse comando verifica se há problemas de conectividade de rede ou de bloqueio de porta de firewall.

  `telnet <CS/ scale-out PS IP address> <135>`

* Além disso, para uma VM do Linux:
  * Verifique se os pacotes OpenSSH, OpenSSH Server e OpenSSL mais recentes estão instalados.
  * Verifique e garanta que o Secure Shell (SSH) esteja habilitado e em execução na porta 22.
  * Serviços SFTP devem estar em execução. Para habilitar a autenticação de subsistema e senha do SFTP no arquivo de _sshd_config_ :

    1. Entre como **raiz**.
    1. Vá para _/etc/ssh/sshd_config_ arquivo, localize a linha que começa com `PasswordAuthentication` .
    1. Remova a marca de comentário da linha e altere o valor para `yes` .
    1. Localize a linha que começa com `Subsystem` e remova a marca de comentário da linha
    1. Reinicie o serviço `sshd` .

* Uma tentativa de conexão pode ter falhado se não houver nenhuma resposta adequada após um período de tempo ou uma conexão estabelecida falhou porque um host conectado falhou ao responder.
* Pode ser um problema relacionado à conectividade/rede/domínio. Também pode ser devido a problemas de resolução de nome DNS ou problema de esgotamento de porta TCP. Verifique se há algum problema conhecido no domínio.

## <a name="connectivity-failure-errorid-95523"></a>Falha de conectividade (errorID: 95523)

Esse erro ocorre quando a rede na qual o computador de origem reside não é encontrada, pode ter sido excluída ou não está mais disponível. A única maneira de resolver o erro é garantir que a rede exista.

## <a name="check-access-for-network-shared-folders-on-source-machine-errorid-9510595523"></a>Verificar o acesso para pastas compartilhadas de rede no computador de origem (errorID: 95105, 95523)

Verifique se as pastas de rede compartilhadas em sua máquina virtual estão acessíveis remotamente usando as credenciais especificadas. Para confirmar o acesso: 

1. Faça logon no computador do servidor de processo.
2. Abra o Explorador de Arquivos. Na barra de endereços, digite `\\<SOURCE-MACHINE-IP>\C$` e clique em Enter.

    ![Abrir pasta no PS](./media/vmware-azure-troubleshoot-push-install/open-folder-process-server.PNG)

3. O Explorador de Arquivos solicitará as credenciais. Insira o nome de usuário e a senha e clique em OK. <br><br/>

    ![Fornecer credenciais](./media/vmware-azure-troubleshoot-push-install/provide-credentials.PNG)

    >[!NOTE]
    > Se o computador de origem estiver ingressado no domínio, forneça o nome de domínio junto com o nome de usuário como `<domainName>\<username>` . Se o computador de origem estiver no grupo de trabalho, forneça apenas o nome de usuário.

4. Se a conexão for bem-sucedida, as pastas do computador de origem ficarão visíveis remotamente do servidor de processo.

    ![Pastas visíveis do computador de origem](./media/vmware-azure-troubleshoot-push-install/visible-folders-from-source.png)

Se a conexão não for bem-sucedida, verifique se todos os pré-requisitos foram atendidos.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Verificação dos serviços de compartilhamento arquivos / impressoras (ErrorID: 95105 & 95106)

Após uma verificação de conectividade, verifique se o serviço de compartilhamento de arquivos e impressoras está habilitado em sua máquina virtual. Essas configurações são necessárias para copiar o agente de mobilidade para o computador de origem.

Para **Windows 2008 R2 e versões anteriores**:

* Para habilitar o compartilhamento de arquivo e impressão através do Firewall do Windows,
  1. Abra sistema do **painel**  >  **de controle e segurança**  >  **Firewall do Windows**. No painel esquerdo, selecione **Configurações avançadas**  >  **regras de entrada** na árvore de console.
  1. Localize as regras de compartilhamento de arquivos e compartilhamento de impressora (NB-Sessão-entrada) e arquivo e impressora (SMB-entrada).
  1. Para cada regra, clique com botão direito na regra e, em seguida, clique em **Habilitar regra**.

* Para habilitar o compartilhamento de arquivos com o Política de Grupo:
  1. Vá para **Iniciar**, digite `gpmc.msc` e pesquise.
  1. No painel de navegação, abra as seguintes pastas: configuração de usuário da **política de computador local**  >    >  **modelos administrativos**  >  **componentes do Windows**  >  **compartilhamento de rede**.
  1. No painel de detalhes, clique duas vezes **Impedir os usuários de compartilhamento de arquivos em seus perfis**.

     Para desabilitar a configuração de Política de Grupo e habilitar a capacidade do usuário de compartilhar arquivos, selecione **desabilitado**.

  1. Selecione **OK** para salvar suas alterações.

  Para saber mais, consulte [habilitar ou desabilitar o compartilhamento de arquivos com o política de grupo](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

Para habilitar o compartilhamento de arquivos e impressoras para versões posteriores do Windows ou Linux, siga as instruções em [instalar o serviço de mobilidade para recuperação de desastres de VMs VMware e servidores físicos](vmware-azure-install-mobility-service.md) .

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Verificação de configuração de Instrumentação de Gerenciamento do Windows (WMI) (código de erro: 95103)

Após a verificação dos serviços de arquivo e impressora, habilite o serviço WMI para perfis privados, públicos e de domínio por meio do firewall. Essas configurações são necessárias para concluir a execução remota no computador de origem.

Para habilitar o WMI:

1. Vá para segurança do **painel de controle**  >   e selecione **Firewall do Windows**.
1. Selecione **alterar configurações** e, em seguida, selecione a guia **exceções** .
1. Na janela **exceções** , marque a caixa de seleção Instrumentação de gerenciamento do Windows (WMI) para habilitar o tráfego WMI por meio do firewall.

Você também pode habilitar o tráfego WMI por meio do firewall no prompt de comando com o seguinte comando:

`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`

Outros artigos de solução de problemas do WMI foi possível encontrar os artigos a seguir.

* [Teste de WMI básico](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf)
* [Solução de problemas de WMI](/windows/win32/wmisdk/wmi-troubleshooting)
* [Solucionar problemas com scripts de WMI e serviços de WMI](/previous-versions/tn-archive/ff406382(v=msdn.10))

## <a name="unsupported-operating-systems"></a>Sistemas operacionais sem suporte

Outro motivo comum para a falha pode ser devido a um sistema operacional sem suporte. Use um sistema operacional com suporte e uma versão do kernel para uma instalação bem-sucedida do serviço de mobilidade. Evite o uso de patches privados.

Para exibir a lista de sistemas operacionais e versões de kernel com suporte pelo Azure Site Recovery, consulte o [documento matriz de suporte](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Configurações de disco de inicialização sem suporte (errorID: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-arent-the-same-disk-errorid-95309"></a>Volumes/partições de inicialização e sistema não são o mesmo disco (errorID: 95309)

Antes da versão 9,20, partições/volumes de inicialização e de sistema em discos diferentes eram uma configuração sem suporte. A partir da [versão 9,20](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery), há suporte para essa configuração.

### <a name="the-boot-disk-isnt-available-errorid-95310"></a>O disco de inicialização não está disponível (errorID: 95310)

Uma máquina virtual sem um disco de inicialização não pode ser protegida. Um disco de inicialização garante uma recuperação tranqüila de uma máquina virtual durante uma operação de failover. A ausência de um disco de inicialização resulta em uma falha ao inicializar o computador após o failover. Verifique se a máquina virtual contém um disco de inicialização e repita a operação. Além disso, não há suporte para vários discos de inicialização no mesmo computador.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Vários discos de inicialização presentes na máquina de origem (errorID: 95311)

Uma máquina virtual com vários discos de inicialização não é uma [configuração com suporte](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage).

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Partição do sistema em vários discos (errorID: 95313)

Antes da versão 9,20, uma partição raiz ou configuração de volume em vários discos era uma configuração sem suporte. A partir da [versão 9,20](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery), há suporte para essa configuração.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>Falha ao habilitar a proteção, pois o nome do dispositivo mencionado na configuração de GRUB em vez de UUID (errorID: 95320)

### <a name="possible-cause"></a>Causa possível

Os arquivos de configuração do GRUB (carregador unificado geral) (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/boot/Grub2/grub.cfg_ ou _/etc/default/grub_) podem conter o valor para a **raiz** dos parâmetros e **retomar** como os nomes de dispositivo reais em vez de um UUID (identificador universalmente exclusivo). Site Recovery exige a abordagem do UUID, pois os nomes dos dispositivos podem mudar na reinicialização da VM. Por exemplo, a VM pode não ficar online com o mesmo nome no failover e que resulte em problemas.

Por exemplo:

- A linha a seguir é do arquivo GRUB _/boot/Grub2/grub.cfg_:

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- A linha a seguir é do arquivo GRUB _/boot/grub/menu.lst_:

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

> [!NOTE]
> As linhas GRUB contêm nomes de dispositivo reais para a **raiz** de parâmetros e **resumem** em vez do UUID.

### <a name="how-to-fix"></a>Como corrigir

os nomes de dispositivo devem ser substituídos pelo UUID correspondente.

1. Localize o UUID do dispositivo executando o comando `blkid \<device name>` .

   Por exemplo:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Agora, substitua o nome do dispositivo pelo UUID no formato como `root=UUID=\<UUID>` . Por exemplo, se substituirmos os nomes de dispositivo com UUID para o parâmetro root e resume mencionado nos arquivos _/boot/Grub2/grub.cfg_, _/boot/Grub2/grub.cfg_ ou _/etc/default/grub_ , as linhas nos arquivos se parecerão com a seguinte linha:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Reinicie a proteção.

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>Instalação do serviço de mobilidade concluída com aviso para reinicialização (errorID: 95265 & 95266)

Site Recovery serviço de mobilidade tem muitos componentes, um dos quais é chamado de driver de filtro. O driver de filtro é carregado na memória do sistema somente durante uma reinicialização do sistema. As correções de driver de filtro só podem ser realizadas quando um novo driver de filtro é carregado no momento da reinicialização do sistema.

> [!IMPORTANT]
> Esse é um aviso e a replicação existente funcionará mesmo após a nova atualização do agente. Você pode optar por reinicializar sempre que desejar obter os benefícios do novo driver de filtro, mas se você não reinicializar, o driver de filtro antigo continuará funcionando. Portanto, após uma atualização sem uma reinicialização, exceto para o driver de filtro, os **benefícios de outros aprimoramentos e correções no serviço de mobilidade são obtidos**. Embora seja recomendado, não é obrigatório reinicializar após cada atualização. Para obter informações sobre quando uma reinicialização é obrigatória, defina a seção [reinicializar após a atualização do serviço de mobilidade](service-updates-how-to.md#reboot-after-mobility-service-upgrade) em atualizações de serviço no Azure site Recovery.

> [!TIP]
>Para obter as práticas recomendadas de agendamento de atualizações durante a janela de manutenção, consulte o [suporte para o sistema operacional/kernel mais recente](service-updates-how-to.md#support-for-latest-operating-systemskernels) em atualizações de serviço no Azure site Recovery.

## <a name="lvm-support-from-920-version"></a>Suporte do LVM começando na versão 9.20

Antes da versão 9,20, o LVM (Gerenciador de volumes lógicos) era compatível apenas com discos de dados. A `/boot` partição deve estar em uma partição de disco e não em um volume LVM.

A partir da [versão 9,20](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery), há suporte para o [disco do sistema operacional no LVM](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) .

## <a name="insufficient-space-errorid-95524"></a>Espaço insuficiente (errorID: 95524)

Quando o agente de mobilidade é copiado para o computador de origem, pelo menos 100 MB de espaço livre é necessário. Verifique se o computador de origem tem a quantidade necessária de espaço livre e repita a operação.

## <a name="low-system-resources"></a>Recursos do sistema insuficientes

As possíveis IDs de erro vistas para esse problema são 95572 e 95573. Esse problema ocorre quando o sistema tem pouca memória disponível e não é capaz de alocar memória para a instalação do serviço de mobilidade. Certifique-se de que memória suficiente tenha sido liberada para que a instalação prossiga e seja concluída com êxito.

## <a name="vss-installation-failures"></a>Falhas na instalação do VSS

A instalação do serviço de cópias de sombra de volume (VSS) faz parte da instalação do agente de mobilidade. Esse serviço é usado no processo para gerar pontos de recuperação consistentes com o aplicativo. Falhas durante a instalação do VSS podem ocorrer devido a vários motivos. Para identificar os erros exatos, consulte _C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_. Alguns dos erros comuns e das etapas de resolução são realçados na seção a seguir.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>Erro VSS-2147023170 [0x800706BE] - código de saída 511

Esse problema é visto com mais frequência quando o software antivírus está bloqueando as operações dos serviços de Azure Site Recovery.

Para resolver o problema:

1. Examine a lista de [exclusões de pasta do programa antivírus](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).
1. Siga as diretrizes publicadas pelo seu provedor de antivírus para desbloquear o registro de DLL no Windows.

### <a name="vss-error-7-0x7---exit-code-511"></a>Erro VSS 7 [0x7] - código de saída 511

Esse erro é um erro de tempo de execução que é causado porque não há memória suficiente para instalar o VSS. Aumente o espaço em disco para a conclusão bem-sucedida desta operação.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>Erro VSS-2147023824 [0x80070430] - código de saída 517

Esse erro ocorre quando o serviço do Provedor VSS do Azure Site Recovery é [marcado para exclusão](/previous-versions/ms838153(v=msdn.10)). Tente instalar o VSS manualmente no computador de origem executando o seguinte comando:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>Erro VSS-2147023841 [0x8007041F] - código de saída 512

Esse erro ocorre quando Azure Site Recovery banco de dados de serviço do provedor de VSS está [bloqueado](/previous-versions/ms833798(v=msdn.10)). Tente instalar o VSS manualmente no computador de origem executando o seguinte comando em um prompt de comando:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Quando houver uma falha, verifique se algum programa antivírus ou outros serviços estão presos em um estado **inicial** . Um processo em um estado de **início** pode manter o bloqueio nos serviços de banco de dados. Isso resultará em falhas na instalação do provedor VSS. Certifique-se de que nenhum serviço esteja em um estado de **início** e repita a operação acima.

### <a name="vss-exit-code-806"></a>Código de saída do VSS 806

Esse erro ocorre quando a conta de usuário usada para instalação não tem permissões para executar o `CSScript` comando. Forneça as permissões necessárias à conta do usuário para executar o script e repita a operação.

### <a name="other-vss-errors"></a>Outros erros do VSS

Tente instalar o serviço do provedor VSS manualmente no computador de origem executando o seguinte comando em um prompt de comando:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

## <a name="vss-error---0x8004e00f"></a>Erro do VSS-0x8004E00F

Esse erro normalmente ocorre durante a instalação do agente de mobilidade devido a problemas no `DCOM` e `DCOM` está em um estado crítico.

Use o procedimento a seguir para determinar a causa do erro.

### <a name="examine-the-installation-logs"></a>Examinar os logs de instalação

1. Abra o log de instalação localizado em _C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_.
2. A presença do erro a seguir indica esse problema:

    ```Output
    Unregistering the existing application...
    Create the catalogue object
    Get the collection of Applications

    ERROR:

    - Error code: -2147164145 [0x8004E00F]
    - Exit code: 802
    ```

Para resolver o problema:

Contate a [equipe da plataforma Microsoft Windows](https://aka.ms/Windows_Support) para obter assistência com a resolução do problema DCOM.

Quando o problema DCOM for resolvido, reinstale o provedor de Azure Site Recovery VSS manualmente usando o seguinte comando em um prompt de comando:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Se a consistência do aplicativo não for crítica para seus requisitos de recuperação de desastre, você poderá ignorar a instalação do provedor do VSS.

Para ignorar a instalação Azure Site Recovery do provedor VSS e instalar manualmente Azure Site Recovery pós-instalação do provedor VSS:

1. Instalar o serviço de Mobilidade. A instalação falhará na etapa: **pós-configuração de instalação**.
1. Para ignorar a instalação do VSS:
   1. Abra o diretório de instalação do serviço de mobilidade Azure Site Recovery localizado em:

      _C:\Arquivos de programas (x86) \Microsoft Azure site Recovery\agent_

   1. Modifique os scripts de instalação do provedor de VSS Azure Site Recovery _InMageVSSProvider_Install_ e _InMageVSSProvider_Uninstall. cmd_ para sempre ter sucesso adicionando as seguintes linhas:

      ```plaintext
      rem @echo off
      setlocal
      exit /B 0
      ```

1. Faça uma instalação manual do agente de mobilidade.
1. Quando a instalação for bem sucedido e passar para a próxima etapa, **Configure**, remova as linhas que você adicionou.
1. Para instalar o provedor VSS, abra um prompt de comando como administrador e execute o seguinte comando:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

1. Verifique se o provedor de VSS Azure Site Recovery está instalado como um serviço nos serviços do Windows. Abra o MMC do serviço de componentes para confirmar se o provedor VSS está listado.
1. Se a instalação do provedor do VSS continuar a falhar, trabalhe com o suporte técnico para resolver os erros de permissões na interface de programação de aplicativo de criptografia (CAPI2).

## <a name="vss-provider-installation-fails-because-the-cluster-service-being-enabled-on-non-cluster-machine"></a>A instalação do provedor VSS falha porque o serviço de cluster está habilitado no computador não clusterizado

Esse problema faz com que a instalação do agente de mobilidade do Azure Site Recovery falhe durante a instalação do provedor de VSS Azure Site Recovery. A falha é porque há um problema com `COM+` que impede a instalação do provedor VSS.

### <a name="to-identify-the-issue"></a>Para identificar o problema

No log localizado no servidor de configuração em _C:\ProgramData\ASRSetupLogs\UploadedLogs \<date-time> UA_InstallLogFile. log_ , você encontrará a seguinte exceção:

```plaintext
COM+ was unable to talk to the Microsoft Distributed Transaction Coordinator (Exception from HRESULT: 0x8004E00F)
```

Para resolver o problema:

1. Verifique se este computador é um computador não clusterizado e se os componentes do cluster não estão sendo usados.
1. Se os componentes não estiverem sendo usados, remova os componentes do cluster do computador.

## <a name="drivers-are-missing-on-the-source-server"></a>Drivers ausentes no servidor de origem

Se a instalação do agente de mobilidade falhar, examine os logs em _C:\ProgramData\ASRSetupLogs_ para determinar se alguns dos drivers necessários estão ausentes em alguns conjuntos de controle.

Para resolver o problema:

1. Usando um editor de registro como `regedit.msc` , abra o registro.
1. Abra o `HKEY_LOCAL_MACHINE\SYSTEM` nó.
1. No `SYSTEM` nó, localize os conjuntos de controles.
1. Abra cada conjunto de controle e verifique se os seguintes drivers do Windows estão presentes:

   * ATAPI
   * VMBus
   * Storflt
   * Storvsc
   * Intelide

1. Reinstale os drivers ausentes.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais](vmware-azure-tutorial.md) sobre como configurar a recuperação de desastre para VMs VMware.
