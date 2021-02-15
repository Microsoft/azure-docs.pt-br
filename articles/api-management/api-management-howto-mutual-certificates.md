---
title: Proteger o back-end de gerenciamento de API usando autenticação de certificado do cliente
titleSuffix: Azure API Management
description: Saiba como gerenciar certificados de cliente e serviços de back-end seguros usando a autenticação de certificado de cliente no gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 01/26/2021
ms.author: apimpm
ms.openlocfilehash: 2e4a398ab71878134887fb8fba025cd8aa6122ad
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492799"
---
# <a name="secure-backend-services-using-client-certificate-authentication-in-azure-api-management"></a>Proteger serviços de back-end usando a autenticação de certificado do cliente no gerenciamento de API do Azure

O gerenciamento de API permite que você proteja o acesso ao serviço de back-end de uma API usando certificados de cliente. Este guia mostra como gerenciar certificados em uma instância do serviço de gerenciamento de API do Azure usando o portal do Azure. Ele também explica como configurar uma API para usar um certificado para acessar um serviço de back-end.

Você também pode gerenciar certificados de gerenciamento de API usando a [API REST de gerenciamento de API](/rest/api/apimanagement/2020-06-01-preview/certificate).

## <a name="certificate-options"></a>Opções de certificado

O gerenciamento de API fornece duas opções para gerenciar certificados usados para proteger o acesso aos serviços de back-end:

* Fazer referência a um certificado gerenciado no [Azure Key Vault](../key-vault/general/overview.md) 
* Adicionar um arquivo de certificado diretamente no gerenciamento de API

O uso de certificados do Key Vault é recomendado porque ajuda a melhorar a segurança do gerenciamento de API:

* Os certificados armazenados em cofres de chaves podem ser reutilizados em todos os serviços
* [Políticas de acesso](../key-vault/general/secure-your-key-vault.md#data-plane-and-access-policies) granular podem ser aplicadas a certificados armazenados em cofres de chaves
* Os certificados atualizados no cofre de chaves são automaticamente girados no gerenciamento de API. Após a atualização no cofre de chaves, um certificado no gerenciamento de API é atualizado dentro de 4 horas. Você também pode atualizar manualmente o certificado usando a portal do Azure ou por meio da API REST de gerenciamento.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Se você ainda não criou uma instância de serviço de gerenciamento de API, consulte [criar uma instância de serviço de gerenciamento de API][Create an API Management service instance].
* Você deve ter seu serviço de back-end configurado para autenticação de certificado de cliente. Para configurar a autenticação de certificado no serviço de Azure App, consulte [Este artigo][to configure certificate authentication in Azure WebSites refer to this article]. 
* Você precisa ter acesso ao certificado e à senha para gerenciamento em um cofre de chaves do Azure ou carregar para o serviço de gerenciamento de API. O certificado deve estar no formato **pfx** . Os certificados autoassinados são permitidos.

### <a name="prerequisites-for-key-vault-integration"></a>Pré-requisitos para a integração do Key Vault

1. Para obter as etapas para criar um cofre de chaves, consulte [início rápido: criar um cofre de chaves usando o portal do Azure](../key-vault/general/quick-create-portal.md).
1. Habilite uma [identidade gerenciada](api-management-howto-use-managed-service-identity.md) atribuída pelo sistema ou atribuída pelo usuário na instância do gerenciamento de API.
1. Atribua uma [política de acesso do cofre de chaves](../key-vault/general/assign-access-policy-portal.md) à identidade gerenciada com permissões para obter e listar certificados do cofre. Para adicionar a política:
    1. No portal, navegue até o cofre de chaves.
    1. Selecione **configurações > políticas de acesso > + adicionar política de acesso**.
    1. Selecione **permissões de certificado** e, em seguida, selecione **obter** e **listar**.
    1. Em **selecionar entidade de segurança**, selecione o nome do recurso da sua identidade gerenciada. Se você estiver usando uma identidade atribuída pelo sistema, o principal será o nome da sua instância de gerenciamento de API.
1. Crie ou importe um certificado para o cofre de chaves. Consulte [início rápido: definir e recuperar um certificado de Azure Key Vault usando o portal do Azure](../key-vault/certificates/quick-create-portal.md).

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-a-key-vault-certificate"></a>Adicionar um certificado do Key Vault

Consulte [pré-requisitos para a integração do Key Vault](#prerequisites-for-key-vault-integration).

> [!CAUTION]
> Ao usar um certificado do Key Vault no gerenciamento de API, tenha cuidado para não excluir o certificado, o cofre de chaves ou a identidade gerenciada usada para acessar o cofre de chaves.

Para adicionar um certificado do Key Vault ao gerenciamento de API:

1. No [portal do Azure](https://portal.azure.com), navegue até a instância do Gerenciamento de API.
1. Em **segurança**, selecione **certificados**.
1. Selecione **certificados**  >  **+ Adicionar**.
1. Em **ID**, insira um nome de sua escolha.
1. Em **certificado**, selecione **cofre de chaves**.
1. Insira o identificador de um certificado do Key Vault ou escolha **selecionar** para selecionar um certificado de um cofre de chaves.
    > [!IMPORTANT]
    > Se você mesmo inserir um identificador de certificado do cofre de chaves, verifique se ele não tem informações de versão. Caso contrário, o certificado não será girado automaticamente no gerenciamento de API após uma atualização no cofre de chaves.
1. Em **identidade do cliente**, selecione uma identidade gerenciada atribuída pelo usuário ou uma existente. Saiba como [Adicionar ou modificar identidades gerenciadas em seu serviço de gerenciamento de API](api-management-howto-use-managed-service-identity.md).
    > [!NOTE]
    > A identidade precisa de permissões para obter e listar o certificado do cofre de chaves. Se você ainda não tiver configurado o acesso ao cofre de chaves, o gerenciamento de API solicitará que ele possa configurar automaticamente a identidade com as permissões necessárias.
1. Selecione **Adicionar**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-kv.png" alt-text="Adicionar certificado do Key Vault":::

## <a name="upload-a-certificate"></a>Carregar um certificado

Para carregar um certificado de cliente para o gerenciamento de API: 

1. No [portal do Azure](https://portal.azure.com), navegue até a instância do Gerenciamento de API.
1. Em **segurança**, selecione **certificados**.
1. Selecione **certificados**  >  **+ Adicionar**.
1. Em **ID**, insira um nome de sua escolha.
1. Em **certificado**, selecione **personalizado**.
1. Navegue para selecionar o arquivo de certificado. pfx e digite sua senha.
1. Selecione **Adicionar**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-add.png" alt-text="Carregar certificado do cliente":::

Depois que o certificado for carregado, ele será exibido na janela **certificados** . Se você tiver muitos certificados, anote a impressão digital do certificado desejado para configurar uma API para usar um certificado de cliente para [autenticação de gateway](#configure-an-api-to-use-client-certificate-for-gateway-authentication).

> [!NOTE]
> Para desativar a validação da cadeia de certificados ao usar, por exemplo, um certificado autoassinado, siga as etapas descritas em [certificados autoassinados](#self-signed-certificates), posteriormente neste artigo.

## <a name="configure-an-api-to-use-client-certificate-for-gateway-authentication"></a>Configurar uma API para usar o certificado do cliente para autenticação de gateway

1. No [portal do Azure](https://portal.azure.com), navegue até a instância do Gerenciamento de API.
1. Em **APIs**, selecione **APIs**.
1. Selecione uma API na lista. 
2. Na guia **design** , selecione o ícone do editor na seção de **back-end** .
3. Em **credenciais de gateway**, selecione certificado de **cliente** e selecione o certificado no menu suspenso.
1. Selecione **Save**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png" alt-text="Usar certificado de cliente para autenticação de gateway":::

> [!CAUTION]
> Essa alteração entra em vigor imediatamente e as chamadas para as operações dessa API usarão o certificado para autenticar no servidor de back-end.

> [!TIP]
> Quando um certificado é especificado para autenticação de gateway para o serviço de back-end de uma API, ele se torna parte da política para essa API e pode ser exibido no editor de políticas.

## <a name="self-signed-certificates"></a>Certificados autoassinados

Se você estiver usando certificados autoassinados, será necessário desabilitar a validação da cadeia de certificados para o gerenciamento de API se comunicar com o sistema de back-end. Caso contrário, retornará um código de erro 500. Para configurar isso, você pode usar os [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) cmdlets do PowerShell (para novo back-end) ou [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) (para back-end existente) e definir o `-SkipCertificateChainValidation` parâmetro como `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

## <a name="delete-a-client-certificate"></a>Excluir um certificado do cliente

Para excluir um certificado, selecione-o e, em seguida, selecione **excluir** no menu de contexto (**...**).

:::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png" alt-text="Excluir um certificado":::

> [!IMPORTANT]
> Se o certificado for referenciado por qualquer política, uma tela de aviso será exibida. Para excluir o certificado, primeiro você deve remover o certificado de todas as políticas que estão configuradas para usá-lo.

## <a name="next-steps"></a>Próximas etapas

* [Como proteger APIs usando a autenticação de certificado do cliente no Gerenciamento de API](api-management-howto-mutual-certificates-for-clients.md)
* Saiba mais sobre [as políticas no gerenciamento de API](api-management-howto-policies.md)


[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: ./api-management-caching-policies.md
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

