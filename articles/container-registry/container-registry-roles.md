---
title: Registro de Contêiner do Azure - funções e permissões
description: Use o controle de acesso baseado nas função do Azure (RBAC) e o gerenciamento de identidades e acesso (IAM) para fornecer permissões refinadas a recursos em um registro de contêiner do Azure.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 03/20/2019
ms.author: danlep
ms.openlocfilehash: 793dbf056201a3315a9b77dfebbb9331a8ed7db1
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310594"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Funções e permissões do Registro de Contêiner do Azure

O serviço de Registro de Contêiner do Azure dá suporte a um conjunto de funções do Azure que fornecem níveis diferentes de permissões para um registro de contêiner do Azure. Use o [controle de acesso baseado em função do Azure](../role-based-access-control/index.yml) (RBAC) para atribuir permissões específicas a usuários ou entidades que precisam interagir com um registro de serviço.

| Função/permissão       | [Acessar o Resource Manager](#access-resource-manager) | [Criar/excluir registro](#create-and-delete-registry) | [Enviar uma imagem por push](#push-image) | [Pull de imagem](#pull-image) | [Excluir dados de imagem](#delete-image-data) | [Alterar políticas](#change-policies) |   [Imagens de entrada](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| Proprietário | X | X | X | X | X | X |  |  
| Colaborador | X | X | X |  X | X | X |  |  
| Leitor | X |  |  |  |  |  |  |
| AcrPush |  |  | X | X | |  |  |  
| AcrPull |  |  |  | X |  |  |  |  
| AcrDelete |  |  |  |  | X |  |  |
| AcrImageSigner |  |  |  |  |  |  | X |

## <a name="differentiate-users-and-services"></a>Diferenciar usuários e serviços

Qualquer permissão de tempo é aplicada, uma prática recomendada é fornecer o conjunto mais limitado de permissões para uma pessoa ou serviço realizar uma tarefa. Os conjuntos de permissões a seguir representam um conjunto de recursos que podem ser usados por seres humanos e serviços sem periféricos.

### <a name="cicd-solutions"></a>Soluções CI/CD

Ao automatizar `docker build` comandos de soluções CI/CD, você precisa de `docker push` recursos. Para esses cenários de serviço sem periféricos, sugerimos atribuir a função **AcrPush**. Essa função, ao contrário da função mais ampla **Colaborador**, impede que a conta execute utras operações de registro ou acesse o Azure Resource Manager.

### <a name="container-host-nodes"></a>Nós de host do contêiner

Da mesma forma, nós que executam seus contêineres precisam da função **AcrPull**, mas não devem exibir os recursos de **Leitor**.

### <a name="visual-studio-code-docker-extension"></a>Extensão do Visual Studio Code Docker

Para ferramentas como o Visual Studio Code [extensão Docker](https://code.visualstudio.com/docs/azure/docker), é necessário acesso de provedor de recursos adicionais para listar os registros de contêiner do Azure disponíveis. Nesse caso, forneça aos usuários acesso para a função de **Leitor** ou **Colaborador**. Essas funções permitem `docker pull`, `docker push`, `az acr list`, `az acr build`e outros recursos. 

## <a name="access-resource-manager"></a>Acessar o Resource Manager

O Acesso ao Azure Resource Manager é necessário para o portal do Azure e o gerenciamento de registro com o [CLI do Azure](/cli/azure/). Por exemplo, para obter uma lista de registros usando o `az acr list` comando, você precisa dessa permissão definida. 

## <a name="create-and-delete-registry"></a>Criar e excluir registros

A capacidade de criar e excluir registros de contêiner do Azure.

## <a name="push-image"></a>Enviar uma imagem por push

A capacidade de `docker push` uma imagem, ou enviar por push outro [artefato com suporte](container-registry-image-formats.md) para um registro, como um gráfico do Helm. Requer [autenticação](container-registry-authentication.md) com o registro usando a identidade autorizada. 

## <a name="pull-image"></a>Pull de imagem

A capacidade de `docker pull` uma imagem de não quarentena, ou de enviar por push outro [artefato com suporte](container-registry-image-formats.md) de um registro, como um gráfico do Helm. Requer [autenticação](container-registry-authentication.md) com o registro usando a identidade autorizada.

## <a name="delete-image-data"></a>Excluir dados de imagem

A capacidade de [Excluir imagens de contêiner](container-registry-delete.md)ou excluir outros [artefatos com suporte](container-registry-image-formats.md) , como gráficos Helm, de um registro.

## <a name="change-policies"></a>Alterar políticas

A capacidade de configurar políticas em um registro. Políticas incluem a limpeza de imagem, habilitando a quarentena e assinatura de imagem.

## <a name="sign-images"></a>Imagens de entrada

A capacidade de imagens de entrada normalmente é atribuída a um processo automatizado, que usaria uma entidade de serviço. Normalmente, essa permissão é combinada com [imagem por push](#push-image) para permitir o envio de uma imagem confiável para um registro. Para obter detalhes, consulte [Confiança de conteúdo no Registro de Contêiner do Azure](container-registry-content-trust.md).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre como atribuir funções RBAC para uma identidade do Azure usando o [portal do Azure](../role-based-access-control/role-assignments-portal.md), [CLI do Azure](../role-based-access-control/role-assignments-cli.md), ou outras ferramentas do Azure.

* Saiba mais sobre [opções de autenticação](container-registry-authentication.md) para Registro de Contêiner do Azure.
