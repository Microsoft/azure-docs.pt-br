---
title: Erros de status do conector no painel do ITSMC
description: Saiba mais sobre os erros comuns existentes no painel de Conector de Gerenciamento de Serviços de TI.
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/18/2021
ms.openlocfilehash: 5cc3c4a07cc698f3592a2ff2fd76e9f4bbef441b
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036445"
---
# <a name="connector-status-errors-in-the-itsmc-dashboard"></a>Erros de status do conector no painel do ITSMC

O painel Conector de Gerenciamento de Serviços de TI (ITSMC) apresenta erros que podem ajudá-lo a corrigir problemas em seu conector.

As seções a seguir descrevem os erros comuns que aparecem na seção status do conector do painel e como você pode resolvê-los.

## <a name="unexpected-response"></a>Resposta inesperada

**Erro**: "resposta inesperada do ServiceNow junto com o código de status de êxito. Resposta: {"import_set": "{import_set_id}", "staging_table": "x_mioms_microsoft_oms_incident", "resultado": [{"transform_map": "incidente do OMS", "tabela": "incidente", "status": "erro", "error_message": "{registro de destino não encontrado | Tabela inválida | Tabela de preparo inválida "}"

**Causa**: o ServiceNow retorna esse erro quando:

* Um script personalizado implantado em uma instância do ServiceNow faz com que os incidentes sejam ignorados.
* O código "aplicativo do integrador do OMS" foi modificado no lado do ServiceNow (por exemplo, por meio do `onBefore` script).

**Resolução**: Desabilite todos os scripts personalizados ou modificações de código.

## <a name="exception-update-failure"></a>Falha na atualização da exceção

**Erro**: "{" erro ": {" Message ":" falha na operação "," Detail ":" falha na atualização da exceção de ACL devido a restrições de segurança "}"

**Causa**: as permissões do ServiceNow estão configuradas incorretamente.

**Resolução**: Verifique se todas as funções estão corretamente atribuídas conforme [especificado](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role).

## <a name="problem-sending-a-request"></a>Problema ao enviar uma solicitação

**Erro**: "ocorreu um erro ao enviar a solicitação."

**Causa**: uma instância do ServiceNow não está disponível.

**Resolução**: Verifique sua instância no ServiceNow. Ele pode ser excluído ou não disponível.

## <a name="servicenow-rate-problem"></a>Problema de taxa do ServiceNow

**Erro**: "ServiceDeskHttpBadRequestException: StatusCode = 429"

**Causa**: os limites de taxa do ServiceNow são muito altos ou muito baixos.

**Resolução**: aumente ou cancele os limites de taxa na instância do servicenow, conforme explicado na [documentação do servicenow](https://docs.servicenow.com/bundle/london-application-development/page/integrate/inbound-rest/task/investigate-rate-limit-violations.html).

## <a name="invalid-refresh-token"></a>Token de atualização inválido

**Erro**: 
  * "AccessToken e RefreshToken inválidos. O usuário precisa autenticar novamente. "
  * "Não foi possível sincronizar a configuração de modelos para evento, alerta, incidente. Consulte a mensagem de exceção para obter mais detalhes. "

**Causa**: um token de atualização expirou.

**Resolução**: Sincronize o ITSMC para gerar um novo token de atualização, conforme explicado em [como corrigir manualmente os problemas de sincronização](./itsmc-resync-servicenow.md).

## <a name="missing-connector"></a>Conector ausente

**Erro**: "não foi possível criar/atualizar o item de trabalho para o alerta {AlertName}. Conector ITSM {connectionIdentifier} não existe ou foi excluído. "

**Causa**: ITSMC foi excluído.

**Resolução**: ITSMC foi excluído, mas os grupos de ações de ITSM (gerenciamento de serviços de ti) definidos ainda estão associados a ele. Há três opções para resolver esse problema:

* Localize e desabilite ou exclua esses grupos de ação.
* [Reconfigure os grupos de ação](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts) para usar uma instância de ITSMC existente.
* [Crie uma nova instância do ITSMC](./itsmc-definition.md#create-an-itsm-connection) e [Reconfigure os grupos de ação para usá-la](itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

## <a name="lack-of-connection-details"></a>Falta de detalhes de conexão

**Erro**: "algo deu errado. Não foi possível obter os detalhes da conexão. " Esse erro aparece quando você define um grupo de ações de ITSM.

**Causa**: esse erro aparece em uma destas situações:

* Uma instância de Conector ITSM recém-criada ainda não concluiu a sincronização inicial.
* O conector não foi definido corretamente.

**Resolução**: 

* Quando uma nova instância de ITSMC é criada, ela inicia a sincronização de informações do sistema ITSM, como modelos de item de trabalho e itens de trabalho. [Sincronize o ITSMC para gerar um novo token de atualização](./itsmc-resync-servicenow.md).
* [Examine os detalhes da conexão em ITSMC](./itsmc-connections-servicenow.md#create-a-connection) e verifique se o ITSMC pode ser [sincronizado](./itsmc-resync-servicenow.md)com êxito.
