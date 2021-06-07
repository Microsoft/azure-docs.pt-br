---
title: Usar chaves gerenciadas pelo cliente ou a API REST BYOK
description: Neste tutorial, use chaves gerenciadas pelo cliente ou BYOK (Bring Your Own Key) com uma conta de armazenamento dos Serviços de Mídia do Azure.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: c8a5b682e2ac4879d2181bdb069cf554bad512d9
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281069"
---
# <a name="tutorial-use-customer-managed-keys-or-byok-with-media-services-rest-api"></a>Tutorial: Usar chaves gerenciadas pelo cliente ou BYOK com a API REST dos Serviços de Mídia

Com a API 2020-05-01, você pode usar uma chave RSA gerenciada pelo cliente com uma conta dos Serviços de Mídia do Azure que tenha uma identidade gerenciada pelo sistema. Este tutorial inclui uma coleção e um ambiente do Postman para enviar solicitações REST aos serviços do Azure. Os serviços usados são:

- Registro de aplicativo do Azure AD (Azure Active Directory) para Postman
- API do Microsoft Graph
- Armazenamento do Azure
- Cofre de Chave do Azure
- Serviços de Mídia do Azure

Neste tutorial, você aprenderá a usar o Postman para:

> [!div class="checklist"]
> - Obter tokens para uso com os serviços do Azure.
> - Criar um grupo de recursos e uma conta de armazenamento.
> - Criar uma conta dos Serviços de Mídia com uma identidade gerenciada pelo sistema.
> - Crie um cofre de chaves para armazenar uma chave RSA gerenciada pelo cliente.
> - Atualizar a conta dos Serviços de Mídia para usar a chave RSA com a conta de armazenamento.
> - Usar variáveis no Postman.

