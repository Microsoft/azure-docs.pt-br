---
title: Tutorial de rotação para recursos com um conjunto de credenciais de autenticação armazenados no Azure Key Vault
description: Use este tutorial para aprender a automatizar a rotação de um segredo para recursos que usam um conjunto de credenciais de autenticação.
services: key-vault
author: msmbaldwin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp
ms.openlocfilehash: e66be3b0e3ecae5caa1a76294cc8b8dc11a5f207
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748656"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-one-set-of-authentication-credentials"></a>Automatizar a rotação de um segredo para recursos que usam um conjunto de credenciais de autenticação

A melhor maneira de se autenticar nos serviços do Azure é usando uma [identidade gerenciada](../general/authentication.md), mas há alguns cenários em que essa não é uma opção. Nesses casos, são usadas chaves de acesso ou segredos. Você deve realizar periodicamente a rotação de chaves de acesso ou segredos.

Este tutorial mostra como automatizar a rotação periódica de segredos para bancos de dados e serviços que usam um conjunto de credenciais de autenticação. Especificamente, este tutorial realiza a rotação das senhas do SQL Server armazenadas no Azure Key Vault usando uma função disparada pela notificação da Grade de Eventos do Azure:


:::image type="content" source="../media/rotate-1.png" alt-text="Diagrama de solução de rotação":::

1. Trinta dias antes da data de validade de um segredo, o Key Vault publica o evento de "expiração próxima" na Grade de Eventos.
1. A Grade de Eventos verifica as assinaturas de eventos e usa HTTP POST para chamar o ponto de extremidade do aplicativo de funções assinado para o evento.
1. O aplicativo de funções recebe as informações secretas, gera uma nova senha aleatória e cria uma versão para o segredo com a nova senha no Key Vault.
1. O aplicativo de funções atualiza o SQL Server com uma nova senha.

> [!NOTE]
> Pode haver um atraso entre as etapas 3 e 4. Durante esse tempo, o segredo no Key Vault não poderá se autenticar no SQL Server. Em caso de uma falha em qualquer uma das etapas, a Grade de Eventos realiza novas tentativas por duas horas.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Cofre de Chave do Azure
* SQL Server

O link de implantação abaixo poderá ser usado se você não tiver um Key Vault nem um SQL Server existentes:

[![Imagem mostrando um botão rotulado como "Implantar no Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmain%2FARM-Templates%2FInitial-Setup%2Fazuredeploy.json)

1. Em **Grupo de recursos**, selecione **Criar**. Dê um nome ao grupo, neste tutorial usamos **akvrotation**.
1. Em **Logon do Administrador do SQL**, digite nome de logon do administrador do SQL. 
1. Selecione **Examinar + criar**.
1. Escolha **Criar**

:::image type="content" source="../media/rotate-2.png" alt-text="Criar um grupo de recursos":::

Agora você terá um Key Vault e uma instância do SQL Server. Você pode verificar essa configuração na CLI do Azure executando o seguinte comando:

```azurecli
az resource list -o table -g akvrotation
```

O resultado terá uma aparência semelhante à da seguinte saída:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv           akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotation-sql          akvrotation      eastus      Microsoft.Sql/servers
akvrotation-sql/master   akvrotation      eastus      Microsoft.Sql/servers/databases
akvrotation-sql2         akvrotation      eastus      Microsoft.Sql/servers
akvrotation-sql2/master  akvrotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-and-deploy-sql-server-password-rotation-function"></a>Criar e implantar a função de rotação de senha do SQL Server
> [!IMPORTANT]
> O modelo abaixo exige que o Key Vault, o SQL Server e a Função do Azure estejam no mesmo grupo de recursos

Em seguida, crie um aplicativo de funções com uma identidade gerenciada pelo sistema, juntamente com os outros componentes necessários e implante as funções de rotação de senha do SQL Server

O aplicativo de funções requer estes componentes:
- Um plano do Serviço de Aplicativo do Azure
- Um Aplicativo de Funções com funções de rotação de senha do SQL com gatilho de evento e gatilho http 
- Uma conta de armazenamento necessária para o gerenciamento de gatilho do aplicativo de funções
- Uma política de acesso para usar a identidade do Aplicativo de Funções para acessar segredos no Key Vault
- Assinatura de evento do EventGrid para o evento **SecretNearExpiry**

1. Selecione o link de implantação de modelo do Azure: 

   [![Imagem mostrando um botão rotulado como "Implantar no Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmain%2FARM-Templates%2FFunction%2Fazuredeploy.json)

1. Na lista **Grupo de recursos**, selecione **akvrotation**.
1. No **Nome do SQL Server**, digite o nome do SQL Server com a senha para rotação
1. No **Nome do Cofre de Chaves**, digite o nome do cofre de chaves
1. No **Nome do Aplicativo de Funções**, digite o nome do aplicativo de funções
1. No **Nome do Segredo**, digite o nome do segredo em que a senha será armazenada
1. Na **URL do Repositório**, digite a localização do GitHub do código de função ( **https://github.com/Azure-Samples/KeyVault-Rotation-SQLPassword-Csharp.git** )
1. Selecione **Examinar + criar**.
1. Selecione **Criar**.

:::image type="content" source="../media/rotate-3.png" alt-text="Selecione Examinar + criar":::
  

Depois de concluir as etapas anteriores, você terá uma conta de armazenamento, um farm de servidores e um aplicativo de funções. Você pode verificar essa configuração na CLI do Azure executando o seguinte comando:

```azurecli
az resource list -o table -g akvrotation
```

O resultado terá uma aparência semelhante à da seguinte saída:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv           akvrotation       eastus      Microsoft.KeyVault/vaults
akvrotation-sql          akvrotation       eastus      Microsoft.Sql/servers
akvrotation-sql/master   akvrotation       eastus      Microsoft.Sql/servers/databases
cfogyydrufs5wazfunctions akvrotation       eastus      Microsoft.Storage/storageAccounts
akvrotation-fnapp        akvrotation       eastus      Microsoft.Web/serverFarms
akvrotation-fnapp        akvrotation       eastus      Microsoft.Web/sites
akvrotation-fnapp        akvrotation       eastus      Microsoft.insights/components
```

Para obter informações sobre como criar um aplicativo de funções e usar a identidade gerenciada para acessar o Key Vault, confira [Criar um aplicativo de funções no portal do Azure](../../azure-functions/functions-create-function-app-portal.md), [Como usar identidades gerenciadas para o Serviço de Aplicativo e o Azure Functions](../../app-service/overview-managed-identity.md) e [Atribuir uma política de acesso do Key Vault usando o portal do Azure](../general/assign-access-policy-portal.md).

### <a name="rotation-function"></a>Função de rotação
A função implantada na etapa anterior usa um evento para disparar a rotação de um segredo atualizando o Key Vault e o Banco de Dados SQL. 

#### <a name="function-trigger-event"></a>Evento de gatilho de função

Essa função lê dados de eventos e executa a lógica de rotação:

```csharp
public static class SimpleRotationEventHandler
{
   [FunctionName("AKVSQLRotation")]
   public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
   {
      log.LogInformation("C# Event trigger function processed a request.");
      var secretName = eventGridEvent.Subject;
      var secretVersion = Regex.Match(eventGridEvent.Data.ToString(), "Version\":\"([a-z0-9]*)").Groups[1].ToString();
      var keyVaultName = Regex.Match(eventGridEvent.Topic, ".vaults.(.*)").Groups[1].ToString();
      log.LogInformation($"Key Vault Name: {keyVaultName}");
      log.LogInformation($"Secret Name: {secretName}");
      log.LogInformation($"Secret Version: {secretVersion}");

      SecretRotator.RotateSecret(log, secretName, keyVaultName);
   }
}
```

#### <a name="secret-rotation-logic"></a>Lógica de rotação do segredo
Esse método de rotação lê as informações do banco de dados do segredo, cria uma versão do segredo e atualiza o banco de dados com um novo segredo:

```csharp
    public class SecretRotator
    {
        private const string CredentialIdTag = "CredentialId";
        private const string ProviderAddressTag = "ProviderAddress";
        private const string ValidityPeriodDaysTag = "ValidityPeriodDays";

        public static void RotateSecret(ILogger log, string secretName, string keyVaultName)
        {
            //Retrieve Current Secret
            var kvUri = "https://" + keyVaultName + ".vault.azure.net";
            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
            KeyVaultSecret secret = client.GetSecret(secretName);
            log.LogInformation("Secret Info Retrieved");

            //Retrieve Secret Info
            var credentialId = secret.Properties.Tags.ContainsKey(CredentialIdTag) ? secret.Properties.Tags[CredentialIdTag] : "";
            var providerAddress = secret.Properties.Tags.ContainsKey(ProviderAddressTag) ? secret.Properties.Tags[ProviderAddressTag] : "";
            var validityPeriodDays = secret.Properties.Tags.ContainsKey(ValidityPeriodDaysTag) ? secret.Properties.Tags[ValidityPeriodDaysTag] : "";
            log.LogInformation($"Provider Address: {providerAddress}");
            log.LogInformation($"Credential Id: {credentialId}");

            //Check Service Provider connection
            CheckServiceConnection(secret);
            log.LogInformation("Service  Connection Validated");
            
            //Create new password
            var randomPassword = CreateRandomPassword();
            log.LogInformation("New Password Generated");

            //Add secret version with new password to Key Vault
            CreateNewSecretVersion(client, secret, randomPassword);
            log.LogInformation("New Secret Version Generated");

            //Update Service Provider with new password
            UpdateServicePassword(secret, randomPassword);
            log.LogInformation("Password Changed");
            log.LogInformation($"Secret Rotated Successfully");
        }
}
```
É possível encontrar o código de exemplo [no GitHub](https://github.com/Azure-Samples/KeyVault-Rotation-SQLPassword-Csharp).

## <a name="add-the-secret-to-key-vault"></a>Adicionar o segredo ao Key Vault
Defina sua política de acesso para conceder as permissões para *gerenciar segredos* a usuários:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

Crie um segredo com marcas que contenham a ID de recurso do SQL Server, o nome de logon do SQL Server e o período de validade do segredo em dias. Forneça o nome do segredo, a senha inicial do Banco de Dados SQL (em nosso exemplo "Simple123") e inclua uma data de validade definida para amanhã.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqlPassword --vault-name akvrotation-kv --value "Simple123" --tags "CredentialId=sqlAdmin" "ProviderAddress=<sql-database-resource-id>" "ValidityPeriodDays=90" --expires $tomorrowDate
```

A criação de um segredo com uma data de validade curta publicará um evento `SecretNearExpiry` em 15 minutos, o que, por sua vez, disparará a função para realizar a rotação do segredo.

## <a name="test-and-verify"></a>Testar e verificar

Para verificar se a rotação do segredo foi realizada, acesse **Key Vault** > **Segredos**:

:::image type="content" source="../media/rotate-8.png" alt-text="Captura de tela que mostra como acessar Key Vault > Segredos.":::

Abra o segredo **sqlPassword** e veja as versões originais e com rotação:

:::image type="content" source="../media/rotate-9.png" alt-text="Acessar Segredos":::

### <a name="create-a-web-app"></a>Criar um aplicativo Web

Para verificar as credenciais do SQL, crie um aplicativo Web. Esse aplicativo Web obterá o segredo do Key Vault, extrairá as informações e as credenciais do Banco de Dados SQL do segredo e testará a conexão com o SQL Server.

O aplicativo Web requer estes componentes:
- Um aplicativo Web com identidade gerenciada pelo sistema
- Uma política de acesso para acessar segredos no Key Vault usando a identidade gerenciada do aplicativo Web

1. Selecione o link de implantação de modelo do Azure: 

   [![Imagem mostrando um botão rotulado como "Implantar no Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-SQLPassword-Csharp-WebApp%2Fmain%2FARM-Templates%2FWeb-App%2Fazuredeploy.json)

1. Selecione o grupo de recursos **akvrotation**.
1. No **Nome do SQL Server**, digite o nome do SQL Server com a senha para rotação
1. No **Nome do Cofre de Chaves**, digite o nome do cofre de chaves
1. No **Nome do Segredo**, digite o nome do segredo no qual a senha está armazenada
1. Na **URL do Repositório**, digite a localização do GitHub do código do aplicativo Web ( **https://github.com/Azure-Samples/KeyVault-Rotation-SQLPassword-Csharp-WebApp.git** )
1. Selecione **Examinar + criar**.
1. Selecione **Criar**.


### <a name="open-the-web-app"></a>Abra o aplicativo Web

Acesse a URL de aplicativo implantado:
 
'https://akvrotation-app.azurewebsites.net/'

Quando o aplicativo for aberto no navegador, você verá o **Valor Secreto Gerado** e um valor de **Banco de Dados Conectado** igual a *verdadeiro*.

## <a name="learn-more"></a>Saiba mais

- Tutorial: [Rotação para recursos com dois conjuntos de credenciais](tutorial-rotation-dual.md)
- Visão geral: [Monitoramento do Key Vault com a Grade de Eventos do Azure](../general/event-grid-overview.md)
- Como fazer: [Receber emails quando o status do cofre de chaves secreto é alterado](../general/event-grid-logicapps.md)
- [Esquema de eventos da Grade de Eventos do Azure para o Azure Key Vault](../../event-grid/event-schema-key-vault.md)
