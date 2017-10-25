---
title: "Gerenciar o Cofre de Chaves do Azure usando a Automação do Azure | Microsoft Docs"
description: "Saiba mais sobre como o serviço de Automação do Azure pode ser usado para gerenciar o Cofre da Chave do Azure."
services: Key-Vault, automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: 4e780762-19b6-4ca6-b894-ebb44c538f35
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: magoedte;csand
ms.openlocfilehash: dee39662472fe54776b591977f2b1ecb39d15b00
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="managing-azure-key-vault-using-azure-automation"></a>Gerenciar o Cofre da Chave do Azure usando a Automação do Azure
Este guia apresentará a você o serviço de Automação do Azure e como ele pode ser usado para simplificar o gerenciamento de suas chaves e segredos no cofre da chave do Azure.

## <a name="what-is-azure-automation"></a>O que é Automação do Azure?
[Automação do Azure](../automation/automation-intro.md) é um serviço do Azure para simplificar o gerenciamento de nuvem por meio da automação de processos e por uma configuração de estado desejada. Com o uso da Automação do Azure, as tarefas manuais, repetidas, de execução longa e propensas a erros podem ser automatizadas para aumentar a confiabilidade, a eficiência e o tempo de retorno para sua organização.

A Automação do Azure fornece um mecanismo de execução de fluxo de trabalho altamente confiável e altamente disponível que é dimensionado para atender às suas necessidades. Na Automação do Azure, processos podem ser inicializados manualmente, por sistemas de terceiros ou em intervalos agendados para que as tarefas acontecem exatamente quando necessário.

Reduza o custo operacional e libere a equipe de TI e DevOps para se concentrar no trabalho que agrega valor de negócios, transferindo as tarefas de gerenciamento de nuvem para serem executadas automaticamente pela Automação do Azure.

## <a name="how-can-azure-automation-help-manage-azure-key-vault"></a>Como a Automação do Azure pode ajudar a gerenciar o cofre da chave do Azure?
O Cofre de Chaves pode ser gerenciado na Automação do Azure usando os [cmdlets do Cofre de Chave AzureRM](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) e os [cmdlets do Cofre de Chaves Clássico do Azure](https://msdn.microsoft.com/library/azure/dn868052.aspx). O módulo do Azure para gerenciar o Cofre da Chave clássico está disponível automaticamente na Automação do Azure, e você pode importar o [módulo AzureRM-KeyVault](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) para a Automação do Azure, para executar muitas de suas tarefas de gerenciamento do Cofre da Chave dentro do serviço. Você também pode combinar esses cmdlets na Automação do Azure com os cmdlets para outros serviços do Azure, para automatizar tarefas complexas nos serviços do Azure e sistemas de terceiros.

Com os cmdlets do Cofre de Chaves do Azure, você pode executar as seguintes tarefas, entre outras: 

* Criar e configurar um cofre de chaves
* Criar ou importar uma chave
* Criar ou atualizar um segredo
* Atualizar os atributos de uma chave
* Obter uma chave ou um segredo
* Excluir uma chave ou um segredo

Veja alguns exemplos de uso do PowerShell para gerenciar o Cofre da Chave:  

* [Cofre da Chave do Azure - passo a passo](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step)
* [Configurando um Cofre da Chave do Azure](https://www.simple-talk.com/cloud/platform-as-a-service/setting-up-and-configuring-an-azure-key-vault)

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu os fundamentos de Automação do Azure e como ela pode ser usada para gerenciar o Cofre da Chave do Azure, siga estes links para obter mais informações sobre a Automação do Azure.

* Consulte o [Tutorial de introdução](../automation/automation-first-runbook-graphical.md)da Automação do Azure.
* Consulte os [scripts do PowerShell do Cofre da Chave do Azure](https://gallery.technet.microsoft.com/scriptcenter/site/search?query=azure%20key%20vault&f%5B0%5D.Value=azure%20key%20vault&f%5B0%5D.Type=SearchText&ac=5).

