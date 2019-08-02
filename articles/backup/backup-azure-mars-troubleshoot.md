---
title: Solucionar problemas do agente de backup do Azure
description: Solucionar problemas de instalação e registro do agente de backup do Azure
author: saurabhsensharma
manager: sivan
ms.service: backup
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: saurse
ms.openlocfilehash: 6dc56e4eccbad0de986551e055e877d3d051b145
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465978"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Solucionar problemas do agente de Serviços de Recuperação do Microsoft Azure (MARS)

Este artigo descreve como resolver erros que podem ser exibidos durante a configuração, o registro, o backup e a restauração.

## <a name="basic-troubleshooting"></a>Solução básica de problemas

Recomendamos que você verifique o seguinte antes de iniciar a solução de problemas do agente MARS (serviços de recuperação do Azure):

- [Verifique se o agente Mars está atualizado](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409).
- [Verifique se você tem conectividade de rede entre o agente Mars e o Azure](https://aka.ms/AB-A4dp50).
- Verifique se o MARS está em execução (no console do serviço). Se você precisar, reinicie e repita a operação.
- [Certifique-se de que 5% a 10% de espaço livre no volume esteja disponível no local da pasta de rascunho](https://aka.ms/AB-AA4dwtt).
- [Verifique se outro processo ou software antivírus está interferindo com o backup do Azure](https://aka.ms/AB-AA4dwtk).
- Se o backup agendado falhar, mas o backup manual funcionar, consulte [os backups não são executados de acordo com o agendamento](https://aka.ms/ScheduledBackupFailManualWorks).
- Verifique se o seu sistema operacional tem as atualizações mais recentes.
- [Certifique-se de que unidades e arquivos sem suporte com atributos sem suporte sejam excluídos do backup](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup).
- Verifique se o relógio no sistema protegido está configurado para o fuso horário correto.
- [Verifique se .NET Framework 4.5.2 ou posterior está instalado no servidor](https://www.microsoft.com/download/details.aspx?id=30653).
- Se você estiver tentando registrar novamente o servidor em um cofre:
  - Verifique se o agente está desinstalado no servidor e se ele foi excluído do Portal.
  - Use a mesma senha que foi usada inicialmente para registrar o servidor.
- Para backups offline, verifique se Azure PowerShell 3.7.0 está instalado tanto na origem quanto no computador de cópia antes de iniciar o backup.
- Se o agente de backup estiver em execução em uma máquina virtual do Azure, consulte [Este artigo](https://aka.ms/AB-AA4dwtr).

## <a name="invalid-vault-credentials-provided"></a>Credenciais do cofre fornecidas inválidas

**Mensagem de erro**: Credenciais do cofre fornecidas inválidas. O arquivo está corrompido ou não possui as credenciais mais recentes associadas ao serviço de recuperação. (ID: 34513)

| Causa | Ações recomendadas |
| ---     | ---    |
| **As credenciais do cofre não são válidas** <br/> <br/> Os arquivos de credencial do cofre podem estar corrompidos ou podem ter expirado. (Por exemplo, eles podem ter sido baixados mais de 48 horas antes do horário de registro.)| Baixe novas credenciais do cofre dos serviços de recuperação no portal do Azure. (Consulte a etapa 6 na seção [baixar o agente Mars](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent) .) Em seguida, siga estas etapas, conforme apropriado: <ul><li> Se você já tiver instalado e registrado o MARS, abra o console do MMC do Backup do Microsoft Azure Agent e, em seguida, selecione **registrar servidor** no painel **ações** para concluir o registro com as novas credenciais. <br/> <li> Se a nova instalação falhar, Tente reinstalar com as novas credenciais.</ul> **Observação**: Se vários arquivos de credencial de cofre tiverem sido baixados, somente o arquivo mais recente será válido nas próximas 48 horas. Recomendamos que você baixe um novo arquivo de credencial de cofre.
| **O servidor proxy/firewall está bloqueando o registro** <br/>ou <br/>**Sem conectividade com a Internet** <br/><br/> Se o seu computador ou servidor proxy tiver conectividade limitada com a Internet e você não garantir o acesso às URLs necessárias, o registro falhará.| Siga estas etapas:<br/> <ul><li> Trabalhe com sua equipe de ti para garantir que o sistema tenha conectividade com a Internet.<li> Se você não tiver um servidor proxy, certifique-se de que a opção proxy não esteja selecionada quando você registrar o agente. [Verifique as configurações de proxy](#verifying-proxy-settings-for-windows).<li> Se você tiver um servidor de firewall/proxy, trabalhe com sua equipe de rede para garantir que essas URLs e endereços IP tenham acesso:<br/> <br> **URLs**<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>**Endereços IP**<br>  20.190.128.0/18 <br>  40.126.0.0/18 <br/></ul></ul>Tente se registrar novamente depois de concluir as etapas de solução de problemas anteriores.
| **O software antivírus está bloqueando o registro** | Se você tiver um software antivírus instalado no servidor, adicione as regras de exclusão necessárias à verificação de antivírus para esses arquivos e pastas: <br/><ul> <li> CBengine.exe <li> CSC.exe<li> A pasta de rascunho. Seu local padrão é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch. <li> A pasta bin em C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.

### <a name="additional-recommendations"></a>Recomendações adicionais
- Vá para C:/Windows/Temp e verifique se há mais de 60.000 ou 65.000 arquivos com a extensão .tmp. Se houver, exclua esses arquivos.
- Verifique se a data e a hora da máquina correspondem ao fuso horário local.
- Verifique se [esses sites](backup-configure-vault.md#verify-internet-access) foram adicionados aos seus sites confiáveis no Internet Explorer.

### <a name="verifying-proxy-settings-for-windows"></a>Verificando as configurações de proxy para o Windows

1. Baixe o PsExec na página do [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) .
1. Execute `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` em um prompt de comandos com privilégios elevados.

   Esse comando abrirá o Internet Explorer.
1. Vá para **ferramentas** > **Opções** > de Internet**conexões** > **configurações de LAN**.
1. Verifique as configurações de proxy para a conta do sistema.
1. Se nenhum proxy estiver configurado e os detalhes do proxy forem fornecidos, remova os detalhes.
1. Se um proxy estiver configurado e os detalhes do proxy estiverem incorretos, verifique se o **IP do proxy** e os detalhes da **porta** estão corretos.
1. Feche o Internet Explorer.

## <a name="unable-to-download-vault-credential-file"></a>Não é possível baixar o arquivo de credenciais do cofre

| Erro   | Ações recomendadas |
| ---     | ---    |
|Falha ao baixar o arquivo de credenciais do cofre. (ID: 403) | <ul><li> Tente baixar as credenciais do cofre usando um navegador diferente ou execute estas etapas: <ul><li> Inicie o Internet Explorer. Selecione F12. </li><li> Vá para a guia **rede** e limpe o cache e os cookies. </li> <li> Atualize a página.<br></li></ul> <li> Verifique se a assinatura está desabilitada/expirada.<br></li> <li> Verifique se alguma regra de firewall está bloqueando o download. <br></li> <li> Verifique se você não esgotou o limite do cofre (50 máquinas por cofre).<br></li>  <li> Verifique se o usuário tem as permissões de backup do Azure que são necessárias para baixar as credenciais do cofre e registrar um servidor com o cofre. Consulte [usar o controle de acesso baseado em função para gerenciar pontos de recuperação do backup do Azure](backup-rbac-rs-vault.md).</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>O Agente de Serviços de Recuperação do Microsoft Azure não pôde se conectar ao Backup do Microsoft Azure

| Erro  | Causa possível | Ações recomendadas |
| ---     | ---     | ---    |
| <br /><ul><li>O agente de serviço de recuperação Microsoft Azure não pôde se conectar ao Backup do Microsoft Azure. (ID: 100050) Verifique as configurações de rede e certifique-se de que você possa se conectar à Internet.<li>(407) autenticação de proxy necessária. |Um proxy está bloqueando a conexão. |  <ul><li>No Internet Explorer, vá para **ferramentas** > **Internet opções** > **segurança** > **Internet**. Selecione **nível personalizado** e role para baixo até a seção **download de arquivo** . Selecione **Habilitar**.<p>Você também pode ter que adicionar [URLs e endereços IP](backup-configure-vault.md#verify-internet-access) aos seus sites confiáveis no Internet Explorer.<li>Altere as configurações para usar um servidor proxy. Em seguida, forneça os detalhes do servidor proxy.<li> Se o seu computador tiver acesso limitado à Internet, verifique se as configurações de firewall no computador ou proxy permitem essas [URLs e endereços IP](backup-configure-vault.md#verify-internet-access). <li>Se você tiver um software antivírus instalado no servidor, exclua esses arquivos da verificação antivírus: <ul><li>CBEngine.exe (em vez de dpmra.exe).<li>CSC.exe (relacionado ao .NET Framework). Há um CSC. exe para cada versão de .NET Framework instalada no servidor. Exclua os arquivos CSC. exe para todas as versões do .NET Framework no servidor afetado. <li>A pasta de rascunho ou o local do cache. <br>O local padrão para a pasta de rascunho ou o caminho do cache é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch.<li>A pasta bin em C:\Program Files\Microsoft Azure Recovery Services Agent\Bin.


## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Falha ao configurar a chave de criptografia para backups seguros

| Erro | Causas possíveis | Ações recomendadas |
| ---     | ---     | ---    |
| <br />Falha ao definir a chave de criptografia para backups seguros. A ativação não foi concluída com êxito, mas a senha de criptografia foi salva no arquivo a seguir. |<li>O servidor já está registrado com outro cofre.<li>Durante a configuração, a frase secreta foi corrompida.| Cancele o registro do servidor do cofre e registre-o novamente com uma nova senha.

## <a name="the-activation-did-not-complete-successfully"></a>A ativação não foi concluída com êxito

| Erro  | Causas possíveis | Ações recomendadas |
|---------|---------|---------|
|<br />A ativação não foi concluída com êxito. A operação atual falhou devido a um erro de serviço interno [0x1FC07]. Aguarde um pouco e repita a operação. Se o problema persistir, contate o suporte da Microsoft.     | <li> A pasta de rascunho está localizada em um volume que não tem espaço suficiente. <li> A pasta de rascunho foi movida incorretamente. <li> O arquivo OnlineBackup.KEK está ausente.         | <li>Atualize para a [versão mais recente](https://aka.ms/azurebackup_agent) do agente Mars.<li>Mova a pasta de rascunho ou o local do cache para um volume com espaço livre entre 5% e 10% do tamanho total dos dados de backup. Para mover corretamente o local do cache, consulte as etapas em [perguntas comuns sobre como fazer backup de arquivos e pastas](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder).<li> Certifique-se de que o arquivo OnlineBackup.KEK está presente. <br>*O local padrão para a pasta de rascunho ou o caminho do cache é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>A frase secreta de criptografia não está configurada corretamente

| Erro  | Causas possíveis | Ações recomendadas |
|---------|---------|---------|
| <br />Error 34506. A senha de criptografia armazenada neste computador não está configurada corretamente.    | <li> A pasta de rascunho está localizada em um volume que não tem espaço suficiente. <li> A pasta de rascunho foi movida incorretamente. <li> O arquivo OnlineBackup.KEK está ausente.        | <li>Atualize para a [versão mais recente](https://aka.ms/azurebackup_agent) do MARS Agent.<li>Mova a pasta de rascunho ou o local do cache para um volume com espaço livre entre 5% e 10% do tamanho total dos dados de backup. Para mover corretamente o local do cache, consulte as etapas em [perguntas comuns sobre como fazer backup de arquivos e pastas](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder).<li> Certifique-se de que o arquivo OnlineBackup.KEK está presente. <br>*O local padrão para a pasta de rascunho ou o caminho do cache é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |


## <a name="backups-dont-run-according-to-schedule"></a>Os backups não são executados de acordo com o agendamento
Se os backups agendados não forem disparados automaticamente, mas os backups manuais funcionarem corretamente, tente as seguintes ações:

- Verifique se o agendamento de backup do Windows Server não entra em conflito com o agendamento de backup de pastas e arquivos do Azure.

- Verifique se o status do backup online está definido como **habilitar**. Para verificar o status, siga estas etapas:

  1. No Agendador de Tarefas, expanda **Microsoft** e selecione **backup online**.
  1. Clique duas vezes em **Microsoft-OnlineBackup** e vá para  a guia gatilhos.
  1. Verifique se o status está definido como **habilitado**. Se não estiver, selecione **Editar**, selecione **habilitado**e, em seguida, selecione **OK**.

- Verifique se a conta de usuário selecionada para executar a tarefa é o grupo de **sistema** ou **Administradores locais** no servidor. Para verificar a conta de usuário, vá para a guia **geral** e verifique as opções de **segurança** .

- Verifique se o PowerShell 3,0 ou posterior está instalado no servidor. Para verificar a versão do PowerShell, execute este comando e verifique se `Major` o número da versão é 3 ou posterior:

  `$PSVersionTable.PSVersion`

- Verifique se esse caminho faz parte da `PSMODULEPATH` variável de ambiente:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Se a política de execução do `LocalMachine` PowerShell para for definida como restrita, o cmdlet do PowerShell que dispara a tarefa de backup poderá falhar. Execute estes comandos no modo elevado para verificar e definir a política de execução como `Unrestricted` ou `RemoteSigned`:

  `PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

  `PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`

- Verifique se não há nenhum arquivo MSOnlineBackup de módulo do PowerShell ausente ou corrompido. Se houver arquivos ausentes ou corrompidos, siga estas etapas:

  1. Em qualquer computador que tenha um agente MARS que esteja funcionando corretamente, copie a pasta MSOnlineBackup de C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules.
  1. No computador problemático, Cole os arquivos copiados no mesmo local de pasta (C:\Program Files\Microsoft Azure Recovery Services Agent\bin\Modules).

     Se já houver uma pasta MSOnlineBackup no computador, Cole os arquivos nela ou substitua os arquivos existentes.


> [!TIP]
> Para garantir que as alterações sejam aplicadas de forma consistente, reinicie o servidor depois de executar as etapas anteriores.


## <a name="troubleshoot-restore-problems"></a>Solucionar problemas de restauração

O Backup do Azure pode não montar com êxito o volume de recuperação, mesmo depois de vários minutos. E você pode receber mensagens de erro durante o processo. Para começar a recuperar normalmente, execute estas etapas:

1.  Cancele o processo de montagem se ele estiver em execução por vários minutos.

2.  Verifique se você tem a versão mais recente do agente de backup. Para verificar a versão, no painel **ações** do console do Mars, selecione **sobre o agente de serviços de recuperação do Microsoft Azure**. Confirme se o número de **versão** é igual ou maior do que a versão mencionada [neste artigo](https://go.microsoft.com/fwlink/?linkid=229525). Selecione este link para [baixar a versão mais recente](https://go.microsoft.com/fwLink/?LinkID=288905).

3.  Vá para **Device Manager** > **controladores de armazenamento** e localize **Microsoft iSCSI Initiator**. Se você o localizar, vá diretamente para a etapa 7.

4.  Se você não conseguir localizar o serviço Iniciador Microsoft iSCSI, tente encontrar uma entrada em **Device Manager** > **controladores de armazenamento** chamados **dispositivo desconhecido** com a ID de hardware **ROOT\ISCSIPRT**.

5.  Clique com o botão direito do mouse em **dispositivo desconhecido** e selecione **atualizar software de driver**.

6.  Atualize o driver ao selecionar a opção para **Pesquisar automaticamente software de driver atualizado**. Esta atualização deve alterar o **dispositivo desconhecido** para **o iniciador iSCSI da Microsoft**:

    ![Captura de tela do Gerenciador de Dispositivos do Backup do Azure, com controladores de Armazenamento realçados](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Vá para serviços do **Gerenciador** > de tarefas **(local)**  > **serviço Iniciador Microsoft iSCSI**:

    ![Captura de tela do Gerenciador de Tarefas do Backup do Azure, com Serviços (Local) realçado](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8.  Reinicie o serviço Iniciador do Microsoft iSCSI. Para fazer isso, clique com o botão direito do mouse no serviço e selecione **parar**. Em seguida, clique com o botão direito do mouse novamente e selecione **Iniciar**.

9.  Repita a recuperação usando a [Restauração Instantânea](backup-instant-restore-capability.md).

Se a recuperação ainda falhar, reinicie o servidor ou o cliente. Se você não quiser reiniciar ou se a recuperação ainda falhar mesmo depois de reiniciar o servidor, tente recuperar-se [de outro computador](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).


## <a name="troubleshoot-cache-problems"></a>Solucionar problemas de cache

A operação de backup poderá falhar se a pasta de cache (também conhecida como pasta de rascunho) estiver configurada incorretamente, se houver pré-requisitos ausentes ou tiver acesso restrito.

### <a name="pre-requisites"></a>Pré-requisitos

Para que as operações do agente MARS tenham sucesso, a pasta de cache precisa aderir aos requisitos a seguir:

- [Certifique-se de que 5% a 10% de espaço livre no volume esteja disponível no local da pasta de rascunho](backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Verifique se o local da pasta de rascunho é válido e acessível](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)
- [Verifique se há suporte para os atributos de arquivo na pasta de cache](backup-azure-file-folder-backup-faq.md#are-there-any-attributes-of-the-cache-folder-that-arent-supported)
- [Verifique se o espaço de armazenamento de cópia de sombra alocado é suficiente para o processo de backup](#increase-shadow-copy-storage)
- [Verifique se não há nenhum outro processo (por exemplo, software antivírus) restringindo o acesso à pasta de cache](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>Aumentar o armazenamento de cópias de sombra
As operações de backup poderão falhar se não houver espaço de armazenamento de cópia de sombra suficiente necessário para proteger a fonte de dados. Para resolver esse problema, aumente o espaço de armazenamento de cópia de sombra no volume protegido usando vssadmin, conforme mostrado abaixo:
- Verifique o espaço de armazenamento de sombra atual do prompt de comando elevado:<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- Aumente o espaço de armazenamento de sombra usando o comando abaixo:<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>Outro processo ou software antivírus bloqueando o acesso à pasta de cache
Se você tiver um software antivírus instalado no servidor, adicione as regras de exclusão necessárias à verificação de antivírus para esses arquivos e pastas:  
- A pasta de rascunho. Seu local padrão é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch
- A pasta bin em C:\Program Files\Microsoft Azure Recovery Services Agent\Bin
- CBengine.exe
- CSC.exe

## <a name="common-issues"></a>Problemas comuns
Esta seção aborda os erros comuns que você encontrar ao usar o agente MARS.

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

Mensagem de erro | Ação recomendada |
-- | --
O agente de Serviços de Recuperação do Microsoft Azure não pôde acessar a soma de verificação de backup armazenada no local de rascunho | Para resolver esse problema, execute o seguinte e reinicie o servidor <br/> - [Verificar se há um antivírus ou outros processos bloqueando os arquivos de local de rascunho](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Verifique se o local de rascunho é válido e acessível ao agente Mars.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

Mensagem de erro | Ação recomendada |
-- | --
O agente de Serviços de Recuperação do Microsoft Azure não pôde acessar o local de rascunho para inicializar o VHD | Para resolver esse problema, execute o seguinte e reinicie o servidor <br/> - [Verificar se há um antivírus ou outros processos bloqueando os arquivos de local de rascunho](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Verifique se o local de rascunho é válido e acessível ao agente Mars.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="sallowdiskspace"></a>SalLowDiskSpace

Mensagem de erro | Ação recomendada |
-- | --
O backup falhou devido ao armazenamento insuficiente no volume em que a pasta de rascunho está localizada | Para resolver esse problema, verifique as etapas abaixo e repita a operação:<br/>- [Verifique se o agente MARS é mais recente](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> - [Verificar e resolver problemas de armazenamento que afetam o espaço transitório de backup](#pre-requisites)

### <a name="salbitmaperror"></a>SalBitmapError

Mensagem de erro | Ação recomendada |
-- | --
Não é possível encontrar as alterações em um arquivo. Isso pode ocorrer por vários motivos. Tente executar a operação novamente | Para resolver esse problema, verifique as etapas abaixo e repita a operação:<br/> - [Verifique se o agente MARS é mais recente](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> - [Verificar e resolver problemas de armazenamento que afetam o espaço transitório de backup](#pre-requisites)


## <a name="next-steps"></a>Próximas etapas
* Obtenha mais detalhes sobre [como fazer backup do Windows Server com o agente de backup do Azure](tutorial-backup-windows-server-to-azure.md).
* Se você precisar restaurar um backup, consulte [restaurar arquivos em um computador Windows](backup-azure-restore-windows-server.md).
