---
title: Visão geral do Agente de VM do Azure Linux | Microsoft Docs
description: Saiba como instalar e configurar o agente Linux (waagent) para gerenciar sua interação de máquina virtual com os Recursos de Infraestrutura do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: e41de979-6d56-40b0-8916-895bf215ded6
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: roiyz
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 59a0cdd29e50501f023faf323948a400f325df0b
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706150"
---
# <a name="understanding-and-using-the-azure-linux-agent"></a>Noções básicas e uso do Agente Linux do Azure

O Agente Linux do Microsoft Azure (waagent) gerencia o provisionamento de Linux e FreeBSD e a interação de VM com o Controlador de malha do Azure. Além do Agente do Linux fornecendo a funcionalidade de provisionamento, o Azure também fornece a opção de usar a inicialização de nuvem para alguns OSes Linux. O Agente Linux fornece a seguinte funcionalidade para implantações IaaS do Linux e FreeBSD:

> [!NOTE]
> Para obter mais informações, veja o [README](https://github.com/Azure/WALinuxAgent/blob/master/README.md).
> 
> 

* **Provisionamento de imagem**
  
  * Criação de uma conta de usuário
  * Configurando os tipos de autenticação do SSH
  * Implantação de chaves públicas do SSH e pares de chaves
  * Configuração do nome de host
  * Publicando o nome do host para a plataforma de DNS
  * Relatório de impressão digital da chave host SSH para a plataforma
  * Gerenciamento de recursos de disco
  * Formatação e montagem do disco do recurso
  * Configurando o espaço de permuta
* **Rede**
  
  * Gerencia as rotas para melhorar a compatibilidade com os servidores DHCP de plataforma
  * Garante a estabilidade do nome da interface de rede
* **Kernel**
  
  * Configura NUMA virtual (desabilitar para kernel <`2.6.37`)
  * Consome entropia de Hyper-V para /dev/random
  * Configura os tempos limite de SCSI para o dispositivo raiz (o qual poderia ser remoto)
* **Diagnostics**
  
  * Redirecionamento de console de porta serial
* **Implantações SCVMM**
  
  * Detecta e inicializa o agente VMM para Linux quando executado em um ambiente de System Center Virtual Machine Manager 2012 R2
* **Extensão de VM**
  
  * Injete o componente criado pela Microsoft e seus Parceiros na VM do Linux (IaaS) para habilitar o software e a automação da configuração
  * Implementação de referência de extensão da VM em [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)

## <a name="communication"></a>Comunicação
O fluxo de informações da plataforma para o agente ocorre por meio de dois canais:

* Um DVD anexado ao tempo de inicialização para as implementações de IaaS. Este DVD inclui um arquivo de configuração compatível com OVF que inclui todas as informações de configuração que não seja os pares de chaves SSH real.
* Um ponto de extremidade TCP expondo uma API REST usada para obter a implantação e a configuração de topologia.

## <a name="requirements"></a>Requisitos
Os sistemas a seguir foram testados e funcionam com o agente Linux do Azure:

> [!NOTE]
> Essa lista pode diferir da lista oficial de sistemas com suporte na Plataforma Microsoft Azure, conforme descrito aqui: [https://support.microsoft.com/kb/2805216](https://support.microsoft.com/kb/2805216)
> 
> 

* CoreOS
* CentOS 6.3+
* Red Hat Enterprise Linux 6.7+
* Debian 7.0+
* Ubuntu 12.04+
* openSUSE 12.3+
* SLES 11 SP3+
* Oracle Linux 6.4+

Outros sistemas com suporte:

* FreeBSD 10+ (Agente Linux do Azure v2.0.10+)

O agente do Linux depende de alguns pacotes de sistema para funcionar corretamente:

* Python 2.6+
* OpenSSL 1.0+
* OpenSSH 5.3+
* Utilitários de sistema de arquivos: sfdisk, fdisk, mkfs, parted
* Ferramentas de senha: chpasswd, sudo
* Ferramentas de processamento de texto: sed, grep
* Ferramentas de rede: roteamento ip
* Suporte a kernel para montar sistemas de arquivos UDF.

## <a name="installation"></a>Instalação
Instalação usando um RPM ou um pacote DEB do repositório de pacotes da distribuição é o método preferencial para instalar e atualizar o Azure do Agente Linux do Azure. Todos os [provedores de distribuição aprovados](../linux/endorsed-distros.md) integram o pacote do agente Linux do Azure em suas imagens e repositórios.

Consulte a documentação do [repositório do agente Linux do Azure no GitHub](https://github.com/Azure/WALinuxAgent) para ver opções de instalação avançada, como instalação da origem ou em locais personalizados ou prefixos.

## <a name="command-line-options"></a>Opções de Linha de Comando
### <a name="flags"></a>Sinalizadores
* verbose: Aumentar o nível de detalhes do comando especificado
* força: Ignorar confirmação interativa para alguns comandos

### <a name="commands"></a>Comandos
* help: Lista os comandos com suporte e sinalizadores.
* Cancelar o provisionamento: Tentativa de limpar o sistema e torná-lo adequado para reprovisionamento. A operação a seguir deleta:
  
  * Todas as chaves de host SSH (se Provisioning.RegenerateSshHostKeyPair for 'y' no arquivo de configuração)
  * Configuração de servidor de nomes em /etc/resolv.conf
  * Senha raiz do /etc/shadow (se Provisioning.DeleteRootPassword for 'y' no arquivo de configuração)
  * Concessões de cliente DHCP em cache
  * Reinicia o nome de host para localdomain.localdomain

> [!WARNING]
> O desprovisionamento não garante que a imagem estará livre de todas as informações confidenciais e adequada para redistribuição.
> 
> 

* deprovision + user: Executa tudo em - deprovision (acima) e também exclui a última conta de usuário provisionado (obtida em /var/lib/waagent) e dados associados. Este parâmetro é quando a desconfiguração de uma imagem que foi anteriormente provisionamento no Azure para podem ser capturada e usada novamente.
* Versão: Exibe a versão do waagent
* serialconsole: Configura GRUB para marcar ttyS0 (a primeira porta serial) como o console de inicialização. Isso garante que os logs de inicialização do kernel são enviados para a porta serial e disponibilizados para depuração.
* daemon: Execute waagent como um daemon para gerenciar a interação com a plataforma. Esse argumento é especificado para waagent no script de inicialização de waagent.
* Início: Executar waagent como um processo em segundo plano

## <a name="configuration"></a>Configuração
Um arquivo de configuração (/ etc/waagent.conf) controla as ações de waagent. A seguir mostra um arquivo de configuração de exemplo:

    ```
    Provisioning.Enabled=y
    Provisioning.DeleteRootPassword=n
    Provisioning.RegenerateSshHostKeyPair=y
    Provisioning.SshHostKeyPairType=rsa
    Provisioning.MonitorHostName=y
    Provisioning.DecodeCustomData=n
    Provisioning.ExecuteCustomData=n
    Provisioning.AllowResetSysUser=n
    Provisioning.PasswordCryptId=6
    Provisioning.PasswordCryptSaltLength=10
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.MountOptions=None
    ResourceDisk.EnableSwap=n
    ResourceDisk.SwapSizeMB=0
    LBProbeResponder=y
    Logs.Verbose=n
    OS.RootDeviceScsiTimeout=300
    OS.OpensslPath=None
    HttpProxy.Host=None
    HttpProxy.Port=None
    AutoUpdate.Enabled=y
    ```

Várias opções de configuração são descritas em detalhes abaixo. Há três tipos opções de configuração: Booliano, String ou Integer. As opções de configuração booliana podem ser especificadas como "y" ou "n". A palavra-chave especial "Nenhum" pode ser usado para entradas de configuração de tipo algum sequência conforme seguintes detalhes:

**Provisioning.Enabled:**  
```
Type: Boolean  
Default: y
```
Isso permite que o usuário habilite ou desabilite a funcionalidade de provisionamento no agente. Os valores válidos são "y" ou "n". Se o provisionamento for desabilitado, as chaves SSH de host e usuário da imagem serão preservadas e qualquer configuração especificada na API de provisionamento do Azure será ignorada.

> [!NOTE]
> O parâmetro `Provisioning.Enabled` segue o padrão "n" do Ubuntu Cloud Images, que usa cloud-init para provisionamento.
> 
> 

**Provisioning.DeleteRootPassword:**  
```
Type: Boolean  
Default: n
```
Se definido, a senha raiz no arquivo sombra é apagado durante o processo de provisionamento.

**Provisioning.RegenerateSshHostKeyPair:**  
```
Type: Boolean  
Default: y
```
Se o conjunto de todos os SSH host pares de chaves (ecdsa, dsa e rsa) será excluído durante o processo de provisionamento de /etc/ssh/. E um único par de chave novo é gerado.

O tipo de criptografia para o novo par de chaves é configurável pela entrada do Provisioning.SshHostKeyPairType. Algumas distribuições novamente criam pares de chaves SSH para todos os tipos de criptografia está faltando quando é reiniciado o daemon do SSH (por exemplo, após uma reinicialização).

**Provisioning.SshHostKeyPairType:**  
```
Type: String  
Default: rsa
```
Isso pode ser definido como um tipo de algoritmo de criptografia com suporte pelo daemon SSH na máquina virtual. Os valores geralmente aceitos são "rsa", "dsa" e "ecdsa". "putty.exe" no Windows não oferece suporte a "ecdsa". Portanto, se você pretende usar putty.exe no Windows para conectar-se a uma implantação do Linux, use "rsa" ou "dsa".

**Provisioning.MonitorHostName:**  
```
Type: Boolean  
Default: y
```
Se definido, waagent monitora máquina virtual Linux para alterações de nome do host (conforme retornado pelo comando "hostname") e atualizar automaticamente a configuração de rede da imagem para refletir a alteração. Para enviar a alteração do nome para os servidores DNS, a rede é reiniciada na máquina virtual. Isso resulta em resumo perda de conectividade com a Internet.

**Provisioning.DecodeCustomData**  
```
Type: Boolean  
Default: n
```
Se definido, o waagent decodifica o CustomData da Base64.

**Provisioning.ExecuteCustomData**  
```
Type: Boolean  
Default: n
```
Se definido, o waagent executa o CustomData após o provisionamento.

**Provisioning.AllowResetSysUser**
```
Type: Boolean
Default: n
```
Essa opção permite que a senha do usuário sys seja redefinida; o padrão é ficar desabilitada.

**Provisioning.PasswordCryptId**  
```
Type: String  
Default: 6
```
Algoritmo usado pelo Crypt ao gerar o hash de senha.  
 1 - MD5  
 2a - Blowfish  
 5 - SHA-256  
 6 - SHA-512  

**Provisioning.PasswordCryptSaltLength**  
```
Type: String  
Default: 10
```
Comprimento de sal aleatório usado ao gerar o hash de senha.

**ResourceDisk.Format:**  
```
Type: Boolean  
Default: y
```
Se definido, o disco de recursos fornecido pela plataforma é formatado e montado por waagent se o tipo de sistema de arquivos solicitado pelo usuário em "ResourceDisk.Filesystem" for algo diferente de "ntfs". Uma única partição do tipo Linux (83) é disponibilizada no disco. Esta partição não é formatada se ela pode ser montado com êxito.

**ResourceDisk.Filesystem:**  
```
Type: String  
Default: ext4
```
Especifica o tipo de sistema de arquivos para o disco do recurso. Valores aceitos variam de acordo com a distribuição do Linux. Se a sequência for X, em seguida, mkfs.X deve estar presente na imagem do Linux. Imagens de 11 SLES geralmente devem utilizar 'ext3'. FreeBSD imagens devem usar 'ufs2' aqui.

**ResourceDisk.MountPoint:**  
```
Type: String  
Default: /mnt/resource 
```
Especifica o caminho em que o disco do recurso é montado. O disco de recurso é um disco *temporário* e pode ser esvaziado quando a VM é desprovisionada.

**ResourceDisk.MountOptions**  
```
Type: String  
Default: None
```
Especifica opções de montagem de disco a serem passadas ao comando de montagem -o. Trata-se de uma lista de valores separados por vírgulas, por exemplo. 'nodev, nosuid'. Consulte montagem(8) para obter detalhes.

**ResourceDisk.EnableSwap:**  
```
Type: Boolean  
Default: n
```
Se definir um arquivo de permuta (/ arquivo de permuta) é criado no disco recursos e adicionado ao espaço de troca de sistema.

**ResourceDisk.SwapSizeMB:**  
```
Type: Integer  
Default: 0
```
Especifica o tamanho máximo do arquivo de permuta em megabytes.

**Logs.Verbose:**  
```
Type: Boolean  
Default: n
```
Se definido, a verbosidade do log é aumentado. Waagent faz /var/log/waagent.log e utiliza a funcionalidade de logrotate do sistema para girar os logs.

**OS.EnableRDMA**  
```
Type: Boolean  
Default: n
```
Se definido, o agente tenta instalar e, em seguida, carregar um driver de kernel RDMA que corresponde à versão do firmware do hardware subjacente.

**OS.RootDeviceScsiTimeout:**  
```
Type: Integer  
Default: 300
```
Esta configuração configura o tempo limite de SCSI em segundos nos drives de disco e os dados de SO. Se não for definido, o sistema de padrões são usados.

**OS.OpensslPath:**  
```
Type: String  
Default: None
```
Esta configuração pode ser usado para especificar um caminho alternativo para o openssl binário a ser usado para operações de criptografia.

**HttpProxy.Host, HttpProxy.Port**  
```
Type: String  
Default: None
```
Se definido, o agente usa este servidor proxy para acessar a internet. 

**AutoUpdate.Enabled**
```
Type: Boolean
Default: y
```
Habilite ou desabilite a atualização automática para o processamento de estado de meta; o padrão é habilitada.



## <a name="ubuntu-cloud-images"></a>Imagens de nuvem do Ubuntu
As Imagens de Nuvem do Ubuntu utilizam [cloud-init](https://launchpad.net/ubuntu/+source/cloud-init) para executar muitas tarefas de configuração que de outra forma seriam gerenciadas pelo Agente Linux do Azure. As seguintes diferenças aplicam:

* **Provisioning.Enabled** segue o padrão "n" nas Imagens de Nuvem do Ubuntu que usam cloud-init para executar tarefas de provisionamento.
* Os seguintes parâmetros de configuração não têm efeito nas Imagens de Nuvem do Ubuntu que usam cloud-init para gerenciar o disco de recurso e o espaço de troca:
  
  * **ResourceDisk.Format**
  * **ResourceDisk.Filesystem**
  * **ResourceDisk.MountPoint**
  * **ResourceDisk.EnableSwap**
  * **ResourceDisk.SwapSizeMB**

* Para mais informações, consulte os seguintes recursos para configurar o ponto de montagem do disco de recurso e o espaço de troca nas Imagens de Nuvem do Ubuntu durante o provisionamento:
  
  * [Ubuntu Wiki: Configurar partições de troca](https://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
  * [Injetando dados personalizados em uma Máquina Virtual do Azure](../windows/classic/inject-custom-data.md)

