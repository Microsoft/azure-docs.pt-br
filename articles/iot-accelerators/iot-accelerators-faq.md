---
title: Perguntas frequentes sobre aceleradores de solução do IoT - Azure | Microsoft Docs
description: Este artigo responde às perguntas frequentes sobre os aceleradores de solução de IoT. Ele inclui links para os repositórios do GitHub.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 1fd2b8461bd66c826dc4890c331b740c4703f896
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96903982"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Perguntas frequentes sobre os aceleradores de solução do IoT

Consulte também as [perguntas frequentes específicas da fábrica conectada](iot-accelerators-faq-cf.md).

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Onde posso encontrar o código-fonte dos aceleradores de solução?

O código-fonte é armazenado nos seguintes repositórios GitHub:

* [Acelerador da solução de Fábrica Conectada](https://github.com/Azure/azure-iot-connected-factory)
* [Acelerador de solução de simulação de dispositivo](https://github.com/Azure/device-simulation-dotnet)

### <a name="where-can-i-find-the-remote-monitoring-and-predictive-maintenance-solution-accelerators"></a>Onde posso encontrar os aceleradores de solução de monitoramento remoto e de manutenção preditiva?

A partir de 10 de dezembro de 2020, os aceleradores remotos de monitoramento e de manutenção preditiva foram removidos do site do [Azure IOT Solution Accelerators](https://www.azureiotsolutions.com/Accelerators) e não estão mais disponíveis para novas implantações. Os repositórios do GitHub para ambos os aceleradores foram arquivados. O código ainda está disponível para qualquer pessoa acessar, mas os repositórios não estão realizando nenhuma contribuição nova.

### <a name="what-happens-to-my-existing-remote-monitoring-and-predictive-maintenance-deployments"></a>O que acontece com o monitoramento remoto e as implantações de manutenção preditiva existentes?

As implantações existentes não são afetadas pela remoção dos aceleradores de solução de monitoramento remoto e de manutenção preditiva e continuarão funcionando. Os repositórios bifurcados também não são afetados. Os repositórios mestres no GitHub foram arquivados.

### <a name="how-do-i-deploy-device-simulation-solution-accelerator"></a>Como fazer implantar o acelerador de solução de simulação de dispositivo?

Para implantar o acelerador de solução de simulação de dispositivo, consulte o repositório GitHub de [simulação de dispositivo](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) .

### <a name="where-can-i-find-information-about-the-removed-solution-accelerators"></a>Onde posso encontrar informações sobre os aceleradores de solução removidos?

Consulte as seguintes páginas no site de versões anteriores:

* [Monitoramento remoto](/previous-versions/azure/iot-accelerators/about-iot-accelerators)
* [Manutenção preditiva](/previous-versions/azure/iot-accelerators/about-iot-accelerators)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Quais SDKs posso usar para desenvolver clientes de dispositivos para os aceleradores de solução?

É possível localizar links para as diferentes SDKs do dispositivo IoT de linguagem (C, .NET, Java, Node.js, Python) nos repositórios do GitHub das [SDKs de IoT do Microsoft Azure](https://github.com/Azure/azure-iot-sdks).

Se você estiver usando o dispositivo DevKit, poderá encontrar recursos e exemplos no repositório GitHub do [SDK de DevKit do IoT](https://github.com/Microsoft/devkit-sdk).

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>Sou um administrador de serviços e gostaria de alterar o mapeamento de diretório entre minha assinatura e um locatário específico do Azure AD. Como posso concluir esta tarefa?

Consulte [Como adicionar uma assinatura existente ao seu diretório do Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Quero alterar um Administrador de Serviços ou um Coadministrador quando o logon for feito com uma conta organizacional

Confira o artigo de suporte [Alterando um Administrador de Serviços e um Coadministrador quando o logon for feito com uma conta organizacional](https://azure.microsoft.com/support/changing-service-admin-and-co-admin).

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Por que vejo este erro? "Sua conta não tem as permissões adequadas para criar uma solução. Verifique com o administrador da conta ou tente com uma conta diferente."

Observe o seguinte diagrama para obter orientação:

![Fluxograma de permissões](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> Se você continuar recebendo o erro após validar que é um administrador global no locatário do Azure AD e um coadministrador na assinatura, solicite ao administrador da conta que remova o usuário e reatribua as permissões necessárias nesta ordem. Primeiro, adicione o usuário como um administrador global e adicione o usuário como um coadministrador na assinatura do Azure. Se o problema persistir, entre em contato com [Ajuda e Suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Por que estou vendo este erro se eu tenho uma assinatura do Azure? “Uma assinatura do Azure é necessária para criar soluções pré-configuradas. Você pode criar uma conta de avaliação gratuita em apenas alguns minutos.”

Se você tiver certeza de que tem uma assinatura do Azure, valide o mapeamento de locatário da assinatura e verifique se o locatário correto está selecionado no menu suspenso. Se você validou que o locatário está correto, siga o diagrama anterior e valide o mapeamento da assinatura e esse locatário do Azure AD.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>Qual é a diferença entre excluir um grupo de recursos no portal do Azure e clicar em excluir em um Solution Accelerator no azureiotsolutions.com?

* Se você excluir o Solution Accelerator no [azureiotsolutions.com](https://www.azureiotsolutions.com/), você excluirá todos os recursos que foram implantados quando criou o Solution Accelerator. Se você adicionou mais recursos ao grupo de recursos, esses recursos também serão excluídos.
* Se você excluir o grupo de recursos no [Portal do Azure](https://portal.azure.com), somente os recursos nesse grupo de recursos serão excluídos. Você também precisa excluir o aplicativo do Azure Active Directory associado ao acelerador de solução.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Posso continuar meus investimentos existentes nos aceleradores de solução do Azure IoT?

Sim. As soluções atualmente existentes continuarão funcionando na assinatura do Azure e o código-fonte ficará disponível no GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Quantas instâncias do Hub IoT posso provisionar em uma assinatura?

Por padrão, você pode provisionar [10 Hubs IoT por assinatura](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-limits). Você pode criar um [tíquete de suporte do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar esse limite. Como resultado, uma vez que cada acelerador de solução provisiona um novo Hub IoT, você só poderá provisionar até 10 aceleradores de solução em uma determinada assinatura.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Quantas instâncias do Azure Cosmos DB posso provisionar em uma assinatura?

Cinquenta. Você pode criar um [tíquete de suporte do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar esse limite, mas, por padrão, você poderá apenas provisionar 50 instâncias do Cosmos DB por assinatura.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Posso criar um acelerador de solução se possuo o Microsoft Azure para DreamSpark?

> [!NOTE]
> O Microsoft Azure para DreamSpark agora é conhecido como Microsoft Imagine para alunos.

Atualmente, você não pode criar um acelerador de solução com uma conta do [Microsoft Azure para DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/). No entanto, você pode criar uma [conta de avaliação gratuita do Azure](https://azure.microsoft.com/free/) em apenas alguns minutos que permite a você criar um acelerador de solução.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Como fazer para excluir um locatário do Azure AD?

Veja a postagem no blog de Eric Golpe, [Passo a passo da exclusão de um locatário do Azure AD](/archive/blogs/ericgolpe/walkthrough-of-deleting-an-azure-ad-tenant).

### <a name="next-steps"></a>Próximas etapas

Você também pode explorar alguns dos outros recursos dos aceleradores de solução de IoT:

* [Implantar o Acelerador de solução de fábrica Conectada](quickstart-connected-factory-deploy.md)
* [Segurança IoT desde o início](../iot-fundamentals/iot-security-ground-up.md)