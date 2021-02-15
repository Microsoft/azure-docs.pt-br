---
title: Navegação e histórico de versão IoT Edge-Azure IoT Edge
description: Descubra as novidades do IoT Edge com informações sobre novos recursos e funcionalidades nas versões mais recentes.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9db51fe9298b7f3329d35df375d027046e1f272e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100366142"
---
# <a name="azure-iot-edge-versions-and-release-notes"></a>Versões de Azure IoT Edge e notas de versão

Azure IoT Edge é um produto criado a partir do projeto de IoT Edge de código-fonte aberto hospedado no GitHub. Todas as novas versões são disponibilizadas no [projeto Azure IOT Edge](https://github.com/Azure/azure-iotedge). Contribuições e relatórios de bugs podem ser feitos no [projeto de IOT Edge de código-fonte aberto](https://github.com/Azure/iotedge).

## <a name="documented-versions"></a>Versões documentadas

A documentação IoT Edge neste site está disponível para duas versões diferentes do produto, para que você possa escolher o conteúdo que se aplica ao seu ambiente de IoT Edge. Atualmente, as duas versões com suporte são:

* **IoT Edge 1,1 (LTS)** é a primeira versão de LTS (suporte a longo prazo) do IOT Edge. A documentação desta versão abrange todos os recursos e funcionalidades de todas as versões anteriores por meio de 1,1. Esta versão de documentação será estável durante o tempo de vida com suporte da versão 1,1 e não refletirá os novos recursos lançados em versões posteriores. O lançamento de 1,1 é a versão mais recente disponível do IoT Edge.
* **IoT Edge 1,2 (versão prévia)** contém conteúdo adicional para recursos e funcionalidades que estão na versão de visualização mais recente, [1,2-RC1](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1)
  * Embora IoT Edge 1,2 esteja em versão prévia, você precisa instalar as versões Release Candidate. Para obter mais informações, consulte [instalação offline ou específica da versão](how-to-install-iot-edge.md?tabs=linux#offline-or-specific-version-installation-optional).

Para obter mais informações sobre as versões de IoT Edge, consulte [Azure IOT Edge sistemas com suporte](support.md).

## <a name="version-history"></a>Histórico de versão

Esta tabela fornece o histórico de versões recentes para IoT Edge lançamentos de pacotes e realça as atualizações de documentação feitas para cada versão.

| Notas de versão e ativos | Digite | Data | Destaques |
| ------------------------ | ---- | ---- | ---------- |
| [1.1](https://github.com/Azure/azure-iotedge/releases/tag/1.1.0) | LTS (suporte de longo prazo) | Fevereiro de 2021 | [Plano de suporte de longo prazo e atualizações de sistemas com suporte](support.md) |
| [1,2-RC1](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1) | Visualizar | Novembro de 2020 | [IoT Edge dispositivos por trás de gateways](how-to-connect-downstream-iot-edge-device.md?view=iotedge-2020-11&preserve-view=true)<br>[Agente do MQTT IoT Edge](how-to-publish-subscribe.md?view=iotedge-2020-11&preserve-view=true) |
| [1.0.10](https://github.com/Azure/azure-iotedge/releases/tag/1.0.10) | Estável | Outubro de 2020 | [Método direto UploadSupportBundle](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics)<br>[Carregar métricas de tempo de execução](how-to-access-built-in-metrics.md)<br>[Prioridade de rota e vida útil](module-composition.md#priority-and-time-to-live)<br>[Ordem de inicialização do módulo](module-composition.md#configure-modules)<br>[Provisionamento manual de X. 509](how-to-register-device.md) |
| [1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) | Estável | Março de 2020 | [Autoprovisionamento automático de X. 509 com DPS](how-to-auto-provision-x509-certs.md)<br>[Método direto RestartModule](how-to-edgeagent-direct-method.md#restart-module)<br>[suporte-comando de pacote](troubleshoot.md#gather-debug-information-with-support-bundle-command) |

## <a name="next-steps"></a>Próximas etapas

* [Exibir todas as versões de Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)
* [Fazer ou revisar solicitações de recursos no fórum de comentários](https://feedback.azure.com/forums/907045-azure-iot-edge)