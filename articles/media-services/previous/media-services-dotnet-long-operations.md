---
title: Operações de Sondagem de Longa Execução | Microsoft Docs
description: Os serviços de mídia do Azure oferecem APIs que enviam solicitações aos serviços de mídia para iniciar operações (por exemplo, criar, iniciar, parar ou excluir um canal), essas operações são de execução longa. Este tópico mostra como sondar as operações de longa execução.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 7926f7aaa427d49d13cab5e13f5153bcd22e5898
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103013805"
---
# <a name="delivering-live-streaming-with-azure-media-services"></a>Fornecendo mídia sob demanda com os Serviços de Mídia do Azure

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Visão geral

Os Serviços de Mídia do Microsoft Azure oferecem APIs que enviam solicitações aos Serviços de Mídia para iniciar operações (por exemplo: criar, iniciar, interromper ou excluir um canal). Essas operações são de execução longa.

O SDK .NET de Serviços de Mídia fornece APIs que enviam a solicitação e aguardam a conclusão da operação (internamente, as APIs realizam a sondagem do andamento da operação em intervalos). Por exemplo, quando você chama o channel.Start(), o método retorna depois que o canal é iniciado. Você também pode usar a versão assíncrona: await channel.StartAsync() (para obter informações sobre o padrão assíncrono baseado em tarefa, consulte [TAP](./media-services-mes-schema.md)). "Métodos de sondagem" é como são chamadas as APIs que enviam uma solicitação de operação e então sondam o status até que a operação seja concluída. Esses métodos (especialmente a versão assíncrona) são recomendados para aplicativos cliente sofisticados e/ou serviços com monitoração de estado.

Existem cenários em que um aplicativo não pode esperar uma solicitação http de execução longa e desejar sondar o andamento da operação manualmente. Um exemplo típico seria um navegador interagindo com um serviço Web sem monitoração de estado: quando o navegador solicita a criação de um canal, o serviço Web inicia uma operação de execução longa e retorna a ID da operação para o navegador. O navegador pode pedir ao serviço Web para obter o status da operação com base na ID. O SDK .NET dos Serviços de Mídia fornece APIs úteis para esse cenário. Essas APIs são chamadas de "métodos sem sondagem".
Os "métodos sem sondagem" têm o padrão de nomenclatura a seguir: Send *OperationName* Operation (por exemplo, SendCreateOperation). Os métodos Send *OperationName* Operation retornam o objeto **IOperation** ; o objeto retornado contém informações que podem ser usadas para controlar a operação. Os métodos Send *OperationName* OperationAsync retornam **Task\<IOperation>**.

Atualmente, as classes a seguir dão suporte a métodos sem sondagem: **Channel**, **StreamingEndpoint** e **Program**.

Para monitorar o status da operação, use o método **GetOperation** na classe **OperationBaseCollection**. Use os seguintes intervalos para verificar o status da operação: para as operações **Channel** e **StreamingEndpoint**, use 30 segundos; para operações **Program**, use 10 segundos.

## <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto do Visual Studio

Configure seu ambiente de desenvolvimento e preencha o arquivo de app.config com informações de conexão, conforme descrito em [desenvolvimento de serviços de mídia com o .net](media-services-dotnet-how-to-use.md).

## <a name="example"></a>Exemplo

O exemplo a seguir define uma classe chamada **ChannelOperations**. Essa definição de classe pode ser um ponto de partida para a definição de classe de serviço Web. Para simplificar, os exemplos a seguir usam as versões de métodos não assíncronas.

O exemplo também mostra como o cliente pode usar essa classe.

### <a name="channeloperations-class-definition"></a>Definição da classe ChannelOperations

