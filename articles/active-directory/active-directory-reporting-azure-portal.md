---
title: "Relatórios do Azure Active Directory | Microsoft Docs"
description: "Fornece uma visão geral sobre a emissão de relatórios do Azure Active Directory."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 6141a333-38db-478a-927e-526f1e7614f4
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/18/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: 738c8f4a56586b87f03973ec258b0a3023affa60
ms.contentlocale: pt-br
ms.lasthandoff: 07/17/2017

---
# <a name="azure-active-directory-reporting"></a>Relatórios do Azure Active Directory

Com os relatórios do Azure Active Directory, você pode ter uma ideia de como o seu ambiente está funcionando.  
Os dados fornecidos permitem a você:

- Determinar como os aplicativos e serviços estão sendo utilizados pelos usuários
- Detectar possíveis riscos que afetem a integridade do seu ambiente
- Solucionar problemas que impedem a conclusão dos trabalhos pelos usuários  

A arquitetura de relatório se baseia em dois pilares principais:

- Relatórios de segurança
- Relatórios de atividades

![Relatórios](./media/active-directory-reporting-azure-portal/01.png)



## <a name="security-reports"></a>Relatórios de segurança

Os relatórios de segurança no Azure Active Directory o ajudam a proteger as identidades da organização.  
Há dois tipos de relatórios de segurança no Azure Active Directory:

- **Usuários sinalizados como risco**: no [relatório de usuários sinalizados como risco de segurança](active-directory-reporting-security-user-at-risk.md), obtenha uma visão geral das contas de usuário que podem ter sido comprometidas.

- **Entradas de risco**: com o [relatório de entradas de risco](active-directory-reporting-security-risky-sign-ins.md), você tem um indicador de tentativas de logon que pode ter sido realizadas por alguém que não é o proprietário legítimo de uma conta de usuário. 

**Qual licença do Azure AD você precisa para acessar a atividade de entrada?**  
Todas as edições do Azure Active Directory fornecem relatórios de usuários sinalizados como risco e de entradas de risco.  
No entanto, o nível de granularidade do relatório varia entre as edições: 

- Nas **edições do Azure Active Directory Gratuita e Basic**, você obtém uma lista de usuários sinalizados como risco e de entradas de risco. 

- A edição do **Azure Active Directory Premium 1** estende esse modelo, também permitindo que você examine alguns dos eventos de risco subjacentes que foram detectados para cada relatório. 

- A edição do **Azure Active Directory Premium 2** fornece as informações mais detalhadas sobre os eventos de risco subjacentes e também permite configurar políticas de segurança que atendem automaticamente a níveis de risco configurados.


## <a name="activity-reports"></a>Relatórios de atividades

Há dois tipos de relatórios de atividade no Azure Active Directory:

- **Trilhas de auditoria**: o [relatório de atividade das trilhas de auditoria](active-directory-reporting-activity-audit-logs.md) fornece acesso ao histórico de todas as tarefas executadas em seu locatário.

- **Entradas**: com o [relatório de atividades de entradas](active-directory-reporting-activity-sign-ins.md), você pode determinar quem realizou as tarefas indicadas pelo relatório das trilhas de auditoria.



O **relatório das trilhas de auditoria** fornece registros de atividades de sistema para fins de conformidade.
Entre outros, os dados fornecidos permitem tratar cenários comuns, como:

- Alguém em meu locatário tem acesso a um grupo de administração. Quem deu o acesso? 

- Quero saber a lista de usuários que estão entrando em um aplicativo específico, já que integrei o aplicativo recentemente e desejo saber se ele está tendo boa recepção

- Quero saber quantas redefinições de senha estão ocorrendo em meu locatário


**Qual é a licença do Azure AD necessária para acessar o relatório das trilhas de auditoria?**  
O relatório das trilhas de auditoria está disponível para os recursos para os quais você possui licenças. Se você tem uma licença para um recurso específico, também tem acesso às informações da trilha de auditoria dele.

Para obter mais detalhes, confira **Comparando recursos geralmente disponíveis das edições Gratuita, Basic e Premium** em [Recursos do Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features).   



O **relatório de atividades de entrada** permite encontrar respostas a perguntas como:

- O que é o padrão de entrada de um usuário?
- Quantos usuários entraram em uma semana?
- Qual é o status dessas entradas?


**Qual licença do Azure AD você precisa para acessar o relatório de atividades de entrada?**  
Para acessar o relatório de atividades de entrada, seu locatário deve ter uma licença do Azure AD Premium associada a ele.


## <a name="programmatic-access"></a>Acesso Programático

Além da interface do usuário, os relatórios do Azure Active Directory também fornecem a você [acesso programático](active-directory-reporting-api-getting-started-azure-portal.md) aos dados de relatórios. Os dados desses relatórios podem ser muito úteis para seus aplicativos, como sistemas SIEM, auditoria e ferramentas de business intelligence. As APIs de relatório do Azure AD fornecem acesso programático aos dados através de um conjunto de APIs baseadas em REST. Você pode chamar essas APIs de várias ferramentas e linguagens de programação. 


## <a name="next-steps"></a>Próximas etapas

Se você quiser saber mais sobre os vários tipos de relatório no Azure Active Directory, confira:

- [Usuários sinalizados como risco](active-directory-reporting-security-user-at-risk.md)
- [Relatório de entradas de risco](active-directory-reporting-security-risky-sign-ins.md)
- [Relatório de trilhas de auditoria](active-directory-reporting-activity-audit-logs.md)
- [Relatório de logs de entrada](active-directory-reporting-activity-sign-ins.md)

Se você quiser saber mais sobre como acessar os dados de relatório usando a API de relatório, confira: 

- [Introdução à API de relatório do Azure Active Directory](active-directory-reporting-api-getting-started-azure-portal.md)


<!--Image references-->
[1]: ./media/active-directory-reporting-azure-portal/ic195031.png
