---
title: Opções do Microsoft Azure IoT | Microsoft Docs
description: Escolha como implementar sua solução IoT do Azure usando o Azure IoT Central, os aceleradores de solução do IoT ou o Hub IoT.
author: dominicbetts
ms.author: dobett
ms.date: 06/09/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 19ec7afeb71f0e9d5602f1c4ba1a2456162cdfae
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077398"
---
# <a name="compare-azure-iot-central-and-azure-iot-options"></a>Comparar as opções do Azure IoT Central e do Azure IoT

O Microsoft Azure IoT Central e o Azure IoT oferecem várias opções para a criação de uma solução de IoT. Essas opções são adequadas para diferentes conjuntos de requisitos do cliente:

* O [Azure IoT Central](overview-iot-central.md) é uma solução SaaS (software como serviço) que usa uma abordagem baseada em modelo para ajudá-lo a criar soluções de IoT de nível empresarial sem a necessidade de experiência em desenvolvimento de soluções de nuvem.

* Os [aceleradores de solução do Azure IoT](https://docs.microsoft.com/azure/iot-accelerators/) é uma coleção de nível empresarial de [aceleradores de solução](../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md) criada no PaaS (plataforma como serviço) do Azure que ajuda você a acelerar o desenvolvimento de soluções personalizadas de IoT.

## <a name="azure-iot-hub"></a>Hub IoT do Azure

O Hub IoT do Azure é a principal PaaS do Azure usada pela Azure IoT Central e pelos aceleradores de solução do Azure IoT. O Hub IoT dá suporte à comunicação bidirecional confiável e segura entre milhões de dispositivos IoT e uma solução de nuvem. O Hub IoT ajuda você a resolver desafios de implementação de IoT como:

* Conectividade e gerenciamento de dispositivos de alto volume
* Ingestão de telemetria de alto volume
* Comando e controle de dispositivos
* Imposição de segurança do dispositivo

## <a name="compare-azure-iot-central-and-azure-iot-solution-accelerators"></a>Comparar os aceleradores de solução do Azure IoT e o Azure IoT Central

Escolher seu produto do Azure IoT é uma parte essencial do planejamento da sua solução de IoT. O Hub IoT é um serviço individual do Azure que, por si só, não fornece uma solução de IoT de ponta a ponta. Você pode usar o Hub IoT como ponto de partida para qualquer solução de IoT. Você também não precisa usar aceleradores de solução do Azure IoT nem o Azure IoT Central para usar o Hub IoT. Os aceleradores de solução do IoT e o Azure IoT Central usam o Hub IoT junto com outros serviços do Azure.

A tabela a seguir resume as principais diferenças entre os aceleradores de solução do IoT e o Azure IoT Central, para ajudá-lo a escolher a opção correta para seus requisitos:

|     | Azure IoT Central | Aceleradores de solução de IoT do Azure |
| --- | ----------- | --------- |
| Uso principal                      | Para acelerar o tempo para colocação no mercado de soluções de IoT simples que não exigem personalização profunda de serviço.                                                    | Para acelerar o desenvolvimento de uma solução de IoT personalizada que precisa obter a máxima flexibilidade.                                                                                                                             |
| Acesso a serviços PaaS subjacentes | SaaS. Como ele é uma solução totalmente gerenciada, os serviços subjacentes não são expostos.                                                                                            | Você tem acesso aos serviços do Azure subjacentes para gerenciá-los ou substituí-los conforme necessário.                                                                                                                    |
| Flexibilidade                        | Média. Você pode usar a experiência do usuário baseada no navegador interno para personalizar o modelo da solução e os aspectos da interface do usuário. A infraestrutura não é personalizável porque os diferentes componentes não são expostos. | Alta. O código para os microsserviços é software livre e pode ser modificado da maneira que você desejar. Além disso, é possível personalizar a infraestrutura de implantação.                                               |
| Nível de habilidade                        | Baixa. Você precisa de habilidades de modelagem para personalizar a solução. Nenhuma habilidade de codificação é necessária.                                                                          | Médio-alto. Você precisa ter habilidades em Java ou .NET para personalizar o back-end da solução. Você precisa de habilidades no JavaScript para personalizar a visualização.                                                                       |
| Experiência de introdução             | Os modelos de aplicativo e de dispositivo fornecem modelos predefinidos. Podem ser implantados em minutos.                                                                                                  | Soluções pré-configuradas implementam cenários comuns de IoT. Podem ser implantados em minutos.                                                                                                                            |
| Preços                            | Estrutura de preços simples e previsíveis.                                                                                                                           | Você pode ajustar os serviços para controlar o custo.                                                                                                                                                            |

O produto a ser usado para criar sua solução de IoT depende do seguinte:

* Seus requisitos de negócios.
* O tipo de solução que você deseja criar.
* O conjunto de habilidades de sua organização para criar e manter a solução no longo prazo.

## <a name="next-steps"></a>Próximas etapas

Com base no produto escolhido e na abordagem, as próximas etapas sugeridas são:

* **Azure IoT Central**: [O que é Azure IoT Central?](overview-iot-central.md)
* **Aceleradores de solução IoT**: [O que são aceleradores de solução do Azure IoT?](../iot-accelerators/iot-accelerators-what-are-solution-accelerators.md)
* **Hub IoT**: [O que é o Hub IoT do Azure?](https://docs.microsoft.com/azure/iot-hub/iot-hub-what-is-iot-hub)