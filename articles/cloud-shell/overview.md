---
title: "Visão geral do Azure Cloud Shell (versão prévia) | Microsoft Docs"
description: "Visão geral do Azure Cloud Shell."
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: juluk
ms.openlocfilehash: 44d2ac2fd35621ab8cd8d7584744139ee3bab5c2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-azure-cloud-shell-preview"></a>Visão geral do Azure Cloud Shell (versão prévia)
O Azure Cloud Shell é um shell interativo e acessível pelo navegador para o gerenciamento de recursos do Azure.
Ele dá a você a flexibilidade de escolher a experiência de shell que melhor se adequa ao modo como você trabalha.
Usuários do Linux podem optar por uma experiência com o Bash, enquanto usuários do Windows podem optar pelo PowerShell.

![Bash no Cloud Shell](media/overview/overview-bash-pic.png)

![PowerShell no Cloud Shell](media/overview/overview-ps-pic.png)

## <a name="features"></a>Recursos
### <a name="browser-based-shell-experience"></a>Experiência de shell baseada no navegador
O Cloud Shell permite o acesso a uma experiência de linha de comando baseada em navegador, que foi criada tendo em mente as tarefas de gerenciamento do Azure.
Utilize o Cloud Shell para trabalhar de forma independente de um computador local, de uma maneira que somente a nuvem pode proporcionar.

### <a name="choice-of-preferred-shell-experience"></a>Opção de experiência de shell preferida
O Azure Cloud Shell dá a você a flexibilidade de escolher a experiência de shell que melhor se adequa ao modo como você trabalha.
Usuários do Linux podem optar por uma experiência com o Bash, enquanto usuários do Windows podem optar pelo PowerShell.

### <a name="pre-configured-azure-workstation"></a>Estação de trabalho pré-configurada do Azure
O Cloud Shell vem pré-instalado com ferramentas de linha de comando populares e suporte para idiomas, para que você possa trabalhar mais rapidamente.

Exiba a lista completa de ferramentas da [experiência do Bash](features.md#tools) e [experiência do PowerShell.](features-powershell.md#tools)

### <a name="automatic-authentication"></a>Autenticação automática
O Cloud Shell é autenticado de forma segura e automática a cada sessão para ter acesso instantâneo a seus recursos por meio da CLI do Azure 2.0.

### <a name="connect-your-azure-file-storage"></a>Conectar o armazenamento de arquivos do Azure
As máquinas do Cloud Shell são temporárias e, consequentemente, exigem que um compartilhamento de arquivos do Azure seja montado como `clouddrive` para manter seu diretório $Home.
Na primeira inicialização, o Cloud Shell solicita a criação de um grupo de recursos, uma conta de armazenamento e um compartilhamento de arquivos em seu nome. Essa é uma etapa única e será anexada automaticamente para todas as sessões. Um compartilhamento de arquivo único pode ser mapeado e será usado pelo Bash e pelo PowerShell no Cloud Shell.

#### <a name="create-new-storage"></a>Criar novo armazenamento
![](media/overview/basic-storage.png)

Uma conta de LRS (armazenamento com redundância local) e um compartilhamento de Arquivos do Azure podem ser criados em seu nome. O compartilhamento de arquivos do Azure será usado para ambientes Bash e PowerShell se você optar por usar ambos. Custos de armazenamento regulares se aplicam.

Três recursos serão criados em seu nome:
1. Um Grupo de Recursos chamado: `cloud-shell-storage-<region>`
2. Uma Conta de Armazenamento chamada: `cs<uniqueGuid>`
3. Um Compartilhamento de Arquivos chamado: `cs-<user>-<domain>-com-<uniqueGuid>`

> [!Note]
> O Bash no Cloud Shell também cria uma imagem de disco de 5 GB padrão para manter `$Home`. Todos os arquivos em seu diretório $Home como chaves SSH são mantidos em sua imagem de disco do usuário armazenada no compartilhamento de arquivo montados. Aplica as práticas recomendadas ao salvar arquivos no diretório $Home e compartilhamento de arquivos montado.

#### <a name="use-existing-resources"></a>Usar recursos existentes
![](media/overview/advanced-storage.png)

Uma opção avançada é fornecida para associar os recursos existentes ao Cloud Shell.
No prompt de configuração de armazenamento, clique em “Mostrar configurações avançadas” para mostrar as opções adicionais.
As listas suspensas são filtradas para sua região do Cloud Shell atribuída e para contas de armazenamento com redundância local/global.

[Saiba mais sobre o armazenamento do Cloud Shell, atualização de compartilhamentos de arquivos e upload/download de arquivos.](persisting-shell-storage.md)

## <a name="concepts"></a>Conceitos
* O Cloud Shell é executado em um computador temporário fornecido por sessão e por usuário
* O Cloud Shell atinge o tempo limite após 20 minutos sem atividade interativa
* O Cloud Shell só pode ser acessado com um compartilhamento de arquivos anexado
* O Cloud Shell usa o mesmo compartilhamento de arquivos para Bash e PowerShell
* É atribuído ao Cloud Shell um computador por conta de usuário
* As permissões são definidas da mesma forma que para um usuário normal do Linux (Bash)

Saiba mais sobre os recursos em [Bash no Cloud Shell](features.md) e [PowerShell no Cloud Shell](features-powershell.md).

## <a name="examples"></a>Exemplos
* Use scripts para automatizar as tarefas de gerenciamento do Azure
* Gerenciar recursos do Azure simultaneamente por meio do portal do Azure e ferramentas de linha de comando do Azure
* Testar CLI do Azure 2.0 ou cmdlets do Azure PowerShell

Experimente esses exemplos em início rápido para [Bash no Cloud Shell](quickstart.md) e [PowerShell no Cloud Shell](quickstart-powershell.md).

## <a name="pricing"></a>Preços
O computador que hospeda o Cloud Shell é gratuito, com o pré-requisito de um compartilhamento de arquivos do Azure montado. Custos de armazenamento regulares se aplicam.

## <a name="supported-browsers"></a>Navegadores com suporte
O Cloud Shell é recomendado para Chrome, Edge e Safari.
Embora tenha suporte para Chrome, Firefox, Safari, IE e Edge, o Cloud Shell está sujeito a configurações específicas do navegador.