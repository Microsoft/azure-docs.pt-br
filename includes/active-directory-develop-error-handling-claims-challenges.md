---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: e54301e01d25fb075325ef34522daa5f1b691dd5
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2020
ms.locfileid: "98760671"
---
## <a name="conditional-access-and-claims-challenges"></a>Acesso condicional e desafio de declarações

Ao obter tokens silenciosamente, o aplicativo poderá receber erros quando um [desafio de declarações de acesso condicional](../articles/active-directory/develop/v2-conditional-access-dev-guide.md), como a Política de MFA, for exigido por uma API que você está tentando acessar.

O padrão para tratar desse erro é obter um token de forma interativa usando a MSAL. Isso solicita ao usuário e dá a oportunidade de atender a política de acesso condicional necessária.

Em determinados casos, ao chamar uma API que exige acesso condicional, você poderá receber um desafio de declarações no erro da API. Por exemplo, se a política de acesso condicional tiver um dispositivo gerenciado (Intune), o erro será semelhante a [AADSTS53000: O dispositivo deverá ser gerenciado para acessar este recurso](../articles/active-directory/develop/reference-aadsts-error-codes.md) ou algo similar. Nesse caso, é possível passar as declarações na chamada do token de aquisição, de modo que o usuário seja solicitado a atender à política apropriada.
