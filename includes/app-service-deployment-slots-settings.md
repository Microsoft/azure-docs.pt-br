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
Quando você clona a configuração de outro slot de implantação, a configuração clonada é editável. Alguns elementos de configuração seguem o conteúdo em uma permuta (não específico do slot), enquanto outros elementos de configuração permanecem no mesmo slot após uma permuta (específica do slot). A lista a seguir mostra as configurações que serão alterada com a troca de slots.

**Configurações que são permutadas**:

* Configurações gerais, como versão do Framework, 32/64 bits, Web Sockets
* Configurações do aplicativo (podem ser configuradas para fixarem-se a um slot)
* Cadeias de conexão (podem ser configuradas para fixarem-se a um slot)
* Mapeamentos de manipulador
* Certificados públicos
* Conteúdo de Trabalhos Web
* Conexões híbridas *
* Pontos de extremidade de serviço *
* Rede de distribuição de conteúdo do Azure *

Os recursos marcados com um asterisco (*) estão planejados para serem desalternados. 

**Configurações que não são alternadas**:

* Pontos de extremidade de publicação
* Nomes de domínio personalizados
* Certificados não públicos e configurações de TLS/SSL
* Configurações de dimensionamento
* Agendadores de Trabalhos Web
* Restrições de IP
* Always On
* Configurações de Diagnóstico
* CORS (Compartilhamento de Recursos entre Origens)
* Integração de rede virtual

> [!NOTE]
> Para tornar essas configurações intercambiáveis, adicione a configuração do aplicativo `WEBSITE_OVERRIDE_PRESERVE_DEFAULT_STICKY_SLOT_SETTINGS` em cada slot do aplicativo e defina seu valor como `0` ou `false` . Essas configurações podem ser trocadas ou não. Você não pode fazer apenas algumas configurações serem trocadas e não as outras.

> Determinadas configurações de aplicativo que se aplicam a configurações não alternadas também são trocadas. Por exemplo, como as configurações de diagnóstico não são trocadas, as configurações de aplicativo relacionadas, como `WEBSITE_HTTPLOGGING_RETENTION_DAYS` e `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` também não são trocadas, mesmo que não apareçam como configurações de slot.
>
