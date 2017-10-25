---
title: "Usar a Política do Azure para criar e gerenciar políticas para impor a conformidade organizacional | Microsoft Docs"
description: "Use a Política do Azure para impor padrões, atender requisitos de auditoria e regulamentares, controlar custos, manter a consistência de segurança e desempenho e impor princípios de design em toda a empresa."
services: azure-policy
keywords: 
author: Jim-Parker
ms.author: jimpark
ms.date: 10/06/2017
ms.topic: tutorial
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 55e5a60294fc5ccb2a55b1e572af2fd27c68f462
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-policies-to-enforce-compliance"></a>Criar e gerenciar políticas para impor a conformidade

Compreender como criar e gerenciar políticas no Azure é importante para manter a conformidade com padrões corporativos e com contratos de nível de serviço. Neste tutorial, você aprenderá a usar a Política do Azure para realizar algumas das tarefas mais comuns relacionadas à criação, à atribuição e ao gerenciamento de políticas em sua organização, como:

> [!div class="checklist"]
> * Atribuir uma política para impor uma condição para os recursos que você criar no futuro
> * Criar e atribuir uma definição de iniciativa para acompanhar a conformidade de vários recursos
> * Resolver um recurso negado ou sem conformidade
> * Implementar uma nova política em toda a organização

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="opt-in-to-azure-policy"></a>Aceitar a Política do Azure

A Política do Azure está disponível na versão prévia limitada e, portanto, você precisa se registrar para solicitar acesso.

1. Vá até a Política do Azure em https://aka.ms/getpolicy e selecione **Inscrever-se** no painel esquerdo.

   ![Pesquisar pela política](media/assign-policy-definition/sign-up.png)

2. Aceite a Política do Azure selecionando na lista **Assinatura** as assinaturas com que gostaria de trabalhar. Em seguida, selecione **Registrar**.

   A lista de assinatura inclui todas as suas assinaturas do Azure.

   ![Aceitar o uso da Política do Azure](media/assign-policy-definition/preview-opt-in.png)

   Dependendo da demanda, pode levar alguns dias para aceitarmos sua solicitação de registro. Após sua solicitação ser aceita, você receberá um email informando que pode começar a usar o serviço.

## <a name="assign-a-policy"></a>Atribuir uma política

A primeira etapa para impor a conformidade com a Política do Azure é atribuir uma definição de política. Uma definição de política define sob quais condições a política será aplicada e qual ação será tomada. Neste exemplo, nós atribuímos uma definição de política interna chamada *Exigir SQL Server versão 12.0* para impor a condição de que todos os bancos de dados do SQL Server devem ser a v12.0 para estar em conformidade.

1. Inicie o serviço de Política do Azure no portal do Azure procurando e selecionando **Política** no painel esquerdo.

   ![Pesquisar pela política](media/assign-policy-definition/search-policy.png)

2. Selecione **Atribuições** no painel esquerdo da página de Política do Azure. Uma atribuição é uma política que foi atribuída para entrar em vigor em um escopo específico.
3. Selecione **Atribuir Política** na parte superior do painel **Atribuições**.

   ![Atribuir uma definição de política](media/create-manage-policy/select-assign-policy.png)

4. Na página **Atribuir Política**, clique no ![botão Definição de política](media/assign-policy-definition/definitions-button.png) ao lado do campo **Política** para abrir a lista de definições disponíveis.

   ![Abra as definições de políticas disponíveis](media/create-manage-policy/open-policy-definitions.png)

5. Selecione **Exigir SQL Server versão 12.0**.
   
   ![Localizar uma política](media/create-manage-policy/select-available-definition.png)

