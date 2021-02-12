---
title: Tutorial para configurar o Azure Active Directory B2C com o Onfido
titleSuffix: Azure AD B2C
description: Saiba como integrar Azure AD B2C autenticação com Onfido para a ID do documento e verificação de biometria facial
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/03/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 46c6eac80ddbff73d99e05c070e66aa1700da174
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928623"
---
# <a name="tutorial-for-configuring-onfido-with-azure-active-directory-b2c"></a>Tutorial para configurar o Onfido com o Azure Active Directory B2C

Neste tutorial de exemplo, fornecemos orientações sobre como integrar o Azure AD B2C com o [Onfido](https://onfido.com/). Onfido é uma ID de documento e um aplicativo de verificação de biometria facial. Ele permite que as empresas atendam aos requisitos de *clientes* e de identidade em tempo real. O Onfido usa a verificação de identidade baseada em ia sofisticada, que primeiro verifica uma ID de foto e a compara com a biometria facial. Essa solução vincula uma identidade digital à sua pessoa do mundo real e fornece uma experiência de integração segura ao mesmo tempo que reduz a fraude.

Neste exemplo, conectamos o serviço do Onfido no fluxo de inscrição ou de logon para fazer a verificação de identidade. Decisões informadas sobre qual produto e serviço o usuário pode acessar é feita com base nos resultados de Onfido.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, você precisará de:

- Uma assinatura do Azure AD. Caso você não tenha uma assinatura, obtenha uma [conta gratuita](https://azure.microsoft.com/free/).

- [Um locatário Azure ad B2C](./tutorial-create-tenant.md) que está vinculado à sua assinatura do Azure.

- Uma [conta de avaliação](https://onfido.com/signup/)do Onfido.

## <a name="scenario-description"></a>Descrição do cenário

A integração do Onfido inclui os seguintes componentes:

- Azure AD B2C locatário – o servidor de autorização, responsável por verificar as credenciais do usuário com base nas políticas personalizadas definidas no locatário. Ele também é conhecido como provedor de identidade. Ele hospeda o aplicativo cliente Onfido, que coleta os documentos do usuário e os transmite para o serviço de API do Onfido.

- Cliente Onfido – um utilitário de coleção de documentos de cliente JavaScript configurável implantado em outras páginas da Web. Coleta os documentos e faz verificações preliminares como o tamanho e a qualidade do documento.

- API REST intermediária – fornece pontos de extremidade para o locatário de Azure AD B2C se comunicar com o serviço de API do Onfido, manipular o processamento de dados e aderir aos requisitos de segurança de ambos.

- Serviço de API Onfido – o serviço de back-end fornecido pelo Onfido, que salva e verifica os documentos fornecidos pelo usuário.

O diagrama de arquitetura a seguir mostra a implementação.

![captura de tela para onfido-arquitetura-diagrama](media/partner-onfido/onfido-architecture-diagram.png)

|Etapa | Descrição |
|:-----| :-----------|
| 1. | O usuário chega em uma página de logon. O usuário se inscreve para criar uma nova conta e insere informações na página. Azure AD B2C coleta os atributos de usuário. O aplicativo cliente Onfido hospedado no Azure AD B2C o preliminar verifica as informações do usuário.
| 2. | Azure AD B2C chama a API de camada intermediária e passa os atributos de usuário.
| 3. | A API de camada intermediária coleta atributos de usuário e os transforma em um formato que a API Onfido pode consumir. Em seguida, envia-o para Onfido.
| 4. | O Onfido consome as informações e processa-a para validar a identificação do usuário. Em seguida, ele retorna o resultado para a API da camada intermediária.
| 5. | A API de camada intermediária processa as informações e envia informações relevantes de volta no formato JSON correto para Azure AD B2C.
| 6. | Azure AD B2C recebe informações de volta da API de camada intermediária. Se ele mostrar uma resposta de falha, uma mensagem de erro será exibida para o usuário. Se ele mostrar uma resposta de êxito, o usuário será autenticado e gravado no diretório.

## <a name="onboard-with-onfido"></a>Integração com o Onfido

1. Para criar uma conta do Onfido, entre em contato com [Onfido](https://onfido.com/signup/).

2. Depois que uma conta for criada, crie uma [chave de API](https://documentation.onfido.com/). As chaves dinâmicas são faturáveis, no entanto, você pode usar as [chaves de área restrita para testar](https://documentation.onfido.com/?javascript#sandbox-and-live-differences) a solução. As chaves de área restrita produzem a mesma estrutura de resultado que as chaves dinâmicas, no entanto, os resultados são sempre predeterminados. Os documentos não são processados ou salvos.

>[!NOTE]
> Você precisará da chave mais tarde.

Para obter mais informações sobre Onfido, consulte [documentação da API do Onfido](https://documentation.onfido.com) e Hub do [desenvolvedor Onfido](https://developers.onfido.com).  

## <a name="configure-azure-ad-b2c-with-onfido"></a>Configurar Azure AD B2C com Onfido

### <a name="part-1---deploy-the-api"></a>Parte 1-implantar a API

- Implante o [código de API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/API/Onfido.Api) fornecido em um serviço do Azure. O código pode ser publicado no Visual Studio, seguindo estas [instruções](/visualstudio/deployment/quickstart-deploy-to-azure).
- Configurar CORS, adicionar **origem permitida** como https://{your_tenant_name}. b2clogin. com

>[!NOTE]
>Você precisará da URL do serviço implantado para configurar o Azure AD com as configurações necessárias.

#### <a name="adding-sensitive-configuration-settings"></a>Adicionando definições de configuração confidenciais

As configurações do aplicativo podem ser configuradas no [serviço de aplicativo no Azure](../app-service/configure-common.md#configure-app-settings). O serviço de aplicativo permite que as configurações sejam configuradas com segurança sem verificá-las em um repositório. A API REST precisa das seguintes configurações:

| Nome da configuração de aplicativo | Fonte | Observações |
|:-------------------------|:-------|:-------|
|OnfidoSettings: AuthToken| Conta do Onfido |

### <a name="part-2---deploy-the-ui"></a>Parte 2-implantar a interface do usuário

#### <a name="configure-your-storage-location"></a>Configurar o local de armazenamento

1. Configurar um [contêiner de armazenamento de BLOBs em sua conta de armazenamento](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)

2. Armazene os arquivos de interface do usuário da [pasta de interface do usuário](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/UI) em seu contêiner de BLOB.

3. Permita o acesso CORS ao contêiner de armazenamento criado seguindo estas instruções:

   a. Vá para **configurações**  > **origem permitida**, insira `https://{your_tenant_name}.b2clogin.com` . Substitua seu-Tenant-Name pelo nome do seu locatário de Azure AD B2C. Por exemplo, https://fabrikam.b2clogin.com . Use todas as letras minúsculas ao inserir o nome do locatário.

   b. Para **métodos permitidos**, selecione `GET` e `PUT` .

   c. Selecione **Salvar**.

#### <a name="update-ui-files"></a>Atualizar arquivos da interface do usuário

1. Nos arquivos da interface do usuário, vá para a pasta [ **ocean_blue**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/UI/ocean_blue)

2. Abra cada arquivo HTML.

3. Localize e substitua {Your-interface-blob-container-URL} pela URL de onde suas pastas de interface do usuário **ocean_blue**, **dist** e **ativos** estão localizadas

4. Localize e substitua {Your-intermediário-API-URL} pela URL do serviço de aplicativo de API intermediário.

#### <a name="upload-your-files"></a>Carregar seus arquivos

1. Armazene os arquivos de interface do usuário da pasta de interface do usuário em seu contêiner de BLOB.

2. Use [Gerenciador de armazenamento do Azure](../virtual-machines/disks-use-storage-explorer-managed-disks.md) para gerenciar seus arquivos e permissões de acesso.

### <a name="part-3---configure-azure-ad-b2c"></a>Parte 3-configurar Azure AD B2C

#### <a name="replace-the-configuration-values"></a>Substituir os valores de configuração

Nas [políticas personalizadas](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/Policies)fornecidas, localize os seguintes espaços reservados e substitua pelos valores correspondentes da sua instância.

| Espaço reservado | Substituir pelo valor | Exemplo  |
|:---------------|:----------------|:-------------------|
| {your_tenant_name}  | Seu nome curto do locatário |  "seulocatario" de yourtenant.onmicrosoft.com |
| {your_tenantID} | Tenantid do seu locatário de Azure AD B2C | 01234567-89ab-cdef-0123-456789ABCDEF           |
| {your_tenant_IdentityExperienceFramework_appid}        | ID do aplicativo IdentityExperienceFramework configurada em seu locatário de Azure AD B2C      | 01234567-89ab-cdef-0123-456789ABCDEF         |
| {your_tenant_ ProxyIdentityExperienceFramework _appid} | ID do aplicativo ProxyIdentityExperienceFramework configurada em seu locatário de Azure AD B2C | 01234567-89ab-cdef-0123-456789ABCDEF         |
| {your_tenant_extensions_appid}                         | ID de aplicativo do aplicativo de armazenamento do seu locatário                                      | 01234567-89ab-cdef-0123-456789ABCDEF         |
| {your_tenant_extensions_app_objectid}                  | ID de objeto do aplicativo de armazenamento do locatário                                   | 01234567-89ab-cdef-0123-456789ABCDEF         |
| {your_app_insights_instrumentation_key} | Chave de instrumentação de sua instância do App insights *| 01234567-89ab-cdef-0123-456789ABCDEF|
|{your_ui_file_base_url}| URL do local em que suas pastas de interface do usuário **ocean_blue**, **dist** e **ativos** estão localizadas | https://yourstorage.blob.core.windows.net/UI/|
| {your_app_service_URL}                                 | URL do serviço de aplicativo que você configurou                                             | `https://yourapp.azurewebsites.net`          |

* O app insights pode estar em um locatário diferente. Esta etapa é opcional. Remova os TechnicalProfiles e OrchestrationSteps correspondentes se não forem necessários.

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>Parte 4-configurar a política de Azure AD B2C

Consulte este [documento](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) para obter instruções sobre como configurar seu locatário Azure ad B2C e configurar políticas.

>[!NOTE]
> Como prática recomendada, recomendamos que os clientes adicionem notificação de autorização na página coleção de atributos. Notifique os usuários que as informações serão enviadas a serviços de terceiros para verificação de identidade.

## <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. Abra o locatário Azure AD B2C e, em políticas, selecione **estrutura de experiência de identidade**.

2. Selecione o **SignUpSignIn** criado anteriormente.

3. Selecione **executar fluxo de usuário** e selecione as configurações:

   a. **Aplicativo**: selecione o aplicativo registrado (exemplo é JWT)

   b. **URL de resposta**: selecione a **URL de redirecionamento**

   c. Selecione **Executar fluxo de usuário**.

4. Percorrer o fluxo de inscrição e criar uma conta

5. O serviço Onfido será chamado durante o fluxo, depois que o atributo de usuário for criado. Se o fluxo estiver incompleto, verifique se o usuário não está salvo no diretório.

## <a name="next-steps"></a>Próximas etapas

Para obter informações adicionais, examine os seguintes artigos:

- [Políticas personalizadas no Azure AD B2C](./custom-policy-overview.md)

- [Introdução às políticas personalizadas no Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
