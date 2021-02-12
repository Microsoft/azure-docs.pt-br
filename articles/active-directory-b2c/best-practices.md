---
title: Práticas recomendadas para Azure AD B2C
titleSuffix: Azure AD B2C
description: Recomendações e práticas recomendadas a serem consideradas ao trabalhar com Azure Active Directory B2C (Azure AD B2C).
services: active-directory-b2c
author: vigunase
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: vigunase
ms.subservice: B2C
ms.openlocfilehash: 1c3c3d38ac0d8334f70f681d8ef86c0d6f86ecfa
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96750213"
---
# <a name="recommendations-and-best-practices-for-azure-active-directory-b2c"></a>Recomendações e práticas recomendadas para Azure Active Directory B2C

As práticas recomendadas e recomendações a seguir abrangem alguns dos principais aspectos da integração do Azure Active Directory (Azure AD) B2C em ambientes de aplicativos novos ou existentes.

## <a name="fundamentals"></a>Princípios básicos

| Melhor prática | Descrição |
|--|--|
| Escolher fluxos de usuário para a maioria dos cenários | A estrutura de experiência de identidade do Azure AD B2C é a força principal do serviço. As políticas descrevem em detalhe experiências de identidade, tais como inscrição, entrada ou edição de perfil. Para ajudá-lo a configurar as tarefas de identidade mais comuns, o portal do Azure AD B2C inclui políticas predefinidas e configuráveis chamadas fluxos dos usuários. Com os fluxos de usuário, você pode criar excelentes experiências do usuário em questão de minutos, com apenas alguns cliques. [Saiba quando usar fluxos de usuário versus políticas personalizadas](custom-policy-overview.md#comparing-user-flows-and-custom-policies).|
| Registros de aplicativo | Todos os aplicativos (Web, nativos) e a API que estão sendo protegidos devem ser registrados em Azure AD B2C. Se um aplicativo tiver uma versão Web e nativa do iOS e do Android, você poderá registrá-las como um aplicativo no Azure AD B2C com a mesma ID do cliente. Saiba como [registrar aplicativos OIDC, SAML, Web e nativos](./tutorial-register-applications.md?tabs=applications). Saiba mais sobre os [tipos de aplicativos que podem ser usados no Azure ad B2C](./application-types.md). |
| Mover para cobrança mensal de usuários ativos | Azure AD B2C passou de autenticações ativas mensais para a cobrança de usuários ativos mensais (MAU). A maioria dos clientes considerará esse modelo econômico. [Saiba mais sobre a cobrança mensal dos usuários ativos](https://azure.microsoft.com/updates/mau-billing/). [Salvar este link](b2clogin.md) |

## <a name="planning-and-design"></a>Planejamento e design

Defina a arquitetura do aplicativo e do serviço, os sistemas atuais de inventário e planeje sua migração para Azure AD B2C.

| Melhor prática | Descrição |
|--|--|
| Arquitetar uma solução de ponta a ponta | Inclua todas as dependências de seus aplicativos ao planejar uma integração de Azure AD B2C. Considere todos os serviços e produtos que estão atualmente em seu ambiente ou que talvez precisem ser adicionados à solução, por exemplo, Azure Functions, sistemas CRM (gerenciamento de relacionamento com o cliente), gateway de gerenciamento de API do Azure e serviços de armazenamento. Leve em conta a segurança e a escalabilidade para todos os serviços. |
| Documente as experiências de seus usuários | Detalhe todos os percursos do usuário que seus clientes podem experimentar em seu aplicativo. Inclua todas as telas e todos os fluxos de ramificação que possam encontrar ao interagir com os aspectos de identidade e perfil do seu aplicativo. Inclua usabilidade, acessibilidade e localização em seu planejamento. |
| Escolha o protocolo de autenticação certo |  Para obter uma análise dos diferentes cenários de aplicativo e seus fluxos de autenticação recomendados, consulte [cenários e fluxos de autenticação com suporte](../active-directory/develop/authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows). |
| Piloto de uma experiência de usuário de ponta a ponta de verificação de conceito (POC) | Comece com nossos exemplos de [código da Microsoft](code-samples.md) e [exemplos da Comunidade](https://github.com/azure-ad-b2c/samples). |
| Criar um plano de migração |O planejamento antecipado pode fazer com que a migração fique mais tranqüila. Saiba mais sobre a [migração do usuário](user-migration.md).|
| Usabilidade versus segurança | Sua solução deve ser o equilíbrio certo entre a usabilidade do aplicativo e o nível aceitável de risco da sua organização. |
| Mover dependências locais para a nuvem | Para ajudar a garantir uma solução resiliente, considere mover dependências de aplicativo existentes para a nuvem. |
| Migrar aplicativos existentes para o b2clogin.com | A substituição de login.microsoftonline.com entrará em vigor para todos os locatários de Azure AD B2C em 04 de dezembro de 2020. [Saiba mais](b2clogin.md). |
| Usar a proteção de identidade e o acesso condicional | Use esses recursos para ter um controle significativamente maior sobre autenticações e políticas de acesso arriscadas. Azure AD B2C Premium P2 é necessário. [Saiba mais](conditional-access-identity-protection-overview.md). |

## <a name="implementation"></a>Implementação

Durante a fase de implementação, considere as recomendações a seguir.

| Melhor prática | Descrição |
|--|--|
| Editar políticas personalizadas com a extensão de Azure AD B2C para Visual Studio Code | Baixe Visual Studio Code e essa extensão criada pela Comunidade [do Visual Studio Code Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c). Embora não seja um produto oficial da Microsoft, a extensão de Azure AD B2C para Visual Studio Code inclui vários recursos que ajudam a facilitar o trabalho com políticas personalizadas. |
| Saiba como solucionar problemas Azure AD B2C | Saiba como [solucionar problemas de políticas personalizadas](./troubleshoot-custom-policies.md?tabs=applications) durante o desenvolvimento. Saiba o que é um fluxo de autenticação normal e use as ferramentas para descobrir erros e anomalias. Por exemplo, use [Application insights](troubleshoot-with-application-insights.md) para examinar os logs de saída de percursos do usuário. |
| Aproveite nossa biblioteca de padrões de política personalizada comprovada | Encontre [exemplos](https://github.com/azure-ad-b2c/samples) de vários percursos de usuário do CIAM (gerenciamento de identidade e acesso) de clientes Azure ad B2C aprimorados. |

## <a name="testing"></a>Testando

Teste e automatize sua implementação de Azure AD B2C.

| Melhor prática | Descrição |
|--|--|
| Conta para tráfego global | Use fontes de tráfego de um endereço global diferente para testar os requisitos de desempenho e localização. Verifique se todos os HTMLs, CSS e dependências podem atender às suas necessidades de desempenho. |
| Teste funcional e de interface do usuário | Teste os fluxos de usuário de ponta a ponta. Adicionar testes sintéticos a cada poucos minutos usando o Selenium, o teste da Web do VS, etc. |
| Teste de caneta | Antes de entrar em funcionamento com sua solução, execute exercícios de teste de penetração para verificar se todos os componentes são seguros, incluindo quaisquer dependências de terceiros. Verifique se você protegeu suas APIs com tokens de acesso e usou o protocolo de autenticação certo para o cenário do aplicativo. Saiba mais sobre os [testes de penetração](../security/fundamentals/pen-testing.md) e as [regras de teste de penetração unificada do Microsoft Cloud](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1). |
| Teste A/B | Lance seus novos recursos com um pequeno conjunto aleatório de usuários antes de distribuir para toda a população. Com o JavaScript habilitado no Azure AD B2C, você pode integrar com ferramentas de teste A/B, como otimizar, clareza e outros. |
| Teste de carga | Azure AD B2C pode dimensionar, mas seu aplicativo só poderá ser dimensionado se todas as suas dependências puderem ser dimensionadas. Teste a carga de suas APIs e CDN. |
| Limitação |  Azure AD B2C limita o tráfego se muitas solicitações forem enviadas da mesma fonte em um curto período de tempo. Use várias fontes de tráfego durante o teste de carga e manipule o `AADB2C90229` código de erro normalmente em seus aplicativos. |
| Automação | Use pipelines de CI/CD (integração e entrega contínuas) para automatizar testes e implantações, por exemplo, [DevOps do Azure](deploy-custom-policies-devops.md). |

## <a name="operations"></a>Operações

Gerencie seu ambiente de Azure AD B2C.

| Melhor prática | Descrição |
|--|--|
| Criar vários ambientes | Para operações mais fáceis e distribuição de implantação, crie ambientes separados para desenvolvimento, teste, pré-produção e produção. Crie Azure AD B2C locatários para cada um. |
| Use o controle de versão para suas políticas personalizadas | Considere o uso do GitHub, Azure Repos ou outro sistema de controle de versão baseado em nuvem para suas Azure AD B2C políticas personalizadas. |
| Use a API de Microsoft Graph para automatizar o gerenciamento de seus locatários B2C | APIs de Microsoft Graph:<br/>Gerenciar a [estrutura de experiência de identidade](/graph/api/resources/trustframeworkpolicy?preserve-view=true&view=graph-rest-beta) (políticas personalizadas)<br/>[Chaves](/graph/api/resources/trustframeworkkeyset?preserve-view=true&view=graph-rest-beta)<br/>[Fluxos de Usuário](/graph/api/resources/identityuserflow?preserve-view=true&view=graph-rest-beta) |
| Integrar com o Azure DevOps | Um [pipeline de CI/CD](deploy-custom-policies-devops.md) torna o código de movimentação entre diferentes ambientes fácil e garante a preparação da produção em todos os momentos.   |
| Integrar com o Azure Monitor | Os [eventos do log de auditoria](view-audit-logs.md) são mantidos apenas por sete dias. [Integre com Azure monitor](azure-monitor.md) para manter os logs para uso de longo prazo ou integre com ferramentas de Siem (gerenciamento de eventos e informações de segurança) de terceiros para obter informações sobre o seu ambiente. |
| Configurar alertas e monitoramento ativos | [Acompanhe o comportamento do usuário](./analytics-with-application-insights.md) em Azure AD B2C usando Application insights. |

## <a name="support-and-status-updates"></a>Atualizações de status e suporte
## <a name="todays-support-and-status-updates"></a>Atuais atualizações de suporte e status
Mantenha-se atualizado com o estado do serviço e encontre opções de suporte.

| Melhor prática | Descrição |
|--|--|
| [Atualizações de serviço](https://azure.microsoft.com/updates/?product=active-directory-b2c) |  Mantenha-se atualizado com Azure AD B2C comunicados e atualizações de produtos. |
| [Suporte da Microsoft](support-options.md) | Arquivo uma solicitação de suporte para problemas técnicos de Azure AD B2C. O suporte para gerenciamento de assinaturas e cobranças é fornecido sem custo adicional. |
| [Status do Azure](https://status.azure.com/status) | Exiba o status de integridade atual de todos os serviços do Azure. |