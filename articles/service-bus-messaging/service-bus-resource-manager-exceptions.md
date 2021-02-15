---
title: Exceções do Gerenciador de recursos do barramento de serviço do Azure | Microsoft Docs
description: Lista de exceções do barramento de serviço na superfície por Azure Resource Manager e ações sugeridas.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: a0b0338da0f002c7b667748ffd2bf5a40c91c580
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85336977"
---
# <a name="service-bus-resource-manager-exceptions"></a>Exceções do Gerenciador de recursos do barramento de serviço

Este artigo lista exceções geradas ao interagir com o barramento de serviço do Azure usando modelos Azure Resource Manager ou chamadas diretas.

> [!IMPORTANT]
> Este documento é atualizado com frequência. Verifique novamente se há atualizações.

Abaixo estão as várias exceções/erros que são exibidos por meio do Azure Resource Manager.

## <a name="error-bad-request"></a>Erro: solicitação inadequada

"Solicitação inadequada" implica que a solicitação recebida pelo Gerenciador de recursos falhou na validação.

| Código do erro | Subcódigo de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Solicitação incorreta | 40000 | Subcódigo = 40000. A propriedade *' nome da propriedade '* não pode ser definida ao criar uma fila porque o namespace *' nome do namespace '* está usando a camada ' básica '. Esta operação só tem suporte na camada ' Standard ' ou ' Premium '. | Na camada básica do barramento de serviço do Azure, as propriedades abaixo não podem ser definidas ou atualizadas- <ul> <li> RequiresDuplicateDetection </li> <li> AutoDeleteOnIdle </li> <li>RequiresSession</li> <li>DefaultMessageTimeToLive </li> <li> DuplicateDetectionHistoryTimeWindow </li> <li> EnableExpress </li> <li> ForwardTo </li> <li> Tópicos </li> </ul> | Considere atualizar da camada básica para Standard ou Premium para usar essa funcionalidade. |
| Solicitação incorreta | 40000 | Subcódigo = 40000. O valor da propriedade ' requiresDuplicateDetection ' de uma fila (ou tópico) existente não pode ser alterado. | A detecção de duplicidades deve ser habilitada/desabilitada no momento da criação da entidade. O parâmetro de configuração de detecção de duplicidades não pode ser alterado após a criação. | Para habilitar a detecção de duplicidades em uma fila/tópico criado anteriormente, você pode criar uma nova fila/tópico com detecção de duplicidades e, em seguida, encaminhar da fila original para a nova fila/tópico. |
| Solicitação incorreta | 40000 | Subcódigo = 40000. O valor especificado 16384 é inválido. A propriedade ' MaxSizeInMegabytes ' deve ser um dos seguintes valores: 1024; 2048; 3072; 4096; 5120. | O valor de MaxSizeInMegabytes é inválido. | Verifique se o MaxSizeInMegabytes é um dos seguintes-1024, 2048, 3072, 4096, 5120. |
| Solicitação incorreta | 40000 | Subcódigo = 40000. O particionamento não pode ser alterado para a fila/tópico. | O particionamento não pode ser alterado para a entidade. | Crie uma nova entidade (fila ou tópico) e habilite partições. | 
| Solicitação incorreta | nenhum | O namespace *' nome do namespace '* não existe. | O namespace não existe em sua assinatura do Azure. | Para resolver esse erro, tente o seguinte <ul> <li> Verifique se a assinatura do Azure está correta. </li> <li> Verifique se o namespace existe. </li> <li> Verifique se o nome do namespace está correto (sem erros de ortografia ou cadeias de caracteres nulas). </li> </ul> | 
| Solicitação incorreta | 40400 | Subcódigo = 40400. A entidade de destino de encaminhamento automático não existe. | O destino para a entidade de destino de encaminhamento automático não existe. | A entidade de destino (fila ou tópico) deve existir antes que a origem seja criada. Tente novamente depois de criar a entidade de destino. |
| Solicitação incorreta | 40000 | Subcódigo = 40000. O tempo de bloqueio fornecido excede o máximo permitido de ' 5 ' minutos. | A hora para a qual uma mensagem pode ser bloqueada deve estar entre 1 minuto (mínimo) e 5 minutos (máximo). | Verifique se o tempo de bloqueio fornecido está entre 1 minuto e 5 minutos. |
| Solicitação incorreta | 40000 | Subcódigo = 40000. A propriedade DelayedPersistence e RequiresDuplicateDetection não podem ser habilitadas juntas. | As entidades com detecção de duplicidades habilitadas nelas devem ser persistentes, portanto, a persistência não pode ser atrasada. | Saiba mais sobre [detecção de duplicidades](duplicate-detection.md) |
| Solicitação incorreta | 40000 | Subcódigo = 40000. O valor da propriedade RequiresSession de uma fila existente não pode ser alterado. | O suporte para sessões deve ser habilitado no momento da criação da entidade. Depois de criado, você não pode habilitar/desabilitar sessões em uma entidade existente (fila ou assinatura) | Exclua e recrie uma nova fila (ou assinatura) com a propriedade "RequiresSession" habilitada. |
| Solicitação incorreta | 40000 | Subcódigo = 40000. ' URI_PATH ' contém caractere (s) que não é permitido pelo barramento de serviço. Os segmentos de entidade podem conter apenas letras, números, pontos (.), hifens (-) e sublinhados (_). | Os segmentos de entidade podem conter apenas letras, números, pontos (.), hifens (-) e sublinhados (_). Quaisquer outros caracteres causam a falha da solicitação. | Verifique se não há caracteres inválidos no caminho do URI. |


