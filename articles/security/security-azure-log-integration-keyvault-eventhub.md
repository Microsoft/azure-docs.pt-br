---
title: Integrar logs do Azure Key Vault usando os Hubs de Eventos | Microsoft Docs
description: "Tutorial que fornece as etapas necessárias para disponibilizar os logs do Key Vault para um SIEM usando a integração de log do Azure"
services: security
author: barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.topic: article
ms.date: 08/07/2017
ms.author: Barclayn
ms.custom: AzLog
ms.openlocfilehash: 3cd80817bf8b2ef2f66e9942eddc186a3eb5b5e4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-log-integration-tutorial-process-azure-key-vault-events-by-using-event-hubs"></a>Tutorial de integração de log do Azure: processar eventos do Azure Key Vault usando Hubs de Eventos

Você pode usar a Integração de Log do Azure para recuperar eventos registrados e disponibilizá-los ao seu SIEM (sistema de informações de segurança e gerenciamento de evento). Este tutorial mostra um exemplo de como a Integração de Log do Azure pode ser usada para processar os logs adquiridos por meio de Hubs de Eventos do Azure.
 
Use este tutorial para se familiarizar com a forma como a Integração de Log do Azure e os Hubs de Eventos funcionam juntos, seguindo as etapas de exemplo e assim entender como cada etapa dá suporte à solução. Em seguida, você poderá pegar o que aprendeu aqui para criar suas próprias etapas para dar suporte a requisitos exclusivos da sua empresa.

>[!WARNING]
As etapas e comandos neste tutorial não devem ser copiados e colados. Elas são apenas exemplos. Não use os comandos do PowerShell "no estado em que se encontram" em seu ambiente dinâmico. Você deve personalizá-los com base em seu ambiente exclusivo.


Este tutorial orienta você no processo de fazer com que a atividade do Azure Key Vault seja registrada em um hub de eventos e disponibilizada como arquivos JSON para o seu sistema SIEM. Em seguida, você pode configurar o sistema SIEM para processar os arquivos JSON.

>[!NOTE]
>A maioria das etapas deste tutorial envolve a configuração de cofres de chaves, contas de armazenamento e hubs de eventos. As etapas específicas da integração de log do Azure estão no final deste tutorial. Não execute estas etapas em um ambiente de produção. Elas servem apenas para um ambiente de laboratório. Personalize-as antes de usá-las em produção.

As informações fornecidas ao longo do processo ajudam você a entender as razões por trás de cada etapa. Links para outros artigos fornecem mais detalhes sobre determinados tópicos.

Para obter mais informações sobre os serviços que este tutorial menciona, consulte: 

- [Cofre da Chave do Azure](../key-vault/key-vault-whatis.md)
- [Hubs de Eventos do Azure](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Integração de log do Azure](security-azure-log-integration-overview.md)


## <a name="initial-setup"></a>Configuração inicial

Antes de concluir as etapas neste artigo, você precisará do seguinte:

