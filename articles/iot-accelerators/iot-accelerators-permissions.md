---
title: Usar o site de Soluções do Azure IoT - Azure | Microsoft Docs
description: Descreve como usar o site AzureIoTSolutions.com para implantar o acelerador de solução.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dobett
ms.openlocfilehash: 5a088cb07fa74b65e497de54be8c97bd51d31c16
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387829"
---
# <a name="use-the-azureiotsolutionscom-site-to-deploy-your-solution-accelerator"></a>Usar o site azureiotsolutions.com para implementar o acelerador de solução

É possível implantar aceleradores de solução do Azure IoT na assinatura do Azure pelo site [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators). O AzureIoTSolutions.com hospeda ambos os aceleradores de solução de parceiro e software livre da Microsoft. Esses aceleradores de solução estão alinhados com a [Arquitetura de referência do Azure IoT](/azure/architecture/reference-architectures/iot). Você pode usar o site para implantar rapidamente um acelerador de solução como um ambiente de produção ou demonstração.

:::image type="content" source="media/iot-accelerators-permissions/iotsolutionscom.png" alt-text="home page de soluções de IoT":::

> [!TIP]
> Se for necessário ter mais controle sobre o processo de implantação, você poderá usar a CLI para implantar um acelerador de solução.

É possível implantar os aceleradores de solução nas seguintes configurações:

* **Standard**: uma implantação de infraestrutura expandida para o desenvolvimento de um ambiente de produção. O Serviço de Contêiner do Azure implanta os microsserviços em máquinas virtuais do Azure. O Kubernetes orquestra os contêineres do Docker que hospedam os microsserviços individuais.
* **Básico**: uma versão de custo reduzida para uma demonstração ou para testar uma implantação. Todos os microsserviços implantam em uma única máquina virtual do Azure.
* **Local**: uma implantação de máquina local para teste e desenvolvimento. Essa abordagem implanta os microsserviços em um contêiner do Docker local e conecta-se ao Hub IoT, Azure Cosmos DB e serviços de armazenamento do Azure na nuvem.

Cada um dos aceleradores de solução usa uma combinação diferente de serviços do Azure como o Hub IoT, Azure Stream Analytics e Cosmos DB. Para obter mais informações, visite [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators) e selecione um acelerador de solução.

## <a name="sign-in-at-azureiotsolutionscom"></a>Entrar no azureiotsolutions.com

Antes de poder implantar um acelerador de solução, você deverá entrar no AzureIoTSolutions.com usando as credenciais associadas a uma assinatura do Azure. Se a conta estiver associada a mais de um locatário do Microsoft AD (Azure Active Directory), você poderá usar o menu suspenso de **Seleção de conta** para escolher o diretório a ser usado.

As permissões para implantar aceleradores de solução, gerenciar usuários e gerenciar serviços do Azure dependem da sua função no diretório selecionado. As funções comuns do Azure AD associadas aos aceleradores de solução incluem:

* **Administrador global**: pode haver muitos [administradores globais](../active-directory/roles/permissions-reference.md) por locatário do Azure AD:

  * Ao criar um locatário do Azure AD, você será, por padrão, o administrador global desse locatário.
  * O administrador global pode implantar aceleradores de solução padrão e básico.

* **Usuário de domínio**: pode haver muitos usuários de domínio por locatário do Azure AD. Um usuário de domínio pode implantar um acelerador de solução básico.

* **Usuário convidado**: pode haver muitos usuários convidados por locatário do Azure AD. Os usuários convidados não podem implantar um acelerador de solução no locatário do Azure AD.

Para obter mais informações sobre usuários e funções no Azure AD, consulte os seguintes recursos:

* [Criar usuários no Azure Active Directory](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
* [Atribuir usuários a aplicativos](../active-directory/manage-apps/assign-user-or-group-access-portal.md)

## <a name="choose-your-device"></a>Escolha o dispositivo

Os links do site AzureIoTSolutions.com para o [catálogo de dispositivos Microsoft Azure Certified para IoT](https://catalog.azureiotsolutions.com/).

O catálogo lista centenas de dispositivos de hardware de IoT certificados que você pode conectar aos aceleradores de solução para começar a criar sua solução de IoT.

![Catálogo de dispositivos](media/iot-accelerators-permissions/devicecatalog.png)

Se você for um fabricante de hardware, clique em **Tornar-se um Parceiro** para saber mais sobre a parceria com a Microsoft no programa Microsoft Azure Certified para IoT.

## <a name="next-steps"></a>Próximas etapas

Para experimentar um dos aceleradores de solução de IoT, confira o guia de início rápido: [Experimente uma solução de fábrica conectada](quickstart-connected-factory-deploy.md).
