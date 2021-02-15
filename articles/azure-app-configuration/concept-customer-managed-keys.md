---
title: Usar chaves gerenciadas pelo cliente para criptografar seus dados de configuração
description: Criptografar seus dados de configuração usando chaves gerenciadas pelo cliente
author: AlexandraKemperMS
ms.author: alkemper
ms.date: 07/28/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 46a1e99a7bc75efa85fcb5eff649c14af5abd17b
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930493"
---
# <a name="use-customer-managed-keys-to-encrypt-your-app-configuration-data"></a>Usar chaves gerenciadas pelo cliente para criptografar os dados de configuração do aplicativo
Azure App configuração [criptografa informações confidenciais em repouso](../security/fundamentals/encryption-atrest.md). O uso de chaves gerenciadas pelo cliente fornece proteção de dados aprimorada, permitindo que você gerencie suas chaves de criptografia.  Quando a criptografia de chave gerenciada é usada, todas as informações confidenciais na configuração do aplicativo são criptografadas com uma chave de Azure Key Vault fornecida pelo usuário.  Isso fornece a capacidade de girar a chave de criptografia sob demanda.  Ele também fornece a capacidade de revogar o acesso de Azure App configuração a informações confidenciais revogando o acesso da instância de configuração de aplicativo à chave.

## <a name="overview"></a>Visão geral 
Azure App configuração criptografa informações confidenciais em repouso usando uma chave de criptografia AES de 256 bits fornecida pela Microsoft. Cada instância de configuração de aplicativo tem sua própria chave de criptografia gerenciada pelo serviço e usada para criptografar informações confidenciais. Informações confidenciais incluem os valores encontrados em pares chave-valor.  Quando a capacidade de chave gerenciada pelo cliente está habilitada, a configuração de aplicativo usa uma identidade gerenciada atribuída à instância de configuração de aplicativo para autenticar com Azure Active Directory. Em seguida, a identidade gerenciada chama Azure Key Vault e encapsula a chave de criptografia da instância de configuração do aplicativo. Em seguida, a chave de criptografia encapsulada é armazenada e a chave de criptografia desencapsulada é armazenada em cache na configuração do aplicativo por uma hora. A configuração do aplicativo atualiza a versão desencapsulada da chave de criptografia da instância de configuração de aplicativo por hora. Isso garante a disponibilidade em condições normais de operação. 

>[!IMPORTANT]
> Se a identidade atribuída à instância de configuração do aplicativo não estiver mais autorizada a desencapsular a chave de criptografia da instância ou se a chave gerenciada for excluída permanentemente, não será mais possível descriptografar informações confidenciais armazenadas na instância de configuração do aplicativo. Usar a função de [exclusão reversível](../key-vault/general/soft-delete-overview.md) do Azure Key Vault reduz a chance de excluir acidentalmente sua chave de criptografia.

Quando os usuários habilitam a capacidade de chave gerenciada pelo cliente em sua instância de configuração do Azure App, eles controlam a capacidade do serviço de acessar suas informações confidenciais. A chave gerenciada serve como uma chave de criptografia raiz. Um usuário pode revogar o acesso da sua instância de configuração de aplicativo à chave gerenciada alterando a política de acesso do cofre de chaves. Quando esse acesso for revogado, a configuração do aplicativo perderá a capacidade de descriptografar os dados do usuário em uma hora. Neste ponto, a instância de configuração do aplicativo proibirá todas as tentativas de acesso. Essa situação é recuperável, concedendo ao serviço acesso à chave gerenciada novamente.  Dentro de uma hora, a configuração do aplicativo poderá descriptografar os dados do usuário e operar em condições normais.

>[!NOTE]
>Todos os Azure App dados de configuração são armazenados por até 24 horas em um backup isolado. Isso inclui a chave de criptografia não encapsulada. Esses dados não estão imediatamente disponíveis para a equipe de serviço ou serviço. No caso de uma restauração de emergência, a configuração do Azure App será revogada novamente a partir dos dados de chave gerenciados.

## <a name="requirements"></a>Requisitos
Os seguintes componentes são necessários para habilitar com êxito a capacidade de chave gerenciada pelo cliente para Azure App configuração:
- Camada Standard Azure App instância de configuração
- Azure Key Vault com recursos de exclusão reversível e de proteção de limpeza habilitados
- Uma chave RSA ou RSA-HSM dentro do Key Vault
    - A chave não deve expirar, deve ser habilitada e deve ter os recursos de encapsulamento e desencapsulamento habilitados

Depois que esses recursos forem configurados, duas etapas permanecerão para permitir que Azure App configuração Use a chave de Key Vault:
1. Atribuir uma identidade gerenciada à instância de configuração de Azure App
2. Conceda a identidade `GET` , `WRAP` e `UNWRAP` permissões na política de acesso do Key Vault de destino.

