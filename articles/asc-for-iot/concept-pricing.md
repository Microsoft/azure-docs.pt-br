---
title: Entender a Central de segurança do Azure para IoT custa a visualização | Microsoft Docs
description: Saiba mais sobre os custos associados com a Central de segurança do Azure para IoT e como controlá-los.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: dd041cdb1608eab60fa2a5fa756f381656a13a46
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618446"
---
# <a name="pricing-and-associated-costs"></a>Preços e custos associados

> [!IMPORTANT]
> A Central de Segurança do Azure para IoT está em versão prévia pública no momento.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo explica a Central de segurança do Azure (ASC) para o modelo de preços de IoT, resume todos os custos associados e explica como gerenciá-los.

## <a name="pricing"></a>Preços

O ASC para IoT, modelo de preços é composto de duas partes e é cobrada depois que um IoT Hub estiver [habilitado](quickstart-onboard-iot-hub.md) no ASC para IoT:

- Custo por dispositivo - recursos de segurança internos com base na análise de logs do IoT Hub.

- Custo por mensagem - com base em mensagens de segurança de dispositivos do IoT Edge ou folha de recursos de segurança avançada.

  >[!Note]
  > Mensagens de segurança também incorrerá em consumo de cota no IoT Hub.

Para obter mais informações, consulte [preços da Central de segurança](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="associated-costs"></a>Custos associados

ASC para IoT tem dois tipos de custos associados, que não fazem parte dos preços direto:

- Consumo de cota do IoT Hub

- Os custos de armazenamento de análise de log

Você pode reduzir os custos associados ao recusar certos recursos, ao alterar suas configurações.

Para alterar suas configurações:

1. Abra o Hub IoT.

2. Sob **segurança**, clique em **visão geral**.

3. Clique em **Configurações**.

A tabela a seguir fornece um resumo dos custos associados e as implicações de cada opção.

|     | Uso | Comentário |
| --- | --- | --- |
| **Consumo de cota do IoT Hub** |  |
| [Exportar dispositivos](https://docs.microsoft.com/azure/iot-hub/iot-hub-bulk-identity-mgmt#export-devices) trabalho (exportação twin) | Uma vez por dia | Desabilitar _coleção de metadados de gêmeos_ |
| **Armazenamento de análise de log** |  |
| Alertas e a recomendação de dispositivo| Recomendação de segurança e alertas gerados pelo serviço | Não opcional |
| Dados brutos de segurança| Dados de segurança bruto de dispositivos de IoT, coletados pelos agentes de segurança | Desabilitar _armazenar eventos de segurança de dispositivo não processado_ |

>[!Important]
> Recusar tem graves implicações para os recursos de segurança disponíveis.
  
| Recusar | Implicações |
| --- | --- |
| _Coleção de metadados do gêmeo_ | Desabilitar [alertas personalizados](quickstart-create-custom-alerts.md) |
| | Desativar as recomendações de manifesto do IoT Edge |
| | Desabilitar alertas e recomendações com base em identidade do dispositivo |
| _Eventos de segurança de dispositivo bruto Store_ | Detalhes sobre recomendações de linha de base do sistema operacional de dispositivo não estão disponíveis |
| | Obter detalhes sobre [alerta](concept-security-alerts.md) e [recomendação](concept-recommendations.md) investigações não estão disponíveis |


## <a name="see-also"></a>Consulte também

- Acesso seu [dados brutos de segurança](how-to-security-data-access.md)
- [Investigar um dispositivo](how-to-investigate-device.md)
- Compreender e explorar [recomendações de segurança](concept-recommendations.md)
- Compreender e explorar [alertas de segurança](concept-security-alerts.md)
