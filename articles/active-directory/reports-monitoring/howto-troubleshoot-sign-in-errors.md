---
title: Como solucionar problemas de relatórios de erros de entrada | Microsoft Docs
description: Aprenda a solucionar erros de login usando relatórios do Azure Active Directory Domain Services no portal do Azure
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4de9f5a21f38f741dcf65d285446d9482716d4f2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "85608119"
---
# <a name="how-to-troubleshoot-sign-in-errors-using-azure-active-directory-reports"></a>Como solucionar problemas de erros de logon usando relatórios do Azure Active Directory Domain Services

O [relatório de logins](concept-sign-ins.md) no Azure AD (Azure Active Directory Domain Services) permite que você encontre respostas a perguntas sobre como gerenciar o acesso aos aplicativos em sua organização, incluindo:

- O que é o padrão de entrada de um usuário?
- Quantos usuários entraram em uma semana?
- Qual é o status dessas entradas?


Além disso, o relatório de logins também pode ajudar você a solucionar falhas de login para usuários em sua organização. Neste guia, você aprende como isolar uma falha de entrada no relatório de entrada e usá-la para reconhecer a causa raiz da falha.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de:

* Um locatário do Microsoft Azure Active Directory com uma licença premium (P1/P2). Consulte [Introdução ao Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) para fazer upgrade da edição do Azure Active Directory.
* Um usuário, que está na função **administrador global**, **administrador de segurança**, **leitor de segurança** ou **leitor de relatório** para o locatário. Além disso, qualquer usuário pode acessar suas próprias entradas. 

## <a name="troubleshoot-sign-in-errors-using-the-sign-ins-report"></a>Solucionar problemas de erros de entrada usando o relatório de entradas

1. Navegue até o [portal do Azure](https://portal.azure.com) e selecione seu diretório.
2. Selecione **Azure Active Directory** e selecione **Entradas** na seção **Monitoramento**. 
3. Use os filtros fornecidos para restringir a falha, seja pelo nome de usuário ou identificador de objeto, nome do aplicativo ou data. Além disso, selecione **falha** na lista suspensa **status** para exibir somente as entradas com falha. 

    ![Filtrar resultados](./media/howto-troubleshoot-sign-in-errors/filters.png)
        
4. Identifique a entrada com falha que você deseja investigar. Selecione-o para abrir a janela detalhes adicionais com mais informações sobre a entrada com falha. Anote o **código de erro de login** e **motivo de falha**. 

    ![Selecione o registro](./media/howto-troubleshoot-sign-in-errors/sign-in-failures.png)
        
5. Você também pode encontrar essas informações na guia **Solução de problemas e suporte** na janela de detalhes.

    ![Solução de problemas e suporte](./media/howto-troubleshoot-sign-in-errors/troubleshooting-and-support.png)

6. O motivo da falha descreve o erro. Por exemplo, no cenário acima, o motivo da falha é **nome de usuário ou senha inválido ou nome de usuário ou senha local inválido**. A correção é simplesmente entrar novamente com o nome de usuário e a senha corretos.

7. Você pode obter informações adicionais, incluindo ideias para correção, pesquisando o código de erro **50126** neste exemplo, na referência de códigos de erro de [log-ins](reference-sign-ins-error-codes.md). 

8. Se tudo mais falhar ou se o problema persistir apesar de ter tomado as medidas recomendadas, [abra um tíquete de suporte](../fundamentals/active-directory-troubleshooting-support-howto.md) seguindo as etapas na guia **Solução de problemas e suporte**. 

## <a name="next-steps"></a>Próximas etapas

* [Códigos de erro de entradas de referência](reference-sign-ins-error-codes.md)
* [Visão geral do relatório de entradas](concept-sign-ins.md)
