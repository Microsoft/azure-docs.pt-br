---
title: Configurar o compartilhamento de recursos entre origens na API do Azure para FHIR
description: Este artigo descreve como configurar o compartilhamento de recursos entre origens na API do Azure para FHIR.
author: matjazl
ms.author: matjazl
ms.date: 3/11/2019
ms.topic: reference
ms.service: healthcare-apis
ms.subservice: fhir
ms.openlocfilehash: dc4c034b0821f1fe5ecb940591fca77d61edc3ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84870936"
---
# <a name="configure-cross-origin-resource-sharing-in-azure-api-for-fhir"></a>Configurar o compartilhamento de recursos entre origens na API do Azure para FHIR

A API do Azure para FHIR (recursos de interoperabilidade do Fast Healthcare) oferece suporte ao [compartilhamento de recursos entre origens (CORS)](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing). O CORS permite que você defina configurações para que os aplicativos de um domínio (origem) possam acessar recursos de um domínio diferente, conhecido como uma solicitação entre domínios.

O CORS é geralmente usado em um aplicativo de página única que deve chamar uma API RESTful para um domínio diferente.

Para definir uma configuração de CORS na API do Azure para FHIR, especifique as seguintes configurações:

- **Origens (Access-Control-Allow-Origin)**. Uma lista de domínios com permissão para fazer solicitações entre origens para a API do Azure para FHIR. Cada domínio (origem) deve ser inserido em uma linha separada. Você pode inserir um asterisco (*) para permitir chamadas de qualquer domínio, mas não é recomendável porque é um risco de segurança.

- **Cabeçalhos (Access-Control-Allow-headers)**. Uma lista de cabeçalhos que a solicitação de origem conterá. Para permitir todos os cabeçalhos, insira um asterisco (*).

- **Métodos (Access-Control-Allow-Methods)**. Os métodos permitidos (PUT, GET, POST e assim por diante) em uma chamada à API. Escolha **selecionar tudo** para todos os métodos.

- **Idade máxima (Access-Control-Max-age)**. O valor em segundos para armazenar em cache os resultados da solicitação de simulação para Access-Control-Allow-Headers e Access-Control-Allow-Methods.

- **Permitir credenciais (Access-Control-Allow-Credentials)**. As solicitações CORS normalmente não incluem cookies para impedir ataques [CSRF (solicitação entre sites forjada)](https://en.wikipedia.org/wiki/Cross-site_request_forgery) . Se você selecionar essa configuração, a solicitação poderá ser feita para incluir credenciais, como cookies. Você não poderá definir essa configuração se já tiver definido origens com um asterisco (*).

![Configurações de CORS (compartilhamento de recursos entre origens)](media/cors/cors.png)

>[!NOTE]
>Você não pode especificar configurações diferentes para origens de domínio diferentes. Todas as configurações (**cabeçalhos**, **métodos**, **idade máxima**e **permitir credenciais**) se aplicam a todas as origens especificadas na configuração origens.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a configurar o compartilhamento entre origens na API do Azure para FHIR. Em seguida, implante uma API do Azure totalmente gerenciada para FHIR:
 
>[!div class="nextstepaction"]
>[Implantar a API do Azure para FHIR](fhir-paas-portal-quickstart.md)