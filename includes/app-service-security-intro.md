---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 04/15/2020
ms.author: cephalin
ms.openlocfilehash: 0fccb1ed092190f88d197c9bf2cd17678a4d1731
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "83649036"
---
Os componentes de plataforma do Serviço de Aplicativo, incluindo VMs do Azure, armazenamento, conexões de rede, estruturas da Web, recursos de integração e gerenciamento, são ativamente seguros e protegidos. O Serviço de Aplicativo passa por verificações de conformidade rigorosas continuamente para garantir que:

- Os recursos do aplicativo estejam [protegidos](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) dos recursos do Azure de outros clientes.
- [Instâncias de VM e software de runtime são atualizados regularmente ](../articles/app-service/overview-patch-os-runtime.md) para tratar vulnerabilidades recentemente descobertas. 
- A comunicação de segredos (como cadeias de conexão) entre o aplicativo e outros recursos do Azure (como [Banco de Dados SQL](https://azure.microsoft.com/services/sql-database/)) permanece no Azure e não ultrapassa limites de rede. Segredos sempre são criptografados quando armazenados.
- Toda a comunicação sobre os recursos de conectividade do Serviço de Aplicativo, como [conexão híbrida](../articles/app-service/app-service-hybrid-connections.md), é criptografada. 
- As conexões com ferramentas de gerenciamento remoto como Azure PowerShell, CLI do Azure, SDKs do Azure, APIs REST são todas criptografadas.
- O gerenciamento de ameaças 24 horas protege a infraestrutura e a plataforma contra malware, ataque de DDoS (negação de serviço distribuído), MITM (man-in-the-middle) e outras ameaças.

Para saber mais sobre segurança de plataforma e infraestrutura no Azure, confira [Centro de Confiabilidade do Azure](https://azure.microsoft.com/overview/trusted-cloud/).