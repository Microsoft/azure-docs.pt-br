---
title: "Tarefas comuns de gerenciamento de serviço de nuvem (clássico) | Microsoft Docs"
description: "Saiba como gerenciar serviços de nuvem no portal clássico do Azure."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 41a30e50-067c-485b-96fd-434a6d057abc
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: adegeo
ms.openlocfilehash: 2ee76dfcb579e53975b1f61a6590f8d78dc0961b
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

Na área **Serviços de Nuvem** do portal clássico do Azure, você pode atualizar uma função de serviço ou uma implantação, promover uma implantação de preparo para produção, vincular recursos ao Serviço de Nuvem para que você possa ver as dependências de recursos e dimensionar os recursos em conjunto, além de excluir um Serviço de Nuvem ou uma implantação.

## <a name="how-to-update-a-cloud-service-role-or-deployment"></a>Como atualizar uma função ou implantação de Serviço de Nuvem
Se você precisar atualizar o código do aplicativo para o seu Serviço de Nuvem, use **Atualizar** no painel, página **Serviços de Nuvem** ou página **Instâncias**. Você pode atualizar única função ou todas as funções. Você precisará carregar um novo pacote de serviços e o arquivo de configuração do serviço.

1. No [Portal Clássico do Azure](https://manage.windowsazure.com/), no painel, na página **Serviços de Nuvem** ou na página **Instâncias**, clique em **Atualizar**.

    ![UpdateDeployment](./media/cloud-services-how-to-manage/CloudServices_UpdateDeployment.png)

2. Em **Rótulo de Implantação**, insira um nome para identificar a implantação (por exemplo, mycloudservice4). Você encontrará o rótulo de implantação em **início rápido** no painel.
3. Em **Pacote**, use **Procurar** para carregar o arquivo do pacote de serviço (.cspkg).
4. Em **Configuração**, use **Procurar** para carregar o arquivo de configuração de serviço (.cscfg).
5. Em **Função**, selecione **Tudo** se desejar atualizar todas as funções no Serviço de Nuvem. Para executar uma única atualização de função, selecione a função que você deseja atualizar. Mesmo que você selecione uma função específica para atualizar, as atualizações no arquivo de configuração do serviço serão aplicadas a todas as funções.
6. Se a atualização alterar o número de funções ou o tamanho de qualquer função, marque a caixa de seleção **Permitir a atualização se os tamanhos ou o número de funções for alterado** para permitir que a atualização continue.

    Lembre-se que, se você alterar o tamanho de uma função (u seja, o tamanho de uma máquina virtual que hospede uma instância de função) ou o número de funções, cada instância de função (máquina virtual) deverá ter outra imagem criada e todos os dados locais serão perdidos.

7. Se alguma das funções de serviço tiver uma instância de função, marque a caixa de seleção **Atualizar mesmo se uma ou mais funções contiverem uma única instância** para permitir que a atualização continue.

    O Azure pode garantir apenas 99,95 por cento de disponibilidade do Serviço de Nuvem durante uma atualização do Serviço de Nuvem se cada função tiver pelo menos duas instâncias de função (máquinas virtuais). Isso permite que uma máquina virtual processe as solicitações do cliente enquanto a outra é atualizada.

8. Clique em **OK** (marca de seleção) para iniciar a atualização do serviço.

## <a name="how-to-swap-deployments-to-promote-a-staged-deployment-to-production"></a>Como permutar implantações para promover a implantação de preparo para produção
Use **Permutar** para promover uma implantação de preparo de um Serviço de Nuvem para produção. Ao optar por implantar uma nova versão do serviço de nuvem, você pode preparar e testar a nova versão em seu ambiente de preparo do serviço em nuvem enquanto os clientes estão usando a versão atual em produção. Quando estiver pronto para promover a nova versão para produção, você poderá usar **Permutar** para alternar as URLs pelas quais as duas implantações são endereçadas.

É possível permutar implantações na página **Serviços de Nuvem** ou no painel.

1. No [portal clássico do Azure](https://manage.windowsazure.com/), clique em **Serviços de Nuvem**.
2. Na lista de serviços de nuvem, clique no serviço de nuvem para selecioná-lo.
3. Clique em **Permutar**.

    O seguinte aviso de confirmação é aberto.

    ![Permutação dos Serviços de Nuvem](./media/cloud-services-how-to-manage/CloudServices_Swap.png)

4. Após verificar as informações da implantação, clique em **Sim** para permutar as implantações.

    A permuta da implantação acontece rapidamente pois a única coisa alterada são os endereços IP virtuais (VIP) para as implantações.

    Para economizar custos de computação, você pode excluir a implantação no ambiente de preparo quando tiver certeza que a nova implantação de produção está funcionado como o esperado.

### <a name="common-questions-about-swapping-deployments"></a>Perguntas comuns sobre troca de implantações

**Quais são os pré-requisitos para a troca de implantações?**

Existem dois pré-requisitos essenciais para uma troca de implantação bem-sucedida:

- Se quiser usar um endereço IP estático para o slot de produção, você também deverá reservar um para o slot de preparo. Caso contrário, a troca falhará.

- Todas as instâncias de suas funções devem estar em execução para que você possa executar a troca. Você pode verificar o status de suas instâncias no Portal Clássico do Azure ou usando [o comando Get-AzureRole no Windows PowerShell](/powershell/module/azure/get-azurerole?view=azuresmps-3.7.0).

Observe que as atualizações do SO convidado e as operações de recuperação de serviço também podem fazer com que as trocas de implantação falhem. Confira [Solucionar problemas de implantação do serviço de nuvem](cloud-services-troubleshoot-deployment-problems.md) para obter mais detalhes.

**Uma troca incorre em tempo de inatividade para meu aplicativo? Como devo lidar com isso?**

Conforme descrito na última seção, uma troca de implantação normalmente é muito rápida, pois é apenas uma alteração de configuração no Azure Load Balancer. Entretanto, em alguns casos, ela pode levar dez segundos ou mais e resultar em falhas de conexão transitórias. Para limitar o impacto sobre os clientes, considere a implementação da [lógica de repetição do cliente](../best-practices-retry-general.md).

## <a name="how-to-link-a-resource-to-a-cloud-service"></a>Como vincular um recurso a um Serviço de Nuvem
Para mostrar as dependências do serviço de nuvem em outros recursos, é possível vincular uma instância de Banco de Dados SQL do Azure ou uma conta de armazenamento ao serviço de nuvem. Você pode vincular e desvincular recursos na página **Recursos vinculados** e monitorar seu uso no painel de serviço de nuvem. Se uma conta de armazenamento vinculada tiver o monitoramento ativado, você poderá monitorar Solicitações Totais no painel de serviços de nuvem.

Use **Vincular** para vincular uma instância nova ou existente do Banco de Dados SQL ou conta de armazenamento ao Serviço de Nuvem. Em seguida, é possível dimensionar um banco de dados junto com a função de Serviço de Nuvem que o está usando na página **Escala**. (Uma conta de armazenamento é dimensionada automaticamente à medida que cresce o uso.) Para saber mais, consulte [Como dimensionar um Serviço de Nuvem e os recursos vinculados](cloud-services-how-to-scale.md).

Você também pode monitorar, gerenciar e dimensionar o banco de dados no nó **Bancos de dados** do portal clássico do Azure.

Neste sentido, a "vinculação" de um recurso não conecta seu aplicativo ao recurso. Se você criar um novo banco de dados usando **Vincular**, precisará adicionar as cadeias de conexão ao código do seu aplicativo e atualizar o Serviço de Nuvem. Você também deverá adicionar cadeias de conexão se seu aplicativo usar recursos em uma conta do armazenamento vinculada.

O procedimento a seguir descreve como vincular uma nova instância de banco de dados SQL, implantada em um novo servidor de banco de dados SQL, a um serviço de nuvem.

### <a name="to-link-a-sql-database-instance-to-a-cloud-service"></a>Para vincular uma instância de banco de dados SQL a um serviço de nuvem
1. No [portal clássico do Azure](http://manage.windowsazure.com/), clique em **Serviços de Nuvem**. Em seguida, clique no nome do Serviço de Nuvem para abrir o painel.
2. Clique em **Recursos Vinculados**.

    A página **Recursos Vinculados** é exibida.

    ![LinkedResourcesPage](./media/cloud-services-how-to-manage/CloudServices_LinkedResourcesPage.png)

3. Clique em **Vincular um Recurso** ou em **Vincular**.

    O assistente de **Vincular Recurso** é iniciado.

    ![Vincular Página1](./media/cloud-services-how-to-manage/CloudServices_LinkedResources_LinkPage1.png)

4. Clique em **Crie um novo recurso** ou em **Vincular um recurso existente**.
5. Escolha o tipo de recurso a ser vinculado. No [portal clássico do Azure](http://manage.windowsazure.com/), clique em **Banco de Dados SQL**. (Somente o Portal Clássico do Azure dá suporte à vinculação de uma conta do armazenamento a um serviço de nuvem).
6. Para concluir a configuração do banco de dados, siga instruções da ajuda para a área **Bancos de Dados SQL** do portal clássico do Azure.

    Então, você pode acompanhar o progresso da operação de vínculo na área de mensagem.

    Quando o vínculo estiver concluído, você poderá monitorar o status do recurso vinculado no painel do Serviço de Nuvem. Para obter informações sobre o dimensionamento de um Banco de Dados SQL vinculado, consulte [Como dimensionar um Serviço de Nuvem e os recursos vinculados](cloud-services-how-to-scale.md).

### <a name="to-unlink-a-linked-resource"></a>Para desvincular um recurso vinculado
1. No [portal clássico do Azure](http://manage.windowsazure.com/), clique em **Serviços de Nuvem**. Em seguida, clique no nome do Serviço de Nuvem para abrir o painel.
2. Clique em **Recursos Vinculados**e selecione o recurso.
3. Clique em **Desvincular**. Em seguida, clique em **Sim** no aviso de confirmação.

    Desvincular o Banco de Dados SQL não afeta o banco de dados ou as conexões do aplicativo ao banco de dados. Ainda é possível monitorar o banco de dados na área **Bancos de Dados SQL** do portal clássico do Azure.

## <a name="how-to-delete-deployments-and-a-cloud-service"></a>Como excluir implantações e um Serviço de Nuvem
Antes de poder excluir um serviço de nuvem, você deve excluir cada implantação existente.

Para economizar custos de computação, você pode excluir a implantação de preparo após verificar que sua implantação de produção está funcionando conforme o esperado. Você é cobrado pelos custos de computação das instâncias de função mesmo que um Serviço de Nuvem não esteja em execução.

Use o procedimento a seguir para excluir uma implantação ou seu serviço de nuvem.

1. No [portal clássico do Azure](http://manage.windowsazure.com/), clique em **Serviços de Nuvem**.
2. Selecione o Serviço de Nuvem e clique em **Excluir**. (Para selecionar um Serviço de Nuvem sem abrir o painel, clique em qualquer lugar exceto no nome na entrada do Serviço de Nuvem.)

    Se você tiver uma implantação em preparo ou em produção, verá um menu de opções semelhante ao menu a seguir na parte inferior da janela. Para poder excluir o Serviço de Nuvem, você deve excluir todas as implantações existentes.

    ![Menu de exclusão](./media/cloud-services-how-to-manage/CloudServices_DeleteMenu.png)

3. Para excluir uma implantação, clique em **Excluir implantação de produção** ou em **Excluir implantação de preparo**. Em seguida, clique em **Sim**no prompt de confirmação.
4. Se você planeja excluir o serviço de nuvem, repita a etapa 3, se necessário, para excluir outras implantações.
5. Para excluir o Serviço de Nuvem, clique em **Excluir o Serviço de Nuvem**. Em seguida, clique em **Sim**no prompt de confirmação.

> [!NOTE]
> Se o monitoramento detalhado estiver configurado para seu serviço de nuvem, o Azure não exclui os dados de monitoramento de sua conta de armazenamento quando você exclui o serviço de nuvem. Você precisará excluir manualmente os dados. Para saber mais sobre onde encontrar as tabelas de métricas, confira "Como acessar dados detalhados de monitoramento fora do portal clássico do Azure" em [Como monitorar serviços de nuvem](cloud-services-how-to-monitor.md).
>
>

## <a name="next-steps"></a>Próximas etapas
* [Configuração geral do serviço de nuvem](cloud-services-how-to-configure.md).
* Saiba como [implantar um serviço de nuvem](cloud-services-how-to-create-deploy.md).
* Configurar um [nome de domínio personalizado](cloud-services-custom-domain-name.md).
* Configurar [certificados SSL](cloud-services-configure-ssl-certificate.md).
