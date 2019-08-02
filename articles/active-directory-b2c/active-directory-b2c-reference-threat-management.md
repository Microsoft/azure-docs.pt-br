---
title: Gerenciar ameaças a recursos e dados no Azure Active Directory B2C
description: Saiba mais sobre técnicas de detecção e mitigação de ataques de negação de serviço e ataques de senha no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7232917df6018c9c8afc7e7edd3730a277b193f4
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798247"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Gerenciar ameaças a recursos e dados no Azure Active Directory B2C

O Microsoft Azure Active Directory (Azure AD) O B2C possui recursos internos que podem ajudá-lo a proteger contra ameaças a seus recursos e dados. Essas ameaças incluem ataques de negação de serviço e ataques de senha. Os ataques de negação de serviço podem tornar os recursos não disponíveis para usuários pretendidos. Os ataques de senha ocasionam o acesso não autorizado aos recursos.

## <a name="denial-of-service-attacks"></a>Ataques de negação de serviço

Azure Active Directory B2C protege contra ataques de inundação SYN usando um cookie SYN. Azure Active Directory B2C também protege contra ataques de negação de serviço usando limites de taxas e conexões.

## <a name="password-attacks"></a>Ataques de senha

As senhas definidas pelos usuários devem ser de complexidade razoável. O Azure AD B2C possui técnicas de atenuação em vigor para ataques de senha. A mitigação inclui detecção de ataques de senha de força bruta e ataques de senha do dicionário. Ao usar vários sinais, o Azure AD B2C analisa a integridade das solicitações. O Azure AD B2C foi projetado para diferenciar, de forma inteligente, os usuários pretendidos de hackers e botnets.

O Azure AD B2C usa uma estratégia sofisticada para bloquear contas. As contas são bloqueadas com base no IP da solicitação e nas senhas inseridas. A duração do bloqueio também aumenta com base na probabilidade de ser um ataque. Depois que uma senha é tentada 10 vezes sem êxito (o limite de tentativas do padrão), ocorre de um bloqueio de um minuto. Na próxima vez que um logon não for bem-sucedida depois que a conta será desbloqueada (ou seja, depois que a conta tem foi desbloqueada automaticamente pelo serviço depois que o período de bloqueio expirar), outro bloqueio de um minuto ocorre e continua para cada logon sem êxito. Digitar a mesma senha repetidamente não conta como vários logins malsucedidos.

Os primeiros 10 períodos de bloqueio têm um minuto de duração. Os próximos 10 períodos de bloqueio são ligeiramente mais longos e aumentam em duração após cada 10 períodos de bloqueio. O contador de bloqueio é redefinido para zero após um login bem-sucedido quando a conta não está bloqueada. Períodos de bloqueio podem durar até cinco horas.

## <a name="manage-password-protection-settings"></a>Gerenciar configurações de proteção de senha

Para gerenciar configurações de proteção de senha, incluindo o limite de bloqueio:

1. Navegue até o [Portal do Azure](https://portal.azure.com).
1. Selecione o **diretório + assinatura** de filtro no menu superior direito do portal e selecione seu locatário do Azure AD B2C.
1. Selecione **Azure Active Directory** no menu à esquerda (ou selecione **todos os serviços** na seção de canto superior esquerdo do portal, em seguida, procure e selecione *Azure Active Directory*).
1. Sob **segurança**, selecione **métodos de autenticação**, em seguida, selecione **proteção por senha**.
1. Insira suas configurações de proteção de senha desejada e selecione **salvar**.

    ![Página de proteção senha portal do Azure em configurações do Azure AD](media/active-directory-b2c-reference-threat-management/portal-02-password-protection.png)
    <br />*Configurando o limite de bloqueio para 5 no **proteção por senha** configurações*.

## <a name="view-locked-out-accounts"></a>Exibir contas bloqueadas

Para obter informações sobre contas bloqueadas, você pode verificar o Active Directory [relatório de atividade de entrada](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md). Sob **Status**, selecione **falha**. Falha nas tentativas de entrar com uma **entrar o código de erro** de `50053` indicar uma conta bloqueada:

![Seção de relatório do Azure AD entrar mostrando conta bloqueada](media/active-directory-b2c-reference-threat-management/portal-01-locked-account.png)

Para saber mais sobre como exibir o relatório de atividade de entrada no Azure Active Directory, consulte [entrar o códigos de erro de relatório de atividade](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md).
