---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/16/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 0cb3de7d893ccfe638468110b1b6f5fb61b2bc7c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60742132"
---
Bloqueios de recursos impedem que os usuários em sua organização acidentalmente excluam ou modifiquem recursos críticos. Ao contrário do controle de acesso baseado em função, bloqueios de recurso aplicam uma restrição a todos os usuários e funções. 

É possível definir o nível de bloqueio como **CanNotDelete** ou **ReadOnly**. No portal, os níveis de bloqueios são exibidos como **Excluir** e **Somente leitura** respectivamente.

* **CanNotDelete** significa que os usuários autorizados ainda poderão ler e modificar um recurso, mas não poderão excluir o recurso. 
* **ReadOnly** significa que os usuários autorizados poderão ler um recurso, mas não poderão excluir ou atualizar o recurso. Aplicar esse bloqueio é semelhante ao restringir todos os usuários autorizados para as permissões concedidas pela função **Leitor**. 

> [!TIP]
> Tenha cuidado ao aplicar um bloqueio **ReadOnly**. Algumas operações que parecem operações de leitura na verdade exigem ações adicionais. Por exemplo, um bloqueio **ReadOnly** em uma conta de armazenamento impede que todos os usuários listem as chaves. A operação de lista de chaves é tratada por meio de uma solicitação POST, pois as chaves retornadas estão disponíveis para operações de gravação. A aplicação de um bloqueio **ReadOnly** em um recurso do Serviço de Aplicativo impedirá o Visual Studio Server Explorer de exibir os arquivos para o recurso, pois essa interação exige acesso de gravação.

Quando você aplica um bloqueio a um escopo pai, todos os recursos filho herdam o mesmo bloqueio. Até mesmo os recursos que você adiciona posteriormente herdam o bloqueio do pai. O bloqueio mais restritivo na herança terá precedência.

Bloqueios do Resource Manager se aplicam apenas às operações que ocorrem no plano de gerenciamento, que consistem em operações enviadas para `https://management.azure.com`. Os bloqueios não restringem a maneira como os recursos processam suas próprias funções. Alterações de recursos são restritas, mas as operações de recursos não são restritas. Por exemplo, um bloqueio de ReadOnly em um banco de dados SQL impede que você de exclua ou modifique o banco de dados. Ele não impede você de criar, atualizar ou excluir dados no banco de dados. Transações de dados são permitidas porque essas operações não são enviadas para `https://management.azure.com`.

### <a name="who-can-create-or-delete-locks-in-your-organization"></a>Quem pode criar ou excluir bloqueios na sua organização
Para criar ou excluir bloqueios de gerenciamento, você deve ter acesso às ações `Microsoft.Authorization/locks/*`. Das funções internas, somente **Proprietário** e **Administrador do Acesso de Usuário** recebem essas ações.
