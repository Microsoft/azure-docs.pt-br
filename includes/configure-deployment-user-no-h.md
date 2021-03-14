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
ms.openlocfilehash: a972b4738ce5646a1ee9eed6495bdc43a40826fd
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102219022"
---
O FTP e o Git local podem implantar em um aplicativo Web do Azure usando um *usuário de implantação*. Após configurar o usuário de implantação, use-o em todas as implantações do Azure. O nome de usuário e a senha da implantação no nível da conta são diferentes das credenciais de assinatura do Azure. 

Para configurar o usuário de implantação, execute o comando [az webapp deployment user set](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) no Azure Cloud Shell. Substitua \<username> e \<password> pelo nome de usuário e a senha do usuário de implantação. 

- O nome de usuário precisa ser exclusivo no Azure. Para envios por push do Git local, não deve conter o símbolo "\@". 
- A senha deve ter pelo menos oito caracteres, com dois destes três elementos: letras, números, símbolos. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

A saída JSON mostra a senha como `null`. Se receber um erro `'Conflict'. Details: 409`, altere o nome de usuário. Se receber um erro `'Bad Request'. Details: 400`, use uma senha mais forte. 

Registre seu nome de usuário e senha para usá-los na implantação de aplicativos Web.
