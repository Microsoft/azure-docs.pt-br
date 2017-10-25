## <a name="what-are-service-bus-queues"></a>O que são as filas do Barramento de Serviço?
Filas do Barramento de Serviço dão suporte a um modelo de comunicação de **sistema de mensagens agenciado** . Ao usar filas, os componentes de um aplicativo distribuído não se comunicam diretamente uns com os outros, mas trocam mensagens por meio de uma fila, que atua como um intermediário (agente). Um produtor de mensagem (remetente) transmite uma mensagem para a fila e, em seguida, continua o processamento. De forma assíncrona, um consumidor de mensagem (receptor) recebe a mensagem da fila e a processa. O produtor não precisa esperar por uma resposta do consumidor a fim de continuar a processar e enviar mais mensagens. As filas oferecem entrega de mensagem do tipo **FIFO (primeiro a entrar, primeiro a sair)** para um ou mais consumidores concorrentes. Ou seja, as mensagens são normalmente recebidas e processadas pelos receptores na ordem em que foram adicionadas à fila, sendo que cada mensagem é recebida e processada por apenas um consumidor de mensagem.

![Conceitos de fila](./media/howto-service-bus-queues/sb-queues-08.png)

Filas do Barramento de Serviço são uma tecnologia de uso geral que pode ser usada para uma grande variedade de cenários:

* Comunicação entre as funções Web e de trabalho em um aplicativo multicamada do Azure.
* Comunicação entre aplicativos locais e aplicativos hospedados pelo Azure em uma solução híbrida.
* Comunicação entre os componentes de um aplicativo distribuído executado localmente em diferentes organizações ou departamentos de uma organização.

O uso de filas permite que você dimensione seus aplicativos com mais facilidade e concede mais resiliência à sua arquitetura.


