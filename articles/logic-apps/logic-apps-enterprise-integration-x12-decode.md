---
title: Decodificar mensagens X12 - Aplicativos Lógicos do Azure | Microsoft Docs
description: Validar EDI e gerar confirmações com o decodificador de mensagem X12 nos Aplicativos Lógicos do Azure com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jonfan, divswa, LADocs
ms.topic: article
ms.assetid: 4fd48d2d-2008-4080-b6a1-8ae183b48131
ms.date: 01/27/2017
ms.openlocfilehash: 4a19462f4f849602fd14fe1204f1c7e3c01e6ec4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106499"
---
# <a name="decode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Decodificar mensagens X12 nos Aplicativos Lógicos do Azure com o Enterprise Integration Pack

Com o conector de mensagem Decodificar X12, você pode validar o envelope no acordo entre parceiros comerciais, validar o EDI e propriedades específicas ao parceiro, dividir intercâmbios em conjuntos de transações ou preservar intercâmbios inteiros e gerar confirmações para transações processadas. Para usar esse conector, você deve adicionar o conector para um gatilho existente em seu aplicativo lógico.

## <a name="before-you-start"></a>Antes de começar

Veja os itens necessários:

* Uma conta do Azure; você pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma [conta de integração](logic-apps-enterprise-integration-create-integration-account.md) que já esteja definida e associada à sua assinatura do Azure. Você precisa ter uma conta de integração para usar o conector de mensagem X12 de decodificação.
* Pelo menos dois [parceiros](logic-apps-enterprise-integration-partners.md) que já estão definidos em sua conta de integração
* Pelo menos dois [contratos X12](logic-apps-enterprise-integration-x12.md) que já estão definidos em sua conta de integração

## <a name="decode-x12-messages"></a>Decodificar mensagens X12

1. [Criar um aplicativo lógico](quickstart-create-first-logic-app-workflow.md).

2. O conector de mensagem X12 de decodificação não possui gatilhos, você deve adicionar um gatilho para iniciar seu aplicativo lógico, como um gatilho de Solicitação. No Designer de Aplicativo Lógico, adicione um gatilho e uma ação ao aplicativo lógico.

3.  Na caixa de pesquisa, digite "x12" como filtro. Selecione **X12 - Decodificar mensagem X12**.
   
    ![Procure por “x12”](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. Se você não criou conexões à sua conta de integração previamente, terá que criá-las agora. Nomeie sua conexão e selecione a conta de integração que você deseja conectar. 

    ![Fornece detalhes da conexão com a conta de integração](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    As propriedades com um asterisco são obrigatórias.

    | Propriedade | Detalhes |
    | --- | --- |
    | Nome da Conexão * |Digite um nome para a conexão. |
    | Uma conta de integração * |Insira um nome para sua conta de integração. Verifique se sua conta de integração e o aplicativo lógico estão no mesmo local do Azure. |

5.  Quando terminar, os detalhes de conexão devem ser semelhantes a este exemplo. Para concluir a criação da sua conexão, escolha **Criar**.
   
    ![detalhes da conexão com a conta de integração](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. Após a conexão ter sido criada, conforme mostrado neste exemplo, selecione a mensagem de arquivo simples X12 para decodificação.

    ![conexão com a conta de integração criada](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    Por exemplo: 

    ![Selecione a mensagem de arquivo simples X12 para decodificação](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

   > [!NOTE]
   > O conteúdo da mensagem real ou payload para a matriz de mensagem, bom ou mau, é codificado com base64. Portanto, você deve especificar uma expressão que processa este conteúdo.
   > Aqui está um exemplo que processa o conteúdo XML que você pode inserir no modo de exibição de código ou usando o construtor de expressões no designer.
   > ``` json
   > "content": "@xml(base64ToBinary(item()?['Payload']))"
   > ```
   > ![Exemplo de conteúdo](media/logic-apps-enterprise-integration-x12-decode/content-example.png)
   >


## <a name="x12-decode-details"></a>Detalhes da Decodificação X12

O conector de Decodificação X12 executa as seguintes tarefas:

* Valida o envelope no contrato de parceiro comercial
* Valida o EDI e as propriedades específicas de parceiro
  * Validação estrutural de EDI e validação de esquema estendido
  * Validação da estrutura do envelope de intercâmbio.
  * Validação de esquema do envelope em relação ao esquema de controle.
  * Validação de esquema dos elementos de dados do conjunto de transações em relação ao esquema de mensagem.
  * Validação de EDI executadas em elementos de dados do conjunto de transação 
* Verifica se os números de controle de conjunto de intercâmbio, grupo e transações não são duplicatas
  * Verifica o número de controle de intercâmbio em relação aos intercâmbios recebidos anteriormente.
  * Verifica o número de controle de grupo em relação a outros números de controle no intercâmbio.
  * Verifica o número de controle do conjunto de transações em relação a outros números de controle de conjunto de transações nesse grupo.
* Divide o intercâmbio em conjuntos de transações ou preserva todo o intercâmbio:
  * Dividir intercâmbio como conjuntos de transação – suspender conjuntos transação com erro: Divide o intercâmbio em transação conjuntos e analisa cada conjunto de transações. 
  A ação Decodificar X12 gera apenas os conjuntos de transações que falharam na validação em `badMessages` e gera os conjuntos de transações restantes em `goodMessages`.
  * Dividir intercâmbio como conjuntos de transação - suspender intercâmbio no erro: Divide o intercâmbio em transação conjuntos e analisa cada conjunto de transações. 
  Se um ou mais conjuntos de transações no intercâmbio falharem na validação, a ação Decodificar X12 gerará todos os conjuntos de transações no intercâmbio em `badMessages`.
  * Preservar intercâmbio - suspender conjuntos transação com erro: Preserve o intercâmbio e processe todo o intercâmbio em lote. 
  A ação Decodificar X12 gera apenas os conjuntos de transações que falharam na validação em `badMessages` e gera os conjuntos de transações restantes em `goodMessages`.
  * Preservar intercâmbio - suspender intercâmbio no erro: Preserve o intercâmbio e processe todo o intercâmbio em lote. 
  Se um ou mais conjuntos de transações no intercâmbio falharem na validação, a ação Decodificar X12 gerará todos os conjuntos de transações no intercâmbio em `badMessages`. 
* Gera uma confirmação técnica e/ou funcional (se configurado).
  * Uma confirmação técnica é gerada como resultado da validação de cabeçalho. A confirmação técnica relata o status do processamento de um cabeçalho e rodapé de intercâmbio pelo destinatário no endereço.
  * Uma confirmação técnica é gerada como resultado da validação do corpo. A confirmação funcional relata cada erro encontrado durante o processamento de documentos recebidos

## <a name="view-the-swagger"></a>Exibir o swagger
Consulte os [detalhes do Swagger](/connectors/x12/). 

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack") 

