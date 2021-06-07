---
title: Início rápido-criar uma VM do Azure VMware em uma nuvem privada-solução do Azure VMware por CloudSimple
description: Saiba como criar uma máquina virtual VMware na nuvem privada do CloudSimple. Acesse o portal do CloudSimple da portal do Azure.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c741f01ccc3e34ec991aa77fc850fd9f60c4d8aa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97899279"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>Crie máquinas virtuais VMware em sua nuvem privada

Para criar máquinas virtuais em sua nuvem privada, comece acessando o portal do CloudSimple da portal do Azure.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Acessar o portal da CloudSimple

1. Selecione **Todos os serviços**.
2. Procure **Serviços CloudSimples**.
3. Selecione o serviço CloudSimple no qual você deseja criar sua nuvem privada.
4. Na página **visão geral** , clique em **ir para o portal do CloudSimple** para abrir uma nova guia do navegador para o portal do CloudSimple.  Se solicitado, entre com suas credenciais de entrada do Azure.  

    ![Iniciar o portal do CloudSimple](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>Iniciar o vCenter Web-UI

Agora você pode iniciar o vCenter para configurar máquinas virtuais e políticas.

Para acessar o vCenter, inicie no portal do CloudSimple. Na Home Page, em **tarefas comuns**, clique em **Iniciar vSphere cliente**.  Selecione a nuvem privada e clique em **Iniciar vSphere cliente** na nuvem privada.

   ![Iniciar cliente do vSphere](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>Carregar um modelo ISO ou vSphere

  > [!WARNING]
  > Para o carregamento ISO, use o cliente vSphere HTML5.  O uso de um cliente Flash pode resultar em um erro.

1. Obtenha o modelo ISO ou vSphere que você deseja carregar no vCenter para criar uma VM e disponibilizá-la no sistema local.
2. No vCenter, clique no ícone de **disco** e selecione **vsanDatastore**. Clique em **arquivos** e, em seguida, clique em **nova pasta**.
    ![ISO do vCenter](media/vciso00.png)

3. Crie uma pasta chamada ' ISOs e templates '.

4. Navegue até a pasta ISOs em ISOs e modelos e clique em **carregar arquivos**. Siga as instruções na tela para carregar o ISO.

## <a name="create-a-virtual-machine-in-vcenter"></a>Criar uma máquina virtual no vCenter

1. No vCenter, clique no ícone **hosts e clusters** .

2. Clique com o botão direito do mouse em **carga de trabalho** e selecione **nova máquina virtual**.
    ![Captura de tela que realça a opção de menu nova máquina virtual.](media/vcvm01.png)

3. Selecione **criar nova máquina virtual** e clique em **Avançar**.
    ![Captura de tela que realça a opção Criar nova máquina virtual.](media/vcvm02.png)

4. Nomeie o computador, selecione o local **da VM de carga de trabalho** e clique em **Avançar**.
    ![Captura de tela que realça a opção de VMs de carga de trabalho.](media/vcvm03.png)

5. Selecione o recurso de computação de **carga de trabalho** e clique em **Avançar**.
    ![Captura de tela que realça o recurso de computação de carga de trabalho.](media/vcvm04.png)

6. Selecione **vsanDatastore** e clique em **Avançar**.
    ![Captura de tela que realça a opção vsanDatastore.](media/vcvm05.png)

7. Mantenha a seleção de compatibilidade do ESXi 6,5 padrão e clique em **Avançar**.
    ![Captura de tela que mostra a opção de compatibilidade ESXi 6,5 selecionada.](media/vcvm06.png)

8. Selecione o sistema operacional convidado do ISO para a VM que você está criando e clique em **Avançar**.
    ![Captura de tela que mostra como selecionar o sistema operacional guese do ISO para a VM.](media/vcvm07.png)

9. Selecione opções de disco rígido e rede. Para nova unidade de CD/DVD, selecione o **arquivo ISO do repositório de armazenamento**.  Se você quiser permitir o tráfego do endereço IP público para essa VM, selecione a rede como **VM-1**.
    ![Captura de tela que realça onde você seleciona o arquivo ISO do repositório de armazenamento.](media/vcvm08.png)

10. Uma janela de seleção é aberta. Selecione o arquivo que você carregou anteriormente para a pasta ISOs e modelos e clique em **OK**.
    ![Nova VM](media/vcvm10.png)

11. Examine as configurações e clique em **OK** para criar a VM.
    ![Captura de tela que mostra onde você revisa as configurações.](media/vcvm11.png)

A VM agora é adicionada aos recursos de computação de carga de trabalho e está pronta para uso. 
![Captura de tela que mostra a VM que foi adicionada aos recursos de computação de carga de trabalho.](media/vcvm12.png)

A configuração básica agora está concluída. Você pode começar a usar sua nuvem privada semelhante a como você usaria sua infraestrutura de VM local.

As seções a seguir contêm informações opcionais sobre como configurar servidores DNS e DHCP para cargas de trabalho de nuvem privada e modificar a configuração de rede padrão.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>Adicionar usuários e fontes de identidade ao vCenter (opcional)

CloudSimple atribui uma conta de usuário padrão do vCenter com o nome de usuários `cloudowner@cloudsimple.local` . Nenhuma configuração de conta adicional é necessária para você começar.  CloudSimple normalmente atribui aos administradores os privilégios necessários para executar operações normais.  Configure seu Active Directory local ou o Azure AD como uma fonte de [identidade adicional](set-vcenter-identity.md) em sua nuvem privada.

## <a name="create-a-dns-and-dhcp-server-optional"></a>Criar um servidor DNS e DHCP (opcional)

Os aplicativos e as cargas de trabalho em execução em um ambiente de nuvem privada exigem a resolução de nomes e os serviços DHCP para pesquisa e atribuição de endereço IP. Uma infraestrutura apropriada de DHCP e DNS é necessária para fornecer esses serviços. Você pode configurar uma máquina virtual no vCenter para fornecer esses serviços em seu ambiente de nuvem privada.

Pré-requisitos

* Um grupo de portas distribuídas com VLAN configurado

* Configuração de rota para servidores DNS locais ou baseados na Internet

* Modelo de máquina virtual ou ISO para criar uma máquina virtual

Os links a seguir fornecem orientação sobre como configurar servidores DHCP e DNS no Linux e no Windows.

#### <a name="linux-based-dns-server-setup"></a>Instalação do servidor DNS baseado em Linux

O Linux oferece vários pacotes para a configuração de servidores DNS.  Aqui está um link para instruções para configurar um servidor DNS de ligação de software livre.

[Configuração de exemplo](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

#### <a name="windows-based-setup"></a>Instalação baseada no Windows

Estes tópicos da Microsoft descrevem como configurar um servidor do Windows como um servidor DNS e como um servidor DHCP.

[Servidor do Windows como servidor DNS](/windows-server/networking/dns/dns-top)

[Windows Server como servidor DHCP](/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>Personalizar a configuração de rede (opcional)

As páginas de rede no portal do CloudSimple permitem que você especifique a configuração de tabelas de firewall e endereços IP públicos para VMs.

### <a name="allocate-public-ips"></a>Alocar IPs públicos

1. Navegue até **rede > IP público** no portal do CloudSimple.
2. Clique em **alocar IP público**.
3. Insira um nome para identificar a entrada de endereço IP.
4. Mantenha o local padrão.
5. Use o controle deslizante para alterar o tempo limite de ociosidade, se desejado.
6. Insira o endereço IP local para o qual você deseja atribuir um endereço IP público.
7. Se desejar, insira um nome DNS associado.
8. Clique em **Concluído**.

    ![IP público](media/quick-create-pc-public-ip.png)

A tarefa de alocar o endereço IP público começa. Você pode verificar o status da tarefa na página **atividades > tarefas** . Quando a alocação for concluída, a nova entrada será mostrada na página IPs públicos.

A VM para a qual esse endereço IP deve ser mapeado precisa ser configurada com o endereço local especificado acima. O procedimento para configurar um endereço IP é específico para o sistema operacional da VM. Consulte a documentação do sistema operacional da VM para obter o procedimento correto.

#### <a name="example"></a>Exemplo

Por exemplo, aqui estão os detalhes do Ubuntu 16, 4.

Adicione o método estático à configuração da família de endereços da inet no arquivo/etc/network/interfaces. Altere o endereço, a máscara de rede e os valores de gateway. Para este exemplo, estamos usando a interface eth0, o endereço IP interno 192.168.24.10, o endereço de gateway 192.168.24.1 e a máscara de rede 255.255.255.0. Para o seu ambiente, as informações de sub-rede disponíveis são fornecidas no email de boas-vindas.

```
sudo vi /etc/network/interfaces
```

```
auto eth0
Iface eth0 inet static
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```
Desabilite manualmente a interface.

```
sudo ifdown eth0
```
Habilite manualmente a interface novamente.

```
sudo ifup eth0
```

Por padrão, todo o tráfego de entrada da Internet é **negado**. Se você quiser abrir qualquer outra porta, crie uma tabela de [Firewall](firewall.md).

Depois de configurar um endereço IP interno como o endereço IP estático, verifique se você pode acessar a Internet de dentro da VM.

```
ping 8.8.8.8
```
Verifique também se você pode acessar a VM da Internet usando o endereço IP público.

Verifique se as regras de iptable na VM não estão bloqueando a entrada da porta 80.
        
```
netstat -an | grep 80
```

Inicie um servidor http que escuta na porta 80.
       
```
python2.7 -m SimpleHTTPServer 80
```

ou

```
python3 -m http.server 80
```
Inicie um navegador na área de trabalho e aponte-o para a porta 80 para o endereço IP público para procurar os arquivos em sua VM.

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>Regras de firewall padrão do CloudSimple para IP público

* Tráfego de VPN: todo o tráfego entre (de/para) a VPN e todas as redes de carga de trabalho e rede de gerenciamento é permitido.
* Tráfego interno de nuvem privada: todo o tráfego leste-oeste entre as redes de carga de trabalho (de/para) e a rede de gerenciamento (mostrada acima) é permitido.
* Tráfego da Internet:
  * Todo o tráfego de entrada da Internet é negado às redes de carga de trabalho e à rede de gerenciamento.
  * Todo o tráfego de saída para a Internet de redes de carga de trabalho ou da rede de gerenciamento é permitido.

Você também pode modificar a maneira como o tráfego é protegido, usando o recurso de regras de firewall. Para obter mais informações, consulte [configurar regras e tabelas de firewall](firewall.md).

## <a name="install-solutions-optional"></a>Instalar soluções (opcional)

Você pode instalar soluções em sua nuvem privada do CloudSimple para aproveitar ao máximo seu ambiente do vCenter de nuvem privada. Você pode configurar o backup, a recuperação de desastre, a replicação e outras funções para proteger suas máquinas virtuais. Os exemplos incluem o VMware Site Recovery Manager (VMware SRM) e o Veeam Backup & Replication.

Para instalar uma solução, você deve solicitar privilégios adicionais por um período limitado. Consulte [escalonar privilégios](escalate-private-cloud-privileges.md).

## <a name="next-steps"></a>Próximas etapas

* [Consumir VMs do VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* [Conectar-se à rede local usando o Azure ExpressRoute](on-premises-connection.md)
* [Configurar gateways de VPN na rede CloudSimple](vpn-gateway.md)
