# Respostas

---

6) Num sistema Linux, considere um serviço que atende os processos clientes através de um único socket de domínio Unix
   do tipo stream. É possível atender múltiplos clientes em simultâneo? Se sim, como se distingue cada um dos clientes
   no serviço (por exemplo, no momento de receber/enviar dados de/para cada um dos clientes)? Se não, como se pode
   desenhar uma solução para suportar este requisito?

   > É possivel atender multiplos clientes em simultâneo, uma vez que ao se usar a função accept() é criado um novo
   > socket para cada cliente que se conecta ao servidor retornando um file descriptor que identifica esse socket. Desta
   > forma é possível distinguir cada um dos clientes no serviço, sendo possível receber e enviar dados ao devido
   > cliente. O atendimento de multiplos clientes em simultâneo pode ser feito através do lançamento de threads que iram
   > processar os pedidos de cada cliente.

7) Alguns processos ativos num sistema Linux correspondem a serviços a operar como daemons. Indique três características
   associadas a este tipo de processos (não específicas de um gestor de serviços).

   > Esse tipo de processos não têm um terminal associado, a sua execução é independente das sessões de utilizador e são
   > projetados para serem executados em segundo plano, sem interferência direta do usuário.

8) Num sistema Linux com systemd, o ficheiro `/etc/systemd/system/tp2.service`, na sua secção [Unit] tem a linha
   Requires=tp2.socket.

   a. Tendo em conta esta linha, que funcionalidade do systemd deverá estar a ser usada no serviço tp2 e porque aparece
   esta diretiva Requires no ficheiro .service?

   > Ao usar a opção `Requires` o systemd vai criar uma dependência entre o serviço, tp2.service, e o socket,
   > tp2.socket, de forma a que o serviço esteja dependente do estado de ativação do socket. Desta forma, quando o
   > serviço for ativado é garantido também que o socket é ativado e se o socket for desativado o serviço também é
   > desativado.

   b. Deverá o ficheiro .socket ter uma linha equivalente (Requires=tp2.service)? Porquê?

   > Não, uma vez que o socket não depende do serviço, mas sim o contrário. Se a linha `Requires=tp2.service` fosse
   > introduzida no ficheiro .socket seria criada uma dependência circular entre o serviço e o socket, o que não é
   > desejável. Pelo contrário se fosse pretendido adicionar uma linha ao ficheiro .socket, esta deveria ser
   > `RequiredBy=tp2.service`.

9) A empresa VMware vende um produto, descrito como um hipervisor, cujas instruções de instalação contêm o seguinte
   texto:

   «*In a typical interactive installation, you boot the ESXi installer and respond to the installer prompts to install
   ESXi to the local host disk. The installer reformats and partitions the target disk and installs the ESXi boot image.
   If you have not installed ESXi on the target disk before, all data on the drive is overwritten, including hardware
   vendor partitions, operating system partitions, and associated data.*»

   Este é um hipervisor de tipo 1 ou de tipo 2? Justifique a sua afirmação com base no texto acima.

   > Este é um hipervisor de tipo 1, uma vez que o mesmo é instalado diretamente no hardware como é descrito na
   > afirmação, "(...) all data on the drive is overwritten, including hardware vendor partitions, operating system
   > partitions, and associated data.".

10) Apresente duas vantagens que resultam da organização das imagens docker em camadas, suportadas por sistemas de
    ficheiros do tipo overlay. Apresente também uma possível desvantagem.

> Uma das vantagens é que se uma das camadas for alterada, apenas essa camada leva rebuild, sendo as restantes
> camadas reaproveitadas a partir do sistema de cache do docker. Outra vantagem é o uso eficiente dos recursos, uma
> vez que se várias imagens compartilharem a mesma camada, apenas uma cópia dessa camada é armazenada. Uma possível
> desvantagem é que se uma camada for alterada, todas as camadas que dependem dessa camada também são alteradas.

11) Considere o Dockerfile apresentado ao lado e três ficheiros: package.json e app.js, com uma aplicação para Node.js,
    e um README.md

      ```dockerfile
      FROM node:alpine
      WORKDIR /home/node
      COPY package.json .
      RUN npm install
      COPY app.js .
      COPY README.md .
      RUN chown -R node.node /home/node
      USER node
      EXPOSE 80
      CMD ["node", "app,js"]
      ```

- Indique que camadas de overlay são criadas sobre a imagem base node:alpine
- Modifique o Dockerfile para minimizar o número de camadas de overlay
- Indique as camadas de overlay criadas sobre a base, considerando as alterações

  > As camadas de overlay criadas sobre a imagem base node:alpine são:
  > - Camada 1: WORKDIR /home/node
  > - Camada 2: COPY package.json .
  > - Camada 3: RUN npm install
  > - Camada 4: COPY app.js .
  > - Camada 5: COPY README.md .
  > - Camada 6: RUN chown -R node.node /home/node
  > - Camada 7: USER node
  > - Camada 8: EXPOSE 80
  > - Camada 9: CMD ["node", "app,js"]

  > Para minimizar o número de camadas o Dockerfile pode ser modificado da seguinte forma:
   ```dockerfile
   FROM node:alpine
   WORKDIR /home/node
   COPY package.json app.js README.md ./
   RUN npm install && \
       chown -R node.node /home/node
   USER node
   EXPOSE 80
   CMD ["node", "app,js"]
   ```

  > As camadas de overlay criadas sobre a imagem base node:alpine, com a alteração, são:
  > - Camada 1: WORKDIR /home/node
  > - Camada 2: COPY package.json app.js README.md ./
  > - Camada 3: RUN npm install && chown -R node.node /home/node
  > - Camada 4: USER node
  > - Camada 5: EXPOSE 80
  > - Camada 6: CMD ["node", "app,js"]

12) Um ficheiro docker-compose.yml, para especificação de uma solução composta com o nome tp2, contém três serviços:
    svca, svcb e svcc, todos colocados na mesma rede, svcnet, de tipo bridge. Os serviços svca e svcc têm apenas uma
    instância cada um, mas o serviço svcb foi lançado com scale=4. Executando um shell (/bin/sh) no contentor do serviço
    svca, qual é a diferença observável entre executar nslookup svcb ou nslookup tp2-svcb-1 ?

    > Ao executar `nslookup svcb` é retornado o endereço IP de uma das instâncias do serviço svcb, enquanto que quando é 
    executado `nslookup tp2-svcb-1` é retornado o endereço IP da instância 1 do serviço svcb.