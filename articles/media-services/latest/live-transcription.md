---
Título: transcrição ao vivo: descrição dos serviços de mídia do Azure: Saiba mais sobre a transcrição ao vivo dos serviços de mídia do Azure.  
serviços: Media-Services documentationcenter: ' ' autor: IngridAtMicrosoft gerente: femila editor: ' ' MS. Service: Media-Services MS. Workload: Media ms.tgt_pltfrm: na MS. DevLang: NE MS. Topic: como-to MS. Date: 08/31/2020 MS. Author: inhenkel

---

# <a name="live-transcription-preview"></a>Transcrição ao vivo (versão prévia)

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

O serviço de mídia do Azure fornece vídeo, áudio e texto em diferentes protocolos. Quando você publica sua transmissão ao vivo usando MPEG-DASH ou HLS/CMAF, juntamente com vídeo e áudio, nosso serviço entrega o texto transcrevedo no TTML compatível com IMSC 1.1. A entrega é empacotada nos fragmentos de MPEG-4, parte 30 (ISO/IEC 14496-30). Se estiver usando a entrega via HLS/TS, o texto será entregue como um VTT em bloco.

Encargos adicionais se aplicam quando a transcrição dinâmica está ativada. Examine as informações de preços na seção vídeo ao vivo da [página de preços dos serviços de mídia](https://azure.microsoft.com/pricing/details/media-services/).

Este artigo descreve como habilitar a transcrição ao vivo ao transmitir um evento ao vivo com os serviços de mídia do Azure. Antes de continuar, verifique se você está familiarizado com o uso das APIs REST dos serviços de mídia v3 (consulte [este tutorial](stream-files-tutorial-with-rest.md) para obter detalhes). Você também deve estar familiarizado com o conceito de [transmissão ao vivo](live-streaming-overview.md) . É recomendável concluir o tutorial do [Stream Live com os serviços de mídia](stream-live-tutorial-with-api.md) .

## <a name="live-transcription-preview-regions-and-languages"></a>Regiões e idiomas de visualização de transcrição dinâmica

A transcrição ao vivo está disponível nas seguintes regiões:

- Sudeste Asiático
- Europa Ocidental
- Norte da Europa
- Leste dos EUA
- Centro dos EUA
- Centro-Sul dos Estados Unidos
- Oeste dos EUA 2
- Brazil South

Esta é a lista de idiomas disponíveis que podem ser transcritas, use o código de idioma na API.

| Linguagem | Código de idioma |
| -------- | ------------- |
| Catalão  | ca-ES |
| Dinamarquês (Dinamarca) | da-DK |
| Alemão (Alemanha) | de-DE |
| Inglês (Austrália) | en-AU |
| Inglês (Canadá) | en-CA |
| Inglês (Reino Unido) | en-GB |
| Inglês (Índia) | en-IN |
| Inglês (Nova Zelândia) | en-NZ |
| Inglês (Estados Unidos) | pt-BR |
| Espanhol (Espanha) | es-ES |
| Espanhol (México) | es-MX |
| Finlandês (Finlândia) | fi-FI |
| Francês (Canadá) | fr-CA |
| Francês (França) | fr-FR |
| Italiano (Itália) | it-IT |
| Holandês (Países Baixos) | nl-NL |
| Português (Brasil) | pt-BR |
| Português (Portugal) | pt-PT |
| Sueco (Suécia) | sv-SE |

## <a name="create-the-live-event-with-live-transcription"></a>Criar o evento ao vivo com a transcrição ao vivo

Para criar um evento ao vivo com a transcrição ativada, envie a operação PUT com a versão da API 2019-05-01-Preview, por exemplo:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

A operação tem o seguinte corpo (em que um evento de passagem ao vivo é criado com RTMP como o protocolo de ingestão). Observe a adição de uma propriedade transcrições.

```
{
  "properties": {
    "description": "Demonstrate how to enable live transcriptions",
    "input": {
      "streamingProtocol": "RTMP",
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "preview": {
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "encoding": {
      "encodingType": "None"
    },
    "transcriptions": [
      {
        "language": "en-US"
      }
    ],
    "vanityUrl": false,
    "streamOptions": [
      "Default"
    ]
  },
  "location": "West US 2"
}
```

## <a name="start-or-stop-transcription-after-the-live-event-has-started"></a>Iniciar ou parar a transcrição após o início do evento ao vivo

Você pode iniciar e parar a transcrição ao vivo enquanto o evento ao vivo está em estado de execução. Para obter mais informações sobre como iniciar e interromper eventos em tempo real, leia a seção operações de execução longa em [desenvolver com APIs dos serviços de mídia v3](media-services-apis-overview.md#long-running-operations).

Para ativar as transcrições dinâmicas ou para atualizar a linguagem de transcrição, corrija o evento ao vivo para incluir uma propriedade "transcrições". Para desativar as transcrições dinâmicas, remova a propriedade "transcrições" do objeto de evento ao vivo.  

> [!NOTE]
> Ativar ou desativar a transcrição **mais de uma vez** durante o evento ao vivo não é um cenário com suporte.

Esta é a chamada de exemplo para ativar transcrições dinâmicas.

DISTRIBUÍDO ```https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview```

```
{
  "properties": {
    "description": "Demonstrate how to enable live transcriptions", 
    "input": {
      "streamingProtocol": "RTMP",
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "preview": {
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "encoding": {
      "encodingType": "None"
    },
    "transcriptions": [
      {
        "language": "en-US"
      }
    ],
    "vanityUrl": false,
    "streamOptions": [
      "Default"
    ]
  },
  "location": "West US 2"
}
```

## <a name="transcription-delivery-and-playback"></a>Entrega e reprodução de transcrição

Examine o artigo [visão geral do empacotamento dinâmico](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) de como nosso serviço usa o empacotamento dinâmico para entregar vídeo, áudio e texto em diferentes protocolos. Quando você publica sua transmissão ao vivo usando MPEG-DASH ou HLS/CMAF, juntamente com vídeo e áudio, nosso serviço entrega o texto transcrevedo no TTML compatível com IMSC 1.1. Essa entrega é empacotada nos fragmentos de MPEG-4, parte 30 (ISO/IEC 14496-30). Se estiver usando a entrega via HLS/TS, o texto será entregue como um VTT em bloco. Você pode usar um Web Player, como o [player de mídia do Azure](use-azure-media-player.md) para reproduzir o fluxo.  

> [!NOTE]
> Se estiver usando Player de Mídia do Azure, use a versão 2.3.3 ou posterior.

## <a name="known-issues"></a>Problemas conhecidos

Para visualização, veja a seguir os problemas conhecidos com a transcrição ao vivo:

- Os aplicativos precisam usar as APIs de visualização, descritas na [especificação openapi dos serviços de mídia v3](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json).
- A proteção de DRM (gerenciamento de direitos digitais) não se aplica à faixa de texto, somente a criptografia de envelope AES é possível.

## <a name="next-steps"></a>Próximas etapas

* [Visão geral dos Serviços de Mídia](media-services-overview.md)
