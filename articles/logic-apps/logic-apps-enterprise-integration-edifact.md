---
title: Mensagens EDIFACT para integração de empresas B2B – Aplicativos Lógicos do Azure | Microsoft Docs
description: Trocar mensagens EDIFACT em formato EDI para Enterprise Integration B2B nos Aplicativos Lógicos do Azure com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 2257d2c8-1929-4390-b22c-f96ca8b291bc
ms.date: 07/26/2016
ms.openlocfilehash: bbcdad7c5496cd08994a613b07e1bc7c611e4572
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60684379"
---
# <a name="exchange-edifact-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Trocar mensagens EDIFACT para Enterprise Integration B2B nos Aplicativos Lógicos do Azure com o Enterprise Integration Pack

Antes de trocar mensagens EDIFACT para aplicativos lógicos do Azure, você deve criar um contrato EDIFACT e armazenar esse contrato em sua conta de integração. Aqui estão as etapas sobre como criar um contrato EDIFACT.

> [!NOTE]
> Esta página abrange os recursos de EDIFACT dos aplicativos lógicos do Azure. Para obter mais informações, consulte [X12](logic-apps-enterprise-integration-x12.md).

## <a name="before-you-start"></a>Antes de começar

Veja os itens necessários:

* Uma [conta de integração](logic-apps-enterprise-integration-create-integration-account.md) que já esteja definida e associada à sua assinatura do Azure  
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já estão definidos em sua conta de integração

> [!NOTE]
> Ao criar um contrato, o conteúdo das mensagens trocadas com o parceiro devem corresponder ao tipo de contrato.

Depois de [criar uma conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) e [adicionar os parceiros](logic-apps-enterprise-integration-partners.md), você poderá criar um contrato EDIFACT executando as seguintes etapas:

## <a name="create-an-edifact-agreement"></a>Criar um contrato EDIFACT 

