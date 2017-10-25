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
 wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/11595/l_mpi_2017.3.196.tgz
   ```
 
 Para saber as etapas de instalação, consulte o [Guia de instalação da Intel MPI Library](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html).

5. Habilite ptrace para os processos que não são de depuração e não são da raiz (necessária para as versões mais recentes da Intel MPI).
 
  ```bash
  echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
  ```
