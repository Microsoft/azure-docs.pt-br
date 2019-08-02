---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 104849557a8580e16fa1860b7919d1c0252debe9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172317"
---
A tabela a seguir lista os limites associados as diferentes camadas de serviço S1, S2, S3 e F1. Para obter informações sobre o custo de cada *unidade* em cada camada, consulte [preços do IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

| Resource | S1 Standard | S2 Standard | Padrão S3 | F1 Gratuito |
| --- | --- | --- | --- | --- |
| Mensagens/dia |400,000 |6\.000.000 |300.000.000 |8\.000 |
| Máximo de unidades |200 |200 |10 |1 |

> [!NOTE]
> Se você antecipar o uso de mais de 200 unidades com um hub da camada S1 ou S2 ou 10 unidades com um hub da camada S3, entre em contato com o Microsoft Support.
> 
> 

A tabela a seguir lista os limites que se aplicam aos recursos do IoT Hub.

| Resource | Limite |
| --- | --- |
| Máximo de hubs IoT por assinatura do Azure |50 |
| Máximo de hubs IoT por assinatura do Azure |1 |
| Número máximo de caracteres em uma ID de dispositivo | 128 |
| Número máximo de identidades de dispositivo<br/> retornadas em uma única chamada |1\.000 |
| Retenção máxima de mensagem do Hub IoT para mensagens do dispositivo para nuvem |7 dias |
| Tamanho máximo da mensagem do dispositivo para a nuvem |256 KB |
| Tamanho máximo do lote do dispositivo para a nuvem |AMQP e HTTP: 256 KB para todo o lote <br/>MQTT: 256 KB para cada mensagem |
| Máximo de mensagens no lote do dispositivo para a nuvem |500 |
| Tamanho máximo da mensagem da nuvem para o dispositivo |64 KB |
| TTL máximo para mensagens da nuvem para o dispositivo |2 dias |
| Contagem máxima de entrega para mensagens <br/> da nuvem para o dispositivo |100 |
| Contagem máxima de entrega de mensagens de comentários <br/> em resposta a uma mensagem da nuvem para o dispositivo |100 |
| TTL máximo de mensagens de comentários <br/> em resposta a uma mensagem da nuvem para o dispositivo |2 dias |
| [Tamanho máximo do dispositivo gêmeo](../articles/iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) <br/> (marcas, propriedades relatadas e propriedades desejadas) | 8 KB |
| Tamanho máximo do valor de cadeia de caracteres do dispositivo gêmeo | 4 KB |
| [Profundidade máxima de objeto no dispositivo gêmeo](../articles/iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format) | 5 |
| Tamanho máximo da carga do método direto | 128 KB |
| Máximo de retenção de histórico do trabalho | 30 dias |
| Máximo de trabalhos simultâneos | 10 (para S3), 5 para (S2), 1 (para S1) |
| Pontos de extremidade adicionais máximo | 10 (para S1, S2 e S3) |
| Máxima de regras de roteamento de mensagens | 100 (para S1, S2 e S3) |
| Número máximo de fluxos de dispositivos conectados simultaneamente | 50 (apenas para S1, S2, S3 e F1) |
| Transferência máxima de dados do fluxo do dispositivo | 300 MB por dia (apenas para S1, S2, S3 e F1) |

> [!NOTE]
> Se você precisar de mais de 50 hubs IoT pagos em uma assinatura do Azure, entre em contato com o Microsoft Support.

> [!NOTE]
> Atualmente, o número máximo de dispositivos que você pode conectar a um único hub IoT é de 1.000.000. Se você quiser aumentar esse limite, entre em contato com o [Suporte da Microsoft](https://azure.microsoft.com/support/options/).

O IoT Hub restringe as solicitações quando as seguintes cotas são excedidas.

| Restrição | Valor por hub |
| --- | --- |
| Operações de registro de identidade <br/> (criar, recuperar, listar, atualizar e excluir), <br/> importação/exportação em massa ou individual |83.33/SEC/Unit (5.000/min/unidade) (para S3). <br/> 1,67/s/unidade (100/min/unidade) (para S1 e S2). |
| Conexões do dispositivo |6\.000/s/unidade (para S3), 120/s/unidade (para S2), 12/s/unidade (para S1). <br/>Mínimo de 100/s. |
| Envios do dispositivo para a nuvem |6\.000/s/unidade (para S3), 120/s/unidade (para S2), 12/s/unidade (para S1). <br/>Mínimo de 100/s. |
| Envios da nuvem para o dispositivo | 83.33/SEC/Unit (5.000/min/unidade) (para S3), 1.67/sec/unit (100/min/unidade) (para S1 e S2). |
| Recebimentos da nuvem para o dispositivo |833.33/SEC/Unit (50.000/min/unidade) (para S3), 16.67/sec/unit (1.000/min/unidade) (para S1 e S2). |
| Operações de upload de arquivo |o carregamento de arquivos de 83,33 notificações/s/unidade (5.000/min/unidade) (para S3), arquivo 1,67 notificações de upload/s/unidade (100/min/unidade) (para S1 e S2). <br/> URIs de SAS de 10.000 podem estar fora de uma conta de armazenamento do Azure ao mesmo tempo.<br/> 10 URIs de SAS/dispositivo podem estar fora ao mesmo tempo. |
| Métodos diretos | 24 MB/s/unidade (para S3), 480 KB/s/unidade (para S2), 160 KB/s/unidade (para S1).<br/> Com base no tamanho de medidor de limitação de 8 KB. |
| Leituras de dispositivo gêmeo | 500/s/unidade (para S3), máximo de 100/s ou 10/s/unidade (para S2), 100/s (para S1) |
| Atualizações de dispositivos gêmeos | 250/s/unidade (para S3), máximo de 50/s ou 5/s/unidade (para S2), 50/s (para S1) |
| Operações de trabalhos <br/> (criar, atualizar, lista e excluir) | 83.33/SEC/Unit (5.000/min/unidade) (para S3), 1.67/sec/unit (100/min/unidade) (para S2), 1.67/sec/unit (100/min/unidade) (para S1). |
| Taxa de transferência de operação de trabalhos por dispositivo | máximo de 50/s/unidade (para S3), de 10/s ou 1/s/unidade (para S2), 10/s (para S1). |
| Taxa de inicialização do fluxo do dispositivo | 5 novos fluxos/s (para S1, S2, S3 e apenas F1). |
