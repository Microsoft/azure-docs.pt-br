---
title: Visão Geral de Segurança do Azure Percept
description: Saiba mais sobre a segurança do Percept do Azure
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/24/2021
ms.custom: template-concept
ms.openlocfilehash: 93884fb87f87651054ffff0a04c4910de634a5eb
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567637"
---
# <a name="azure-percept-security-overview"></a>Visão Geral de Segurança do Azure Percept

Os dispositivos Percept do Azure são projetados com uma raiz de hardware de confiança. Essa segurança interna ajuda a proteger os dados de inferência e os sensores sensíveis à privacidade, como câmeras e microfones, e habilita a autenticação e a autorização de dispositivos para os serviços do Azure Percept Studio.

> [!NOTE]
> O Azure Percept DK é licenciado para uso somente em ambientes de desenvolvimento e teste.

## <a name="devices"></a>Dispositivos

### <a name="azure-percept-dk"></a>Azure Percept DK

O Azure Percept DK inclui um Trusted Platform Module (TPM) versão 2,0, que pode ser utilizado para conectar o dispositivo ao DPS (serviços de provisionamento de dispositivos) do Azure com segurança adicional. O TPM é um padrão ISO em todo o setor, da Trusted Computing Group. Confira o [site Trusted Computing Group](https://trustedcomputinggroup.org/resource/tpm-library-specification/) para obter mais informações sobre a especificação completa do TPM 2,0 ou a especificação ISO/IEC 11889. Para obter mais informações sobre como o DPS pode provisionar dispositivos de maneira segura, consulte [serviço de provisionamento de dispositivos no Hub IOT do Azure-atestado de TPM](../iot-dps/concepts-tpm-attestation.md).

### <a name="azure-percept-system-on-modules-soms"></a>Sistema Percept do Azure-no-modules (SoMs)

O sistema do Azure Percept Vision-on-Module (SoM) e o SoM de áudio do Azure Percept incluem uma MCU (unidade de microcontrolador) para proteger o acesso aos sensores de ia inseridos. A cada inicialização, o firmware de MCU autentica e autoriza o acelerador de ia com os serviços do Azure Percept Studio usando a arquitetura do mecanismo de composição do identificador de dispositivo (serviços). Os trabalhos funcionam dividindo a inicialização em camadas e criando um UDS (segredos de dispositivo exclusivo) para cada camada e configuração. Se um código ou configuração diferente for inicializado em qualquer ponto da cadeia, os segredos serão diferentes. Você pode ler mais sobre os na [especificação do grupo de trabalho](https://trustedcomputinggroup.org/work-groups/dice-architectures/)dos Para configurar o acesso ao Azure Percept Studio e aos serviços necessários, consulte o artigo sobre como [Configurar firewalls para o Azure PERCEPT DK](concept-security-configuration.md).

Os dispositivos Percept do Azure usam a raiz de hardware de confiança para proteger o firmware. A ROM de inicialização garante a integridade do firmware entre a ROM e o carregador do sistema operacional (SO), que, por sua vez, garante a integridade dos outros componentes de software, criando uma cadeia de confiança.

## <a name="services"></a>Serviços

### <a name="iot-edge"></a>IoT Edge

O Azure Percept DK se conecta ao Azure Percept Studio com segurança adicional e outros serviços do Azure utilizando o protocolo TLS. O Azure Percept DK é um dispositivo habilitado para Azure IoT Edge. IoT Edge tempo de execução é uma coleção de programas que transforma um dispositivo em um dispositivo IoT Edge. Coletivamente, os componentes de tempo de execução IoT Edge permitem que os dispositivos IoT Edge recebam código para serem executados na borda e comuniquem os resultados. O Azure Percept DK utiliza contêineres do Docker para isolar cargas de trabalho IoT Edge do sistema operacional do host e aplicativos habilitados para borda. Para obter mais informações sobre a estrutura de segurança do Azure IoT Edge, leia sobre o [Gerenciador de segurança do IOT Edge](../iot-edge/iot-edge-security-manager.md).

### <a name="device-update-for-iot-hub"></a>Documentação da Atualização de dispositivo para o Hub IoT

A atualização de dispositivo para o Hub IoT permite uma atualização mais segura, escalonável e confiável por satélite que traz a segurança renovável aos dispositivos Percept do Azure. Ele fornece controles de gerenciamento avançados e conformidade de atualização por meio de insights. O Azure Percept DK inclui uma solução de atualização de dispositivo integrada que fornece atualização resiliente (A/B) de firmware para camadas do sistema operacional.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre as configurações de firewall e recomendações de segurança](concept-security-configuration.md)

> [!div class="nextstepaction"]
> [Comprar um Azure Percept DK na Microsoft Online Store](https://go.microsoft.com/fwlink/p/?LinkId=2155270)
