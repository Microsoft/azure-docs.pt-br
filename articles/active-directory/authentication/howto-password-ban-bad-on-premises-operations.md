---
title: Habilitar a proteção de senha do Azure AD local
description: Saiba como habilitar a proteção de senha do Azure AD para um ambiente de Active Directory Domain Services local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2e11fa1bb9f1d3a3986a19da8ed44229829ec81
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96741772"
---
# <a name="enable-on-premises-azure-active-directory-password-protection"></a>Habilitar o local Azure Active Directory proteção por senha

Os usuários geralmente criam senhas que usam palavras locais comuns, como uma escola, uma equipe esportiva ou uma pessoa famosa. Essas senhas são fáceis de serem adivinhadas e fracas contra os ataques baseados em dicionário. Para impor senhas fortes em sua organização, a proteção de senha do Azure Active Directory (Azure AD) fornece uma lista de senhas excluídas global e personalizada. Uma solicitação de alteração de senha falhará se houver uma correspondência nessa lista de senhas banidas.

Para proteger seu ambiente de Active Directory Domain Services (AD DS) local, você pode instalar e configurar a proteção de senha do Azure AD para trabalhar com seu DC local. Este artigo mostra como habilitar a proteção de senha do Azure AD para seu ambiente local.

Para obter mais informações sobre como a proteção de senha do Azure AD funciona em um ambiente local, consulte [como impor a proteção de senha do Azure ad para o Windows Server Active Directory](concept-password-ban-bad-on-premises.md).

## <a name="before-you-begin"></a>Antes de começar

Este artigo mostra como habilitar a proteção de senha do Azure AD para seu ambiente local. Antes de concluir este artigo, [Instale e registre o serviço proxy de proteção de senha do Azure AD e os agentes de DC](howto-password-ban-bad-on-premises-deploy.md) em seu ambiente de AD DS local.

## <a name="enable-on-premises-password-protection"></a>Habilitar a proteção de senha local

1. Entre no [portal do Azure](https://portal.azure.com) e navegue até **Azure Active Directory**  >  métodos de autenticação de **segurança**  >    >  **proteção de senha**.
1. Defina a opção para **habilitar a proteção por senha no Windows Server Active Directory** como *Sim*.

    Quando essa configuração é definida como *não*, todos os agentes de DC de proteção de senha do Azure ad implantados entram em um modo inativo onde todas as senhas são aceitas no estado em que se encontram. Nenhuma atividade de validação é executada e eventos de auditoria não são gerados.

1. É recomendável definir inicialmente o **modo** como *auditoria*. Depois que estiver familiarizado com o recurso e o impacto sobre os usuários em sua organização, você poderá alternar o **modo** para *imposto*. Para obter mais informações, consulte a seção a seguir sobre [modos de operação](#modes-of-operation).
1. Quando estiver pronto, selecione **Salvar**.

    [![Habilitar a proteção de senha local em métodos de autenticação no portal do Azure](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords-cropped.png)](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords.png#lightbox)

## <a name="modes-of-operation"></a>Modos de operação

Ao habilitar a proteção de senha do Azure AD local, você pode usar o modo de *auditoria* ou o modo *impor* . Recomendamos que a implantação inicial e o teste sempre sejam iniciados no modo de auditoria. As entradas no log de eventos devem ser monitoradas para antecipar se os processos operacionais existentes seriam incomodados depois que o modo *imposição* for habilitado.

### <a name="audit-mode"></a>Modo de auditoria

O modo de *auditoria* é projetado como uma maneira de executar o software em um modo "e se". Cada serviço de agente de DC de proteção de senha do Azure AD avalia uma senha de entrada de acordo com a política ativa no momento.

Se a política atual estiver configurada para estar no modo de auditoria, as senhas "incorretas" resultarão em mensagens de log de eventos, mas serão processadas e atualizadas. Esse comportamento é a única diferença entre o modo de auditoria e de aplicação. Todas as outras operações são executadas da mesma.

### <a name="enforced-mode"></a>Modo imposto

O modo *imposto* destina-se à configuração final. Assim como no modo de auditoria, cada serviço de agente de DC de proteção de senha do Azure AD avalia as senhas de entrada de acordo com a política ativa no momento. No entanto, quando o modo imposto está habilitado, uma senha considerada insegura de acordo com a política é rejeitada.

Quando uma senha é rejeitada no modo imposto pelo agente de DC da proteção de senha do Azure AD, um usuário final vê um erro semelhante, como verá se a senha foi rejeitada pela imposição de complexidade de senha local tradicional. Por exemplo, um usuário pode ver a seguinte mensagem de erro tradicional na tela de logon ou alterar senha do Windows:

*"Não é possível atualizar a senha. O valor fornecido para a nova senha não atende aos requisitos de comprimento, complexidade ou histórico do domínio. "*

Essa mensagem é apenas um exemplo dos vários resultados possíveis. A mensagem de erro específica pode variar dependendo do software real ou do cenário que está tentando definir uma senha insegura.

Os usuários finais afetados podem precisar trabalhar com a equipe de ti para entender os novos requisitos e escolher senhas seguras.

> [!NOTE]
> A proteção por senha do Azure AD não tem controle sobre a mensagem de erro específica exibida pelo computador cliente quando uma senha fraca é rejeitada.

## <a name="next-steps"></a>Próximas etapas

Para personalizar a lista de senhas banidas para sua organização, consulte [Configurar a lista de senhas banidas personalizadas da proteção de senha do Azure ad](tutorial-configure-custom-password-protection.md).

Para monitorar eventos locais, consulte [monitorando a proteção de senha do Azure ad local](howto-password-ban-bad-on-premises-monitor.md).
