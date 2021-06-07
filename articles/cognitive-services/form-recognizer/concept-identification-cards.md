---
title: IDs – Reconhecimento de Formulários
titleSuffix: Azure Cognitive Services
description: Aprenda conceitos relacionados à extração de dados de documentos de identidade usando uma API predefinida de IDs do Reconhecimento de Formulários.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: ed8516f9a898131338fb5b4d75e25cd774c5ab43
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285344"
---
# <a name="form-recognizer-prebuilt-identification-card-id-model"></a>Modelo de cartão de ID (identificação) predefinida do Reconhecimento de Formulários

O Reconhecimento de Formulários do Azure pode analisar e extrair informações de cartões de ID (identificação) do governo usando um modelo de IDs predefinidas. Ele combina nossas funcionalidades avançadas de [OCR (Reconhecimento Óptico de Caracteres)](../computer-vision/overview-ocr.md) com funcionalidades de reconhecimento de ID para extrair informações importantes de passaportes internacionais e carteiras de motorista dos EUA (de todos os 50 estados e da capital). Uma API de IDs extrai informações importantes desses documentos de identidade, como o primeiro nome, o sobrenome, a data de nascimento, o número do documento e muito mais. Essa API está disponível na versão prévia do Reconhecimento de Formulários v2.1 como um serviço de nuvem e contêiner local.

## <a name="what-does-the-id-service-do"></a>Qual é a função do serviço de ID? 

O serviço de IDs predefinidas extrai valores importantes de passaportes internacionais e carteiras de motorista dos EUA e retorna essas informações em uma resposta JSON estruturada e organizada. 

![Carteira de motorista de exemplo](./media/id-example-drivers-license.JPG)

![Passaporte de exemplo](./media/id-example-passport-result.JPG)

### <a name="fields-extracted"></a>Campos extraídos

|Nome| Tipo | Descrição | Valor | 
|:-----|:----|:----|:----|
|  País | country | O código do país em conformidade com o padrão ISO 3166 | "EUA" | 
|  DateOfBirth | date | Nascimento no formato DD-MM-AAAA | "01-01-1980" | 
|  DateOfExpiration | date | Data de validade no formato DD-MM-AAAA | "05-05-2019" |  
|  DocumentNumber | string | Números relevantes do passaporte, da carteira de motorista etc. | "340020013" |  
|  Nome | string | O primeiro nome e nome do meio extraídos, se aplicável | "JENNIFER" | 
|  LastName | string | Sobrenome extraído | "BROOKS" |   
|  Nacionalidade | country | O código do país em conformidade com o padrão ISO 3166 | "EUA" |
|  Gênero | gender | Os possíveis valores extraídos incluem: "M", "F" e "X" | "F" | 
|  MachineReadableZone | objeto | Um MRZ extraído do passaporte, incluindo duas linhas de 44 caracteres cada | "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816" |
|  DocumentType | string | Tipo de documento, como passaporte e carteira de motorista | "passaporte" |  
|  Endereço | string | Endereço extraído (somente da carteira de motorista) | "ENDEREÇO Nº 123 SUA CIDADE WA 99999-1234"|
|  Região | string | Informações extraídas, como região, estado, província etc. (somente da carteira de motorista) | "Washington" | 

### <a name="additional-features"></a>Recursos adicionais

Uma API de IDs também retorna as seguintes informações:

* Nível de confiança do campo (cada campo retorna um valor de confiança associado)
* Texto bruto de OCR (saída de texto extraída por OCR do recibo inteiro)
* Caixa delimitadora de cada campo extraído das carteiras de motorista dos EUA
* Caixa delimitadora para MRZ (Zona de Leitura por Computador) em passaportes

  > [!NOTE]
  > As IDs predefinidas não detectam a autenticidade da ID
  >
  > As IDs predefinidas do Reconhecimento de Formulários extrai dados importantes de dados da ID. No entanto, elas não detectam a validade nem a autenticidade do documento de identidade original. 

## <a name="try-it-out"></a>Experimente

Acesse a Ferramenta de Interface do Usuário de Exemplo disponível online para experimentar o serviço de IDs do Reconhecimento de Formulários:

