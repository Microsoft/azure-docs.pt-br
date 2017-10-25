---
title: "Tarefas comuns de gerenciamento de serviço de nuvem | Microsoft Docs"
description: "Saiba como gerenciar serviços de nuvem no portal do Azure. Esses exemplos usam o portal do Azure."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: cb218ad9-77d4-4149-83db-71159c00767e
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: adegeo
ms.openlocfilehash: 4650cebe18153e3b10bbec685a66a590348c99e9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-cloud-services"></a>Como gerenciar serviços de nuvem
> [!div class="op_single_selector"]
> * [Portal do Azure](cloud-services-how-to-manage-portal.md)
> * [Portal clássico do Azure](cloud-services-how-to-manage.md)
>
>

Na área **Serviços de Nuvem (clássico)** do Portal do Azure, você pode atualizar uma função de serviço ou uma implantação, promover uma implantação de preparo para produção, vincular recursos ao Serviço de Nuvem para que você possa ver as dependências de recursos e dimensionar os recursos em conjunto, além de excluir um Serviço de Nuvem ou uma implantação.

Obtenha mais informações sobre como dimensionar seu serviço de nuvem [aqui](cloud-services-how-to-scale-portal.md).

## <a name="how-to-update-a-cloud-service-role-or-deployment"></a>Como atualizar uma função ou implantação de Serviço de Nuvem
Se você precisar atualizar o código do aplicativo para o seu serviço de nuvem, use **Atualizar** na folha do serviço de nuvem. Você pode atualizar única função ou todas as funções. Para atualizar, você pode carregar um novo pacote de serviços ou o arquivo de configuração do serviço.

