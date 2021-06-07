---
title: Use o Key Vault para armazenar e acessar chaves do Azure Cosmos DB
description: Use o Azure Key Vault para armazenar e acessar a cadeia de conexão, chaves e pontos de extremidade do Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: how-to
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: a9bea0664f99a21ac734de666c802e9875ff00b5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359312"
---
# <a name="secure-azure-cosmos-keys-using-azure-key-vault"></a>Proteger chaves do Azure Cosmos usando o Azure Key Vault 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

>[!IMPORTANT]
> A solução recomendada para acessar Azure Cosmos DB chaves é usar uma [identidade gerenciada atribuída pelo sistema](managed-identity-based-authentication.md). Se o serviço não puder tirar proveito das identidades gerenciadas, use a [solução baseada em certificado](certificate-based-authentication.md). Se a solução de identidade gerenciada e a solução baseada em certificado não atenderem às suas necessidades, use a solução Key Vault abaixo.

Ao usar Azure Cosmos DB para seus aplicativos, você pode acessar o banco de dados, coleções, documentos usando o ponto de extremidade e a chave dentro do arquivo de configuração do aplicativo.  No entanto, não é seguro colocar as chaves e a URL diretamente no código do aplicativo porque elas estão disponíveis no formato de texto não criptografado para todos os usuários. Você deseja verificar se o ponto de extremidade e as chaves estão disponíveis, mas por meio de um mecanismo seguro. É aí que o Azure Key Vault pode ajudá-lo a armazenar e gerenciar os segredos do aplicativo com segurança.

As etapas a seguir são necessárias para armazenar e ler as chaves de acesso do Azure Cosmos DB usando o Key Vault:

* Criar um cofre de chaves  
* Adicionar chaves de acesso do Azure Cosmos DB no Key Vault  
* Criar um aplicativo Web do Azure  
* Registrar o aplicativo e conceder permissões para ler o Key Vault  


## <a name="create-a-key-vault"></a>Criar um cofre de chaves

1. Entre no [Portal do Azure](https://portal.azure.com/).  
2. Selecione **Criar um recurso > Segurança > Key Vault**.  
3. A seção **Criar cofre de chaves** fornece as seguintes informações:  
   * **Nome:** forneça um nome exclusivo para seu Key Vault.  
   * **Assinatura:** escolha a assinatura que você usará.  
   * Em **grupo de recursos** , escolha **criar novo** e insira um nome de grupo de recursos.  
   * No menu suspenso Local, escolha um local.  
   * Deixe as outras opções com os padrões.  
4. Depois de fornecer as informações acima, selecione **Criar**.  

## <a name="add-azure-cosmos-db-access-keys-to-the-key-vault"></a>Adicione as chaves de acesso do Azure Cosmos DB ao Key Vault.
1. Navegue até o Key Vault que você criou na etapa anterior e abra a guia **Segredos**.  
2. Selecione **+ gerar/importar**, 

   * Selecione **manual** para **Opções de upload**.
   * Forneça um **Nome** para seu segredo
   * Insira a cadeia de conexão da sua conta do Cosmos DB no campo **Valor**. Em seguida, selecione **Criar**.

   :::image type="content" source="./media/access-secrets-from-keyvault/create-a-secret.png" alt-text="Criar um segredo":::

4. Depois que o segredo for criado, abra-o e copie o **Identificador do Segredo que está no formato a seguir. Você usará esse identificador na próxima seção. 

   `https://<Key_Vault_Name>.vault.azure.net/secrets/<Secret _Name>/<ID>`

## <a name="create-an-azure-web-application"></a>Criar um aplicativo Web do Azure

1. Crie um aplicativo Web do Azure ou baixe o aplicativo do [Repositório do GitHub](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/Demo/keyvaultdemo). Trata-se de um aplicativo MVC simples.  

2. Descompacte o aplicativo baixado e abra o arquivo **HomeController.cs**. Atualize a ID do segredo na linha a seguir:

   `var secret = await keyVaultClient.GetSecretAsync("<Your Key Vault’s secret identifier>")`

3. **Salve** o arquivo **Crie** a solução.  
4. Em seguida, implante o aplicativo no Azure. Clique com o botão direito do mouse no projeto e escolha **Publicar**. Crie um perfil do Serviço de Aplicativo (você pode nomear o aplicativo como WebAppKeyVault1) e selecione **Publicar**.   

5. Depois que o aplicativo for implantado. No portal do Azure, navegue até o aplicativo Web que você implantou e habilite a **Identidade de Serviço Gerenciada** desse aplicativo.  

   :::image type="content" source="./media/access-secrets-from-keyvault/turn-on-managed-service-identity.png" alt-text="Identidade de Serviço Gerenciada":::

Se você for executar o aplicativo agora, ocorrerá o seguinte erro, pois você ainda não concedeu nenhuma permissão para esse aplicativo no Key Vault.

:::image type="content" source="./media/access-secrets-from-keyvault/app-deployed-without-access.png" alt-text="Aplicativo implantado sem acesso":::

## <a name="register-the-application--grant-permissions-to-read-the-key-vault"></a>Registrar o aplicativo e conceder permissões para ler o Key Vault

Nesta seção, você registrará o aplicativo no Azure Active Directory e concederá permissões para o aplicativo ler o Key Vault. 

1. Navegue até o portal do Azure e abra o **Key Vault** criado na seção anterior.  

2. Abra **Políticas de acesso**, selecione **+Adicionar Novo**, localize o aplicativo Web implantado, escolha as permissões e selecione **OK**.  

   :::image type="content" source="./media/access-secrets-from-keyvault/add-access-policy.png" alt-text="Adicionar política de acesso":::

Agora, ao executar o aplicativo, você poderá ler o segredo do Key Vault.

:::image type="content" source="./media/access-secrets-from-keyvault/app-deployed-with-access.png" alt-text="Aplicativo implantado com o segredo":::
 
Da mesma forma, você pode adicionar um usuário para acessar o Key Vault. Você precisa adicionar você mesmo ao Key Vault selecionando **Políticas de Acesso**. Em seguida, conceda todas as permissões que você precisa para executar o aplicativo usando o Visual Studio. Quando esse aplicativo estiver em execução na área de trabalho, ele usará sua identidade.

## <a name="next-steps"></a>Próximas etapas

* Para configurar um firewall para Azure Cosmos DB consulte o artigo de [suporte do firewall](how-to-configure-firewall.md) .
* Para configurar o ponto de extremidade de serviço de rede virtual, confira o artigo [Proteger o acesso usando o ponto de extremidade de serviço da VNET](how-to-configure-vnet-service-endpoint.md).
