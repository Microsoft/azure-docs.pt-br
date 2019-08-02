---
title: O que é o OPC Twin – Azure | Microsoft Docs
description: Visão geral do OPC Twin
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: d58dc18d5513259d5c01f7ddcc54736796e5c824
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603621"
---
# <a name="what-is-opc-twin"></a>O que é um OPC Twin?

O OPC Twin é composto por microsserviços que usam o Azure IoT Edge e o Hub IoT para se conectarem à nuvem e à rede de fábrica. O OPC Twin fornece descoberta, registro e controle remoto de dispositivos industriais por meio de APIs REST. O OPC Twin não exige um SDK da UA (Arquitetura Unificada) de OPC, é independente de linguagem de programação e pode ser incluído em um fluxo de trabalho sem servidor. Este artigo descreve vários casos de uso do OPC Twin.

## <a name="discovery-and-control"></a>Descoberta e controle
Você pode usar o OPC Twin para descoberta e registro simples.

### <a name="simple-discovery-and-registration"></a>Descoberta e registro simples
O OPC Twin permite que os operadores de fábrica verifiquem a rede de fábrica de modo que os servidores da UA de OPC possam ser descobertos e registrados. Como alternativa, os operadores de fábrica também podem registrar manualmente dispositivos da UA de OPC usando uma URL de descoberta conhecida. Por exemplo, para conectar-se a todos os dispositivos da UA de OPC após a instalação do gateway do IoT Edge com um módulo OPC Twin no chão de fábrica, o operador de fábrica pode disparar remotamente uma verificação da rede e observar visualmente todos os servidores da UA de OPC. 

### <a name="simple-control"></a>Controle simples
O OPC Twin permite que os operadores de fábrica reajam a eventos e reconfigurem seus computadores de chão de fábrica da nuvem manual ou automaticamente em tempo real. O OPC Twin fornece APIs REST para invocar os serviços no servidor da UA de OPC, procurar seu espaço de endereço, bem como ler/gravar variáveis e executar métodos. Por exemplo, uma caldeira usa KPI de temperatura para controlar a linha de produção. O sensor de temperatura publica a alteração nos dados usando o OPC Publisher. O operador de fábrica recebe o alerta de que a temperatura atingiu o limite. A linha de produção é resfriada automaticamente por meio do OPC Twin. O operador de fábrica é notificado do resfriamento.

## <a name="authentication"></a>Authentication
Você pode usar o OPC Twin para autenticação e uma experiência de desenvolvedor simples.

### <a name="simple-authentication"></a>Autenticação simples 
O OPC Twin usa autenticação baseada em AAD (Azure Active Directory) e auditoria de ponta a ponta. Por exemplo, o OPC Twin permite que o aplicativo seja criado sobre o OPC Twin para determinar o que um operador executou em um computador. No lado do computador, é por meio de auditoria da UA de OPC. No lado da nuvem, é por meio de armazenamento de um log de auditoria de cliente imutável e autenticação do AAD na API REST.

### <a name="simple-developer-experience"></a>Experiência simples para o desenvolvedor 
O OPC Twin pode ser usado com aplicativos escritos em qualquer linguagem de programação por meio de APIs REST. Uma vez que os desenvolvedores integram um cliente da UA de OPC a uma solução, não é necessário conhecimento sobre o SDK da UA de OPC. O OPC Twin pode integrar-se perfeitamente a arquiteturas sem servidor e sem estado. Por exemplo, um desenvolvedor Web de pilha completa que desenvolve um aplicativo para um painel de alarme e evento pode escrever a lógica para responder a eventos em JavaScript ou TypeScript usando o OPC Twin sem ter conhecimento de C, C# nem da implementação de pilha completa da UA de OPC. 

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre o OPC Twin e seus usos, está é a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [O que é um OPC Vault](overview-opc-twin-architecture.md)