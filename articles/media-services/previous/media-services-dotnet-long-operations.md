---
title: Operações de Sondagem de Longa Execução | Microsoft Docs
description: Este tópico mostra como sondar as operações de longa execução.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 9a68c4b1-6159-42fe-9439-a3661a90ae03
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 752c502268ef53d3c0575d92e75ce6a965fccd9f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61464973"
---
# <a name="delivering-live-streaming-with-azure-media-services"></a>Fornecendo mídia sob demanda com os Serviços de Mídia do Azure

## <a name="overview"></a>Visão geral

Os Serviços de Mídia do Microsoft Azure oferecem APIs que enviam solicitações aos Serviços de Mídia para iniciar operações (por exemplo: criar, iniciar, interromper ou excluir um canal). Essas operações são de execução longa.

O SDK .NET de Serviços de Mídia fornece APIs que enviam a solicitação e aguardam a conclusão da operação (internamente, as APIs realizam a sondagem do andamento da operação em intervalos). Por exemplo, quando você chama o channel.Start(), o método retorna depois que o canal é iniciado. Você também pode usar a versão assíncrona: await channel.StartAsync() (para obter informações sobre o padrão assíncrono baseado em tarefa, consulte [TAP](https://msdn.microsoft.com/library/hh873175\(v=vs.110\).aspx)). "Métodos de sondagem" é como são chamadas as APIs que enviam uma solicitação de operação e então sondam o status até que a operação seja concluída. Esses métodos (especialmente a versão assíncrona) são recomendados para aplicativos cliente sofisticados e/ou serviços com monitoração de estado.

Existem cenários em que um aplicativo não pode esperar uma solicitação http de execução longa e desejar sondar o andamento da operação manualmente. Um exemplo típico seria um navegador interagindo com um serviço Web sem monitoração de estado: quando o navegador solicita a criação de um canal, o serviço Web inicia uma operação de execução longa e retorna a ID da operação para o navegador. O navegador pode pedir ao serviço Web para obter o status da operação com base na ID. O SDK .NET dos Serviços de Mídia fornece APIs úteis para esse cenário. Essas APIs são chamadas de "métodos sem sondagem".
Os "métodos sem sondagem" têm o seguinte padrão de nomenclatura: Envie a Operação *NomeOperação* (por exemplo, SendCreateOperation). Os métodos Send*OperationName*Operation retornam o objeto **IOperation** ; o objeto retornado contém informações que podem ser usadas para controlar a operação. O envio*OperationName*métodos OperationAsync retornam **tarefa\<IOperation >**.

Atualmente, as classes a seguir dão suporte aos métodos sem sondagem:  **Channel**, **StreamingEndpoint** e **Program**.

Para monitorar o status da operação, use o método **GetOperation** na classe **OperationBaseCollection**. Use os seguintes intervalos para verificar o status da operação: para as operações **Channel** e **StreamingEndpoint**, use 30 segundos; para operações **Program**, use 10 segundos.

## <a name="create-and-configure-a-visual-studio-project"></a>Criar e configurar um projeto do Visual Studio

Configure seu ambiente de desenvolvimento e preencha o arquivo de configuração app.config com as informações de conexão, conforme descrito em [Desenvolvimento de Serviços de Mídia com o .NET](media-services-dotnet-how-to-use.md).

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

