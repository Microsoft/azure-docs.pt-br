---
title: Mensagens X12 para integração de empresas B2B – Aplicativos Lógicos do Azure | Microsoft Docs
description: Trocar mensagens X12 em formato EDI para Enterprise Integration B2B nos Aplicativos Lógicos do Azure com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 7422d2d5-b1c7-4a11-8c9b-0d8cfa463164
ms.date: 01/31/2017
ms.openlocfilehash: f06e213dbae31c9d7c4e212d605cc962aba71d2d
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766551"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Trocar mensagens X12 para Enterprise Integration B2B nos Aplicativos Lógicos do Azure com Enterprise Integration Pack

Antes de trocar mensagens X12 para aplicativos lógicos do Azure, você deve criar um contrato X12 e armazenar esse contrato na sua conta de integração. Seguem as etapas sobre como criar um contrato X12.

> [!NOTE]
> Essa página aborda os recursos X12 para os aplicativos lógicos do Azure. Para obter mais informações, confira [EDIFACT](logic-apps-enterprise-integration-edifact.md).

## <a name="before-you-start"></a>Antes de começar

Veja os itens necessários:

* Uma [conta de integração](logic-apps-enterprise-integration-create-integration-account.md) que já esteja definida e associada à sua assinatura do Azure
* Pelo menos dois [parceiros](../logic-apps/logic-apps-enterprise-integration-partners.md) já definidos em sua conta de integração e configurados com o identificador X12 nas **identidades comerciais**    
* Um [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) necessário que você carrega à sua conta de integração

Depois de [criar uma conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), [adicionar parceiros](logic-apps-enterprise-integration-partners.md)e ter um [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) que deseja usar, você pode criar um contrato X12 seguindo as etapas abaixo:

## <a name="create-an-x12-agreement"></a>Criar um contrato X12

