---
title: Trabalhar com o Defender para comandos CLI de IoT
description: Este artigo descreve os comandos do defender for IoT CLI para sensores e consoles de gerenciamento local.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/12/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 93efc89722d3152d92b6f8c8038deaa566741f7c
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100636553"
---
# <a name="work-with-defender-for-iot-cli-commands"></a>Trabalhar com o Defender para comandos CLI de IoT

Este artigo descreve os comandos da CLI para sensores e consoles de gerenciamento local. Os comandos podem ser acessados pelos seguintes usuários:

- Administrador
- CyberX 
- Suporte

Para começar a trabalhar na CLI, conecte-se usando um terminal. Por exemplo, nome do terminal `Putty` e `Support` usuário. 

## <a name="create-local-alert-exclusion-rules"></a>Criar regras de exclusão de alerta local

Você pode criar uma regra de exclusão de alerta local inserindo o seguinte comando na CLI:

```azurecli-interactive
alerts exclusion-rule-create [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

Os seguintes atributos podem ser usados com as regras de exclusão de alerta:

| Atributo | Descrição |
|--|--|
| [-h] | Imprime as informações de ajuda para o comando. |
| -n nome | O nome da regra que está sendo criada. |
| [-TS vezes] | O período de tempo para o qual a regra está ativa. Isso deve ser especificado como:<br />`xx:yy-xx:yy`<br />Você pode definir mais de um período de tempo usando uma vírgula entre eles. Por exemplo: `xx:yy-xx:yy, xx:yy-xx:yy`. |
| [– direção da dir] | A direção na qual a regra é aplicada. Isso deve ser especificado como:<br />`both | src | dst` |
| [-dispositivos de desenvolvimento] | O endereço IP e o tipo de endereço dos dispositivos a serem excluídos pela regra, especificado como:<br />`ip-x.x.x.x`<br />`mac-xx:xx:xx:xx:xx:xx`<br />`subnet: x.x.x.x/x` |
| [-a alertas] | O nome do alerta que a regra excluirá:<br />`0x00000`<br />`0x000001` |

## <a name="append-local-alert-exclusion-rules"></a>Acrescentar regras de exclusão de alerta local

Você pode acrescentar regras de exclusão de alertas locais digitando o seguinte comando na CLI:

```azurecli-interactive
alerts exclusion-rule-append [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

Os atributos usados aqui são os mesmos que os atributos explicados na seção criar regras de exclusão de alerta local. A diferença no uso é que, aqui, os atributos são aplicados nas regras existentes.

## <a name="show-local-alert-exclusion-rules"></a>Mostrar regras de exclusão de alerta local

Digite o seguinte comando para apresentar a lista existente de regras de exclusão:

```azurecli-interactive
alerts exclusion-rule-list [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

## <a name="delete-local-alert-exclusion-rules"></a>Excluir regras de exclusão de alerta local

Você pode excluir uma regra de exclusão de alerta existente inserindo o seguinte comando:

```azurecli-interactive
alerts exclusion-rule-remove [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

O atributo a seguir pode ser usado com as regras de exclusão de alerta:

| Atributo | Descrição|
| --------- | ---------------------------------- |
| -n nome | O nome da regra a ser excluída. |

## <a name="sync-time-from-the-ntp-server"></a>Tempo de sincronização do servidor NTP

Você pode habilitar ou desabilitar uma sincronização de horário de um servidor NTP especificado.

### <a name="enable-ntp-sync"></a>Habilitar a sincronização de NTP

Digite o seguinte comando para recuperar periodicamente a hora do servidor NTP especificado:

```azurecli-interactive
ntp enable IP
```

O atributo que você pode definir no comando é o endereço IP do servidor NTP.

### <a name="disable-ntp-sync"></a>Desabilitar a sincronização de NTP

Digite o seguinte comando para desabilitar a sincronização de horário com o servidor NTP especificado:

```azurecli-interactive
ntp disable IP
```

O atributo que você pode definir no comando é o endereço IP do servidor NTP.

## <a name="network-configuration"></a>Configuração de rede

A tabela a seguir descreve os comandos disponíveis para configurar suas opções de rede para o Azure defender para IoT:

|Nome|Comando|Descrição|
|-----------|-------|-----------|
|Ping|`ping IP`| Execute ping em um endereço fora da plataforma defender para IoT.|
|Blink|`network blink`| Localize uma conexão fazendo com que as luzes da interface pisquem. |
|Reconfigurar a rede |`network edit-settings`| Habilite uma alteração nos parâmetros de configuração de rede. |
|Mostrar configurações de rede |`network list`|Exibe os parâmetros do adaptador de rede. |
|Validar a configuração de rede |`network validate` |Apresenta as configurações de rede de saída. <br /> <br />Por exemplo: <br /> <br />Configurações de rede atuais: <br /> interface: eth0 <br /> IP: 10.100.100.1 <br />sub-rede: 255.255.255.0 <br />gateway padrão: 10.100.100.254 <br />DNS: 10.100.100.254 <br />interfaces de monitor: eth1|
|Importar um certificado |`certificate import FILE` |Importa o certificado HTTPS. Você precisará especificar o caminho completo, que leva a um \* arquivo. CRT. |
|Mostrar a data |`date` |Retorna a data atual no host no formato GMT. |

## <a name="filter-network-configurations"></a>Filtrar configurações de rede

O `network capture-filter` comando permite que os administradores eliminem o tráfego de rede que não precisa ser analisado. Você pode filtrar o tráfego usando uma lista de inclusão ou uma lista de exclusão.

```azurecli-interactive
network capture-filter
```

Depois de inserir o comando, você será avisado com a seguinte pergunta:

>`Would you like to supply devices and subnet masks you wish to include in the capture filter? [Y/N]:`

Selecione `Y` para abrir um arquivo do nano onde é possível adicionar um dispositivo, canal, porta e subconjunto de acordo com a seguinte sintaxe:

| Atributo | Descrição |
|--|--|
| 1.1.1.1 | Inclui todo o tráfego para este dispositivo. |
| 1.1.1.1, 2.2.2.2 | Inclui todo o tráfego para esse canal. |
| 1.1.1, 2.2.2 | Inclui todo o tráfego para essa sub-rede. |

Separe os argumentos soltando uma linha.

Quando você inclui um dispositivo, canal ou sub-rede, o sensor processa todo o tráfego válido para esse argumento, incluindo portas e tráfego que normalmente não seria processado.

Em seguida, será feita a seguinte pergunta:

>`Would you like to supply devices and subnet masks you wish to exclude from the capture filter? [Y/N]:`

Selecione `Y` para abrir um arquivo do nano onde é possível adicionar um dispositivo, canal, porta e subconjuntos de acordo com a seguinte sintaxe:

| Atributo | Descrição |
|--|--|
| 1.1.1.1 | Exclui todo o tráfego para este dispositivo. |
| 1.1.1.1, 2.2.2.2 | Exclui todo o tráfego para esse canal, o que significa todo o tráfego entre dois dispositivos. |
| 1.1.1.1, 2.2.2.2, 443 | Exclui todo o tráfego para esse canal por porta. |
| 1.1.1 | Exclui todo o tráfego desta sub-rede. |
| 1.1.1, 2.2.2 | Exclui todo o tráfego para entre sub-redes. |

Separe os argumentos soltando uma linha.

Quando você exclui um dispositivo, canal ou sub-rede, o sensor excluirá todo o tráfego válido para esse argumento.

### <a name="ports"></a>Portas

Inclua ou exclua portas UDP e TCP para todo o tráfego.

>`502`: porta única

>`502,443`: ambas as portas

>`Enter tcp ports to include (delimited by comma or Enter to skip):`

>`Enter udp ports to include (delimited by comma or Enter to skip):`

>`Enter tcp ports to exclude (delimited by comma or Enter to skip):`

>`Enter udp ports to exclude (delimited by comma or Enter to skip):`

### <a name="components"></a>Componentes

Você terá a seguinte pergunta:

>`In which component do you wish to apply this capture filter?`

Suas opções são:  `all` , `dissector` , `collector` , `statistics-collector` , `rpc-parser` ou `smb-parser` .

Na maioria dos casos de uso, selecione `all` .

### <a name="custom-base-capture-filter"></a>Filtro de captura de base personalizado

O filtro de captura de base é a linha de base dos componentes. Por exemplo, o filtro determina quais portas estão disponíveis para o componente.

Selecione `Y` para todas as opções a seguir. Todos os filtros são adicionados à linha de base depois que as alterações são definidas. Se você fizer uma alteração, ela substituirá a linha de base existente.

>`Would you like to supply a custom base capture filter for the dissector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the collector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the statistics-collector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the rpc-parser component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the smb-parser component? [Y/N]:`

>`Type Y for "internal" otherwise N for "all-connected" (custom operation mode enabled) [Y/N]:`

Se você optar por excluir uma sub-rede, como 1.1.1:

- `internal` excluirá apenas essa sub-rede.

- `all-connected` excluirá essa sub-rede e todo o tráfego de e para essa sub-rede.

Recomendamos que você selecione `internal` .

> [!NOTE]
> Suas opções são usadas para todos os filtros na ferramenta e não dependem da sessão. Em outras palavras, você não pode escolher `internal`   alguns filtros e  `all-connected`   para outros.

### <a name="comments"></a>Comentários

Você pode exibir filtros em  ```/var/cyberx/properties/cybershark.properties``` :

- **estatísticas-coletor**:  `bpf_filter property` em ```/var/cyberx/properties/net.stats.collector.properties```

- **dessetor**: `override.capture_filter`   propriedade em ```/var/cyberx/properties/cybershark.properties```

- **RPC-Parser**: `override.capture_filter` propriedade em ```/var/cyberx/properties/rpc-parser.properties```

- **SMB-Parser**:  `override.capture_filter`   propriedade em ```/var/cyberx/properties/smb-parser.properties```

- **coletor**: `general.bpf_filter`   propriedade em ```/var/cyberx/properties/collector.properties```

Você pode restaurar a configuração padrão inserindo o seguinte código para o usuário CyberX:

```azurecli-interactive
sudo cyberx-xsense-capture-filter -p all -m all-connected
```

## <a name="define-client-and-server-hosts"></a>Definir hosts de cliente e servidor

Se o defender para IoT não detectar automaticamente o cliente e os hosts do servidor, digite o seguinte comando para definir os hosts do cliente e do servidor:

```azurecli-interactive
directions [-h] [--identifier IDENTIFIER] [--port PORT] [--remove] [--add]  
[--tcp] [--udp]
```

Você pode usar os seguintes atributos com o `directions` comando:

| Atributo | Descrição |
|--|--|
| [-h] | Imprime informações de ajuda para o comando. |
| [--identificador do identificador] | O identificador do servidor. |
| [--porta da porta] | A porta do servidor. |
| [--remover] | Remove um host de cliente ou servidor da lista. |
| [--adicionar] | Adiciona um host de cliente ou servidor à lista. |
| [--TCP] | Use TCP ao se comunicar com este host. |
| [--UDP] | Use UDP ao se comunicar com este host. |

## <a name="system-actions"></a>Ações do sistema
A tabela a seguir descreve os comandos disponíveis para executar várias ações do sistema no defender para IoT:

|Nome|Código|Descrição|
|----|----|-----------|
|Mostrar a data|`date`|Retorna a data atual no host no formato GMT.|
|Reinicialize o host|`system reboot`|Reinicia o dispositivo de host.|
|Desligar o host|`system shutdown`|Desliga o host.|
|Fazer backup do sistema|`system backup`|Inicia um backup imediato (um backup não agendado).|
|Restaurar o sistema de um backup|`system restore`|Restaura a partir do backup mais recente.|
|Listar os arquivos de backup|`system backup-list`|Lista os arquivos de backup disponíveis.|
|Exibir o status de todos os serviços do defender para a plataforma IoT|`system sanity`|Verifica o desempenho do sistema listando o status atual de todos os serviços do defender para a plataforma IoT.|
|Mostrar a versão do software|`system version`|Exibe a versão do software em execução no momento no sistema.|

## <a name="deploy-ssl-and-tls-certificates-to-appliances"></a>Implantar certificados SSL e TLS em dispositivos

Digite o seguinte comando para importar SSL e certificados corporativos TLS para a CLI:

```azurecli-interactive
cyberx-xsense-certificate-import
```
Para usar a ferramenta, você precisa carregar os arquivos de certificado para o dispositivo. Você pode fazer isso por meio de ferramentas como WinSCP ou wget. 

O comando dá suporte aos seguintes sinalizadores de entrada:

| Sinalizador | Descrição |
|--|--|
| -H | Mostra a sintaxe da ajuda da linha de comando. |
| --CRT | O caminho para o arquivo de certificado (extensão. CRT). |
| --chave | O \* arquivo. Key. O comprimento da chave deve ser no mínimo 2.048 bits. |
| --cadeia | Caminho para o arquivo da cadeia de certificados (opcional). |
| --aprovado | Frase secreta usada para criptografar o certificado (opcional). |
| --passphrase-Set | O padrão é **false**, **não usado**. <br />Defina como **true** para usar a frase secreta anterior fornecida com o certificado anterior (opcional). |  |

Quando você estiver usando a ferramenta:

- Verifique se os arquivos de certificado estão legíveis no dispositivo. 

- Confirme com ele o domínio do dispositivo (como ele aparece no certificado) com o servidor DNS e o endereço IP correspondente. 
    
## <a name="see-also"></a>Confira também

[APIs do defender for IoT API e do console de gerenciamento](references-work-with-defender-for-iot-apis.md)
