---
title: Tutorial sobre como provisionar o Azure Data Box Gateway no VMware | Microsoft Docs
description: O segundo tutorial de implantação do Azure Data Box Gateway envolve o provisionamento de um dispositivo virtual no VMware.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 948005942be193c8b15c363282aa06c006eb244e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727610"
---
# <a name="tutorial-provision-azure-data-box-gateway-in-vmware"></a>Tutorial: Provisionar o Azure Data Box Gateway no VMware

## <a name="overview"></a>Visão geral

Este tutorial descreve como provisionar um Data Box Gateway em um sistema de host que executa o VMware ESXi 6.0, 6.5 ou 6.7. 

Você precisa de privilégios de administrador para provisionar e conectar-se a um dispositivo virtual. O provisionamento e a configuração inicial podem levar cerca de 10 minutos para ser concluídos. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Verificar se o host atende aos requisitos mínimos de dispositivo
> * Provisionar um dispositivo virtual no VMWare
> * Iniciar o dispositivo virtual e obter o endereço IP

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos para provisionar um dispositivo virtual em um sistema de host que executa o VMware ESXi 6.0, 6.5 ou 6.7 são os seguintes.

### <a name="for-the-data-box-gateway-resource"></a>Para o recurso de Data Box Gateway

Antes de começar, verifique se:

* Você concluiu todas as etapas em [Preparar o portal para o Data Box Gateway](data-box-gateway-deploy-prep.md).
* Você fez o download da imagem do dispositivo virtual para VMware do portal do Azure, conforme descrito em [Preparar o portal para o Data Box Gateway](data-box-gateway-deploy-prep.md).

  > [!IMPORTANT]
  > O software em execução no Data Box Gateway só pode ser usado com o recurso Data Box Gateway.

### <a name="for-the-data-box-gateway-virtual-device"></a>Para o dispositivo virtual do Data Box Gateway

Antes de implantar um dispositivo virtual, verifique se:

* Você tem acesso a um sistema de host que executa o VMware (ESXi 6.0, 6.5 ou 6.7) que pode ser usado para provisionar um dispositivo.
* O sistema de host é capaz de dedicar os recursos a seguir para provisionar seu dispositivo virtual:

  * Um mínimo de quatro núcleos.
  * Pelo menos 8 GB de RAM. É altamente recomendável ter pelo menos 16 GB de RAM.
  * Uma interface de rede.
  * Um disco de SO de 250 GB.
  * Um disco virtual de 2 TB de dados do sistema.

### <a name="for-the-network-in-datacenter"></a>Para a rede no datacenter

Antes de começar:

