---
title: "Guia estratégico do Azure Active Directory Identity Protection | Microsoft Docs"
description: Saiba como o Azure AD Identity Protection permite limitar a capacidade de um invasor de explorar uma identidade ou um dispositivo comprometidos ou um dispositivo que sofreu comprometimento conhecido ou suspeito anteriormente.
services: active-directory
keywords: "azure active directory identity protection, cloud app discovery, gerenciamento de aplicativos, segurança, risco, nível de risco, vulnerabilidade, política de segurança"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 60836abf-f0e9-459d-b344-8e06b8341d25
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 2ecd07faed785fa6aa179ac1cca35a70d965e1dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-identity-protection-playbook"></a>Guia estratégico do Azure Active Directory Identity Protection
Este guia estratégico vai ajudá-lo a:

* Popular dados no ambiente do Identity Protection, simulando eventos de riscos e vulnerabilidades
* Configurar políticas de acesso condicional baseadas em risco e testar o impacto dessas políticas

## <a name="simulating-risk-events"></a>Simulação de Eventos de Risco
Esta seção fornece as etapas para simular os seguintes tipos de evento de risco:

* Entradas de endereços IP anônimos (fácil)
* Entradas de locais desconhecidos (moderado)
* Viagem impossível a locais atípicos (difícil)

Outros eventos de risco não podem ser simulados de maneira segura.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Entradas de endereços IP anônimos
Esse tipo de evento de risco identifica os usuários que entraram com êxito de um endereço IP que foi identificado como um endereço IP de proxy anônimo. Esses proxies geralmente são usados por usuários que desejam ocultar o endereço IP de seu dispositivo e podem ser usados com objetivos mal-intencionados.

**Para simular uma entrada de um IP anônimo, realize as seguintes etapas**:

