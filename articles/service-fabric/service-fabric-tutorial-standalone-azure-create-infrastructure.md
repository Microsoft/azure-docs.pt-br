---
title: 'Tutorial: Criando a infraestrutura para um cluster do Service Fabric em VMs do Azure – Azure Service Fabric | Microsoft Docs'
description: Neste tutorial, você aprenderá a configurar a infraestrutura da VM do Azure para executar um cluster do Service Fabric.
services: service-fabric
documentationcenter: .net
author: v-vasuke
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: v-vasuke
ms.custom: mvc
ms.openlocfilehash: d9db71a1b64ea6bf2dc73500160ce8e5e6022ef6
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385018"
---
# <a name="tutorial-create-azure-vm-infrastructure-to-host-a-service-fabric-cluster"></a>Tutorial: Criar infraestrutura de VM do Azure para hospedar um cluster do Service Fabric

Os clusters autônomos do Service Fabric oferecem a opção de escolher seu próprio ambiente e criar um cluster como parte da abordagem “qualquer sistema operacional, qualquer nuvem” que está sendo adotada pelo Service Fabric. Nesta série de tutoriais, você cria um cluster autônomo hospedado em VMs do Azure e instala um aplicativo nelas.

Este tutorial é a primeira parte de uma série. Neste artigo, você gerará os recursos de VM do Azure necessários para hospedar o cluster autônomo do Service Fabric. Em artigos futuros, você precisará instalar o conjunto autônomo do Service Fabric, além de um aplicativo de exemplo no seu cluster e, por fim, limpar seu cluster.

Na primeira parte da série, você aprenderá a:

> [!div class="checklist"]
> * Criar um conjunto de instâncias de VM do Azure
> * Modificar o grupo de segurança
> * Fazer logon em uma das instâncias
> * Preparar a instância do Service Fabric

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa de uma assinatura do Azure.  Caso ainda não tenha uma conta, vá até o [portal do Azure](https://portal.azure.com) para criá-la.

## <a name="create-azure-virtual-machine-instances"></a>Criar instâncias de Máquina Virtual do Azure

1. Entre no portal do Azure e selecione **Máquinas virtuais**, não Máquinas Virtuais (clássico).

   ![VM do portal do Azure][az-console]

2. Selecione o botão **Adicionar** para abrir o formulário **Criar uma máquina virtual**.

3. Na guia **Noções Básicas**, escolha o grupo de recursos e a assinatura desejados (recomendamos usar um novo grupo de recursos).

4. Altere o tipo de **Imagem** para **Windows Server 2016 Datacenter**. 
 
5. Altere o **Tamanho** da instância para **Standard DS2 v2**. Defina o **Nome de usuário** e a **Senha** do administrador e anote-os.

6. Deixe as **Regras de Porta de Entrada** bloqueadas por enquanto; vamos configurá-las na próxima seção.

7. Na guia **Rede**, crie uma nova **Rede Virtual** e anote seu nome.

8. Em seguida, defina o **Grupo de segurança de rede do adaptador de rede** como **Avançado**. Crie um novo grupo de segurança, anote seu nome e crie as seguintes regras para permitir tráfego TCP vindo de qualquer fonte:

   ![sf-inbound][sf-inbound]

   * Porta `3389`, para RDP e ICMP (conectividade básica).
   * Portas `19000-19003`, para o Service Fabric.
   * Portas `19080-19081`, para o Service Fabric.
   * Porta `8080`, para solicitações do navegador da Web.

   > [!TIP]
   > Para conectar as máquinas virtuais juntas no Service Fabric, as VMs que estão hospedando sua infraestrutura precisam ter as mesmas credenciais.  Há duas maneiras comuns de obter credenciais consistentes: uni-las todas no mesmo domínio ou definir a mesma senha de administrador em cada VM. Felizmente, o Azure permite que todas as máquinas virtuais na mesma **Rede virtual** conectem-se com facilidade; para isso, precisamos ter todas as nossas instâncias na mesma rede.

9. Adicione outra regra. Defina a fonte como **Marca de Serviço** e defina a marca de serviço da fonte como **VirtualNetwork**. O Service Fabric requer que as seguintes portas estejam abertas para comunicação com o cluster: 135, 137 a 139, 445, 20001 a 20031, 20606 a 20861.

   ![vnet-inbound][vnet-inbound]

10. O restante das opções pode ficar no estado padrão. Examine-as, se quiser, e inicie sua máquina virtual.

## <a name="creating-more-instances-for-your-service-fabric-cluster"></a>Criando mais instâncias para o cluster do Service Fabric

Inicie mais duas **Máquinas Virtuais** e mantenha as mesmas configurações definidas na seção anterior. Mais especificamente, mantenha os mesmos nome de usuário e senha de administrador. A **Rede Virtual** e o **Grupo de segurança de rede do adaptador de rede** não devem ser recriados; selecione os que você já criou no menu suspenso. A implantação de cada instância pode demorar alguns minutos.

## <a name="connect-to-your-instances"></a>Conectar-se às instâncias

1. Selecione uma das instâncias na seção **Máquina Virtual**.

2. Na guia **Visão geral**, anote o endereço IP *privado*. Clique em **Conectar**.

3. Na guia **RDP**, observe que estamos usando o endereço IP público e a porta 3389, que abrimos especificamente em outra etapa. Baixe o arquivo RDP.
 
4. Abra o arquivo RDP e, quando solicitado, insira o nome de usuário e a senha fornecidos na configuração da VM.

5. Quando estiver conectado a uma instância, você precisará validar que o registro remoto estava em execução, habilitar o SMB e abrir as portas necessárias para o SMB e o registro remoto.

   Para habilitar o SMB, use este comando do PowerShell:

   ```powershell
   netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
   ```

6. Este é o comando do PowerShell para abrir as portas no firewall:

   ```powershell
   New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
   ```

7. Repita o processo para suas outras instâncias, anotando sempre os endereços IP privados.

## <a name="verify-your-settings"></a>Verificar suas configurações

1. Para validar a conectividade básica, conecte-se a uma das VMs usando RDP.

2. Abra o **Prompt de Comando** nessa VM e use o comando ping para se conectar de uma VM a outra, substituindo o endereço IP abaixo por um dos endereço IP privado anotado anteriormente (não o IP da VM à qual você já está conectado).

   ```
   ping 172.31.20.163
   ```

   Se a saída se parecer com `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128` repetido quatro vezes, isso significa que sua conexão entre as instâncias está funcionando.

3. Agora valide se seu compartilhamento SMB funciona com o seguinte comando:

   ```
   net use * \\172.31.20.163\c$
   ```

   Ele deve retornar `Drive Z: is now connected to \\172.31.20.163\c$.` como a saída.


   Agora suas instâncias já estão devidamente preparadas para o Service Fabric.

## <a name="next-steps"></a>Próximas etapas

Na primeira parte da série, você aprendeu como iniciar três instâncias de VM do Azure e como configurá-las para a instalação do Service Fabric:

> [!div class="checklist"]
> * Criar um conjunto de instâncias de VM do Azure
> * Modificar o grupo de segurança
> * Fazer logon em uma das instâncias
> * Preparar a instância do Service Fabric

Avance até segunda parte da série para configurar o Service Fabric no cluster.

> [!div class="nextstepaction"]
> [Instalar o Service Fabric](service-fabric-tutorial-standalone-create-service-fabric-cluster.md)

<!-- IMAGES -->
[az-console]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/az-console.png
[sf-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/sf-inbound.png
[vnet-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/vnet-inbound.png
