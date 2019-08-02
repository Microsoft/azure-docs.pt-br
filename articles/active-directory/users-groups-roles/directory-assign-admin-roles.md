---
title: Descrições e permissões da função de administrador – Azure Active Directory | Microsoft Docs
description: Uma função de administrador pode adicionar usuários, atribuir funções administrativas, redefinir senhas de usuário, gerenciar licenças de usuário ou gerenciar domínios.
services: active-directory
author: curtand
manager: mtillman
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/17/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24d3da81fabf55bc0c3944f0c03829dee4fcce46
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304405"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Permissões da função de administrador no Azure Active Directory

Usando o Azure Active Directory (AD do Azure), você pode designar administradores limitados para gerenciar tarefas de identidade em funções com menos privilégios. Os administradores podem ser atribuídos para tais finalidades como adição ou alteração de usuários, atribuição de funções administrativas, redefinição de senhas de usuário, gerenciamento de licenças de usuário e gerenciamento de nomes de domínio. As permissões de usuário padrão podem ser alteradas somente nas configurações do usuário no Azure AD.

O administrador global tem acesso a todos os recursos administrativos. Por padrão, a pessoa que se inscreve para uma assinatura do Azure recebe a função de Administrador Global para o diretório. Apenas Administradores Globais e Administradores de Funções Privilegiadas podem delegar funções de administrador. Para reduzir o risco comercial, recomendamos que você atribua essa função apenas a algumas pessoas na empresa.

## <a name="assign-or-remove-administrator-roles"></a>Atribuir ou remover funções de administrador

Para saber como atribuir funções administrativas a um usuário no Azure Active Directory, veja [Exibir e atribuir funções de administrador no Azure Active Directory](directory-manage-roles-portal.md).

## <a name="available-roles"></a>Funções disponíveis

As seguintes funções de administrador estão disponíveis:

* **[Administrador de Aplicativos](#application-administrator)** : Os usuários nessa função podem criar e gerenciar todos os aspectos de aplicativos empresariais, registros dos aplicativos e configurações de proxy de aplicativos. Essa função também concede a capacidade de consentimento para permissões delegadas e permissões do aplicativo excluindo o Microsoft Graph e o Microsoft Azure AD Graph. Os usuários atribuídos a essa função não são adicionados como proprietários ao criar novos registros de aplicativo ou aplicativos empresariais.

  <b>Importante</b>: Essa função concede a capacidade de gerenciar credenciais de aplicativos. Os usuários atribuídos a essa função podem adicionar credenciais a um aplicativo e usar essas credenciais para representar a identidade do aplicativo. Se a identidade do aplicativo tiver acesso ao Azure Active Directory, como a capacidade de criar ou atualizar o usuário ou outros objetos, um usuário atribuído a essa função poderá executar essas ações enquanto estiver representando o aplicativo. Essa capacidade de representar a identidade do aplicativo pode ser uma elevação de privilégio sobre o que o usuário pode fazer por meio de suas atribuições de função no Azure AD. É importante entender que atribuir um usuário à função de administrador do aplicativo permite que ele represente a identidade de um aplicativo.

* **[Desenvolvedor de Aplicativos](#application-developer)** : Os usuários nessa função podem criar registros dos aplicativos quando a configuração "Usuários podem registrar aplicativos" estiver definida como Não. Essa função também concede permissão para consentimento em um nome próprio quando a configuração "os usuários podem consentir com aplicativos que acessam dados da empresa em seu nome" está definida como não. Os usuários atribuídos a essa função são adicionados como proprietários ao criar novos registros de aplicativo ou aplicativos empresariais.

* **[Administrador de Autenticação](#authentication-administrator)** : Usuários com essa função podem definir ou redefinir credenciais de não senha e podem atualizar senhas para todos os usuários. Os administradores de autenticação podem exigir que os usuários se registrem novamente em relação à credencial não-senha existente (por exemplo, MFA ou FIDO) e revogue **lembram a MFA no dispositivo**, que solicita a MFA na próxima entrada de usuários que não são administradores ou atribuídas apenas as seguintes funções:
  * Administrador de Autenticação
  * Leitores de Diretório
  * Emissor do Convite ao Convidado
  * Leitor do Centro de Mensagens
  * Leitor de Relatórios

  A função Administrador de autenticação está atualmente em visualização pública. Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

  <b>Importante</b>: Usuários com essa função podem alterar credenciais de pessoas que podem ter acesso a informações confidenciais ou particulares ou a configurações críticas dentro e fora do Azure Active Directory. A alteração das credenciais de um usuário pode significar a capacidade de assumir a identidade e as permissões do usuário. Por exemplo:

  * Proprietários de Registro de Aplicativo e Aplicativos Empresariais, que podem gerenciar credenciais de aplicativos que eles possuem. Esses aplicativos podem ter permissões privilegiadas no Azure AD e em outro lugar que não foram concedidas a Administradores de Autenticação. Por meio desse caminho, um administrador de autenticação pode assumir a identidade de um proprietário do aplicativo e assumir ainda mais a identidade de um aplicativo com privilégios atualizando as credenciais para o aplicativo.
  * Proprietários de assinaturas do Azure, que podem ter acesso a informações confidenciais ou privadas ou configurações críticas no Azure.
  * Proprietários de Grupos de Segurança e de Grupos do Office 365, que podem gerenciar a associação de grupo. Esses grupos podem conceder acesso a informações confidenciais ou privadas ou configurações críticas no Azure AD e em outros lugares.
  * Administradores em outros serviços fora do Azure AD, como o Exchange Online, a Segurança do Office e o Centro de Conformidade e sistemas de recursos humanos.
  * Não administradores, como executivos, o departamento jurídico e os funcionários de recursos humanos, que podem ter acesso a informações confidenciais ou privadas.

* **[Administrador da proteção de informações do Azure](#azure-information-protection-administrator)** : Usuários com essa função têm todas as permissões no serviço de Proteção de Informações do Azure. Esta função pode configurar rótulos para a política da Proteção de Informações do Azure, gerenciar modelos de proteção e ativar a proteção. Esta função não garante permissões de usuário no Identity Protection Center, Privileged Identity Management, Monitorar Integridade de Serviço do Office 365 ou Centro de Segurança e Conformidade do Office 365.

* **[Administrador do fluxo de usuário do B2C](#b2c-user-flow-administrator)** : Os usuários com essa função podem criar e gerenciar Fluxos dos Usuários B2C (também conhecido como políticas "internas") no portal do Azure. Ao criar ou editar fluxos de usuário, esses usuários podem alterar o conteúdo HTML/CSS/JavaScript da experiência do usuário, alterar os requisitos de MFA por fluxo de usuário, alterar as declarações no token e ajustar as configurações de sessão para todas as políticas no locatário. Por outro lado, essa função não inclui a capacidade de revisar os dados do usuário ou fazer alterações nos atributos que estão incluídos no esquema do locatário. As alterações feitas nas políticas da estrutura de experiência de identidade (também conhecidas como personalizadas) também estão fora do escopo dessa função.

* **[Administrador de atributos de fluxo do usuário B2C](#b2c-user-flow-attribute-administrator)** : Os usuários com essa função adicionam ou excluem atributos personalizados disponíveis para todos os fluxos de usuário no locatário. Dessa forma, os usuários com essa função podem alterar ou adicionar novos elementos ao esquema do usuário final e impactar o comportamento de todos os fluxos do usuário e resultar indiretamente em alterações em quais dados podem ser solicitados aos usuários finais e, por fim, enviados como declarações para os aplicativos. Essa função não pode editar fluxos de usuário.

* Administrador do conjunto de **[chaves B2C IEF](#b2c-ief-keyset-administrator)** :    O usuário pode criar e gerenciar chaves de política e segredos para criptografia de token, assinaturas de token e criptografia/descriptografia de declaração. Ao adicionar novas chaves a contêineres de chave existentes, esse administrador limitado pode sobrepor os segredos conforme necessário, sem afetar os aplicativos existentes. Esse usuário pode ver o conteúdo completo desses segredos e suas datas de expiração mesmo após a criação.
    
  <b>Importante:</b> essa é uma função confidencial. A função de administrador do conjunto de chaves deve ser cuidadosamente auditada e atribuída com cuidado durante a pré-produção e a produção.

* **[Administrador da política do IEF B2C](#b2c-ief-policy-administrator)** : Os usuários nessa função têm a capacidade de criar, ler, atualizar e excluir todas as políticas personalizadas no Azure AD B2C e, portanto, ter controle total sobre a estrutura de experiência de identidade no locatário do Azure AD B2C relevante. Editando políticas, esse usuário pode estabelecer a Federação direta com provedores de identidade externos, alterar o esquema de diretório, alterar todo o conteúdo voltado para o usuário (HTML, CSS, JavaScript), alterar os requisitos para concluir uma autenticação, criar novos usuários, enviar dados do usuário para sistemas externos, incluindo migrações completas, e editar todas as informações do usuário, incluindo campos confidenciais, como senhas e números de telefone. Por outro lado, essa função não pode alterar as chaves de criptografia ou editar os segredos usados para federação no locatário.

  <b>Importante:</b> O administrador da política IEF B2 é uma função altamente confidencial que deve ser atribuída de forma muito limitada para locatários em produção. As atividades por esses usuários devem ser rigorosamente auditadas, especialmente para locatários em produção.

* **[Administrador de Cobrança](#billing-administrator)** : Faz compras, gerencia assinaturas, gerencia tíquetes de suporte e monitora a integridade do serviço.

* **[Administrador de Aplicativos de Nuvem](#cloud-application-administrator)** : Os usuários nessa função têm as mesmas permissões que a função Administrador de Aplicativos, excluindo a capacidade de gerenciar o proxy de aplicativo. Essa função concede a capacidade de criar e gerenciar todos os aspectos de aplicativos corporativos e os registros do aplicativo. Essa função também concede a capacidade de consentimento para permissões delegadas e permissões do aplicativo excluindo o Microsoft Graph e o Microsoft Azure AD Graph. Os usuários atribuídos a essa função não são adicionados como proprietários ao criar novos registros de aplicativo ou aplicativos empresariais.

  <b>Importante</b>: Essa função concede a capacidade de gerenciar credenciais de aplicativos. Os usuários atribuídos a essa função podem adicionar credenciais a um aplicativo e usar essas credenciais para representar a identidade do aplicativo. Se a identidade do aplicativo tiver acesso ao Azure Active Directory, como a capacidade de criar ou atualizar o usuário ou outros objetos, um usuário atribuído a essa função poderá executar essas ações enquanto estiver representando o aplicativo. Essa capacidade de representar a identidade do aplicativo pode ser uma elevação de privilégio sobre o que o usuário pode fazer por meio de suas atribuições de função no Azure AD. É importante entender que atribuir um usuário à função de administrador do Cloud Application permite que ele represente a identidade de um aplicativo.

* **[Administrador de Dispositivo de Nuvem](#cloud-device-administrator)** : Os usuários nessa função podem habilitar, desabilitar e excluir dispositivos no Azure AD e ler chaves do Windows 10 BitLocker (se houver) no portal do Azure. A função não concede permissões para gerenciar nenhuma outra propriedade no dispositivo.

* **[Administrador de Conformidade](#compliance-administrator)** : Os usuários com essa função têm permissões para gerenciar recursos relacionados à conformidade no centro de conformidade do Microsoft 365, no centro de administração do Microsoft 365, no Azure e no Centro de Conformidade e Segurança do Office 365. Os usuários também podem gerenciar todos os recursos no centro de administração do Exchange e no centro de administração do Teams e do Skype for Business e criar tíquetes de suporte para o Azure e o Microsoft 365. Há mais informações disponíveis em [Sobre as funções de administrador do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

  No | O que ele pode fazer
  ----- | ----------
  [Centro de conformidade do Microsoft 365](https://protection.office.com) | Proteger e gerenciar dados da sua organização em todos os serviços do Microsoft 365<br>Gerenciar alertas de conformidade
  [Gerenciador de Conformidade](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Acompanhar, atribuir e verificar as atividades de conformidade regulatória da sua organização
  [Centro de Conformidade e Segurança do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gerenciar a governança de dados<br>Executar investigação jurídica e de dados<br>Gerenciar solicitação do titular dos dados
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Exibir todos os dados de auditoria do Intune
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Tem permissões somente leitura e pode gerenciar alertas<br>Pode criar e modificar políticas de arquivo e permitir ações de governança de arquivo<br> Pode exibir todos os relatórios internos em Gerenciamento de Dados

* **[Administrador de dados de conformidade](#compliance-data-administrator)** : Os usuários com essa função têm permissões para proteger e rastrear dados no centro de conformidade Microsoft 365, no centro de administração Microsoft 365 e no Azure. Os usuários também podem gerenciar todos os recursos no Centro de administração do Exchange, no Gerenciador de Conformidade e no Centro de administração do Teams e do Skype for Business, além de criar tíquetes de suporte para o Azure e o Microsoft 365.

  No | O que ele pode fazer
  ----- | ----------
  [Centro de conformidade do Microsoft 365](https://protection.office.com) | Monitorar políticas relacionadas à conformidade em serviços de Microsoft 365<br>Gerenciar alertas de conformidade
  [Gerenciador de Conformidade](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Acompanhar, atribuir e verificar as atividades de conformidade regulatória da sua organização
  [Centro de Conformidade e Segurança do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gerenciar a governança de dados<br>Executar investigação jurídica e de dados<br>Gerenciar solicitação do titular dos dados
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Exibir todos os dados de auditoria do Intune
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Tem permissões somente leitura e pode gerenciar alertas<br>Pode criar e modificar políticas de arquivo e permitir ações de governança de arquivo<br> Pode exibir todos os relatórios internos em Gerenciamento de Dados

* **[Administrador do Acesso Condicional](#conditional-access-administrator)** : Os usuários com essa função têm a capacidade de gerenciar Azure Active Directory configurações de acesso condicional.
  > [!NOTE]
  > Para implantar a política de acesso condicional do Exchange ActiveSync no Azure, o usuário também deve ser um administrador global.
  
* **[Aprovador de acesso do Sistema de Proteção de Dados do Cliente](#customer-lockbox-access-approver)** : gerencia [solicitações do Sistema de Proteção de Dados do Cliente](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests) em sua organização. O aprovador recebe notificações de solicitações do Sistema de Proteção de Dados do Cliente por email e pode aprovar e negar solicitações do Centro de administração do Microsoft 365. Ele também pode ligar ou desligar o recurso Sistema de Proteção de Dados do Cliente. Somente os administradores globais podem redefinir as senhas das pessoas atribuídas à função acima.
  <!--  This was announced in August of 2018. https://techcommunity.microsoft.com/t5/Security-Privacy-and-Compliance/Customer-Lockbox-Approver-Role-Now-Available/ba-p/223393-->

* **[Administrador do desktop Analytics](#desktop-analytics-administrator)** : Os usuários nessa função podem gerenciar a análise de desktops e a personalização do Office & serviços de política. Para análise de desktops, isso inclui a capacidade de exibir o inventário de ativos, criar planos de implantação, exibir o status de integridade e implantação. Para a personalização do Office & serviço de política, essa função permite que os usuários gerenciem as políticas do Office.

* **[Administrador do dispositivo](#device-administrators)** : Essa função está disponível para atribuição apenas como um administrador local adicional em [Configurações do dispositivo](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Os usuários com essa função se tornam administradores de computador local em todos os dispositivos Windows 10 associados ao Azure Active Directory. Eles não têm a capacidade de gerenciar objetos de dispositivos no Azure Active Directory. 

* **[Leitores de Diretório](#directory-readers)** : Essa é uma função que deve ser atribuída somente a aplicativos herdados que não dão suporte à [estrutura de consentimento](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Não o atribua aos usuários.

* **[Contas de Sincronização de Diretório](#directory-synchronization-accounts)** : Não use. Essa função é automaticamente atribuída ao serviço do Azure AD Connect e não tem intenção ou suporte para outros usos.

* **[Gravadores de Diretório](#directory-writers)** : Essa é uma função herdada que deve ser atribuída a aplicativos que não tenham suporte em [Estrutura de Consentimento](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Ele não deve ser atribuído a nenhum usuário.

* **[Administrador do Dynamics 365/Administrador do CRM](#crm-service-administrator)** : Os usuários com essa função têm permissões globais no Microsoft Dynamics 365 Online, quando o serviço está presente, bem como a capacidade de gerenciar tíquete de suporte e monitorar a integridade do serviço. Mais informações em [usar a função de administrador de serviço para gerenciar seu locatário](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).
  > [!NOTE] 
  > Na API do Microsoft Graph, na API do Azure AD Graph e no PowerShell do Azure AD, a função é identificada como "Administrador do Dynamics 365 Service". É "Administrador do Dynamics 365" no [portal do Azure](https://portal.azure.com).

* **[Administrador do Exchange](#exchange-service-administrator)** : Os usuários com essa função têm permissões globais no Microsoft Exchange Online, quando o serviço está presente. Eles também tem a capacidade de criar e gerenciar todos os Grupos do Office 365, gerenciar tíquetes de suporte e monitorar a integridade do serviço. Mais informações em [Sobre funções de administrador do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > Na API do Microsoft Graph, na API do Graph do Azure AD e no Azure AD PowerShell, essa função é identificada como "Exchange Service Administrator". É "Administrador do Exchange" no [portal do Azure](https://portal.azure.com). É o "Administrador do Exchange Online" no [Centro de Administração do Exchange](https://go.microsoft.com/fwlink/p/?LinkID=529144). 

* **[Administrador do provedor de identidade externo](#external-identity-provider-administrator)** : Esse administrador gerencia a Federação entre Azure Active Directory locatários e provedores de identidade externos. Com essa função, os usuários podem adicionar novos provedores de identidade e definir todas as configurações disponíveis (por exemplo, caminho de autenticação, ID de serviço, contêineres de chave atribuídos). Esse usuário pode habilitar o locatário para confiar em autenticações de provedores de identidade externos. O impacto resultante sobre as experiências do usuário final depende do tipo de locatário:
  * Azure Active Directory locatários para funcionários e parceiros: A adição de uma federação (por exemplo, com o Gmail) afetará imediatamente todos os convites de convidados que ainda não foram resgatados. Consulte [adicionando o Google como um provedor de identidade para usuários convidados B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).
  * Azure Active Directory B2C locatários: A adição de uma federação (por exemplo, com o Facebook ou com outro Azure Active Directory) não afeta imediatamente os fluxos do usuário final até que o provedor de identidade seja adicionado como uma opção em um fluxo de usuário (também conhecido como diretiva interna). Consulte [Configurando um conta Microsoft como um provedor de identidade](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app) para obter um exemplo. Para alterar os fluxos de usuário, é necessário ter a função limitada de "administrador de fluxo de usuário do B2C".

* **[Administrador Global/Administrador de Empresa](#company-administrator)** : Os usuários com essa função têm acesso a todos os recursos administrativos do Azure Active Directory, bem como aos serviços que usam identidades do Azure Active Directory como centro de segurança do Microsoft 365, centro de conformidade do Microsoft 365, Exchange Online, SharePoint Online e Skype for Business Online. A pessoa que se inscreve no locatário do Azure Active Directory torna-se um administrador global. Somente os administradores globais podem atribuir outras funções de administrador. Pode haver mais de um administrador global na sua empresa. Administradores globais podem redefinir a senha para qualquer usuário e todos os outros administradores.

  > [!NOTE]
  > Na API do Graph da Microsoft, na API do Graph do Azure AD e no Azure AD PowerShell, essa função é identificada como "Administrador da Empresa". É "Administrador Global" no [portal do Azure](https://portal.azure.com).
  >
  >

* **[Emissor do Convite ao Convidado](#guest-inviter)** : Usuários nessa função podem gerenciar convites de usuários convidados do Azure Active Directory B2B quando a configuração do usuário **Membros podem convidar** estiver definida como Não. Mais informações sobre a colaboração B2B em [Sobre a colaboração B2B do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Ela não inclui nenhuma outra permissão.

* **[Administrador do Intune](#intune-service-administrator)** : Usuários com essa função têm permissões globais no Microsoft Intune Online, quando o serviço está presente. Além disso, essa função contém a capacidade de gerenciar usuários e dispositivos para associar a política, bem como criar e gerenciar grupos. Mais informações em [Controle de administração baseada em função (RBAC) com o Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control)
  > [!NOTE]
  > Na API do Microsoft Graph, na API do Graph do Azure AD e no Azure AD PowerShell, essa função é identificada como "Administrador do Serviço do Intune". É "Administrador do Intune" no [portal do Azure](https://portal.azure.com).
  
 * **[Administrador do Kaizala](#kaizala-administrator)** : Os usuários com essa função têm permissões globais para gerenciar configurações no Microsoft Kaizala, quando o serviço está presente, bem como a capacidade de gerenciar tíquetes de suporte e monitorar a integridade do serviço.
Além disso, o usuário pode acessar relatórios relacionados à adoção & uso de Kaizala por membros da organização e relatórios comerciais gerados usando as ações do Kaizala. 

* **[Administrador de Licenças](#license-administrator)** : Usuários nessa função podem adicionar, remover e atualizar as atribuições de licenças em usuários, grupos (usando o licenciamento baseado em grupo) e gerenciar a localização de uso dos usuários. A função não concede a capacidade de comprar ou gerenciar assinaturas, criar ou gerenciar grupos, ou criar ou gerenciar usuários além do local de uso. Essa função não tem acesso para exibir, criar nem gerenciar tíquetes de suporte.

* **[Leitor de privacidade do centro de mensagens](#message-center-privacy-reader)** : Os usuários nessa função podem monitorar todas as notificações no centro de mensagens, incluindo mensagens de privacidade de dados. Os leitores de privacidade do centro de mensagens recebem notificações por email, incluindo aquelas relacionadas à privacidade dos dados, e podem cancelar a assinatura usando as preferências do centro de mensagens. Somente o administrador global e o leitor de privacidade do centro de mensagens podem ler mensagens de privacidade de dados. Além disso, essa função contém a capacidade de exibir grupos, domínios e assinaturas. Essa função não tem permissão para exibir, criar ou gerenciar solicitações de serviço.

* **[Leitor do Centro de Mensagens](#message-center-reader)** : Usuários nessa função podem monitorar notificações e atualizações de integridade de consultoria no [Centro de Mensagens do Office 365](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) da organização em serviços configurados como Exchange, Intune e Microsoft Teams. Os Leitores do Centro de Mensagens recebem por email resumos semanais de postagens, atualizações e podem compartilhar postagens do Centro de Mensagens no Office 365. No Azure AD, os usuários atribuídos a essa função terão acesso somente leitura aos serviços do Azure AD como usuários e grupos. Essa função não tem acesso para exibir, criar nem gerenciar tíquetes de suporte.

* **[Suporte de Camada1 a Parceiros](#partner-tier1-support)** : Não use. Essa função foi substituída e será removida do Azure AD no futuro. Essa função é destinada a um pequeno número de parceiros de revenda da Microsoft e não se destina ao uso geral.

* **[Suporte de Camada2 a Parceiros](#partner-tier2-support)** : Não use. Essa função foi substituída e será removida do Azure AD no futuro. Essa função é destinada a um pequeno número de parceiros de revenda da Microsoft e não se destina ao uso geral.

* **[Administrador da assistência técnica (senha)](#helpdesk-administrator)** : Usuários com essa função podem alterar senhas, invalidar tokens de atualização, gerenciar solicitações de serviço e monitorar a integridade do serviço. Invalidar um token de atualização força o usuário a entrar novamente. Os administradores de assistência técnica podem redefinir senhas e invalidar tokens de atualização de outros usuários que não são administradores ou que atribuíram as seguintes funções somente:
  * Leitores de Diretório
  * Emissor do Convite ao Convidado
  * Administrador de assistência técnica
  * Leitor do Centro de Mensagens
  * Leitor de Relatórios
  
  <b>Importante</b>: Usuários com essa função podem alterar senhas de pessoas que podem ter acesso a informações confidenciais ou particulares ou a configurações críticas dentro e fora do Azure Active Directory. A alteração da senha de um usuário pode significar a capacidade de assumir a identidade e as permissões do usuário. Por exemplo:
  * Proprietários de Registro de Aplicativo e Aplicativos Empresariais, que podem gerenciar credenciais de aplicativos que eles possuem. Esses aplicativos podem ter permissões privilegiadas no Azure AD e em outro lugar, não concedidas a Administradores de Assistência Técnica. Por esse caminho, um Administrador de Assistência Técnica pode ser capaz de assumir a identidade de um proprietário de aplicativo e, depois, assumir a identidade de um aplicativo com privilégios, atualizando as credenciais do aplicativo.
  * Proprietários de assinaturas do Azure, que podem ter acesso a informações confidenciais ou privadas ou configurações críticas no Azure.
  * Proprietários de Grupos de Segurança e de Grupos do Office 365, que podem gerenciar a associação de grupo. Esses grupos podem conceder acesso a informações confidenciais ou privadas ou configurações críticas no Azure AD e em outros lugares.
  * Administradores em outros serviços fora do Azure AD, como o Exchange Online, a Segurança do Office e o Centro de Conformidade e sistemas de recursos humanos.
  * Não administradores, como executivos, o departamento jurídico e os funcionários de recursos humanos, que podem ter acesso a informações confidenciais ou privadas.


  > [!NOTE]
  > A delegação de permissões administrativas em subconjuntos de usuários e aplicação de políticas a um subconjunto de usuários é possível com [unidades administrativas (versão prévia)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units).


  > [!NOTE]
  > Essa função anteriormente era chamada de "administrador de senha" no [portal do Azure](https://portal.azure.com/). Estamos alterando seu nome para "administrador de assistência técnica" para corresponder ao nome no PowerShell do Azure AD, no Azure AD API do Graph e na API de Microsoft Graph. Por um curto período, alteraremos o nome para "administrador da assistência técnica (senha)" em portal do Azure antes da alteração para "administrador de assistência técnica".


* **[Administrador do Power BI](#power-bi-service-administrator)** : Usuários com essa função têm permissões globais no Microsoft Power BI, quando o serviço está presente, bem como a capacidade de gerenciar tíquetes de suporte e monitorar a integridade do serviço. Mais informações em [Noções básicas sobre a função de administrador do Power BI](https://docs.microsoft.com/power-bi/service-admin-role).
  > [!NOTE]
  > Na API do Microsoft Graph, na API do Graph do Azure AD e no Azure AD PowerShell, essa função é identificada como "Administrador do Serviço do Power BI". É "Administrador do Power BI" no [portal do Azure](https://portal.azure.com).

* **[Administrador de autenticação privilegiada](#privileged-authentication-administrator)** : Os usuários com essa função podem definir ou redefinir credenciais de não senha para todos os usuários, incluindo administradores globais, e podem atualizar senhas para todos os usuários. Os administradores de autenticação privilegiada podem forçar os usuários a se registrarem novamente em relação à credencial não-senha existente (por exemplo, MFA, FIDO) e revogar "lembrar MFA no dispositivo", solicitando a MFA no próximo logon de todos os usuários. Os administradores de autenticação privilegiada podem:
  * Forçar os usuários a se registrar novamente em relação a credenciais de não senha existentes (por exemplo, MFA, FIDO)
  * REVOKE ' lembrar MFA no dispositivo ', solicitando a MFA no próximo logon

* **[Administrador com Função com Privilégios](#privileged-role-administrator)** : Usuários com essa função podem gerenciar as atribuições de função no Azure Active Directory, bem como Azure Active Directory Privileged Identity Management. Além disso, essa função permite o gerenciamento de todos os aspectos de Privileged Identity Management e de unidades administrativas.

  <b>Importante</b>: Essa função concede a capacidade de gerenciar atribuições para todas as funções do Azure AD, incluindo a função de administrador global. Essa função não inclui outras habilidades privilegiadas no Azure AD, como criar ou atualizar usuários. No entanto, os usuários atribuídos a essa função podem conceder a si mesmos ou aos privilégios adicionais de outras pessoas atribuindo funções adicionais.

* **[Leitor de Relatórios](#reports-reader)** : Os usuários com essa função podem exibir dados de relatório de uso e o painel relatórios no centro de administração Microsoft 365 e o pacote de contexto de adoção no Power BI. Além disso, a função fornece acesso a relatórios de entrada e atividades no Azure AD e a dados retornados pela API de relatórios do Microsoft Graph. Um usuário atribuído à função Leitor de Relatórios pode acessar somente o uso relevante e as métricas de adoção. Eles não têm permissões de administrador para definir configurações ou acessar que os centros da administração de produtos específicos como o Exchange. Essa função não tem acesso para exibir, criar nem gerenciar tíquetes de suporte.

* **[Administrador de pesquisa](#search-administrator)** : Os usuários nesta função têm acesso completo a todos os recursos de gerenciamento do Microsoft Search no centro de administração Microsoft 365. Os administradores de pesquisa podem delegar os administradores de pesquisa e as funções do editor de pesquisa aos usuários, bem como criar e gerenciar conteúdo, como indicadores, p & como e locais. Além disso, esses usuários podem exibir o centro de mensagens, monitorar a integridade do serviço e criar solicitações de serviço.

* **[Editor de pesquisa](#search-editor)** : Os usuários nessa função podem criar, gerenciar e excluir conteúdo do Microsoft Search no centro de administração Microsoft 365, incluindo indicadores, p & como e locais.

* **[Administrador de Segurança](#security-administrator)** : Os usuários com essa função têm permissões para gerenciar recursos relacionados à segurança na central de segurança do Microsoft 365, Azure Active Directory Identity Protection, Proteção de Informações do Azure e Centro de Conformidade e Segurança do Office 365. Mais informações sobre permissões do Office 365 estão disponíveis em [Permissões no Centro de Conformidade de Segurança do Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).
  
  No | O que ele pode fazer
  --- | ---
  [Central de segurança do Microsoft 365](https://protection.office.com) | Monitorar políticas relacionadas a segurança em todos os serviços do Microsoft 365<br>Gerenciar alertas e ameaças de segurança<br>Exibir relatórios
  Identity Protection Center | Todas as permissões da função Leitor de Segurança<br>Além disso, a habilidade de executar todas as operações do Centro de Proteção de Identidade, exceto redefinir senhas
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Todas as permissões da função Leitor de Segurança<br>**Não é possível** gerenciar as atribuições ou configurações de função do Azure AD
  [Centro de Conformidade e Segurança do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gerenciar políticas de segurança<br>Exibir, investigar e responder a ameaças de segurança<br>Exibir relatórios
  Proteção Avançada contra Ameaças do Azure | Monitorar e responder a atividades suspeitas de segurança
  Windows Defender ATP e EDR | Atribuir funções<br>Gerenciar grupos de computadores<br>Configurar a detecção de ameaças do ponto de extremidade e a correção automatizada<br>Exibir, investigar e responder a alertas
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Exibe informações de usuário, dispositivo, registro, configuração e aplicativo<br>Não pode fazer alterações no Intune
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Adicionar administradores, adicionar políticas e configurações, carregar logs e executar ações de governança
  [Central de Segurança do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Pode exibir políticas de segurança, exibir estados de segurança, editar políticas de segurança, exibir alertas e recomendações, ignorar alertas e recomendações
  [Integridade do serviço do Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Exibir a integridade de serviços do Office 365

* **[Operador de segurança](#security-operator)** : Os usuários com essa função podem gerenciar alertas e ter acesso somente leitura global no recurso relacionado à segurança, incluindo todas as informações na central de segurança Microsoft 365, Azure Active Directory, proteção de identidade, Privileged Identity Management e Office 365 Centro de Conformidade e Segurança. Mais informações sobre permissões do Office 365 estão disponíveis em [Permissões no Centro de Conformidade de Segurança do Office 365](https://docs.microsoft.com/office365/securitycompliance/permissions-in-the-security-and-compliance-center).

  No | O que ele pode fazer
  --- | ---
  [Central de segurança do Microsoft 365](https://protection.office.com) | Todas as permissões da função Leitor de Segurança<br>Exibir, investigar e responder a alertas de ameaças de segurança
  Identity Protection Center | Todas as permissões da função Leitor de Segurança<br>Além disso, a habilidade de executar todas as operações do Centro de Proteção de Identidade, exceto redefinir senhas
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Todas as permissões da função Leitor de Segurança
  [Centro de Conformidade e Segurança do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Todas as permissões da função Leitor de Segurança<br>Exibir, investigar e responder a alertas de segurança
  Windows Defender ATP e EDR | Todas as permissões da função Leitor de Segurança<br>Exibir, investigar e responder a alertas de segurança
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Todas as permissões da função Leitor de Segurança
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Todas as permissões da função Leitor de Segurança
  [Integridade do serviço do Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Exibir a integridade de serviços do Office 365
<!--* **[Security Operator](#security-operator)**: Users with this role can manage alerts and have global read-only access on security-related feature, including all information in Microsoft 365 security center, Azure Active Directory, Identity Protection, Privileged Identity Management.-->

* **[Leitor de Segurança](#security-reader)** : Usuários com essa função têm acesso somente leitura global em recurso relacionado à segurança, incluindo todas as informações no centro de segurança do Microsoft 365, no Azure Active Directory, no Identity Protection e no Privileged Identity Management, bem como a capacidade de ler logs de auditoria e relatórios de entrada do Azure Active Directory e no Centro de Conformidade e Segurança do Office 365. Mais informações sobre permissões do Office 365 estão disponíveis em [Permissões no Centro de Conformidade de Segurança do Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

  No | O que ele pode fazer
  --- | ---
  [Central de segurança do Microsoft 365](https://protection.office.com) | Exibir políticas relacionadas à segurança em todos os serviços do Microsoft 365<br>Exibir alertas e ameaças de segurança<br>Exibir relatórios
  Identity Protection Center | Ler todos os relatórios de segurança e informações de configurações para recursos de segurança<br><ul><li>Anti-spam<li>Criptografia<li>Prevenção de perda de dados<li>Antimalware<li>Proteção avançada contra ameaças<li>Antiphishing<li>Regras de fluxo de mensagens
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Tem acesso somente leitura a todas as informações exibidas no PIM do Azure AD: Políticas e relatórios para atribuições de função do Azure AD, revisões de segurança e, futuramente, acesso de leitura a dados de política e relatórios para cenários, além da atribuição de função do Azure AD.<br>**Não pode** se inscrever no Azure AD PIM nem fazer alterações nele. No portal do PIM ou por meio do PowerShell, alguém nessa função pode ativar funções adicionais (por exemplo, administrador global ou administradores de função com privilégios), se o usuário estiver qualificado para eles.
  [Centro de Conformidade e Segurança do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Exibir políticas de segurança<br>Exibir e investigar ameaças de segurança<br>Exibir relatórios
  Windows Defender ATP e EDR | Exibir e investigar alertas
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Exibe informações de usuário, dispositivo, registro, configuração e aplicativo. Não pode fazer alterações no Intune.
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Tem permissões somente leitura e pode gerenciar alertas
  [Central de Segurança do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Pode exibir recomendações e alertas, exibir políticas de segurança, exibir estados de segurança, mas não pode fazer alterações
  [Integridade do serviço do Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Exibir a integridade de serviços do Office 365

* **[Administrador de Serviço de Suporte](#service-support-administrator)** : Os usuários com essa função podem abrir solicitações de suporte com a Microsoft para serviços do Azure e do Office 365 e exibir o painel de serviço e o centro de mensagens no [portal do Azure](https://portal.azure.com) e [Microsoft 365 centro de administração](https://admin.microsoft.com). Mais informações em [Sobre funções de administrador do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > Na API do Microsoft Graph, na API do Graph do Azure AD e no Azure AD PowerShell, essa função é identificada como "Administrador de Suporte de Serviço". É "administrador de serviços" na [portal do Azure](https://portal.azure.com), no [centro de administração de Microsoft 365](https://admin.microsoft.com)e no portal do Intune.

* **[Administrador do SharePoint](#sharepoint-service-administrator)** : Usuários com essa função têm permissões globais no Microsoft SharePoint Online, quando o serviço está presente, bem como a capacidade de criar e gerenciar todos os Grupos do Office 365, gerenciar tíquetes de suporte e monitorar a integridade do serviço. Mais informações em [Sobre funções de administrador do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > Na API do Microsoft Graph, na API do Graph do Azure AD e no Azure AD PowerShell, essa função é identificada como "Administrador do Serviço do SharePoint". É "Administrador do SharePoint" no [portal do Azure](https://portal.azure.com).

* **[Administrador do Lync/Skype for Business](#lync-service-administrator)** : Usuários com essa função têm permissões globais no Microsoft Skype for Business, quando o serviço está presente, além de gerenciar atributos de usuário específicos do Skype no Azure Active Directory. Além disso, essa função concede a capacidade de gerenciar tíquetes de suporte e monitorar a integridade do serviço, além de acessar o centro de administração do Skype for Business e do Teams. A conta também deve ser licenciada para o Teams ou não poderá executar os cmdlets do PowerShell do Teams. Mais informações em [Sobre a função de administrador do Skype for Business](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) e informações de licenciamento do Teams em [licenciamento de complemento do Skype for Business e Microsoft Teams](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

  > [!NOTE]
  > Na API do Graph da Microsoft, na API do Graph do Azure AD e no Azure AD PowerShell, essa função é identificada como "Administrador de Serviços do Lync". É "Administrador do Skype for Business" no [portal do Azure](https://portal.azure.com/).

* **[Administrador de Equipes](#teams-service-administrator)** : Usuários nessa função podem gerenciar todos os aspectos da carga de trabalho do Microsoft Teams pelo centro de administração do Microsoft Teams e Skype for Business e respectivos módulos do PowerShell. Isso inclui, entre outras áreas, todas as ferramentas de gerenciamento relacionadas a telefonia, mensagens, reuniões e às próprias equipes. Além disso, essa função concede a capacidade de criar e gerenciar todos os Grupos do Office 365, gerenciar tíquetes de suporte e monitorar a integridade do serviço.
  > [!NOTE]
  > Na API do Microsoft Graph, na API do Graph do Azure AD e no Azure AD PowerShell, essa função é identificada como "Administrador do Serviço de Equipes". É "Administrador de Equipes" no [portal do Azure](https://portal.azure.com).

* **[Administrador de Comunicações de Equipes](#teams-communications-administrator)** : Usuários nessa função podem gerenciar aspectos da carga de trabalho do Microsoft Teams relacionados a voz e telefonia. Isso inclui as ferramentas de gerenciamento para atribuição de número de telefone, políticas de reuniões e voz e acesso completo ao conjunto de ferramentas de análise de chamada.

* **[Engenheiro de Suporte de Comunicações de Equipes](#teams-communications-support-engineer)** : Usuários nessa função podem solucionar problemas de comunicação no Microsoft Teams e Skype for Business usando as ferramentas de solução de problemas de chamada de usuário no centro de administração do Microsoft Teams e Skype for Business. Os usuários nesta função podem exibir informações do registro de chamadas completas para todos os participantes envolvidos. Essa função não tem acesso para exibir, criar nem gerenciar tíquetes de suporte.

* **[Especialista de Suporte de Comunicações de Equipes](#teams-communications-support-specialist)** : Usuários nessa função podem solucionar problemas de comunicação no Microsoft Teams e Skype for Business usando as ferramentas de solução de problemas de chamada de usuário no centro de administração do Microsoft Teams e Skype for Business. Os usuários nessa função só podem exibir detalhes do usuário na chamada para o usuário específico que eles pesquisaram. Essa função não tem acesso para exibir, criar nem gerenciar tíquetes de suporte.

* **[Administrador do usuário](#user-administrator)** : Os usuários com essa função podem criar usuários e gerenciar todos os aspectos de usuários com algumas restrições (veja abaixo) e podem atualizar as políticas de expiração de senha. Além disso, os usuários com essa função podem criar e gerenciar todos os grupos. Essa função também inclui a capacidade de criar e gerenciar exibições de usuários, gerenciar tickets de suporte e monitorar a integridade do serviço.

  | | |
  | --- | --- |
  |Permissões gerais|<p>Criar usuários e grupos</p><p>Criar e gerenciar modos de exibição do usuário</p><p>Gerenciar tíquetes de suporte do Office<p>Atualizar políticas de expiração de senha|
  |<p>Em todos os usuários, inclusive todos os administradores</p>|<p>Gerenciar licenças</p><p>Gerenciar todas as propriedades de usuário, exceto o nome Principal do usuário</p>
  |Somente em usuários não administradores ou em qualquer um destes procedimentos limitada de funções de administrador:<ul><li>Leitores de Diretório<li>Emissor do Convite ao Convidado<li>Administrador de assistência técnica<li>Leitor do Centro de Mensagens<li>Leitor de Relatórios<li>Administrador de usuários|<p>Excluir e restauração</p><p>Desativar e ativar</p><p>Invalidar Tokens de atualização</p><p>Gerenciar todas as propriedades de usuário, incluindo o nome Principal do usuário</p><p>Redefinir senha</p><p>Atualizar chaves de dispositivo FIDO)</p>
  
  <b>Importante</b>: Usuários com essa função podem alterar senhas de pessoas que podem ter acesso a informações confidenciais ou particulares ou a configurações críticas dentro e fora do Azure Active Directory. A alteração da senha de um usuário pode significar a capacidade de assumir a identidade e as permissões do usuário. Por exemplo:
  * Proprietários de Registro de Aplicativo e Aplicativos Empresariais, que podem gerenciar credenciais de aplicativos que eles possuem. Esses aplicativos podem ter permissões privilegiadas no Azure AD e em outro lugar, não concedida a Administradores de Usuário. Por esse caminho, um Administrador de Usuário pode ser capaz de assumir a identidade de um proprietário de aplicativo e, depois, assumir a identidade de um aplicativo com privilégios, atualizando as credenciais do aplicativo.
  * Proprietários de assinaturas do Azure, que podem ter acesso a informações confidenciais ou privadas ou configurações críticas no Azure.
  * Proprietários de Grupos de Segurança e de Grupos do Office 365, que podem gerenciar a associação de grupo. Esses grupos podem conceder acesso a informações confidenciais ou privadas ou configurações críticas no Azure AD e em outros lugares.
  * Administradores em outros serviços fora do Azure AD, como o Exchange Online, a Segurança do Office e o Centro de Conformidade e sistemas de recursos humanos.
  * Não administradores, como executivos, o departamento jurídico e os funcionários de recursos humanos, que podem ter acesso a informações confidenciais ou privadas.

## <a name="role-permissions"></a>Permissões de Função
As tabelas a seguir descrevem as permissões específicas no Azure Active Directory fornecidas a cada função. Algumas funções podem ter permissões adicionais nos serviços da Microsoft fora do Azure Active Directory.

### <a name="application-administrator"></a>Administrador de aplicativos
Pode criar e gerenciar todos os aspectos de registros de aplicativo e aplicativos empresariais.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Atualize a propriedade applications.audience no Azure Active Directory. |
| microsoft.aad.directory/applications/authentication/update | Atualize a propriedade applications.authentication no Azure Active Directory. |
| microsoft.aad.directory/applications/basic/update | Atualize as propriedades básicas dos aplicativos no Active Directory do Azure. |
| microsoft.aad.directory/applications/create | Crie aplicativos no Active Directory do Azure. |
| microsoft.aad.directory/applications/credentials/update | Atualize a propriedade applications.credentials no Azure Active Directory. |
| microsoft.aad.directory/applications/delete | Excluir aplicativos no Active Directory do Azure. |
| microsoft.aad.directory/applications/owners/update | Atualize a propriedade applications.owners no Azure Active Directory. |
| microsoft.aad.directory/applications/permissions/update | Atualize a propriedade applications.permissions no Azure Active Directory. |
| microsoft.aad.directory/applications/policies/update | Atualize a propriedade applications.policies no Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Crie appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/read | Leia appRoleAssignments no Active Directory do Azure. |
| microsoft.aad.directory/appRoleAssignments/update | Atualize o appRoleAssignments no Active Directory do Azure. |
| microsoft.aad.directory/appRoleAssignments/delete | Exclua appRoleAssignments em Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em auditLogs no Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Ler policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Atualize policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Crie políticas no Active Directory do Azure. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Exclua policies em Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Ler policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Atualize policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Ler policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Atualize a propriedade Approleassignedto no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Atualizar servicePrincipals.appRoleAssignments property em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Atualizar a propriedade servicePrincipals.audience no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Atualizar a propriedade servicePrincipals.authentication no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Atualize as propriedades básicas em servicePrincipals no Active Directory do Azure. |
| microsoft.aad.directory/servicePrincipals/create | Criar servicePrincipals em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Atualizar a propriedade servicePrincipals.credentials no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Excluir servicePrincipals em Azure Active Directory. |
| microsoft.aad.dimicrosoft.aad.directory/servicePrincipals/owners/updaterectory/servicePrincipals/owners/update | Atualizar servicePrincipals.owners property em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Atualizar a propriedade servicePrincipals.permissions no Azure Active Directory. |
| microsoftmicrosoft.aad.directory/servicePrincipals/policies/update.aad.directory/servicePrincipals/policies/update | Atualizar servicePrincipals.policies property in Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em signInReports no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="application-developer"></a>Desenvolvedor de aplicativos
Pode criar registros de aplicativos independentemente da configuração "Usuários podem registrar aplicativos".

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | Crie aplicativos no Active Directory do Azure. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| microsoft.aad.directory/appRoleAssignments/createAsOwner | Crie appRoleAssignments no Azure Active Directory. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| microsoft.aad.directory/oAuth2PermissionGrants/createAsOwner | Crie oAuth2PermissionGrants no Azure Active Directory. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| microsoft.aad.directory/servicePrincipals/createAsOwner | Criar servicePrincipals em Azure Active Directory. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |

### <a name="authentication-administrator"></a>Administrador de Autenticação
Permitido para exibir, definir e redefinir as informações de método de autenticação para qualquer usuário não administrador.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidar todos os tokens de atualização de usuário no Azure Active Directory. |
| microsoft.aad.directory/users/strongAuthentication/update | Atualize propriedades de autenticação forte, como informações de credencial MFA. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |
| Microsoft.AAD.Directory/Users/Password/Update | Atualize as senhas de todos os usuários na organização do Office 365. Consulte a documentação online para obter mais detalhes. |

### <a name="azure-information-protection-administrator"></a>Administrador da proteção de informações do Azure
Pode gerenciar todos os aspectos do serviço de proteção de informações do Azure.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Gerencie todos os aspectos da proteção de informações do Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="b2c-user-flow-administrator"></a>Administrador de Fluxo de Usuário B2C
Crie e gerencie todos os aspectos de fluxos de usuário.

| **Ações** | **Descrição** |
| --- | --- |
| Microsoft. AAD. B2C/userflows/tarefas | Ler e configurar fluxos de usuário no Azure Active Directory B2C. |

### <a name="b2c-user-flow-attribute-administrator"></a>Administrador de Atributo de Fluxo do Usuário B2C
Crie e gerencie o esquema de atributo disponível para todos os fluxos de usuário.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.b2c/userAttributes/allTasks | Ler e configurar atributos de usuário no Azure Active Directory B2C. |

### <a name="b2c-ief-keyset-administrator"></a>Administrador de Conjunto de Chaves IEF B2C
Gerencie segredos para Federação e criptografia na estrutura de experiência de identidade.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/keySets/allTasks | Ler e configurar conjuntos de chaves em Azure Active Directory B2C. |

### <a name="b2c-ief-policy-administrator"></a>Administrador de Política IEF B2C
Crie e gerencie políticas de estrutura confiável na estrutura de experiência de identidade.

| **Ações** | **Descrição** |
| --- | --- |
| Microsoft. AAD. B2C/trustFramework/Policies/TaskId | Ler e configurar políticas personalizadas no Azure Active Directory B2C. |

### <a name="billing-administrator"></a>Administrador de cobrança
Pode executar tarefas comuns de relacionadas à cobrança, como atualizar informações de pagamento.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/organization/basic/update | Atualize as propriedades básicas em organização no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Gerencie todos os aspectos de cobrança do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |


### <a name="cloud-application-administrator"></a>Administrador de Aplicativos de Nuvem
Pode criar e gerenciar todos os aspectos de registros de aplicativo e aplicativos empresariais, exceto o Proxy de Aplicativo.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Atualize a propriedade applications.audience no Azure Active Directory. |
| microsoft.aad.directory/applications/authentication/update | Atualize a propriedade applications.authentication no Azure Active Directory. |
| microsoft.aad.directory/applications/basic/update | Atualize as propriedades básicas dos aplicativos no Active Directory do Azure. |
| microsoft.aad.directory/applications/create | Crie aplicativos no Active Directory do Azure. |
| microsoft.aad.directory/applications/credentials/update | Atualize a propriedade applications.credentials no Azure Active Directory. |
| microsoft.aad.directory/applications/delete | Excluir aplicativos no Active Directory do Azure. |
| microsoft.aad.directory/applications/owners/update | Atualize a propriedade applications.owners no Azure Active Directory. |
| microsoft.aad.directory/applications/permissions/update | Atualize a propriedade applications.permissions no Azure Active Directory. |
| microsoft.aad.directory/applications/policies/update | Atualize a propriedade applications.policies no Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Crie appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Atualize o appRoleAssignments no Active Directory do Azure. |
| microsoft.aad.directory/appRoleAssignments/delete | Exclua appRoleAssignments em Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em auditLogs no Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Crie políticas no Active Directory do Azure. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Ler policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Atualize policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Exclua policies em Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Ler policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Atualize policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Ler policies.applicationConfiguration property em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Atualize a propriedade Approleassignedto no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Atualizar servicePrincipals.appRoleAssignments property em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Atualizar a propriedade servicePrincipals.audience no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Atualizar a propriedade servicePrincipals.authentication no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Atualize as propriedades básicas em servicePrincipals no Active Directory do Azure. |
| microsoft.aad.directory/servicePrincipals/create | Criar servicePrincipals em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Atualizar a propriedade servicePrincipals.credentials no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Excluir servicePrincipals em Azure Active Directory. |
| microsoft.aad.dimicrosoft.aad.directory/servicePrincipals/owners/updaterectory/servicePrincipals/owners/update | Atualizar servicePrincipals.owners property em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Atualizar a propriedade servicePrincipals.permissions no Azure Active Directory. |
| microsoftmicrosoft.aad.directory/servicePrincipals/policies/update.aad.directory/servicePrincipals/policies/update | Atualizar servicePrincipals.policies property in Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em signInReports no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="cloud-device-administrator"></a>Administrador de Dispositivo de Nuvem
Acesso completo para gerenciar os dispositivos no Azure AD.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em auditLogs no Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Ler a propriedade devices.bitLockerRecoveryKeys no Azure Active Directory. |
| Microsoft.AAD.Directory/Devices/Delete | Exclua dispositivos no Azure Active Directory. |
| Microsoft.AAD.Directory/Devices/Disable | Desabilite dispositivos no Azure Active Directory. |
| microsoft.aad.directory/devices/enable | Habilite dispositivos no Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em signInReports no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |

### <a name="company-administrator"></a>Administradores de Empresa
Pode gerenciar todos os aspectos do Azure AD e dos serviços da Microsoft que usam identidades do Azure AD.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Criar e excluir todos os recursos e ler e atualizar as propriedades padrão em microsoft.aad.cloudAppSecurity. |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | Criar e excluir administrativeUnits e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/applications/allProperties/allTasks | Criar e excluir aplicativos e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/allProperties/allTasks | Criar e excluir appRoleAssignments e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em auditLogs no Azure Active Directory. |
| microsoft.aad.directory/contacts/allProperties/allTasks | Criar e excluir contatos e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/contracts/allProperties/allTasks | Criar e excluir contratos e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/devices/allProperties/allTasks | Criar e excluir dispositivos e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/allProperties/allTasks | Criar e excluir DirectoryRoles, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/directoryRoleTemplates/allProperties/allTasks | Criar e excluir DirectoryRoleTemplates, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/domains/allProperties/allTasks | Criar e excluir Domínios, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/groups/allProperties/allTasks | Criar e excluir Grupos, e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/groupSettings/allProperties/allTasks | Criar e excluir groupSettings e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/allProperties/allTasks | Criar e excluir groupSettingTemplates e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/loginTenantBranding/allProperties/allTasks | Criar e excluir loginTenantBranding e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/allProperties/allTasks | Criar e excluir oAuth2PermissionGrants e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/organization/allProperties/allTasks | Criar e excluir organização e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/policies/allProperties/allTasks | Criar e excluir políticas, ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | Criar e excluir roleAssignments e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Criar e excluir roleDefinitions e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/scopedRoleMemberships/allProperties/allTasks | Criar e excluir scopedRoleMemberships e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/serviceAction/activateService | Pode executar a ação de serviço Activateservice no Azure Active Directory |
| microsoft.aad.directory/serviceAction/disableDirectoryFeature | Pode executar a ação de serviço Disabledirectoryfeature no Azure Active Directory |
| microsoft.aad.directory/serviceAction/enableDirectoryFeature | Pode executar a ação de serviço Enabledirectoryfeature no Azure Active Directory |
| microsoft.aad.directory/serviceAction/getAvailableExtentionProperties | Pode executar a ação de serviço Getavailableextentionproperties no Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/allProperties/allTasks | Criar e excluir servicePrincipals e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em signInReports no Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/allProperties/allTasks | Criar e excluir subscribedSkus e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directory/users/allProperties/allTasks | Criar e excluir usuários e ler e atualizar todas as propriedades no Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Executar todas as ações no Azure AD Connect. |
| microsoft.aad.identityProtection/allEntities/allTasks | Criar e excluir todos os recursos e ler e atualizar propriedades padrão em microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Ler todos os recursos em microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Ler todos os recursos em microsoft.azure.advancedThreatProtection. |
| microsoft.azure.informationProtection/allEntities/allTasks | Gerencie todos os aspectos da proteção de informações do Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Gerencie todos os aspectos de cobrança do Office 365. |
| microsoft.intune/allEntities/allTasks | Gerencie todos os aspectos do Intune. |
| Microsoft.office365.complianceManager/allEntities/allTasks | Gerenciar todos os aspectos do Gerenciador de conformidade do Office 365 |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Gerenciar todos os aspectos da Análise de Área de Trabalho. |
| Microsoft.office365.Exchange/allEntities/allTasks | Gerencie todos os aspectos do Exchange Online. |
| Microsoft.office365.lockbox/allEntities/allTasks | Gerenciar todos os aspectos do Cofre de cliente do Office 365 |
| microsoft.office365.messageCenter/messages/read | Ler mensagens em microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Ler securityMessages em microsoft.office365.messageCenter. |
| Microsoft.office365.protectionCenter/allEntities/allTasks | Gerencie todos os aspectos do Centro de proteção do Office 365. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Criar e excluir todos os recursos e ler e atualizar as propriedades padrão em microsoft.office365.securityComplianceCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| Microsoft.office365.SharePoint/allEntities/allTasks | Criar e excluir todos os recursos e ler e atualizar propriedades padrão em microsoft.office365.sharepoint. |
| Microsoft.office365.skypeForBusiness/allEntities/allTasks | Gerencie todos os aspectos do Skype for Business Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |
| Microsoft.office365.usageReports/allEntities/Read | Leia os relatórios de uso do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Gerencie todos os aspectos do Dynamics 365. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Gerencie todos os aspectos do Power BI. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Ler todos os recursos em microsoft.windows.defenderAdvancedThreatProtection. |

### <a name="compliance-administrator"></a>Administrador de conformidade
Pode ler e gerenciar a configuração de conformidade e relatórios no Azure AD e no Office 365.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| Microsoft.office365.complianceManager/allEntities/allTasks | Gerenciar todos os aspectos do Gerenciador de conformidade do Office 365 |
| Microsoft.office365.Exchange/allEntities/allTasks | Gerencie todos os aspectos do Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| Microsoft.office365.SharePoint/allEntities/allTasks | Criar e excluir todos os recursos e ler e atualizar propriedades padrão em microsoft.office365.sharepoint. |
| Microsoft.office365.skypeForBusiness/allEntities/allTasks | Gerencie todos os aspectos do Skype for Business Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="compliance-data-administrator"></a>Administrador de dados de conformidade
Cria e gerencia o conteúdo de conformidade.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Ler e configurar Microsoft Cloud App Security. |
| microsoft.azure.informationProtection/allEntities/allTasks | Gerencie todos os aspectos da proteção de informações do Azure. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| Microsoft.office365.complianceManager/allEntities/allTasks | Gerenciar todos os aspectos do Gerenciador de conformidade do Office 365 |
| Microsoft.office365.Exchange/allEntities/allTasks | Gerencie todos os aspectos do Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| Microsoft.office365.SharePoint/allEntities/allTasks | Criar e excluir todos os recursos e ler e atualizar propriedades padrão em microsoft.office365.sharepoint. |
| Microsoft.office365.skypeForBusiness/allEntities/allTasks | Gerencie todos os aspectos do Skype for Business Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="conditional-access-administrator"></a>Administrador de acesso condicional
Pode gerenciar recursos de acesso condicional.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/policies/conditionalAccess/basic/read | Ler a propriedade policies.conditionalAccess no Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/basic/update | Atualize a propriedade policies.conditionalAccess no Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/create | Crie políticas no Active Directory do Azure. |
| microsoft.aad.directory/policies/conditionalAccess/delete | Exclua policies em Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/read | Ler a propriedade policies.conditionalAccess no Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/update | Atualize a propriedade policies.conditionalAccess no Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/policiesAppliedTo/read | Ler a propriedade policies.conditionalAccess no Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/tenantDefault/update | Atualize a propriedade policies.conditionalAccess no Azure Active Directory. |

### <a name="crm-service-administrator"></a>Administrador de serviços do CRM
Pode gerenciar todos os aspectos do produto Dynamics 365.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Gerencie todos os aspectos do Dynamics 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="customer-lockbox-access-approver"></a>Aprovador de acesso do cofre do cliente
Pode aprovar solicitações de suporte da Microsoft para acessar dados organizacionais do cliente.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| Microsoft.office365.lockbox/allEntities/allTasks | Gerenciar todos os aspectos do Cofre de cliente do Office 365 |

### <a name="desktop-analytics-administrator"></a>Administrador de Análise de Área de Trabalho
O pode gerenciar a análise de desktops e a personalização do Office & serviços de política. Para análise de desktops, isso inclui a capacidade de exibir o inventário de ativos, criar planos de implantação, exibir o status de integridade e implantação. Para a personalização do Office & serviço de política, essa função permite que os usuários gerenciem as políticas do Office.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Gerenciar todos os aspectos da Análise de Área de Trabalho. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="device-administrators"></a>Administradores de Dispositivo
Os usuários atribuídos a essa função são adicionados ao grupo local de administradores em dispositivos ingressados no Azure AD.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/groupSettings/basic/read | Ler as propriedades básicas no groupSettings no Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Ler as propriedades básicas no groupSettingTemplates no Azure Active Directory. |

### <a name="directory-readers"></a>Leitores de Diretório
Pode ler informações básicas do diretório. Para conceder acesso a aplicativos, não destinado a usuários.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/basic/read | Ler as propriedades básicas em administrativeUnits no Azure Active Directory. |
| microsoft.aad.directory/administrativeUnits/members/read | Ler a propriedade Administrativeunits no Azure Active Directory. |
| microsoft.aad.directory/applications/basic/read | Ler as propriedades básicas em aplicativos do Azure Active Directory. |
| Microsoft.AAD.Directory/Applications/Owners/Read | Ler a propriedade Owners no Azure Active Directory. |
| microsoft.aad.directory/applications/policies/read | Leia a propriedade applications.policies no Active Directory do Azure. |
| microsoft.aad.directory/contacts/basic/read | Ler as propriedades básicas em contatos no Azure Active Directory. |
| microsoft.aad.directory/contacts/memberOf/read | Ler a propriedade Contacts no Azure Active Directory. |
| microsoft.aad.directory/contracts/basic/read | Ler as propriedades básicas sobre os contratos no Azure Active Directory. |
| microsoft.aad.directory/devices/basic/read | Ler as propriedades básicas em dispositivos no Azure Active Directory. |
| microsoft.aad.directory/devices/memberOf/read | Ler a propriedade de memberOf no Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/read | Ler a propriedade registeredowners no Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/read | Ler a propriedade registeredusers no Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/basic/read | Ler as propriedades básicas no directoryRoles no Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/eligibleMembers/read | Ler a propriedade Eligiblemembers no Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/members/read | Ler a propriedade Directoryroles no Azure Active Directory. |
| microsoft.aad.directory/domains/basic/read | Leia as propriedades básicas em domínios no Active Directory do Azure. |
| microsoft.aad.directory/groups/appRoleAssignments/read | Leia a propriedade groups.appRoleAssignments no Active Directory do Azure. |
| microsoft.aad.directory/groups/basic/read | Leia as propriedades básicas em grupos no Active Directory do Azure. |
| microsoft.aad.directory/groups/memberOf/read | Leia a propriedade groups.memberOf no Active Directory do Azure. |
| microsoft.aad.directory/groups/members/read | Leia a propriedade groups.members no Azure Active Directory. |
| microsoft.aad.directory/groups/owners/read | Leia a propriedade groups.owners no Active Directory do Azure. |
| microsoft.aad.directory/groups/settings/read | Leia a propriedade groups.settings no Active Directory do Azure. |
| microsoft.aad.directory/groupSettings/basic/read | Ler as propriedades básicas no groupSettings no Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Ler as propriedades básicas no groupSettingTemplates no Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/basic/read | Leia as propriedades básicas em oAuth2PermissionGrants no Active Directory do Azure. |
| microsoft.aad.directory/organization/basic/read | Leia as propriedades básicas da organização no Active Directory do Azure. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | Leia a propriedade organization.trustedCAsForPasswordlessAuth no Active Directory do Azure. |
| microsoft.aad.directory/roleAssignments/basic/read | Leia as propriedades básicas em roleAssignments no Azure Active Directory. |
| microsoft.aad.directory/roleDefinitions/basic/read | Leia as propriedades básicas em roleDefinitions no Active Directory do Azure. |
| microsoft.aad.directormicrosoft.aad.directory/servicePrincipals/appRoleAssignedTo/ready/servicePrincipals/appRoleAssignedTo/read | Ler a propriedade Approleassignedto no Ler a propriedade Approleassignedto no Azure Active Directory.Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Ler a propriedade ServicePrincipals.AppRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/read | Ler as propriedades básicas em entidades de serviço no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Ler a propriedade servicePrincipals.oAuth2PermissionGrants no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Ler a propriedade servicePrincipals.policies no Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/basic/read | Ler as propriedades básicas no subscribedSkus no Azure Active Directory. |
| microsoft.aad.directory/umicrosoft.aad.directory/users/appRoleAssignments/readsers/appRoleAssignments/read | Leia a propriedade users.appRoleAssignments no Active Directory do Azure. |
| microsoft.aad.directory/users/basic/read | Leia as propriedades básicas dos usuários no Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Leia a propriedade users.directReports no Active Directory do Azure. |
| microsoft.aad.directory/users/manager/read | Leia a propriedade users.manager no Active Directory do Azure. |
| microsoft.aad.directory/users/memberOf/read | Leia a propriedade users.memberOf no Active Directory do Azure. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Leia a propriedade users.oAuth2PermissionGrants no Active Directory do Azure. |
| microsoft.aad.directory/users/ownedDevices/read | Leia a propriedade users.ownedDevices no Active Directory do Azure. |
| microsoft.aad.directory/users/ownedObjects/read | Leia a propriedade users.ownedObjects no Active Directory do Azure. |
| microsoft.aad.directory/users/registeredDevices/read | Leia a propriedade users.registeredDevices no Active Directory do Azure. |

### <a name="directory-synchronization-accounts"></a>Contas de sincronização de diretório
Apenas usado pelo serviço do Azure AD Connect.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/organization/dirSync/update | Atualize a propriedade organization.dirSync no Azure Active Directory. |
| Microsoft.AAD.Directory/Policies/Create | Crie políticas no Active Directory do Azure. |
| Microsoft.AAD.Directory/Policies/Delete | Exclua policies em Azure Active DirectoryExclua políticas no Azure Active Directory. |
| microsoft.aad.directory/policies/basic/read | Ler as propriedades básicas em políticas no Azure Active Directory. |
| microsoft.aad.directory/policies/basic/update | Atualize as propriedades básicas em políticas no Azure Active Directory. |
| Microsoft.AAD.Directory/Policies/Owners/Read | Ler a propriedade Owners no Azure Active Directory. |
| Microsoft.AAD.Directory/Policies/Owners/Update | Atualize a propriedade Owners no Azure Active Directory. |
| microsoft.aad.directory/policies/policiesAppliedTo/read | Ler a propriedade policies.policiesAppliedTo no Azure Active Directory. |
| microsoft.aad.directory/policies/tenantDefault/update | Atualizar a propriedade policies.tenantDefault no Azure Active Directory. |
| microsoft.aad.directormicrosoft.aad.directory/servicePrincipals/appRoleAssignedTo/ready/servicePrincipals/appRoleAssignedTo/read | Ler a propriedade Approleassignedto no Ler a propriedade Approleassignedto no Azure Active Directory.Azure Active Directory. |
| microsoftmicrosoft.aad.directory/servicePrincipals/appRoleAssignedTo/update.aad.directory/servicePrincipals/appRoleAssignedTo/update | Atualize a propriedade Approleassignedto no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Ler a propriedade ServicePrincipals.AppRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Atualizar servicePrincipals.appRoleAssignments property em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Atualizar a propriedade servicePrincipals.audience no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Atualizar a propriedade servicePrincipals.authentication no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/read | Ler as propriedades básicas em entidades de serviço no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Atualize as propriedades básicas em servicePrincipals no Active Directory do Azure. |
| microsoft.aad.directory/servicePrincipals/create | Criar servicePrincipals em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Atualizar a propriedade servicePrincipals.credentials no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Ler a propriedade servicePrincipals.oAuth2PermissionGrants no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.aad.dimicrosoft.aad.directory/servicePrincipals/owners/updaterectory/servicePrincipals/owners/update | Atualizar servicePrincipals.owners property em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Ler a propriedade Serviceprincipals no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Atualizar a propriedade servicePrincipals.permissions no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Ler a propriedade servicePrincipals.policies no Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Atualizar servicePrincipals.policies property in Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Executar todas as ações no Azure AD Connect. |

### <a name="directory-writers"></a>Gravadores de diretório
Pode ler e gravar informações básicas do diretório. Para conceder acesso a aplicativos, não destinado a usuários.

| **Ações** | **Descrição** |
| --- | --- |
| Microsoft.AAD.Directory/Groups/Create | Crie grupos no Active Directory do Azure. |
| microsoft.aad.directory/groups/createAsOwner | Crie grupos no Active Directory do Azure. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.aad.directory/groups/basic/update | Atualize as propriedades básicas nos grupos do Active Directory do Azure. |
| Microsoft.AAD.Directory/Groups/Members/Update | Atualize a propriedade Groups no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Owners/Update | Atualize a propriedade Owners no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Settings/Update | Atualize a propriedade Groups no Azure Active Directory. |
| microsoft.aad.directory/groupSettings/basic/update | Atualize as propriedades básicas em groupSettings no Azure Active Directory. |
| microsoft.aad.directory/groupSettings/create | Crie groupSettings no Azure Active Directory. |
| microsoft.aad.directory/groupSettings/delete | Exclua groupSettings no Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gerenciar licenças em usuários no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Basic/Update | Atualize as propriedades básicas nos usuários no Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidar todos os tokens de atualização de usuário no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Manager/Update | Atualize a propriedade Users no Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Atualize a propriedade users.userPrincipalName no Azure Active Directory. |

### <a name="exchange-service-administrator"></a>Administrador de serviços do Exchange
Pode gerenciar todos os aspectos do produto Exchange.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Atualize a propriedade groups.unified no Active Directory do Azure. |
| microsoft.aad.directory/groups/unified/basic/update | Atualizar as propriedades básicas de Grupos do Office 365. |
| microsoft.aad.directory/groups/unified/create | Criar Grupos do Office 365. |
| microsoft.aad.directory/groups/unified/delete | Excluir Grupos do Office 365. |
| microsoft.aad.directory/groups/unified/members/update | Atualizar associação de Grupos do Office 365. |
| microsoft.aad.directory/groups/unified/owners/update | Atualizar a propriedade de Grupos do Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| Microsoft.office365.Exchange/allEntities/allTasks | Gerencie todos os aspectos do Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="external-identity-provider-administrator"></a>Administrador do provedor de identidade externo
Configure os provedores de identidade para uso na Federação direta.

| **Ações** | **Descrição** |
| --- | --- |
| Microsoft. AAD. B2C/identityProviders/minhas tarefas | Ler e configurar provedores de identidade no Azure Active Directory B2C. |

### <a name="guest-inviter"></a>Emissor do Convite ao Convidado
Pode convidar usuários convidados independentemente da configuração "membros podem convidar pessoas".

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/umicrosoft.aad.directory/users/appRoleAssignments/readsers/appRoleAssignments/read | Leia a propriedade users.appRoleAssignments no Active Directory do Azure. |
| microsoft.aad.directory/users/basic/read | Leia as propriedades básicas dos usuários no Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Leia a propriedade users.directReports no Active Directory do Azure. |
| microsoft.aad.directory/users/inviteGuest | Convidar usuários convidados no Azure Active Directory. |
| microsoft.aad.directory/users/manager/read | Leia a propriedade users.manager no Active Directory do Azure. |
| microsoft.aad.directory/users/memberOf/read | Leia a propriedade users.memberOf no Active Directory do Azure. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Leia a propriedade users.oAuth2PermissionGrants no Active Directory do Azure. |
| microsoft.aad.directory/users/ownedDevices/read | Leia a propriedade users.ownedDevices no Active Directory do Azure. |
| microsoft.aad.directory/users/ownedObjects/read | Leia a propriedade users.ownedObjects no Active Directory do Azure. |
| microsoft.aad.directory/users/registeredDevices/read | Leia a propriedade users.registeredDevices no Active Directory do Azure. |

### <a name="helpdesk-administrator"></a>Administrador de assistência técnica
Pode redefinir senhas para não administradores e Administradores de Assistência Técnica.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Ler a propriedade devices.bitLockerRecoveryKeys no Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidar todos os tokens de atualização de usuário no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Password/Update | Atualize senhas para todos os usuários no Active Directory do Azure. Consulte a documentação online para obter mais detalhes. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="intune-service-administrator"></a>Administrador de serviços do Intune
Pode gerenciar todos os aspectos do produto Intune.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Atualize as propriedades básicas em contatos no Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/Create | Crie contatos no Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/Delete | Exclua contatos no Azure Active Directory. |
| microsoft.aad.directory/devices/basic/update | Atualize as propriedades básicas em dispositivos no Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Ler a propriedade devices.bitLockerRecoveryKeys no Azure Active Directory. |
| Microsoft.AAD.Directory/Devices/Create | Crie dispositivos no Azure Active Directory. |
| Microsoft.AAD.Directory/Devices/Delete | Exclua dispositivos no Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/update | Atualize a propriedade registeredowners no Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/update | Atualize a propriedade registeredusers no Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.aad.directory/groups/basic/update | Atualize as propriedades básicas nos grupos do Active Directory do Azure. |
| Microsoft.AAD.Directory/Groups/Create | Crie grupos no Active Directory do Azure. |
| microsoft.aad.directory/groups/createAsOwner | Crie grupos no Active Directory do Azure. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| Microsoft.AAD.Directory/Groups/Delete | Exclua grupos no Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Ler a propriedade hiddenmembers no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Members/Update | Atualize a propriedade Groups no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Owners/Update | Atualize a propriedade Owners no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Restore | Restaure grupos no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Settings/Update | Atualize a propriedade Groups no Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Basic/Update | Atualize as propriedades básicas nos usuários no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Manager/Update | Atualize a propriedade Users no Azure Active Directory. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.intune/allEntities/allTasks | Gerencie todos os aspectos do Intune. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="kaizala-administrator"></a>Administrador do Kaizala
Pode gerenciar as configurações do Microsoft Kaizala.  

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >  
  
| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leia o centro de administração do Office 365. |

### <a name="license-administrator"></a>Administrador de Licenças
Pode gerenciar licenças de produto em usuários e grupos.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/users/assignLicense | Gerenciar licenças em usuários no Azure Active Directory. |
| microsoft.aad.directory/users/usageLocation/update | Atualizar a propriedade users.usageLocation no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |

### <a name="lync-service-administrator"></a>Administrador de serviços do Lync
Pode gerenciar todos os aspectos do produto Skype for Business.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| Microsoft.office365.skypeForBusiness/allEntities/allTasks | Gerencie todos os aspectos do Skype for Business Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="message-center-privacy-reader"></a>Leitor de privacidade do centro de mensagens
Pode ler postagens do centro de mensagens, mensagens de privacidade de dados, grupos, domínios e assinaturas.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/messages/read | Ler mensagens em microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Ler securityMessages em microsoft.office365.messageCenter. |

### <a name="message-center-reader"></a>Leitor do Centro de Mensagens
Pode ler as mensagens e as atualizações para sua organização somente no Centro de Mensagens do Office 365. 

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/messages/read | Ler mensagens em microsoft.office365.messageCenter. |

### <a name="partner-tier1-support"></a>Suporte de camada 1 do parceiro
Não use – não se destina para uso geral.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Atualize as propriedades básicas em contatos no Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/Create | Crie contatos no Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/Delete | Exclua contatos no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Create | Crie grupos no Active Directory do Azure. |
| microsoft.aad.directory/groups/createAsOwner | Crie grupos no Active Directory do Azure. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| Microsoft.AAD.Directory/Groups/Members/Update | Atualize a propriedade Groups no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Owners/Update | Atualize a propriedade Owners no Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gerenciar licenças em usuários no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Basic/Update | Atualize as propriedades básicas nos usuários no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Delete | Exclua usuários no Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidar todos os tokens de atualização de usuário no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Manager/Update | Atualize a propriedade Users no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Password/Update | Atualize senhas para todos os usuários no Active Directory do Azure. Consulte a documentação online para obter mais detalhes. |
| Microsoft.AAD.Directory/Users/Restore | Restaurar usuários excluídos no Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Atualize a propriedade users.userPrincipalName no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="partner-tier2-support"></a>Suporte de camada 2 do parceiro
Não use – não se destina para uso geral.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Atualize as propriedades básicas em contatos no Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/Create | Crie contatos no Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/Delete | Exclua contatos no Azure Active Directory. |
| microsoft.aad.directory/domains/allTasks | Criar e excluir domínios e ler e atualizar propriedades padrão no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Create | Crie grupos no Active Directory do Azure. |
| Microsoft.AAD.Directory/Groups/Delete | Exclua grupos no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Members/Update | Atualize a propriedade Groups no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Restore | Restaure grupos no Azure Active Directory. |
| microsoft.aad.directory/organization/basic/update | Atualize as propriedades básicas em organização no Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gerenciar licenças em usuários no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Basic/Update | Atualize as propriedades básicas nos usuários no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Delete | Exclua usuários no Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidar todos os tokens de atualização de usuário no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Manager/Update | Atualize a propriedade Users no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Password/Update | Atualize senhas para todos os usuários no Active Directory do Azure. Consulte a documentação online para obter mais detalhes. |
| Microsoft.AAD.Directory/Users/Restore | Restaurar usuários excluídos no Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Atualize a propriedade users.userPrincipalName no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="power-bi-service-administrator"></a>Administrador de serviços do Power BI
Pode gerenciar todos os aspectos do produto Power BI.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Gerencie todos os aspectos do Power BI. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="privileged-authentication-administrator"></a>Administrador de autenticação privilegiada
Permissão para exibir, definir e redefinir informações do método de autenticação para qualquer usuário (administrador ou não administrador).

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidar todos os tokens de atualização de usuário no Azure Active Directory. |
| microsoft.aad.directory/users/strongAuthentication/update | Atualize propriedades de autenticação forte, como informações de credencial MFA. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |
| Microsoft.AAD.Directory/Users/Password/Update | Atualize as senhas de todos os usuários na organização do Office 365. Consulte a documentação online para obter mais detalhes. |
### <a name="privileged-role-administrator"></a>Administrador de função com privilégios
Pode gerenciar atribuições de função do AD do Azure e todos os aspectos do Privileged Identity Management.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Criar e excluir todos os recursos e ler e atualizar propriedades padrão em microsoft.aad.privilegedIdentityManagement. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/allTasks | Ler e configurar a propriedade servicePrincipalName. appRoleAssignedTo em Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/allTasks | Ler e configurar a propriedade servicePrincipalName. oAuth2PermissionGrants em Azure Active Directory. |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | Criar e gerenciar unidades administrativas (incluindo membros) |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | Criar e gerenciar atribuições de função. |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Criar e gerenciar definições de função. |

### <a name="reports-reader"></a>Leitor de Relatórios
Pode ler relatórios de entrada e de auditoria.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em auditLogs no Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em signInReports no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| Microsoft.office365.usageReports/allEntities/Read | Leia os relatórios de uso do Office 365. |

### <a name="search-administrator"></a>Administrador de pesquisa
Pode criar e gerenciar todos os aspectos das configurações do Microsoft Search.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Ler mensagens em microsoft.office365.messageCenter. |
| microsoft.office365.search/allEntities/allProperties/allTasks | Criar e excluir todos os recursos, e ler e atualizar todas as propriedades no Microsoft. office365. Search. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |
| Microsoft.office365.usageReports/allEntities/Read | Leia os relatórios de uso do Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |

### <a name="search-editor"></a>Editor de pesquisa
Pode criar e gerenciar o conteúdo editorial, como indicadores, p e as, Locations, floorplan.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Ler mensagens em microsoft.office365.messageCenter. |
| microsoft.office365.search/content/allProperties/allTasks | Criar e excluir conteúdo, e ler e atualizar todas as propriedades no Microsoft. office365. Search. |
| Microsoft.office365.usageReports/allEntities/Read | Leia os relatórios de uso do Office 365. |

### <a name="security-administrator"></a>Administrador de segurança
Pode ler relatórios e informações de segurança e gerenciar a configuração no Azure AD e o Office 365.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/applications/policies/update | Atualize a propriedade applications.policies no Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em auditLogs no Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Ler a propriedade devices.bitLockerRecoveryKeys no Azure Active Directory. |
| microsoft.aad.directory/policies/basic/update | Atualize as propriedades básicas em políticas no Azure Active Directory. |
| Microsoft.AAD.Directory/Policies/Create | Crie políticas no Active Directory do Azure. |
| Microsoft.AAD.Directory/Policies/Delete | Exclua policies em Azure Active Directory. |
| Microsoft.AAD.Directory/Policies/Owners/Update | Atualize a propriedade Owners no Azure Active Directory. |
| microsoft.aad.directory/policies/tenantDefault/update | Atualizar a propriedade policies.tenantDefault no Azure Active Directory. |
| microsoftmicrosoft.aad.directory/servicePrincipals/policies/update.aad.directory/servicePrincipals/policies/update | Atualizar servicePrincipals.policies property in Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em signInReports no Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Ler todos os recursos em microsoft.aad.identityProtection. |
| microsoft.aad.identityProtection/allEntities/update | Atualize todos os recursos em microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityMmicrosoft.aad.privilegedIdentityManagement/allEntities/readanagement/allEntities/read | Ler todos os recursos em microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| Microsoft.office365.protectionCenter/allEntities/Read | Ler todos os aspectos do Centro de Proteção do Office 365. |
| Microsoft.office365.protectionCenter/allEntities/Update | Atualize todos os recursos em microsoft.office365.protectionCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |

### <a name="security-operator"></a>Operador de segurança
Cria e gerencia eventos de segurança.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Ler e configurar Microsoft Cloud App Security. |
| microsoft.aad.identityProtection/allEntities/read | Ler todos os recursos em microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityMmicrosoft.aad.privilegedIdentityManagement/allEntities/readanagement/allEntities/read | Ler todos os recursos em microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Leia e configure a proteção avançada contra ameaças do Azure AD. |
| microsoft.intune/allEntities/allTasks | Gerencie todos os aspectos do Intune. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Ler e configurar Centro de Conformidade e Segurança. |
| Microsoft.office365.usageReports/allEntities/Read | Leia os relatórios de uso do Office 365. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Leia e configure a proteção avançada contra ameaças do Windows Defender. |

### <a name="security-reader"></a>Leitor de segurança
Pode ler relatórios e informações de segurança no Azure AD e no Office 365.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em auditLogs no Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Ler a propriedade devices.bitLockerRecoveryKeys no Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Ler todas as propriedades (incluindo as propriedades privilegiadas) em signInReports no Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Ler todos os recursos em microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityMmicrosoft.aad.privilegedIdentityManagement/allEntities/readanagement/allEntities/read | Ler todos os recursos em microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| Microsoft.office365.protectionCenter/allEntities/Read | Ler todos os aspectos do Centro de Proteção do Office 365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |

### <a name="service-support-administrator"></a>Administrador de suporte a serviço
Pode ler informações de integridade do serviço e gerenciar os tíquetes de suporte.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="sharepoint-service-administrator"></a>Administrador de serviços do SharePoint
Pode gerenciar todos os aspectos do serviço SharePoint.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Atualize a propriedade groups.unified no Active Directory do Azure. |
| microsoft.aad.directory/groups/unified/basic/update | Atualizar as propriedades básicas de Grupos do Office 365. |
| microsoft.aad.directory/groups/unified/create | Criar Grupos do Office 365. |
| microsoft.aad.directory/groups/unified/delete | Excluir Grupos do Office 365. |
| microsoft.aad.directory/groups/unified/members/update | Atualizar associação de Grupos do Office 365. |
| microsoft.aad.directory/groups/unified/owners/update | Atualizar a propriedade de Grupos do Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| Microsoft.office365.SharePoint/allEntities/allTasks | Criar e excluir todos os recursos e ler e atualizar propriedades padrão em microsoft.office365.sharepoint. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

### <a name="teams-communications-administrator"></a>Administrador de Comunicações do Teams
Pode gerenciar recursos de reuniões e chamadas no serviço do Microsoft Teams.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |
| Microsoft.office365.usageReports/allEntities/Read | Leia os relatórios de uso do Office 365. |

### <a name="teams-communications-support-engineer"></a>Engenheiro de Suporte de Comunicações do Teams
Pode solucionar problemas de comunicação no Teams usando ferramentas avançadas.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |

### <a name="teams-communications-support-specialist"></a>Especialista em Suporte de Comunicações do Teams
Pode solucionar problemas de comunicação no Teams equipes usando ferramentas básicas.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |

### <a name="teams-service-administrator"></a>Administrador de Serviços do Teams
Pode gerenciar o serviço do Microsoft Teams.

  > [!NOTE]
  > Essa função tem permissões adicionais fora do Azure Active Directory. Para obter mais informações, consulte a descrição da função acima.
  >
  >

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/groups/hiddenMembers/read | Ler a propriedade hiddenmembers no Azure Active Directory. |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Atualize a propriedade groups.unified no Active Directory do Azure. |
| microsoft.aad.directory/groups/unified/basic/update | Atualizar as propriedades básicas de Grupos do Office 365. |
| microsoft.aad.directory/groups/unified/create | Criar Grupos do Office 365. |
| microsoft.aad.directory/groups/unified/delete | Excluir Grupos do Office 365. |
| microsoft.aad.directory/groups/unified/members/update | Atualizar associação de Grupos do Office 365. |
| microsoft.aad.directory/groups/unified/owners/update | Atualizar a propriedade de Grupos do Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |
| Microsoft.office365.usageReports/allEntities/Read | Leia os relatórios de uso do Office 365. |

### <a name="user-administrator"></a>Administrador de usuários
Pode gerenciar todos os aspectos de usuários e grupos, incluindo a redefinição de senhas para administradores limitados.

| **Ações** | **Descrição** |
| --- | --- |
| microsoft.aad.directory/appRoleAssignments/create | Crie appRoleAssignments no Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Exclua appRoleAssignments em Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Atualize o appRoleAssignments no Active Directory do Azure. |
| microsoft.aad.directory/contacts/basic/update | Atualize as propriedades básicas em contatos no Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/Create | Crie contatos no Azure Active Directory. |
| Microsoft.AAD.Directory/Contacts/Delete | Exclua contatos no Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.aad.directory/groups/basic/update | Atualize as propriedades básicas nos grupos do Active Directory do Azure. |
| Microsoft.AAD.Directory/Groups/Create | Crie grupos no Active Directory do Azure. |
| microsoft.aad.directory/groups/createAsOwner | Crie grupos no Active Directory do Azure. O criador é adicionado como o primeiro proprietário e o objeto criado conta com a cota de 250 objetos criados pelo criador. |
| Microsoft.AAD.Directory/Groups/Delete | Exclua grupos no Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Ler a propriedade hiddenmembers no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Members/Update | Atualize a propriedade Groups no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Owners/Update | Atualize a propriedade Owners no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Restore | Restaure grupos no Azure Active Directory. |
| Microsoft.AAD.Directory/Groups/Settings/Update | Atualize a propriedade Groups no Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Atualize a propriedade approleassignments no Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gerenciar licenças em usuários no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Basic/Update | Atualize as propriedades básicas nos usuários no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Create | Crie usuários no Active Directory do Azure. |
| Microsoft.AAD.Directory/Users/Delete | Exclua usuários no Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidar todos os tokens de atualização de usuário no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Manager/Update | Atualize a propriedade Users no Azure Active Directory. |
| Microsoft.AAD.Directory/Users/Password/Update | Atualize senhas para todos os usuários no Active Directory do Azure. Consulte a documentação online para obter mais detalhes. |
| Microsoft.AAD.Directory/Users/Restore | Restaurar usuários excluídos no Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Atualize a propriedade users.userPrincipalName no Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade do Serviço do Azure. |
| microsoftmicrosoft.azure.supportTickets/allEntities/allTasks.azure.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte de Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Ler as propriedades básicas em todos os recursos em microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Ler e configurar a Integridade de Serviço do Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Criar e gerenciar tíquetes de suporte do Office 365. |

## <a name="role-template-ids"></a>IDs de modelo de função

As IDs de modelo de função são usadas principalmente por usuários API do Graph ou PowerShell.

DisplayName de grafo | portal do Azure nome de exibição | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
Administrador de aplicativos | Administrador de aplicativos | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
Desenvolvedor de aplicativos | Desenvolvedor de aplicativos | CF1C38E5-3621-4004-A7CB-879624DCED7C
Administrador de Autenticação | Administrador de autenticação | c4e39bd9-1100-46d3-8c65-fb160da0071f
Administrador da proteção de informações do Azure | Administrador da proteção de informações do Azure | 7495fdc4-34c4-4d15-a289-98788ce399fd
Administrador de fluxo de usuário B2C | Administrador de fluxo de usuário B2C | 6e591065-9bad-43ed-90f3-e9424366d2f0
Administrador de Atributo de Fluxo do Usuário B2C | Administrador de Atributo de Fluxo do Usuário B2C | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
Administrador de Conjunto de Chaves IEF B2C | Administrador de Conjunto de Chaves IEF B2C | aaf43236-0c0d-4d5f-883a-6955382ac081
Administrador de Política IEF B2C | Administrador de Política IEF B2C | 3edaf663-341e-4475-9f94-5c398ef6c070
Administrador de cobrança | Administrador de cobrança | b0f54661-2d74-4c50-afa3-1ec803f12efe
Administrador de Aplicativos de Nuvem | Administrador de aplicativos de nuvem | 158c047a-c907-4556-b7ef-446551a6b5f7
Administrador de Dispositivo de Nuvem | Administrador de dispositivo em nuvem | 7698a772-787b-4ac8-901f-60d6b08affd2
Administradores de Empresa | Administrador global | 62e90394-69f5-4237-9190-012177145e10
Administrador de conformidade | Administrador de conformidade | 17315797-102d-40b4-93e0-432062caca18
Administrador de dados de conformidade | Administrador de dados de conformidade | e6d1a23a-da11-4be4-9570-befc86d067a7
Administrador de acesso condicional | Administrador de acesso condicional | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
Administrador de serviços do CRM | Administrador do Dynamics 365 | 44367163-eba1-44c3-98af-f5787879f96a
Aprovador de acesso do cofre do cliente | Aprovador de acesso do Sistema de Proteção de Dados do Cliente | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
Administrador de Análise de Área de Trabalho | Administrador de Análise de Área de Trabalho | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
Administradores de Dispositivo | Administradores de dispositivo | 9f06204d-73c1-4d4c-880a-6edb90606fd8
Ingresso de Dispositivo | Ingresso no dispositivo | 9c094953-4995-41c8-84c8-3ebb9b32c93f
Gerenciadores de Dispositivo | Gerenciadores de dispositivos | 2b499bcd-da44-4968-8aec-78e1674fa64d
Usuários de Dispositivo | Usuários do dispositivo | d405c6df-0af8-4e3b-95e4-4d06e542189e
Leitores de Diretório | Leitores de diretórios | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
Contas de sincronização de diretório | Contas de sincronização de diretório | d29b2b05-8046-44ba-8758-1e26182fcf32
Gravadores de diretório | Gravadores de diretório | 9360feb5-f418-4baa-8175-e2a00bac4301
Administrador de serviços do Exchange | Administrador do Exchange | 29232cdf-9323-42fd-ade2-1d097af3e4de
Administrador do provedor de identidade externo | Administrador do provedor de identidade externo | be2f45a1-457d-42af-a067-6ec1fa63bc45
Emissor do Convite ao Convidado | Emissor do convite ao convidado | 95e79109-95c0-4d8e-aee3-d01accf2d47b
Administrador de assistência técnica | Administrador de senha | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Administrador de serviços do Intune | Administrador do Intune | 3a2c62db-5318-420d-8d74-23affee5d9d5
Administrador do Kaizala | Administrador do Kaizala | 74ef975b-6605-40af-a5d2-b9539d836353
Administrador de Licenças | Administrador de licenças | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Administrador de serviços do Lync | Administrador do Skype for Business | 75941009-915a-4869-abe7-691bff18279e
Leitor de privacidade do centro de mensagens | Leitor de privacidade do centro de mensagens | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
Leitor do Centro de Mensagens | Leitor do centro de mensagens | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
Suporte de camada 1 do parceiro | Suporte de camada 1 do parceiro | 4ba39ca4-527c-499a-b93d-d9b492c50246
Suporte de camada 2 do parceiro | Suporte de camada 2 do parceiro | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
Administrador de serviços do Power BI | Administrador de Power BI | a9ea8996-122f-4c74-9520-8edcd192826c
Administrador de autenticação privilegiada | Administrador de autenticação privilegiada | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
Administrador de função com privilégios | Administrador de função com privilégios | e8611ab8-c189-46e8-94e1-60213ab1f814
Leitor de Relatórios | Leitor de relatórios | 4a5d8f65-41da-4de4-8968-e035b65339cf
Administrador de pesquisa | Administrador de pesquisa | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
Editor de pesquisa | Editor de pesquisa | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
Administrador de segurança | Administrador de segurança | 194ae4cb-b126-40b2-bd5b-6091b380977d
Operador de segurança | Operador de segurança | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f
Leitor de segurança | Leitor de segurança | 5d6b6bb7-de71-4623-b4af-96380a352509
Administrador de suporte a serviço | Administrador de serviço | f023fd81-a637-4b56-95fd-791ac0226033
Administrador de serviços do SharePoint | Administrador do SharePoint | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Administrador de Comunicações do Teams | Administrador de Comunicações do Teams | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Engenheiro de Suporte de Comunicações do Teams | Engenheiro de Suporte de Comunicações do Teams | f70938a0-fc10-4177-9e90-2178f8765737
Especialista em Suporte de Comunicações do Teams | Especialista em Suporte de Comunicações do Teams | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Administrador de Serviços do Teams | Administrador de Serviços do Teams | 69091246-20e8-4a56-aa4d-066075b2a7a8
User | User | a0b1b346-4d3e-4e8b-98f8-753987be4970
Administrador da conta de usuário | Administrador de usuários | fe930be7-5e62-47db-91af-98c3a49a38b1
Ingresso no Dispositivo no Local de Trabalho | Ingresso no dispositivo do local de trabalho | c34f683f-4d5a-4403-affd-6615e00e3a7f


## <a name="deprecated-roles"></a>Funções preteridas

As seguintes funções não devem ser usadas. Eles foram preteridos e serão removidos do AD do Azure no futuro.

* Administrador de Licenças AdHoc
* Ingresso de Dispositivo
* Gerenciadores de Dispositivo
* Usuários de Dispositivo
* Criador de Usuário Verificado por Email
* Administrador de caixa de correio
* Ingresso no Dispositivo no Local de Trabalho

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como atribuir um usuário como um administrador de uma assinatura do Azure, veja [Gerenciar o acesso usando o portal do Azure e RBAC](../../role-based-access-control/role-assignments-portal.md)
* Para saber mais sobre como o acesso aos recursos é controlado no Microsoft Azure, confira [Noções básicas sobre o acesso a recursos no Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Para saber mais sobre como o Azure Active Directory está relacionado à sua assinatura do Azure, consulte [Como as assinaturas do Azure estão associadas ao Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
