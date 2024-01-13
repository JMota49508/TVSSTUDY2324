# Respostas

---

1) Para implementar um serviço para operar como daemon pondera-se receber os pedidos através de um named pipe (ou fifo,
   com tipo p no sistema de ficheiros) ou através de um socket de domínio Unix (tipo s no sistema de ficheiros) de tipo
   stream. Qual seria a principal desvantagem para o named pipe?

   > A principal desvantagem de um `named pipe` é que o mesmo está limitado ao uso para comunicação entre processos do
   > mesmo host e que a mesma é unidirecional, enquanto que um `socket` de domínio Unix pode ser usado para comunicação
   entre processos de hosts
   > diferentes ou entre processos do mesmo host e que a mesma é bidirecional.

2) Um dos mecanismos previstos para comunicação entre processos (IPC) em sistemas da família Unix é a memória
   partilhada. No entanto, a utilização de memória partilhada geralmente implica a utilização de outros mecanismos de
   comunicação entre processos. Porquê?

   > A utilização de memória partilhada geralmente implica a utilização de outros mecanismos de comunicação entre
   > processos porque, é necessário garantir que os acessos à memória partilhada sejam sincronizados, isto é, que a
   > escrita e a leitura de dados na memória partilhada ocorram de forma ordenada. Essa sincronização é essencial para
   > garantir que os acessos à memória partilhada sejam Thread-Safe, protegendo a memória de possíveis conflitos gerados
   > por acessos em concorrência.

3) Um serviço systemd pode estar: enabled, disabled, active, inactive.

   a. O que indica cada um destes quatro estados?
   > `enabled` e `disabled` indicam se o serviço está configurado para iniciar automaticamente durante o arranque do
   > sistema. `active` e `inactive` indicam se o serviço está em execução ou não.

   b. Indique os pares destes quatro estados que são legítimos de ocorrer (compatíveis entre si).
   > Todos os pares são compatíveis excepto `active` com `inactive` ou `enabled` com `disabled`

4) No âmbito do systemd, explique sucintamente o que é um ficheiro de unidade do tipo .socket e o que se entende por
   socket activation.

   > Um ficheirode unidade do tipo `.socket` é um ficheiro de configuração que descreve um socket de domínio Unix, que
   > serve para iniciar um serviço quando uma conexão é estabelecida nesse socket por um cliente. `socket activation` é
   > um mecanismo que permite que um serviço seja iniciado dinamicamente quando uma conexão é estabelecida num socket de
   > domínio Unix. Este mecanismo é utilizado em serviços que não precisam de estar sempre em execução, mas que
   > precisam de ser iniciados quando necessário.

5) Explique sucintamente o que se entende por paravirtualização e qual a sua principal desvantagem.

   > A paravirtualização é uma técnica de virtualização que permite que máquinas virtuais (VMs) sejam executadas em um
   > computador host com modificações mínimas no kernel do host. Isso é alcançado criando uma camada fina de software
   > entre a máquina virtual e o hardware do host, o que permite à máquina virtual a capacidade de comunicar
   > diretamente com os recursos do host, em vez de emulá-los. No entanto, uam desvantagem significativa da
   > paravirtualização é a necessidade de modificações no kernel do sistema operativo do host e uma menor flexível
   > quando comparada com a virtualização completa.

6) Explique sucintamente o que se entende por máquinas virtuais de processo. Dê exemplos práticos da utilização deste
   tipo de virtualização no cenário particular do set de instruções da arquitetura do host ser distinto do existente no
   ambiente virtualizado.

   > Uma máquina virtual de processo é um ambiente de execução que permite que programas escritos em uma linguagem de
   > programação específica sejam executados em uma plataforma diferente daquela em que foram originalmente escritos. Um
   > máquina virtual de processo atua como um interpretador entre o programa e o hardware subjacente, permitindo que o
   > programa seja executado sem ser compilado para a plataforma específica. Um exemplo prático da utilização deste tipo
   > de virtualização é o Java Virtual Machine (JVM), que permite que programas escritos em Java sejam executados em
   > qualquer plataforma que tenha uma JVM instalada.

7) Comente a seguinte afirmação:

   «Um dos custos incontornáveis do sistema de contentores Docker é o de manter múltiplas versões
   do kernel Linux para que, por exemplo, as imagens baseadas em Ubuntu 22.04 usem o kernel 5.15
   enquanto as imagens baseadas em Red Hat Enterprise Linux 8 precisam do kernel 4.18.»

   > A afirmação é falsa porque, o Docker não requer a manutenção de múltiplas versões do kernel Linux, uma vez que o
   > Docker não é um sistema de virtualização, mas sim um sistema de contentores. O Docker utiliza o kernel do host para
   > executar os seus contentores. Essa abordagem aumenta a eficiência e consistência dos contentores, uma vez que os
   > contentores não precisam de ter um kernel próprio.

8) Considere a operação docker build guiada por um Dockerfile.

   a. Que condições dão origem a uma nova camada de overlay na imagem resultante do build?

   > Durante a operação `docker build`, uma nova camada de overlay é gerada na imagem resultante do build sempre que
   > uma instrução(`FROM`, `RUN`, `COPY`, `ADD`, etc.) é executada e resulta numa alteração no sistema de ficheiros do
   > contentor.

   b. Na reconstrução de uma imagem, que condições permitem o reaproveitamento das imagens intermédias(em cache) do
   build anterior?

   > Na reconstrução de uma imagem, as imagens intermédias(em cache) do build anterior podem ser reaproveitadas se
   > as camadas do build anterior não tiverem sido alteradas ou se as instruções armazenadas em cache forem idênticas as
   > presentes no Dockerfile.

9) Um ficheiro docker-compose.yml, para especificação de uma solução composta com o nome tp2, contém três serviços:
   svca, svcb e svcc, todos colocados na mesma rede, svcnet, de tipo bridge. Os serviços svca e svcc têm apenas uma
   instância cada um, mas o serviço svcb foi lançado com scale=4. Executando um shell (/bin/sh) no contentor do serviço
   svca, qual é a diferença observável entre executar ping svcb ou ping tp2-svcb-1 ?

   > A execução do comando `ping svcb` vai ser interpretada pelo Docker como um pedido de comunicação para qualquer
   > instância de `svcb` que se encontre em execução. Por outro lado, a execução de `ping tp2-svcb-1` vai ser
   > interpretado como um pedido de comunicação para a instância específica de `svcb` com o nome `tp2-svcb-1`.


