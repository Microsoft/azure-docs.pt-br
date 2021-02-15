---
title: Controle de acesso HSM gerenciado do Azure
description: Gerenciar permissões de acesso para HSM e chaves gerenciados pelo Azure. Aborda o modelo de autenticação e autorização para HSM gerenciado e como proteger seus HSMs.
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 816941fe0ec3a81c41da56acedcedf2de7febe74
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445227"
---
# <a name="managed-hsm-access-control"></a>Controle de acesso ao HSM Gerenciado

> [!NOTE]
> Key Vault provedor de recursos dá suporte a dois tipos de recursos: **cofres** e **HSMs gerenciados**. O controle de acesso descrito neste artigo se aplica somente a **HSMs gerenciados**. Para saber mais sobre o controle de acesso para HSM gerenciado, consulte [fornecer acesso a Key Vault chaves, certificados e segredos com um controle de acesso baseado em função do Azure](../general/rbac-guide.md).

O HSM gerenciado do Azure Key Vault é um serviço de nuvem que protege as chaves de criptografia. Como esses dados são confidenciais e comercialmente críticos, é necessário proteger o acesso aos seus HSMs gerenciados permitindo que apenas aplicativos e usuários autorizados os acessem. Este artigo apresenta uma visão geral do modelo de controle de acesso do HSM gerenciado. Explica a autenticação e a autorização e descreve como proteger o acesso ao seus HSMs gerenciados.

## <a name="access-control-model"></a>Modelo de controle de acesso

O acesso a um HSM gerenciado é controlado por meio de duas interfaces: o **plano de gerenciamento** e o **plano de dados**. O plano de gerenciamento é onde você gerencia o próprio HSM. As operações nesse plano incluem a criação e a exclusão de HSMs gerenciados e a recuperação de propriedades HSM gerenciadas. O plano de dados é onde você trabalha com os dados armazenados em um HSM gerenciado, que são chaves de criptografia com suporte do HSM. Você pode adicionar, excluir, modificar e usar chaves para executar operações criptográficas, gerenciar atribuições de função para controlar o acesso às chaves, criar um backup completo do HSM, restaurar o backup completo e gerenciar o domínio de segurança da interface do plano de dados.

