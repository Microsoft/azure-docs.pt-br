---
title: Usando o Chef com o Azure
description: Introdução para usar o Chef para configurar e testar sua infraestrutura do Azure
ms.service: virtual-machines-linux
keywords: azure, chef, devops, máquinas virtuais, visão geral, automatizar
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 5c4e20177c1b334a34f7ce9328dfaa7bd8c66d3d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60656567"
---
# <a name="using-chef-with-azure"></a>Usando o Chef com o Azure
[Chef](https://www.chef.io) é uma plataforma de automação avançada que transforma a infraestrutura da máquina virtual no Azure no código. O Chef automatiza como a infraestrutura é configurada, implantada e gerenciada em sua rede, independentemente de seu tamanho.

Este artigo descreve os benefícios de usar o Chef para gerenciar a infraestrutura do Azure.

## <a name="chef-extension-on-azure"></a>Extensão da Chef no Azure
Provisione uma máquina virtual com o cliente da Chef em execução como um serviço em segundo plano com a [extensão da Chef](https://docs.microsoft.com/azure/chef/chef-extension-portal) no portal do Azure. Depois de provisionadas, essas máquinas virtuais estão prontas para serem gerenciadas por um servidor da Chef.

## <a name="chef-cloud-shell"></a>Cloud Shell da Chef
Use uma estação de trabalho da Chef diretamente no Azure Cloud Shell! Execute todos os seus utilitários da Chef e o InSpec diretamente do Cloud Shell. Você pode utilizar os comandos da Chef de:

* [chef](https://docs.chef.io/ctl_chef.html)
* [kitchen](https://docs.chef.io/ctl_kitchen.html)
* [inspec](https://www.inspec.io/docs/reference/cli/)
* [knife](https://docs.chef.io/knife.html)
* [cookstyle](https://docs.chef.io/cookstyle.html)
* [foodcritic](https://docs.chef.io/foodcritic.html)
* [chef-run](https://www.chef.sh/docs/chef-workstation/getting-started/)

Combine nossos utilitários de comando com as ferramentas disponíveis no Cloud Shell, tais como `git`, `az-cli`, e `terraform`, e escreva sua automação de infraestrutura e de conformidade por meio do navegador.

## <a name="automate-infrastructure-apps-and-compliance-with-one-platform"></a>Automatizar a infraestrutura, aplicativos e conformidade com uma plataforma
As empresas requerem velocidade e segurança para competir no mercado digital. Chef e Microsoft juntos ajudam pessoas, equipes e negócios a realizar todas essas coisas. Com uma única plataforma, o Chef Automate, você pode agora automatizar e entregar continuamente sua infraestrutura, aplicativos e conformidade pelo seu estado Microsoft.

## <a name="test-drive-chef-automate-on-azure"></a>Test drive da Automatização do Chef no Azure
Suportado pelo Chef, a [solução Chef Automate Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/chef-software.chef-automate) possibilita que você construa, implante e gerencia sua infraestrutura e aplicativos colaborativamente. Com um clique, você obtém acesso a todos os recursos comerciais incluídos no Chef Automate, obtém visibilidade de ponta a ponta da sua frota inteira, habilita a conformidade contínua e gerencia todas as alterações com um fluxo de trabalho unificado.

## <a name="next-steps"></a>Próximas etapas

* [Criar uma Máquina Virtual do Windows no Azure usando o Chef](/azure/virtual-machines/windows/chef-automation)
