---
title: Extensão do Azure Policy para Visual Studio Code
description: Saiba como usar a extensão de Azure Policy para Visual Studio Code para procurar Azure Resource Manager aliases.
ms.date: 01/11/2021
ms.topic: how-to
ms.openlocfilehash: 4c4ba0eeb0506179ff92ead0ee86f048600d157e
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98107932"
---
# <a name="use-azure-policy-extension-for-visual-studio-code"></a>Usar extensão de Azure Policy para Visual Studio Code

> Aplica-se a Azure Policy versão de extensão **0.1.1** e mais recente

Saiba como usar a extensão de Azure Policy para Visual Studio Code para pesquisar [aliases](../concepts/definition-structure.md#aliases), examinar recursos e políticas, exportar objetos e avaliar definições de políticas. Primeiro, descreveremos como instalar a extensão de Azure Policy no Visual Studio Code. Em seguida, vamos examinar como Pesquisar aliases.

A extensão de Azure Policy para Visual Studio Code pode ser instalada no Windows.

## <a name="prerequisites"></a>Pré-requisitos

Os itens a seguir são necessários para concluir as etapas neste artigo:

- Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- [Visual Studio Code](https://code.visualstudio.com).

## <a name="install-and-configure-the-azure-policy-extension"></a>Instalar e configurar a extensão de Azure Policy

Depois de atender aos pré-requisitos, você pode instalar Azure Policy extensão para Visual Studio Code seguindo estas etapas:

1. Abra o Visual Studio Code.
1. Na barra de menus, vá para **Exibir**  >  **extensões**.
1. Na caixa de pesquisa, digite **Azure Policy**.
1. Selecione **Azure Policy** nos resultados da pesquisa e, em seguida, selecione **instalar**.
1. Selecione **Recarregar** quando necessário.

Para um usuário de nuvem nacional, siga estas etapas para definir o ambiente do Azure primeiro:

1. Selecione **File\Preferences\Settings**.
1. Pesquise na seguinte cadeia de caracteres: _Azure: Cloud_
1. Selecione a nuvem de país na lista:

   :::image type="content" source="../media/extension-for-vscode/set-default-azure-cloud-sign-in.png" alt-text="Captura de tela da seleção da entrada da nuvem do Azure da nação para Visual Studio Code." border="false":::

## <a name="using-the-policy-extension"></a>Usando a extensão de política

> [!NOTE]
> As alterações feitas localmente nas políticas exibidas na extensão de Azure Policy para Visual Studio Code não são sincronizadas com o Azure.

### <a name="connect-to-an-azure-account"></a>Conectar-se a uma conta do Azure

Para avaliar os recursos e os aliases de pesquisa, você deve se conectar à sua conta do Azure. Siga estas etapas para se conectar ao Azure por meio do Visual Studio Code:

1. Entre no Azure por meio da extensão de Azure Policy ou da paleta de comandos.

   - Extensão de Azure Policy

     Na extensão Azure Policy, selecione **entrar no Azure**.

     :::image type="content" source="../media/extension-for-vscode/azure-cloud-sign-in-policy-extension.png" alt-text="Captura de tela de Visual Studio Code e o ícone da extensão de Azure Policy." border="false":::

   - Paleta de Comandos

     Na barra de menus, vá para **Exibir**  >  **paleta de comandos** e insira **Azure: entrar**.

     :::image type="content" source="../media/extension-for-vscode/azure-cloud-sign-in-command-palette.png" alt-text="Captura de tela das opções de entrada na nuvem do Azure para Visual Studio Code na paleta de comandos." border="false":::

1. Siga as instruções de entrada para entrar no Azure. Depois que você estiver conectado, o nome da sua conta do Azure será mostrado na barra de status na parte inferior da janela de Visual Studio Code.

### <a name="select-subscriptions"></a>Selecione assinaturas

Quando você entra pela primeira vez, somente os recursos e as políticas de assinatura padrão são carregados pela extensão de Azure Policy. Para adicionar ou remover assinaturas da exibição de recursos e políticas, siga estas etapas:

1. Inicie o comando de assinatura na paleta de comandos ou no rodapé da janela.

   - Paleta de comandos:

     Na barra de menus, vá para **Exibir** > **paleta de comandos** e insira **Azure: selecionar assinaturas**.

   - Rodapé da janela

     No rodapé da janela na parte inferior da tela, selecione o segmento que corresponde ao **Azure: \<your account\>**.

1. Use a caixa de filtro para localizar rapidamente as assinaturas por nome. Em seguida, verifique ou remova a verificação de cada assinatura para definir as assinaturas mostradas pela extensão de Azure Policy. Quando terminar de adicionar ou remover assinaturas a serem exibidas, selecione **OK**.

### <a name="search-for-and-view-resources"></a>Pesquisar e exibir recursos

A extensão Azure Policy lista os recursos nas assinaturas selecionadas por provedor de recursos e por grupo de recursos no painel **recursos** . O TreeView inclui os seguintes agrupamentos de recursos dentro da assinatura selecionada ou no nível da assinatura:

- **Provedores de recursos**
  - Cada provedor de recursos registrado com recursos e recursos filho relacionados que têm aliases de política
- **Grupos de recursos**
  - Todos os recursos do grupo de recursos em que estão

Por padrão, a extensão filtra a parte ' provedor de recursos ' por recursos existentes e recursos que têm aliases de política. Altere esse comportamento em extensões de **configurações**  >    >  **Azure Policy** para ver todos os provedores de recursos sem filtragem.

Clientes com centenas ou milhares de recursos em uma única assinatura podem preferir uma maneira pesquisável de localizar seus recursos. A extensão Azure Policy torna possível pesquisar um recurso específico com as seguintes etapas:

1. Inicie a interface de pesquisa na Azure Policy extensão ou na paleta de comandos.

   - Extensão de Azure Policy

     Na extensão Azure Policy, passe o mouse sobre o painel **recursos** e selecione as reticências e, em seguida, selecione **Pesquisar recursos**.

   - Paleta de comandos:

     Na barra de menus, vá para **Exibir** > **paleta de comandos** e insira **recursos: pesquisar recursos**.

1. Se mais de uma assinatura for selecionada para exibição, use o filtro para selecionar qual assinatura será pesquisada.

1. Use o filtro para selecionar qual grupo de recursos a ser pesquisado faz parte da assinatura escolhida anteriormente.

1. Use o filtro para selecionar qual recurso Exibir. O filtro funciona tanto para o nome do recurso quanto para o tipo de recurso.

### <a name="discover-aliases-for-resource-properties"></a>Descobrir aliases para propriedades de recurso

Quando um recurso é selecionado, seja por meio da interface de pesquisa ou selecionando-o no modo de exibição de árvore, a extensão Azure Policy abre o arquivo JSON que representa esse recurso e todos os seus valores de propriedade de Azure Resource Manager.

Quando um recurso estiver aberto, passar o mouse sobre o nome ou o valor da Propriedade do Resource Manager exibirá o Azure Policy alias, se houver. Neste exemplo, o recurso é um `Microsoft.Compute/virtualMachines` tipo de recurso e a propriedade **Propriedades. StorageProfile. imageReference. offer** é focalizada. Focalizar mostra os aliases correspondentes.

:::image type="content" source="../media/extension-for-vscode/extension-hover-shows-property-alias.png" alt-text="Captura de tela da extensão de Azure Policy para Visual Studio Code passando uma propriedade para exibir os nomes de alias." border="false":::

> [!NOTE]
> A extensão VS Code só dá suporte à avaliação de propriedades do modo do Resource Manager. Para obter mais informações sobre os modos, consulte as [definições de modo](../concepts/definition-structure.md#mode).

### <a name="search-for-and-view-policies-and-assignments"></a>Pesquisar e exibir políticas e atribuições

A extensão Azure Policy lista os tipos de política e as atribuições de política como um TreeView para as assinaturas selecionadas a serem exibidas no painel **políticas** . Clientes com centenas ou milhares de políticas ou atribuições em uma única assinatura podem preferir uma maneira pesquisável de localizar suas políticas ou atribuições. A extensão Azure Policy torna possível pesquisar uma política ou atribuição específica com as seguintes etapas:

1. Inicie a interface de pesquisa na Azure Policy extensão ou na paleta de comandos.

   - Extensão de Azure Policy

     Na extensão Azure Policy, passe o mouse sobre o painel **políticas** e selecione as reticências e, em seguida, selecione **políticas de pesquisa**.

   - Paleta de comandos:

     Na barra de menus, vá para **Exibir** > **paleta de comandos** e insira **políticas: políticas de pesquisa**.

1. Se mais de uma assinatura for selecionada para exibição, use o filtro para selecionar qual assinatura será pesquisada.

1. Use o filtro para selecionar o tipo de política ou a atribuição a ser pesquisada que faça parte da assinatura escolhida anteriormente.

1. Use o filtro para selecionar qual política ou exibir. O filtro funciona para _DisplayName_ para a definição de política ou atribuição de política.

Ao selecionar uma política ou atribuição, seja por meio da interface de pesquisa ou selecionando-a no modo de exibição de árvore, a extensão de Azure Policy abre o JSON que representa a política ou atribuição e todos os seus valores de Propriedade do Resource Manager. A extensão pode validar o esquema do Azure Policy JSON aberto.

### <a name="export-objects"></a>Exportar objetos

Os objetos de suas assinaturas podem ser exportados para um arquivo JSON local. No painel **recursos** ou **políticas** , passe o mouse sobre ou selecione um objeto exportável. No final da linha realçada, selecione o ícone salvar e selecione uma pasta para salvar os recursos JSON.

Os seguintes objetos podem ser exportados localmente:

- Painel de recursos
  - Grupos de recursos
  - Recursos individuais (em um grupo de recursos ou em um provedor de recursos)
- Painel de políticas
  - Atribuições de política
  - Definições de política internas
  - Definições de política personalizadas
  - Iniciativas

### <a name="on-demand-evaluation-scan"></a>Exame de avaliação sob demanda

Uma verificação de avaliação pode ser iniciada com a extensão de Azure Policy para Visual Studio Code. Para iniciar uma avaliação, selecione e fixe cada um dos seguintes objetos: um recurso, uma definição de política e uma atribuição de política.

1. Para fixar cada objeto, localize-o no painel **recursos** ou no painel **políticas** e selecione o ícone fixar em uma guia Editar. A fixação de um objeto o adiciona ao painel de **avaliação** da extensão.
1. No painel **avaliação** , selecione um de cada objeto e use o ícone selecionar para avaliação para marcá-lo como incluído na avaliação.
1. Na parte superior do painel de **avaliação** , selecione o ícone executar avaliação. Um novo painel no Visual Studio Code é aberto com os detalhes de avaliação resultantes no formato JSON.

> [!NOTE]
> Para definições de política [AuditIfNotExists](../concepts/effects.md#auditifnotexists) ou [DeployIfNotExists](../concepts/effects.md#deployifnotexists) , use o ícone de adição no painel **avaliação** para selecionar um recurso _relacionado_ para a verificação de existência.

Os resultados da avaliação fornecem informações sobre a definição de política e a atribuição de política junto com a propriedade **policyEvaluations. evaluationResult** . O resultado é semelhante ao exemplo a seguir:

```json
{
    "policyEvaluations": [
        {
            "policyInfo": {
                ...
            },
            "evaluationResult": "Compliant",
            "effectDetails": {
                "policyEffect": "Audit",
                "existenceScope": "None"
            }
        }
    ]
}
```

> [!NOTE]
> A extensão VS Code só dá suporte à avaliação de propriedades do modo do Resource Manager. Para obter mais informações sobre os modos, consulte as [definições de modo](../concepts/definition-structure.md#mode).
>
> O recurso de avaliação não funciona em instalações do macOS e Linux da extensão.

### <a name="sign-out"></a>Sair

Na barra de menus, vá para **Exibir**  >  **paleta de comandos** e, em seguida, insira **Azure: sair**.

## <a name="next-steps"></a>Próximas etapas

- Revise os exemplos em [Exemplos do Azure Policy](../samples/index.md).
- Revise a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Revisar [Compreendendo os efeitos da política](../concepts/effects.md).
- Entenda como [criar políticas de maneira programática](programmatically-create.md).
- Saiba como [corrigir recursos fora de conformidade](remediate-resources.md).
- Veja o que é um grupo de gerenciamento com [Organizar seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).
