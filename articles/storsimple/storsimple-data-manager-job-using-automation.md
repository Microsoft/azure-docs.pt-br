---
title: "Usar a Automação do Azure para disparar um trabalho | Microsoft Docs"
description: "Saiba como usar a Automação do Azure para disparar trabalhos do StorSimple Data Manager (versão prévia particular)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/16/2017
ms.author: vidarmsft
ms.openlocfilehash: 9691408bcd80afb6eba534f26749b76dd3bfe315
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-automation-to-trigger-a-job-private-preview"></a>Usar a Automação do Azure para disparar um trabalho (Versão prévia particular)

Este artigo descreve como usar a Automação do Azure para disparar um trabalho do StorSimple Data Manager.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você tem:

*   Azure Powershell instalado. [Baixar o Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   Definições de configuração para inicializar o trabalho de Transformação de Dados (as instruções para obter essas configurações estão incluídas aqui).
*   Uma definição de trabalho que foi configurada corretamente em um Recurso de Dados Híbridos dentro de um grupo de recursos.
*   Baixe o arquivo [zip](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) `DataTransformationApp.zip` no repositório GitHub.
*   Baixe o [script](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Get-ConfigurationParams.ps1) `Get-ConfigurationParams.ps1` no repositório GitHub.
*   Baixe o [script](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) `Trigger-DataTransformation-Job.ps1` no repositório GitHub.

## <a name="step-by-step"></a>Passo a passo

### <a name="get-azure-active-directory-permissions-for-the-automation-job-to-run-the-job-definition"></a>Obter permissões do Azure Active Directory para que o trabalho de automação execute a definição de trabalho

1. Para recuperar os parâmetros de configuração do Active Directory, realize as seguintes as etapas:

    1. Abra o Windows PowerShell no computador local. Verifique se o [Azure PowerShell](https://azure.microsoft.com/downloads/) está instalado.
    1. Execute o script `Get-ConfigurationParams.ps1` (na pasta baixada acima). Digite o seguinte comando na janela do PowerShell:

        ```
        .\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```

        A ActiveDirectoryKey é uma senha que será usada mais tarde. Insira uma senha de sua escolha. AppName pode ser qualquer cadeia de caracteres.

2. Esse script gera os valores a seguir, que devem ser usados ao disparar o runbook de automação. Anote esses valores.

    - Id do Cliente
    - ID do locatário
    - Chave do Active Directory (a mesma inserida acima)
    - ID da assinatura

### <a name="set-up-the-automation-account"></a>Configurar a conta de automação

1. Faça logon no Azure e abra sua conta de Automação.
2. Clique no bloco **Ativos** para abrir a lista de ativos.
3. Clique no bloco **Módulos** para abrir a lista de módulos.
4. Clique no botão **+ Adicionar um módulo** e a folha Adicionar módulo é iniciada.

    ![Configurações da conta de automação](./media/storsimple-data-manager-job-using-automation/add-module1m.png)

5. Depois de selecionar o arquivo `DataTransformationApp.zip` no computador local, clique em **OK** para importar o módulo.

   Quando a Automação do Azure importa um módulo para sua conta, ele extrai os metadados sobre o módulo. Essa operação pode levar alguns minutos.

   ![Configurações da conta de automação](./media/storsimple-data-manager-job-using-automation/add-module2m.png)

   

6. Você recebe uma notificação informando que o módulo está sendo implantado e outra notificação quando o processo é concluído.  Você também pode verificar o status no bloco **Módulos**.

### <a name="to-import-the-runbook-that-triggers-the-job-definition"></a>Para importar o runbook que dispara a definição de trabalho

1. No portal do Azure, abra sua conta da Automação.
2. Clique no bloco **Runbooks** para abrir a lista de runbooks.
3. Clique em **+ Adicionar um runbook** e, em seguida, em **Importar um runbook existente**.

   ![Importar um runbook existente](./media/storsimple-data-manager-job-using-automation/import-a-runbook.png)

4. Clique em **Arquivo de runbook** para selecionar o arquivo para importar `Trigger-DataTransformation-Job.ps1`.
5. Clique em **Criar** para importar o runbook. O novo runbook é exibido na lista de runbooks da conta de Automação.
7. Clique no runbook **Trigger-DataTransformation-Job** e, em seguida, em **Editar**.
8. Clique em **Publicar** e em **Sim** quando solicitado para confirmação.


### <a name="to-run-the-runbook"></a>Para executar o runbook:
1. No portal do Azure, abra sua conta da Automação.
2. Clique no bloco **Runbooks** para abrir a lista de runbooks.
3. Clique em **Trigger-DataTransformation-Job**.
4. Clique em **Iniciar** para iniciar o runbook.

   ![Iniciar runbook](./media/storsimple-data-manager-job-using-automation/run-runbook1m.png)

5. Na folha **Iniciar runbook**, insira todos os parâmetros. Clique em **OK** para enviar o trabalho de Transformação de Dados.

   ![Iniciar runbook](./media/storsimple-data-manager-job-using-automation/run-runbook2m.png)


## <a name="next-steps"></a>Próximas etapas

[Use a interface do usuário do Gerenciador de Dados StorSimple para transformar seus dados](storsimple-data-manager-ui.md).