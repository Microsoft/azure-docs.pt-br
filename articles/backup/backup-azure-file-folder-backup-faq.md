---
title: Perguntas comuns ao fazer backup de arquivos e pastas com Backup do Azure
description: Aborda perguntas comuns sobre como fazer backup de arquivos e pastas com Backup do Azure.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: dacurwin
ms.openlocfilehash: dd800c0eeb18fe45b44a72aeb58b500623b2b366
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705089"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>Perguntas comuns sobre como fazer backup de arquivos e pastas

Este artigo possui respostas para perguntas comuns sobram fazendo backup de arquivos e pastas com o agente do Microsoft Azure Recovery Services (MARS) na [Backup do Azure](backup-overview.md) service.

## <a name="general"></a>Geral

## <a name="configure-backups"></a>Configurar backups

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>Onde posso baixar a versão mais recente do agente MARS?
O agente de MARS mais recente usado ao fazer backup de máquinas do Windows Server, o System Center DPM e o servidor de Backup do Microsoft Azure está disponível para [baixar](https://aka.ms/azurebackup_agent).

### <a name="how-long-are-vault-credentials-valid"></a>Quanto tempo são as credenciais do cofre válido?
As credenciais do cofre expiram após 48 horas. Se o arquivo de credenciais expirar, baixe o arquivo novamente do portal do Azure.

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>De quais drives pode fazer backup de arquivos e pastas?

Você não pode fazer backup dos seguintes tipos de unidades e volumes:

* Mídia removível: Todas as fontes de item de backup devem ser indicadas como fixas.
* Volumes somente leitura: O volume deve ser gravável para que o VSS (Serviço de Cópias de Sombra de Volume) funcione.
* Volumes offline: O volume deve estar online para que o VSS funcione.
* Compartilhamentos de rede: O volume deve ser local para o backup do servidor usando o backup online.
* Volumes protegidos pelo BitLocker: O volume deverá ser desbloqueado antes que o backup possa ocorrer.
* Identificação de Sistema de Arquivos: O NTFS é o único sistema de arquivos com suporte.

### <a name="what-file-and-folder-types-are-supported"></a>Há suporte para quais tipos de arquivo e pasta?

[Saiba mais](backup-support-matrix-mars-agent.md#supported-file-types-for-backup) sobre os tipos de arquivos e pastas com suporte para backup.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>Pode usar o agente MARS para fazer backup de arquivos e pastas em uma VM do Azure?  
Sim. O Backup do Azure fornece backup em nível de VM para VMs do Azure usando a extensão de VM para o agente de VM do Azure. Se você quiser fazer backup de arquivos e pastas no sistema operacional convidado Windows na VM, você pode instalar o agente MARS para fazer isso.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>Pode usar o agente MARS para fazer backup de arquivos e pastas no armazenamento temporário para a VM do Azure?
Sim. Instale o agente MARS e fazer backup de arquivos e pastas no sistema operacional convidado Windows em um armazenamento temporário.

- Trabalhos de backup falham quando os dados de armazenamento temporário forem apagados.
- Se os dados de armazenamento temporário são excluídos, você só pode restaurar para o armazenamento não volátil.

### <a name="how-do-i-register-a-server-to-another-region"></a>Como registrar um servidor para outra região?

Dados de backup são enviados para o datacenter do cofre no qual o servidor está registrado. A maneira mais fácil alterar o datacenter é desinstalar e reinstalar o agente e, em seguida, registre o computador para um novo cofre na região em que você precisa.

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>O Windows Server 2012 do suporte do agente de MARS de duplicação ocorre?
Sim. O agente do MARS converte os dados com eliminação de duplicação em dados normais quando prepara a operação de backup. Ele, em seguida, otimiza os dados para backup, criptografa os dados e, em seguida, envia os dados criptografados no cofre.

## <a name="manage-backups"></a>Gerenciar backups

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>O que acontece se eu renomear um computador Windows configurado para backup?

Quando você renomeia uma máquina Windows, todos os backups atualmente configurados serão interrompidos.

- Você precisa registrar o novo nome de máquina com o Cofre de Backup.
- Ao registrar o novo nome com o cofre, a primeira operação é uma *completo* backup.
- Se você precisar recuperar dados de backup no cofre com o nome antigo do servidor, use a opção para restaurar para um local alternativo no Assistente para recuperar dados. [Saiba mais](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>O que é o comprimento do caminho de arquivo máximo para o backup?
O agente do MARS se baseia em NTFS e usa a especificação de comprimento de caminho do arquivo limitada pela [API do Windows](/windows/desktop/FileIO/naming-a-file#fully-qualified-vs-relative-paths). Se os arquivos que você deseja proteger são maiores do que o valor permitido, faça backup da pasta pai ou a unidade de disco.  

### <a name="what-characters-are-allowed-in-file-paths"></a>Quais caracteres são permitidos em caminhos de arquivo?

O agente do MARS se baseia em NTFS e permite [suporte para caracteres](/windows/desktop/FileIO/naming-a-file#naming-conventions) em nomes ou caminhos de arquivo.

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>O aviso "Backups do Azure não foram configurados para este servidor" é exibida.
Esse aviso pode aparecer mesmo que você configurou uma política de backup, quando as configurações de agendamento de backup armazenadas no servidor local não são as mesmas que as configurações armazenadas no cofre de backup.
- Quando o servidor ou as configurações tiverem sido recuperadas para um bom estado conhecido, agendas de backup podem perder a sincronização.
- Se você receber esse aviso [configurar](backup-azure-manage-windows-server.md) política de backup novamente e, em seguida, executar uma demanda de backup para sincronizar novamente o servidor local com o Azure.


## <a name="manage-the-backup-cache-folder"></a>Gerenciar a pasta de cache de backup

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>Qual é o requisito de tamanho mínimo para a pasta de cache?
O tamanho da pasta de cache determina a quantidade de dados submetida a backup.
- Os volumes de pasta de cache devem ter espaço livre igual a pelo menos de 5 a 10% do tamanho total dos dados de backup.
- Se o volume tiver menos de 5% de espaço livre, aumente o tamanho do volume ou mova a pasta de cache para um volume com espaço suficiente.
- Se você fizer backup do estado do sistema Windows, você precisaria mais 30 a 35 GB de espaço livre no volume que contém a pasta de cache.

### <a name="how-to-check-if-scratch-folder-is-valid-and-accessible"></a>Como verificar se a pasta de rascunho é válido e está acessível?

1. Por padrão a pasta de rascunho está localizada em `\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
2. Verifique se o caminho do seu local de pasta de rascunho corresponde com os valores das entradas de chave do Registro mostradas abaixo:

  | Caminho do registro | Chave do Registro | Valor |
  | --- | --- | --- |
  | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Novo local da pasta de cache* |
  | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Novo local da pasta de cache* |

### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>Como altero o local do cache para o agente de MARS?

1. Execute este comando no prompt de comandos com privilégios elevados para interromper o mecanismo de Backup:

    ```PS C:\> Net stop obengine```

2. Não mova os arquivos. Em vez disso, copie a pasta de espaço do cache para uma unidade diferente que tenha espaço suficiente.
3. Atualize as seguintes entradas de registro com o caminho da nova pasta de cache.<br/>

    | Caminho do registro | Chave do Registro | Valor |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Novo local da pasta de cache* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Novo local da pasta de cache* |

4. Reinicie o mecanismo de Backup em um prompt de comando elevado:

    ```PS C:\> Net start obengine```

5. Depois que o backup for concluído com êxito usando o novo local, você pode remover a pasta de cache original.


### <a name="where-should-the-cache-folder-be-located"></a>Onde a pasta de cache deve estar localizada?

Os locais a seguir para a pasta de cache não são recomendados:

* Mídia removível compartilhamento de rede: A pasta de cache deve ser local para o servidor que precisa de backup usando o backup online. Não há suporte para os locais de rede ou para a mídia removível como unidades USB
* Volumes offline: A pasta de cache deve estar online para o backup esperado com o Agente de Backup do Azure

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>Há todos os atributos da pasta de cache que não têm suporte?
Os atributos a seguir, ou suas combinações, não têm suporte para a pasta de cache:

* Criptografado
* Eliminação de duplicação
* Compactado
* Esparsos
* Ponto de nova análise

A pasta de cache e o VHD dos metadados não têm os atributos necessários para o agente de Backup do Azure.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>Há uma maneira de ajustar a quantidade de largura de banda usada para o backup?

Sim, você pode usar o **alterar propriedades** opção no agente de MARS para ajustar a largura de banda e o intervalo. [Saiba mais](backup-configure-vault.md#enable-network-throttling).

## <a name="restore"></a>Restaurar

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>O que acontecerá se eu cancelar um trabalho de restauração em andamento?

Se um trabalho de restauração em andamento for cancelado, interrompe o processo de restauração. Todos os arquivos restaurados antes do cancelamento permanecem no destino configurado (local original ou alternativo), sem qualquer reversões.


## <a name="next-steps"></a>Próximas etapas

[Saiba mais](tutorial-backup-windows-server-to-azure.md) como fazer backup de um computador Windows.
