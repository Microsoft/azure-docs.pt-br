---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 81d2804d99896200ea6f68592ea168112e172c20
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/24/2020
ms.locfileid: "97762583"
---
Selecione **Compilar**. No painel que será aberto, selecione uma pasta para exportar o projeto do Xcode.

   Depois que a exportação for concluída, a pasta que contém o projeto do Xcode exportado será exibida.

   > [!NOTE]
   > Caso uma janela apareça com uma mensagem perguntando se você deseja substituir ou acrescentar, recomendamos clicar em **Acrescentar** porque essa é a opção mais rápida. Você deverá clicar em **Substituir** somente se estiver alterando ativos em sua cena. Por exemplo, pode ser que você esteja adicionando, removendo ou alterando relações pai/filho, bem como adicionando, removendo ou alterando as propriedades. Se você estiver fazendo alterações no código fonte, apenas **Acrescentar** deve ser suficiente.

## <a name="open-the-xcode-project"></a>Abrir o projeto do Xcode

Agora você pode abrir o projeto `Unity-iPhone.xcodeproj` no Xcode. 

É possível iniciar o Xcode e abrir o projeto `Unity-iPhone.xcodeproj` exportado ou iniciar o projeto no Xcode executando o seguinte comando na localização em que você exportou o projeto:

 ```bash
open ./Unity-iPhone.xcodeproj
```

Selecione o nó raiz **Unity-iPhone** para exibir as configurações do projeto e, em seguida, selecione a guia **Geral**.

Em **Informações sobre a implantação**, verifique se o destino da implantação está definido como **iOS 11.0**.

Selecione a guia **Assinatura e Recursos**, bem como verifique se a opção **Gerenciar assinatura de modo automático** está habilitada. Caso contrário, habilite a opção e redefina as configurações de build selecionando **Habilitação Automática** no painel que aparecer.

## <a name="deploy-the-app-to-your-ios-device"></a>Implantar o aplicativo em seu dispositivo iOS

Conecte o dispositivo iOS ao Mac e defina o **esquema ativo** para seu dispositivo iOS.

   ![Uma captura de tela do botão Meu iPhone para selecionar o dispositivo.](./media/spatial-anchors-unity/select-device.png)

Selecione **Compilar e, em seguida, executar o esquema atual**.

   ![Uma captura de tela do botão de seta "Implantar e executar".](./media/spatial-anchors-unity/deploy-run.png)
