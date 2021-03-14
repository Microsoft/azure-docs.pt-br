---
title: Solucionar problemas do host de sessão de área de trabalho virtual do Windows
description: Como resolver problemas quando estiver configurando máquinas virtuais do host de sessão de área de trabalho virtual do Windows.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f1f2caa194972bb870dc58cfa0bd6ccb2f8db053
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100099907"
---
# <a name="session-host-virtual-machine-configuration"></a>Configuração da máquina virtual do host da sessão

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver usando a Área de Trabalho Virtual do Windows (clássica), sem objetos do Azure Resource Manager, confira [este artigo](./virtual-desktop-fall-2019/troubleshoot-vm-configuration-2019.md).

Use este artigo para solucionar problemas que você está tendo ao configurar as VMs (máquinas virtuais) do host de sessão de área de trabalho virtual do Windows.

## <a name="provide-feedback"></a>Fornecer comentários

Visite a [Comunidade Tecnológica da Área de Trabalho Virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para comentar sobre o serviço da Área de Trabalho Virtual do Windows com a equipe do produto e membros ativos da comunidade.

## <a name="vms-are-not-joined-to-the-domain"></a>As VMs não ingressaram no domínio

Siga estas instruções se você estiver tendo problemas para ingressar máquinas virtuais (VMs) no domínio.

- Ingresse a VM manualmente usando o processo em [ingressar uma máquina virtual do Windows Server em um domínio gerenciado](../active-directory-domain-services/join-windows-vm.md) ou usando o [modelo de ingresso no domínio](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
- Tente executar o ping no nome de domínio por meio de uma linha de comando na VM.
- Examine a lista de mensagens de erro de ingresso no domínio em [Solucionando problemas de mensagens de erro de ingresso no domínio](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx).

### <a name="error-incorrect-credentials"></a>Erro: credenciais incorretas

**Causa:** Houve um erro de digitação quando as credenciais foram inseridas nas correções da interface do modelo de Azure Resource Manager.

**Correção:** Execute uma das ações a seguir para resolver.

- Adicione manualmente as VMs a um domínio.
- Reimplante o modelo depois que as credenciais tiverem sido confirmadas. Consulte [criar um pool de hosts com o PowerShell](create-host-pools-powershell.md).
- Ingresse VMs em um domínio usando um modelo com [une uma VM do Windows existente ao domínio do AD](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).

### <a name="error-timeout-waiting-for-user-input"></a>Erro: tempo limite de espera para a entrada do usuário

**Causa:** A conta usada para concluir o ingresso no domínio pode ter a autenticação multifator (MFA).

**Correção:** Execute uma das ações a seguir para resolver.

- Remova temporariamente a MFA da conta.
- Use uma conta de serviço.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Erro: a conta usada durante o provisionamento não tem permissões para concluir a operação

**Causa:** A conta que está sendo usada não tem permissões para unir VMs ao domínio devido a conformidade e regulamentos.

**Correção:** Execute uma das ações a seguir para resolver.

- Use uma conta que seja membro do grupo de administradores.
- Conceda as permissões necessárias para a conta que está sendo usada.

### <a name="error-domain-name-doesnt-resolve"></a>Erro: o nome de domínio não é resolvido

**Causa 1:** As VMs estão em uma rede virtual que não está associada à rede virtual (VNET) em que o domínio está localizado.

**Correção 1:** Crie um emparelhamento VNET entre a VNET em que as VMs foram provisionadas e a VNET em que o DC (controlador de domínio) está em execução. Consulte [criar um emparelhamento de rede virtual – Gerenciador de recursos, assinaturas diferentes](../virtual-network/create-peering-different-subscriptions.md).

**Causa 2:** Ao usar Azure Active Directory Domain Services (Azure AD DS), a rede virtual não tem suas configurações de servidor DNS atualizadas para apontar para os controladores de domínio gerenciados.

**Correção 2:** Para atualizar as configurações de DNS para a rede virtual que contém o AD DS do Azure, consulte [Atualizar configurações de DNS para a rede virtual do Azure](../active-directory-domain-services/tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network).

**Causa 3:** As configurações do servidor DNS da interface de rede não apontam para o servidor DNS apropriado na rede virtual.

**Correção 3:** Execute uma das ações a seguir para resolver, seguindo as etapas em [alterar servidores DNS].
- Altere as configurações do servidor DNS da interface de rede para **personalizado** com as etapas de [alterar servidores DNS](../virtual-network/virtual-network-network-interface.md#change-dns-servers) e especifique os endereços IP privados dos servidores DNS na rede virtual.
- Altere as configurações do servidor DNS da interface de rede para **herdar da rede virtual** com as etapas de [alterar servidores DNS](../virtual-network/virtual-network-network-interface.md#change-dns-servers)e, em seguida, altere as configurações do servidor DNS da rede virtual com as etapas de [alterar servidores DNS](../virtual-network/manage-virtual-network.md#change-dns-servers).

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>O agente de área de trabalho virtual do Windows e o carregador de inicialização do Windows Virtual Desktop não estão instalados

A maneira recomendada para provisionar VMs é usar o modelo de criação de portal do Azure. O modelo instala automaticamente o agente de área de trabalho virtual do Windows e o carregador de inicialização do agente de desktop virtual do Windows.

Siga estas instruções para confirmar se os componentes estão instalados e para verificar se há mensagens de erro.

1. Confirme se os dois componentes estão instalados marcando em **painel de controle**  >  **programas** programas  >  **e recursos**. Se o **agente de área de trabalho virtual do Windows** e o carregador de inicialização do **Windows Virtual Desktop Agent** não estiverem visíveis, eles não serão instalados na VM.
2. Abra o **Explorador de arquivos** e navegue até **C:\Windows\Temp\ScriptLog.log**. Se o arquivo estiver ausente, isso indica que a DSC do PowerShell que instalou os dois componentes não pôde ser executada no contexto de segurança fornecido.
3. Se o arquivo **C:\Windows\Temp\ScriptLog.log** estiver presente, abra-o e verifique se há mensagens de erro.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptloglog-is-also-missing"></a>Erro: o agente de área de trabalho virtual do Windows e o carregador de inicialização do Windows Virtual Desktop Agent estão ausentes. C:\Windows\Temp\ScriptLog.log também está ausente

**Causa 1:** As credenciais fornecidas durante a entrada para o modelo de Azure Resource Manager estavam incorretas ou as permissões eram insuficientes.

**Correção 1:** Adicione manualmente os componentes ausentes às VMs usando [criar um pool de hosts com o PowerShell](create-host-pools-powershell.md).

**Causa 2:** O DSC do PowerShell foi capaz de iniciar e executar, mas não foi concluído, pois não conseguiu entrar na área de trabalho virtual do Windows e obter as informações necessárias.

**Correção 2:** Confirme os itens na lista a seguir.

- Verifique se a conta não tem MFA.
- Confirme se o nome do pool de hosts é preciso e se o pool de hosts existe na área de trabalho virtual do Windows.
- Confirme se a conta tem pelo menos permissões de colaborador na assinatura do Azure ou no grupo de recursos.

### <a name="error-authentication-failed-error-in-cwindowstempscriptloglog"></a>Erro: falha na autenticação, erro em C:\Windows\Temp\ScriptLog.log

**Causa:** O DSC do PowerShell foi capaz de executar, mas não pôde se conectar à área de trabalho virtual do Windows.

**Correção:** Confirme os itens na lista a seguir.

- Registre manualmente as VMs com o serviço de área de trabalho virtual do Windows.
- Confirme que a conta usada para conexão com a área de trabalho virtual do Windows tem permissões na assinatura do Azure ou no grupo de recursos para criar pools de hosts.
- A conta de confirmação não tem MFA.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>O agente de área de trabalho virtual do Windows não está se registrando no serviço de área de trabalho virtual do Windows

Quando o agente de área de trabalho virtual do Windows é instalado pela primeira vez em VMs de host de sessão (manualmente ou por meio do modelo de Azure Resource Manager e DSC do PowerShell), ele fornece um token de registro. A seção a seguir aborda a solução de problemas que se aplicam ao agente de área de trabalho virtual do Windows e ao token.

### <a name="error-the-status-filed-in-get-azwvdsessionhost-cmdlet-shows-status-as-unavailable"></a>Erro: o status registrado em Get-AzWvdSessionHost cmdlet mostra o status como indisponível

> [!div class="mx-imgBorder"]
> ![O cmdlet Get-AzWvdSessionHost mostra o status como indisponível.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Causa:** O agente não é capaz de se atualizar para uma nova versão.

**Correção:** Siga estas instruções para atualizar manualmente o agente.

1. Baixe uma nova versão do agente na VM host da sessão.
2. Inicie o Gerenciador de tarefas e, na guia serviço, interrompa o serviço RDAgentBootLoader.
3. Execute o instalador para a nova versão do agente de área de trabalho virtual do Windows.
4. Quando for solicitado o token de registro, remova a entrada INVALID_TOKEN e pressione Avançar (um novo token não é necessário).
5. Conclua o assistente de instalação.
6. Abra o Gerenciador de tarefas e inicie o serviço RDAgentBootLoader.

## <a name="error-windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Erro: a entrada do registro isregister do agente de área de trabalho virtual do Windows mostra um valor de 0

**Causa:** O token de registro expirou.

**Correção:** Siga estas instruções para corrigir o erro de registro do agente.

1. Se já houver um token de registro, remova-o com Remove-AzWvdRegistrationInfo.
2. Execute o cmdlet **New-AzWvdRegistrationInfo** para gerar um novo token.
3. Confirme se o parâmetro *-ExpriationTime* está definido como 3 dias.

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-azwvdsessionhost"></a>Erro: o agente de área de trabalho virtual do Windows não relata uma pulsação ao executar Get-AzWvdSessionHost

**Causa 1:** O serviço RDAgentBootLoader foi interrompido.

**Correção 1:** Inicie o Gerenciador de tarefas e, se a guia serviço relatar um status parado para o serviço RDAgentBootLoader, inicie o serviço.

**Causa 2:** A porta 443 pode estar fechada.

**Correção 2:** Siga estas instruções para abrir a porta 443.

1. Confirme se a porta 443 está aberta baixando a ferramenta PSPing de [Ferramentas do Sysinternal](/sysinternals/downloads/psping/).
2. Instale o PSPing na VM host da sessão em que o agente está em execução.
3. Abra o prompt de comando como administrador e emita o comando a seguir:

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. Confirme se o PSPing recebeu informações de volta do RDBroker:

    ```
    PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2016 Mark Russinovich
    Sysinternals - www.sysinternals.com
    TCP connect to 13.77.160.237:443:
    5 iterations (warmup 1) ping test:
    Connecting to 13.77.160.237:443 (warmup): from 172.20.17.140:60649: 2.00ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60650: 3.83ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60652: 2.21ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60653: 2.14ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60654: 2.12ms
    TCP connect statistics for 13.77.160.237:443:
    Sent = 4, Received = 4, Lost = 0 (0% loss),
    Minimum = 2.12ms, Maximum = 3.83ms, Average = 2.58ms
    ```

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>Solucionando problemas com a pilha lado a lado da área de trabalho virtual do Windows

A pilha lado a lado da área de trabalho virtual do Windows é instalada automaticamente com o Windows Server 2019. Use o MSI (Microsoft Installer) para instalar a pilha lado a lado no Microsoft Windows Server 2016 ou no Windows Server 2012 R2. Para o Microsoft Windows 10, a pilha lado a lado da área de trabalho virtual do Windows é habilitada com **enablesxstackrs.ps1**.

Há três maneiras principais pelas quais a pilha lado a lado é instalada ou habilitada nas VMs do pool de hosts de sessão:

- Com o modelo de criação de portal do Azure
- Ao ser incluído e habilitado na imagem mestra
- Instalado ou habilitado manualmente em cada VM (ou com extensões/PowerShell)

Se você estiver tendo problemas com a pilha lado a lado da área de trabalho virtual do Windows, digite o comando **Qwinsta** no prompt de comando para confirmar se a pilha lado a lado está instalada ou habilitada.

A saída de **Qwinsta** listará o **RDP-SxS** na saída se a pilha lado a lado estiver instalada e habilitada.

> [!div class="mx-imgBorder"]
> ![Pilha lado a lado instalada ou habilitada com Qwinsta listado como RDP-SxS na saída.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Examine as entradas de registro listadas abaixo e confirme se seus valores correspondem. Se as chaves do registro estiverem ausentes ou se os valores forem incompatíveis, verifique se você está executando [um sistema operacional com suporte](troubleshoot-agent.md#error-operating-a-pro-vm-or-other-unsupported-os). Se você estiver, siga as instruções em [criar um pool de hosts com o PowerShell](create-host-pools-powershell.md) sobre como reinstalar a pilha lado a lado.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>Erro: O_REVERSE_CONNECT_STACK_FAILURE

> [!div class="mx-imgBorder"]
> ![O_REVERSE_CONNECT_STACK_FAILURE código de erro.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Causa:** A pilha lado a lado não está instalada na VM host da sessão.

**Correção:** Siga estas instruções para instalar a pilha lado a lado na VM host de sessão.

1. Use protocolo RDP (RDP) para obter diretamente a VM host da sessão como administrador local.
2. Instale a pilha lado a lado usando [criar um pool de hosts com o PowerShell](create-host-pools-powershell.md).

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>Como corrigir uma pilha lado a lado da área de trabalho virtual do Windows que não funciona corretamente

Há circunstâncias conhecidas que podem causar o mau funcionamento da pilha lado a lado:

- Não seguindo a ordem correta das etapas para habilitar a pilha lado a lado
- Atualização automática para o disco versátil aprimorado do Windows 10 (EVD)
- Função de Host da Sessão da Área de Trabalho Remota (RDSH) ausente
- Executando o enablesxsstackrc.ps1 várias vezes
- Executando enablesxsstackrc.ps1 em uma conta que não tem privilégios de administrador local

As instruções nesta seção podem ajudá-lo a desinstalar a pilha lado a lado da área de trabalho virtual do Windows. Depois de desinstalar a pilha lado a lado, vá para "registrar a VM com o pool de hosts da área de trabalho virtual do Windows" em [criar um pool de hosts com o PowerShell](create-host-pools-powershell.md) para reinstalar a pilha lado a lado.

A VM usada para executar a correção deve estar na mesma sub-rede e domínio que a VM com a pilha lado a lado com funcionamento inadequado.

Siga estas instruções para executar a correção da mesma sub-rede e domínio:

1. Conecte-se com o protocolo RDP padrão (RDP) à VM de onde a correção será aplicada.
2. Baixe o PsExec de https://docs.microsoft.com/sysinternals/downloads/psexec .
3. Descompacte o arquivo baixado.
4. Inicie o prompt de comando como administrador local.
5. Navegue até a pasta em que o PsExec foi descompactado.
6. No prompt de comando, use o seguinte comando:

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!NOTE]
    >VMname é o nome do computador da VM com a pilha lado a lado defeituosa.

7. Aceite o contrato de licença do PsExec clicando em Concordo.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela do contrato de licença de software.](media/SoftwareLicenseTerms.png)

    >[!NOTE]
    >Esta caixa de diálogo será mostrada apenas na primeira vez que o PsExec for executado.

8. Depois que a sessão de prompt de comando for aberta na VM com a pilha lado a lado com problemas de funcionamento, execute Qwinsta e confirme se uma entrada chamada RDP-SxS está disponível. Caso contrário, uma pilha lado a lado não está presente na VM, portanto, o problema não está vinculado à pilha lado a lado.

    > [!div class="mx-imgBorder"]
    > ![Prompt de comando do administrador](media/AdministratorCommandPrompt.png)

9. Execute o comando a seguir, que listará os componentes da Microsoft instalados na VM com a pilha lado a lado com funcionamento inadequado.

    ```cmd
        wmic product get name
    ```

10. Execute o comando abaixo com nomes de produtos da etapa acima.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. Desinstale todos os produtos que começam com "Área de Trabalho Remota".

12. Depois que todos os componentes da área de trabalho virtual do Windows tiverem sido desinstalados, siga as instruções para seu sistema operacional:

13. Se o seu sistema operacional for o Windows Server, reinicie a VM que tinha a pilha lado a lado com problemas (seja com portal do Azure ou da ferramenta PsExec).

Se o seu sistema operacional for o Microsoft Windows 10, continue com as instruções abaixo:

14. Na VM que executa o PsExec, abra o explorador de arquivos e copie disablesxsstackrc.ps1 para a unidade do sistema da VM com a pilha lado a lado com defeito.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname é o nome do computador da VM com a pilha lado a lado defeituosa.

15. O processo recomendado: na ferramenta PsExec, inicie o PowerShell e navegue até a pasta da etapa anterior e execute disablesxsstackrc.ps1. Como alternativa, você pode executar os seguintes cmdlets:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. Quando os cmdlets forem concluídos em execução, reinicie a VM com a pilha lado a lado com problemas.

## <a name="remote-desktop-licensing-mode-isnt-configured"></a>O modo de licenciamento Área de Trabalho Remota não está configurado

Se você entrar no Windows 10 Enterprise Multi-Session usando uma conta administrativa, poderá receber uma notificação dizendo que "o modo de licenciamento Área de Trabalho Remota não está configurado, Serviços de Área de Trabalho Remota deixará de funcionar em X dias. No servidor do agente de conexão, use Gerenciador do Servidor para especificar o modo de licenciamento de Área de Trabalho Remota ".

Se o limite de tempo expirar, será exibida uma mensagem de erro dizendo "a sessão remota foi desconectada porque não há licenças de acesso de cliente Área de Trabalho Remota disponíveis para este computador".

Se você vir uma dessas mensagens, isso significa que a imagem não tem as atualizações mais recentes do Windows instaladas ou que você está definindo o modo de licenciamento Área de Trabalho Remota por meio da diretiva de grupo. Siga as etapas nas próximas seções para verificar a configuração da política de grupo, identificar a versão do Windows 10 Enterprise Multi-Session e instalar a atualização correspondente.

>[!NOTE]
>A área de trabalho virtual do Windows requer uma CAL (licença de acesso para cliente) do RDS quando o pool de hosts contém hosts de sessão do Windows Server. Para saber como configurar um RDS CAL, confira [licenciar sua implantação de RDS com licenças de acesso para cliente](/windows-server/remote/remote-desktop-services/rds-client-access-license/).

### <a name="disable-the-remote-desktop-licensing-mode-group-policy-setting"></a>Desabilitar a configuração de política de grupo do modo de licenciamento Área de Trabalho Remota

Verifique a configuração da política de grupo abrindo o editor de política de grupo na VM e navegando até **modelos administrativos**  >  **componentes do Windows**  >  **serviços de área de trabalho remota**  >  **host da sessão da área de trabalho remota**  >  **Licenciamento**  >  **defina o modo de licenciamento área de trabalho remota**. Se a configuração política de grupo estiver **habilitada**, altere-a para **desabilitado**. Se ele já estiver desabilitado, deixe-o como está.

>[!NOTE]
>Se você definir a política de grupo por meio de seu domínio, desabilite essa configuração em políticas direcionadas a essas VMs de várias sessões do Windows 10 Enterprise.

### <a name="identify-which-version-of-windows-10-enterprise-multi-session-youre-using"></a>Identificar qual versão do Windows 10 Enterprise Multi-Session você está usando

Para verificar qual versão do Windows 10 Enterprise Multi-Session você tem:

1. Entre com sua conta de administrador.
2. Digite "sobre" na barra de pesquisa ao lado do menu iniciar.
3. Selecione **sobre seu PC**.
4. Verifique o número ao lado de "versão". O número deve ser "1809" ou "1903", conforme mostrado na imagem a seguir.

    > [!div class="mx-imgBorder"]
    > ![Uma captura de tela da janela especificações do Windows. O número de versão é realçado em azul.](media/windows-specifications.png)

Agora que você conhece o número de versão, pule para a seção relevante.

### <a name="version-1809"></a>Versão 1809

Se o seu número de versão diz "1809", instale [a atualização do KB4516077](https://support.microsoft.com/help/4516077).

### <a name="version-1903"></a>Versão 1903

Reimplante o sistema operacional do host com a versão mais recente da imagem do Windows 10, versão 1903 da galeria do Azure.

## <a name="we-couldnt-connect-to-the-remote-pc-because-of-a-security-error"></a>Não foi possível conectar ao computador remoto devido a um erro de segurança

Se os usuários veem um erro que diz "não foi possível conectar-se ao computador remoto devido a um erro de segurança. Se isso continuar acontecendo, peça ao administrador ou suporte técnico para obter ajuda "validar as políticas existentes que alteram as permissões de RDP padrão. Uma política que pode causar esse erro aparece é "permitir logon por meio de Serviços de Área de Trabalho Remota política de segurança".

Para saber mais sobre essa política, consulte [Permitir logon por meio de serviços de área de trabalho remota](/windows/security/threat-protection/security-policy-settings/allow-log-on-through-remote-desktop-services).

## <a name="next-steps"></a>Próximas etapas

- Confira uma visão geral da solução de problemas da Área de Trabalho Virtual do Windows e das faixas de escalonamento em [Visão geral da solução de problemas, comentários e suporte](troubleshoot-set-up-overview.md).
- Para solucionar problemas durante a criação de um pool de hosts em um ambiente de área de trabalho virtual do Windows, consulte [ambiente e criação de pool de hosts](troubleshoot-set-up-issues.md).
- Confira como solucionar problemas ao configurar uma VM (máquina virtual) na Área de Trabalho Virtual do Windows em [Configuração da máquina virtual do host da sessão](troubleshoot-vm-configuration.md).
- Para solucionar problemas relacionados ao agente de área de trabalho virtual do Windows ou conectividade de sessão, consulte [solucionar problemas comuns do agente de área de trabalho virtual do Windows](troubleshoot-agent.md).
- Para solucionar problemas com conexões de cliente de área de trabalho virtual do Windows, consulte [conexões do serviço área de trabalho virtual do Windows](troubleshoot-service-connection.md).
- Para solucionar problemas com clientes Área de Trabalho Remota, consulte [solucionar problemas do cliente área de trabalho remota](troubleshoot-client.md)
- Veja como solucionar problemas ao usar o PowerShell com a Área de Trabalho Virtual do Windows em [PowerShell da Área de Trabalho Virtual do Windows](troubleshoot-powershell.md).
- Para saber mais sobre o serviço, consulte [ambiente de área de trabalho virtual do Windows](environment-setup.md).
- Acompanhe um tutorial de solução de problemas em [Tutorial: Solucionar problemas de implantações de modelos do Resource Manager](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Para saber sobre as ações de auditoria, consulte [Auditar operações com o Gerenciador de Recursos](../azure-resource-manager/management/view-activity-logs.md).
- Para saber sobre as ações para determinar os erros durante a implantação, consulte [Exibir operações de implantação](../azure-resource-manager/templates/deployment-history.md).
