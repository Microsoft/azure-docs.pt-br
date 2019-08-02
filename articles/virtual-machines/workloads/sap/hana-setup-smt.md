---
title: Como configurar o servidor SMT de instalação para o SAP HANA no Azure (instâncias grandes) | Microsoft Docs
description: Como configurar o servidor SMT para o SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 879bea079a5de6558b20edfc05ba432c98254c9b
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707382"
---
# <a name="set-up-smt-server-for-suse-linux"></a>Configurar o servidor SMT para o SUSE Linux
As Instâncias Grandes do SAP HANA não têm conectividade direta com a Internet. Não é um processo simples registrar uma unidade como essa no provedor do sistema operacional e baixar e aplicar atualizações. Uma solução para o SUSE Linux é configurar um servidor SMT em uma máquina virtual do Azure. Hospede a máquina virtual em uma rede virtual do Azure conectada à HANA em Instância Grande. Com um servidor SMT desse tipo, a unidade do HANA em Instância Grande pode registrar e baixar atualizações. 

Para obter mais documentação sobre o SUSE, confira a [Ferramenta de Gerenciamento de Assinaturas para SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

Pré-requisitos para instalar um servidor SMT que realiza a tarefa para o HANA em Instâncias Grandes são:

- Uma rede virtual do Azure conectada ao circuito de ExpressRoute do HANA em Instância Grande.
- Uma conta SUSE associada a uma organização. A organização deve ter uma assinatura válida do SUSE.

## <a name="install-smt-server-on-an-azure-virtual-machine"></a>Instalar o servidor SMT em uma máquina virtual do Azure

Primeiro, entre no [Atendimento ao Cliente do SUSE](https://scc.suse.com/).

Vá para **Organização** > **Credenciais da Organização**. Nesta seção, você deve encontrar as credenciais necessárias para configurar o servidor SMT.

Em seguida, instale uma VM do Linux SUSE na rede virtual do Azure. Para implantar a máquina virtual, pegue uma imagem de galeria do SLES 12 SP2 do Azure (selecione imagem SUSE BYOS). No processo de implantação, não defina um nome DNS e não use endereços IP estáticos.

![Captura de tela da implantação da máquina virtual para o servidor SMT](./media/hana-installation/image3_vm_deployment.png)

A máquina virtual implantada é menor e obteve o endereço IP interno na rede virtual do Azure de 10.34.1.4. O nome da máquina virtual é *smtserver*. Após a instalação, a conectividade com a unidade do HANA em Instância Grande foi verificada. Dependendo de como você organizou a resolução de nomes, talvez precise configurar a resolução das unidades do HANA em Instância Grande em etc/hosts da máquina virtual do Azure. 

Adicionar um disco à máquina virtual. Use esse disco para armazenar as atualizações; o disco de inicialização em si pode ser muito pequeno. Aqui, o disco foi montado em /srv/www/htdocs, conforme mostrado na captura de tela a seguir. Um disco de 100 GB deve ser suficiente.

![Captura de tela da implantação da máquina virtual para o servidor SMT](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Entre em uma ou mais unidades do HANA em Instância Grande, mantenha /etc/hosts e verifique se você consegue acessar a máquina virtual do Azure que deve executar o servidor SMT na rede.

Após essa verificação, entre na máquina virtual do Azure que deve executar o servidor SMT. Se você estiver usando o putty para entrar na máquina virtual, execute esta sequência de comandos na janela Bash:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Reinicie o Bash para ativar as configurações. Depois, inicie o YAST.

Conecte sua VM (smtserver) para o site do SUSE.

```
smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
Registered SLES_SAP 12.2 x86_64
To server: https://scc.suse.com
Using E-Mail: email address
Successfully registered system.
```

Depois que a máquina virtual estiver conectada ao site do SUSE, instale os pacotes de smt. Digite o seguinte comando para instalar os pacotes necessários.

```
smtserver:~ # zypper in smt
Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
Loading repository data...
Reading installed packages...
Resolving package dependencies...
```


Você também pode usar a ferramenta YAST para instalar os pacotes smt. No YAST, acesse **Manutenção de Software** e pesquise smt. Selecione **smt**, que alterna automaticamente para yast2-smt.

![Captura de tela do SMT no YAST](./media/hana-installation/image5_smt_in_yast.PNG)


Aceite a seleção para a instalação no smtserver. Após a conclusão da instalação, vá para a configuração do servidor SMT. Insira as credenciais da organização do Atendimento ao Cliente SUSE recuperado anteriormente. Insira também o nome de host de máquina virtual do Azure como a URL do servidor SMT. Nesta demonstração, é um https:\//smtserver.

![Captura de tela da configuração do servidor SMT](./media/hana-installation/image6_configuration_of_smtserver1.png)

Agora teste se a conexão ao Atendimento ao Cliente do SUSE funciona. Como visto na seguinte captura de tela, neste caso de demonstração, isso funcionou.

![Captura de tela da conexão de teste ao Atendimento ao Cliente do SUSE](./media/hana-installation/image7_test_connect.png)

Após inicia a instalação do SMT, forneça uma senha de banco de dados. Porque é uma nova instalação, você deve definir essa senha conforme mostrado na seguinte captura de tela.

![Captura de tela da definição de senha para o banco de dados](./media/hana-installation/image8_define_db_passwd.PNG)

A próxima etapa é criar um certificado.

![Captura de tela da criação de um certificado para o servidor SMT](./media/hana-installation/image9_certificate_creation.PNG)

Ao final da configuração, pode levar alguns minutos para executar a verificação de sincronização. Após a instalação e configuração do servidor SMT, você deverá encontrar o repositório do diretório sob o ponto de montagem /srv/www/htdocs/. Também há alguns subdiretórios no repositório. 

Reinicie o servidor SMT e seus serviços relacionados com estes comandos.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-packages-onto-smt-server"></a>Baixar pacotes para o servidor SMT

Depois que todos os serviços forem reiniciados, selecione os pacotes apropriados no Gerenciamento SMT usando o YAST. A seleção do pacote depende da imagem do sistema operacional do servidor do HANA em Instância Grande. A seleção do pacote não depende da versão do SLES nem da versão da máquina virtual que executa o servidor SMT. As capturas de tela a seguir mostram um exemplo da tela de seleção.

![Captura de tela da seleção de pacotes](./media/hana-installation/image10_select_packages.PNG)

Em seguida, comece a cópia inicial dos pacotes selecionados para o servidor SMT que você configurou. Essa cópia é disparada no shell com o comando smt-mirror.

![Captura de tela do download de pacotes no servidor SMT](./media/hana-installation/image11_download_packages.PNG)

Os pacotes devem ser copiados para os diretórios criados no ponto de montagem /srv/www/htdocs. Esse processo pode levar uma hora ou mais, dependendo de quantos pacotes você selecionar. Quando esse processo for concluído, vá para a instalação do cliente SMT. 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Configurar o cliente SMT em unidades de Instância Grande do HANA

Nesse caso, um ou mais clientes são as unidades do HANA em Instância Grande. A instalação do servidor SMT copiou o script clientSetup4SMT.sh para a máquina virtual do Azure. Copie esse script para a unidade de Instância Grande do HANA que você deseja conectar ao servidor SMT. Inicie o script com a opção -h e dê a ele o nome do servidor SMT como um parâmetro. Neste exemplo, o nome é *smtserver*.

![Captura de tela da configuração do cliente SMT](./media/hana-installation/image12_configure_client.PNG)

É possível que o carregamento do certificado do servidor pelo cliente seja bem-sucedido, mas o registro falhe, conforme mostra a seguinte captura de tela.

![Captura de tela de falha no registro do cliente](./media/hana-installation/image13_registration_failed.PNG)

Se o registro falhar, confira [Documentos de suporte do SUSE](https://www.suse.com/de-de/support/kb/doc/?id=7006024) e execute as etapas descritas lá.

> [!IMPORTANT] 
> Para o nome do servidor, forneça o nome da máquina virtual (neste caso, *smtserver*, sem o nome de domínio totalmente qualificado). 

Depois de executar estas etapas, execute o seguinte comando na unidade do HANA em Instância Grande:

```
SUSEConnect –cleanup
```

> [!Note] 
> Aguarde alguns minutos após essa etapa. Se você executar clientSetup4SMT.sh imediatamente, poderá receber um erro.

Se você encontrar um problema que precise corrigir com base nas etapas do artigo do SUSE, reinicie clientSetup4SMT.sh na unidade do HANA em Instância Grande. Agora, ele deverá ser concluído com êxito.

![Captura de tela de sucesso no registro do cliente](./media/hana-installation/image14_finish_client_config.PNG)

Você configurou o cliente SMT da unidade do HANA em Instância Grande para se conectar ao servidor SMT instalado na máquina virtual do Azure. Agora, você pode usar "zypper up" ou "zypper in" para instalar atualizações do sistema operacional no HANA em Instâncias Grandes ou instalar outros pacotes. Você pode obter apenas as atualizações que baixou antes no servidor SMT.

## <a name="next-steps"></a>Próximas etapas
- [Instalação do HANA em HLI](hana-example-installation.md).











