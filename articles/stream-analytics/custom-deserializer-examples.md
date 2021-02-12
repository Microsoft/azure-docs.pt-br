---
title: Ler entrada em qualquer formato usando desserializadores .NET personalizados no Azure Stream Analytics
description: Este artigo explica o formato de serialização e as interfaces que definem desserializadores .NET personalizados para trabalhos de nuvem e borda do Azure Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 1/28/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 140a836882ad3abe048047120e4fe1ebc0a3067c
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018149"
---
# <a name="read-input-in-any-format-using-net-custom-deserializers"></a>Ler entradas em qualquer formato usando desserializadores .NET personalizados

Os desserializadores .NET personalizados permitem que o trabalho do Azure Stream Analytics leia dados de formatos fora dos três [formatos de dados internos](stream-analytics-parsing-json.md). Este artigo explica o formato de serialização e as interfaces que definem desserializadores .NET personalizados para trabalhos de nuvem e borda do Azure Stream Analytics. Também há exemplos de desserializadores para buffer de protocolo e formato CSV.

## <a name="net-custom-deserializer"></a>Desserializador .NET personalizado

Os exemplos de código a seguir são as interfaces que definem o desserializador personalizado e implementam o `StreamDeserializer<T>`.

`UserDefinedOperator` é a classe base para todos os operadores de streaming personalizados. Ele inicializa `StreamingContext`, que fornece contexto que inclui o mecanismo para a publicação de diagnósticos para os quais você precisará depurar qualquer problema com o desserializador.

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

O trecho de código a seguir é a desserialização para streaming de dados. 

Os erros ignoráveis devem ser emitidos usando `IStreamingDiagnostics` passado pelo método Initialize do `UserDefinedOperator`. Todas as exceções serão tratadas como erros, e o desserializador será recriado. Após um determinado número de erros, o trabalho vai para um status de falha.

`StreamDeserializer<T>` desserializa um fluxo em um objeto do tipo `T`. As seguintes condições devem ser atendidas:

1. T é uma classe ou struct.
1. Todos os campos públicos em T são
    1. Um de [sbyte, byte, short, ushort, int, uint, long, DateTime, string, float, double] ou seus equivalentes anuláveis.
    1. Outra struct ou classe seguindo as mesmas regras.
    1. Matriz do tipo `T2` que segue as mesmas regras.
    1. IList`T2` em que T2 segue as mesmas regras.
    1. Não tem nenhum tipo recursivo.

O parâmetro `stream` é o fluxo que contém o objeto serializado. `Deserialize` retorna um conjunto de instâncias de `T`.

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext` fornece o contexto que inclui o mecanismo de publicação de diagnóstico para o operador de usuário.

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics` é o diagnóstico para operadores definidos pelo usuário, incluindo serializador, desserializador e funções definidas pelo usuário.

`WriteError` grava uma mensagem de erro nos logs de recursos e envia o erro para diagnóstico.

`briefMessage` é uma breve mensagem de erro. Essa mensagem aparece no diagnóstico e é usada pela equipe do produto para fins de depuração. Não inclua informações confidenciais e mantenha a mensagem com menos de 200 caracteres

`detailedMessage` é uma mensagem de erro detalhada que só é adicionada aos logs de recursos em seu armazenamento. A mensagem deve ter menos de 2000 caracteres.

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>Exemplos de desserializador

