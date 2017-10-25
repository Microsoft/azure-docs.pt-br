---
title: "Configurar o Cofre de Chaves do Azure com a rotação de chaves e auditoria de ponta a ponta | Microsoft Docs"
description: "Use estas instruções para ajudá-lo a concluir a configuração com a rotação de chaves e o monitoramento de logs do Cofre de Chaves."
services: key-vault
documentationcenter: 
author: swgriffith
manager: mbaldwin
tags: 
ms.assetid: 9cd7e15e-23b8-41c0-a10a-06e6207ed157
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: jodehavi;stgriffi
ms.openlocfilehash: f98ba1e2da6924476392948a4d18c807d68e39e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-azure-key-vault-with-end-to-end-key-rotation-and-auditing"></a>Configurar o Cofre de Chaves do Azure com a rotação de chaves e auditoria de ponta a ponta
## <a name="introduction"></a>Introdução
Depois de criar o cofre de chaves, você poderá começar a usá-lo para armazenar chaves e segredos. Seus aplicativos não precisam manter suas chaves ou segredos, mas solicitá-los do Cofre de Chaves conforme necessário. Isso permite que você atualize chaves e segredos sem afetar o comportamento do aplicativo, o que abre uma gama de possibilidades em relação ao gerenciamento de chaves e segredos.

Este artigo apresenta um exemplo de uso do Cofre de Chaves do Azure para armazenar um segredo, neste caso, uma chave de Conta de Armazenamento do Azure que é acessada por um aplicativo. Ele também demonstra a implementação de uma rotação agendada dessa chave de conta de armazenamento. Por fim, ele percorre uma demonstração de como monitorar os logs de auditoria do Cofre de Chaves e gerar alertas quando são feitas solicitações inesperadas.

> [!NOTE]
> Este tutorial não se destina a explicar em detalhes a configuração inicial do cofre de chaves. Para obter essas informações, confira [Introdução ao Cofre da Chave do Azure](key-vault-get-started.md). Para obter instruções sobre a Interface de Linha de Comando de Plataforma Cruzada, confira [Gerenciar Cofre de Chaves usando a CLI](key-vault-manage-with-cli2.md).
>
>

## <a name="set-up-key-vault"></a>Configurar o Cofre de Chaves
Para permitir que um aplicativo recupere um segredo do Cofre de Chaves, primeiro crie o segredo e carregue-o no cofre. Isso pode ser feito iniciando uma sessão do Azure PowerShell e entrando em sua conta do Azure com o seguinte comando:

```powershell
Login-AzureRmAccount
```

Na janela pop-up do navegador, insira o nome de usuário e a senha da sua conta do Azure. O PowerShell obterá todas as assinaturas que estão associadas a essa conta. O PowerShell usa a primeira por padrão.

Se tiver várias assinaturas, você terá que especificar a que foi usada para criar o cofre de chaves. Digite o seguinte para ver as assinaturas de sua conta:

```powershell
Get-AzureRmSubscription
```

Para especificar a assinatura associada ao cofre de chaves do qual os logs serão registrados, digite:

```powershell
Set-AzureRmContext -SubscriptionId <subscriptionID>
```

Como este artigo demonstra como armazenar uma chave de conta de armazenamento como um segredo, você deve obter essa chave de conta de armazenamento.

```powershell
Get-AzureRmStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Depois de recuperar o segredo, nesse caso, a chave da conta de armazenamento, você deve convertê-lo em uma cadeia de caracteres segura e criar um segredo com esse valor no Cofre de Chaves.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzureKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```
Em seguida, obtenha o URI do segredo que você criou. Isso é usado em uma etapa posterior ao chamar o cofre de chaves para recuperar o segredo. Execute o seguinte comando do PowerShell e anote o valor de ID, que é o URI do segredo:

