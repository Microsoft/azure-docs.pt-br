---
title: Tutorial para configurar o Azure Active Directory B2C com o LexisNexis
titleSuffix: Azure AD B2C
description: Saiba como integrar a autenticação de Azure AD B2C com o LexisNexis, que é um serviço de validação de identidade e criação de perfil, e é usado para verificar a identificação do usuário e fornecer avaliações de risco abrangentes com base no dispositivo do usuário.
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 5f408699cae9580188a3780fc8f8654eaa97c26b
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108426"
---
# <a name="tutorial-for-configuring-lexisnexis-with-azure-active-directory-b2c"></a>Tutorial para configurar o LexisNexis com o Azure Active Directory B2C

Neste tutorial de exemplo, fornecemos orientações sobre como integrar o Azure AD B2C com o [LexisNexis](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd). O LexisNexis fornece uma variedade de soluções, você pode encontrá-las [aqui](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd). Neste tutorial de exemplo, abordaremos a solução **ThreatMetrix** da LexisNexis. ThreatMetrix é um serviço de validação de identidade e criação de perfil. Ele é usado para verificar a identificação do usuário e fornecer avaliações de risco abrangentes com base no dispositivo do usuário.

Essa integração faz a criação de perfil com base em algumas partes de informações do usuário, que é fornecida pelo usuário durante o fluxo de inscrição. ThreatMetrix determina se o usuário deve ter permissão para continuar a fazer logon ou não. Os seguintes atributos são considerados na análise de risco do ThreatMetrix:

- Email
- Número do telefone
- Informações de criação de perfil coletadas do computador do usuário

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará de:

- Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).

- [Um locatário Azure ad B2C](./tutorial-create-tenant.md) que está vinculado à sua assinatura do Azure.

## <a name="scenario-description"></a>Descrição do cenário

A integração do ThreatMetrix inclui os seguintes componentes:

- Azure AD B2C – o servidor de autorização, responsável por verificar as credenciais do usuário, também conhecido como provedor de identidade

- ThreatMetrix – o serviço ThreatMetrix usa as entradas fornecidas pelo usuário e a combina com informações de criação de perfil coletadas do computador do usuário para verificar a segurança da interação do usuário.

- API REST personalizada – essa API implementa a integração entre Azure AD B2C e o serviço ThreatMetrix.

O diagrama de arquitetura a seguir mostra a implementação.

![captura de tela para LexisNexis-arquitetura-diagrama](media/partner-lexisnexis/lexisnexis-architecture-diagram.png)

|Etapa | Descrição |
|:--------------|:-------------|
|1. | O usuário chega em uma página de logon. O usuário seleciona inscrever-se para criar uma nova conta e inserir informações na página. Azure AD B2C coleta os atributos de usuário.
| 2. | Azure AD B2C chama a API de camada intermediária e passa os atributos de usuário.
| 3. | A API de camada intermediária coleta atributos de usuário e os transforma em um formato que a API LexisNexis pode consumir. Em seguida, envia-o para LexisNexis.  
| 4. | O LexisNexis consome as informações e processa-a para validar a identificação do usuário com base na análise de riscos. Em seguida, ele retorna o resultado para a API da camada intermediária.
| 5. | A API de camada intermediária processa as informações e envia informações relevantes de volta para Azure AD B2C.
| 6. | Azure AD B2C recebe informações de volta da API de camada intermediária. Se ele mostrar uma resposta de falha, uma mensagem de erro será exibida para o usuário. Se ele mostrar uma resposta de êxito, o usuário será autenticado e terá acesso concedido.

## <a name="onboard-with-lexisnexis"></a>Integração com o LexisNexis

1. Para criar uma conta do LexisNexis, entre em contato com [LexisNexis](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)

2. Crie uma política de LexisNexis que atenda às suas necessidades. Use a documentação disponível [aqui](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd).

>[!NOTE]
> O nome da política será usado posteriormente.

Depois que uma conta for criada, você receberá as informações necessárias para a configuração da API. As seções a seguir descrevem o processo.

## <a name="configure-azure-ad-b2c-with-lexisnexis"></a>Configurar Azure AD B2C com LexisNexis

### <a name="part-1---deploy-the-api"></a>Parte 1-implantar a API

Implante o [código de API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/Api) fornecido em um serviço do Azure. O código pode ser publicado no Visual Studio, seguindo estas [instruções](/visualstudio/deployment/quickstart-deploy-to-azure).

>[!NOTE]
>Você precisará da URL do serviço implantado para configurar o Azure AD com as configurações necessárias.

### <a name="part-2---configure-the-api"></a>Parte 2-configurar a API

