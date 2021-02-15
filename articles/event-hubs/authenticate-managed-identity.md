---
title: Autenticação de uma identidade gerenciada com o Azure Active Directory
description: Este artigo fornece informações sobre como autenticar uma identidade gerenciada com Azure Active Directory para acessar os recursos dos hubs de eventos do Azure
ms.topic: conceptual
ms.date: 01/25/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 2070cfd94b39a08afb86ffd3579f1116faac72d5
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98805277"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>Autenticar uma identidade gerenciada com Azure Active Directory para acessar recursos de hubs de eventos
Os hubs de eventos do Azure dão suporte à autenticação Azure Active Directory (Azure AD) com [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md). Identidades gerenciadas para recursos do Azure podem autorizar o acesso a recursos de hubs de eventos usando as credenciais do Azure AD de aplicativos em execução em VMs (máquinas virtuais) do Azure, aplicativos de funções, conjuntos de dimensionamento de máquinas virtuais e outros serviços. Usando identidades gerenciadas para recursos do Azure junto com a autenticação do Azure AD, você pode evitar o armazenamento de credenciais com seus aplicativos que são executados na nuvem.

Este artigo mostra como autorizar o acesso a um hub de eventos usando uma identidade gerenciada de uma VM do Azure.

## <a name="enable-managed-identities-on-a-vm"></a>Habilitar identidades gerenciadas em uma VM
Antes de poder usar identidades gerenciadas para recursos do Azure para autorizar recursos de hubs de eventos de sua VM, você deve primeiro habilitar identidades gerenciadas para recursos do Azure na VM. Para saber como habilitar identidades gerenciadas para Recursos do Azure, confira um dos seguintes artigos:

