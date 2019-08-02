---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 06/17/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 29ab9b3c33aae6005510c34b207c7f87714149e5
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608061"
---
### <a name="preview"></a>Como posso participar da versão prévia pública?

Você precisa estar integrado para participar da versão prévia pública. Use as etapas descritas [neste artigo](../articles/bastion/bastion-create-host-portal.md) para criar um novo recurso do Azure Bastion. Atualmente, ao acessar e usar este serviço, você precisa usar o [Portal do Azure – versão prévia](https://aka.ms/BastionHost) em vez do portal do Azure comum.

### <a name="regions"></a>Quais regiões estão disponíveis durante a versão prévia?

É possível implantar e usar o recurso do Bastion em qualquer uma dessas regiões de versão prévia usando o [link do portal do Azure – versão prévia](https://aka.ms/BastionHost).

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="portal"></a>Não consigo encontrar o recurso do Bastion no portal do Azure. O que devo fazer?

Verifique se você está usando o [link do portal do Azure – versão prévia](https://aka.ms/BastionHost), não o portal do Azure comum.

### <a name="publicip"></a>É necessário ter um IP público em minha máquina virtual?

Você NÃO precisa de um IP público na máquina virtual do Azure a que está se conectando com o serviço do Azure Bastion. O serviço do Bastion abrirá o RDP/SSH/sessão/conexão para sua máquina virtual usando o IP privado da máquina virtual, dentro de sua rede virtual.

### <a name="rdpssh"></a>Eu preciso de um cliente de RDP ou SSH?

Você não precisa de um cliente de RDP ou SSH para acessar o RDP/SSH para sua máquina virtual do Azure no portal do Azure. Use o [link do portal do Azure – versão prévia](https://aka.ms/BastionHost) para acessar a versão piloto da versão prévia do portal. Isso permitirá que você tenha acesso de RDP/SSH à sua máquina virtual diretamente no navegador.

### <a name="agent"></a>Eu preciso ter um agente em execução na máquina virtual do Azure?

Não é necessário instalar um agente ou um software no navegador ou na máquina virtual do Azure. O serviço do Bastion é sem agente e não requer software adicional para RDP/SSH.

### <a name="browsers"></a>Quais navegadores são compatíveis?

Na versão prévia pública, use o navegador Microsoft Edge ou o Google Chrome no Windows. Para o Apple Mac, use o navegador Google Chrome. O Microsoft Edge Chromium também é compatível com o Windows e o Mac, respectivamente.

### <a name="roles"></a>É necessário ter alguma função para acessar uma máquina virtual?

Para fazer uma conexão, são necessárias as seguintes funções:

* Função de leitor na máquina virtual
* Função de leitor na placa de interface de rede com endereço IP privado da máquina virtual
* Função de leitor no recurso do Azure Bastion

### <a name="previewbill"></a>Preço – Serei cobrado por participar da versão prévia?

Você será cobrado apenas parcialmente durante a versão prévia pública. No entanto, não há nenhum SLA anexado à implantação. Para saber mais, confira a [página de preço](https://aka.ms/BastionHostPricing).

### <a name="previewbill"></a>Por que recebo a mensagem de erro "Sua sessão expirou" antes de iniciar a sessão do Bastion?

Uma sessão deve ser iniciada somente por meio do portal do Azure. Entre no portal do Azure e inicie sua sessão novamente. Se você acessar a URL diretamente em outra guia ou sessão do navegador, esse erro será esperado. Ele ajuda a garantir que sua sessão seja mais segura e que ela possa ser acessada somente por meio do portal do Azure.