> [!div class="nextstepaction"]
> [Experimentar modelos predefinidos](https://fott-preview.azurewebsites.net/)

## <a name="input-requirements"></a>Requisitos de entrada

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="supported-id-types"></a>Tipos de IDs com suporte  

* **IDs predefinidas v2.1 – versão prévia.3**: extrai valores importantes de passaportes internacionais e carteiras de motorista dos EUA. 

  > [!NOTE]
  > Suporte a tipos de IDs 
  >
  > Atualmente, os tipos de IDs com suporte incluem passaportes internacionais e carteiras de motorista dos EUA. Estamos buscando expandir de modo ativo nosso suporte a IDs para outros documentos de identidade em todo o mundo.

## <a name="post-analyze-id-document"></a>Documento do método POST de Analisar ID

A operação de [Analisar ID](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5f74a7daad1f2612c46f5822) usa uma imagem ou um arquivo PDF de uma ID como entrada e extrai os valores de seu interesse. A chamada retorna um campo de cabeçalho de resposta chamado `Operation-Location`. O valor `Operation-Location` é uma URL que contém a ID do Resultado a ser usada na próxima etapa.

|Cabeçalho de resposta| URL do Resultado |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/idDocument/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="get-analyze-id-document-result"></a>Resultado de um documento da função GET de Analisar ID

<!---
Need to update this with updated APIM links when available
-->

A segunda etapa é baseada em executar uma chamada à operação [**Obter Resultado da Análise do idDocument**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeFormResult). Essa operação usa a ID do Resultado criada pela operação Analisar ID como entrada. Ela retorna uma resposta JSON que contém um campo de **status** com os valores possíveis a seguir. Execute uma chamada a essa operação de modo iterativo até que ela retorne o valor **succeeded**. Use um intervalo de três a cinco segundos para evitar que a taxa de RPS (solicitações por segundo) seja excedida.

|Campo| Tipo | Valores possíveis |
|:-----|:----:|:----|
|status | string | notStarted: a operação de análise não foi iniciada. |
| |  | running: a operação de análise está em andamento. |
| |  | failed: falha na operação de análise. |
| |  | succeeded: a operação de análise foi executada com êxito. |

Quando o campo **status** tiver o valor **succeeded**, a resposta JSON incluirá os resultados de reconhecimento de texto e de entendimento do recibo. O resultado das IDs é organizado como um dicionário de valores de campo nomeados, em que cada valor contém o texto extraído, o valor normalizado, a caixa delimitadora, a confiança e os elementos de palavras correspondentes. O resultado do reconhecimento de texto é organizado como uma hierarquia de linhas e palavras, com informações de texto, de caixa delimitadora e de confiança.

![resultados de recibo de exemplo](./media/id-example-passport-result.JPG)

### <a name="sample-json-output"></a>Saída JSON de exemplo

Confira este exemplo de uma resposta JSON com êxito: o nó `readResults` contém todo o texto reconhecido. O texto é organizado por página, depois por linha e, em seguida, por palavras individuais. O nó `documentResults` contém os valores de ID que o modelo descobriu. Também será possível encontrar nesse nó pares chave-valor úteis, como o primeiro nome, o sobrenome, a data de nascimento, o número do documento e muito mais.

```json
{ 
   "status": "succeeded",
  "createdDateTime": "2021-03-04T22:29:33Z",
  "lastUpdatedDateTime": "2021-03-04T22:29:36Z",
  "analyzeResult": {
    "version": "2.1.0",
    "readResults": [
     {
        "page": 1,
        "angle": 0.3183,
        "width": 549,
        "height": 387,
        "unit": "pixel",
        "lines": [
          {
            "text": "PASSPORT",
            "boundingBox": [
              57,
              10,
              120,
              11,
              119,
              22,
              57,
              22
            ],
            "words": [
              {
                "text": "PASSPORT",
                "boundingBox": [
                  57,
                  11,
                  119,
                  11,
                  118,
                  23,
                  57,
                  22
                ],
                "confidence": 0.994
              }
            ],
          ...
      }
    ],
    
     "documentResults": [
      {
        "docType": "prebuilt:idDocument:passport",
        "docTypeConfidence": 0.995,
        "pageRange": [
          1,
          1
        ],
        "fields": {
          "Country": {
            "type": "country",
            "valueCountry": "USA",
            "text": "USA"
          },
          "DateOfBirth": {
            "type": "date",
            "valueDate": "1980-01-01",
            "text": "800101"
          },
          "DateOfExpiration": {
            "type": "date",
            "valueDate": "2019-05-05",
            "text": "190505"
          },
          "DocumentNumber": {
            "type": "string",
            "valueString": "340020013",
            "text": "340020013"
          },
          "FirstName": {
            "type": "string",
            "valueString": "JENNIFER",
            "text": "JENNIFER"
          },
          "LastName": {
            "type": "string",
            "valueString": "BROOKS",
            "text": "BROOKS"
          },
          "Nationality": {
            "type": "country",
            "valueCountry": "USA",
            "text": "USA"
          },
          "Sex": {
            "type": "gender",
            "valueGender": "F",
            "text": "F"
          },
          "MachineReadableZone": {
            "type": "object",
            "text": "P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816",
            "boundingBox": [
              16,
              314.1,
              504.2,
              317,
              503.9,
              363,
              15.7,
              360.1
            ],
            "page": 1,
            "confidence": 0.384,
            "elements": [
              "#/readResults/0/lines/33/words/0",
              "#/readResults/0/lines/33/words/1",
              "#/readResults/0/lines/33/words/2",
              "#/readResults/0/lines/33/words/3",
              "#/readResults/0/lines/33/words/4",
              "#/readResults/0/lines/34/words/0"
            ]
          },
          "DocumentType": {
            "type": "string",
            "text": "passport",
            "confidence": 0.995
          }
        }
      }
    ]
  }
}
```


## <a name="next-steps"></a>Próximas etapas

- Experimente usar IDs e exemplos próprios na [Interface do Usuário de Exemplo do Reconhecimento de Formulários](https://fott-preview.azurewebsites.net/).
- Conclua um [Guia de início rápido do Reconhecimento de Formulários](quickstarts/client-library.md) para começar a escrever um aplicativo de processamento de IDs usando o Reconhecimento de Formulários na linguagem de desenvolvimento de sua preferência.

## <a name="see-also"></a>Confira também

* [**O que é o Reconhecimento de Formulários?**](./overview.md)
* [**Documentos de referência da API REST**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)
