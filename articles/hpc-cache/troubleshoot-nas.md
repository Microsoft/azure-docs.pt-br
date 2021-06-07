---
title: Solucionar problemas de destinos de armazenamento NFS do cache do Azure HPC
description: Dicas para evitar e corrigir erros de configuração e outros problemas que podem causar falha ao criar um destino de armazenamento NFS
author: ekpgh
ms.service: hpc-cache
ms.topic: troubleshooting
ms.date: 03/18/2020
ms.author: v-erkel
ms.openlocfilehash: bb17918774d23dbeb2747fa55eefc4956812e254
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775690"
---
# <a name="troubleshoot-nas-configuration-and-nfs-storage-target-issues"></a>Solucionar problemas de configuração do NAS e destino de armazenamento NFS

Este artigo fornece soluções para alguns erros de configuração comuns e outros problemas que podem impedir que o cache HPC do Azure adicione um sistema de armazenamento NFS como um destino de armazenamento.

Este artigo inclui detalhes sobre como verificar portas e como habilitar o acesso de raiz a um sistema NAS. Ele também inclui informações detalhadas sobre problemas menos comuns que podem causar falha na criação do destino de armazenamento NFS.

> [!TIP]
> Antes de usar este guia, leia [pré-requisitos para destinos de armazenamento NFS](hpc-cache-prerequisites.md#nfs-storage-requirements).

Se a solução para seu problema não estiver incluída aqui, [abra um tíquete de suporte](hpc-cache-support-ticket.md) para que o serviço e o suporte da Microsoft possam trabalhar com você para investigar e resolver o problema.

## <a name="check-port-settings"></a>Verificar as configurações de porta

O cache HPC do Azure precisa de acesso de leitura/gravação a várias portas UDP/TCP no sistema de armazenamento NAS de back-end. Verifique se essas portas estão acessíveis no sistema NAS e também se o tráfego é permitido para essas portas por meio de firewalls entre o sistema de armazenamento e a sub-rede de cache. Talvez seja necessário trabalhar com firewall e administradores de rede para que sua data center Verifique essa configuração.

As portas são diferentes para sistemas de armazenamento de diferentes fornecedores, portanto, verifique os requisitos do seu sistema ao configurar um destino de armazenamento.

Em geral, o cache precisa de acesso a essas portas:

| Protocolo | Porta  | Serviço  |
|----------|-------|----------|
| TCP/UDP  | 111   | rpcbind  |
| TCP/UDP  | 2049  | NFS      |
| TCP/UDP  | 4045  | nlockmgr |
| TCP/UDP  | 4046  | montado   |
| TCP/UDP  | 4047  | status   |

Para saber as portas específicas necessárias para seu sistema, use o comando a seguir ``rpcinfo`` . Este comando abaixo lista as portas e formata os resultados relevantes em uma tabela. (Use o endereço IP do seu sistema no lugar do *<storage_IP>* termo.)

Você pode emitir esse comando de qualquer cliente Linux que tenha a infraestrutura de NFS instalada. Se você usar um cliente dentro da sub-rede do cluster, ele também poderá ajudar a verificar a conectividade entre a sub-rede e o sistema de armazenamento.

```bash
rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
```

Verifique se todas as portas retornadas pela ``rpcinfo`` consulta permitem o tráfego irrestrito da sub-rede do cache HPC do Azure.

Verifique essas configurações no próprio NAS e também em todos os firewalls entre o sistema de armazenamento e a sub-rede de cache.

## <a name="check-root-access"></a>Verificar acesso à raiz

O cache HPC do Azure precisa de acesso às exportações do seu sistema de armazenamento para criar o destino de armazenamento. Especificamente, ele monta as exportações como ID de usuário 0.

Diferentes sistemas de armazenamento usam métodos diferentes para habilitar esse acesso:

* Os servidores Linux geralmente são adicionados ``no_root_squash`` ao caminho exportado no ``/etc/exports`` .
* OS sistemas NetApp e EMC normalmente controlam o acesso com regras de exportação que estão vinculadas a endereços IP ou redes específicos.

Se você estiver usando regras de exportação, lembre-se de que o cache pode usar vários endereços IP diferentes da sub-rede de cache. Permitir o acesso de todo o intervalo de endereços IP de sub-rede possíveis.

> [!NOTE]
> Embora o cache precise de acesso raiz ao sistema de armazenamento de back-end, você pode restringir o acesso para clientes que se conectam por meio do cache. Controle de leitura [acesso de cliente](access-policies.md#root-squash) para obter detalhes.

Trabalhe com seu fornecedor de armazenamento NAS para habilitar o nível certo de acesso para o cache.

### <a name="allow-root-access-on-directory-paths"></a>Permitir acesso raiz em caminhos de diretório
<!-- linked in prereqs article -->

Para sistemas NAS que exportam diretórios hierárquicos, o cache HPC do Azure precisa de acesso de raiz para cada nível de exportação.

Por exemplo, um sistema pode mostrar três exportações como estas:

* ``/ifs``
* ``/ifs/accounting``
* ``/ifs/accounting/payroll``

A exportação ``/ifs/accounting/payroll`` é um filho de ``/ifs/accounting`` e ``/ifs/accounting`` é um filho de ``/ifs`` .

Se você adicionar a ``payroll`` exportação como um destino de armazenamento de cache do HPC, o cache realmente montará ``/ifs/`` e acessará o diretório de folha de pagamento a partir daí. Portanto, o cache HPC do Azure precisa de acesso à raiz para ``/ifs`` acessar a ``/ifs/accounting/payroll`` exportação.

Esse requisito está relacionado à maneira como o cache indexa arquivos e evita colisões de arquivo, usando identificadores de arquivo que o sistema de armazenamento fornece.

Um sistema NAS com exportações hierárquicas pode fornecer diferentes identificadores de arquivo para o mesmo arquivo se o arquivo for recuperado de diferentes exportações. Por exemplo, um cliente pode montar ``/ifs/accounting`` e acessar o arquivo ``payroll/2011.txt`` . Outro cliente monta ``/ifs/accounting/payroll`` e acessa o arquivo ``2011.txt`` . Dependendo de como o sistema de armazenamento atribui identificadores de arquivo, esses dois clientes podem receber o mesmo arquivo com diferentes identificadores de arquivo (um para ``<mount2>/payroll/2011.txt`` e um para ``<mount3>/2011.txt`` ).

O sistema de armazenamento de back-end mantém aliases internos para identificadores de arquivo, mas o cache HPC do Azure não pode informar quais identificadores de arquivo em seu índice fazem referência ao mesmo item. Portanto, é possível que o cache possa ter gravações diferentes armazenadas em cache para o mesmo arquivo e aplicar as alterações incorretamente porque não sabe que elas são o mesmo arquivo.

Para evitar essa colisão de arquivo possível para arquivos em várias exportações, o cache HPC do Azure monta automaticamente a exportação disponível mais superficialmente no caminho ( ``/ifs`` no exemplo) e usa o identificador de arquivo fornecido por essa exportação. Se várias exportações usarem o mesmo caminho base, o cache HPC do Azure precisará de acesso de raiz para esse caminho.

<!-- ## Enable export listing

The NAS must list its exports when the Azure HPC Cache queries it.

On most NFS storage systems, you can test this by sending the following query from a Linux client: ``showmount -e <storage IP address>``

Use a Linux client from the same virtual network as your cache, if possible.

If that command doesn't list the exports, the cache will have trouble connecting to your storage system. Work with your NAS vendor to enable export listing.  -->

## <a name="adjust-vpn-packet-size-restrictions"></a>Ajustar restrições de tamanho de pacote VPN
<!-- link in prereqs article and configuration article -->

Se você tiver uma VPN entre o cache e o dispositivo NAS, a VPN poderá bloquear pacotes de Ethernet de 1500 bytes de tamanho completo. Você pode ter esse problema se as trocas grandes entre o NAS e a instância de cache do Azure HPC não forem concluídas, mas as atualizações menores funcionarão conforme o esperado.

Não há uma maneira simples de dizer se o seu sistema tem esse problema, a menos que você saiba os detalhes da sua configuração de VPN. Aqui estão alguns métodos que podem ajudá-lo a verificar esse problema.

* Use farejadores de pacotes em ambos os lados da VPN para detectar quais pacotes são transferidos com êxito.
* Se a sua VPN permitir comandos ping, você poderá testar o envio de um pacote de tamanho completo.

  Execute um comando ping na VPN para o NAS com essas opções. (Use o endereço IP do seu sistema de armazenamento no lugar do *<storage_IP valor>* .)

   ```bash
   ping -M do -s 1472 -c 1 <storage_IP>
   ```

  Estas são as opções no comando:

  * ``-M do`` -Não fragmentar
  * ``-c 1`` -Enviar apenas um pacote
  * ``-s 1472`` -Defina o tamanho da carga para 1472 bytes. Essa é a carga de tamanho máximo para um pacote de 1500 bytes após a contabilização da sobrecarga de Ethernet.

  Uma resposta bem-sucedida tem a seguinte aparência:

  ```bash
  PING 10.54.54.11 (10.54.54.11) 1472(1500) bytes of data.
  1480 bytes from 10.54.54.11: icmp_seq=1 ttl=64 time=2.06 ms
  ```

  Se o ping falhar com 1472 bytes, provavelmente haverá um problema de tamanho de pacote.

Para corrigir o problema, talvez seja necessário configurar o MSS fixação MSS na VPN para fazer com que o sistema remoto detecte corretamente o tamanho máximo do quadro. Leia a [documentação de parâmetros de IPSec/IKE do gateway de VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) para saber mais.

Em alguns casos, alterar a configuração de MTU do cache do Azure HPC para 1400 pode ajudar. No entanto, se você restringir o MTU no cache, também deverá restringir as configurações de MTU para clientes e sistemas de armazenamento de back-end que interagem com o cache. Leia [definir configurações adicionais de cache do Azure HPC](configuration.md#adjust-mtu-value) para obter detalhes.

## <a name="check-for-acl-security-style"></a>Verificar o estilo de segurança da ACL

Alguns sistemas de NAS usam um estilo de segurança híbrido que combina ACLs (listas de controle de acesso) com segurança de POSIX ou UNIX tradicional.

Se o seu sistema relatar seu estilo de segurança como UNIX ou POSIX sem incluir o acrônimo "ACL", esse problema não afetará você.

Para sistemas que usam ACLs, o cache HPC do Azure precisa controlar valores adicionais específicos do usuário para controlar o acesso ao arquivo. Isso é feito por meio da habilitação de um cache de acesso. Não há um controle voltado para o usuário para ativar o cache de acesso, mas você pode abrir um tíquete de suporte para solicitar que ele seja habilitado para os destinos de armazenamento afetados em seu sistema de cache.

## <a name="next-steps"></a>Próximas etapas

Se você tiver um problema que não foi resolvido neste artigo, [abra um tíquete de suporte](hpc-cache-support-ticket.md) para obter ajuda de especialistas.
