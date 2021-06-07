---
title: Enviar e receber mensagens X12 para B2B
description: Trocar mensagens X12 para cenários de integração corporativa B2B usando Aplicativos Lógicos do Azure com Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/29/2020
ms.openlocfilehash: 87a2bcc386ec5688fadb68aabdd2e5239e205516
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077458"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Trocar mensagens X12 para Enterprise Integration B2B nos Aplicativos Lógicos do Azure com Enterprise Integration Pack

Para trabalhar com mensagens X12 nos Aplicativos Lógicos do Azure, você pode usar o conector X12, que fornece gatilhos e ações para gerenciar a comunicação X12. Para saber mais sobre mensagens EDIFACT, confira [Trocar mensagens EDIFACT](logic-apps-enterprise-integration-edifact.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso você ainda não tenha uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* O aplicativo lógico de onde você deseja usar o conector X12 e um gatilho que inicia o fluxo de trabalho do aplicativo lógico. O conector X12 fornece apenas ações, não gatilhos. Se ainda não estiver familiarizado com aplicativo lógicos, consulte [O que são os Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Início Rápido: criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) associada à sua assinatura do Azure e vinculada ao aplicativo lógico em que você planeja usar o conector X12. O aplicativo lógico e a conta de integração devem existir no mesmo local ou região do Azure.

* Pelo menos dois [parceiros comerciais](../logic-apps/logic-apps-enterprise-integration-partners.md) que você já definiu em sua conta de integração usando o qualificador de identidade X12.

* Os [esquemas](../logic-apps/logic-apps-enterprise-integration-schemas.md) a serem usados para validação XML que você já adicionou à sua conta de integração. Se você estiver trabalhando com esquemas HIPAA (Health Insurance Portability and Accountability Act), confira [Esquemas HIPAA](#hipaa-schemas).

* Antes de poder usar o conector X12, você deve criar um [contrato](../logic-apps/logic-apps-enterprise-integration-agreements.md) X12 entre seus parceiros comerciais e armazená-lo em sua conta de integração. Se você estiver trabalhando com esquemas HIPAA (Health Insurance Portability and Accountability Act), precisará adicionar uma seção `schemaReferences` ao seu contrato. Para saber mais, confira [Esquemas HIPAA](#hipaa-schemas).

<a name="receive-settings"></a>

## <a name="receive-settings"></a>Configurações de Recebimento

Depois de definir as propriedades do contrato, você pode configurar como este contrato identifica e lida com as mensagens de entrada que você recebe do seu parceiro por meio deste contrato.

1. Em **Adicionar**, selecione **Configurações de Recebimento**.

1. Configure essas propriedades com base em seu contrato com o parceiro que troca mensagens com você. As **Configurações de Recebimento** são organizadas nas seguintes seções:

   * [Identificadores](#inbound-identifiers)
   * [Confirmação](#inbound-acknowledgement)
   * [Esquemas](#inbound-schemas)
   * [Envelopes](#inbound-envelopes)
   * [Números de Controle](#inbound-control-numbers)
   * [Validações](#inbound-validations)
   * [Configurações Internas](#inbound-internal-settings)

   Para obter as descrições da propriedade, confira as tabelas nesta seção.

1. Quando terminar, salve as configurações selecionando **OK**.

<a name="inbound-identifiers"></a>

### <a name="receive-settings---identifiers"></a>Configurações de Recebimento - Identificadores

![Propriedades do identificador para mensagens de entrada](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-identifiers.png)

| Propriedade | Descrição |
|----------|-------------|
| **ISA1 (Qualificador de Autorização)** | O valor do Qualificador de Autorização que você deseja usar. O valor padrão é **00 - Nenhuma informação de autorização presente**. <p>**Observação**: Se você selecionar outros valores, especifique um valor para a propriedade **ISA2**. |
| **ISA2** | O valor de Informação de Autorização a ser usado quando a propriedade **ISA1** não for **00 - Nenhuma informação de autorização presente**. Esse valor da propriedade deve ter no mínimo um caractere alfanumérico e no máximo 10. |
| **ISA3 (Qualificador de Segurança)** | O valor do Qualificador de Segurança que você deseja usar. O valor padrão é **00 - Nenhuma informação de segurança presente**. <p>**Observação**: Se você selecionar outros valores, especifique um valor para a propriedade **ISA4**. |
| **ISA4** | O valor de Informação de Segurança a ser usado quando a propriedade **ISA3** não for **00 - Nenhuma informação de segurança presente**. Esse valor da propriedade deve ter no mínimo um caractere alfanumérico e no máximo 10. |
|||

<a name="inbound-acknowledgement"></a>

### <a name="receive-settings---acknowledgement"></a>Configurações de Recebimento - Confirmação

![Confirmação para mensagens de entrada](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-acknowledgement.png)

| Propriedade | Descrição |
|----------|-------------|
| **TA1 esperado** | Retorna uma confirmação técnica (TA1) ao emissor de intercâmbio. |
| **FA esperado** | Retorna uma confirmação funcional (FA) ao emissor de intercâmbio. <p>Para a propriedade **Versão de FA**, com base na versão do esquema, selecione as confirmações 997 ou 999. <p>Para habilitar a geração de loops AK2 em confirmações funcionais para conjuntos de transações aceitas, selecione **Incluir Loop AK2/IK2**. |

<a name="inbound-schemas"></a>

### <a name="receive-settings---schemas"></a>Configurações de Recebimento - Esquemas

![Esquemas para mensagens de entrada](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-schemas.png)

Para esta seção, selecione um [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) da sua [conta de integração](./logic-apps-enterprise-integration-create-integration-account.md) para cada tipo de transação (ST01) e Aplicativo Remetente (GS02). O Pipeline de Recebimento EDI desmonta a mensagem recebida, combinando os valores e o esquema que você define nesta seção com os valores de ST01 e GS02 na mensagem recebida e com o esquema da mensagem recebida. Depois de concluir cada linha, uma nova linha vazia é exibida automaticamente.

| Propriedade | Descrição |
|----------|-------------|
| **Versão** | A versão X12 para o esquema |
| **Tipo de Transação (ST01)** | O tipo de transação |
| **Aplicativo do Remetente (GS02)** | O aplicativo do remetente |
| **Esquema** | O arquivo de esquema que você deseja usar |
|||

<a name="inbound-envelopes"></a>

### <a name="receive-settings---envelopes"></a>Configurações de Recebimento - Envelopes

![Separadores a serem usados em conjuntos de transações para mensagens de entrada](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-envelopes.png)

| Propriedade | Descrição |
|----------|-------------|
| **Uso de ISA11** | O separador a ser usado em um conjunto de transações: <p>- **Identificador padrão**: usa um ponto (.) em notação decimal, em vez de uma notação decimal no documento de entrada do Pipeline de Recebimento EDI. <p>- **Separador de repetição**: especifica o separador para as ocorrências repetidas de um elemento de dados simples ou uma estrutura de dados repetidos. Por exemplo, geralmente o circunflexo (^) é usado como o separador de repetição. Para esquemas HIPAA, só é possível usar circunflexo (^). |
|||

<a name="inbound-control-numbers"></a>

### <a name="receive-settings---control-numbers"></a>Configurações de Recebimento - Números de Controle

![Tratamento de duplicatas de número de controle para mensagens de entrada](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-control-numbers.png) 

| Propriedade | Descrição |
|----------|-------------|
| **Não permitir duplicatas do número de controle de intercâmbio** | Bloqueia o intercâmbio de duplicatas. Verifica o número de controle de intercâmbio (ISA13) para o número de controle de intercâmbio recebido. Se uma correspondência for detectada, o Pipeline de Recebimento EDI não processará o intercâmbio. <p><p>Para especificar o número de dias para executar a verificação, insira um valor para a propriedade **Verificação de ISA13 duplicados a cada (dias)** . |
| **Não permitir duplicatas do número de controle de grupo** | Bloqueia intercâmbios que tenham números de controle de grupo em duplicata. |
| **Não permitir duplicatas do número de controle do conjunto de transações** | Bloqueia intercâmbios que tenham números de controle de conjunto de transações em duplicata. |
|||

<a name="inbound-validations"></a>

### <a name="receive-settings---validations"></a>Configurações de Recebimento - Validações

![Validações para mensagens de entrada](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-validations.png)

A linha **Padrão** mostra as regras de validação usadas para um tipo de mensagem EDI. Se você deseja definir regras diferentes, selecione cada caixa em que deseja que a regra seja definida como **true**. Depois de concluir cada linha, uma nova linha vazia é exibida automaticamente.

| Propriedade | Descrição |
|----------|-------------|
| **Tipo de mensagem** | O tipo de mensagem EDI |
| **Validação de EDI** | Executa a validação de EDI nos tipos de dados conforme a definição das propriedades de EDI do esquema, restrições de comprimento, elementos de dados vazios e separadores à direita. |
| **Validação Estendida** | Se o tipo de dados não for EDI, a validação será realizada nos requisitos de elementos de dados e na permissão de repetições, enumerações e validação de tamanho de elementos de dados (mín. ou máx.). |
| **Permitir Zeros à Esquerda/Direita** | Mantém zeros à esquerda ou à direita adicionais e caracteres de espaço. Não remova esses caracteres. |
| **Cortar Zeros à Esquerda/Direita** | Remove zeros à esquerda ou à direita e caracteres de espaço. |
| **Política de Separador à Direita** | Gera separadores à direita. <p>- **Não permitido**: proíbe delimitadores e separadores à direita no intercâmbio de entrada. Se o intercâmbio tiver delimitadores e separadores à direita, o intercâmbio é declarado não válido. <p>- **Opcional**: aceita intercâmbios com ou sem delimitadores e separadores à direita. <p>- **Obrigatório**: o intercâmbio de entrada deve ter delimitadores e separadores à direita. |
|||

<a name="inbound-internal-settings"></a>

### <a name="receive-settings---internal-settings"></a>Configurações de Recebimento - Configurações Internas

![Configurações internas para mensagens de entrada](./media/logic-apps-enterprise-integration-x12/x12-receive-settings-internal-settings.png)

| Propriedade | Descrição |
|----------|-------------|
| **Converter o formato decimal implícito Nn para o valor numérico de base 10** | Converte um número de EDI especificado no formato "Nn" em um valor numérico de base 10. |
| **Criar marcas XML vazias se forem permitidos separadores à direita** | Faz com que o remetente do intercâmbio inclua marcas XML vazias para separadores à direita. |
| **Dividir intercâmbio como conjuntos de transação – suspender conjuntos de transação com erro** | Analisa cada conjunto de transações em um intercâmbio dentro de um documento XML separado ao aplicar o envelope apropriado ao conjunto de transações. Suspende somente as transações em que a validação falha. |
| **Dividir intercâmbio como conjuntos de transação – suspender intercâmbio com erro** | Analisa cada conjunto de transações em um intercâmbio dentro de um documento XML separado ao aplicar o envelope apropriado. Suspende o intercâmbio inteiro se um ou mais conjuntos de transações no intercâmbio falharem na validação. |
| **Preservar intercâmbio – suspender conjuntos de transações com erro** | Mantém o intercâmbio intacto e cria um documento XML para o intercâmbio em lote inteiro. Suspende somente os conjuntos de transações com falha na validação, mas continua a processar todos os outros conjuntos de transações. |
| **Preservar intercâmbio – suspender intercâmbio com erro** |Mantém o intercâmbio intacto, cria um documento XML para o intercâmbio em lote inteiro. Suspende o intercâmbio inteiro se um ou mais conjuntos de transações no intercâmbio falharem na validação. |
|||

<a name="send-settings"></a>

## <a name="send-settings"></a>Configurações de Envio

Depois de definir as propriedades do contrato, você pode configurar como este contrato identifica e lida com as mensagens de saída que você envia ao seu parceiro por meio deste contrato.

1. Em **Adicionar**, selecione **Configurações de Envio**.

1. Configure essas propriedades com base em seu contrato com o parceiro que troca mensagens com você. Para obter as descrições da propriedade, confira as tabelas nesta seção.

   As **Configurações de Envio** são organizadas nas seguintes seções:

   * [Identificadores](#outbound-identifiers)
   * [Confirmação](#outbound-acknowledgement)
   * [Esquemas](#outbound-schemas)
   * [Envelopes](#outbound-envelopes)
   * [Número de Versão de Controle](#outbound-control-version-number)
   * [Números de Controle](#outbound-control-numbers)
   * [Conjuntos de Caracteres e Separadores](#outbound-character-sets-separators)
   * [Validação](#outbound-validation)

1. Quando terminar, salve as configurações selecionando **OK**.

<a name="outbound-identifiers"></a>

### <a name="send-settings---identifiers"></a>Configurações de Envio - Identificadores

![Propriedades do identificador para mensagens de saída](./media/logic-apps-enterprise-integration-x12/x12-send-settings-identifiers.png)

| Propriedade | Descrição |
|----------|-------------|
| **ISA1 (Qualificador de Autorização)** | O valor do Qualificador de Autorização que você deseja usar. O valor padrão é **00 - Nenhuma informação de autorização presente**. <p>**Observação**: Se você selecionar outros valores, especifique um valor para a propriedade **ISA2**. |
| **ISA2** | O valor de Informação de Autorização a ser usado quando a propriedade **ISA1** não for **00 - Nenhuma informação de autorização presente**. Esse valor da propriedade deve ter no mínimo um caractere alfanumérico e no máximo 10. |
| **ISA3 (Qualificador de Segurança)** | O valor do Qualificador de Segurança que você deseja usar. O valor padrão é **00 - Nenhuma informação de segurança presente**. <p>**Observação**: Se você selecionar outros valores, especifique um valor para a propriedade **ISA4**. |
| **ISA4** | O valor de Informação de Segurança a ser usado quando a propriedade **ISA3** não for **00 - Nenhuma informação de segurança presente**. Esse valor da propriedade deve ter no mínimo um caractere alfanumérico e no máximo 10. |
|||

<a name="outbound-acknowledgement"></a>

### <a name="send-settings---acknowledgement"></a>Configurações de Envio - Confirmação

![Propriedades de confirmação para mensagens de saída](./media/logic-apps-enterprise-integration-x12/x12-send-settings-acknowledgement.png)

| Propriedade | Descrição |
|----------|-------------|
| **TA1 esperado** | Retorna uma confirmação técnica (TA1) ao emissor de intercâmbio. <p>Essa configuração especifica que o parceiro host que está enviando a mensagem solicitou uma confirmação do parceiro convidado no contrato. Essas confirmações são esperadas pelo parceiro host com base nas Configurações de Recebimento do contrato. |
| **FA esperado** | Retorna uma confirmação funcional (FA) ao emissor de intercâmbio. Para a propriedade **Versão de FA**, com base na versão do esquema, selecione as confirmações 997 ou 999. <p>Essa configuração especifica que o parceiro host que está enviando a mensagem solicitou uma confirmação do parceiro convidado no contrato. Essas confirmações são esperadas pelo parceiro host com base nas Configurações de Recebimento do contrato. |
|||

<a name="outbound-schemas"></a>

### <a name="send-settings---schemas"></a>Configurações de Envio - Esquemas

![Esquemas para mensagens de saída](./media/logic-apps-enterprise-integration-x12/x12-send-settings-schemas.png)

Para esta seção, selecione um [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) da sua [conta de integração](./logic-apps-enterprise-integration-create-integration-account.md) para cada tipo de transação (ST01). Depois de concluir cada linha, uma nova linha vazia é exibida automaticamente.

| Propriedade | Descrição |
|----------|-------------|
| **Versão** | A versão X12 para o esquema |
| **Tipo de Transação (ST01)** | O tipo de transação para o esquema |
| **Esquema** | O arquivo de esquema que você deseja usar. Se você selecionar o esquema primeiro, a versão e o tipo de transação serão definidos automaticamente. |
|||

<a name="outbound-envelopes"></a>

### <a name="send-settings---envelopes"></a>Configurações de Envio - Envelopes

![Separadores em um conjunto de transações a ser usado para mensagens de saída](./media/logic-apps-enterprise-integration-x12/x12-send-settings-envelopes.png)

| Propriedade | Descrição |
|----------|-------------|
| **Uso de ISA11** | O separador a ser usado em um conjunto de transações: <p>- **Identificador padrão**: usa um ponto (.) em notação decimal, em vez de uma notação decimal no documento de saída do Pipeline de Envio EDI. <p>- **Separador de repetição**: especifica o separador para as ocorrências repetidas de um elemento de dados simples ou uma estrutura de dados repetidos. Por exemplo, geralmente o circunflexo (^) é usado como o separador de repetição. Para esquemas HIPAA, só é possível usar circunflexo (^). |
|||

<a name="outbound-control-version-number"></a>

### <a name="send-settings---control-version-number"></a>Configurações de Envio - Número de Versão de Controle

![Número de versão de controle para mensagens de saída](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-version-number.png)

Para esta seção, selecione um [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) da sua [conta de integração](./logic-apps-enterprise-integration-create-integration-account.md) para cada intercâmbio. Depois de concluir cada linha, uma nova linha vazia é exibida automaticamente.

| Propriedade | Descrição |
|----------|-------------|
| **Número de Versão de Controle (ISA12)** | A versão do padrão X12 |
| **Indicador de Uso (ISA15)** | O contexto de um intercâmbio, que consiste em dados de **Teste**, dados de **Informações** ou dados de **Produção** |
| **Esquema** | O esquema a ser usado para gerar os segmentos GS e ST para um intercâmbio de codificação X12 que ele envia para o Pipeline de Envio EDI. |
| **GS1** | Opcional, seleciona o código funcional. |
| **GS2** | Opcional, especifica o remetente do aplicativo. |
| **GS3** | Opcional, especifica o receptor do aplicativo. |
| **GS4** | Opcional, selecionar **CCYYMMDD** ou **YYMMDD**. |
| **GS5** | Opcional, selecionar **HHMM**, **HHMMSS** ou **HHMMSSdd**. |
| **GS7** | Opcional, selecionar um valor para a agência responsável. |
| **GS8** | Opcional, especifica a versão do documento de esquema. |
|||

<a name="outbound-control-numbers"></a>

### <a name="send-settings---control-numbers"></a>Configurações de Envio - Números de Controle

![Números de controle para mensagens de saída](./media/logic-apps-enterprise-integration-x12/x12-send-settings-control-numbers.png)

| Propriedade | Descrição |
|----------|-------------|
| **Número de Controle de Intercâmbio (ISA13)** | O intervalo de valores para o número de controle de intercâmbio, que pode ter um valor mínimo de 1 e um valor máximo de 999999999 |
| **Número de Controle de Grupo (GS06)** | O intervalo de valores para o número de controle de grupo, que pode ter um valor mínimo de 1 e um valor máximo de 999999999 |
| **Número de Controle de Conjunto de Transações (ST02)** | O intervalo de valores para o número de controle do conjunto de transações, que pode ter um valor mínimo de 1 e um valor máximo de 999999999 <p>- **Prefixo**: Opcional, um valor alfanumérico <br>- **Sufixo**: Opcional, um valor alfanumérico |
|||

<a name="outbound-character-sets-separators"></a>

### <a name="send-settings---character-sets-and-separators"></a>Configurações de Envio - Conjuntos de Caracteres e Separadores

![Delimitadores para tipos de mensagem em mensagens de saída](./media/logic-apps-enterprise-integration-x12/x12-send-settings-character-sets-separators.png)

A linha **Padrão** mostra o conjunto de caracteres usado como delimitadores para um esquema de mensagens. Se não quiser usar o conjunto de caracteres  **Padrão**, você pode inserir um conjunto diferente de delimitadores para cada tipo de mensagem. Depois de concluir cada linha, uma nova linha vazia é exibida automaticamente.

> [!TIP]
> Para fornecer valores de caractere especial, edite o contrato como JSON e forneça o valor ASCII para o caractere especial.

| Propriedade | Descrição |
|----------|-------------|
| **Conjunto de Caracteres a ser usado** | O conjunto de caracteres X12, que é **Básico**, **Estendido** ou **UTF8**. |
| **Esquema** | O esquema que você deseja usar. Depois de selecionar o esquema, selecione o conjunto de caracteres que deseja usar, com base nas descrições do separador abaixo. |
| **Tipo de Entrada** | O tipo de entrada para o conjunto de caracteres. |
| **Separador de Componente** | Um único caractere que separa elementos de dados compostos. |
| **Separador de Elemento de Dados** | Um único caractere que separa elementos de dados simples nos dados compostos. |
| **Separador de Caracteres de Substituição** | Um caractere de substituição que substitui todos os caracteres separadores dos dados de conteúdo ao gerar a mensagem X12 de saída. |
| **Terminador de Segmento** | Um único caractere que indica o final de um segmento EDI. |
| **Suffix** | O caractere a ser usado com o identificador de segmento. Se especificar um sufixo, o elemento de dados de terminador de segmento poderá ser esvaziado. Se o terminador de segmento for deixado vazio, será necessário designar um sufixo. |
|||

<a name="outbound-validation"></a>

### <a name="send-settings---validation"></a>Configurações de Envio - Validação

![Propriedades de validação para mensagens de saída](./media/logic-apps-enterprise-integration-x12/x12-send-settings-validation.png) 

A linha **Padrão** mostra as regras de validação usadas para um tipo de mensagem EDI. Se você deseja definir regras diferentes, selecione cada caixa em que deseja que a regra seja definida como **true**. Depois de concluir cada linha, uma nova linha vazia é exibida automaticamente.

| Propriedade | Descrição |
|----------|-------------|
| **Tipo de mensagem** | O tipo de mensagem EDI |
| **Validação de EDI** | Executa a validação de EDI nos tipos de dados conforme a definição das propriedades de EDI do esquema, restrições de comprimento, elementos de dados vazios e separadores à direita. |
| **Validação Estendida** | Se o tipo de dados não for EDI, a validação será realizada nos requisitos de elementos de dados e na permissão de repetições, enumerações e validação de tamanho de elementos de dados (mín. ou máx.). |
| **Permitir Zeros à Esquerda/Direita** | Mantém zeros à esquerda ou à direita adicionais e caracteres de espaço. Não remova esses caracteres. |
| **Cortar Zeros à Esquerda/Direita** | Remove zeros à esquerda ou à direita e caracteres de espaço. |
| **Política de Separador à Direita** | Gera separadores à direita. <p>- **Não permitido**: proíbe delimitadores e separadores à direita no intercâmbio de saída. Se o intercâmbio tiver delimitadores e separadores à direita, o intercâmbio é declarado não válido. <p>- **Opcional**: envia intercâmbios com ou sem delimitadores e separadores à direita. <p>- **Obrigatório**: o intercâmbio de saída deve ter delimitadores e separadores à direita. |
|||

<a name="hipaa-schemas"></a>

## <a name="hipaa-schemas-and-message-types"></a>Tipos de mensagens e esquemas HIPAA

Ao trabalhar com esquemas HIPAA e os tipos de mensagem 277 ou 837, é necessário executar algumas etapas adicionais. Os [números de versão do documento (GS8)](#outbound-control-version-number) para esses tipos de mensagens têm mais e 9 caracteres, por exemplo, "005010X222A1". Além disso, alguns números de versão do documento são mapeados para tipos de mensagens variantes. Se você não fizer referência ao tipo de mensagem correto no seu esquema e no seu contrato, receberá esta mensagem de erro:

`"The message has an unknown document type and did not resolve to any of the existing schemas configured in the agreement."`

Esta tabela lista os tipos de mensagens afetados, quaisquer variantes e os números de versão do documento que são mapeados para esses tipos de mensagens:

| Tipo ou variante de mensagem |  Descrição | Número da versão do documento (GS8) |
|-------------------------|--------------|-------------------------------|
| 277 | Notificação de status de informações de assistência médica | 005010X212 |
| 837_I | Declaração de assistência médica institucional | 004010X096A1 <br>005010X223A1 <br>005010X223A2 |
| 837_D | Declaração de assistência médica dental | 004010X097A1 <br>005010X224A1 <br>005010X224A2 |
| 837_P | Declaração de assistência médica profissional | 004010X098A1 <br>005010X222 <br>005010X222A1 |
|||

Também é preciso desativar a validação EDI ao usar esses números de versão do documento, pois eles resultam em um erro que indica que o comprimento do caractere é inválido.

Para especificar esses números de versão de documento e tipos de mensagens, siga estas etapas:

1. No esquema HIPAA, substitua o tipo de mensagem atual pelo tipo de mensagem variante do número da versão do documento que você deseja usar.

   Por exemplo, suponha que você queira usar o número de versão do documento `005010X222A1` com o tipo de mensagem `837`. No seu esquema, substitua cada valor `"X12_00501_837"` pelo valor `"X12_00501_837_P"`.

   Para atualizar o esquema, siga estas etapas:

   1. No portal do Azure, vá para sua conta de integração. Localize e faça download do seu esquema. Substitua o tipo de mensagem e renomeie o arquivo de esquema e faça upload do esquema revisado para sua conta de integração. Para saber mais, confira [Editar esquemas](../logic-apps/logic-apps-enterprise-integration-schemas.md#edit-schemas).

   1. Nas configurações de mensagem do seu contrato, selecione o esquema revisado.

1. No objeto `schemaReferences` do seu contrato, adicione outra entrada que especifique o tipo de mensagem variante que corresponde ao número da versão do documento.

   Por exemplo, suponha que você queira usar o número de versão do documento `005010X222A1` com o tipo de mensagem `837`. Seu contrato tem uma seção `schemaReferences` com essas propriedades e valores:

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      }
   ]
   ```

   Nesta seção `schemaReferences`, adicione outra entrada que tenha estes valores:

   * `"messageId": "837_P"`
   * `"schemaVersion": "00501"`
   * `"schemaName": "X12_00501_837_P"`

   Ao concluir, sua seção `schemaReferences` ficará assim:

   ```json
   "schemaReferences": [
      {
         "messageId": "837",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837"
      },
      {
         "messageId": "837_P",
         "schemaVersion": "00501",
         "schemaName": "X12_00501_837_P"
      }
   ]
   ```

1. Nas configurações de mensagem do seu contrato, desative a validação EDI desmarcando a caixa de seleção **Validação EDI** para cada tipo de mensagem ou para todos os tipos de mensagem, se você estiver usando os valores **Padrão**.

   ![Desabilitar a validação para todos os tipos de mensagem ou cada tipo de mensagem](./media/logic-apps-enterprise-integration-x12/x12-disable-validation.png) 

## <a name="connector-reference"></a>Referência de conector

Para detalhes técnicos adicionais sobre esse conector, como ações e limites, conforme descrito no arquivo Swagger do conector, confira a [página de referência do conector](/connectors/x12/).

> [!NOTE]
> Para aplicativos lógicos em um [ambiente de serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), a versão rotulada do ISE deste conector usa os [limites de mensagem B2B para o ISE](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores para Aplicativos Lógicos](../connectors/apis-list.md)
