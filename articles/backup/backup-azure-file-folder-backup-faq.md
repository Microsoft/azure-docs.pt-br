---
title: Agente de Serviços de Recuperação do Microsoft Azure (MARS) – perguntas frequentes
description: Aborda perguntas comuns sobre como fazer backup de arquivos e pastas com o backup do Azure.
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 1edfaed99e60409774496c5ae75df8be99a8fe1f
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917351"
---
# <a name="frequently-asked-questions---microsoft-azure-recovery-services-mars-agent"></a>Agente de perguntas frequentes – Serviços de Recuperação do Microsoft Azure (MARS)

Este artigo responde a perguntas comuns sobre como fazer backup de dados com o agente de Serviços de Recuperação do Microsoft Azure (MARS) no serviço de [backup do Azure](backup-overview.md) .

## <a name="configure-backups"></a>Configurar backups

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>Onde posso baixar a versão mais recente do agente MARS?

O agente MARS mais recente usado ao fazer backup de computadores com Windows Server, System Center DPM e Backup do Microsoft Azure Server está disponível para [Download](https://aka.ms/azurebackup_agent).

### <a name="where-can-i-download-the-vault-credentials-file"></a>Onde posso baixar o arquivo de credenciais do cofre?

Na portal do Azure, navegue até **Propriedades** do cofre. Em **credenciais de backup**, marque a caixa de seleção **já usando o agente de serviços de recuperação mais recente**. Selecione **Baixar**.

![Baixar credenciais](./media/backup-azure-file-folder-backup-faq/download-credentials.png)

### <a name="how-long-are-vault-credentials-valid"></a>Por quanto tempo as credenciais do cofre são válidas?

As credenciais do cofre expiram após 10 dias. Se o arquivo de credenciais expirar, baixe o arquivo novamente no portal do Azure.

### <a name="what-characters-are-allowed-for-the-passphrase"></a>Quais caracteres são permitidos para a frase secreta?

A frase secreta deve usar caracteres do conjunto de caracteres ASCII, com [valores ASCII menores ou iguais a 127](/office/vba/language/reference/user-interface-help/character-set-0127).

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>De quais unidades posso fazer backup de arquivos e pastas?

Não é possível fazer backup dos seguintes tipos de unidades e volumes:

* Mídia removível: todas as fontes de itens de backup devem ser relatadas como fixas.
* Volumes somente leitura: o volume deve ser gravável para que o VSS (serviço de cópias de sombra de volume) funcione.
* Volumes offline: o volume deve estar online para que o VSS funcione.
* Compartilhamentos de rede: o volume deve ser local para o backup do servidor usando o backup online.
* Volumes protegidos pelo BitLocker: o volume deve ser desbloqueado antes que o backup possa ocorrer.
* Identificação do sistema de arquivos: NTFS é o único sistema de arquivos com suporte.

### <a name="what-file-and-folder-types-are-supported"></a>Quais tipos de arquivos e pastas têm suporte?

[Saiba mais](backup-support-matrix-mars-agent.md#supported-file-types-for-backup) sobre os tipos de arquivos e pastas com suporte para backup.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>Posso usar o agente MARS para fazer backup de arquivos e pastas em uma VM do Azure?  

Sim. O backup do Azure fornece backup em nível de VM para VMs do Azure usando a extensão de VM para o agente de VM do Azure. Se desejar fazer backup de arquivos e pastas no sistema operacional Windows convidado na VM, você poderá instalar o agente MARS para fazer isso.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>Posso usar o agente MARS para fazer backup de arquivos e pastas no armazenamento temporário da VM do Azure?

Sim. Instale o agente MARS e faça backup de arquivos e pastas no sistema operacional Windows convidado para o armazenamento temporário.

* Os trabalhos de backup falham quando dados de armazenamento temporários são apagados.
* Se os dados de armazenamento temporários forem excluídos, você só poderá restaurar para o armazenamento não volátil.

### <a name="how-do-i-register-a-server-to-another-region"></a>Como fazer registrar um servidor em outra região?

Os dados de backup são enviados para o datacenter do cofre no qual o servidor está registrado. A maneira mais fácil de alterar o datacenter é desinstalar e reinstalar o agente e, em seguida, registrar o computador em um novo cofre na região de que você precisa.

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>O agente MARS dá suporte à eliminação de duplicação do Windows Server 2012?

Sim. O agente MARS converte os dados com eliminação de duplicação em dados normais quando prepara a operação de backup. Em seguida, ele otimiza os dados para backup, criptografa os dados e, em seguida, envia os dados criptografados para o cofre.

### <a name="do-i-need-administrator-permissions-to-install-and-configure-the-mars-agent"></a>É necessário ter permissões de administrador para instalar e configurar o agente MARS?

Sim, a instalação do agente MARS e a configuração de backups usando o console MARS precisam que o usuário seja um administrador local no servidor protegido.

### <a name="what-is-the-impact-on-mars-agent-backups-of-transferring-the-vault-subscription-to-a-different-azure-ad-directory"></a>Qual é o impacto nos backups do agente MARS de transferir a assinatura do cofre para um diretório diferente do Azure AD?

A alteração do diretório do AD do Azure não terá impacto sobre os backups do agente MARS. 

## <a name="manage-backups"></a>Gerenciar backups

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>O que acontece se eu renomear um computador Windows configurado para backup?

Quando você renomeia um computador Windows, todos os backups atualmente configurados são interrompidos.

* Você precisa registrar o novo nome do computador com o cofre de backup.
* Quando você registra o novo nome com o cofre, a primeira operação é um backup *completo* .
* Se você precisar recuperar dados do backup para o cofre com o nome do servidor antigo, use a opção para restaurar em um local alternativo no assistente de recuperação de dados. [Saiba mais](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>Qual é o comprimento máximo do caminho de arquivo para backup?

O agente MARS depende do NTFS e usa a especificação de comprimento FilePath limitada pela API do [Windows](/windows/win32/FileIO/naming-a-file#fully-qualified-vs-relative-paths). Se os arquivos que você deseja proteger forem maiores do que o valor permitido, faça backup da pasta pai ou da unidade de disco.  

### <a name="what-characters-are-allowed-in-file-paths"></a>Quais caracteres são permitidos em caminhos de arquivo?

O agente MARS depende do NTFS e permite [caracteres com suporte](/windows/win32/FileIO/naming-a-file#naming-conventions) em nomes/caminhos de arquivo.

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>O aviso "backups do Azure não foram configurados para este servidor" aparece

Esse aviso pode aparecer mesmo que você tenha configurado uma política de backup, quando as configurações de agendamento de backup armazenadas no servidor local não são iguais às configurações armazenadas no cofre de backup.

* Quando o servidor ou as configurações forem recuperados para um estado válido conhecido, os agendamentos de backup poderão se tornar não sincronizados.
* Se você receber esse aviso, [Configure](backup-azure-manage-windows-server.md) a política de backup novamente e, em seguida, execute um backup sob demanda para ressincronizar o servidor local com o Azure.

## <a name="manage-the-backup-cache-folder"></a>Gerenciar a pasta de cache de backup

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>Qual é o requisito de tamanho mínimo para a pasta de cache?

O tamanho da pasta de cache determina a quantidade de dados que você está fazendo backup.

* Os volumes da pasta de cache devem ter espaço livre igual a pelo menos 5-10% do tamanho total dos dados de backup.
* Se o volume tiver menos de 5% de espaço livre, aumente o tamanho do volume ou mova a pasta de cache para um volume com espaço suficiente seguindo [estas etapas](#how-do-i-change-the-cache-location-for-the-mars-agent).
* Se você fizer backup do estado do sistema do Windows, precisará de mais 30-35 GB de espaço livre no volume que contém a pasta de cache.

### <a name="how-to-check-if-scratch-folder-is-valid-and-accessible"></a>Como verificar se a pasta de rascunho é válida e acessível?

1. Por padrão, a pasta de rascunho está localizada em `\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
2. Verifique se o caminho do local da pasta de rascunho corresponde aos valores das entradas da chave do registro mostradas abaixo:

    | Caminho do registro | Chave do Registro | Valor |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Novo local da pasta de cache* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Novo local da pasta de cache* |

### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>Como fazer alterar o local do cache para o agente MARS?

1. Execute este comando em um prompt de comandos com privilégios elevados para interromper o mecanismo de backup:

    ```Net stop obengine```
2. Se você tiver configurado o backup do estado do sistema, abra o gerenciamento de disco e desmonte os discos com nomes no formato `"CBSSBVol_<ID>"` .
3. Por padrão, a pasta de rascunho está localizada em `\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
4. Copie a `\Scratch` pasta inteira para uma unidade diferente que tenha espaço suficiente. Certifique-se de que o conteúdo seja copiado, não movido.
5. Atualize as entradas de registro a seguir com o caminho da pasta de rascunho recentemente movida.

    | Caminho do registro | Chave do Registro | Valor |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Novo local da pasta de rascunho* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Novo local da pasta de rascunho* |

6. Reinicie o mecanismo de backup em um prompt de comandos com privilégios elevados:

    ```command
    Net stop obengine

    Net start obengine
    ```

7. Executar um backup sob demanda. Depois que o backup for concluído com êxito usando o novo local, você poderá remover a pasta de cache original.

### <a name="where-should-the-cache-folder-be-located"></a>Onde a pasta de cache deve ser localizada?

Os seguintes locais para a pasta de cache não são recomendados:

* Compartilhamento de rede/mídia removível: a pasta de cache deve ser local para o servidor que precisa de backup usando o backup online. Não há suporte para locais de rede ou mídias removíveis, como unidades USB.
* Volumes offline: a pasta de cache deve estar online para o backup esperado usando o agente de backup do Azure

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>Há algum atributo da pasta de cache que não tem suporte?

Os seguintes atributos ou suas combinações não têm suporte para a pasta de cache:

* Criptografado
* Eliminação de duplicação
* Compressed
* Esparsos
* Ponto de nova análise

A pasta de cache e o VHD de metadados não têm os atributos necessários para o agente de backup do Azure.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>Há uma maneira de ajustar a quantidade de largura de banda usada para backup?

Sim, você pode usar a opção **alterar propriedades** no agente Mars para ajustar a largura de banda e o tempo. [Saiba mais](backup-windows-with-mars-agent.md#enable-network-throttling).

## <a name="restore"></a>Restaurar

### <a name="manage"></a>Gerenciar

#### <a name="can-i-recover-if-i-forgot-my-passphrase"></a>Posso recuperar se esqueci minha frase secreta?

O agente de backup do Azure requer uma frase secreta (que você forneceu durante o registro) para descriptografar os dados de backup durante a restauração. Examine os cenários abaixo para entender suas opções de tratamento de uma senha perdida:

| Computador original <br> *(computador de origem onde os backups foram feitos)* | Senha | Opções Disponíveis |
| --- | --- | --- |
| Disponível |Excluído |Se o computador original (em que os backups foram feitos) estiver disponível e ainda estiver registrado no mesmo cofre dos serviços de recuperação, você poderá regenerar a senha seguindo estas [etapas](./backup-azure-manage-mars.md#re-generate-passphrase).  |
| Excluído |Excluído |Não é possível recuperar os dados ou os dados não estão disponíveis |

Considere as seguintes condições:

* Se você desinstalar e registrar novamente o agente no mesmo computador original com o
  * *Mesma senha*, você pode restaurar os dados de backup.
  * *Senha diferente*. em seguida, você não pode restaurar os dados de backup.
* Se você instalar o agente em um *computador diferente* com o
  * *Mesma senha* (usada no computador original), você pode restaurar os dados de backup.
  * *Senha diferente*, você não pode restaurar os dados de backup.
* Se o computador original estiver corrompido (impedindo a regeneração da senha por meio do console do MARS), mas você puder restaurar ou acessar a pasta de rascunho original usada pelo agente MARS, poderá restaurar (se você esqueceu a senha). Para obter mais assistência, entre em contato com o atendimento ao cliente.

#### <a name="how-do-i-recover-if-i-lost-my-original-machine-where-backups-were-taken"></a>Como fazer recuperar se eu perder minha máquina original (onde os backups foram feitos)?

Se você tiver a mesma senha (que você forneceu durante o registro) da máquina original, poderá restaurar os dados de backup para um computador alternativo. Examine os cenários abaixo para entender as opções de restauração.

| Computador original | Senha | Opções Disponíveis |
| --- | --- | --- |
| Excluído |Disponível |Você pode instalar e registrar o agente MARS em outro computador com a mesma senha que você forneceu durante o registro do computador original. Escolha a **opção de recuperação**  >  **outro local** para executar a restauração. Para obter mais informações, consulte este [artigo](./backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).
| Excluído |Excluído |Não é possível recuperar os dados ou os dados não estão disponíveis |

### <a name="my-backup-jobs-have-been-failing-or-not-running-for-a-long-time-im-past-the-retention-period-can-i-still-restore"></a>Meus trabalhos de backup estavam falhando ou não em execução há muito tempo. Estou além do período de retenção. Ainda posso restaurar?

Como medida de segurança, o backup do Azure preservará o ponto de recuperação mais recente, mesmo que ele esteja além do período de retenção. Quando os backups forem retomados e novos pontos de recuperação forem disponibilizados, o ponto de recuperação mais antigo será removido de acordo com a retenção especificada.

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>O que acontecerá se eu cancelar um trabalho de restauração em andamento?

Se um trabalho de restauração em andamento for cancelado, o processo de restauração será interrompido. Todos os arquivos restaurados antes do cancelamento permanecem no destino configurado (local original ou alternativo), sem nenhuma reversão.

### <a name="does-the-mars-agent-back-up-and-restore-acls-set-on-files-folders-and-volumes"></a>O agente MARS faz backup e restaura ACLs definidas em arquivos, pastas e volumes?

* O agente MARS faz backup de ACLs definidas em arquivos, pastas e volumes
* Para a opção de recuperação de volume, o agente MARS fornece uma opção para ignorar a restauração de permissões de ACL para o arquivo ou pasta que está sendo recuperada
* Para a opção de recuperação de arquivo e pastas individuais, o agente MARS será restaurado com permissões de ACL (não há nenhuma opção para ignorar a restauração de ACL).

## <a name="next-steps"></a>Próximas etapas

[Saiba](tutorial-backup-windows-server-to-azure.md) como fazer backup de um computador Windows.
