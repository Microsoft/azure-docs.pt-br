---
title: "Notas de versão do Catálogo de Dados do Azure | Microsoft Docs"
description: "Notas de versão do Catálogo de Dados do Azure."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 3aca9c49-45a4-4352-92e6-bd25ee3eacf7
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: d3db9bee0558cac5fb4f5b8fb4ab67a35ce0f141
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-data-catalog-release-notes"></a>Notas de versão do Catálogo de Dados do Azure
## <a name="notes-for-the-november-20-2015-release-of-azure-data-catalog"></a>Notas da versão de 20 de novembro de 2015 do Catálogo de Dados do Azure
### <a name="opening-data-sources-in-power-bi-desktop"></a>Abrindo fontes no Power BI Desktop
Ao usar a opção "Abrir no Power BI Desktop" no portal do **Catálogo de Dados do Azure** , os usuários podem encontrar um de dois problemas no aplicativo Power BI Desktop:

* Será exibida uma caixa de diálogo com o título "Não é possível abrir o documento"
* O Power BI Desktop abre, mas o arquivo parece estar vazio

Para cada situação, o problema pode ser resolvido ao baixar e instalar a versão mais recente do Power BI Desktop em [PowerBI.com](https://powerbi.com).

## <a name="notes-for-the-november-13-2015-release-of-azure-data-catalog"></a>Notas da versão de 13 de novembro de 2015 do Catálogo de Dados do Azure
### <a name="registering-and-connecting-to-teradata"></a>Registrando e conectando ao Teradata
Ao se conectar a fontes de dados Teradata, os usuários devem ter instalado o driver ODBC correto do Teradata que coincida com o número de bits (32 bits ou 64 bits) do software que está sendo usado.

A partir dessa data de lançamento do ADC, o [driver ODBC do Teradata para windows (versão 15.10)](http://downloads.teradata.com/download/connectivity/odbc-driver/windows) mais recente é compatível com o Office 2013, mas não com o Office 2016.

## <a name="notes-for-the-july-13-2015-release-of-azure-data-catalog"></a>Notas da versão de 13 de julho de 2015 do Catálogo de Dados do Azure
### <a name="registering-and-connecting-to-oracle-database"></a>Registrando e conectando-se ao Banco de Dados do Oracle
Ao conectar-se às fontes de dados do banco de dados Oracle, os usuários devem ter instalado os drivers corretos do Oracle que correspondem ao número de bits (32 bits ou 64 bits) do software que está sendo usado.

* Ao registrar fontes de dados Oracle em um computador executando o Windows de 32 bits, os drivers do Oracle de 32 bits serão usados
* Ao registrar fontes de dados Oracle em um computador executando o Windows de 64 bits, os drivers do Oracle de 64 bits serão usados
* Ao conectar-se às fontes de dados Oracle usando o Excel em um computador executando a versão de 32 bits do Microsoft Office, inclusive no Windows de 64 bits, os drivers do Oracle de 32 bits serão usados
* Ao conectar-se às fontes de dados Oracle usando o Excel em um computador executando a versão de 64 bits do Microsoft Office, os drivers do Oracle de 64 bits serão usados

### <a name="registering-and-connecting-to-sql-server-reporting-services"></a>Registro e conexão ao SQL Server Reporting Services
Atualmente, o suporte para as fontes de dados do SSRS (SQL Server Reporting Services) está limitado somente a servidores do Modo Nativo. O suporte para o SSRS no modo SharePoint será adicionado em uma versão posterior.

### <a name="opening-data-assets-in-excel"></a>Abrindo ativos de dados no Excel
Ao abrir os ativos de dados no Microsoft Excel, no portal do **Catálogo de Dados do Azure**, os usuários poderão ver uma caixa de diálogo **Aviso de Segurança do Microsoft Excel**. Esse é um comportamento padrão e esperado, e os usuários podem selecionar **Habilitar** para continuar.

Para obter mais informações, veja [Habilitar ou desabilitar alertas de segurança sobre links e arquivos de sites suspeitos](https://support.office.com/article/Enable-or-disable-security-alerts-about-links-and-files-from-suspicious-websites-A1AC6AE9-5C4A-4EB3-B3F8-143336039BBE).

### <a name="proxy-and-policy-configuration-and-data-source-registration"></a>Registro da fonte de dados e configuração de proxy e política
Os usuários podem encontrar uma situação em que podem acessar o portal do Catálogo de Dados do Azure, mas quando tentam fazer logon na ferramenta de registro da fonte de dados encontram uma mensagem de erro que impede o logon.

Há duas causas possíveis para esse comportamento de problema:

**Causa 1: configuração dos Serviços de Federação do Active Directory** A ferramenta de registro de fonte de dados usa a Autenticação de formulários para validar logons de usuário no Active Directory. Para um logon bem-sucedido, a autenticação de formulários deve ser habilitada na Política de Autenticação Global por um administrador do Active Directory.

Em algumas situações, esse comportamento de erro pode ocorrer apenas quando o usuário está na rede da empresa, ou quando está se conectando de fora da rede da empresa. A Política de Autenticação Global permite que os métodos de autenticação sejam habilitados separadamente para conexões intranet e extranet. Erros de logon poderão ocorrer se a autenticação de formulários não estiver habilitada para a rede por meio da qual o usuário está se conectando.

Para obter mais informações, consulte [Configurando políticas de autenticação](https://technet.microsoft.com/library/dn486781.aspx).

**Causa 2: configuração de proxy da rede** Se a rede corporativa usar um servidor proxy, a ferramenta de registro não poderá se conectar ao Active Directory do Azure por meio do proxy. Os usuários podem garantir a ferramenta de registro editando o arquivo de configuração da ferramenta, adicionando esta seção ao arquivo:

      <system.net>
        <defaultProxy useDefaultCredentials="true" enabled="true">
          <proxy usesystemdefault="True"
                          proxyaddress="http://<your corporate network proxy url>"
                          bypassonlocal="True"/>
        </defaultProxy>
      </system.net>


Para localizar o arquivo RegistrationTool.exe.config, inicie a ferramenta de registro e, em seguida, abra o utilitário Gerenciador de Tarefas do Windows. Na guia Detalhes do Gerenciador de tarefas, clique com o botão direito em RegistrationTool.exe e escolha Abrir local do arquivo no menu pop-up.
