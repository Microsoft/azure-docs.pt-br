---
title: Usar o ReportViewer em um site da Web | Microsoft Docs
description: Este tópico descreve como criar um site da Web do Microsoft Azure com o controle ReportViewer do Visual Studio que exibe um relatório armazenado em uma máquina virtual do Microsoft Azure.
services: virtual-machines-windows
documentationcenter: na
author: markingmyname
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: 78b76318-d9bf-48ef-9d9e-d1b7d8cf3042
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/11/2017
ms.author: maghan
ms.openlocfilehash: b554dc1fa33519d87aa0c9c5ba9130b47cbea142
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60580061"
---
# <a name="use-reportviewer-in-a-web-site-hosted-in-azure"></a>Usar o ReportViewer em um site da Web hospedado no Azure
> [!IMPORTANT]
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos.

Você pode criar um site da Web do Microsoft Azure com o controle ReportViewer do Visual Studio que exibe um relatório armazenado em uma máquina virtual do Microsoft Azure. O controle ReportViewer é um aplicativo Web que você cria usando o modelo de aplicativo Web ASP.NET.

> [!IMPORTANT]
> Os modelos de aplicativo Web MVC ASP.NET não dão suporte ao controle ReportViewer.

Para incorporar o ReportViewer ao site da Web do Microsoft Azure, é necessário concluir as tarefas a seguir.

* **Adicionar** Assemblies ao Pacote de Implantação
* **Configurar** Autenticação e Autorização
* **Publicar** o aplicativo Web ASP.NET no Azure

## <a name="prerequisites"></a>Pré-requisitos
Leia a seção "Recomendações gerais e práticas recomendadas" no [Business Intelligence do SQL Server nas Máquinas Virtuais do Azure](../classic/ps-sql-bi.md).

