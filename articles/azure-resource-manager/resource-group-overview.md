---
title: Visão geral do Azure Resource Manager | Microsoft Docs
description: Descreve como usar o Gerenciador de Recursos do Azure para implantação, gerenciamento e controle de acesso dos recursos do Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: overview
ms.date: 05/31/2019
ms.author: tomfitz
ms.openlocfilehash: 1b0135bfff7233367eaf47a2f6dd1ee3ccfbcaf6
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205519"
---
# <a name="azure-resource-manager-overview"></a>Visão geral do Azure Resource Manager

O Azure Resource Manager é p serviço de implantação e gerenciamento do Azure. Ele fornece uma camada de gerenciamento uniforme que lhe permite criar, atualizar e excluir recursos em sua assinatura do Azure. Você pode usar seus recursos de controle de acesso, auditoria e marcação para proteger e organizar seus recursos após a implantação.

Quando você executa ações por meio do portal, do PowerShell, da CLI do Azure, de APIs REST ou de SDKs do cliente, a API do Azure Resource Manager lida com sua solicitação. Como todas as solicitações são manipuladas por meio da mesma API, você verá funcionalidades e resultados uniformes em todas as diferentes ferramentas. Todos as funcionalidades disponíveis no portal também estão disponíveis por meio do PowerShell, da CLI do Azure, das APIs REST e dos SDKs de cliente. A funcionalidade inicialmente lançada por meio de APIs será representada no portal em até 180 dias depois da versão inicial.

A imagem a seguir mostra como todas as ferramentas interagem com a API do Azure Resource Manager. A API passa as solicitações para o serviço do Gerenciador de Recursos, que autentica e autoriza as solicitações. O Resource Manager, em seguida, encaminha as solicitações para o serviço apropriado.

![Modelo de solicitação do Gerenciador de Recursos](./media/resource-group-overview/consistent-management-layer.png)

## <a name="terminology"></a>Terminologia

Se você for um novo usuário do Azure Resource Manager, há alguns termos com os quais talvez você não esteja familiarizado.

