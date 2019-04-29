---
title: Solucionar problemas do Agente de Backup do Azure
description: Solucionar problemas de instalação e registro do Azure Backup Agent
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: saurse
ms.openlocfilehash: 4bad788156b2068f24484d3b248f2091409752ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61216039"
---
# <a name="troubleshoot-microsoft-azure-recovery-services-mars-agent"></a>Solucionar problemas do agente do MARS (Serviços de Recuperação do Microsoft Azure)

Veja como resolver possíveis erros durante a configuração, registro, backup e restauração.

## <a name="invalid-vault-credentials-provided"></a>Credenciais do cofre fornecidas inválidas

| Detalhes do erro | Possíveis causas | Ações recomendadas |
| ---     | ---     | ---    |
| **Erro** </br> *Credenciais do cofre inválidas fornecidas. O arquivo está corrompido ou não possui as credenciais mais recentes associadas ao serviço de recuperação. (ID: 34513)* | <ul><li> As credenciais do cofre são inválidas (ou seja, elas são baixadas mais de 48 horas antes da hora de registro).<li>MARS Agent não consegue fazer o download de arquivos para o diretório Temp do Windows. <li>As credenciais do cofre estão em um local de rede. <li>TLS 1.0 está desabilitado<li> Um servidor proxy configurado está bloqueando a conexão. <br> |  <ul><li>Faça o download das novas credenciais do cofre. (**Observação**: Se vários arquivos de credenciais do cofre forem baixados anteriormente, somente o último arquivo baixado será válido dentro de 48 horas.) <li>Inicie **IE** > **Configuração** > **Opções da Internet** > **Segurança** > **Internet**. Em seguida, selecione **Nível personalizado** e role até encontrar a seção de download de arquivo. Em seguida, selecione **Habilitar**.<li>Você também precisará adicionar esses sites no IE [sites confiáveis](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins).<li>Altere as configurações para usar um servidor proxy. Em seguida, forneça os detalhes do servidor proxy. <li> Sincronize a data e a hora com as de seu computador.<li>Se você receber um erro informando que os downloads de arquivos não são permitidos, é provável que haja um grande número de arquivos no diretório C: / Windows / Temp directory.<li>Vá para C:/Windows/Temp e verifique se há mais de 60.000 ou 65.000 arquivos com a extensão .tmp. Se houver, exclua esses arquivos.<li>Certifique-se de que tenha o .NET Framework 4.6.2 instalado. <li>Se você desabilitou o TLS 1.0 devido à conformidade de PCI, consulte esta [página de solução de problemas](https://support.microsoft.com/help/4022913). <li>Se você tiver um antivírus instalado no servidor, exclua os seguintes arquivos da verificação de antivírus: <ul><li>CBengine.exe<li>CSC.exe, relacionado ao .NET Framework. Há um CSC.exe para cada versão .NET instalada no servidor. Exclua os arquivos CSC.exe vinculados a todas as versões do .NET Framework no servidor afetado. <li>Pasta de Rascunho ou local do cache. <br>*O local padrão para a pasta de rascunho ou o caminho do local do cache é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.<br><li>A pasta bin C: \ Arquivos de Programas \ Microsoft Agente dos Serviços de Recuperação do Azure \ Bin

## <a name="unable-to-download-vault-credential-file"></a>Não é possível baixar o arquivo de credenciais do cofre

| Detalhes do erro | Ações recomendadas |
| ---     | ---    |
|Falha ao baixar o arquivo de credenciais do cofre. (ID: 403) | <ul><li> Tente baixar as credenciais do cofre usando um navegador diferente ou execute as etapas a seguir: <ul><li> Inicie o Internet Explorer, pressione F12. </li><li> Vá até a guia **Rede** para limpar o cache e os cookies do IE </li> <li> Atualize a página<br>(OU)</li></ul> <li> Verifique se a assinatura está desabilitada/expirada<br>(OU)</li> <li> Verifique se alguma regra de firewall está bloqueando o download do arquivo de credenciais do cofre <br>(OU)</li> <li> Verifique se você não esgotou o limite do cofre (50 computadores por cofre)<br>(OU)</li>  <li> Verifique se o usuário obteve permissão de Backup do Azure para baixar credencial do cofre e registrar o servidor com o cofre, consulte [artigo](backup-rbac-rs-vault.md)</li></ul> | 

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>O Agente de Serviços de Recuperação do Microsoft Azure não pôde se conectar ao Backup do Microsoft Azure

| Detalhes do erro | Possíveis causas | Ações recomendadas |
| ---     | ---     | ---    |
| **Erro** <br /><ol><li>*O Agente de Serviços de Recuperação do Microsoft Azure não pôde se conectar ao Backup do Microsoft Azure. (ID: 100050) Verifique as configurações de rede e veja se é possível conectar a Internet*<li>*(407) Autenticação de Proxy Obrigatória* |Proxy bloqueando a conexão. |  <ul><li>Inicie **IE** > **Configuração** > **Opções da Internet** > **Segurança** > **Internet**. Em seguida, selecione **Nível personalizado** e role até encontrar a seção de download de arquivo. Selecione **Habilitar**.<li>Você também precisará adicionar esses sites no IE [sites confiáveis](https://docs.microsoft.com/azure/backup/backup-try-azure-backup-in-10-mins).<li>Altere as configurações para usar um servidor proxy. Em seguida, forneça os detalhes do servidor proxy. <li>Se você tiver um antivírus instalado no servidor, exclua os seguintes arquivos da verificação de antivírus. <ul><li>CBEngine.exe (em vez de dpmra.exe).<li>CSC.exe (relacionado ao .NET Framework). Há um CSC.exe para cada versão .NET instalada no servidor. Exclua os arquivos CSC.exe vinculados a todas as versões do .NET Framework no servidor afetado. <li>Pasta de Rascunho ou local do cache. <br>*O local padrão para a pasta de rascunho ou o caminho do local do cache é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.<li>A pasta bin C: \ Arquivos de Programas \ Microsoft Agente dos Serviços de Recuperação do Azure \ Bin


## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Falha ao configurar a chave de criptografia para backups seguros

| Detalhes do erro | Possíveis causas | Ações recomendadas |
| ---     | ---     | ---    |
| **Erro** <br />*Falha ao configurar a chave de criptografia para backups seguros. A ativação não foi bem-sucedida, mas a frase secreta de criptografia foi salva no seguinte arquivo* . |<li>O servidor já está registrado com outro cofre.<li>Durante a configuração, a frase secreta foi corrompida.| Cancele o registro do servidor do cofre e registre-se novamente com uma nova frase secreta.

## <a name="the-activation-did-not-complete-successfully"></a>A ativação não foi concluída com êxito

| Detalhes do erro | Possíveis causas | Ações recomendadas |
|---------|---------|---------|
|**Erro** <br /><ol>*A ativação não foi concluída com êxito. A operação atual falhou devido a um erro de serviço interno [0x1FC07]. Repita a operação após algum tempo. Se o problema persistir, contate o suporte da Microsoft*      | <li> A pasta de Rascunho está localizada em um volume que não possui espaço suficiente. <li> A pasta de Rascunho é movida incorretamente para outro local. <li> O arquivo OnlineBackup.KEK está ausente.         | <li>Atualize para a [versão mais recente](https://aka.ms/azurebackup_agent) do MARS Agent.<li>Mova a pasta temporária ou o local do cache para um volume com espaço livre igual a 5-10% do tamanho total dos dados de backup. Para mover corretamente o local do cache, consulte as etapas em [Perguntas sobre o Azure Backup Agent](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Certifique-se de que o arquivo OnlineBackup.KEK está presente. <br>*O local padrão para a pasta de rascunho ou o caminho do local do cache é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>A frase secreta de criptografia não está configurada corretamente

| Detalhes do erro | Possíveis causas | Ações recomendadas |
|---------|---------|---------|
|**Erro** <br /><ol>*Erro 34506. A senha de criptografia armazenada neste computador não está configurada corretamente*.    | <li> A pasta de Rascunho está localizada em um volume que não possui espaço suficiente. <li> A pasta de Rascunho é movida incorretamente para outro local. <li> O arquivo OnlineBackup.KEK está ausente.        | <li>Atualize para a [versão mais recente](https://aka.ms/azurebackup_agent) do MARS Agent.<li>Mova a pasta de rascunho ou o local do cache para um volume com espaço livre equivalente a 5-10% do tamanho total dos dados de backup. Para mover corretamente o local do cache, consulte as etapas em [Perguntas sobre o Azure Backup Agent](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#backup).<li> Certifique-se de que o arquivo OnlineBackup.KEK está presente. <br>*O local padrão para a pasta de rascunho ou o caminho do local do cache é C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch*.         |


## <a name="backups-dont-run-according-to-the-schedule"></a>Os backups não são executados de acordo com o agendamento
Se os backups agendados não forem acionados automaticamente, mas os backups manuais funcionarem sem problemas, tente estas ações:

- Certifique-se de agendamento de backup do Windows Server não está em conflito com o Azure agenda de backup de arquivos e pastas.
- Acesse o **Painel de Controle** > **Ferramentas Administrativas** > **Agendador de Tarefas**. Expanda **Microsoft** e selecione **Backup Online**. Clique duas vezes em **Microsoft-OnlineBackup** e acesse a guia **Gatilhos**. Certifique-se de que o status esteja definido como **Habilitado**. Se não estiver, selecione **Editar**, selecione a caixa de seleção **Habilitado** e clique em **OK**. Na guia **Geral**, vá até **Opções de segurança** e certifique-se de que a conta de usuário selecionada para execução da tarefa seja **SYSTEM** ou o **grupo de Administradores Locais** no servidor.

- Verifique se o PowerShell 3.0 ou posterior está instalado no servidor. Para verificar a versão do PowerShell, execute o seguinte comando e verifique se o número da versão *Principal* é igual ou maior do que 3.

  `$PSVersionTable.PSVersion`

- Verifique se o caminho a seguir faz parte da variável de ambiente *PSMODULEPATH*.

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- Se a política de execução do powershell para *LocalMachine* estiver definida como restrita, o cmdlet do PowerShell que aciona a tarefa de backup poderá falhar. Execute os comandos a seguir no modo elevado para verificar e definir a política de execução como *Unrestricted* ou *RemoteSigned*.

  `PS C:\WINDOWS\system32> Get-ExecutionPolicy -List`

  `PS C:\WINDOWS\system32> Set-ExecutionPolicy Unrestricted`

> [!TIP]
> Para garantir a aplicação consistente das alterações, reinicie o servidor após executar as etapas acima.


## <a name="troubleshoot-restore-issues"></a>Solucionar problemas de restauração

O Backup do Azure pode não montar com êxito o volume de recuperação, mesmo depois de vários minutos. Talvez você também receba mensagens de erro durante o processo. Para iniciar a recuperação normalmente, execute estas etapas:

1.  Cancele o processo de montagem em andamento, caso ele esteja em execução há vários minutos.

2.  Verifique se você está usando a versão mais recente do Agente de Backup do Azure. Para descobrir a versão, no painel **Ações** do console do MARS, selecione **Sobre o Agente dos Serviços de Recuperação do Microsoft Azure**. Confirme se o número de **versão** é igual ou maior do que a versão mencionada [neste artigo](https://go.microsoft.com/fwlink/?linkid=229525). Baixe a versão mais recente [aqui](https://go.microsoft.com/fwLink/?LinkID=288905).

3.  Acesse **Gerenciador de Dispositivos** > **Controladores de Armazenamento** e localize o **Iniciador do Microsoft iSCSI**. Se conseguir localizá-lo, vá diretamente para a etapa 7.

4.  Se você não conseguir localizar o serviço Iniciador do Microsoft iSCSI, tente encontrar uma entrada em **Gerenciador de Dispositivos** > **Controladores de Armazenamento** chamada **Dispositivo Desconhecido** com a ID de Hardware **ROOT\ISCSIPRT**.

5.  Clique com o botão direito em **Dispositivo Desconhecido** e selecione **Atualizar Software de Driver**.

6.  Atualize o driver ao selecionar a opção para **Pesquisar automaticamente software de driver atualizado**. A conclusão da atualização deve alterar o **Dispositivo Desconhecido** para o **Iniciador do Microsoft iSCSI** conforme mostrado abaixo.

    ![Captura de tela do Gerenciador de Dispositivos do Backup do Azure, com controladores de Armazenamento realçados](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Vá para **Gerenciador de Tarefas** > **Serviços (Local)** > **Serviço Iniciador do Microsoft iSCSI**.

    ![Captura de tela do Gerenciador de Tarefas do Backup do Azure, com Serviços (Local) realçado](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8.  Reinicie o serviço Iniciador do Microsoft iSCSI. Para fazer isso, clique com botão direito no serviço, selecione **Parar**, clique novamente com botão direito e selecione **Iniciar**.

9.  Repita a recuperação usando a [**Restauração Instantânea**](backup-instant-restore-capability.md).

Se a recuperação ainda falhar, reinicie o servidor ou o cliente. Se você não quiser reinicializar, ou a recuperação ainda falhar mesmo após a reinicialização do servidor, tente recuperar de uma máquina alternativa. Execute as etapas [deste artigo](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte
Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

## <a name="next-steps"></a>Próximas etapas
* Obtenha mais detalhes sobre [como fazer backup do Windows Server com o Azure Backup Agent](tutorial-backup-windows-server-to-azure.md).
* Se você precisar restaurar um backup, use este artigo para [restaurar os arquivos para um computador que usa o Windows](backup-azure-restore-windows-server.md).
