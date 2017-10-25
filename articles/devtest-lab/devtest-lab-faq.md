---
title: Perguntas frequentes sobre o Azure DevTest Labs | Microsoft Docs
description: Encontre respostas para perguntas comuns sobre o Azure DevTest Labs.
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: afe83109-b89f-4f18-bddd-b8b4a30f11b4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: tarcher
ms.openlocfilehash: 1f261f97bbd9233d47eadc7e902e00ee87af9e34
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-devtest-labs-faq"></a>Perguntas frequentes sobre o Azure DevTest Labs
Obtenha respostas para algumas das perguntas mais comuns sobre o Azure DevTest Labs.

**Geral**
## <a name="what-if-my-question-isnt-answered-here"></a>E se dúvida não foi respondida aqui?
Caso sua pergunta não esteja listada aqui, fale conosco e nós lhe ajudaremos a encontrar uma resposta.

* Poste uma pergunta no final destas Perguntas Frequentes. Entre em contato com a equipe de Cache do Azure e outros membros da comunidade a respeito deste artigo.
* Para alcançar um público maior, poste uma pergunta no [Fórum do MSDN do Azure DevTest Labs](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs). Envolva-se com a equipe do Azure DevTest Labs e outros membros da comunidade.
* Para solicitações de recurso, envie solicitações e ideias para o [User Voice do Azure DevTest Labs](https://feedback.azure.com/forums/320373-azure-devtest-labs).

## <a name="why-should-i-use-azure-devtest-labs"></a>Por que devo usar o Azure DevTest Labs?
O Azure DevTest Labs pode economizar dinheiro e tempo da equipe. Os desenvolvedores podem criar seus próprios ambientes usando várias bases diferentes. Eles também podem usar artefatos para implantar e configurar aplicativos rapidamente. Usando fórmulas e imagens personalizadas, você pode salvar VMs (máquinas virtuais) como modelos e reproduzi-los facilmente entre a equipe. O DevTest Labs também oferece várias políticas configuráveis que permitem aos administradores do laboratório reduzir o desperdício e gerenciar os ambientes de uma equipe. Essas políticas incluem desligamento automático, limite de custo, máximo de VMs por usuário e tamanhos máximos de VM. Para obter uma explicação mais detalhada sobre o DevTest Labs, veja a [visão geral](devtest-lab-overview.md) ou o [vídeo introdutório](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs).

## <a name="what-does-worry-free-self-service-mean"></a>O que significa "autoatendimento sem preocupações"?
Autoatendimento sem preocupações significa que os desenvolvedores e testadores criam seus próprios ambientes conforme necessário. Os administradores têm a segurança de saber que o DevTest Labs pode ajudar a minimizar o desperdício e controlar os custos. Os administradores podem especificar quais tamanhos de VM são permitidos, o número máximo de VMs e quando as VMs são inicializadas e desligadas. O DevTest Labs também torna mais fácil monitorar os custos e definir alertas, para ajudar você a saber sempre como os recursos do laboratório estão sendo usados.

## <a name="how-can-i-use-devtest-labs"></a>Como posso usar o Azure DevTest Labs?
O DevTest Labs será útil sempre que você precisar de ambientes de desenvolvimento ou teste e desejar reproduzi-los rapidamente ou gerenciá-los com as políticas de redução de custos.

Aqui estão alguns cenários em que nossos clientes usam o DevTest Labs:

* Gerenciar ambientes de desenvolvimento e teste em um único local. Usar políticas para reduzir custos e criar imagens personalizadas para compartilhar builds entre a equipe.
* Desenvolver um aplicativo usando imagens personalizadas para salvar o estado do disco em todas as fases de desenvolvimento.
* Acompanhar o custo em relação ao andamento.
* Criar ambientes de teste em massa para testes de garantia de qualidade.
* Usar fórmulas e artefatos para configurar e reproduzir um aplicativo em ambientes diversos rapidamente.
* Distribuir VMs para hackathons (trabalho colaborativo de desenvolvimento ou teste) e desprovisioná-las com facilidade quando o evento termina.

## <a name="how-am-i-billed-for-devtest-labs"></a>Como eu sou cobrado pelo DevTest Labs?
O DevTest Labs é um serviço gratuito. Criar e configurar políticas, modelos e artefatos no DevTest Labs é gratuito. Você paga apenas pelos recursos do Azure usados em seus laboratórios, assim como VMs, contas de armazenamento e redes virtuais. Para obter mais informações sobre o custo de recursos de laboratório, veja [preço do Azure DevTest Labs](https://azure.microsoft.com/pricing/details/devtest-lab/).


**Segurança**
## <a name="what-are-the-different-security-levels-in-devtest-labs"></a>Quais são os diferentes níveis de segurança no DevTest Labs?
O acesso de segurança é determinado pelo [RBAC (controle de acesso baseado em função)](../active-directory/role-based-access-built-in-roles.md). Para entender como o acesso funciona, é importante entender as diferenças entre um escopo, uma permissão e uma função, conforme definido pelo RBAC.

* **Permissão**: uma permissão é um acesso definido para uma ação específica. Por exemplo, uma permissão pode ser de acesso de leitura a todas as VMs.
* **Função**: uma função é um conjunto de permissões que podem ser agrupadas e atribuídas a um usuário. Por exemplo, um usuário com uma função de proprietário da assinatura tem acesso a todos os recursos dentro de uma assinatura.
* **Escopo**: um escopo é um nível dentro da hierarquia de um recurso do Azure. Por exemplo, um escopo pode ser um grupo de recursos, um único laboratório ou toda a assinatura.

Dentro do escopo do DevTest Labs, há dois tipos de funções para definir permissões de usuário:

* **Proprietário de laboratório**: um proprietário de laboratório tem acesso a qualquer recurso no laboratório. Um proprietário de laboratório pode modificar políticas, ler e gravar para quaisquer VMs, alterar a rede virtual e assim por diante.
* **Usuário de laboratório**: um usuário de laboratório pode exibir todos os recursos de laboratório, tais como VMs, políticas e redes virtuais. Mas um usuário de laboratório não é capaz de modificar políticas nem nenhuma máquina virtual que tenha sido criada por outros usuários. 

Você também pode criar funções personalizadas no DevTest Labs. Para saber como criar funções personalizadas no DevTest Labs, consulte [Conceder permissões de usuário para políticas específicas do laboratório](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Já que os escopos são hierárquicos, quando um usuário tem permissões em um determinado escopo, o usuário recebe essas permissões automaticamente em cada escopo de nível inferior que está no escopo. Por exemplo, se a função de proprietário da assinatura é atribuída a um usuário, ele tem acesso a todos os recursos em uma assinatura. Esses recursos incluem todas as VMs, todas as redes virtuais e todos os laboratórios. Um proprietário de assinatura herda automaticamente a função de proprietário de laboratório. No entanto, o oposto não é verdadeiro. O proprietário de um laboratório tem acesso a um laboratório, que é um escopo menor que o nível de assinatura. Portanto, um proprietário de laboratório não pode consultar VMs, redes virtuais ou outros recursos que estão fora do laboratório.

## <a name="how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task"></a>Como criar uma função para permitir que os usuários executem uma tarefa específica?
Para um artigo abrangente sobre como criar funções personalizadas e atribuir permissões a uma função, consulte [Conceder permissões de usuário para políticas específicas do laboratório](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). Eis um exemplo de um script que cria a função *usuário Advanced do DevTest Labs*, que tem permissão para inicializar e desligar todas as VMs no laboratório:

    $policyRoleDef = Get-AzureRmRoleDefinition "DevTest Labs User"
    $policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "DevTest Labs Advanced User"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
    $policyRoleDef = New-AzureRmRoleDefinition -Role $policyRoleDef  


**Integração e automação de CI/CD**
## <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>O DevTest Labs se integra à minha cadeia de ferramentas de CI/CD?
Se você está usando o Visual Studio Team Services, você pode usar uma [extensão Tarefas do Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) para automatizar seu pipeline de lançamento no DevTest Labs. Algumas das tarefas que você pode fazer com essa extensão incluem:

* Criar e implantar uma VM automaticamente. Você também pode configurar a VM com a versão mais recente usando tarefas do Azure File Copy ou do PowerShell Team Services.
* Capture o estado de uma VM automaticamente após os testes para reproduzir um bug na mesma VM a fim de investigar melhor.
* Exclua a VM no final do pipeline de lançamento quando ela não for mais necessária.

As postagens de blog a seguir oferecem diretrizes e informações sobre como usar a extensão do Team Services:

* [DevTest Labs e a extensão do Visual Studio Team Services](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/)
* [Implantar uma nova VM em um laboratório do DevTest Labs existente do Team Services](http://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
* [Usando o gerenciamento de versão do Team Services para implantações contínuas no DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

Para outras cadeias de ferramentas de CI (integração contínua)/CD (entrega contínua), você pode obter os mesmos cenários implantando [modelos do Azure Resource Manager](https://aka.ms/dtlquickstarttemplate) usando [cmdlets do Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) e [SDKs do .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). Você também pode usar [APIs REST para DevTest Labs](http://aka.ms/dtlrestapis) a fim de integrar sua cadeia de ferramentas.  


**Máquinas virtuais**
## <a name="why-cant-i-see-vms-on-the-virtual-machines-blade-that-i-see-in-devtest-labs"></a>Por que não é possível ver as VMs na folha de máquinas virtuais que eu vejo no DevTest Labs?
Quando você cria uma VM no DevTest Labs, você tem permissão para acessar essa VM. Você pode exibir a VM na folha de laboratórios e na folha **Máquinas Virtuais**. Os usuários atribuídos à função de usuário de laboratório do DevTest Labs podem ver todas as VMs criadas no laboratório na folha **Todas as Máquinas Virtuais** do laboratório. No entanto, usuários na função de usuário de laboratório do DevTest Labs não recebem automaticamente acesso de leitura para recursos de VM criados por outros usuários. Portanto, essas VMs não são exibidas na folha **Máquinas Virtuais**.

## <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>Qual é a diferença entre uma imagem personalizada e uma fórmula?
Uma imagem personalizada é um VHD (disco rígido virtual). Uma fórmula é uma imagem que você pode definir com configurações adicionais e, em seguida, salvar e reproduzir. Uma imagem personalizada poderá ser preferível se você quer criar rapidamente vários ambientes usando a mesma imagem básica e imutável. Uma fórmula pode ser melhor se você deseja reproduzir a configuração da VM de bits mais recentes, como parte de uma sub-rede ou rede virtual, ou como uma VM de um tamanho específico. Para uma explicação mais detalhada, veja [Comparando imagens personalizadas e fórmulas no DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).

## <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Como criar várias VMs do mesmo modelo simultaneamente?
Você tem duas opções para criar simultaneamente várias VMs do mesmo modelo:
* Você pode usar a [extensão de Tarefas do Visual Studio Team Services](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks). 
* Você pode [gerar um modelo do Azure Resource Manager](devtest-lab-add-vm.md#save-azure-resource-manager-template) durante a criação de uma VM e [implantar o modelo do Azure Resource Manager do Windows PowerShell](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Como mover minhas VMs do Azure existentes para meu laboratório do DevTest Labs?
Para copiar suas VMs existentes para o DevTest Labs:

1. Copie o arquivo VHD da VM existente usando um [script do Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyVHDFromVMToLab.ps1).
2. [Crie a imagem personalizada](devtest-lab-create-template.md) em seu laboratório do DevTest Labs.
3. Crie uma VM no laboratório com sua imagem personalizada.

## <a name="can-i-attach-multiple-disks-to-my-vms"></a>Posso anexar vários discos às minhas VMs?
Sim, você pode anexar vários discos a suas VMs.  

## <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Se eu quiser usar uma imagem do sistema operacional Windows para o meu teste, preciso comprar uma assinatura do MSDN?
Para usar imagens do sistema operacional cliente Windows (Windows 7 ou posterior) para desenvolvimento ou teste no Azure, realize uma das seguintes ações:

- [Comprar uma assinatura do MSDN](https://www.visualstudio.com/products/how-to-buy-vs).
- Se você tiver um Contrato Enterprise, crie uma assinatura do Azure com a [oferta de Desenvolvimento/Teste Enterprise](https://azure.microsoft.com/en-us/offers/ms-azr-0148p).

Para obter mais informações sobre os créditos Azure para cada oferta do MSDN, consulte [Crédito Azure mensal para assinantes do Visual Studio](https://azure.microsoft.com/en-us/pricing/member-offers/msdn-benefits-details/).

## <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Como automatizar o processo de carregamento de arquivos VHD para criar imagens personalizadas?
Para automatizar o upload de arquivos VHD para criar imagens personalizadas, você tem duas opções:

* Usar o [AzCopy](../storage/common/storage-use-azcopy.md#upload-blobs-to-blob-storage) para copiar ou carregar arquivos VHD na conta de armazenamento associada ao laboratório.
* Usar o [Gerenciador de Armazenamento do Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md). O Gerenciador de Armazenamento é um aplicativo autônomo que é executado no Windows, no OS X e no Linux.   

Para localizar a conta de armazenamento de destino associada a seu laboratório:

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **Grupos de Recursos** no menu esquerdo.
3. Localize e selecione o grupo de recursos associado ao seu laboratório.
4. Em **Visão geral**, selecione uma das contas de armazenamento.
5. Selecione **Blobs**.
6. Procure os carregamentos na lista. Se não houver nenhuma, retorne para a etapa 4 e tente escolher outra conta de armazenamento.
7. Use a **URL** como o destino no comando AzCopy.

## <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Como posso automatizar o processo de exclusão de todas as VMs em meu laboratório?
Você pode excluir as VMs do seu laboratório no Portal do Azure. Você também pode excluir todas as VMs no laboratório usando um script do PowerShell. No exemplo a seguir, no comentário **Valores a serem alterados**, modifique os valores de parâmetro. Você pode recuperar os valores `subscriptionId`, `labResourceGroup` e `labName` do painel do laboratório no Portal do Azure.

    # Delete all the VMs in a lab.

    # Values to change:
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource group here>"
    $labName = "<Enter lab name here>"

    # Sign in to your Azure account.
    Login-AzureRmAccount

    # Select the Azure subscription that has the lab. This step is optional
    # if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    # Get the lab that has the VMs that you want to delete.
    $lab = Get-AzureRmResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

    # Get the VMs from that lab.
    $labVMs = Get-AzureRmResource | Where-Object {
              $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
              $_.ResourceName -like "$($lab.ResourceName)/*"}

    # Delete the VMs.
    foreach($labVM in $labVMs)
    {
        Remove-AzureRmResource -ResourceId $labVM.ResourceId -Force
    }

**Artefatos**
## <a name="what-are-artifacts"></a>O que são os artefatos?
Artefatos são elementos personalizáveis que podem ser usados para implantar os bits mais recentes ou as ferramentas de desenvolvimento em uma VM. Anexe artefatos à VM quando criá-la. Depois que a VM é provisionada, os artefatos a implantam e configuram. Vários artefatos preexistentes estão disponíveis no nosso [repositório GitHub público](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts). Você também pode [criar seus próprios artefatos](devtest-lab-artifact-author.md).


**Configuração do laboratório**
## <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Como criar um laboratório de um modelo do Resource Manager?
Nós oferecemos um [repositório GitHub dos modelos do laboratório do Azure Resource Manager](https://aka.ms/dtlquickstarttemplate) que você pode implantar como são ou modificar para criar modelos personalizados para seus laboratórios. Cada modelo tem um link para implantar o laboratório no estado em que se encontra na sua própria assinatura do Azure. Ou então você pode personalizar o modelo e [implantar usando o PowerShell ou a CLI do Azure](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups"></a>Por que as minhas VMs são criadas em grupos de recursos diferentes com nomes arbitrários? Posso renomear ou modificar esses grupos de recursos?
Grupos de recursos são criados dessa maneira para que o DevTest Labs possa gerenciar permissões de usuário e o acesso a VMs. Embora você possa mover uma VM para outro grupo de recursos e usar o nome que você deseja, é recomendável que você não faça alterações aos grupos de recursos. Estamos trabalhando na melhoria dessa experiência para proporcionar mais flexibilidade.   

## <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Quantos laboratórios posso criar na mesma assinatura?
Não há um limite específico quanto ao número de laboratórios que podem ser criados por assinatura. No entanto, a quantidade de recursos usados por assinatura é limitada. Você pode ler sobre os [limites e cotas para assinaturas do Azure](../azure-subscription-service-limits.md) e sobre [como aumentar esses limites](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

## <a name="how-many-vms-can-i-create-per-lab"></a>Quantas VMs posso criar por laboratório?
Não há nenhum limite específico quanto ao número de VMs que podem ser criadas por laboratório. No entanto, os recursos usados (núcleos de VMs, endereços IP públicos, etc.) são limitados por assinatura. Você pode ler sobre os [limites e cotas para assinaturas do Azure](../azure-subscription-service-limits.md) e sobre [como aumentar esses limites](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

## <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Como faço para compartilhar um link direto para o meu laboratório?

1. No Portal do Azure, vá para o laboratório.
2. Copie a URL do laboratório do navegador e compartilhe-a com os usuários do laboratório.

> [!NOTE]
> Se um usuário do laboratório for um usuário externo que tenha uma [conta da Microsoft](#what-is-a-microsoft-account), mas que não seja um membro da instância do Active Directory da sua organização, o usuário poderá ver uma mensagem de erro ao tentar acessar o link compartilhado. Se um usuário externo vê uma mensagem de erro, peça ao usuário para primeiro selecionar o nome dele no canto superior direito do Portal do Azure. Em seguida, na seção **Diretório** do menu, o usuário pode selecionar o diretório em que o laboratório existe.
>
>

## <a name="what-is-a-microsoft-account"></a>O que é uma conta da Microsoft?
Uma conta da Microsoft é uma conta que você utiliza para quase tudo o que faz com os serviços e dispositivos da Microsoft. É um endereço de email e senha que você usa para entrar no Skype, Outlook.com, OneDrive, Windows phone e Xbox Live. Uma conta única significa que seus arquivos, fotos, contatos e configurações, podem seguir você em qualquer dispositivo.

> [!NOTE]
> A conta da Microsoft costumava ser chamada de *Windows Live ID*.
>
>


**Solução de problemas**
## <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Meu artefato falhou durante a criação da VM. Como solucionar isso?
Para saber como obter logs relacionados ao artefato com falha, veja [Como diagnosticar falhas de artefato no DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md).

## <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Por que a minha rede virtual existente não está salvando corretamente?
Uma das possibilidades é que o nome da rede virtual contém pontos. Nesse caso, tente remover os pontos ou substituí-los por hifens. Em seguida, tente novamente salvar a rede virtual.

## <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Por que recebo um erro "Recurso pai não encontrado" ao provisionar uma VM do PowerShell?
Quando um recurso é pai de outro recurso, o recurso pai deve existir antes de você criar o recurso filho. Se o recurso pai não existir, você verá uma mensagem **ParentResourceNotFound**. Se você não especificar uma dependência no recurso pai, o recurso filho poderá obter implantado antes do pai.

As VMs são recursos filhos em um laboratório em um grupo de recursos. Quando você usa modelos do Resource Manager para implantar VMs pelo uso do PowerShell, o nome do grupo de recursos fornecido no script do PowerShell deve ser o nome do grupo de recursos do laboratório. Para obter mais informações, consulte [Solução de erros comuns de implantação do Azure](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-common-deployment-errors#parentresourcenotfound).

## <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Onde posso encontrar mais informações sobre erros se há falha na implantação de uma VM?
Os erros de implantação de VM são capturados nos logs de atividades. É possível encontrar os logs de atividades de VMs do laboratório em **Logs de auditoria** ou **Diagnóstico de máquina virtual** no menu de recursos da folha de VMs do laboratório (a folha é exibida após a seleção da VM na lista **Minhas máquinas virtuais**).

Às vezes, o erro de implantação ocorre antes do início da implantação da VM. Um exemplo é quando o limite de assinatura para um recurso que foi criado com a VM é excedido. Nesse caso, os detalhes do erro são capturados nos logs de atividades de nível de laboratório. Os logs de atividades estão localizados na parte inferior das configurações **Políticas e configurações**. Para obter mais informações sobre como usar os logs de atividades no Azure, consulte [Exibir logs de atividades para auditar ações em recursos](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-audit).

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
