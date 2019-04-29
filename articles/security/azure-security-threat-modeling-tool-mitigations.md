---
title: Mitigações - Microsoft Threat Modeling Tool - Azure | Microsoft Docs
description: Página Mitigações para o Microsoft Threat Modeling Tool destacando as possíveis soluções para as ameaças geradas mais expostas.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 24aa49fd4ccccda372d2632ef4aee22bd5cb2bf6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611313"
---
# <a name="microsoft-threat-modeling-tool-mitigations"></a>Mitigações do Microsoft Threat Modeling Tool

A ferramenta de modelagem de ameaça é um elemento principal do Microsoft Security Development Lifecycle (SDL). Ele permite identificar e mitigar possíveis problemas de segurança no início, quando eles são relativamente fácil e econômica para resolver os arquitetos de software. Como resultado, ele reduz o custo total de desenvolvimento. Além disso, criamos a ferramenta com especialistas de não-segurança em mente, facilitando a modelagem de ameaças para todos os desenvolvedores fornecendo orientação clara sobre a criação e análise de modelos de ameaças.

Visite o **[Threat Modeling Tool](./azure-security-threat-modeling-tool.md)** para começar a usá-lo hoje mesmo!

## <a name="mitigation-categories"></a>Categorias de mitigação

As atenuações da ferramenta de modelagem de ameaças são categorizadas de acordo com o quadro de segurança de aplicativo da Web, que consiste no seguinte:

| Category | DESCRIÇÃO |
| -------- | ----------- |
| **[Auditoria e Log](./azure-security-threat-modeling-tool-auditing-and-logging.md)** | Quem fez o que e quando? Auditoria e log consultem como seu aplicativo registra eventos relacionados à segurança |
| **[Autenticação](./azure-security-threat-modeling-tool-authentication.md)** | Quem é você? Autenticação é o processo no qual uma entidade comprova a identidade de outra entidade, normalmente por meio de credenciais, como um nome de usuário e senha |
| **[Autorização](./azure-security-threat-modeling-tool-authorization.md)** | O que você pode fazer? A autorização é como o seu aplicativo fornece controles de acesso para recursos e operações |
| **[Segurança da Comunicação](./azure-security-threat-modeling-tool-communication-security.md)** | Que você está falando com? Segurança de comunicação garante que toda a comunicação feita é tão segura quanto possível |
| **[Gerenciamento da Configuração](./azure-security-threat-modeling-tool-configuration-management.md)** | Que seu aplicativo executar como? Quais bancos de dados ele se conecta ao? Como o seu aplicativo é administrado? Como essas configurações são protegidas? Gerenciamento de configuração se refere a como o seu aplicativo lida com esses problemas operacionais |
| **[Criptografia](./azure-security-threat-modeling-tool-cryptography.md)** | Como você mantém os segredos (confidencialidade)? Como são à prova de adulteração seus dados ou bibliotecas (integridade)? Como você gera valores aleatórios que devem ser criptograficamente forte? Criptografia refere-se a como seu aplicativo impõe a integridade e confidencialidade |
| **[Gerenciamento de Exceções](./azure-security-threat-modeling-tool-exception-management.md)** | Quando uma chamada de método no seu aplicativo falha, o que ele faz? Quanto você revela? Você retornar informações de erro amigável para usuários finais? Você transmite informações úteis de exceção para o chamador? Seu aplicativo não consegue normalmente? |
| **[Validação da Entrada](./azure-security-threat-modeling-tool-input-validation.md)** | Como você sabe que a entrada que recebe seu aplicativo é válidos e seguros? Validação de entrada se refere a como seu aplicativo filtra, limpa ou recusa dados antes do processamento adicional. Considere a restrição de entrada por meio de pontos de entrada e a codificação de saída por meio de pontos de saída. Você confia em dados de fontes, como bancos de dados e compartilhamentos de arquivos? |
| **[Dados Confidenciais](./azure-security-threat-modeling-tool-sensitive-data.md)** | Como o seu aplicativo lida com dados confidenciais? Dados confidenciais se refere a como seu aplicativo lida com dados que precisam ser protegidos na memória, pela rede, ou no armazenamento persistente |
| **[Gerenciamento da Sessão](./azure-security-threat-modeling-tool-session-management.md)** | Como o seu aplicativo controlar e proteger as sessões de usuário? Uma sessão é uma série de interações relacionadas entre um usuário e o aplicativo da Web |

Isso ajuda a identificar:

* Onde os erros mais comuns são feitos
* Onde estão as melhorias mais viáveis

Como resultado, você pode usa essas categorias para se concentrar e priorizar seu trabalho de segurança, para que se você souber que os principais problemas de segurança ocorrem nas categorias de validação, autenticação e autorização de entrada, você pode iniciar lá. Para saber mais, visite **[este link patenteado](https://www.google.com/patents/US7818788)**

## <a name="next-steps"></a>Próximas etapas

Visite **[Threat Modeling Tool Threats](./azure-security-threat-modeling-tool-threats.md)** (Ameaças do Threat Modeling Tool) para saber mais sobre as categorias de ameaça que a ferramenta usa para gerar possíveis ameaças de design.