6. Forneça um **Nome** de exibição para a atribuição de política. Neste caso, vamos usar *Exigir SQL Server versão 12.0*. Você também pode adicionar uma **Descrição**opcional. A descrição fornece detalhes sobre como essa atribuição de política garante que todos os servidores SQL criados nesse ambiente sejam da versão 12.0.
7. Altere o tipo de preço para **Standard** para garantir que a política seja aplicada aos recursos existentes.

   Há dois tipos de preço na Política do Azure – *Gratuito* e *Standard*. Com a camada Gratuita, você só pode impor políticas para recursos futuros, enquanto com a Standard, você também pode impô-las para recursos existentes para compreender melhor seu estado de conformidade. Como estamos na versão prévia limitada, ainda não lançamos um modelo de preços, de modo que você não receberá uma fatura por selecionar a opção *Standard*. Para saber mais sobre os preços, veja: [Preços da Política do Azure](https://acom-milestone-ignite.azurewebsites.net/pricing/details/azure-policy/).

8. Selecione o **Escopo** – a assinatura (ou grupo de recursos) que você registrou anteriormente quando aceitou a Política do Azure. Um escopo determina em quais recursos ou agrupamento de recursos a atribuição de política é imposta. Pode variar de uma assinatura a grupos de recursos.

   Para este exemplo, estamos usando esta assinatura – **Azure Analytics Capacity Dev**. Sua assinatura será diferente.

10. Selecione **Atribuir**.

## <a name="implement-a-new-custom-policy"></a>Implementar uma nova política personalizada

Agora que atribuímos a definição de política, vamos criar uma nova política para reduzir custos, garantindo que VMs criadas em seu ambiente não possam estar na série G. Dessa forma, sempre que um usuário na organização tentar criar uma VM na série G, a solicitação será negada.

1. Selecione **Definição** em **Criação** no painel esquerdo.

   ![Definição em criação](media/create-manage-policy/definition-under-authoring.png)

2. Selecione **+ Definição de Política**.
3. Insira o seguinte:

   - O nome da definição de política – *Exigir SKUs de VM menores que a série G*
   - A descrição do que a definição de política se destina a fazer – Esta definição de política impõe que todas as VMs criadas nesse escopo tenham SKUs menores que a série G para reduzir o custo.
   - A assinatura em que a definição de política operará – neste caso, nossa definição de política operará em **Advisor Analytics Capacity Dev**. Sua lista de assinaturas será diferente.
   - Escreva o código JSON com:
      - Os parâmetros da política.
      - As regras/condições da política, nesse caso – tamanho do SKU de VM igual a série G
      - O efeito da política, neste caso – **Negar**.
   
   Esta deve ser a aparência do JSON

```json
{
    "policyRule": {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/sku.name",
            "like": "Standard_G*"
          }
        ]
      },
      "then": {
        "effect": "deny"
      }
    }
}
```

   Para exibir exemplos de código JSON, consulte este artigo – [Visão geral de políticas de recursos](../azure-resource-manager/resource-manager-policy.md)
   
4. Selecione **Salvar**.

## <a name="create-and-assign-an-initiative-definition"></a>Criar e atribuir uma definição de iniciativa

Com uma definição de iniciativa, você pode agrupar várias definições de política para alcançar uma meta geral. Você cria uma definição de iniciativa para garantir que os recursos dentro do escopo da definição fiquem em conformidade com as definições de política que compõem a definição da iniciativa.  Consulte a [Visão Geral da Política do Azure](./azure-policy-introduction.md) para obter mais informações sobre definições de iniciativas.

### <a name="create-an-initiative-definition"></a>Criar uma definição de iniciativa

1. Selecione **Definições** em **Criação** no painel esquerdo.

   ![Selecionar definições](media/create-manage-policy/select-definitions.png)

2. Selecione **Definição de Iniciativa** na parte superior da página, esta opção leva você para o formulário **Definição de Iniciativa**.
3. Insira o nome e a descrição da iniciativa.

   Neste exemplo, como queremos garantir que os recursos estejam em conformidade com as definições de política sobre a manutenção da segurança, o nome da iniciativa seria **Manter Segurança** e a descrição seria: **Esta iniciativa foi criada para lidar com todas as definições de política associadas à proteção de recursos**.

   ![Definição de iniciativa](media/create-manage-policy/initiative-definition.png)

4. Percorra a lista de **Definições Disponíveis** e selecione as definições de política que você deseja adicionar a essa iniciativa. Para nossa iniciativa **Manter Segurança**, adicione as seguintes definições de política internas:
   - Requer o SQL Server versão 12.0
   - Monitorar aplicativos Web desprotegidos na central de segurança.
   - Monitorar redes permissivas na central de segurança.
   - Monitorar possíveis listas de permissões de aplicativo na central de segurança.
   - Monitorar discos de VMs não criptografadas na central de segurança.

   ![Definições de iniciativa](media/create-manage-policy/initiative-definition-2.png)

   Após selecionar as definições de política na lista, você poderá vê-las em **Políticas e parâmetros**, conforme mostrado acima.

5. Selecione **Criar**.

### <a name="assign-an-initiative-definition"></a>Atribuir uma definição de iniciativa

1. Vá até a guia **Definições** em **Criação**.
2. Pesquise a definição de **Manter Segurança** que você criou.
3. Selecione a definição da iniciativa e, em seguida, selecione **Atribuir**.

   ![Atribuir uma definição](media/create-manage-policy/assign-definition.png)

4. Preencha o formulário de **Atribuição** digitando:
   - nome: Atribuição Manter segurança
   - descrição: esta atribuição de iniciativa é voltada para impor este grupo de definições de política à assinatura **Azure Advisor Capacity Dev**
   - tipo de preço: Standard
   - escopo a que você deseja que essa atribuição seja aplicada: **Azure Advisor Capacity Dev**

5. Selecione **Atribuir**. 

## <a name="resolve-a-non-compliant-or-denied-resource"></a>Resolver um recurso negado ou sem conformidade

Seguindo o exemplo acima, após atribuir a definição de política para exigir o SQL Server versão 12.0, um SQL Server criado com uma versão diferente seria recusado. Nesta seção, veremos como resolver uma tentativa negada de criar um SQL Server de uma versão diferente.

1. Selecione **Atribuições** no painel esquerdo.
2. Navegue por todas as atribuições de política e inicie a atribuição *Exigir SQL Server versão 12.0*.
3. Solicite uma exclusão para os grupos de recursos em que você está tentando criar o SQL Server. Nesse caso, excluiremos Microsoft.Sql/servers/databases: *baconandbeer/Cheetos* e *baconandbeer/Chorizo*.

   ![Solicitar exclusão](media/create-manage-policy/request-exclusion.png)

   Outras maneiras de resolver um recurso negado incluem: contatar o contato associado à política se você tiver uma justificativa forte para precisar que o SQL Server seja criado e editar a política diretamente se você tiver acesso a ela.

4. Selecione **Salvar**.

Nesta seção, você resolveu a recusa de sua tentativa de criar um SQL Server com a versão 12.0, solicitando uma exclusão para os recursos.

## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja continuar trabalhando com os tutoriais subsequentes, não limpe os recursos criados neste guia. Se não planejar continuar, use as etapas a seguir para excluir as atribuições ou definições criadas acima:

1. Selecione **Definições** (ou **Atribuições** se você estiver tentando excluir uma atribuição) no painel esquerdo.
2. Pesquisar pela nova iniciativa ou definição de política (ou atribuição) que acabou de criar.
3. Selecione as reticências no final da definição ou da atribuição e selecione **Excluir Definição** (ou **Excluir Atribuição**).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você fez o seguinte com sucesso:

> [!div class="checklist"]
> * Atribuiu uma política para impor uma condição para os recursos que você criar no futuro
> * Criou e atribuiu uma definição de iniciativa para acompanhar a conformidade de vários recursos
> * Resolveu um recurso negado ou sem conformidade
> * Implementou uma nova política em toda a organização

Para saber mais sobre as estruturas das definições de políticas, consulte este artigo:

> [!div class="nextstepaction"]
> [Estrutura da Definição de Política](../azure-resource-manager/resource-manager-policy.md#policy-definition-structure)
