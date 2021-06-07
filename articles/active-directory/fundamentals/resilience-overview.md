---
title: Criando gerenciamento de identidades e acesso resiliente com o Azure Active Directory
description: Um guia para arquitetos, administradores de ti e desenvolvedores sobre a criação de resiliência para a interrupção de seus sistemas de identidade.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39021fecb79f6b8e7536daad592378ed998760a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96454341"
---
# <a name="building-resilience-into-identity-and-access-management-with-azure-active-directory"></a>Criando resiliência no gerenciamento de identidade e acesso com Azure Active Directory

O IAM (gerenciamento de acesso e identidade) é uma estrutura de processos, políticas e tecnologias que facilitam o gerenciamento de identidades e o que eles acessam. Ele inclui os vários componentes que dão suporte à autenticação e autorização de usuário e outras contas em seu sistema.

A resiliência IAM é a capacidade de suportar a interrupção nos componentes do sistema e se recuperar com impacto mínimo sobre seus negócios, usuários, clientes e operações. Reduzir dependências, complexidade e pontos únicos de falha, ao mesmo tempo em que garante um tratamento de erros abrangente, aumentará sua resiliência.

A interrupção pode vir de qualquer componente de seus sistemas IAM. Para criar um sistema IAM resiliente, presume-se que as interrupções ocorram e planeje-se. 

Ao planejar a resiliência da sua solução IAM, considere os seguintes elementos: 

* Seus aplicativos que dependem de seu sistema IAM.

* As infraestruturas públicas que suas chamadas de autenticação usam, incluindo empresas de telecomunicações, provedores de serviços de Internet e provedores de chave pública.

* Seus provedores de identidade locais e de nuvem.

* Outros serviços que dependem de seu IAM e as APIs que os conectam.

* Quaisquer outros componentes locais no seu sistema.

Qualquer que seja a origem, o reconhecimento e o planejamento de contingências é importante. No entanto, a adição de sistemas de identidade adicionais e suas dependências e complexidade resultantes podem reduzir a resiliência em vez de aumentá-la.

Para criar mais resiliência em seus sistemas, examine os seguintes artigos:

* [Crie resiliência em sua infraestrutura IAM](resilience-in-infrastructure.md)

* [Crie resiliência IAM em seus aplicativos](resilience-app-development-overview.md)

* [Crie resiliência em seus sistemas CIAM (gerenciamento de acesso e identidade do cliente)](resilience-b2c.md)
