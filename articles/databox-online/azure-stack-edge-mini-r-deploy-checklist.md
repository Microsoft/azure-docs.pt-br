---
title: Lista de verificação de pré-implantação para implantar Azure Stack dispositivo mini R de borda
description: Este artigo descreve as informações que podem ser coletadas antes de implantar o dispositivo de borda do Azure Stack mini R.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: 8bb07827d26efce3ab3454f370afb116ba13eb19
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533801"
---
# <a name="deployment-checklist-for-your-azure-stack-edge-mini-r-device"></a>Lista de verificação de implantação para o dispositivo mini R do Azure Stack Edge  

Este artigo descreve as informações que podem ser coletadas à frente da implantação real do dispositivo do Azure Stack Edge mini R. 

Use a lista de verificação a seguir para garantir que você tenha essas informações depois de ter colocado um pedido para um dispositivo de borda Azure Stack mini R e antes de receber o dispositivo. 

## <a name="deployment-checklist"></a>Lista de verificação para implantação 

| Fase                             | Parâmetro                                                                                                                                                                                                                           | Detalhes                                                                                                           |
|-----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| Gerenciamento de dispositivo               | <li>Assinatura do Azure</li><li>Provedores de recursos registrados</li><li>Conta de Armazenamento do Azure</li>|<li>Habilitado para Azure Stack o mini R/Gateway do Data Box do Edge, acesso de proprietário ou colaborador.</li><li>Em portal do Azure, vá para **página inicial > assinaturas > sua assinatura > provedores de recursos**. Pesquise `Microsoft.DataBoxEdge` e registre-se. Repita para `Microsoft.Devices` se implantar cargas de trabalho de IOT.</li><li>Precisa de credenciais de acesso</li> |
| Instalação do dispositivo               | Cabos de alimentação no pacote. <br>Para nós, um cabo SVE 18/3 classificado para 125 V e 15 amps com um 15P de C13 do NEMA 5, com um conector de entrada para saída (Input to Output), é enviado. | Para obter mais informações, consulte a lista de [cabos de energia com suporte por país](azure-stack-edge-technical-specifications-power-cords-regional.md)  |
|                                   | <li>Pelo menos 1 X 1-cabo de rede RJ-45 de GbE para a porta 1  </li><li> Pelo menos 1 X 25-GbE SFP + cabo de cobre para a porta 3, porta 4, porta 5 ou porta 6</li>| O cliente precisa adquirir esses cabos.<br>Para obter uma lista completa dos cabos de rede, comutadores e transceptores com suporte para placas de rede do dispositivo, consulte [matriz de interoperabilidade da série Cavium FastlinQ 41000](https://www.marvell.com/documents/xalflardzafh32cfvi0z/) e [adaptadores de rede Mellanox Dual Port 25g ConnectX-4 Channel Adapter](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products).| 
| Conexão do dispositivo pela primeira vez      | <li>Laptop cujas configurações de IPv4 podem ser alteradas. Esse laptop se conecta à porta 1 por meio de um comutador ou de um adaptador USB para Ethernet.  </li><!--<li> A minimum of 1 GbE switch must be used for the device once the initial setup is complete. The local web UI will not be accessible if the connected switch is not at least 1 Gbe.</li>-->|   |
| Entrada do dispositivo                      | Senha de administrador do dispositivo, entre 8 e 16 caracteres e contém três dos seguintes: letras maiúsculas, letras minúsculas, números e caracteres especiais.                                            | A senha padrão é *password1* que expira na primeira entrada.                                                     |
| Configurações de rede                  | O dispositivo vem com duas portas de rede de 2 x 1 GbE, 4 x 25 GbE. <li>A porta 1 é usada para definir apenas as configurações de gerenciamento. Uma ou mais portas de dados podem ser conectadas e configuradas. </li><li> Pelo menos uma interface de rede de dados entre a porta 2 – a porta 6 precisa estar conectada à Internet (com conectividade com o Azure).</li><li> Configuração de DHCP estático e IPv4 com suporte. | A configuração estática de IPv4 requer IP, servidor DNS e gateway padrão.   |
| Configurações de rede de computação     | <li>Exigir dois IPs gratuitos, estáticos e contíguos para nós kubernetes e um IP estático para o serviço IoT Edge.</li><li>Exija um IP adicional para cada serviço extra ou módulo que você implantará.</li>| Somente a configuração IPv4 estática tem suporte.|
| Adicional Configurações de proxy Web     | <li>IP/FQDN do servidor proxy da Web, porta </li><li>Nome de usuário do proxy Web, senha</li> | Não há suporte para o proxy Web com a configuração de computação. |
| Configurações de firewall e porta        | Se estiver usando o firewall, verifique se os [padrões de URLs e portas listadas](azure-stack-edge-system-requirements.md#networking-port-requirements) são permitidos para IPS de dispositivo. |  |
| Aconselhável Configurações de hora       | Configure o fuso horário, o servidor NTP primário, o servidor NTP secundário. | Configure o servidor NTP primário e secundário na rede local.<br>Se o servidor local não estiver disponível, os servidores NTP públicos poderão ser configurados.                                                    |
| Adicional Atualizar configurações do servidor | <li>Exigir o endereço IP do servidor de atualização na rede local, caminho para o servidor do WSUS. </li> | Por padrão, o servidor Windows Update público é usado.|
| Configurações do dispositivo | <li>FQDN (nome de domínio totalmente qualificado) do dispositivo </li><li>Domínio DNS</li> | |
| Adicional Certificado  | Para testar cargas de trabalho de não produção, use a [opção gerar certificados](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates) <br><br> Se você usa seus certificados, inclusive as cadeias de assinaturas, então [Adicione certificados](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates) no formato apropriado.| Configure certificados somente se você alterar o nome do dispositivo e/ou o domínio DNS. |
| Ativação  | Exigir chave de ativação do recurso Azure Stack Edge.    | Depois de gerada, a chave expira em 3 dias. |

<!--
| (Optional) MAC Address            | If MAC address needs to be approved, get the address of the connected port from local UI of the device. |                                                                                                                   |
| (Optional) Network switch port    | Device hosts Hyper-V VMs for compute. Some network switch port configurations don’t accommodate these setups by default.                                                                                                        |                                                                                                                   |-->


## <a name="next-steps"></a>Próximas etapas

Prepare-se para implantar o [dispositivo mini R do Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md).
