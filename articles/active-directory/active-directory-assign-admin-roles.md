---
title: "Atribuindo funções de administrador no Azure Active Directory | Microsoft Docs"
description: "Uma função de administrador pode ser usada para criar ou editar usuários, atribuir funções administrativas, redefinir senhas de usuário, gerenciar licenças de usuário ou gerenciar domínios. Um usuário que tem uma função de administrador atribuída tem as mesmas permissões em todos os serviços de nuvem ao quais sua organização tenha assinado."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 7fc27e8e-b55f-4194-9b8f-2e95705fb731
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: curtand
ms.reviewer: Vince.Smith
ms.custom: oldportal;it-pro;
robots: NOINDEX
ms.openlocfilehash: 8e50ea660df0a3ca1fbd23f7b610a7e57eb737d1
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>Atribuindo funções de administrador no Azure Active Directory
> [!div class="op_single_selector"]
> * [Portal do Azure](active-directory-assign-admin-roles-azure-portal.md)
> * [Portal clássico do Azure](active-directory-assign-admin-roles.md)
>
>

Use o Azure AD (Azure Active Directory) para designar administradores distintos para diferentes funções. Esses administradores podem acessar recursos selecionados no Portal do Azure ou no Portal Clássico do Azure e, dependendo da sua função, poderão criar ou editar usuários, atribuir funções administrativas a outros usuários, redefinir senhas de usuários, gerenciar licenças de usuários e gerenciar domínios, entre outras coisas. Um usuário ao qual seja atribuída uma função administrativa terá as mesmas permissões em todos os serviços de nuvem que sua organização tenha assinado, independentemente de você atribuir a função no portal do Office 365, no Portal Clássico do Azure ou usando o módulo do Azure AD para Microsoft PowerShell.

