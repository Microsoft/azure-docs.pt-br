---
title: Funções e permissões do Azure Data Factory
description: Descreve as funções e permissões necessárias para criar Data Factories e para trabalhar com recursos filho.
ms.date: 11/5/2018
ms.topic: conceptual
ms.service: data-factory
author: dcstwh
ms.author: weetok
ms.openlocfilehash: cec5df9a5046e912ab8542c91bde4344affa0925
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100364470"
---
# <a name="roles-and-permissions-for-azure-data-factory"></a>Funções e permissões do Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


Este artigo descreve as funções necessárias para criar e gerenciar recursos do Azure Data Factory e as permissões concedidas por essas funções.

## <a name="roles-and-requirements"></a>Requisitos e funções

Para criar instâncias de Data Factory, a conta de usuário que você usa para entrar no Azure deve ser membro da função de *colaborador* , da função de *proprietário* ou de um *administrador* da assinatura do Azure. Para exibir as permissões que você tem na assinatura, portal do Azure, selecione seu nome de usuário no canto superior direito e selecione **Permissões**. Se tiver acesso a várias assinaturas, selecione a que for adequada. 

Para criar e gerenciar recursos filho para o Data Factory – incluindo conjuntos de dados, serviços vinculados, pipelines, gatilhos e runtimes de integração –, os requisitos a seguir são aplicáveis:
- Para criar e gerenciar recursos filho no portal do Azure, você deve pertencer à função de **colaborador de data Factory** no nível do grupo de **recursos** ou acima.
- Para criar e gerenciar recursos filho com o PowerShell ou o SDK, a função de **colaborador** no nível do recurso ou superior é suficiente.

Para obter instruções de exemplo sobre como adicionar um usuário a uma função, confira o artigo [Adicionar funções](../cost-management-billing/manage/add-change-subscription-administrator.md).

## <a name="set-up-permissions"></a>Configurar permissões

Depois de criar um Data Factory, convém permitir que outros usuários trabalhem com ele. Para conceder esse acesso a outros usuários, você precisa adicioná-los à função de colaborador de **Data Factory** interna no grupo de **recursos** que contém o data Factory.

### <a name="scope-of-the-data-factory-contributor-role"></a>Escopo da função de Colaborador do Data Factory

A associação da função **colaborador de data Factory** permite que os usuários realizem as seguintes ações:
- Criem, editem e excluam os data factories e os recursos filho, incluindo conjuntos de dados, serviços vinculados, pipelines, gatilhos e runtimes de integração.
- Implantem modelos do Resource Manager. A implantação do Resource Manager é o método de implantação usado pelo Data Factory no portal do Azure.
- Gerenciem alertas do App Insights de um data factory.
- Criem tíquetes de suporte.

Para obter mais informações sobre essa função, confira [Função de Colaborador do Data Factory](../role-based-access-control/built-in-roles.md#data-factory-contributor).

### <a name="resource-manager-template-deployment"></a>Implantação de modelo do Resource Manager

A função **Colaborador do Data Factory**, no nível do grupo de recursos ou acima, permite que os usuários implantem modelos do Resource Manager. Como resultado, os membros da função podem usar modelos do Resource Manager para implantar o data factory e seus recursos filho, incluindo conjuntos de dados, serviços vinculados, pipelines, gatilhos e runtimes de integração. A associação nessa função não permite que o usuário crie outros recursos.

As permissões no Azure Repos e no GitHub são independentes das permissões do Data Factory. Como resultado, um usuário com permissões de repo que é apenas um membro da função Leitor pode editar os recursos filhos do Data Factory e confirmar alterações no repo, mas não pode publicar essas alterações.


> [!IMPORTANT]
> A implantação de modelo do Resource Manager com a função **Colaborador do Data Factory** não eleva as permissões. Por exemplo, se você implantar um modelo que cria uma máquina virtual do Azure e você não tiver permissão para criar máquinas virtuais, a implantação falhará com um erro de autorização.

   No contexto de publicação, a permissão **Microsoft. datafactory/factories/Write** se aplica aos modos a seguir.
- Essa permissão só é necessária no modo dinâmico quando o cliente modifica os parâmetros globais.
- Essa permissão sempre é necessária no modo git, uma vez que sempre que o cliente é publicado, o objeto de fábrica com a última ID de confirmação precisa ser atualizado.

### <a name="custom-scenarios-and-custom-roles"></a>Cenários personalizados e funções personalizadas

Às vezes, você precisa conceder diferentes níveis de acesso para diferentes usuários do data factory. Por exemplo:
- Você pode precisar de um grupo em que os usuários só têm permissões em um data factory específico.
- Ou você pode precisar de um grupo em que os usuários só podem monitorar um data factory (ou factories), mas não podem modificá-lo.

Você pode obter esses cenários personalizados criando funções personalizadas e atribuindo usuários a essas funções. Para obter mais informações sobre funções personalizadas, confira [Funções personalizadas no Azure](..//role-based-access-control/custom-roles.md).

Aqui estão alguns exemplos que demonstram o que você pode obter com as funções personalizadas:

- Permitir que um usuário crie, edite ou exclua qualquer data factory em um grupo de recursos por meio do portal do Azure.

  Atribuir a função interna **Colaborador do Data Factory** no nível do grupo de recursos ao usuário. Se você quiser permitir o acesso a qualquer data factory em uma assinatura, atribua a função no nível da assinatura.

- Permitir que um usuário exiba (leia) e monitore um data factory, mas não o edite ou altere.

  Atribuir a função interna **leitor** no recurso do data factory ao usuário.

- Permitir que um usuário edite um único data factory no portal do Azure.

  Esse cenário requer duas atribuições de função.

  1. Atribuir a função interna **Colaborador** no nível do data factory.
  2. Crie uma função personalizada com a permissão **Microsoft.Resources/deployments/** . Atribua essa função personalizada ao usuário no nível do grupo de recursos.

- Permitir que um usuário seja capaz de testar a conexão em um serviço vinculado ou Visualizar dados em um DataSet

    Crie uma função personalizada com permissões para as seguintes ações: **Microsoft. datafactory/factories/Getfeaturevalue/Read** e **Microsoft. datafactory/factories/getDataPlaneAccess/Action**. Atribua essa função interna no recurso do data factory para o usuário.

- Permitir que um usuário atualize um data factory usando o PowerShell ou o SDK, mas não o portal do Azure.

  Atribuir a função interna **Colaborador** no recurso do data factory ao usuário. Essa função permite que o usuário veja os recursos no portal do Azure, mas não permite que ele acesse os botões **Publicar** e **Publicar Tudo**.


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as funções no Azure – [Entender as definições de função](../role-based-access-control/role-definitions.md)

- Saiba mais sobre a função **Colaborador do Data Factory** – [Função de Colaborador do Data Factory](../role-based-access-control/built-in-roles.md#data-factory-contributor).