> [!NOTE]
> Os controles ReportViewer são fornecidos com o Visual Studio Standard Edition ou versões posteriores. Se estiver usando o Web Developer Express Edition, você deverá instalar o [MICROSOFT REPORT VIEWER 2012 RUNTIME](https://www.microsoft.com/download/details.aspx?id=35747) para usar os recursos de tempo de execução do ReportViewer.
>
> Não há suporte para o ReportViewer configurado em modo de processamento local no Microsoft Azure.

## <a name="adding-assemblies-to-the-deployment-package"></a>Adicionando assemblies ao pacote de implantação
Quando você hospeda seu aplicativo ASP.NET localmente, os assemblies do ReportViewer geralmente são instalados diretamente no GAC (cache de assembly global) do servidor IIS durante a instalação do Visual Studio e podem ser acessados diretamente pelo aplicativo. No entanto, ao hospedar o aplicativo ASP.NET na nuvem, o Microsoft Azure não permite que nada seja instalado no GAC, de modo que você deve verificar se os assemblies do ReportViewer estão disponíveis localmente para seu aplicativo. É possível fazer isso adicionando referências a eles no seu projeto e configurando-os para serem copiados localmente.

No modo de processamento remoto, o controle ReportViewer usa os seguintes assemblies:

* **Microsoft.ReportViewer.WebForms.dll**: Contém o código do ReportViewer, que você precisa para usar o ReportViewer em sua página. Uma referência para esse assembly é adicionada ao seu projeto quando você solta um controle ReportViewer em uma página ASP.NET em seu projeto.
* **Microsoft.ReportViewer.Common.dll**: Contém classes usadas pelo controle ReportViewer no tempo de execução. Ele não é adicionado automaticamente ao seu projeto.

### <a name="to-add-a-reference-to-microsoftreportviewercommon"></a>Para adicionar uma referência ao Microsoft.ReportViewer.Common
* Clique com o botão direito do mouse no nó **Referências** do projeto e selecione **Adicionar Referência**, selecione o assembly na guia .NET e clique em **OK**.

### <a name="to-make-the-assemblies-locally-accessible-by-your-aspnet-application"></a>Para tornar os assemblies acessíveis localmente ao aplicativo ASP.NET
1. Na pasta **Referências** , clique no assembly Microsoft.ReportViewer.Common para que suas propriedades apareçam no painel Propriedades.
2. No painel Propriedades, defina **Local da Cópia** como True.
3. Repita as etapas 1 e 2 para Microsoft.ReportViewer.WebForms.

### <a name="to-get-reportviewer-language-pack"></a>Para obter o pacote de idiomas do ReportViewer
1. Instale o pacote redistribuível do Microsoft Report Viewer 2012 Runtime apropriado por meio do [Centro de Download da Microsoft](https://go.microsoft.com/fwlink/?LinkId=317386).
2. Selecione o idioma na lista suspensa e a página será redirecionada para a página do centro de download correspondente.
3. Clique em **Baixar** para iniciar o download do ReportViewerLP.exe.
4. Depois de baixar o ReportViewerLP.exe, clique em **Executar** para instalá-lo imediatamente ou clique em **Salvar** para salvá-lo no computador. Se você clicar em **Salvar**, lembre-se do nome da pasta em que você salvar o arquivo.
5. Localize a pasta onde você salvou o arquivo. Clique com o botão direito do mouse em ReportViewerLP.exe, clique em **Executar como administrador** e em **Sim**.
6. Depois de executar ReportViewerLP.exe, você verá que os arquivos de recursos **Microsoft.ReportViewer.Webforms.Resources** e **Microsoft.ReportViewer.Common.Resources** estão em c:\windows\assembly.

### <a name="to-configure-for-localized-reportviewer-control"></a>Para configurar o controle ReportViewer localizado
1. Baixe e instale o pacote redistribuível do Microsoft Report Viewer 2012 Runtime seguindo as instruções especificadas acima.
2. Crie a pasta \<idioma\> no projeto e copie nela os arquivos de assembly do recurso associado. Os arquivos de assembly do recurso a serem copiados são: **Microsoft.ReportViewer.Webforms.Resources.dll** e **Microsoft.ReportViewer.Common.Resources.dll**. Selecione os arquivos de assembly do recurso e, no painel Propriedades, defina **Copiar para Diretório de Saída** como "**Copiar sempre**".
3. Defina Culture e UICulture para o projeto Web. Para obter mais informações sobre como definir Cultura e Cultura de interface de usuário para uma página da Web ASP.NET, consulte [Como: Definir a cultura e cultura da interface do usuário para globalização de página da Web do ASP.NET](https://go.microsoft.com/fwlink/?LinkId=237461).

## <a name="configuring-authentication-and-authorization"></a>Configurando a autenticação e a autorização
O ReportViewer precisa usar credenciais apropriadas para se autenticar no servidor de relatório, e as credenciais devem ser autorizadas pelo servidor de relatório a acessar os relatórios que você deseja. Para obter informações sobre autenticação, consulte o white paper [Controle do visualizador de relatórios do Reporting Services e servidores de relatórios com base em máquina virtual do Microsoft Azure](https://msdn.microsoft.com/library/azure/dn753698.aspx).

## <a name="publish-the-aspnet-web-application-to-azure"></a>Publicar o aplicativo Web ASP.NET no Azure
Para obter instruções sobre como publicar um aplicativo Web ASP.NET no Azure, consulte [como: Migrar e publicar um aplicativo Web para Azure do Visual Studio ](../../../vs-azure-tools-migrate-publish-web-app-to-cloud-service.md) e [Iniciar com os Aplicativos Web e ASP.NET](../../../app-service/app-service-web-get-started-dotnet.md).

> [!IMPORTANT]
> Se o comando Adicionar Projeto de Implantação do Azure ou Adicionar Projeto de Serviço de Nuvem do Azure não aparecer no menu de atalho do Gerenciador de Soluções, talvez seja necessário alterar a estrutura de destino do projeto para o .NET Framework 4.
>
> Os dois comandos fornecem basicamente a mesma funcionalidade. Um ou outro comando aparecerá no menu de atalho de acordo com a versão do Microsoft Azure SDK que você instalou.
>
>

## <a name="resources"></a>Recursos
[Relatórios da Microsoft](https://go.microsoft.com/fwlink/?LinkId=205399)

[Business Intelligence do SQL Server em Máquinas Virtuais do Azure](../classic/ps-sql-bi.md)

[Use o PowerShell para criar uma VM do Azure com um servidor de relatório em modo nativo](../classic/ps-sql-report.md)
