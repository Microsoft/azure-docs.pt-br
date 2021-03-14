---
title: Solucionar problemas do agente de área de trabalho virtual do Windows – Azure
description: Como resolver problemas comuns de agente e conectividade.
author: Sefriend
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: b0fc5bd16aaa455ce3f6d634ce35e9a389a6f13b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732574"
---
# <a name="troubleshoot-common-windows-virtual-desktop-agent-issues"></a>Solucionar problemas comuns do agente de área de trabalho virtual do Windows

O agente de área de trabalho virtual do Windows pode causar problemas de conexão devido a vários fatores:
   - Um erro no agente que faz o agente parar o serviço.
   - Problemas com atualizações.
   - Problemas de instalação durante a instalação do agente, o que interrompe a conexão com o host da sessão.

Este artigo guiará você pelas soluções para esses cenários comuns e como abordar problemas de conexão.

>[!NOTE]
>Para solucionar problemas relacionados à conectividade de sessão e ao agente de área de trabalho virtual do Windows, recomendamos que você examine os logs de eventos no aplicativo **Visualizador de eventos**  >  **logs do Windows**  >  . Procure eventos que tenham uma das seguintes fontes para identificar seu problema:
>
>- WVD-Agent
>- WVD-agente-atualizador
>- RDAgentBootLoader
>- MsiInstaller

## <a name="error-the-rdagentbootloader-andor-remote-desktop-agent-loader-has-stopped-running"></a>Erro: o carregador do agente RDAgentBootLoader e/ou Área de Trabalho Remota parou de funcionar

Se você estiver vendo qualquer um dos problemas a seguir, isso significa que o carregador de inicialização, que carrega o agente, não pôde instalar o agente corretamente e o serviço Agent não está em execução:
- O **RDAgentBootLoader** foi interrompido ou não está em execução.
- Não há status para o **carregador do agente de área de trabalho remota**.

Para resolver esse problema, inicie o carregador de inicialização RDAgent:

