---
title: Locais de dados para a área de trabalho virtual do Windows (clássico)-Azure
description: Uma breve visão geral de quais locais os dados e metadados da área de trabalho virtual do Windows (clássico) são armazenados.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 869defde657c9cb4c8bea6bbacebb9458e5a2b96
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88008807"
---
# <a name="data-locations-for-windows-virtual-desktop-classic"></a>Locais de dados para a área de trabalho virtual do Windows (clássico)

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows (clássica), que não é compatível com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver tentando gerenciar objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager, confira [este artigo](../data-locations.md).

A área de trabalho virtual do Windows está disponível atualmente para todas as localizações geográficas. Inicialmente, os metadados de serviço só podem ser armazenados na geografia de Estados Unidos (EUA). Os administradores podem escolher o local para armazenar os dados do usuário ao criarem as máquinas virtuais do pool de hosts e serviços associados, como servidores de arquivos. Saiba mais sobre as geografias do Azure no [mapa de datacenter do Azure](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>A Microsoft não controla nem limita as regiões nas quais você ou seus usuários podem acessar seus dados específicos do aplicativo e do usuário.

>[!IMPORTANT]
>A área de trabalho virtual do Windows armazena informações de metadados globais, como nomes de locatário, nomes de pool de hosts, nomes de grupos de aplicativos e nomes de entidades de usuário em um Datacenter localizado no Estados Unidos. Os metadados armazenados são criptografados em repouso e os espelhos com redundância geográfica são mantidos dentro do Estados Unidos. Todos os dados do cliente, como configurações do aplicativo e dados do usuário, residem no local escolhido pelo cliente e não são gerenciados pelo serviço.

Os metadados de serviço são replicados no Estados Unidos para fins de recuperação de desastre.