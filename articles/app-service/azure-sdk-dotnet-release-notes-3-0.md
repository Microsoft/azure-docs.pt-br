---
title: "Notas de versão do SDK do Azure para .NET 3.0 | Microsoft Docs"
description: "Notas de Versão do SDK do Azure para .NET 3.0"
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: 
ms.assetid: c83d815b-fc19-4260-821e-7d2a7206dffc
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 03/07/2017
ms.author: juliako
ms.openlocfilehash: eea4e569ac2d0192ed7872d2fcb9bed03614832b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-sdk-for-net-30-release-notes"></a>Notas de versão do SDK do Azure para .NET 3.0

Este tópico inclui as notas de versão para a versão 3.0 do SDK do Azure para .NET.

##<a name="azure-sdk-for-net-30-release-summary"></a>Resumo da versão do SDK do Azure para .NET 3.0

Data do lançamento: 03/07/2017
 
Nenhuma alteração significativa do Azure SDK 3.0 foi introduzida nesta versão. Também não há nenhum processo de atualização necessário para aproveitar esse SDK com projetos de Serviço de Nuvem existentes. Para permitir o uso do SDK do Azure 3.0 sem a necessidade de um processo de atualização, o SDK do Azure 3.0 instala os mesmos diretórios do SDK do Azure 2.9. A maioria dos componentes não alterou a versão principal 2.9, apenas atualizou o número de build.

## <a name="visual-studio-2017-rtw"></a>Visual Studio 2017 RTW

- No Visual Studio 2017, esta versão do SDK do Azure para .NET é integrada à Carga de Trabalho do Azure. Todas as ferramentas que você precisa para fazer o desenvolvimento do Azure farão parte do Visual Studio 2017 no futuro. Para o Visual Studio 2015, o SDK ainda estará disponível por meio do WebPI. Agora que o Visual Studio 2017 foi lançado, descontinuamos as versões do SDK do Azure para .NET para Visual Studio 2013.

### <a name="azure-diagnostics"></a>Diagnóstico do Azure

- Foi alterado o comportamento para armazenar somente uma cadeia de conexão parcial somente com a chave substituída por um token de cadeia de conexão do armazenamento de diagnóstico dos Serviços de Nuvem. A chave de armazenamento real agora é armazenada na pasta de perfil do usuário para que seu acesso possa ser controlado. O Visual Studio lerá a chave de armazenamento da pasta de perfil de usuário para depuração local e processo de publicação. 
- Em resposta à alteração descrita acima, a equipe do Visual Studio Online aprimorou o modelo de tarefa de implantação dos Serviços de Nuvem do Azure para que os usuários possam especificar a chave de armazenamento para configurar a extensão de diagnóstico ao publicar no Azure na Implantação e Integração Contínua.
- Tornamos possível armazenar a cadeia de conexão segura e a geração de tokens para Diagnóstico do Azure (WAD) a fim de ajudá-lo a solucionar problemas de configuração em ambientes.
 
### <a name="windows-server-2016-virtual-machines"></a>Máquinas virtuais do Windows Server 2016

- O Visual Studio agora oferece suporte à implantação de Serviços de Nuvem nas máquinas virtuais da Família do SO 5 (Windows Server 2016). Para serviços de nuvem existentes, é possível alterar suas configurações para direcionar a nova Família de SO. Ao criar novos serviços de nuvem, se você optar por criar o serviço usando o .net 4.6 ou posterior, isso padronizará o serviço para usar a Família do SO 5.  Para obter mais informações, é possível examinar a [tabela de suporte da Família do SO Convidado](../cloud-services/cloud-services-guestos-update-matrix.md).

### <a name="known-issues"></a>Problemas conhecidos

- O SDK do Azure para .NET 3.0 apresentou um problema ao remover o Visual Studio 2017 em uma configuração lado a lado com o Visual Studio 2015.  Se você tiver o SDK do Azure instalado para Visual Studio 2015, o Emulador de Armazenamento do Microsoft Azure e o emulador de computação do Microsoft Azure serão removidos se você desinstalar o Visual Studio 2017.  Isso produzirá um erro durante a criação e depuração de novos projetos de serviços de nuvem no Visual Studio 2015. Para corrigir esse problema, reinstale o SDK do Azure do Web Platform Installer.  O problema será resolvido em uma atualização futura do Visual Studio 2017.  .

 
### <a name="azure-in-role-cache"></a>Cache na função do Azure 

- O suporte para Cache na Função do Azure terminou em 30 de novembro de 2016. Para obter mais detalhes, clique [aqui](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/).




