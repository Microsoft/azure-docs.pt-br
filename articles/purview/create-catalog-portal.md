---
title: 'Início Rápido: Criar uma conta do Azure Purview no portal do Azure (versão prévia)'
description: Este guia de início rápido descreve como criar uma conta do Azure Purview e configurar permissões para começar a usá-la.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: quickstart
ms.date: 10/23/2020
ms.openlocfilehash: 0346b467bc299b4eb6125df04a4449e94c035e47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101666467"
---
# <a name="quickstart-create-an-azure-purview-account-in-the-azure-portal"></a>Início Rápido: Criar uma conta do Azure Purview no portal do Azure

> [!IMPORTANT]
> Atualmente, o Azure Purview está em VERSÃO PRÉVIA. Os [Termos de Uso Complementares para Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluem termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, versão prévia ou que, de outra forma, ainda não foram lançados em disponibilidade geral.

Neste início rápido, você criará uma conta do Azure Purview.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* O próprio [locatário do Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Sua conta precisa ter permissão para criar recursos na assinatura

* Se o **Azure Policy** estiver impedindo todos os aplicativos de criarem uma **Conta de armazenamento** e um **namespace do EventHub**, será necessário criar uma exceção de política usando a marca, que poderá ser inserida durante o processo de criação de uma conta do Purview. O principal motivo é que, para cada conta do Purview criada, ele precisa criar um grupo de recursos gerenciados e, dentro desse grupo de recursos, uma conta de armazenamento e um namespace do EventHub.

    > [!important]
    > Você não precisará seguir esta etapa se não tiver o Azure Policy ou se um Azure Policy existente não estiver bloqueando a criação da **conta de armazenamento** e do **namespace do Hub de Eventos**.

    1. Navegue até o portal do Azure e pesquise **Policy**
    1. Siga [Criar uma definição de política personalizada](../governance/policy/tutorials/create-custom-policy-definition.md) ou modificar a política existente para adicionar duas exceções com o operador `not` e marca `resourceBypass`:

        ```json
        {
          "mode": "All",
          "policyRule": {
            "if": {
              "anyOf": [
              {
                "allOf": [
                {
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
                },
                {
                  "not": {
                    "field": "tags['<resourceBypass>']",
                    "exists": true
                  }
                }]
              },
              {
                "allOf": [
                {
                  "field": "type",
                  "equals": "Microsoft.EventHub/namespaces"
                },
                {
                  "not": {
                    "field": "tags['<resourceBypass>']",
                    "exists": true
                  }
                }]
              }]
            },
            "then": {
              "effect": "deny"
            }
          },
          "parameters": {}
        }
        ```
        
        > [!Note]
        > A marca pode ser qualquer coisa ao lado de `resourceBypass` e cabe a você definir o valor ao criar o Purview nas últimas etapas, desde que a política possa detectar a marca.

        :::image type="content" source="./media/create-catalog-portal/policy-definition.png" alt-text="Captura de tela mostrando como criar uma definição de política.":::

    1. [Crie uma atribuição de política](../governance/policy/assign-policy-portal.md) usando a política personalizada criada.

        [ ![Captura de tela mostrando como criar a atribuição de política](./media/create-catalog-portal/policy-assignment.png)](./media/create-catalog-portal/policy-assignment.png#lightbox)

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com) com sua conta do Azure.

## <a name="configure-your-subscription"></a>Configurar sua assinatura

Se necessário, siga essas etapas para configurar sua assinatura para permitir que o Azure Purview seja executado em sua assinatura:

   1. No Portal do Azure, pesquise e selecione **Assinaturas**.

   1. Na lista de assinaturas, selecione a assinatura que você deseja usar. É necessária a permissão de acesso administrativo para a assinatura.

      :::image type="content" source="./media/create-catalog-portal/select-subscription.png" alt-text="Captura de tela mostrando como selecionar uma assinatura no portal do Azure.":::

   1. Em sua assinatura, selecione **Provedores de recursos**. No painel **Provedores de recursos**, pesquise e registre todos os três provedores de recursos: 
       1. **Microsoft.Purview**
       1. **Microsoft.Storage**
       1. **Microsoft.EventHub** 
      
      Se não estiverem registrados, registre-os selecionando **Registrar**.

      :::image type="content" source="./media/create-catalog-portal/register-purview-resource-provider.png" alt-text="Captura de tela mostrando como registrar o provedor de recursos do Microsoft dot Azure Purview no portal do Azure.":::

## <a name="create-an-azure-purview-account-instance"></a>Criar uma instância da conta do Azure Purview

1. Acesse a página **Contas do Purview** no portal do Azure e selecione **Adicionar** para criar uma conta do Azure Purview. Como alternativa, você pode acessar a pesquisa do marketplace de **Contas do Purview** e selecionar **Criar**. Observe que você pode adicionar apenas uma conta do Azure Purview por vez.

   :::image type="content" source="./media/create-catalog-portal/add-purview-instance.png" alt-text="Captura de tela mostrando como criar uma instância de conta do Azure Purview no portal do Azure.":::

    > [!Note] 
    > O Azure Purview não dá suporte à movimentação da conta entre regiões. Encontre mais informações sobre isso na [página de serviços compatíveis do Azure](../azure-resource-manager/management/region-move-support.md).

1. Na folha **Noções Básicas**, faça o seguinte:
    1. Selecione um **Grupo de recursos**.
    1. Insira um **nome da conta do Purview** para seu catálogo. Não são permitidos espaços nem símbolos.
    1. Escolha uma **Localização** e selecione **Avançar: configuração**.
1. Na guia **Configuração**, selecione o **Tamanho da plataforma** desejado – os valores permitidas são 4 CU (unidades de capacidade) e 16 CU. Selecione **Avançar: Marcas**.
1. Na guia **Marcas**, você pode opcionalmente adicionar uma ou mais marcas. Essas marcas são para uso apenas no portal do Azure, não no Azure Purview. 

    > [!Note] 
    > Se você tiver **Azure Policy** e precisar adicionar uma exceção como em **Pré-requisitos**, será preciso adicionar a marca correta. Por exemplo, você pode adicionar a marca `resourceBypass`: :::image type="content" source="./media/create-catalog-portal/add-purview-tag.png" alt-text="Adicionar marca à conta do Purview.":::

1. Selecione **Examinar e Criar** e escolha **Criar**. A criação leva alguns minutos para ser concluída. A instância de conta do Azure Purview recém-criada aparece na lista na página **Contas do Purview**.
1. Quando o provisionamento da nova conta for concluído, selecione **Ir para o recurso**.

    > [!Note]
    > Se o provisionamento falhou com o status `Conflict`, isso significa que há uma política do Azure impedindo o Purview de criar uma **conta de armazenamento** e um **namespace do EventHub**. Você precisa percorrer as etapas de **Pré-requisitos** para adicionar exceções.
    > :::image type="content" source="./media/create-catalog-portal/purview-conflict-error.png" alt-text="Mensagem de erro de conflito do Purview":::

1. Selecione **Iniciar conta do Purview**.

   :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="Captura de tela da seleção para iniciar o catálogo de contas do Azure Purview.":::

## <a name="add-a-security-principal-to-a-data-plane-role"></a>Adicionar uma entidade de segurança a uma função de plano de dados

Antes que você ou sua equipe possa começar a usar o Azure Purview, uma ou mais entidades de segurança precisam ser adicionadas a uma das funções de Plano de Dados predefinidas: **Leitor de Dados do Purview**, **Curador de Dados do Purview** ou **Administrador de Fonte de Dados do Purview**. Para obter mais informações sobre as permissões do Catálogo de Dados do Azure Purview, confira [Permissões do catálogo](catalog-permissions.md).

Para adicionar uma entidade de segurança à função de plano de dados do **Curador de Dados do Purview** em uma conta do Azure Purview:

1. Acesse a página [**Contas do Purview**](https://aka.ms/purviewportal) no portal do Azure.

1. Selecione a conta do Azure Purview que você deseja modificar.

1. Na página **Conta do Purview**, selecione a guia **Controle de acesso (IAM)**

1. Clique em **+ Adicionar**

Se, ao clicar em Adicionar, você vê duas opções exibidas como marcadas (desabilitadas), isso significa que você não tem as permissões corretas para adicionar qualquer pessoa a uma função de plano de dados na conta do Azure Purview. Você precisa de um Proprietário, Administrador de Acesso do Usuário ou outra pessoa com autoridade de atribuição de função em sua conta do Azure Purview. Você pode procurar as pessoas certas selecionando a guia **Atribuições de função** e rolando para baixo para procurar o Proprietário ou Administrador de Acesso do Usuário e entrar em contato com essas pessoas.

1. Selecione **Adicionar atribuição de função**.

1. Para o tipo de Função em **Função Curador de Dados do Purview** ou **Função Administrador de Fonte de Dados do Purview**, dependendo da finalidade de uso da entidade de segurança (confira [Permissões de catálogo](catalog-permissions.md) e [Objetos de aplicativo e de entidade de serviço no Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md) para obter detalhes).

1. Em **Atribuir acesso a**, deixe o padrão, **Usuário, grupo ou entidade de serviço**.

1. Em **Selecionar**, insira o nome do usuário, o grupo ou a entidade de serviço do Azure Active Directory que você deseja atribuir e clique no nome dele no painel de resultados.

1. Clique em **Save**.

## <a name="clean-up-resources"></a>Limpar recursos

Se você não precisar mais dessa conta do Azure Purview, exclua-a com as seguintes etapas:

1. Acesse a página **Contas do Purview** no portal do Azure.

2. Selecione a conta do Azure Purview que você criou no início deste guia de início rápido. Selecione **Excluir**, insira o nome da conta e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a criar uma conta do Azure Purview.

Passe para o próximo artigo para saber como permitir que os usuários acessem sua conta do Azure Purview. 

> [!div class="nextstepaction"]
> [Adicionar usuários à sua conta do Azure Purview](catalog-permissions.md)