# Respostas

---

1) O código abaixo implementa um serviço, a operar como daemon, para guardar e recuperar 4 bytes. No entanto, a forma
   como o named pipe é utilizado está fundamentalmente errada. Porquê?

   > O erro fundamental é que a `named pipe` é aberta para leitura e escrita, uma vez que a `named pipe` deve ser um
   > canal unidirecional de comunicação, não podendo a mesma utilizar operações de leitura e escrita em simultâneo.

2) Vários processos relacionados comunicam através de um espaço de memória partilhada. Um dos processos aloca espaço
   para um objeto com malloc, preenche devidamente todos os campos do objeto e publica o ponteiro para o objeto na zona
   de memória partilhada. Há processos a tentar consultar os campos do objeto publicado e a terminar a execução com
   indicação de segmentation fault. Qual é o problema?

   > O problema é que o processo que aloca espaço para o objeto com `malloc` não está a alocar espaço na memória
   > partilhada, mas sim na memória privada do processo, ao que os outros processos não têm acesso. Para que os outros
   > processos tenham acesso a esta alocação, é necessário que o processo que aloca espaço para o objeto utilize a
   > função `shm_open` para localizar o endereço onde se encontra memória partilhada e a função `mmap` para mapear o
   > endereco de memória para onde o objeto deve ser alocado para que os outros processos tenham acesso.

3) No arranque do sistema, o systemd precisa de determinar o conjunto de serviços em estado enabled. Como se determina
   essa informação?

   > Durante o arranque do sistema, o `systemd` lê os ficheiros de configuração de cada serviço, determinando quais dos
   > serviços devem ser iniciados a partir das dependências definidas nos ficheiros de configuração pelas
   > opções `WantedBy` e `RequiredBy` da secção `[Install]`.

4) Na definição de ficheiro de unidade para o systemd, é possível adicionar um serviço (especificado num ficheiro
   .service) a um target (especificado num ficheiro .target) sem editar, direta ou indiretamente, o ficheiro .target.

   a. Indique como se pode conseguir o efeito indicado.

   > Com uso do comando `systemctl add-wants <service> <target>` é possível adicionar um serviço a um target sem editar,
   > direta ou indiretamente, o ficheiro .target. Sendo essa dependência manipulada dinamicamente no sistema e não pelos
   > ficheiros de configuração.

   b. Indique qual o propósito para a existência desta funcionalidade.

   > Para ser possível adicionar dependências a ficheiros de unidade para o systemd sem ser necessário editar os
   ficheiros de configuração dessas unidades.

5) Os processadores ARM v8 de 64 bits (por vezes designados por aarch64) suportam um modo de tradução de endereços com
   duas fases, em que um endereço virtual é primeiro traduzido para um endereço intermédio e a seguir passa por uma
   segunda tradução para se obter o endereço físico final. Qual é o propósito deste esquema de tradução com duas fases?

   > O propósito deste esquema de tradução com duas fases é permitir que o sistema operativo tenha controlo sobre a
   > tradução de endereços, permitindo que o mesmo possa definir as traduções de endereços que pretende, ao invés de
   > deixar essa tarefa para o hardware. Isso permite que o sistema operativo gerencie a tradução de endereços de acordo
   > com suas políticas específicas, requisitos de segurança e estratégias de gerenciamento de memória.

6) Comente a seguinte afirmação:

   «Um dos custos incontornáveis do sistema de contentores Docker é o de precisar de uma máquina
   virtual auxiliar para correr um kernel Linux, que fica em execução em simultâneo com o kernel do
   host, seja num sistema Windows, Mac ou Linux.»

   > A afirmação é falsa, uma vez que o Docker não precisa necessáriamente de uma máquina virtual auxiliar para
   > correr um kernel Linux. Num sistema Windows, por exemplo, pode-se utilizar o WSL2 (Windows Subsystem for Linux 2)
   > para correr o Docker no kernel do host sem a necessidade de uma máquina virtual auxiliar. No caso do Linux, não é
   > preciso instalar nenhum software adicional ou utilizar uma máquina virtual auxiliar para correr o Docker, uma vez
   > que o mesmo pode correr diretamente no kernel do host.

7) Considere o Dockerfile apresentado ao lado e dois ficheiros, package.json e app.js, com uma aplicação para Node.js

    - A construção da imagem falha na linha com RUN cp, que se pretendia que colocasse os ficheiros da aplicação Node.js
      na diretoria de destino. Indique porquê e corrija.
    - Modifique ainda o Dockerfile para minimizar o número de reconstruções de camadas quando algum dos ficheiros da
      aplicação Node.js é alterado e reduza o número total de camadas.

   > O comando `RUN cp` falha uma vez que '*' não é interpretado pelo docker como a diretoria onde se encontra o
   > ficheiro, mas sim como um ficheiro com o nome '*'.

   Correção e diminuição das layers do DockerFile:
   ```dockerfile
   FROM ubuntu
   WORKDIR /opt/isel/tp2
   COPY * /opt/isel/tp2/
   RUN apt update && \
       apt install -y npm nodejs && \
       npm install
   EXPOSE 80
   CMD ["node", "app.js"]
   ```

8) Uma solução para docker compose, composta por múltiplos serviços, utiliza redes distintas para vários grupos de
   serviços dessa solução. Consequentemente, os vários contentores da solução estarão em execução com definições
   distintas de rede, possivelmente todas elas diferentes das definições de rede do sistema anfitrião. No entanto, todos
   os processos desses contentores são também processos do sistema anfitrião. Como podem coexistir no mesmo sistema
   operativo processos com definições de rede diferentes?

   > Os processos dos contentores podem coexistir no mesmo sistema operativo com definições de rede diferentes uma vez
   > que, cada contentor possui a sua própria interface de rede virtual podendo então ter definições de rede diferentes
   > das definições de rede do sistema anfitrião sem causar qualquer conflito com o sistema ou os outros contentores.
   > Isto é possível porque, as suas definições existem num sistema isolado não afetando os outros contentores ou o 
   > sistema anfitrião.