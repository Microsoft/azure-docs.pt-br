---
title: Gerenciar o servidor de configuração para servidores físicos no Azure Site Recovery
description: Este artigo descreve como gerenciar o servidor de configuração do Azure Site Recovery para recuperação de desastres do servidor físico no Azure.
services: site-recovery
author: mayurigupta13
ms.service: site-recovery
ms.topic: article
ms.date: 02/28/2019
ms.author: mayg
ms.openlocfilehash: ff612b7c052ead5658ea4bbfafd7aace51ba3c02
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017433"
---
# <a name="manage-the-configuration-server-for-physical-server-disaster-recovery"></a>Gerenciar servidor de configuração para recuperação de desastres do servidor físico

Você configura um servidor de configuração local quando você usa o serviço do [Azure Site Recovery](site-recovery-overview.md) para a recuperação após desastres de VMs de servidores físicos para o Azure. O servidor de configuração coordena a comunicação entre o computadores locais e o Azure e gerencia a replicação de dados. Este artigo resume as tarefas comuns para gerenciar o servidor de configuração depois de implantado.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

A tabela resume os pré-requisitos para implantação do computador do servidor de configuração local.

| **Componente** | **Requisito** |
| --- |---|
| Núcleos de CPU| 8 |
| RAM | 16 GB|
| Número de discos | Três, incluindo o disco de OS, disco de cache do servidor de processo e unidade de retenção para failback |
| Espaço livre em disco (cache do servidor de processo) | 600 GB
| Espaço livre em disco (disco de retenção) | 600 GB|
| Sistema operacional  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Localidade do sistema operacional | Inglês (EUA)|
| Versão do VMware vSphere PowerCLI | Não obrigatório|
| Funções do Windows Server | Não habilite essas funções: <br> - Active Directory Domain Services <br>- Serviços de Informações da Internet <br> - Hyper-V |
| Políticas de grupo| Não habilite estas políticas de grupo: <br> - Impedir o acesso ao prompt de comando <br> - Impedir o acesso às ferramentas de edição do registro <br> - Lógica de confiança para anexos de arquivo <br> - Ativar a execução do script <br> [Saiba mais](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))|
| IIS | – Nenhum site da Web padrão já existente <br> -Habilitar  [autenticação anônima](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br> - Habilitar configuração [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10))  <br> – Nenhum aplicativo/site da Web pré-existente escutando na porta 443<br>|
| Tipo de NIC | VMXNET3 (quando implantado como uma VM VMware) |
| Tipo de endereço IP | Estático |
| Acesso à Internet | O servidor precisa de acesso a estas URLs: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - `https://management.azure.com` <br> - *.services.visualstudio.com <br> - https://dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi (não obrigatório para servidores de processo de expansão) <br> - time.nist.gov <br> - time.windows.com |
| Portas | 443 (orquestração do canal de controle)<br>9443 (transporte de dados)|

## <a name="download-the-latest-installation-file"></a>Baixe o arquivo de instalação mais recente

