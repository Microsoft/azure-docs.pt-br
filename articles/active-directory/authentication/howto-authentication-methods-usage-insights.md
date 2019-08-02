---
title: Uso de métodos de autenticação & relatórios de insights (visualização) – Azure Active Directory
description: Relatórios sobre a senha de autoatendimento do Azure AD redefinição e o uso do método de autenticação de autenticação multifator
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0f6a74308f1bc4a7b77576fb9f39f965de0a4f8
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561037"
---
# <a name="authentication-methods-usage--insights-preview"></a>Uso de métodos de autenticação & insights (visualização)

Uso & insights permite que você entenda como os métodos de autenticação para recursos, como redefinição de senha de autoatendimento e autenticação multifator do Azure estão funcionando em sua organização. Essa funcionalidade de relatório oferece à sua organização os meios para compreender quais métodos estão sendo registrados e como eles estão sendo usados.

## <a name="permissions-and-licenses"></a>Permissões e licenças

As seguintes funções podem acessar o uso e insights:

- Administrador global
- Leitor de segurança
- Administrador de segurança
- Leitor de Relatórios

Nenhuma licença adicional é necessária para o uso de acesso e insights. Autenticação multifator do Azure e informações de licenciamento de SSPR (redefinição) de senha de autoatendimento podem ser encontradas na [Azure Active Directory, no site de preços](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="how-it-works"></a>Como ele funciona

Para acessar informações e o uso do método de autenticação:

1. Navegue até o [Portal do Azure](https://portal.azure.com).
1. Navegue até **Azure Active Directory** > **redefinição de senha** > **uso & insights**.
1. Dos **registro** ou **uso** visões gerais, que você pode optar por abrir o pré-filtrada relatórios para filtrar com base em suas necessidades.

![Visão geral de uso & insights](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

Para acessar diretamente os uso & insights, vá para [ https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade). Esse link levará você para a visão geral do registro.

Os usuários registraram, os usuários habilitados e blocos com capacidade dos usuários mostram os seguintes dados de registro para seus usuários:

- Registrado: Um usuário é considerado registrado se eles (ou um administrador) registrou suficiente métodos de autenticação para atender à política de autenticação multifator ou o SSPR de sua organização.
- Habilitado: Um usuário é considerado ativado se eles estão no escopo para a política SSPR. Se SSPR estiver habilitada para um grupo, em seguida, o usuário é considerado ativado se eles estiverem no grupo. Se o SSPR está habilitado para todos os usuários, em seguida, todos os usuários no locatário (exceto convidados) são considerados habilitado.
- Compatível com: Um usuário está sendo capaz de se elas são registradas e habilitadas. Esse status significa que eles podem executar SSPR a qualquer momento se necessário.

Clicar em qualquer um desses blocos ou as informações mostradas neles, você será levado a uma lista pré-filtrada de detalhes do registro.

O **registros** do gráfico na **registro** guia mostra o número de autenticação bem-sucedidas e com falha de registros do método pelo método de autenticação. O **redefine** do gráfico na **uso** guia mostra o número de êxito e fluxo de redefinição de autenticações com falha durante a senha pelo método de autenticação.

Clicar em qualquer um dos gráficos levam você a uma lista pré-filtrada de registro ou eventos de redefinição.

Usando o controle no canto superior direito, você pode alterar o intervalo de datas para os dados de auditoria mostrados nos gráficos de registros e redefine a 24 horas, 7 dias ou 30 dias.

Dados do registro das 

### <a name="registration-details"></a>Detalhes do registro

Clicar na **usuários registrados**, **usuários habilitados**, ou **usuários compatíveis com** blocos ou insights o levará para os detalhes do registro.

O relatório de registro de detalhes mostra as seguintes informações para cada usuário:

- NOME
- Nome de usuário
- Status do registro (todos, registrado, não registrado)
- Status de habilitado (todos, habilitado, não habilitada)
- Compatível com status (All, capacidade, não compatível com)
- Métodos (notificação de aplicativo, código do aplicativo, chamada telefônica, SMS, Email, perguntas de segurança)

Usando os controles na parte superior da lista, você pode pesquisar por um usuário e filtrar a lista de usuários de acordo com as colunas mostradas.

### <a name="reset-details"></a>Redefinir detalhes

Clicando em gráficos os registros ou redefine o levará até os detalhes de redefinição.

O relatório de redefinição de detalhes mostra os eventos de registro e redefinição dos últimos 30 dias incluindo:

- NOME
- Nome de usuário
- Recurso (todos, registro, redefinir)
- Método de autenticação (notificação de aplicativo, código do aplicativo, chamada telefônica, chamada de Office, SMS, Email, perguntas de segurança)
- Status (todos, sucesso, falha)

Usando os controles na parte superior da lista, você pode pesquisar por um usuário e filtrar a lista de usuários de acordo com as colunas mostradas.

## <a name="limitations"></a>Limitações

Os dados mostrados nesses relatórios serão atrasados até 60 minutos. Existe um campo de "Última atualização" no portal do Azure para identificar como recentes são de seus dados.

Dados de uso e insights não são uma substituição para os relatórios de atividade de autenticação multifator do Azure ou as informações contidas no relatório de entradas do Azure AD.

## <a name="next-steps"></a>Próximas etapas

- [Trabalhando com o relatório de uso de métodos de autenticação API](https://docs.microsoft.com/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [Escolher métodos de autenticação para sua organização](concept-authentication-methods.md)
- [Combinados a experiência de registro](concept-registration-mfa-sspr-combined.md)
