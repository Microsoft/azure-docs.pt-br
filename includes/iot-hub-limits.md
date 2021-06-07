---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 007c19a10db5e000770c8c80189453d4a80edec2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "82204615"
---
A tabela a seguir listará os limites associados às diferentes camadas de serviço, como S1, S2, S3 e F1. Para obter informações sobre o custo de uma *unidade* em cada camada, confira [Preços do Hub IoT do Azure](https://azure.microsoft.com/pricing/details/iot-hub/).

| Recurso | S1 Standard | S2 Standard | Padrão S3 | F1 Gratuito |
| --- | --- | --- | --- | --- |
| Mensagens/dia |400.000 |6\.000.000 |300.000.000 |8,000 |
| Máximo de unidades |200 |200 |10 |1 |

> [!NOTE]
> Caso preveja usar mais de 200 unidades com um hub de camada S1 ou S2 ou 10 unidades com um hub de camada S3, entre em contato com o Suporte da Microsoft.
> 
> 

A tabela a seguir listará os limites que se aplicam aos recursos do Hub IoT.

| Recurso | Limite |
| --- | --- |
| Máximo de hubs IoT por assinatura do Azure |50 |
| Máximo de hubs IoT por assinatura do Azure |1 |
| Número máximo de caracteres em uma ID do dispositivo | 128 |
| Número máximo de identidades de dispositivo<br/>  retornadas em uma única chamada |1,000 |
| Retenção máxima de mensagem do Hub IoT para mensagens do dispositivo para nuvem |7 dias |
| Tamanho máximo da mensagem do dispositivo para a nuvem |256 KB |
| Tamanho máximo do lote do dispositivo para a nuvem |AMQP e HTTP: 256 KB para todo o lote <br/>MQTT: 256 KB para cada mensagem |
| Máximo de mensagens no lote do dispositivo para a nuvem |500 |
| Tamanho máximo da mensagem da nuvem para o dispositivo |64 KB |
| TTL máximo para mensagens da nuvem para o dispositivo |2 dias |
| Contagem máxima de entrega para mensagens  <br/> da nuvem para o dispositivo |100 |
| Profundidade máxima da fila da nuvem para dispositivo por dispositivo |50 |
| Contagem máxima de entrega de mensagens de comentários  <br/>  em resposta a uma mensagem da nuvem para o dispositivo |100 |
| TTL máximo de mensagens de comentários  <br/>  em resposta a uma mensagem da nuvem para o dispositivo |2 dias |
| [Tamanho máximo de dispositivo gêmeo](../articles/iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) | São 8 KB para a seção de marcas e 32 KB para cada seção de propriedades desejadas e relatadas |
| Comprimento máximo da chave da cadeia de caracteres do dispositivo gêmeo | 1 KB |
| Comprimento máximo do valor da cadeia de caracteres do dispositivo gêmeo | 4 KB |
| [Profundidade máxima de objeto em dispositivo gêmeo](../articles/iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format) | 10 |
| Tamanho máximo da carga do método direto | 128 KB |
| Máximo de retenção de histórico do trabalho | 30 dias |
| Máximo de trabalhos simultâneos | 10 (para S3), 5 para (S2), 1 (para S1) |
| Pontos de extremidade adicionais máximo | 10 (para S1, S2 e S3) |
| Máxima de regras de roteamento de mensagens | 100 (para S1, S2 e S3) |
| Número máximo de fluxos de dispositivos conectados simultaneamente | 50 (apenas para S1, S2, S3 e F1) |
| Transferência máxima de dados do fluxo do dispositivo | 300 MB por dia (apenas para S1, S2, S3 e F1) |

> [!NOTE]
> Caso precise de mais de 50 hubs IoT pagos em uma assinatura do Azure, entre em contato com o Suporte da Microsoft.

> [!NOTE]
> No momento, o número total de dispositivos mais módulos que podem ser registrados em um hub IoT é limitado a 1 milhão. Se você quiser aumentar esse limite, entre em contato com o [Suporte da Microsoft](https://azure.microsoft.com/support/options/).

O Hub IoT limita as solicitações quando as cotas a seguir forem excedidas.

| Restrição | Valor por hub |
| --- | --- |
| Operações de registro de identidade <br/> (criar, recuperar, listar, atualizar e excluir), <br/>  importação/exportação em massa ou individual |83,33/s/unidade (5.000/min/unidade) (para S3). <br/> 1,67/s/unidade (100/min/unidade) (para S1 e S2). |
| Conexões do dispositivo |6\.000/s/unidade (para S3), 120/s/unidade (para S2) e 12/s/unidade (para S1). <br/>Mínimo de 100/s. |
| Envios do dispositivo para a nuvem |6\.000/s/unidade (para S3), 120/s/unidade (para S2) e 12/s/unidade (para S1). <br/>Mínimo de 100/s. |
| Envios da nuvem para o dispositivo | 83,33/s/unidade (5.000/min/unidade) (para S3) e 1,67/s/unidade (100/min/unidade) (para S1 e S2). |
| Recebimentos da nuvem para o dispositivo |833,33/s/unidade (50.000/min/unidade) (para S3) e 16,67/s/unidade (1.000/min/unidade) (para S1 e S2). |
| Operações de upload de arquivo |83,33 iniciações de carregamento de arquivos/s/unidade (5.000/s/unidade) (para S3) e 1,67 iniciações de carregamento de arquivos/s/unidade (100/min/unidade) (para S1 e S2). <br/> 10.000 URIs de SAS podem estar fora de uma conta de Armazenamento do Azure ao mesmo tempo.<br/>  10 URIs de SAS/dispositivo podem estar fora ao mesmo tempo. |
| Métodos diretos | 24 MB/s/unidade (para S3), 480 KB/s/unidade (para S2) e 160 KB/s/unidade (para S1).<br/> Com base no tamanho de um medidor de limitação de 8 KB. |
| Leituras de dispositivo gêmeo | 500/s/unidade (para S3), máximo de 100/s ou 10/s/unidade (para S2) e 100/s (para S1) |
| Atualizações de dispositivos gêmeos | 250/s/unidade (para S3), máximo de 50/s ou 5/s/unidade (para S2) e 50/s (para S1) |
| Operações de trabalhos <br/> (criar, atualizar, listar e excluir) | 83,33/s/unidade (5.000/min/unidade) (para S3), 1,67/s/unidade (100/min/unidade) (para S2) e 1,67/s/unidade (100/min/unidade) (para S1). |
| Taxa de transferência de operação de trabalhos por dispositivo | 50/s/unidade (para S3), máximo de 10/s ou 1/s/unidade (para S2) e 10/s (para S1). |
| Taxa de inicialização do fluxo do dispositivo | Cinco novos fluxos/s (somente para S1, S2, S3 e F1). |
