---
title: Atividade dos métodos de autenticação-Azure Active Directory
description: Visão geral dos métodos de autenticação que os usuários registram para entrar e redefinir senhas.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/16/2021
ms.author: justinha
author: sopand
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a1cb71917fdb30ffccda21bedffe1c7f2a428c1
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105557948"
---
# <a name="authentication-methods-activity"></a>Atividade de métodos de autenticação 

O painel de atividade novos métodos de autenticação permite que os administradores monitorem o registro e o uso do método de autenticação em sua organização. Essa funcionalidade de relatório fornece à sua organização os meios para entender quais métodos estão sendo registrados e como eles estão sendo usados.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="permissions-and-licenses"></a>Permissões e licenças

As funções internas e personalizadas com as seguintes permissões podem acessar a folha de atividade de métodos de autenticação e as APIs:

- Microsoft. Directory/auditLogs/myproperties/Read
- Microsoft. Directory/signInReports/myproperties/Read

As funções a seguir têm as permissões necessárias:

- Leitor de Relatórios
- Leitor de segurança
- Leitor global
- Operador de segurança
- Administrador de Segurança
- Administrador Global

 Uma licença Azure AD Premium P1 ou P2 é necessária para acessar o uso e as informações. As informações de licenciamento de autenticação multifator do Azure AD e SSPR (redefinição de senha de autoatendimento) podem ser encontradas no [site de preços Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="how-it-works"></a>Como ele funciona

Para acessar o uso e as informações do método de autenticação:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Clique **Azure Active Directory**  >    >  **atividade métodos de autenticação** de segurança  >  .
1. Há duas guias no relatório: **registro** e **uso**.

   ![Visão geral da atividade dos métodos de autenticação](media/how-to-authentication-methods-usage-insights/registration-usage-tabs.png)

## <a name="registration-details"></a>Detalhes do registro

Você pode acessar a [**guia registro**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade) para mostrar o número de usuários com capacidade de autenticação multifator, autenticação passowordless e redefinição de senha de autoatendimento. 

Clique em qualquer uma das opções a seguir para filtrar previamente uma lista de detalhes de registro do usuário:

- **Os usuários capazes de autenticação multifator do Azure** mostram a divisão de usuários que são ambos:
  - Registrado para um método de autenticação forte 
  - Habilitado pela política para usar esse método para MFA 
  
  Esse número não reflete os usuários registrados para MFA fora do Azure AD. 
- **Os usuários** que têm a autenticação sem senha mostram a divisão dos usuários que estão registrados para entrar com uma senha usando o FIDO2, o Windows Hello para empresas ou a entrada por telefone com senha com o aplicativo Microsoft Authenticator. 
- **Os usuários com capacidade de redefinição de senha de autoatendimento** mostram a divisão dos usuários que podem redefinir suas senhas. Os usuários podem redefinir sua senha se forem ambas:
  - Registrado para métodos suficientes para satisfazer a política de sua organização para redefinição de senha de autoatendimento 
  - Habilitado para redefinir sua senha 

  ![Captura de tela de usuários que podem se registrar](media/how-to-authentication-methods-usage-insights/users-capable.png)

**Os usuários registrados por método de autenticação** mostram quantos usuários estão registrados para cada método de autenticação. Clique em um método de autenticação para ver quem está registrado para esse método.

![Captura de tela de usuários registrados](media/how-to-authentication-methods-usage-insights/users-registered.png)

**Registro recente por método de autenticação** mostra quantos registros tiveram êxito e falharam, classificados por método de autenticação. Clique em um método de autenticação para ver eventos de registro recentes para esse método.

![Captura de tela de registrado recentemente](media/how-to-authentication-methods-usage-insights/recently-registered.png)

## <a name="usage-details"></a>Detalhes de uso

O relatório **uso** mostra quais métodos de autenticação são usados para entrar e redefinir senhas.

![Captura de tela da página de uso](media/how-to-authentication-methods-usage-insights/usage-page.png)

As **entradas por requisito de autenticação** mostram o número de entradas interativas de usuário bem-sucedidas que eram necessárias para a autenticação de fator único versus multifator no Azure AD. Entradas em que a MFA foi imposta por um provedor de MFA de terceiros não está incluída.

![Captura de tela de entradas por requisito de autenticação](media/how-to-authentication-methods-usage-insights/sign-ins-protected.png)

As **entradas por método de autenticação** mostram o número de entradas interativas do usuário (êxito e falha) por método de autenticação usado. Ele não inclui entradas em que o requisito de autenticação foi atendido por uma declaração no token.

![Captura de tela de entradas por método](media/how-to-authentication-methods-usage-insights/sign-ins-by-method.png)

**Número de redefinições de senha e desbloqueios de conta** mostra o número de alterações de senha e redefinições de senha com êxito (autoatendimento e administrador) ao longo do tempo.

![Captura de tela de redefinições e desbloqueios](media/how-to-authentication-methods-usage-insights/password-changes.png)

**Redefinições de senha por método de autenticação** mostra o número de autenticações bem-sucedidas e com falha durante o método de autenticação do fluxo de redefinição de senha.

![Captura de tela de redefinições por método](media/how-to-authentication-methods-usage-insights/resets-by-method.png)

## <a name="user-registration-details"></a>Detalhes de registro do usuário 

Usando os controles na parte superior da lista, você pode pesquisar um usuário e filtrar a lista de usuários com base nas colunas mostradas.

O relatório detalhes do registro mostra as seguintes informações para cada usuário:

- Nome UPN
- Name
- Compatível com MFA (capaz, sem capacidade)
- Com capacidade de senha (capaz, sem capacidade)
- SSPR registrado (registrado, não registrado)
- SSPR habilitado (habilitado, não habilitado)
- Compatível com SSPR (capaz, sem capacidade) 
- Métodos registrados (email, telefone celular, telefone celular alternativo, telefone comercial, Microsoft Authenticator Push, senha de um software uma vez, FIDO2, chave de segurança, perguntas de segurança)

  ![Captura de tela dos detalhes de registro do usuário](media/how-to-authentication-methods-usage-insights/registration-details.png)

## <a name="registration-and-reset-events"></a>Eventos de registro e redefinição 

**Eventos de registro e redefinição** mostram os eventos de registro e redefinição das últimas 24 horas, dos últimos sete dias ou dos últimos 30 dias, incluindo:

- Data
- Nome de usuário
- Usuário 
- Recurso (registro, redefinição)
- Método usado (notificação do aplicativo, código do aplicativo, chamada telefônica, chamada do Office, chamada móvel alternativa, SMS, email, perguntas de segurança)
- Status (êxito, falha)
- Motivo da falha (explicação)

  ![Captura de tela de eventos de registro e redefinição](media/how-to-authentication-methods-usage-insights/registration-and-reset-logs.png)

## <a name="limitations"></a>Limitações

- Os dados no relatório não são atualizados em tempo real e podem refletir uma latência de até algumas horas.
- Os registros de aprovação de acesso temporário não são refletidos na guia registro do relatório, pois eles só são válidos por um curto período de tempo.
- Os métodos **PhoneAppNotification** ou **PhoneAppOTP** que um usuário pode ter configurado não são exibidos no painel. 

## <a name="next-steps"></a>Próximas etapas

- [Trabalhando com a API de relatório de uso dos métodos de autenticação](/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [Escolhendo métodos de autenticação para sua organização](concept-authentication-methods.md)
- [Experiência de registro combinada](concept-registration-mfa-sspr-combined.md)