Se você não tiver uma assinatura do Azure, [crie uma conta de avaliação gratuita](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

1. Registrar uma entidade de serviço com as permissões adequadas.
1. Instalar o [Postman](https://www.postman.com).
1. Baixe a coleção do Postman para este tutorial em [Exemplos do Azure: media-services-customer-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok).

### <a name="register-a-service-principal-with-the-needed-permissions"></a>Registrar uma entidade de serviço com as permissões necessárias

1. [Crie uma entidade de serviço](../../active-directory/develop/howto-create-service-principal-portal.md).
1. Vá para a [Opção 2: Criar um segredo do aplicativo](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options) para obter o segredo da entidade de serviço.

   > [!IMPORTANT]
   >Copie e salve o segredo para uso posterior. Você não poderá acessar o segredo depois de sair da página do segredo no portal.

1. Atribua permissões à entidade de serviço, conforme mostrado na seguinte captura de tela:

   :::image type="complex" source="./media/tutorial-byok/service-principal-permissions-1.png" alt-text="Captura de tela mostrando as permissões necessárias para a entidade de serviço.":::
   As permissões são listadas por serviço, nome da permissão, tipo e descrição. Azure Key Vault: representação do usuário, delegada, acesso completo ao Azure Key Vault. Gerenciamento de Serviços do Azure: representação do usuário, delegada, acesso ao Gerenciamento de Serviços do Azure como usuário da organização. Armazenamento do Azure: representação do usuário, delegada, acesso ao Armazenamento do Azure. Serviços de mídia: representação do usuário, delegada, acesso aos serviços de mídia. Microsoft Graph: user.read, delegada, entrada no perfil do usuário e leitura.
   :::image-end:::

### <a name="install-postman"></a>Instale o Postman

Se você ainda não instalou o Postman para uso com o Azure, pode obtê-lo em [postman.com](https://www.postman.com/).

### <a name="download-and-import-the-collection"></a>Baixar e importar a coleção

Baixe a coleção do Postman para este tutorial em [Exemplos do Azure: media-services-customer-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok).

## <a name="install-the-postman-collection-and-environment"></a>Instalar a coleção e o ambiente do Postman

1. Execute o Postman.
1. Selecione **Importar**.
1. Selecione **Carregar arquivos**.
1. Vá até onde você salvou a coleção e os arquivos de ambiente.
1. Selecione os arquivos de coleção e ambiente.
1. Selecione **Abrir**. Você verá um aviso de que os arquivos não serão importados como uma API, mas como coleções. Esse aviso não representa um problema. É o que você deseja.

Essa coleção agora aparece em suas coleções como BYOK. Além disso, as variáveis de ambiente são exibidas agora em seus ambientes.

### <a name="understand-the-rest-api-requests-in-the-collection"></a>Entender as solicitações da API REST na coleção

A coleção fornece as solicitações de API REST a seguir.

> [!NOTE]
>
>- As solicitações precisam ser enviadas na sequência fornecida.
>- A maioria das solicitações tem scripts de teste que criam dinamicamente variáveis globais para a próxima solicitação na sequência.
>- Você não precisa criar variáveis globais manualmente.

No Postman, você verá essas variáveis contidas dentro de colchetes. Por exemplo, `{{bearerToken}}`.

1. Obter um token do Azure AD: o teste define a variável global **bearerToken**.
2. Obter um token do Microsoft Graph: o teste define a variável global **graphToken**.
3. Obter os detalhes da entidade de serviço: o teste define a variável global **servicePrincipalObjectId**.
4. Criar uma conta de armazenamento: o teste define a variável global **storageAccountId**.
5. Criar uma conta dos Serviços de Mídia com uma identidade gerenciada pelo sistema: o teste define a variável global **principalId**.
6. Criar um cofre de chaves para permitir acesso à entidade de serviço: o teste define a variável global **keyVaultId**.
7. Obter um token do Key Vault: o teste define a variável global **keyVaultToken**.
8. Criar a chave RSA no cofre de chaves: o teste define a variável global **keyId**.
9. Atualizar a conta dos Serviços de Mídia para usar a chave com a conta de armazenamento: não há nenhum script de teste para essa solicitação.

## <a name="define-environment-variables"></a>Definir variáveis de ambiente

1. Selecione a lista suspensa do ambiente para fazer a alternância para o ambiente que você baixou.
1. Estabeleça suas variáveis de ambiente no Postman. Elas também são usadas como variáveis contidas dentro de colchetes. Por exemplo, `{{tenantId}}`.

    - **tenantId**: ID do locatário.
    - **servicePrincipalId**: a ID da entidade de serviço que você estabelece com seu método favorito, como o portal ou a CLI.
    - **servicePrincipalSecret**: o segredo criado para a entidade de serviço.
    - **Assinatura**: A ID de sua assinatura.
    - **storageName**: o nome que você deseja dar ao seu armazenamento.
    - **accountName**: o nome da conta dos Serviços de Mídia que você deseja usar.
    - **keyVaultName**: o nome do cofre de chaves que você deseja usar.
    - **resourceLocation**: a localização **CentralUs** ou onde você deseja colocar seus recursos. Essa coleção só foi testada com **CentralUs**.
    - **resourceGroup**: O nome do grupo de recursos.

    As variáveis a seguir são o padrão para trabalhar com recursos do Azure. Portanto, não há necessidade de alterá-las.

    - **armResource**: `https://management.core.windows.net`
    - **graphResource**: `https://graph.windows.net/`
    - **keyVaultResource**: `https://vault.azure.net`
    - **armEndpoint**: `management.azure.com`
    - **graphEndpoint**: `graph.windows.net`
    - **aadEndpoint**: `login.microsoftonline.com`
    - **keyVaultDomainSuffix**: `vault.azure.net`

## <a name="send-the-requests"></a>Enviar as solicitações

Depois de definir as variáveis de ambiente, você poderá executar as solicitações uma de cada vez na sequência anterior. Ou você poderá usar o executor do Postman para executar a coleção.

## <a name="change-the-key"></a>Alterar a chave

Os Serviços de Mídia detectam automaticamente quando a chave é alterada. Para testar esse processo, crie outra versão de chave para a mesma chave. Os Serviços de Mídia devem detectar a chave em menos de 15 minutos.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não pretender continuar usando os recursos que criou e *não quiser continuar sendo cobrado*, exclua-os.

## <a name="next-steps"></a>Próximas etapas

Vá para o próximo artigo para saber como:
> [!div class="nextstepaction"]
> [Codificar um arquivo remoto baseado em URL e transmitir o vídeo com o REST](stream-files-tutorial-with-rest.md)