1. Na janela Serviços, clique com o botão direito do mouse em **área de trabalho remota carregador de agente**.
2. Selecione **Iniciar**. Se essa opção estiver esmaecida para você, você não terá permissões de administrador e precisará obtê-las para iniciar o serviço.
3. Aguarde 10 segundos e clique com o botão direito do mouse em **área de trabalho remota carregador de agente**.
4. Selecione **Atualizar**.
5. Se o serviço for interrompido depois que você iniciar e atualizá-lo, você poderá ter uma falha de registro. Para obter mais informações, consulte [INVALID_REGISTRATION_TOKEN](#error-invalid_registration_token).

## <a name="error-invalid_registration_token"></a>Erro: INVALID_REGISTRATION_TOKEN

Vá para **Visualizador de eventos**  >  aplicativo de **logs do Windows**  >  . Se você vir um evento com a ID 3277, que diz **INVALID_REGISTRATION_TOKEN** na descrição, o token de registro que você tem não será reconhecido como válido.

Para resolver esse problema, crie um token de registro válido:

1. Para criar um novo token de registro, siga as etapas na seção [gerar uma nova chave de registro para a VM](#step-3-generate-a-new-registration-key-for-the-vm) .
2. Abra o Editor do Registro. 
3. Acesse **HKEY_LOCAL_MACHINE**  >  **software**  >  **Microsoft**  >  **RDInfraAgent**.
4. Selecione **IsRegistered**. 
5. Na caixa **dados do valor:** , digite **0** e selecione **OK**. 
6. Selecione **RegistrationToken**. 
7. Na caixa de entrada **dados do valor:** , Cole o token de registro da etapa 1. 

   > [!div class="mx-imgBorder"]
   > ![Captura de tela de IsRegistered 0](media/isregistered-token.png)

8. Abra um prompt de comando como administrador.
9. Digite **net stop RDAgentBootLoader**. 
10. Digite **net start RDAgentBootLoader**. 
11. Abra o Editor do Registro.
12. Acesse **HKEY_LOCAL_MACHINE**  >  **software**  >  **Microsoft**  >  **RDInfraAgent**.
13. Verifique se **IsRegistered** está definido como 1 e não há nada na coluna de dados para **RegistrationToken**. 

   > [!div class="mx-imgBorder"]
   > ![Captura de tela de IsRegistered 1](media/isregistered-registry.png)

## <a name="error-agent-cannot-connect-to-broker-with-invalid_form"></a>Erro: o agente não pode se conectar ao agente com INVALID_FORM

Vá para **Visualizador de eventos**  >  aplicativo de **logs do Windows**  >  . Se você vir um evento com a ID 3277 que diga "INVALID_FORM" na descrição, algo deu errado com a comunicação entre o agente e o agente. O agente não pode se conectar ao agente ou alcançar uma URL específica devido a determinadas configurações de firewall ou DNS.

Para resolver esse problema, verifique se você pode alcançar o BrokerURI e o BrokerURIGlobal:
1. Abra o editor do registro. 
2. Acesse **HKEY_LOCAL_MACHINE**  >  **software**  >  **Microsoft**  >  **RDInfraAgent**. 
3. Anote os valores de **BrokerURI** e **BrokerURIGlobal**.

   > [!div class="mx-imgBorder"]
   > ![Captura de tela de URI do agente e URI do agente global](media/broker-uri.png)

 
4. Abra um navegador e vá para *\<BrokerURI\> API/integridade*. 
   - Certifique-se de usar o valor da etapa 3 no **BrokerURI**. No exemplo desta seção, seria <https://rdbroker-g-us-r0.wvd.microsoft.com/api/health> .
5. Abra outra guia no navegador e vá para *\<BrokerURIGlobal\> API/integridade*. 
   - Certifique-se de usar o valor da etapa 3 no link do **BrokerURIGlobal** . No exemplo desta seção, seria <https://rdbroker.wvd.microsoft.com/api/health> .
6. Se a rede não estiver bloqueando a conexão do agente, ambas as páginas serão carregadas com êxito e mostrarão uma mensagem dizendo **"o agente de RD está íntegro"** , conforme mostrado nas capturas de tela a seguir. 

   > [!div class="mx-imgBorder"]
   > ![Captura de tela de acesso URI do agente carregado com êxito](media/broker-uri-web.png)

   > [!div class="mx-imgBorder"]
   > ![Captura de tela do acesso de URI global do Broker carregado com êxito](media/broker-global.png)
 

7. Se a rede estiver bloqueando a conexão do agente, as páginas não serão carregadas, conforme mostrado na captura de tela a seguir. 

   > [!div class="mx-imgBorder"]
   > ![Captura de tela de acesso do agente carregado sem êxito](media/unsuccessful-broker-uri.png)

   > [!div class="mx-imgBorder"]
   > ![Captura de tela de acesso global do agente carregado sem êxito](media/unsuccessful-broker-global.png)

8. Se a rede estiver bloqueando essas URLs, será necessário desbloquear as URLs necessárias. Para obter mais informações, consulte a [lista de URL necessária](safe-url-list.md).
9. Se isso não resolver o problema, verifique se você não tem nenhuma política de grupo com codificações que bloqueiem a conexão do agente com o agente. A área de trabalho virtual do Windows usa as mesmas codificações TLS 1,2 que o [Azure front door](../frontdoor/front-door-faq.MD#what-are-the-current-cipher-suites-supported-by-azure-front-door). Para obter mais informações, consulte [segurança de conexão](network-connectivity.md#connection-security).

## <a name="error-3703"></a>Erro: 3703

Vá para **Visualizador de eventos**  >  aplicativo de **logs do Windows**  >  . Se você vir um evento com a ID 3703 que diz "URL do gateway RD: não está acessível" na descrição, o agente não poderá acessar as URLs do gateway. Para se conectar com êxito ao seu host de sessão e permitir que o tráfego de rede para esses pontos de extremidade ignore as restrições, você deve desbloquear as URLs na [lista de URL necessária](safe-url-list.md). Além disso, verifique se suas configurações de firewall ou proxy não bloqueiam essas URLs. É necessário desbloquear essas URLs para usar a área de trabalho virtual do Windows.

Para resolver esse problema, verifique se as configurações de firewall e/ou DNS não estão bloqueando essas URLs:
1. [Use o Firewall do Azure para proteger implantações de área de trabalho virtual do Windows.](../firewall/protect-windows-virtual-desktop.md).
2. Defina [as configurações de DNS do firewall do Azure](../firewall/dns-settings.md).

## <a name="error-3019"></a>Erro: 3019

Vá para **Visualizador de eventos**  >  aplicativo de **logs do Windows**  >  . Se você vir um evento com a ID 3019, isso significa que o agente não pode acessar as URLs de transporte de soquete da Web. Para se conectar com êxito ao seu host de sessão e permitir que o tráfego de rede ignore essas restrições, você deve desbloquear as URLs listadas na [lista URL necessária](safe-url-list.md). Trabalhe com a equipe de rede do Azure para verificar se suas configurações de firewall, proxy e DNS não estão bloqueando essas URLs. Você também pode verificar os logs de rastreamento de rede para identificar onde o serviço de área de trabalho virtual do Windows está sendo bloqueado. Se você abrir uma solicitação de suporte para esse problema específico, certifique-se de anexar os logs de rastreamento de rede à solicitação.

## <a name="error-installationhealthcheckfailedexception"></a>Erro: InstallationHealthCheckFailedException

Vá para **Visualizador de eventos**  >  aplicativo de **logs do Windows**  >  . Se você vir um evento com a ID 3277 que diga "InstallationHealthCheckFailedException" na descrição, isso significa que o ouvinte de pilha não está funcionando porque o servidor de terminal alternau a chave do registro para o ouvinte de pilha.

Para resolver o problema:
1. Verifique se [o ouvinte de pilha está funcionando](#error-stack-listener-isnt-working-on-windows-10-2004-vm).
2. Se o ouvinte de pilha não estiver funcionando, [desinstale e reinstale manualmente o componente de pilha](#error-vms-are-stuck-in-unavailable-or-upgrading-state).

## <a name="error-endpoint_not_found"></a>Erro: ENDPOINT_NOT_FOUND

Vá para **Visualizador de eventos**  >  aplicativo de **logs do Windows**  >  . Se você vir um evento com a ID 3277 que diga "ENDPOINT_NOT_FOUND" na descrição, o que significa que o agente não pôde encontrar um ponto de extremidade para estabelecer uma conexão. Esse problema de conexão pode ocorrer por um dos seguintes motivos:

- Não há VMs no pool de hosts
- As VMs em seu pool de hosts não estão ativas
- Todas as VMs em seu pool de hosts excederam o limite máximo de sessões
- Nenhuma das VMs em seu pool de hosts tem o serviço do Agent em execução neles

Para resolver o problema:

1. Verifique se a VM está ligada e não foi removida do pool de hosts.
2. Certifique-se de que a VM não exceda o limite máximo de sessão.
3. Verifique se o [serviço Agent está em execução](#error-the-rdagentbootloader-andor-remote-desktop-agent-loader-has-stopped-running) e se o [ouvinte de pilha está funcionando](#error-stack-listener-isnt-working-on-windows-10-2004-vm).
4. Verifique se [o agente pode se conectar ao agente](#error-agent-cannot-connect-to-broker-with-invalid_form).
5. Verifique se [sua VM tem um token de registro válido](#error-invalid_registration_token).
6. Verifique se [o token de registro da VM não expirou](faq.md#how-often-should-i-turn-my-vms-on-to-prevent-registration-issues). 

## <a name="error-installmsiexception"></a>Erro: InstallMsiException

Vá para **Visualizador de eventos**  >  aplicativo de **logs do Windows**  >  . Se você vir um evento com a ID 3277, que diz **InstallMsiException** na descrição, o instalador já estará sendo executado para outro aplicativo enquanto você estiver tentando instalar o agente ou uma política está bloqueando a execução do programa de msiexec.exe.

Para resolver esse problema, desabilite a seguinte política:
   - Desligar Windows Installer  
      - Caminho da categoria: configuração do computador \ Modelos Administrativos\Componentes do Windows\windows Installer
   
>[!NOTE]
>Essa não é uma lista abrangente de políticas, apenas aquelas que estamos cientes atualmente.

Para desabilitar uma política:
1. Abra um prompt de comando como administrador.
2. Insira e execute **RSoP. msc**.
3. Na janela **conjunto de políticas resultante** que aparece, vá para o caminho da categoria.
4. Selecione a política.
5. Selecione **Desabilitado**.
6. Escolha **Aplicar**.   

   > [!div class="mx-imgBorder"]
   > ![Captura de tela da política de Windows Installer no conjunto de políticas resultante](media/gpo-policy.png)

## <a name="error-win32exception"></a>Erro: Win32exception

Vá para **Visualizador de eventos**  >  aplicativo de **logs do Windows**  >  . Se você vir um evento com a ID 3277, que diz **InstallMsiException** na descrição, uma política está bloqueando a inicialização do cmd.exe. O bloqueio desse programa impede que você execute a janela do console, que é o que você precisa usar para reiniciar o serviço sempre que o agente é atualizado.

Para resolver esse problema, desabilite a seguinte política:
   - Impedir o acesso ao prompt de comando   
      - Caminho da categoria: usuário \ Configuração de Templates\System
    
>[!NOTE]
>Essa não é uma lista abrangente de políticas, apenas aquelas que estamos cientes atualmente.

Para desabilitar uma política:
1. Abra um prompt de comando como administrador.
2. Insira e execute **RSoP. msc**.
3. Na janela **conjunto de políticas resultante** que aparece, vá para o caminho da categoria.
4. Selecione a política.
5. Selecione **Desabilitado**.
6. Escolha **Aplicar**.   

## <a name="error-stack-listener-isnt-working-on-windows-10-2004-vm"></a>Erro: o ouvinte de pilha não está funcionando na VM do Windows 10 2004

Execute **Qwinsta** no prompt de comando e anote o número de versão que aparece ao lado de **RDP-SxS**. Se você não estiver vendo os componentes **RDP-TCP** e **RDP-SxS** , digamos que **escutem** ao lado deles ou que não estejam aparecendo depois de executar o **Qwinsta**, isso significa que há um problema de pilha. As atualizações de pilha são instaladas junto com as atualizações de agente e, quando essa instalação vai errado, o ouvinte de área de trabalho virtual do Windows não funcionará.

Para resolver o problema:
1. Abra o Editor do Registro.
2. Vá para **HKEY_LOCAL_MACHINE**  >  controle CurrentControlSet do **sistema**  >    >    >  **Terminal Server**  >  **WinStations**.
3. Em **WinStations** , você pode ver várias pastas para versões de pilha diferentes, selecionar a pasta que corresponde às informações de versão que você viu ao executar o **Qwinsta** no prompt de comando.
4. Localize **fReverseConnectMode** e verifique se seu valor de dados é **1**. Verifique também se **fEnableWinStation** está definido como **1**.

   > [!div class="mx-imgBorder"]
   > ![Captura de tela de fReverseConnectMode](media/fenable-2.png)

5. Se **fReverseConnectMode** não estiver definido como **1**, selecione **fReverseConnectMode** e insira **1** em seu campo de valor. 
6. Se **fEnableWinStation** não estiver definido como **1**, selecione **fEnableWinStation** e insira **1** em seu campo de valor.
7. Reinicie a VM. 

>[!NOTE]
>Para alterar o modo **fReverseConnectMode** ou **FEnableWinStation** para várias VMs de cada vez, você pode executar uma das duas ações a seguir:
>
>- Exporte a chave do registro do computador que você já está trabalhando e importe-a para todos os outros computadores que precisam dessa alteração.
>- Crie um objeto de política geral (GPO) que define o valor da chave do registro para os computadores que precisam da alteração.

7. Vá para **HKEY_LOCAL_MACHINE**  >  controle CurrentControlSet do **sistema**  >    >    >  **Terminal Server**  >  **ClusterSettings**.
8. Em **ClusterSettings**, localize **SessionDirectoryListener** e verifique se seu valor de dados é **RDP-SXS..**..
9. Se **SessionDirectoryListener** não estiver definido como **RDP-SXS..**., você precisará seguir as etapas na seção [desinstalar o agente e o carregador de inicialização](#step-1-uninstall-all-agent-boot-loader-and-stack-component-programs) para primeiro desinstalar o agente, o carregador de inicialização e os componentes de pilha e, em seguida, [reinstalar o agente e o carregador de inicialização](#step-4-reinstall-the-agent-and-boot-loader). Isso reinstalará a pilha lado a lado.

## <a name="error-heartbeat-issue-where-users-keep-getting-disconnected-from-session-hosts"></a>Erro: problema de pulsação em que os usuários continuam sendo desconectados dos hosts de sessão

Se o servidor não estiver selecionando uma pulsação do serviço de área de trabalho virtual do Windows, você precisará alterar o limite de pulsação. Siga as instruções nesta seção se um ou mais dos seguintes cenários se aplicarem a você:

- Você está recebendo um erro **CheckSessionHostDomainIsReachableAsync**
- Você está recebendo um erro **ConnectionBrokenMissedHeartbeatThresholdExceeded**
- Você está recebendo um erro **ConnectionEstablished: UnexpectedNetworkDisconnect**
- Os clientes do usuário continuam sendo desconectados
- Os usuários continuam sendo desconectados de seus hosts de sessão

Para alterar o limite de pulsação:
1. Abra o prompt de comando como administrador.
2. Insira o comando **Qwinsta** e execute-o.
3. Deve haver dois componentes de pilha exibidos: **RDP-TCP** e **RDP-SxS**. 
   - Dependendo da versão do sistema operacional que você está usando, o **RDP-SxS** pode ser seguido pelo número da compilação. Se for, certifique-se de anotar esse número para mais tarde.
4. Abra o Editor do Registro.
5. Vá para **HKEY_LOCAL_MACHINE**  >  controle CurrentControlSet do **sistema**  >    >    >  **Terminal Server**  >  **WinStations**.
6. Em **WinStations**, você pode ver várias pastas para diferentes versões de pilha. Selecione a pasta que corresponde ao número de versão da etapa 3.
7. Crie um novo registro DWORD clicando com o botão direito do mouse no editor do registro e selecionando **novo**  >  **valor DWORD (32 bits)**. Ao criar o DWORD, insira os seguintes valores:
   - HeartbeatInterval: 10000
   - HeartbeatWarnCount: 30 
   - HeartbeatDropCount: 60 
8. Reinicie a VM.

>[!NOTE]
>Se a alteração do limite de pulsação não resolver o problema, você poderá ter um problema de rede subjacente que precisará entrar em contato com a equipe de rede do Azure.

## <a name="error-downloadmsiexception"></a>Erro: DownloadMsiException

Vá para **Visualizador de eventos**  >  aplicativo de **logs do Windows**  >  . Se você vir um evento com a ID 3277, que diz **DownloadMsiException** na descrição, não haverá espaço suficiente no disco para o RDAgent.

Para resolver esse problema, libere espaço em seu disco:
   - Excluindo arquivos que não estão mais no usuário
   - Aumentando a capacidade de armazenamento de sua VM

## <a name="error-agent-fails-to-update-with-missingmethodexception"></a>Erro: o agente falha ao atualizar com MissingMethodException

Vá para **Visualizador de eventos**  >  aplicativo de **logs do Windows**  >  . Se você vir um evento com a ID 3389 que diga "MissingMethodException: método não encontrado" na descrição, isso significa que o agente de área de trabalho virtual do Windows não foi atualizado com êxito e revertido para uma versão anterior. Isso pode ocorrer porque o número de versão do .NET Framework instalado atualmente em suas VMs é inferior a 4.7.2. Para resolver esse problema, você precisa atualizar o .NET para a versão 4.7.2 ou posterior seguindo as instruções de instalação na [documentação do .NET Framework](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2).


## <a name="error-vms-are-stuck-in-unavailable-or-upgrading-state"></a>Erro: as VMs estão presas no estado indisponível ou atualizando

Abra uma janela do PowerShell como administrador e execute o seguinte cmdlet:

```powershell
Get-AzWvdSessionHost -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> | Select-Object *
```

Se o status listado para host de sessão ou hosts em seu pool de hosts sempre diz "não disponível" ou "Atualizando", o agente ou pilha não foi instalado com êxito.

Para resolver esse problema, reinstale a pilha lado a lado:
1. Abra um prompt de comando como administrador.
2. Digite **net stop RDAgentBootLoader**. 
3. Acesse **Painel de Controle** > **Programas** > **Programas e Recursos**.
4. Desinstale a versão mais recente da **pilha de rede serviços de área de trabalho remota SxS** ou a versão listada em **HKEY_LOCAL_MACHINE**  >  controle de **sistema**  >  **CurrentControlSet**  >    >  **Terminal Server**  >  **WinStations** em **ReverseConnectListener**.
5. Abra uma janela de console como administrador e vá para **arquivos de programas**  >  **Microsoft RDInfra**.
6. Selecione o componente **SxSStack** ou execute o comando **msiexec/i SxSStack- <version> . MSI** para instalar o MSI.
8. Reinicie a VM.
9. Volte para o prompt de comando e execute o comando **Qwinsta** .
10. Verifique se o componente de pilha instalado na etapa 6 diz **escutar** ao lado dele.
   - Nesse caso, digite **net start RDAgentBootLoader** no prompt de comando e reinicie a VM.
   - Caso contrário, será necessário [registrar novamente sua VM e reinstalar o componente do agente](#your-issue-isnt-listed-here-or-wasnt-resolved) .

## <a name="error-connection-not-found-rdagent-does-not-have-an-active-connection-to-the-broker"></a>Erro: conexão não encontrada: RDAgent não tem uma conexão ativa com o agente

Suas VMs podem estar em seu limite de conexão, portanto, a VM não pode aceitar novas conexões.

Para resolver o problema:
   - Diminua o limite máximo de sessão. Isso garante que os recursos sejam distribuídos de forma mais uniforme entre os hosts de sessão e impedirão o esgotamento de recursos.
   - Aumente a capacidade de recursos das VMs.

## <a name="error-operating-a-pro-vm-or-other-unsupported-os"></a>Erro: operando uma VM pro ou outro sistema operacional sem suporte

A pilha lado a lado só tem suporte em SKUs do Windows Enterprise ou Windows Server, o que significa que sistemas operacionais como a VM pro não são. Se você não tiver um SKU corporativo ou de servidor, a pilha será instalada em sua VM, mas não será ativada, portanto, você não verá que ela aparecerá quando você executar o **Qwinsta** na linha de comando.

Para resolver esse problema, crie uma VM que seja Windows Enterprise ou Windows Server.
1. Vá para [detalhes da máquina virtual](create-host-pools-azure-marketplace.md#virtual-machine-details) e siga as etapas 1-12 para configurar uma das seguintes imagens recomendadas:
   - Windows 10 Enterprise Multi-Session, versão 1909
   - Windows 10 Enterprise Multi-Session, versão 1909 + aplicativos Microsoft 365
   - Windows Server 2019 Datacenter
   - Windows 10 Enterprise Multi-Session, versão 2004
   - Windows 10 Enterprise Multi-Session, versão 2004 + aplicativos Microsoft 365
2. Selecione **revisar e criar**.

## <a name="error-name_already_registered"></a>Erro: NAME_ALREADY_REGISTERED

O nome da VM já foi registrado e provavelmente é uma duplicata.

Para resolver o problema:
1. Siga as etapas na seção [remover o host de sessão do pool de hosts](#step-2-remove-the-session-host-from-the-host-pool) .
2. [Crie outra VM](expand-existing-host-pool.md#add-virtual-machines-with-the-azure-portal). Certifique-se de escolher um nome exclusivo para essa VM.
3. Vá para a [portal do Azure](https://portal.azure.com) e abra a página **visão geral** do pool de hosts em que sua VM estava. 
4. Abra a guia **hosts de sessão** e verifique se todos os hosts de sessão estão no pool de hosts.
5. Aguarde 5-10 minutos para que o status do host da sessão **fique disponível**.

   > [!div class="mx-imgBorder"]
   > ![Captura de tela do host de sessão disponível](media/hostpool-portal.png)

## <a name="your-issue-isnt-listed-here-or-wasnt-resolved"></a>Seu problema não está listado aqui ou não foi resolvido

Se você não encontrar o problema neste artigo ou se as instruções não ajudarem, recomendamos desinstalar, reinstalar e registrar novamente o agente de área de trabalho virtual do Windows. As instruções nesta seção mostrarão como registrar novamente sua VM no serviço de área de trabalho virtual do Windows desinstalando todos os componentes agente, carregador de inicialização e pilha, removendo o host de sessão do pool de hosts, gerando uma nova chave de registro para a VM e reinstalando o agente e o carregador de inicialização. Se um ou mais dos cenários a seguir se aplicarem a você, siga estas instruções:
- Sua VM está presa na **atualização** ou **não está disponível**
- O ouvinte de pilha não está funcionando e você está executando no Windows 10 1809, 1903 ou 1904
- Você está recebendo um erro de **EXPIRED_REGISTRATION_TOKEN**
- Você não está vendo suas VMs aparecem na lista de hosts de sessão
- Você não vê o **carregador do agente de área de trabalho remota** na janela Serviços
- Você não vê o componente **RdAgentBootLoader** no Gerenciador de tarefas
- Você está recebendo um **agente de conexão não foi possível validar o** erro de configurações em VMs de imagem personalizada
- As instruções neste artigo não resolveram o problema

### <a name="step-1-uninstall-all-agent-boot-loader-and-stack-component-programs"></a>Etapa 1: desinstalar todos os programas do agente, do carregador de inicialização e do componente de pilha

Antes de reinstalar o agente, o carregador de inicialização e a pilha, você deve desinstalar todos os programas de componentes existentes da VM. Para desinstalar todos os programas de agente, carregador de inicialização e componente de pilha:
1. Entre em sua VM como administrador. 
2. Acesse **Painel de Controle** > **Programas** > **Programas e Recursos**.
3. Remova os seguintes programas:
   - Carregador de inicialização do agente Área de Trabalho Remota
   - Serviços de Área de Trabalho Remota Agente de infraestrutura
   - Agente de infraestrutura do Serviços de Área de Trabalho Remota Geneva
   - Pilha de rede Serviços de Área de Trabalho Remota SxS
   
>[!NOTE]
>Você pode ver várias instâncias desses programas. Certifique-se de remover todos eles.

   > [!div class="mx-imgBorder"]
   > ![Captura de tela da desinstalação de programas](media/uninstall-program.png)

### <a name="step-2-remove-the-session-host-from-the-host-pool"></a>Etapa 2: remover o host de sessão do pool de hosts

Quando você remove o host da sessão do pool de hosts, o host da sessão não é mais registrado nesse pool de hosts. Isso funciona como uma redefinição para o registro de host da sessão. Para remover o host de sessão do pool de hosts:
1. Vá para a página **visão geral** do pool de hosts em que sua VM está, na [portal do Azure](https://portal.azure.com). 
2. Vá para a guia **hosts de sessão** para ver a lista de todos os hosts de sessão nesse pool de hosts.
3. Examine a lista de hosts de sessão e selecione a VM que você deseja remover.
4. Selecione **Remover**.  

   > [!div class="mx-imgBorder"]
   > ![Captura de tela da remoção da VM do pool de hosts](media/remove-sh.png)

### <a name="step-3-generate-a-new-registration-key-for-the-vm"></a>Etapa 3: gerar uma nova chave de registro para a VM

Você deve gerar uma nova chave de registro que é usada para registrar novamente sua VM no pool de hosts e no serviço. Para gerar uma nova chave de registro para a VM:
1. Abra o [portal do Azure](https://portal.azure.com) e vá para a página **visão geral** do pool de hosts da VM que você deseja editar.
2. Selecione a **chave de registro**.

   > [!div class="mx-imgBorder"]
   > ![Captura de tela da chave de registro no portal](media/reg-key.png)

3. Abra a guia **chave de registro** e selecione **gerar nova chave**.
4. Insira a data de expiração e selecione **OK**.  

>[!NOTE]
>A data de validade não pode ser inferior a uma hora e não mais do que 27 dias a partir de sua data e hora de geração. É altamente recomendável que você defina a data de validade para o máximo de 27 dias.

5. Copie a chave gerada recentemente para a área de transferência. Você precisará dessa chave mais tarde.

### <a name="step-4-reinstall-the-agent-and-boot-loader"></a>Etapa 4: reinstalar o agente e o carregador de inicialização

Reinstalando a versão mais atualizada do agente e do carregador de inicialização, a pilha lado a lado e o agente de monitoramento Geneva também são instalados automaticamente. Para reinstalar o agente e o carregador de inicialização:
1. Entre em sua VM como administrador e use a versão correta do instalador do agente para sua implantação, dependendo da versão do Windows que sua VM está executando. Se você tiver uma VM do Windows 10, siga as instruções em [registrar máquinas virtuais](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool) para baixar o **agente de área de trabalho virtual do Windows** e o carregador de janelas do agente de área de **trabalho virtual do Windows**. Se você tiver uma VM do Windows 7, siga as etapas 13-14 em [registrar máquinas virtuais](deploy-windows-7-virtual-machine.md#configure-a-windows-7-virtual-machine) para baixar o **agente de área de trabalho virtual do windows** e o Gerenciador de agentes de área de **trabalho virtual do Windows**.

   > [!div class="mx-imgBorder"]
   > ![Captura de tela da página de download do agente e do carregador de páginas](media/download-agent.png)

2. Clique com o botão direito do mouse no agente e nos instaladores do carregador de inicialização baixados.
3. Selecione **Propriedades**.
4. Escolha **Desbloquear**.
5. Selecione **OK**.
6. Execute o instalador do agente.
7. Quando o instalador solicitar o token de registro, Cole a chave de registro da área de transferência. 

   > [!div class="mx-imgBorder"]
   > ![Captura de tela do token de registro colado](media/pasted-agent-token.png)

8. Execute o instalador do carregador de inicialização.
9. Reinicie a VM. 
10. Vá para a [portal do Azure](https://portal.azure.com) e abra a página **visão geral** do pool de hosts ao qual sua VM pertence.
11. Vá para a guia **hosts de sessão** para ver a lista de todos os hosts de sessão nesse pool de hosts.
12. Agora você deve ver o host de sessão registrado no pool de hosts com o status **disponível**. 

   > [!div class="mx-imgBorder"]
   > ![Captura de tela do host de sessão disponível](media/hostpool-portal.png)

## <a name="next-steps"></a>Próximas etapas

Se o problema continuar, crie um caso de suporte e inclua informações detalhadas sobre o problema que você está tendo e as ações que você executou para tentar resolvê-lo. A lista a seguir inclui outros recursos que você pode usar para solucionar problemas em sua implantação de área de trabalho virtual do Windows.

- Confira uma visão geral da solução de problemas da Área de Trabalho Virtual do Windows e das faixas de escalonamento em [Visão geral da solução de problemas, comentários e suporte](troubleshoot-set-up-overview.md).
- Para solucionar problemas durante a criação de um pool de hosts em um ambiente de área de trabalho virtual do Windows, consulte [ambiente e criação de pool de hosts](troubleshoot-set-up-issues.md).
- Confira como solucionar problemas ao configurar uma VM (máquina virtual) na Área de Trabalho Virtual do Windows em [Configuração da máquina virtual do host da sessão](troubleshoot-vm-configuration.md).
- Para solucionar problemas com conexões de cliente de área de trabalho virtual do Windows, consulte [conexões do serviço área de trabalho virtual do Windows](troubleshoot-service-connection.md).
- Para solucionar problemas com clientes Área de Trabalho Remota, consulte [solucionar problemas do cliente área de trabalho remota](troubleshoot-client.md).
- Veja como solucionar problemas ao usar o PowerShell com a Área de Trabalho Virtual do Windows em [PowerShell da Área de Trabalho Virtual do Windows](troubleshoot-powershell.md).
- Para saber mais sobre o serviço, consulte [ambiente de área de trabalho virtual do Windows](environment-setup.md).
- Acompanhe um tutorial de solução de problemas em [Tutorial: Solucionar problemas de implantações de modelos do Resource Manager](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Para saber sobre as ações de auditoria, consulte [Auditar operações com o Gerenciador de Recursos](../azure-resource-manager/management/view-activity-logs.md).
- Para saber sobre as ações para determinar os erros durante a implantação, consulte [Exibir operações de implantação](../azure-resource-manager/templates/deployment-history.md).