```csharp
using Microsoft.WindowsAzure.MediaServices.Client;
using System;
using System.Collections.Generic;
using System.Configuration;
using System.Net;

/// <summary> 
/// The ChannelOperations class only implements 
/// the Channel’s creation operation. 
/// </summary> 
public class ChannelOperations
{
    // Read values from the App.config file.
    private static readonly string _AADTenantDomain =
        ConfigurationManager.AppSettings["AMSAADTenantDomain"];
    private static readonly string _RESTAPIEndpoint =
        ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
    private static readonly string _AMSClientId =
        ConfigurationManager.AppSettings["AMSClientId"];
    private static readonly string _AMSClientSecret =
        ConfigurationManager.AppSettings["AMSClientSecret"];

    // Field for service context.
    private static CloudMediaContext _context = null;

    public ChannelOperations()
    {
        AzureAdTokenCredentials tokenCredentials = 
            new AzureAdTokenCredentials(_AADTenantDomain,
                new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                AzureEnvironments.AzureCloudEnvironment);

        var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
    }

    /// <summary>  
    /// Initiates the creation of a new channel.  
    /// </summary>  
    /// <param name="channelName">Name to be given to the new channel</param>  
    /// <returns>  
    /// Operation Id for the long running operation being executed by Media Services. 
    /// Use this operation Id to poll for the channel creation status. 
    /// </returns> 
    public string StartChannelCreation(string channelName)
    {
        var operation = _context.Channels.SendCreateOperation(
            new ChannelCreationOptions
            {
                Name = channelName,
                Input = CreateChannelInput(),
                Preview = CreateChannelPreview(),
                Output = CreateChannelOutput()
            });

        return operation.Id;
    }

    /// <summary> 
    /// Checks if the operation has been completed. 
    /// If the operation succeeded, the created channel Id is returned in the out parameter.
    /// </summary> 
    /// <param name="operationId">The operation Id.</param> 
    /// <param name="channel">
    /// If the operation succeeded, 
    /// the created channel Id is returned in the out parameter.</param>
    /// <returns>Returns false if the operation is still in progress; otherwise, true.</returns> 
    public bool IsCompleted(string operationId, out string channelId)
    {
        IOperation operation = _context.Operations.GetOperation(operationId);
        bool completed = false;

        channelId = null;

        switch (operation.State)
        {
            case OperationState.Failed:
                // Handle the failure. 
                // For example, throw an exception. 
                // Use the following information in the exception: operationId, operation.ErrorMessage.
                break;
            case OperationState.Succeeded:
                completed = true;
                channelId = operation.TargetEntityId;
                break;
            case OperationState.InProgress:
                completed = false;
                break;
        }
        return completed;
    }

    private static ChannelInput CreateChannelInput()
    {
        return new ChannelInput
        {
            StreamingProtocol = StreamingProtocol.RTMP,
            AccessControl = new ChannelAccessControl
            {
                IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelInput001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
            }
        };
    }

    private static ChannelPreview CreateChannelPreview()
    {
        return new ChannelPreview
        {
            AccessControl = new ChannelAccessControl
            {
                IPAllowList = new List<IPRange>
                    {
                        new IPRange
                        {
                            Name = "TestChannelPreview001",
                            Address = IPAddress.Parse("0.0.0.0"),
                            SubnetPrefixLength = 0
                        }
                    }
            }
        };
    }

    private static ChannelOutput CreateChannelOutput()
    {
        return new ChannelOutput
        {
            Hls = new ChannelOutputHls { FragmentsPerSegment = 1 }
        };
    }
}
```

### <a name="the-client-code"></a>O código do cliente

```csharp
ChannelOperations channelOperations = new ChannelOperations();
string opId = channelOperations.StartChannelCreation("MyChannel001");

string channelId = null;
bool isCompleted = false;

while (isCompleted == false)
{
    System.Threading.Thread.Sleep(TimeSpan.FromSeconds(30));
    isCompleted = channelOperations.IsCompleted(opId, out channelId);
}

// If we got here, we should have the newly created channel id.
Console.WriteLine(channelId);
```

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]