---
title: Recomendações de segurança IOT do Azure | Microsoft Docs
description: Este artigo resume as etapas adicionais para garantir a segurança em sua solução do IoT Hub do Azure.
author: dsk-2015
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dkshir
ms.custom: security-recommendations
ms.openlocfilehash: 4416f3149c33a0c9a437b2fbd6a48729a5a7f044
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722864"
---
# <a name="security-recommendations-for-azure-internet-of-things-iot-deployment"></a>Recomendações de segurança para implantação do Azure da Internet das coisas (IoT)

Este artigo contém recomendações de segurança para os serviços de IoT do Azure. Implementando essas recomendações ajuda a cumprir suas obrigações de segurança como um cliente de IoT do Azure e será melhorará a segurança geral para suas soluções de IoT. Para obter mais informações sobre os recursos de segurança intrínseco fornecidos pelo IoT do Azure, leia [segurança de IoT desde o início](iot-security-ground-up.md).

## <a name="general"></a>Geral

| Recomendações | Comentários |
|-|-|
| Mantenha-se atualizado | Use as versões mais recentes de plataformas com suporte, estruturas, protocolos e linguagens de programação. |
| Proteger as chaves de autenticação | Mantenha as IDs de dispositivo e suas chaves de autenticação fisicamente seguro após a implantação. Isso evitará uma máscara de dispositivo mal-intencionados como um dispositivo registrado. |
| Use os SDKs do dispositivo quando possível | SDKs do dispositivo implementam uma variedade de recursos de segurança, como criptografia, autenticação e assim por diante, para ajudar a desenvolver um aplicativo de dispositivo robustos e seguros. Ver [compreender e usar SDKs do Hub IoT do Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) para obter mais informações. |


## <a name="identity-and-access-management"></a>Gerenciamento de identidade e de acesso

| Recomendações | Comentários |
|-|-|
| Definir controle de acesso para o hub | [Compreender e definir o tipo de acesso](iot-security-deployment.md#securing-the-cloud) cada componente terá em sua solução do IoT Hub, com base na funcionalidade. As permissões permitidas são *leitura de registro*, *RegistryReadWrite*, *ServiceConnect*, e *DeviceConnect*. Padrão [políticas de acesso em seu hub IoT compartilhado](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#access-control-and-permissions) também pode ajudar a definir as permissões para cada componente com base em sua função. |
| Definir controle de acesso para os serviços de back-end | Dados ingeridos por sua solução do IoT Hub podem ser consumidos por outros serviços do Azure, como [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/), [Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/), [serviço de aplicativo](https://docs.microsoft.com/azure/app-service/), [aplicativos lógicos](https://docs.microsoft.com/azure/logic-apps/), e [armazenamento de BLOBs](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction). Certifique-se de entender e permitir que as permissões de acesso apropriados, conforme documentado para esses serviços. |


## <a name="data-protection"></a>Proteção de dados

| Recomendações | Comentários |
|-|-|
| Autenticação segura de dispositivos | Certifique-se de uma comunicação segura entre os dispositivos e hub IoT, usando o [uma chave de identidade exclusiva ou segurança token](iot-security-deployment.md#iot-hub-security-tokens), ou [um certificado X.509 no dispositivo](iot-security-deployment.md#x509-certificate-based-device-authentication) para cada dispositivo. Use o método apropriado para [usar tokens de segurança baseados no protocolo escolhido (MQTT, AMQP ou HTTPS)](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security). |
| Proteger a comunicação do dispositivo | O IoT Hub protege a conexão para os dispositivos usando o padrão de segurança de camada de transporte (TLS), que dão suporte a versões 1.0, 1.2 e 1.2. Use [TLS 1.2](https://tools.ietf.org/html/rfc5246) para garantir a segurança máxima. |
| Proteger comunicações de serviço | O IoT Hub fornece pontos de extremidade para se conectar aos serviços de back-end, como [armazenamento do Azure](/azure/storage/) ou [Hubs de eventos](/azure/event-hubs) usando apenas o protocolo TLS e nenhum ponto de extremidade é exposto em um canal não criptografado. Depois que esses dados atingirem esses serviços de back-end para armazenamento ou análise, certifique-se empregar os métodos apropriados de segurança e criptografia para o serviço e proteger informações confidenciais no back-end. |


## <a name="networking"></a>Rede

| Recomendações | Comentários |
|-|-|
| Proteger o acesso a seus dispositivos | Manter hardware portas em seus dispositivos ao mínimo necessário para evitar o acesso indesejado. Além disso, crie mecanismos para evitar ou detectar violação física do dispositivo. Leia [práticas recomendadas de segurança de IoT](iot-security-best-practices.md) para obter detalhes. |
| Criar o hardware seguro | Incorpore recursos de segurança, como armazenamento criptografado ou Trusted Platform Module (TPM), para manter os dispositivos e a infraestrutura mais segura. Manter o sistema operacional do dispositivo e os drivers atualizados para versões mais recentes e, se houver espaço, instale as funcionalidades antivírus e antimalware. Leia [arquitetura de segurança IoT](iot-security-architecture.md) para entender como isso pode ajudar a atenuar várias ameaças de segurança. |


## <a name="monitoring"></a>Monitoramento

| Recomendações | Comentários |
|-|-|
| Monitorar o acesso não autorizado aos seus dispositivos |  Use o recurso de registro de dispositivo do seu sistema operacional para monitorar quaisquer violações de segurança ou violação física do dispositivo ou suas portas. |
| Monitorar sua solução de IoT da nuvem | Monitorar a integridade geral da sua solução do IoT Hub usando o [métricas no Azure Monitor](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics). |
| Configurar o diagnóstico | Inspecione de perto as operações de log de eventos em sua solução e, em seguida, enviando os logs de diagnóstico para o Azure Monitor para obter visibilidade sobre o desempenho. Leia [monitorar e diagnosticar problemas em seu hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health) para obter mais informações. |

## <a name="next-steps"></a>Próximas etapas

Para cenários avançados que envolvem a IoT do Azure, você precisa considerar os requisitos de segurança adicionais. Ver [arquitetura de segurança IoT](iot-security-architecture.md) para obter mais diretrizes.

