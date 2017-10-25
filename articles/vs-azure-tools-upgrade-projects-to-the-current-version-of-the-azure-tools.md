---
title: "Como atualizar projetos para a versão atual das ferramentas do Azure | Microsoft Docs"
description: "Aprenda como atualizar um projeto do Azure no Visual Studio para a versão atual das ferramentas do Azure"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 1d64070a-078d-468a-87f4-e6715de6475f
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/18/2016
ms.author: kraigb
ms.openlocfilehash: 9a35de7ca0e7161468181b21709e1bd9915d566f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-upgrade-projects-to-the-current-version-of-the-azure-tools-for-visual-studio"></a>Como atualizar projetos para a versão atual das ferramentas do Azure para Visual Studio
## <a name="overview"></a>Visão geral
Depois de instalar a versão atual das Ferramentas do Azure (ou uma versão anterior mais recente que a 1.6), todos os projetos criados usando as Ferramentas do Azure de versão anterior à 1.6 (novembro de 2011) serão atualizados automaticamente assim que você abri-los. Se você criou projetos usando a versão 1.6 (novembro de 2011) dessas ferramentas e você ainda tiver a versão instalada, pode abrir esses projetos na versão anterior e posteriormente decidir se deseja atualizá-los.

## <a name="how-your-project-changes-when-you-upgrade-it"></a>Como o projeto muda quando você o atualiza
Se um projeto é atualizado automaticamente ou se você especificar que deseja atualizá-lo, o projeto será modificado para funcionar com as versões atuais de determinados assemblies e algumas propriedades também são alteradas conforme descrito nesta seção. Se seu projeto exigir outras alterações para ser compatível com a versão mais recente das ferramentas, você deve fazer essas alterações manualmente.

* O arquivo Web.config para funções web e o arquivo app.config para funções de trabalho são atualizados para fazer referência à versão mais recente do Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitoirTraceListener.dll.
* Os assemblies Microsoft.WindowsAzure.StorageClient.dll, Microsoft.WindowsAzure.Diagnostics.dll e Microsoft.WindowsAzure.ServiceRuntime.dll são atualizados para novas versões.
* Perfis de publicação que foram armazenados no arquivo de projeto do Azure (.ccproj) são movidos para um arquivo separado, com a extensão .azurePubXml, no subdiretório **Publicar** .
* Algumas propriedades no perfil de publicação são atualizadas para dar suporte a recursos novos e alterados. **AllowUpgrade** é substituído por **DeploymentReplacementMethod** porque você pode atualizar um serviço de nuvem implantado simultaneamente ou gradativamente.
* A propriedade **UseIISExpressByDefault** é adicionada e definida como false para que o servidor Web usado para depurar não seja alterado automaticamente de Serviços de Informações da Internet (IIS) para IIS Express. O IIS Express é o servidor Web padrão para projetos que são criados com as versões mais recentes das ferramentas.
* Se o Caching do Azure está hospedado em uma ou mais funções de seu projeto, algumas propriedades na configuração de serviço (arquivo .csdef) e a definição de serviço (arquivo .cscfg) são alteradas quando um projeto é atualizado. Se o projeto usa o pacote NuGet de Caching do Azure, o projeto é atualizado para a versão mais recente do pacote. Você deve abrir o arquivo web.config e verificar se a configuração do cliente foi mantida corretamente durante o processo de atualização. Se você adicionar as referências aos assemblies de cliente de Caching do Azure sem usar o pacote NuGet, esses assemblies não serão atualizados. Você deve atualizar manualmente essas referências às novas versões.

> [!IMPORTANT]
> Para projetos F #, você deve atualizar manualmente as referências aos assemblies do Azure para que eles façam referência às versões mais recentes desses assemblies.
> 
> 

### <a name="how-to-upgrade-an-azure-project-to-the-current-release"></a>Como atualizar um projeto do Azure para a versão atual
1. Instale a versão atual das ferramentas do Azure na instalação do Visual Studio que você deseja usar para o projeto atualizado e, em seguida, abra o projeto que você deseja atualizar. Se o projeto foi criado com ferramentas do Azure versão anterior à 1.6 (novembro de 2011), o projeto é atualizado automaticamente para a versão atual. Se o projeto foi criado com a versão de novembro de 2011 e essa versão ainda estiver instalada, o projeto será aberto nessa versão.
2. No Gerenciador de Soluções, abra o menu de atalho para o nó do projeto, escolha **Propriedades** e, em seguida, escolha a guia **Aplicativo** na caixa de diálogo que aparece.
   
    A guia **Aplicativo** exibe a versão das ferramentas que está associada com o projeto. Se for exibida a versão atual das ferramentas do Azure, o projeto já foi atualizado. Se você instalou uma versão mais recente das ferramentas do que aquela mostrada pela guia, um botão **Atualizar** é exibido.
3. Escolha o botão **Atualizar** para atualizar um projeto para a versão atual das ferramentas.
4. Compile o projeto e resolva os erros resultantes das alterações de API. Para obter informações sobre como modificar seu código para a nova versão, consulte a documentação da API específica.

