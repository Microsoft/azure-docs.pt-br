---
title: Faturas – Reconhecimento de Formulários
titleSuffix: Azure Cognitive Services
description: Aprenda conceitos relacionados à análise de faturas usando uma API do Reconhecimento de Formulários – Uso e limites.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: a47c4c5bdc90e148916900b1e72bc2a392d2e473
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285324"
---
# <a name="form-recognizer-prebuilt-invoice-model"></a>Modelo predefinido de faturas do Reconhecimento de Formulários

O Reconhecimento de Formulários do Azure pode analisar e extrair informações de faturas de vendas usando modelos predefinidos de faturas. Uma API de Fatura permite que os clientes recebam faturas em diversos formatos e retornem dados estruturados para automatizar o processamento delas. Ela combina nossas funcionalidades avançadas de [OCR (Reconhecimento Óptico de Caracteres)](../computer-vision/overview-ocr.md) com modelos de aprendizado profundo de reconhecimento de faturas para extrair informações importantes de faturas em inglês. Ela extrai textos, tabelas e informações, incluindo: nome do cliente, nome do fornecedor, ID da fatura, data de vencimento da fatura, valor total, valor devido da fatura, valor do imposto, endereço de entrega, endereço de cobrança, itens de linha e muito mais. Uma API predefinida de Fatura está disponível de modo público na versão prévia do Reconhecimento de Formulários v2.1.

## <a name="what-does-the-invoice-service-do"></a>Qual é a função do serviço de Fatura?

Uma API de Fatura extrai campos de chave e itens de linha de faturas e os retorna em uma resposta JSON estruturada e organizada. As faturas podem ter uma variedade de formatos e qualidade, incluindo: imagens capturadas por telefone, documentos digitalizados e arquivos PDF digitais. Uma API de Fatura extrairá saídas estruturadas de todas essas faturas. 

![Exemplo de fatura da Contoso](./media/invoice-example-new.jpg)

## <a name="try-it-out"></a>Experimente

Acesse a Ferramenta de Interface do Usuário de Exemplo disponível online para experimentar o Serviço de Fatura do Reconhecimento de Formulários:

