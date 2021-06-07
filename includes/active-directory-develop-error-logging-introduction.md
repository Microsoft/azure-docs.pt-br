---
author: mmacy
ms.author: marsma
ms.date: 12/16/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 53198c663d318a2eb47bcb3207939bbcb1fdd59c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98763322"
---
Os aplicativos da MSAL (biblioteca de autenticação da Microsoft) geram mensagens de log que podem ajudar a diagnosticar problemas. Um aplicativo pode configurar o registro em log com algumas linhas de código, ter um controle personalizado sobre o nível de detalhes e determinar se dados pessoais e organizacionais serão registrados. Recomendamos que você crie um retorno de chamada de log MSAL e forneça uma maneira para os usuários enviarem logs quando tiverem problemas de autenticação.

## <a name="logging-levels"></a>Níveis de log

O MSAL fornece vários níveis de detalhes de log:

- Erro: indica que algo deu errado e um erro foi gerado. Usado para depuração e identificação de problemas.
- Aviso: não há necessariamente erro ou falha, mas destina-se a problemas de diagnóstico e de identificação.
- Info: o MSAL registrará em log eventos destinados a fins informativos não necessariamente destinados à depuração.
- Verbose: padrão. O MSAL registra em log os detalhes completos do comportamento da biblioteca.

## <a name="personal-and-organizational-data"></a>Dados pessoais e organizacionais

Por padrão, o agente de log do MSAL não captura dados pessoais ou organizacionais altamente confidenciais. A biblioteca fornece a opção de habilitar o registro em log de dados pessoais e organizacionais se você decidir fazer isso.

As seções a seguir fornecem mais detalhes sobre o log de erros do MSAL para seu aplicativo.