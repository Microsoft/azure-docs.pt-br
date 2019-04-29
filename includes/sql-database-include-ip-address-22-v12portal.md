---
title: Regras de firewall no nível do servidor
description: Regras de firewall no nível do servidor
keywords: conexão sql, cadeia de conexão
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 8d0f9899dbb7599340b8d15ca010a0157011fb9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60202064"
---
1. Entre no [Portal do Azure](https://portal.azure.com/).

2. Na lista à esquerda, selecione **Todos os serviços**.

3. Role e selecione **servidores SQL**.

    ![Encontre o seu servidor do Banco de Dados SQL do Azure no portal][b21-FindServerInPortal]
5. Na caixa de texto de filtro, comece digitando o nome do seu servidor. A linha é exibida.

6. Selecione a linha do servidor. Uma folha do servidor é exibida.

7. Na folha do seu servidor, selecione **Configurações**.

8. Selecione **Firewall**.

    ![Selecione Configurações > Firewall][b31-SettingsFirewallNavig]
9. Selecione **Adicionar Cliente IP**. Digite um nome para a nova regra na primeira caixa de texto.

10. Digite os valores baixos e altos de endereços IP para o intervalo que deseja habilitar.

    * Pode ser útil ter o valor baixo terminado com **0,0** e o valor alto terminado com **0,255**.

11. Clique em **Salvar**.

<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->
