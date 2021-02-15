---
title: O que há de novo no Reconhecimento de Formulários?
titleSuffix: Azure Cognitive Services
description: Entenda as alterações mais recentes na API do reconhecedor de formulário.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: pafarley
ms.openlocfilehash: c7b6586f02d14d4e49ce9a5024b19ea15c8d2267
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364674"
---
# <a name="whats-new-in-form-recognizer"></a>O que há de novo no Reconhecimento de Formulários?

O serviço do reconhecedor de formulário é atualizado em uma base contínua. Use este artigo para se manter atualizado com os aprimoramentos de recursos, correções e atualizações de documentação.

## <a name="november-2020"></a>Novembro de 2020

### <a name="new-features"></a>Novos recursos

**O formulário Recognizer v 2.1 público preview 2 já está disponível.** V 2.1-Preview. 2 foi lançado, incluindo os seguintes recursos: 

- **Novo modelo de fatura predefinido** -o novo modelo de fatura predefinido permite que os clientes façam faturas em uma variedade de formatos e retornem dados estruturados para automatizar o processamento da fatura. Ele combina nossos poderosos recursos de OCR (reconhecimento óptico de caracteres) com a nota fiscal que entende os modelos de aprendizado profundo para extrair informações importantes das faturas em inglês. Ele extrai o texto, as tabelas e as informações, como cliente, fornecedor, ID da fatura, data de vencimento da fatura, total, valor devido, valor do imposto, remessa, cobrança e muito mais.

  > [Saiba mais sobre o modelo de fatura predefinida](concept-invoices.md)

  :::image type="content" source="./media/invoice-example.jpg" alt-text="exemplo de fatura" lightbox="./media/invoice-example.jpg":::

- **Extração de tabela aprimorada** – o formulário reconhecedor agora fornece extração de tabela avançada, que combina nossos poderosos recursos de OCR (reconhecimento óptico de caracteres) com um modelo de extração de tabela de aprendizado profundo. O reconhecedor de formulário pode extrair dados de tabelas, incluindo tabelas complexas com colunas mescladas, linhas, sem bordas e muito mais. 
 
  :::image type="content" source="./media/tables-example.jpg" alt-text="exemplo de tabelas" lightbox="./media/tables-example.jpg":::

 
  > [Saiba mais sobre extração de layout](concept-layout.md)

