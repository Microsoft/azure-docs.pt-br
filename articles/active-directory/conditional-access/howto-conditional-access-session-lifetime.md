---
title: Configurar o gerenciamento de sessão de autenticação-Azure Active Directory
description: Personalize a configuração da sessão de autenticação do Azure AD, incluindo a frequência de entrada do usuário e a persistência da sessão do navegador.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/23/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6116ab543d6dfc886e44206c2a60e4456b39fbc9
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102558178"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>Configurar o gerenciamento de sessão de autenticação com acesso condicional

Em implantações complexas, as organizações podem ter a necessidade de restringir as sessões de autenticação. Alguns cenários podem incluir:

* Acesso a recursos de um dispositivo não gerenciado ou compartilhado
* Acesso a informações confidenciais de uma rede externa
* Usuários com alto impacto
* Aplicativos de negócios críticos

Os controles de acesso condicional permitem que você crie políticas que se destinam a casos de uso específicos em sua organização sem afetar todos os usuários.

Antes de mergulhar em detalhes sobre como configurar a política, vamos examinar a configuração padrão.

## <a name="user-sign-in-frequency"></a>Frequência de entrada do usuário

A frequência de entrada define o período de tempo antes que um usuário seja solicitado a entrar novamente ao tentar acessar um recurso.

A configuração padrão do Azure Active Directory (Azure AD) para a frequência de entrada do usuário é uma janela sem interrupção de 90 dias. Solicitar aos usuários que as credenciais geralmente pareça uma coisa sensata a ser feita, mas pode ser revelado: os usuários treinados para inserir suas credenciais sem pensar podem fornecê-las involuntariamente a um prompt de credencial mal-intencionado.

Pode parecer que o alarme não pede que um usuário entre novamente, na realidade, qualquer violação das políticas de ti revogará a sessão. Alguns exemplos incluem (mas não estão limitados a) uma alteração de senha, um dispositivo incompatível ou uma conta desabilitada. Você também pode [revogar explicitamente as sessões de usuários usando o PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken). A configuração padrão do Azure AD é "não pedir que os usuários forneçam suas credenciais se a postura de segurança de suas sessões não tiver mudado".

A configuração frequência de entrada funciona com aplicativos que implementaram protocolos OAUTH2 ou OIDC de acordo com os padrões. A maioria dos aplicativos nativos da Microsoft para Windows, Mac e Mobile, incluindo os seguintes aplicativos Web, estão em conformidade com a configuração.

- Word, Excel, PowerPoint online
- OneNote online
- Office.com
- Portal de administração do Microsoft 365
- Exchange Online
- SharePoint e OneDrive
- Cliente Web de equipes
- Dynamics CRM Online
- Portal do Azure

A configuração frequência de entrada também funciona com aplicativos SAML, desde que eles não descartam seus próprios cookies e sejam redirecionados para o Azure AD para autenticação regularmente.

### <a name="user-sign-in-frequency-and-multi-factor-authentication"></a>Frequência de entrada do usuário e autenticação multifator

Frequência de entrada aplicada anteriormente somente à autenticação de primeiro fator em dispositivos que eram ingressados no Azure AD, ingressado no Azure AD híbrido e registro do Azure AD. Não havia uma maneira fácil para nossos clientes reimporem a MFA (autenticação multifator) nesses dispositivos. Com base nos comentários dos clientes, a frequência de entrada também se aplicará ao MFA.

