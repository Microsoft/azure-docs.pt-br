---
title: Links adicionais sobre o Avere vFXT para Azure
description: Use estes recursos para obter informações adicionais sobre o avere vFXT para Azure, incluindo a documentação do cluster avere e a documentação de gerenciamento do vFXT.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 6f9a9f8e6d2a279871ded3dff614be99dd2901df
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88271083"
---
# <a name="additional-documentation"></a>Documentação adicional

Este artigo contém links para documentação adicional sobre a interface de gerenciamento do painel de controle do Avere, bem como tópicos relacionados.

## <a name="avere-cluster-documentation"></a>Documentação sobre o cluster do Avere

Documentação adicional sobre o cluster do Avere pode ser encontrada no site em <https://azure.github.io/Avere/>. Esses documentos podem ajudar você a compreender os recursos do cluster e como definir suas configurações.

* O [Guia de Criação de Cluster FXT](<https://azure.github.io/Avere/#fxt_cluster>) foi projetado para clusters compostos por nós de hardware físico, mas algumas informações no documento são relevantes também para clusters do vFXT. Em particular, novos administradores de cluster do vFXT podem se beneficiar da leitura destas seções:
  * [Personaliza configurações de monitoramento e suporte](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_support.html#config-support>) explica como personalizar configurações de upload de suporte e habilitar o monitoramento remoto.
  * [Configurar VServers e o Namespace Global](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_vserver.html#config-vserver>) tem informações sobre como criar um namespace voltado ao cliente.
  * [Configurar o DNS para o cluster do Avere](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_network.html#dns-overview>) explica como configurar o DNS com round robin.
  * [Adicionar o armazenamento de back-end](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) documenta como adicionar os principais Filers.

* O [Guia de Configuração do Cluster](<https://azure.github.io/Avere/#operations>) é uma referência completa de configurações e opções para um cluster do Avere. Um cluster do vFXT usa um subconjunto dessas opções, mas a maioria das mesmas páginas de configuração se aplica.

* O [Guia do Painel](<https://azure.github.io/Avere/#operations>) explica como usar o monitoramento de recursos de cluster do Painel de Controle do Avere.

## <a name="vfxt-creation-and-management-documentation"></a>documentação sobre criação e gerenciamento de vFXT

Um guia completo para usar o vfxt.py, um utilitário de criação e gerenciamento de cluster de nuvem baseado em script, é fornecido no GitHub: [Gerenciamento de cluster de nuvem com vfxt.py](https://github.com/Azure/AvereSDK/blob/master/docs/README.md).
