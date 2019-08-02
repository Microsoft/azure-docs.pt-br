---
title: Gerenciar o servidor de configuração para recuperação de desastre do VMware e do servidor físico com o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como gerenciar um servidor de configuração existente para recuperação de desastre de VMs VMware e servidores físicos no Azure com o Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: ramamill
ms.openlocfilehash: 66022b5e4885c515bd6117f9a44b8108ff84ae5c
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68250110"
---
# <a name="manage-the-configuration-server-for-vmware-vm-disaster-recovery"></a>Gerenciar o servidor de configuração para recuperação de desastres de VM do VMware

Você configura um servidor de configuração local quando você usa o [Azure Site Recovery](site-recovery-overview.md) para recuperação de desastre de VMs VMware e servidores físicos para o Azure. O servidor de configuração coordena a comunicação entre o ambiente de VMware local e o Azure e gerencia a replicação de dados. Este artigo resume as tarefas comuns para gerenciar o servidor de configuração depois de implantado.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="access-configuration-server"></a>Acessar servidor de configuração

Você pode acessar o servidor de configuração da seguinte maneira:

* Entre na VM na qual ela está implantada e inicie o **Azure Site Recovery Configuration Manager** no atalho da área de trabalho.
* Como alternativa, você pode acessar o servidor de configuração remotamente em https://*ConfigurationServerName*/:44315/. Entre com credenciais de administrador.

## <a name="modify-vmware-server-settings"></a>Modifique as configurações do servidor VMware

