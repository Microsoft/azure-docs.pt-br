---
title: Linhas de base e verificações personalizadas
description: Saiba mais sobre o conceito de Azure defender para a linha de base do IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2019
ms.author: mlottner
ms.openlocfilehash: 04fe87cd69efc4c064b8fbdc596a5f9e187abbb1
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102233"
---
# <a name="azure-defender-for-iot-baseline-and-custom-checks"></a>Azure defender para IoT Baseline e verificações personalizadas

Este artigo explica o defender para a linha de base IoT e resume todas as propriedades associadas de verificações personalizadas de linha de base.

## <a name="baseline"></a>Linha de base

Uma linha de base estabelece o comportamento padrão para cada dispositivo e torna mais fácil estabelecer comportamento incomum ou desvio de normas esperadas.

## <a name="baseline-custom-checks"></a>Verificações personalizadas de linha de base

As verificações personalizadas de linha de base estabelecem uma lista personalizada de verificações para cada linha de base de dispositivo usando a **identidade do módulo** "/" do dispositivo.

## <a name="setting-baseline-properties"></a>Definindo propriedades de linha de base

1. No Hub IoT, localize e selecione o dispositivo que você deseja alterar.

1. Clique no dispositivo e, em seguida, clique no módulo **azureiotsecurity** .

1. Clique em **identidade do módulo**.

1. Carregue o arquivo de **verificações personalizadas de linha de base** no dispositivo.

1. Adicione Propriedades de linha de base ao módulo de segurança e clique em **salvar**.

### <a name="baseline-custom-check-file-example"></a>Exemplo de arquivo de verificação personalizada de linha de base

Para configurar verificações personalizadas de linha de base:

   ```json
    "desired": {
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
        "baselineCustomChecksEnabled": {
          "value" : true
        },
        "baselineCustomChecksFilePath": {
          "value" : "/home/user/full_path.xml"
        },
        "baselineCustomChecksFileHash": {
          "value" : "#hashexample!"
        }
      }
    },
   ```

## <a name="baseline-custom-check-properties"></a>Propriedades da verificação personalizada de linha de base

| Name| Status | Valores válidos| Valores padrão| Descrição |
|------|-----|------|-----|-----|
|baselineCustomChecksEnabled|Necessário: verdadeiro |Valores válidos: **booliano** |Valor padrão: **false** |Intervalo de tempo máximo antes que as mensagens de alta prioridade sejam enviadas.|
|baselineCustomChecksFilePath |Necessário: verdadeiro|Valores válidos: **cadeia de caracteres**, **nulo** |Valor padrão: **nulo** |Caminho completo da configuração XML de linha de base|
|baselineCustomChecksFileHash |Necessário: verdadeiro|Valores válidos: **cadeia de caracteres**, **nulo** |Valor padrão: **nulo** |`sha256sum` do arquivo de configuração XML. Use a [referência sha256sum](https://linux.die.net/man/1/sha256sum) para obter informações adicionais. |

Para examinar exemplos de linha de base adicionais, consulte [exemplo de linha de base personalizado-1](https://ascforiot.blob.core.windows.net/public/custom_baseline_example_hyperv_ubuntu1804.xml) e [exemplo de linha de base personalizado-2](https://ascforiot.blob.core.windows.net/public/oms_audits.xml).

## <a name="next-steps"></a>Próximas etapas

- Acessar seus [dados brutos de segurança](how-to-security-data-access.md)
- [Investigar um dispositivo](how-to-investigate-device.md)
- Entender e explorar as [recomendações de segurança](concept-recommendations.md)
- Entender e explorar [alertas de segurança](concept-security-alerts.md)
