---
title: Configuração de dados de diagnóstico e uso do Azure SQL Edge
description: Saiba como configurar os dados de diagnóstico e de uso no Azure SQL Edge.
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 08/04/2020
ms.openlocfilehash: 81b2aabbae148faec06c9ab420bdfecde475b4bb
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97604984"
---
# <a name="azure-sql-edge-usage-and-diagnostics-data-configuration"></a>Configuração de dados de diagnóstico e uso do Azure SQL Edge

Por padrão, o Azure SQL Edge coleta informações sobre como seus clientes estão usando o aplicativo. Especificamente, o Azure SQL Edge coleta informações sobre a experiência de implantação, o uso e o desempenho. Essas informações ajudam a Microsoft a melhorar o produto para melhor atender às necessidades do cliente. Por exemplo, a Microsoft coleta informações sobre os tipos de códigos de erro que os clientes encontram para que possamos corrigir bugs relacionados, melhorar nossa documentação sobre como usar o Azure SQL Edge e determinar se os recursos devem ser adicionados ao produto para atender melhor aos clientes.

Especificamente, a Microsoft não envia nenhum dos seguintes tipos de informações por meio desse mecanismo:

- Quaisquer valores de dentro de tabelas de usuário.
- Quaisquer credenciais de logon ou outras informações de autenticação.
- Quaisquer dados pessoais ou de clientes.

O seguinte exemplo de cenário inclui informações de uso de recursos que ajudam a melhorar o produto.

Uma consulta de exemplo das consultas usadas para a coleta de dados de diagnóstico e uso é fornecida abaixo. A consulta identifica a contagem e os tipos de diferentes fontes de dados de streaming que estão sendo usadas no Azure SQL Edge. Esses dados ajudam a Microsoft a identificar quais fontes de dados de streaming estão sendo usadas normalmente, de modo que a Microsoft possa melhorar o desempenho e a experiência do usuário associadas a essas fontes de dados. 

```sql
select 
count(*) as [count], sum(inputs) as inputs, sum(outputs) as outputs, sum(linked_to_job) 
as linked_to_job, data_source_type
from ( 
select isnull(value,'unknown') as data_source_type, inputs, outputs, linked_to_job
from 
    ( 
        select 
        convert(sysname, lower(substring(ds.location, 0, charindex('://', ds.location))), 1) as data_source_type, 
        isnull(inputs, 0) as inputs, isnull(outputs, 0) as outputs, isnull(js.stream_id/js.stream_id, 0) as linked_to_job 
        from sys.external_streams es
        join sys.external_data_sources ds 
             on es.data_source_id = ds.data_source_id
        left join 
            ( 
            select stream_id, max(cast(is_input as int)) inputs, max(cast(is_output as int)) outputs 
            from sys.external_job_streams group by stream_id 
            ) js
             on js.stream_id = es.object_id 
    ) ds
left join 
    (
        select value from string_split('edgehub,sqlserver,kafka', ',')) as known_ep on data_source_type = value 
    ) known_ds
group by data_source_type
```

## <a name="disable-usage-and-diagnostic-data-collection"></a>Desabilitar a coleta de dados de diagnóstico e uso

A coleta de dados de diagnóstico e uso no Azure SQL Edge pode ser desabilitada usando qualquer um dos métodos abaixo.

> [!NOTE]
> O uso e os dados de diagnóstico não podem ser desabilitados para a versão do desenvolvedor.

### <a name="disable-usage-and-diagnostics-using-environment-variables"></a>Desabilitar o uso e o diagnóstico usando variáveis de ambiente

Para desabilitar a coleta de dados de diagnóstico e uso no Azure SQL Edge, adicione a seguinte variável de ambiente e defina seu valor como `*False*` . Para obter mais informações sobre como configurar o Azure SQL Edge usando variáveis de ambiente, consulte [Configurar usando variáveis de ambiente](configure.md#configure-by-using-environment-variables).

`MSSQL_TELEMETRY_ENABLED = TRUE | FALSE`

- TRUE – habilita a coleta de dados de diagnóstico e uso. Essa é a configuração padrão.
- FALSE-desabilita a coleta de dados de diagnóstico e de uso.

### <a name="disable-usage-and-diagnostics-using-mssqlconf-file"></a>Desabilitar o uso e diagnóstico usando o arquivo MSSQL. conf

Para desabilitar a coleta de dados de diagnóstico e uso no Azure SQL Edge, adicione as seguintes linhas no arquivo MSSQL. conf na unidade de armazenamento persistente que está mapeada para a pasta/var/opt/MSSQL/no módulo SQL Edge. Para obter mais informações sobre como configurar o Azure SQL Edge usando o arquivo MSSQL. conf, consulte [Configurar usando o arquivo MSSQL. conf](configure.md#configure-by-using-an-mssqlconf-file).

```ini
[telemetry]
customerfeedback = false
```

## <a name="local-audit-of-usage-and-diagnostic-data-collection"></a>Auditoria local de coleta de dados de diagnóstico e uso

O componente de auditoria local do uso do Azure SQL Edge e da coleta de dados de diagnóstico pode gravar dados coletados pelo serviço em uma pasta designada, representando os dados (logs) que serão enviados à Microsoft. A finalidade da Auditoria Local é permitir que os clientes vejam todos os dados que a Microsoft coleta com esse recurso, para fins de conformidade, regulatórios ou de validação de privacidade.

### <a name="enable-local-audit-of-usage-and-diagnostics-data"></a>Habilitar a auditoria local de dados de diagnóstico e uso

Para habilitar o uso de auditoria local e dados de diagnóstico no Azure SQL Edge

1. Crie um diretório de destino para o novo armazenamento de log de auditoria local. Esse diretório de destino pode estar no host ou dentro do contêiner. No exemplo abaixo, o diretório de destino é criado no mesmo volume de montagem que é mapeado para o caminho/var/opt/MSSQL/no SQL Edge.

   ```bash
   sudo mkdir <host mount path>/audit
   ```

2. Configure a auditoria de dados de diagnóstico e uso usando as variáveis de ambiente ou o arquivo MSSQL. conf.

   - Usando variáveis de ambiente – adicione a seguinte variável de ambiente à implantação do SQL Edge e especifique o diretório de destino para os arquivos de auditoria.
   
     `*MSSQL_TELEMETRY_DIR = <host mount path>/audit*`
   
   - Usando o arquivo MSSQL. conf – adicione as seguintes linhas no arquivo MSSQL. conf e especifique o diretório de destino para os arquivos de auditoria.
       ```ini
       [telemetry]
       userrequestedlocalauditdirectory  = <host mount path>/audit
       ```  

## <a name="next-steps"></a>Próximas etapas

- [Conectar-se ao SQL do Azure no Edge](connect.md)
- [Criar uma solução IoT de ponta a ponta com o SQL no Edge](tutorial-deploy-azure-resources.md)