1. Para associar outro servidor VMware ao servidor de configuração, após a [entrada](#access-configuration-server), selecione **Adicionar vCenter Server/servidor vSphere ESXi**.
2. Digite os detalhes e, em seguida, selecione **OK**.

## <a name="modify-credentials-for-automatic-discovery"></a>Modificar as credenciais para a descoberta automática

1. Para atualizar as credenciais usadas para conectar-se ao servidor VMware para descoberta automática de VMs do VMware, após a [entrada](#access-configuration-server), escolha a conta e clique em **Editar**.
2. Digite as novas credenciais e selecione **OK**.

    ![Modificar VMware](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)

Também é possível modificar as credenciais por meio de CSPSConfigtool.exe.

1. Fazer logon no servidor de configuração e iniciar o CSPSCconfigtool.exe
2. Escolha a conta que você deseja modificar e clique em **Editar**.
3. Insira as credenciais modificadas e clique em **Ok**

## <a name="modify-credentials-for-mobility-service-installation"></a>Modificar credenciais para instalação do Serviço de Mobilidade

Modifique as credenciais usadas para instalar automaticamente o Serviço de Mobilidade nas VMs do VMware que você habilitar para replicação.

1. Depois de [entrar](#access-configuration-server), selecione **Gerenciar credenciais de máquina virtual**
2. Escolha a conta que você deseja modificar e clique em **Editar**
3. Digite as novas credenciais e selecione **OK**.

    ![Modificar as credenciais de Serviço de Mobilidade](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

Também é possível modificar credenciais por meio de CSPSConfigtool.exe.

1. Fazer logon no servidor de configuração e iniciar o CSPSCconfigtool.exe
2. Escolha a conta que você deseja modificar e clique em **Editar**
3. Insira as novas credenciais e clique em **Ok**.

## <a name="add-credentials-for-mobility-service-installation"></a>Adicionar credenciais para a instalação do serviço de Mobilidade

Se você perdeu a adição de credenciais durante a implantação de OVF do servidor de configuração,

1. Depois de [entrar](#access-configuration-server), selecione **Gerenciar credenciais de máquina virtual**.
2. Clique em **Adicionar credenciais de máquina virtual**.
    ![add-mobility-credentials](media/vmware-azure-manage-configuration-server/add-mobility-credentials.png)
3. Insira as novas credenciais e clique em **Adicionar**.

Também é possível adicionar as credenciais por meio de CSPSConfigtool.exe.

1. Fazer logon no servidor de configuração e iniciar o CSPSCconfigtool.exe
2. Clique em **Adicionar**, insira as novas credenciais e clique em **Ok**.

## <a name="modify-proxy-settings"></a>Modificar as configurações de proxy

Modificar as configurações de proxy usadas pela máquina do servidor de configuração para acesso à internet para o Azure. Se você tiver uma máquina de servidor de processo, além do servidor de processo padrão em execução na máquina do servidor de configuração, modifique as definições em ambos os computadores.

1. Depois de [entrar](#access-configuration-server) no servidor de configuração, selecione **Gerenciar conectividade**.
2. Atualize os valores de proxy. Selecione **Salvar** para atualizar as configurações.

## <a name="add-a-network-adapter"></a>Adicione um adaptador de rede

O modelo Open Virtualization Format (OVF) implanta a VM do servidor de configuração com um único adaptador de rede.

- Você pode [adicionar outro adaptador à VM](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter), mas precisa adicioná-lo antes de registrar o servidor de configuração no cofre.
- Para adicionar um adaptador depois de registrar o servidor de configuração no cofre, adicione o adaptador nas propriedades da VM. Então é necessário [registrar novamente](#reregister-a-configuration-server-in-the-same-vault) o servidor no cofre.


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Registrar um servidor de configuração no mesmo cofre

Você pode registrar novamente o servidor de configuração no mesmo cofre se necessário. Se você tiver uma máquina de servidor de processo adicional, além do servidor de processo padrão em execução na máquina do servidor de configuração, registre novamente em ambos os computadores.


1. No cofre, abra **gerenciar** > **infra-estrutura do Azure Site Recovery** > **Servidores de Configuração**.
2. Em **Servidores**, selecione **Baixar a chave de registro** para baixar o arquivo de credenciais do cofre.
3. Conecte-se ao computador do servidor de configuração.
4. Em **%ProgramData%\ASR\home\svsystems\bin**, abra **cspsconfigtool.exe**.
5. Sobre o **registro do cofre** guia, selecione **procurar**e localize o arquivo de credenciais de cofre que você baixou.
6. Se necessário, forneça os detalhes do servidor proxy. Em seguida, selecione **Registrar**.
7. Abra uma janela de comando do PowerShell de administrador e execute o comando a seguir:
   ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
   ```

    >[!NOTE]
    >Para efetuar **pull dos certificados mais recentes** do servidor de configuração para o servidor de processo de expansão, execute o comando *\<"Installation Drive\Microsoft Azure site Recovery\agent\cdpcli.exe >"--registermt*

8. Finalmente, reinicie o obengine executando o seguinte comando.
   ```
        net stop obengine
        net start obengine
   ```


## <a name="register-a-configuration-server-with-a-different-vault"></a>Registrar um servidor de configuração com um cofre diferente

> [!WARNING]
> A etapa a seguir desassocia o servidor de configuração do cofre atual e a replicação de todas as máquinas virtuais protegidas no servidor de configuração é interrompida.

1. Faça logon no servidor de configuração.
2. Abra uma janela de comando do PowerShell de administrador e execute o comando a seguir:

    ```
    reg delete "HKLM\Software\Microsoft\Azure Site Recovery\Registration"
    net stop dra
    ```
3. Inicie o portal do navegador do dispositivo do servidor de configuração usando o atalho em sua área de trabalho.
4. Siga as etapas de registro semelhantes ao [registro](vmware-azure-tutorial.md#register-the-configuration-server) de um novo servidor de configuração.

## <a name="upgrade-the-configuration-server"></a>Atualizar o servidor de configuração

Você executa pacotes cumulativos de atualização para atualizar o servidor de configuração. As atualizações podem ser aplicadas até versões N-4. Por exemplo:

- Se executar 9.7, 9.8, 9.9 ou 9.10, você poderá atualizar diretamente para 9.11.
- Se executar 9.6 ou anterior e quiser atualizar para 9.11, você deverá primeiramente atualizar para a versão 9.7. antes de 9.11.

Para obter orientação detalhada sobre a declaração de suporte a componentes do Azure Site Recovery, consulte [aqui](https://aka.ms/asr_support_statement).
Os links para pacotes cumulativos de atualização para todas as versões do servidor de configuração estão disponíveis [aqui](https://aka.ms/asr_update_rollups).

> [!IMPORTANT]
> Com cada nova versão de ' n' de um componente do Azure Site Recovery que é liberado, todas as versões abaixo ' n-4' é considerado sem suporte. É sempre aconselhável atualizar para as versões mais recentes disponíveis.</br>
> Para obter orientação detalhada sobre a declaração de suporte a componentes do Azure Site Recovery, consulte [aqui](https://aka.ms/asr_support_statement).

Atualize o servidor da seguinte maneira:

1. No cofre, vá para **Gerenciar**  >  **Infraestrutura de Recuperação do Site**  >  **Servidores de Configuração**.
2. Se uma atualização estiver disponível, aparecerá um link no **versão do agente** > coluna.
    ![Atualização](./media/vmware-azure-manage-configuration-server/update2.png)
3. Baixe o arquivo instalador da atualização no servidor de configuração.

    ![Atualização](./media/vmware-azure-manage-configuration-server/update1.png)

4. Clique duas vezes para executar o instalador.
5. O instalador detecta a versão atual em execução no computador. Clique em **Sim** para iniciar a atualização.
6. Quando a atualização for concluída valida a configuração do servidor.

    ![Atualização](./media/vmware-azure-manage-configuration-server/update3.png)

7. Clique em **Finish** para fechar o instalador.
8. Para atualizar o restante dos componentes do Site Recovery, veja as nossas [diretrizes de atualização](https://aka.ms/asr_vmware_upgrades).

## <a name="upgrade-configuration-serverprocess-server-from-the-command-line"></a>Atualize o servidor de configuração/servidor de processo na linha de comando

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


### <a name="parameters"></a>parâmetros

|Nome do Parâmetro| Tipo | DESCRIÇÃO| Valores|
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
MySQLRootPassword = "Password>"
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

## <a name="delete-or-unregister-a-configuration-server"></a>Excluir ou cancelar o registro de um Servidor de Configuração

1. [ Desative a proteção ](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) para todas as VMs no servidor de configuração.
2. [Desassociar](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) e [Excluir](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) todas as políticas de Replicação do Servidor de Configuração.
3. [Exclua](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) todos os servidores vCenter/hosts vSphere associados ao servidor de configuração.
4. No cofre, abra **Infraestrutura do Site Recovery** > **Servidores de Configuração**.
5. Clique no servidor de configuração que deseja remover. Em seguida, na página **Detalhes**, selecione **Excluir**.

    ![Excluir um servidor de configuração](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)


### <a name="delete-with-powershell"></a>Excluir com o PowerShell

Opcionalmente, você pode excluir o servidor de configuração usando o PowerShell.

1. [Instale](https://docs.microsoft.com/powershell/azure/install-Az-ps) o módulo Azure PowerShell.
2. Conecte à sua conta do Azure usando este comando:

    `Connect-AzAccount`
3. Selecione a assinatura do cofre.

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  Definir o contexto do cofre.

    ```
    $vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Recupere o servidor de configuração.

    `$fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Excluir o servidor de configuração.

    `Remove-AzSiteRecoveryFabric -Fabric $fabric [-Force]`

> [!NOTE]
> Você pode usar a opção **-Force** em Remove-AzSiteRecoveryFabric para a exclusão forçada do servidor de configuração.

## <a name="generate-configuration-server-passphrase"></a>Gerar a Frase secreta do servidor de configuração

1. Entre no servidor de configuração e, em seguida, abra uma janela do prompt de comando como administrador.
2. Para alterar o diretório para a pasta bin, execute o comando **cd %ProgramData%\ASR\home\svsystems\bin**
3. Para gerar o arquivo de frase secreta, execute **genpassphrase.exe -v > MobSvc.passphrase**.
4. A frase secreta será armazenada no arquivo localizado em **%ProgramData%\ASR\home\svsystems\bin\MobSvc.passphrase**.

## <a name="renew-ssl-certificates"></a>Renovar certificados SSL

O servidor de configuração tem uma servidor de Web embutido, que coordena as atividades do Serviço de Mobilidade, dos servidores de processo e do servidores de destino mestre conectados a ele. O servidor da web usa um certificado SSL para autenticar clientes. O certificado expira depois de três anos e pode ser renovado a qualquer momento.

### <a name="check-expiry"></a>Verificar expiração

Para implantações de servidor de configuração antes de maio de 2016, a expiração do certificado foi definida para um ano. Se você tiver um certificado prestes a expirar, ocorrerá o seguinte:

- Quando a data de expiração é de dois meses ou menos, o serviço começa a enviar notificações no portal e por email (se você tiver assinado as notificações do Site Recovery).
- Uma faixa de notificação será exibida na página de recursos do cofre. Para obter mais informações, selecione a faixa.
- Se você vir um botão **Atualizar agora**, isso indica que alguns componentes em seu ambiente não foram atualizados para a versão 9.4.xxxx.x ou superiores. Atualize os componentes antes de renovar o certificado. Não é possível renovar versões mais antigas.

### <a name="renew-the-certificate"></a>Renovar o certificado

1. No cofre, abra **Infraestrutura do Site Recovery** > **Servidor de Configuração**. Selecione o servidor de configuração necessário.
2. A data de expiração aparece sob **Integridade do servidor de configuração**.
3. Selecione **Renovar Certificados**.

## <a name="refresh-configuration-server"></a>Atualizar servidor de configuração

1. No portal do Azure, navegue para **Cofre de Serviços de Recuperação** > **Gerenciar** > **Infraestrutura de Recuperação do Site** > **Para máquinas VMware e físicas** > **Servidores de Configuração**
2. Clique no servidor de configuração que você deseja atualizar.
3. No blade com detalhes do servidor de configuração escolhido, clique em **Mais** >  **Refresh Server**.
4. Monitore o progresso do trabalho em **Cofre de Serviços de Recuperação** > **Monitorando** >  **trabalhos de Recuperação do Site**.

## <a name="update-windows-license"></a>Atualizar a licença do Windows

A licença fornecida com o modelo OVF é uma licença de avaliação válida por 180 dias. Para uso ininterrupto, você deve ativar o Windows com uma licença adquirida.

## <a name="failback-requirements"></a>Requisitos de failback

Durante a reprotecção e o failback, o servidor de configuração local deve estar em execução e em um estado conectado. Para um failback bem-sucedido, a máquina virtual que está sendo reprovada deve existir no banco de dados do servidor de configuração.

Assegure-se de fazer backups agendados regulares de seu servidor de configuração. Se ocorrer um desastre e o servidor de configuração for perdido, você deve primeiro restaurar o servidor de configuração a partir de uma cópia de backup e garantir que o servidor de configuração restaurado tenha o mesmo endereço IP com o qual foi registrado no vault. O failback não funcionará se um endereço IP diferente for usado para o servidor de configuração restaurado.

## <a name="next-steps"></a>Próximas etapas

Analise os tutoriais para configurar a recuperação de desastre de [VMs VMware](vmware-azure-tutorial.md) para o Azure.
