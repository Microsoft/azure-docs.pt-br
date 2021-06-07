---
title: incluir arquivo
description: incluir arquivo
services: virtual-network
author: jimdial
ms.service: virtual-network
ms.topic: include
ms.date: 05/10/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 93caf39216ef0479ec2799267a9ba8181f37f802
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "84194216"
---
## <a name="add-ip-addresses-to-a-vm-operating-system"></a><a name="os-config"></a>Adicionar endereços IP em um sistema operacional da VM

Conecte-se e faça logon em uma VM criada com vários endereços IP privados. Você deve adicionar manualmente todos os endereços IP privados (incluindo o principal) que você adicionou à VM. Complete as etapas a seguir para seu sistema operacional VM.

### <a name="windows-server"></a>Windows Server
<details>
  <summary>Expanda</summary>

1. Em um prompt de comando, digite *ipconfig /all*.  Você vê apenas o endereço IP privado *Primário* (por meio do DHCP).
2. Digite *ncpa.cpl* no prompt de comando para abrir a janela **Conexões de rede**.
3. Abra as propriedades do adaptador apropriado: **Ethernet**.
4. Clique duas vezes em versão do Protocolo de Internet 4 (IPv4).
5. Selecione **Usar o seguinte endereço IP** e insira os seguintes valores:

    * **Endereço IP**: insira o endereço IP privado *primário*
    * **Máscara de sub-rede**: defina com base na sua sub-rede. Por exemplo, se a sub-rede for uma sub-rede /24, então, a máscara de sub-rede será 255.255.255.0.
    * **Gateway padrão**: o primeiro endereço IP na sub-rede. Se sua sub-rede for 10.0.0.0/24, o endereço IP do gateway será 10.0.0.1.
    * Selecione **Usar os seguintes endereços do servidor DNS** e insira os seguintes valores:
        * **Servidor DNS preferencial**: digite 168.63.129.16 se você não estiver usando seu próprio servidor DNS.  Se você estiver usando seu próprio servidor DNS, digite o endereço IP do seu servidor.  (Para o servidor DNS alternativo, você pode escolher qualquer endereço de servidor DNS público gratuito.)
    * Selecione o botão **Avançado** e adicione mais endereços IP. Adicione cada um dos endereços IP privados secundários, que você adicionou à interface de rede do Azure em uma etapa anterior à interface de rede do Windows que recebe o endereço IP principal atribuído à interface de rede do Azure.

        Nunca atribua manualmente o endereço IP público atribuído a uma máquina virtual do Azure no sistema operacional da máquina virtual. Ao definir manualmente o endereço IP privado no sistema operacional, verifique se é o mesmo endereço que o endereço IP privado atribuído ao [adaptador de rede](../articles/virtual-network/virtual-network-network-interface-addresses.md#change-ip-address-settings) do Azure ou se é possível perder a conectividade com a máquina virtual. Saiba mais sobre as configurações de [endereço IP privado](../articles/virtual-network/virtual-network-network-interface-addresses.md#private). Nunca atribua um endereço de IP público do Azure dentro do sistema operacional.

    * Clique em **OK** para fechar as configurações de TCP/IP e, em seguida, em **OK** novamente para fechar as configurações do adaptador. A conexão RDP é restabelecida.

6. Em um prompt de comando, digite *ipconfig /all*. Verifique se todos os endereços IP que você adicionou são mostrados e se o DHCP está desativado.
7. Configure o Windows para usar o endereço IP privado da configuração de IP primário no Azure como o endereço IP primário para o Windows. Consulte [Sem acesso à Internet de VM do Windows Azure que tem vários endereços IP](https://support.microsoft.com/help/4040882/no-internet-access-from-azure-windows-vm-that-has-multiple-ip-addresse) para obter detalhes. 

### <a name="validation-windows-server"></a>Validação (Windows Server)

Para garantir que você possa se conectar à Internet de sua configuração de IP secundário via IP público associado, depois de ter adicionado o IP corretamente usando as etapas acima, use o seguinte comando (substituindo 10.0.0.7 pelo endereço IP privado público):

```bash
ping -S 10.0.0.7 outlook.com
```
>[!NOTE]
>Para configurações de IP secundárias, você só pode executar ping para a Internet se a configuração tiver um endereço IP público associado a ela. Para configurações de IP primárias, um endereço IP público não é necessário executar ping na Internet.
</details>

### <a name="linux-ubuntu-1416"></a>Linux (Ubuntu 14/16)
<details>
  <summary>Expanda</summary>
É recomendável observar a documentação mais recente para sua distribuição do Linux. 

1. Abra uma janela de terminal.
2. Verifique se você é o usuário raiz. Se não for, digite o seguinte comando:

   ```bash
   sudo -i
   ```

3. Atualize o arquivo de configuração do adaptador de rede (supondo que 'eth0').

   * Mantenha o item de linha existente para o dhcp. O endereço IP principal permanece configurado como era anteriormente.
   * Adicione uma configuração para um endereço IP estático adicional com os seguintes comandos:

     ```bash
     cd /etc/network/interfaces.d/
     ls
     ```

     Você deve ver um arquivo. cfg.
4. Abra o arquivo. Você verá as seguintes linhas ao final do arquivo:

   ```bash
   auto eth0
   iface eth0 inet dhcp
   ```

5. Adicione as seguintes linhas após as linhas existentes neste arquivo:

   ```bash
   iface eth0 inet static
   address <your private IP address here>
   netmask <your subnet mask>
   ```

6. Salve o arquivo usando o seguinte comando:

   ```bash
   :wq
   ```

7. Reinicie o adaptador de rede com o seguinte comando:

   ```bash
   sudo ifdown eth0 && sudo ifup eth0
   ```

   > [!IMPORTANT]
   > Execute ifdown e ifup na mesma linha se você estiver usando uma conexão remota.
   >

8. Verifique se que o endereço IP foi adicionado ao adaptador de rede com o seguinte comando:

   ```bash
   ip addr list eth0
   ```

   Você verá o endereço IP adicionado como parte da lista.

### <a name="validation-ubuntu-1416"></a>Validação (Ubuntu 14/16)

Para garantir que você possa se conectar à internet de seu IP secundário configuração via o IP público associado, use o seguinte comando:

```bash
ping -I 10.0.0.5 outlook.com
```
>[!NOTE]
>Para configurações de IP secundárias, você só pode executar ping para a Internet se a configuração tiver um endereço IP público associado a ela. Para configurações de IP primárias, um endereço IP público não é necessário executar ping na Internet.

Para VMs do Linux, ao tentar validar a conectividade de saída de uma NIC secundária, talvez seja necessário adicionar rotas apropriadas. Há várias maneiras de fazer isso. Veja a documentação apropriada para sua distribuição do Linux. Este é um método para fazer isso:

```bash
echo 150 custom >> /etc/iproute2/rt_tables 

ip rule add from 10.0.0.5 lookup custom
ip route add default via 10.0.0.1 dev eth2 table custom

```
- Substitua:
    - **10.0.0.5** pelo endereço IP privado que tem um endereço IP público associado a ele
    - **10.0.0.1** pelo seu gateway padrão
    - **eth2** pelo nome de sua NIC secundária </details>

### <a name="linux-ubuntu-1804"></a>Linux (Ubuntu 18.04+)
<details>
  <summary>Expanda</summary>
O Ubuntu 18.04 e versões superiores foram alterados para `netplan` para o gerenciamento de rede do SO. É recomendável observar a documentação mais recente para sua distribuição do Linux. 

1. Abra uma janela de terminal.
2. Verifique se você é o usuário raiz. Se não for, digite o seguinte comando:

    ```bash
    sudo -i
    ```

3. Crie um arquivo para a segunda interface e abra-o em um editor de texto:

    ```bash
    vi /etc/netplan/60-static.yaml
    ```

4. Adicione as seguintes linhas ao arquivo, substituindo `10.0.0.6/24` pelo seu IP/máscara de rede:

    ```bash
    network:
        version: 2
        ethernets:
            eth0:
                addresses:
                    - 10.0.0.6/24
    ```

5. Salve o arquivo usando o seguinte comando:

    ```bash
    :wq
    ```

6. Teste as alterações usando [netplan try](http://manpages.ubuntu.com/manpages/cosmic/man8/netplan-try.8.html) para confirmar a sintaxe:

    ```bash
    netplan try
    ```

> [!NOTE]
> O `netplan try` aplicará as alterações temporariamente e as reverterá após 120 segundos. Se houver uma perda de conectividade, aguarde 120 segundos e, em seguida, reconecte-se. Nesse momento, as alterações serão revertidas.

7. Supondo que não haja problemas com `netplan try`, aplique as alterações de configuração:

    ```bash
    netplan apply
    ```

8. Verifique se que o endereço IP foi adicionado ao adaptador de rede com o seguinte comando:

    ```bash
    ip addr list eth0
    ```

    Você verá o endereço IP adicionado como parte da lista. Exemplo:

    ```bash
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host
        valid_lft forever preferred_lft forever
    2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
        link/ether 00:0d:3a:8c:14:a5 brd ff:ff:ff:ff:ff:ff
        inet 10.0.0.6/24 brd 10.0.0.255 scope global eth0
        valid_lft forever preferred_lft forever
        inet 10.0.0.4/24 brd 10.0.0.255 scope global secondary eth0
        valid_lft forever preferred_lft forever
        inet6 fe80::20d:3aff:fe8c:14a5/64 scope link
        valid_lft forever preferred_lft forever
    ```
### <a name="validation-ubuntu-1804"></a>Validação (Ubuntu 18.04+)

Para garantir que você possa se conectar à internet de seu IP secundário configuração via o IP público associado, use o seguinte comando:

```bash
ping -I 10.0.0.5 outlook.com
```
>[!NOTE]
>Para configurações de IP secundárias, você só pode executar ping para a Internet se a configuração tiver um endereço IP público associado a ela. Para configurações de IP primárias, um endereço IP público não é necessário executar ping na Internet.

Para VMs do Linux, ao tentar validar a conectividade de saída de uma NIC secundária, talvez seja necessário adicionar rotas apropriadas. Há várias maneiras de fazer isso. Veja a documentação apropriada para sua distribuição do Linux. Este é um método para fazer isso:

```bash
echo 150 custom >> /etc/iproute2/rt_tables 

ip rule add from 10.0.0.5 lookup custom
ip route add default via 10.0.0.1 dev eth2 table custom

```
- Substitua:
    - **10.0.0.5** pelo endereço IP privado que tem um endereço IP público associado a ele
    - **10.0.0.1** pelo seu gateway padrão
    - **eth2** pelo nome de sua NIC secundária </details>

### <a name="linux-red-hat-centos-and-others"></a>Linux (Red Hat, CentOS e outros)
<details>
  <summary>Expanda</summary>

1. Abra uma janela de terminal.
2. Verifique se você é o usuário raiz. Se não for, digite o seguinte comando:

    ```bash
    sudo -i
    ```

3. Digite sua senha e siga as instruções conforme solicitado. Quando você for o usuário raiz, navegue até a pasta de scripts de rede com o seguinte comando:

    ```bash
    cd /etc/sysconfig/network-scripts
    ```

4. Liste os arquivos ifcfg relacionados usando o seguinte comando:

    ```bash
    ls ifcfg-*
    ```

    Você deve ver *ifcfg-eth0* como um dos arquivos.

5. Para adicionar um endereço IP, crie um arquivo de configuração para ele, conforme mostrado abaixo. Observe que um arquivo deve ser criado para cada configuração de IP.

    ```bash
    touch ifcfg-eth0:0
    ```

6. Abra o arquivo *ifcfg-eth0:0* com o seguinte comando:

    ```bash
    vi ifcfg-eth0:0
    ```

7. Adicionar conteúdo para o arquivo *eth0:0* nesse caso, com o comando a seguir. Atualize as informações com base em seu endereço IP.

    ```bash
    DEVICE=eth0:0
    BOOTPROTO=static
    ONBOOT=yes
    IPADDR=192.168.101.101
    NETMASK=255.255.255.0
    ```

8. Salve o arquivo com o seguinte comando:

    ```bash
    :wq
    ```

9. Reinicie os serviços de rede e certifique-se de que as alterações foram bem-sucedidas executando os seguintes comandos:

    ```bash
    /etc/init.d/network restart
    ifconfig
    ```

    Você verá o endereço IP adicionado, *eth0:0*, na lista retornada.

### <a name="validation-red-hat-centos-and-others"></a>Validação (Red Hat, CentOS e outros)

Para garantir que você possa se conectar à internet de seu IP secundário configuração via o IP público associado, use o seguinte comando:

```bash
ping -I 10.0.0.5 outlook.com
```
>[!NOTE]
>Para configurações de IP secundárias, você só pode executar ping para a Internet se a configuração tiver um endereço IP público associado a ela. Para configurações de IP primárias, um endereço IP público não é necessário executar ping na Internet.

Para VMs do Linux, ao tentar validar a conectividade de saída de uma NIC secundária, talvez seja necessário adicionar rotas apropriadas. Há várias maneiras de fazer isso. Veja a documentação apropriada para sua distribuição do Linux. Este é um método para fazer isso:

```bash
echo 150 custom >> /etc/iproute2/rt_tables 

ip rule add from 10.0.0.5 lookup custom
ip route add default via 10.0.0.1 dev eth2 table custom

```
- Substitua:
    - **10.0.0.5** pelo endereço IP privado que tem um endereço IP público associado a ele
    - **10.0.0.1** pelo seu gateway padrão
    - **eth2** pelo nome de sua NIC secundária </details>