> [!div class="nextstepaction"]
> [Experimentar modelos predefinidos](https://fott-preview.azurewebsites.net/)

Será preciso obter uma assinatura do Azure ([crie uma sem custos](https://azure.microsoft.com/free/cognitive-services)), bem como um ponto de extremidade e uma chave do [Recurso de Reconhecimento de Formulários](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) para experimentar o Serviço de Fatura do Reconhecimento de Formulários. 

:::image type="content" source="media/analyze-invoice-new.png" alt-text="Exemplo de fatura analisada" lightbox="media/analyze-invoice-new.png":::

### <a name="input-requirements"></a>Requisitos de entrada

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-invoice-operation"></a>A operação de Análise de Fatura

A operação de [Análise de Fatura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9843c2794cbb1a96291) usa uma imagem ou um arquivo PDF de uma fatura como entrada e extrai os valores de seu interesse. A chamada retorna um campo de cabeçalho de resposta chamado `Operation-Location`. O valor `Operation-Location` é uma URL que contém a ID do Resultado a ser usada na próxima etapa.

|Cabeçalho de resposta| URL do Resultado |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/invoice/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-invoice-result-operation"></a>A operação Obter Resultado da Análise de Fatura

A segunda etapa será executar uma chamada à operação [Obter Resultado da Análise de Fatura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9acb78c40a2533aee83). Essa operação usa como entrada a ID do Resultado criada pela operação de Análise de Fatura. Ela retorna uma resposta JSON que contém um campo de **status** com os valores possíveis a seguir. Execute uma chamada a essa operação de modo iterativo até que ela retorne o valor **succeeded**. Use um intervalo de três a cinco segundos para evitar que a taxa de RPS (solicitações por segundo) seja excedida.

|Campo| Tipo | Valores possíveis |
|:-----|:----:|:----|
|status | string | notStarted: a operação de análise não foi iniciada.<br /><br />running: a operação de análise está em andamento.<br /><br />failed: falha na operação de análise.<br /><br />succeeded: a operação de análise foi executada com êxito.|

Quando o campo de **status** exibir o valor **succeeded**, a resposta JSON incluirá: os resultados de reconhecimento da fatura, as tabelas extraídas e os resultados opcionais de reconhecimento de texto, caso seja solicitado. O resultado do reconhecimento da fatura é organizado como um dicionário de valores de campo nomeados, em que cada valor contém o texto extraído, o valor normalizado, a caixa delimitadora, a confiança e os elementos de palavras correspondentes. Ele também incluirá itens de linha extraídos. Neles, cada item de linha contém o valor, a descrição, o unitPrice, a quantidade etc. Além disso, o resultado do reconhecimento de texto é organizado como uma hierarquia de linhas e palavras, com informações do texto, da caixa delimitadora e de confiança.

### <a name="sample-json-output"></a>Saída JSON de exemplo

A resposta à operação Obter Resultado da Análise de Fatura será a representação estruturada da fatura com todas as informações extraídas. Confira este artigo para obter um [arquivo da fatura de exemplo](media/sample-invoice.jpg) e a respectiva saída estruturada da [saída da fatura de exemplo](media/invoice-example-new.jpg).

A saída JSON é composta por três partes: 
* O nó `"readResults"` contém todas as marcas de texto e seleção reconhecidas. O texto é organizado por página, depois por linha e, em seguida, por palavras individuais. 
* O nó `"pageResults"` contém tabelas e células extraídas com as caixas delimitadoras, a confiança e uma referência às linhas e palavras em "readResults".
* O nó `"documentResults"` contém valores específicos da fatura e os itens de linha que o modelo descobriu. Você encontrará aqui todos os campos da fatura, como ID da fatura, endereço de envio, endereço de cobrança, nome do cliente, valor total, itens de linha e muito mais.

## <a name="example-output"></a>Saída de exemplo

O Serviço de Fatura extrairá o texto, as tabelas e 26 campos da fatura. A seguir serão mostrados os campos extraídos de uma fatura na resposta de saída JSON (a saída abaixo usará esta [fatura de exemplo](media/sample-invoice.jpg)).

|Nome| Tipo | Descrição | Texto | Valor (saída padronizada) |
|:-----|:----|:----|:----| :----|
| CustomerName | string | O cliente recebendo a fatura | Microsoft Corp. |  |
| CustomerId | string | A ID de referência do cliente | CID-12345 |  |
| PurchaseOrder | string | Um número de referência da ordem de compra | PO-3333 | | 
| InvoiceId | string | A ID desta fatura específica (geralmente exibida como "Número da Fatura") | INV-100 | | 
| InvoiceDate | date | A data em que a fatura foi emitida | 15/11/2019 | 15-11-2019 | 
| DueDate | date | A data de vencimento do pagamento desta fatura | 15/12/2019 | 15-12-2019 |
| VendorName | string | O fornecedor que criou esta fatura | Contoso Ltd. | |
| VendorAddress | string | O endereço para correspondência do fornecedor | 123 456th St New York, NY, 10001 | |
| VendorAddressRecipient | string | Nome associado ao VendorAddress | Sede da Contoso | |
| CustomerAddress | string | Endereço para correspondência do cliente | 123 Other St, Redmond WA, 98052 | |
| CustomerAddressRecipient | string | Nome associado ao CustomerAddress | Microsoft Corp. | |
| BillingAddress | string | Endereço explícito para cobrança do cliente | 123 Bill St, Redmond WA, 98052 | |
| BillingAddressRecipient | string | Nome associado ao BillingAddress | Serviços Microsoft | |
| ShippingAddress | string | Endereço explícito para entrega do cliente | 123 Ship St, Redmond WA, 98052 | |
| ShippingAddressRecipient | string | Nome associado ao ShippingAddress | Entrega da Microsoft | |
| SubTotal | número | Campo do subtotal identificado nesta fatura | US$ 100,00 | 100 | 
| TotalTax | número | Campo do imposto total identificado nesta fatura | US$ 10,00 | 10 |
| InvoiceTotal | número | Total de novos encargos associados a esta fatura | US$ 110,00 | 110 |
| AmountDue |  número | Valor total devido ao fornecedor | US$ 610,00 | 610 |
| ServiceAddress | string | Endereço de serviço ou endereço de propriedade explícito do cliente | 123 Service St, Redmond WA, 98052 | |
| ServiceAddressRecipient | string | Nome associado ao ServiceAddress | Serviços Microsoft | |
| RemittanceAddress | string | Remessa explícita ou endereço para pagamento do cliente | 123 Remit St New York, NY, 10001 |  |
| RemittanceAddressRecipient | string | Nome associado ao RemittanceAddress | Cobrança da Contoso |  |
| ServiceStartDate | date | Primeira data do período de serviço (por exemplo, um período da fatura serviços públicos essenciais) | 14/10/2019 | 14-10-2019 |
| ServiceEndDate | date | Data de término do período de serviço (por exemplo, um período da fatura serviços públicos essenciais) | 14/11/2019 | 14-11-2019 |
| PreviousUnpaidBalance | número | Saldo explícito não pago anteriormente | US$ 500,00 | 500 |

Abaixo serão mostrados os itens de linha extraídos de uma fatura na resposta de saída JSON (a saída abaixo usará esta [fatura de exemplo](./media/sample-invoice.jpg))  

|Nome| Tipo | Descrição | Texto (item de linha nº 1) | Valor (saída padronizada) |
|:-----|:----|:----|:----| :----|
| Itens | string | Linha de texto de uma cadeia de caracteres completa do item de linha | 04/03/2021 A123 Serviços de Consultoria 2 horas US$ 30,00 10% US$ 60,00 | |
| Valor | número | O valor do item de linha | US$ 60,00 | 100 |
| Descrição | string | A descrição de texto do item de linha da fatura | Serviço de consultoria | Serviço de consultoria |
| Quantidade | número | O valor deste item de linha da fatura | 2 | 2 |
| UnitPrice | número | O preço líquido ou bruto (de acordo com a configuração da fatura bruta) de uma unidade deste item | US$ 30,00 | 30 |
| ProductCode | string| O código do produto, o número do produto ou o SKU associado a um item de linha específico | A123 | |
| Unidade | string| A unidade do item de linha, por exemplo: kg, lb etc. | horas | |
| Data | data| A data correspondente a cada item de linha. Com frequência, essa será a data em que o item de linha foi enviado | 04/03/2021| 04-03-2021 |
| Imposto | número | Imposto associado a cada item de linha. Os valores possíveis incluem: valor do imposto, percentual do imposto e imposto S/N | 10% | |


## <a name="next-steps"></a>Próximas etapas

- Experimente usar faturas e exemplos próprios na [Interface do Usuário de Exemplo do Reconhecimento de Formulários](https://fott-preview.azurewebsites.net/).
- Conclua o [Guia de início rápido do Reconhecimento de Formulários](quickstarts/client-library.md) para começar a escrever um aplicativo de processamento de faturas usando o Reconhecimento de Formulários na linguagem de desenvolvimento de sua preferência.

## <a name="see-also"></a>Confira também

* [O que é o Reconhecimento de Formulários?](./overview.md)
* [Documentos de referência da API REST](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9843c2794cbb1a96291)