Esta seção mostra como gravar desserializadores personalizados para Protobuf e CSV. Para ver exemplos adicionais, como o formato AVRO para o Event Hub Capture, visite [Azure Stream Analytics no GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

### <a name="protocol-buffer-protobuf-format"></a>Formato de buffer de protocolo (Protobuf)

Este é um exemplo que usa o formato de buffer de protocolo.

Suponha a seguinte definição de buffer de protocolo.

```proto
syntax = "proto3";
// protoc.exe from nuget "Google.Protobuf.Tools" is used to generate .cs file from this schema definition.
// Run below command to generate the csharp class
// protoc.exe --csharp_out=. MessageBodyProto.proto

package SimulatedTemperatureSensor;
message MessageBodyProto {
    message Ambient {
      double temperature = 1;
      int64 humidity = 2;
    }

    message Machine {
      double temperature = 1;
      double pressure = 2;
    }

    Machine machine = 1;
    Ambient ambient = 2;
    string timeCreated = 3;
}
```

A execução de `protoc.exe` do NuGet **Google.Protobuf.Tools** gera um arquivo .cs com a definição. O arquivo gerado não aparece aqui. Você deve garantir que a versão do Protobuf NuGet que você usa em seu projeto de Stream Analytics corresponda à versão do Protobuf usada para gerar a entrada. 

O trecho de código a seguir é a implementação do desserializador, supondo que o arquivo gerado esteja incluído no projeto. Essa implementação é apenas um wrapper fino no arquivo gerado.

```csharp
    public class MessageBodyDeserializer : StreamDeserializer<SimulatedTemperatureSensor.MessageBodyProto>
    {
        public override IEnumerable<SimulatedTemperatureSensor.MessageBodyProto> Deserialize(Stream stream)
        {
            while (stream.Position < stream.Length)
            {
                yield return SimulatedTemperatureSensor.MessageBodyProto.Parser.ParseDelimitedFrom(stream);
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
        }
    }
```

### <a name="csv"></a>CSV

O trecho de código a seguir é um desserializador CSV simples que também demonstra erros de propagação.

```csharp
using System.Collections.Generic;
using System.IO;

using Microsoft.Azure.StreamAnalytics;
using Microsoft.Azure.StreamAnalytics.Serialization;

namespace ExampleCustomCode.Serialization
{
    public class CustomCsvDeserializer : StreamDeserializer<CustomEvent>
    {
        private StreamingDiagnostics streamingDiagnostics;

        public override IEnumerable<CustomEvent> Deserialize(Stream stream)
        {
            using (var sr = new StreamReader(stream))
            {
                string line = sr.ReadLine();
                while (line != null)
                {
                    if (line.Length > 0 && !string.IsNullOrWhiteSpace(line))
                    {
                        string[] parts = line.Split(',');
                        if (parts.Length != 3)
                        {
                            streamingDiagnostics.WriteError("Did not get expected number of columns", $"Invalid line: {line}");
                        }
                        else
                        {
                            yield return new CustomEvent()
                            {
                                Column1 = parts[0],
                                Column2 = parts[1],
                                Column3 = parts[2]
                            };
                        }
                    }

                    line = sr.ReadLine();
                }
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
            this.streamingDiagnostics = streamingContext.Diagnostics;
        }
    }

    public class CustomEvent
    {
        public string Column1 { get; set; }

        public string Column2 { get; set; }

        public string Column3 { get; set; }
    }
}

```

## <a name="serialization-format-for-rest-apis"></a>Formato de serialização para APIs REST

Cada entrada do Azure Stream Analytics tem um **formato de serialização**. Para obter mais informações sobre opções de entrada, consulte a documentação [API REST de entrada](/rest/api/streamanalytics/2016-03-01/inputs).

O código JavaScript a seguir é um exemplo do formato de serialização do desserializador do .NET ao se usar a API REST:

```javascript
{    
   "properties":{    
      "type":"stream",  
      "serialization":{    
         "type":"CustomCLR",  
         "properties":{    
            "serializationDllPath":"<path to the dll inside UserCustomCode\CLR\ folder>", 
            "serializationClassName":"<Full name of the deserializer class name>" 
         }  
      }
   }  
}  
```

`serializationClassName` deve ser uma classe que implementa `StreamDeserializer<T>`. Isso é descrito na seção a seguir.

## <a name="region-support"></a>Suporte de regiões

Este recurso está disponível nas seguintes regiões:

* Centro-Oeste dos EUA
* Norte da Europa
* Leste dos EUA
* Oeste dos EUA
* Leste dos EUA 2
* Europa Ocidental

Você pode [solicitar suporte](https://aka.ms/ccodereqregion) para outras regiões.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>Quando esse recurso estará disponível em todas as regiões do Azure?

Esse recurso está disponível em [6 regiões](#region-support). Se você estiver interessado em usar essa funcionalidade em outra região, [envie uma solicitação](https://aka.ms/ccodereqregion). Estamos trabalhando para oferecer suporte a todas as regiões do Azure.

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>Posso acessar o MetadataPropertyValue de minhas entradas semelhantes à função GetMetadataPropertyValue?

Não há mais suporte para essa funcionalidade. Se você precisar desse recurso, vote nessa solicitação em [UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese).

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>Posso compartilhar minha implementação de desserializador com a comunidade para que outras pessoas se beneficiem?

Depois de implementar seu desserializador, você poderá ajudar outras pessoas compartilhando-o com a comunidade. Envie seu código para o [repositório do Azure Stream Analytics no GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

### <a name="what-are-the-other-limitations-of-using-custom-deserializers-in-stream-analytics"></a>Quais são as outras limitações do uso de desserializadores personalizados no Stream Analytics?

Se a entrada for do formato Protobuf com um esquema que contém o `MapField` tipo, você não poderá implementar um desserializador personalizado. Além disso, os desserializadores personalizados não dão suporte a dados de exemplo ou dados de visualização. 

## <a name="next-steps"></a>Próximas etapas

* [Desserializadores .NET personalizados para trabalhos de nuvem do Azure Stream Analytics](custom-deserializer.md)
