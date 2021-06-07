---
title: Soluções para erros comuns e problemas em cenários B2B
description: Encontre soluções para erros e problemas comuns ao solucionar problemas de cenários B2B em aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/02/2017
ms.openlocfilehash: 6400cfe7e524dcc16e08c2bba7dfba4a62d00b2e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86232552"
---
# <a name="b2b-errors-and-solutions-for-azure-logic-apps"></a>Erros e soluções B2B para os Aplicativos Lógicos do Azure

Este artigo ajuda você a solucionar problemas de erros que podem ocorrer em cenários de Aplicativos Lógicos B2B e sugere ações adequadas para corrigir esses erros.

## <a name="agreement-resolution"></a>Resolução do contrato

### <a name="no-agreement-found"></a>Nenhum contrato encontrado 

**Descrição do erro**: nenhum contrato encontrado com parâmetros de resolução do contrato.

**Ação do usuário**: o contrato deve ser adicionado à conta de integração com identidades de negócios acordadas. As identidades comerciais devem corresponder às IDs de mensagens de entrada.

### <a name="no-agreement-found-with-identities"></a>Não foi encontrado nenhum contrato com identidades

**Descrição do erro**: nenhum contrato encontrado com identidades: ' AS2Identity ':: ' Partner1 ' and'AS2Identity ':: ' Partner3 '

**Ação do usuário**: AS2-From ou AS2-To inválidos configurados para o contrato. Corrigir os cabeçalhos "AS2-From" ou "AS2-To" da mensagem AS2 ou o contrato para corresponder as IDs do AS2 nos cabeçalhos de mensagem AS2 com as configurações de contrato.

## <a name="as2"></a>AS2

### <a name="missing-as2-message-headers"></a>Cabeçalhos de mensagem AS2 ausentes  

**Descrição do erro**: cabeçalhos AS2 inválidos. Um dos cabeçalhos "AS2-To" ou "AS2-From" está vazio.

**Ação do usuário**: foi recebida uma mensagem AS2 que não continha o AS2-From ou AS2-To ou ambos os cabeçalhos. Verifique os cabeçalhos AS2-From e AS2-To da mensagem AS2 e corrija-os com base na configuração do contrato.

### <a name="missing-as2-message-body-and-headers"></a>Cabeçalhos e corpo da mensagem AS2 ausentes    

**Descrição do erro**: o conteúdo da solicitação é nulo ou está vazio.

**Ação do usuário**: foi recebida uma mensagem AS2 que não continha o corpo da mensagem.

### <a name="as2-message-decryption-failure"></a>Falha na descriptografia mensagem AS2

**Descrição do erro**: [processado/erro: descriptografia-falha]

**Ação do usuário**: Adicionar @base64ToBinary ao AS2Message antes de enviar para o parceiro.

Por exemplo:

```json
"HTTP": {
   "inputs": {
   "body": "@base64ToBinary(body('Encode_to_AS2_message')?['AS2Message']?['Content'])",
   "headers": "@body('Encode_to_AS2_message')?['AS2Message']?['OutboundHeaders']",
   "method": "POST",
   "uri": "xxxxx.xxx"
},
``` 

### <a name="mdn-decryption-failure"></a>Falha na descriptografia do MDN

**Descrição do erro**: [processado/erro: descriptografia-falha]

**Ação do usuário**: Adicionar @base64ToBinary ao MDN antes de enviar para o parceiro.

Por exemplo:

```json
"Response": {
   "inputs": {
   "body": "@base64ToBinary(body('Decode_AS2_message')?['OutgoingMDN']?['Content'])",
   "headers": "@body('Decode_AS2_message')?['OutgoingMDN']?['OutboundHeaders']",
   "statusCode": 200
},               
``` 

### <a name="missing-signing-certificate"></a>Certificado de assinatura ausente

**Descrição do erro**: o certificado de autenticação não foi configurado para a parte AS2. AS2-From: partner1 AS2-To: partner2

**Ação do usuário**: defina as configurações do contrato AS2 com o certificado correto para a assinatura.

## <a name="x12-and-edifact"></a>X12 e EDIFACT

### <a name="leading-or-trailing-space-found"></a>Espaço à esquerda ou à direita encontrado    

**Descrição do erro**: erro encontrado durante a análise. O conjunto de transações EDIFACT com a ID ' 123456 ' contido no intercâmbio (sem grupo) com a ID ' 987654 ', com a ID de remetente ' Partner1 ', ID do destinatário ' Partner2 ' está sendo suspenso com os seguintes erros: "separador à direita encontrado"

**Ação do usuário**: as configurações de contrato a serem configuradas para permitir o espaço à esquerda e à direita. Edite as configurações do contrato para permitir o espaço à esquerda e à direita.

![permitir espaço](./media/logic-apps-enterprise-integration-b2b-list-errors-solutions/leadingandtrailing.png)

### <a name="duplicate-check-has-enabled-in-the-agreement"></a>A verificação dupla foi habilitada no contrato

**Descrição do erro**: número de controle duplicado

**Ação do usuário**: esse erro indica que a mensagem recebida tem números de controle duplicados. Corrija o número de controle e reenvie a mensagem.

### <a name="missing-schema-in-the-agreement"></a>Esquema ausente no contrato

**Descrição do erro**: erro encontrado durante a análise. O conjunto de transações X12 com a ID ' 564220001 ' contido no grupo funcional com a ID ' 56422 ', no intercâmbio com a ID ' 000056422 ', com a ID de remetente ' 12345678 ', ID do destinatário ' 87654321 ' está sendo suspenso com os seguintes erros: "a mensagem tem um tipo de documento desconhecido e não foi resolvida para nenhum dos esquemas existentes configurados no contrato"

**Ação do usuário**: Configure o esquema nas configurações do contrato.

### <a name="incorrect-schema-in-the-agreement"></a>Esquema incorreto no contrato

**Descrição do erro**: a mensagem tem um tipo de documento desconhecido e não foi resolvida para nenhum dos esquemas existentes configurados no contrato.

**Ação do usuário**: Configure o esquema correto nas configurações do contrato.

## <a name="flat-file"></a>Arquivo simples

### <a name="input-message-with-no-body"></a>Mensagem de entrada sem corpo

**Descrição do erro**: invalidatemplate. Não é possível processar as expressões da linguagem do modelo nas entradas da ação “Flat_File_Decoding” na linha “1” e coluna “1902”: “A propriedade obrigatória “content” espera um valor, mas recebeu nulo. Caminho “”.”.

**Ação do usuário**: esse erro indica que a mensagem de entrada não contém um corpo.
