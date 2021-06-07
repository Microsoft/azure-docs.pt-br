---
title: 'Tutorial: Implementar o Azure Policy como código com o GitHub'
description: Neste tutorial, você implementará um fluxo de trabalho de Azure Policy como Código com exportação, GitHub Actions e fluxos de trabalho do GitHub
ms.date: 03/31/2021
ms.topic: tutorial
ms.openlocfilehash: 64957671597ad6df237f92176e10280dc45018c9
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106092748"
---
# <a name="tutorial-implement-azure-policy-as-code-with-github"></a>Tutorial: Implementar o Azure Policy como código com o GitHub

Um fluxo de trabalho do Azure Policy como Código torna possível gerenciar suas definições e atribuições de política como código, controlar o ciclo de vida de atualização dessas definições e automatizar a validação dos resultados de conformidade. Neste tutorial, você aprenderá a usar os recursos do Azure Policy com o GitHub para criar um processo de ciclo de vida. Essas tarefas incluem:

> [!div class="checklist"]
> - Exportar definições e atribuições de política para o GitHub
> - Enviar objetos de política por push atualizados no GitHub para o Azure
> - Disparar uma verificação de conformidade da ação do GitHub

Se você deseja atribuir uma política para identificar o estado atual de conformidade dos recursos existentes, os artigos de início rápido explicam como fazer isso.

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- Examine [Crie um fluxo de trabalho do Azure Policy como Código](../concepts/policy-as-code.md) para entender os padrões de design usados neste tutorial.

### <a name="export-azure-policy-objects-from-the-azure-portal"></a>Exportar objetos do Azure Policy do portal do Azure

Para exportar uma definição de política do portal do Azure, siga estas etapas:

1. Inicie o serviço de Azure Policy no portal do Azure clicando em **Todos os serviços**, em seguida pesquisando e selecionando **Política**.

1. Selecione **Definições** no lado esquerdo da página do Azure Policy.

1. Use o botão **Exportar definições** ou selecione as reticências na linha de uma definição de política. Em seguida, selecione **Exportar definição**.