Para acessar um HSM gerenciado em qualquer plano, todos os chamadores devem ter autenticação e autorização adequadas. A autenticação estabelece a identidade do chamador. A autorização determina quais operações o chamador pode executar. Um chamador pode ser qualquer uma das [entidades de segurança](../../role-based-access-control/overview.md#security-principal) definidas em Azure Active Directory-User, Group, entidade de serviço ou identidade gerenciada.

Ambos os planos usam Azure Active Directory para autenticação. Para autorização, eles usam sistemas diferentes da seguinte maneira
- O plano de gerenciamento usa o controle de acesso baseado em função do Azure--RBAC do Azure--um sistema de autorização criado no Azure Azure Resource Manager 
- O plano de dados usa um RBAC gerenciado no nível HSM (RBAC local HSM gerenciado) – um sistema de autorização implementado e imposto no nível de HSM gerenciado.

Quando um HSM gerenciado é criado, o solicitante também fornece uma lista de administradores de plano de dados (há suporte para todas as [entidades de segurança](../../role-based-access-control/overview.md#security-principal) ). Somente esses administradores podem acessar o plano de dados HSM gerenciado para executar operações de chave e gerenciar atribuições de função de plano de dados (RBAC local HSM gerenciado).

O modelo de permissão para ambos os planos usa a mesma sintaxe, mas eles são impostos em níveis diferentes e atribuições de função usam escopos diferentes. Plano de gerenciamento o RBAC do Azure é imposto por Azure Resource Manager enquanto o RBAC local do HSM gerenciado pelo plano de dados é imposto pelo próprio HSM gerenciado.

> [!IMPORTANT]
> A concessão de um plano de gerenciamento principal de segurança acesso a um HSM gerenciado não concede a eles nenhum acesso ao plano de dados para acessar as chaves ou as atribuições de função do plano de dados no RBAC local HSM gerenciado. Esse isolamento é por design para evitar a expansão inadvertida de privilégios que afetam o acesso às chaves armazenadas no HSM gerenciado.

Por exemplo, um administrador de assinatura (já que eles têm permissão de "colaborador" para todos os recursos na assinatura) podem excluir um HSM gerenciado em sua assinatura, mas se eles não tiverem acesso de plano de dados especificamente concedido por meio do RBAC local HSM gerenciado, eles não poderão obter acesso a chaves ou gerenciar atribuição de função no HSM gerenciado para conceder a si mesmos ou a outros acessos ao plano

## <a name="azure-active-directory-authentication"></a>Autenticação do Azure Active Directory

Quando você cria um HSM gerenciado em uma assinatura do Azure, ele é automaticamente associado ao locatário Azure Active Directory da assinatura. Todos os chamadores em ambos os planos devem ser registrados nesse locatário e autenticar para acessar o HSM gerenciado.

O aplicativo é autenticado com Azure Active Directory antes de chamar qualquer plano. O aplicativo pode usar qualquer [método de autenticação com suporte](../../active-directory/develop/authentication-vs-authorization.md) com base no tipo de aplicativo. O aplicativo adquire um token para um recurso no plano para obter acesso. O recurso é um ponto de extremidade no plano de gerenciamento ou de dados, com base no ambiente do Azure. O aplicativo usa o token e envia uma solicitação da API REST para o ponto de extremidade HSM gerenciado. Para saber mais, examine o [fluxo de autenticação completo](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

O uso de um único mecanismo de autenticação para ambos os planos tem vários benefícios:

- As organizações podem controlar o acesso centralmente a todos os HSMs gerenciados em sua organização.
- Se um usuário deixar, ele perderá instantaneamente o acesso a todos os HSMs gerenciados na organização.
- As organizações podem personalizar a autenticação usando as opções no Azure Active Directory, como habilitar a autenticação multifator para aumentar a segurança.

## <a name="resource-endpoints"></a>Pontos de extremidade do recurso

As entidades de segurança acessam os planos por meio de pontos de extremidade. Os controles de acesso dos dois planos trabalham de forma independente. Para conceder a um aplicativo acesso para usar chaves em um HSM gerenciado, conceda acesso ao plano de dados usando o RBAC local do HSM gerenciado. Para conceder a um usuário acesso ao recurso HSM gerenciado para criar, ler, excluir, mover os HSMs gerenciados e editar outras propriedades e marcas, use o RBAC do Azure.

A tabela a seguir mostra os pontos de extremidade para os planos de gerenciamento e de dados.

| Plano de&nbsp;acesso | Pontos de extremidade de acesso | Operações | Mecanismo de controle de acesso |
| --- | --- | --- | --- |
| Plano de gerenciamento | **Global:**<br> management.azure.com:443<br> | Criar, ler, atualizar, excluir e mover HSMs gerenciados<br>Definir marcas HSM gerenciadas | RBAC do Azure |
| Plano de dados | **Global:**<br> &lt;HSM-name &gt; . Vault.Azure.net:443<br> | **Chaves** : descriptografar, criptografar,<br> desencapsular, encapsular, verificar, assinar, obter, listar, atualizar, criar, importar, excluir, fazer backup, restaurar, limpar<br/><br/> **Função do plano de dados – gerenciamento (RBAC local gerenciado HSM)**_: listar definições de função, atribuir funções, excluir atribuições <br/> <br/> de função, definir funções personalizadas_ * backup/restauração **: backup, restauração, <br/> <br/> verificar status backup/restaurar operações** domínio de segurança * *: baixar e carregar domínio de segurança | RBAC local HSM gerenciado |
|||||
## <a name="management-plane-and-azure-rbac"></a>Plano de gerenciamento e RBAC do Azure

No plano de gerenciamento, você usa o RBAC do Azure para autorizar as operações que um chamador pode executar. No modelo RBAC do Azure, cada assinatura do Azure tem uma instância de Azure Active Directory. Você pode conceder acesso a usuários, grupos e aplicativos desse diretório. O acesso é concedido para gerenciar recursos na assinatura do Azure que usa o modelo de implantação do Azure Resource Manager. Para conceder acesso, use o [Portal do Azure](https://portal.azure.com/), a [CLI do Azure](/cli/azure/install-classic-cli), o [Azure PowerShell](/powershell/azureps-cmdlets-docs) ou as [APIs REST do Azure Resource Manager](/rest/api/authorization/roleassignments).

Você cria um cofre de chaves em um grupo de recursos e gerencia o acesso usando Azure Active Directory. Conceda a usuários ou grupos a capacidade de gerenciar os cofres de chaves em um grupo de recursos. Conceda o acesso em um nível de escopo específico atribuindo funções apropriadas do Azure. Para conceder acesso a um usuário para gerenciar os cofres de chaves, atribua uma função `key vault Contributor` predefinida ao usuário em um escopo específico. Os seguintes níveis de escopos podem ser atribuídos a uma função do Azure:

- **Grupo de gerenciamento** : uma função do Azure atribuída no nível de assinatura se aplica a todas as assinaturas nesse grupo de gerenciamento.
- **Assinatura** : uma função do Azure atribuída no nível de assinatura se aplica a todos os grupos de recursos e recursos dentro dessa assinatura.
- **Grupo de recursos** : uma função do Azure atribuída no nível do grupo de recursos se aplica a todos os recursos nesse grupo de recursos.
- **Recurso específico** : uma função do Azure atribuída a um recurso específico se aplica a esse recurso. Nesse caso, o recurso é um cofre de chaves específico.

Há várias funções predefinidas. Se uma função predefinida não atender às suas necessidades, você poderá definir sua própria função. Para obter mais informações, consulte [RBAC do Azure: funções internas](../../role-based-access-control/built-in-roles.md).

## <a name="data-plane-and-managed-hsm-local-rbac"></a>Plano de dados e RBAC local HSM gerenciado

Você concede um acesso de entidade de segurança para executar operações de chave específicas atribuindo uma função. Para cada atribuição de função, você precisa especificar uma função e um escopo sobre o qual essa atribuição se aplica. Para dois escopos de RBAC locais do HSM gerenciado estão disponíveis.

- **"/" ou "/Keys"** : escopo de nível de HSM. As entidades de segurança atribuídas a uma função nesse escopo podem executar as operações definidas na função para todos os objetos (chaves) no HSM gerenciado.
- **"/Keys/ &lt; Key-Name &gt; "** : escopo de nível de chave. As entidades de segurança atribuídas a uma função nesse escopo podem executar as operações definidas nesta função somente para todas as versões da chave especificada.

## <a name="next-steps"></a>Próximas etapas

- Para ver um tutorial de introdução para um administrador, consulte [O que é o HSM gerenciado?](overview.md).
- Para um tutorial de gerenciamento de função, consulte [RBAC local HSM gerenciado](role-management.md)
- Para obter mais informações sobre o registro em log do uso para o registro em log do HSM gerenciado, confira [Registro em log do HSM gerenciado](logging.md).