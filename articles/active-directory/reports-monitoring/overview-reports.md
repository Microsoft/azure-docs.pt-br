---
title: O que são os relatórios do Azure Active Directory? | Microsoft Docs
description: Uma visão geral dos relatórios do Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 6141a333-38db-478a-927e-526f1e7614f4
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 09/30/2020
ms.author: markvi
ms.reviewer: sarbar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f9a51c10a4f390e5627bccf35ab5dc74689e9c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91566815"
---
# <a name="what-are-azure-active-directory-reports"></a>O que são os relatórios do Azure Active Directory?

Os relatórios do Azure Active Directory (Azure AD) fornecem uma visão abrangente da atividade em seu ambiente. Os dados fornecidos permitem a você:

- Determinar como os aplicativos e serviços estão sendo utilizados pelos usuários
- Detectar possíveis riscos que afetem a integridade do seu ambiente
- Solucionar problemas que impedem a conclusão dos trabalhos pelos usuários  

A arquitetura de relatório se baseia em dois pilares principais:

- [Relatórios de segurança](#security-reports)
- [Relatórios de atividades](#activity-reports)

![Relatório](./media/overview-reports/01.png)


## <a name="security-reports"></a>Relatórios de segurança

Os relatórios de segurança ajudam a proteger as identidades da organização. Há dois tipos de relatórios de segurança:

- **Usuários sinalizados como risco**: no [relatório de usuários sinalizados como risco de segurança](../identity-protection/overview-identity-protection.md), obtenha uma visão geral das contas de usuário que podem ter sido comprometidas.

- **Entradas de risco**: com o [relatório de entradas de risco](../identity-protection/overview-identity-protection.md), você tem um indicador de tentativas de logon que pode ter sido realizadas por alguém que não é o proprietário legítimo de uma conta de usuário. 

### <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Qual licença do Azure AD você precisa para acessar a atividade de entrada?  

Todas as edições do Azure AD fornecem relatórios de usuários sinalizados como risco e de entradas de risco. No entanto, o nível de granularidade do relatório varia entre as edições: 

- Nas **edições do Azure Active Directory Gratuito e Básico**, você obtém uma lista de usuários sinalizados como risco e de entradas de risco. 

- A edição **Azure Active Directory Premium 1** estende esse modelo, permitindo também que você examine algumas das detecções de risco subjacentes detectadas para cada relatório. 

- A edição **Azure Active Directory Premium 2** fornece as informações mais detalhadas sobre as detecções de risco subjacentes e também permite configurar políticas de segurança que respondem automaticamente aos níveis de risco configurados.


## <a name="activity-reports"></a>Relatórios de atividades

Os relatórios de atividade ajudam você a compreender o comportamento de usuários em sua organização. Há dois tipos de relatórios de atividade no Azure AD:

- **Logs de auditoria**: o [relatório de atividade dos logs de auditoria](concept-audit-logs.md) fornece acesso ao histórico de todas as tarefas executadas em seu locatário.

- **Entradas**: com o [relatório de atividades de entradas](concept-sign-ins.md), você pode determinar quem realizou as tarefas indicadas pelo relatório dos logs de auditoria.



> [!VIDEO https://www.youtube.com/embed/ACVpH6C_NL8]




### <a name="audit-logs-report"></a>Relatório de logs de auditoria 

O [relatório dos logs de auditoria](concept-audit-logs.md) fornece logs de atividades de sistema para fins de conformidade. Esses dados permitem abordar cenários comuns, como:

- Alguém em meu locatário tem acesso a um grupo de administração. Quem deu o acesso? 

- Quero saber a lista de usuários que estão entrando em um aplicativo específico, já que integrei o aplicativo recentemente e desejo saber se ele está tendo boa recepção

- Quero saber quantas redefinições de senha estão ocorrendo em meu locatário


#### <a name="what-azure-ad-license-do-you-need-to-access-the-audit-logs-report"></a>Qual é a licença do Azure AD necessária para acessar o relatório dos logs de auditoria?  

O relatório dos logs de auditoria está disponível para os recursos para os quais você possui licenças. Se você tem uma licença para um recurso específico, também tem acesso às informações da trilha de auditoria dele. Veja uma comparação detalhada dos recursos conforme os [diferentes tipos de licenças](../fundamentals/active-directory-whatis.md#what-are-the-azure-ad-licenses) na [página de preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Para obter mais detalhes, veja [Recursos e funcionalidades do Azure Active Directory](../fundamentals/active-directory-whatis.md#which-features-work-in-azure-ad).

### <a name="sign-ins-report"></a>Relatório de entradas

O [relatório de entradas](concept-sign-ins.md) permite encontrar respostas para perguntas como:

- O que é o padrão de entrada de um usuário?
- Quantos usuários entraram em uma semana?
- Qual é o status dessas entradas?

#### <a name="what-azure-ad-license-do-you-need-to-access-the-sign-ins-activity-report"></a>Qual licença do Azure AD você precisa para acessar o relatório de atividades de entrada?  

Para acessar o relatório de atividades de entrada, seu locatário deve ter uma licença do Azure AD Premium associada a ele.

## <a name="programmatic-access"></a>Acesso Programático

Além da interface do usuário, o Azure AD também oferece a você [acesso programático](concept-reporting-api.md) aos dados de relatórios, por meio de um conjunto de APIs baseadas em REST. Você pode chamar essas APIs de várias ferramentas e linguagens de programação. 

## <a name="next-steps"></a>Próximas etapas

- [Relatório de entradas de risco](../identity-protection/overview-identity-protection.md)
- [Relatório de logs de auditoria](concept-audit-logs.md)
- [Relatório de logs de entrada](concept-sign-ins.md)