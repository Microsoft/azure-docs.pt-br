---
title: "Criar uma atribuição de política para identificar recursos sem conformidade em seu ambiente do Azure | Microsoft Docs"
description: "Este artigo orienta você quanto às etapas para criar uma definição de política para identificar recursos sem conformidade."
services: azure-policy
keywords: 
author: Jim-Parker
ms.author: jimpark
ms.date: 10/06/2017
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 2e0962ae02dd8132d878792634abc1f63b2c29a1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment"></a>Criar uma atribuição de política para identificar recursos sem conformidade em seu ambiente do Azure
A primeira etapa para compreender a conformidade no Azure é saber qual é a situação de seus recursos atuais. Este guia de início rápido orienta você quanto ao processo de criação de uma atribuição de política para identificar recursos que não estão usando o SQL Server versão 12.0. No final deste processo, você terá identificado com êxito quais servidores são de uma versão diferente e, portanto, *sem conformidade*.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="opt-in-to-azure-policy"></a>Aceitar a Política do Azure

A Política do Azure está disponível na versão prévia limitada e, portanto, você precisa se registrar para solicitar acesso.

1. Vá até a Política do Azure em https://aka.ms/getpolicy e selecione **Inscrever-se** no painel esquerdo.

   ![Pesquisar pela política](media/assign-policy-definition/sign-up.png)

2. Aceite a Política do Azure selecionando na lista **Assinatura** as assinaturas com que gostaria de trabalhar. Em seguida, selecione **Registrar**.

   ![Aceitar o uso da Política do Azure](media/assign-policy-definition/preview-opt-in.png)

   Pode levar alguns dias para aceitarmos sua solicitação de registro, com base na demanda. Após sua solicitação ser aceita, você receberá um email informando que pode começar a usar o serviço.

## <a name="create-a-policy-assignment"></a>Criar uma atribuição de política

Neste guia de início rápido, criamos uma atribuição de política e atribuímos a definição *Exigir SQL Server versão 12.0*. 

1. Selecione **Atribuições** no painel esquerdo da página de Política do Azure.
2. Selecione **Atribuir Política** na parte superior do painel **Atribuições**.

   ![Atribuir uma definição de política](media/assign-policy-definition/select-assign-policy.png)

3. Na página **Atribuir Política**, clique no ![botão Definição de política](media/assign-policy-definition/definitions-button.png) ao lado do campo **Política** para abrir a lista de definições disponíveis.

   ![Abra as definições de políticas disponíveis](media/assign-policy-definition/open-policy-definitions.png)

   A Política do Azure vem com definições de políticas internas que você pode usar. Você verá definições de políticas internas como:

   - Impor marca e seu valor
   - Aplicar marca e seu valor
   - Requer o SQL Server versão 12.0

4. Pesquise por suas definições de política para encontrar a definição *Exigir SQL Server versão 12.0*. Clique na política e clique em **Selecionar**.

   ![Encontre a definição de política correta](media/assign-policy-definition/select-available-definition.png)

5. Forneça um **Nome** de exibição para a atribuição de política. Neste caso, vamos usar *Exigir SQL Server versão 12.0*. Você também pode adicionar uma **Descrição**opcional. A descrição fornece detalhes sobre como essa atribuição de política garante que todos os servidores SQL criados nesse ambiente sejam da versão 12.0.
6. Altere o tipo de preço para **Standard** para garantir que a política seja aplicada aos recursos existentes.

   Há dois tipos de preço na Política do Azure – *Gratuito* e *Standard*. Com a camada Gratuita, você só pode impor políticas para recursos futuros, enquanto com a Standard, você também pode impô-las para recursos existentes para compreender melhor seu estado de conformidade. Como estamos na versão prévia limitada, ainda não lançamos um modelo de preços, de modo que você não receberá uma fatura por selecionar a opção *Standard*. Para saber mais sobre os preços, veja: [Preços da Política do Azure](https://acom-milestone-ignite.azurewebsites.net/pricing/details/azure-policy/).

7. Selecione o **Escopo** a que você gostaria que a política fosse aplicado.  Um escopo determina em quais recursos ou agrupamento de recursos a atribuição de política é imposta. Pode variar de uma assinatura a grupos de recursos.
8. Selecione a assinatura (ou grupo de recursos) que você registrou anteriormente quando aceitou a Política do Azure. Neste exemplo, estamos usando esta assinatura – **Azure Analytics Capacity Dev**, mas suas opções serão diferentes.

   ![Encontre a definição de política correta](media/assign-policy-definition/assign-policy.png)

9. Selecione **Atribuir**.

Agora, você está pronto para identificar recursos sem conformidade para compreender o estado de conformidade de seu ambiente.

## <a name="identify-non-compliant-resources"></a>Identificar recursos sem conformidade

Selecione **Conformidade** no painel esquerdo e pesquise a atribuição de política criada por você.

![Conformidade da política](media/assign-policy-definition/policy-compliance.png)

Se houver recursos sem conformidade com essa nova atribuição, eles aparecerão na guia **Recursos sem conformidade**.

Se uma condição for avaliada em recursos existentes e seu resultado for true para alguns deles, esses recursos serão marcados como sem conformidade com a política. Veja uma tabela de como as ações diferentes que temos disponíveis atualmente funcionam com o resultado da avaliação da condição e com o estado de conformidade de seus recursos.

|Recurso  |Se a condição na política for avaliada como  |Ação na política   |Estado de conformidade  |
|-----------|---------|---------|---------|
|Exists     |Verdadeiro     |NEGAR     |Sem conformidade |
|Exists     |Falso    |NEGAR     |Em conformidade     |
|Exists     |Verdadeiro     |Acrescentar   |Sem conformidade |
|Exists     |Falso    |Acrescentar   |Em conformidade     |
|Exists     |Verdadeiro     |Audit    |Sem conformidade |
|Exists     |Falso    |Audit    |Sem conformidade |

## <a name="clean-up-resources"></a>Limpar recursos

Outros guias desta coleção dão continuidade a este guia de início rápido. Se você planeja continuar trabalhando com os tutoriais subsequentes, não limpe os recursos criados neste guia de início rápido. Caso contrário, siga estas etapas para excluir todos os recursos criados por esse início rápido no Portal do Azure.
1. Selecione **Atribuições** no painel esquerdo.
2. Pesquise pela atribuição que você acabou de criar.

   ![Excluir uma atribuição](media/assign-policy-definition/delete-assignment.png)

3.  Selecione **Excluir Atribuição**.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você atribuiu uma definição de política a um escopo para garantir que todos os recursos nesse escopo estejam em conformidade e para identificar quais não são.

Para saber mais sobre a atribuição de políticas, para garantir que recursos que você criar no **futuro** estejam em conformidade, continue com o tutorial:

> [!div class="nextstepaction"]
> [Criando e gerenciando políticas](./create-manage-policy.md)

