---
title: Interfaces resilientes com processos externos usando o Azure AD B2C | Microsoft Docs
description: Métodos para criar interfaces resilientes com processos externos
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58ef522f5b048db0ef120625d9e894c8e14c070e
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724400"
---
# <a name="resilient-interfaces-with-external-processes"></a>Interfaces resilientes com processos externos

Neste artigo, fornecemos orientações sobre como planejar e implementar as APIs RESTFul no percurso do usuário e tornar seu aplicativo mais resiliente a falhas de API.

![Imagem mostra interfaces com componentes de processo externo](media/resilient-external-processes/external-processes-architecture.png)

## <a name="ensure-correct-placement-of-the-apis"></a>Garantir o posicionamento correto das APIs

As políticas do IEF (Identity Experience Framework) permitem chamar um sistema externo usando um [perfil técnico da API RESTful](../../active-directory-b2c/restful-technical-profile.md). Os sistemas externos não são controlados pelo ambiente de tempo de execução do IEF e são um ponto de falha potencial.

### <a name="how-to-manage-external-systems-using-apis"></a>Como gerenciar sistemas externos usando APIs

- Ao chamar uma interface para acessar determinados dados, verifique se os dados vão impulsionar a decisão de autenticação. Avalie se as informações são essenciais para a funcionalidade principal do aplicativo. Por exemplo, um e-commerce versus uma funcionalidade secundária, como uma administração. Se as informações não forem necessárias para a autenticação e forem necessárias apenas para cenários secundários, considere mover a chamada para a lógica do aplicativo.

- Se os dados necessários para a autenticação forem relativamente estáticos e pequenos, e não tiver nenhuma outra razão comercial para ser externamente do diretório, considere tê-lo no diretório.

- Remova as chamadas de API do caminho previamente autenticado sempre que possível. Se não puder, você deve fazer proteções estritas para negação de serviço (DoS) e ataques de DDoS (negação de serviço distribuído) na frente de suas APIs. Os invasores podem carregar a página de entrada e tentar inundar sua API com ataques de DoS e inviável seu aplicativo. Por exemplo, usando CAPTCHA em sua entrada, o fluxo de inscrição pode ajudar.

- Use [conectores de API do fluxo de usuário de inscrição interno](../../active-directory-b2c/api-connectors-overview.md) sempre que possível para integrar com APIs Web depois de entrar com um provedor de identidade ou antes de criar o usuário. Como os fluxos de usuário já estão amplamente testados, é provável que você não precise executar testes funcionais, de desempenho ou de escala no nível de fluxo do usuário. Você ainda precisa testar seus aplicativos quanto à funcionalidade, ao desempenho e à escala.

- Os [perfis técnicos](../../active-directory-b2c/restful-technical-profile.md) da API RESTFul do Azure ad não fornecem nenhum comportamento de Caching. Em vez disso, o perfil de API RESTFul implementa uma lógica de repetição e um tempo limite que é criado na política.

- Para APIs que precisam gravar dados, enfileirar uma tarefa para que essas tarefas sejam executadas por um trabalhador de segundo plano. Serviços como [filas do Azure](../../storage/queues/storage-queues-introduction.md) podem ser usados. Isso fará com que a API retorne com eficiência aumentando o desempenho de execução da política.  

## <a name="api-error-handling"></a>Tratamento de erro de API

Como as APIs residem fora do sistema Azure AD B2C, é necessário ter um tratamento de erros adequado no perfil técnico. Certifique-se de que o usuário final seja informado adequadamente e que o aplicativo possa lidar com falhas normalmente.

### <a name="how-to-gracefully-handle-api-errors"></a>Como lidar normalmente com erros de API

- Uma API pode falhar por vários motivos, tornar seu aplicativo resiliente a tais falhas. [Retorne uma mensagem de erro http 4xx](../../active-directory-b2c/restful-technical-profile.md#returning-validation-error-message) se a API não puder concluir a solicitação. Na política de Azure AD B2C, tente tratar normalmente a indisponibilidade da API e, talvez, gerar uma experiência reduzida.

- [Manipule erros transitórios normalmente](../../active-directory-b2c/restful-technical-profile.md#error-handling). O perfil de API RESTFul permite que você configure mensagens de erro para vários [disjuntores de circuito](/azure/architecture/patterns/circuit-breaker).

- Monitorar de forma proativa e usar a integração contínua/entrega contínua (CICD), girar as credenciais de acesso à API, como senhas e certificados usados pelo [mecanismo de perfil técnico](../../active-directory-b2c/restful-technical-profile.md).

## <a name="api-management---best-practices"></a>Gerenciamento de API-práticas recomendadas

Enquanto você implanta as APIs REST e configura o perfil técnico RESTful, seguir as melhores práticas recomendadas ajudará você a não fazer erros comuns e as coisas que estão sendo ignoradas.

### <a name="how-to-manage-apis"></a>Como gerenciar APIs

- O gerenciamento de API (APIM) publica, gerencia e analisa suas APIs. O APIM também manipula a autenticação para fornecer acesso seguro aos serviços de back-end e aos microservices. Use um gateway de API para expandir as implantações de API, o cache e o balanceamento de carga.

- A recomendação é obter o token correto no início do percurso do usuário em vez de chamar várias vezes para cada API e [proteger uma API APIM do Azure](../../active-directory-b2c/secure-api-management.md?tabs=app-reg-ga).

## <a name="next-steps"></a>Próximas etapas

- [Recursos de resiliência para desenvolvedores de Azure AD B2C](resilience-b2c.md)
  - [Experiência do usuário final resiliente](resilient-end-user-experience.md)
  - [Resiliência por meio de práticas recomendadas para desenvolvedores](resilience-b2c-developer-best-practices.md)
  - [Resiliência por meio de monitoramento e análise](resilience-with-monitoring-alerting.md)
- [Crie resiliência em sua infraestrutura de autenticação](resilience-in-infrastructure.md)
- [Aumentar a resiliência de autenticação e autorização em seus aplicativos](resilience-app-development-overview.md)