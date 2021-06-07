---
title: Configuração do Cache Conectado da Microsoft em um Azure IoT Edge para IoT industrial | Microsoft Docs
description: Tutorial de configuração do Cache Conectado da Microsoft em um Azure IoT Edge para IoT industrial
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 0d70ed8b906c171c001c5bda81a79ca9b65febac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101658607"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-microsoft-connected-cache-within-an-azure-iot-edge-for-industrial-iot-configuration"></a>Exemplo de cenário de implantação de visualização do Cache Conectado da Microsoft: configuração do Cache Conectado da Microsoft em um Azure IoT Edge para IoT industrial

As redes de manufatura geralmente são organizadas em camadas hierárquicas seguindo o [modelo de rede Purdue](https://en.wikipedia.org/wiki/Purdue_Enterprise_Reference_Architecture) (incluído nos padrões [ISA 95](https://en.wikipedia.org/wiki/ANSI/ISA-95) e [ISA 99](https://www.isa.org/standards-and-publications/isa-standards/isa-standards-committees/isa99)). Nessas redes, apenas a camada superior tem conectividade com a nuvem e as camadas inferiores na hierarquia só podem se comunicar com as camadas norte e sul adjacentes.

Este exemplo de GitHub, [Azure IoT Edge para IoT industrial](https://github.com/Azure-Samples/iot-edge-for-iiot), implanta o seguinte:

* Rede Purdue simulada no Azure
* Ativos industriais 
* Hierarquia de gateways de Azure IoT Edge
  
Esses componentes serão usados para adquirir dados industriais e carregá-los com segurança na nuvem sem comprometer a segurança da rede. O Cache Conectado da Microsoft pode ser implantado para dar suporte ao download de conteúdo em todos os níveis na rede em conformidade com o ISA 95.

A chave para configurar implantações do Cache Conectado da Microsoft em uma rede compatível com o ISA 95 é configurar o proxy de OT *e* o host upstream no gateway de IoT Edge L3.

1. Configure implantações do Cache Conectado da Microsoft nos níveis L5 e L4, conforme descrito no exemplo de gateway de IoT Edge aninhado em dois níveis 
2. A implantação no gateway de IoT Edge L3 deve especificar:
   
   * UPSTREAM_HOST – O IP/FQDN do gateway de IoT Edge L4, no qual o Cache Conectado de L3 da Microsoft solicitará conteúdo.
   * UPSTREAM_PROXY – O IP/FQDN:PORT do servidor proxy de OT.

3. O proxy de OT deve adicionar o endereço IP/FQDN do MCC L4 à lista de permissões.

Para validar se o Cache Conectado da Microsoft está funcionando corretamente, execute o seguinte comando no terminal do dispositivo IoT Edge, hospedando o módulo ou qualquer dispositivo na rede.

```bash
    wget "http://<L3 IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```