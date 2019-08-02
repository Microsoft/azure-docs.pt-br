---
title: Criação de pool de locatário e o host na área de trabalho de Virtual do Windows - Azure
description: Como resolver problemas quando você estiver configurando uma sessão e locatário host VM (máquina virtual) em um ambiente de área de trabalho Virtual do Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: v-chjenk
ms.openlocfilehash: 96a9d8fc7495ea473b0a3250b34251afc5f30c13
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786718"
---
# <a name="tenant-and-host-pool-creation"></a>Criação do pool de host e de locatário

Use este artigo para solucionar problemas que surgirem ao configurar as máquinas de virtuais de host de sessão de área de trabalho Virtual do Windows (VMs).

## <a name="provide-feedback"></a>Fornecer comentários

Atualmente, não estamos usando casos de suporte durante a versão prévia da Área de Trabalho Virtual do Windows. Visite a [Comunidade Tecnológica da Área de Trabalho Virtual do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para comentar sobre o serviço da Área de Trabalho Virtual do Windows com a equipe do produto e membros ativos da comunidade.

## <a name="vms-are-not-joined-to-the-domain"></a>VMs não ingressaram no domínio

Siga estas instruções se você estiver tendo problemas ao adicionar VMs ao domínio.

- Adicionar a máquina virtual manualmente usando o processo [ingressar uma máquina virtual Windows Server em um domínio gerenciado](https://docs.microsoft.com/azure/active-directory-domain-services/Active-directory-ds-admin-guide-join-windows-vm-portal) ou usando o [modelo de associação de domínio](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
- Tente executar o ping para o nome de domínio da linha de comando na VM.
- Examine a lista de mensagens de erro de associação de domínio em [solução de problemas de mensagens de erro de associação de domínio](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx).

### <a name="error-incorrect-credentials"></a>Erro: Credenciais incorretas

**Causa:** Ocorreu um erro de digitação feito quando as credenciais foram inseridas no Azure Resource Manager corrige de interface do modelo.

**Correção:** Siga estas instruções para corrigir as credenciais.

1. Adicione manualmente as VMs em um domínio.
2. Reimplante depois de confirmar as credenciais. Ver [criar um pool de host com o PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).
3. Adicionar VMs a um domínio usando um modelo com [une a uma VM existente do Windows ao domínio do AD](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).

### <a name="error-timeout-waiting-for-user-input"></a>Erro: Tempo limite ao aguardar a entrada do usuário

**Causa:** A conta usada para concluir o ingresso no domínio pode ter a autenticação multifator (MFA).

**Correção:** Siga estas instruções para concluir o ingresso no domínio.

1. Remova temporariamente o MFA para a conta.
2. Use uma conta de serviço.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Erro: A conta usada durante o provisionamento não tem permissões para concluir a operação

**Causa:** A conta que está sendo usada não tem permissões para ingressar no domínio devido à conformidade e regulamentos de VMs.

**Correção:** Siga estas instruções.

1. Use uma conta que seja membro do grupo de administrador.
2. Conceda as permissões necessárias para a conta que está sendo usada.

### <a name="error-domain-name-doesnt-resolve"></a>Erro: Nome de domínio não for resolvido

**Causa 1:** As VMs estão em um grupo de recursos que não está associada com a rede virtual (VNET) no qual o domínio está localizado.

**Corrigi 1:** Crie emparelhamento VNET entre a rede virtual em que as VMs foram provisionadas e a rede virtual onde o controlador de domínio (DC) está em execução. Ver [criar um emparelhamento de rede virtual – Resource Manager, assinaturas diferentes](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions).

**Causa 2:** Ao usar AadService (AADS), as entradas DNS não foram definidas.

**Corrigi 2:** Para definir os serviços de domínio, consulte [habilitar o Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns).

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>Agente de área de trabalho Virtual do Windows e o carregador de inicialização de área de trabalho Virtual do Windows não estão instalados

A maneira recomendada para provisionar VMs é usar o Azure Resource Manager **criar e provisionar o Windows de área de trabalho Virtual hospedam pool** modelo. O modelo automaticamente instala o agente de área de trabalho Virtual do Windows e o carregador de inicialização de agente do Virtual Desktop do Windows.

Siga estas instruções para confirmar que os componentes estão instalados e verifique se há mensagens de erro.

1. Confirme que os dois componentes são instalados por fazer check-in **painel de controle** > **programas** > **programas e recursos**. Se **agente de área de trabalho Virtual do Windows** e **carregador de inicialização de agente do Windows Virtual Desktop** não são visíveis, eles não são instalados na VM.
2. Abra **Explorador de arquivos** e navegue até **C:\Windows\Temp\scriptlogs.log**. Se o arquivo estiver ausente, ele indica que o DSC do PowerShell que instalaram os dois componentes não foi capaz de executar no contexto de segurança fornecido.
3. Se o arquivo **C:\Windows\Temp\scriptlogs.log** estiver presente, abri-lo e verificar se há mensagens de erro.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptlogslog-is-also-missing"></a>Erro: Agente de área de trabalho Virtual do Windows e o carregador de inicialização de agente do Virtual Desktop do Windows estão ausentes. C:\Windows\Temp\scriptlogs.log também está ausente

**Causa 1:** As credenciais fornecidas durante a entrada para o modelo do Azure Resource Manager estavam incorretas ou as permissões estavam insuficientes.

**Corrigi 1:** Adicionar manualmente os componentes ausentes para as VMs usando [criar um pool de host com o PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

**Causa 2:** DSC do PowerShell não conseguiu iniciar e executar, mas falhou ao concluir porque ele não é possível entrar área de trabalho Virtual do Windows e obter as informações necessárias.

**Corrigi 2:** Confirme se os itens na lista a seguir.

- Verifique se que a conta não tiver a MFA.
- Confirme que o nome do locatário sejam preciso e o locatário existe na área de trabalho Virtual do Windows.
- Confirme a conta tem pelo menos permissões de Colaborador de RDS.

### <a name="error-authentication-failed-error-in-cwindowstempscriptlogslog"></a>Erro: Falha na autenticação, erro na C:\Windows\Temp\scriptlogs.log

**Causa:** PowerShell DSC foi capaz de executar, mas não foi possível conectar à área de trabalho Virtual do Windows.

**Correção:** Confirme se os itens na lista a seguir.

- Registre manualmente as máquinas virtuais com o serviço de área de trabalho Virtual do Windows.
- Confirme se a conta usada para conectar-se a área de trabalho Virtual do Windows tem permissões no locatário para criar pools de host.
- Confirme se a conta não tiver a MFA.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>Agente de área de trabalho Virtual do Windows não está registrando com o serviço de área de trabalho Virtual do Windows

Quando o agente de área de trabalho Virtual do Windows é instalado pela primeira vez na sessão de hospedar máquinas virtuais (tanto manualmente ou através do modelo do Resource Manager e PowerShell DSC), ele fornece um token de registro. A seção a seguir aborda a solução de problemas aplicável para o agente de área de trabalho Virtual do Windows e o token.

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>Erro: O status arquivado no cmdlet Get-RdsSessionHost mostra o status como não disponível

![Cmdlet Get-RdsSessionHost mostra o status como indisponível.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Causa:** O agente não é capaz de se atualizar para uma nova versão.

**Correção:** Siga estas instruções para atualizar manualmente o agente.

1. Baixe uma nova versão do agente na VM do host de sessão.
2. Inicie o Gerenciador de tarefas e, na guia do serviço, interrompa o serviço de RDAgentBootLoader.
3. Execute o instalador para a nova versão do agente de área de trabalho Virtual do Windows.
4. Quando solicitado para o token de registro, remova a entrada INVALID_TOKEN e pressione Avançar (um novo token não é necessário).
5. Conclua o Assistente de instalação.
6. Abra o Gerenciador de tarefas e iniciar o serviço RDAgentBootLoader.

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Erro:  Entrada de registro do agente de área de trabalho Virtual do Windows IsRegistered mostra um valor de 0

**Causa:** Token de registro expirou ou foi gerado com o valor de expiração do 999999.

**Correção:** Siga estas instruções para corrigir o erro de registro do agente.

1. Se já houver um token de registro, você deve removê-lo com Remove-RDSRegistrationInfo.
2. Gere novo token NewRegistrationInfo Rds.
3. Confirme se o parâmetro - ExpriationHours é definido como 72 (o valor máximo é 99999).

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>Erro: Agente de área de trabalho Virtual do Windows não está relatando uma pulsação ao executar Get-RdsSessionHost

**Causa 1:** RDAgentBootLoader serviço foi interrompido.

**Corrigi 1:** Inicie o Gerenciador de tarefas e, se a guia serviço relata um status parado para serviço RDAgentBootLoader, inicie o serviço.

**Causa 2:** A porta 443 esteja fechada.

**Corrigi 2:** Siga estas instruções para abrir a porta 443.

1. Confirme se a porta 443 está aberta, baixando a ferramenta PSPing [ferramentas do Sysinternal](https://docs.microsoft.com/sysinternals/downloads/psping).
2. Instale o PSPing em VM em que o agente está em execução no host da sessão.
3. Abra o prompt de comando como administrador e execute o comando a seguir:

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. Confirme essas informações PSPing recebida do RDBroker:

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

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>Solução de problemas com a pilha de lado a lado da área de trabalho Virtual do Windows

A pilha de lado a lado da área de trabalho Virtual do Windows é instalada automaticamente com o Windows Server 2019. Use o Microsoft Installer (MSI) para instalar a pilha de lado a lado no Microsoft Windows Server 2016 ou Windows Server 2012 R2. Para o Microsoft Windows 10, a pilha de lado a lado da área de trabalho Virtual do Windows está habilitada com **enablesxstackrs.ps1**.

Há três maneiras principais de pilha side-by-side obtém instalada ou habilitada no pool de host de sessão VMs:

- Com o Azure Resource Manager **criar e provisionar novo Windows de área de trabalho Virtual host pool** modelo
- Por que está sendo incluído e habilitado na imagem mestre
- Instalado ou habilitado manualmente em cada VM (ou com extensões/PowerShell)

Se você estiver tendo problemas com a pilha de lado a lado da área de trabalho Virtual do Windows, digite o **qwinsta** comando no prompt de comando para confirmar que a pilha de lado a lado é instalada ou habilitada.

A saída de **qwinsta** listará **rdp sxs** na saída se a pilha de lado a lado está instalada e habilitada.

![Pilha side-by-side instalado ou habilitado com o qwinsta listado como rdp-sxs na saída.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Examine as entradas do Registro listadas abaixo e confirme que seus valores correspondam. Se as chaves do registro estão ausentes ou valores forem incompatíveis, siga as instruções em [criar um pool de host com o PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) sobre como reinstalar a pilha de lado a lado.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-oreverseconnectstackfailure"></a>Erro: O_REVERSE_CONNECT_STACK_FAILURE

![Código de erro O_REVERSE_CONNECT_STACK_FAILURE.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Causa:** A pilha de lado a lado não está instalada na VM do host de sessão.

**Correção:** Siga estas instruções para instalar a pilha de lado a lado na VM do host de sessão.

1. Use o protocolo de área de trabalho remota (RDP) para obter diretamente para a VM do host de sessão como administrador local.
2. Baixe e importe [o Windows Virtual Desktop módulo do PowerShell](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) usar em sua sessão do PowerShell se você ainda não fez isso.
3. Instalar a pilha de lado a lado usando [criar um pool de host com o PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>Como corrigir uma pilha de lado a lado de área de trabalho Virtual do Windows funciona incorretamente

Há circunstâncias que podem fazer com que a pilha de lado a lado não funcione corretamente conhecidos:

- Não seguir a ordem correta das etapas para habilitar a pilha de lado a lado
- Atualização automática para Windows 10 aprimorada versátil disco (EVD)
- Tem a função de Host de sessão de área de trabalho remota (RDSH)
- Executando enablesxsstackrc.ps1 várias vezes
- Executando enablesxsstackrc.ps1 em uma conta que não tem privilégios de administrador local

As instruções nesta seção podem ajudá-lo a desinstalar a pilha de área de trabalho Virtual do Windows lado a lado. Depois de desinstalar a pilha de lado a lado, vá para "Registrar a VM com o pool de área de trabalho de host Virtual do Windows" [criar um pool de host com o PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) reinstalar a pilha de lado a lado.

A VM usada para executar a correção deve estar no mesmo domínio e subrede da VM com a pilha de lado a lado com defeito.

Siga estas instruções para executar a correção na mesma sub-rede e domínio:

1. Conecte-se com o padrão protocolo RDP (Remote Desktop) para a VM de onde a correção será aplicada.
2. Baixar o PsExec de https://docs.microsoft.com/sysinternals/downloads/psexec.
3. Descompacte o arquivo baixado.
4. Inicie o prompt de comando como administrador local.
5. Navegue até a pasta onde o PsExec foi descompactado.
6. Do prompt de comando, use o seguinte comando:

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >VMname é o nome do computador da VM com a pilha de lado a lado com defeito.

7. Aceite o contrato de licença do PsExec ao clicar em Concordo.

    ![Tela de contrato de licença de software.](media/SoftwareLicenseTerms.png)

    >[!Note]
    >Essa caixa de diálogo será exibida apenas na primeira vez em que o PsExec é executado.

8. Depois que a sessão de prompt de comando é aberta na VM com a pilha de lado a lado com defeito, execute o qwinsta e confirme que uma entrada denominada sxs rdp está disponível. Caso contrário, uma pilha de lado a lado não estiver presente na VM para que o problema não está vinculado a pilha de lado a lado.

    ![Prompt de comando do administrador](media/AdministratorCommandPrompt.png)

9. Execute o comando a seguir, que lista os componentes da Microsoft instalados na VM com a pilha de lado a lado com defeito.

    ```cmd
        wmic product get name
    ```

10. Execute o comando abaixo com nomes de produto da etapa acima.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. Desinstale todos os produtos que começam com "Área de trabalho remota."

12. Depois que todos os componentes de área de trabalho Virtual do Windows tem sido desinstalados, siga as instruções para seu sistema operacional:

13. Se seu sistema operacional for Windows Server, reinicie a VM que tinha a pilha de lado a lado com defeito (ou com o portal do Azure ou da ferramenta PsExec).

Se seu sistema operacional é o Microsoft Windows 10, continue com as instruções abaixo:

14. Na VM que executa o PsExec, abra o Explorador de arquivos e copie disablesxsstackrc.ps1 à unidade do sistema da VM com a pilha side-by-side malfunctioned.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname é o nome do computador da VM com a pilha de lado a lado com defeito.

15. O processo recomendado: na ferramenta PsExec, inicie o PowerShell e navegue até a pasta da etapa anterior e execute disablesxsstackrc.ps1. Como alternativa, você pode executar os cmdlets a seguir:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. Quando terminar os cmdlets em execução, reinicie a VM com a pilha de lado a lado com defeito.

## <a name="remote-licensing-model-is-not-configured"></a>Modelo de licença remoto não está configurado

Se você entrar várias sessões de Windows 10 Enterprise usando uma conta administrativa, você poderá receber uma notificação que diz: "modo de licenciamento de área de trabalho remota não está configurado, os serviços de área de trabalho remota vai parar de funcionar em X dias. No servidor do agente de Conexão, use o Gerenciador do servidor para especificar o modo de licenciamento de área de trabalho remota". Se você vir essa mensagem, isso significa que você precisa configurar manualmente o modo de licenciamento **por usuário**.

Para configurar manualmente o modo de licenciamento:  

1. Vá para seu **menu Iniciar** caixa de pesquisa, em seguida, localize e abra **gpedit. msc** para acessar o editor de diretiva de grupo local. 
2. Vá para **configuração do computador** > **modelos administrativos** > **componentes do Windows**  >   **Serviços de área de trabalho remota** > **Host da sessão da área de trabalho remota** > **licenciamento**. 
3. Selecione **definir o modo de licenciamento de área de trabalho remota** e altere-a para **por usuário**.

Estamos no momento, analisando problemas de tempo limite de notificação e o período de carência e planeja resolvê-los em uma atualização futura. 

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral sobre solução de área de trabalho Virtual do Windows e as faixas de escalonamento de bloqueios, consulte [solução de problemas de visão geral, comentários e suporte](troubleshoot-set-up-overview.md).
- Para solucionar problemas durante a criação de um pool de locatário e o host em um ambiente de área de trabalho Virtual do Windows, consulte [criação de pool de locatário e host](troubleshoot-set-up-issues.md).
- Para solucionar problemas ao configurar uma máquina virtual (VM) na área de trabalho Virtual do Windows, consulte [configuração de máquina virtual do host de sessão](troubleshoot-vm-configuration.md).
- Para solucionar problemas com conexões de cliente de área de trabalho Virtual do Windows, consulte [conexões de cliente de área de trabalho remota](troubleshoot-client-connection.md).
- Para solucionar problemas ao usar o PowerShell com a área de trabalho Virtual do Windows, consulte [Windows PowerShell de área de trabalho Virtual](troubleshoot-powershell.md).
- Para saber mais sobre o serviço de visualização, consulte [ambiente de visualização de área de trabalho do Windows](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Para percorrer um tutorial de solução de problemas, confira [Tutorial: Solucionar problemas de implantações de modelo do Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Para saber sobre as ações de auditoria, consulte [Auditar operações com o Gerenciador de Recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Para saber sobre as ações para determinar os erros durante a implantação, consulte [Exibir operações de implantação](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).