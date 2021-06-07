---
title: Conectar-se ao Microsoft Store cliente – Azure
description: Como se conectar à área de trabalho virtual do Windows usando o cliente do Microsoft Store.
author: Heidilohr
ms.topic: how-to
ms.date: 10/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9bab2a11ff9e7907621087e5027929e3e42eaf29
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91744726"
---
# <a name="connect-with-the-microsoft-store-client"></a>Conectar-se com o cliente Microsoft Store

>Aplica-se a: Windows 10.

Você pode acessar os recursos da área de trabalho virtual do Windows em dispositivos com o Windows 10.

## <a name="install-the-microsoft-store-client"></a>Instalar o cliente do Microsoft Store

Você pode instalar o cliente do para o usuário atual, que não exige direitos de administrador. Como alternativa, seu administrador pode instalar e configurar o cliente para que todos os usuários no dispositivo possam acessá-lo.

Depois de instalado, o cliente pode ser iniciado no menu Iniciar pesquisando Área de Trabalho Remota.

Para começar, [Baixe e instale o cliente do Microsoft Store](https://www.microsoft.com/store/productId/9WZDNCRFJ3PS).

## <a name="subscribe-to-a-workspace"></a>Assinar um espaço de trabalho

Assine o espaço de trabalho fornecido pelo administrador para obter a lista de recursos gerenciados que você pode acessar em seu computador.

Para assinar um espaço de trabalho:

1. Na tela do centro de conexão, toque em **+ Adicionar** e, em seguida, toque em **espaços de trabalho**.
2. Insira a URL do espaço de trabalho no campo URL do espaço de trabalho fornecido pelo administrador. A URL do espaço de trabalho pode ser uma URL ou um endereço de email.
   
   - Se você estiver usando uma URL de espaço de trabalho, use a URL que o administrador lhe forneceu.
   - Se você estiver se conectando da área de trabalho virtual do Windows, use uma das seguintes URLs dependendo da versão do serviço que você está usando:
       - Área de trabalho virtual do Windows (clássica): `https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx` .
       - Área de trabalho virtual do Windows: `https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery` .
  
3. Toque em **assinar**.
4. Forneça suas credenciais quando solicitado.
5. Após a assinatura, os espaços de trabalho devem ser exibidos no centro de conexões.

Os espaços de trabalho podem ser adicionados, alterados ou removidos com base nas alterações feitas pelo administrador.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar o cliente do Microsoft Store, confira introdução [ao cliente do Microsoft Store](/windows-server/remote/remote-desktop-services/clients/windows/).