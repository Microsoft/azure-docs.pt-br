---
title: Função de administrador de cluster do Azure Red Hat OpenShift | Microsoft Docs
description: Atribuição e uso da função de administrador de cluster do Red Hat OpenShift do Azure
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: f4fc57ebe39a2169ea91423b295f4bc436746b29
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100636230"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Função de administrador de clientes do Red Hat OpenShift do Azure

> [!IMPORTANT]
> O Azure Red Hat OpenShift 3,11 será desativado em 30 de junho de 2022. O suporte para a criação de novos clusters do Azure Red Hat OpenShift 3,11 continua até 30 de novembro de 2020. Após a aposentadoria, os clusters restantes do Azure Red Hat OpenShift 3,11 serão desligados para evitar vulnerabilidades de segurança.
> 
> Siga este guia para [criar um cluster do Azure Red Hat OpenShift 4](tutorial-create-cluster.md).
> Se você tiver dúvidas específicas, [entre em contato conosco](mailto:arofeedback@microsoft.com).

Você é o administrador de cluster de um cluster do Azure Red Hat OpenShift. Sua conta aumentou as permissões e o acesso a todos os projetos criados pelo usuário.

Quando sua conta tiver a função de autorização Customer-admin-cluster associada a ela, ela poderá gerenciar automaticamente um projeto.

> [!Note] 
> A função de cluster Customer-admin-cluster não é a mesma que a função de cluster de administrador de cluster.

Por exemplo, você pode executar ações associadas a um conjunto de verbos ( `create` ) para operar em um conjunto de nomes de recursos ( `templates` ). Para exibir os detalhes dessas funções e seus conjuntos de verbos e recursos, execute o seguinte comando:

`$ oc get clusterroles customer-admin-cluster -o yaml`

Os nomes de verbo não necessariamente são mapeados diretamente para `oc` comandos. Eles correspondem mais geralmente aos tipos de operações da CLI que você pode executar. 

Por exemplo, ter o `list` verbo significa que você pode exibir uma lista de todos os objetos de um nome de recurso ( `oc get` ). O `get` verbo significa que você pode exibir os detalhes de um objeto específico se souber seu nome ( `oc describe` ).

## <a name="configure-the-customer-administrator-role"></a>Configurar a função de administrador do cliente

Você pode configurar a função de cluster Customer-admin-cluster somente durante a criação do cluster, fornecendo o sinalizador `--customer-admin-group-id` . Este campo não é configurável no momento no portal do Azure. Para saber como configurar Azure Active Directory e o grupo de administradores, confira [integração Azure Active Directory para o Azure Red Hat OpenShift](howto-aad-app-configuration.md).

## <a name="confirm-membership-in-the-customer-administrator-role"></a>Confirmar associação na função de administrador do cliente

Para confirmar sua associação no grupo administrador do cliente, tente os comandos da CLI do OpenShift `oc get nodes` ou `oc projects` . `oc get nodes` mostrará uma lista de nós se você tiver a função Customer-admin-cluster e um erro de permissão se você tiver apenas a função Customer-admin-Project. `oc projects` mostrará todos os projetos no cluster em vez de apenas os projetos nos quais você está trabalhando.

Para explorar ainda mais as funções e permissões no cluster, você pode usar o [`oc policy who-can <verb> <resource>`](https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html#managing-role-bindings) comando.

## <a name="next-steps"></a>Próximas etapas

Configure a função de cluster Customer-admin-cluster:
> [!div class="nextstepaction"]
> [Integração do Azure Active Directory para o Azure Red Hat OpenShift](howto-aad-app-configuration.md)
