---
title: "Recursos, funções e controle de acesso no Azure Application Insights | Microsoft Docs"
description: "Proprietários, colaboradores e leitores de percepções de sua organização."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 49f736a5-67fe-4cc6-b1ef-51b993fb39bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: bwren
ms.openlocfilehash: c979a8bfbeecacc7c0bbc112e02a4b68e874c219
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Recursos, funções e controle de acesso no Application Insights
Você pode controlar quem tem acesso de leitura e atualização a seus dados no [Application Insights do Azure][start] usando o [Controle de acesso baseado em função no Microsoft Azure](../active-directory/role-based-access-control-configure.md).

> [!IMPORTANT]
> Atribua acesso aos usuários no **grupo de recursos ou assinatura** ao qual o recurso do aplicativo pertence, não no próprio recurso. Atribua a função **Colaborador de componente do Application Insights** . Isso garante o controle uniforme de acesso a testes na Web e alertas, juntamente com o recurso do aplicativo. [Saiba mais](#access).
> 
> 

## <a name="resources-groups-and-subscriptions"></a>Recursos, grupos e assinaturas
Primeiro, algumas definições:

* **Recurso** ‒ uma instância de um serviço do Microsoft Azure. O recurso do Application Insights coleta, analisa e exibe os dados de telemetria enviados de seu aplicativo.  Outros tipos de recursos do Azure incluem aplicativos Web, bancos de dados e VMs.
  
    Para ver seus recursos, abra o [portal do Azure][portal], conecte-se e clique em Todos os Recursos. Para encontrar um recurso, digite uma parte de seu nome no campo de filtro.
  
    ![Lista de recursos do Azure](./media/app-insights-resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Grupo de recursos**][group] ‒ cada recurso pertence a um grupo. Um grupo é uma maneira conveniente de gerenciar recursos relacionados, particularmente para controle de acesso. Por exemplo, em um grupo de recursos, você pode colocar um aplicativo Web, um recurso do Application Insights para monitorar o aplicativo e um Recurso de armazenamento para manter os dados exportados.

    ![Escolha Procurar e Grupos de recursos e, em seguida, escolha um grupo](./media/app-insights-resources-roles-access-control/11-group.png)

* [**Assinatura**](https://manage.windowsazure.com) - para usar o Application Insights ou outros recursos do Azure, entre em uma assinatura do Azure. Cada grupo de recursos pertence a uma assinatura do Azure, em que você escolhe o pacote de preços e, se for uma assinatura de organização, escolhe os membros e suas permissões de acesso.
* [**Conta da Microsoft**][account] - o nome de usuário e a senha que você usa para entrar nas assinaturas do Microsoft Azure, no XBox Live, no Outlook.com e em outros serviços da Microsoft.

## <a name="access"></a> Controlar o acesso no grupo de recursos
É importante entender que, além do recurso criado para seu aplicativo, também há recursos ocultos separados para alertas e testes na Web. Eles estão conectados ao mesmo [grupo de recursos](#resource-group) do seu aplicativo. Você também pode colocar outros serviços do Azure nele, como sites ou armazenamento.

![Recursos no Application Insights](./media/app-insights-resources-roles-access-control/00-resources.png)

Portanto, para controlar o acesso a esses recursos, é recomendável:

* Controlar o acesso no nível de **grupo de recursos ou assinatura** .
* Atribuir a função **Colaborador de componente do Application Insights** aos usuários. Isso permite editar testes na Web, alertas e recursos do Application Insights, sem fornecer acesso a qualquer outro serviço no grupo.

## <a name="to-provide-access-to-another-user"></a>Para fornecer acesso a outro usuário
Você deve ter direitos de Proprietário para a assinatura ou o grupo de recursos.

O usuário deve ter uma [conta da Microsoft][account] ou acesso à sua [conta organizacional da Microsoft](../active-directory/sign-up-organization.md). Você pode fornecer acesso a indivíduos e também a grupos de usuários definidos no Azure Active Directory.

#### <a name="navigate-to-the-resource-group"></a>Navegue até o grupo de recursos
Adicione o usuário a ele.

![Na folha de recursos de seu aplicativo, abra Essentials, abra o grupo de recursos e selecione Configurações/Usuários. Clique em Adicionar.](./media/app-insights-resources-roles-access-control/01-add-user.png)

Ou então, você poderia ir para um nível acima e adicionar o usuário à Assinatura.

#### <a name="select-a-role"></a>Selecione uma função
![Selecione uma função para o novo usuário](./media/app-insights-resources-roles-access-control/03-role.png)

| Função | No grupo de recursos |
| --- | --- |
| Proprietário |Pode alterar qualquer item, incluindo o acesso do usuário |
| Colaborador |Pode editar qualquer item, incluindo todos os recursos |
| Colaborador de componente do Application Insights |Pode editar recursos do Application Insights, testes na Web e alertas |
| Leitor |Pode exibir, mas não pode alterar nada |

A 'edição' inclui a criação, a exclusão e a atualização:

* Recursos
* Testes da Web
* Alertas
* Exportação contínua

#### <a name="select-the-user"></a>Selecione o usuário

Se o usuário desejado não estiver no diretório, você poderá convidar qualquer pessoa com uma conta da Microsoft.
(Se ela usa serviços como Outlook.com, OneDrive, Windows Phone ou XBox Live, já tem uma conta da Microsoft.)

## <a name="related-content"></a>Conteúdo relacionado

* [Controle de acesso baseado em função no Azure](../active-directory/role-based-access-control-configure.md)

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../azure-resource-manager/resource-group-overview.md
[portal]: https://portal.azure.com/
[start]: app-insights-overview.md
