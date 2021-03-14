---
title: Protegendo a MFA baseada em telefone no Azure AD B2C
titleSuffix: Azure AD B2C
description: Aprenda dicas para proteger a MFA (autenticação multifator) baseada em telefone em seu locatário do Azure AD B2C usando Azure Monitor relatórios e alertas do Log Analytics. Use nossa pasta de trabalho para identificar autenticações de telefone fraudulentas e atenuar os inscrições fraudulentos. =
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 02/01/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cc9e0be90c138ba33e1b4dfe11ea6f9c8b7da297
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033547"
---
# <a name="securing-phone-based-multi-factor-authentication-mfa"></a>Protegendo a MFA (autenticação multifator) baseada em telefone

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Com a MFA (autenticação multifator) do Azure Active Directory (Azure AD), os usuários podem optar por receber uma chamada de voz automática em um número de telefone que eles se registram para verificação. Os usuários mal-intencionados podem aproveitar esse método criando várias contas e fazendo chamadas telefônicas sem concluir o processo de registro de MFA. Essas várias falhas de entrada podem esgotar as tentativas de inscrição permitidas, impedindo que outros usuários se inscrevam em novas contas em seu locatário de Azure AD B2C. Para ajudar a proteger contra esses ataques, você pode usar Azure Monitor para monitorar falhas de autenticação de telefone e atenuar as entradas fraudulentas.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, crie um [espaço de trabalho log Analytics](azure-monitor.md).

## <a name="create-a-phone-based-mfa-events-workbook"></a>Criar uma pasta de trabalho de eventos do MFA baseado em telefone

O repositório [Azure ad B2C relatórios & alertas](https://github.com/azure-ad-b2c/siem#phone-authentication-failures) no GitHub contém artefatos que você pode usar para criar e publicar relatórios, alertas e painéis com base nos logs do Azure ad B2C. A pasta de trabalho de rascunho mostrada abaixo realça as falhas relacionadas ao telefone.

### <a name="overview-tab"></a>Guia Visão Geral

As informações a seguir são mostradas na guia **visão geral** :

- Motivos de falha (o número total de autenticações de telefone com falha para cada motivo determinado)
- Bloqueado devido a uma má reputação
- Endereço IP com autenticações de telefone com falha (a contagem total de autenticações de telefone com falha para cada endereço IP fornecido)
- Números de telefone com endereço IP-autenticações de telefone com falha
- Navegador (falhas de autenticação por telefone por navegador do cliente)
- Sistema operacional (falhas de autenticação por telefone por sistema operacional cliente)

![Guia Visão Geral](media/phone-based-mfa/overview-tab.png)

### <a name="details-tab"></a>Guia Detalhes

As informações a seguir são relatadas na guia **detalhes** :

- Política de Azure AD B2C-autenticações de telefone com falha
- Falhas de autenticação de telefone por número de telefone – gráfico de tempo (linha do tempo ajustável)
- Falhas de autenticação de telefone por Azure AD B2C política – gráfico de tempo (linha do tempo ajustável)
- Falhas de autenticação de telefone por endereço IP – gráfico de tempo (linha do tempo ajustável)
- Selecione o número de telefone para exibir os detalhes da falha (selecione um número de telefone para uma lista detalhada de falhas)

![Guia de detalhes 1 de 3](media/phone-based-mfa/details-tab-1.png)

![Guia detalhes 2 de 3](media/phone-based-mfa/details-tab-2.png)

![Guia de detalhes 3 de 3](media/phone-based-mfa/details-tab-3.png)

## <a name="use-the-workbook-to-identify-fraudulent-sign-ups"></a>Use a pasta de trabalho para identificar as entradas fraudulentas

Você pode usar a pasta de trabalho para entender os eventos de MFA baseados em telefone e identificar o uso potencialmente mal-intencionado do serviço de telefonia.

1. Entenda o que é normal para seu locatário respondendo a estas perguntas:

   - Onde estão as regiões das quais você espera o MFA baseado em telefone?
   - Examine os motivos mostrados para tentativas de MFA baseadas em telefone com falha; Eles são considerados normais ou esperados?

2. Reconheça as características da inscrição fraudulenta:

   - **Baseado no local**: examine as **falhas de autenticação do telefone por endereço IP** para todas as contas associadas a locais dos quais você não espera que os usuários se inscrevam.

   > [!NOTE]
   > O endereço IP fornecido é uma região aproximada.

   - **Baseado em velocidade**: examine as **horas extras de autenticações de telefone com falha (por dia)**, que indica os números de telefone que estão fazendo um número anormal de tentativas de autenticação por telefone com falha por dia, ordenadas da mais alta (esquerda) para a mais baixa (direita).

3. Reduza os inscrições fraudulentos seguindo as etapas na próxima seção.
 

## <a name="mitigate-fraudulent-sign-ups"></a>Mitigar entradas fraudulentas

Execute as seguintes ações para ajudar a mitigar as entradas fraudulentas.

- Use as versões **recomendadas** dos fluxos de usuário para fazer o seguinte:
     
   - [Habilite o recurso de senha de uso único (OTP) de email](phone-authentication-user-flows.md) para MFA (aplica-se a fluxos de inscrição e entrada).
   - [Configure uma política de acesso condicional](conditional-access-user-flow.md) para bloquear entradas com base no local (aplica-se somente a fluxos de entrada, não a fluxos de inscrição).
   - Use conectores de API para [integrar com uma solução AntiBot como reCAPTCHA](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-captcha) (aplica-se a fluxos de inscrição).

- Remova os códigos de país que não são relevantes para sua organização no menu suspenso em que o usuário verifica seu número de telefone (essa alteração será aplicada a futuras entradas):
    
   1. Entre no [portal do Azure](https://portal.azure.com) como administrador global do locatário Azure AD B2C.

   2. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.

   3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.

   4. Selecione o fluxo de usuário e, em seguida, selecione **idiomas**. Selecione o idioma da localização geográfica da sua organização para abrir o painel detalhes do idioma. (Para este exemplo, selecionaremos o **inglês EN** para o Estados Unidos). Selecione a **página autenticação multifator** e selecione **padrões de download (EN)**.
 
      ![Carregar novas substituições para baixar padrões](media/phone-based-mfa/download-defaults.png)

   5. Abra o arquivo JSON que foi baixado na etapa anterior. No arquivo, procure `DEFAULT` e substitua a linha por `"Value": "{\"DEFAULT\":\"Country/Region\",\"US\":\"United States\"}"` . Certifique-se de definir `Overrides` como `true` .

   > [!NOTE]
   > Você pode personalizar a lista de códigos de país permitidos no `countryList` elemento (consulte o [exemplo de página de autenticação do fator de telefone](localization-string-ids.md#phone-factor-authentication-page-example)).

   7. Salve o arquivo JSON. No painel detalhes do idioma, em **carregar novas substituições**, selecione o arquivo JSON modificado para carregá-lo.

   8. Feche o painel e selecione **executar fluxo de usuário**. Para este exemplo, confirme se **Estados Unidos** é o único código de país disponível na lista suspensa:
 
      ![Menu suspenso de código do país](media/phone-based-mfa/country-code-drop-down.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [a proteção de identidade e o acesso condicional para Azure ad B2C](conditional-access-identity-protection-overview.md) 

- Aplicar [acesso condicional a fluxos de usuário no Azure Active Directory B2C](conditional-access-user-flow.md)