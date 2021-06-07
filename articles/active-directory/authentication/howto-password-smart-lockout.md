---
title: Impedir ataques usando o bloqueio inteligente-Azure Active Directory
description: Saiba como Azure Active Directory o bloqueio inteligente ajuda a proteger sua organização contra ataques de força bruta que tentam adivinhar senhas de usuário.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5f501c19da3c2ddc06ad89fe5649789477af7ec
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99255366"
---
# <a name="protect-user-accounts-from-attacks-with-azure-active-directory-smart-lockout"></a>Proteger contas de usuário contra ataques com bloqueio inteligente de Azure Active Directory

O bloqueio inteligente ajuda a bloquear atores mal-intencionados que tentam adivinhar as senhas dos usuários ou usar métodos de força bruta para entrada. O bloqueio inteligente pode reconhecer entradas provenientes de usuários válidos e tratá-las de forma diferente do que as entradas dos invasores e de outras fontes desconhecidas. Os invasores são bloqueados, enquanto os usuários podem acessar as contas e trabalhar.

## <a name="how-smart-lockout-works"></a>Como o bloqueio inteligente funciona

Por padrão, o bloqueio inteligente bloqueia a conta de tentativas de login por um minuto após 10 tentativas malsucedidas. A conta é bloqueada novamente após cada tentativa de entrada com falha subsequente durante um minuto na primeira vez, e tempos mais longos em tentativas subsequentes. Para minimizar as formas como um invasor pode contornar esse comportamento, não divulgamos a taxa de crescimento do período de bloqueio em tentativas de entrada malsucedidas adicionais.

O bloqueio inteligente rastreia os últimos três hashes de senha incorreta para evitar o incremento do contador de bloqueio para a mesma senha. Se alguém inserir a mesma senha errada várias vezes, esse comportamento não fará com que a conta seja bloqueada.

> [!NOTE]
> A funcionalidade de controle de hash não está disponível para clientes com autenticação de passagem habilitada, pois a autenticação ocorre localmente, não na nuvem.

As implantações federadas que usam o AD FS 2016 e o AF FS 2019 podem permitir benefícios semelhantes usando [AD FS bloqueio de extranet e bloqueio inteligente de extranet](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection).

O bloqueio inteligente está sempre ativado para todos os clientes do Azure AD com as configurações padrão que oferecem a combinação certa de segurança e usabilidade. Personalização das configurações de bloqueio inteligente, com valores específicos de sua organização, requer o Azure AD Premium P1 ou superior licenças para seus usuários.

O uso do bloqueio inteligente não garantirá que um usuário genuíno nunca seja bloqueado. Quando o bloqueio inteligente bloqueia uma conta de usuário, fazemos nosso melhor para não bloquear o usuário original. O serviço de bloqueio tenta garantir que atores mal-intencionados não obtenham acesso a uma conta de usuário original. As seguintes considerações se aplicam:

* Cada data center do Azure AD controla o bloqueio de forma independente. Um usuário terá (*threshold_limit * datacenter_count*) tentativas, se ele acessar cada datacenter.
* O Smart Lockout usa localização familiar versus local desconhecido para diferenciar entre um ator ruim e o usuário genuíno. Localizações conhecidas e não conhecidas têm contadores de bloqueio separados.

O bloqueio inteligente pode ser integrado a implantações híbridas que usam a sincronização de hash de senha ou a autenticação de passagem para proteger contas do AD DS (Active Directory Domain Services) local contra o bloqueio por invasores. Ao definir políticas de bloqueio inteligente no Azure AD adequadamente, os ataques podem ser filtrados antes que elas atinjam o AD DS local.

Ao usar a [autenticação de passagem](../hybrid/how-to-connect-pta.md), as seguintes considerações se aplicam:

* O limite de bloqueio do Azure AD deve ser **menor** que o limite de bloqueio da conta do AD DS. Defina os valores de modo que o limite de bloqueio da conta do AD DS seja pelo menos duas ou três vezes maior do que o limite de bloqueio do Azure AD.
* A duração do bloqueio do Azure AD deve ser definida como maior do que a do contador de bloqueio da conta de redefinição do AD DS após a duração. A duração do Azure AD é definida em segundos, e a duração do AD é definida em minutos.