1. Selecione o botão **Entrar com o GitHub**. Se você ainda não tiver feito a autenticação com o GitHub para autorizar o Azure Policy a exportar o recurso, examine o acesso de que a [Ação do GitHub](https://github.com/features/actions) precisa na nova janela aberta e selecione **Autorizar AzureGitHubActions** para dar continuidade ao processo de exportação. Após a conclusão, a nova janela será fechada automaticamente.

1. Na guia **Noções Básicas**, defina as seguintes opções e selecione a guia **Políticas** ou o botão **Avançar: Políticas** na parte inferior da página.

   - **Filtro de repositório**: defina como _Meus repositórios_ para ver apenas os repositórios que você tem ou _Todos os repositórios_ para ver todos aqueles aos quais você concedeu à Ação do GitHub acesso.
   - **Repositório**: defina como o repositório ao qual você deseja exportar os recursos do Azure Policy.
   - **Branch**: defina o branch no repositório. Usar um branch diferente do padrão é uma boa maneira de validar suas atualizações antes de mesclá-las em seu código-fonte.
   - **Diretório**: a _pasta de nível raiz_ à qual exportar os recursos do Azure Policy. As subpastas nesse diretório são criadas com base em quais recursos são exportados.

1. Na guia **Políticas**, defina o escopo a ser pesquisado selecionando as reticências e escolhendo uma combinação de grupos de gerenciamento, assinaturas ou grupos de recursos.
   
1. Use o botão **Adicionar definições de política** para pesquisar o escopo para os objetos a serem exportados. Na janela lateral aberta, selecione cada objeto a ser exportado. Filtre a seleção pela caixa de pesquisa ou pelo tipo. Após selecionar todos os objetos a serem exportados, use o botão **Adicionar** na parte inferior da página.

1. Para cada objeto selecionado, selecione as opções de exportação desejadas, como _Somente Definição_ ou _Definição e Atribuições_ para uma definição de política. Em seguida, selecione a guia **Examinar + Exportar** ou o botão **Avançar: Examinar + Exportar** na parte inferior da página.

   > [!NOTE]
   > Se a opção _Definição e Atribuições_ for escolhida, somente serão exportadas as atribuições de política dentro do escopo definido pelo filtro quando a definição de política for adicionada.

1. Na guia **Examinar + Exportar**, verifique se os detalhes coincidem e use o botão **Exportar** na parte inferior da página.

1. Verifique seu repositório do GitHub, o branch e a _pasta de nível raiz_ para ver que os recursos selecionados agora são exportados para o controle do código-fonte.

Os recursos do Azure Policy são exportados para a seguinte estrutura dentro do repositório GitHub selecionado e da _pasta de nível raiz_:

```text
|
|- <root level folder>/  ________________ # Root level folder set by Directory property
|  |- policies/  ________________________ # Subfolder for policy objects
|     |- <displayName>_<name>____________ # Subfolder based on policy displayName and name properties
|        |- policy.json _________________ # Policy definition
|        |- assign.<displayName>_<name>__ # Each assignment (if selected) based on displayName and name properties
|
```

### <a name="push-policy-objects-updated-in-github-to-azure"></a>Enviar objetos de política por push atualizados no GitHub para o Azure

1. Quando os objetos de política são exportados, um arquivo do [fluxo de trabalho do GitHub](https://docs.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) chamado `.github/workflows/manage-azure-policy-<randomLetters>.yml` também é criado para você começar.

   > [!NOTE]
   > O arquivo de fluxo de trabalho do GitHub é criado toda vez que a exportação é usada. Cada instância do arquivo é específica às opções durante essa ação de exportação.

1. Esse arquivo de fluxo de trabalho usa a ação [Gerenciar Azure Policy](https://github.com/marketplace/actions/manage-azure-policy) para enviar por push as alterações feitas aos objetos de política exportados no repositório do GitHub de volta para o Azure Policy. Por padrão, a ação considera e sincroniza somente os arquivos que são diferentes dos existentes no Azure. Você também pode usar o parâmetro `assignments` na ação para sincronizar apenas as alterações feitas em arquivos de atribuição específicos. Esse parâmetro pode ser usado para aplicar atribuições de política somente para um ambiente específico. Para obter mais informações, confira o [Leiame Gerenciar repositório de Azure Policy](https://github.com/Azure/manage-azure-policy).

1. Por padrão, o fluxo de trabalho precisa ser disparado manualmente. Para fazer isso, use as **Ações** no GitHub, selecione o fluxo de trabalho `manage-azure-policy-<randomLetters>`, selecione **Executar fluxo de trabalho** e **Executar fluxo de trabalho** novamente.

   :::image type="content" source="../media/policy-as-code-github/manually-trigger-workflow.png" alt-text="Captura de tela da guia Ação, do fluxo de trabalho e dos botões Executar fluxo de trabalho na interface Web do GitHub.":::

   > [!NOTE]
   > O arquivo de fluxo de trabalho precisa estar no branch padrão a ser detectado e executado manualmente.

1. O fluxo de trabalho sincroniza as alterações feitas nos objetos de política com o Azure e fornece o status nos logs.

   :::image type="content" source="../media/policy-as-code-github/workflow-logging.png" alt-text="Captura de tela do fluxo de trabalho em ação e detalhes registrados nos logs.":::

1. O fluxo de trabalho também adiciona detalhes em objetos `properties.metadata` do Azure Policy para você acompanhar.

   :::image type="content" source="../media/policy-as-code-github/updated-definition-metadata.png" alt-text="Captura de tela da definição do Azure Policy no portal do Azure atualizado com metadados específicos à ação do GitHub.":::

### <a name="trigger-compliance-scans-using-github-action"></a>Disparar verificações de conformidade usando a ação do GitHub

Usando a [ação de Verificação de Conformidade do Azure Policy](https://github.com/marketplace/actions/azure-policy-compliance-scan), você pode disparar uma verificação de avaliação de conformidade sob demanda de seu [fluxo de trabalho do GitHub](https://docs.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) em um ou vários recursos, grupos de recursos ou assinaturas e alterar o caminho do fluxo de trabalho com base no estado de conformidade desses recursos. Você também pode configurar o fluxo de trabalho para ser executado em um horário agendado para obter o último status de conformidade em um horário conveniente. Opcionalmente, essa ação do GitHub também pode gerar um relatório sobre o estado de conformidade de recursos verificados para análise posterior ou arquivamento.

O exemplo a seguir executa uma verificação de conformidade para uma assinatura. 

```yaml

on:
  schedule:    
    - cron:  '0 8 * * *'  # runs every morning 8am
jobs:
  assess-policy-compliance:    
    runs-on: ubuntu-latest
    steps:         
    - name: Login to Azure
      uses: azure/login@v1
      with:
        creds: ${{secrets.AZURE_CREDENTIALS}} 

    
    - name: Check for resource compliance
      uses: azure/policy-compliance-scan@v0
      with:
        scopes: |
          /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

```

## <a name="review"></a>Revisão

Neste tutorial, você realizou as seguintes tarefas com sucesso:

> [!div class="checklist"]
> - Exportou atribuições e definições de política para o GitHub
> - Enviou por push para o Azure objetos de política atualizados no GitHub
> - Disparou uma verificação de conformidade da ação do GitHub

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as estruturas das definições de políticas, consulte este artigo:

> [!div class="nextstepaction"]
> [Estrutura de definição do Azure Policy](../concepts/definition-structure.md)