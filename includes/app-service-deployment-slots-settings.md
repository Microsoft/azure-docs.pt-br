---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: b4234acb2ce32980a268e389cb31de9a57ed18e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82132145"
---
Quando você clona a configuração de outro slot de implantação, a configuração clonada é editável. Alguns elementos de configuração acompanham o conteúdo quando acontece um swap (que não seja específico do slot), enquanto outros elementos de configuração permanecem no mesmo slot após um swap (específico do slot). As listas a seguir mostram as configurações que são alteradas com a troca de slots.

**Configurações que são trocadas**:

* Configurações gerais, como versão do framework, 32/64 bits, web sockets
* Configurações de aplicativo (podem ser configuradas para se fixarem a um slot)
* Strings de conexão (podem ser configuradas para se fixarem a um slot)
* Mapeamentos de manipuladores
* Certificados públicos
* Conteúdo do WebJobs
* Conexões híbridas *
* Integração com rede virtual *
* Endpoints do serviço *
* Rede de distribuição de conteúdo do Azure *

Recursos marcados com um asterisco (*) estão planejados para que não sejam alternados.

**Configurações que não são alternadas**:

* Endpoints de publicação
* Nomes de domínio personalizados
* Certificados não públicos e configurações de TLS/SSL
* Configurações de dimensionamento
* Agendadores de WebJobs
* Restrições de IP
* Always On
* Configurações de Diagnóstico
* CORS (Cross-origin resource sharing)

> [!NOTE]
> Determinadas configurações de aplicativo que se aplicam a configurações não alternadas também são trocadas. Por exemplo, como as configurações de diagnóstico não são trocadas, as configurações de aplicativo relacionadas, como `WEBSITE_HTTPLOGGING_RETENTION_DAYS` e `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` também não são trocadas, mesmo que não apareçam como configurações de slot.
>
