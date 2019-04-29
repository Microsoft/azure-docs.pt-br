---
title: Solucionar problemas com o servidor de configuração durante a recuperação de desastres de VMs do VMware e servidores físicos no Azure usando o Azure Site Recovery | Microsoft Docs
description: Este artigo fornece informações para a solução de problemas com a implantação do servidor de configuração para recuperação de desastres de VMs do VMware e de servidores físicos no Azure usando o Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/13/2019
ms.author: ramamill
ms.openlocfilehash: f08d7bb2087ef4f30b325b3796a13e387ccdea22
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725560"
---
# <a name="troubleshoot-configuration-server-issues"></a>Solucionar problemas do servidor de configuração

Este artigo ajuda a solucionar problemas ao implantar e gerenciar o servidor de configuração do [Azure Site Recovery](site-recovery-overview.md). O servidor de configuração funciona como um servidor de gerenciamento. Use o servidor de configuração para configurar a recuperação de desastres de VMs do VMware local e de servidores físicos no Azure usando o Site Recovery. As seções a seguir tratam das falhas mais comuns que podem ocorrer quando você adiciona um novo servidor de configuração e quando gerencia um servidor de configuração.

## <a name="registration-failures"></a>Falhas de registro

O computador de origem se registra no servidor de configuração quando você instala o agente de mobilidade. Você pode depurar qualquer falha durante esta etapa seguindo estas diretrizes:

1. Abra o arquivo C:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log. (A pasta ProgramData pode ser uma pasta oculta. Se você não vir a pasta ProgramData, no Explorador de Arquivos, na guia **Exibir**, na seção **Mostrar/ocultar**, marque a caixa **Itens ocultos**.) As falhas podem ser causadas por vários problemas.

2. Procure a cadeia de caracteres **Nenhum endereço IP válido encontrado**. Se a cadeia de caracteres for encontrada,
   1. verifique se a ID do host solicitada é a mesma que a ID do host do computador de origem.
   2. verifique se o computador de origem tem pelo menos um endereço IP atribuído à NIC física. Para que o registro do agente no servidor de configuração ocorra com êxito, o computador de origem deve ter pelo menos um endereço IP v4 válido atribuído à NIC física.
   3. Execute um dos seguintes comandos no computador de origem para obter todos os endereços IP da máquina de origem:
      - No Windows: `> ipconfig /all`
      - No Linux: `# ifconfig -a`

3. Se a cadeia de caracteres **Nenhum endereço IP válido encontrado** não for encontrada, procure a cadeia de caracteres **Reason=>NULL**. Esse erro ocorre se o computador de origem usar um host vazio para se registrar no servidor de configuração. Se a cadeia de caracteres for encontrada,
    - após solucionar os problemas, siga as diretrizes em [Registrar o computador de origem no servidor de configuração](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server) para repetir o registro manualmente.

4. Se a cadeia de caracteres **Reason=>NULL** não for encontrada, no computador de origem, abra o arquivo C:\ProgramData\ASRSetupLogs\UploadedLogs\ASRUnifiedAgentInstaller.log. (A pasta ProgramData pode ser uma pasta oculta. Se você não vir a pasta ProgramData, no Explorador de Arquivos, na guia **Exibir**, na seção **Mostrar/ocultar**, marque a caixa **Itens ocultos**.) As falhas podem ser causadas por vários problemas. 

