---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 04/03/2020
ms.author: crtreasu
ms.openlocfilehash: 44fd3b2bc89ac53e7a7c0293961098509d3f5c76
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "102044524"
---
### <a name="set-up-the-windows-device-portal"></a>Configurar o Portal de Dispositivos do Windows

Para se conectar ao seu HoloLens pelo WiFi, siga estas etapas:

1. Primeiro [Conecte seu HoloLens ao WiFi](https://docs.microsoft.com/hololens/hololens-network).

2. Depois obtenha o endereço IP do dispositivo em **Configurações > Rede e Internet > WiFi > Opções Avançadas**.

3. Usando um navegador da Web em seu computador, acesse `https://<YOUR_HOLOLENS_IP_ADDRESS>`. O navegador exibirá a seguinte mensagem: “Há um problema com o certificado de segurança desse site”. Essa mensagem aparece porque o certificado emitido para o Portal de Dispositivos é um certificado autoassinado. Você pode ignorar o erro de certificado e continuar.

Consulte [aqui](https://docs.microsoft.com/windows/mixed-reality/using-the-windows-device-portal) para obter mais informações sobre como configurar o Portal de Dispositivos do Windows.
