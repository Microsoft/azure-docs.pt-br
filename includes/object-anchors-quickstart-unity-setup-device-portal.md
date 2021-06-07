---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 04/03/2020
ms.author: crtreasu
ms.openlocfilehash: 81c4e95660eb2875cd1b03bdfa670aeabadedc3f
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105459"
---
### <a name="set-up-the-windows-device-portal"></a>Configurar o Portal de Dispositivos do Windows

Para se conectar ao seu HoloLens pelo WiFi, siga estas etapas:

1. Primeiro [Conecte seu HoloLens ao WiFi](/hololens/hololens-network).

2. Depois obtenha o endereço IP do dispositivo em **Configurações > Rede e Internet > WiFi > Opções Avançadas**.

3. Usando um navegador da Web em seu computador, acesse `https://<YOUR_HOLOLENS_IP_ADDRESS>`. O navegador exibirá a seguinte mensagem: “Há um problema com o certificado de segurança desse site”. Essa mensagem aparece porque o certificado emitido para o Portal de Dispositivos é um certificado autoassinado. Você pode ignorar o erro de certificado e continuar.

Consulte [aqui](/windows/mixed-reality/using-the-windows-device-portal) para obter mais informações sobre como configurar o Portal de Dispositivos do Windows.