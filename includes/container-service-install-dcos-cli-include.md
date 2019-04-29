---
title: Instalar a CLI do DC/OS | Microsoft Docs
description: Instale a CLI do DC/OS.
services: container-service
documentationcenter: ''
author: rgardler
manager: timlt
editor: ''
tags: acs, azure-container-service
keywords: Contêineres, Microsserviços, DC/OS, Azure
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/10/2016
ms.author: rogardle
ms.openlocfilehash: e2601d5200f0b8ebcfb856bffb871f01b3acb215
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60557506"
---
> [!NOTE]
> Isso é para trabalhar com clusters ACS baseados no DC/OS. Não é necessário fazer isso para clusters ACS baseados no Swarm.
> 
> 

Primeiro, [conectar-se ao cluster ACS baseado no DC/OS](../articles/container-service/container-service-connect.md). Depois de fazer isso, você poderá instalar a CLI do DC/OS em seu computador cliente com os comandos abaixo:

```bash
sudo pip install virtualenv
mkdir dcos && cd dcos
wget https://raw.githubusercontent.com/mesosphere/dcos-cli/master/bin/install/install-optout-dcos-cli.sh
chmod +x install-optout-dcos-cli.sh
./install-optout-dcos-cli.sh . http://localhost --add-path yes
```

Se estiver usando uma versão antiga do Python, talvez você observe algumas “InsecurePlatformWarnings”. Você pode ignorá-los com segurança.

Para começar a usar sem reiniciar o shell, execute:

```bash
source ~/.bashrc
```

Esta etapa não será necessária quando você iniciar novos shells.

Agora você pode confirmar se a CLI está instalada:

```bash
dcos --help
```