1. Entre no [portal do Azure](https://portal.azure.com "portal do Azure"). 

2. No menu principal do Azure, selecione **Todos os serviços**. Insira “integração” na caixa de pesquisa e, em seguida, selecione **Contas de Integração**.

   ![Localizar sua conta de integração](./media/logic-apps-enterprise-integration-edifact/edifact-0.png)

   > [!TIP]
   > Se **Todos os serviços** não aparecer, talvez seja necessário expandir o menu primeiro. Na parte superior do menu recolhido, selecione **Mostrar rótulos de texto**.

3. Em **Contas de Integração**, selecione a conta de integração onde você deseja criar o contrato.

   ![Selecione a conta de integração onde deseja criar o contrato](./media/logic-apps-enterprise-integration-edifact/edifact-1-4.png)

4. Escolha **Contratos**. Se não tiver um bloco de Contratos, primeiro adicione o bloco.   

   ![Escolha o bloco de "Contratos"](./media/logic-apps-enterprise-integration-edifact/edifact-1-5.png)

5. Na página Contratos, escolha **Adicionar**.

   ![Escolha "Adicionar"](./media/logic-apps-enterprise-integration-edifact/edifact-agreement-2.png)

6. Em **Adicionar**, insira um **Nome** para o seu contrato. No **Tipo de contrato**, selecione **EDIFACT**. Selecione o **Parceiro Host**, a **Identidade do Host**, o **Parceiro Convidado** e a **Identidade do Convidado** para o contrato.

   ![Fornecer detalhes de contrato](./media/logic-apps-enterprise-integration-edifact/edifact-1.png)

   | Propriedade | Descrição |
   | --- | --- |
   | NOME |Nome do contrato |
   | Tipo de contrato | Deve ser EDIFACT |
   | Parceiro de Host |Um contrato precisa dos parceiros host e convidado. O parceiro host representa a organização que está configurando o contrato. |
   | Identidade do Host |Um identificador para o parceiro host |
   | Parceiro Convidado |Um contrato precisa dos parceiros host e convidado. O parceiro convidado representa a organização que está fazendo negócios com o parceiro host. |
   | Identidade do Convidado |Um identificador para o parceiro convidado |
   | Configurações de Recebimento |Essas propriedades se aplicam a todas as mensagens recebidas por um contrato. |
   | Configurações de Envio |Essas propriedades se aplicam a todas as mensagens enviadas por um contrato. |
   ||| 

## <a name="configure-how-your-agreement-handles-received-messages"></a>Configurar como seu contrato lida com mensagens recebidas

Depois de configurar as propriedades do contrato, você pode configurar como este contrato identifica e manipula mensagens de entrada recebidas do seu parceiro por meio deste contrato.

1. Em **Adicionar**, selecione **Configurações de Recebimento**.
Configure essas propriedades com base em seu contrato com o parceiro que troca mensagens com você. Para obter as descrições da propriedade, confira as tabelas nesta seção.

   **Configurações de recebimento** é organizado nas seguintes seções: Identificadores, confirmação, esquemas, números de controle, validação e configurações internas.

   ![Configure "Configurações de recebimento"](./media/logic-apps-enterprise-integration-edifact/edifact-2.png)  

2. Após terminar, salve suas configurações, escolhendo **OK**.

Agora o contrato está pronto para lidar com mensagens de entrada de acordo com as configurações selecionadas.

### <a name="identifiers"></a>Identificadores

| Propriedade | DESCRIÇÃO |
| --- | --- |
| UNB 6.1 (Senha de Referência do Destinatário) |Insira um valor alfanumérico entre 1 e 14 caracteres. |
| UNB 6.2 (Qualificador de Referência do Destinatário) |Insira um valor alfanumérico com no mínimo um caractere e no máximo dois caracteres. |

### <a name="acknowledgments"></a>Agradecimentos

| Propriedade | DESCRIÇÃO |
| --- | --- |
| Recebimento de Mensagem (CONTRL) |Marque essa caixa de seleção para retornar uma confirmação técnica (CONTRL) ao remetente do intercâmbio. A confirmação é enviada ao remetente do intercâmbio com base nas Configurações de Envio do contrato. |
| Confirmação (CONTRL) |Marque essa caixa de seleção para retornar uma confirmação funcional (CONTRL) ao emissor de intercâmbio. A confirmação é enviada ao emissor de intercâmbio com base nas Configurações de Envio do contrato. |

### <a name="schemas"></a>Esquemas

| Propriedade | DESCRIÇÃO |
| --- | --- |
| UNH2.1 (TIPO) |Selecione um tipo de conjunto de transação. |
| UNH2.2 (VERSÃO) |Insira o número de versão da mensagem. (No mínimo um caractere; no máximo três caracteres). |
| UNH2.3 (LIBERAÇÃO) |Insira o número de liberação da mensagem. (No mínimo um caractere; no máximo três caracteres). |
| UNH2.5 (CÓDIGO ATRIBUÍDO ASSOCIADO) |Insira o código atribuído. (No máximo seis caracteres. Deve ser alfanuméricos). |
| UNG 2.1 (ID DE REMETENTE DO APLICATIVO) |Insira um valor alfanumérico com no mínimo um caractere e no máximo 35 caracteres. |
| UNG 2.2 (QUALIFICADOR DE CÓDIGO DO APLICATIVO REMETENTE) |Insira um valor alfanumérico, com no máximo quatro caracteres. |
| Esquema |Selecione o esquema carregado anteriormente que você deseja usar de sua Conta de Integração associada. |

### <a name="control-numbers"></a>Números de Controle
| Propriedade | DESCRIÇÃO |
| --- | --- |
| Recusar duplicatas de Números de Controle de Intercâmbio |Para bloquear intercâmbios de duplicatas, selecione essa propriedade. Se essa opção for selecionada, a Ação de Decodificação do EDIFACT verificará se o número de controle de intercâmbio (UNB5) do intercâmbio recebido não corresponde ao número de controle de intercâmbio processado anteriormente. Se houver uma correspondência, o intercâmbio não será processado. |
| Verificar UNB5 duplicado a cada (dias) |Se optou por não permitir números de controle de intercâmbio de duplicatas, você pode especificar o número de dias quando executar a verificação, fornecendo o valor apropriado para esta configuração. |
| Recusar duplicatas de Números de controle de grupo |Marque essa propriedade para bloquear intercâmbios com números de controle de grupo duplicados (UNG5). |
| Recusar duplicatas de Números de controle de Conjuntos de transações |Marque essa propriedade para bloquear intercâmbios com números de controle de conjunto de transações duplicados (UNH1). |
| Número de controle da confirmação EDIFACT |Para designar os números de referência do conjunto de transações a serem usados em uma confirmação, insira um valor para o prefixo, um intervalo de números de referência e um sufixo. |

### <a name="validations"></a>Validações

Quando você conclui cada linha de validação, outra é adicionada automaticamente. Se você não especificar regras, a validação usa a linha "Padrão".

| Propriedade | DESCRIÇÃO |
| --- | --- |
| Tipo de Mensagem |Seleciona o tipo de mensagem EDI. |
| Validação de EDI |Executa a validação de EDI nos tipos de dados conforme a definição das propriedades de EDI do esquema, restrições de comprimento, elementos de dados vazios e separadores à direita. |
| Validação Estendida |Se o tipo de dados não for EDI, a validação será realizada nos requisitos de elementos de dados e na permissão de repetições, enumerações e validação de tamanho de elementos de dados (mín./máx.). |
| Permitir Zeros à Esquerda/Direita |Retém zeros à esquerda ou à direita adicionais e caracteres de espaço. Não remova esses caracteres. |
| Cortar Zeros à Esquerda/Direita |Remove zeros à esquerda ou à direita e caracteres de espaço. |
| Política de Separador à Direita |Gera separadores à direita. <p>Selecione **Não permitido** para proibir delimitadores e separadores à direita no intercâmbio recebido. Se o intercâmbio tiver delimitadores e separadores à direita, o intercâmbio é declarado não válido. <p>Escolha **Opcional** para aceitar intercâmbios com ou sem delimitadores e separadores à direita. <p>Escolha **Obrigatório** se o intercâmbio recebido precisar conter delimitadores e separadores à direita. |

### <a name="internal-settings"></a>Configurações Internas

| Propriedade | DESCRIÇÃO |
| --- | --- |
| Criar marcas XML vazias se forem permitidos separadores à direita |Marque essa caixa de seleção para que o remetente do intercâmbio inclua marcas XML vazias para separadores à direita. |
| Dividir Intercâmbio como conjuntos de transação – suspender conjuntos de transação com erro|Analisa cada conjunto de transações em um intercâmbio dentro de um documento XML separado ao aplicar o envelope apropriado ao conjunto de transações. Suspenda somente os conjuntos de transações com falha na validação. |
| Dividir Intercâmbio como conjuntos de transação – suspender intercâmbio com erro|Analisa cada conjunto de transações em um intercâmbio dentro de um documento XML separado ao aplicar o envelope apropriado. Suspende o intercâmbio inteiro se um ou mais conjuntos de transações no intercâmbio falharem na validação. | 
| Preservar Intercâmbio – suspender conjuntos transação com erro |Mantém o intercâmbio intacto, cria um documento XML para o intercâmbio em lote inteiro. Suspenda somente os conjuntos de transações com falha na validação, enquanto continua a processar todos os outros conjuntos de transações. |
| Preservar Intercâmbio – suspender intercâmbio com erro |Mantém o intercâmbio intacto, cria um documento XML para o intercâmbio em lote inteiro. Suspende o intercâmbio inteiro se um ou mais conjuntos de transações no intercâmbio falharem na validação. |

## <a name="configure-how-your-agreement-sends-messages"></a>Configurar como seu contrato envia mensagens

Você pode configurar como este contrato identifica e trata mensagens de saída enviadas a seus parceiros por meio deste contrato.

1.  Em **Adicionar**, selecione **Configurações de Envio**.
Configure essas propriedades com base no seu contrato com seu parceiro que troca mensagens com você. Para obter as descrições da propriedade, confira as tabelas nesta seção.

    **Configurações de envio** é organizado nas seguintes seções: Identificadores, confirmação, esquemas, Envelopes, conjuntos de caracteres e separadores, números de controle e validações.

    ![Configurar "Configurações de Envio"](./media/logic-apps-enterprise-integration-edifact/edifact-3.png)    

2. Após terminar, salve suas configurações, escolhendo **OK**.

Agora o contrato está pronto para lidar com mensagens de saída de acordo com as configurações selecionadas.

### <a name="identifiers"></a>Identificadores

| Propriedade | DESCRIÇÃO |
| --- | --- |
| UNB1.2 (Versão de sintaxe) |Selecione um valor entre **1** e **4**. |
| UNB 2.3 (Endereço de Roteamento do Remetente Inverso) |Insira um valor alfanumérico com no mínimo um caractere e no máximo 14 caracteres. |
| UNB3.3 (Endereço de Roteamento do Destinatário Inverso) |Insira um valor alfanumérico com no mínimo um caractere e no máximo 14 caracteres. |
| UNB 6.1 (Senha de Referência do Destinatário) |Insira um valor alfanumérico com no mínimo um e no máximo 14 caracteres. |
| UNB 6.2 (Qualificador de Referência do Destinatário) |Insira um valor alfanumérico com no mínimo um caractere e no máximo dois caracteres. |
| UNB7 (ID de Referência do Aplicativo) |Insira um valor alfanumérico com no mínimo um caractere e no máximo 14 caracteres |

### <a name="acknowledgment"></a>Confirmação
| Propriedade | DESCRIÇÃO |
| --- | --- |
| Recebimento de Mensagem (CONTRL) |Marque esta caixa de seleção se o parceiro hospedado espera receber uma confirmação técnica (CONTRL). Essa configuração especifica que o parceiro hospedado, que está enviando a mensagem, solicitou uma confirmação do parceiro convidado. |
| Confirmação (CONTRL) |Marque esta caixa de seleção se o parceiro hospedado espera receber uma confirmação funcional (CONTRL). Essa configuração especifica que o parceiro hospedado, que está enviando a mensagem, solicitou uma confirmação do parceiro convidado. |
| Gerar loop SG1/SG4 para conjuntos de transação aceitos |Se você escolher solicitar uma confirmação funcional, marque essa caixa de seleção para forçar a geração de loops SG1/SG4 em confirmações funcionais CONTRL para conjuntos de transação aceitos. |

### <a name="schemas"></a>Esquemas
| Propriedade | DESCRIÇÃO |
| --- | --- |
| UNH2.1 (TIPO) |Selecione um tipo de conjunto de transação. |
| UNH2.2 (VERSÃO) |Insira o número de versão da mensagem. |
| UNH2.3 (LIBERAÇÃO) |Insira o número de liberação da mensagem. |
| ESQUEMA |Selecione o esquema a ser usado. Esquema estão localizados na sua conta de integração. Para acessar seus esquemas, vincule sua conta de integração ao seu aplicativo lógico. |

### <a name="envelopes"></a>Envelopes
| Propriedade | DESCRIÇÃO |
| --- | --- |
| UNB8 (Código de Prioridade de Processamento) |Insira um valor alfabético que não tenha mais de um caractere de comprimento. |
| UNB10 (Contrato de Comunicação) |Insira um valor alfanumérico com no mínimo um caractere e no máximo 40 caracteres. |
| UNB11 (Indicador de Teste) |Marque essa caixa de seleção para indicar que o intercâmbio gerado são dados de teste |
| Aplicar o Segmento UNA (Aviso de Cadeia de Caracteres de Serviço) |Marque essa caixa de seleção para gerar um segmento UNA para o intercâmbio a ser enviado. |
| Aplicar os segmentos UNG (Cabeçalho de Grupo de Função) |Marque essa caixa de seleção para criar segmentos de agrupamento no cabeçalho de grupo funcional nas mensagens enviadas ao parceiro convidado. Os seguintes valores são usados para criar os segmentos UNG: <p>Para **UNG1**, insira um valor alfanumérico com no mínimo um e no máximo seis caracteres. <p>Para **UNG2.1**, insira um valor alfanumérico com no mínimo um e no máximo 35 caracteres. <p>Para **UNG2.2**, insira um valor alfanumérico, com no máximo quatro caracteres. <p>Para **UNG3.1**, insira um valor alfanumérico com no mínimo um e no máximo 35 caracteres. <p>Para **UNG3.2**, insira um valor alfanumérico, com no máximo quatro caracteres. <p>Para **UNG6**, insira um valor alfanumérico com no mínimo um e no máximo três caracteres. <p>Para **UNG7.1**, insira um valor alfanumérico com no mínimo um e no máximo três caracteres. <p>Para **UNG7.2**, insira um valor alfanumérico com no mínimo um e no máximo três caracteres. <p>Para **UNG7.3**, insira um valor alfanumérico com no mínimo um e no máximo seis caracteres. <p>Para **UNG8**, insira um valor alfanumérico com no mínimo um e no máximo 14 caracteres. |

### <a name="character-sets-and-separators"></a>Conjuntos de Caracteres e Separadores

Além do conjunto de caracteres, você pode inserir um conjunto diferente de delimitadores a serem usados para cada tipo de mensagem. Se um conjunto de caracteres não for especificado para um determinado esquema de mensagem, o conjunto de caracteres padrão será usado.

| Propriedade | DESCRIÇÃO |
| --- | --- |
| UNB1.1 (Identificador do Sistema) |Selecione o conjunto de caracteres EDIFACT a ser aplicado no intercâmbio de saída. |
| Esquema |Selecione um esquema na lista suspensa. Depois de concluir cada linha, uma nova linha é adicionada automaticamente. Para o esquema selecionado, selecione o conjunto de separadores que deseja usar, com base nas descrições do separador abaixo. |
| Tipo de entrada |Selecione um tipo de entrada na lista suspensa. |
| Separador de componente |Insira um único caractere para separar os elementos de dados compostos. |
| Separador de elemento de dados |Insira um único caractere para separar os elementos de dados simples dentro dos elementos de dados compostos. |
| Terminador de segmento |Insira um único caractere para indicar o final de um segmento EDI. |
| Suffix |Selecionar o caractere a ser usado com o identificador de segmento. Se você designar um sufixo, o elemento de dados de terminador de segmento poderá ficar vazio. Se o terminador de segmento ficar vazio, você deverá designar um sufixo. |

### <a name="control-numbers"></a>Números de Controle
| Propriedade | DESCRIÇÃO |
| --- | --- |
| UNB5 (Número de Controle de Intercâmbio) |Insira um prefixo, um intervalo de valores para o número de controle de intercâmbio e um sufixo. Esses valores são usados para gerar um intercâmbio de saída. O prefixo e sufixo são opcionais; o número de controle é obrigatório. O número de controle é incrementado para cada nova mensagem; o prefixo e sufixo permanecem os mesmos. |
| UNG5 (Número de Controle de Grupo) |Insira um prefixo, um intervalo de valores para o número de controle de intercâmbio e um sufixo. Esses valores são usados para gerar o número de controle de grupo. O prefixo e sufixo são opcionais; o número de controle é obrigatório. O número de controle é incrementado para cada nova mensagem até que o valor máximo seja alcançado; o prefixo e sufixo permanecem os mesmos. |
| UNH1 (Número de Referência do Cabeçalho da Mensagem) |Insira um prefixo, um intervalo de valores para o número de controle de intercâmbio e um sufixo. Esses valores são usados para gerar o número de referência de cabeçalho da mensagem. O prefixo e sufixo são opcionais; o número de referência é obrigatório. O número de referência é incrementado para cada nova mensagem; o prefixo e sufixo permanecem os mesmos. |

### <a name="validations"></a>Validações

Quando você conclui cada linha de validação, outra é adicionada automaticamente. Se você não especificar regras, a validação usa a linha "Padrão".

| Propriedade | DESCRIÇÃO |
| --- | --- |
| Tipo de Mensagem |Seleciona o tipo de mensagem EDI. |
| Validação de EDI |Executa a validação de EDI nos tipos de dados conforme a definição das propriedades de EDI do esquema, restrições de comprimento, elementos de dados vazios e separadores à direita. |
| Validação Estendida |Se o tipo de dados não for EDI, a validação será realizada nos requisitos de elementos de dados e na permissão de repetições, enumerações e validação de tamanho de elementos de dados (mín./máx.). |
| Permitir Zeros à Esquerda/Direita |Retém zeros à esquerda ou à direita adicionais e caracteres de espaço. Não remova esses caracteres. |
| Cortar Zeros à Esquerda/Direita |Remove zeros à esquerda ou à direita adicionais. |
| Política de Separador à Direita |Gera separadores à direita. <p>Selecione **Não permitido** para proibir delimitadores e separadores à direita no intercâmbio enviado. Se o intercâmbio tiver delimitadores e separadores à direita, o intercâmbio é declarado não válido. <p>Escolha **Opcional** para enviar intercâmbios com ou sem delimitadores e separadores à direita. <p>Selecione **Obrigatório** se o intercâmbio enviado deve ter delimitadores e separadores à direita. |

## <a name="find-your-created-agreement"></a>Como localizar seu contrato criado

1.  Depois que você terminar de definir todas as suas propriedades de contrato, na página **Adicionar**, escolha **OK** para terminar de criar o contrato e retornar para a conta de integração.

    Agora seu contrato recém-adicionado é exibido na lista **Contratos**.

2.  Você também pode visualizar seus contratos na visão geral de conta de integração. No menu de conta de integração, escolha **Visão geral** e selecione o bloco **Contratos**. 

    ![Escolha o bloco de "Contratos"](./media/logic-apps-enterprise-integration-edifact/edifact-4.png)   

## <a name="view-swagger-file"></a>Exibir o arquivo do Swagger
Para exibir os detalhes de Swagger para o conector do EDIFACT, consulte [EDIFACT](/connectors/edifact/).

## <a name="learn-more"></a>Saiba mais
* [Saiba mais sobre o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")  

