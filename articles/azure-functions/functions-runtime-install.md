---
title: "Instalação do Azure Functions Runtime | Microsoft Docs"
description: Como Instalar o Azure Functions Runtime
services: functions
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/08/2017
ms.author: anwestg
ms.openlocfilehash: 1e4188313a87d07f396e5f8edc8969dd5da2c436
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="install-the-azure-functions-runtime-preview"></a>Instalar a visualização do Azure Functions Runtime

Se você quiser instalar a visualização do Azure Functions Runtime, execute estas etapas:

1. Certifique-se de que sua máquina atenda aos requisitos mínimos
1. Baixe o [Instalador da visualização do Azure Functions Runtime](https://aka.ms/azafr). 
1. Instalar a visualização do Azure Functions Runtime
1. Concluir a configuração da visualização do Azure Functions Runtime

## <a name="prerequisites"></a>Pré-requisitos

Antes de instalar a visualização do Azure Functions Runtime, você deve ter o seguinte:

1. Um computador executando o Microsoft Windows Server 2016 ou a Atualização do Microsoft Windows 10 para Criadores (Professional ou Enterprise Edition).
1. Uma instância do SQL Server em execução em sua rede.  O requisito mínimo de edição é o SQL Server Express.

## <a name="install-the-azure-functions-runtime-preview"></a>Instalar a visualização do Azure Functions Runtime

O instalador da versão prévia do Azure Functions Runtime orienta você durante a instalação das Funções de Gerenciamento e de Trabalho da versão prévia do Azure Functions Runtime.  É possível instalar as Funções de Gerenciamento e de Trabalho no mesmo computador.  No entanto, à medida que você adiciona mais funções, é necessário implantar mais funções de trabalho em computadores adicionais para poder escalar suas funções para vários trabalhadores.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>Instalar as Funções de Gerenciamento e de Trabalho no mesmo computador

1. Execute o Instalador da visualização do Azure Functions Runtime.

    ![Instalador da visualização do Azure Functions Runtime][1]

1. **Clique em Avançar** para passar para o primeiro estágio do instalador
1. Depois de ler os termos do **EULA**, **marque a caixa** para aceitar os termos e **clique em Avançar** para avançar.
1. Agora, selecione as funções que você deseja instalar neste computador **Função de Gerenciamento do Functions** e/ou **Função de Trabalho do Functions** e **clique em Avançar**

    ![Instalador da visualização do Azure Functions Runtime - Seleção de função][3]

    > [!NOTE]
    > Você pode instalar a **Função de Trabalho do Functions** em muitas outras máquinas. Para fazer isso, siga estas instruções e selecione somente a **Função de Trabalho do Functions** no instalador.

1. **Clique em Avançar** para instalar o **Instalador do Azure Functions Runtime** em seu computador.
1. Após a conclusão, o instalador iniciará a **ferramenta de Configuração do Azure Functions Runtime**.

    ![Instalador da visualização do Azure Functions Runtime concluída][5]

    > [!NOTE]
    > Se você estiver instalando no **Windows 10** e o recurso **Contêiner** não tiver sido habilitado, o instalador do **Azure Functions Runtime** solicitará a reinicialização do computador para concluir a instalação.

## <a name="configure-the-azure-functions-runtime"></a>Configurar o Azure Functions Runtime

Para concluir a instalação do Azure Functions Runtime, você deverá concluir a configuração.

1. A **ferramenta de Configuração do Azure Functions Runtime** mostra quais funções estão instaladas em seu computador.

    ![Ferramenta de configuração da visualização do Azure Functions Runtime][6]

1. Clique na guia **Banco de Dados**, insira os **detalhes da conexão de sua instância do SQL Server** e **clique em Aplicar**.  Isso é necessário para que o Azure Functions Runtime crie um banco de dados para dar suporte ao Runtime.
    
    ![Configuração de banco de dados da visualização do Azure Functions Runtime][7]

1. Clique na guia **Credenciais**.  Nessa tela, você deve criar duas credenciais novas para uso com um compartilhamento de arquivos para hospedagem de todas as suas Azure Functions.  **Especifique combinações de nome de usuário e senha** para o **Proprietário do Compartilhamento de Arquivo** e para o **Usuário do Compartilhamento de Arquivo** e clique em **Aplicar**.

    ![Credenciais da visualização do Azure Functions Runtime][8]

1. Clique na guia **Compartilhamento de Arquivos**.  Nessa tela, você deve especificar os detalhes do **Local do Compartilhamento de Arquivo**.  Isso pode ser criado para você, ou você pode usar um Compartilhamento de Arquivo existente e clicar em **Aplicar**.  Se você selecionar um novo local de Compartilhamento de Arquivos, especifique um diretório para ser usado pelo Azure Functions Runtime.
    
    ![Compartilhamento de arquivo da visualização do Azure Functions Runtime][9]

1. Clique na guia **IIS**.  Essa guia mostra os detalhes dos sites no IIS que serão criados pela instalação do Azure Functions Runtime.  **Clique em Aplicar** para concluir.

    ![IIS da visualização do Azure Functions Runtime][10]

1. Clique na guia **Serviços**.  Essa guia mostra o status dos serviços em sua instalação do Azure Functions Runtime.  Se, após a configuração inicial, o **Serviço de Ativação de Host do Azure Functions** não estiver em execução, clique em **Iniciar Serviço**

    ![Configuração da visualização do Azure Functions Runtime completa][11]

1. Por fim, navegue até o **Portal do Azure Functions Runtime** como`https://<machinename>/`

    ![Portal de visualização do Azure Functions Runtime][12]


<!--Image references-->
[1]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer1.png
[2]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer2-EULA.png
[3]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer3-ChooseRoles.png
[4]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer4-Install.png
[5]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer5-InstallComplete.png
[6]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration1.png
[7]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration2_SQL.png
[8]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration3_Credentials.png
[9]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration4_Fileshare.png
[10]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration5_IIS.png
[11]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration6_Services.png
[12]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal.png