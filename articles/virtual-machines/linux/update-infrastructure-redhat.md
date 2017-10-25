---
title: "Infraestrutura de Atualização do Red Hat | Microsoft Docs"
description: "Saiba mais sobre a Infraestrutura de Atualização do Red Hat para as instâncias sob demanda do Red Hat Enterprise Linux no Microsoft Azure"
services: virtual-machines-linux
documentationcenter: 
author: BorisB2015
manager: timlt
editor: 
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/10/2017
ms.author: borisb
ms.openlocfilehash: ea9a5cbd9b9b7b67ceb131cb8ba1d2476dbd5f72
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Infraestrutura de Atualização do Red Hat para as VMs Red Hat Enterprise do Linux sob demanda no Azure
 A RHUI ([Infraestrutura de Atualização do Red Hat](https://access.redhat.com/products/red-hat-update-infrastructure)) permite que os provedores de nuvem, como o Azure, espelhem o conteúdo do repositório hospedado pelo Red Hat, criem repositórios personalizados com conteúdo específico ao Azure e o disponibilizem para as VMs do usuário final.

As imagens PAYG (Pagas conforme o uso) do RHEL (Red Hat Enterprise Linux) vêm pré-configuradas para acessar o RHUI do Azure. Nenhuma configuração adicional é necessária. Para obter as atualizações mais recentes, execute `sudo yum update` depois que sua instância do RHEL estiver pronta. Este serviço é incluído como parte das taxas de software PAYG do RHEL.

## <a name="important-information-about-azure-rhui"></a>Informações importantes sobre o RHUI do Azure
* Atualmente, o RHUI do Azure é compatível apenas com última versão secundária em cada família do RHEL (RHEL6 ou RHEL7). Para atualizar uma instância VM do RHEL conectada ao RHUI para a versão secundária mais recente, execute `sudo yum update`.

    Por exemplo, se você provisionar uma VM com base em uma imagem PAYG do RHEL 7.2 e executar `sudo yum update`, você acabará com uma VM do RHEL 7.4 (a versão secundária mais recente da família RHEL7).

    Para evitar esse comportamento, você precisa criar sua própria imagem conforme descrito no artigo [Criar e carregar uma máquina virtual com base em Red Hat para o Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Em seguida, você precisa conectar-se para uma infraestrutura de atualização diferente ([diretamente aos servidores de entrega de conteúdo do Red Hat](https://access.redhat.com/solutions/253273) ou a um [servidor do Red Hat Satellite](https://access.redhat.com/products/red-hat-satellite)).

* O acesso ao RHUI hospedado pelo Azure é incluído no preço de imagem PAYG do RHEL. Cancelar o registro de uma VM RHEL PAYG do RHUI hospedado no Azure não converte a máquina virtual em uma VM do tipo BYOL (traga sua própria licença). Se você registrar a mesma VM com outra origem de atualizações, você pode incorrer em encargos duplos _indiretos_. Você será cobrado pela primeira vez pela taxa de software RHEL do Azure. Você será cobrado pela segunda vez por assinaturas do Red Hat adquiridas anteriormente. Se você precisar usar consistentemente uma infraestrutura de atualização diferente do RHUI hospedado pelo Azure, considere a possibilidade de criar e implantar suas próprias imagens (do tipo BYOL). Esse processo é descrito em [Criar e carregar uma máquina virtual com base em Red Hat para o Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

* Duas classes de imagens PAYG do RHEL no Azure (RHEL for SAP HANA e RHEL for SAP Business Applications) estão conectadas a canais dedicados do RHUI que permanecem na versão secundária específica do RHEL, conforme necessário para a certificação da SAP. 

* O acesso ao RHUI hospedado pelo Azure é limitado às VMs nos [Intervalos de IP do datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Se você estiver encaminhando por proxy todo o tráfego de VM por meio da infraestrutura de rede local, talvez você precise configurar rotas definidas pelo usuário para as VMs PAYG do RHEL para acessar o RHUI do Azure.

### <a name="the-ips-for-the-rhui-content-delivery-servers"></a>Os IPs para os servidores de distribuição de conteúdo do RHUI

O RHUI está disponível em todas as regiões onde as imagens do RHEL sob demanda estão disponíveis. Atualmente, inclui todas as regiões públicas listadas na página [Painel de status do Azure](https://azure.microsoft.com/status/) e as regiões Microsoft Azure Alemanha e Governo dos EUA do Azure. 

Se você estiver usando uma configuração de rede para restringir o acesso de VMs PAYG do RHEL, verifique se os seguintes IPs são permitidos para que `yum update` funcione dependendo do ambiente em que você está: 

```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213

# Azure US Government
13.72.186.193

# Azure Germany
51.5.243.77
51.4.228.145
```

## <a name="rhui-azure-infrastructure-update"></a>Atualização da infraestrutura RHUI do Azure

Em setembro de 2016, implantamos uma RHUI atualizada do Azure. Em abril de 2017, desligamos o antigo RHUI do Azure. Se você usa as imagens PAYG do RHEL (ou seus instantâneos) de setembro de 2016 ou posterior, você está se conectando automaticamente ao novo RHUI do Azure. No entanto, se houver instantâneos mais antigos em suas VMs, você precisará atualizar manualmente a configuração deles para acessar o RHUI do Azure, conforme descrito na seção a seguir.

Os novos servidores do Azure RHUI são implantados com o [Gerenciador de Tráfego do Azure](https://azure.microsoft.com/services/traffic-manager/). No Gerenciador de Tráfego, um único ponto de extremidade (rhui-1.microsoft.com) pode ser usado por qualquer VM, independentemente da região. 

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Solução de problemas de conexão com o RHUI do Azure
Se você estiver tendo problemas para se conectar ao RHUI do Azure de sua VM PAYG do Azure RHEL, siga estas etapas:

1. Inspecione a configuração da VM para o ponto de extremidade do RHUI do Azure:

    a. Verifique se o arquivo `/etc/yum.repos.d/rh-cloud.repo` contém uma referência para `rhui-[1-3].microsoft.com` na `baseurl` da seção `[rhui-microsoft-azure-rhel*]` do arquivo. Se esse é o caso, significa que você está usando o novo RHUI do Azure.

    b. No entanto, se ele estiver apontando para um local com o padrão a seguir, será necessária uma atualização da configuração: `mirrorlist.*cds[1-4].cloudapp.net`. Você está usando o instantâneo de VM antigo e precisa atualizá-lo o para que ele aponte para o novo RHUI do Azure.

2. O acesso ao RHUI hospedado no Azure é limitado às VMs nos [intervalos de IP do datacenter do Microsoft Azure] (https://www.microsoft.com/download/details.aspx?id=41653).
 
3. Se estiver usando a nova configuração, verifique se a VM se conecta do intervalo de IP do Azure e, se ainda não puder se conectar ao RHUI do Azure, registre um caso de suporte na Microsoft ou no Red Hat.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Procedimento de atualização manual para usar os servidores do RHUI do Azure
Esse procedimento é fornecido apenas para referência. Imagens RHEL PAYG já tem a configuração correta para se conectar ao Azure RHUI. Para atualizar manualmente a configuração para usar os servidores de RHUI do Azure, conclua as seguintes etapas:

1. Baixe a assinatura de chave pública por meio de curl.

   ```bash
   curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
   ```

2. Verifique a validade da chave baixada.

   ```bash
   gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
   ```

3. Verifique a saída e, em seguida, verifique o `keyid` e o `user ID packet`.

   ```bash
   Version: GnuPG v1.4.7 (GNU/Linux)
   :public key packet:
           version 4, algo 1, created 1446074508, expires 0
           pkey[0]: [2048 bits]
           pkey[1]: [17 bits]
           keyid: EB3E94ADBE1229CF
   :user ID packet: "Microsoft (Release signing) <gpgsecurity@microsoft.com>"
   :signature packet: algo 1, keyid EB3E94ADBE1229CF
           version 4, created 1446074508, md5len 0, sigclass 0x13
           digest algo 2, begin of digest 1a 9b
           hashed subpkt 2 len 4 (sig created 2015-10-28)
           hashed subpkt 27 len 1 (key flags: 03)
           hashed subpkt 11 len 5 (pref-sym-algos: 9 8 7 3 2)
           hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)
           hashed subpkt 22 len 2 (pref-zip-algos: 2 1)
           hashed subpkt 30 len 1 (features: 01)
           hashed subpkt 23 len 1 (key server preferences: 80)
           subpkt 16 len 8 (issuer key ID EB3E94ADBE1229CF)
           data: [2047 bits]
   ```

4. Instale a chave pública.

   ```bash
   sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
   sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
   ```

5. Baixar, verificar e instalar um cliente RPM (Gerenciador de Pacotes RPM).
    
    >[!NOTE]
    >As versões do pacote sofrem alterações. Se você se conectar manualmente ao RHUI do Azure, talvez encontre a última versão do pacote do cliente para cada família do RHEL provisionando a última imagem da galeria.
  
   a. Baixar. 
   
    - Para RHEL 6:
        ```bash
        curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.1-32.noarch.rpm 
        ```
    
    - Para RHEL 7:
        ```bash
        curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.1-19.noarch.rpm  
        ```

   b. Verificar.

   ```bash
   rpm -Kv azureclient.rpm
   ```

   c. Verifique a saída para garantir que a assinatura do pacote esteja OK.

   ```bash
   azureclient.rpm:
       Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
       Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
       V3 RSA/SHA256 Signature, key ID be1229cf: OK
       MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
   ```

   d. Instale o RPM.

    ```bash
    sudo rpm -U azureclient.rpm
    ```

6. Após a conclusão, verifique se você pode acessar o RHUI do Azure por meio da VM.

## <a name="next-steps"></a>Próximas etapas
Para criar uma VM do Red Hat Enterprise Linux por meio de uma imagem PAYG do Azure Marketplace e aproveitar o RHUI hospedado pelo Azure, acesse o [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/). 