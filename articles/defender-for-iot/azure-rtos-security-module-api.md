---
title: Módulo de segurança para a API do Azure RTOS
description: API de referência para o módulo de segurança para os RTOS do Azure.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: cec28f9290808836ec2dfd334b23fe8c76df03fc
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120055"
---
# <a name="security-module-for-azure-rtos-api"></a>Módulo de segurança para a API do Azure RTOS 

Essa API destina-se ao uso somente com o módulo de segurança para os RTOS do Azure. Para obter recursos adicionais, consulte o [módulo de segurança para o recurso GitHub de RTOs do Azure](https://github.com/azure-rtos/azure-iot-preview/releases). 

## <a name="enable-security-module-for-azure-rtos"></a>Habilitar o módulo de segurança para RTOS do Azure

**nx_azure_iot_security_module_enable**

### <a name="prototype"></a>Protótipo

```c
UINT nx_azure_iot_security_module_enable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Descrição

Essa rotina habilita o subsistema do módulo de segurança do Azure IoT. Um computador de estado interno gerencia a coleta de eventos de segurança e os envia para o Hub IoT do Azure. Apenas uma instância de NX_AZURE_IOT_SECURITY_MODULE é necessária e é necessária para gerenciar a coleta de dados.

### <a name="parameters"></a>Parâmetros

| Nome | Descrição |
|---------|---------|
| nx_azure_iot_ptr [in]    | Um ponteiro para um `NX_AZURE_IOT` .  |

### <a name="return-values"></a>Valores retornados

|Valores retornados  |Descrição |
|---------|---------|
|NX_AZURE_IOT_SUCCESS|   Módulo de segurança de IoT do Azure habilitado com êxito.     |
|NX_AZURE_IOT_FAILURE   |  Falha ao habilitar o módulo de segurança de IoT do Azure devido a um erro interno.    |
|NX_AZURE_IOT_INVALID_PARAMETER   |  O módulo de segurança requer uma instância de #NX_AZURE_IOT válida.      |

### <a name="allowed-from"></a>Permitido de

Threads

## <a name="disable-azure-iot-security-module"></a>Desabilitar o módulo de segurança de IoT do Azure

**nx_azure_iot_security_module_disable**


### <a name="prototype"></a>Protótipo

```c
UINT nx_azure_iot_security_module_disable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Descrição

Essa rotina desabilita o subsistema do módulo de segurança do Azure IoT.

### <a name="parameters"></a>Parâmetros

| Nome | Descrição |
|---------|---------|
| nx_azure_iot_ptr [in]    | Um ponteiro para `NX_AZURE_IOT`. Se NULL, a instância singleton será desabilitada. |

### <a name="return-values"></a>Valores retornados

|Valores retornados  |Descrição |
|---------|---------|
|NX_AZURE_IOT_SUCCESS     |   Êxito quando o módulo de segurança do Azure IoT é desabilitado com êxito.      |
|NX_AZURE_IOT_INVALID_PARAMETER   |  A instância do Hub IoT do Azure é diferente da instância composta singleton.       |
|NX_AZURE_IOT_FAILURE    |  Falha ao desabilitar o módulo de segurança de IoT do Azure devido a um erro interno.       |

### <a name="allowed-from"></a>Permitido de

Threads


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como começar a usar o módulo de segurança dos RTOS do Azure, confira os seguintes artigos:

- Examine a [visão geral](iot-security-azure-rtos.md)do módulo de segurança do defender para IOT RTOs.