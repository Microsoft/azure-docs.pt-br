---
title: Localizar e editar rostos na API dos Serviços de Mídia do Azure v3 | Microsoft Docs
description: Os Serviços de Mídia do Azure v3 fornece uma predefinição de edição e detecção facial (desfoque) que permite que você envie um arquivo de vídeo, detecte rostos e, opcionalmente, aplique a edição (desfoque) neles em uma passagem combinada ou por meio de uma operação de duas fases, permitindo a edição. Este artigo demonstra como localizar e editar rostos com a predefinição do Detector Facial na API v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/25/2021
ms.author: johndeu
ms.custom: devx-track-csharp
ms.openlocfilehash: 4907a81fc8cb55499fa97f2b02a3e19e7117bbbc
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286361"
---
# <a name="find-and-redact-blur-faces-with-the-face-detector-preset"></a>Localizar e editar (desfoque) rostos com a predefinição do Detector Facial

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

A API dos Serviços de Mídia do Azure v3 inclui uma predefinição do Detector Facial que oferece detecção facial e edição (desfoque) escalonável na nuvem. A edição facial permite que você modifique seu vídeo para desfocar rostos de pessoas selecionadas. Você pode querer usar o serviço de edição facial em cenários de segurança pública e de notícias veiculadas. A edição manual de alguns minutos de filmagem que contém vários rostos pode levar horas. Entretanto, com esta predefinição, o processo de edição facial exigirá apenas algumas etapas simples.

Este artigo fornece detalhes sobre a **Predefinição do Detector Facial** e mostra como usá-la com o SDK dos Serviços de Mídia do Azure para .NET.

[!INCLUDE [regulation](../video-indexer/includes/regulation.md)]

## <a name="compliance-privacy-and-security"></a>Conformidade, privacidade e segurança
 