## <a name="error-code-429"></a>Código de erro: 429

Assim como no HTTP, "código de erro 429" indica "muitas solicitações". Isso implica que o recurso específico (namespace) está sendo limitado devido a muitas solicitações (ou devido a operações conflitantes) nesse recurso.

| Código do erro | Subcódigo de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 5.0004 | Subcódigo = 50004. A solicitação foi encerrada porque o namespace *do namespace* está sendo limitado. | Essa condição de erro é pressionada quando o número de solicitações de entrada excede a limitação do recurso. | Aguarde alguns segundos e tente novamente. <br/> <br/> Saiba mais sobre as [cotas](service-bus-quotas.md) e os [limites de solicitação de Azure Resource Manager](../azure-resource-manager/management/request-limits-and-throttling.md)|
| 429 | 40901 | Subcódigo = 40901. Outra operação conflitante está em andamento. | Outra operação conflitante está em andamento no mesmo recurso/entidade | Aguarde a conclusão da operação em andamento atual antes de tentar novamente. |
| 429 | 40900 | Subcódigo = 40900. Conflito. Você está solicitando uma operação não permitida no estado atual do recurso. | Essa condição pode ser atingida quando várias solicitações são feitas para executar as operações na mesma entidade (fila, tópico, assinatura ou regra) ao mesmo tempo. | Aguarde alguns segundos e tente novamente |
| 429 | 40901 | A solicitação na entidade *' nome da entidade '* entrou em conflito com outra solicitação | Outra operação conflitante está em andamento no mesmo recurso/entidade | Aguarde a conclusão da operação anterior antes de tentar novamente |
| 429 | 40901 | Outra solicitação de atualização está em andamento para a entidade *' nome da entidade '*. | Outra operação conflitante está em andamento no mesmo recurso/entidade | Aguarde a conclusão da operação anterior antes de tentar novamente |
| 429 | nenhum | Conflito de recursos ocorrido. Outra operação conflitante pode estar em andamento. Se esta for uma nova tentativa para uma operação com falha, a limpeza em segundo plano ainda estará pendente. Tente novamente mais tarde. | Essa condição pode ser atingida quando há uma operação pendente na mesma entidade. | Aguarde a conclusão da operação anterior antes de tentar novamente. |


## <a name="error-code-not-found"></a>Código de erro: não encontrado

Essa classe de erros indica que o recurso não foi encontrado.

| Código do erro | Subcódigo de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Não encontrado | nenhum | A entidade *' nome da entidade '* não foi encontrada. | A entidade na qual a operação não foi encontrada. | Verifique se a entidade existe e tente a operação novamente. |
| Não encontrado | nenhum | Não encontrado. A operação não existe. | A operação que você está tentando executar não existe. | Verifique a operação e tente novamente. |
| Não encontrado | nenhum | A solicitação de entrada não é reconhecida como uma solicitação de Put de política de namespace. | O corpo da solicitação de entrada é nulo e, portanto, não pode ser executado como uma solicitação Put. | Verifique o corpo da solicitação para garantir que ele não seja nulo. | 
| Não encontrado | nenhum | Não foi possível encontrar a entidade de mensagens *' nome da entidade '* . | A entidade na qual você está tentando executar a operação não foi encontrada. | Verifique se a entidade existe e tente a operação novamente. |

## <a name="error-code-internal-server-error"></a>Código de erro: erro interno do servidor

Essa classe de erros indica que houve um erro interno do servidor

| Código do erro | Subcódigo de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Erro interno do servidor | 50000 | Subcódigo = 50000. Erro interno do servidor| Pode ocorrer por vários motivos. Alguns dos sintomas são- <ul> <li> A solicitação/o corpo do cliente está corrompido e leva a um erro. </li> <li> A solicitação do cliente atingiu o tempo limite devido a problemas de processamento no serviço. </li> </ul> | Para resolver isso <ul> <li> Verifique se os parâmetros de solicitações não são nulos ou malformados. </li> <li> Tente novamente a solicitação. </li> </ul> |

## <a name="error-code-unauthorized"></a>Código de erro: não autorizado

Essa classe de erros indica a ausência de autorização para executar o comando.

| Código do erro | Subcódigo de erro | Mensagem de erro | Descrição | Recomendação |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Não Autorizado | nenhum | Operação inválida no namespace secundário. O namespace secundário é somente leitura. | A operação foi executada em relação ao namespace secundário, que é configurado como um namespace ReadOnly. | Repita o comando no namespace primário. Saiba mais sobre o [namespace secundário](service-bus-geo-dr.md) |
| Não Autorizado | nenhum | MissingToken: o cabeçalho de autorização não foi encontrado. | Esse erro ocorre quando a autorização tem valores nulos ou incorretos. | Verifique se o valor do token mencionado no cabeçalho de autorização está correto e não é nulo. |