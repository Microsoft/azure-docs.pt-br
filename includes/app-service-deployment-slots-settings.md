---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: 7458f6868d7fbee72b55ad002148691a113c269d
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97531846"
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
* Integração com rede virtual

> [!NOTE]
> Para tornar essas configurações intercambiáveis, adicione a configuração do aplicativo `WEBSITE_OVERRIDE_PRESERVE_DEFAULT_STICKY_SLOT_SETTINGS` em cada slot do aplicativo e defina seu valor como `0` ou `false` . Essas configurações podem ser trocadas ou não. Você não pode fazer apenas algumas configurações serem trocadas e não as outras.

> Determinadas configurações de aplicativo que se aplicam a configurações não alternadas também são trocadas. Por exemplo, como as configurações de diagnóstico não são trocadas, as configurações de aplicativo relacionadas, como `WEBSITE_HTTPLOGGING_RETENTION_DAYS` e `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` também não são trocadas, mesmo que não apareçam como configurações de slot.
>
