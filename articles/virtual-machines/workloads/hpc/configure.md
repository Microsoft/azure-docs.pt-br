---
title: Computação de alto desempenho - máquinas virtuais do Azure | Microsoft Docs
description: Saiba mais sobre computação de alto desempenho no Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: 10549abfbdacf1fc1ae6b99f4cab20a290c32a2d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707816"
---
# <a name="optimization-for-linux"></a>Otimização para Linux

Este artigo mostra algumas técnicas principais para otimizar sua imagem do sistema operacional. Saiba mais sobre [habilitando InfiniBand](enable-infiniband.md) e otimizar as imagens do sistema operacional.

## <a name="update-lis"></a>Atualizar LIS

Se implantar usando uma imagem personalizada (por exemplo, um SO mais antigo, como o RHEL/CentOS 7.4 ou 7.5), atualize o LIS na VM.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

## <a name="reclaim-memory"></a>Recupera a memória

Melhore a eficiência, recuperando automaticamente a memória para evitar o acesso remoto à memória.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

Para fazer isso persistir após a reinicialização de VM:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

## <a name="disable-firewall-and-selinux"></a>Desabilitar o firewall e o SELinux

Desabilite o firewall e o SELinux.

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

## <a name="disable-cpupower"></a>Desabilitar cpupower

Desabilite cpupower.

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [habilitando InfiniBand](enable-infiniband.md) e otimizar imagens do sistema operacional.

* Saiba mais sobre [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) no Azure.
