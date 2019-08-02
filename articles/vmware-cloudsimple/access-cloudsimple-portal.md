---
title: Solução de VMware do Azure acesso por CloudSimple - Portal
description: Descreve como acessar a solução VMware pelo portal CloudSimple do portal do Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 61c3d37f365034984231c780199e181872c010c6
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544123"
---
# <a name="accessing-the-vmware-solution-by-cloudsimple-portal-from-azure-portal"></a>Acessando a solução VMware CloudSimple portal do portal do Azure

O logon único é compatível com acesso ao portal do CloudSimple. Depois de entrar no portal do Azure, você pode acessar o portal de CloudSimple sem precisar entrar novamente. A primeira vez que acessar o portal de CloudSimple você será solicitado a autorizar o [autorização do serviço CloudSimple](#consent-to-cloudsimple-service-authorization-application) aplicativo.  A autorização é uma ação única.

## <a name="before-you-begin"></a>Antes de começar

Os usuários com builtin **proprietário** e **Colaborador** funções podem acessar o portal de CloudSimple.  As funções devem ser configuradas no grupo de recursos onde CloudSimple serviço foi implantado.  As funções também podem ser configuradas no objeto de serviço CloudSimple.  Para obter mais informações sobre a verificação da sua função, consulte [exibir atribuições de função](https://docs.microsoft.com/azure/role-based-access-control/check-access) artigo.

Se você estiver usando funções personalizadas, a função deve ter qualquer uma das seguintes operações em ```Actions```.  Para obter mais informações sobre funções personalizadas, consulte [funções personalizadas para recursos do Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).  Se qualquer uma das operações é uma parte do ```NotActions```, o usuário não é possível acessar o portal de CloudSimple. 

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

2. Pesquise **CloudSimple serviços**.

3. Selecione o serviço CloudSimple no qual você deseja criar sua nuvem privada.

4. Sobre o **visão geral** , clique em **acesse o portal CloudSimple**.  Se você estiver acessando o portal de CloudSimple do portal do Azure pela primeira vez, você será solicitado a autorizar o [autorização do serviço CloudSimple](#consent-to-cloudsimple-service-authorization-application) aplicativo. 

    ![Inicie o portal de CloudSimple](media/launch-cloudsimple-portal.png)

> [!TIP]
> Se você selecionar uma operação de nuvem privada (como criar ou expandir uma nuvem privada) diretamente do portal do Azure, o portal CloudSimple abre a página indicada.

No portal do CloudSimple, selecione **Home** no menu do lado para exibir informações resumidas sobre suas nuvens privadas. Os recursos e a capacidade de suas nuvens privadas são mostrados, juntamente com alertas e tarefas que exigem atenção. Para tarefas comuns, clique nos ícones nomeados na parte superior da página.

![Home Page](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>Consentimento ao aplicativo de autorização do serviço CloudSimple

Iniciar o portal de CloudSimple do portal do Azure pela primeira vez requer o seu consentimento para o aplicativo de autorização do serviço CloudSimple.  Selecione **Accept** para conceder permissões solicitadas e acessar o portal de CloudSimple. 

![Consentimento para autorização do serviço CloudSimple - os administradores](media/cloudsimple-azure-consent.png)

Se você tiver privilégios de administrador global, você pode dar consentimento para sua organização.  Selecione **consentir em nome de sua organização**.

![Consentimento para autorização do serviço CloudSimple - administrador global](media/cloudsimple-azure-consent-global-admin.png)

Se suas permissões não permitem acesso ao portal do CloudSimple, entre em contato com o administrador global do seu locatário para conceder as permissões necessárias.  Um administrador global pode consentir em nome de sua organização.

![Consentimento para autorização do serviço CloudSimple – exige que os administradores](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Próximas etapas

* Saiba como [criar uma nuvem privada](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Saiba como [configurar um ambiente de nuvem privada](quickstart-create-private-cloud.md)
