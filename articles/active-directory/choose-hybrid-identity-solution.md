---
title: "Escolher uma solução de identidade híbrida do Azure | Microsoft Docs"
description: "Obtenha uma compreensão básica das soluções de identidade híbridas disponíveis e das recomendações para tomar a melhor decisão de governança de identidade para sua organização."
keywords: 
author: jeffgilb
manager: femila
ms.reviewer: jsnow
ms.author: jeffgilb
ms.date: 7/5/2017
ms.topic: article
ms.prod: 
ms.service: azure
ms.technology: 
ms.assetid: 
ms.custom: it-pro
ms.openlocfilehash: 5838e3276765f4f074bca2e3cae81b17edfa7c69
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="microsoft-hybrid-identity-solutions"></a>Soluções de identidade híbrida da Microsoft
As soluções de identidade híbridas do [Microsoft Azure AD (Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) permitem que você sincronize objetos de diretório locais com o Azure AD enquanto ainda está gerenciando seus usuários locais. A primeira decisão a tomar ao planejar sincronizar seu Windows Server Active Directory localmente com o Azure AD é se você deseja usar a identidade sincronizada ou a identidade federada. As identidades sincronizadas e, opcionalmente, os hashes de senha, permitem que os usuários usem a mesma senha para acessar os recursos organizacionais locais e baseados em nuvem. Para requisitos de cenário mais avançados, como SSO (logon único) ou MFA local, você precisa implantar os Serviços de Federação do Active Directory (AD FS) para federar as identidades. 

Há várias opções disponíveis para a configuração de identidade híbrida. Este artigo fornece informações para ajudá-lo a escolher a melhor para sua organização com base em facilidade de implantação e em suas necessidades de gerenciamento de acesso e identidades específicas. Conforme considera qual modelo de identidade melhor atenda às necessidades de sua organização, você também precisa pensar no tempo, na infraestrutura existente, na complexidade e no custo. Esses fatores são diferentes para cada organização e podem mudar ao longo do tempo. No entanto, se seus requisitos realmente mudarem, você também tem a flexibilidade para mudar para um modelo de identidade diferente.

> [!TIP]
> Essas soluções são todas entregues pelo [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="synchronized-identity"></a>Identidade sincronizada 
A identidade sincronizada é a maneira mais simples de sincronizar objetos de diretório locais (usuários e grupos) com o Azure AD. 

![Identidade híbrida sincronizada](./media/choose-hybrid-identity-solution/synchronized-identity.png)

Embora a identidade sincronizada seja o método mais rápido e fácil, os usuários ainda precisam manter uma senha separada para os recursos baseados em nuvem. Para evitar isso, você também pode (opcionalmente) [sincronizar um hash das senhas dos usuários](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-synchronization#what-is-password-synchronization) ao seu diretório do Azure AD. Sincronizar os hashes de senha permite aos usuários fazer logon em recursos organizacionais baseados em nuvem com o mesmo nome de usuário e senha que eles usam localmente. O Azure AD Connect verifica periodicamente seu diretório local quanto a alterações e mantém seu diretório do Azure AD sincronizado. Quando um atributo de usuário ou senha é alterado no Active Directory local, ele é automaticamente atualizado no Azure AD. 

Para a maioria das organizações que precisam apenas habilitar seus usuários para fazer logon no Office 365, aplicativos SaaS e outros recursos baseados no Azure AD, a opção de sincronização de senha padrão é recomendável. Se isso não funcionar para você, será necessário decidir entre a autenticação de passagem e o AD FS.

> [!TIP]
> As senhas de usuário são armazenadas Windows Server Active Directory local na forma de um valor de hash que representa a senha de usuário real. Um valor de hash é um resultado de uma função matemática unidirecional (o algoritmo de hash). Não há um método para reverter o resultado de uma função unidirecional para a versão de texto sem formatação de uma senha. Não é possível usar o hash de senha para entrar na sua rede local. Quando você opta por sincronizar senhas, o Azure AD Connect extrai os hashes de senha do Active Directory local e aplica o processamento de segurança extra ao hash de senha antes de ele ser sincronizado com o Azure AD. A sincronização de senha também pode ser usada em conjunto com write-back de senha para habilitar a redefinição de senha de autoatendimento no Azure AD. Além disso, você pode habilitar o SSO (Logon único) para usuários em computadores ingressados no domínio que estejam conectados à rede corporativa. Com o logon único, os usuários habilitados só precisarão inserir um nome de usuário para acessar com segurança os recursos de nuvem. 

## <a name="pass-through-authentication"></a>Autenticação de passagem
A [Autenticação de passagem do Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication) fornece uma solução de validação de senha simples para serviços baseados no Azure AD usando o Active Directory local. Se as políticas de conformidade e segurança para a sua organização não permitirem enviar as senhas dos usuários, mesmo em um formulário de hash e você só precisará dar suporte ao SSO de área de trabalho para dispositivos ingressados no domínio, é recomendável que você avalie o uso da autenticação de passagem. A autenticação de passagem não exige qualquer implantação na DMZ, o que simplifica a infraestrutura de implantação quando comparada ao AD FS. Quando os usuários entram usando o Azure AD, esse método de autenticação valida as senhas dos usuários diretamente no seu Active Directory local.

![Autenticação de passagem](./media/choose-hybrid-identity-solution/pass-through-authentication.png)

Com a autenticação de passagem, não é necessária uma infraestrutura de rede complexa e você não precisa armazenar senhas locais na nuvem. Combinada com o logon único, a autenticação de passagem fornece uma experiência realmente integrada ao entrar no Azure AD ou outros serviços de nuvem.

A autenticação de passagem é configurada por meio do Azure AD Connect, que utiliza um agente local simples que escuta solicitações de validação de senha. O agente pode ser implantado facilmente em várias máquinas para fornecer alta disponibilidade e balanceamento de carga. Como todas as comunicações são apenas de saída, não há requisitos para o conector ser instalado em uma DMZ. Os requisitos do computador do servidor para o conector são os seguintes:

- Windows Server 2012 R2 ou posterior
- Ingressado em um domínio na floresta em que os usuários são validados

> [!NOTE]
> A autenticação de passagem do Azure AD está na versão prévia no momento e tem suporte para clientes baseados em navegador da Web e clientes do Office que dão suporte à autenticação moderna. Para clientes que não têm suporte, como clientes herdados do Office e Exchange ActiveSync (incluindo clientes de email nativo em dispositivos móveis), é recomendável usar a autenticação moderna equivalente. A autenticação moderna não só permite a autenticação de passagem, mas também permite que políticas de acesso condicional sejam aplicadas, como a autenticação multifator. 

A autenticação de passagem no momento não tem suporte ao usar dispositivos Windows 10 ingressados no Azure AD. No entanto, você pode usar a sincronização de hash de senha como um fallback automático para dar suporte ao Windows 10 e aos clientes herdados mencionados anteriormente. Durante a versão prévia, a sincronização de hash de senha está habilitada por padrão quando a autenticação de passagem é selecionada como a opção de entrada no Azure AD Connect.


## <a name="federated-identity-ad-fs"></a>Identidade federada (AD FS)
Para obter mais controle sobre como os usuários acessam o Office 365 e outros serviços de nuvem, você pode configurar a sincronização de diretórios com SSO (logon único) usando os [Serviços de Federação do Active Directory (AD FS)](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/whats-new-active-directory-federation-services-windows-server-2016). Federar as entradas de seus usuários com o AD FS delega a autenticação para um servidor local que valida as credenciais do usuário. Nesse modelo, as credenciais do Active Directory local nunca são passadas para o Azure AD.

![Identidade federada](./media/choose-hybrid-identity-solution/federated-identity.png)

Também chamado de federação de identidade, esse método de entrada garante que todas as autenticações de usuário sejam controladas localmente e permite que os administradores implementem níveis mais rigorosos de controle de acesso. A federação de identidade com o AD FS é a opção mais complicada e exige a implantação de servidores adicionais no seu ambiente local. A federação de identidade também o compromete a fornecer suporte 24 horas por dia, 7 dias por semana para sua infraestrutura do Active Directory e AD FS. Esse nível alto de suporte é necessário porque se seu acesso à Internet local, seu controlador de domínio ou seus servidores AD FS estiverem indisponíveis, os usuários não poderão entrar nos serviços de nuvem.

> [!TIP]
> Se você optar por usar a os Serviços de Federação do Active Directory (AD FS), poderá opcionalmente configurar a sincronização de senha como um backup em caso de falha na infraestrutura do AD FS.


## <a name="common-scenarios-and-recommendations"></a>Cenários e recomendações comuns
Aqui estão alguns dos cenários comuns de gerenciamento de acesso e identidade híbrida com recomendações sobre qual opção de identidade híbrida pode ser apropriada para cada um.

|Eu preciso de:|PWS e SSO<sup>1</sup>| PTA e SSO<sup>2</sup> | AD FS<sup>3</sup>|
|-----|-----|-----|-----|
|Sincronizar automaticamente na nuvem as contas de novo usuário, de contato e de grupo criadas no meu Active Directory local.|![Recomendadas](./media/choose-hybrid-identity-solution/ic195031.png)| ![Recomendadas](./media/choose-hybrid-identity-solution/ic195031.png) |![Recomendadas](./media/choose-hybrid-identity-solution/ic195031.png)|
|Configurar meu locatário para cenários híbridos do Office 365|![Recomendadas](./media/choose-hybrid-identity-solution/ic195031.png)| ![Recomendadas](./media/choose-hybrid-identity-solution/ic195031.png) |![Recomendadas](./media/choose-hybrid-identity-solution/ic195031.png)|
|Habilitar os usuários para entrar e acessar serviços de nuvem usando suas senhas locais|![Recomendadas](./media/choose-hybrid-identity-solution/ic195031.png)| ![Recomendadas](./media/choose-hybrid-identity-solution/ic195031.png) |![Recomendadas](./media/choose-hybrid-identity-solution/ic195031.png)|
|Implementar o logon único usando credenciais corporativas|![Recomendadas](./media/choose-hybrid-identity-solution/ic195031.png)| ![Recomendadas](./media/choose-hybrid-identity-solution/ic195031.png) |![Recomendadas](./media/choose-hybrid-identity-solution/ic195031.png)|
|Certificar-me de que os hashes de senha não sejam armazenados na nuvem| |![Recomendadas](./media/choose-hybrid-identity-solution/ic195031.png)|![Recomendadas](./media/choose-hybrid-identity-solution/ic195031.png)|
|Habilitar soluções de autenticação multifator locais| | |![Recomendadas](./media/choose-hybrid-identity-solution/ic195031.png)|
|Suporte à autenticação de cartão inteligente para meus usuários<sup>4</sup>| | |![Recomendadas](./media/choose-hybrid-identity-solution/ic195031.png)|
|Exibir notificações de expiração de senha no Portal do Office e na área de trabalho do Windows 10| | |![Recomendadas](./media/choose-hybrid-identity-solution/ic195031.png)|

> <sup>1</sup> Sincronização de senha com logon único. 

> <sup>2</sup> Autenticação de passagem e Logon único. 

> <sup>3</sup> Logon único federado com o AD FS.

> <sup>4</sup> O AD FS pode ser integrado com a Enterprise PKI para permitir a entrada usando certificados. Esses certificados podem ser certificados suaves implantados por meio dos canais de provisionamento confiáveis como certificados de cartão inteligente (incluindo cartões PIV/CAC), MDM, GPO ou Hello for Business (certificado confiável). Para obter mais informações sobre o suporte à autenticação de cartão inteligente, consulte [este blog](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/).


## <a name="next-steps"></a>Próximas etapas
[Saiba mais em um ambiente de prova de conceito do Azure](https://aka.ms/aad-poc)

[Instalar o Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)

[Monitorar a sincronização de identidade híbrida](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health)

