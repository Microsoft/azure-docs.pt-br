---
title: Procedimentos armazenados de gerenciamento-banco de dados do Azure para MySQL
description: Saiba quais procedimentos armazenados no banco de dados do Azure para MySQL são úteis para ajudá-lo a configurar a replicação de dados, definir o fuso horário e eliminar as consultas.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 7a1aa061bb8c8be3a676e0e5bb690b2a9749b6c8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94536125"
---
# <a name="azure-database-for-mysql-management-stored-procedures"></a>Procedimentos armazenados de gerenciamento do banco de dados do Azure para MySQL

Os procedimentos armazenados estão disponíveis no banco de dados do Azure para servidores MySQL para ajudar a gerenciar seu servidor MySQL. Isso inclui o gerenciamento de conexões do seu servidor, consultas e configuração do Replicação de Dados.  

## <a name="data-in-replication-stored-procedures"></a>Replicação de Dados procedimentos armazenados

A Replicação nos dados permite sincronizar dados de um Servidor MySQL em execução local, em máquinas virtuais ou em serviços de banco de dados hospedados por outros provedores de nuvem com o serviço do Banco de Dados do Azure para MySQL.

Os procedimentos armazenados a seguir são usados para configurar ou remover Replicação de Dados entre uma origem e uma réplica.

|**Nome do procedimento armazenado**|**Parâmetros de Entrada**|**Parâmetros de saída**|**Observação de uso**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|N/D|Para transferir dados com o modo SSL, passe o contexto do certificado de autoridade de certificação para o parâmetro master_ssl_ca. </br><br>Para transferir dados sem SSL, passe uma cadeia de caracteres vazia para o parâmetro master_ssl_ca.|
|*mysql.az_replication _start*|N/D|N/D|Inicia a replicação.|
|*mysql.az_replication _stop*|N/D|N/D|Para a replicação.|
|*mysql.az_replication _remove_master*|N/D|N/D|Remove a relação de replicação entre a origem e a réplica.|
|*mysql.az_replication_skip_counter*|N/D|N/D|Ignora um erro de replicação.|

Para configurar Replicação de Dados entre uma origem e uma réplica no banco de dados do Azure para MySQL, consulte [como configurar o replicação de dados](howto-data-in-replication.md).

## <a name="other-stored-procedures"></a>Outros procedimentos armazenados

Os procedimentos armazenados a seguir estão disponíveis no banco de dados do Azure para MySQL para gerenciar o servidor.

|**Nome do procedimento armazenado**|**Parâmetros de Entrada**|**Parâmetros de saída**|**Observação de uso**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|N/D|Equivalente a [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) Command. Encerrará a conexão associada ao processlist_id fornecido depois de encerrar qualquer instrução que a conexão estiver executando.|
|*mysql.az_kill_query*|processlist_id|N/D|Equivalente a [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html) Command. Encerrará a instrução que a conexão está executando no momento. Deixa a própria conexão ativa.|
|*mysql.az_load_timezone*|N/D|N/D|Carrega tabelas de fuso horário para permitir que o `time_zone` parâmetro seja definido para valores nomeados (ex. "EUA/Pacífico").|

## <a name="next-steps"></a>Próximas etapas
- Saiba como configurar [replicação de dados](howto-data-in-replication.md)
- Saiba como usar as [tabelas de fuso horário](howto-server-parameters.md#working-with-the-time-zone-parameter)
