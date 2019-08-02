---
title: Habilitar InifinBand com SR-IOV - máquinas virtuais do Azure | Microsoft Docs
description: Saiba como habilitar InfiniBand com SR-IOV.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 2e28627359f339a3bf818a15d6a5c8e456fb554a
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797534"
---
# <a name="enable-infiniband-with-sr-iov"></a>Habilitar InfiniBand com SR-IOV

A maneira mais simples e recomendada de configurar sua imagem VM personalizada com InfiniBand (IB) é adicionar a extensão de VM InfiniBandDriverWindows InfiniBandDriverLinux à sua implantação.
Saiba como usar essas extensões de VM com [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) e [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances)

Para configurar manualmente o InfiniBand SR-iov habilitado VMs (atualmente HB e HC de série), siga as etapas abaixo. Essas etapas são apenas para o RHEL/CentOS. Para Ubuntu (16.04 e 18.04) e SLES (12 SP4 e 15), os drivers de caixa de entrada funcionam bem.

## <a name="manually-install-ofed"></a>Instalar manualmente o OFED

Instalar os drivers mais recentes do MLNX_OFED ConnectX-5 da [Mellanox](https://www.mellanox.com/page/products_dyn?product_family=26).

Para RHEL/CentOS (exemplo abaixo para 7.6):

```bash
sudo yum install -y kernel-devel python-devel
sudo yum install -y redhat-rpm-config rpm-build gcc-gfortran gcc-c++
sudo yum install -y gtk2 atk cairo tcl tk createrepo
wget --retry-connrefused --tries=3 --waitretry=5 http://content.mellanox.com/ofed/MLNX_OFED-4.5-1.0.1.0/MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
tar zxvf MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
sudo ./MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64/mlnxofedinstall --add-kernel-support
```

Para Windows, baixe e instale os drivers de 2 WinOF ConnectX-5 do [Mellanox](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34)

## <a name="enable-ipoib"></a>Habilitar IPoIB

```bash
sudo sed -i 's/LOAD_EIPOIB=no/LOAD_EIPOIB=yes/g' /etc/infiniband/openib.conf
sudo /etc/init.d/openibd restart
if [ $? -eq 1 ]
then
  sudo modprobe -rv  ib_isert rpcrdma ib_srpt
  sudo /etc/init.d/openibd restart
fi
```

## <a name="assign-an-ip-address"></a>Atribuir um endereço IP

Atribua um endereço IP à interface ib0, usando:

- Atribua manualmente o endereço IP para a Interface ib0 (como raiz).

    ```bash
    ifconfig ib0 $(sed '/rdmaIPv4Address=/!d;s/.*rdmaIPv4Address="\([0-9.]*\)".*/\1/' /var/lib/waagent/SharedConfig.xml)/16
    ```

OU

- Use WALinuxAgent para atribuir o endereço IP e torná-lo a persistir.

    ```bash
    yum install -y epel-release
    yum install -y python-pip
    python -m pip install --upgrade pip setuptools wheel
    wget "https://github.com/Azure/WALinuxAgent/archive/release-2.2.36.zip"
    unzip release-2.2.36.zip
    cd WALinuxAgent*
    python setup.py install --register-service --force
    sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
    sed -i -e 's/# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
    systemctl restart waagent
    ```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) no Azure.