## <a name="enable-customer-managed-key-encryption-for-your-azure-app-configuration-instance"></a>Habilitar a criptografia de chave gerenciada pelo cliente para sua instância de configuração de Azure App
Para começar, você precisará de uma instância de configuração de Azure App configurada corretamente. Se você ainda não tiver uma instância de configuração de aplicativo disponível, siga um destes inícios rápidos para configurar uma:
- [Criar um aplicativo ASP.NET Core com a configuração Azure App](quickstart-aspnet-core-app.md)
- [Criar um aplicativo .NET Core com Azure App configuração](quickstart-dotnet-core-app.md)
- [Criar um aplicativo .NET Framework com a Configuração de Aplicativo do Azure](quickstart-dotnet-app.md)
- [Criar um aplicativo Java Spring com a Configuração de Aplicativos do Azure](quickstart-java-spring-app.md)

>[!TIP]
> O Azure Cloud Shell é um shell interativo gratuito que pode ser usado para executar as instruções de linha de comando contidas neste artigo.  Ele tem ferramentas comuns do Azure pré-instaladas, incluindo o SDK do .NET Core. Se você fez logon na assinatura do Azure, inicie o [Azure Cloud Shell](https://shell.azure.com) em shell.azure.com.  Você pode saber mais sobre o Azure Cloud Shell [lendo nossa documentação](../cloud-shell/overview.md)

### <a name="create-and-configure-an-azure-key-vault"></a>Criar e configurar um Azure Key Vault
1. Crie um Azure Key Vault usando o CLI do Azure.  Observe que o `vault-name` e o `resource-group-name` são fornecidos pelo usuário e devem ser exclusivos.  Usamos `contoso-vault` e, `contoso-resource-group` nesses exemplos.

    ```azurecli
    az keyvault create --name contoso-vault --resource-group contoso-resource-group
    ```
    
1. Habilite a exclusão reversível e a proteção de limpeza para o Key Vault. Substitua os nomes dos Key Vault ( `contoso-vault` ) e do grupo de recursos ( `contoso-resource-group` ) criados na etapa 1.

    ```azurecli
    az keyvault update --name contoso-vault --resource-group contoso-resource-group --enable-purge-protection --enable-soft-delete
    ```
    
1. Crie uma chave de Key Vault. Forneça um exclusivo `key-name` para essa chave e substitua os nomes do Key Vault ( `contoso-vault` ) criado na etapa 1. Especifique se você prefere `RSA` ou `RSA-HSM` criptografar.

    ```azurecli
    az keyvault key create --name key-name --kty {RSA or RSA-HSM} --vault-name contoso-vault
    ```
    
    A saída desse comando mostra a ID da chave ("Kid") para a chave gerada.  Anote a ID da chave a ser usada posteriormente neste exercício.  A ID da chave tem o formato: `https://{my key vault}.vault.azure.net/keys/{key-name}/{Key version}` .  A ID da chave tem três componentes importantes:
    1. URI do Key Vault: ' https://{My Key Vault}. Vault. Azure. net
    1. Nome da chave de Key Vault: {nome da chave}
    1. Versão da chave de Key Vault: {Version Key}

1. Crie uma identidade gerenciada atribuída pelo sistema usando o CLI do Azure, substituindo o nome da instância de configuração do aplicativo e do grupo de recursos usado nas etapas anteriores. A identidade gerenciada será usada para acessar a chave gerenciada. Usamos `contoso-app-config` para ilustrar o nome de uma instância de configuração de aplicativo:
    
    ```azurecli
    az appconfig identity assign --name contoso-app-config --resource-group contoso-resource-group --identities [system]
    ```
    
    A saída desse comando inclui a ID da entidade de segurança ("PrincipalId") e a ID do locatário ("tenandId") da identidade atribuída pelo sistema.  Essas IDs serão usadas para conceder o acesso de identidade à chave gerenciada.

    ```json
    {
    "principalId": {Principal Id},
    "tenantId": {Tenant Id},
    "type": "SystemAssigned",
    "userAssignedIdentities": null
    }
    ```

1. A identidade gerenciada da instância de configuração do Azure App precisa acessar a chave para executar a validação, criptografia e descriptografia da chave. O conjunto específico de ações às quais ele precisa de acesso inclui: `GET` , `WRAP` e `UNWRAP` para chaves.  A concessão do acesso exige a ID da entidade de segurança da instância de configuração do aplicativo. Esse valor foi obtido na etapa anterior. Ele é mostrado abaixo como `contoso-principalId` . Conceda permissão para a chave gerenciada usando a linha de comando:

    ```azurecli
    az keyvault set-policy -n contoso-vault --object-id contoso-principalId --key-permissions get wrapKey unwrapKey
    ```

1. Depois que a instância de configuração do Azure App pode acessar a chave gerenciada, podemos habilitar a capacidade de chave gerenciada pelo cliente no serviço usando o CLI do Azure. Relembre as seguintes propriedades registradas durante as etapas de criação de chave: `key name` `key vault URI` .

    ```azurecli
    az appconfig update -g contoso-resource-group -n contoso-app-config --encryption-key-name key-name --encryption-key-version key-version --encryption-key-vault key-vault-Uri
    ```

Sua instância de configuração do Azure App agora está configurada para usar uma chave gerenciada pelo cliente armazenada no Azure Key Vault.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você configurou sua instância de configuração de Azure App para usar uma chave gerenciada pelo cliente para criptografia.  Saiba como [integrar seu serviço com identidades gerenciadas do Azure](howto-integrate-azure-managed-service-identity.md).
