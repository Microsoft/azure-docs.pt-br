---
title: Compreender a identidade do Azure | Microsoft Docs
description: "Obtenha uma compreensão básica dos termos, dos conceitos e das recomendações da solução de identidade do Microsoft Azure para que você possa tomar a melhor decisão de controle de identidade para sua organização."
keywords: 
author: jeffgilb
manager: femila
ms.reviewer: jsnow
ms.author: jeffgilb
ms.date: 7/17/2017
ms.topic: article
ms.prod: 
ms.service: azure
ms.technology: 
ms.assetid: 
ms.custom: it-pro
ms.openlocfilehash: e368f14638c480a632afa7c17023aa8ae4c8833f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="understand-azure-identity-solutions"></a>Compreender as soluções de identidade do Azure
O Microsoft Azure Active Directory (Azure AD) é um dispositivo de nuvem de gerenciamento de identidade e acesso que fornece serviços de diretório, governança de identidade e gerenciamento de acesso do aplicativo. O Azure AD rapidamente [habilita SSO (logon único)](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso) para milhares de aplicativos comerciais e personalizados pré-integrados na [Galeria de aplicativos do Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/). Muitos desses aplicativos você provavelmente já usa, como o Office 365, Salesforce.com, Box, ServiceNow e Workday.

Um único diretório do Azure AD é automaticamente associado a uma assinatura do Azure quando é criado. Como o serviço de identidade no Azure, o Azure AD fornece todas as funções de controle de acesso e gerenciamento de identidade para recursos baseados em nuvem. Esses recursos podem incluir usuários, aplicativos e grupos para um locatário individual (organização), conforme mostrado no diagrama a seguir:

![Azure Active Directory](./media/understand-azure-identity-solutions/azure-ad.png)

O Microsoft Azure oferece várias maneiras de aproveitar IDaaS (identidade como um serviço) com níveis variados de complexidade para atender às necessidades individuais da sua organização. O restante deste artigo o ajudará a entender a terminologia e os conceitos básicos de identidade do Azure, bem como recomendações para você fazer a melhor escolha entre as opções disponíveis.

## <a name="terms-to-know"></a>Termos a conhecer

Antes de tomar uma decisão quanto à solução de identidade do Azure para a sua organização, você precisará ter uma compreensão básica dos termos comumente usados quando falamos sobre os serviços de identidade do Azure.

