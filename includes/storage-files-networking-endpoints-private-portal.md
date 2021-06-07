---
title: incluir arquivo
description: incluir arquivo
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 5/11/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 09723030f0da0252120f66f36347cad2e3a3546a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93376167"
---
Navegue até a conta de armazenamento para a qual gostaria de criar o ponto de extremidade privado. Na sumário da conta de armazenamento, selecione **Conexões de ponto de extremidade privado** e, em seguida, **+ Ponto de extremidade privado** para criar um ponto de extremidade privado. 

[![Uma captura de tela do item conexões de ponto de extremidade privado no sumário da conta de armazenamento](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-0.png)](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-0.png#lightbox)

O assistente resultante tem várias páginas a serem preenchidas.

Na folha **Básico**, selecione o grupo de recursos, o nome e a região desejados para o ponto de extremidade privado. Essas configurações podem ter o valor que você quiser, elas não precisam corresponder à conta de armazenamento, embora seja necessário criar o ponto de extremidade privado na mesma região que a rede virtual na qual você deseja criá-lo.

![Uma captura de tela da seção Noções Básicas da seção Criar ponto de extremidade privado](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-1.png)

Na folha **Recurso**, selecione o botão de opção para **Conectar-se a um recurso do Azure em meu diretório**. Em **Tipo de recurso**, selecione **Microsoft.Storage/storageAccounts**. O campo **Recurso** é a conta de armazenamento com o compartilhamento de arquivo do Azure ao qual você deseja se conectar. O sub-recurso de destino é **arquivo**, uma vez que se trata dos Arquivos do Azure.

A folha **Configuração** permite que você selecione a rede virtual específica e a sub-rede à qual deseja adicionar o ponto de extremidade privado. Você precisa selecionar uma sub-rede diferente daquela à qual adicionou o ponto de extremidade de serviço acima. A folha Configuração também contém as informações para criar/atualizar a zona DNS privada. Recomendamos usar a zona `privatelink.file.core.windows.net` padrão.

![Captura de tela da seção Configuração](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-2.png)

Clique em **Examinar + Criar** para criar o ponto de extremidade privado. 
