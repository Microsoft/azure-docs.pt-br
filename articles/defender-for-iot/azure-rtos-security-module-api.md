---
title: Defender-IoT-micro-Agent para a API de RTOS do Azure
description: API de referência para o defender-IoT-micro-Agent para RTOS do Azure.
ms.topic: reference
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: e7000a7e6d8ba332432f1ececa12bd9543e9e4a7
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779385"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-api-preview"></a>Defender-IoT-micro-Agent para a API dos RTOS do Azure (versão prévia)

Essa API destina-se ao uso somente com o defender-IoT-micro-Agent para os RTOS do Azure. Para obter recursos adicionais, consulte o [recurso do GitHub do defender-IOT-micro-Agent para Azure RTOs](https://github.com/azure-rtos/azure-iot-preview/releases). 

## <a name="enable-defender-iot-micro-agent-for-azure-rtos"></a>Habilitar o defender-IoT-micro-Agent para RTOS do Azure

**nx_azure_iot_security_module_enable**

### <a name="prototype"></a>Protótipo

```c
UINT nx_azure_iot_security_module_enable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Descrição

Essa rotina habilita o subsistema Azure IoT defender-IoT-micro-Agent. Um computador de estado interno gerencia a coleta de eventos de segurança e os envia para o Hub IoT do Azure. Apenas uma instância de NX_AZURE_IOT_SECURITY_MODULE é necessária e é necessária para gerenciar a coleta de dados.

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

## <a name="disable-azure-iot-defender-iot-micro-agent"></a>Desabilitar o Azure IoT defender-IoT-micro-Agent

**nx_azure_iot_security_module_disable**


### <a name="prototype"></a>Protótipo

```c
UINT nx_azure_iot_security_module_disable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>Descrição

Essa rotina desabilita o subsistema Azure IoT defender-IoT-micro-Agent.

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

Para saber mais sobre como começar a usar o Azure RTOS defender-IoT-micro-Agent, confira os seguintes artigos:

- Examine a [visão geral](iot-security-azure-rtos.md)do defender para IOT RTOs defender-IOT-micro-Agent.
