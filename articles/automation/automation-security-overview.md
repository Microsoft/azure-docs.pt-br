---
title: Introdução à autenticação na Automação do Azure
description: Este artigo fornece uma visão geral da segurança de automação e dos diferentes métodos de autenticação disponíveis para Contas de automação na Automação do Azure.
keywords: segurança de automação, automação segura; autenticação de automação
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/19/2018
ms.topic: conceptual
manager: carmonm
ROBOTS: NOINDEX
ms.openlocfilehash: 88f1826191934ee76c565bd73de907a26d368c88
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476835"
---
# <a name="introduction-to-authentication-in-azure-automation"></a>Introdução à autenticação na Automação do Azure  
A Automação do Azure permite automatizar tarefas em relação a recursos no Azure, locais e com outros provedores de nuvem, como AWS (Amazon Web Services).  Para que um runbook execute as ações necessárias, ele deve ter permissões para acessar os recursos com segurança e apenas com os direitos mínimos necessários na assinatura.

Este artigo abordará os vários cenários de autenticação com suporte na Automação do Azure e mostrará como iniciar baseado nos ambientes que você precisa gerenciar.  

## <a name="automation-account-overview"></a>Visão geral da Conta de Automação
Ao iniciar a Automação do Azure pela primeira vez, você deve criar pelo menos uma conta de Automação. As contas de Automação permitem isolar seus recursos de Automação (runbooks, ativos, configurações) dos recursos contidos em outras contas de Automação. Você pode usar contas de Automação para separar os recursos em ambientes lógicos separados. Por exemplo, você pode usar uma conta para desenvolvimento, outra para produção e outra para seu ambiente local.  Uma conta de Automação do Azure é diferente de sua conta da Microsoft ou de contas criadas em sua assinatura do Azure.

Os recursos de Automação para cada conta de Automação estão associados a uma única região do Azure, mas as contas de Automação podem gerenciar todos os recursos em sua assinatura. O principal motivo para criar contas de Automação em regiões diferentes seria se você tiver políticas que exijam que dados e recursos sejam isolados em uma região específica.

Todas as tarefas que podem ser executadas em relação a recursos usando o Azure Resource Manager e os cmdlets do Azure na Automação do Azure devem ser autenticadas no Azure usando a autenticação baseada em credenciais de identidade organizacional do Azure Active Directory.  A autenticação baseada em certificado era o método de autenticação original do modo clássico do Azure, mas sua instalação era complicada.  A autenticação no Azure com o usuário do Azure AD foi introduzida em 2014 não apenas para simplificar o processo de configuração de uma conta de Autenticação, mas também para dar suporte à capacidade de executar a autenticação não interativa no Azure com uma única conta de usuário que funcionava com o Azure Resource Manager e recursos clássicos.   

Atualmente, quando você cria uma nova conta de Automação no portal do Azure, ela cria automaticamente:

* A conta Executar como, que cria uma nova entidade de serviço no Azure Active Directory, um certificado, e atribui o controle de acesso baseado em função de Colaborador (RBAC), que será usado para gerenciar os recursos do Resource Manager usando runbooks.
* Conta Executar como clássica carregando um certificado de gerenciamento, que é usado para gerenciar os recursos clássicos do Azure usando runbooks.  

O controle de acesso baseado em função está disponível com o Azure Resource Manager para conceder ações permitidas a uma conta de usuário do Azure AD e uma conta Executar como e autenticar essa entidade de serviço.  Leia [o artigo Controle de acesso baseado em função da Automação do Azure](automation-role-based-access-control.md) para obter mais informações que o ajudarão a desenvolver seu modelo para gerenciar permissões de Automação.  

Runbooks em execução em um Hybrid Runbook Worker em seu datacenter ou em relação a serviços de computação no AWS não podem usar o mesmo método que é normalmente usado para autenticar runbooks em recursos do Azure.  Isso ocorre porque esses recursos estão em execução fora do Azure e, assim, exigirão suas próprias credenciais de segurança definidas na Automação para se autenticarem em recursos que acessarão localmente.  

## <a name="authentication-methods"></a>Métodos de autenticação
A tabela a seguir resume os diferentes métodos de autenticação para cada ambiente com suporte na Automação do Azure e o artigo que descreve como configurar a autenticação para seus runbooks.

| Método | Ambiente | Artigo |
| --- | --- | --- |
| Conta de Usuário do Azure AD |Implantação clássica do Azure e do Azure Resource Manager |[Autenticar Runbooks com uma conta de Usuário do Azure AD](automation-create-aduser-account.md) |
| Conta Executar como do Azure |Gerenciador de Recursos do Azure |[Autenticar Runbooks com uma conta Executar como do Azure](automation-sec-configure-azure-runas-account.md) |
| Conta Executar como do Azure Clássico |Azure clássico |[Autenticar Runbooks com uma conta Executar como do Azure](automation-sec-configure-azure-runas-account.md) |
| Autenticação do Windows |Datacenter local |[Autenticar Runbooks para Hybrid Runbook Workers](automation-hybrid-runbook-worker.md) |
| Credenciais do AWS |Amazon Web Services |[Autenticar Runbooks com o AWS (Amazon Web Services)](automation-config-aws-account.md) |

