---
title: Usar serviços de PaaS (plataforma como serviço) no Azure DevTest Labs
description: Saiba como usar serviços de passagem (plataforma como serviço) no Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: eec37527386098174906dc2737d7b763241da3f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85478732"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>Usar serviços de PaaS (plataforma como serviço) no Azure DevTest Labs
O PaaS tem suporte no DevTest Labs por meio do recurso de ambientes. Os ambientes no DevTest Labs têm suporte de modelos de Azure Resource Manager pré-configurados em um repositório git. Os ambientes podem conter recursos de PaaS e IaaS. Eles permitem que você crie sistemas complexos que podem incluir recursos do Azure, como máquinas virtuais, bancos de dados, redes virtuais e aplicativos Web, que são personalizados para trabalhar juntos. Esses modelos permitem implantação consistente e gerenciamento aprimorado de ambientes usando o controle do código-fonte. 

Um sistema configurado corretamente permite os seguintes cenários: 

- Os desenvolvedores tenham ambientes independentes e vários
- Testando em diferentes configurações de forma assíncrona
- Integração em pipelines de produção e de preparo sem nenhuma alteração de modelo
- Ter máquinas de desenvolvimento e ambientes no mesmo laboratório melhora a facilidade de gerenciamento e relatórios de custos.  

O provedor de recursos do DevTest Labs cria recursos no nome do usuário do laboratório, portanto, nenhuma permissão extra precisa ser dada ao usuário do laboratório para habilitar o uso dos recursos de PaaS. A imagem a seguir mostra um Cluster Service Fabric como um ambiente no laboratório.

![Service Fabric cluster como um ambiente](./media/create-environment-service-fabric-cluster/cluster-created.png)

Para obter mais informações sobre como configurar ambientes, consulte [criar ambientes de várias VMs e recursos de PaaS com modelos de Azure Resource Manager](devtest-lab-create-environment-from-arm.md). O DevTest Labs tem um repositório público de modelos de Azure Resource Manager que você pode usar para criar ambientes sem precisar se conectar a uma fonte externa do GitHub por conta própria. Para obter mais informações sobre ambientes públicos, consulte [configurar e usar ambientes públicos no Azure DevTest Labs](devtest-lab-configure-use-public-environments.md).

Em grandes organizações, as equipes de desenvolvimento normalmente fornecem ambientes como ambientes de teste personalizado/isolado. Pode haver ambientes que devem ser usados por todas as equipes em uma unidade de negócios ou uma divisão. O grupo de ti pode desejar fornecer seus ambientes que podem ser usados por todas as equipes da organização.  

## <a name="customizations"></a>Personalizações

#### <a name="sandbox"></a>Área restrita 
O proprietário do laboratório pode personalizar ambientes de laboratório para alterar a função do usuário do **leitor** para o **colaborador** dentro do grupo de recursos. Essa funcionalidade está na página **configurações do laboratório** , sob a **configuração e as políticas** do laboratório. Essa alteração na função permite que o usuário adicione ou remova recursos dentro desse ambiente. Se você quiser restringir ainda mais o acesso, use as políticas do Azure. Essa funcionalidade permite que você personalize os recursos ou a configuração sem o acesso no nível da assinatura.

#### <a name="custom-tokens"></a>Tokens personalizados
Há algumas informações de laboratório personalizadas que estão fora do grupo de recursos e são específicas de ambientes que o modelo pode acessar. Eis algumas delas: 

- Identificação de rede do laboratório
- Location
- Conta de armazenamento na qual os arquivos de modelos do Resource Manager são armazenados. 
 
#### <a name="lab-virtual-network"></a>Rede virtual do laboratório
O artigo [conectando ambientes à rede virtual do laboratório](connect-environment-lab-virtual-network.md) descreve como modificar seu modelo do Resource Manager para usar o `$(LabSubnetId)` token. Quando um ambiente é criado, o `$(LabSubnetId)` token é substituído pela primeira marca de sub-rede onde a opção **usar na criação de máquina virtual** é definida como **true**. Ele permite que nosso ambiente use redes criadas anteriormente. Se você quiser usar os mesmos modelos do Resource Manager em ambientes em teste como preparo e produção, use `$(LabSubnetId)` como um valor padrão em um parâmetro de modelo do Resource Manager. 