A versão mais recente do arquivo de instalação do servidor de configuração está disponível no portal do Site Recovery. Além disso, o agente pode ser baixado diretamente do [Centro de Download da Microsoft](https://aka.ms/unifiedsetup).

1. Faça logon no Portal do Azure e navegue até seu Cofre dos Serviços de Recuperação.
2. Navegue até **site Recovery**  >  **servidores de configuração** de infraestrutura (em para VMware & computadores físicos).
3. Clique no botão **+Servidores**.
4. Na página **Adicionar Servidor**, clique no botão Baixar para baixar a chave de Registro. Você precisa dessa chave durante a instalação do servidor de configuração para registrá-lo no serviço Azure Site Recovery.
5. Clique no link **Baixar a instalação do Microsoft Azure Site Recovery Unified** para baixar a versão mais recente do Servidor de Configuração.

   ![Página Download](./media/physical-manage-configuration-server/downloadcs.png)


## <a name="install-and-register-the-server"></a>Instalar e registrar o servidor

1. Execute o arquivo de instalação de Configuração Unificada.
2. Em **Antes de começar**, selecione **Instalar o servidor de configuração e o servidor em processo**.

    ![Antes de começar](./media/physical-manage-configuration-server/combined-wiz1.png)

3. Em **Licença de Software de Terceiros**, clique em **Aceito** para baixar e instalar o MySQL.
4. Em **Configurações da Internet**, especifique como o Provedor em execução no servidor de configuração se conecta ao Azure Site Recovery pela Internet. Verifique se você permitiu as URLs necessárias.

    - Se você desejar se conectar ao proxy que está configurado atualmente no computador, selecione **Conectar-se ao Azure Site Recovery usando um servidor proxy**.
    - Se quiser que o Provedor se conecte diretamente, selecione **Conectar diretamente o Azure Site Recovery sem um servidor proxy**.
    - Se o proxy existente exigir autenticação ou se você quiser usar um proxy personalizado para a conexão do provedor, selecione **Conectar-se com as configurações de proxy personalizadas** e especifique o endereço, a porta e as credenciais.
     ![Firewall](./media/physical-manage-configuration-server/combined-wiz4.png)
6. Em **Verificação de Pré-requisitos**, a configuração executa uma verificação para garantir que a instalação pode ser executada. Se aparecer um aviso sobre a **Verificação de sincronização de tempo global**, verifique se a hora no relógio do sistema (configurações de **Data e Hora**) é a mesma que a do fuso horário.

    ![Pré-requisitos](./media/physical-manage-configuration-server/combined-wiz5.png)
7. Em **Configuração do MySQL**, crie credenciais para fazer logon na instância do servidor MySQL instalada.

    ![MySQL](./media/physical-manage-configuration-server/combined-wiz6.png)
8. Em **Detalhes do Ambiente**, selecione se você replicará as VMs VMware. Se a resposta for positiva, a Instalação verificará se o PowerCLI 6.0 está instalado.
9. Em **Localização de Instalação**, selecione a localização em que você deseja instalar os binários e armazenar o cache. A unidade selecionado deve ter ao menos 5 GB de espaço em disco disponível, mas é recomendável uma unidade de cache com ao menos 600 GB de espaço livre.

    ![Local da instalação](./media/physical-manage-configuration-server/combined-wiz8.png)
10. Em **Seleção da Rede**, primeiro selecione a NIC usada pelo servidor de processo interno para descoberta e instalação por push do serviço de mobilidade em computadores de origem. Em seguida, selecione a NIC que o servidor de configuração usa para conectividade com o Azure. A porta 9443 é a porta padrão usada para enviar e receber o tráfego de replicação, mas você pode modificar esse número de porta para atender aos requisitos do seu ambiente. Além da porta 9443, também podemos abrir a porta 443, usada por um servidor Web para coordenar operações de replicação. Não use a porta 443 para enviar ou receber tráfego de replicação.

    ![Seleção da Rede](./media/physical-manage-configuration-server/combined-wiz9.png)


11. Em **Resumo**, examine as informações e clique em **Instalar**. Após a conclusão da instalação, uma frase secreta é gerada. Você precisará dela quando habilitar a replicação, portanto copie-a e guarde-a em um local seguro.


Após a conclusão do registro, o servidor é exibido na folha **Configurações** > **Servidores** no cofre.


## <a name="install-from-the-command-line"></a>Instalar usando a linha de comando

Execute o arquivo de instalação da seguinte maneira:

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>Amostra de uso
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="parameters"></a>Parâmetros

|Nome do Parâmetro| Type | Descrição| Valores|
|-|-|-|-|
| /ServerMode|Obrigatório|Especifica se os servidores de configuração e de processo devem ser instalados ou somente o servidor de processo|CS<br>PS|
|/InstallLocation|Obrigatório|A pasta na qual os componentes estão instalados| Qualquer pasta no computador|
|/MySQLCredsFilePath|Obrigatório|O caminho do arquivo no qual as credenciais do servidor MySQL são armazenadas|O arquivo deve ser do formato especificado abaixo|
|/VaultCredsFilePath|Obrigatório|O caminho do arquivo de credenciais do cofre|Caminho de arquivo válido|
|/EnvType|Obrigatório|Tipo de ambiente que você deseja proteger |VMware<br>NonVMware|
|/PSIP|Obrigatório|Endereço IP da NIC a ser usada para transferência de dados de replicação| Qualquer endereço IP válido|
|/CSIP|Obrigatório|O endereço IP da NIC na qual o servidor de configuração está escutando| Qualquer endereço IP válido|
|/PassphraseFilePath|Obrigatório|O caminho completo para o local do arquivo de senha|Caminho de arquivo válido|
|/BypassProxy|Opcional|Especifica se o servidor de gerenciamento se conecta ao Azure sem um proxy|Para obter esse valor de Venu|
|/ProxySettingsFilePath|Opcional|Configurações de proxy (o proxy padrão exige autenticação ou um proxy personalizado)|O arquivo deve estar no formato especificado abaixo|
|DataTransferSecurePort|Opcional|O número da porta no PSIP a ser usada para dados de replicação| Número da porta válido (o valor padrão é 9433)|
|/SkipSpaceCheck|Opcional|Ignorar verificação de espaço do disco de cache| |
|/AcceptThirdpartyEULA|Obrigatório|Sinalizar implica na aceitação do EULA de terceiros| |
|/ShowThirdpartyEULA|Opcional|Exibe o EULA de terceiros. Se fornecido como entrada, todos os outros parâmetros serão ignorados| |



### <a name="create-file-input-for-mysqlcredsfilepath"></a>Criar entrada do arquivo para MYSQLCredsFilePath

O parâmetro MySQLCredsFilePath utiliza um arquivo como entrada. Crie o arquivo usando o formato a seguir e passá-lo como parâmetro de entrada MySQLCredsFilePath.
```ini
[MySQLCredentials]
MySQLRootPassword = "Password"
MySQLUserPassword = "Password"
```
### <a name="create-file-input-for-proxysettingsfilepath"></a>Criar entrada do arquivo para ProxySettingsFilePath
O parâmetro ProxySettingsFilePath utiliza um arquivo como entrada. Crie o arquivo usando o formato a seguir e passá-lo como parâmetro de entrada ProxySettingsFilePath.

```ini
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modify-proxy-settings"></a>Modificar as configurações de proxy

Você pode modificar as configurações de proxy para o computador do servidor de configuração da seguinte maneira:

1. Faça logon no servidor de configuração.
2. Inicie o cspsconfigtool.exe usando o atalho na sua área de trabalho.
3. Clique na guia **Registro do Cofre**.
4. Faça o download de um novo arquivo de registro do cofre no portal e forneça-o como entrada para a ferramenta.

   ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Forneça os detalhes do novo proxy e clique no botão **Registrar**.
6. Abra uma janela de comando do PowerShell do Administrador.
7. Execute o comando a seguir:

   ```powershell
   $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
   net stop obengine
   net start obengine
   ```

   > [!WARNING]
   > Se você tiver servidores de processo adicionais anexados a este servidor de configuração, você precisará [corrigir as configurações de proxy em todos os servidores de processo de expansão](vmware-azure-manage-process-server.md#modify-proxy-settings-for-an-on-premises-process-server) em sua implantação.

## <a name="reregister-a-configuration-server-with-the-same-vault"></a>Registrar um servidor de configuração com o mesmo cofre
1. Faça logon no Servidor de Configuração.
2. Inicie o cspsconfigtool.exe usando o atalho na sua área de trabalho.
3. Clique na guia **Registro do Cofre**.
4. Faça o download de um novo arquivo de registro no portal e forneça-o como entrada para a ferramenta.
      ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Forneça os detalhes do servidor Proxy e clique no **registrar** botão.  
6. Abra uma janela de comando do PowerShell do Administrador.
7. Executar o seguinte comando

    ```powershell
    $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
    net stop obengine
    net start obengine
    ```

   > [!WARNING]
   > Se você tiver vários servidores de processo, você precisará [registrá-los novamente](vmware-azure-manage-process-server.md#reregister-a-process-server).

## <a name="register-a-configuration-server-with-a-different-vault"></a>Registrar um servidor de configuração com um cofre diferente

> [!WARNING]
> A etapa a seguir desassocia o servidor de configuração do cofre atual e a replicação de todas as máquinas virtuais protegidas no servidor de configuração é interrompida.

1. Faça logon no servidor de configuração
2. em um prompt de comando de administrador, execute o comando:

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. Inicie o cspsconfigtool.exe usando o atalho na sua área de trabalho.
4. Clique na guia **Registro do Cofre**.
5. Faça o download de um novo arquivo de registro no portal e forneça-o como entrada para a ferramenta.
6. Forneça os detalhes do servidor Proxy e clique no **registrar** botão.  
7. Abra uma janela de comando do PowerShell do Administrador.
8. Executar o seguinte comando
    ```powershell
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrade-a-configuration-server"></a>Atualizar um servidor de configuração

Você executa pacotes cumulativos de atualização para atualizar o servidor de configuração. As atualizações podem ser aplicadas até versões N-4. Por exemplo:

- Se estiver executando 9.7, 9.8, 9.9 ou 9.10, você poderá atualizar diretamente para 9.11.
- Se você estiver executando 9.6 ou anterior e você quiser atualizar para 9.11, você deverá primeiramente atualizar para a versão 9.7. antes de 9.11.

Os links para pacotes cumulativos de atualização para todas as versões do servidor de configuração estão disponíveis no [página de atualizações wiki](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Atualize o servidor da seguinte maneira:

1. Baixe o arquivo instalador da atualização no servidor de configuração.
2. Clique duas vezes para executar o instalador.
3. O instalador detecta a versão atual em execução no computador.
4. Clique em **OK** para confirmar e executar a atualização. 


## <a name="delete-or-unregister-a-configuration-server"></a>Excluir ou cancelar o registro de um Servidor de Configuração

> [!WARNING]
> Verifique o seguinte antes de iniciar, encerrar o servidor de configuração.
> 1. [Desabilite a proteção](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) para todas as máquinas virtuais neste servidor de configuração.
> 2. [Desassocie](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) e [exclua](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) todas as políticas de replicação do servidor de configuração.
> 3. [Exclua](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) todos os servidores vCenters/hosts vSphere associados ao servidor de configuração.


### <a name="delete-the-configuration-server-from-azure-portal"></a>Excluir o servidor de configuração do portal do Azure
1. Em portal do Azure, navegue até **site Recovery**  >  **servidores de configuração** de infraestrutura no menu do cofre.
2. Clique no servidor de configuração que você deseja encerrar.
3. Na página de detalhes de configuração do servidor, clique no botão **Excluir**.
4. Clique em **Sim** para confirmar a exclusão do servidor.

### <a name="uninstall-the-configuration-server-and-its-dependencies"></a>Desinstalar o servidor de configuração e suas dependências
> [!TIP]
>   Se você planejar reutilizar o servidor de configuração com o Azure Site Recovery novamente, poderá ignorar a etapa 4 diretamente

1. Faça logon no Servidor de Configuração como um Administrador.
2. Abrir o Painel de Controle > Programa > Desinstalar Programas
3. Desinstale os programas na sequência a seguir:
   * Agente de Serviços dos Serviços de Recuperação do Microsoft Azure
   * Servidor de destino do Microsoft Azure Site Recovery mobilidade mestra do serviço
   * Provedor do Microsoft Azure Site Recovery
   * Servidor de processo/servidor de configuração do Microsoft Azure Site Recovery
   * Dependências de servidor de configuração do Microsoft Azure Site Recovery
   * MySQL Server 5.5
4. Execute o seguinte comando de um prompt de comando de administrador.
   ```
   reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
   ```

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>Excluir ou cancelar o servidor de configuração (PowerShell)

1. [Instalar](/powershell/azure/install-Az-ps) o Módulo Azure PowerShell
2. Faça logon na sua conta do Azure usando o comando
    
    `Connect-AzAccount`
3. Selecione a assinatura sob a qual o cofre está presente

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  Agora configure o contexto do cofre
    
    ```powershell
    $Vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $Vault
    ```
4. Obtenha a seleção do servidor de configuração

    `$Fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Excluir o Servidor de Configuração

    `Remove-AzSiteRecoveryFabric -Fabric $Fabric [-Force]`

> [!NOTE]
> A opção **-Force** na Remove-AzSiteRecoveryFabric pode ser usada para forçar a remoção/exclusão do servidor de configuração.

## <a name="renew-tlsssl-certificates"></a>Renovar certificados TLS/SSL
O servidor de configuração tem uma servidor de Web embutido, que coordena as atividades dos serviços móveis, servidores de processo e servidores de destino conectados a ele. O servidor Web usa um certificado TLS/SSL para autenticar clientes. O certificado expira depois de três anos e pode ser renovado a qualquer momento.

### <a name="check-expiry"></a>Verificar expiração

Para implantações de servidor de configuração antes de maio de 2016, a expiração do certificado foi definida para um ano. Se você tiver um certificado prestes a expirar, ocorrerá o seguinte:

- Quando a data de expiração é de dois meses ou menos, o serviço começa a enviar notificações no portal e por e-mail (se você tiver assinado as notificações do Azure Site Recovery).
- Uma faixa de notificação será exibida na página de recursos do cofre. Clique na faixa para obter mais detalhes.
- Se você vir um botão **Atualizar agora**, isso indica que há alguns componentes em seu ambiente que ainda não foram atualizados para a versão 9.4.xxxx.x ou superior. Atualize os componentes antes de renovar o certificado. Não é possível renovar versões mais antigas.

### <a name="renew-the-certificate"></a>Renovar o certificado

1. No cofre, abra **site Recovery**  >  **servidor de configuração** de infraestrutura e clique no servidor de configuração necessário.
2. A data de expiração aparece em **integridade do servidor de configuração**
3. Clique em **Renovar certificados**. 




## <a name="common-issues"></a>Problemas comuns
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>Próximas etapas

Analisar os tutoriais para configurar a recuperação após desastres de [servidores físicos](./physical-azure-disaster-recovery.md) no Azure.
