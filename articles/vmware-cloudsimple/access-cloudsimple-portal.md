---
title: Acessar a solução VMware do Azure por CloudSimple-Portal
description: Descreve como acessar a solução VMware pelo portal CloudSimple do portal do Azure
author: Ajayan1008
ms.author: v-hborys
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0aa7a9a1f19a9d4fb2c555b08753e0b57c657974
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97895148"
---
# <a name="access-the-vmware-solution-by-cloudsimple-portal-from-the-azure-portal"></a>Acesse a solução VMware pelo portal do CloudSimple da portal do Azure

O logon único tem suporte para acesso ao portal do CloudSimple. Depois de entrar no portal do Azure, você poderá acessar o portal do CloudSimple sem entrar novamente. Na primeira vez que você acessar o portal do CloudSimple, você será solicitado a autorizar o aplicativo de [autorização de serviço do CloudSimple](#consent-to-cloudsimple-service-authorization-application) .  A autorização é uma ação única.

## <a name="before-you-begin"></a>Antes de começar

Usuários com funções de **colaborador** e **proprietário** interno podem acessar o portal do CloudSimple.  As funções devem ser configuradas no grupo de recursos em que o serviço CloudSimple está implantado.  As funções também podem ser configuradas no objeto de serviço CloudSimple.  Para obter mais informações sobre como verificar sua função, consulte o artigo [exibir atribuições de função](../role-based-access-control/check-access.md) . Somente os usuários com funções de **proprietário** e **colaborador** internas podem acessar o portal do CloudSimple.  As funções devem ser configuradas na assinatura.  Para obter mais informações sobre como verificar sua função, consulte o artigo [exibir atribuições de função](../role-based-access-control/check-access.md) .

Se você estiver usando funções personalizadas, a função deverá ter qualquer uma das operações a seguir em ```Actions``` .  Para obter mais informações sobre funções personalizadas, consulte [funções personalizadas do Azure](../role-based-access-control/custom-roles.md).  Se qualquer uma das operações fizer parte do ```NotActions``` , o usuário não poderá acessar o portal do CloudSimple.

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Acessar o portal da CloudSimple

1. Selecione **Todos os serviços**.

2. Procure **Serviços CloudSimples**.

3. Selecione o serviço CloudSimple no qual você deseja criar sua nuvem privada.

4. Na página **visão geral** , clique em **ir para o portal do CloudSimple**.  Se estiver acessando o portal do CloudSimple da portal do Azure pela primeira vez, você será solicitado a autorizar o aplicativo de [autorização de serviço do CloudSimple](#consent-to-cloudsimple-service-authorization-application) . 

    ![Iniciar o portal do CloudSimple](media/launch-cloudsimple-portal.png)

> [!NOTE]
> Se você selecionar uma operação de nuvem privada (como criar ou expandir uma nuvem privada) diretamente da portal do Azure, o portal do CloudSimple será aberto na página indicada.

No portal do CloudSimple, selecione **página inicial** no menu lateral para exibir informações de resumo sobre suas nuvens privadas. Os recursos e a capacidade de suas nuvens privadas são mostrados, juntamente com alertas e tarefas que exigem atenção. Para tarefas comuns, clique nos ícones nomeados na parte superior da página.

![Página inicial](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>Consentimento para o aplicativo de autorização do serviço CloudSimple

Iniciar o portal do CloudSimple da portal do Azure pela primeira vez requer seu consentimento para o aplicativo de autorização do serviço CloudSimple.  Selecione **aceitar** para conceder permissões solicitadas e acessar o portal do CloudSimple.

![Consentimento para a autorização do serviço CloudSimple-administradores](media/cloudsimple-azure-consent.png)

Se você tiver um privilégio de administrador global, poderá consentir para sua organização.  Selecione **Consentir em nome de sua organização**.

![Consentimento para a autorização do serviço CloudSimple-administrador global](media/cloudsimple-azure-consent-global-admin.png)

Se suas permissões não permitirem acesso ao portal do CloudSimple, entre em contato com o administrador global do seu locatário para conceder as permissões necessárias.  Um administrador global pode consentir em nome de sua organização.

![Consentimento para a autorização do serviço CloudSimple-requer administradores](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Próximas etapas

* Saiba como [criar uma nuvem privada](./create-private-cloud.md)
* Saiba como [configurar um ambiente de nuvem privada](quickstart-create-private-cloud.md)
