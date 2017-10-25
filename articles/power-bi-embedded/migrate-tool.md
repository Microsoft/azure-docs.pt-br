---
title: "Usar a ferramenta de migração do Power BI Embedded | Microsoft Docs"
description: "A ferramenta de migração do Power BI Embedded pode ser usada para copiar seus relatórios das Coleções de Espaços de trabalho do Power BI para o Power BI Embedded."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: 0b7b5089045daf6dd88fcd84e316b2bd44f8c927
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-power-bi-embedded-migration-tool"></a>Usar a ferramenta de migração do Power BI Embedded

A ferramenta de migração do Power BI Embedded pode ser usada para copiar seus relatórios das Coleções de Espaços de trabalho do Power BI para o Power BI Embedded.

A migração do conteúdo de suas coleções de espaços de trabalho para o serviço do Power BI pode ser executada em paralelo à sua solução atual e não requer nenhum tempo de inatividade.

## <a name="limitations"></a>Limitações

* Conjuntos de dados enviados por push não podem ser baixados e precisarão ser recriado usando as APIs REST do Power BI para o serviço do Power BI.
* Arquivos PBIX importados antes de 26 de novembro de 2016 não estarão poderão ser baixados.

## <a name="download"></a>Baixar

Você pode baixar o exemplo de ferramenta de migração do [GitHub](https://github.com/Microsoft/powerbi-migration-sample). Você pode baixar um zip do repositório ou cloná-lo localmente. Após o download, você pode abrir *powerbi-migration-sample.sln* dentro do Visual Studio para compilar e executar a ferramenta de migração.

## <a name="migration-plans"></a>Planos de migração

O plano de migração são metadados que catalogam o conteúdo dentro de Coleções de Espaços de Trabalho do Power BI e como você deseja publicá-lo no Power BI Embedded.

### <a name="start-with-a-new-migration-plan"></a>Começar com um novo plano de migração

Um plano de migração são os metadados dos itens disponíveis em Coleções de Espaços de Trabalho do Power BI que você deseja mover para o Power BI Embedded. O plano de migração é armazenado como um arquivo XML.

Você desejará começar criando um novo plano de migração. Para criar um novo plano de migração, faça o seguinte.

1. Selecione **Arquivo** > **New Migration Plan (Novo Plano de Migração)**.

    ![Novo Plano de Migração](media/migrate-tool/migrate-tool-plan.png)

2. Na caixa de diálogo **Select Power BI Workspace Collections Resource Group (Selecionar Grupo de Recursos de Coleções de Espaços de Trabalho do Power BI)**, você deve selecionar a lista suspensa **Ambiente** e selecionar o de produção.

3. Você será solicitado a entrar. Você usará o logon de assinatura do Azure.

    > [!IMPORTANT]
    > Ele **não** é sua conta de organização do Office 365 com a qual você entra no Power BI.

4. Selecione a assinatura do Azure que armazena o recurso de Coleções de Espaços de Trabalho do Power BI.

    ![Selecionar sua assinatura do Azure](media/migrate-tool/migrate-tool-select-resource-group.png)

5. Abaixo da lista de assinatura, selecione o **Grupo de Recursos** que contém as coleções de espaços de trabalho e selecione **Selecionar**.

    ![Selecionar o grupo de recursos](media/migrate-tool/migrate-tool-select-resource-group2.png)

6. Selecione **Analisar**. Isso obterá um inventário dos itens dentro de sua assinatura do Azure para você começar seu plano.

    ![Selecionar o botão Analisar](media/migrate-tool/migrate-tool-analyze-group.png)

    > [!NOTE]
    > O processo de análise pode levar vários minutos dependendo do número de coleções de espaços de trabalho e da quantidade de conteúdo existente na coleção de espaços de trabalho.

7. Quando a ação de **Analisar** for concluída, será solicitado que você salve o plano de migração.

Neste ponto, você se conectou seu plano de migração à sua assinatura do Azure. Leia abaixo para entender o fluxo de como trabalhar com seu plano de migração. Isso inclui analisar e planejar a migração, baixar, criar grupos e carregar.

### <a name="save-your-migration-plan"></a>Salvar o plano de migração

Você pode salvar o plano de migração para uso posterior. Isso criará um arquivo XML que contém todas as informações em seu plano de migração.

Para salvar o plano de migração, faça o seguinte.

1. Selecione **Arquivo** > **Save Migration Plan (Salvar Plano de Migração)**.

    ![Opção de menu para salvar o plano de migração](media/migrate-tool/migrate-tool-save-plan.png)

2. Nomeie o arquivo ou use o nome de arquivo gerado e selecione **Salvar**.

### <a name="open-an-existing-migration-plan"></a>Abrir um plano de migração existente

Você pode abrir um plano de migração salvo para continuar a trabalhar na migração.

Para abrir o plano de migração existente, faça o seguinte.

1. Selecione **Arquivo** > **Open Existing Migration Plan (Abrir Plano de Migração Existente)**.

    ![Opção de menu para abrir plano de migração existente](media/migrate-tool/migrate-tool-open-plan.png)

2. Selecione o arquivo de migração e selecione **Abrir**.

## <a name="step-1-analyze-and-plan-migration"></a>Etapa 1: analisar e planejar a migração

A guia **Analyze & Plan Migration (Analisar e Planejar Migração)** fornece uma exibição do que está atualmente no grupo de recursos da sua assinatura do Azure.

![Guia Analyze & Plan Migration (Analisar e Planejar Migração)](media/migrate-tool/migrate-tool-step1.png)

Vamos examinar *SampleResourceGroup* como exemplo.

### <a name="paas-topology"></a>Topologia de PaaS

Essa é uma lista de seu *Grupo de Recursos > Coleções de espaços de trabalho > Espaços de Trabalho*. As coleções de espaços de trabalho e o grupo de recursos mostrarão um nome amigável. Os espaços de trabalho mostrarão um GUID.

Os itens na lista também exibirão uma cor e um número no formato (#/#). Isso indica o número de relatórios que podem ser baixados.

Uma cor preta significa que todos os relatórios podem ser baixados. Uma cor vermelha significa que alguns relatórios não podem ser baixados. O número à esquerda indicará o número total de relatórios que podem ser baixados. O número à direita indica o número total de relatórios do agrupamento.

Você pode selecionar um item dentro da topologia de PaaS para exibir os relatórios na seção de relatórios.

### <a name="reports"></a>Relatórios

A seção de relatórios listará os relatórios disponíveis e indicará se eles podem ser baixados ou não.

![Lista de relatórios nas Coleções de Espaços de Trabalho do Power BI](media/migrate-tool/migrate-tool-analyze-reports.png)

### <a name="target-structure"></a>Target structure (Estrutura de destino)

A **Target structure (Estrutura de destino)** é onde você indica para a ferramenta o local em que os itens serão baixados e como carregá-los.

#### <a name="download-plan"></a>Download plan (Plano de download)

Um caminho será criado automaticamente para você. Se desejar, você pode alterar esse caminho. Se você alterar o caminho, precisará selecionar **Update paths (Atualizar caminhos)**.

**Isso não realiza o download realmente.** Isso apenas especifica a estrutura do local para o qual os relatórios serão baixados.

#### <a name="upload-plan"></a>Upload plan (Plano de upload)

Aqui você pode especificar um prefixo a ser usado para os espaços de trabalho do aplicativo que serão criados no serviço do Power BI. Depois o prefixo será o GUID para o espaço de trabalho existente no Azure.

![Especificar o prefixo do nome do grupo](media/migrate-tool/migrate-tool-upload-plan.png)

**Isso não cria realmente os grupos no serviço do Power BI.** Isso apenas define a estrutura de nomenclatura para os grupos.

Se você alterar o prefixo, precisará selecionar **Generate Upload Plan (Gerar Plano de upload)**.

Você pode clicar com o botão direito do mouse em um grupo e escolher renomeá-lo no plano de upload diretamente, se desejado.

![Opção do menu de contexto Rename group (Renomear grupo)](media/migrate-tool/migrate-tool-upload-report-rename-item.png)

> [!NOTE]
> O nome do *Grupo* não deve conter espaços ou caracteres inválidos.

## <a name="step-2-download"></a>Etapa 2: baixar

Na guia **Baixar**, você verá a lista de relatórios e metadados associados. Você pode ver que status de exportação está junto com o status de exportação anterior.

![Guia Baixar](media/migrate-tool/migrate-tool-download-tab.png)

Você tem duas opções.

* Selecione relatórios específicos e selecione **Baixar Selecionados**
* Selecione **Baixar Tudo**.

![Botão Baixar Selecionados](media/migrate-tool/migrate-tool-download-options.png)

Para um download bem-sucedido, você verá um status de *Concluído* e ele refletirá que o arquivo PBIX existe.

Depois que o download for concluído, selecione a guia **Criar Grupos**.

## <a name="step-3-create-groups"></a>Etapa 3: criar grupos

Depois de ter baixado os relatórios que estão disponíveis, você pode ir para a guia **Criar Grupos**. Esta guia criará os espaços de trabalho do aplicativo no serviço do Power BI com base no plano de migração que você criou. Ele criará o espaço de trabalho do aplicativo com o nome fornecido na guia **Carregar** em **Analyze & Plan Migration (Analisar e Planejar Migração)**.

![Guia Criar Grupos](media/migrate-tool/migrate-tool-create-groups.png)

Para criar os espaços de trabalho do aplicativo, você pode selecionar **Create Selected Groups (Criar Grupos Selecionados)** ou **Create All Missing Groups (Criar Todos os Grupos Ausentes)**.

Quando você selecionar uma dessas opções, será solicitado a entrar. *Você desejará usar suas credenciais do serviço do Power BI no qual deseja criar os espaços de trabalho de aplicativo.*

![Tela de entrada do Power BI](media/migrate-tool/migrate-tool-create-group-sign-in.png)

Isso criará o espaço de trabalho do aplicativo no serviço do Power BI. Isso não carrega os relatórios para o espaço de trabalho do aplicativo.

Você pode verificar que o espaço de trabalho do aplicativo foi criado entrando no Power BI e validando se o espaço de trabalho existe. Você observará que não há nada no espaço de trabalho.

![Espaço de trabalho do aplicativo no serviço do Power BI](media/migrate-tool/migrate-tool-app-workspace.png)

Depois que o espaço de trabalho é criado, você pode passar para a guia **Carregar**.

## <a name="step-4-upload"></a>Etapa 4: carregar

Na guia **Carregar**, isso carregará os relatórios para o serviço do Power BI. Você verá uma lista dos relatórios que baixamos na guia baixar junto com o nome do grupo de destino com base em seu plano de migração.

![Guia Carregar](media/migrate-tool/migrate-tool-upload-tab.png)

Você pode carregar relatórios selecionados ou carregar todos os relatórios. Você também pode redefinir o status de upload para carregar novamente os itens.

Você também tem a opção de selecionar o que fazer se houver um relatório com o mesmo nome. Você pode escolher entre **Anular**, **Ignorar** e **Substituir**.

![Lista suspensa de opções do que fazer se o relatório existir](media/migrate-tool/migrate-tool-upload-report-same-name.png)

![Carregar resultados selecionados](media/migrate-tool/migrate-tool-upload-selected.png)

### <a name="duplicate-report-names"></a>Nomes de relatório duplicados

Se você tiver um relatório que tem o mesmo nome, mas sabe que é um relatório diferente, precisará alterar o **TargetName** do relatório. Você pode alterar o nome editando manualmente o XML do plano de migração.

Você precisará fechar a ferramenta de migração para fazer a alteração e, em seguida, abrir a ferramenta e o plano de migração novamente.

No exemplo acima, um dos relatórios clonados falhou ao indicar que existia um relatório com o mesmo nome. Se formos analisar o XML do plano de migração, veremos o seguinte.

```
<ReportMigrationData>
    <PaaSWorkspaceCollectionName>SampleWorkspaceCollection</PaaSWorkspaceCollectionName>
    <PaaSWorkspaceId>4c04147b-d8fc-478b-8dcb-bcf687149823</PaaSWorkspaceId>
    <PaaSReportId>525a8328-b8cc-4f0d-b2cb-c3a9b4ba2efe</PaaSReportId>
    <PaaSReportLastImportTime>1/3/2017 2:10:19 PM</PaaSReportLastImportTime>
    <PaaSReportName>cloned</PaaSReportName>
    <IsPushDataset>false</IsPushDataset>
    <IsBoundToOldDataset>false</IsBoundToOldDataset>
    <PbixPath>C:\MigrationData\SampleResourceGroup\SampleWorkspaceCollection\4c04147b-d8fc-478b-8dcb-bcf687149823\cloned-525a8328-b8cc-4f0d-b2cb-c3a9b4ba2efe.pbix</PbixPath>
    <ExportState>Done</ExportState>
    <LastExportStatus>OK</LastExportStatus>
    <SaaSTargetGroupName>SampleMigrate</SaaSTargetGroupName>
    <SaaSTargetGroupId>6da6f072-0135-4e6c-bc92-0886d8aeb79d</SaaSTargetGroupId>
    <SaaSTargetReportName>cloned</SaaSTargetReportName>
    <SaaSImportState>Failed</SaaSImportState>
    <SaaSImportError>Report with the same name already exists</SaaSImportError>
</ReportMigrationData>
```

Para o item com falha, podemos alterar o nome do SaaSTargetReportName.

```
<SaaSTargetReportName>cloned2</SaaSTargetReportName>
```

Podemos, em seguida, abrir o plano novamente, na ferramenta de migração e carregar o relatório com falha.

Voltando ao Power BI, podemos ver que os relatórios e os conjuntos de dados foram carregados no espaço de trabalho do aplicativo.

![Lista de relatórios no serviço do Power BI](media/migrate-tool/migrate-tool-upload-app-workspace.png)

<a name="upload-local-file"></a>
### <a name="upload-a-local-pbix-file"></a>Carregar um arquivo PBIX local

Você pode carregar uma versão local de um arquivo do Power BI Desktop. Você terá que fechar a ferramenta, editar o XML e colocar o caminho completo para o PBIX local na propriedade **PbixPath**.

```
<PbixPath>[Full Path to PBIX file]</PbixPath>
```

Depois de editar o XML, abra novamente o plano de dentro da ferramenta de migração e carregue o relatório.

<a name="directquery-reports"></a>
### <a name="directquery-reports"></a>Relatórios de DirectQuery

Você precisará atualizar para atualizar a cadeia de conexão para relatórios de DirectQuery. Isso pode ser feito em *powerbi.com* ou você pode consultar programaticamente a cadeia de conexão do Power BI Embedded (PaaS). Para obter um exemplo, consulte [Extrair uma cadeia de conexão do DirectQuery do relatório de PaaS](migrate-code-snippets.md#extract-directquery-connection-string-from-power-bi-workspace-collections).

Em seguida, você pode atualizar a cadeia de conexão para o conjunto de dados no serviço do Power BI e definir as credenciais da fonte de dados. Você pode analisar os exemplos a seguir para ver como fazer isso.

* [Atualizar a cadeia de conexão do DirectQuery no Power BI Embedded](migrate-code-snippets.md#update-directquery-connection-string-in-power-bi-embedded)
* [Definir as credenciais do DirectQuery no Power BI Embedded](migrate-code-snippets.md#set-directquery-credentials-in-power-bi-embedded)

## <a name="next-steps"></a>Próximas etapas

Agora que seus relatórios foram migrados de Coleções de Espaços de Trabalho do Power BI para o Power BI Embedded, você pode atualizar seu aplicativo e começar a inserir os relatórios no espaço de trabalho deste aplicativo.

Para obter mais informações, consulte [Como migrar o conteúdo da Coleção de Espaços de Trabalho do Power BI para o Power BI Embedded](migrate-from-power-bi-workspace-collections.md).

Mais perguntas? [Experimente perguntar para a Comunidade do Power BI](http://community.powerbi.com/)