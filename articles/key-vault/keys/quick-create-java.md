---
title: Guia de início rápido – Biblioteca de clientes da Chave do Azure Key Vault para Java
description: Fornece um guia de início rápido para usar a biblioteca de clientes de Chaves do Azure Key Vault para Java.
author: msmbaldwin
ms.custom: devx-track-java
ms.author: mbaldwin
ms.date: 01/05/2021
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.openlocfilehash: 75cb7b6c9225e8579561f980df10da8994257133
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777177"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-java"></a>Início Rápido: biblioteca de clientes da Chave do Azure Key Vault para Java
Introdução à biblioteca de clientes da Chave do Azure Key Vault para Java. Siga as etapas abaixo para instalar o pacote e testar o código de exemplo para tarefas básicas.

Recursos adicionais:

* [Código-fonte](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys)
* [Documentação de referência da API](https://azure.github.io/azure-sdk-for-java/keyvault.html)
* [Documentação do produto](index.yml)
* [Amostras](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys/src/samples/java/com/azure/security/keyvault/keys)

## <a name="prerequisites"></a>Pré-requisitos
- Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/java/azure/jdk/) versão 8 ou superior
- [Apache Maven](https://maven.apache.org)
- [CLI do Azure](/cli/azure/install-azure-cli)

Este início rápido pressupõe que você está executando a [CLI do Azure](/cli/azure/install-azure-cli) e o [Apache Maven](https://maven.apache.org) em uma janela de terminal do Linux.

## <a name="setting-up"></a>Configurando
Este início rápido usa a biblioteca de Identidades do Azure com a CLI do Azure para autenticar usuários nos Serviços do Azure. Os desenvolvedores também podem usar o Visual Studio ou o Visual Studio Code para autenticar as chamadas. Para saber mais, confira [Autenticar o cliente na biblioteca de clientes do Azure Idendity](/java/api/overview/azure/identity-readme).

### <a name="sign-in-to-azure"></a>Entrar no Azure
1. Execute o comando `login`.

    ```azurecli-interactive
    az login
    ```

   Se a CLI puder abrir o navegador padrão, ela o fará e carregará uma página de entrada do Azure.

   Caso contrário, abra uma página de navegador em [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e insira o código de autorização exibido no terminal.

2. Entre com suas credenciais de conta no navegador.

### <a name="create-a-new-java-console-app"></a>Criar um aplicativo de console Java
Em uma janela de console, use o comando `mvn` para criar um novo aplicativo do console do Java com o nome `akv-keys-java`.

```console
mvn archetype:generate -DgroupId=com.keyvault.keys.quickstart
                       -DartifactId=akv-keys-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

O resultado da geração do projeto deve ser algo similar a:

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.keys.quickstart
[INFO] Parameter: artifactId, Value: akv-keys-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.keys.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.keys.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.keys.quickstart
[INFO] Parameter: artifactId, Value: akv-keys-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-keys-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Altere o diretório para a pasta `akv-keys-java/` recém-criada.

```console
cd akv-keys-java
```

### <a name="install-the-package"></a>Instalar o pacote
Abra o arquivo *pom.xml* no seu editor de texto. Adicione os seguintes elementos de dependência ao grupo de dependências.

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-keys</artifactId>
      <version>4.2.3</version>
    </dependency>

    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-identity</artifactId>
      <version>1.2.0</version>
    </dependency>
```

### <a name="create-a-resource-group-and-key-vault"></a>Criar um grupo de recursos e um cofre de chaves
[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

#### <a name="grant-access-to-your-key-vault"></a>Permitir acesso ao cofre de chaves
Crie uma política de acesso para o cofre de chaves que conceda permissões de chave à sua conta de usuário.

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --key-permissions delete get list create purge
```

#### <a name="set-environment-variables"></a>Definir variáveis de ambiente
Este aplicativo usa o nome de seu cofre de chaves como uma variável de ambiente chamada `KEY_VAULT_NAME`.

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS ou Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Modelo de objeto
A biblioteca de clientes da Chave do Azure Key Vault para Java permite gerenciar chaves. A seção [Exemplos de código](#code-examples) mostrará como criar um cliente e uma chave, bem como recuperar e excluir uma chave.

Todo o aplicativo de console está [abaixo](#sample-code).

## <a name="code-examples"></a>Exemplos de código
### <a name="add-directives"></a>Adicionar diretivas
Adicione as seguintes diretivas à parte superior de seu código:

```java
import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.keys.KeyClient;
import com.azure.security.keyvault.keys.KeyClientBuilder;
import com.azure.security.keyvault.keys.models.DeletedKey;
import com.azure.security.keyvault.keys.models.KeyType;
import com.azure.security.keyvault.keys.models.KeyVaultKey;
```

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente
Neste início rápido, um usuário conectado é usado para autenticação no Key Vault, que é o método preferencial usado para desenvolvimento local. Para aplicativos implantados no Azure, uma Identidade Gerenciada deverá ser atribuída a um Serviço de Aplicativo ou uma Máquina Virtual. Para obter mais informações, confira [Visão geral da Identidade Gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

No exemplo abaixo, o nome do cofre de chaves é expandido para o URI do cofre de chaves, no formato "https://\<your-key-vault-name\>.vault.azure.net". Esse exemplo usa a classe ['DefaultAzureCredential()'](https://docs.microsoft.com/java/api/com.azure.identity.defaultazurecredential), que permite usar o mesmo código em diferentes ambientes com diferentes opções para fornecer uma identidade. Para obter mais informações, confira [Autenticação de credenciais do Azure padrão](https://docs.microsoft.com/java/api/overview/azure/identity-readme).

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

KeyClient keyClient = new KeyClientBuilder()
    .vaultUrl(keyVaultUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="create-a-key"></a>Criar uma chave
Agora que seu aplicativo está autenticado, será possível criar uma chave no cofre de chaves usando o método `keyClient.createKey`. Isso requer obter um nome para a chave e um tipo de chave. Nesse exemplo, atribuímos o valor "myKey" à variável `keyName` e usamos um `KeyType` RSA.

```java
keyClient.createKey(keyName, KeyType.RSA);
```

Confirme se a chave foi definida com o comando [az keyvault key show](/cli/azure/keyvault/key?#az_keyvault_key_show):

```azurecli
az keyvault key show --vault-name <your-unique-key-vault-name> --name myKey
```

### <a name="retrieve-a-key"></a>Recuperar uma chave
Agora você pode recuperar a chave criada anteriormente com o método `keyClient.getKey`.

```java
KeyVaultKey retrievedKey = keyClient.getKey(keyName);
 ```

Agora é possível acessar detalhes da chave recuperada usando operações como `retrievedKey.getProperties`, `retrievedKey.getKeyOperations` etc.

### <a name="delete-a-key"></a>Excluir uma chave
Por fim, vamos excluir a chave do cofre de chaves usando o método `keyClient.beginDeleteKey`.

A exclusão de chave é uma operação de execução prolongada, na qual será possível examinar o progresso ou aguardar a conclusão da execução.

```java
SyncPoller<DeletedKey, Void> deletionPoller = keyClient.beginDeleteKey(keyName);
deletionPoller.waitForCompletion();
```

É possível verificar se a chave foi excluída usando o comando [az keyvault key show](/cli/azure/keyvault/key?#az_keyvault_key_show):

```azurecli
az keyvault key show --vault-name <your-unique-key-vault-name> --name myKey
```

## <a name="clean-up-resources"></a>Limpar os recursos
Quando o cofre de chaves e o grupo de recursos correspondente não forem mais necessários, use a CLI do Azure ou o Azure PowerShell para removê-los.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Código de exemplo
```java
package com.keyvault.keys.quickstart;

import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.keys.KeyClient;
import com.azure.security.keyvault.keys.KeyClientBuilder;
import com.azure.security.keyvault.keys.models.DeletedKey;
import com.azure.security.keyvault.keys.models.KeyType;
import com.azure.security.keyvault.keys.models.KeyVaultKey;

public class App {
    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {
        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and key vault URI = %s \n", keyVaultName, keyVaultUri);

        KeyClient keyClient = new KeyClientBuilder()
                .vaultUrl(keyVaultUri)
                .credential(new DefaultAzureCredentialBuilder().build())
                .buildClient();

        String keyName = "myKey";

        System.out.print("Creating a key in " + keyVaultName + " called '" + keyName + " ... ");

        keyClient.createKey(keyName, KeyType.RSA);

        System.out.print("done.");
        System.out.println("Retrieving key from " + keyVaultName + ".");

        KeyVaultKey retrievedKey = keyClient.getKey(keyName);

        System.out.println("Your key's ID is '" + retrievedKey.getId() + "'.");
        System.out.println("Deleting your key from " + keyVaultName + " ... ");

        SyncPoller<DeletedKey, Void> deletionPoller = keyClient.beginDeleteKey(keyName);
        deletionPoller.waitForCompletion();

        System.out.print("done.");
    }
}
```

## <a name="next-steps"></a>Próximas etapas
Neste guia de início rápido, você criou um cofre de chaves e uma chave, bem como recuperou e excluiu a chave. Para saber mais sobre o Key Vault e como integrá-lo a seus aplicativos, confira os artigos abaixo.

- Leia uma [Visão geral do Azure Key Vault](../general/overview.md)
- Leia a [visão geral de segurança do Key Vault](../general/security-overview.md)
- Confira o [Guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- Como [Proteger o acesso a um cofre de chaves](../general/security-overview.md)