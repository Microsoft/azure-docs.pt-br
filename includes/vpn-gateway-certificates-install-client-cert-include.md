---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 393b29245141b2970e7c1a227d6e8b1b131c445c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93061580"
---
Se você quiser criar uma conexão P2S de um computador cliente diferente daquele usada para gerar os certificados cliente, instale um certificado de cliente. Ao instalar um certificado do cliente, você precisará da senha criada durante a exportação do certificado do cliente.

1. Localize e copie o arquivo *.pfx* no computador cliente. No computador cliente, clique duas vezes no arquivo *.pfx* para instalá-lo. Deixe o **local do repositório** como **usuário atual** e, em seguida, selecione **Avançar**.
1. Na página **Arquivo** a importar, não faça nenhuma alteração. Selecione **Avançar**.
1. Na página **proteção de chave privada** , insira a senha do certificado ou verifique se a entidade de segurança está correta e, em seguida, selecione **Avançar**.
1. Na página **repositório de certificados** , deixe o local padrão e, em seguida, selecione **Avançar**.
1. Selecione **Concluir**. No **aviso de segurança** para a instalação do certificado, selecione **Sim**. Você pode selecionar ' Sim ' confortavelmente para este aviso de segurança porque gerou o certificado.
1. O certificado foi importado com êxito.
