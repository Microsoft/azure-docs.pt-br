---
title: Investigação de riscos com o Azure Active Directory B2C Identity Protection
description: Saiba como investigar usuários suspeitos e detecções no Azure AD B2C Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 03/03/2021
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celested
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 8919285f31e04a51ce10afe3313b28cf86b64ee0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102055178"
---
# <a name="investigate-risk-with-identity-protection-in-azure-ad-b2c"></a>Investigar o risco com o Identity Protection no Azure AD B2C

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

O Identity Protection fornece detecção de risco contínua para seu locatário do Azure AD B2C. Ele permite que as organizações descubram, investiguem e corrijam riscos baseados em identidade. O Identity Protection tem relatórios de risco que podem ser usados para investigar os riscos de identidade em locatários do Azure AD B2C. Neste artigo, você aprenderá a investigar e mitigar os riscos.

## <a name="overview"></a>Visão geral

O Azure AD B2C Identity Protection fornece dois relatórios. O relatório de *Usuários suspeitos* é o local em que os administradores podem descobrir quais usuários estão em risco e detalhes sobre as detecções. O relatório de *detecções de risco* fornece informações sobre cada detecção de risco, incluindo tipo, outros riscos disparados ao mesmo tempo, localização da tentativa de conexão e muito mais.

Cada relatório é iniciado com uma lista de todas as detecções do período que aparece na parte superior do relatório. Os relatórios podem ser filtrados usando-se os filtros na parte superior do relatório. Os administradores podem optar por baixar os dados ou usar a [API do MS Graph e o SDK do Microsoft Graph para PowerShell](../active-directory/identity-protection/howto-identity-protection-graph-api.md) para exportar os dados continuamente.

## <a name="service-limitations-and-considerations"></a>Limitações e considerações do serviço

Ao usar o Identity Protection, considere o seguinte:

- Por padrão, o Identity Protection está ativado.
- O Identity Protection está disponível para identidades locais e sociais, como Google ou Facebook. Para identidades sociais, o Acesso Condicional precisa ser ativado. A detecção é limitada porque as credenciais da conta de rede social são gerenciadas pelo provedor de identidade externo.
- Nos locatários do Azure AD B2C, somente um subconjunto das [detecções de risco do Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) está disponível. As seguintes detecções de risco são compatíveis com o Azure AD B2C:  

|Tipo de detecção de risco  |Descrição  |
|---------|---------|
| Viagem atípica     | Conexão proveniente de uma localização atípica com base nas conexões recentes do usuário.        |
|Endereço IP anônimo     | Conexão de um endereço IP anônimo (por exemplo: navegador Tor, VPNs para anonimato).        |
|Endereço IP vinculado a malware     | Conexão de um endereço IP vinculado a malware.         |
|Propriedades de entrada desconhecidas     | Conexão com propriedades que não foram vistas recentemente para o usuário especificado.        |
|Usuário comprometido confirmado pelo administrador    | Um administrador indicou que um usuário foi comprometido.             |
|Pulverização de senha     | Conexão por meio de um ataque de pulverização de senha.      |
|Inteligência contra ameaças do Azure AD     | As fontes internas e externas de inteligência contra ameaças da Microsoft identificaram um padrão de ataque conhecido.        |

## <a name="pricing-tier"></a>Tipo de preço

O Azure AD B2C Premium P2 é necessário para alguns recursos do Identity Protection. Se necessário, [altere o tipo de preço do Azure AD B2C para Premium P2](./billing.md). A tabela a seguir resume os recursos do Identity Protection e o tipo de preço necessário.  

|Recurso   |P1   |P2|
|----------|:-----------:|:------------:|
|Relatório de usuários arriscados     |&#x2713; |&#x2713; |
|Detalhes do relatório de usuários suspeitos  | |&#x2713; |
|Correção do relatório de usuários suspeitos    | &#x2713; |&#x2713; |
|Relatório de detecções de risco   |&#x2713;|&#x2713;|
|Detalhes do relatório de detecções de risco  ||&#x2713;|
|Download do relatório |  &#x2713;| &#x2713;|
|Acesso à API do MS Graph |  &#x2713;| &#x2713;|

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="investigate-risky-users"></a>Investigar usuários arriscados

Com as informações fornecidas pelo relatório de usuários suspeitos, os administradores podem descobrir:

- Quais usuários estão em risco, tiveram o risco corrigido ou tiveram o risco ignorado?
- Detalhes sobre as detecções
- Histórico de todas as entradas suspeitas
- Histórico de risco
 
Depois, os administradores poderão optar por tomar medidas sobre esses eventos. Os administradores podem escolher:

- Redefinir a senha do usuário
- Confirmar usuário comprometido
- Ignorar o risco de usuário
- Bloquear a entrada de um usuário
- Investigar mais usando o ATP do Azure

### <a name="navigating-the-risky-users-report"></a>Navegando no relatório de usuários suspeitos

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.

1. Em **Serviços do Azure**, selecione **Azure AD B2C**. Ou use a caixa de pesquisa para localizar e selecionar **Azure AD B2C**.

1. Em **Segurança**, escolha **Usuários suspeitos (Versão Prévia)** .

   ![Usuários de risco](media/identity-protection-investigate-risk/risky-users.png)

    A seleção de entradas individuais expande uma janela de detalhes abaixo das detecções. A exibição de detalhes permite que os administradores investiguem e executem ações em cada detecção.

    ![Ações de usuários suspeitos](media/identity-protection-investigate-risk/risky-users-report-actions.png)


## <a name="risk-detections-report"></a>Relatório de detecções de risco

O relatório de detecções de risco contém dados filtráveis para até os últimos 90 dias (três meses).

Com as informações fornecidas pelo relatório de detecções de risco, os administradores podem descobrir:

- Informações sobre cada detecção de risco, incluindo o tipo.
- Outros riscos disparados ao mesmo tempo.
- Local da tentativa de entrada.

Depois, os administradores poderão optar por retornar ao relatório de risco ou de entradas do usuário para executar ações com base nas informações coletadas.

### <a name="navigating-the-risk-detections-report"></a>Como navegar no relatório de detecções de risco

1. No portal do Azure, pesquise e selecione **Azure AD B2C**.
1. Em **Segurança**, escolha **Detecções de risco (Versão Prévia)** .

   ![Detecções de risco](media/identity-protection-investigate-risk/risk-detections.png)


## <a name="next-steps"></a>Próximas etapas

- [Adicionar o acesso condicional a um fluxo de usuário](conditional-access-user-flow.md).