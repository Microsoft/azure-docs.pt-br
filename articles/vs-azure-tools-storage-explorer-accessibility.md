---
title: Acessibilidade de Gerenciador de Armazenamento do Azure | Microsoft Docs
description: Entenda a acessibilidade no Gerenciador de Armazenamento do Azure. Examine quais leitores de tela estão disponíveis, a capacidade de zoom, temas de alto contraste e teclas de atalho.
services: storage
documentationcenter: na
author: MrayermannMSFT
manager: jinglouMSFT
editor: ''
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2018
ms.author: marayerm
ms.openlocfilehash: ca4a8d719277eaa1d853d53d282649f839256be9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88035478"
---
# <a name="storage-explorer-accessibility"></a>Acessibilidade do Gerenciador de Armazenamento

## <a name="screen-readers"></a>Leitores de tela

O Gerenciador de Armazenamento dá suporte ao uso de um leitor de tela no Windows e no Mac. Os leitores de tela abaixo são recomendados para cada plataforma:

Plataforma | Leitor de tela
---------|--------------
Windows  | NVDA
Mac      | Voice over
Linux    | (os leitores de tela não têm suporte no Linux)

Se você tiver um problema de acessibilidade ao usar o Gerenciador de Armazenamento, [abra um problema no GitHub](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="zoom"></a>Zoom

Você pode ampliar o texto no Gerenciador de Armazenamento usando o recurso de ampliação. Para ampliar, clique em **Ampliar** no menu Ajuda. Você também pode usar o menu Ajuda para reduzir e redefinir o nível de zoom de volta para o nível padrão.

![Opções de ampliação no menu Ajuda][0]

A configuração de zoom aumenta o tamanho da maioria dos elementos de interface do usuário. É recomendável também habilitar texto grande e ampliar as configurações para seu sistema operacional para garantir que todos os elementos de interface do usuário são dimensionados corretamente.

## <a name="high-contrast-themes"></a>Temas de Alto Contraste

O Gerenciador de Armazenamento tem dois temas de alto contraste, **Alto Contraste Claro** e **Alto Contraste Escuro**. Você pode alterar seu tema selecionando no no menu ajuda > temas.

![Submenu Temas][1]

A configuração de tema altera a cor da maioria dos elementos de interface do usuário. É recomendável habilitar também o tema de alto contraste correspondente do seu sistema operacional para garantir que todos os elementos de interface do usuário sejam coloridos corretamente.

## <a name="shortcut-keys"></a>Teclas de Atalho

### <a name="window-commands"></a>Comandos de janela

Comando       | Atalho de teclado
--------------|--------------------
Nova Janela    | **Control+Shift+N**
Fechar Editor  | **Control+F4**
Encerrar          | **Control+Shift+W**

### <a name="navigation-commands"></a>Comandos de navegação

Comando                | Atalho de teclado
-----------------------|----------------------
Foco próximo painel       | **F6**
Foco painel anterior   | **Shift + F6**
Explorer               | **Control+Shift+E**
Gerenciamento de Contas     | **Control+Shift+A**
Alternar a barra lateral        | **Control+B**
Log de Atividade           | **Control+Shift+L**
Ações e Propriedades | **Control+Shift+P**
Editor atual         | **Control+Home**
Próximo editor            | **Control+Page Down**
Editor anterior        | **Control+Page Up**

### <a name="zoom-commands"></a>Comandos de zoom

Comando  | Atalho de teclado
---------|------------------
Ampliar  | **Controle + =**
Reduzir | **Controle +-**

### <a name="blob-and-file-share-editor-commands"></a>Comandos do Editor de compartilhamento de arquivo e blob

Comando | Atalho de teclado
--------|--------------------
Voltar    | **Alt + seta para esquerda**
Encaminhar | **Alt + seta para a direita**
Up      | **Alt+Up Arrow**

### <a name="editor-commands"></a>Comandos do editor

Comando | Atalho de teclado
--------|------------------
Copiar    | **Control + C**
Recortar     | **Control + X**
Colar   | **Control + V**
Atualizar  | **Control+R**

### <a name="other-commands"></a>Outros comandos

Comando                | Atalho de teclado
-----------------------|------------------
Alterar ferramentas para desenvolvedores | **F12**
Recarregar                 | **Alt + Control + R**

[0]: ./media/vs-azure-tools-storage-explorer-accessibility/Zoom.png
[1]: ./media/vs-azure-tools-storage-explorer-accessibility/HighContrast.png
