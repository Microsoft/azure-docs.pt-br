---
title: Solucionar erros ao fazer backup de bancos de dados do SAP HANA usando o backup do Azure | Microsoft Docs
description: Descreve como solucionar erros comuns que podem ocorrer quando você usa o backup do Azure para fazer backup de bancos de dados do SAP HANA.
author: pvrk
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: pullabhk
ms.openlocfilehash: 221b669c141681749709d6a5a406c78499f21032
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465482"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Solucionar problemas de backup de bancos de dados SAP HANA no Azure

Este artigo fornece informações de solução de problemas para fazer backup de bancos de dados SAP HANA em máquinas virtuais do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Como parte dos pré-requisitos, verifique se o script de [pré-](backup-azure-sap-hana-database.md#prerequisites)registro foi executado na máquina virtual em que o Hana está instalado.

### <a name="setting-up-permissions"></a>Configurando permissões

O que o script de auto-registro faz:

1. Cria AZUREWLBACKUPHANAUSER no sistema HANA e adiciona essas funções e permissões necessárias:
    - ADMIN. do banco de dados: para criar novos bancos durante a restauração.
    - LEITURA do catálogo: para ler o catálogo de backup.
    - SAP_INTERNAL_HANA_SUPPORT: para acessar algumas tabelas privadas.
2. Adiciona uma chave para Hdbuserstore para o plug-in do HANA para lidar com todas as operações (consultas de banco de dados, operações de restauração, configuração e execução de backup).
   
   Para confirmar a criação da chave, execute o comando HDBSQL no computador HANA com as credenciais do SIDADM:

    ``` hdbsql
    hdbuserstore list
    ```
    
    A saída do comando deve exibir a chave {SID} {DBNAME}, com o usuário mostrado como AZUREWLBACKUPHANAUSER.

> [!NOTE]
> Verifique se você tem um conjunto exclusivo de arquivos SSFS em **/usr/SAP/{Sid}/Home/.HDB/** . Deve haver apenas uma pasta neste caminho.

### <a name="setting-up-backint-parameters"></a>Configurando parâmetros de BackInt

Depois que um banco de dados é escolhido para backup, o serviço de backup do Azure configura os parâmetros backInt no nível do banco de dados:

- [catalog_backup_using_backint: true]
- [enable_accumulated_catalog_backup: false]
- [parallel_data_backup_backint_channels: 1]
- [log_backup_timeout_s:900)]
- [backint_response_timeout: 7200]

> [!NOTE]
> Verifique se esses parâmetros *não* estão presentes no nível do host. Os parâmetros de nível de host substituirão esses parâmetros e poderão causar um comportamento inesperado.

## <a name="common-user-errors"></a>Erros comuns do usuário

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

dados| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| Falha ao conectar ao sistema HANA. Verifique se o sistema está em execução.| O serviço de backup do Azure não pode se conectar ao HANA porque o banco de dados do HANA está inoperante. Ou o HANA está em execução, mas não permite que o serviço de backup do Azure se conecte. | Verifique se o serviço ou banco de dados HANA está inoperante. Se o banco de dados ou serviço do HANA estiver em execução, verifique se [todas as permissões estão definidas](#setting-up-permissions). Se a chave estiver ausente, execute novamente o script de auto-registro para criar uma nova chave. |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Mensagem de erro | Possíveis causas | Ação recomendada |
|---|---|---|
| Configuração de BACKINT inválida detectada. Interrompa a proteção e reconfigure o banco de dados.| Os parâmetros backInt estão especificados incorretamente para o backup do Azure. | Verifique se [os parâmetros estão definidos](#setting-up-backint-parameters). Se os parâmetros baseados em backInt estiverem presentes no HOST, remova-os. Se os parâmetros não estiverem presentes no nível do HOST, mas tiverem sido modificados manualmente em um nível de banco de dados, reverta-os para os valores apropriados, conforme descrito anteriormente. Ou execute o **Stop Protection e mantenha os dados de backup** do portal do Azure e selecione retomar **backup**.|