As configurações do aplicativo podem ser [configuradas no serviço de aplicativo no Azure](../app-service/configure-common.md#configure-app-settings).  Com esse método, as configurações podem ser configuradas com segurança sem verificá-las em um repositório. Você precisará fornecer as seguintes configurações para a API REST:

| Configurações do aplicativo | Fonte | Observações |
| :-------- | :------------| :-----------|
|ThreatMetrix: URL | Configuração da conta do ThreatMetrix |     |
|ThreatMetrix: OrgId | Configuração da conta do ThreatMetrix |     |
|ThreatMetrix: ApiKey |Configuração da conta do ThreatMetrix|  |
|ThreatMetrix: política | Nome da política criada em ThreatMetrix | |
| BasicAuth:ApiUsername |Definir um nome de usuário para a API| O nome de usuário será usado na configuração de Azure AD B2C
| BasicAuth:ApiPassword | Definir uma senha para a API | A senha será usada na configuração do Azure AD B2C

### <a name="part-3---deploy-the-ui"></a>Parte 3-implantar a interface do usuário

Essa solução usa modelos de interface do usuário personalizados que são carregados pelo Azure AD B2C. Esses modelos de interface do usuário fazem a criação de perfil que é enviada diretamente ao serviço ThreatMetrix.

Consulte estas [instruções](./customize-ui-with-html.md#custom-page-content-walkthrough) para implantar os arquivos de [interface do usuário](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/ui-template) incluídos em uma conta de armazenamento de BLOBs. As instruções incluem a configuração de uma conta de armazenamento de BLOBs, a configuração do CORS e a habilitação do acesso público.

A interface do usuário é baseada no [modelo azul do oceano](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/ui-template/ocean_blue). Todos os links na interface do usuário devem ser atualizados para fazer referência ao local implantado. Na pasta de interface do usuário, localize e substitua https://yourblobstorage/blobcontainer pelo local implantado.

### <a name="part-4---create-api-policy-keys"></a>Parte 4-criar chaves de política de API

Consulte este [documento](./secure-rest-api.md#add-rest-api-username-and-password-policy-keys) e crie duas chaves de política – uma para o nome de usuário da API e outra para a senha da API que você definiu acima.

A política de exemplo usa estes nomes de chave:

- B2C_1A_RestApiUsername

- B2C_1A_RestApiPassword

### <a name="part-5---update-the-api-url"></a>Parte 5-atualizar a URL da API

Na [política TrustFrameworkExtensions](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/ThreatMetrix/policy/TrustFrameworkExtensions.xml)fornecida, localize o perfil técnico chamado `Rest-LexisNexus-SessionQuery` e atualize o item de `ServiceUrl` metadados com o local da API implantada acima.

### <a name="part-6---update-ui-url"></a>Parte 6-atualizar URL da interface do usuário

Na [política TrustFrameworkExtensions](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/ThreatMetrix/policy/TrustFrameworkExtensions.xml)fornecida, faça uma localização e substitua para pesquisar https://yourblobstorage/blobcontainer/ pelo local em que os arquivos de interface do usuário são implantados.

>[!NOTE]
> Como prática recomendada, recomendamos que os clientes adicionem notificação de autorização na página coleção de atributos. Notifique os usuários que as informações serão enviadas a serviços de terceiros para verificação de identidade.

### <a name="part-7---configure-the-azure-ad-b2c-policy"></a>Parte 7-configurar a política de Azure AD B2C

Consulte este [documento](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) para baixar o [pacote inicial de contas locais](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) e configurar a [política](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/policy) para o locatário Azure ad B2C.

>[!NOTE]
>Atualize as políticas fornecidas para relacionar-se ao seu locatário específico.

## <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. Abra o locatário Azure AD B2C e, em políticas, selecione **fluxos de usuário**.

2. Selecione o fluxo de **usuário** criado anteriormente.

3. Selecione **executar fluxo de usuário** e selecione as configurações:

   a. **Aplicativo**: selecione o aplicativo registrado (exemplo é JWT)

   b. **URL de resposta**: selecione a **URL de redirecionamento**

   c. Selecione **Executar fluxo de usuário**.

4. Percorrer o fluxo de inscrição e criar uma conta

5. Fazer logoff

6. Percorrer o fluxo de entrada  

7. O quebra-cabeça ThreatMetrix será exibido depois que você entrar em **continuar**.

## <a name="next-steps"></a>Próximas etapas

Para obter informações adicionais, examine os seguintes artigos:

- [Políticas personalizadas no Azure AD B2C](./custom-policy-overview.md)

- [Introdução às políticas personalizadas no Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