|Termo a conhecer| Descrição|
|-----|-----|
|Assinatura do Azure |As assinaturas são usadas para pagar pelos serviços de nuvem do Azure e normalmente são vinculadas a um cartão de crédito. Você pode ter várias assinaturas, mas pode ser difícil compartilhar recursos entre assinaturas.|
|Locatário do Azure | Um locatário do Azure AD é representativo de uma única organização. É uma instância dedicada e confiável do Azure AD criada automaticamente quando uma organização inscreve-se para uma assinatura do serviço de nuvem da Microsoft, como Azure, Intune ou Office 365. Os locatários podem obter acesso a serviços em um ambiente dedicado (locatário único) ou em um ambiente compartilhado com outras organizações (multilocatário).|
|Diretório do AD do Azure | Cada locatário do Azure tem um diretório do Azure AD e confiável que contém os usuários, os grupos e os aplicativos do locatário. Ele é usado para realizar as funções de gerenciamento de acesso e identidade para recursos de locatário. Uma vez que um diretório do Azure AD exclusivo é provisionado automaticamente para representar sua organização quando você se inscreve para um serviço de nuvem da Microsoft como o Azure, o Microsoft Intune ou o Office 365, às vezes você verá os termos *locatário*, *Azure AD* e *diretório do Azure AD* serem usados de maneira intercambiável. |
|Domínio personalizado | Quando você se inscreve pela primeira vez para uma assinatura de serviço de nuvem da Microsoft, seu locatário (organização) usa um nome de domínio *.onmicrosoft.com*. No entanto, a maioria das organizações tem um ou mais nomes de domínio que são usados para fazer negócios e que os usuários finais usam para acessar os recursos da empresa. Você pode adicionar seu nome de domínio personalizado ao Azure AD para que o nome de domínio seja familiar para seus usuários, como *alice@contoso.com*, em vez de *alice@contoso.onmicrosoft.com*. |
|Conta do AD do Azure | Essas são as identidades criadas usando o Azure AD ou outro serviço de nuvem da Microsoft, como o Office 365. Elas são armazenadas no Azure AD e ficam acessíveis a qualquer uma das assinaturas do serviço de nuvem da organização. |
|Administrador de assinatura do Azure| O administrador da conta é a pessoa que assinou ou comprou a assinatura do Azure. Eles podem usar o [Centro de Contas](https://account.azure.com/Subscriptions) e a realizar várias tarefas de gerenciamento, como criar assinaturas, cancelar assinaturas, alterar a cobrança de uma assinatura ou alterar o administrador de serviços. |
|Administrador Global do Azure AD | Os administradores globais do Azure AD têm acesso completo a todos os recursos administrativos do Azure AD. A pessoa que se inscreve para uma assinatura de serviço de nuvem da Microsoft automaticamente se torna um administrador global por padrão. Você pode ter mais de um administrador global, mas somente administradores globais podem atribuir qualquer uma das [outras funções de administrador](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) a usuários. |
|Conta da Microsoft | Contas da Microsoft (criadas por você para seu uso pessoal) fornecem acesso a todos os produtos e serviços de nuvem voltados ao consumidor, por exemplo, Outlook (Hotmail), OneDrive, Xbox LIVE ou Office 365. Essas identidades são criadas e armazenadas no sistema conta de identidade do consumidor da Microsoft executado pela Microsoft.|
|Contas corporativas ou de estudante | Contas corporativas ou de estudante (emitidas por um administrador para uso comercial/acadêmico) dão acesso a todos os serviços de nuvem da Microsoft no nível corporativo, como Azure, Intune ou Office 365.|


## <a name="concepts-to-understand"></a>Conceitos de entender

Agora que você conhece os termos básicos de identidade do Azure, deve saber mais sobre esses conceitos de identidade do Azure que o ajudarão a tomar uma decisão bem informada sobre o serviço de identidade do Azure.

|Conceito a entender |Descrição|
|-----|-----|
|[Como as assinaturas do Azure são associadas ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-how-subscriptions-associated-directory) |Cada assinatura do Azure tem uma relação de confiança com um diretório do Azure AD para autenticar usuários, serviços e dispositivos. *Várias assinaturas podem confiar no mesmo diretório do Azure AD, mas uma assinatura confiará apenas em um único diretório do Azure AD*. Essa relação de confiança é diferente da relação que uma assinatura tem com outros recursos do Azure (sites, bancos de dados e assim por diante), que são mais como recursos filho de uma assinatura. Se uma assinatura expirar, o acesso aos recursos associados à assinatura que não o Azure AD também será interrompido. No entanto, o diretório do Azure AD permanece no Azure, de modo que você pode associar outra assinatura a esse diretório e continuar a gerenciar recursos de locatário.|
|[Como funciona o licenciamento do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-get-started-azure-portal) | Quando você compra ou ativa o Enterprise Mobility Suite, Azure AD Premium ou Azure AD Basic, seu diretório é atualizado com a assinatura, incluindo período de validade e licenças pré-pagas. Quando a assinatura está ativa, o serviço pode ser gerenciado pelos administradores globais do Azure AD e usado por usuários licenciados. Suas informações de assinatura, incluindo o número de licenças atribuídas ou disponíveis, estão disponíveis no portal do Azure na folha **Azure Active Directory** > **Licenças**. Este é também o melhor lugar para gerenciar suas atribuições de licença.|
|[Controle de acesso baseado em função no Portal do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is)|O Azure RBAC (controle de acesso baseado em função) fornece o gerenciamento de acesso refinado para os recursos do Azure. Um excesso de permissões pode expor uma conta a invasores. Permissões insuficientes fazem com que os funcionários não consigam trabalhar com eficiência. Usando o RBAC, você pode oferecer aos funcionários as permissões exatas necessárias com base em três funções básicas que se aplicam a todos os grupos de recursos: proprietário, colaborador, leitor. Você também pode criar até 2.000 das suas próprias [funções de RBAC personalizadas](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) para atender às suas necessidades específicas. |
|[Identidade híbrida](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect)|Identidade híbrida é obtida integrando seu Windows Server Active Directory (AD DS) local com o Azure AD usando o [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect). Isso permite que você forneça uma identidade comum para seus usuários para Office 365, Azure e aplicativos locais ou aplicativos SaaS integrados ao Azure AD. Com identidade híbrida, você efetivamente estende seu ambiente local para a nuvem para acesso e identidade.|

### <a name="the-difference-between-windows-server-ad-ds-and-azure-ad"></a>A diferença entre o Windows Server AD DS e o Azure AD
Tanto o Azure AD (Azure Active Directory) quanto o Active Directory (Active Directory Domain Services ou AD DS) local são sistemas que armazenam dados de diretório e gerenciam a comunicação entre usuários e recursos, incluindo processos de logon do usuário, autenticação e pesquisas de diretório.

Se você já está familiarizado com o Windows Server AD DS (Active Directory Domain Services) local, lançado inicialmente com o Windows 2000 Server, provavelmente já conhece o conceito básico de um serviço de identidade. No entanto, também é importante entender que o Azure AD não é apenas um controlador de domínio na nuvem. É uma nova maneira de fornecer IDaaS (identidade como um serviço) no Azure que requer uma nova maneira de pensar para adotar totalmente os recursos baseados em nuvem e proteger sua organização contra ameaças modernas. 

O AD DS é uma função de servidor no Windows Server, o que significa que ele pode ser implantado em máquinas físicas ou virtuais. Ele tem uma estrutura hierárquica baseada em X.500. Ele usa o DNS para localizar objetos, usa basicamente Kerberos para autenticação e é possível interagir com ele usando LDAP. O Active Directory permite OUs (unidades organizacionais) e GPOs (Objetos de Política de Grupo), além do ingresso de máquinas no domínio e as relações de confiança são criadas entre domínios.

A TI protegeu seu perímetro de segurança por anos usando o AD DS, mas empresas modernas sem perímetro que dão suporte a necessidades de identidade para funcionários, clientes e parceiros precisam de um novo plano de controle. O Azure AD é esse plano de controle de identidade. A segurança foi além do firewall corporativo para a nuvem, na qual o Azure AD protege os recursos da empresa e o acesso fornecendo uma identidade comum para os usuários (localmente ou na nuvem). Isso dá aos usuários flexibilidade para acessar com segurança os aplicativos de que eles precisam para trabalhar de praticamente qualquer dispositivo. Também são fornecidos os controles de proteção contínua de dados baseada em risco, com o apoio de recursos de machine learning e relatórios detalhados, de que a TI precisa para manter os dados da empresa seguros.

O Azure AD é um serviço de diretório público para vários clientes, o que significa que dentro dele é possível criar um locatário para seus servidores e aplicativos de nuvem, como o Office 365. Usuários e grupos são criados em uma estrutura simples sem OUs ou GPOs. A autenticação é realizada por meio de protocolos como SAML, WS-Federation e OAuth. É possível consultar o Azure AD, mas em vez de usar o LDAP, você deve usar uma API REST chamado API do AD Graph. Todos eles usam HTTP e HTTPS.

### <a name="extend-office-365-management-and-security-capabilities"></a>Estender as capacidades de gerenciamento e segurança do Office 365
Já está usando o Office 365? Você pode acelerar sua transformação digital estendendo os recursos internos do Office 365 com o Azure AD para proteger todos os seus recursos, habilitando produtividade segura para toda a sua equipe. Quando você usa o Azure AD, além dos recursos do Office 365, você pode proteger todo o seu portfólio de aplicativo com uma identidade que habilita logon único para todos os aplicativos. Você pode expandir os recursos de acesso condicional com base não apenas no estado do dispositivo, como também em usuário, local, aplicativo e risco. As capacidades de MFA (Autenticação Multifator) fornecem ainda mais proteção quando necessário. Você obterá supervisão adicional de privilégios do usuário e fornecerá acesso administrativo sob demanda just-in-time. Os usuários serão mais produtivos e criarão menos chamados de assistência técnica graças aos recursos de autoatendimento que o Azure AD oferece, como redefinir senhas esquecidas, solicitações de acesso ao aplicativo e criação e gerenciamento de grupos.

> [!TIP]
> Quer saber mais sobre como usar o gerenciamento de identidade do Azure AD com o Office 365? [Obtenha o livro eletrônico](https://info.microsoft.com/Extend-Office-365-security-with-EMS.html).

## <a name="microsoft-azure-identity-solutions"></a>Soluções de identidade do Microsoft Azure

O Microsoft Azure oferece várias maneiras de gerenciar as identidades dos usuários, não importa se elas são mantidas de modo totalmente local, somente na nuvem ou até mesmo em algum lugar entre essas duas opções. Essas opções incluem: AD DS do tipo DIY (faça você mesmo) no Azure, Azure AD (Azure Active Directory), Identidade Híbrida e Azure AD Domain Services.

### <a name="do-it-yourself-diy-ad-ds"></a>AD DS do tipo DIY (faça você mesmo)
Para empresas que precisam apenas de um pequeno espaço na nuvem, é possível usar o **AD DS do tipo DIY (faça você mesmo)** no Azure. Essa opção dá suporte a vários cenários do Windows Server AD DS apropriados para implantação como VMs (máquinas virtuais) no Azure. Por exemplo, você pode criar uma VM do Azure como um controlador de domínio em execução em um datacenter distante conectado à rede remota. De lá, a VM seria capaz de dar suporte a solicitações de autenticação de usuários remotos e melhorar o desempenho de autenticação. Essa opção também é adequada como um substituto de custo relativamente baixo para locais de recuperação de desastres de outra forma dispendiosos ao hospedar um pequeno número de controladores de domínio e uma única rede virtual no Azure. Por fim, talvez você queira implantar um aplicativo no Azure, como o SharePoint, que requer o Windows Server AD DS, mas não tenha nenhuma dependência da rede local nem do Windows Server Active Directory corporativo. Nesse caso, você poderia implantar uma floresta isolada no Azure para atender aos requisitos do farm de servidores do SharePoint. Também há suporte para a implantação de aplicativos de rede que exigem conectividade com a rede local e o Active Directory local.

### <a name="azure-active-directory-azure-ad"></a>Active Directory do Azure (Azure AD)
O **Azure AD autônomo** é uma solução de IDaaS (identidade e gerenciamento de acesso como um serviço) totalmente baseada em nuvem. O Azure AD fornece um conjunto robusto de capacidades para gerenciar usuários e grupos. Ele ajuda a assegurar o acesso a aplicativos locais e na nuvem, incluindo serviços Web da Microsoft como o Office 365 e muitos aplicativos SaaS (software como serviço) não Microsoft. O Azure AD é fornecido em três edições: Gratuita, Básica e Premium. O Azure AD aumenta a eficácia organizacional e amplia a segurança para além do firewall de perímetro para um novo plano de controle protegido pelo Azure Machine Learning e outros recursos de segurança avançados.

### <a name="hybrid-identity"></a>Identidade híbrida
Em vez de escolher entre soluções de identidade baseada em nuvem ou locais, muitos CIOs e negócios de vanguarda, que começaram a prever a direção de longo prazo da empresa, estão estendendo seus diretórios locais para a nuvem por meio de soluções de **identidade híbrida**. Com identidade híbrida, você receberá uma solução global de gerenciamento de acesso e identidade que dá acesso seguro e produtivo aos aplicativos de que os usuários precisam para trabalhar.

> [!TIP]
> Para saber mais sobre como os CIOs tornaram o Azure Active Directory uma parte central de suas estratégias de TI, baixe o [Guia do CIO para o Azure Active Directory](https://aka.ms/AzureADCIOGuide).

### <a name="azure-ad-domain-services"></a>Azure AD Domain Services
O **Azure AD Domain Services** oferece uma opção baseada em nuvem para usar o AD DS para controle simples de configuração da VM do Azure e uma maneira de atender aos requisitos locais de identidade para teste e desenvolvimento de aplicativos de rede. O Azure AD Domain Services não se destina a migrar usando o método lift-and-shift a sua infraestrutura local do AD DS para VMs do Azure gerenciadas pelo Azure AD Domain Services. Em vez disso, as VMs do Azure em domínios gerenciados devem ser usadas para dar suporte a desenvolvimento, teste e movimentação de aplicativos locais, que exigem métodos de autenticação do AD DS, para a nuvem.

## <a name="common-scenarios-and-recommendations"></a>Cenários e recomendações comuns

Aqui estão alguns dos cenários comuns de identidade e acesso com recomendações sobre qual opção de identidade do Azure pode ser mais apropriada para cada um.

|Cenário de identidade| Recomendações|
|-----|-----|
|Minha organização fez grandes investimentos no Windows Server Active Directory local, mas quer estender a identidade para a nuvem.| A solução de identidade do Azure mais amplamente usados é a [identidade híbrida](https://docs.microsoft.com/azure/active-directory/active-directory-hybrid-identity-design-considerations-overview). Se você já tiver feito investimentos no AD DS local, poderá estender facilmente a identidade para a nuvem usando o Azure AD Connect.|
|Minha empresa nasceu na nuvem e não temos investimentos em soluções de identidade locais.| O [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) é a melhor opção para empresas somente em nuvem sem investimentos locais.|
|Preciso de controle e configuração simples de VM do Azure para atender aos requisitos de identidade locais para teste e desenvolvimento de aplicativos.|O [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) é uma boa opção para quem precisa usar o AD DS para controle simples de configuração de VM do Azure ou está procurando desenvolver ou migrar aplicativos locais herdados e com reconhecimento de diretório para a nuvem.|  
|Preciso dar suporte a algumas máquinas virtuais no Azure, mas minha empresa ainda investe intensamente no AD DS (Active Directory) local.|Use [AD DS do tipo DIY](https://msdn.microsoft.com/library/azure/jj156090.aspx) para usar VMs do Azure quando você precisar dar suporte a algumas máquinas virtuais e tiver grandes investimentos em AD DS local. |

## <a name="where-can-i-learn-more"></a>Onde posso saber mais?
Temos inúmeros ótimos recursos online para ajudá-lo a aprender tudo sobre o AD do Azure. Aqui está uma lista de artigos excelentes para você começar:

* [Habilitando seu diretório para gerenciamento híbrido com o Azure AD Connect](active-directory-aadconnect.md)
* [Segurança adicional para um mundo sempre conectado](../multi-factor-authentication/multi-factor-authentication.md)
* [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](active-directory-saas-app-provisioning.md)
* [Introdução aos Relatórios do AD do Azure](active-directory-reporting-getting-started.md)
* [Gerenciar suas senhas de qualquer lugar](active-directory-passwords.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](active-directory-saas-app-provisioning.md)
* [Como fornecer acesso remoto seguro a aplicativos locais](active-directory-application-proxy-get-started.md)
* [Gerenciamento de acesso a recursos com grupos do Active Directory do Azure](active-directory-manage-groups.md)
* [O que é o licenciamento do Active Directory do Microsoft Azure?](active-directory-licensing-what-is.md)
* [Como descobrir aplicativos na nuvem não aprovados, usados em minha organização](active-directory-cloudappdiscovery-whatis.md)

## <a name="next-steps"></a>Próximas etapas

Agora que você entende os conceitos de identidade do Azure e as opções disponíveis, pode usar os seguintes recursos para começar a implementar a opção escolhida:

[Saiba mais sobre as soluções de identidade híbridas do Azure](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution)

[Saiba mais em um ambiente de prova de conceito do Azure](https://aka.ms/aad-poc)

[Implantar o Azure AD em produção](https://aka.ms/aad-onboard)