- Revise os requisitos de rede para implantar um Data Box Gateway e configurar a rede de datacenter de acordo com os requisitos. Saiba mais em [Requisitos de rede do Data Box Gateway](data-box-gateway-system-requirements.md#networking-port-requirements).
- Garanta que a largura de banda mínima da Internet seja de 20 Mbps, para permitir o funcionamento ideal do dispositivo.

## <a name="check-the-host-system"></a>Verificar o sistema host

Para criar um dispositivo virtual, você precisa de:

* Acesso a um sistema host com VMware ESXi 6.0, 6.5 ou 6.7. O sistema host é capaz de dedicar os recursos a seguir para o seu dispositivo virtual:
 
  * Um mínimo de 4 processadores virtuais.
  * Pelo menos 8 GB de RAM. 
  * Uma interface de rede conectada à rede capaz de rotear o tráfego para a Internet.
  * Um disco de SO de 250 GB.
  * Um disco virtual de 2 TB para dados.
* Cliente VMware vSphere em seu sistema para gerenciar o host ESXi.


## <a name="provision-a-virtual-device-in-hypervisor"></a>Provisionar um dispositivo virtual no hipervisor

Execute as etapas a seguir para provisionar um dispositivo virtual no seu hipervisor.

1. Copie a imagem do dispositivo virtual no seu sistema. Você fez o download dessa imagem virtual (dois arquivos) por meio do portal do Azure. Anote o local em que você copiou a imagem, pois ela será usada posteriormente no procedimento.

2. Entre no servidor ESXi por meio de um navegador nesta URL: `https://<IP address of the ESXi server>`. Você precisa ter privilégios de administrador para criar uma máquina virtual.

   ![Página de entrada](./media/data-box-gateway-deploy-provision-vmware/image1.png)
  
3. Carregar o VMDK para o servidor ESXi. No painel Navegação, selecione **Armazenamento**.

   ![Captura de tela de uma página no site do servidor ESXi que mostra o painel Navegador com a opção de armazenamento selecionada.](./media/data-box-gateway-deploy-provision-vmware/image2.png)

4. No painel direito, em **Repositórios de Dados**, selecione o repositório de dados no qual deseja carregar o VMDK. 

    - O repositório de dados deve ser do tipo VMFS5. 
    - O repositório de dados deve ter espaço livre suficiente para os discos de sistema operacional e de dados.
   
5. Clique com o botão direito do mouse e selecione **Procurar no Repositório de Dados**.

   ![Procurar no Repositório de Dados](./media/data-box-gateway-deploy-provision-vmware/image3.png)

6. Uma janela **Navegador de Repositório de Dados** é exibida.

   ![Navegador de repositório de dados](./media/data-box-gateway-deploy-provision-vmware/image4.png)

7. Na barra de ferramentas, clique no ícone **Criar diretório** para criar uma nova pasta. Especifique o nome da pasta e anote-o. Você usará posteriormente este nome de pasta ao criar uma máquina virtual (melhor prática recomendada). Clique em **Criar diretório**.

   ![Criar diretório](./media/data-box-gateway-deploy-provision-vmware/image5.png)

8. A nova pasta aparece no painel à esquerda do **Navegador do Repositório de Dados**. Clique no ícone **Upload** e selecione **Fazer Upload de Arquivo**.

    ![Carregar arquivo](./media/data-box-gateway-deploy-provision-vmware/image6.png)

9. Procure e aponte para os arquivos VMDK que você baixou. Existem dois itens. Selecione um arquivo para carregar.

    ![Selecione arquivo para carregar](./media/data-box-gateway-deploy-provision-vmware/image7.png)

10. Clique em **Abrir**. O upload do arquivo VMDK para o repositório de dados especificado é iniciado. Pode levar vários minutos para que o arquivo seja carregado.
11. Quando o upload for concluído, você verá o arquivo no repositório de dados na pasta que você criou. Agora carregue o segundo arquivo VMDK para o mesmo repositório de dados. Após o upload dos arquivos, os dois arquivos serão mesclados em um único arquivo. Assim, verá um único arquivo no diretório.

    ![Dois arquivos VMDK são mesclados em um único arquivo](./media/data-box-gateway-deploy-provision-vmware/image8.png)

12. Retorne à janela do cliente vSphere. No painel Navegação, selecione **Máquinas Virtuais**. No painel direito, clique em **Criar/Registrar VM**.

    ![Criar ou registrar a VM](./media/data-box-gateway-deploy-provision-vmware/image9.png)

13. Uma **Nova Máquina Virtual** é exibida. Em Selecionar tipo de criação, escolha **Criar uma nova máquina virtual** e clique em **Avançar**.
    ![Selecione a página de tipo de criação](./media/data-box-gateway-deploy-provision-vmware/image10.png)

14. Na página **Selecionar um Nome e Nome e Local do Sistema Operacional**, especifique o **Nome** da máquina virtual. Esse nome deve corresponder ao nome da pasta (melhor prática recomendada) que você especificou anteriormente na Etapa 7. Escolha **Família do SO convidado** como o Windows e **Versão do SO convidado** como Microsoft Windows Server 2016 (64 bits). Clique em **Próximo**.

    ![Selecionar a página Nome e Nome e Local do Sistema Operacional](./media/data-box-gateway-deploy-provision-vmware/image11.png)

15. Na página **Selecionar armazenamento**, selecione um repositório de dados que você deseja usar para provisionar sua VM. Clique em **Próximo**.

    ![Selecionar a página de armazenamento](./media/data-box-gateway-deploy-provision-vmware/image12.png)
16. Na página **Personalizar as configurações**, defina **CPU** como 4, **Memória** como 8192 MB (ou mais), **Disco rígido 1** como 2 TB (ou mais). Escolha o **disco rígido SCSI** a adicionar. Nesse caso, era SAS de Lógica LSI. **Não há suporte para discos de IDE estáticos.** O **Disco rígido 1** é o disco de dados virtual. Observe que não é possível reduzir o disco após o provisionamento. A tentativa de reduzir o disco resulta na perda de todos os dados locais no dispositivo. 

    ![Personalizar a página de configurações](./media/data-box-gateway-deploy-provision-vmware/image13.png)

    Na mesma página, clique em **Adicionar disco rígido** e, em seguida, selecione **Disco rígido existente**. Selecione o arquivo VMDK no repositório de dados. Isso adicionará um disco do sistema operacional. 

     ![Personalizar configurações – Adicionar um disco rígido](./media/data-box-gateway-deploy-provision-vmware/customize-settings-add-hard-disk.png)

    Role a tela para baixo até ver o **Novo disco rígido** e expanda-o para exibir as configurações. Defina o **Nó do Dispositivo Virtual** como **Controlador IDE 0**.

     ![Personalizar configurações – configurar um novo disco rígido](./media/data-box-gateway-deploy-provision-vmware/customize-settings-new-disk.png)

17. (Opcional) *Execute esta etapa somente se você estiver executando o VMware ESXi Server 6.7*. Na página **Personalizar as configurações**, clique em **Opções de VM**. Acesse **Opções de inicialização > Firmware** e altere-a para **BIOS**. Por padrão, o valor é definido como EFI. Clique em **Próximo**.

    ![Personalizar a página de configurações se executar o VMware ESXi Server 6.7](./media/data-box-gateway-deploy-provision-vmware/customize-settings-new-disk-esxi.png)

18. Na página **Pronto para Concluir** , examine todas as configurações associadas à nova máquina virtual. Verifique se a CPU é 4, memória é 8192 MB, adaptador de rede é 1 e Disco rígido 2 tem o controlador IDE 0. Clique em **Concluir**.
   
    ![Página Pronto para Concluir](./media/data-box-gateway-deploy-provision-vmware/image16.png)
    ![Página Pronto para Concluir 2](./media/data-box-gateway-deploy-provision-vmware/image17.png)

Sua máquina virtual está agora provisionada. Você verá uma notificação sobre isso, e a nova máquina virtual será adicionada à lista de VMs.

![Nova máquina virtual adicionada à lista de VMs](./media/data-box-gateway-deploy-provision-vmware/image17.png)

A próxima etapa é ativar essa VM e obter o endereço IP.

> [!NOTE]
> Recomendamos que você não instale as ferramentas do VMware em seu dispositivo virtual (como provisionado acima). A instalação das ferramentas do VMware resultará em uma configuração sem suporte.

## <a name="start-the-virtual-device-and-get-the-ip"></a>Iniciar o dispositivo virtual e obter o IP

Execute as etapas a seguir para iniciar o dispositivo virtual e conectar-se a ele.

#### <a name="to-start-the-virtual-device"></a>Para iniciar o dispositivo virtual
1. Inicie o dispositivo virtual. No painel direito, selecione seu dispositivo na lista de VMs e clique com o botão direito para abrir o menu de contexto. Selecione **Ligar/Desligar** e, em seguida, selecione **Ligar**. Isso deve ligar sua máquina virtual. Veja o status no painel inferior do cliente da Web.

    ![Ligar o dispositivo virtual](./media/data-box-gateway-deploy-provision-vmware/image19.png)

2. Novamente, selecione sua VM. Clique com botão direito e selecione **Console** e, em seguida, selecione **Abrir em uma nova janela**.

    ![Abrir console do dispositivo virtual](./media/data-box-gateway-deploy-provision-vmware/image20.png)

3. O console da máquina virtual abre em uma nova janela. 

    ![Console do dispositivo virtual](./media/data-box-gateway-deploy-provision-vmware/image21.png)

4. Quando o dispositivo estiver em execução, aponte e clique com o cursor na guia na parte superior central da janela do console. Selecione **Sistema operacional convidado > Enviar chaves > Ctrl+Alt+Delete**. Isso desbloqueará a VM.

   ![Desbloquear o dispositivo virtual](./media/data-box-gateway-deploy-provision-vmware/image22.png)

5. Forneça a senha para entrar no computador. A senha padrão é *Senha1*.

   ![Inserir a senha do dispositivo virtual](./media/data-box-gateway-deploy-provision-vmware/image23.png)

6. As etapas 6 a 8 se aplicam somente na inicialização de um ambiente não DHCP. Se você estiver em um ambiente DHCP, ignore essas etapas e vá para a etapa 9. Caso tenha inicializado seu dispositivo em um ambiente não DHCP, você uma mensagem sobre isso: **Use o cmdlet Set-HcsIPAddress para configurar a rede**. 
   
7. Para configurar a rede, no prompt de comando, use o comando `Get-HcsIpAddress` para listar as interfaces de rede habilitadas em seu dispositivo virtual. Se o dispositivo tiver uma única interface de rede habilitada, o nome padrão atribuído a ela é `Ethernet`.

8. Use o cmdlet `Set-HcsIpAddress` para configurar a rede. Um exemplo é mostrado abaixo:

    `Set-HcsIpAddress –Name Ethernet0 –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

9. Depois que a configuração inicial for concluída e o dispositivo for inicializado, você verá o texto da faixa do dispositivo. Anote o endereço IP e a URL exibida no texto do banner para gerenciar o dispositivo. Você usará esse endereço IP para se conectar à interface do usuário da Web do seu dispositivo virtual e concluir a configuração local e ativação.

   ![Texto do Banner e conexão URL para o dispositivo virtual](./media/data-box-gateway-deploy-provision-vmware/image24.png)

Se o dispositivo não cumprir os requisitos mínimos de configuração, você verá um erro no texto da faixa (mostrado abaixo). Será necessário modificar a configuração do dispositivo para que ele tenha recursos adequados para cumprir os requisitos mínimos. Em seguida, você pode reiniciar e conectar-se ao dispositivo. Consulte os requisitos mínimos de configuração em [Verificar se o sistema host atende aos requisitos mínimos de dispositivo virtual](#check-the-host-system).

Caso observe algum outro erro durante a configuração inicial usando a interface do usuário da Web local, veja os seguintes fluxos de trabalho:

- [Execute testes de diagnóstico para solucionar problemas na configuração da interface do usuário da Web](data-box-gateway-troubleshoot.md#run-diagnostics).
- [Gere um pacote de log e exiba os arquivos de log](data-box-gateway-troubleshoot.md#collect-support-package).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre os tópicos do Data Box Gateway, como:

> [!div class="checklist"]
> * Verificar se o host atende aos requisitos mínimos de dispositivo
> * Provisionar um dispositivo virtual no VMWare
> * Iniciar o dispositivo virtual e obter o endereço IP

Vá para o próximo tutorial para aprender a conectar, configurar e ativar suas redes virtuais.

* [Configurar e conectar-se a compartilhamentos em seu Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md)