```powershell
Get-AzureKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Configurar o aplicativo
Agora que tem um segredo armazenado, você pode usar o código para recuperá-lo e usá-lo. Algumas etapas são necessárias para fazer isso. A primeira e mais importante etapa é registrar o aplicativo com o Azure Active Directory e informar ao cofre de chaves as informações do aplicativo para que ele possa permitir solicitações do aplicativo.

> [!NOTE]
> O aplicativo deve ser criado no mesmo locatário do Azure Active Directory que o cofre de chaves.
>
>

Abra a guia de aplicativos do Azure Active Directory.

![Abrir os aplicativos no Azure Active Directory](./media/keyvault-keyrotation/AzureAD_Header.png)

Escolha **ADICIONAR** para adicionar um aplicativo ao Azure Active Directory.

![Escolha ADICIONAR](./media/keyvault-keyrotation/Azure_AD_AddApp.png)

Deixe o tipo de aplicativo como **APLICATIVO WEB E/OU API WEB** e dê um nome ao aplicativo.

![Nomear o aplicativo](./media/keyvault-keyrotation/AzureAD_NewApp1.png)

Dê ao aplicativo uma **URL de logon** e um **URI de ID de aplicativo**. Eles podem ser qualquer coisa que você desejar para esta demonstração e podem ser alterados posteriormente, se necessário.

![Fornecer os URIs necessários](./media/keyvault-keyrotation/AzureAD_NewApp2.png)

Depois que o aplicativo for adicionado ao Azure Active Directory, você será levado à página do aplicativo. Clique na guia **Configurar** e localize e copie o valor de **ID do cliente**. Anote a ID do cliente para as etapas posteriores.

Em seguida, gere uma chave para o aplicativo para que ele possa interagir com o Azure Active Directory. Você pode criá-la na seção **Chaves** na guia **Configuração**. Anote a chave recém-gerada do aplicativo do Azure Active Directory para uso em uma etapa posterior.

![Chaves de aplicativo do Azure Active Directory](./media/keyvault-keyrotation/Azure_AD_AppKeys.png)

Antes de estabelecer as chamadas do aplicativo no cofre de chaves, você deve informar o cofre de chaves sobre o aplicativo e suas permissões. O comando a seguir obtém o nome do cofre e a ID do cliente do aplicativo do Azure Active Directory e concede acesso **Get** para o Cofre de Chaves para o aplicativo.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Agora você está pronto para começar a criar as chamadas do aplicativo. No aplicativo, você deve instalar primeiro os pacotes NuGet necessários para interagir com o Cofre de Chaves do Azure e o Azure Active Directory. No Visual Studio Package Manager Console, insira os comandos a seguir. Na elaboração deste artigo, a versão atual do pacote do Azure Active Directory é 3.10.305231913. Portanto, convém confirmar a versão mais recente e atualizar da maneira adequada.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

No código do aplicativo, crie uma classe para manter o método de autenticação do Azure Active Directory. Neste exemplo a classe é chamada **Utils**. Adicione a seguinte instrução usando:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Em seguida, adicione o seguinte método para recuperar o token JWT do Azure Active Directory. Para a facilidade de manutenção, convém mover os valores de cadeia de caracteres embutidos no código para a configuração do aplicativo ou Web.

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

Adicione o código necessário para chamar o Cofre de Chaves e recuperar o valor do segredo. Primeiro é necessário adicionar o seguinte usando a instrução:

```csharp
using Microsoft.Azure.KeyVault;
```

Adicione as chamadas de método para invocar o Cofre de Chaves e recuperar o segredo. Nesse método, você fornece o URI de segredo que salvou na etapa anterior. Observe o uso do método **GetToken** da classe **Utils** criada anteriormente.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Ao executar o aplicativo, agora você deverá se autenticar no Azure Active Directory e recuperar o valor do segredo do Cofre de Chaves do Azure.

## <a name="key-rotation-using-azure-automation"></a>Rotação de chaves usando a Automação do Azure
Há várias opções para implementar uma estratégia de rotação para valores armazenados como segredos do Cofre de Chaves do Azure. Os segredos podem ser rotacionados como parte de um processo manual, programaticamente usando chamadas à API ou por meio de um script de Automação. Para os fins deste artigo, você usará o Azure PowerShell combinado à Automação do Azure para alterar uma chave de acesso da Conta de Armazenamento do Azure. Em seguida, você atualizará um segredo do cofre de chaves com essa nova chave.

Para permitir que a Automação do Azure defina valores de segredo no Cofre de Chaves, você deve obter a ID do cliente para a conexão chamada AzureRunAsConnection, que foi criada ao estabelecer a instância de Automação do Azure. Você pode localizar a ID selecionando **Ativos** da instância da Automação do Azure. Lá, você seleciona **Conexões** e seleciona o princípio de serviço **AzureRunAsConnection**. Anote a **ID do aplicativo**.

![ID do cliente da Automação do Azure](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

Em **Ativos**, escolha **Módulos**. Nos **Módulos**, selecione **Galeria** e pesquise e use a opção **Importar** para as versões atualizadas de cada um dos seguintes módulos:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage


> [!NOTE]
> Na elaboração deste artigo, somente os módulos mencionados anteriormente precisaram ser atualizados para o script a seguir. Se achar que o trabalho de automação está falhando, confirme se importou todos os módulos necessários e suas dependências.
>
>

Depois de recuperar a ID do aplicativo para a conexão da Automação do Azure, você deve informar ao cofre de chaves que o aplicativo tem acesso aos segredos atualizados no cofre. Isso pode ser feito com o seguinte comando do PowerShell:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Em seguida, selecione **Runbooks** na instância da Automação do Azure e selecione **Adicionar um Runbook**. Selecione **Criação Rápida**. Atribua um nome ao runbook e selecione **PowerShell** como o tipo do runbook. Você tem a opção de adicionar uma descrição. Por fim, clique em **Criar**.

![Criar runbook](./media/keyvault-keyrotation/Create_Runbook.png)

Cole o seguinte script do PowerShell no painel do editor para o novo runbook:

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection "
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Add-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

#Optionally you may set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

No painel do editor, escolha **Painel de teste** para testar o script. Depois que o script for executado sem erros, você poderá selecionar **Publicar** e poderá aplicar um cronograma ao runbook no painel de configuração.

## <a name="key-vault-auditing-pipeline"></a>Pipeline de auditoria do Cofre de Chaves
Ao configurar um cofre de chaves, você pode ativar a auditoria para coletar logs de solicitações de acesso feitas para o cofre de chaves. Esses logs são armazenados em uma conta de armazenamento do Azure designada e podem ser extraídos, monitorados e analisados. O cenário a seguir usa funções do Azure, aplicativos lógicos do Azure e logs de auditoria de cofre de chaves para criar um pipeline para enviar um email quando um aplicativo que corresponde à ID de aplicativo do aplicativo Web recupera segredos do cofre.

Primeiro, você deve habilitar o log no cofre de chaves. Isso pode ser feito por meio dos seguintes comandos do PowerShell (detalhes completos podem ser obtidos em [key-vault-logging](key-vault-logging.md)):

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzureRmKeyVault -VaultName '<vaultName>'
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent
```