1. Baixe o [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en).
2. Usando o Tor Browser, navegue até [https://myapps.microsoft.com](https://myapps.microsoft.com).   
3. Insira as credenciais da conta que deseja exibir no relatório **Entradas de endereços IP anônimos** .

A entrada será exibida no painel do Identity Protection dentro de 5 minutos. 

### <a name="sign-ins-from-unfamiliar-locations"></a>Entradas de locais desconhecidos
O risco de locais desconhecidos é um mecanismo de avaliação de entrada em tempo real que considera locais de entrada anteriores (IP, Latitude/Longitude e ASN) para determinar os locais novos/desconhecidos. O sistema armazena IPs, Latitude/Longitude e ASNs anteriores de um usuário e os considera como locais “conhecidos”. Um local de entrada é considerado desconhecido se não corresponder a nenhum dos locais familiares existentes.

Azure Active Directory Identity Protection:  

* tem um período inicial de aprendizado de 14 dias, durante o qual ele não sinaliza nenhum local novo como desconhecido.
* ignora entradas de dispositivos conhecidos e locais que estão geograficamente próximos de um local conhecido existente.

Para simular locais desconhecidos, você precisa entrar de um local e um dispositivo nunca utilizados para entrada na conta. 

**Para simular uma entrada de um local desconhecido, realize as seguintes etapas**:

1. Escolha uma conta com um histórico de entrada de, pelo menos, 14 dias. 
2. Realize uma das seguintes opções:
   
   a. Usando uma VPN, navegue até [https://myapps.microsoft.com](https://myapps.microsoft.com) e insira as credenciais da conta para a qual deseja simular o evento de risco.
   
   b. Peça que um colega de um local diferente entre usando as credenciais da conta (não recomendado).

A entrada será exibida no painel do Identity Protection dentro de 5 minutos.

### <a name="impossible-travel-to-atypical-location"></a>Viagem impossível a um local atípico
É difícil simular a condição de viagem impossível porque o algoritmo usa o aprendizado de máquina para eliminar falsos positivos, tais como viagens impossíveis de dispositivos conhecidos ou entradas de VPNs usadas por outros usuários no diretório. Além disso, o algoritmo exige um histórico de entrada de três a 14 dias para o usuário antes de começar a gerar eventos de risco.

**Para simular uma viagem impossível para um local atípico, realize as seguintes etapas**:

1. Usando seu navegador padrão, navegue até [https://myapps.microsoft.com](https://myapps.microsoft.com).  
2. Insira as credenciais da conta para a qual deseja gerar um evento de risco de viagem impossível.
3. Altere o agente do usuário. É possível alterar o agente do usuário no Internet Explorer nas Ferramentas de Desenvolvedor ou então no Firefox ou Chrome usando um complemento de alternador de agente do usuário.
4. Altere seu endereço IP. É possível alterar seu endereço IP usando uma VPN, um complemento do Tor ou criando um novo computador no Azure em um diferente.
5. Entre em [https://myapps.microsoft.com](https://myapps.microsoft.com) usando as mesmas credenciais de antes, alguns minutos após a entrada anterior.

A entrada será exibida no painel do Identity Protection dentro de 2 a 4 horas.<br>
Devido aos complexos modelos de aprendizado de máquina envolvidos, há uma chance de que isso não seja captado.<br> É conveniente replicar essas etapas para várias contas do Azure AD.

## <a name="simulating-vulnerabilities"></a>Simulação de vulnerabilidades
Vulnerabilidades são pontos fracos no seu ambiente do Azure AD que podem ser explorados por um ator maligno. Atualmente, três tipos de vulnerabilidades são exibidas no Azure AD Identity Protection que aproveitam os outros recursos do Azure AD. Essas vulnerabilidades serão exibidas no painel do Identity Protection automaticamente depois que esses recursos forem configurados.

* Azure AD [Multi-Factor Authentication?](../multi-factor-authentication/multi-factor-authentication.md)
* Azure AD [Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md).
* Azure AD [Privileged Identity Management](active-directory-privileged-identity-management-configure.md). 

## <a name="user-compromise-risk"></a>Risco de comprometimento do usuário
**Para testar o Risco de comprometimento do usuário, realize as seguintes etapas**:

1. Entre em [https://portal.azure.com](https://portal.azure.com) com as credenciais de administrador global para seu locatário.
2. Navegue até **Identity Protection**. 
3. Na folha principal de **Azure AD Identity Protection**, clique em **Configurações**. 
4. Na folha **Configurações do Portal**, em **Regras de segurança**, clique em **Risco de comprometimento do usuário**. 
5. Na folha **Risco de Entrada**, desative **Habilitar regra** e clique em **Salvar** configurações.
6. Para uma determinada conta de usuário, simule um evento de risco de locais desconhecidos ou IP anônimo. Isso elevará o nível de risco do usuário para **Médio**.
7. Aguarde alguns minutos e verifique se o nível do usuário é **Médio**.
8. Vá para a folha **Configurações do Portal** .
9. Na folha **Risco de Comprometimento do Usuário**, em **Habilitar regra**, selecione **Ativado**. 
10. Selecione uma das seguintes opções:
    
    a. Para bloquear, selecione **Médio** em **Bloquear entrada**.
    
    b. Para impor a alteração de senha de segurança, selecione **Médio** em **Exigir autenticação multifator**.
11. Clique em **Salvar**.
12. Agora você pode testar o acesso condicional baseado em risco ao entrar usando um usuário com um nível de risco elevado. Se o risco do usuário for Médio, dependendo da configuração da política, sua entrada será bloqueada ou você será forçado a alterar a senha. 
    <br><br>
    ![Guia estratégico](./media/active-directory-identityprotection-playbook/201.png "Guia estratégico")
    <br>

## <a name="sign-in-risk"></a>Risco de entrada
**Para testar um risco de entrada, realize as seguintes etapas:**

1. Entre em [https://portal.azure.com ](https://portal.azure.com) com as credenciais de administrador global para seu locatário.
2. Navegue até **Identity Protection**.
3. Na folha principal de **Azure AD Identity Protection**, clique em **Configurações**. 
4. Na folha **Configurações do Portal**, em **Regras de segurança**, clique em **Risco de entrada**.
5. Na folha **Risco de Entrada**, selecione **Ativado** em **Habilitar regra**. 
6. Selecione uma das seguintes opções:
   
   a. Para bloquear, selecione **Médio** em **Bloquear entrada**
   
   b. Para impor a alteração de senha de segurança, selecione **Médio** em **Exigir autenticação multifator**.
7. Para bloquear, selecione Médio em Bloquear entrada.
8. Para impor a autenticação multifator, selecione **Médio** em **Exigir autenticação multifator**.
9. Clique em **Save**.
10. Agora é possível testar o acesso condicional baseado em risco simulando eventos de risco de locais desconhecidos ou IP anônimo, pois são considerados eventos de risco **Médio** .


![Guia estratégico](./media/active-directory-identityprotection-playbook/200.png "Guia estratégico")


## <a name="see-also"></a>Consulte também
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)

