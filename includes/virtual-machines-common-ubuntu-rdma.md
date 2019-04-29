---
ms.openlocfilehash: 9a5a2d92f70c411c46ebb4efb35e17e9b0c477ca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542145"
---
1. Instalar dapl, rdmacm, ibverbs e mlx4

   ```bash
   sudo apt-get update

   sudo apt-get install libdapl2 libmlx4-1
   ```

2. Em /etc/waagent.conf, habilite o RDMA removendo as marcas de comentários das seguintes linhas de configuração. Você precisa de acesso à raiz para editar esse arquivo.

   ```
   OS.EnableRDMA=y

   OS.UpdateRdmaDriver=y
   ```

3. Adicione ou altere as seguintes configurações de memória em KB no arquivo /etc/security/limits.conf. Você precisa de acesso à raiz para editar esse arquivo. Para fins de teste, é possível definir memlock como ilimitado. Por exemplo: `<User or group name>   hard    memlock   unlimited`.

   ```
   <User or group name> hard    memlock <memory required for your application in KB>

   <User or group name> soft    memlock <memory required for your application in KB>
   ```

4. Instalar a Intel MPI Library. [Comprar e baixe](https://software.intel.com/intel-mpi-library/) a biblioteca da Intel ou baixe a [versão de avaliação gratuita](https://registrationcenter.intel.com/en/forms/?productid=1740).

   ```bash
   wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
   ```

   Há suporte apenas para tempos de execução MPI Intel 5.x.

   Para saber as etapas de instalação, consulte o [Guia de instalação da Intel MPI Library](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html).

5. Habilite ptrace para os processos que não são de depuração e não são da raiz (necessária para as versões mais recentes da Intel MPI).

   ```bash
   echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
   ```
   <!--ms.date: 07/30/2018-->