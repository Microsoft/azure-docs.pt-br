---
title: Autenticação para soluções de identidade híbrida do Azure AD
titleSuffix: Active Directory
description: Este guia ajuda Presidentes de empresas, Diretores de Informática, Arquitetos de identidade, Arquitetos corporativos e os responsáveis pelas decisões de TI e de segurança, a escolher um método de autenticação para sua solução de identidade híbrida do Azure AD em organizações de médio a grande porte.
keywords: ''
author: martincoetzer
ms.author: martinco
ms.date: 10/30/2019
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.workload: identity
ms.openlocfilehash: 15d62f40b50617fd1f6e543cb404a0d38361d3bd
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836488"
---
# <a name="choose-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Escolha o método de autenticação certo para sua solução de identidade híbrida do Azure Active Directory

Escolher o método de autenticação correto é a principal preocupação para organizações que desejam migrar seus aplicativos para a nuvem. Não subestime essa decisão pelos seguintes motivos:

1. É a primeira decisão para uma organização que deseja migrar para a nuvem.

2. O método de autenticação é um componente crítico da presença de uma organização na nuvem. Ele controla o acesso a todos os recursos e dados na nuvem.

3. É a base de todos os outros recursos de segurança avançada e experiência do usuário no Azure AD.

A identidade é o novo plano de controle da segurança de TI, portanto, a autenticação é a proteção de acesso da organização para o novo mundo de nuvem. As organizações precisam de um plano de controle de identidade que reforce a segurança e mantenha os aplicativos de nuvem protegidos contra invasores.

> [!NOTE]
> Alterar seu método de autenticação requer planejamento, teste e potencialmente tempo de inatividade. A [distribuição em etapas](./how-to-connect-staged-rollout.md) é uma ótima maneira de testar a migração dos usuários da Federação para a autenticação na nuvem.

### <a name="out-of-scope"></a>Fora do escopo
As organizações que não têm um espaço de diretório local existente não são o foco deste artigo. Normalmente, essas empresas criam identidades somente na nuvem, o que não requer uma solução de identidade híbrida. As identidades somente na nuvem existem apenas na nuvem e não estão associadas a identidades locais correspondentes.

## <a name="authentication-methods"></a>Métodos de autenticação
Quando a solução de identidade híbrida do Azure AD é seu novo plano de controle, a autenticação é a base do acesso à nuvem. Escolher o método de autenticação correto é uma primeira decisão fundamental na configuração de uma solução de identidade híbrida do Azure AD. Implemente o método de autenticação que é configurado usando o Azure AD Connect, que também provisiona usuários na nuvem.

Para escolher um método de autenticação, é necessário considerar o tempo, a infraestrutura existente, a complexidade e o custo de implementação de sua escolha. Esses fatores são diferentes para cada organização e podem mudar ao longo do tempo.

