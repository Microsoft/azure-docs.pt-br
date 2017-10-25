---
title: "Autenticação de passagem do Azure AD – início rápido | Microsoft Docs"
description: "Este artigo descreve como começar a usar a autenticação de passagem do Azure AD (Azure Active Directory)."
services: active-directory
keywords: "Autenticação de Passagem do Azure AD Connect, instalar o Active Directory, componentes necessários para o Azure AD, SSO, Logon único"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: billmath
ms.openlocfilehash: 4f4fa884694dc8dad6349e3835e7c7ba2c4d2bdf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Autenticação de passagem do Azure Active Directory: início rápido

## <a name="how-to-deploy-azure-ad-pass-through-authentication"></a>Como implantar a autenticação de passagem do Azure AD

A autenticação de passagem do Azure AD (Azure Active Directory) permite que os usuários entrem em aplicativos locais e baseados em nuvem usando as mesmas senhas. Ela permite a entrada de usuários validando suas senhas diretamente no Active Directory local.

>[!IMPORTANT]
>Se você está usando esse recurso por meio da versão prévia, é necessário atualizar as versões prévias dos Agentes de autenticação usando as instruções fornecidas [aqui](./active-directory-aadconnect-pass-through-authentication-upgrade-preview-authentication-agents.md).

Você precisa seguir estas instruções para implantar a Autenticação de passagem:

## <a name="step-1-check-prerequisites"></a>Etapa 1: verificar pré-requisitos

Verifique se os seguintes pré-requisitos estão em vigor:

### <a name="on-the-azure-active-directory-admin-center"></a>No centro de administração do Azure Active Directory

1. Crie uma conta Administrador Global somente em nuvem no seu locatário do Azure AD. Dessa forma, você pode gerenciar a configuração do seu locatário caso seus serviços locais falhem ou fiquem indisponíveis. Saiba mais sobre [adicionar uma conta de Administrador Global somente de nuvem](../active-directory-users-create-azure-portal.md). Executar essa etapa é essencial para garantir que você não seja bloqueado de seu locatário.
2. Adicione um ou mais [nomes de domínio personalizados](../active-directory-add-domain.md) ao seu locatário do Azure AD. Os usuários entram usando um desses nomes de domínio.

### <a name="in-your-on-premises-environment"></a>Em seu ambiente local

1. Identifique um servidor que execute o Windows Server 2012 R2 ou posterior para executar o Azure AD Connect. Adicione o servidor à mesma floresta do AD como os usuários cujas senhas precisam ser validadas.
2. Instale a [última versão do Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) no servidor identificado na etapa anterior. Se o Azure AD Connect já está em execução, verifique se a versão é a 1.1.557.0 ou posterior.
3. Identifique um servidor adicional executando o Windows Server 2012 R2 ou posterior no qual o Agente de Autenticação autônomo será executado. A versão do Agente de autenticação precisa ser a 1.5.193.0 ou posterior. Este servidor é necessário para garantir a alta disponibilidade das solicitações de entrada. Adicione o servidor à mesma floresta do AD como os usuários cujas senhas precisam ser validadas.
4. Se houver um firewall entre os servidores e o Azure AD, será necessário configurar os seguintes itens:
   - Certifique-se de que os Agentes de Autenticação podem fazer solicitações de **saída** ao Azure AD sobre as seguintes portas:
   
   | Número da porta | Como ele é usado |
   | --- | --- |
   | **80** | Baixando as CRLs (listas de certificados revogados) ao validar o certificado SSL |
   | **443** | Toda a comunicação de saída com o nosso serviço |
   
   Se o firewall impõe as regras de acordo com os usuários originadores, abra essas portas para o tráfego proveniente dos serviços do Windows que são executados como um serviço de rede.
   - Se o seu firewall ou proxy permitirem lista de permissões de DNS, adicione as conexões a **\*msappproxy.net** e **\*servicebus.windows.net** à lista de permissões. Caso contrário, permita acesso aos [Intervalos de IP do DataCenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653), que são atualizados semanalmente.
   - Os seus Agentes de Autenticação devem acessar **login.windows.net** e **login.microsoftonline.net** para o registro inicial, portanto, abra seu firewall para essas URLs também.
   - Para validação de certificado, desbloqueie as seguintes URLs: **mscrl.microsoft.com:80**, **crl.microsoft.com:80**, **ocsp.msocsp.com:80** e **www.microsoft.com:80**. Essas URLs são usadas para a validação de certificado com outros produtos da Microsoft, então você talvez já tenha essas URLs desbloqueadas.

