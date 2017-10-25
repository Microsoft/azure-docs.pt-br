---
title: "Criar ambientes de várias VMs e recursos de PaaS com modelos do Azure Resource Manager | Microsoft Docs"
description: "Saiba como criar ambientes de várias VMs e recursos de PaaS no Azure DevTest Labs com base em um modelo do Azure Resource Manager"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: tarcher
ms.openlocfilehash: 4e1aae6c041e4572e7e2281203f969e7649e1480
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Criar ambientes de várias VMs e recursos de PaaS com modelos do Azure Resource Manager

O [portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) permite que você [crie e adicione facilmente uma VM a um laboratório](https://docs.microsoft.com/en-us/azure/devtest-lab/devtest-lab-add-vm). Isso funciona bem para criar uma VM por vez. No entanto, se o ambiente contiver várias VMs, cada VM terá de ser criada individualmente. Para cenários como um aplicativo Web com várias camadas ou um farm do SharePoint, é necessário um mecanismo para permitir a criação de várias VMs em uma única etapa. Usando os modelos do Azure Resource Manager, agora você pode definir a infraestrutura e a configuração de sua solução do Azure e implantar repetidamente várias VMs em um estado consistente. Esse recurso oferece os seguintes benefícios:

- Os modelos do Azure Resource Manager são carregados diretamente a partir de seu repositório de controle de origem (GitHub ou Git do Team Services).
- Uma vez configurados, os usuários podem criar um ambiente selecionando um modelo do Azure Resource Manager no portal do Azure como o que podem fazer com outros tipos de [VM básica](./devtest-lab-comparing-vm-base-image-types.md).
- Os recursos de PaaS do Azure podem ser provisionados em um ambiente usando um modelo do Azure Resource Manager, além das VMs de IaaS.
- O custo dos ambientes pode ser controlado no laboratório, além das VMs individuais criadas por outros tipos de bases.
- Recursos de PaaS são criados e aparecerão no acompanhamento de custo; no entanto, desligamento automático VM não se aplica a recursos de PaaS.
- Os usuários têm o mesmo controle de política da VM para os ambientes que para as VMs de laboratório único.

Saiba mais sobre os muitos [benefícios de usar os modelos do Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#the-benefits-of-using-resource-manager) para implantar, atualizar ou excluir todos os seus recursos de laboratório em uma única operação.

> [!NOTE]
> Quando você usa um modelo do Resource Manager como base para criar mais VMs do laboratório, há algumas diferenças a ter em mente se você estiver criando várias VMs ou VMs únicas. Usar o modelo do Azure Resource Manager de uma máquina virtual explica essas diferenças mais detalhadamente.
>
>

## <a name="configure-azure-resource-manager-template-repositories"></a>Configurar os repositórios de modelo do Azure Resource Manager

Como uma das práticas recomendadas com a infraestrutura como código e a configuração como código, os modelos de ambiente devem ser gerenciados no controle de origem. O Azure DevTest Labs segue essa prática e carrega todos os modelos do Azure Resource Manager diretamente a partir de seus repositórios GitHub ou VSTS Git. Como resultado, os modelos do Resource Manager podem ser usados em todo o ciclo de versão de inteiro, do ambiente de teste ao ambiente de produção.

Há algumas regras a seguir para organizar seus modelos do Azure Resource Manager em um repositório:

- O arquivo de modelo mestre deve ser nomeado `azuredeploy.json`. 

    ![Principais arquivos de modelo do Azure Resource Manager](./media/devtest-lab-create-environment-from-arm/master-template.png)

- Se você quiser usar os valores do parâmetro definidos em um arquivo de parâmetro, o arquivo de parâmetro deve ser nomeado `azuredeploy.parameters.json`.
- Você pode usar os parâmetros `_artifactsLocation` e `_artifactsLocationSasToken` para construir o valor do URI parametersLink, permitindo ao DevTest Labs gerenciar automaticamente os modelos aninhados. Consulte [Como o Azure DevTest Labs facilita as implantações de modelo do Resource Manager para ambientes de teste](https://blogs.msdn.microsoft.com/devtestlab/2017/05/23/how-azure-devtest-labs-makes-nested-arm-template-deployments-easier-for-testing-environments/) para obter mais informações.
- Os metadados podem ser definidos para especificar o nome de exibição do modelo e a descrição. Esses metadados devem estar em um arquivo denominado `metadata.json`. O arquivo de metadados de exemplo a seguir ilustra como especificar o nome de exibição e a descrição: 

```json
{
 
"itemDisplayName": "<your template name>",
 
"description": "<description of the template>"
 
}
```

As etapas a seguir irão orientá-lo na adição de um repositório ao seu laboratório usando o portal do Azure. 

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **Mais Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
1. Na lista de laboratórios, selecione o laboratório desejado.   
1. Na folha do laboratório, selecione **Configuração e Políticas**.

    ![Configuração e políticas](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. Na lista de configurações **Configuração e Políticas**, selecione **Repositórios**. A folha **Repositórios** lista os repositórios que foram adicionados ao laboratório. Um repositório denominado `Public Repo` é gerado automaticamente para todos os laboratórios e conecta o [repositório DevTest Labs GitHub](https://github.com/Azure/azure-devtestlab) que contém vários artefatos da VM para usar.

    ![Repositório público](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. Selecione **Adicionar+** para adicionar seu repositório de modelos do Azure Resource Manager.
1. Quando a segunda folha **Repositórios** abrir, insira as informações necessárias da seguinte maneira:
    - **Nome** - insira o nome do repositório usado no laboratório.
    - **URL de clone do Git** - Insira a URL de clone HTTPS do GIT a partir do GitHub ou do Visual Studio Team Services.  
    - **Ramificação** - insira o nome da ramificação para acessar suas definições de modelo do Azure Resource Manager. 
    - **Token de acesso pessoal** - o token de acesso pessoal é usado para acessar o repositório com segurança. Para obter o token no Visual Studio Team Services, selecione **&lt;SeuNome > > Meu perfil > Segurança > Token de acesso público**. Para obter o token no GitHub, selecione seu avatar, em seguida, selecione **Configurações > Token de acesso público**. 
    - **Caminhos da pasta** - usando um dos dois campos de entrada, digite o caminho da pasta que começa com uma barra invertida - / - e é relativo ao URI de clone do Git para suas definições de artefato (primeiro campo de entrada) ou suas definições de modelo do Azure Resource Manager.   
    
        ![Repositório público](./media/devtest-lab-create-environment-from-arm/repo-values.png)

1. Assim que todos os campos obrigatórios forem inseridos e passarem na validação, selecione **Salvar**.

A próxima seção orientará a criação de ambientes a partir de um modelo do Azure Resource Manager.

## <a name="create-an-environment-from-an-azure-resource-manager-template"></a>Criar um ambiente a partir de um modelo do Azure Resource Manager

Assim que um repositório de modelos do Azure Resource Manager for configurado no laboratório, os usuários do laboratório poderão criar um ambiente usando o portal do Azure com as seguintes etapas:

1. Entre no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **Mais Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
1. Na lista de laboratórios, selecione o laboratório desejado.   
1. Na folha do laboratório, selecione **Adicionar+**.
1. A folha **Escolher uma base** exibe as imagens básicas que você pode usar com os modelos do Azure Resource Manager listados primeiro. Selecione o modelo desejado do Azure Resource Manager.

    ![Escolher uma base](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)
  
1. Na folha **Adicionar**, insira o valor **Nome do ambiente**. O nome do ambiente é o exibido para os usuários no laboratório. Os campos de entrada restantes são definidos no modelo do Azure Resource Manager. Se os valores padrão forem definidos no modelo ou o arquivo `azuredeploy.parameter.json` estiver presente, os valores padrão serão exibidos nesses campos de entrada. Para os parâmetros do tipo *proteger a cadeia de caracteres*, você pode usar os segredos armazenados no [repositório secreto pessoal](https://azure.microsoft.com/en-us/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store) do laboratório.

    ![Adicionar folha](./media/devtest-lab-create-environment-from-arm/add.png)

    > [!NOTE]
    > Há diversos valores de parâmetro que, mesmo se especificados, são exibidos como valores vazios. Portanto, se os usuários atribuírem esses valores aos parâmetros em um modelo do Azure Resource Manager, o DevTest Labs não exibirá os valores. Em vez disso, mostrará campos de entrada em branco onde os usuários do laboratório precisarão digitar um valor ao criar o ambiente.
    > 
    > - GEN-UNIQUE
    > - GEN-UNIQUE-[N]
    > - GEN-SSH-PUB-KEY
    > - GEN-PASSWORD 
 
1. Selecione **Adicionar** para criar o ambiente. O ambiente inicia o provisionamento imediatamente com a exibição do status na lista **Minhas máquinas virtuais**. Um novo grupo de recursos é criado automaticamente pelo laboratório para provisionar todos os recursos definidos no modelo do Azure Resource Manager.
1. Assim que o ambiente for criado, selecione-o na lista **Minhas máquinas virtuais** para abrir a folha do grupo de recursos e procurar todos os recursos provisionados no ambiente.
    
    ![Lista Minhas máquinas virtuais](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)
   
   Você também pode expandir o ambiente para exibir apenas a lista de VMs que estão provisionadas no ambiente.
    
    ![Lista Minhas máquinas virtuais](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Clique em qualquer um dos ambientes para exibir as ações disponíveis - como aplicar artefatos, anexar discos de dados, mudar a hora de finalização automática e muito mais.

    ![Ações do ambiente](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

## <a name="next-steps"></a>Próximas etapas
* Após a criação da VM, você pode conectar a VM selecionando **Conectar** na folha da VM.
* Exiba e gerencie recursos em um ambiente selecionando-o na lista **Minhas máquinas virtuais** no seu laboratório. 
* Explorar os [modelos do Azure Resource Manager na galeria de modelos de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates)
