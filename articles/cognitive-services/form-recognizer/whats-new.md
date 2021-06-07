---
title: O que há de novo no Reconhecimento de Formulários?
titleSuffix: Azure Cognitive Services
description: Entenda as alterações mais recentes na API do Reconhecimento de Formulários.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 81115f5a9ed802f1d07c45ec928dc4b84ea2917b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048741"
---
<!-- markdownlint-disable MD024 -->
# <a name="whats-new-in-form-recognizer"></a>O que há de novo no Reconhecimento de Formulários?

O serviço Reconhecimento de Formulários é continuamente atualizado. Use este artigo para se atualizar sobre os aprimoramentos de recursos, correções e atualizações de documentação.

## <a name="march-2021"></a>Março de 2021

**A versão prévia pública 3 do Reconhecimento de Formulários v2.1 já está disponível.** O v2.1-preview.3 foi lançado, incluindo os seguintes recursos:

- **Novo modelo de ID predefinido** O novo modelo de ID predefinido permite que os clientes obtenham IDs e retornem dados estruturados para automatizar o processamento. Ele combina os poderosos recursos de OCR (reconhecimento óptico de caracteres) com modelos de reconhecimento de ID para extrair informações importantes de passaportes e carteiras de habilitação dos EUA, como nome, data de nascimento, data de emissão, data de validade e muito mais.

  [Saiba mais sobre o modelo de ID predefinido](concept-identification-cards.md)

   :::image type="content" source="./media/id-canada-passport-example.png" alt-text="exemplo de passaporte" lightbox="./media/id-canada-passport-example.png":::

- **Extração de item de linha para modelo de fatura predefinido** – O modelo de fatura predefinido agora dá suporte à extração de item de linha; agora é possível extrair itens completos e suas partes: descrição, valor, quantidade, ID do produto, data e muito mais. Com uma simples chamada à API/ao SDK, você pode extrair dados úteis de suas faturas: texto, tabela, pares chave-valor e itens de linha.

   [Saiba mais sobre o modelo de fatura predefinido](concept-invoices.md)

