---
title: Solucionar problema de Acesso Condicional usando a ferramenta What If – Azure Active Directory
description: Onde encontrar quais políticas de Acesso Condicional foram aplicadas e por quê
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0112ab53c501d639d3f8e0d09d82ef3a12cb93a8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94837239"
---
# <a name="troubleshooting-conditional-access-using-the-what-if-tool"></a>Solucionar problema de Acesso Condicional usando a ferramenta What If

A [ferramenta What If](what-if-tool.md) do Acesso Condicional é eficiente para entender por que uma política foi ou não foi aplicada a um usuário em uma circunstância específica ou se uma política seria aplicada em um estado conhecido.

A ferramenta What If está localizada no **portal do Azure** > **Azure Active Directory** > **Acesso Condicional** > **What If**.

![Ferramenta de Acesso Condicional What If no estado padrão](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-tool.png)

> [!NOTE]
> Atualmente, a ferramenta What If não avalia políticas no modo somente de relatório.

## <a name="gathering-information"></a>Reunindo informações

A ferramenta What If requer apenas um **Usuário** para começar. 

As informações adicionais a seguir são opcionais, mas ajudarão a restringir o escopo para casos específicos.

* Aplicativos na nuvem ou ações
* Endereço IP 
* País/Região
* Plataforma de dispositivos
* Aplicativos do cliente (versão prévia)
* Estado do dispositivo (versão prévia) 
* Risco de entrada

Essas informações podem ser coletadas do usuário, do dispositivo deles ou do log de entradas do Azure AD.

## <a name="generating-results"></a>Gerar resultados

Insira os critérios coletados na seção anterior e selecione **What If** para gerar uma lista de resultados. 

A qualquer momento, você pode selecionar **Redefinir** para limpar qualquer entrada de critério e retornar ao estado padrão.

## <a name="evaluating-results"></a>Avaliar resultados

### <a name="policies-that-will-apply"></a>Políticas que serão aplicadas

Esta lista mostrará quais políticas de acesso condicional se aplicam de acordo com as condições. A lista incluirá os controles de concessão e de sessão que se aplicam. Os exemplos incluem exigir a autenticação multifator para acessar um aplicativo específico.

### <a name="policies-that-will-not-apply"></a>Políticas que não serão aplicadas

Esta lista mostrará políticas de Acesso Condicional que não se aplicarão se as condições forem aplicadas. A lista incluirá todas as políticas e o motivo pelo qual elas não se aplicam. Os exemplos incluem usuários e grupos que podem ser excluídos de uma política.

## <a name="use-case"></a>Caso de uso

Muitas organizações criam políticas com base em locais de rede, permitindo locais confiáveis e bloqueando locais onde o acesso não deve ocorrer.

Para validar que uma configuração foi feita adequadamente, um administrador pode usar a ferramenta de What If para imitar o acesso, de um local que deve ser permitido e de um local que deve ser negado.

[![Ferramenta de What If mostrando resultados com acesso de bloqueio](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png)](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png#lightbox)

Nessa instância, o usuário será impedido de acessar qualquer aplicativo de nuvem em sua viagem à Coreia do Norte, pois a Contoso bloqueou o acesso desse local.

Esse teste pode ser expandido para incorporar outros pontos de dados para restringir o escopo.

## <a name="next-steps"></a>Próximas etapas

* [O que é Acesso Condicional?](overview.md)
* [O que é Azure Active Directory Identity Protection?](../identity-protection/overview-identity-protection.md)
* [O que é uma identidade do dispositivo?](../devices/overview.md)
* [Como funciona: autenticação multifator do Azure AD](../authentication/concept-mfa-howitworks.md)