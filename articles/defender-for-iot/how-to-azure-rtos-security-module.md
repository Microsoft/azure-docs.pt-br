---
title: Configurar e personalizar o defender-IoT-micro-Agent para RTOS do Azure
description: Saiba mais sobre como configurar e personalizar seu defender-IoT-micro-Agent para os RTOS do Azure.
ms.topic: how-to
ms.date: 03/07/2021
ms.openlocfilehash: afab823b6bb187c9a7b7529f52efc37b20e8c66f
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778977"
---
# <a name="configure-and-customize-defender-iot-micro-agent-for-azure-rtos-preview"></a>Configurar e personalizar o defender-IoT-micro-Agent para RTOS do Azure (versão prévia)

Este artigo descreve como configurar o defender-IoT-micro-Agent para seu dispositivo RTOS do Azure, para atender aos requisitos de rede, largura de banda e memória.

Você deve selecionar um arquivo de distribuição de destino que tenha uma `*.dist` extensão, no `netxduo/addons/azure_iot/azure_iot_security_module/configs` diretório.  

Ao usar um ambiente de compilação CMake, você deve definir um parâmetro de linha de comando para `IOT_SECURITY_MODULE_DIST_TARGET` para o valor escolhido. Por exemplo, `-DIOT_SECURITY_MODULE_DIST_TARGET=RTOS_BASE`.

Em um IAR ou outro ambiente de compilação não CMake, você deve adicionar o `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/<target distribution>/` caminho a qualquer caminho incluído conhecido. Por exemplo, `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/RTOS_BASE`.

Use o arquivo a seguir para configurar o comportamento do dispositivo.

**netxduo/Complementos/azure_iot/azure_iot_security_module/Inc/configs/ \<target distribution> /asc_config. h**

Em um ambiente de compilação CMake, você deve alterar a configuração padrão editando o `netxduo/addons/azure_iot/azure_iot_security_module/configs/<target distribution>.dist` arquivo. Use o seguinte formato CMake `set(ASC_XXX ON)` ou o arquivo a seguir `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/<target distribution>/asc_config.h` para todos os outros ambientes. Por exemplo, `#define ASC_XXX`.

O comportamento padrão de cada configuração é fornecido nas seguintes tabelas: 

## <a name="general"></a>Geral

| Nome | Type | Padrão | Detalhes |
| - | - | - | - |
| ASC_SECURITY_MODULE_ID | String | defender-IOT-micro-Agent | O identificador exclusivo do dispositivo.  |
| SECURITY_MODULE_VERSION_ (PRINCIPAL) (SECUNDÁRIA) (PATCH)  | Número | 3.2.1 | A versão. |
| ASC_SECURITY_MODULE_SEND_MESSAGE_RETRY_TIME  | Número  | 3 | A quantidade de tempo que o defender-IoT-micro-Agent executará para enviar a mensagem de segurança após uma falha. (em segundos) |
| ASC_SECURITY_MODULE_PENDING_TIME  | Número | 300 | O tempo pendente do defender-IoT-micro-Agent (em segundos). O estado será alterado para suspender, se o tempo for excedido. |

## <a name="collection"></a>Coleção

| Nome | Type | Padrão | Detalhes |
| - | - | - | - |
| ASC_FIRST_COLLECTION_INTERVAL | Número  | 30  | O deslocamento do intervalo de coleta de inicialização do coletor. Durante a inicialização, o valor será adicionado à coleção do sistema para evitar o envio de mensagens de vários dispositivos simultaneamente.  |
| ASC_HIGH_PRIORITY_INTERVAL | Número | 10 | O intervalo do grupo de alta prioridade do coletor (em segundos). |
| ASC_MEDIUM_PRIORITY_INTERVAL | Número | 30 | O intervalo do grupo de prioridade média do coletor (em segundos). |
| ASC_LOW_PRIORITY_INTERVAL | Número | 145.440  | O intervalo do grupo de baixa prioridade do coletor (em segundos). |

#### <a name="collector-network-activity"></a>Atividade de rede do coletor

Para personalizar a configuração de atividade de rede do coletor, use o seguinte:

| Nome | Type | Padrão | Detalhes |
| - | - | - | - |
| ASC_COLLECTOR_NETWORK_ACTIVITY_TCP_DISABLED | Boolean | false | Filtra a `TCP` atividade de rede. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_UDP_DISABLED | Boolean | false | Filtra os `UDP` eventos de atividade de rede. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ICMP_DISABLED | Boolean | false | Filtra os `ICMP` eventos de atividade de rede. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_CAPTURE_UNICAST_ONLY | Boolean | true | Captura somente os pacotes de entrada unicast. Quando definido como false, ele também capturará a difusão e o multicast. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_SEND_EMPTY_EVENTS  | Boolean  | false  | Envia eventos vazios do coletor. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV4_OBJECTS_IN_CACHE | Número | 64 | O número máximo de eventos de rede IPv4 a serem armazenados na memória. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV6_OBJECTS_IN_CACHE | Número | 64  | O número máximo de eventos de rede IPv6 a serem armazenados na memória. |

### <a name="collectors"></a>Coletores
| Nome | Type | Padrão | Detalhes |
| - | - | - | - |
| ASC_COLLECTOR_HEARTBEAT_ENABLED | Boolean | ATIVADO | Habilita o coletor de pulsação. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ENABLED  | Boolean | ATIVADO | Habilita o coletor de atividade de rede. |
| ASC_COLLECTOR_SYSTEM_INFORMATION_ENABLED | Boolean | ATIVADO | Habilita o coletor de informações do sistema.  |

Outros sinalizadores de configuração são avançados e têm recursos sem suporte. Contate o suporte para alterar isso ou para obter mais informações.
 
## <a name="supported-security-alerts-and-recommendations"></a>Recomendações e alertas de segurança com suporte

O defender-IoT-micro-Agent para RTOS do Azure dá suporte a alertas e recomendações de segurança específicos. Certifique-se de [examinar e personalizar os valores relevantes de alerta e recomendação](concept-rtos-security-alerts-recommendations.md) para seu serviço.

## <a name="log-analytics-optional"></a>Log Analytics (opcional)

Você pode habilitar e configurar Log Analytics para investigar eventos e atividades do dispositivo. Leia sobre como configurar e use [log Analytics com o serviço defender para IOT](how-to-security-data-access.md#log-analytics) para saber mais. 

## <a name="next-steps"></a>Próximas etapas


- Examinar e personalizar o defender-IoT-micro-Agent para [alertas e recomendações de segurança](concept-rtos-security-alerts-recommendations.md) dos RTOs do Azure
- Consulte o [defender-IOT-micro-Agent para a API de RTOs do Azure](azure-rtos-security-module-api.md) , conforme necessário.