#### <a name="environment-storage-account"></a>Conta de armazenamento do ambiente
O DevTest Labs dá suporte ao uso de [modelos aninhados do Resource Manager](../azure-resource-manager/templates/linked-templates.md). O artigo [[implantar modelos de Azure Resource Manager aninhados para ambientes de teste](deploy-nested-template-environments.md) explica como usar  `_artifactsLocation` e `_artifactsLocationSasToken` tokens para criar um URI para um modelo do Resource Manager na mesma pasta que ou em uma pasta aninhada do modelo principal. Para obter mais informações sobre esses dois tokens, consulte a seção **artefatos de implantação** em [Azure Resource Manager – guia de práticas recomendadas](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md).

## <a name="user-experience"></a>Experiência de usuário

## <a name="developer"></a>Desenvolvedor
Os desenvolvedores usam o mesmo fluxo de trabalho para a criação de uma VM para criar um ambiente específico. Eles selecionam o ambiente versus a imagem do computador e inserem as informações necessárias exigidas pelo modelo. Cada desenvolvedor que tem um ambiente permite a implantação de alterações e a depuração de loop interno aprimorada. O ambiente pode ser criado a qualquer momento usando o modelo mais recente.  Esse recurso permite que os ambientes sejam destruídos e recriados para ajudar a reduzir o tempo de inatividade da criação manual do sistema ou da recuperação do teste de falhas.  

### <a name="testing"></a>Testando
Os ambientes do DevTest Labs permitem testes independentes de código e configurações específicas de forma assíncrona. A prática comum é configurar o ambiente com o código da solicitação pull individual e iniciar qualquer teste automatizado. Depois que o teste automatizado for executado até a conclusão, qualquer teste manual poderá ser executado em relação ao ambiente específico. Normalmente, esse processo é feito como parte do pipeline de CI/CD. 

## <a name="management-experience"></a>Experiência de gerenciamento

### <a name="cost-tracking"></a>Controle de custos
O recurso de controle de custo inclui recursos do Azure dentro de ambientes diferentes como parte da tendência de custo geral. O custo por recursos não divide os diferentes recursos no ambiente, mas exibe o ambiente como um único custo.

### <a name="security"></a>Segurança
Uma assinatura do Azure configurada corretamente com o DevTest Labs pode [limitar o acesso aos recursos do Azure somente por meio do laboratório](devtest-lab-add-devtest-user.md). Com ambientes, um proprietário de laboratório pode permitir que os usuários acessem recursos de PaaS com configurações aprovadas sem permitir acesso a outros recursos do Azure. No cenário em que os usuários do laboratório personalizam ambientes, o proprietário do laboratório pode permitir o acesso de colaborador. O acesso de colaborador permite que o usuário do laboratório adicione ou remova recursos do Azure somente dentro do grupo de recursos gerenciado. Ele permite o controle e o gerenciamento mais fáceis em vez de permitir o acesso de colaborador do usuário à assinatura.

### <a name="automation"></a>Automação
A automação é um componente fundamental para um ecossistema de grande escala e eficaz. A automação é necessária para lidar com o gerenciamento ou acompanhamento de vários ambientes em assinaturas e laboratórios.

### <a name="cicd-pipeline"></a>Pipeline de CI/CD
Os serviços de PaaS no DevTest Labs podem ajudar a melhorar o pipeline de CI/CD tendo implantações focadas em que o acesso é controlado pelo laboratório.

## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes artigos para obter detalhes sobre ambientes: 

- 
- [Criar ambientes de várias VMs e recursos de PaaS com modelos do Azure Resource Manager](devtest-lab-create-environment-from-arm.md)
- [Configurar e usar ambientes públicos no Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Criar um ambiente com cluster de Service Fabric autônomo no Azure DevTest Labs](create-environment-service-fabric-cluster.md)
- [Conectar um ambiente à rede virtual do seu laboratório no Azure DevTest Labs](connect-environment-lab-virtual-network.md)
- [Integre ambientes em seus pipelines de CI/CD do Azure DevOps](integrate-environments-devops-pipeline.md)
 