* **recurso** -um item gerenciável que está disponível por meio do Azure. Máquinas virtuais, contas de armazenamento, aplicativos Web, bancos de dados e redes virtuais são exemplos de recursos.
* **grupo de recursos** - Um contêiner que mantém os recursos relacionados a uma solução do Azure. O grupo de recursos inclui esses recursos que você deseja gerenciar como um grupo. Você decide como alocar recursos para grupos de recursos com base no que faz mais sentido para sua organização. Confira [Grupos de recursos](#resource-groups).
* **provedor de recursos** – um serviço que fornece recursos do Azure. Por exemplo, um provedor de recursos comum é **Microsoft.Compute**, que fornece o recurso de máquina virtual. **Microsoft.Storage** é outro provedor de recursos comum. Confira [Provedores de recursos](#resource-providers).
* **Modelo do Resource Manager** – um arquivo JSON (JavaScript Object Notation) que define um ou mais recursos para implantação em um grupo de recursos ou assinatura. O modelo pode ser usado para implantar os recursos de forma consiste e repetida. Confira [Implantação de modelo](#template-deployment).
* **sintaxe declarativa** - sintaxe que permite a declaração "Isso é o que pretendo criar" sem precisar escrever a sequência de comandos de programação para criá-la. O modelo do Resource Manager é um exemplo de sintaxe declarativa. No arquivo, você define as propriedades da infraestrutura a ser implantada no Azure.

## <a name="the-benefits-of-using-resource-manager"></a>Os benefícios de usar o Gerenciador de Recursos

O Gerenciador de Recursos fornece vários benefícios:

* Você pode implantar, gerenciar e monitorar todos os recursos da sua solução como um grupo em vez de tratá-los individualmente.
* Você pode implantar a solução repetidamente em todo seu ciclo de vida de desenvolvimento e com a confiança de que seus recursos serão implantados em um estado consistente.
* Você pode gerenciar sua infraestrutura por meio de modelos declarativos em vez de scripts.
* Você pode definir as dependências entre os recursos para que eles sejam implantados na ordem correta.
* Você pode aplicar o controle de acesso a todos os serviços no grupo de recursos, pois o RBAC (Controle de Acesso Baseado em Função) é integrado nativamente à plataforma de gerenciamento.
* Você pode aplicar marcas aos recursos para organizar de modo lógico todos os recursos em sua assinatura.
* Você pode esclarecer a cobrança da sua organização exibindo os custos para um grupo de recursos que compartilha a mesma marcação.

## <a name="understand-scope"></a>Compreender o escopo

O Azure fornece quatro níveis de escopo: [grupos de gerenciamento](../governance/management-groups/index.md), assinaturas, [grupos de recursos](#resource-groups) e recursos. A imagem a seguir mostra um exemplo dessas camadas.

![Escopo](./media/resource-group-overview/scope-levels.png)

As configurações de gerenciamento são aplicadas em qualquer desses níveis de escopo. O nível que você seleciona determina o quão amplamente a configuração é aplicada. Os níveis inferiores herdam as configurações de níveis superiores. Por exemplo, ao aplicar uma [política](../governance/policy/overview.md) à assinatura, ela será aplicada a todos os grupos de recursos e recursos em sua assinatura. Ao aplicar uma política no grupo de recursos, ela será aplicada ao grupo de recursos e a todos os recursos. No entanto, outro grupo de recursos não terá essa atribuição de política.

Você pode implantar modelos em grupos de gerenciamento, assinaturas ou grupos de recursos.

## <a name="guidance"></a>Diretrizes

As sugestões a seguir ajudarão você a aproveitar ao máximo o Gerenciador de Recursos quando estiver trabalhando com suas soluções.

* Defina e implante a sua infraestrutura através de sintaxe declarativa em modelos do Gerenciador de Recursos, e não de comandos imperativos.
* Defina todas as etapas de implantação e configuração no modelo. Você não deve ter nenhuma etapa manual para configurar sua solução.
* Execute comandos imperativos para gerenciar os recursos, como iniciar ou interromper um aplicativo ou máquina.
* Organize recursos com o mesmo ciclo de vida em um grupo de recursos. Use marcas para as demais organizações de recursos.

Para obter orientação sobre como as empresas podem usar o Resource Manager para gerenciar assinaturas de forma eficaz, consulte [Azure enterprise scaffold – controle de assinatura prescritivas](/azure/architecture/cloud-adoption-guide/subscription-governance?toc=%2fazure%2fazure-resource-manager%2ftoc.json).

Para obter recomendações para a criação de modelos do Resource Manager, consulte [Melhores práticas para modelos do Azure Resource Manager](template-best-practices.md).

## <a name="resource-groups"></a>Grupos de recursos
Existem alguns fatores importantes a considerar ao definir seu grupo de recursos:

* Todos os recursos no grupo devem compartilhar o mesmo ciclo de vida. Você os implanta, atualiza e exclui juntos. Se um recurso, como um servidor de banco de dados, precisar existir em um ciclo de implantação diferente, ele deve ser de outro grupo de recursos.
* Cada recurso só pode existir em um grupo de recursos.
* Você pode adicionar ou remover um recurso de um grupo de recursos a qualquer momento.
* Você pode mover um recurso de um grupo de recursos para outro grupo. Para saber mais, confira [Mover recursos para um novo grupo de recursos ou assinatura](resource-group-move-resources.md).
* Um grupo de recursos pode conter recursos localizados em regiões diferentes.
* Um grupo de recursos pode ser usado para definir o escopo de controle de acesso para ações administrativas.
* Um recurso pode interagir com recursos em outros grupos de recursos. Essa interação é comum quando dois recursos estão relacionados, mas não compartilham o mesmo ciclo de vida (por exemplo, aplicativos Web que se conectam a um banco de dados).

Ao criar um grupo de recursos, você precisará fornecer um local para ele. Você pode estar se perguntando: "Por que um grupo de recursos precisa de um local? E, se os recursos podem ter locais diferentes do grupo de recursos, por que o local do grupo de recursos importa?" O grupo de recursos armazena metadados sobre os recursos. Portanto, quando você especifica um local para o grupo de recursos, especifica onde os metadados são armazenados. Por motivos de conformidade, você precisa fazer com que os dados sejam armazenados em determinada região.

Se a região do grupo de recursos está temporariamente indisponível, você não pode atualizar os recursos no grupo de recursos porque os metadados não estão disponíveis. Os recursos em outras regiões ainda funcionarão conforme o esperado, mas não será possível atualizá-los. Saiba mais sobre a criação de aplicativos confiáveis em [Desenvolver aplicativos do Azure confiáveis](/azure/architecture/reliability/).

## <a name="resource-providers"></a>Provedores de recursos

Cada provedor de recursos oferece um conjunto de recursos e operações para trabalhar com esses recursos. Por exemplo, se você quer armazenar chaves e segredos, trabalhe com o provedor de recursos **Microsoft.KeyVault** . Este provedor de recursos oferece um tipo de recurso denominado **cofres** para criar o cofre da chave.

O nome de um tipo de recurso está no formato: **{provedor-de-recursos}/{tipo-de-recurso}** . O tipo de recurso de um cofre de chaves é **Microsoft.KeyVault/vaults**.

Antes de começar a implantação de recursos, você deve ter uma compreensão dos provedores de recursos disponíveis. Conhecer os nomes dos provedores de recursos e dos recursos ajuda a definir os recursos que você deseja implantar no Azure. Além disso, você precisa conhecer os locais e as versões de API válidos para cada tipo de recurso. Para saber mais, veja [Provedores e tipos de recursos](resource-manager-supported-services.md).

Para conhecer todas as operações oferecidas pelos provedores de recursos, consulte [APIs REST do Azure](/rest/api/azure/).

## <a name="template-deployment"></a>Implantação de modelo

Com o Gerenciador de Recursos, você pode criar um modelo (no formato JSON) que define a infraestrutura e a configuração de sua solução do Azure. Usando um modelo, você pode implantar a solução repetidamente em todo seu ciclo de vida e com a confiança de que seus recursos serão implantados em um estado consistente.

Para saber mais sobre o formato do modelo e como construí-o, consulte [Noções básicas de estrutura e sintaxe dos modelos do Azure Resource Manager](resource-group-authoring-templates.md). Para exibir a sintaxe JSON para os tipos de recursos, consulte [Definir recursos nos modelos do Azure Resource Manager](/azure/templates/).

O Resource Manager processa o modelo como qualquer outra solicitação. Ele analisa o modelo e converte sua sintaxe em operações da API REST para provedores de recurso apropriado. Por exemplo, quando o Gerenciador de Recursos recebe um modelo com a seguinte definição de recurso:

```json
"resources": [
  {
    "apiVersion": "2016-01-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {
    }
  }
]
```

Converte a definição para a seguinte operação de API REST, que é enviada para o provedor de recursos Microsoft.Storage:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2016-01-01
REQUEST BODY
{
  "location": "westus",
  "properties": {
  }
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "Storage"
}
```

Como você define grupos de recursos e modelos é de sua responsabilidade e de como você deseja gerenciar a sua solução. Por exemplo, você pode implantar seu aplicativo de três camadas por meio de um único modelo para um único grupo de recursos.

![modelo de três camadas](./media/resource-group-overview/3-tier-template.png)

Mas, você não precisa definir toda a sua infraestrutura em um único modelo. Muitas vezes, faz sentido dividir seus requisitos de implantação em um conjunto de modelos com destinação e fins específicos. Você pode reutilizar esses modelos facilmente para soluções diferentes. Para implantar uma solução específica, você deve criar um modelo mestre que vincule todos os modelos necessários. A imagem a seguir mostra como implantar uma solução de três camadas com o modelo pai que inclui três modelos aninhados.

![modelo de camadas aninhadas](./media/resource-group-overview/nested-tiers-template.png)

Ao prever suas camadas com ciclos de vida separados, você pode implantar os três níveis para separar grupos de recursos. Observe que os recursos ainda podem ser vinculados aos recursos em outros grupos de recursos.

![modelo de camada](./media/resource-group-overview/tier-templates.png)

Para obter informações sobre modelos aninhados, confira [Usando modelos vinculados com o Azure Resource Manager](resource-group-linked-templates.md).

O Azure Resource Manager analisa as dependências para garantir que os recursos sejam criados na ordem correta. Se um recurso depende de um valor de outro recurso (como uma máquina virtual que precisa de uma conta de armazenamento para discos), você pode definir uma dependência. Para saber mais, confira [Definindo as dependências nos modelos do Gerenciador de Recursos do Azure](resource-group-define-dependencies.md).

Você também pode usar o modelo para atualizações de infraestrutura. Por exemplo, você pode adicionar um recurso à sua solução e adicionar regras de configuração para os recursos que já foram implantados. Se o modelo definir um recurso que já existe, o Resource Manager atualizará o recurso existente em vez de criar um novo.

O Gerenciador de Recursos fornece extensões para cenários que precisam de operações adicionais, como a instalação de um software específico que não está incluído na configuração. Se você já estiver usando um serviço de gerenciamento de configuração, como DSC, Chef ou Puppet, poderá continuar trabalhando com esse serviço usando as extensões. Para obter informações sobre extensões de máquina virtual, confira [Sobre recursos e extensões de máquina virtual](../virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Quando você cria uma solução no Portal, ela inclui automaticamente um modelo de implantação. Você não precisa criar seu modelo do zero, pois é possível iniciar com o modelo da sua solução e personalizá-lo para atender às suas necessidades específicas. Para obter uma amostra, confira [Início Rápido: Crie e implante modelos do Azure Resource Manager usando o portal do Azure](./resource-manager-quickstart-create-templates-use-the-portal.md). Você também pode recuperar um modelo de um grupo de recursos existente exportando o estado atual do grupo de recursos ou exibindo o modelo usado para determinada implantação. A exibição do [modelo exportado](./manage-resource-groups-portal.md#export-resource-groups-to-templates) é uma maneira útil de saber mais sobre a sintaxe do modelo.

Finalmente, o modelo se torna parte do código-fonte do seu aplicativo. Você pode adicioná-lo ao repositório de código-fonte e atualizá-lo conforme a evolução de seu aplicativo. Você pode editar o modelo com o Visual Studio.

Depois de definir o modelo, você está pronto para implantar os recursos para o Azure. Para implantar os recursos, confira:

* [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](resource-group-template-deploy.md)
* [Implantar recursos com modelos do Resource Manager e a CLI do Azure](resource-group-template-deploy-cli.md)
* [Implantar recursos com modelos do Resource Manager e o portal do Azure](resource-group-template-deploy-portal.md)
* [Implantar recursos com modelos do Resource Manager e a API REST do Resource Manager](resource-group-template-deploy-rest.md)

## <a name="safe-deployment-practices"></a>Práticas de implantação segura

Ao implantar um serviço complexo no Azure, você talvez precise implantar seu serviço em várias regiões e verificar sua integridade antes de prosseguir para a próxima etapa. Use o [Gerenciador de Implantação do Azure](deployment-manager-overview.md) para coordenar uma distribuição pré-configurada do serviço. Ao preparar a distribuição do seu serviço, você poderá encontrar problemas em potencial antes de ele ter sido implantado para todas as regiões. Se você não precisa dessas precauções, as operações de implantação na seção anterior são a melhor opção.

O Deployment Manager está em versão prévia pública.

## <a name="resiliency-of-azure-resource-manager"></a>Resiliência do Azure Resource Manager

O serviço do Azure Resource Manager foi projetado para resiliência e disponibilidade contínua. As operações do Resource Manager e do painel de controle (solicitações enviadas para management.azure.com) na API REST são:

* Distribuídas entre regiões. Alguns serviços são regionais.

* Distribuídas entre Zonas de Disponibilidade (e também regiões) em locais que têm várias Zonas de Disponibilidade.

* Não dependem de um datacenter lógico único.

* Nunca são desativadas para atividades de manutenção.

Essa resiliência se aplica aos serviços que recebem solicitações por meio do Resource Manager. Por exemplo, o Key Vault se beneficia dessa resiliência.

[!INCLUDE [arm-tutorials-quickstarts](../../includes/resource-manager-tutorials-quickstarts.md)]

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a usar o Azure Resource Manager para implantação, gerenciamento e controle de acesso dos recursos do Azure. Vá para o próximo artigo para aprender a criar seu primeiro modelo do Azure Resource Manager.

> [!div class="nextstepaction"]
> [Início Rápido: Criar e implantar modelos do Azure Resource Manager usando o portal do Azure](./resource-manager-quickstart-create-templates-use-the-portal.md)
