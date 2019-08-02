---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-network
author: jimdial
ms.service: virtual-network
ms.topic: include
ms.date: 05/10/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 5aeb0e01192c0635def8eef0c73aa2d14b7921e2
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171845"
---
## <a name="os-config"></a>Adicionar endereços IP em um sistema operacional da VM

Conecte-se e faça logon em uma VM criada com vários endereços IP privados. Você deve adicionar manualmente todos os endereços IP privados (incluindo o principal) que você adicionou à VM. Complete as etapas a seguir para seu sistema operacional VM.

### <a name="windows"></a>Windows

1. Em um prompt de comando, digite *ipconfig /all*.  Você vê apenas o endereço IP privado *Primário* (por meio do DHCP).
2. Digite *ncpa.cpl* no prompt de comando para abrir a janela **Conexões de rede**.
3. Abra as propriedades do adaptador apropriado: **Conexão de área local**.
4. Clique duas vezes em versão do Protocolo de Internet 4 (IPv4).
5. Selecione **Usar o seguinte endereço IP** e insira os seguintes valores:

    * **Endereço IP**: Insira o *primário* endereço IP privado
    * **Máscara de sub-rede**: Definidos com base em sua sub-rede. Por exemplo, se a sub-rede for uma sub-rede /24, então, a máscara de sub-rede será 255.255.255.0.
    * **Gateway padrão**: O primeiro endereço IP na sub-rede. Se sua sub-rede for 10.0.0.0/24, o endereço IP do gateway será 10.0.0.1.
    * Selecione **Usar os seguintes endereços do servidor DNS** e insira os seguintes valores:
        * **Servidor DNS preferencial**: Se você não estiver usando seu próprio servidor DNS, digite 168.63.129.16.  Se você estiver usando seu próprio servidor DNS, digite o endereço IP do seu servidor.
    * Selecione o botão **Avançado** e adicione mais endereços IP. Adicione cada um dos endereços IP privados secundários, que você adicionou à interface de rede do Azure em uma etapa anterior à interface de rede do Windows que recebe o endereço IP principal atribuído à interface de rede do Azure.

        Nunca atribua manualmente o endereço IP público atribuído a uma máquina virtual do Azure no sistema operacional da máquina virtual. Ao definir manualmente o endereço IP privado no sistema operacional, verifique se é o mesmo endereço que o endereço IP privado atribuído ao [adaptador de rede](../articles/virtual-network/virtual-network-network-interface-addresses.md#change-ip-address-settings) do Azure ou se é possível perder a conectividade com a máquina virtual. Saiba mais sobre as configurações de [endereço IP privado](../articles/virtual-network/virtual-network-network-interface-addresses.md#private). Nunca atribua um endereço de IP público do Azure dentro do sistema operacional.

    * Clique em **OK** para fechar as configurações de TCP/IP e, em seguida, em **OK** novamente para fechar as configurações do adaptador. A conexão RDP é restabelecida.

6. Em um prompt de comando, digite *ipconfig /all*. Todos os endereços IP que você adicionou são mostrados e o DHCP está desativado.
7. Configure o Windows para usar o endereço IP privado da configuração de IP primário no Azure como o endereço IP primário para o Windows. Consulte [Sem acesso à Internet de VM do Windows Azure que tem vários endereços IP](https://support.microsoft.com/help/4040882/no-internet-access-from-azure-windows-vm-that-has-multiple-ip-addresse) para obter detalhes. 

### <a name="validation-windows"></a>Validação (Windows)

Para garantir que você possa se conectar à internet de seu IP secundário configuração via o IP público associado, depois de ter adicionado corretamente usando as etapas acima, use o seguinte comando:

```bash
ping -S 10.0.0.5 hotmail.com
```
>[!NOTE]
>Para configurações de IP secundárias, você só pode executar ping para a Internet se a configuração tiver um endereço IP público associado a ela. Para configurações de IP primárias, um endereço IP público não é necessário executar ping na Internet.

### <a name="linux-ubuntu-1416"></a>Linux (Ubuntu 14/16)
É recomendável examinar a versão mais recente a documentação para sua distribuição do Linux. 

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

### <a name="linux-red-hat-centos-and-others"></a>Linux (Red Hat, CentOS e outros)

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

### <a name="validation-linux"></a>Validação (Linux)

Para garantir que você possa se conectar à internet de seu IP secundário configuração via o IP público associado, use o seguinte comando:

```bash
ping -I 10.0.0.5 hotmail.com
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
    - **eth2** pelo nome de sua NIC secundária
