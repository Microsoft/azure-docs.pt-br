---
title: Guia estratégico do Azure Active Directory Identity Protection | Microsoft Docs
description: Saiba como o Azure AD Identity Protection permite limitar a capacidade de um invasor de explorar uma identidade ou um dispositivo comprometidos ou um dispositivo que sofreu comprometimento conhecido ou suspeito anteriormente.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 273a6aca2050676650b955ec078b47b2ffcfe319
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333935"
---
# <a name="azure-active-directory-identity-protection-playbook"></a>Guia estratégico do Azure Active Directory Identity Protection

Este guia estratégico vai ajudá-lo a:

* Popular dados no ambiente do Identity Protection, simulando eventos de riscos e vulnerabilidades
* Configurar políticas de acesso condicional com base em risco e testar o impacto dessas políticas

## <a name="simulating-risk-events"></a>Simulação de Eventos de Risco

Esta seção fornece as etapas para simular os seguintes tipos de evento de risco:

* Entradas de endereços IP anônimos (fácil)
* Entradas de locais desconhecidos (moderado)
* Viagem impossível a locais atípicos (difícil)

Outros eventos de risco não podem ser simulados de maneira segura.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Entradas de endereços IP anônimos

Para obter mais informações sobre esse evento de risco, consulte [Entradas de endereços IP anônimos](../reports-monitoring/concept-risk-events.md#sign-ins-from-anonymous-ip-addresses). 

A conclusão do procedimento a seguir requer que você use:

- O [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en) para simular endereços IP anônimos. Talvez você precise usar uma máquina virtual, caso sua organização restrinja o uso do navegador Tor.
- Uma conta de teste que ainda não está registrada para autenticação multifator.

**Para simular uma entrada de um IP anônimo, realize as seguintes etapas**:

1. Usando o [Navegador Tor](https://www.torproject.org/projects/torbrowser.html.en), navegue até [https://myapps.microsoft.com](https://myapps.microsoft.com).   
2. Insira as credenciais da conta que deseja exibir no relatório **Entradas de endereços IP anônimos** .

A entrada aparece no painel de segurança Identity Protection dentro de 10 a 15 minutos. 

### <a name="sign-ins-from-unfamiliar-locations"></a>Entradas de locais desconhecidos

Para obter mais informações sobre esse evento de risco, consulte [Entradas de locais desconhecidos](../reports-monitoring/concept-risk-events.md#sign-in-from-unfamiliar-locations). 

Para simular locais desconhecidos, você deve entrar a partir de um local e do dispositivo em que a sua conta de teste não tenha entrado anteriormente.

O procedimento abaixo usa um criado recentemente:

- Conexão VPN, para simular novo local.
- Máquina virtual, para simular um novo dispositivo.

A conclusão do procedimento a seguir requer que você use uma conta de usuário que tenha:

- Pelo menos um histórico entrada de 30 dias.
- Autenticação multifator habilitada.

**Para simular uma entrada de um local desconhecido, realize as seguintes etapas**:

1. Ao entrar com sua conta de teste, falhe o desafio MFA ao não passar pelo desafio MFA.
2. Usando sua nova VPN, navegue até [https://myapps.microsoft.com](https://myapps.microsoft.com) e insira as credenciais da sua conta de teste.

A entrada aparece no painel de segurança Identity Protection dentro de 10 a 15 minutos.

### <a name="impossible-travel-to-atypical-location"></a>Viagem impossível a um local atípico

Para obter mais informações sobre esse evento de risco, consulte [Viagem impossível para local atípico](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations). 

É difícil simular a condição de viagem impossível porque o algoritmo usa o aprendizado de máquina para eliminar falsos positivos, tais como viagens impossíveis de dispositivos conhecidos ou entradas de VPNs usadas por outros usuários no diretório. Além disso, o algoritmo requer um histórico de entrada de 14 dias e 10 logons do usuário, antes que ele comece a gerar eventos de risco. Por causa dos modelos do Machine Learning complexos e regras acima, há a possibilidade de que as etapas a seguir não potencialize um evento de risco. É conveniente replicar essas etapas em várias contas do Azure Active Directory para publicar esse evento de risco.

**Para simular uma viagem impossível para um local atípico, realize as seguintes etapas**:

1. Usando o navegador padrão, navegue até [ https://myapps.microsoft.com ](https://myapps.microsoft.com).  
2. Insira as credenciais da conta para a qual deseja gerar um evento de risco de viagem impossível.
3. Altere o agente do usuário. É possível alterar o agente do usuário no Internet Explorer nas Ferramentas de Desenvolvedor ou então no Firefox ou Chrome usando um complemento de alternador de agente do usuário.
4. Altere seu endereço IP. É possível alterar seu endereço IP usando uma VPN, um complemento do Tor ou criando um novo computador no Azure em um diferente.
5. Entre em [https://myapps.microsoft.com](https://myapps.microsoft.com) usando as mesmas credenciais de antes, alguns minutos após a entrada anterior.

A entrada aparece no painel de controle Identity Protection dentro de 2 a 4 horas.

## <a name="simulating-vulnerabilities"></a>Simulação de vulnerabilidades
Vulnerabilidades são pontos fracos no seu ambiente do Azure AD que podem ser explorados por um ator maligno. Atualmente, três tipos de vulnerabilidades são exibidas no Azure AD Identity Protection que aproveitam os outros recursos do Azure AD. Essas vulnerabilidades serão exibidas no painel do Identity Protection automaticamente depois que esses recursos forem configurados.

* Autenticação Multifator do [Microsoft Azure AD](../authentication/multi-factor-authentication.md)
* [Cloud Discovery](https://docs.microsoft.com/cloud-app-security/) do Azure AD.
* Azure AD [Privileged Identity Management](../privileged-identity-management/pim-configure.md). 

## <a name="testing-security-policies"></a>Testar as políticas de segurança

Esta seção fornece as etapas para testar o risco do usuário e a política de segurança de risco de entrada.

### <a name="user-risk-security-policy"></a>Política de segurança de risco do usuário

Para obter mais informações, consulte [Como configurar a política de risco do usuário](howto-user-risk-policy.md).

![Risco de usuário](./media/playbook/02.png "Manual")

**Para testar uma política de segurança de risco de usuário, execute as seguintes etapas**:

1. Entre em [https://portal.azure.com](https://portal.azure.com) com as credenciais de administrador global para seu locatário.
2. Navegue até **Identity Protection**. 
3. Na página **Azure AD Identity Protection**, clique em **Política de risco do usuário**.
4. Na seção**Atribuições**, selecione os usuários (e grupos) desejados e o nível de risco do usuário.

    ![Risco de usuário](./media/playbook/03.png "Manual")

5. Na seção Controles, selecione o Controle de Acesso desejado (por exemplo, Requerer mudança de senha).
5. Como **Impor Política**, selecione **Desativado**.
6. Eleve o risco de usuário de uma conta de teste, por exemplo, simulando um dos eventos de risco algumas vezes.
7. Aguarde alguns minutos e, em seguida, verifique se o nível do usuário para o usuário é Médio. Caso contrário, simule mais eventos de risco para o usuário.
8. Como **Impor Política**, selecione **Ativado**.
9. Agora você pode testar o acesso condicional baseado em risco do usuário entrando usando um usuário com um nível de risco elevado.

### <a name="sign-in-risk-security-policy"></a>Política de segurança de risco de entrada

Para obter mais informações, consulte [Como configurar a política de risco de entrada](howto-sign-in-risk-policy.md).

![Risco de entrada](./media/playbook/01.png "Manual")

**Para testar uma política de segurança de entrada, execute as seguintes etapas:**

1. Entre em [https://portal.azure.com](https://portal.azure.com) com as credenciais de administrador global para seu locatário.
2. Navegue para **Azure AD Identity Protection**.
3. Na página principal **Azure AD Identity Protection**, clique em **Política de entrada**. 
4. Na seção **Atribuições**, selecione os usuários desejados (e grupos) e o nível de risco de entrada.

    ![Risco de entrada](./media/playbook/04.png "Manual")

5. Na seção **Controles** selecione o controle de acesso desejado (por exemplo, **Requer autenticação multifator**). 
6. Como **Impor Política**, selecione **Ativado**.
7. Clique em **Salvar**.
8. Agora você pode testar o acesso condicional com base em risco de entrada, entrando usando uma sessão arriscada (por exemplo, usando o navegador Tor). 

## <a name="see-also"></a>Consulte também

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
