---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/08/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0c5e3a095b312d36cb05a78e84a13bc2a73d95d5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91875523"
---
As configurações de DNS não são parte obrigatória dessa configuração, mas o DNS será necessário se você quiser a resolução de nomes entre as VMs. A especificação de um valor não cria um novo servidor DNS. O endereço IP do servidor DNS especificado deve ser um servidor DNS que pode resolver os nomes dos recursos aos quais você está se conectando.

Depois de criar a rede virtual, você pode adicionar o endereço IP de um servidor DNS para lidar com a resolução de nomes. Abra as configurações para sua rede virtual, selecione servidores DNS e adicione o endereço IP do servidor DNS que você deseja usar para a resolução de nomes.

1. Localize a rede virtual no Portal.
2. Na página de sua rede virtual, na seção **configurações** , selecione **servidores DNS**.
3. Adicionar um servidor DNS.
4. Para salvar suas configurações, selecione **salvar** na parte superior da página.