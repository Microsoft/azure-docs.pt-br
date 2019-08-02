---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: d8b6b1bfcbceb1168d0f74c73e72bd42b41bb2ec
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562498"
---
Selecione **Compilar**. Na caixa de diálogo que é aberta, selecione uma pasta para exportar o projeto Xcode.

Quando a exportação estiver concluída, uma pasta contendo o projeto Xcode aparecerá.

> [!NOTE]
> Se for exibida uma janela perguntando se você deseja substituir ou anexar, é recomendável que você selecione **Acrescentar** porque ele é mais rápido. Você só precisa selecionar **Substituir** se você estiver alterando ativos na sua cena. (por exemplo, se você estiver adicionando, removendo, ou alterando relações pai/filho ou se você estiver adicionando, removendo ou alterando as propriedades.) Se você estiver fazendo alterações no código fonte, apenas **Acrescentar** deve ser suficiente.

### <a name="convert-the-xcode-project-to-xcworkspace-containing-azure-spatial-anchors-references"></a>Converter o projeto Xcode em um xcworkspace contendo referências às Âncoras Espaciais do Azure

Na pasta do projeto do Xcode exportado, execute este comando para instalar o CocoaPods necessário para o projeto:

```bash
pod install --repo-update
```

Agora você pode abrir `Unity-iPhone.xcworkspace` para abrir o projeto no Xcode:

```bash
open ./Unity-iPhone.xcworkspace
```

> [!NOTE]
> Se um erro `library not found for -lPods-Unity-iPhone` é exibido, provavelmente, você abriu o arquivo `.xcodeproj` em vez do arquivo `.xcworkspace`. 

Selecione o nó raiz **Unity-iPhone** para exibir as configurações do projeto e, em seguida, selecione a guia **Geral**.

Em **Assinando**, verifique se **Gerenciar assinatura automaticamente** está habilitado. Se não estiver, habilitá-o e, em seguida, selecione **Habilitar Automático** na caixa de diálogo que aparece para redefinir as configurações de compilação.

Em **Informações de Implantação**, verifique se o **Destino de Implantação** está definido como `11.0`.

### <a name="deploy-the-app-to-your-ios-device"></a>Implantar o aplicativo em seu dispositivo iOS

Conecte o dispositivo iOS ao Mac e defina o **esquema ativo** como o dispositivo iOS.

![Selecione o dispositivo](./media/spatial-anchors-unity/select-device.png)

Selecione **Compilar e, em seguida, executar o esquema atual**.

![Implantar e executar](./media/spatial-anchors-unity/deploy-run.png)