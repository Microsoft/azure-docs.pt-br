---
title: "Uso do Emulator Express para executar e depurar um serviço de nuvem do Azure em um computador local | Microsoft Docs"
description: "Usando o Emulator Express para executar e depurar um serviço de nuvem em um computador local"
services: visual-studio-online
documentationcenter: n/a
author: kraigb
manager: ghogen
editor: 
ms.assetid: 73108f98-a552-4817-b7a1-551367b71906
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/06/2017
ms.author: kraigb
ms.openlocfilehash: d7d87045569fdc473333deae05f95d1df343b68c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-emulator-express-to-run-and-debug-an-azure-cloud-service-on-a-local-machine"></a>Uso do Emulator Express para executar e depurar um serviço de nuvem do Azure em um computador local
Ao usar o Emulator Express, você poderá testar e depurar um serviço de nuvem sem executar o Visual Studio como um administrador. É possível definir as configurações do projeto para usar o Emulator Express ou o emulador completo, dependendo dos requisitos do seu serviço de nuvem. Para saber mais sobre o emulador completo, consulte [Executar um aplicativo Azure no emulador de computação](storage/common/storage-use-emulator.md).

## <a name="using-emulator-express-in-visual-studio"></a>Uso do Emulator Express no Visual Studio
Quando você cria um projeto n SDK 2.3 ou posterior do Azure, o Emulator Express é usado automaticamente. No caso de projetos existentes que foram criados com uma versão anterior do SDK do Azure, use as seguintes etapas para selecionar o Emulator Express:

1. Crie ou abra um projeto de serviço de nuvem do Azure no Visual Studio.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e, no menu de contexto, selecione **Propriedades**.

1. Nas páginas de propriedades dos projetos, selecione a guia **Web**.

    ![Propriedades de um projeto de serviço de nuvem do Azure](./media/vs-azure-tools-emulator-express-debug-run/web-properties.png)

1. Em **Development Server Local**, escolha a opção **Usar o IIS Express**.

1. Em **Emulador**, selecione **Usar Emulator Express**.
   
1. Para iniciar o Emulator Express, execute o seguinte comando em um prompt de comando: 

    ```
    csrun.exe /useemulatorexpress
    ```

## <a name="emulator-express-limitations"></a>Limitações do Emulator Express
Os seguintes problemas são limitações conhecidas do Emulator Express: 

- O Emulator Express não é compatível com o Servidor Web do IIS.
- O serviço de nuvem pode conter várias funções, mas cada função é limitada a uma instância.
- Você não pode acessar números de porta abaixo de 1000. Se você usar um provedor de autenticação que normalmente usa uma porta abaixo de 1000, talvez seja necessário alterar esse valor para um número de porta acima de 1000.
- As limitações que se aplicam ao Emulador de Computação do Azure também se aplicam ao Emulator Express. Por exemplo, você não pode ter mais de 50 instâncias de função por implantação. Para saber mais sobre o Emulador de Computação do Azure, confira [Executar um aplicativo do Azure no Emulador de Computação](http://go.microsoft.com/fwlink/p/?LinkId=623050).

## <a name="next-steps"></a>Próximas etapas
[Depuração de serviços de nuvem do Azure](https://msdn.microsoft.com/library/azure/ee405479.aspx)
