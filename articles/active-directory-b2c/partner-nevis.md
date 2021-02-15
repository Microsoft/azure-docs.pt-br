---
title: Tutorial para configurar Azure Active Directory B2C com Nevis
titleSuffix: Azure AD B2C
description: Saiba como integrar a autenticação de Azure AD B2C com Névis para autenticação sem senha
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/23/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 282ec6a25dc381dc51f28534d272bae57d2e792e
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98674986"
---
# <a name="tutorial-to-configure-nevis-with-azure-active-directory-b2c-for-passwordless-authentication"></a>Tutorial para configurar o Nevis com Azure Active Directory B2C para autenticação sem senha

Neste tutorial de exemplo, saiba como estender Azure AD B2C com  [Névis](https://www.nevis.net/solution/authentication-cloud) para habilitar a autenticação sem senha. Nevis fornece uma experiência de usuário final de primeira marca e móvel com identidade visual com o aplicativo de acesso do Nevis para fornecer autenticação de cliente forte e cumprir os requisitos de transação de PSD2 (Diretiva de serviços de pagamento 2).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará de:

- Uma [conta de avaliação](https://www.nevis-security.com/aadb2c/) do Nevis

- Uma assinatura do Azure AD. Se você não tiver uma, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).

- Um [locatário Azure ad B2C](./tutorial-create-tenant.md) que está vinculado à sua assinatura do Azure.

- Configurado Azure AD B2C ambiente para usar [políticas personalizadas](./custom-policy-get-started.md), se você quiser integrar o Nevis ao seu fluxo de política de inscrição.

## <a name="scenario-description"></a>Descrição do cenário

Nesse cenário, adicione um aplicativo de acesso totalmente marcado ao seu aplicativo de back-end para autenticação sem senha. Os seguintes componentes compõem a solução:

- Um locatário Azure AD B2C, com uma política de entrada e inscrição combinada para seu back-end
- Nevis instance e sua API REST para aprimorar Azure AD B2C
- Seu próprio aplicativo de acesso de identidade visual

O diagrama mostra a implementação.

![Fluxo de entrada de senha de alto nível com Azure AD B2C e Névis](./media/partner-nevis/nevis-architecture-diagram.png)

|Etapa | Descrição |
|:-----| :-----------|
| 1. | Um usuário tenta entrar ou se inscrever em um aplicativo por meio Azure AD B2C política de entrada e inscrição.
| 2. | Durante a inscrição, o aplicativo de acesso Nevis é registrado no dispositivo de usuário usando um código QR. Uma chave privada é gerada no dispositivo de usuário e é usada para assinar as solicitações do usuário.
| 3. |  Azure AD B2C usa um perfil técnico RESTful para iniciar o logon com a solução Nevis.
| 4. | A solicitação de logon é enviada para o aplicativo Access, seja como uma mensagem de push, um código QR ou um link profundo.
| 5. | O usuário aprova a tentativa de entrada com sua biometria. Uma mensagem é retornada para Névis, que verifica o logon com a chave pública armazenada.
| 6. | Azure AD B2C envia uma última solicitação para Nevis para confirmar que o logon foi concluído com êxito.
| 7. |Com base na mensagem de êxito/falha do Azure AD B2C usuário é concedido/negado acesso ao aplicativo.

## <a name="integrate-your-azure-ad-b2c-tenant"></a>Integrar seu locatário do Azure AD B2C

### <a name="onboard-to-nevis"></a>Integração a Névis 

[Inscreva-se para uma conta do Nevis](https://www.nevis-security.com/aadb2c/).
Você receberá dois emails:

1. Uma notificação de conta de gerenciamento

2. Um convite de aplicativo móvel.

### <a name="add-your-azure-ad-b2c-tenant-to-your-nevis-account"></a>Adicionar seu locatário do Azure AD B2C à sua conta do Nevis

1. No email de avaliação da conta de gerenciamento do Nevis, copie sua chave de gerenciamento para a área de transferência.

2. Abra https://console.nevis.cloud/ o em um navegador.

3. Entre no console de gerenciamento com sua chave.

4. Selecione **Adicionar instância**

5. Selecione a instância recém-criada para abri-la.

6. Na barra de navegação lateral, selecione **integrações personalizadas**

7. Selecione **Adicionar integração personalizada**.

8. Para nome de integração, insira o nome do seu Azure AD B2C locatário.

9. Para URL/domínio, insira `https://yourtenant.onmicrosoft.com`

10. Selecione **Avançar**

>[!NOTE]
>Você precisará do Nevis Access token mais tarde.

11. Selecione **Concluído**.

### <a name="install-the-nevis-access-app-on-your-phone"></a>Instalar o aplicativo de acesso Nevis em seu telefone

1. No email de avaliação do Nevis Mobile App, abra o convite do **aplicativo de vôo de teste** .

2. Instale o aplicativo.

3. Siga as instruções fornecidas para instalar o aplicativo de acesso do Nevis.

### <a name="integrate-azure-ad-b2c-with-nevis"></a>Integre Azure AD B2C com Nevis

1. Abra o [portal do Azure](https://portal.azure.com/).

2. Alterne para o locatário do Azure AD B2C. Verifique se você selecionou o locatário correto, pois o locatário de Azure AD B2C geralmente está em um locatário separado.

3. No menu, selecione **Identity Experience Framework (IEF)**

4. Selecionar **chaves de política**

5. Selecione **Adicionar** e crie uma nova chave com as seguintes configurações:

      a. Selecionar **manual** em opções

      b. Definir nome como **AuthCloudAccessToken**

      c. Cole o token de **acesso do Nevis** armazenado anteriormente no campo segredo

      d. Para o uso da chave, selecione **criptografia**

      e. Escolha **Criar**

### <a name="configure-and-upload-the-nevishtml-to-azure-blob-storage"></a>Configurar e carregar o nevis.html para o armazenamento de BLOBs do Azure

1. No seu ambiente de identidade (IDE), vá para a pasta [**política**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) .

2. Abra o arquivo  [**nevis.html**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/nevis.html) .

3. Substitua o  **authentication_cloud_url** pela URL do seu Nevis admin console- `https://<instance_id>.mauth.nevis.cloud` .

4. **Salve** as alterações no arquivo.

5. Siga as [instruções](./customize-ui-with-html.md#2-create-an-azure-blob-storage-account) e carregue o arquivo **nevis.html** no armazenamento de BLOBs do Azure.

6. Siga as [instruções](./customize-ui-with-html.md#3-configure-cors) e habilite o compartilhamento de recursos entre origens (CORS) para este arquivo.

7. Quando o upload for concluído e o CORS estiver habilitado, selecione o arquivo **nevis.html** na lista.

8. Na guia **visão geral** , ao lado da **URL**, selecione o ícone **Copiar link** .

9. Abra o link em uma nova guia do navegador para verificar se ele exibe uma caixa cinza.

>[!NOTE]
>Você precisará do link de blob mais tarde.

### <a name="customize-your-trustframeworkbasexml"></a>Personalizar seu TrustFrameworkBase.xml

1. No IDE, vá para a pasta [**política**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) .

2. Abra o arquivo [**TrustFrameworkBase.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkBase.xml) .

3. Substitua **seulocatario** pelo nome da sua conta de locatário do Azure na **tenantid**.

4. Substitua **seulocatario** pelo nome da sua conta de locatário do Azure em **PublicPolicyURI**.

5. Substituir todas as instâncias de **authentication_cloud_url** pela URL do seu Nevis admin console

6. **Salve** as alterações em seu arquivo.

### <a name="customize-your-trustframeworkextensionsxml"></a>Personalizar seu TrustFrameworkExtensions.xml

1. No IDE, vá para a pasta [**política**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) .

2. Abra o arquivo [**TrustFrameworkExtensions.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkExtensions.xml) .

3. Substitua **seulocatario** pelo nome da sua conta de locatário do Azure na **tenantid**.

4. Substitua **seulocatario** pelo nome da sua conta de locatário do Azure em **PublicPolicyURI**.

5. Em **BasePolicy**, na **tenantid**, substitua também _seulocatario_ pelo nome da sua conta de locatário do Azure.

6. Em **BuildingBlocks**, substitua **LoadUri** pela URL de link de blob do seu _nevis.html_ em sua conta de armazenamento de BLOBs.

7. **Salve** o arquivo.

### <a name="customize-your-signuporsigninxml"></a>Personalizar seu SignUpOrSignin.xml

1. No IDE, vá para a pasta [**política**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) .

2. Abra o arquivo [**SignUpOrSignin.xml**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/SignUpOrSignin.xml) .

3. Substitua **seulocatario** pelo nome da sua conta de locatário do Azure na **tenantid**.

4. Substitua **seulocatario** pelo nome da sua conta de locatário do Azure em **PublicPolicyUri**.

5. Em **BasePolicy**, em **tenantid**, substitua também **seulocatario** pelo nome da sua conta de locatário do Azure.

6. **Salve** o arquivo.

### <a name="upload-your-custom-policies-to-azure-ad-b2c"></a>Carregue suas políticas personalizadas para Azure AD B2C

1. Abra seu [Azure ad B2C](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview) página inicial do locatário.

2. Selecione **Estrutura de Experiência de Identidade**.

3. Selecione **Carregar política personalizada**.

4. Selecione o arquivo de **TrustFrameworkBase.xml** que você modificou.

5. Marque a caixa de seleção **substituir a política personalizada se ela já existir** .
6. Selecione **Carregar**.

7. Repita as etapas 5 e 6 para **TrustFrameworkExtensions.xml**.

8. Repita as etapas 5 e 6 para **SignUpOrSignin.xml**.

## <a name="test-the-user-flow"></a>Testar o fluxo de usuário

### <a name="test-account-creation-and-nevis-access-app-setup"></a>Testar a criação da conta e a instalação do aplicativo de acesso Nevis

1. Abra seu [Azure ad B2C](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview) página inicial do locatário.

2. Selecione **Estrutura de Experiência de Identidade**.

3. Role para baixo até políticas personalizadas e selecione **B2C_1A_signup_signin**.

4. Selecione **Executar Agora**.

5. Na janela pop-up, selecione **inscrever-se agora**.

6. Adicione seu endereço de email.

7. Selecione **enviar código de verificação**.

8. Copie sobre o código de verificação do email.

9. Selecione **Verificar**.

10. Preencha o formulário com a nova senha e o nome de exibição.

11. Selecione **Criar**.

12. Você será levado para a página de verificação de código QR.

13. Em seu telefone, abra o **aplicativo de acesso do Nevis**.

14. Selecione **ID do rosto**.

15. Quando a tela informar que o **registro do autenticador foi bem-sucedido**, selecione **continuar**.

16. Em seu telefone, autentique-se com o seu rosto novamente.

17. Você será levado para a página de aterrissagem do [JWT.ms](https://jwt.ms) que exibe os detalhes do token decodificado.

### <a name="test-the-pure-passwordless-sign-in"></a>Testar a entrada pura sem senha

1. Em **Identity Experience Framework**, selecione o **B2C_1A_signup_signin**.

2. Selecione **Executar Agora**.

3. Na janela pop-up, selecione **autenticação sem senha**.

4. Insira seu endereço de email.

5. Selecione **Continuar**.

6. Em seu telefone, em notificações, selecione **notificação de aplicativo de acesso Nevis**.

7. Autentique com seu rosto.

8. Você será levado automaticamente para a página de aterrissagem do [JWT.ms](https://jwt.ms) que exibe seus tokens.

## <a name="next-steps"></a>Próximas etapas

Para obter informações adicionais, examine os artigos a seguir

- [Políticas personalizadas no Azure AD B2C](./custom-policy-overview.md)

- [Introdução às políticas personalizadas no Azure AD B2C](./custom-policy-get-started.md?tabs=applications)