1. Uma assinatura do Azure e uma conta nessa assinatura com direitos de administrador. Se você não tem uma assinatura, você pode criar um [conta gratuita](https://azure.microsoft.com/free/).
 
2. Um sistema com acesso à Internet que atenda aos requisitos para a instalação da integração de log do Azure. O sistema pode estar em um serviço de nuvem ou hospedado localmente.

3. [Integração de log do Azure](https://www.microsoft.com/download/details.aspx?id=53324) instalada. Para instalá-la:

   a. Use a Área de Trabalho Remota para conectar-se ao sistema mencionado na etapa 2.   
   b. Copie o instalador da integração de log do Azure no sistema. Você pode [baixar os arquivos de instalação](https://www.microsoft.com/download/details.aspx?id=53324).   
   c. Inicie o instalador e aceite os Termos de Licença para Software Microsoft.   
   d. Se você vai fornecer informações de telemetria, deixe a caixa de seleção marcada. Se você preferir não enviar informações de uso à Microsoft, desmarque a caixa de seleção.
   
   Para obter mais informações sobre a integração de log do Azure e como instalá-la, consulte [Integração de Log do Azure com log de Diagnóstico do Azure e Encaminhamento de Eventos do Windows](security-azure-log-integration-get-started.md).

4. A versão mais recente do PowerShell.
 
   Se você tem o Windows Server 2016 instalado, você tem, no mínimo, o PowerShell 5.0. Se estiver usando outra versão do Windows Server, pode ser que você tenha uma versão anterior do PowerShell instalada. Você pode verificar a versão digitando ```get-host``` em uma janela do PowerShell. Se você não tiver o PowerShell 5.0 instalado, você poderá [baixá-lo](https://www.microsoft.com/download/details.aspx?id=50395).

   Depois de ter pelo menos o PowerShell 5.0, você continuar para instalar a versão mais recente:
   
   a. Em uma janela do PowerShell, insira o comando ```Install-Module Azure```. Conclua as etapas de instalação.    
   b. Insira o comando ```Install-Module AzureRM```. Conclua as etapas de instalação.

   Para obter mais informações, consulte [Instalar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0).


## <a name="create-supporting-infrastructure-elements"></a>Criar elementos de suporte da infraestrutura

1. Abra uma janela do PowerShell com privilégios elevados e acesse **c:\Program Files\Microsoft Azure Log Integration**.
2. Importe os cmdlets do AzLog executando o script LoadAzLogModule.ps1. Insira o comando `.\LoadAzLogModule.ps1`. (Observe o ".\" nesse comando.) Você deverá ver algo assim:</br>

   ![Lista de módulos carregados](./media/security-azure-log-integration-keyvault-eventhub/loaded-modules.png)

3. Insira o comando `Login-AzureRmAccount`. Na janela de logon, insira as informações de credenciais da assinatura que você usará para este tutorial.

   >[!NOTE]
   >Se esta for a primeira vez que você está fazendo logon no Azure neste computador, você verá uma mensagem sobre permitir que a Microsoft colete dados de uso do PowerShell. É recomendável que você habilite essa coleta de dados porque ela será usada para melhorar o Azure PowerShell.

4. Após a autenticação bem-sucedida, você estará conectado e verá as informações da seguinte captura de tela. Anote a ID da assinatura e o nome da assinatura, porque eles são necessários para concluir etapas posteriores.

   ![Janela do PowerShell](./media/security-azure-log-integration-keyvault-eventhub/login-azurermaccount.png)
5. Crie variáveis para armazenar valores que serão usados posteriormente. Insira cada uma das seguintes opções nas linhas do PowerShell. Talvez seja necessário ajustar os valores de acordo com seu ambiente.
    - ```$subscriptionName = ‘Visual Studio Ultimate with MSDN’``` (O nome da sua assinatura pode ser diferente. Você pode ver isso como parte da saída do comando anterior.)
    - ```$location = 'West US'``` (Essa variável será usada para passar o local em que os recursos devem ser criados. Você pode alterar essa variável para qualquer outro local da sua escolha.)
    - ```$random = Get-Random```
    - ``` $name = 'azlogtest' + $random``` (O nome pode ser qualquer um, contanto que tenha apenas letras minúsculas e números).
    - ``` $storageName = $name``` (Essa variável será usada para o nome da conta de armazenamento).
    - ```$rgname = $name ``` (Essa variável será usada para o nome do grupo de recursos).
    - ``` $eventHubNameSpaceName = $name``` (Esse é o nome do namespace do hub de eventos).
6. Especifique a assinatura com a qual você trabalhará:
    
    ```Select-AzureRmSubscription -SubscriptionName $subscriptionName```
7. Crie um grupo de recursos:
    
    ```$rg = New-AzureRmResourceGroup -Name $rgname -Location $location```
    
   Se inserir `$rg` neste momento, você verá uma saída semelhante à essa captura de tela:

   ![Saída após a criação de um grupo de recursos](./media/security-azure-log-integration-keyvault-eventhub/create-rg.png)
8. Crie uma conta de armazenamento que será usada para manter controle sobre as informações de estado:
    
    ```$storage = New-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
9. Crie o namespace do hub de eventos. Isso é necessário para criar um hub de eventos.
    
    ```$eventHubNameSpace = New-AzureRmEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
10. Obtenha a ID da regra que será usada com o provedor de informações:
    
    ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey' ```
11. Obtenha todos os possíveis locais do Azure e adicione os nomes a uma variável que possa ser usada em uma etapa posterior:
    
    a. ```$locationObjects = Get-AzureRMLocation```    
    b. ```$locations = @('global') + $locationobjects.location```
    
    Se você inserir `$locations` nesse momento, verá os nomes de local sem as informações adicionais retornadas pelo Get-AzureRmLocation.
12. Criar um perfil de log do Azure Resource Manager: 
    
    ```Add-AzureRmLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```
    
    Para obter mais informações sobre o perfil de log do Azure, consulte [Visão geral do Log de atividades do Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

> [!NOTE]
> Você poderá obter uma mensagem de erro ao tentar criar um perfil de log. Em seguida, você poderá examinar a documentação para o Get-AzureRmLogProfile e o Remove-AzureRmLogProfile. Se você executar o Get-AzureRmLogProfile, verá as informações sobre o perfil de log. Você pode excluir o perfil de log existente inserindo o comando ```Remove-AzureRmLogProfile -name 'Log Profile Name' ```.
>
>![Erro de perfil do Resource Manager](./media/security-azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="create-a-key-vault"></a>Criar um cofre de chave

1. Crie o cofre de chaves:

   ```$kv = New-AzureRmKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location ```

2. Configure o registro em log para o cofre de chaves:

   ```Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true ```

## <a name="generate-log-activity"></a>Gerar atividade de log

As solicitações precisam ser enviados para o Key Vault para gerar a atividade de log. Ações como geração de chaves, armazenamento de segredos ou leitura de segredos do Key Vault criarão entradas de log.

1. Exibir as chaves de armazenamento atuais:
    
   ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
2. Gerar uma nova **key2**:
    
   ```New-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
3. Exibir as chaves novamente e ver que **key2** tem um valor diferente:
    
   ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
4. Definir e ler um segredo para gerar entradas de log adicionais:
    
   a. ```Set-AzureKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)``` b. ```(Get-AzureKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

   ![Segredo retornado](./media/security-azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configure-azure-log-integration"></a>Configurar a integração de log do Azure

Agora que você configurou todos os elementos necessários para que o Key Vault registre em log em um hub de eventos, você precisa configurar a integração de log do Azure:

1. ```$storage = Get-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename```
2. ```$eventHubKey = Get-AzureRmEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
3. ```$storagekeys = Get-AzureRmStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
4. ``` $storagekey = $storagekeys[0].Value```

Execute o comando AzLog para cada hub de eventos:

1. ```$eventhubs = Get-AzureRmEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
2. ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

Após um minuto ou mais de execução dos dois últimos comandos, você verá os arquivos JSON sendo gerados. Você pode confirmar isso pelo monitoramento do diretório **C:\users\AzLog\EventHubJson**.

## <a name="next-steps"></a>Próximas etapas

- [Perguntas frequentes sobre a integração de log do Azure](security-azure-log-integration-faq.md)
- [Introdução à integração de log do Azure](security-azure-log-integration-get-started.md)
- [Integrar logs de recursos do Azure nos seus sistemas SIEM](security-azure-log-integration-overview.md)