Por exemplo, se você quiser que seu contador do Azure AD seja maior que AD DS, o Azure AD deve ser de 120 segundos (2 minutos), e o AD local deve ser definido como 1 minuto (60 segundos).

> [!IMPORTANT]
> Atualmente, um administrador não poderá desbloquear as contas de nuvem dos usuários se elas tiverem sido bloqueadas pelo recurso de bloqueio inteligente. O administrador deve aguardar a duração do bloqueio expirar. No entanto, o usuário pode desbloquear usando a SSPR (redefinição de senha de autoatendimento) de um dispositivo ou local confiável.

## <a name="verify-on-premises-account-lockout-policy"></a>Verificar políticas de bloqueio de conta no local

Para verificar sua política de bloqueio de conta AD DS local, conclua as seguintes etapas de um sistema ingressado em domínio com privilégios de administrador:

1. Abra a ferramenta de Gerenciamento de Política de Grupo.
2. Edite a política de grupo que inclui a política de bloqueio de conta da sua organização, como a **política de domínio padrão**.
3. Navegue até **configuração do computador**  >  **políticas**  >  **configurações do Windows** configuração de  >  **segurança**  >  **políticas de conta**  >  **política de bloqueio de conta**.
4. Verifique seu **limite de bloqueio de conta** e **redefina o contador de bloqueios de conta após** os valores.

![Modificar a política de bloqueio de conta Active Directory local](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Gerenciar valores de bloqueio inteligente do Azure AD

Com base em seus requisitos organizacionais, você pode personalizar os valores do bloqueio inteligente do Azure AD. Personalização das configurações de bloqueio inteligente, com valores específicos de sua organização, requer o Azure AD Premium P1 ou superior licenças para seus usuários.

Para verificar ou modificar os valores de bloqueio inteligente para sua organização, conclua as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Procure e selecione *Azure Active Directory* e, em seguida, selecione **segurança**  >  **métodos de autenticação**  >  **proteção por senha**.
1. Coloque o **limite de bloqueio**, baseado em quantas vezes de logins falhados é permitido em uma conta antes do primeiro bloqueio.

    O padrão é 10.

1. Coloque a **duração do bloqueio em segundos**, para a duração em segundos de cada bloqueio.

    O padrão é 60 segundos (um minuto).

> [!NOTE]
> Se o primeiro login depois do bloqueio também falhar, a conta será bloqueada de novo. Se uma conta bloquear repetidamente, o bloqueio aumentará a duração.

![Personalize a política de bloqueio inteligente do Azure AD no portal do Azure](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>Como determinar se o recurso de bloqueio inteligente está funcionando ou não

Quando o limite de bloqueio inteligente for disparado, você receberá a seguinte mensagem enquanto a conta estiver bloqueada:

*Sua conta está temporariamente bloqueada para impedir o uso não autorizado. Tente novamente mais tarde e, se você ainda tiver problemas, entre em contato com seu administrador.*

Quando você testa o bloqueio inteligente, suas solicitações de entrada podem ser tratadas por data centers diferentes devido à natureza distribuída geograficamente e com balanceamento de carga do serviço de autenticação do Azure AD. Nesse cenário, como cada Datacenter do Azure AD acompanha o bloqueio de forma independente, pode levar mais do que o número limite definido de tentativas de bloqueio para causar um bloqueio. Um usuário tem (*threshold_limit * datacenter_count*) número de tentativas inadequadas se o usuário atinge cada datacenter antes de ocorrer um bloqueio.

## <a name="next-steps"></a>Próximas etapas

Para personalizar ainda mais a experiência, você pode [Configurar senhas banidas personalizadas para a proteção de senha do Azure ad](tutorial-configure-custom-password-protection.md).

Para ajudar os usuários a redefinir ou alterar sua senha em um navegador da Web, você pode [Configurar a redefinição de senha de autoatendimento do Azure ad](tutorial-enable-sspr.md).