1. No [Portal do Azure][Azure portal], selecione o serviço de nuvem que você deseja atualizar. Esta etapa abrirá a folha da instância do serviço de nuvem.
2. Na folha, clique no botão **Atualizar** .

    ![Botão Atualizar](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Atualize a implantação com um novo arquivo de pacote de serviço (.cspkg) e o arquivo de configuração de serviço (.cscfg).

    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. **Como opção** , atualize o rótulo de implantação e a conta de armazenamento.
5. Se alguma das funções tiver uma instância de função, selecione **Implantar mesmo se uma ou mais funções contiverem uma única instância** para permitir que a atualização continue.

    O Azure pode garantir apenas 99,95 por cento de disponibilidade do Serviço de Nuvem durante uma atualização do Serviço de Nuvem se cada função tiver pelo menos duas instâncias de função (máquinas virtuais). Com duas instâncias de função, uma máquina virtual processa as solicitações do cliente enquanto a outra é atualizada.

6. Marque a opção **Iniciar implantação** para que a atualização seja aplicada após terminar o upload do pacote.
7. Clique em **OK** para iniciar a atualização do serviço.

## <a name="how-to-swap-deployments-to-promote-a-staged-deployment-to-production"></a>Como permutar implantações para promover a implantação de preparo para produção
Ao optar por implantar uma nova versão do serviço de nuvem, prepare e teste a nova versão em seu ambiente de preparo. Use **Permutar** para alternar as URLs pelas quais as duas implantações são endereçadas e para promover uma nova versão para produção.

É possível permutar implantações na página **Serviços de Nuvem** ou no painel.

1. No [Portal do Azure][Azure portal], selecione o serviço de nuvem que você deseja atualizar. Esta etapa abrirá a folha da instância do serviço de nuvem.
2. Na folha, clique o botão **Trocar** .

    ![Permutação dos Serviços de Nuvem](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. O seguinte aviso de confirmação é aberto.

    ![Permutação dos Serviços de Nuvem](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Após verificar as informações da implantação, clique em **OK** para trocar as implantações.

    A permuta da implantação acontece rapidamente pois a única coisa alterada são os endereços IP virtuais (VIP) para as implantações.

    Para economizar custos de computação, você pode excluir a implantação de preparo após verificar que sua implantação de produção está funcionando conforme o esperado.

### <a name="common-questions-about-swapping-deployments"></a>Perguntas comuns sobre troca de implantações

**Quais são os pré-requisitos para a troca de implantações?**

Existem dois pré-requisitos essenciais para uma troca de implantação bem-sucedida:

- Se quiser usar um endereço IP estático para o slot de produção, você também deverá reservar um para o slot de preparo. Caso contrário, a troca falhará.

- Todas as instâncias de suas funções devem estar em execução para que você possa executar a troca. Você pode verificar o status de suas instâncias na folha Visão geral do Portal do Azure. Como alternativa, use o comando [Get-AzureRole](/powershell/module/azure/get-azurerole?view=azuresmps-3.7.0) no Windows PowerShell.

Observe que as atualizações do SO convidado e as operações de recuperação de serviço também podem fazer com que as trocas de implantação falhem. Para saber mais, confira [Solucionar problemas de implantação do serviço de nuvem](cloud-services-troubleshoot-deployment-problems.md).

**Uma troca incorre em tempo de inatividade para meu aplicativo? Como devo lidar com isso?**

Conforme descrito na última seção, uma troca de implantação normalmente é rápida, pois é apenas uma alteração de configuração no Azure Load Balancer. Entretanto, em alguns casos, ela pode levar dez segundos ou mais e resultar em falhas de conexão transitórias. Para limitar o impacto sobre os clientes, considere a implementação da [lógica de repetição do cliente](../best-practices-retry-general.md).

## <a name="how-to-link-a-resource-to-a-cloud-service"></a>Como vincular um recurso a um Serviço de Nuvem
O portal do Azure não vinculada recursos como o portal clássico do Azure atual. Em vez disso, implante recursos adicionais para o mesmo grupo de recursos que está sendo usado pelo Serviço de Nuvem.

## <a name="how-to-delete-deployments-and-a-cloud-service"></a>Como excluir implantações e um Serviço de Nuvem
Antes de poder excluir um serviço de nuvem, você deve excluir cada implantação existente.

Para economizar custos de computação, você pode excluir a implantação de preparo após verificar que sua implantação de produção está funcionando conforme o esperado. Você será cobrado por custos de computação de instâncias de função implantadas que estejam paradas.

Use o procedimento a seguir para excluir uma implantação ou seu serviço de nuvem.

1. No [Portal do Azure][Azure portal], selecione o serviço de nuvem que você deseja excluir. Esta etapa abrirá a folha da instância do serviço de nuvem.
2. Na folha, clique no botão **Excluir** .

    ![Permutação dos Serviços de Nuvem](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. Você pode excluir o serviço de nuvem inteiro marcando **Serviços de nuvem e suas implantações** ou escolhendo **Implantação de produção** ou **Implantação de preparo**.

    ![Permutação dos Serviços de Nuvem](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. Clique no botão **Excluir** na parte inferior.
5. Para excluir o Serviço de Nuvem, clique em **Excluir o Serviço de Nuvem**. Em seguida, clique em **Sim**no prompt de confirmação.

> [!NOTE]
> Quando um serviço de nuvem for excluído, e o monitoramento detalhado estiver configurado, você deverá excluir manualmente os dados de sua conta de armazenamento. Para obter informações sobre onde encontrar as tabelas de métricas, consulte [este](cloud-services-how-to-monitor.md) artigo.


## <a name="how-to-find-more-information-about-failed-deployments"></a>Como localizar mais informações sobre implantações com falha
A folha **Visão geral** tem uma barra de status na parte superior. Quando você clica na barra, uma nova folha é aberta e exibe informações de erro. Se a implantação não contiver erros, a folha de informações ficará em branco.

![Permutação dos Serviços de Nuvem](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Próximas etapas
* [Configuração geral do serviço de nuvem](cloud-services-how-to-configure-portal.md).
* Saiba como [implantar um serviço de nuvem](cloud-services-how-to-create-deploy-portal.md).
* Configurar um [nome de domínio personalizado](cloud-services-custom-domain-name-portal.md).
* Configurar [certificados SSL](cloud-services-configure-ssl-certificate-portal.md).
