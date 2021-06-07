---
title: Conectar-se à Área de Trabalho Virtual do Windows do Android – Azure
description: Como se conectar à Área de Trabalho Virtual do Windows usando o cliente Android.
author: Heidilohr
ms.topic: how-to
ms.date: 03/25/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 07e0754cc9fb940218999e2012a9cb87341557cb
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448076"
---
# <a name="connect-to-windows-virtual-desktop-with-the-android-client"></a>Conectar-se à Área de Trabalho Virtual do Windows usando o cliente Android

> Aplica-se a: Android 4.1 e superior, Chromebooks com ChromeOS 53 e superior.

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver usando a Área de Trabalho Virtual do Windows (clássica), sem objetos do Azure Resource Manager, confira [este artigo](./virtual-desktop-fall-2019/connect-android-2019.md).

Você pode acessar os recursos da Área de Trabalho Virtual do Windows no seu dispositivo Android com nosso cliente disponível para download. Você também pode usar o cliente Android em dispositivos Chromebook que dão suporte à Google Play Store. Este guia descreve como configurar o cliente Android.

## <a name="install-the-android-client"></a>Instalar o cliente Android

Para começar, [baixe](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) e instale o cliente no seu dispositivo Android.

## <a name="subscribe-to-a-feed"></a>Assinar um feed

Assine o feed fornecido pelo seu administrador para obter uma lista de recursos gerenciados que você pode acessar no seu dispositivo Android.

Para assinar um feed:

1. Na Central de Conexão, toque em **+** e, em seguida, toque em **Feed de Recursos Remotos**.
2. Insira a URL do feed no campo **URL do Feed**. Essa URL do feed pode ser uma URL ou um endereço de email.
   - Se usar uma URL, use a que o administrador forneceu a você, normalmente <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery>.
   - Para usar email, insira seu endereço de email. O cliente pesquisará uma URL associada ao seu endereço de email, caso o administrador tenha configurado o servidor dessa maneira.
   - Para se conectar por meio do portal de US Gov, use <https://rdweb.wvd.azure.us/api/arm/feeddiscovery>.
3. Toque em **AVANÇAR**.
4. Forneça suas credenciais quando solicitado.
   - Em **Nome de usuário**, forneça o nome de usuário com permissão para acessar recursos.
   - Em **Senha**, forneça a senha associada ao nome de usuário.
   - Talvez você também receba uma solicitado para fornecer fatores adicionais se o administrador configurou a autenticação dessa maneira.

Depois de assinar, a Central de Conexão deve exibir os recursos remotos.

Após a assinatura de um feed, o conteúdo dele será atualizado automaticamente de maneira regular. Os recursos podem ser adicionados, alterados ou removidos com base nas alterações realizadas pelo seu administrador.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar o cliente Android, confira [Introdução ao cliente Android](/windows-server/remote/remote-desktop-services/clients/remote-desktop-android/).