>[!VIDEO https://www.youtube.com/embed/YtW2cmVqSEw]

O Azure AD dá suporte aos seguintes métodos de autenticação para soluções de identidade híbrida.

### <a name="cloud-authentication"></a>Autenticação na nuvem
Quando você escolhe esse método de autenticação, o Azure AD cuida do processo de entrada dos usuários. Juntamente com o SSO (logon único) contínuo, os usuários podem entrar em aplicativos de nuvem sem precisar inserir suas credenciais novamente. Com a autenticação na nuvem, é possível escolher entre duas opções:

**Sincronização de hash de senha do Azure AD**. A maneira mais simples de habilitar a autenticação para objetos de diretório locais no Azure AD. Os usuários podem usar o mesmo nome de usuário e senha que usam localmente, sem a necessidade de implantar nenhuma infraestrutura adicional. Alguns recursos Premium do Azure AD, como o Identity Protection e os [Serviços de Domínio do Azure AD](../../active-directory-domain-services/tutorial-create-instance.md), exigem a sincronização de hash de senha para o método de autenticação escolhido, seja qual for.

> [!NOTE]
> As senhas nunca são armazenadas em texto não criptografado ou são criptografadas com um algoritmo reversível no Azure AD. Para obter mais informações sobre o processo real de sincronização de hash de senha, confira [Implementar a sincronização de hash de senha com a sincronização do Azure AD Connect](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).

**Autenticação de Passagem do Azure AD**. Fornece uma validação de senha simples para serviços de autenticação do Azure AD usando um agente de software que é executado em um ou mais servidores locais. Os servidores validam os usuários diretamente no Active Directory local, o que garante que a validação de senha não ocorra na nuvem.

As empresas com um requisito de segurança de imposição imediata de estados de conta de usuário local, políticas de senha e horas de entrada podem usar esse método de autenticação. Para obter mais informações sobre o processo real de autenticação de passagem, confira [Entrada do usuário com autenticação de passagem do Azure AD](../../active-directory/hybrid/how-to-connect-pta.md).

### <a name="federated-authentication"></a>Autenticação federada
Quando você escolhe esse método, o Azure AD transfere o processo de autenticação para um sistema de autenticação confiável separado, como os Serviços de Federação do Active Directory (AD FS) locais, para validar a senha do usuário.

O sistema de autenticação pode fornecer requisitos de autenticação avançada adicionais. Entre os exemplos estão a autenticação baseada em cartão inteligente ou a autenticação multifator de terceiros. Para obter mais informações, consulte [Implantando os Serviços de Federação do Active Directory (AD FS)](/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide).

A seção a seguir ajudará você a decidir qual método de autenticação é adequado para você, usando uma árvore de decisão. Ela ajudará a determinar se você deve implantar a autenticação federada ou na nuvem para sua solução de identidade híbrida do Azure AD.

## <a name="decision-tree"></a>Árvore de decisão

![Árvore de decisão da autenticação do Azure AD](./media/choose-ad-authn/azure-ad-authn-image1.png)

Detalhes sobre questões de decisão:

1. O Microsoft Azure AD pode manipular a entrada para usuários sem depender de componentes locais para verificar senhas.
2. O Azure AD pode transferir a entrada do usuário para um provedor de autenticação confiável, como o AD FS da Microsoft.
3. Se você precisar aplicar políticas de segurança do Active Directory em nível de usuário, como conta expirada, conta desativada, senha expirada, bloqueio de conta e horas de login na entrada de cada usuário, o Azure AD exigirá alguns componentes locais.
4. Recursos de login não suportados de forma nativa pelo Microsoft Azure Active Directory:
   * Entrar usando cartões inteligentes ou certificados.
   * Entrar usando o servidor MFA local.
   * Entrar usando uma solução de autenticação de terceiros.
   * Solução de autenticação de múltiplos sites locais.
5. O Azure AD Identity Protection requer Sincronização de Hash de Senha, independentemente de qual método de entrada escolhido, para fornecer o relatório *Usuários com credenciais vazadas*. As organizações podem fazer failover para sincronização de Hash de senha se o método de entrada principal falha, e ele foi configurado antes do evento de falha.

> [!NOTE]
> O Azure AD Identity Protection requer licenças do [Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="detailed-considerations"></a>Considerações detalhadas

### <a name="cloud-authentication-password-hash-synchronization"></a>Autenticação na nuvem: Sincronização de hash de senha

* **Esforço**. A sincronização de hash de senha exige o mínimo de esforço de implantação, manutenção e infraestrutura.  Esse nível de esforço normalmente se aplica a organizações que precisam apenas de seus usuários para entrar em Microsoft 365, aplicativos SaaS e outros recursos baseados no Azure AD. Quando ativada, a sincronização de hash de senha faz parte do processo de sincronização do Azure AD Connect e é executada a cada dois minutos.

* **Experiência do usuário**. Para melhorar a experiência de entrada dos usuários, implante o SSO contínuo com a sincronização de hash de senha. O SSO contínuo elimina prompts desnecessários quando os usuários estão conectados.

* **Cenários avançados**. Se for uma escolha das organizações, é possível usar insights de identidades com relatórios do Azure AD Identity Protection com o Azure AD Premium P2. Um exemplo é o relatório de credenciais vazadas. O Windows Hello para Empresas tem [requisitos específicos quando a sincronização de hash de senha é usada](/windows/access-protection/hello-for-business/hello-identity-verification). [O Azure AD Domain Services](../../active-directory-domain-services/tutorial-create-instance.md) requer a sincronização de hash de senha para provisionar aos usuários as credenciais corporativas no domínio gerenciado.

    As organizações que exigem a autenticação multifator com a sincronização de hash de senha devem usar a autenticação multifator do Azure AD ou [controles personalizados de acesso condicional](../../active-directory/conditional-access/controls.md#custom-controls-preview). Essas organizações não podem usar um método de autenticação multifator local ou de terceiros que dependa da federação.

> [!NOTE]
> O Acesso condicional do Azure AD requer licenças do [Azure AD Premium P1](https://azure.microsoft.com/pricing/details/active-directory/).

* **Continuidade dos negócios**. O uso da sincronização de hash de senha com a autenticação na nuvem está altamente disponível como um serviço de nuvem que pode ser expandido para todos os datacenters da Microsoft. Para garantir que a sincronização de hash de senha não ficará inativa por longos períodos, implante um segundo servidor do Azure AD Connect em modo de preparo em uma configuração em espera.

* **Considerações**. No momento, a sincronização de hash de senha não impõe imediatamente as alterações nos estados da conta local. Nessa situação, um usuário tem acesso aos aplicativos de nuvem até que o estado da conta do usuário seja sincronizado com o Azure AD. As organizações podem desejar superar essa limitação executando um novo ciclo de sincronização depois que os administradores fizerem atualizações em massa nos estados da conta do usuário local. Um exemplo é a desabilitação de contas.

> [!NOTE]
> No momento, os estados de senha expirada e conta bloqueada não estão sincronizados com o Azure AD usando o Azure AD Connect. Quando você altera a senha de um usuário e define o sinalizador *o usuário deve alterar a senha no próximo logon*, o hash de senha não será sincronizado com o Azure AD usando o Azure AD Connect até que o usuário altere a senha.

Veja [como implementar a sincronização de hash de senha](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) para obter as etapas de implantação.

### <a name="cloud-authentication-pass-through-authentication"></a>Autenticação na nuvem: Autenticação de Passagem  

* **Esforço**. Para a autenticação de passagem, você precisa de um ou mais (recomendamos três) agentes leves instalados nos servidores existentes. Esses agentes precisam ter acesso ao Active Directory Domain Services local, incluindo os controladores de domínio locais do AD. Esses precisam ter acesso de saída à Internet e acesso aos controladores de domínio. Por esse motivo, não há suporte para implantação dos agentes em uma rede de perímetro.

    A autenticação de passagem exige acesso à rede irrestrito aos controladores de domínio. Todo o tráfego de rede é criptografado e limitado a solicitações de autenticação. Para obter mais informações sobre esse processo, consulte a [análise aprofundada sobre segurança](../../active-directory/hybrid/how-to-connect-pta-security-deep-dive.md) na autenticação de passagem.

* **Experiência do usuário**. Para melhorar a experiência de entrada dos usuários, implante o SSO contínuo com a Autenticação de Passagem. O SSO contínuo elimina prompts desnecessários após a entrada dos usuários.

* **Cenários avançados**. A Autenticação de Passagem impõe a política de conta local no momento da entrada. Por exemplo, o acesso é negado quando o estado da conta de um usuário local é desabilitado, bloqueado ou a [senha expira](../../active-directory/hybrid/how-to-connect-pta-faq.md#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication), ou quando a tentativa de logon está fora do horário de entrada permitida do usuário.

    As organizações que exigem a autenticação multifator com autenticação de passagem devem usar a MFA (autenticação multifator) do Azure AD ou [controles personalizados de acesso condicional](../../active-directory/conditional-access/controls.md#custom-controls-preview). Essas organizações não podem usar um método de autenticação multifator local ou de terceiros que dependa da federação. Os recursos avançados exigem a implantação da sincronização de hash de senha, independentemente de você escolher ou não a autenticação de passagem. Um exemplo é o relatório de credenciais vazadas do Identity Protection.

* **Continuidade dos negócios**. Recomendamos a implantação de dois agentes extras de autenticação de passagem. Esses extras complementam o primeiro agente no servidor do Azure AD Connect. Essa implantação adicional garante a alta disponibilidade das solicitações de autenticação. Quando você tiver três agentes implantados, um deles ainda poderá falhar quando outro agente estiver inoperante para manutenção.

    Há outro benefício em implantar a sincronização de hash de senha, além da autenticação de passagem. Ela funciona como um método de autenticação de backup quando o método de autenticação primário não está mais disponível.

* **Considerações**. É possível usar a sincronização de hash de senha como um método de autenticação de backup a para autenticação de passagem quando os agentes não puderem validar as credenciais de um usuário devido a uma falha local significativa. Failover para sincronização de hash de senha não acontece automaticamente e você deve usar o Azure AD Connect para alternar o método de entrada manualmente.

    Para outras considerações sobre a Autenticação de Passagem, incluindo o suporte à ID Alternativa, confira as [perguntas frequentes](../../active-directory/hybrid/how-to-connect-pta-faq.md).

Consulte a [implementação da autenticação de passagem](../../active-directory/hybrid/how-to-connect-pta.md) para ver etapas de implantação.

### <a name="federated-authentication"></a>Autenticação federada

* **Esforço**. Um sistema de autenticação federada depende de um sistema externo confiável para autenticar os usuários. Algumas empresas desejam reutilizar seus investimentos em sistemas federados existentes com sua solução de identidade híbrida do Azure AD. A manutenção e o gerenciamento do sistema federado fica fora do controle do Azure AD. Cabe à organização usar o sistema federado para garantir que ele seja implantado de forma segura e possa lidar com a carga de autenticação.

* **Experiência do usuário**. A experiência do usuário de autenticação federada depende da implementação dos recursos, da topologia e da configuração do farm de federação. Algumas organizações precisam dessa flexibilidade para adaptar e configurar o acesso ao farm de federação de acordo com seus requisitos de segurança. Por exemplo, é possível configurar os usuários e os dispositivos conectados internamente para conectar os usuários automaticamente, sem notificá-los para que insiram suas credenciais. Essa configuração funciona porque eles já entraram em seus dispositivos. Se necessário, alguns recursos de segurança avançada dificultam ainda mais o processo de entrada dos usuários.

* **Cenários avançados**. Uma solução de autenticação federada é necessária quando os clientes têm um requisito de autenticação ao qual o Azure AD não dá suporte nativamente. Veja informações detalhadas para ajudá-lo a [escolher a opção ideal de entrada](/archive/blogs/samueld/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365). Considere os seguintes requisitos comuns:

  * Autenticação que exige certificados ou cartões inteligentes.
  * Servidores MFA locais ou provedores de autenticação multifator de terceiros exigindo um provedor de identidade federado.
  * Autenticação usando soluções de autenticação de terceiros. Consulte a [lista de compatibilidade de federação do Azure AD](../../active-directory/hybrid/how-to-connect-fed-compatibility.md).
  * Entrada que exige um sAMAccountName, por exemplo, DOMAIN\nome de usuário, em vez de um nome UPN, por exemplo, user@domain.com.

* **Continuidade dos negócios**. Normalmente, os sistemas federados exigem uma matriz com balanceamento de carga de servidores, conhecida como um farm. Esse farm é configurado em uma rede interna e topologia de rede de perímetro para garantir a alta disponibilidade para as solicitações de autenticação.

    Implante a sincronização de hash de senha junto com a autenticação federada como um método de autenticação de backup quando o método de autenticação primário não estiver mais disponível. Um exemplo é quando os servidores locais não estão disponíveis. Algumas organizações empresariais de grande porte exigem uma solução de federação para dar suporte a vários pontos de entrada da Internet configurados com DNS geográfico para solicitações de autenticação de baixa latência.

* **Considerações**. Normalmente, os sistemas federados exigem um investimento mais significativo em infraestrutura local. A maioria das organizações escolherá essa opção caso já tenha um investimento de federação local. E se houver um requisito imperativo dos negócios para uso de um provedor de identidade única. A federação é mais complexa de operar e de resolver problemas em comparação com soluções de autenticação na nuvem.

Para um domínio não roteável que não pode ser verificado no Azure AD, é necessário fazer uma configuração extra para implementar a entrada da ID de usuário. Esse requisito é conhecido como suporte à ID de logon alternativa. Consulte [Configurando a ID de logon alternativa](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) para ver requisitos e limitações. Se você optar por usar um provedor de autenticação multifator de terceiros com federação, verifique se o provedor dá suporte ao WS-Trust para permitir que os dispositivos ingressem no Azure AD.

Veja [Implantando servidores de federação](/windows-server/identity/ad-fs/deployment/deploying-federation-servers) para obter as etapas de implantação.

> [!NOTE]
> Quando você implanta sua solução de identidade híbrida do Azure AD, precisa implementar uma das tecnologias do Azure AD Connect compatíveis. Saiba mais sobre configurações compatíveis e não compatíveis em [Topologias para o Azure AD Connect](../../active-directory/hybrid/plan-connect-topologies.md).

## <a name="architecture-diagrams"></a>Diagramas de arquitetura

Os diagramas a seguir descrevem os componentes da arquitetura de alto nível necessários para cada método de autenticação que você pode usar com sua solução de identidade híbrida do Azure AD. Eles fornecem uma visão geral para ajudá-lo a comparar as diferenças entre as soluções.

* Simplicidade de uma solução de sincronização de hash de senha:

    ![Identidade híbrida do Azure AD com Sincronização de hash de senha](./media/choose-ad-authn/azure-ad-authn-image2.png)

* Requisitos de autenticação de passagem do agente, usando dois agentes para redundância:

    ![Identidade híbrida do Azure AD com Autenticação de passagem](./media/choose-ad-authn/azure-ad-authn-image3.png)

* Componentes necessários para federação no perímetro e na rede interna de sua organização:

    ![Identidade híbrida do Azure AD com autenticação federada](./media/choose-ad-authn/azure-ad-authn-image4.png)

## <a name="comparing-methods"></a>Comparando métodos

|Consideração|Sincronização de hash de senha + SSO Contínuo|Autenticação de Passagem + SSO Contínuo|Federação com o AD FS|
|:-----|:-----|:-----|:-----|
|Onde a autenticação ocorre?|Na nuvem|Na nuvem, após uma troca de verificação de senha segura com o agente de autenticação local|Local|
|Quais são os requisitos de servidor local além do sistema de provisionamento: Azure AD Connect?|Nenhum|Um servidor para cada agente de autenticação adicional|Dois ou mais servidores do AD FS<br><br>Dois ou mais servidores WAP na rede de perímetro/DMZ|
|Quais são os requisitos de Internet e de redes locais, além do sistema de provisionamento?|Nenhum|[Acesso à Internet de saída](../../active-directory/hybrid/how-to-connect-pta-quick-start.md) dos servidores executando agentes de autenticação|[Acesso à Internet de entrada](/windows-server/identity/ad-fs/overview/ad-fs-requirements) aos servidores WAP no perímetro<br><br>Acesso à rede de entrada aos servidores AD FS por meio dos servidores WAP no perímetro<br><br>Balanceamento de carga de rede|
|Há algum requisito de certificado TLS/SSL?|Não|Não|Sim|
|Há alguma solução de monitoramento de integridade?|Não obrigatório|Status do agente fornecido pelo [Centro de administração do Azure Active Directory](../../active-directory/hybrid/tshoot-connect-pass-through-authentication.md)|[Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md)|
|Usuários podem obter o logon único para recursos de nuvem de dispositivos que ingressaram no domínio dentro da rede da empresa?|Sim, com [SSO Contínuo](../../active-directory/hybrid/how-to-connect-sso.md)|Sim, com [SSO Contínuo](../../active-directory/hybrid/how-to-connect-sso.md)|Sim|
|Há suporte para quais tipos de entrada?|Nome Principal do Usuário + senha<br><br>Autenticação Integrada do Windows usando [SSO Contínuo](../../active-directory/hybrid/how-to-connect-sso.md)<br><br>[ID de logon alternativa](../../active-directory/hybrid/how-to-connect-install-custom.md)|Nome Principal do Usuário + senha<br><br>Autenticação Integrada do Windows usando [SSO Contínuo](../../active-directory/hybrid/how-to-connect-sso.md)<br><br>[ID de logon alternativa](../../active-directory/hybrid/how-to-connect-pta-faq.md)|Nome Principal do Usuário + senha<br><br>sAMAccountName + senha<br><br>Autenticação integrada do Windows<br><br>[Autenticação de certificado e cartão inteligente](/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[ID de logon alternativa](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|Há suporte para o Windows Hello for Business?|[Modelo de confiança de chave](/windows/security/identity-protection/hello-for-business/hello-identity-verification)|[Modelo de confiança de chave](/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>*Requer nível funcional do domínio do Windows Server 2016*|[Modelo de confiança de chave](/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Modelo de confiança de certificado](/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|Quais são as opções de autenticação multifator?|[MFA do Azure AD](/azure/multi-factor-authentication/)<br><br>[Controles personalizados com Acesso Condicional*](../../active-directory/conditional-access/controls.md)|[MFA do Azure AD](/azure/multi-factor-authentication/)<br><br>[Controles personalizados com Acesso Condicional*](../../active-directory/conditional-access/controls.md)|[MFA do Azure AD](/azure/multi-factor-authentication/)<br><br>[Servidor MFA do Azure](../../active-directory/authentication/howto-mfaserver-deploy.md)<br><br>[MFA de Terceiros](/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)<br><br>[Controles personalizados com Acesso Condicional*](../../active-directory/conditional-access/controls.md)|
|Há suporte para quais estados de conta de usuário?|Contas desabilitadas<br>(até 30 minutos de atraso)|Contas desabilitadas<br><br>Conta bloqueada<br><br>Conta expirada<br><br>Senha expirada<br><br>Horários de entrada|Contas desabilitadas<br><br>Conta bloqueada<br><br>Conta expirada<br><br>Senha expirada<br><br>Horários de entrada|
|Quais são as opções de Acesso Condicional?|[Acesso Condicional do Azure AD, com o Azure AD Premium](../../active-directory/conditional-access/overview.md)|[Acesso Condicional do Azure AD, com o Azure AD Premium](../../active-directory/conditional-access/overview.md)|[Acesso Condicional do Azure AD, com o Azure AD Premium](../../active-directory/conditional-access/overview.md)<br><br>[Regras de declaração do AD FS](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|Há suporte para protocolos herdados de bloqueio?|[Sim](../../active-directory/conditional-access/overview.md)|[Sim](../../active-directory/conditional-access/overview.md)|[Sim](/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|Você pode personalizar o logotipo, a imagem e a descrição nas páginas de entrada?|[Sim, com o Azure AD Premium](../../active-directory/fundamentals/customize-branding.md)|[Sim, com o Azure AD Premium](../../active-directory/fundamentals/customize-branding.md)|[Sim](../../active-directory/hybrid/how-to-connect-fed-management.md)|
|Há suporte para quais cenários avançados?|[Bloqueio de senha inteligente](../../active-directory/authentication/howto-password-smart-lockout.md)<br><br>[Relatórios de credenciais vazadas, com o Azure AD Premium P2](../identity-protection/overview-identity-protection.md)|[Bloqueio de senha inteligente](../../active-directory/authentication/howto-password-smart-lockout.md)|Sistema de autenticação de baixa latência para vários sites<br><br>[Bloqueio de extranet de AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)<br><br>[Integração com sistemas de identidade de terceiros](../../active-directory/hybrid/how-to-connect-fed-compatibility.md)|

> [!NOTE]
> Atualmente, os controles personalizados no Acesso Condicional do Azure AD não dão suporte para registro de dispositivos.

## <a name="recommendations"></a>Recomendações
Seu sistema de identidade garante que os usuários tenham acesso aos aplicativos de nuvem e de linha de negócios que você migra e disponibiliza na nuvem. Para manter os usuários autorizados produtivos e os atores ruins fora dos dados confidenciais de sua organização, a autenticação controla o acesso aos aplicativos.

Use ou habilite a sincronização de hash de senha para o método de autenticação escolhido, seja qual for, pelos seguintes motivos:

1. **Alta disponibilidade e recuperação de desastre**. A Autenticação de Passagem e a federação dependem da infraestrutura local. Para a autenticação de passagem, a superfície local inclui a rede e o hardware do servidor necessários para os agentes de Autenticação de Passagem. Para a federação, a superfície local é ainda maior. Ela exige que os servidores na rede de perímetro atuem como um proxy para as solicitações de autenticação e os servidores internos de federação.

    Para evitar pontos únicos de falha, implante servidores redundantes. Assim, as solicitações de autenticação sempre serão atendidas se um componente falhar. A autenticação de passagem e a federação também dependem dos controladores de domínio para responder às solicitações de autenticação, que também podem falhar. Muitos desses componentes precisam de manutenção para permanecerem íntegros. Há uma probabilidade maior de interrupções quando a manutenção não é planejada e implementada corretamente. Evite interrupções usando a sincronização de hash de senha, porque o serviço de autenticação na nuvem do Microsoft Azure AD é dimensionado globalmente e está sempre disponível.

2. **Sobrevivência à interrupção local**.  As consequências de uma interrupção local devido a um ataque cibernético ou desastre podem ser significativas, variando de danos à reputação da marca até a paralisação da organização que não consegue conter o ataque. Recentemente, muitas organizações foram vítimas de ataques de malware, incluindo ransomware direcionado, o que fez seus servidores locais ficarem inativos. Quando a Microsoft ajuda os clientes a lidar com esses tipos de ataques, ela observa duas categorias de organizações:

   * As organizações que anteriormente também ativaram a sincronização de hash com base na autenticação de passagem ou federada alteraram seu método de autenticação principal para usar a sincronização de hash de senha. Elas voltaram a ficar online em questão de horas. Ao usar o acesso ao email via Microsoft 365, eles trabalharam para resolver problemas e acessar outras cargas de trabalho baseadas em nuvem.

   * As organizações que anteriormente não habilitaram a sincronização de hash de senha precisaram recorrer a sistemas de email de consumidor externo não confiáveis para comunicação para resolução dos problemas. Nesses casos, levava semanas para restaurar sua infraestrutura de identidade local, antes que os usuários conseguissem entrar em aplicativos baseados em nuvem novamente.

3. **Proteção de identidade**. Uma das melhores maneiras de proteger os usuários na nuvem é o Azure AD Identity Protection com o Azure AD Premium P2. A Microsoft examina a Internet continuamente para ver se há listas de usuários e de senhas comercializadas e disponibilizadas por atores mal-intencionados na dark Web. O Azure AD poderá usar essas informações para verificar se os nomes de usuário e senhas em sua organização estiverem comprometidos. Portanto, é fundamental habilitar a sincronização de hash de senha, independentemente do método de autenticação usado, seja a autenticação federada, seja a de passagem. As credenciais vazadas são apresentadas como um relatório. Use essas informações para bloquear ou forçar os usuários a alterar suas senhas quando tentarem entrar com senhas vazadas.

## <a name="conclusion"></a>Conclusão

Este artigo descreve várias opções de autenticação que as organizações podem configurar e implantar para dar suporte ao acesso a aplicativos de nuvem. Para atender a vários requisitos técnicos, de negócios e de segurança, as organizações podem escolher entre a sincronização de hash de senha, a Autenticação de Passagem e a federação.

Considere cada método de autenticação. O esforço para implantar a solução e a experiência do usuário do processo de entrada atende aos seus requisitos de negócios? Avalie se a organização precisa dos cenários avançados e dos recursos de continuidade dos negócios de cada método de autenticação. Por fim, avalie as considerações de cada método de autenticação. Algum deles impede a implementação de sua escolha?

## <a name="next-steps"></a>Próximas etapas

Atualmente, as ameaças estão presentes 24 horas por dia e vêm de qualquer lugar. Implemente o método de autenticação correto e isso atenuará os riscos de segurança e protegerá as identidades.

[Comece](../fundamentals/active-directory-whatis.md) a usar o Azure AD e implante a solução de autenticação adequada para sua organização.

Caso esteja considerando migrar da autenticação federada para a autenticação na nuvem, saiba mais sobre [como alterar o método de entrada](../../active-directory/hybrid/plan-connect-user-signin.md). Para ajudá-lo a planejar e implementar a migração, use [esses planos de implantação de projeto](../fundamentals/active-directory-deployment-plans.md) ou considere usar o novo recurso [Distribuição em Etapas](../../active-directory/hybrid/how-to-connect-staged-rollout.md) para migrar usuários federados para o uso da autenticação de nuvem em uma abordagem em etapas.