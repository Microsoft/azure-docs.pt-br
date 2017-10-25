## <a name="scenario"></a>Cenário
Este documento explica uma implantação que usa um endereço IP público estático atribuído a uma VM (máquina virtual). Nesse cenário, você tem uma única VM com seu próprio endereço IP estático. A VM é parte de uma sub-rede chamada **FrontEnd** e também tem um endereço IP privado estático (**192.168.1.101**) nessa sub-rede.

Talvez seja necessário um endereço IP estático para servidores Web que exigem conexões SSL no qual o certificado SSL está vinculado a um endereço IP. 

![DESCRIÇÃO DA IMAGEM](./media/virtual-network-deploy-static-pip-scenario-include/figure1.png)

Você pode seguir as etapas abaixo para implantar o ambiente mostrado na figura acima.

