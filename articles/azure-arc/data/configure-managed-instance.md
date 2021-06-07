---
title: Configurar instância gerenciada SQL do Arc habilitada para o Azure
description: Configurar instância gerenciada SQL do Arc habilitada para o Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 6e2443014f6788504a11784945078187a5a72de4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98985862"
---
# <a name="configure-azure-arc-enabled-sql-managed-instance"></a>Configurar instância gerenciada SQL do Arc habilitada para o Azure

Este artigo explica como configurar a instância gerenciada do SQL do Arc habilitado para o Azure.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configure-resources"></a>Configurar recursos

### <a name="configure-using-azure-data-cli-azdata"></a>Configurar usando [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]

Você pode editar a configuração das instâncias gerenciadas do SQL do Arc do Azure habilitadas com o [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] . Execute o comando a seguir para ver as opções de configuração. 

```
azdata arc sql mi edit --help
```

O exemplo a seguir define o núcleo da CPU e as solicitações e limites de memória.

```
azdata arc sql mi edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n <NAME_OF_SQL_MI>
```

Para exibir as alterações feitas na instância gerenciada do SQL, você pode usar os seguintes comandos para exibir o arquivo de configuração YAML:

```
azdata arc sql mi show -n <NAME_OF_SQL_MI>
```

## <a name="configure-server-options"></a>Configurar opções de servidor

Você pode definir definições de configuração do servidor para a instância gerenciada SQL do Arc habilitada após a hora de criação. Este artigo descreve como definir configurações como habilitar ou desabilitar o agente MSSQL, habilitar sinalizadores de rastreamento específicos para cenários de solução de problemas.

Para alterar uma dessas configurações, siga estas etapas:

1. Crie um arquivo `mssql-custom.conf` personalizado que inclua as configurações de destino. O exemplo a seguir habilita o SQL Agent e habilita o sinalizador de rastreamento 1204.:

   ```
   [sqlagent]
   enabled=true
   
   [traceflag]
   traceflag0 = 1204
   ```

1. Copie o arquivo `mssql-custom.conf` para `/var/opt/mssql` no contêiner `mssql-miaa` no pod `master-0`. Substitua `<namespaceName>` pelo nome do cluster de Big Data.

   ```bash
   kubectl cp mssql-custom.conf master-0:/var/opt/mssql/mssql-custom.conf -c mssql-server -n <namespaceName>
   ```

1. Reinicie a Instância do SQL Server.  Substitua `<namespaceName>` pelo nome do cluster de Big Data.

   ```bash
   kubectl exec -it master-0  -c mssql-server -n <namespaceName> -- /bin/bash
   supervisorctl restart mssql-server
   exit
   ```


**Limitações conhecidas**
- As etapas acima exigem permissões de administrador do cluster do Kubernetes
- Isso está sujeito a alterações durante a visualização
