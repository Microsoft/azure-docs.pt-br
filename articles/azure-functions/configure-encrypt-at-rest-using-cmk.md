---
title: Criptografar a origem do aplicativo em repouso
description: Criptografe os dados do aplicativo no armazenamento do Azure e implante-os como um arquivo de pacote.
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: 6e4cf16118e748e3ee1d1ff4899730463565ec70
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108024"
---
# <a name="encrypt-your-application-data-at-rest-using-customer-managed-keys"></a>Criptografar os dados do aplicativo em repouso usando chaves gerenciadas pelo cliente

Criptografar os dados de aplicativo do aplicativo de funções em repouso requer uma conta de armazenamento do Azure e um Azure Key Vault. Esses serviços são usados quando você executa o aplicativo de um pacote de implantação.

  - O [armazenamento do Azure fornece criptografia em repouso](../storage/common/storage-service-encryption.md). Você pode usar as chaves fornecidas pelo sistema ou suas próprias chaves gerenciadas pelo cliente. É aí que os dados do aplicativo são armazenados quando não estão em execução em um aplicativo de funções no Azure.
  - A [execução de um pacote de implantação](run-functions-from-deployment-package.md) é um recurso de implantação do serviço de aplicativo. Ele permite que você implante o conteúdo do site de uma conta de armazenamento do Azure usando uma URL de SAS (assinatura de acesso compartilhado).
  - [Referências de Key Vault](../app-service/app-service-key-vault-references.md) são um recurso de segurança do serviço de aplicativo. Ele permite que você importe segredos em tempo de execução como configurações de aplicativo. Use isso para criptografar a URL SAS da sua conta de armazenamento do Azure.

## <a name="set-up-encryption-at-rest"></a>Configurar a criptografia em repouso

### <a name="create-an-azure-storage-account"></a>Criar uma conta de Armazenamento do Azure

Primeiro, [crie uma conta de armazenamento do Azure](../storage/common/storage-account-create.md) e [criptografe-a com as chaves gerenciadas pelo cliente](../storage/common/customer-managed-keys-overview.md). Depois que a conta de armazenamento for criada, use o [Gerenciador de armazenamento do Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) para carregar arquivos de pacote.

Em seguida, use o Gerenciador de Armazenamento para [gerar uma SAS](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer). 

> [!NOTE]
> Salve essa URL SAS, que será usada posteriormente para habilitar o acesso seguro do pacote de implantação em tempo de execução.

### <a name="configure-running-from-a-package-from-your-storage-account"></a>Configurar a execução de um pacote de sua conta de armazenamento
  
Depois de carregar o arquivo no armazenamento de BLOBs e ter uma URL SAS para o arquivo, defina a `WEBSITE_RUN_FROM_PACKAGE` configuração de aplicativo para a URL SAS. O exemplo a seguir faz isso usando CLI do Azure:

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

