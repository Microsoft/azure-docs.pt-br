---
title: Build de linha de comando do Azure| Microsoft Docs
description: "Compilação de linha de comando do Azure"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 94b35d0d-0d35-48b6-b48b-3641377867fd
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2017
ms.author: kraigb
ms.openlocfilehash: 5fe910e2757dd5ec783538e23e7f52e2f5725b39
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="building-azure-projects-from-the-command-line"></a>Criação de projetos do Azure na linha de comando
Ao usar o Microsoft Build Engine (MSBuild), você pode criar produtos nos ambientes de laboratório de criação em que o Visual Studio não está instalado. O MSBuild usa um formato XML para arquivos de projeto extensíveis e com suporte total da Microsoft. Usando o formato de arquivo MSBuild, é possível descrever quais itens devem ser criados para uma ou mais plataformas e configurações.

Você também pode executar o MSBuild na linha de comando, e este tópico descreve essa abordagem. Ao configurar propriedades na linha de comando, você pode criar configurações específicas de um projeto. Da mesma forma, também é possível definir os destinos que o comando MSBuild cria. Para saber mais sobre parâmetros de linha de comando e o MSBuild, confira [Referência de linha de comando MSBuild](https://msdn.microsoft.com/library/ms164311.aspx).

## <a name="msbuild-parameters"></a>Parâmetros do MSBuild
A maneira mais simples de criar um pacote é executar o MSBuild com a opção `/t:Publish` . Por padrão, este comando cria um diretório em relação à pasta raiz do projeto, como `<ProjectDirectory>\bin\Configuration\app.publish\`. Quando você cria um projeto do Azure, dois arquivos são gerados: o arquivo de pacote em si e o arquivo de configuração que o acompanha:

* Arquivo de pacote (`project.cspkg`)
* Arquivo de configuração (`ServiceConfiguration.TargetProfile.cscfg`)

Por padrão, cada projeto do Azure inclui um arquivo de configuração de serviço para criações locais (depuração) e outro para criações na nuvem (preparo ou produção). No entanto, você pode adicionar ou remover arquivos de configuração de serviço conforme necessário. Ao compilar um pacote no Visual Studio, você define o arquivo de configuração de serviço a ser incluído com o pacote. Quando você compila um pacote usando o MSBuild, o arquivo de configuração de serviço local é incluído por padrão. Para incluir um arquivo de configuração de serviço diferente, defina a propriedade `TargetProfile` do comando MSBuild (`MSBuild /t:Publish /p:TargetProfile=ProfileName`).

Se você quiser usar um diretório alternativo para o pacote armazenado e arquivos de configuração, defina o caminho usando a opção `/p:PublishDir=Directory\`, incluindo o separador de barra invertida à direita.

## <a name="next-steps"></a>Próximas etapas
Depois que o pacote é compilado, você pode implantá-lo no Azure. Para ver um tutorial que demonstra como automatizar esse processo, confira [Fornecimento contínuo de serviços de nuvem no Azure](./cloud-services/cloud-services-dotnet-continuous-delivery.md).