[![Frequência de entrada e MFA](media/howto-conditional-access-session-lifetime/conditional-access-flow-chart-small.png)](media/howto-conditional-access-session-lifetime/conditional-access-flow-chart.png#lightbox)

### <a name="user-sign-in-frequency-and-device-identities"></a>Frequência de entrada do usuário e identidades do dispositivo

Se você tiver ingressado no Azure AD, ingressado no Azure AD híbrido ou dispositivos registrados no Azure AD, quando um usuário desbloquear seu dispositivo ou entrar de forma interativa, esse evento também atenderá à política de frequência de entrada. Nos dois exemplos a seguir, a frequência de entrada do usuário é definida como 1 hora:

Exemplo 1:

- Em 00:00, um usuário entra em seu dispositivo ingressado no Azure AD do Windows 10 e inicia o trabalho em um documento armazenado no SharePoint Online.
- O usuário continua trabalhando no mesmo documento em seu dispositivo por uma hora.
- Em 01:00, o usuário é solicitado a entrar novamente com base no requisito de frequência de entrada na política de acesso condicional configurada pelo administrador.

Exemplo 2:

- Em 00:00, um usuário entra em seu dispositivo ingressado no Azure AD do Windows 10 e inicia o trabalho em um documento armazenado no SharePoint Online.
- Às 00:30, o usuário Obtém e faz um bloqueio de interrupção de seu dispositivo.
- Em 00:45, o usuário retorna de sua quebra e desbloqueia o dispositivo.
- Em 01:45, o usuário é solicitado a entrar novamente com base no requisito de frequência de entrada na política de acesso condicional configurada pelo administrador desde que a última entrada ocorreu às 00:45.

## <a name="persistence-of-browsing-sessions"></a>Persistência de sessões de navegação

Uma sessão persistente do navegador permite que os usuários permaneçam conectados após fechar e reabrir a janela do navegador.

O padrão do Azure AD para persistência de sessão de navegador permite que os usuários em dispositivos pessoais escolham se deseja manter a sessão mostrando uma "permanecer conectado?" avisar após a autenticação bem-sucedida. Se a persistência do navegador estiver configurada no AD FS usando as diretrizes no artigo [AD FS configurações de Sign-On único](/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
), vamos obedecer a essa política e persistir a sessão do Azure ad também. Você também pode configurar se os usuários em seu locatário veem o "permanecer conectado?" Solicite a alteração da configuração apropriada no painel de identidade visual da empresa em portal do Azure usando as diretrizes no artigo [personalizar sua página de entrada do Azure ad](../fundamentals/customize-branding.md).

## <a name="configuring-authentication-session-controls"></a>Configurando controles de sessão de autenticação

O acesso condicional é um recurso Azure AD Premium e requer uma licença Premium. Se você quiser saber mais sobre o acesso condicional, consulte [o que é o acesso condicional no Azure Active Directory?](overview.md#license-requirements)

> [!WARNING]
> Se você estiver usando o recurso de [tempo de vida de token configurável](../develop/active-directory-configurable-token-lifetimes.md) atualmente em visualização pública, observe que não há suporte para a criação de duas políticas diferentes para a mesma combinação de usuário ou aplicativo: uma com esse recurso e outra com o recurso de tempo de vida de token configurável. A Microsoft desativou o recurso de tempo de vida de token configurável para atualização e tempos de vida de token de sessão em 30 de janeiro de 2021 e substituído pelo recurso de gerenciamento de sessão de autenticação de acesso condicional.  
>
> Antes de habilitar a frequência de entrada, verifique se outras configurações de reautenticação estão desabilitadas em seu locatário. Se "lembrar MFA em dispositivos confiáveis" estiver habilitado, certifique-se de desabilitá-lo antes de usar a frequência de entrada, pois usar essas duas configurações em conjunto pode levar à solicitação de usuários inesperadamente. Para saber mais sobre prompts de reautenticação e tempo de vida da sessão, consulte o artigo [otimizar prompts de reautenticação e entender o tempo de vida da sessão para a autenticação multifator do Azure ad](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

### <a name="policy-1-sign-in-frequency-control"></a>Política 1: controle de frequência de entrada

1. Criar política
1. Escolha todas as condições necessárias para o ambiente do cliente, incluindo os aplicativos de nuvem de destino.

   > [!NOTE]
   > É recomendável definir a frequência de prompt de autenticação igual para aplicativos de Microsoft Office de chave, como o Exchange Online e o SharePoint Online para melhor experiência do usuário.

1. Acesse a sessão de **controles**  >   de acesso e clique em **frequência de entrada**
1. Insira o valor necessário de dias e horas na primeira caixa de texto
1. Selecione um valor de **horas** ou **dias** no menu suspenso
1. Salvar sua política

![Política de acesso condicional configurada para frequência de entrada](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

No Azure AD, os dispositivos registrados do Windows entram no dispositivo é considerado um prompt. Por exemplo, se você tiver configurado a frequência de entrada para 24 horas para aplicativos do Office, os usuários em dispositivos Windows registrados no Azure AD atenderão à política de frequência de entrada entrando no dispositivo e não serão solicitados novamente ao abrir aplicativos do Office.

### <a name="policy-2-persistent-browser-session"></a>Política 2: sessão persistente do navegador

1. Criar política
1. Escolha todas as condições necessárias.

   > [!NOTE]
   > Observe que esse controle exige que você escolha "todos os aplicativos de nuvem" como uma condição. A persistência da sessão do navegador é controlada pelo token da sessão de autenticação. Todas as guias em uma sessão de navegador compartilham um único token de sessão e, portanto, todos precisam compartilhar o estado de persistência.

1. Acesse a sessão de **controles de acesso**  >   e clique em **sessão de navegador persistente**
1. Selecione um valor na lista suspensa
1. Salvar política

![Política de acesso condicional configurada para navegador persistente](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> A configuração de sessão persistente do navegador no acesso condicional do Azure AD substituirá a "continuar conectado?" configuração no painel de identidade visual da empresa no portal do Azure para o mesmo usuário se você tiver configurado ambas as políticas.

## <a name="validation"></a>Validação

Use a ferramenta What-If para simular um logon do usuário para o aplicativo de destino e outras condições com base em como você configurou sua política. Os controles de gerenciamento de sessão de autenticação aparecem no resultado da ferramenta.

![Resultados da ferramenta de What If de acesso condicional](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Implantação de política

Para garantir que sua política funcione conforme o esperado, a melhor prática é testá-la antes de distribuí-la em produção. O ideal é usar um locatário de teste para verificar se a nova política funciona conforme o esperado. Para obter mais informações, consulte o artigo [planejar uma implantação de acesso condicional](plan-conditional-access.md).

## <a name="next-steps"></a>Próximas etapas

* Se você estiver pronto para configurar políticas de acesso condicional para seu ambiente, consulte o artigo [planejar uma implantação de acesso condicional](plan-conditional-access.md).