A adição dessa configuração de aplicativo faz com que seu aplicativo de funções seja reiniciado. Depois que o aplicativo for reiniciado, navegue até ele e verifique se o aplicativo foi iniciado corretamente usando o pacote de implantação. Se o aplicativo não tiver sido iniciado corretamente, consulte o [Guia de solução de problemas de execução do pacote](run-functions-from-deployment-package.md#troubleshooting).

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>Criptografar a configuração do aplicativo usando referências de Key Vault

Agora você pode substituir o valor da `WEBSITE_RUN_FROM_PACKAGE` configuração do aplicativo por um Key Vault referência à URL codificada para SAS. Isso mantém a URL SAS criptografada no Key Vault, que fornece uma camada extra de segurança.

1. Use o comando a seguir [`az keyvault create`](/cli/azure/keyvault#az-keyvault-create) para criar uma instância de Key Vault.       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. Siga [estas instruções para conceder acesso](../app-service/app-service-key-vault-references.md#granting-your-app-access-to-key-vault) ao seu aplicativo ao seu cofre de chaves:

1. Use o seguinte [`az keyvault secret set`](/cli/azure/keyvault/secret#az-keyvault-secret-set) comando para adicionar a URL externa como um segredo no cofre de chaves:   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  Use o comando a seguir [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) para criar a `WEBSITE_RUN_FROM_PACKAGE` configuração de aplicativo com o valor como uma referência Key Vault para a URL externa:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    O `<secret-version>` estará na saída do `az keyvault secret set` comando anterior.

A atualização dessa configuração de aplicativo faz com que seu aplicativo de funções seja reiniciado. Depois que o aplicativo for reiniciado, navegue até ele para verificar se ele foi iniciado corretamente usando a referência de Key Vault.

## <a name="how-to-rotate-the-access-token"></a>Como girar o token de acesso

É uma prática recomendada girar periodicamente a chave SAS da sua conta de armazenamento. Para garantir que o aplicativo de funções não tenha acesso inadvertidamente, você também deve atualizar a URL SAS no Key Vault.

1. Gire a chave SAS navegando até sua conta de armazenamento no portal do Azure. Em **configurações**  >  **chaves de acesso**, clique no ícone para girar a chave SAS.

1. Copie a nova URL de SAS e use o comando a seguir para definir a URL SAS atualizada no cofre de chaves:

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ``` 

1. Atualize a referência do cofre de chaves em sua configuração de aplicativo para a nova versão secreta:

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    O `<secret-version>` estará na saída do `az keyvault secret set` comando anterior.

## <a name="how-to-revoke-the-function-apps-data-access"></a>Como revogar o acesso a dados do aplicativo de funções

Há dois métodos para revogar o acesso do aplicativo de funções à conta de armazenamento. 

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>Girar a chave SAS para a conta de armazenamento do Azure

Se a chave SAS para a conta de armazenamento for girada, o aplicativo de funções não terá mais acesso à conta de armazenamento, mas continuará a ser executado com a última versão baixada do arquivo de pacote. Reinicie o aplicativo de funções para limpar a última versão baixada.

### <a name="remove-the-function-apps-access-to-key-vault"></a>Remover o acesso do aplicativo de funções para Key Vault

Você pode revogar o acesso do aplicativo de funções aos dados do site desabilitando o acesso do aplicativo de funções a Key Vault. Para fazer isso, remova a política de acesso para a identidade do aplicativo de funções. Essa é a mesma identidade que você criou anteriormente ao configurar referências do Key Vault.

## <a name="summary"></a>Resumo

Os arquivos do aplicativo agora são criptografados em repouso em sua conta de armazenamento. Quando o aplicativo de funções é iniciado, ele recupera a URL SAS do cofre de chaves. Por fim, o aplicativo de funções carrega os arquivos de aplicativo da conta de armazenamento. 

Se precisar revogar o acesso do aplicativo de funções à sua conta de armazenamento, você poderá revogar o acesso ao cofre de chaves ou girar as chaves da conta de armazenamento, o que invalida a URL da SAS.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="is-there-any-additional-charge-for-running-my-function-app-from-the-deployment-package"></a>Há algum custo adicional para executar meu aplicativo de funções do pacote de implantação?

Somente o custo associado à conta de armazenamento do Azure e quaisquer encargos de saída aplicáveis.

### <a name="how-does-running-from-the-deployment-package-affect-my-function-app"></a>Como a execução do pacote de implantação afeta meu aplicativo de funções?

- Executar seu aplicativo do pacote de implantação torna `wwwroot/` -se somente leitura. Seu aplicativo recebe um erro ao tentar gravar nesse diretório.
- Não há suporte para formatos TAR e GZIP.
- Este recurso não é compatível com o cache local.

## <a name="next-steps"></a>Próximas etapas

- [Referências de Key Vault para o serviço de aplicativo](../app-service/app-service-key-vault-references.md)
- [Criptografia do Armazenamento do Azure para dados em repouso](../storage/common/storage-service-encryption.md)