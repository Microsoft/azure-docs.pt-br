---
title: Inicializar o hardware – Microsoft Azure FXT Edge Filer
description: Como definir uma senha inicial nos nós do Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 11cf9f49014648fff1e78aff91c5a724a812e9e7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67451757"
---
# <a name="tutorial-set-hardware-passwords"></a>Tutorial: Definir senhas de hardware

Da primeira vez que você ligar um nó do Azure FXT Edge Filer, defina uma senha raiz. Os nós de hardware não são entregues com uma senha padrão. 

As portas de rede serão desabilitadas até a senha ser definida e o usuário raiz entrar.

Siga esta etapa após a instalação e o cabeamento do nó, mas antes de tentar criar o cluster. 

Este tutorial explica como se conectar ao nó de hardware e definir a senha. 

Neste tutorial, você aprenderá a: 

> [!div class="checklist"]
> * Conectar um teclado e o monitor ao nó e ligá-lo
> * Definir as senhas do usuário raiz e a porta do iDRAC neste nó
> * Entrar como raiz 

Repita estas etapas para cada nó que você usará em seu cluster. 

Este tutorial leva cerca de 15 minutos para ser concluído. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, conclua estas etapas: 

* [Instale](fxt-install.md) cada nó do Azure FXT Edge Filer em um rack de equipamento e conecte os cabos de alimentação e o acesso à rede conforme descrito no [tutorial anterior](fxt-network-power.md). 
* Encontre um teclado conectado por USB e um monitor com conexão VGA que possam ser conectados aos nós de hardware. A porta serial do nó fica inativa até você definir a senha.

## <a name="connect-a-keyboard-and-monitor-to-the-node"></a>Conectar o teclado e o monitor ao nó

Conecte fisicamente um monitor e um teclado ao nó do Azure FXT Edge Filer. 

* Conecte o monitor à porta VGA.
* Conecte o teclado a uma das portas USB. 

Use este diagrama de referência para localizar as portas na parte posterior do chassi. 

> [!NOTE]
> A porta serial fica inativa até que a senha é definida. 

![Diagrama da parte posterior do Azure FXT Edge Filer com as portas serial, VGA e USB rotuladas](media/fxt-back-serial-vga-usb.png)

Conecte um comutador KVM se quiser conectar mais de um nó aos mesmos periféricos. 

Ative o nó pressionando o botão de energia na parte frontal. 

![Diagrama da frente do Azure FXT Edge Filer – o botão de energia redondo está rotulado perto do canto superior direito](media/fxt-front-annotated.png)

## <a name="set-initial-passwords"></a>Definir senhas inicias 

O nó do Azure FXT Edge Filer imprimirá várias mensagens no monitor durante a inicialização. Após alguns instantes, ele mostrará uma tela de configuração inicial, como esta:

```
------------------------------------------------------
        Microsoft FXT node initial setup
------------------------------------------------------
Password Setup
---------------
Enter a password to set iDRAC and temporary root password.
Minimum password length is 8.
Enter new password:
```

A senha inserida é usada para duas coisas: 

* É a senha raiz temporária para este nó do Azure FXT Edge Filer. 

  Essa senha será alterada quando você criar um cluster com este nó ou quando você adicionar este nó ao cluster. A senha de gerenciamento de cluster (associada ao usuário ``admin``) também é a senha raiz para todos os nós em um cluster.

* É a senha de longo prazo para a porta de gerenciamento de hardware do iDRAC/IPMI.

  É importante lembrar da senha caso seja necessário entrar com o IPMI posteriormente para solucionar um problema de hardware.

Digite e confirme a senha: 

```
Enter new password:**********
Re-enter password:**********
Loading AvereOS......
```

Depois que você digita a senha, o sistema continua com a inicialização. Quando termina, ele oferece um aviso do ``login:``. 

## <a name="sign-in-as-root"></a>Entrar como raiz

Entre como ``root`` com a senha que você acabou de definir. 

```
login: root
Password:**********
```

Depois que você entrar como raiz, as portas de rede serão ativadas e entrarão em contato com o servidor DHCP para endereços IP. 

## <a name="next-steps"></a>Próximas etapas

O nó está pronto para fazer parte de um cluster. Você pode usá-lo para criar o cluster do Azure FXT Edge Filer ou pode [adicioná-lo a um cluster existente](fxt-add-nodes.md). 

> [!div class="nextstepaction"]
> [Criar um cluster](fxt-cluster-create.md)