É importante lembrar que você precisa obedecer a todas as leis aplicáveis em seu uso dos Serviços de Mídia do Azure. Você não deve usar os Serviços de Mídia do Azure nem qualquer outro serviço do Azure de modo que viole os direitos de outras pessoas. Antes de carregar qualquer vídeo, incluindo dados biométricos, no serviço dos Serviços de Mídia do Azure para processamento e armazenamento, você precisa ter todos os direitos apropriados, incluindo todos os consentimentos apropriados, dos indivíduos mostrados no vídeo. Para saber mais sobre a conformidade, a privacidade e a segurança nos Serviços de Mídia do Azure, leia os [Termos dos Serviços Cognitivos](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/) do Azure. Para saber mais sobre as obrigações de privacidade e o tratamento de seus dados pela Microsoft, confira a [Política de Privacidade](https://privacy.microsoft.com/PrivacyStatement), os [OST (Termos dos Serviços Online)](https://www.microsoft.com/licensing/product-licensing/products) e o [DPA (Adendo de Processamento de Dados)](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) da Microsoft. Mais informações sobre privacidade, incluindo sobre retenção de dados, exclusão/destruição, estão disponíveis no OST e [aqui](../video-indexer/faq.md). Ao usar os Serviços de Mídia do Azure, você concorda em vincular-se aos Termos dos Serviços Cognitivos, ao OST, ao DPA e à Política de Privacidade

## <a name="face-redaction-modes"></a>Modos de edição facial

A edição facial trabalha detectando rostos em cada quadro de vídeo e controlando o objeto de rosto para frente e para trás no tempo, para que a mesma pessoa possa ser desfocada também de outros ângulos. O processo de edição automatizado é complexo e nem sempre desfoca totalmente todos os rostos. Por esse motivo, a predefinição pode ser usada em um modo de duas passagens para aprimorar a qualidade e a precisão do desfoque por meio de uma fase de edição antes de enviar o arquivo para a passagem de desfoque final. 

Além de um modo **Combinado** totalmente automático, o fluxo de trabalho de duas passagens permite que você escolha os rostos que deseja desfocar (ou não desfocar) por meio de uma lista de IDs de rosto. Para fazer ajustes arbitrários por quadro, a predefinição usa um arquivo de metadados no formato JSON como entrada para a segunda passagem. Esse fluxo de trabalho é dividido nos modos **Analisar** e **Editar**.

Você pode combinar os dois modos em uma passagem única que executa ambas as tarefas em um mesmo trabalho. Esse modo é chamado **Combinado**.  Neste artigo, o código de exemplo mostrará como usar o modo simplificado de passagem única **Combinado** em um arquivo de origem de exemplo.

### <a name="combined-mode"></a>Modo Combinado

Isso produz um arquivo de vídeo MP4 editado em uma passagem única, sem a necessidade de qualquer edição manual do arquivo JSON. A saída na pasta de ativo para o trabalho será um arquivo .mp4 que contém rostos desfocados usando o efeito de desfoque selecionado. Use a propriedade de resolução definida como **SourceResolution** para obter os melhores resultados para a edição.

| Estágio | Nome do Arquivo | Observações |
| --- | --- | --- |
| Ativo de entrada |"ignite-sample.mp4" |Vídeo em formato WMV, MOV ou MP4 |
| Configuração predefinida |Configuração do Detector Facial | **mode**: FaceRedactorMode.Combined,  **blurType**: BlurType.Med, **resolution**: AnalysisResolution.SourceResolution |
| Ativo de saída |"ignite-redacted.mp4 |Vídeo com efeito de desfoque aplicado aos rostos |

### <a name="analyze-mode"></a>Modo Analisar

A passagem **Analisar** do fluxo de trabalho de duas passagens pega uma entrada de vídeo e produz um arquivo JSON com uma lista das localizações dos rostos, IDs dos Rostos e imagens jpg de cada rosto detectado. 

| Estágio | Nome do Arquivo | Observações |
| --- | --- | --- |
| Ativo de entrada |"ignite-sample.mp4" |Vídeo em formato WMV, MPV ou MP4 |
| Configuração predefinida |Configuração do Detector Facial |**mode**: FaceRedactorMode.Analyze, **resolution**: AnalysisResolution.SourceResolution|
| Ativo de saída |ignite-sample_annotations.json |Dados de anotação da localização dos rostos no formato JSON. Isso pode ser editado pelo usuário para modificar as caixas delimitadoras de desfoque. Confira o exemplo abaixo. |
| Ativo de saída |foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |Um jpg cortado de cada rosto detectado, em que o número indica o labelId do rosto |

#### <a name="output-example"></a>Exemplo de saída

```json
{
  "version": 1,
  "timescale": 24000,
  "offset": 0,
  "framerate": 23.976,
  "width": 1280,
  "height": 720,
  "fragments": [
    {
      "start": 0,
      "duration": 48048,
      "interval": 1001,
      "events": [
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [
          {
            "index": 13,
            "id": 1138,
            "x": 0.29537,
            "y": -0.18987,
            "width": 0.36239,
            "height": 0.80335
          },
          {
            "index": 13,
            "id": 2028,
            "x": 0.60427,
            "y": 0.16098,
            "width": 0.26958,
            "height": 0.57943
          }
        ],

    ... truncated
```

### <a name="redact-blur-mode"></a>Modo de edição (Desfoque)

A segunda etapa do fluxo de trabalho tem um grande número de entradas que precisam ser combinadas em um único ativo.

Isso inclui uma lista de IDs a serem desfocados, o vídeo original e o JSON de anotações. Esse modo usa as anotações para aplicar desfoque ao vídeo de entrada.

O resultado da etapa Analisar não inclui o vídeo original. O vídeo precisa ser carregado no ativo de entrada para a tarefa do modo Editar e selecionado como o arquivo primário.

| Estágio | Nome do Arquivo | Observações |
| --- | --- | --- |
| Ativo de entrada |"ignite-sample.mp4" |Vídeo em formato WMV, MPV ou MP4. O mesmo vídeo da etapa 1. |
| Ativo de entrada |"ignite-sample_annotations.json" |arquivo de metadados de anotações da fase um, com modificações opcionais, se você quiser alterar os rostos desfocados. Isso deve ser editado em um aplicativo, código ou editor de texto externo. |
| Ativo de entrada | "ignite-sample_IDList.txt" (Optional) | Nova lista opcional separada por linhas de IDs de rostos para editar. Se for deixada em branco, todos os rostos na origem terão o desfoque aplicado. Você pode usar a lista para escolher seletivamente não desfocar rostos específicos. |
| Predefinição do Detector Facial  |Configuração predefinida  | **mode**: FaceRedactorMode.Redact, **blurType**: BlurType.Med |
| Ativo de saída |"ignite-sample-redacted.mp4" |Vídeo com desfoque aplicado com base nas anotações |

#### <a name="example-output"></a>Saída de exemplo

É a saída de uma IDList com uma ID selecionada.

foo_IDList.txt de exemplo

```
1
2
3
```

## <a name="blur-types"></a>Tipos de desfoque

No modo **Combinado** ou **Edição**, você pode escolher cinco modos distintos de desfoque por meio da configuração de entrada JSON: **Baixo**, **Médio**, **Alto**, **Caixa** e **Preto**. Por padrão, **Med** é usado.

Encontre exemplos dos tipos de desfoque abaixo.


#### <a name="low"></a>Baixo

![Exemplo de configuração de desfoque de baixa resolução.](./media/media-services-face-redaction/blur-1.png)

#### <a name="med"></a>Med

![Exemplo de configuração de desfoque de resolução média.](./media/media-services-face-redaction/blur-2.png)

#### <a name="high"></a>Alto

![Exemplo de configuração de desfoque de alta resolução.](./media/media-services-face-redaction/blur-3.png)

#### <a name="box"></a>Box

![Modo de caixa para uso na depuração da saída.](./media/media-services-face-redaction/blur-4.png)

#### <a name="black"></a>Preto

![O modo de caixa preta cobre todos os rostos com caixas pretas.](./media/media-services-face-redaction/blur-5.png)

## <a name="elements-of-the-output-json-file"></a>Elementos do arquivo JSON de saída

O MP de edição fornece detecção facial e acompanhamento de alta precisão local de até 64 rostos humanos em um quadro de vídeo. Faces frontais fornecem os melhores resultados, enquanto as faces laterais e faces pequenas (menores ou iguais a 24x24 pixels) têm resultados menos precisos.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>Código de exemplo do .NET

O seguinte programa mostra como usar o modo de edição de passagem única **Combinado**:

- Criar um ativo e carregar um arquivo de mídia nesse ativo.
- Configurar a predefinição do Detector Facial que usa as configurações mode e blurType.
- Criar uma Transformação usando a predefinição do Detector Facial
- Baixar o arquivo de vídeo de saída editado.

## <a name="download-and-configure-the-sample"></a>Baixar e configurar o exemplo

Clone um repositório do GitHub que contém o exemplo de .NET em sua máquina usando o comando a seguir:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
 ```

O exemplo está localizado na pasta [FaceRedactor](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoAnalytics/FaceRedactor). Abra [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/VideoAnalytics/FaceRedactor/appsettings.json) no projeto que você baixou. Substitua os valores pelas credenciais que você obteve de [acesso às APIs](./access-api-howto.md).

**Opcionalmente**, você pode copiar o arquivo **[sample.env](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/sample.env)** na raiz do repositório, preencher as informações nesse arquivo e renomeá-lo para **.env** (Observe o ponto na frente!) para que ele possa ser usado em todos os projetos de exemplo no repositório.  Isso elimina a necessidade de ter um arquivo appsettings.json preenchido em cada exemplo e impede que você verifique configurações nos próprios repositórios clonados do GitHub.

#### <a name="examples"></a>Exemplos

Esse código mostra como configurar o **FaceDetectorPreset** para um desfoque no modo **Combinado**.

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPreset)]

Esse exemplo de código mostra como a predefinição é passada para um objeto de Transformação durante a criação. Após a criação da Transformação, os trabalhos poderão ser enviados diretamente a ele.

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPresetTransform)]

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