Depois que isso é habilitado, os logs de auditoria iniciam a coleta para a conta de armazenamento designada. Esses logs contêm eventos sobre como e quando os cofres de chaves são acessados e por quem.

> [!NOTE]
> Você pode acessar as informações de log 10 minutos após a operação do cofre de chaves. Geralmente será mais rápido do que isso.
>
>

A próxima etapa é [criar uma fila do Barramento de Serviço do Azure](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Esse é o local para o qual os logs de auditoria do cofre de chaves são enviados. Quando as mensagens de log de auditoria estão na fila, o aplicativo lógico as obtém e age em relação a elas. Crie um barramento de serviço com as seguintes etapas:

1. Crie um namespace do Barramento de Serviço (se você já tiver um que deseja usar para isso, vá para a Etapa 2).
2. Navegue até o barramento de serviço no portal do Azure e selecione o namespace no qual deseja criar a fila.
3. Selecione **Novo**, selecione **Barramento de Serviço -> Fila** e insira os detalhes necessários.
4. Selecione as informações de conexão do Barramento de Serviço escolhendo o namespace e clicando em **Informações de Conexão**. Você precisará dessas informações para a próxima seção.

Em seguida, [crie uma função do Azure](../azure-functions/functions-create-first-azure-function.md) para sondar os logs do cofre de chaves na conta de armazenamento e obter novos eventos. Essa será uma função disparada em um cronograma.

Para criar uma função do Azure, escolha **Novo > Aplicativo de Função** no portal do Azure. Durante a criação, você pode usar um plano de hospedagem existente ou criar um novo. Você também pode optar pela hospedagem dinâmica. Mais detalhes sobre opções de hospedagem de Função podem ser encontrados em [Como dimensionar as Funções do Azure](../azure-functions/functions-scale.md).

Quando a função do Azure for criada, navegue até ela, escolha uma função de timer e C\#. Em seguida, clique em **Criar esta função**.

![Folha de Início das Funções do Azure](./media/keyvault-keyrotation/Azure_Functions_Start.png)

Na guia **Desenvolver** , substitua o código de run.csx pelo seguinte:

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging;
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log)
{
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didnt have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //required to order by time as they may not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```


> [!NOTE]
> Substitua as variáveis no código anterior para apontar para sua conta de armazenamento em que os logs do cofre de chaves são gravados, os barramentos de serviço criados anteriormente e o caminho específico para os logs de armazenamento do cofre de chaves.
>
>

A função obtém o arquivo de log mais recente da conta de armazenamento em que os logs do cofre de chaves são gravados, capta os eventos mais recentes do arquivo e os envia por push para uma fila do Barramento de Serviço. Como um único arquivo pode ter vários eventos, crie um arquivo sync.txt que a função também analisa para determinar o carimbo de data/hora do último evento que foi obtido. Isso garante que você não envie o mesmo evento por push várias vezes. Esse arquivo sync.txt contém um carimbo de data/hora para o último evento encontrado. Os logs, quando carregados, precisam ser classificados com base no carimbo de data/hora para garantir que estão classificados corretamente.

Para essa função, referenciamos algumas bibliotecas adicionais que não estão disponíveis pré-configuradas no Azure Functions. Para incluí-las, precisamos que o Azure Functions as realize o pull usando o NuGet. Escolha a opção **Exibir Arquivos**.

![Opção Exibir Arquivos](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Adicione um arquivo chamado project.json com o seguinte conteúdo:

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```
Após **Salvar**, as Funções do Azure baixarão os binários necessários.

Mude para a guia **Integrar** e atribua ao parâmetro do temporizador um nome significativo para ser usado na função. No código anterior, ele espera que o timer seja chamado de *myTimer*. Especifique uma [expressão CRON](../app-service/web-sites-create-web-jobs.md#CreateScheduledCRON) da seguinte maneira: 0 \* \* \* \* \* para o temporizador que fará com que a função seja executada uma vez por minuto.

Na mesma guia **Integrar**, adicione uma entrada do tipo **Armazenamento de Blobs do Azure**. Ela apontará para o arquivo sync.txt que contém o carimbo de data/hora do último evento analisado pela função. Isso será disponibilizado na função pelo nome do parâmetro. No código anterior, a entrada do Armazenamento de Blobs do Azure espera que o nome do parâmetro seja *inputBlob*. Escolha a conta de armazenamento em que o arquivo sync.txt residirá (pode ser a mesma ou outra conta de armazenamento). No campo do caminho, forneça o caminho em que o arquivo reside, no formato {nome-do-contêiner}/caminho/to/sync.txt.

Adicione uma saída do tipo *Armazenamento de Blobs do Azure*. Isso apontará para o arquivo sync.txt que você definiu na entrada. Ele é usado pela função para gravar o carimbo de data/hora do último evento analisado. O código anterior espera que esse parâmetro seja chamado de *outputBlob*.

Neste ponto, a função está pronta. Volte para a guia **Desenvolver** e salve o código. Verifique a janela de saída quanto a quaisquer erros de compilação e corrija-os adequadamente. Se o código for compilado, agora o código deverá verificar os logs do cofre de chaves a cada minuto e enviar os novos eventos para a fila do Barramento de Serviço definido. Você deve ver as informações de log gravadas na janela de log sempre que a função for disparada.

### <a name="azure-logic-app"></a>Aplicativo lógico do Azure
Em seguida, você deve criar um aplicativo lógico do Azure que obtém os eventos que a função envia à fila do Barramento de Serviço, analisa o conteúdo e envia um email com base em uma condição que está sendo correspondida.

[Crie um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md) indo até **Novo -> Aplicativo Lógico**.

Após o aplicativo lógico ser criado, navegue até ele e selecione **editar**. No editor do aplicativo lógico, escolha **Fila do Barramento de Serviço** e insira as credenciais do Barramento de Serviço para conectá-lo à fila.

![Barramento de Serviço de aplicativo lógico do Azure](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Depois, escolha **Adicionar uma condição**. Na condição, mude para o editor avançado e insira o seguinte código, substituindo o APP_ID pelo APP_ID real do aplicativo Web:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Essa expressão retornará essencialmente **false** se *appid* do evento recebido (que é o corpo da mensagem do Barramento de Serviço) não for a *appid* do aplicativo.

Agora, crie uma ação em **Se não, não faça nada**.

![Escolher ação do aplicativo lógico do Azure](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Para a ação, escolha **Office 365 - send email (Office 365 – enviar email)**. Preencha os campos para criar um email para enviar quando a condição definida retornar **false**. Se você não tiver o Office 365, poderá consultar alternativas para obter os mesmos resultados.

Nesse ponto, você tem um pipeline de ponta a ponta que procura novos logs de auditoria do cofre de chaves a cada minuto. Ele envia os novos logs que encontra para uma fila do barramento de serviço. O aplicativo lógico é disparado quando uma nova mensagem chega na fila. Se o *appid* no evento não corresponder à ID do aplicativo de chamada, ele enviará um email.
