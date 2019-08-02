---
title: Guia de início rápido - criar uma VM do VMware em uma nuvem privada
description: Descreve como criar e uma VM do VMware na nuvem privada CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/03/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 33354ce09ad6ba1a9a7c08a8cd3b945f3788011a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595693"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>Criar máquinas virtuais VMware em sua nuvem privada

Para criar máquinas virtuais em sua nuvem privada, comece acessando o portal de CloudSimple do portal do Azure.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Acessar o portal da CloudSimple

1. Selecione **Todos os serviços**.
2. Pesquise **CloudSimple serviços**.
3. Selecione o serviço CloudSimple no qual você deseja criar sua nuvem privada.
4. Dos **visão geral** , clique em **acesse o portal CloudSimple** para abrir uma nova guia do navegador para o portal de CloudSimple.  Se solicitado, entre com seu Azure as credenciais de logon.  

    ![Inicie o portal de CloudSimple](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>Inicie a interface de usuário da web de vCenter

Agora você pode iniciar o vCenter para configurar máquinas virtuais e políticas.

Para acessar o vCenter, inicie a partir do portal CloudSimple. Na Home page, sob **tarefas comuns**, clique em **inicie o cliente vSphere**.  Selecione a nuvem privada e, em seguida, clique em **inicie o cliente vSphere** sobre a nuvem privada.

   ![Inicie o cliente vSphere](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>Carregar um modelo de ISO ou vSphere

> [!WARNING]
> Para carregamentos ISO, use o cliente do vSphere HTML5.  Usar o cliente Flash pode resultar em um erro.

1. Obter o modelo ISO ou vSphere que você deseja carregar no vCenter para criar uma máquina virtual e fazê-lo disponível em seu sistema local.

2. No vCenter, clique o **disco** ícone e selecione **vsanDatastore**. Clique em **arquivos** e, em seguida, clique em **nova pasta**.

    ![vCenter ISO](media/vcenter-create-folder.png)

3. Crie uma pasta para armazenar arquivos ISO.

4. Navegue até a nova pasta criada e, em seguida, clique em **carregar arquivos**. Siga na tela instruções para carregar o ISO.

## <a name="create-a-virtual-machine-in-vcenter"></a>Criar uma máquina Virtual no vCenter

1. No vCenter, clique o **Hosts e Clusters** ícone.

2. Clique com botão direito **carga de trabalho** e selecione **nova máquina Virtual**.
    
    ![Criar máquina virtual](media/create-vcenter-virtual-machine-01.png)

3. Selecione **criar nova máquina virtual** e clique em **próxima**.

    ![Assistente de nova máquina virtual](media/create-vcenter-virtual-machine-02.png)

4. Nome do computador, selecione o **da carga de trabalho de VM** pasta e clique em **próxima**.

    ![Selecionar nome e pasta](media/create-vcenter-virtual-machine-03.png)

5. Selecione o **carga de trabalho** recurso de computação e clique em **próxima**.

    ![Selecione o recurso de computação](media/create-vcenter-virtual-machine-04.png)

6. Selecione **vsanDatastore** e clique em **próxima**.

    ![Selecione armazenamento](media/create-vcenter-virtual-machine-05.png)

7. Mantenha a seleção de compatibilidade padrão ESXi 6.5 e clique em **próxima**.

    ![Selecione compatibilidade](media/create-vcenter-virtual-machine-06.png)

8. Selecione o sistema operacional convidado da ISO para a máquina virtual e clique em **próxima**.

    ![Personalizar o sistema operacional convidado](media/create-vcenter-virtual-machine-07.png)

9. Selecione o disco rígido e opções de rede. Para a unidade de CD/DVD novo, selecione **arquivo ISO Datastore**.  Se você quiser permitir o tráfego do endereço IP público para esta máquina virtual, selecione a rede como **vm-1**.

    ![Marque a personalização de hardware](media/create-vcenter-virtual-machine-08.png)

10. Abre uma janela de seleção. Selecione o arquivo que você carregou anteriormente para a pasta de arquivos ISO e modelos e clique em **Okey**.

    ![Selecionar ISO](media/create-vcenter-virtual-machine-10.png)

11. Examine as configurações e clique em **Okey** para criar a máquina virtual.

    ![Opções de revisão](media/create-vcenter-virtual-machine-11.png)

A máquina virtual agora é adicionada para os recursos de computação da carga de trabalho e está pronta para uso. 

![Nova máquina virtual no vCenter](media/create-vcenter-virtual-machine-12.png)

A configuração básica agora está concluída. Você pode começar a usar sua nuvem privada, semelhante a como você usaria a sua infraestrutura de máquina virtual local.

Seções a seguir contêm informações opcionais sobre a configuração DNS e DHCP servidores para a nuvem privada cargas de trabalho e modificar a configuração de rede padrão.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>Adicionar usuários e fontes de identidade ao vCenter (opcional)

CloudSimple atribui uma conta de usuário do vCenter padrão com o nome de usuário **cloudowner@cloudsimple.local** . Nenhuma configuração de conta adicional é necessária para você começar a usar.  CloudSimple normalmente atribui administradores de privilégios necessários para executar as operações normais.  Configure seu active directory no local ou o Azure AD como um [fonte de identidade adicional](https://docs.azure.cloudsimple.com/set-vcenter-identity/) na sua nuvem privada.

## <a name="create-a-dns-and-dhcp-server-optional"></a>Criar um servidor DHCP e DNS (opcional)

Aplicativos e cargas de trabalho em execução em um ambiente de nuvem privada exigem resolução de nomes e serviços DHCP para atribuição de endereço IP e de pesquisa. Uma infraestrutura DNS e DHCP adequada é necessário para fornecer esses serviços. Você pode configurar uma máquina virtual no vCenter para fornecer esses serviços em seu ambiente de nuvem privada.

### <a name="prerequisites"></a>Pré-requisitos

* Um grupo de portas distribuído com VLAN configurado

* Configurar locais ou servidores DNS baseados na Internet de rota

* Modelo de máquina virtual ou ISO para criar uma máquina virtual

Os links a seguir fornecem orientação sobre como configurar servidores DNS e DHCP no Linux e Windows.

### <a name="linux-based-dns-server-setup"></a>Instalação de servidor DNS baseado em Linux

Linux oferece vários pacotes para configurar servidores DNS.  Aqui está um link para instruções sobre como configurar um servidor de DNS BIND do código-fonte aberto.

[Configuração de exemplo](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

### <a name="windows-based-setup"></a>Instalação baseada em Windows

Estes artigos da Microsoft descrevem como configurar um servidor Windows como um servidor DNS e como um servidor DHCP.
<br>
[Windows Server como o servidor DNS](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[Windows Server como servidor DHCP](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>Personalizar a configuração de rede (opcional)

As páginas de rede no portal do CloudSimple permitem que você especifique a configuração para tabelas de firewall e endereços IP públicos para máquinas virtuais.

### <a name="allocate-public-ips"></a>Alocar IPs públicos

1. Navegue até **rede > IP público** no portal do CloudSimple.
2. Clique em **alocar um IP público**.
3. Insira um nome para identificar a entrada de endereço IP.
4. Selecione o local da sua nuvem privada.
5. Use o controle deslizante para alterar o tempo limite de ociosidade, se desejado.
6. Insira o endereço IP local para o qual você deseja atribuir um endereço IP público.
7. Insira um nome DNS associado, se desejado.
8. Clique em **Concluído**.

    ![IP público](media/quick-create-pc-public-ip.png)

Inicia a tarefa de alocar o endereço IP público. Você pode verificar o status da tarefa sobre o **atividade > tarefas** página. Quando a alocação é concluída, a nova entrada é mostrada na página de IPs públicos.

A máquina virtual à qual esse endereço IP deve ser mapeado precisa ser configurado com o endereço do local especificado acima. O procedimento para configurar um endereço IP é específico para o sistema operacional da máquina virtual. Consulte a documentação do seu sistema operacional de máquina virtual para o procedimento correto.

#### <a name="example"></a>Exemplo

Por exemplo, aqui estão os detalhes para o Ubuntu 16.04.

Adicione o método estático na configuração de família de endereço inet no arquivo ```/etc/network/interfaces```. Altere os valores de endereço, máscara de rede e gateway. Neste exemplo, estamos usando a interface eth0, endereço IP interno 192.168.24.10, 192.168.24.1 de endereço de gateway e máscara de rede 255.255.255.0. 

Edite o arquivo ```interfaces``` .

```
sudo vi /etc/network/interfaces
```

Atualize a seção a seguir no ```interfaces``` arquivo.

```
auto eth0
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```

Desabilite a interface.

```
sudo ifdown eth0
```

Habilite novamente a interface.

```
sudo ifup eth0
```

Por padrão, todo o tráfego de entrada da Internet é **negado**. Se você quiser abrir qualquer outra porta, crie uma [tabela de firewall](https://docs.azure.cloudsimple.com/firewall/).

Depois de configurar um endereço IP interno como o endereço IP estático, verifique se é possível acessar a Internet de dentro da máquina virtual.

```
ping 8.8.8.8
```

Verifique se é possível acessar a máquina virtual da Internet usando o endereço IP público.

Certifique-se de que as regras de firewall (iptable) na máquina virtual não estão bloqueando a porta 80 entrada.

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

Inicie um navegador na área de trabalho e apontá-lo para a porta 80 para o endereço IP público procurar arquivos em sua máquina virtual. 

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>Regras de firewall CloudSimple padrão para o IP público

* Tráfego VPN: Todo o tráfego entre (de/para) o VPN e todas as redes de carga de trabalho e rede de gerenciamento é permitido.
* Tráfego interno de nuvem privada: Todo o tráfego Leste-Oeste entre (de/para) redes de carga de trabalho e a rede de gerenciamento (mostrado acima) é permitido.
* Tráfego de Internet:
    * Todo o tráfego de entrada da Internet é negado para redes de carga de trabalho e a rede de gerenciamento.
    * Todo tráfego de saída à Internet de redes de carga de trabalho ou da rede de gerenciamento é permitido.

Você também pode modificar a maneira como o tráfego é protegido, usando o recurso de regras de Firewall. Para obter mais informações, consulte [configurar tabelas de firewall e regras](https://docs.azure.cloudsimple.com/firewall/).

## <a name="install-solutions-optional"></a>Instalar soluções (opcionais)
Você pode instalar as soluções em sua nuvem privada CloudSimple para aproveitar ao máximo sua nuvem privada ambiente do vCenter. Você pode configurar a recuperação de desastres, backup, replicação e outras funções para proteger suas máquinas virtuais. Os exemplos incluem o Gerenciador de recuperação de Site do VMware (VMware SRM) e Veeam Backup & Replication.

Para instalar uma solução, você deve solicitar privilégios adicionais durante um período limitado. Ver [escalonar privilégios](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges).

## <a name="next-steps"></a>Próximas etapas

* [Consumir máquinas virtuais do VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* [Conectar-se à rede local usando o Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection)
* [Configurar gateways de VPN na rede CloudSimple](https://docs.azure.cloudsimple.com/vpn-gateway)
