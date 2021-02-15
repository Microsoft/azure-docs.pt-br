---
title: Simulando as detecções de risco no Azure AD Identity Protection
description: Saiba como simular as detecções de riscos na proteção de identidade
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7eafeaf59757fcda978fa89b4bc2f9882b769e48
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94835893"
---
# <a name="simulating-risk-detections-in-identity-protection"></a>Simulando detecções de riscos na proteção de identidades

Os administradores podem querer simular riscos em seu ambiente para realizar os seguintes itens:

- Preencha os dados no ambiente de proteção de identidade, simulando as detecções e as vulnerabilidades de risco.
- Configure políticas de acesso condicional com base em risco e teste o impacto dessas políticas.

Este artigo fornece etapas para simular os seguintes tipos de detecção de risco:

- Endereço IP anônimo (fácil)
- Propriedades de entrada desconhecidas (moderada)
- Viagem atípicos (difícil)

Outras detecções de risco não podem ser simuladas de maneira segura.

Mais informações sobre cada detecção de risco podem ser encontradas no artigo [o que é risco](concept-identity-protection-risks.md).

## <a name="anonymous-ip-address"></a>Endereço IP anônimo

A conclusão do procedimento a seguir requer que você use:

- O [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en) para simular endereços IP anônimos. Talvez você precise usar uma máquina virtual, caso sua organização restrinja o uso do navegador Tor.
- Uma conta de teste que ainda não está registrada para a autenticação multifator do Azure AD.

**Para simular uma entrada de um IP anônimo, realize as seguintes etapas**:

1. Usando o [Navegador Tor](https://www.torproject.org/projects/torbrowser.html.en), navegue até [https://myapps.microsoft.com](https://myapps.microsoft.com).   
2. Insira as credenciais da conta que deseja exibir no relatório **Entradas de endereços IP anônimos** .

A entrada aparece no painel de segurança Identity Protection dentro de 10 a 15 minutos. 

## <a name="unfamiliar-sign-in-properties"></a>Propriedades de entrada desconhecidas

Para simular locais desconhecidos, você deve entrar a partir de um local e do dispositivo em que a sua conta de teste não tenha entrado anteriormente.

O procedimento abaixo usa um criado recentemente:

- Conexão VPN, para simular novo local.
- Máquina virtual, para simular um novo dispositivo.

A conclusão do procedimento a seguir requer que você use uma conta de usuário que tenha:

- Pelo menos um histórico entrada de 30 dias.
- Autenticação multifator do Azure AD habilitada.

**Para simular uma entrada de um local desconhecido, realize as seguintes etapas**:

1. Ao entrar com sua conta de teste, reprovar o desafio da autenticação multifator (MFA) não passando o desafio de MFA.
2. Usando sua nova VPN, navegue até [https://myapps.microsoft.com](https://myapps.microsoft.com) e insira as credenciais da sua conta de teste.

A entrada aparece no painel de segurança Identity Protection dentro de 10 a 15 minutos.

## <a name="atypical-travel"></a>Viagem atípica

A simulação da condição de viagem atípicos é difícil porque o algoritmo usa o aprendizado de máquina para eliminar falsos positivos, como o atípicos Travel de dispositivos conhecidos, ou entradas de VPNs que são usadas por outros usuários no diretório. Além disso, o algoritmo requer um histórico de entrada de 14 dias e 10 logons do usuário antes de começar a gerar as detecções de risco. Devido aos modelos complexos de aprendizado de máquina e às regras acima, há a possibilidade de que as etapas a seguir não levem a uma detecção de risco. Talvez você queira replicar essas etapas para várias contas do Azure AD para simular essa detecção.

**Para simular uma detecção de risco do atípicos Travel, execute as seguintes etapas**:

1. Usando seu navegador padrão, navegue até [https://myapps.microsoft.com](https://myapps.microsoft.com) .  
2. Insira as credenciais da conta para a qual você deseja gerar uma detecção de risco de viagem de atípicos.
3. Altere o agente do usuário. Você pode alterar o agente do usuário no Microsoft Edge de Ferramentas para Desenvolvedores (F12).
4. Altere seu endereço IP. Você pode alterar seu endereço IP usando uma VPN, um complemento do Tor ou criando uma nova máquina virtual no Azure em um data center diferente.
5. Entre no [https://myapps.microsoft.com](https://myapps.microsoft.com) usando as mesmas credenciais de antes e em alguns minutos após a entrada anterior.

A entrada aparece no painel de controle Identity Protection dentro de 2 a 4 horas.

## <a name="testing-risk-policies"></a>Testando políticas de risco

Esta seção fornece etapas para testar o usuário e as políticas de risco de entrada criadas no artigo [como: configurar e habilitar políticas de risco](howto-identity-protection-configure-risk-policies.md).

### <a name="user-risk-policy"></a>Política de risco do usuário

Para testar uma política de segurança de risco de usuário, execute as seguintes etapas:

1. Navegue até o [Portal do Azure](https://portal.azure.com).
1. Navegue até **Azure Active Directory**  >  **Security**  >  **visão geral** de segurança.
1. Selecione **Configurar política de risco do usuário**.
   1. Em **atribuições**
      1. **Usuários** – escolha **todos os usuários** ou **selecione indivíduos e grupos** se limitar a distribuição.
         1. Opcionalmente, você pode optar por excluir usuários da política.
      1. **Condições**  -  do **Risco do usuário** A recomendação da Microsoft é definir essa opção como **alta**.
   1. Em **controles**
      1. **Acesso** -a recomendação da Microsoft é **permitir o acesso** e **exigir alteração de senha**.
   1. **Impor política**  -  **Desativado**
   1. **Salvar** – esta ação retornará à página **visão geral** .
1. Eleve o risco do usuário de uma conta de teste, por exemplo, simulando uma das detecções de risco algumas vezes.
1. Aguarde alguns minutos e verifique se o risco foi elevado para o usuário. Caso contrário, simule mais detecções de risco para o usuário.
1. Retorne à sua política de risco e defina **aplicar política** como **ativado** e **salve** a alteração da política.
1. Agora você pode testar o acesso condicional baseado em risco do usuário entrando usando um usuário com um nível de risco elevado.

### <a name="sign-in-risk-security-policy"></a>Política de segurança de risco de entrada

Para testar uma política de segurança de entrada, execute as seguintes etapas:

1. Navegue até o [Portal do Azure](https://portal.azure.com).
1. Navegue até **Azure Active Directory**  >  **Security**  >  **visão geral** de segurança.
1. Selecione **Configurar política de risco de entrada**.
   1. Em **atribuições**
      1. **Usuários** – escolha **todos os usuários** ou **selecione indivíduos e grupos** se limitar a distribuição.
         1. Opcionalmente, você pode optar por excluir usuários da política.
      1. **Condições**  -  do **Risco de entrada** A recomendação da Microsoft é definir essa opção como **média e posterior**.
   1. Em **controles**
      1. **Acesso** -a recomendação da Microsoft é **permitir o acesso** e **exigir a autenticação multifator**.
   1. **Impor política**  -  **Em**
   1. **Salvar** – esta ação retornará à página **visão geral** .
1. Agora você pode testar o acesso condicional com base em risco de entrada, entrando usando uma sessão arriscada (por exemplo, usando o navegador Tor). 

## <a name="next-steps"></a>Próximas etapas

- [O que é risco?](concept-identity-protection-risks.md)

- [Como configurar e habilitar políticas de risco](howto-identity-protection-configure-risk-policies.md)

- [Azure Active Directory Identity Protection](overview-identity-protection.md)
