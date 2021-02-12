---
title: Configurar o Identity Protection e o acesso condicional no Azure AD B2C
description: Saiba como configurar o Identity Protection e o acesso condicional no seu locatário do Azure AD B2C para ver entradas suspeitas e outros eventos de risco, além de criar políticas baseadas em detecções de risco.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/01/2020
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 654206bccd25bf09fcdc5c3e7ee72ba97c75af2a
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98785474"
---
# <a name="set-up-identity-protection-and-conditional-access-in-azure-ad-b2c"></a>Configurar o Identity Protection e o acesso condicional no Azure AD B2C

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

O Identity Protection fornece detecção de risco contínua para seu locatário do Azure AD B2C. Se o tipo de preço do seu locatário do Azure AD B2C for Premium P2, você poderá ver os eventos de risco do Identity Protection detalhados no portal do Azure. Você também poderá usar políticas de [acesso condicional](../active-directory/conditional-access/overview.md) com base nessas detecções de risco para determinar ações e impor políticas organizacionais.

## <a name="prerequisites"></a>Pré-requisitos

- Seu locatário do Azure AD B2C precisa estar [vinculado a uma assinatura do Azure AD](billing.md#link-an-azure-ad-b2c-tenant-to-a-subscription).
- É necessário que o Azure AD B2C Premium P2 use o acesso condicional baseado em risco do usuário e na entrada. Se necessário, [altere o tipo de preço do Azure AD B2C para Premium P2](./billing.md). 
- Para gerenciar o Identity Protection e o acesso condicional no seu locatário B2C, você precisará ter uma conta que tenha recebido a função administrador global ou administrador de segurança.
- Para usar esses recursos no seu locatário, primeiro, você precisará alternar para o tipo de preço Azure AD B2C Premium P2.

## <a name="set-up-identity-protection"></a>Configurar o Identity Protection

Por padrão, o Identity Protection está ativado. Para ver os eventos de risco do Identity Protection no seu locatário do Azure AD B2C, basta vinculá-lo a uma assinatura do Azure AD e selecionar o tipo de preço Azure AD B2C Premium P2. Veja os relatórios detalhados dos eventos de risco no portal do Azure.

### <a name="supported-identity-protection-risk-detections"></a>Detecções de risco do Identity Protection compatíveis

No momento, há suporte para as seguintes detecções de risco no Azure AD B2C:  

|Tipo de detecção de risco  |Descrição  |
|---------|---------|
| Viagem atípica     | Entrada proveniente de uma localização atípica com base nas conexões recentes do usuário.        |
|Endereço IP anônimo     | Entrada de um endereço IP anônimo (por exemplo: navegador Tor, VPNs para anonimato).        |
|Endereço IP vinculado a malware     | Entrada de um endereço IP vinculado a malware.         |
|Propriedades de entrada desconhecidas     | Entrada com propriedades que não vimos recentemente para o usuário especificado.        |
|Usuário comprometido confirmado pelo administrador    | Um administrador indicou que um usuário foi comprometido.             |
|Pulverização de senha     | Entre por meio de um ataque de pulverização de senha.      |
|Inteligência contra ameaças do Azure AD     | As fontes internas e externas de inteligência contra ameaças da Microsoft identificaram um padrão de ataque conhecido.        |

## <a name="view-risk-events-for-your-azure-ad-b2c-tenant"></a>Ver os eventos de risco no seu locatário do Azure AD B2C

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.

1. No portal do Azure, pesquise e selecione **Azure AD B2C**.

1. Em **Segurança**, escolha **Usuários suspeitos (Versão Prévia)** .

   ![Usuários de risco](media/conditional-access-identity-protection-setup/risky-users.png)

1. Em **Segurança**, escolha **Detecções de risco (Versão Prévia)** .

   ![Detecções de risco](media/conditional-access-identity-protection-setup/risk-detections.png)

## <a name="add-a-conditional-access-policy"></a>Adicionar uma política de acesso condicional 

Para adicionar uma política de acesso condicional com base nas detecções de risco do Identity Protection, verifique se os padrões de segurança estão desabilitados no seu locatário do Azure AD B2C e crie políticas de acesso condicional.

### <a name="to-disable-security-defaults"></a>Para desabilitar os padrões de segurança

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.

3. Na portal do Azure, procure e selecione **Azure Active Directory**.

4. Escolha **Propriedades** e selecione **Gerenciar Padrões de segurança**.

   ![Desabilitar os padrões de segurança](media/conditional-access-identity-protection-setup/disable-security-defaults.png)

5. Em Habilitar Padrões de segurança, selecione Não. 

   ![Defina a alternância Habilitar padrões de segurança como Não](media/conditional-access-identity-protection-setup/enable-security-defaults-toggle.png)

### <a name="to-create-a-conditional-access-policy"></a>Para criar uma política de acesso condicional

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.

1. No portal do Azure, pesquise e selecione **Azure AD B2C**.

1. Em **Segurança**, escolha **Acesso Condicional (Versão Prévia)** . A página **Políticas de Acesso Condicional** será aberta. 

1. Selecione **Nova política** e siga a documentação do acesso condicional do Azure AD para criar uma política. Para políticas baseadas em risco, você precisará configurar políticas separadas com base no [risco do usuário](../active-directory/conditional-access/howto-conditional-access-policy-risk-user.md#enable-with-conditional-access-policy) ou no [risco de entrada](../active-directory/conditional-access/howto-conditional-access-policy-risk.md#enable-with-conditional-access-policy) dependendo do tipo de risco que você deseja usar como condição. Não recomendamos o uso de ambos os tipos de risco em uma política.

   > [!IMPORTANT]
   > Ao selecionar os usuários aos quais você deseja aplicar a política, não escolha apenas **Todos os usuários** ou você poderá bloquear sua inscrição.

## <a name="test-the-conditional-access-policy"></a>Testar a política de acesso condicional

1. Crie uma política de acesso condicional, conforme mencionado acima, com as seguintes configurações:
   
   - Em **usuários e grupos**, selecione o usuário de teste. Não selecione **Todos os usuários** ou você bloqueará sua inscrição.
   - Em **Aplicativos de nuvem ou ações**, escolha **Selecionar aplicativos** e o aplicativo de terceira parte confiável.
   - Em Condições, escolha **Risco de entrada** e os níveis de risco **Alto**, **Médio** e **Baixo**.
   - Em **Concessão**, escolha **Bloquear o acesso**.

      ![Escolher Bloquear o acesso](media/conditional-access-identity-protection-setup/test-conditional-access-policy.png)

1. Habilite a política de acesso condicional de teste selecionando **Criar**.

1. Simule uma entrada suspeita usando o [Navegador Tor](https://www.torproject.org/download/). 

1. No token decodificado jwt.ms para a tentativa de entrada, você verá que a entrada foi bloqueada:

   ![Testar uma entrada bloqueada](media/conditional-access-identity-protection-setup/test-blocked-sign-in.png)

## <a name="review-conditional-access-outcomes-in-the-audit-report"></a>Examinar os resultados do acesso condicional no relatório de auditoria

Para examinar o resultado de um evento de acesso condicional:

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.

3. No portal do Azure, pesquise e selecione **Azure AD B2C**.

4. Em **Atividades**, selecione **Logs de auditoria**.

5. Filtre o log de auditoria definindo **Categoria** como **B2C** e configurando **Tipo de Recurso de Atividade** como **IdentityProtection**. Em seguida, selecione **Aplicar**.

6. Examine a atividade de auditoria no período de até os últimos sete dias. Os seguintes tipos de atividades estão incluídos:

   - **Avaliar as políticas de acesso condicional**: essa entrada de log de auditoria indica que uma avaliação de acesso condicional foi executada durante uma autenticação.
   - **Corrigir usuário**: essa entrada indica que a concessão ou os requisitos de uma política de acesso condicional foram atendidos pelo usuário final e que essa atividade foi relatada ao mecanismo de risco para reduzir o risco (mitigar) do usuário.

7. Selecione uma entrada de log **Avaliar a política de acesso condicional** na lista para abrir a página **Detalhes da Atividade: Log de auditoria**, que mostra os identificadores de log de auditoria, juntamente com estas informações na seção **Detalhes Adicionais**:

   - ConditionalAccessResult: a concessão necessária para a avaliação da política condicional.
   - AppliedPolicies: uma lista de todas as políticas de acesso condicional em que as condições foram atendidas e as políticas estão ativadas.
   - ReportingPolicies: uma lista das políticas de acesso condicional que foram definidas para o modo somente relatório e nas quais as condições foram atendidas.

## <a name="next-steps"></a>Próximas etapas

[Adicionar o acesso condicional a um fluxo de usuário](conditional-access-user-flow.md).