## <a name="step-2-enable-exchange-activesync-support-optional"></a>Etapa 2: Habilitar o suporte do Exchange ActiveSync (opcional)

Siga estas instruções para habilitar o suporte do Exchange ActiveSync:

1. Use o [PowerShell do Exchange](https://technet.microsoft.com/library/mt587043(v=exchg.150).aspx) para executar o comando a seguir:
```
Get-OrganizationConfig | fl per*
```

2. Verifique o valor da configuração `PerTenantSwitchToESTSEnabled`. Se o valor for **true**, seu locatário está configurado corretamente - esse é geralmente o caso para a maioria dos clientes. Se o valor for **false**, execute o seguinte comando:
```
Set-OrganizationConfig -PerTenantSwitchToESTSEnabled:$true
```

3. Verifique se o valor da configuração `PerTenantSwitchToESTSEnabled` agora está definida como **true**. Aguarde uma hora antes de passar para a próxima etapa.

Se você enfrentar problemas durante esta etapa, verifique nosso [guia de solução de problemas](active-directory-aadconnect-troubleshoot-pass-through-authentication.md#exchange-activesync-configuration-issues) para obter mais informações.

## <a name="step-3-enable-the-feature"></a>Etapa 3: Habilitar o recurso

A autenticação de passagem pode ser habilitada usando o [Azure AD Connect](active-directory-aadconnect.md).

>[!IMPORTANT]
>A autenticação de passagem pode ser habilitada no servidor primário ou de preparo do Azure AD Connect. É altamente recomendável habilitá-la do servidor primário.

Se você estiver instalando o Azure AD Connect pela primeira vez, escolha o [caminho de instalação personalizado](active-directory-aadconnect-get-started-custom.md). Na página **Entrada de usuário** escolha **Autenticação de Passagem** como o método de logon. Após a conclusão bem-sucedida, um Agente de Autenticação de passagem estará instalado no mesmo servidor do Azure AD Connect. Além disso, o recurso de autenticação de passagem estará habilitado em seu locatário.

![Azure AD Connect – conexão do usuário](./media/active-directory-aadconnect-sso/sso3.png)

Se você já tiver instalado o Azure AD Connect (usando o caminho de [instalação expressa](active-directory-aadconnect-get-started-express.md) ou de [instalação personalizada](active-directory-aadconnect-get-started-custom.md)), selecione **Alterar página de entrada do usuário** no Azure AD Connect e clique em **Avançar**. Em seguida, selecione **Autenticação de passagem** como o método de logon. Após a conclusão bem-sucedida, um Agente de Autenticação de passagem estará instalado no mesmo servidor que o Azure AD Connect e o recurso estará habilitado em seu locatário.

![Azure AD Connect – Alterar entrada do usuário](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

>[!IMPORTANT]
>A Autenticação de Passagem é um recurso no nível do locatário. A ativação desse recurso afeta a entrada de usuários em _todos_ os domínios gerenciados no seu locatário. Se estiver alternando do AD FS para Autenticação de Passagem, recomendamos que você aguarde pelo menos 12 horas antes de desligar a infraestrutura do AD FS - esse tempo de espera é para garantir que os usuários podem continuar entrando no Exchange ActiveSync durante a transição.

## <a name="step-4-test-the-feature"></a>Etapa 4: testar o recurso

Siga estas instruções para verificar se você habilitou a Autenticação de passagem corretamente:

1. Entre no [centro de administração do Azure Active Directory](https://aad.portal.azure.com) com as credenciais do Administrador Global do seu locatário.
2. Selecione **Azure Active Directory** na navegação à esquerda.
3. Selecione **Azure AD Connect**.
4. Verifique se o recurso **Autenticação de passagem** aparece como **Habilitado**.
5. Selecione **Autenticação de passagem**. Essa folha lista os servidores em que os Agentes de autenticação estão instalados.

![Centro de administração do Azure Active Directory - folha Azure AD Connect](./media/active-directory-aadconnect-pass-through-authentication/pta7.png)

![Centro de administração do Azure Active Directory - folha Autenticação de Passagem](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

Nesse momento, os usuários de todos os domínios gerenciados no seu locatário podem entrar usando a Autenticação de passagem. No entanto, os usuários de domínios federados continuam a entrar usando o Serviços de Federação do Active Directory (AD FS) ou outro provedor de federação que já esteja configurado. Se você converter um domínio de federado para gerenciado, todos os usuários nesse domínio passarão automaticamente a entrar usando a autenticação de passagem. Os usuários somente em nuvem não são afetados pelo recurso de autenticação de passagem.

## <a name="step-5-ensure-high-availability"></a>Etapa 5: Verificar a alta disponibilidade

Se você planeja implantar autenticação de passagem em um ambiente de produção, instale um Agente de Autenticação autônomo. Instale esse segundo Agente de Autenticação em um servidor _que não seja_ o que está executando o Azure AD Connect e o primeiro Agente de Autenticação. Esta instalação fornece alta disponibilidade para solicitações de entrada. Siga estas instruções para implantar um Agente de Autenticação autônomo:

1. **Baixe a versão mais recente do Agente de Autenticação (versões 1.5.193.0 ou posteriores)**: entre no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) com as credenciais de Administrador Global do seu locatário.
2. Selecione **Azure Active Directory** na navegação à esquerda.
3. Selecione **Azure AD Connect** e, em seguida, **Autenticação de passagem**. E selecione **Baixar agente**.
4. Clique no botão **Aceitar termos e baixar**.
5. **Instalar a versão mais recente do Agente de Autenticação**: Execute o executável baixado na etapa anterior. Forneça as suas credenciais de Administrador Global do locatário quando solicitado.

![Centro de administração do Azure Active Directory - botão de Baixar Autenticação de Passagem](./media/active-directory-aadconnect-pass-through-authentication/pta9.png)

![Centro de administração do Azure Active Directory - folha para Baixar Agente](./media/active-directory-aadconnect-pass-through-authentication/pta10.png)

>[!NOTE]
>Você também pode baixar o Agente de Autenticação [aqui](https://aka.ms/getauthagent). Certifique-se de ler e aceitar os [Termos de Serviço](https://aka.ms/authagenteula) do Agente de Autenticação _antes_ de instalá-lo.

## <a name="next-steps"></a>Próximas etapas
- [**Bloqueio Inteligente**](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) – configura a capacidade de Bloqueio Inteligente no seu locatário para proteger as contas de usuário.
- [**Limitações atuais**](active-directory-aadconnect-pass-through-authentication-current-limitations.md) - Saiba quais cenários têm suporte e quais não têm.
- [**Aprofundamento técnico**](active-directory-aadconnect-pass-through-authentication-how-it-works.md) – entenda como esse recurso funciona.
- [**Perguntas frequentes**](active-directory-aadconnect-pass-through-authentication-faq.md) – respostas para perguntas frequentes.
- [**Solução de problemas**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) – Saiba como resolver problemas comuns do recurso.
- [**Aprofundamento em Segurança**](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) – informações técnicas aprofundadas adicionais sobre o recurso.
- [**SSO contínuo do Azure AD**](active-directory-aadconnect-sso.md) – Saiba mais sobre esse recurso complementar.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – para registrar solicitações de novos recursos.
