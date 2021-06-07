---
title: Solucionar erros de backup de bancos de dados SAP HANA
description: Descreve como solucionar erros comuns que podem ocorrer ao usar o Backup do Microsoft Azure nos bancos de dados SAP HANA.
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: 22800adc323bda8a60278160f24bc559103fb57e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101713330"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Solucionar problemas de backup dos bancos de dados SAP HANA no Azure

Este artigo fornece informações sobre a solução de problemas no backup dos bancos de dados SAP HANA nas máquinas virtuais do Azure. Para obter mais informações sobre os cenários de backup SAP HANA que têm suporte atualmente, confira [Suporte de cenário](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="prerequisites-and-permissions"></a>Pré-requisitos e permissões

Veja as seções [pré-requisitos](tutorial-backup-sap-hana-db.md#prerequisites) e [O que o script de pré-registro faz](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) antes de configurar backups.

## <a name="common-user-errors"></a>Erros comuns de usuário

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **Mensagem de erro**      | <span style="font-weight:normal">O backup do Azure não tem os privilégios de função necessários para executar o backup</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **Possíveis causas:**    | A função pode ter sido substituída.                          |
| **Ação recomendada** | Para resolver o problema, execute o script no painel **Descobrir banco de dados** ou baixe-o [aqui](https://aka.ms/scriptforpermsonhana). Como alternativa, adicione a função 'SAP_INTERNAL_HANA_SUPPORT' ao Workload Backup User (AZUREWLBACKUPHANAUSER). |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| Mensagem de erro      | <span style="font-weight:normal">Falha ao conectar-se ao sistema HANA</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas:**    | A instância do SAP HANA pode estar inativa.<br/>As permissões necessárias para o backup do Azure interagir com o banco de dados HANA não estão definidas. |
| **Ação recomendada** | Verifique se o banco de dados SAP HANA está ativo. Se o banco de dados estiver funcionando perfeitamente, verifique se todas as permissões necessárias foram definidas. Se alguma das permissões estiver ausente, execute o [script de pré-registro](https://aka.ms/scriptforpermsonhana) para adicionar as permissões ausentes. |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| Mensagem de erro      | <span style="font-weight:normal">A instância do SAP HANA especificada é inválida ou não pode ser encontrada</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas:**    | Não é possível fazer backup de várias instâncias do SAP HANA em uma única VM do Azure. |
| **Ação recomendada** | Execute o [script de pré-registro](https://aka.ms/scriptforpermsonhana) na instância do SAP HANA em que você deseja fazer backup. Se o problema persistir, entre em contato com o suporte da Microsoft. |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| Mensagem de erro      | <span style="font-weight:normal">Não há suporte para a operação do SAP HANA especificada</span>              |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas:**    | O backup do Azure para SAP HANA não dá suporte a backup incremental e ações executadas em clientes SAP HANA nativos (ferramenta cockpit de Studio/cockpit/DBA) |
| **Ação recomendada** | Para obter mais informações, confira [aqui](./sap-hana-backup-support-matrix.md#scenario-support). |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| Mensagem de erro      | <span style="font-weight:normal">A cadeia de logs de backup foi interrompida</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas:**    | O destino do backup de logs pode ter sido atualizado do backint para o sistema de arquivos ou o backint executável pode ter sido alterado |
| **Ação recomendada** | Acione um backup completo para resolver o problema                   |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| Mensagem de erro      | <span style="font-weight:normal">Atualização de SDC para MDC detectada</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas:**    | A instância do SAP HANA foi atualizada de SDC para MDC. Os backups falharão após a atualização. |
| **Ação recomendada** | Siga as etapas listadas na [atualização de SDC para MDC](#sdc-to-mdc-upgrade-with-a-change-in-sid) para resolver o problema |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Mensagem de erro      | <span style="font-weight:normal">Configuração de backint inválida detectada</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **Possíveis causas:**    | Os parâmetros de backup estão especificados incorretamente para o backup do Azure |
| **Ação recomendada** | Verifique se os seguintes parâmetros (backint) foram definidos:<br/>\* [catalog_backup_using_backint:true]<br/>\* [enable_accumulated_catalog_backup:false]<br/>\* [parallel_data_backup_backint_channels:1]<br/>\* [log_backup_timeout_s:900)]<br/>\* [backint_response_timeout:7200]<br/>Se os parâmetros baseados em backint estiverem presentes no HOST, remova-os. Se os parâmetros não estiverem presentes no nível do HOST, mas foram modificados manualmente no nível do banco de dados, reverta-os para os valores apropriados, conforme descrito anteriormente. Ou execute [Parar proteção e manter dados de backup](./sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) no portal do Azure e selecione **Retomar backup**. |

### <a name="usererrorincompatiblesrctargetsystemsforrestore"></a>UserErrorIncompatibleSrcTargetSystemsForRestore

|Mensagem de erro  |Os sistemas de origem e destino para restauração são incompatíveis  |
|---------|---------|
|Possíveis causas   | Os sistemas de origem e destino selecionados para restauração são incompatíveis        |
|Ação recomendada   |   Verifique se o cenário de restauração não está na seguinte lista de possíveis restaurações incompatíveis: <br><br>   **Caso 1:** Não é possível renomear SYSTEMDB durante a restauração.  <br><br> **Caso 2:** Origem - SDC e destino - MDC: Não é possível restaurar o banco de dados de origem como SYSTEMDB ou banco de dados de locatário no destino. <br><br> **Caso 3:** Origem - MDC e destino - SDC: Não é possível restaurar o banco de dados de origem (SYSTEMDB ou banco de dados de locatário) no destino. <br><br>  Para obter mais informações, confira a observação **1642148** no Launchpad do [suporte SAP](https://launchpad.support.sap.com). |

## <a name="restore-checks"></a>Restaurar verificações

### <a name="single-container-database-sdc-restore"></a>Restauração do banco de dados de contêiner individual (SDC)

Tome cuidado com as entradas durante a restauração de um banco de dados de contêiner individual (SDC) para HANA para outro computador SDC. O nome do banco de dados deve ser fornecido com letras minúsculas e com "sdc" entre parênteses. A instância do HANA será exibida em maiúsculas.

Suponha que foi feito o backup de uma instância do SDC HANA "H21". A página dos itens de backup mostrará o nome do item de backup como **"h21(sdc)"** . Se você tentar restaurar esse banco de dados para outro SDC de destino, digamos o H11, será necessário fornecer as entradas a seguir.

![Nome do banco de dados SDC restaurado](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Observe os seguintes pontos:

- Por padrão, o nome do banco de dados restaurado será preenchido com o nome do item de backup. Neste caso, h21(sdc).
- Selecionar o destino como H11 não alterará automaticamente o nome do banco de BD restaurado. **Ele deve ser editado para h11(sdc)** . Para SDC, o nome do banco de dados restaurado será a ID da instância de destino com letras minúsculas e 'sdc' entre parênteses.
- Como o SDC pode ter apenas um único banco de dados, você também precisa marcar a caixa de seleção para permitir a substituição dos existentes com os dados de ponto de recuperação.
- O Linux diferencia maiúsculas de minúsculas. Portanto, tenha cuidado para manter as maiúsculas e minúsculas.

### <a name="multiple-container-database-mdc-restore"></a>Restauração do banco de dados de contêiner múltiplo (SDC)

Em bancos de dados de contêiner múltiplo para HANA, a configuração padrão é SYSTEMDB + 1 ou mais bancos de dados de locatário. A restauração de uma instância inteira do SAP HANA significa restaurar os bancos de dados de SYSTEMDB e de locatário. Uma restaura o SYSTEMDB primeiro e depois continua com o banco de dados de locatário. Basicamente, banco de dados do sistema significa substituir as informações do sistema no destino selecionado. Essa restauração também substitui as informações relacionadas ao BackInt na instância de destino. Portanto, depois que o banco de dados do sistema for restaurado para uma instância de destino, execute o script de pré-registro novamente. Somente então as restaurações subsequentes do banco de bancos de locatário serão realizadas com sucesso.

## <a name="back-up-a-replicated-vm"></a>Backup de uma VM replicada

### <a name="scenario-1"></a>Cenário 1

A VM original foi replicada usando o Azure Site Recovery ou backup de VM do Azure. A nova VM foi criada para simular a VM antiga. Ou seja, as configurações são exatamente as mesmas. (Isso ocorre porque a VM original foi excluída e a restauração foi feita a partir do backup da VM ou do Azure Site Recovery).

Esse cenário pode incluir dois possíveis casos. Saiba como fazer backup da VM replicada em ambos os casos:

1. A nova VM criada tem o mesmo nome e está no mesmo grupo de recursos e assinatura que a VM excluída.

    - A extensão já está presente na VM, mas não fica visível para os serviços
    - Execute o script de pré-registro
    - Registre a extensão novamente para o mesmo computador no portal do Azure (**Backup** -> **Exibir detalhes** -> Selecione a VM do Azure correspondente -> Registrar novamente)
    - Os bancos de dados já existentes com backup (da VM excluída) devem ser iniciados com sucesso no backup

2. A nova VM criada tem:

    - um nome diferente da VM excluída
    - o mesmo nome que a VM excluída, mas está em um grupo de recursos ou assinatura diferente (em comparação à VM excluída)

    Nesse caso, siga estas etapas:

    - A extensão já está presente na VM, mas não fica visível para os serviços
    - Execute o script de pré-registro
    - Se você descobrir e proteger os novos bancos de dados, começará a ver bancos de dados ativos duplicados no Portal. Para evitar isso, [Pare a proteção com retenção de dados](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) para os bancos de dados antigos. Em seguida, continue com as etapas restantes.
    - Descubra os bancos de dados para habilitar o backup
    - Habilite os backups nesses bancos de dados
    - Os backups de bancos de dados já existentes (da VM excluída) continuarão a ser armazenados no vault (e os backups são retidos de acordo com a política)

### <a name="scenario-2"></a>Cenário 2

A VM original foi replicada usando o Azure Site Recovery ou backup de VM do Azure. A nova VM foi criada fora do conteúdo - para ser usada como modelo. Essa é uma nova VM com um novo SID.

Siga estas etapas para habilitar os backups na nova VM:

- A extensão já está presente na VM, mas não está visível para os serviços
- Execute o script de pré-registro. Com base no SID da nova VM, podem surgir dois cenários:
  - A VM original e a nova VM têm o mesmo SID. O script de pré-registro é executado com sucesso.
  - A VM original e a nova VM têm SIDs diferentes. O script de pré-registro falha. Entre em contato com o suporte para obter ajuda neste cenário.
- Descubra os bancos de dados para os quais você deseja fazer backup
- Habilite os backups nesses bancos de dados

## <a name="sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm"></a>Atualização de versão do SDC ou atualização de versão do MDC na mesma VM

Atualizações no sistema operacional, alterações de versão do SDC ou alterações de versão do MDC que não mudam o SID podem ser tratadas da seguinte maneira:

- Verifique se a nova versão do sistema operacional, SDC ou MDC é [aceita pelo Backup do Azure](sap-hana-backup-support-matrix.md#scenario-support) no momento
- [Pare a proteção com retenção de dados](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) para o banco de dados
- Realize o upgrade ou a atualização
- Execute novamente o script de pré-registro. Geralmente, o processo de atualização pode remover [as funções necessárias](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does). A execução do script de pré-registro ajudará a verificar todas as funções necessárias.
- Retome a proteção para um banco de dados SQL

## <a name="sdc-to-mdc-upgrade-with-no-change-in-sid"></a>Atualização de SDC para MDC sem alteração no SID

As atualizações de SDC para MDC que não mudam o SID podem ser tratadas da seguinte maneira:

- Verifique se a nova versão do MDC é [aceita pelo Backup do Azure](sap-hana-backup-support-matrix.md#scenario-support) no momento
- [Pare a proteção com retenção de dados](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) para o banco de dados SDC antigo
- Realize a atualização. Após a conclusão, o sistema HANA agora é MDC com um banco de dados do sistema e os bancos de banco de locatário
- Execute novamente o [script de pré-registro](https://aka.ms/scriptforpermsonhana)
- Registre a extensão novamente para o mesmo computador no portal do Azure (**Backup** -> **Exibir detalhes** -> Selecione a VM do Azure correspondente -> Registrar novamente)
- Selecione **redescobrir bancos** de todos para a mesma VM. Essa ação deve mostrar os novos bancos de bancos na etapa 3 como SYSTEMDB e o banco de dados de locatário, não o SDC
- O banco de dados SDC mais antigo continua existindo no vault e os dados de backup antigos são retidos de acordo com a política
- Configure o backup para esses bancos de dados

## <a name="sdc-to-mdc-upgrade-with-a-change-in-sid"></a>Atualização de SDC para MDC sem uma alteração no SID

As atualizações de SDC para MDC que mudam o SID podem ser tratadas da seguinte maneira:

- Verifique se a nova versão do MDC é [aceita pelo Backup do Azure](sap-hana-backup-support-matrix.md#scenario-support) no momento
- **Pare a proteção com retenção de dados** para o banco de dados SDC antigo
- Realize a atualização. Após a conclusão, o sistema HANA agora é MDC com um banco de dados do sistema e os bancos de banco de locatário
- Execute novamente o [script de pré-registro](https://aka.ms/scriptforpermsonhana) com os detalhes corretos (novo SID e MDC). Devido a uma alteração no SID, você pode ter problemas ao executar o script com sucesso. Entre em contato com o suporte de Backup do Azure, se você tiver problemas.
- Registre a extensão novamente para o mesmo computador no portal do Azure (**Backup** -> **Exibir detalhes** -> Selecione a VM do Azure correspondente -> Registrar novamente)
- Selecione **redescobrir bancos** de todos para a mesma VM. Essa ação deve mostrar os novos bancos de bancos na etapa 3 como SYSTEMDB e o banco de dados de locatário, não o SDC
- O banco de dados SDC mais antigo continua existindo no vault e os dados de backup antigos são retidos de acordo com a política
- Configure o backup para esses bancos de dados

## <a name="re-registration-failures"></a>Falhas no novo registro

Verifique se um ou mais dos seguintes sintomas existem, antes de acionar a operação de novo registro:

- Todas as operações (como backup, restauração e configuração de backup) falham na VM com um dos seguintes códigos de erro: **WorkloadExtensionNotReachable, UserErrorWorkloadExtensionNotInstalled, WorkloadExtensionNotPresent, WorkloadExtensionDidntDequeueMsg**.
- Se a área de **Status de backup** para o item de backup estiver mostrando **Não acessível**, descarte todas as outras causas que possam resultar no mesmo status:

  - Falta de permissão para realizar as operações relacionadas ao backup na VM
  - A VM está desligada, portanto, os backups não podem ser realizados
  - Problemas de rede

Esses sintomas podem surgir devido a uma ou mais das seguintes razões:

- Uma extensão foi excluída ou desinstalada no portal.
- A VM foi restaurada retroativamente por meio da restauração de disco in-loco.
- A VM foi desligada por um período prolongado, portanto, a configuração de extensão expirou.
- A VM foi excluída e outra VM foi criada com o mesmo nome e no mesmo grupo de recursos que a VM excluída.

Nos cenários anteriores, recomendamos que você acione uma operação de novo registro na VM.

## <a name="next-steps"></a>Próximas etapas

- Revise as [perguntas frequentes](./sap-hana-faq-backup-azure-vm.md) sobre o backup de bancos de dados SAP HANA nas VMs do Azure.
