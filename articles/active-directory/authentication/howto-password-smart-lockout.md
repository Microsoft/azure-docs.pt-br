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
ms.openlocfilehash: b8a57e77ea572f899bf540714e8ac9968988f028
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741721"
---
# <a name="protect-user-accounts-from-attacks-with-azure-active-directory-smart-lockout"></a>Proteger contas de usuário contra ataques com bloqueio inteligente de Azure Active Directory

O bloqueio inteligente ajuda a bloquear atores ruins que tentam adivinhar as senhas de seus usuários ou usar métodos de força bruta para entrar. O bloqueio inteligente pode reconhecer entradas provenientes de usuários válidos e tratá-los de forma diferente daqueles de invasores e outras fontes desconhecidas. Os invasores são bloqueados, enquanto os usuários continuam acessando suas contas e são produtivos.

## <a name="how-smart-lockout-works"></a>Como o bloqueio inteligente funciona

Por padrão, o bloqueio inteligente bloqueia a conta de tentativas de login por um minuto após 10 tentativas malsucedidas. A conta é bloqueada novamente após cada tentativa de entrada com falha subsequente durante um minuto na primeira vez, e tempos mais longos em tentativas subsequentes. Para minimizar as maneiras como um invasor pode contornar esse comportamento, não divulgamos a taxa na qual o período de bloqueio cresce em tentativas de entrada malsucedidas adicionais.

O bloqueio inteligente rastreia os últimos três hashes de senha incorreta para evitar o incremento do contador de bloqueio para a mesma senha. Se alguém inserir a mesma senha inadequada várias vezes, esse comportamento não fará com que a conta seja bloqueada.

> [!NOTE]
> A funcionalidade de controle de hash não está disponível para clientes com autenticação de passagem habilitada, pois a autenticação ocorre localmente, não na nuvem.

As implantações federadas que usam o AD FS 2016 e o AF FS 2019 podem permitir benefícios semelhantes usando [AD FS bloqueio de extranet e bloqueio inteligente de extranet](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection).

O bloqueio inteligente está sempre ativado, para todos os clientes do Azure AD, com essas configurações padrão que oferecem a combinação certa de segurança e usabilidade. A personalização das configurações de bloqueio inteligente, com valores específicos à sua organização, requer Azure AD Premium as licenças P1 ou superior para os usuários.

Usar o bloqueio inteligente não garante que um usuário original nunca seja bloqueado. Quando o bloqueio inteligente bloqueia uma conta de usuário, nós experimentamos o melhor para não bloquear o usuário original. O serviço de bloqueio tenta garantir que os atores ruins não possam obter acesso a uma conta de usuário original. As seguintes considerações se aplicam:

* Cada data center do Azure AD controla o bloqueio de forma independente. Um usuário tem o número de tentativas (*threshold_limit * datacenter_count*), se o usuário atingir cada data center.
* O Smart Lockout usa localização familiar versus local desconhecido para diferenciar entre um ator ruim e o usuário genuíno. Locais desconhecidos e familiares têm contadores de bloqueio separados.

O bloqueio inteligente pode ser integrado a implantações híbridas que usam sincronização de hash de senha ou autenticação de passagem para proteger contas de Active Directory Domain Services (AD DS) locais de serem bloqueadas por invasores. Ao definir as políticas de bloqueio inteligente no Azure AD adequadamente, os ataques podem ser filtrados antes de alcançarem AD DS locais.

Ao usar a [autenticação de passagem](../hybrid/how-to-connect-pta.md), as seguintes considerações se aplicam:

* O limite de bloqueio do Azure AD é **menor** que o limite de bloqueio de conta de AD DS. Defina os valores para que o limite de bloqueio da conta de AD DS seja pelo menos duas ou três vezes mais tempo do que o limite de bloqueio do Azure AD.
* A duração do bloqueio do Azure AD deve ser definida por mais tempo do que o AD DS reiniciar o contador de bloqueios de conta após a duração. A duração do Azure AD é definida em segundos, enquanto a duração do anúncio é definida em minutos.

Por exemplo, se você quiser que seu contador do Azure AD seja maior que AD DS, o Azure AD seria de 120 segundos (2 minutos) enquanto seu AD local é definido como 1 minuto (60 segundos).

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

Com base em seus requisitos organizacionais, você pode personalizar os valores de bloqueio inteligente do Azure AD. A personalização das configurações de bloqueio inteligente, com valores específicos à sua organização, requer Azure AD Premium as licenças P1 ou superior para os usuários.

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

## <a name="next-steps"></a>Próximas etapas

Para personalizar ainda mais a experiência, você pode [Configurar senhas banidas personalizadas para a proteção de senha do Azure ad](tutorial-configure-custom-password-protection.md).

Para ajudar os usuários a redefinir ou alterar sua senha em um navegador da Web, você pode [Configurar a redefinição de senha de autoatendimento do Azure ad](tutorial-enable-sspr.md).