> [!IMPORTANT]
> A Microsoft recomenda que você gerencie o Azure AD usando o [Centro de administração do AD do Azure](https://aad.portal.azure.com) no portal do Azure em vez de usar o portal clássico do Azure mencionado neste artigo. Para saber com atribuir funções de administrador no centro de administração do Azure AD, consulte [Atribuindo funções de administrador no Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md).


As seguintes funções de administrador estão disponíveis:

* **Administrador de Cobrança**: faz compras, gerencia as assinaturas, gerencia tíquetes de suporte e monitora a integridade do serviço.

* **Administrador de Conformidade**: os usuários com essa função têm permissões de gerenciamento no Centro de Segurança e Conformidade do Office 365 e no Centro de Administração do Exchange. Mais informações em “[Sobre funções de administrador do Office 365](https://support.office.com/en-us/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)”.

* **Administrador de acesso condicional**: os usuários com essa função têm a capacidade de gerenciar configurações de acesso condicional do Azure Active Directory.

* **Administrador de Serviços do CRM**: os usuários com essa função têm permissões globais no Microsoft CRM Online quando o serviço está presente, além da capacidade de gerenciar tíquetes de suporte e monitorar a integridade do serviço. Mais informações em [Sobre funções de administrador do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Administradores do Dispositivo**: os usuários com essa função se tornam administradores de computador local em todos os dispositivos Windows 10 associados ao Azure Active Directory. Eles não têm a capacidade de gerenciar objetos de dispositivos no Azure Active Directory.

* **Leitores de Diretório**: esta é uma função herdada que deve ser atribuída aos aplicativos que não dão suporte a [Estrutura de Consentimento](active-directory-integrating-applications.md). Ele não deve ser atribuído a nenhum usuário.

* **Contas de Sincronização de Diretório**: não use. Essa função é automaticamente atribuída ao serviço do Azure AD Connect e não tem intenção ou suporte para outros usos.

* **Gravadores de Diretório**: esta é uma função herdada que deve ser atribuída aos aplicativos que não dão suporte a [Estrutura de Consentimento](active-directory-integrating-applications.md). Ele não deve ser atribuído a nenhum usuário.

* **Administrador de serviços do Exchange**: os usuários com essa função têm permissões globais no Microsoft Exchange Online quando o serviço está presente. Mais informações em [Sobre funções de administrador do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Administrador Global/Administrador da Empresa**: os usuários com essa função têm acesso a todos os recursos administrativos do Azure Active Directory, bem como serviços que realizam a federação do Azure Active Directory assim como o Exchange Online, o SharePoint Online e o Skype for Business Online. A pessoa que se inscreve no locatário do Azure Active Directory torna-se um administrador global. Somente os administradores globais podem atribuir outras funções de administrador. Pode haver mais de um administrador global na sua empresa. Administradores globais podem redefinir a senha para qualquer usuário e todos os outros administradores.

  > [!NOTE]
  > Na API do Graph da Microsoft, na API do Graph do Azure AD e no Azure AD PowerShell, essa função é identificada como "Administrador da Empresa". É "Administrador Global" no [portal do Azure](https://portal.azure.com).
  >
  >

* **Emissor do Convite ao Convidado**: os usuários nessa função podem gerenciar convites para usuários convidados do Azure Active Directory B2B quando a configuração de usuário "Membros podem convidar" está definida como Não. Mais informações sobre a colaboração B2B podem ser obtidas em [Sobre a Visualização de Colaboração do Azure AD B2B](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Ela não inclui nenhuma outra permissão.

* **Administrador de Serviços do Intune**: os usuários com essa função têm permissões globais no Microsoft Intune Online quando o serviço está presente. Além disso, essa função contém a capacidade de gerenciar usuários e dispositivos para associar a política, bem como criar e gerenciar grupos.

* **Administrador de Caixa de Correio**: essa função é usada somente como parte do suporte por email do Exchange Online para dispositivos RIM Blackberry. Se sua organização não usar o email do Exchange Online em dispositivos RIM Blackberry, não use essa função.

* **Suporte ao parceiro de Nível 1**: não use. Essa função foi substituída e será removida do Azure AD no futuro. Essa função é destinada a um pequeno número de parceiros de revenda da Microsoft e não se destina ao uso geral.

* **Suporte ao parceiro de Nível 2**: não use. Essa função foi substituída e será removida do Azure AD no futuro. Essa função é destinada a um pequeno número de parceiros de revenda da Microsoft e não se destina ao uso geral.

* **Administrador de Senha/Administrador de Assistência Técnica**: usuários com essa função podem redefinir senhas, gerenciar solicitações de serviço e monitorar a integridade do serviço. Administradores de senha podem redefinir senhas somente para os usuários e outros administradores de senha.

  > [!NOTE]
  > Na API do Graph da Microsoft, na API do Graph do Azure AD e no Azure AD PowerShell, essa função é identificada como "Administrador da Assistência Técnica". Ele é um "Administrador de Senha" no [Portal do Azure](https://portal.azure.com/).
  >
  >
  
* **Administrador de Serviços do Power BI**: os usuários com essa função têm permissões globais no Microsoft Power BI quando o serviço está presente, além da capacidade de gerenciar tíquetes de suporte e monitorar a integridade do serviço. Mais informações em [Sobre funções de administrador do Office 365](https://support.office.com/en-us/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d?ui=en-US&rs=en-001&ad=US).

* **Administrador da função com privilégios**: os usuários com essa função podem gerenciar atribuições de função no Azure Active Directory, bem como dentro do Azure AD Privileged Identity Management. Além disso, essa função permite o gerenciamento de todos os aspectos do Privileged Identity Management.

* **Administrador de Segurança**: os usuários com essa função têm todas as permissões somente leitura da função de Leitor de segurança, mais a capacidade de gerenciamento de configuração de serviços relacionados à segurança: Azure Active Directory Identity Protection, Proteção de Informações do Azure, Privileged Identity Management e Centro de Segurança e Conformidade do Office 365. Mais informações sobre permissões do Office 365 estão disponíveis em [Permissões no Centro de Conformidade de Segurança do Office 365](https://support.office.com/en-us/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

* **Leitor de Segurança**: os usuários com essa função têm acesso somente leitura global, incluindo todas as informações no Azure Active Directory, Identity Protection e Privileged Identity Management, bem como a capacidade de ler relatórios de entrada do Azure Active Directory e logs de auditoria. A função também concede permissão somente leitura no Centro de Conformidade e Segurança do Office 365. Mais informações sobre permissões do Office 365 estão disponíveis em [Permissões no Centro de Conformidade de Segurança do Office 365](https://support.office.com/en-us/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

* **Administrador de Suporte do Serviço**: os usuários com essa função podem abrir solicitações de suporte com a Microsoft para serviços do Azure e do Office 365, além de exibições de painel de serviço e do centro de mensagens no Portal do Azure e no portal de administração do Office 365. Mais informações em [Sobre funções de administrador do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Administrador de Serviços do SharePoint**: os usuários com essa função têm permissões globais no Microsoft SharePoint Online quando o serviço está presente, além da capacidade de gerenciar tíquetes de suporte e monitorar a integridade do serviço. Mais informações em [Sobre funções de administrador do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Administrador de Serviços do Lync/Skype for Business**: os usuários com essa função têm permissões globais no Microsoft Skype for Business quando o serviço está presente, além de gerenciar atributos de usuário específicos do Skype no Azure Active Directory. Além disso, essa função concede a capacidade de gerenciar tíquetes de suporte e monitorar a integridade do serviço. Mais informações em [Sobre funções de administrador do Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

  > [!NOTE]
  > Na API do Graph da Microsoft, na API do Graph do Azure AD e no Azure AD PowerShell, essa função é identificada como "Administrador de Serviços do Lync". Ela equivale a "Administrador de Serviços do Skype for Business" no [Portal do Azure](https://portal.azure.com/).
  >
  >

* **Administrador da Conta de Usuário**: os usuários com essa função podem criar e gerenciar todos os aspectos de usuários e grupos. Além disso, essa função inclui a capacidade de gerenciar tíquetes de suporte e monitora a integridade do serviço. Algumas restrições se aplicam. Por exemplo, essa função não permite a exclusão de um administrador global e, embora permita a alteração de senhas para não administradores, ela não permite a alteração de senhas para os administradores globais ou outros administradores com privilégios.

## <a name="administrator-permissions"></a>Permissões de administrador

### <a name="billing-administrator"></a>Administrador de cobrança

| O que ele pode fazer | O que não pode fazer |
| --- | --- |
|<p>Exibir informações da empresa e do usuário</p><p>Gerenciar tíquetes de suporte do Office</p><p>Executar operações de cobrança e compra de produtos do Office</p> |<p>Redefinir senhas de usuário</p><p>Criar e gerenciar modos de exibição do usuário</p><p>Criar, editar e excluir usuários e grupos e gerenciar licenças de usuário</p><p>Gerenciar domínios</p><p>Editar informações da empresa</p><p>Delegar funções administrativas a outros</p><p>Usar sincronização de diretório</p><p>Exibir logs de auditoria</p>|

### <a name="conditional-access-administrator"></a>Administrador de acesso condicional
| O que ele pode fazer | O que não pode fazer |
| --- | --- |
|<p>Exibir informações da empresa e do usuário</p><p>Gerenciar configurações de acesso condicional</p> |<p>Redefinir senhas de usuário</p><p>Criar e gerenciar modos de exibição do usuário</p><p>Criar, editar e excluir usuários e grupos e gerenciar licenças de usuário</p><p>Gerenciar domínios</p><p>Editar informações da empresa</p><p>Delegar funções administrativas a outros</p><p>Usar sincronização de diretório</p><p>Exibir logs de auditoria</p>|

### <a name="global-administrator"></a>Administrador global
| O que ele pode fazer | O que não pode fazer |
| --- | --- |
|<p>Exibir informações da empresa e do usuário</p><p>Gerenciar tíquetes de suporte do Office</p><p>Executar operações de cobrança e compra de produtos do Office</p><p>Redefinir senhas de usuário</p><p>Redefinir senhas de outro administrador</p><p>Criar e gerenciar modos de exibição do usuário</p><p>Criar, editar e excluir usuários e grupos e gerenciar licenças de usuário</p><p>Gerenciar domínios</p><p>Editar informações da empresa</p><p>Delegar funções administrativas a outros</p><p>Usar sincronização de diretório</p><p>Habilitar ou desabilitar autenticação multifator</p><p>Exibir logs de auditoria</p> |<p>N/D</p>|

### <a name="password-administrator"></a>Administrador de senha
| O que ele pode fazer | O que não pode fazer |
| --- | --- |
| <p>Exibir informações da empresa e do usuário</p><p>Gerenciar tíquetes de suporte do Office</p><p>Redefinir senhas de usuário</p> <p>Redefinir senhas de outro administrador</p>|<p>Executar operações de cobrança e compra de produtos do Office</p><p>Criar e gerenciar modos de exibição do usuário</p><p>Criar, editar e excluir usuários e grupos e gerenciar licenças de usuário</p><p>Gerenciar domínios</p><p>Editar informações da empresa</p><p>Delegar funções administrativas a outros</p><p>Usar sincronização de diretório</p><p>Exibir relatórios</p>|

### <a name="service-administrator"></a>Administrador de serviço
| O que ele pode fazer | O que não pode fazer |
| --- | --- |
| <p>Exibir informações da empresa e do usuário</p><p>Gerenciar tíquetes de suporte do Office</p> |<p>Redefinir senhas de usuário</p><p>Executar operações de cobrança e compra de produtos do Office</p><p>Criar e gerenciar modos de exibição do usuário</p><p>Criar, editar e excluir usuários e grupos e gerenciar licenças de usuário</p><p>Gerenciar domínios</p><p>Editar informações da empresa</p><p>Delegar funções administrativas a outros</p><p>Usar sincronização de diretório</p><p>Exibir logs de auditoria</p> |

### <a name="user-administrator"></a>Administrador de usuários
| O que ele pode fazer | O que não pode fazer |
| --- | --- |
| <p>Exibir informações da empresa e do usuário</p><p>Gerenciar tíquetes de suporte do Office</p><p>Redefinir senhas de usuário, com limitações.</p><p>Redefinir senhas de outro administrador</p><p>Redefinir senhas de outros usuários</p><p>Criar e gerenciar modos de exibição do usuário</p><p>Criar, editar e excluir usuários e grupos e gerenciar licenças de usuário, com limitações. Eles não podem excluir um administrador global ou criar outros administradores.</p> |<p>Executar operações de cobrança e compra de produtos do Office</p><p>Gerenciar domínios</p><p>Editar informações da empresa</p><p>Delegar funções administrativas a outros</p><p>Usar sincronização de diretório</p><p>Habilitar ou desabilitar autenticação multifator</p><p>Exibir logs de auditoria</p> |

### <a name="security-reader"></a>Leitor de segurança
| Nesse | O que ele pode fazer |
| --- | --- |
| Identity Protection Center |Ler todos os relatórios de segurança e informações de configurações para recursos de segurança<ul><li>Anti-spam<li>Criptografia<li>Prevenção de perda de dados<li>Antimalware<li>Proteção avançada contra ameaças<li>Antiphishing<li>Regras de fluxo de mensagens |
| Privileged Identity Management |<p>Tem acesso somente leitura a todas as informações exibidas no Azure AD PIM: políticas e relatórios de atribuições de função do Azure AD, análises de segurança e, no futuro, acesso de leitura aos dados de política e relatórios para cenários além da atribuição de função do Azure AD.<p>**Não pode** se inscrever no Azure AD PIM nem fazer alterações nele. No portal do PIM ou por meio do PowerShell, alguém nesta função poderá ativar funções adicionais (por exemplo, administrador global ou administrador com função com privilégios) se o usuário for um candidato a elas. |
| <p>Monitorar a integridade do serviço Office 365</p><p>Centro de Conformidade e Segurança do Office 365</p> |<ul><li>Ler e gerenciar alertas<li>Ler políticas de segurança<li>Ler informações sobre inteligência contra ameaças, Cloud App Discovery e quarentena em Pesquisar e Investigar<li>Ler todos os relatórios |

### <a name="security-administrator"></a>Administrador de segurança
| Nesse | O que ele pode fazer |
| --- | --- |
| Identity Protection Center |<ul><li>Todas as permissões da função Leitor de Segurança.<li>Além disso, a capacidade de executar todas as operações de IPC, exceto para a redefinição de senhas. |
| Privileged Identity Management |<ul><li>Todas as permissões da função Leitor de Segurança.<li>**Não é possível** gerenciar associações de função ou configurações do Azure AD. |
| <p>Monitorar a integridade do serviço Office 365</p><p>Centro de Conformidade e Segurança do Office 365 |<ul><li>Todas as permissões da função Leitor de Segurança.<li>Pode configurar todas as configurações no recurso de Proteção avançada contra ameaças (proteção contra malware e vírus, configuração de URL mal-intencionado, rastreamento de URL, etc). |

## <a name="details-about-the-global-administrator-role"></a>Detalhes sobre a função de administrador global
O administrador global tem acesso a todos os recursos administrativos. Por padrão, a pessoa que se inscreve para uma assinatura do Azure recebe a função de administrador global para o diretório. Somente os administradores globais podem atribuir outras funções de administrador.

### <a name="to-add-a-colleague-as-a-global-administrator"></a>Para adicionar um colega como um administrador global

1. Entre no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) com uma conta que seja um administrador global do diretório de locatário.

   ![Como abrir o Centro de administração do Azure AD](./media/active-directory-assign-admin-roles/active-directory-admin-center.png)

2. Selecione **Usuários e grupos &gt; Todos os usuários**

3. Encontre o usuário que você deseja designar como administrador global e abra a folha para esse usuário.

4. Na folha do usuário, selecione a **função Diretório**.
 
5. Na folha da função de diretório, selecione a função **Administrador global** e salve.

## <a name="assign-or-remove-administrator-roles"></a>Atribuir ou remover funções de administrador
Para saber como atribuir funções administrativas a um usuário no Azure Active Directory, veja [Atribuir um usuário a funções de administrador no Azure Active Directory](active-directory-users-assign-role-azure-portal.md).

## <a name="deprecated-roles"></a>Funções preteridas

As seguintes funções não devem ser usadas. Elas foram preteridas e serão removidas do Azure AD no futuro.

* Administrador de Licenças AdHoc
* Criador de Usuário Verificado por Email
* Ingresso de Dispositivo
* Gerenciadores de Dispositivo
* Usuários de Dispositivo
* Ingresso no Dispositivo no Local de Trabalho

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre como alterar administradores para uma assinatura do Azure, veja [Como adicionar ou alterar as funções de administrador do Azure](../billing-add-change-azure-subscription-administrator.md)
* Para saber mais sobre como o acesso aos recursos é controlado no Microsoft Azure, confira [Noções básicas sobre o acesso aos recursos do Azure](active-directory-understanding-resource-access.md)
* Para saber mais sobre como o Azure Active Directory está relacionado à sua assinatura do Azure, confira [Como as assinaturas do Azure estão associadas ao Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Gerenciar usuários](active-directory-create-users.md)
* [Gerenciar senhas](active-directory-manage-passwords.md)
* [Gerenciar grupos](active-directory-manage-groups.md)
