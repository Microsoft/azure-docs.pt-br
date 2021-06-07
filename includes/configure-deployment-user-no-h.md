---
title: incluir arquivo
description: incluir arquivo
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/14/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 8f1fe6ae38c708c5205f8c1230da05457d6b7010
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764289"
---
O FTP e o Git local podem implantar em um aplicativo Web do Azure usando um *usuário de implantação*. Após configurar o usuário de implantação, use-o em todas as implantações do Azure. O nome de usuário e a senha da implantação no nível da conta são diferentes das credenciais de assinatura do Azure. 

Para configurar o usuário de implantação, execute o comando [az webapp deployment user set](/cli/azure/webapp/deployment/user#az_webapp_deployment_user_set) no Azure Cloud Shell. Substitua \<username> e \<password> pelo nome de usuário e a senha do usuário de implantação. 

- O nome de usuário precisa ser exclusivo no Azure. Para envios por push do Git local, não deve conter o símbolo "\@". 
- A senha deve ter pelo menos oito caracteres, com dois destes três elementos: letras, números, símbolos. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

A saída JSON mostra a senha como `null`. Se receber um erro `'Conflict'. Details: 409`, altere o nome de usuário. Se receber um erro `'Bad Request'. Details: 400`, use uma senha mais forte. 

Registre seu nome de usuário e senha para usá-los na implantação de aplicativos Web.
