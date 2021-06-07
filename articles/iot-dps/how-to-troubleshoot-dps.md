---
title: Diagnosticar e solucionar problemas de desconexões com o DPS no Hub IoT do Azure
description: Aprenda a diagnosticar e solucionar erros comuns com a conectividade de dispositivos do DPS (Serviço de Provisionamento de Dispositivos) no Hub IoT do Azure
author: xujing-ms
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/14/2021
ms.author: xujing
ms.openlocfilehash: fd7d4407f7ea552409599946d1c13a7e9d1d7268
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106062532"
---
# <a name="troubleshooting-with-azure-iot-hub-device-provisioning-service"></a>Solução de problemas do Serviço de Provisionamento de Dispositivos no Hub IoT do Azure

Os problemas de conectividade de dispositivos IoT podem ser difíceis de serem solucionados, porque há muitos pontos possíveis de falhas, como falhas de atestado, de registro etc. Este artigo fornece diretrizes sobre como detectar e solucionar problemas de conectividade do dispositivo por meio do [Azure Monitor](../azure-monitor/overview.md).

## <a name="using-azure-monitor-to-view-metrics-and-set-up-alerts"></a>Como usar o Azure Monitor para ver métricas e configurar alertas

O procedimento a seguir descreve como ver e configurar alertas na métrica do Serviço de Provisionamento de Dispositivos no Hub IoT. 

1. Entre no [portal do Azure](https://portal.azure.com).

2. Procure o Serviço de Provisionamento de Dispositivos no Hub IoT.

3. Selecione **Métricas**.

4. Selecione a métrica desejada. 
   <br />Atualmente, há três métricas no DPS:

    | Nome da métrica | Descrição |
    |-------|------------|
    | Tentativas de atestado | Número de dispositivos que tentaram se autenticar no Serviço de Provisionamento de Dispositivos|
    | Tentativas de Registro | Número de dispositivos que tentaram se registrar no Hub IoT após a autenticação bem-sucedida|
    | Dispositivo atribuído | Número de dispositivos que foram atribuídos com êxito ao Hub IoT|

5. Selecione o método de agregação desejado para criar uma exibição visual da métrica. 

6. Para configurar um alerta de uma métrica, selecione **Novas regras de alerta** no canto superior direito da folha da métrica. Da mesma forma, acesse a folha **Alerta** e selecione **Novas regras de alerta**.

7. Selecione **Adicionar condição** e escolha a métrica e o limite desejados seguindo os avisos.

Para saber mais, confira [Alertas do Azure Monitor](../azure-monitor/alerts/alerts-overview.md).

## <a name="using-log-analytic-to-view-and-resolve-errors"></a>Como usar a Análise de Log para ver e resolver erros

1. Entre no [portal do Azure](https://portal.azure.com).

2. Navegue até seu hub IoT.

3. Selecionar **configurações de Diagnóstico**.

4. Selecione **Ativar diagnóstico**.

5. Habilite os logs que deseja coletar.

    | Nome do log | Descrição |
    |-------|------------|
    | DeviceOperations | Logs relacionados aos eventos de conexão do dispositivo |
    | ServiceOperations | Logs de eventos relacionados ao uso do SDK do serviço (por exemplo, criação ou atualização de grupos de registro)|

6. Ative a opção **Enviar para o Log Analytics** ([confira os preços](https://azure.microsoft.com/pricing/details/log-analytics/)). 

7. Acesse a guia **Logs** no portal do Azure no recurso do Serviço de Provisionamento de Dispositivos.

8. Clique em **Executar** para ver os eventos recentes.

9. Se houver resultados, procure `OperationName`, `ResultType`, `ResultSignature` e `ResultDescription` (mensagem de erro) para obter mais detalhes sobre o erro.


## <a name="common-error-codes"></a>Códigos de erro comuns
Use esta tabela para entender e resolver erros comuns.

| Código do Erro| Descrição | Código de status HTTP |
|-------|------------|------------|
| 400 | O corpo da solicitação não é válido. Por exemplo, ele não pode ser analisado ou o objeto não pode ser validado.| 400 Formato inválido |
| 401 | O token de autorização não pode ser validado. Por exemplo, ele expirou ou não se aplica ao URI da solicitação. Esse código de erro também é retornado para os dispositivos como parte do fluxo de atestado do TPM. | 401 Não Autorizado|
| 404 | A instância do Serviço de Provisionamento de Dispositivos ou um recurso (por exemplo, um registro) não existe. |404 Não Encontrado |
| 412 | A Etag da solicitação não corresponde à Etag do recurso existente, de acordo com o RFC7232. | 412 Falha na pré-condição |
| 429 | As operações estão sendo limitadas pelo serviço. Para obter os limites de serviço específicos, confira [Limites do Serviço de Provisionamento de Dispositivos no Hub IoT](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-device-provisioning-service-limits). | 429 Número excessivo de solicitações |
| 500 | Ocorreu um erro interno. | Erro interno de servidor 500|