- [Azure portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [PowerShell do Azure](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [CLI do Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modelo do Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Bibliotecas de cliente Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Conceder permissões a uma identidade gerenciada no Azure AD
Para autorizar uma solicitação ao serviço de hubs de eventos de uma identidade gerenciada em seu aplicativo, primeiro configure as configurações do Azure RBAC (controle de acesso baseado em função) para essa identidade gerenciada. Os hubs de eventos do Azure definem funções do Azure que abrangem permissões para enviar e ler de hubs de eventos. Quando a função do Azure é atribuída a uma identidade gerenciada, a identidade gerenciada recebe acesso aos dados dos hubs de eventos no escopo apropriado.

Para obter mais informações sobre como atribuir funções do Azure, consulte [autenticar com Azure Active Directory para acessar os recursos dos hubs de eventos](authorize-access-azure-active-directory.md).

## <a name="use-event-hubs-with-managed-identities"></a>Usar os Hubs de Eventos com identidades gerenciadas
Para usar os hubs de eventos com identidades gerenciadas, você precisa atribuir a função e o escopo apropriado à identidade. O procedimento nesta seção usa um aplicativo simples que é executado sob uma identidade gerenciada e acessa recursos de hubs de eventos.

Aqui, estamos usando um aplicativo Web de exemplo hospedado no [serviço Azure app](https://azure.microsoft.com/services/app-service/). Para obter as instruções passo a passo para criar um aplicativo Web, consulte [criar um ASP.NET Core aplicativo Web no Azure](../app-service/quickstart-dotnetcore.md)

Depois que o aplicativo for criado, siga estas etapas: 

1. Vá para **configurações** e selecione **identidade**. 
1. Selecione o **status** a ser **ativado**. 
1. Clique em **Salvar** para salvar a configuração. 

    :::image type="content" source="./media/authenticate-managed-identity/identity-web-app.png" alt-text="Identidade gerenciada para um aplicativo Web":::
4. Selecione **Sim** na mensagem de informações. 

    Depois de habilitar essa configuração, uma nova identidade de serviço será criada em seu Azure Active Directory (AD do Azure) e configurada no host do serviço de aplicativo.

    Agora, atribua essa identidade de serviço a uma função no escopo necessário em seus recursos de hubs de eventos.

### <a name="to-assign-azure-roles-using-the-azure-portal"></a>Para atribuir funções do Azure usando o portal do Azure
Para atribuir uma função aos recursos dos hubs de eventos, navegue até esse recurso na portal do Azure. Exiba as configurações de controle de acesso (IAM) para o recurso e siga estas instruções para gerenciar as atribuições de função:

> [!NOTE]
> As etapas a seguir atribui uma função de identidade de serviço aos seus namespaces de hubs de eventos. Você pode seguir as mesmas etapas para atribuir uma função com escopo a qualquer recurso de hubs de eventos. 

1. Na portal do Azure, navegue até o namespace de seus hubs de eventos e exiba a **visão geral** do namespace. 
1. Selecione **controle de acesso (iam)** no menu à esquerda para exibir as configurações de controle de acesso para o Hub de eventos.
1.  Selecione a guia **Atribuições de função** para ver as atribuições de função atuais.
3.  Selecione **Adicionar** e, em seguida, selecione **Adicionar atribuição de função** _.
4.  Na página _ *Adicionar atribuição de função**, siga estas etapas:
    1. Para **função**, selecione a função de hubs de eventos que você deseja atribuir. Neste exemplo, é o **proprietário dos dados dos hubs de eventos do Azure**.
    1. Para o campo **atribuir acesso a** , selecione **serviço de aplicativo** em **identidade gerenciada atribuída pelo sistema**. 
    1. Selecione a **assinatura** na qual a identidade gerenciada para o aplicativo Web foi criada.
    1. Selecione a **identidade gerenciada** para o aplicativo Web que você criou. O nome padrão da identidade é o mesmo que o nome do aplicativo Web. 
    1. Em seguida, selecione **Salvar**. 
    
        ![Página Adicionar atribuição de função](./media/authenticate-managed-identity/add-role-assignment-page.png)

    Depois de atribuir a função, o aplicativo Web terá acesso aos recursos dos hubs de eventos no escopo definido. 

    > [!NOTE]
    > Para obter uma lista de serviços que dão suporte a identidades gerenciadas, consulte [serviços que dão suporte a identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

### <a name="test-the-web-application"></a>Testar o aplicativo Web
1. Criar um namespace de Hubs de Eventos e um hub de eventos. 
2. Implante o aplicativo Web no Azure. Consulte a seguinte seção com guias para obter links para o aplicativo Web no GitHub. 
3. Verifique se o SendReceive. aspx está definido como o documento padrão para o aplicativo Web. 
3. Habilite a **identidade** para o aplicativo Web. 
4. Atribua essa identidade à função de **proprietário de dados dos hubs de eventos** no nível do namespace ou no nível do hub de eventos. 
5. Execute o aplicativo Web, insira o nome do namespace e o nome do hub de eventos, uma mensagem e selecione **Enviar**. Para receber o evento, selecione **receber**. 

#### <a name="azuremessagingeventhubs-latest"></a>[Azure. Messaging. EventHubs (mais recente)](#tab/latest)
Agora você pode iniciar o aplicativo Web e apontar seu navegador para a página aspx de exemplo. Você pode encontrar o aplicativo Web de exemplo que envia e recebe dados de recursos de hubs de eventos no [repositório GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp).

Instale o pacote mais recente do [NuGet](https://www.nuget.org/packages/Azure.Messaging.EventHubs/)e comece a enviar eventos para os hubs de eventos usando **EventHubProducerClient** e recebendo eventos usando o **EventHubConsumerClient**. 

> [!NOTE]
> Para um exemplo de Java que usa uma identidade gerenciada para publicar eventos em um hub de eventos, consulte [Publicar eventos com o exemplo de identidade do Azure no GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs).

```csharp
protected async void btnSend_Click(object sender, EventArgs e)
{
    await using (EventHubProducerClient producerClient = new EventHubProducerClient(txtNamespace.Text, txtEventHub.Text, new DefaultAzureCredential()))
    {
        // create a batch
        using (EventDataBatch eventBatch = await producerClient.CreateBatchAsync())
        {

            // add events to the batch. only one in this case. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes(txtData.Text)));

            // send the batch to the event hub
            await producerClient.SendAsync(eventBatch);
        }

        txtOutput.Text = $"{DateTime.Now} - SENT{Environment.NewLine}{txtOutput.Text}";
    }
}
protected async void btnReceive_Click(object sender, EventArgs e)
{
    await using (var consumerClient = new EventHubConsumerClient(EventHubConsumerClient.DefaultConsumerGroupName, $"{txtNamespace.Text}.servicebus.windows.net", txtEventHub.Text, new DefaultAzureCredential()))
    {
        int eventsRead = 0;
        try
        {
            using CancellationTokenSource cancellationSource = new CancellationTokenSource();
            cancellationSource.CancelAfter(TimeSpan.FromSeconds(5));

            await foreach (PartitionEvent partitionEvent in consumerClient.ReadEventsAsync(cancellationSource.Token))
            {
                txtOutput.Text = $"Event Read: { Encoding.UTF8.GetString(partitionEvent.Data.Body.ToArray()) }{ Environment.NewLine}" + txtOutput.Text;
                eventsRead++;
            }
        }
        catch (TaskCanceledException ex)
        {
            txtOutput.Text = $"Number of events read: {eventsRead}{ Environment.NewLine}" + txtOutput.Text;
        }
    }
}
```

#### <a name="microsoftazureeventhubs-legacy"></a>[Microsoft.Azure.EventHubs (herdado)](#tab/old)
Agora você pode iniciar o aplicativo Web e apontar seu navegador para a página aspx de exemplo. Você pode encontrar o aplicativo Web de exemplo que envia e recebe dados de recursos de hubs de eventos no [repositório GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp).

Instale o pacote mais recente do [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)e comece a enviar e receber dados de hubs de eventos usando o EventHubClient, conforme mostrado no código a seguir: 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```
---

## <a name="event-hubs-for-kafka"></a>Hubs de Eventos para Kafka
Você pode usar Apache Kafka aplicativos para enviar e receber mensagens de hubs de eventos do Azure usando a identidade gerenciada OAuth. Consulte o exemplo a seguir no GitHub: [hubs de eventos para Kafka-enviar e receber mensagens usando a identidade gerenciada OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity).

## <a name="samples"></a>Exemplos
- Exemplos **do Azure. Messaging. EventHubs**
    - [.NET](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Exemplos de Microsoft. Azure. EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Esses exemplos usam a antiga biblioteca **Microsoft. Azure. EventHubs** , mas você pode atualizá-lo facilmente para usar a biblioteca **Azure. Messaging. EventHubs** mais recente. Para mover o exemplo do usando a biblioteca antiga para uma nova, consulte o [guia para migrar de Microsoft. Azure. EventHubs para Azure. Messaging. EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
    Este exemplo foi atualizado para usar a biblioteca **Azure. Messaging. EventHubs** mais recente.
- [Hubs de eventos para Kafka-enviar e receber mensagens usando o OAuth de identidade gerenciada](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity)


## <a name="next-steps"></a>Próximas etapas
- Consulte o seguinte artigo para saber mais sobre identidades gerenciadas para recursos do Azure: [o que são identidades gerenciadas para recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)
- Consulte os seguintes artigos relacionados:
    - [Autenticar solicitações para hubs de eventos do Azure por meio de um aplicativo usando Azure Active Directory](authenticate-application.md)
    - [Autenticar solicitações para hubs de eventos do Azure usando assinaturas de acesso compartilhado](authenticate-shared-access-signature.md)
    - [Autorizar o acesso aos recursos de hubs de eventos usando Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Autorizar o acesso a recursos de hubs de eventos usando assinaturas de acesso compartilhado](authorize-access-shared-access-signature.md)