- **Rotulagem e treinamento de tabela supervisionado, rotulagem de valor vazio** – Além dos [recursos avançados de extração de tabela automáticos de aprendizado profundo](https://techcommunity.microsoft.com/t5/azure-ai/enhanced-table-extraction-from-documents-with-form-recognizer/ba-p/2058011) do Reconhecimento de Formulários, agora também é possível que os clientes façam um treinamento usando tabelas e as rotulem. Essa nova versão inclui a capacidade de fazer um treinamento usando itens de linha/tabelas (dinâmicos e fixos) e rotulá-los, além de treinar um modelo personalizado para extrair pares chave-valor e itens de linha. O modelo treinado será capaz de extrair itens de linha como parte da saída JSON na seção documentResults.

    :::image type="content" source="./media/table-labeling.png" alt-text="Rotulagem de tabela" lightbox="./media/table-labeling.png":::

    Além de rotular tabelas, você agora pode rotular valores e regiões vazios; se alguns documentos no seu conjunto de treinamento não tiverem valores para determinados campos, você poderá usar esse recurso para que o modelo saiba como extrair valores corretamente dos documentos analisados.

- **Suporte para 66 novos idiomas** – A API de Layout do Reconhecimento de Formulários e os modelos personalizados agora dão suporte a 73 idiomas.

  [Saiba mais sobre o suporte a idiomas do Reconhecimento de Formulários](language-support.md)

- **Ordem de leitura natural, classificação de manuscrito e seleção de página** – Com essa atualização, você pode optar por obter as saídas de linha de texto na ordem de leitura natural, em vez da ordenação padrão da esquerda para a direita e de cima para baixo. Use o novo parâmetro de consulta readingOrder e defina-o como valor "natural" para uma saída de ordem de leitura mais amigável. Além disso, para idiomas latinos, o Reconhecimento de Formulários classificará linhas de texto como estilo manuscrito ou não e dará uma pontuação de confiança.

- **Aprimoramentos de qualidade do modelo de Recibo predefinido** Essa atualização inclui várias melhorias de qualidade para o modelo de Recibo predefinido, especialmente relativo à extração de itens de linha.

## <a name="november-2020"></a>Novembro de 2020

### <a name="new-features"></a>Novos recursos

**A versão prévia pública 2 do Reconhecimento de Formulários v2.1 já está disponível.** O v2.1-preview.2 foi lançado, incluindo os seguintes recursos: 

- **Novo modelo de fatura predefinido** – O novo modelo de fatura predefinido permite que os clientes obtenham faturas em diversos formatos e retornem dados estruturados para automatizar o processamento das faturas. Ele combina nossos poderosos recursos de OCR (reconhecimento óptico de caracteres) com modelos de aprendizado profundo de reconhecimento de faturas para extrair informações importantes das faturas em inglês. Ele extrai o texto, as tabelas e as informações, como cliente, fornecedor, ID da fatura, data de vencimento da fatura, total, valor devido, valor do imposto, destinatário da remessa, recebedor da fatura e muito mais.

  > [Saiba mais sobre o modelo de fatura predefinido](concept-invoices.md)

  :::image type="content" source="./media/invoice-example.jpg" alt-text="exemplo de fatura" lightbox="./media/invoice-example.jpg":::

- **Extração de tabela aprimorada** – O Reconhecimento de Formulários agora oferece extração de tabela aprimorada, que combina nossos poderosos recursos de OCR (reconhecimento óptico de caracteres) com um modelo de extração de tabela de aprendizado profundo. O Reconhecimento de Formulários pode extrair dados de tabelas, incluindo tabelas complexas com colunas mescladas, linhas, sem bordas e muito mais. 
 
  :::image type="content" source="./media/tables-example.jpg" alt-text="exemplo de tabelas" lightbox="./media/tables-example.jpg":::

 
  > [Saiba mais sobre a Extração de layout](concept-layout.md)

- **Atualização da biblioteca de clientes** – As versões mais recentes das [bibliotecas de clientes](quickstarts/client-library.md) para .NET, Python, Java e JavaScript dão suporte à API do Reconhecimento de Formulários 2.1.
- **Novo idioma com suporte: japonês** – Os novos idiomas a seguir agora têm suporte: para `AnalyzeLayout` e `AnalyzeCustomForm`: Japonês (`ja`). [Suporte ao idioma](language-support.md)
- **Indicação de estilo de linha de texto (manuscrito/outro) (somente para idiomas latinos)** – O Reconhecimento de Formulários agora gera um objeto `appearance` que classifica se cada linha de texto está no estilo manuscrito ou não, juntamente com uma pontuação de confiança. Esse recurso é compatível apenas com idiomas latinos.
- **Aprimoramentos de qualidade** – Aprimoramentos de extração, incluindo melhorias de extração de dígito único.
- **Novo recurso Experimentar na Ferramenta de Amostra e Rotulagem do Reconhecimento de Formulários** – Capacidade de experimentar modelos de cartão de visita, recibos e fatura predefinidos e a API de Layout usando a Ferramenta de Amostra e Rotulagem do Reconhecimento de Formulários. Veja como os dados serão extraídos sem escrever nenhum código.

  > [Experimente a Ferramenta de Amostra do Reconhecimento de Formulários](https://fott-preview.azurewebsites.net/)

  ![Exemplo de FOTT](./media/ui-preview.jpg)
  
- **Loop de comentários** – Ao analisar arquivos por meio da ferramenta de rotulagem de amostra, agora você também pode adicioná-los ao conjunto de treinamento, ajustar os rótulos, se necessário, e treinar para aprimorar o modelo.
- **Rótulo automático de documentos** – rotula automaticamente documentos adicionais com base em documentos rotulados anteriormente no projeto.

## <a name="august-2020"></a>Agosto de 2020

### <a name="new-features"></a>Novos recursos

**A versão prévia pública do Reconhecimento de Formulários v2.1 já está disponível.** O v2.1-preview.1 foi lançado, incluindo os seguintes recursos: 


- A **referência da API REST está disponível** – Veja a [referência do v2.1-preview.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync) 
- **Novos idiomas com suporte. Além do inglês**, agora há suporte para os seguintes [idiomas](language-support.md): para `Layout` e `Train Custom Model`: inglês (`en`), chinês (simplificado) (`zh-Hans`), holandês (`nl`), francês (`fr`), alemão (`de`), italiano (`it`), português (`pt`) e espanhol (`es`).
- **Detecção de seleção/marca de seleção** – O Reconhecimento de Formulários dá suporte à detecção e extração de marcas de seleção, como caixas de seleção e botões de opção. As marcas de seleção são extraídas no `Layout`, e agora você também pode rotular e treinar no `Train Custom Model` - _Treinar com Rótulos_ para extrair pares chave-valor de marcas de seleção. 
- **Composição de modelo** – permite que vários modelos sejam compostos e chamados com uma única ID de modelo. Quando um documento é enviado para ser analisado com uma ID de modelo composto, uma etapa de classificação é executada primeiro para encaminhá-lo para o modelo personalizado correto. A Composição de modelo está disponível para `Train Custom Model` - _Treinar com rótulos_.
- **Nome do modelo** – adicione um nome amigável a seus modelos personalizados para facilitar o gerenciamento e o acompanhamento.
- **[Novo modelo predefinido para cartões de visita](concept-business-cards.md)** para extrair campos comuns em cartões de visita no idioma inglês.
- **[Novas localidades para Recibos predefinidos](concept-receipts.md)** , além do EN-US, agora também há suporte para EN-AU, EN-CA, EN-GB, EN-IN
- **Aprimoramentos de qualidade** para `Layout`, `Train Custom Model` - _Treinar sem Rótulos_ e _Treinar com Rótulos_.

A **v2.0** inclui a seguinte atualização:

- As [bibliotecas de cliente](quickstarts/client-library.md) para .NET, Python, Java e JavaScript estão em disponibilidade geral. 

Há **novas amostras** disponíveis no GitHub. 

- O [Guia estratégico de formulários – receitas de extração de conhecimento](https://github.com/microsoft/knowledge-extraction-recipes-forms) reúne as melhores práticas de compromissos reais de clientes do Reconhecimento de Formulários e oferece exemplos de código, listas de verificação e pipelines de exemplos funcionais usados no desenvolvimento desses projetos. 
- A [ferramenta de rotulagem de amostras](https://github.com/microsoft/OCR-Form-Tools) foi atualizada para dar suporte à nova funcionalidade da v2.1. Consulte este [início rápido](quickstarts/label-tool.md) para começar a usar a ferramenta. 
- A amostra [Quiosque Inteligente](https://github.com/microsoft/Cognitive-Samples-IntelligentKiosk/blob/master/Documentation/FormRecognizer.md) do Reconhecimento de Formulários mostra como integrar `Analyze Receipt` e `Train Custom Model` - _Treinar sem Rótulos_.

## <a name="july-2020"></a>Julho de 2020

### <a name="new-features"></a>Novos recursos
<!-- markdownlint-disable MD004 -->
* **referência da v2.0 disponível** – Confira a [Referência da API v2.0](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) e os SDKs atualizados para [.NET](/dotnet/api/overview/azure/ai.formrecognizer-readme), [Python](/python/api/overview/azure/), [Java](/java/api/overview/azure/ai-formrecognizer-readme) e [JavaScript](/javascript/api/overview/azure/).
* **Aprimoramentos de tabela e aprimoramentos de extração** – inclui aprimoramentos de precisão e aprimoramentos de extrações de tabela, especificamente a capacidade de aprender cabeçalhos e estruturas de tabelas em _treinamento personalizado sem rótulos_. 

* **Suporte a moedas** – Detecção e extração de símbolos de moeda globais.
* **Azure Gov** O reconhecimento de formulários agora também está disponível no Azure Gov.
* **Recursos de segurança aprimorados**: 
  * **Bring Your Own Key** – O Reconhecimento de Formulários criptografa automaticamente seus dados quando persistidos na nuvem para protegê-los e ajudá-lo a alcançar os compromissos de segurança e conformidade da organização. Por padrão, sua assinatura usa chaves de criptografia gerenciadas pela Microsoft. Agora você também pode gerenciar sua assinatura com suas próprias chaves de criptografia. [As chaves gerenciadas pelo cliente, também conhecidas como Bring Your Own Key (BYOK)](./encrypt-data-at-rest.md), oferecem maior flexibilidade para criar, alternar, desabilitar e revogar controles de acesso. Você também pode auditar as chaves de criptografia usadas para proteger seus dados.  
  * **Pontos de extremidade privados** – Permite que você [acesse dados com segurança por meio de um Link Privado](../../private-link/private-link-overview.md) em uma VNet (rede virtual).

## <a name="june-2020"></a>Junho de 2020

### <a name="new-features"></a>Novos recursos

* **API CopyModel adicionada aos SDKs cliente** – Agora você pode usar os SDKs cliente para copiar modelos de uma assinatura para outra. Confira [Fazer backup e recuperar modelos](./disaster-recovery.md) para obter informações gerais sobre esse recurso.
* **Integração do Azure Active Directory** – Agora você pode usar suas credenciais do Azure AD para autenticar os objetos cliente do Reconhecimento de Formulários nos SDKs.
* **Alterações específicas do SDK** – Inclui adições de recursos menores e alterações interruptivas. Consulte os logs de mudanças do SDK para obter mais informações.
  * [Log de mudanças do SDK do C# Preview 3](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/formrecognizer/Azure.AI.FormRecognizer/CHANGELOG.md)
  * [Log de mudanças do SDK do Python Preview 3](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Log de mudanças do SDK do Java Preview 3](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Log de mudanças do SDK do JavaScript Preview 3](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_1.0.0-preview.3/sdk/formrecognizer/ai-form-recognizer/CHANGELOG.md)

## <a name="april-2020"></a>Abril de 2020

### <a name="new-features"></a>Novos recursos

* **Suporte do SDK para a versão prévia pública da API do Reconhecimento de Formulários v2.0** – Neste mês, expandimos nosso suporte de serviço para incluir um SDK de versão prévia para o lançamento do Reconhecimento de Formulários v2.0 (versão prévia). Use os links abaixo para começar com o idioma de sua escolha: 
  * [SDK .NET](/dotnet/api/overview/azure/ai.formrecognizer-readme)
  * [Java SDK](/java/api/overview/azure/ai-formrecognizer-readme)
  * [SDK do Python](/python/api/overview/azure/ai-formrecognizer-readme)
  * [SDK do JavaScript](/javascript/api/overview/azure/ai-form-recognizer-readme)

  O novo SDK dá suporte a todos os recursos da API REST v2.0 para o Reconhecimento de Formulários. Por exemplo, você pode treinar um modelo com ou sem rótulos e extrair texto, pares chave-valor e tabelas de seus formulários, extrair dados de recibos com o serviço de recibos predefinidos e extrair texto e tabelas com o serviço de layout em seus documentos. Você pode compartilhar seus comentários sobre os SDKs por meio do [formulário de comentários do SDK](https://aka.ms/FR_SDK_v1_feedback).

* **Copiar Modelo Personalizado** Agora você pode copiar modelos entre regiões e assinaturas usando o novo recurso Copiar Modelo Personalizado. Antes de invocar a API Copiar Modelo Personalizado, primeiro você deve obter autorização para copiar no recurso de destino chamando a operação de Autorização de Cópia no ponto de extremidade do recurso de destino.

  * API REST [gerar uma autorização de cópia](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModelAuthorization)
  * API REST [copiar um modelo personalizado](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModel) 

### <a name="security-improvements"></a>Aprimoramentos de segurança

* As chaves gerenciadas pelo cliente já estão disponíveis para o FormRecognizer. Para obter mais informações, consulte [Criptografia de dados em repouso para o Reconhecimento de Formulários](./encrypt-data-at-rest.md).
* Use identidades gerenciadas para acessar recursos do Azure com o Azure Active Directory. Para obter mais informações, consulte [Autorizar o acesso a identidades gerenciadas](../authentication.md#authorize-access-to-managed-identities).

## <a name="march-2020"></a>Março de 2020

### <a name="new-features"></a>Novos recursos

* **Tipos de valor para rotulagem** Agora você pode especificar os tipos de valores que está rotulando com a ferramenta de rotulagem de amostra do Reconhecimento de Formulários. Os tipos de valor e as variações a seguir são compatíveis no momento:
  * `string`
    * padrão, `no-whitespaces`, `alphanumeric`
  * `number`
    * padrão, `currency`
  * `date` 
    * padrão, `dmy`, `mdy`, `ymd`
  * `time`
  * `integer`

  Confira o guia da [Ferramenta de rotulagem de amostra](./quickstarts/label-tool.md#specify-tag-value-types) para saber como usar esse recurso.


* **Visualização de tabela** A ferramenta de rotulagem de amostra agora exibe tabelas que foram reconhecidas no documento. Esse recurso permite exibir as tabelas que foram reconhecidas e extraídas do documento, antes de rotular e analisar. Esse recurso pode ser ativado/desativado usando a opção de camadas.

  A imagem a seguir é um exemplo de como as tabelas são reconhecidas e extraídas:

  > [!div class="mx-imgBorder"]
  > ![Visualização de tabela na ferramenta de rotulagem de amostra](./media/whats-new/table-viz.png)

    As tabelas extraídas estão disponíveis na saída JSON em `"pageResults"`.

  > [!IMPORTANT]
  > Não há suporte para rotular tabelas. Se as tabelas não forem reconhecidas nem extraídas automaticamente, você só poderá rotulá-las como pares chave/valor. Ao rotular tabelas como pares chave/valor, rotule cada célula como um valor exclusivo.

### <a name="extraction-enhancements"></a>Aprimoramentos de extração

Esta versão inclui aprimoramentos de extração e melhorias de precisão, especificamente, a capacidade de rotular e extrair vários pares chave/valor na mesma linha de texto. 
 
### <a name="sample-labeling-tool-is-now-open-source"></a>A ferramenta de rotulagem de amostra agora é de software livre

A ferramenta de rotulagem de amostra do Reconhecimento de Formulários agora está disponível como um projeto de software livre. Você pode integrá-la em suas soluções e fazer alterações específicas do cliente para atender às suas necessidades.

Para obter mais informações sobre a ferramenta de rotulagem de amostra do Reconhecimento de Formulários, examine a documentação disponível no [GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

### <a name="tls-12-enforcement"></a>Imposição do TLS 1.2

Agora o TLS 1.2 é obrigatório para todas as solicitações HTTP a este serviço. Para saber mais, confira [Segurança nos Serviços Cognitivos do Azure](../cognitive-services-security.md).

## <a name="january-2020"></a>Janeiro de 2020

Esta versão apresenta o Reconhecimento de Formulários 2.0 (versão prévia). Nas seções a seguir, você encontrará mais informações sobre novos recursos, aprimoramentos e alterações. 

### <a name="new-features"></a>Novos recursos

* **Modelo personalizado**
  * **Treinar com rótulos** Agora você pode treinar um modelo personalizado usando dados rotulados manualmente. Esse método resulta em modelos de melhor desempenho e pode produzir modelos que funcionam com formulários complexos ou formulários que contêm valores sem chaves.
  * **API Assíncrona** Você pode usar chamadas à API assíncronas para treinar e analisar grandes conjuntos de dados e arquivos.
  * **Suporte a arquivos TIFF** Agora você pode treinar e extrair dados de documentos TIFF.
  * **Aprimoramentos na precisão da extração**

* **Modelo de recibo predefinido**
  * **Valores de gorjeta** Agora você pode extrair valores de gorjeta e outros valores manuscritos.
  * **Extração de item de linha** Você pode extrair valores de item de linha de recibos.
  * **Valores de confiança** Você pode exibir a confiança do modelo para cada valor extraído.
  * **Aprimoramentos na precisão da extração**

* **Extração de layout** Agora você pode usar a API de Layout para extrair dados de texto e dados de tabela em seus formulários.

### <a name="custom-model-api-changes"></a>Alterações da API de modelo personalizado

Todas as APIs para treinamento e uso de modelos personalizados foram renomeadas e alguns métodos síncronos agora são assíncronos. A seguir estão as alterações mais significativas:

* O processo de treinamento de um modelo agora é assíncrono. Você inicia o treinamento por meio da chamada à API **/custom/models**. Essa chamada retorna uma ID de operação, que pode ser passada em **custom/models/{modelID}** para retornar os resultados de treinamento.
* A extração de chave/valor agora é iniciada pela chamada à API **/custom/models/{modelID}/analyze**. Essa chamada retorna uma ID de operação, que pode ser passada em **custom/models/{modelID}/analyzeResults/{resultID}** para retornar os resultados de extração.
* As IDs de operação para a operação de treinamento agora estão localizadas no cabeçalho **Location** das respostas HTTP, não no cabeçalho **Operation-Location**.

### <a name="receipt-api-changes"></a>Alterações da API de recibo

As APIs para ler os recibos de vendas foram renomeadas.

* A extração de dados de recibos agora é iniciada pela chamada à API **/prebuilt/receipt/analyze**. Essa chamada retorna uma ID de operação, que pode ser passada em **/prebuilt/receipt/analyzeResults/{resultID}** para retornar os resultados de extração.

### <a name="output-format-changes"></a>Alterações do formato de saída

As respostas JSON para todas as chamadas à API têm novos formatos. Algumas chaves e valores foram adicionados, removidos ou renomeados. Consulte os guias de início rápido para obter exemplos dos formatos JSON atuais.

## <a name="next-steps"></a>Próximas etapas

Conclua um [guia de início rápido](quickstarts/client-library.md) para começar a escrever um aplicativo de processamento de formulários com o Reconhecimento de Formulários na linguagem de desenvolvimento de sua escolha.

## <a name="see-also"></a>Confira também

* [O que é o Reconhecimento de Formulários?](./overview.md)