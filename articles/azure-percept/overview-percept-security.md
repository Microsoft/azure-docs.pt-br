---
title: Visão Geral de Segurança do Azure Percept
description: Saiba mais sobre a segurança do Percept do Azure
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 6a3049709c6c094f722a8132ee4c4b2051e24d95
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102616682"
---
# <a name="azure-percept-security-overview"></a>Visão Geral de Segurança do Azure Percept

Os dispositivos do Azure Percept DK são projetados com uma raiz de hardware de confiança: segurança interna adicional em cada dispositivo. Ele ajuda a proteger os sensores sensíveis à privacidade, como câmeras e microfones, dados de inferência e habilita a autenticação e autorização do dispositivo para os serviços do Azure Percept Studio.

> [!NOTE]
> O Azure Percept DK é licenciado para uso somente em ambientes de desenvolvimento e teste.

## <a name="devices"></a>Dispositivos

### <a name="azure-percept-dk"></a>Azure Percept DK

O Azure Percept DK inclui um Trusted Platform Module (TPM) versão 2,0 que pode ser utilizado para conectar o dispositivo aos serviços de provisionamento de dispositivos do Azure com segurança adicional. O TPM é um padrão ISO, de todo o setor, da Trusted Computing Group, e você pode ler mais sobre o TPM na [especificação completa do tpm 2,0](https://trustedcomputinggroup.org/resource/tpm-library-specification/) ou a especificação ISO/IEC 11889. Para obter mais informações sobre como o DPS pode provisionar dispositivos de maneira segura [, consulte serviço de provisionamento de dispositivos no Hub IOT do Azure-atestado de TPM](https://docs.microsoft.com/azure/iot-dps/concepts-tpm-attestation).

### <a name="azure-percept-system-on-module-som"></a>Sistema de Percept do Azure no módulo (SOM)

O sistema habilitado para visão do Azure Percept DK no módulo (SOM) e o SOM de acessório de áudio Percept do Azure incluem uma MCU (unidade micro Controller) para proteger o acesso aos sensores de ia inseridos. A cada inicialização, o firmware de MCU autentica e autoriza o acelerador de ia com os serviços do Azure Percept Studio usando a arquitetura do mecanismo de composição do identificador de dispositivo (serviços). Os trabalhos funcionam dividindo a inicialização em camadas e criando segredos exclusivos para cada camada e configuração com base em um UDS (segredo de dispositivo exclusivo). Se um código ou configuração diferente for inicializado, em qualquer ponto da cadeia, os segredos serão diferentes. Você pode ler mais sobre os na [especificação do grupo de trabalho](https://trustedcomputinggroup.org/work-groups/dice-architectures/)dos Para configurar o acesso ao Azure Percept Studio e aos serviços necessários, consulte o **Configurando firewalls para o Azure PERCEPT DK** abaixo.

Os dispositivos Percept do Azure usam a relação de confiança de raiz de hardware para proteger o firmware. A ROM de inicialização garante a integridade do firmware entre a ROM e o carregador do sistema operacional (SO) que, por sua vez, garante a integridade dos outros componentes de software que criam uma cadeia de confiança.

## <a name="services"></a>Serviços

### <a name="iot-edge"></a>IoT Edge

O Azure Percept DK se conecta ao Azure Percept Studio com segurança adicional e outros serviços do Azure utilizando o protocolo TLS. O Azure Percept DK é um dispositivo habilitado Azure IoT Edge. IoT Edge tempo de execução é uma coleção de programas que transforma um dispositivo em um dispositivo IoT Edge. Coletivamente, os componentes de tempo de execução IoT Edge permitem que os dispositivos IoT Edge recebam código para serem executados na borda e comuniquem os resultados. O Azure Percept DK utiliza contêineres do Docker para isolar cargas de trabalho IoT Edge do sistema operacional do host e aplicativos habilitados para borda. Para obter mais informações sobre a estrutura de segurança do Azure IoT Edge, leia sobre o [Gerenciador de segurança do IOT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-security-manager).

### <a name="device-update-for-iot-hub"></a>Documentação da Atualização de dispositivo para o Hub IoT

A atualização de dispositivo para o Hub IoT permite uma atualização mais segura, escalonável e confiável por satélite que traz a segurança renovável aos dispositivos Percept do Azure. Ele fornece controles de gerenciamento avançados e conformidade de atualização por meio de insights. O Azure Percept DK inclui uma solução de atualização de dispositivo integrada que fornece atualização resiliente (A/B) de firmware para camadas do sistema operacional.

<!---I think the below topics need to be somewhere else, (i.e. not on the main page)
--->

## <a name="configuring-firewalls-for-azure-percept-dk"></a>Configurando firewalls para o Azure Percept DK

Se sua configuração de rede exigir que você explicitamente permita conexões feitas de dispositivos do Azure Percept DK, examine a lista de componentes a seguir.

Esta lista de verificação é um ponto de partida para regras de firewall:

|URL (* = curinga) |Portas TCP de Saída|    Uso|
|-------------------|------------------|---------|
|*. auth.azureperceptdk.azure.net|   443|    Autenticação e autorização de SOM do Azure DK|
|*. auth.projectsantacruz.azure.net| 443|    Autenticação e autorização de SOM do Azure DK|

Além disso, examine a lista de [conexões usadas pelo Azure IOT Edge](https://docs.microsoft.com/azure/iot-edge/production-checklist#allow-connections-from-iot-edge-devices).

<!---
## Additional Recommendations for Deployment to Production

Azure Percept DK offers a great variety of security capabilities out of the box. In addition to those powerful security features included in the current release, Microsoft also suggests the following guidelines when considering production deployments:

- Strong physical protection of the device itself
- Ensuring data at rest encryption is enabled
- Continuously monitoring the device posture and quickly responding to alerts
- Limiting the number of administrators who have access to the device
--->


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os [modelos disponíveis do Azure PERCEPT ia](./overview-ai-models.md).
