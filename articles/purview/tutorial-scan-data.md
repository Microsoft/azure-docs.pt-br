---
title: 'Tutorial: Examinar dados com o Azure Purview (versão prévia)'
description: Neste tutorial, você executará um kit de início para configurar um acervo de dados e examinar dados das fontes de dados no catálogo do Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 16692ac75f0ab6df0c8ee1bebef393848ca066b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676537"
---
# <a name="tutorial-scan-data-with-azure-purview-preview"></a>Tutorial: Examinar dados com o Azure Purview (versão prévia)

> [!IMPORTANT]
> Atualmente, o Azure Purview está em VERSÃO PRÉVIA. Os [Termos de Uso Complementares para Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluem termos legais adicionais que se aplicam aos recursos do Azure que estão em versão beta, versão prévia ou que, de outra forma, ainda não foram lançados em disponibilidade geral.

Nesta *série de tutoriais de cinco partes*, você aprenderá os conceitos básicos de como gerenciar a governança de dados em um acervo de dados usando o Azure Purview (versão prévia). O acervo de dados criado nesta parte do tutorial é usado para o restante da série.

Na Parte 1 desta série de tutoriais, você vai:

> [!div class="checklist"]
>
> * Criar um acervo de dados com vários recursos de dados do Azure.
> * Examinar dados em um catálogo.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
* Uma [conta do Azure Purview](create-catalog-portal.md).
* [O kit de início](https://github.com/Azure/Purview-Samples/blob/master/PurviewStarterKitV4.zip) que implantará seu acervo de dados.

> [!NOTE]
> O kit de início só está disponível para o Windows.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="create-a-data-estate"></a>Criar um acervo de dados

Nesta seção, você executará os scripts do kit de início para criar um acervo de dados simulado. Um acervo de dados é um portfólio de todos os dados que uma empresa têm. O script do kit de início executa as seguintes ações:

* Cria uma conta do Armazenamento de Blobs do Azure e preenche a conta com alguns dados.
* Cria uma conta do Azure Data Lake Storage Gen2.
* Cria uma instância do Azure Data Factory e associa a instância ao Azure Purview.
* Configura e dispara um pipeline de atividade de cópia entre o Armazenamento de Blobs do Azure e as contas do Azure Data Lake Storage Gen2.
* Envia por push a linhagem associada do Azure Data Factory para o Azure Purview.

### <a name="prepare-to-run-the-starter-kit"></a>Preparar a execução do kit de início

Siga estas etapas para configurar o software cliente do kit de início no computador Windows:

1. [Baixe o kit de início](https://github.com/Azure/Purview-Samples/blob/master/PurviewStarterKitV4.zip) e extraia o conteúdo dele para a localização de sua escolha.


1. No computador, insira **PowerShell** na caixa de pesquisa da barra de tarefas do Windows. Na lista de pesquisa, clique com o botão direito do mouse em **Windows PowerShell** e selecione **Executar como administrador**.

1. Na janela do PowerShell, insira o comando a seguir, substituindo `<path-to-starter-kit>` pelo caminho da pasta dos arquivos extraídos do kit de início.

   ```powershell
   dir -Path <path-to-starter-kit> -Recurse | Unblock-File
   ```

1. Insira o comando a seguir para instalar os cmdlets do Azure.

   ```powershell
   Install-Module -Name Az -AllowClobber -Scope CurrentUser
   ```

1. Se você vir o prompt de aviso *O provedor do NuGet é necessário para continuar*, insira **Y** e clique em ENTER.

1. Se você vir o prompt de aviso *Repositório não confiável*, insira **A** e clique em ENTER.

Pode levar até um minuto para que o PowerShell instale os módulos necessários.

### <a name="collect-data-needed-to-run-the-scripts"></a>Coletar os dados necessários para executar os scripts

Antes de executar os scripts do PowerShell para inicializar o catálogo, obtenha os valores dos seguintes argumentos a serem usados nos scripts:

* TenantID
   1. No [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory**.
   1. Na seção **Gerenciar** do painel de navegação à esquerda, selecione **Propriedades**. Em seguida, selecione o ícone de cópia de **ID de Locatário** para salvar o valor na área de transferência. Cole o valor em um editor de texto para uso posterior.

* SubscriptionID
   1. No portal do Azure, procure e selecione o nome da instância do Azure Purview que você criou como um pré-requisito.
   1. Selecione a seção **Visão geral** e salve o GUID da **ID da Assinatura**.

   > [!NOTE]
   > - Verifique se você está usando a mesma assinatura que aquela na qual criou a conta do Azure Purview. Essa é a mesma assinatura que foi colocada na lista de permissões.
   > - Às vezes, a linhagem pode estar ausente no Azure Purview após a execução do kit de início. Isso ocorre porque o Data Factory criado pelo kit de início tem permissões ausentes no Purview. Selecione [**este link de documento**](how-to-link-azure-data-factory.md#view-existing-data-factory-connections) para garantir que o Data Factory esteja configurado corretamente e tenha recebido a função apropriada no Purview


* CatalogName: o nome da conta do Azure Purview que você criou em [Criar uma conta do Azure Purview](create-catalog-portal.md).

* CatalogResourceGroupName: o nome do grupo de recursos no qual você criou sua conta do Azure Purview.

### <a name="verify-permissions"></a>Verificar permissões

Siga estas etapas para adicionar o usuário que executa o script à conta do Azure Purview que foi criada como um pré-requisito. Os usuários precisam das funções *Curador de Dados do Purview* e *Administrador de Fonte de Dados do Purview*. 

Se você tiver criado a conta do Azure Purview por conta própria, receberá acesso automaticamente e poderá ignorar esta seção.

1. Acesse a página Contas do Purview no portal do Azure e selecione a conta do Azure Purview que deseja modificar.

1. Na página da conta, selecione a guia **Controle de acesso (IAM)** e **+ Adicionar**.

1. Selecione **Adicionar atribuição de função**.

1. Insira **Função de Curador de Dados do Purview** para a *Função*.
 
1. Use o valor padrão para *Atribuir acesso a*. O valor padrão deve ser **Usuário, grupo ou entidade de serviço**.

1. Insira o nome do usuário que está executando o script em **Selecionar**.

1. Clique em **Salvar**.

1. Repita as etapas anteriores com a *Função* definida como **Função de Administrador de Fonte de Dados do Purview**.

Para obter mais informações, confira [Permissões do Catálogo](catalog-permissions.md).

### <a name="run-the-client-side-setup-scripts"></a>Executar os scripts de instalação do lado do cliente

Depois que a configuração do catálogo for concluída, execute os scripts a seguir na janela do PowerShell para criar os ativos, substituindo os espaços reservados pelos valores coletados anteriormente.

1. Use o comando a seguir para navegar até o diretório do kit de início. Substitua `path-to-starter-kit` pelo caminho da pasta do arquivo extraído.

   ```powershell
   cd <path-to-starter-kit>
   ```

1. O comando a seguir define a política de execução para o computador local. Insira **A** para *Sim para Todos* quando precisar alterar a política de execução.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted
   ```

1. Conecte-se ao Azure usando o comando a seguir. Substitua os espaços reservados `TenantID` e `SubscriptionID` .

   ```powershell
   .\RunStarterKit.ps1 -ConnectToAzure -TenantId <TenantID> `
   -SubscriptionId <SubscriptionID>
   ```

   Quando você executar o comando, uma janela pop-up poderá ser exibida para que você entre usando suas credenciais do Azure Active Directory.


1. Use o comando a seguir para executar o kit de início. Substitua os espaços reservados `CatalogName`, `TenantID`, `SubscriptionID`, `NewResourceGroupName` e `CatalogResourceGroupName`. Em `NewResourceGroupName`, use um nome exclusivo (somente com caracteres alfanuméricos minúsculos) para o grupo de recursos que conterá o acervo de dados.

   > [!IMPORTANT]
   > O **newresourcegroupname** usa apenas números e letras minúsculas e precisa ter menos de 17 caracteres. **Não são permitidos caracteres especiais nem letras maiúsculas.** Essa restrição vem das regras de nomenclatura da conta de armazenamento.

   ```powershell
   .\RunStarterKit.ps1 -CatalogName <CatalogName> -TenantId <TenantID>`
   -ResourceGroup <newresourcegroupname> `
   -SubscriptionId <SubscriptionID> `
   -CatalogResourceGroup <CatalogResourceGroupName>
   ```

Pode levar até 10 minutos para que o ambiente seja configurado. Durante esse tempo, você poderá ver várias janelas pop-up, que podem ser ignoradas. Não feche a janela do **BlobDataCreator.exe**; ela será fechada automaticamente quando for concluída.

Quando você vir a mensagem `Executing Copy pipeline xxxxxxxxxx-487e-4fc4-9628-92dd8c2c732b`, aguarde outra instância do **BlobDataCreator.exe** iniciar e concluir a execução.

> [!IMPORTANT]
> Caso o 'Número de tarefas ativas' pare de diminuir, saia da janela do criador de blob e clique em ENTER na janela do PowerShell

Depois que o processo for concluído, um grupo de recursos com o nome fornecido será criado. As contas do Azure Data Factory, do Armazenamento de Blobs do Azure e do Azure Data Lake Storage Gen2 estão todas contidas nesse grupo de recursos. O grupo de recursos está contido na assinatura que você especificou.

## <a name="scan-data-into-the-catalog"></a>Examinar dados no catálogo

A verificação é um processo pelo qual o catálogo se conecta diretamente a uma fonte de dados de acordo com uma agenda especificada pelo usuário. O catálogo reflete o acervo de dados de uma empresa por meio de verificação, da linhagem, do portal e da API. As metas incluem examinar o que está dentro, extrair esquemas e tentar entender a semântica. Nesta seção, você vai configurar uma verificação das fontes de dados geradas com o kit de início.

O script do kit de início que você executou criou duas fontes de dados: o Armazenamento de Blobs do Azure e o Azure Data Lake Storage Gen2. Examine essas fontes de dados no catálogo, uma de cada vez.

### <a name="authenticate-to-your-storage-with-managed-identity"></a>Autenticar-se no seu armazenamento com a identidade gerenciada

Uma identidade gerenciada com o mesmo nome da sua conta do Azure Purview é criada automaticamente quando a sua conta é criada. Para examinar seus dados, conceda permissões de função do Azure Purview nas suas contas de armazenamento.

1. Acesse o grupo de recursos criado pelo kit de início e selecione sua conta do Armazenamento de Blobs.

1. Selecione **Controle de Acesso (IAM)** no menu de navegação à esquerda. Em seguida, selecione **+ Adicionar**.

1. Defina a Função como **Leitor de Dados do Storage Blob** e insira o nome da sua conta do Azure Purview em **Selecionar**. Em seguida, selecione **Salvar** para fornecer essa atribuição de função à sua conta do Purview.

   :::image type="content" source="media/tutorial-scan-data/add-role-assignment.png" alt-text="Adicionar atribuição de função":::

1. Repita as etapas anteriores para o Azure Data Lake Storage Gen2.

### <a name="scan-your-data-sources"></a>Examinar suas fontes de dados

1. Procure o recurso do Azure Purview no [portal do Azure](https://portal.azure.com) e selecione *Abrir o Purview Studio*. Você é direcionado automaticamente para a home page do Purview Studio.

1. Selecione **Fontes** na página da Web do catálogo e escolha **Registrar**. Escolha **Armazenamento de Blobs do Azure** e **Continuar**.

   :::image type="content" source="media/tutorial-scan-data/add-blob-storage.png" alt-text="Registrar recurso do Armazenamento de Blobs":::

1. Na página **Registrar fontes**, insira um **Nome**. Escolha o **Nome da conta de armazenamento** da conta do Armazenamento de Blobs do Azure criada anteriormente com o kit de início. A conta tem o nome `<YourResourceGroupName>adcblob`. Selecione **Concluir**.

   :::image type="content" source="./media/tutorial-scan-data/register-azure-blob-storage.png" alt-text="Captura de tela mostrando as configurações necessárias para registrar uma fonte de dados do Armazenamento de Blobs do Azure." border="true":::

1. Na exibição de mapa **Fonte de dados**, selecione **Nova verificação** no bloco do Armazenamento de Blobs do Azure.

   :::image type="content" source="./media/tutorial-scan-data/select-setup-scan.png" alt-text="Captura de tela mostrando como selecionar uma configuração de verificação por meio de uma fonte de dados." border="true":::

1. Na página **Examinar**, insira um nome de verificação, selecione sua identidade gerenciada na lista suspensa **Credenciais** e **Continuar**.

   :::image type="content" source="media/tutorial-scan-data/scan-blob.png" alt-text="Examinar o Armazenamento de Blobs no Azure Purview":::

1. Restrinja o escopo da verificação a blobs individuais. Neste tutorial, mantenha todos os ativos marcados para examinar tudo e selecione **Continuar**.

   :::image type="content" source="media/tutorial-scan-data/scope-your-scan.png" alt-text="Definir o escopo da verificação de armazenamento":::

1. Selecione o conjunto de regras de verificação padrão e **Continuar**.

1. Configure um gatilho de verificação para as verificações recorrentes. Para este tutorial, selecione **Uma vez** e **Continuar**.

1. Escolha **Salvar e executar** para concluir a verificação.

1. Repita as etapas anteriores para examinar sua conta do Azure Data Lake Storage Gen2.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:
> [!div class="checklist"]
>
> * Execute o script do kit de início para configurar um ambiente de acervo de dados.
> * Examine os dados de um catálogo do Azure Purview.

Prossiga para o próximo tutorial para saber como navegar pela home page e procurar um ativo.

> [!div class="nextstepaction"]
> [Navegar pela home page e procurar um ativo](tutorial-asset-search.md)
