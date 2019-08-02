---
title: Impedindo ataques de força bruta usando o bloqueio inteligente do Azure AD-Azure Active Directory
description: Bloqueio inteligente de Active Directory do Azure ajuda a proteger sua organização contra ataques de força bruta tentando descobrir senhas
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd03e2b98b1fd1a2a45b5feecc963bcfc7bfe83c
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499888"
---
# <a name="azure-active-directory-smart-lockout"></a>Azure Active Directory Sync smart lockout

O bloqueio inteligente ajuda a bloquear atores mal-intencionados que estão tentando adivinhar as senhas dos usuários ou usar métodos de força bruta para entrada. Ele pode reconhecer entradas provenientes de usuários válidos e tratá-las de forma diferente do que as entradas dos invasores e de outras fontes desconhecidas. O bloqueio inteligente bloqueia os invasores, enquanto permite que os usuários continuem acessando suas contas e sendo produtivos.

Por padrão, o bloqueio inteligente bloqueia a conta de tentativas de login por um minuto após 10 tentativas malsucedidas. A conta é bloqueada novamente após cada tentativa de entrada com falha subsequente durante um minuto na primeira vez, e tempos mais longos em tentativas subsequentes.

O bloqueio inteligente rastreia os últimos três hashes de senha incorreta para evitar o incremento do contador de bloqueio para a mesma senha. Se alguém inserir a mesma senha incorreta várias vezes, esse comportamento não causará o bloqueio da conta.

 > [!NOTE]
 > A funcionalidade de acompanhamento de hash não está disponível para clientes com a autenticação de passagem habilitada, pois a autenticação ocorre localmente, não na nuvem.

Implantações federadas usando AD FS 2016 e AF FS 2019 podem permitir benefícios semelhantes usando [AD FS bloqueio de extranet e bloqueio inteligente de extranet](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection).

O bloqueio inteligente está sempre ativado para todos os clientes do Azure AD com as configurações padrão que oferecem a combinação certa de segurança e usabilidade. Personalização das configurações de bloqueio inteligente, com valores específicos de sua organização, requer o Azure AD Basic ou superior licenças para seus usuários.

O uso do bloqueio inteligente não garante que um usuário genuíno nunca seja bloqueado. Quando o bloqueio inteligente bloqueia uma conta de usuário, tentamos o nosso melhor para não bloquear o usuário genuíno. O serviço de bloqueio tenta garantir que atores mal-intencionados não obtenham acesso a uma conta de usuário original.  

* Cada centro de dados do Azure Active Directory rastreia o bloqueio de forma independente. Um usuário terá (threshold_limit * datacenter_count) número de tentativas, se o usuário acessar cada datacenter.
* O Smart Lockout usa localização familiar versus local desconhecido para diferenciar entre um ator ruim e o usuário genuíno. Locais desconhecidos e familiares ambos terá contadores separados de bloqueio.

Bloqueio inteligente pode ser integrado com implantações híbridas, usando a sincronização de hash de senha ou autenticação de passagem para proteger contas do Active Directory no local de bloqueio por invasores. Definindo políticas de bloqueio inteligente no AD do Azure adequadamente, ataques podem ser filtrados antes que elas atinjam o Active Directory no local.

Ao usar [autenticação de passagem](../hybrid/how-to-connect-pta.md), você precisará certificar-se de que:

* O limite de bloqueio do Azure AD seja **menor** que o limite de bloqueio da conta do Active Directory. Defina os valores de forma que o limite de bloqueio da conta do Active Directory seja pelo menos duas ou três vezes maior do que o limite de bloqueio do Azure AD. 
* A duração do bloqueio do Azure AD deve ser definida por mais tempo do que o Active Directory reiniciar o contador de bloqueios de conta após a duração. Lembre-se de que a duração do Azure AD é definida em segundos, enquanto a duração do anúncio é definida em minutos. 

Por exemplo, se você quiser que seu contador do Azure AD seja maior que o AD, o Azure AD será de 120 segundos (2 minutos) enquanto seu AD local for definido como 1 minuto (60 segundos).

> [!IMPORTANT]
> No momento um administrador não pode desbloquear contas de nuvem dos usuários se eles foram bloqueados fora da funcionalidade do Bloqueio Inteligente. O administrador deve aguardar a duração do bloqueio expirar.

## <a name="verify-on-premises-account-lockout-policy"></a>Verificar políticas de bloqueio de conta no local

Use as instruções a seguir para verificar suas políticas de bloqueio de conta no local do Active Directory:

1. Abra a ferramenta de Gerenciamento de Política de Grupo.
2. Editar a política de grupo que inclui a diretiva de bloqueio de conta da sua organização, por exemplo, o **Default Domain Policy**.
3. Navegue até **Configuração do Computador** > **Políticas** > **Configurações do Windows** > **Configurações de Segurança** > **Políticas de Conta** > **Política de Bloqueio de Conta**.
4. Verifique os valores de **Limite de bloqueio de conta** e **Redefinir contador de bloqueios de conta após**.

![Modificar a política de bloqueio de conta Active Directory local](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Gerenciar valores de bloqueio inteligente do Azure AD

Baseado nos seus requisitos organizacionais, os valores de bloqueio inteligente talvez precisem ser personalizados. Personalização das configurações de bloqueio inteligente, com valores específicos de sua organização, requer o Azure AD Basic ou superior licenças para seus usuários.

Para verificar ou modificar os valores de bloqueio inteligente para a sua organização, use os seguintes passos:

1. Faça logon no [portal do Azure](https://portal.azure.com) e clique em **Azure Active Directory**, então **Métodos de Autenticação**.
1. Coloque o **limite de bloqueio**, baseado em quantas vezes de logins falhados é permitido em uma conta antes do primeiro bloqueio. O padrão é 10.
1. Coloque a **duração do bloqueio em segundos**, para a duração em segundos de cada bloqueio. O padrão é 60 segundos (um minuto).

> [!NOTE]
> Se o primeiro login depois do bloqueio também falhar, a conta será bloqueada de novo. Se uma conta bloquear repetidamente, o bloqueio aumentará a duração.

![Personalize a política de bloqueio inteligente do Azure AD no portal do Azure](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>Como determinar se o recurso de bloqueio inteligente está funcionando ou não

Quando o limite de bloqueio inteligente for disparado, você receberá a seguinte mensagem enquanto a conta estiver bloqueada:

**Sua conta está temporariamente bloqueada para impedir o uso não autorizado. Tente novamente depois e, se ainda tiver problemas, entre em contato com seu administrador.**

## <a name="next-steps"></a>Próximas etapas

* [Descubra como banir senhas ruins na sua organização usando Azure AD.](howto-password-ban-bad.md)
* [Configure reset de senhas self-service para permitir usuários desbloquearem suas próprias contas.](quickstart-sspr.md)
