---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: azure-policy
author: craigshoemaker
ms.service: azure-policy
ms.topic: include
ms.date: 05/18/2018
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: e6a0ded137162328fd446b65ddb4a15fa6f1db88
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172135"
---
## <a name="deleting-personal-information"></a>Excluir informações pessoais

[!INCLUDE [gdpr-intro-sentence.md](gdpr-intro-sentence.md)]

Informações pessoais são relevantes para o serviço de importação/exportação (através do portal e API) durante as operações de importação e exportação. Dados usados durante esses processos incluem:

- Nome de contato
- Número de telefone
- Email
- Endereço
- City
- CEP/Código postal
- Estado
- Região/País/Região
- ID da Unidade
- Número da conta da operadora
- Enviar número de controle

Quando um trabalho de importação/exportação é criado, os usuários fornecem informações de contato e um endereço de envio. Informações pessoais são armazenadas em até dois locais diferentes: no trabalho e, opcionalmente, nas configurações do portal. Somente informações pessoais são armazenadas nas configurações do portal se você marcar a caixa de seleção rotulada, **Salvar a operadora e retornar como padrão** durante a seção*Retornar informações de envio* do processo de exportação.

Informações de contato pessoais podem ser excluídas das seguintes maneiras:

- Dados salvos com o trabalho são excluídos com o trabalho. Os usuários podem excluir trabalhos manualmente e trabalhos concluídos são excluídos automaticamente depois de 90 dias. Você poderá excluir manualmente os trabalhos por meio da API REST ou do portal do Microsoft Azure. Para excluir o trabalho no portal do Microsoft Azure, vá para o trabalho de importação/exportação e clique em *Excluir* na barra de comandos. Para obter detalhes sobre como excluir um trabalho de importação/exportação por meio da API REST, consulte [Excluir um trabalho de importação/exportação](../articles/storage/common/storage-import-export-cancelling-and-deleting-jobs.md).

- Informações de contato salvas nas configurações do portais podem ser removidas, excluindo as configurações do portal. É possível excluir as configurações do portal seguindo as etapas a seguir:
  - Entre no [Portal do Azure](https://portal.azure.com).
  - Clique no ícone *Configurações* ![Ícone de Configurações do Microsoft Azure](media/storage-import-export-delete-personal-info/azure-settings-icon.png)
  - Clique em *Exportar todas as configurações* (para salvar suas configurações atuais para um `.json` arquivo).
  - Clique em *Excluir todas as configurações e os painéis privados* para excluir todas as configurações, incluindo informações de contato salvas.

Para mais informações, revise a Política de Privacidade da Microsoft na [Central de Confiabilidade](https://www.microsoft.com/trustcenter)