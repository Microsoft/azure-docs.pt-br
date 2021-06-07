---
title: Amostras do PowerShell
description: Encontre amostras do Azure PowerShell para alguns dos cenários comuns do Serviço de Aplicativo. Saiba como automatizar suas tarefas de implantação ou gerenciamento do Serviço de Aplicativo.
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.topic: sample
ms.date: 07/07/2020
ms.custom: mvc
ms.openlocfilehash: a1577d42de9a4452467a448a0de5cd5f9575a55f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86169418"
---
# <a name="powershell-samples-for-azure-app-service"></a>Amostras do PowerShell para o Serviço de Aplicativo do Azure

A tabela a seguir inclui links para scripts do PowerShell compilados usando-se o Azure PowerShell.

| Script | Descrição |
|-|-|
|**Como criar o aplicativo**||
| [Criar um aplicativo com a implantação do GitHub](./scripts/powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria um aplicativo do Serviço de Aplicativo que efetua pull do código do GitHub. |
| [Criar um aplicativo com a implantação contínua do GitHub](./scripts/powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria um aplicativo do Serviço de Aplicativo que implanta continuamente o código do GitHub. |
| [Criar um aplicativo e implantar o código com FTP](./scripts/powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria um aplicativo do Serviço de Aplicativo e carrega arquivos de um diretório local usando FTP. |
| [Criar um aplicativo e implantar o código de um repositório Git local](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria um aplicativo do Serviço de Aplicativo e configura o push de código de um repositório Git local. |
| [Criar um aplicativo e implantar o código em um ambiente de preparo](./scripts/powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria um aplicativo do Serviço de Aplicativo com um slot de implantação para alterações de código de preparo. |
|  [Criar um aplicativo e expor seu aplicativo com um ponto de extremidade privado](./scripts/powershell-deploy-private-endpoint.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria um aplicativo do Serviço de Aplicativo com um ponto de extremidade privado. |
|**Como configurar o aplicativo**||
| [Mapear um domínio personalizado para um aplicativo](./scripts/powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria um aplicativo do Serviço de Aplicativo e mapeia um nome de domínio personalizado para ele. |
| [Associar um certificado TLS/SSL personalizado a um aplicativo](./scripts/powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria um aplicativo do Serviço de Aplicativo e associa o certificado TLS/SSL de um nome de domínio personalizado a ele. |
|**Como escalar um aplicativo**||
| [Dimensionar manualmente um aplicativo](./scripts/powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria um aplicativo do Serviço de Aplicativo e dimensiona-o em duas instâncias. |
| [Escalar um aplicativo em todo o mundo com uma arquitetura de alta disponibilidade](./scripts/powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria dois aplicativos do Serviço de Aplicativo em duas regiões geográficas diferentes e disponibiliza-os por meio de um único ponto de extremidade usando o Gerenciador de Tráfego do Azure. |
|**Como conectar o aplicativo aos recursos**||
| [Conectar um aplicativo a um Banco de Dados SQL](./scripts/powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria um aplicativo do Serviço de Aplicativo e um banco de dados no Banco de Dados SQL do Azure e adiciona a cadeia de conexão de banco de dados às configurações do aplicativo. |
| [Conectar um aplicativo a uma conta de armazenamento](./scripts/powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria um aplicativo do Serviço de Aplicativo e uma conta de armazenamento e, em seguida, adiciona a cadeia de conexão de armazenamento às configurações do aplicativo. |
|**Aplicativo de backup e restauração**||
| [Fazer backup de um aplicativo](./scripts/powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria um aplicativo do Serviço de Aplicativo e um backup avulso para ele. |
| [Criar um backup agendado para um aplicativo](./scripts/powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria um aplicativo do Serviço de Aplicativo e um backup agendado para ele. |
| [Excluir um backup de um aplicativo](./scripts/powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Exclui um backup existente para um aplicativo. |
| [Restaurar um aplicativo de um backup](./scripts/powershell-backup-restore.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Restaura um aplicativo de um backup concluído anteriormente. |
| [Restaurar um backup entre assinaturas](./scripts/powershell-backup-restore-diff-sub.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Restaura um aplicativo Web de um backup em outra assinatura. |
|**Como monitorar o aplicativo**||
| [Monitorar um aplicativo com logs do servidor Web](./scripts/powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria um aplicativo do Serviço de Aplicativo, habilita o log para ele e baixa os logs no computador local. |
| | |