5. Procure a cadeia de caracteres **publicar solicitação: (7) – Não foi possível se conectar ao servidor**. Se a cadeia de caracteres for encontrada,
    1. resolva os problemas de rede entre o computador de origem e o servidor de configuração. Verifique se o servidor de configuração está acessível no computador de origem usando as ferramentas de rede, como ping, traceroute ou um navegador da Web. Verifique se o computador de origem é capaz de acessar o servidor de configuração usando a porta 443.
    2. Verifique se alguma regra de firewall no computador de origem está bloqueando a conexão entre o computador de origem e o servidor de configuração. Atue em conjunto com os administradores de rede para desbloquear quaisquer problemas de conexão.
    3. Verifique se as pastas listadas em [Exclusões de pasta do Site Recovery de programas antivírus](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) foram excluídas do software antivírus.
    4. Após solucionar os problemas de rede, repita o registro seguindo as diretrizes apresentadas em [Registrar o computador de origem no servidor de configuração](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

6. Se a cadeia de caracteres **publicar solicitação: (7) – não foi possível se conectar ao servidor** não for encontrada, no mesmo arquivo de log, procure a cadeia de caracteres **solicitação: (60) – o certificado par não pode ser autenticado com os certificados da Autoridade de Certificação fornecidos**. Esse erro pode ocorrer porque o certificado do servidor de configuração expirou ou o computador de origem não oferece suporte a TLS 1.0 ou a protocolos SSL mais recentes. Tal erro também pode ocorrer se um firewall bloquear a comunicação SSL entre o computador de origem e o servidor de configuração. Se a cadeia de caracteres for encontrada, 
    1. para resolver o problema, conecte-se ao endereço IP do servidor de configuração usando um navegador da Web no computador de origem. Use a URI https:\/\/<endereço IP do servidor de configuração\>:443/. Verifique se o computador de origem é capaz de acessar o servidor de configuração usando a porta 443.
    2. Confira se alguma regra de firewall no computador de origem precisa ser adicionada ou removida para que esse computador se comunique com o servidor de configuração. Em razão da variedade de softwares de firewall que pode estar sendo usada, não é possível listar todas as configurações de firewall necessárias. Atue em conjunto com os administradores de rede para desbloquear quaisquer problemas de conexão.
    3. Verifique se as pastas listadas em [Exclusões de pasta do Site Recovery de programas antivírus](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) foram excluídas do software antivírus.  
    4. Após solucionar os problemas, repita o registro seguindo as diretrizes em [Registrar o computador de origem no servidor de configuração](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

7. No Linux, se o valor da plataforma em <INSTALLATION_DIR\>/etc/drscout.conf estiver corrompido, ocorrerá falha do registro. Para identificar esse problema, abra o arquivo /var/log/ua_install.log. Procure a cadeia de caracteres **Anular configuração pois o valor de VM_PLATFORM é nulo ou não é VmWare/Azure**. A plataforma deve ser definida como **VmWare** ou **Azure**. Se o arquivo drscout.conf estiver corrompido, é recomendável [desinstalar o agente de mobilidade](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) e, em seguida, reinstalá-lo. Se a falha na desinstalação, conclua as etapas a seguir: um. Abra o arquivo Installation_Directory/uninstall.sh e comente a chamada para a função **StopServices**.
    b. Abra o arquivo Installation_Directory/Vx/bin/uninstall.sh e comente a chamada para a função **stop_services**.
    c. Abra o arquivo Installation_Directory/Fx/uninstall.sh e comente a seção completa que está tentando interromper o serviço de Fx.
    d. [Desinstale](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) o agente de mobilidade. Após a desinstalação bem-sucedida, reinicialize o sistema e tente reinstalar o agente de mobilidade.

## <a name="installation-failure-failed-to-load-accounts"></a>Falha na instalação: falha ao carregar contas

Esse erro ocorre quando o serviço não pode ler os dados de conexão de transporte ao instalar o agente de mobilidade e registrar no servidor de configuração. Para resolver o problema, verifique se o TLS 1.0 está habilitado no computador de origem.

## <a name="vcenter-discovery-failures"></a>Falhas de descoberta do vCenter

Para resolver falhas de descoberta do vCenter, adicione o servidor vCenter às configurações de proxy da lista byPass. 

- Baixe a ferramenta PsExec [aqui](https://aka.ms/PsExec) para acessar o conteúdo de usuário do sistema.
- Abra o Internet Explorer no conteúdo de usuário do sistema a seguinte linha de comando psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"
- Adicione configurações de proxy no Internet Explorer e reinicie o serviço de tmanssvc.
- Para definir as configurações de proxy do DRA, execute    cd C:\Program Files\Microsoft Azure Site Recovery Provider
- Em seguida, execute DRCONFIGURATOR.EXE /configure /AddBypassUrls [adicione o Endereço IP/FQDN do vCenter Server fornecido durante a etapa **Configurar vCenter Server/servidor vSphere ESXi** da [implantação de Servidor de Configuração](vmware-azure-deploy-configuration-server.md#configure-settings)]

## <a name="change-the-ip-address-of-the-configuration-server"></a>Alteração do endereço IP do servidor de configuração

É altamente recomendável que você não altere o endereço IP de um servidor de configuração. Verifique se todos os endereços IP atribuídos ao servidor de configuração são endereços IP estáticos. Não use endereços IP DHCP.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: o token SAML é inválido

Para evitar esse erro, verifique se não há uma diferença superior a 15 minutos entre o horário no relógio do sistema e o horário local. Execute novamente o instalador para concluir o registro.

## <a name="failed-to-create-a-certificate"></a>Falha ao criar um certificado

Não é possível criar o certificado necessário para autenticar o Site Recovery. Tente instalar novamente após verificar que você a está executando como administrador local.

## <a name="failure-to-activate-windows-license-from-server-standard-evaluation-to-server-standard"></a>Falha ao ativar a licença do Windows da avaliação de padrão de servidor para servidor padrão

1. Como parte da implantação de servidor de configuração por meio do OVF, uma licença de avaliação é usada, o que é válido por 180 dias. É preciso ativar esta licença antes que ela expire. Caso contrário, isso pode resultar em frequente desligamento do servidor de configuração e, portanto, causar obstáculo para atividades de replicação.
2. Se não for possível ativar a licença do Windows, entre em contato com a [equipe de suporte do Windows](https://aka.ms/Windows_Support) para resolver o problema.

## <a name="register-source-machine-with-configuration-server"></a>Registre a máquina de origem com o servidor de configuração

### <a name="if-the-source-machine-runs-windows"></a>Se o computador de origem estiver executando o Windows

Execute o comando a seguir no computador de origem:

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <configuration server IP address> /PassphraseFilePath <passphrase file path>
```

Configuração | Detalhes
--- | ---
Uso | UnifiedAgentConfigurator.exe  /CSEndPoint <endereço IP do servidor de configuração\> /PassphraseFilePath <caminho do arquivo de frase secreta\>
Logs de configuração do agente | Localizado em %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Parâmetro obrigatório. Especifica o endereço de IP do servidor de configuração. Use qualquer endereço de IP válido.
/PassphraseFilePath |  Obrigatório. O local da frase secreta. Use qualquer caminho UNC ou arquivo local válido.

### <a name="if-the-source-machine-runs-linux"></a>Se o computador de origem estiver executando o Linux

Execute o comando a seguir no computador de origem:

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <configuration server IP address> -P /var/passphrase.txt
  ```

Configuração | Detalhes
--- | ---
Uso | cd /usr/local/ASR/Vx/bin<br /><br /> UnifiedAgentConfigurator.sh -i <endereço IP do servidor de configuração\> -P <caminho do arquivo de frase secreta\>
-i | Parâmetro obrigatório. Especifica o endereço de IP do servidor de configuração. Use qualquer endereço de IP válido.
-P |  Obrigatório. Caminho completo do arquivo em que a frase secreta está salva. Use qualquer pasta válida.

## <a name="unable-to-configure-the-configuration-server"></a>Não é possível configurar o servidor de configuração

Se você instalar aplicativos diferentes do servidor de configuração na máquina virtual, talvez não seja possível configurar o destino mestre. 

O servidor de configuração deve ser um servidor de finalidade única, e não há suporte para seu uso como um servidor compartilhado. 

Para obter mais informações, confira as perguntas frequentes sobre configuração em [Implantar um servidor de configuração](vmware-azure-deploy-configuration-server.md#faq). 

## <a name="remove-the-stale-entries-for-protected-items-from-the-configuration-server-database"></a>Remover as entradas obsoletas dos itens protegidos do banco de dados do servidor de configuração 

Para remover o computador protegido obsoleto no servidor de configuração, use as etapas a seguir. 
 
1. Para determinar o computador de origem e o endereço IP da entrada obsoleta: 

    1. Abra o cmdline do MYSQL no modo de administrador. 
    2. Execute os seguintes comandos. 
   
        ```
        mysql> use svsdb1;
        mysql> select id as hostid, name, ipaddress, ostype as operatingsystem, from_unixtime(lasthostupdatetime) as heartbeat from hosts where name!='InMageProfiler'\G;
        ```

        Isso retorna a lista de computadores registrados junto com seus endereços IP e a última pulsação. Encontre o host que apresenta pares de replicação obsoletos.

2. Abra um prompt de comandos com privilégios elevados e navegue até C:\ProgramData\ASR\home\svsystems\bin. 
4. Para remover os detalhes dos hosts registrados e as informações de entrada obsoletas do servidor de configuração, execute o seguinte comando usando o computador de origem e o endereço IP da entrada obsoleta. 
   
    `Syntax: Unregister-ASRComponent.pl -IPAddress <IP_ADDRESS_OF_MACHINE_TO_UNREGISTER> -Component <Source/ PS / MT>`
 
    Se você tiver uma entrada de servidor de origem de "OnPrem VM01" com um endereço ip 10.0.0.4, em seguida, use o comando a seguir.
 
    `perl Unregister-ASRComponent.pl -IPAddress 10.0.0.4 -Component Source`
 
5. Reinicie os seguintes serviços no computador de origem para se registrar novamente no servidor de configuração. 
 
    - Serviço de aplicativo InMage Scout
    - Agente do InMage Scout VX – Sentinel/Outpost

## <a name="upgrade-fails-when-the-services-fail-to-stop"></a>A atualização falha quando os serviços não conseguem parar

A atualização do servidor de configuração falha quando determinados serviços não param. 

Para identificar o problema, navegue até C:\ProgramData\ASRSetupLogs\CX_TP_InstallLogFile no servidor de configuração. Se você encontrar os seguintes erros, siga as etapas abaixo para resolver o problema: 

    2018-06-28 14:28:12.943   Successfully copied php.ini to C:\Temp from C:\thirdparty\php5nts
    2018-06-28 14:28:12.943   svagents service status - SERVICE_RUNNING
    2018-06-28 14:28:12.944   Stopping svagents service.
    2018-06-28 14:31:32.949   Unable to stop svagents service.
    2018-06-28 14:31:32.949   Stopping svagents service.
    2018-06-28 14:34:52.960   Unable to stop svagents service.
    2018-06-28 14:34:52.960   Stopping svagents service.
    2018-06-28 14:38:12.971   Unable to stop svagents service.
    2018-06-28 14:38:12.971   Rolling back the install changes.
    2018-06-28 14:38:12.971   Upgrade has failed.

Como resolver o problema:

Interrompa manualmente os seguintes serviços:

- cxprocessserver
- Agente do InMage Scout VX – Sentinel/Outpost, 
- Agente dos Serviços de Recuperação do Microsoft Azure, 
- Serviço do Microsoft Azure Site Recovery, 
- tmansvc
  
Para atualizar o servidor de configuração, execute a [configuração unificada](service-updates-how-to.md#links-to-currently-supported-update-rollups) novamente.

## <a name="azure-active-directory-application-creation-failure"></a>Falha na criação de aplicativo do Azure Active Directory

Você não tem permissões suficientes para criar um aplicativo no Azure Active Directory (AAD) usando o modelo [Open Virtualization Application (OVA)](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template
).

Para resolver o problema, entre no portal do Azure e siga um destes procedimentos:

- Solicite a função de Desenvolvedor de Aplicativos no AAD. Para obter mais informações sobre a função de Desenvolvedor de Aplicativos, consulte [Permissões da função de Administrador no Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).
- Verifique se o sinalizador **Usuário pode criar aplicativo** está definido como *true* no AAD. Para obter mais informações, confira [Como: Usar o portal para criar um aplicativo e uma entidade de serviço do Azure AD que possa acessar recursos](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

## <a name="process-servermaster-target-are-unable-to-communicate-with-the-configuration-server"></a>O servidor de processo e o destino mestre não conseguem se comunicar com o servidor de configuração 

Os módulos servidor de processo (PS) e destino mestre (MT) não conseguem se comunicar com o servidor de configuração (CS), e seu status é mostrado como não conectado no portal do Azure.

Normalmente, isso ocorre devido a um erro na porta 443. Use as etapas a seguir para desbloquear a porta e reativar a comunicação com o CS.

**Verificar se o agente MARS está sendo invocado pelo agente de destino mestre**

Para verificar se o agente de destino mestre pode criar uma sessão TCP para o IP do servidor de configuração, procure um rastreamento semelhante ao seguinte nos logs do agente de destino mestre:

TCP \<substituir IP com IP CS aqui >: 52739 \<substituir IP com IP CS aqui >: 443 SYN_SENT 

TCP    192.168.1.40:52739     192.168.1.40:443      SYN_SENT  // Substitua o IP pelo IP do CS aqui

Se você encontrar rastreamentos semelhantes aos seguintes nos logs do agente de destino mestre, ele está relatando erros na porta 443:

    #~> (11-20-2018 20:31:51):   ERROR  2508 8408 313 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
    #~> (11-20-2018 20:31:54):   ERROR  2508 8408 314 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
 
Esse erro pode ser encontrado quando outros aplicativos também estão usando a porta 443 ou devido a uma configuração de firewall que bloqueia a porta.

Como resolver o problema:

- Verifique se a porta 443 não está bloqueada pelo firewall.
- Se a porta estiver inacessível por estar sendo usada por outro aplicativo, pare e desinstale o aplicativo.
  - Se interromper o aplicativo não for viável, configure um novo CS limpo.
- Reinicie o servidor de configuração.
- Reinicie o serviço de IIS.

### <a name="configuration-server-is-not-connected-due-to-incorrect-uuid-entries"></a>Servidor de configuração não conectado devido a entradas UUID incorretas

Esse erro pode ocorrer quando há várias entradas UUID da instância do servidor de configuração (CS) no banco de dados. O problema geralmente ocorre quando você clona a máquina virtual do servidor de configuração.

Como resolver o problema:

1. Remova a máquina virtual do CS obsoleto/antigo do vCenter. Para obter mais informações, confira [Remover servidores e desabilitar a proteção](site-recovery-manage-registration-and-protection.md).
2. Entre na VM do servidor de configuração e conecte-se ao banco de dados svsdb1 do MySQL. 
3. Execute a seguinte consulta:

    > [!IMPORTANT]
    >
    > Verifique se você está inserindo os detalhes do UUID do servidor de configuração clonado ou a entrada obsoleta do servidor de configuração que não é mais usada para proteger máquinas virtuais. Inserir um UUID incorreto resultará na perda das informações de todos os itens protegidos existentes.
   
    ```
        MySQL> use svsdb1;
        MySQL> delete from infrastructurevms where infrastructurevmid='<Stale CS VM UUID>';
        MySQL> commit; 
    ```
4. Atualize a página do portal.

## <a name="an-infinite-sign-in-loop-occurs-when-entering-your-credentials"></a>Um loop de entrada infinito ocorre ao inserir suas credenciais

Depois de inserir o nome de usuário e a senha corretos no OVF do servidor de configuração, o login do Azure continua a solicitar as credenciais corretas.

Esse problema pode ocorrer quando a hora do sistema está incorreta.

Como resolver o problema:

Defina a hora correta no computador e tente entrar novamente. 
 