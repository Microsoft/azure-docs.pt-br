---
title: incluir arquivo
description: incluir arquivo
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 8d7ac457041474f4e774414b1d5e6f9ed09dc856
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "67172019"
---
<!-- this tells how to get the connection string for your hub -->
<!-- This assumes the user is looking at his hub in the portal. -->

Depois que o hub tiver sido criado, recupere a cadeia de conexão para o hub. Isso é usado para se conectar a dispositivos e aplicativos para o hub. 

1. Clique em seu hub para ver o painel do Hub IoT com as Configurações e assim por diante. Clique em **Políticas de acesso compartilhado**.
   
2. Em **Políticas de acesso compartilhado**, selecione a política **iothubowner**. 

3. Em **Chaves de acesso compartilhado**, copie a **Cadeia de Conexão – chave primária** a ser usada posteriormente.

    ![Mostrar como recuperar a cadeia de conexão](./media/iot-hub-include-find-connection-string/iot-hub-get-connection-string.png)

    Para obter mais informações, consulte [Controle de acesso](../articles/iot-hub/iot-hub-devguide-security.md) no "Guia do desenvolvedor do Hub IoT".