- **Atualização da biblioteca de cliente** -as versões mais recentes das [bibliotecas de cliente](quickstarts/client-library.md) para .net, Python, Java e JavaScript dão suporte à API do Form Recognizer 2,1.
- **Novo idioma com suporte: Japonês** -os novos idiomas a seguir agora têm suporte: para `AnalyzeLayout` e `AnalyzeCustomForm` : Japonês ( `ja` ). [Suporte ao idioma](language-support.md)
- **Indicação de estilo de linha de texto (manuscrito/outro) (somente idiomas latinos)** – o reconhecedor de formulário agora gera um `appearance` objeto que classifica se cada linha de texto está no estilo manuscrito ou não, juntamente com uma pontuação de confiança. Esse recurso tem suporte apenas para idiomas latinos.
- **Melhorias de qualidade** -melhorias de extração, incluindo melhorias de extração de dígito único.
- **Novo recurso Try-it-out no formulário reconhecedor de formato e ferramenta de rotulamento** – capacidade de experimentar modelos de cartão de visita, recebimento e fatura pré-criados e a API de layout usando a ferramenta de rótulo de amostra do reconhecedor de formulário. Veja como seus dados serão extraídos sem escrever nenhum código.

  > [Experimente a ferramenta de exemplo do reconhecedor de formulário](https://fott-preview.azurewebsites.net/)

  ![Exemplo de FOTT](./media/ui-preview.jpg)
  
- **Loop de comentários** – ao analisar arquivos por meio da ferramenta de rótulo de exemplo, agora você também pode adicioná-lo ao conjunto de treinamento e ajustar os rótulos, se necessário, e treinar para melhorar o modelo.
- **Documentos de rótulo automático** – rotula automaticamente documentos adicionais com base em documentos rotulados anteriormente no projeto.

## <a name="august-2020"></a>Agosto de 2020

### <a name="new-features"></a>Novos recursos

**A visualização pública do reconhecedor de formulário v 2.1 já está disponível.** V 2.1-Preview. 1 foi lançado, incluindo os seguintes recursos: 


- A **referência da API REST está disponível** -Veja a [referência v 2.1-Preview. 1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync) 
- **Novos idiomas com suporte além do inglês**, agora há suporte para os seguintes [idiomas](language-support.md) : para `Layout` e `Train Custom Model` : Inglês ( `en` ), chinês (simplificado) ( `zh-Hans` ), holandês ( `nl` ), francês ( `fr` ), alemão () `de` , italiano () `it` , Português ( `pt` ) e espanhol ( `es` ).
- **Detecção de seleção/marca de seleção** – o reconhecedor de formulário dá suporte à detecção e extração de marcas de seleção, como caixas de seleção e botões de opção. As marcas de seleção são extraídas no `Layout` e agora você pode também rotular e treinar em `Train Custom Model`  -  _treinamento com rótulos_ para extrair pares de chave-valor para marcas de seleção. 
- **Compor modelo** – permite que vários modelos sejam compostos e chamados com uma única ID de modelo. Quando um documento é enviado para ser analisado com uma ID de modelo composto, uma etapa de classificação é executada primeiro para encaminhá-la para o modelo personalizado correto. A composição de modelo está disponível para `Train Custom Model`  -  _treinamento com rótulos_.
- **Nome do modelo** -adicione um nome amigável a seus modelos personalizados para facilitar o gerenciamento e o controle.
- **[Novo modelo predefinido para cartões de negócios](concept-business-cards.md)** para extrair campos comuns em inglês, cartões de visita de idioma.
- **[Novas localidades para recebimentos pré-criados](concept-receipts.md)** , além do en-US, o suporte agora está disponível para en-au, en-CA, en-GB, en-in
- **Aprimoramentos de qualidade** para `Layout` , `Train Custom Model`  -  _treine sem rótulos_ e _treine com rótulos_.


o **v 2.0** inclui a seguinte atualização:

- As [bibliotecas de cliente](quickstarts/client-library.md) para net, Python, Java e JavaScript entraram em disponibilidade geral. 


**Novos exemplos** estão disponíveis no github. 
- O [guia estratégico de receitas de extração de conhecimento](https://github.com/microsoft/knowledge-extraction-recipes-forms) coleta as práticas recomendadas de compromissos do cliente do reconhecedor de formato real e fornece exemplos de código, listas de verificação e pipelines de exemplo utilizáveis usados no desenvolvimento desses projetos. 
- A [ferramenta de rotulagem de exemplo](https://github.com/microsoft/OCR-Form-Tools) foi atualizada para dar suporte à nova funcionalidade v 2.1. Consulte este guia de [início rápido](quickstarts/label-tool.md) para começar a usar a ferramenta. 
- O exemplo de reconhecedor de formulário de [quiosque inteligente](https://github.com/microsoft/Cognitive-Samples-IntelligentKiosk/blob/master/Documentation/FormRecognizer.md) mostra como integrar `Analyze Receipt` e `Train Custom Model`  -  _treinar sem rótulos_.



## <a name="july-2020"></a>Julho de 2020

### <a name="new-features"></a>Novos recursos

* **referência v 2.0 disponível** -Veja a [referência da API v 2.0](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) e os SDKs atualizados para [.net](/dotnet/api/overview/azure/ai.formrecognizer-readme), [Python](/python/api/overview/azure/), [Java](/java/api/overview/azure/ai-formrecognizer-readme)e [JavaScript](/javascript/api/overview/azure/).
* Aprimoramentos de **tabela e aprimoramentos de extração** -inclui melhorias de precisão e aprimoramentos de extrações de tabela, especificamente, a capacidade de aprender cabeçalhos e estruturas de tabelas em _treinamento personalizado sem rótulos_. 

* **Suporte a moeda** – detecção e extração de símbolos de moeda global.
* O reconhecimento de formulários **do Azure gov** agora também está disponível no Azure gov.
* **Recursos de segurança aprimorados**: 
   * **Traga seu próprio** reconhecedor de formulário de chave criptografa automaticamente seus dados quando persistidos na nuvem para protegê-los e ajudá-lo a atender aos compromissos de segurança e conformidade da organização. Por padrão, sua assinatura usa chaves de criptografia gerenciadas pela Microsoft. Agora você também pode gerenciar sua assinatura com suas próprias chaves de criptografia. [As chaves gerenciadas pelo cliente, também conhecidas como BYOK (traga sua própria chave)](./form-recognizer-encryption-of-data-at-rest.md), oferecem maior flexibilidade para criar, girar, desabilitar e revogar controles de acesso. Você também pode auditar as chaves de criptografia usadas para proteger seus dados.  
   * **Pontos de extremidade privados** – permite que você em uma rede virtual (VNet) [acesse dados com segurança por meio de um link privado.](../../private-link/private-link-overview.md)


## <a name="june-2020"></a>Junho de 2020

### <a name="new-features"></a>Novos recursos
* **API do CopyModel adicionada aos SDKs do cliente** – agora você pode usar os SDKs do cliente para copiar modelos de uma assinatura para outra. Consulte [fazer backup e recuperar modelos](./disaster-recovery.md) para obter informações gerais sobre esse recurso.
* **Integração do Azure Active Directory** – agora você pode usar suas credenciais do Azure ad para autenticar os objetos de cliente do reconhecedor de formulário nos SDKs.
* **Alterações específicas do SDK** -isso inclui adições de recursos menores e alterações significativas. Consulte os changelog do SDK para obter mais informações.
  * [Log do C# SDK Preview 3](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/formrecognizer/Azure.AI.FormRecognizer/CHANGELOG.md)
  * [Log do Python SDK Preview 3](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Java SDK Preview 3 changelog](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Changelog do JavaScript SDK 3 log de alterações](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_1.0.0-preview.3/sdk/formrecognizer/ai-form-recognizer/CHANGELOG.md)

## <a name="april-2020"></a>Abril de 2020

### <a name="new-features"></a>Novos recursos
* **Suporte do SDK para a visualização pública da API v 2.0 do Forms Recognizer** – este mês, expandimos nosso suporte de serviço para incluir um SDK de visualização para a versão do Recognizer v 2.0 (visualização). Use os links abaixo para começar a usar o idioma de sua escolha: 
   * [SDK .NET](/dotnet/api/overview/azure/ai.formrecognizer-readme)
   * [Java SDK](/java/api/overview/azure/ai-formrecognizer-readme)
   * [SDK do Python](/python/api/overview/azure/ai-formrecognizer-readme)
   * [SDK do JavaScript](/javascript/api/overview/azure/ai-form-recognizer-readme)

  O novo SDK dá suporte a todos os recursos da API REST v 2.0 para o reconhecedor de formulário. Por exemplo, você pode treinar um modelo com ou sem rótulos e extrair texto, pares chave-valor e tabelas de seus formulários, extrair dados de recibos com o serviço de recebimentos pré-criados e extrair texto e tabelas com o serviço de layout de seus documentos. Você pode compartilhar seus comentários sobre os SDKs por meio do [formulário de comentários do SDK](https://aka.ms/FR_SDK_v1_feedback).
 
* **Copiar modelo personalizado** Agora você pode copiar modelos entre regiões e assinaturas usando o novo recurso de modelo personalizado de cópia. Antes de invocar a API de modelo personalizado de cópia, primeiro você deve obter autorização para copiar no recurso de destino chamando a operação de cópia de autorização no ponto de extremidade do recurso de destino.
   * [Gerar uma autorização de cópia](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModelAuthorization) API REST
   * [Copiar um modelo personalizado](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModel) API REST 

### <a name="security-improvements"></a>Aprimoramentos de segurança

* Customer-Managed chaves agora estão disponíveis para FormRecognizer. Para obter mais informações, consulte [criptografia de dados em repouso para o reconhecedor de formulário](./form-recognizer-encryption-of-data-at-rest.md).
* Use identidades gerenciadas para acessar recursos do Azure com Azure Active Directory. Para obter mais informações, consulte [autorizar o acesso a identidades gerenciadas](../authentication.md#authorize-access-to-managed-identities).

## <a name="march-2020"></a>Março de 2020 

### <a name="new-features"></a>Novos recursos

* **Tipos de valor para rotulagem** Agora você pode especificar os tipos de valores que está rotulando com a ferramenta de rotulagem de amostra do reconhecedor de formulário. Os tipos de valor e as variações a seguir são compatíveis no momento:
  * `string`
    * padrão, `no-whitespaces`, `alphanumeric`
  * `number`
    * padrão, `currency`
  * `date` 
    * padrão, `dmy`, `mdy`, `ymd`
  * `time`
  * `integer`

  Consulte o guia de [ferramentas de rótulo de exemplo](./quickstarts/label-tool.md#specify-tag-value-types) para saber como usar esse recurso.


* **Visualização de tabela** A ferramenta de rótulo de exemplo agora exibe tabelas que foram reconhecidas no documento. Esse recurso permite exibir as tabelas que foram reconhecidas e extraídas do documento, antes de rotular e analisar. Esse recurso pode ser ativado/desativado usando a opção camadas.

  A imagem a seguir é um exemplo de como as tabelas são reconhecidas e extraídas:

  > [!div class="mx-imgBorder"]
  > ![Visualização de tabela usando a ferramenta de rótulo de exemplo](./media/whats-new/table-viz.png)

    As tabelas extraídas estão disponíveis na saída JSON em `"pageResults"` .

  > [!IMPORTANT]
  > Não há suporte para rotular tabelas. Se as tabelas não forem reconhecidas e extrated automaticamente, você só poderá rotulá-las como pares de chave/valor. Ao rotular tabelas como pares de chave/valor, rotule cada célula como um valor exclusivo.

### <a name="extraction-enhancements"></a>Aprimoramentos de extração

Esta versão inclui aprimoramentos de extração e melhorias de precisão, especificamente, a capacidade de rotular e extrair vários pares de chave/valor na mesma linha de texto. 
 
### <a name="sample-labeling-tool-is-now-open-source"></a>A ferramenta de rótulo de amostra agora é de código-fonte aberto

A ferramenta de rotulagem de amostra do reconhecedor de formulário agora está disponível como um projeto de código-fonte aberto. Você pode integrá-lo em suas soluções e fazer alterações específicas do cliente para atender às suas necessidades.

Para obter mais informações sobre a ferramenta de rótulo de exemplo do reconhecedor de formulário, examine a documentação disponível no [GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

### <a name="tls-12-enforcement"></a>Imposição do TLS 1.2

Agora o TLS 1.2 é obrigatório para todas as solicitações HTTP a este serviço. Para saber mais, confira [Segurança nos Serviços Cognitivos do Azure](../cognitive-services-security.md).

## <a name="january-2020"></a>Janeiro de 2020

Esta versão apresenta o formulário Recognizer 2,0 (visualização). Nas seções a seguir, você encontrará mais informações sobre novos recursos, aprimoramentos e alterações. 

### <a name="new-features"></a>Novos recursos

* **Modelo personalizado**
  * **Treinar com rótulos** Agora você pode treinar um modelo personalizado com dados rotulados manualmente. Esse método resulta em modelos de melhor desempenho e pode produzir modelos que funcionam com formulários ou formulários complexos contendo valores sem chaves.
  * **API assíncrona** Você pode usar chamadas de API assíncronas para treinar e analisar grandes conjuntos de dados e arquivos.
  * **Suporte a arquivo TIFF** Agora você pode treinar e extrair dados de documentos TIFF.
  * **Aprimoramentos de precisão de extração**

* **Modelo de recebimento predefinido**
  * **Valores de gorjeta** Agora você pode extrair valores de gorjeta e outros valores manuscritos.
  * **Extração de item de linha** Você pode extrair valores de item de linha de recebimentos.
  * **Valores de confiança** Você pode exibir a confiança do modelo para cada valor extraído.
  * **Aprimoramentos de precisão de extração**

* **Extração de layout** Agora você pode usar a API de layout para extrair dados de texto e dados de tabela de seus formulários.

### <a name="custom-model-api-changes"></a>Alterações da API do modelo personalizado

Todas as APIs para treinamento e uso de modelos personalizados foram renomeadas e alguns métodos síncronos agora são assíncronos. Estas são as principais alterações:

* O processo de treinamento de um modelo agora é assíncrono. Você inicia o treinamento por meio da chamada à API do **/Custom/Models** . Essa chamada retorna uma ID de operação, que pode ser passada em **Custom/Models/{ModelId}** para retornar os resultados de treinamento.
* A extração de chave/valor agora é iniciada pela chamada à API **/Custom/Models/{modelID}/Analyze** . Essa chamada retorna uma ID de operação, que pode ser passada em **Custom/Models/{ModelId}/analyzeResults/{resultid}** para retornar os resultados de extração.
* As IDs de operação para a operação de treinamento agora estão localizadas no cabeçalho **local** de respostas http, não no cabeçalho **Operation-Location** .

### <a name="receipt-api-changes"></a>Alterações da API de recebimento

As APIs para ler os recibos de vendas foram renomeadas.

* A extração de dados de recebimento agora é iniciada pela chamada à API **/prebuilt/Receipt/Analyze** . Essa chamada retorna uma ID de operação, que pode ser passada em **/prebuilt/Receipt/analyzeResults/{resultID}** para retornar os resultados de extração.

### <a name="output-format-changes"></a>Alterações de formato de saída

As respostas JSON para todas as chamadas de API têm novos formatos. Algumas chaves e valores foram adicionados, removidos ou renomeados. Consulte os guias de início rápido para obter exemplos dos formatos JSON atuais.

## <a name="next-steps"></a>Próximas etapas

Conclua um [guia de início rápido](quickstarts/client-library.md) para começar a escrever um aplicativo de processamento de formulários com o Reconhecimento de Formulários na linguagem de desenvolvimento de sua escolha.

## <a name="see-also"></a>Confira também

* [O que é o Reconhecimento de Formulários?](./overview.md)
