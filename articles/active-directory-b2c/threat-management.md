---
title: Gerenciar ameaças a recursos e dados
titleSuffix: Azure AD B2C
description: Saiba mais sobre técnicas de detecção e mitigação de ataques de negação de serviço e ataques de senha no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e5184698cab1874f327173fb30cf527feee48cad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85384967"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Gerenciar ameaças a recursos e dados no Azure Active Directory B2C

O Azure Active Directory B2C (Azure AD B2C) tem recursos internos que podem ajudá-lo a proteger contra ameaças a seus recursos e dados. Essas ameaças incluem ataques de negação de serviço e ataques de senha. Os ataques de negação de serviço podem tornar os recursos não disponíveis para usuários pretendidos. Os ataques de senha ocasionam o acesso não autorizado aos recursos.

## <a name="denial-of-service-attacks"></a>Ataques de negação de serviço

Azure Active Directory B2C protege contra ataques de inundação SYN usando um cookie SYN. Azure Active Directory B2C também protege contra ataques de negação de serviço usando limites de taxas e conexões.

## <a name="password-attacks"></a>Ataques de senha

As senhas definidas pelos usuários devem ser de complexidade razoável. O Azure AD B2C possui técnicas de atenuação em vigor para ataques de senha. A mitigação inclui a detecção de ataques de senha de força bruta e ataques de senha de dicionário. Ao usar vários sinais, o Azure AD B2C analisa a integridade das solicitações. O Azure AD B2C foi projetado para diferenciar, de forma inteligente, os usuários pretendidos de hackers e botnets.

O Azure AD B2C usa uma estratégia sofisticada para bloquear contas. As contas são bloqueadas com base no IP da solicitação e nas senhas inseridas. A duração do bloqueio também aumenta com base na probabilidade de ser um ataque. Depois que uma senha é tentada 10 vezes sem êxito (o limite de tentativas padrão), ocorre um bloqueio de um minuto. Na próxima vez que um logon não for bem-sucedido depois que a conta for desbloqueada (ou seja, depois que a conta for desbloqueada automaticamente pelo serviço depois que o período de bloqueio expirar), ocorrerá outro bloqueio de um minuto e continuará para cada logon malsucedido. Digitar a mesma senha repetidamente não conta como vários logins malsucedidos.

Os primeiros 10 períodos de bloqueio têm um minuto de duração. Os próximos 10 períodos de bloqueio são ligeiramente mais longos e aumentam em duração após cada 10 períodos de bloqueio. O contador de bloqueio é redefinido para zero após um login bem-sucedido quando a conta não está bloqueada. Períodos de bloqueio podem durar até cinco horas.

## <a name="manage-password-protection-settings"></a>Gerenciar configurações de proteção de senha

Para gerenciar as configurações de proteção de senha, incluindo o limite de bloqueio:

1. Entre no [Portal do Azure](https://portal.azure.com)
1. Use o filtro **diretório + assinatura** no menu superior para selecionar o diretório que contém seu locatário Azure ad B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Em **segurança**, selecione **métodos de autenticação (versão prévia)** e selecione **proteção por senha**.
1. Insira as configurações de proteção de senha desejadas e, em seguida, selecione **salvar**.

    ![Página de proteção de senha portal do Azure nas configurações do Azure AD](./media/threat-management/portal-02-password-protection.png)
    <br />*Definindo o limite de bloqueio como 5 em configurações de **proteção de senha** *.

## <a name="view-locked-out-accounts"></a>Exibir contas bloqueadas

Para obter informações sobre contas bloqueadas, você pode verificar o Active Directory [relatório de atividade de entrada](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md). Em **status**, selecione **falha**. Tentativas de entrada com falha com um **código de erro de entrada** de `50053` indicar uma conta bloqueada:

![Seção do relatório de entrada do Azure AD mostrando a conta bloqueada](./media/threat-management/portal-01-locked-account.png)

Para saber mais sobre como exibir o relatório de atividade de entrada no Azure Active Directory, consulte [códigos de erro de relatório de atividade de entrada](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md).