1. Entre no [portal do Azure](https://portal.azure.com "portal do Azure"). 

2. No menu principal do Azure, selecione **Todos os serviços**. 
   Na caixa de pesquisa, insira "integração" e selecione **Contas de integração**.  

   ![Localizar sua conta de integração](./media/logic-apps-enterprise-integration-x12/account-1.png)

   > [!TIP]
   > Se **Todos os serviços** não aparecer, talvez seja necessário expandir o menu primeiro. Na parte superior do menu recolhido, selecione **Mostrar menu**.

3. Em **Contas de Integração**, selecione a conta de integração onde você deseja adicionar o contrato.

   ![Selecione a conta de integração onde deseja criar o contrato](./media/logic-apps-enterprise-integration-x12/account-3.png)

4. Selecione **Visão geral** e, em seguida, selecione o bloco de **Contratos**. 
   Se não tiver um bloco de Contratos, primeiro adicione o bloco. 

   ![Escolha o bloco de "Contratos"](./media/logic-apps-enterprise-integration-x12/agreement-1.png)

5. Em **Contratos**, escolha **Adicionar**.

   ![Escolha "Adicionar"](./media/logic-apps-enterprise-integration-x12/agreement-2.png)     

6. Em **Adicionar**, insira um **Nome** para o seu contrato. 
   Quanto ao tipo de contrato, selecione **X12**. 
   Selecione o **Parceiro Host**, a **Identidade do Host**, o **Parceiro Convidado** e a **Identidade do Convidado** para o contrato. 
   Confira a tabela dessa etapa para obter mais informações sobre a propriedade.

    ![Fornecer detalhes de contrato](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

    | Propriedade | Descrição |
    | --- | --- |
    | NOME |Nome do contrato |
    | Tipo de contrato | Deve ser X12 |
    | Parceiro de Host |Um contrato precisa dos parceiros host e convidado. O parceiro host representa a organização que está configurando o contrato. |
    | Identidade do Host |Um identificador para o parceiro host |
    | Parceiro Convidado |Um contrato precisa dos parceiros host e convidado. O parceiro convidado representa a organização que está fazendo negócios com o parceiro host. |
    | Identidade do Convidado |Um identificador para o parceiro convidado |
    | Configurações de Recebimento |Essas propriedades se aplicam a todas as mensagens recebidas por um contrato. |
    | Configurações de Envio |Essas propriedades se aplicam a todas as mensagens enviadas por um contrato. |  

   > [!NOTE]
   > A resolução do contrato X12 depende da correspondência entre o identificador e qualificador de remetente e o identificador e qualificador do receptor definidos no parceiro e na mensagem de entrada. Se mudar os valores do parceiro, não deixe de atualizar o contrato também.

## <a name="configure-how-your-agreement-handles-received-messages"></a>Configurar como seu contrato lida com mensagens recebidas

Depois de configurar as propriedades do contrato, você pode configurar como este contrato identifica e manipula mensagens de entrada recebidas do seu parceiro por meio deste contrato.

1.  Em **Adicionar**, selecione **Configurações de Recebimento**.
Configure essas propriedades com base em seu contrato com o parceiro que troca mensagens com você. Para obter as descrições da propriedade, confira as tabelas nesta seção.

    **Configurações de recebimento** é organizado nas seguintes seções: Identificadores, confirmação, esquemas, Envelopes, números de controle, validações e configurações internas.

2. Após terminar, salve suas configurações, escolhendo **OK**.

Agora o contrato está pronto para lidar com mensagens de entrada de acordo com as configurações selecionadas.

### <a name="identifiers"></a>Identificadores

![Definir propriedades do identificador](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| Propriedade | DESCRIÇÃO |
| --- | --- |
| ISA1 (Qualificador de Autorização) |Selecione o valor do Qualificador de autorização na lista suspensa. |
| ISA2 |Opcional. Insira o valor de Informações de autorização. Se o valor inserido para ISA1 é diferente de 00, insira no mínimo um caractere alfanumérico e no máximo 10. |
| ISA3 (Qualificador de Segurança) |Selecione o valor do Qualificador de segurança na lista suspensa. |
| ISA4 |Opcional. Insira o valor de Informações de segurança. Se o valor inserido para ISA3 é diferente de 00, insira no mínimo um caractere alfanumérico e no máximo 10. |

### <a name="acknowledgment"></a>Confirmação

![Definir propriedades de confirmação](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| Propriedade | DESCRIÇÃO |
| --- | --- |
| TA1 esperado |Retorna uma confirmação técnica ao emissor de intercâmbio |
| FA esperado |Retorna uma confirmação funcional ao emissor de intercâmbio. Então, selecione se deseja as confirmações 997 ou 999, com base na versão de esquema |
| Incluir Loop AK2/IK2 |Habilita a geração de loops AK2 em confirmações funcionais para conjuntos de transação aceitos |

### <a name="schemas"></a>Esquemas

Escolha um esquema para cada tipo de transação (ST1) e o Aplicativo de remetente (GS2). O pipeline de recebimento desmontará a mensagem recebida associando os valores de ST1 e GS2 na mensagem recebida aos valores definidos aqui e o esquema da mensagem recebida com o esquema definido aqui.

![Selecione um esquema](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| Propriedade | DESCRIÇÃO |
| --- | --- |
| Version |Selecione a versão X12 |
| Tipo de Transação (ST01) |Selecione o tipo de transação |
| Aplicativo do Remetente (GS02) |Selecione o aplicativo do remetente |
| Esquema |Seleciona o arquivo de esquema que você deseja usar. Os esquemas são adicionados em sua conta de integração. |

> [!NOTE]
> Configura o [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) necessário, que será carregado para a sua [conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Envelopes

![Especifica o separador em um conjunto de transações: escolha o identificador padrão ou o separador de repetição](./media/logic-apps-enterprise-integration-x12/x12-34.png)

| Propriedade | DESCRIÇÃO |
| --- | --- |
| Uso de ISA11 |Especifica o separador a ser usado em um conjunto de transações: <p>Selecione o **identificador padrão** para usar um ponto (.) em notação decimal, em vez de uma notação decimal no documento de entrada do pipeline de recebimento EDI. <p>Selecione o **separador de repetição** para especificar o separador para as ocorrências repetidas de um elemento de dados simples ou uma estrutura de dados repetidos. Por exemplo, geralmente o circunflexo (^) é usado como o separador de repetição. Para esquemas HIPAA, só é possível usar circunflexo (^). |

### <a name="control-numbers"></a>Números de Controle

![Selecione como manipular duplicatas de número de controle](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| Propriedade | DESCRIÇÃO |
| --- | --- |
| Recusar duplicatas de Números de Controle de Intercâmbio |Bloqueia o intercâmbio de duplicatas. Verifica o número de controle de intercâmbio (ISA13) para o número de controle de intercâmbio recebido. Se uma correspondência for detectada, o pipeline de recebimento não processará o intercâmbio. Você pode especificar o número de dias para executar a verificação. Para isso, forneça um valor para a *Verificação de ISA13 duplicados a cada (dias)*. |
| Recusar duplicatas de Números de controle de grupo |Bloqueia intercâmbios com números de controle de grupo de duplicatas. |
| Recusar duplicatas de Números de controle de Conjuntos de transações |Bloqueia intercâmbios com números de controle de conjunto de transações de duplicatas. |

### <a name="validations"></a>Validações

![Definir propriedades de validação das mensagens recebidas](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

Quando você conclui cada linha de validação, outra é adicionada automaticamente. Se você não especificar regras, a validação usa a linha "Padrão".

| Propriedade | DESCRIÇÃO |
| --- | --- |
| Tipo de Mensagem |Seleciona o tipo de mensagem EDI. |
| Validação de EDI |Executa a validação de EDI nos tipos de dados conforme a definição das propriedades de EDI do esquema, restrições de comprimento, elementos de dados vazios e separadores à direita. |
| Validação Estendida |Se o tipo de dados não for EDI, a validação será realizada nos requisitos de elementos de dados e na permissão de repetições, enumerações e validação de tamanho de elementos de dados (mín./máx.). |
| Permitir Zeros à Esquerda/Direita |Retém zeros à esquerda ou à direita adicionais e caracteres de espaço. Não remova esses caracteres. |
| Cortar Zeros à Esquerda/Direita |Remove zeros à esquerda ou à direita e caracteres de espaço. |
| Política de Separador à Direita |Gera separadores à direita. <p>Selecione **Não permitido** para proibir delimitadores e separadores à direita no intercâmbio recebido. Se o intercâmbio tiver delimitadores e separadores à direita, o intercâmbio é declarado não válido. <p>Escolha **Opcional** para aceitar intercâmbios com ou sem delimitadores e separadores à direita. <p>Selecione **Obrigatório** quando o intercâmbio deve ter delimitadores e separadores à direita. |

### <a name="internal-settings"></a>Configurações Internas

![Selecione Configurações internas](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| Propriedade | DESCRIÇÃO |
| --- | --- |
| Converte o formato decimal implícito "Nn" para o valor numérico de base 10 |Converte um número de EDI especificado no formato "Nn" em um valor numérico de base 10 |
| Criar marcas XML vazias se forem permitidos separadores à direita |Marque essa caixa de seleção para que o remetente do intercâmbio inclua marcas XML vazias para separadores à direita. |
| Dividir Intercâmbio como conjuntos de transação – suspender conjuntos de transação com erro|Analisa cada conjunto de transações em um intercâmbio dentro de um documento XML separado ao aplicar o envelope apropriado ao conjunto de transações. Suspende somente as transações em que a validação falhar. |
| Dividir Intercâmbio como conjuntos de transação – suspender intercâmbio com erro|Analisa cada conjunto de transações em um intercâmbio dentro de um documento XML separado ao aplicar o envelope apropriado. Suspende o intercâmbio inteiro se um ou mais conjuntos de transações no intercâmbio falharem na validação. | 
| Preservar Intercâmbio – suspender conjuntos transação com erro |Mantém o intercâmbio intacto, cria um documento XML para o intercâmbio em lote inteiro. Suspende somente os conjuntos de transações com falha na validação, enquanto continua a processar todos os outros conjuntos de transações. |
| Preservar Intercâmbio – suspender intercâmbio com erro |Mantém o intercâmbio intacto, cria um documento XML para o intercâmbio em lote inteiro. Suspende o intercâmbio inteiro se um ou mais conjuntos de transações no intercâmbio falharem na validação. |

## <a name="configure-how-your-agreement-sends-messages"></a>Configurar como seu contrato envia mensagens

Você pode configurar como este contrato identifica e trata mensagens de saída enviadas a seus parceiros por meio deste contrato.

1.  Em **Adicionar**, selecione **Configurações de Envio**.
Configure essas propriedades com base no seu contrato com seu parceiro que troca mensagens com você. Para obter as descrições da propriedade, confira as tabelas nesta seção.

    **Configurações de envio** é organizado nas seguintes seções: Identificadores, confirmação, esquemas, Envelopes, conjuntos de caracteres e separadores, números de controle e validação.

2. Após terminar, salve suas configurações, escolhendo **OK**.

Agora o contrato está pronto para lidar com mensagens de saída de acordo com as configurações selecionadas.

### <a name="identifiers"></a>Identificadores

![Definir propriedades do identificador](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| Propriedade | DESCRIÇÃO |
| --- | --- |
| Qualificador de Autorização (ISA1) |Selecione o valor do Qualificador de autorização na lista suspensa. |
| ISA2 |Insira o valor de Informações de autorização. Se esse valor é diferente de 00, insira no mínimo um caractere alfanumérico e no máximo 10. |
| Qualificador de segurança (ISA3) |Selecione o valor do Qualificador de segurança na lista suspensa. |
| ISA4 |Insira o valor de Informações de segurança. Se esse valor é diferente de 00, insira no mínimo um valor alfanumérico e no máximo 10 na caixa de texto Valor (ISA4). |

### <a name="acknowledgment"></a>Confirmação

![Definir propriedades de confirmação](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Propriedade | DESCRIÇÃO |
| --- | --- |
| TA1 esperado |Retorna uma confirmação técnica (TA1) ao emissor de intercâmbio. Essa configuração especifica que o parceiro host que está enviando a mensagem solicitou uma confirmação do parceiro convidado no contrato. Essas confirmações são esperadas pelo parceiro host com base nas Configurações de Recebimento do contrato. |
| FA esperado |Retorna uma confirmação funcional (FA) ao emissor de intercâmbio. Selecione se quer as confirmações 997 ou as 999, com base nas versões de esquema com as quais você está trabalhando. Essas confirmações são esperadas pelo parceiro host com base nas Configurações de Recebimento do contrato. |
| Versão de FA |Selecione a versão de FA |

### <a name="schemas"></a>Esquemas

![Selecione o esquema a ser usado](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Propriedade | DESCRIÇÃO |
| --- | --- |
| Version |Selecione a versão X12 |
| Tipo de Transação (ST01) |Selecione o tipo de transação |
| Esquema |Selecione o esquema a ser usado. Esquema estão localizados na sua conta de integração. Se você selecionar o esquema pela primeira vez, ele configurará automaticamente o tipo de transação e a versão  |

> [!NOTE]
> Configura o [esquema](../logic-apps/logic-apps-enterprise-integration-schemas.md) necessário, que será carregado para a sua [conta de integração](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Envelopes

![Especifica o separador em um conjunto de transações: escolha o identificador padrão ou o separador de repetição](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| Propriedade | DESCRIÇÃO |
| --- | --- |
| Uso de ISA11 |Especifica o separador a ser usado em um conjunto de transações: <p>Selecione o **identificador padrão** para usar um ponto (.) em notação decimal, em vez de uma notação decimal no documento de entrada do pipeline de recebimento EDI. <p>Selecione o **separador de repetição** para especificar o separador para as ocorrências repetidas de um elemento de dados simples ou uma estrutura de dados repetidos. Por exemplo, geralmente o circunflexo (^) é usado como o separador de repetição. Para esquemas HIPAA, só é possível usar circunflexo (^). |

### <a name="control-numbers"></a>Números de Controle

![Especifique as propriedades do número de controle](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

| Propriedade | DESCRIÇÃO |
| --- | --- |
| Número de Versão de Controle (ISA12) |Selecione a versão do padrão X12 |
| Indicador de Uso (ISA15) |Selecione o contexto de um intercâmbio.  Os valores são informações, dados de produção ou dados de teste |
| Esquema |Gera os segmentos GS e ST para um intercâmbio de codificação X12 que ele envia para o Pipeline de Envio |
| GS1 |Opcional, selecionar um valor para o código funcional na lista suspensa |
| GS2 |Opcional, remetente do aplicativo |
| GS3 |Opcional, receptor do aplicativo |
| GS4 |Opcional, selecionar CCYYMMDD ou YYMMDD |
| GS5 |Opcional, selecionar HHMM, HHMMSS ou HHMMSSdd |
| GS7 |Opcional, selecionar um valor para a agência responsável na lista suspensa |
| GS8 |Opcional, versão do documento |
| Número de Controle de Intercâmbio (ISA13) |Obrigatório, inserir um intervalo de valores para o número de controle de intercâmbio. Inserir um valor numérico entre 1 e 999999999 |
| Número de Controle de Grupo (GS06) |Obrigatório, inserir um intervalo de números para o número de controle de grupo. Inserir um valor numérico entre 1 e 999999999 |
| Número de Controle de Conjunto de Transações (ST02) |Obrigatório, inserir um intervalo de números para o número de controle de conjunto de transações. Insira um valor numérico entre 1 e 999999999 |
| Prefixo |Opcional, designado para o intervalo de números de controle de conjuntos de transações usados na confirmação. Insira um valor numérico nos dois campos centrais e um valor alfanumérico (se desejado) nos campos de prefixo e sufixo. Os campos centrais são obrigatórios e contêm os valores mínimo e máximo para o número de controle |
| Suffix |Opcional, designado para o intervalo de números de controle de conjuntos de transações usados em uma confirmação. Inserir um valor numérico nos dois campos centrais e um valor alfanumérico (se desejado) nos campos de prefixo e sufixo. Os campos centrais são obrigatórios e contêm os valores mínimo e máximo para o número de controle |

### <a name="character-sets-and-separators"></a>Conjuntos de Caracteres e Separadores

Além do conjunto de caracteres, você pode inserir um conjunto diferente de delimitadores para cada tipo de mensagem. Se você não especificar um conjunto de caracteres para um esquema de mensagem específico, será usado o conjunto de caracteres padrão.

![Especifique os delimitadores para os tipos de mensagem](./media/logic-apps-enterprise-integration-x12/x12-9.png) 

| Propriedade | DESCRIÇÃO |
| --- | --- |
| Conjunto de Caracteres a ser usado |Selecione o conjunto de caracteres X12 para validar as propriedades. As opções são básico, estendido e UTF8. |
| Esquema |Selecione um esquema na lista suspensa. Depois de concluir cada linha, uma nova linha é adicionada automaticamente. Para o esquema selecionado, selecione o conjunto de separadores que deseja usar, com base nas descrições do separador abaixo. |
| Tipo de entrada |Selecione um tipo de entrada na lista suspensa. |
| Separador de componente |Insira um único caractere para separar os elementos de dados compostos. |
| Separador de elemento de dados |Insira um único caractere para separar os elementos de dados simples dentro dos elementos de dados compostos. |
| Caractere de substituição |Insira um caractere de substituição usado para substituir todos os caracteres separadores dos dados de conteúdo ao gerar a mensagem X12 de saída. |
| Terminador de segmento |Insira um único caractere para indicar o final de um segmento EDI. |
| Suffix |Selecionar o caractere a ser usado com o identificador de segmento. Se você designar um sufixo, o elemento de dados de terminador de segmento poderá ficar vazio. Se o terminador de segmento ficar vazio, você deverá designar um sufixo. |

> [!TIP]
> Para fornecer valores de caractere especial, edite o contrato como JSON e forneça o valor ASCII para o caractere especial.

### <a name="validation"></a>Validação

![Definição de propriedades de validação para o envio de mensagens](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

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

    ![Escolha o bloco de "Contratos"](./media/logic-apps-enterprise-integration-x12/x12-1-5.png)   

## <a name="view-the-swagger"></a>Exibir o swagger
Consulte os [detalhes do Swagger](/connectors/x12/). 

## <a name="learn-more"></a>Saiba mais
* [Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")  

