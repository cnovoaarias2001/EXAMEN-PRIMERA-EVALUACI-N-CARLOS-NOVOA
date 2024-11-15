# EXAMEN PRIMERA EVALUCIÓN SRI CARLOS NÓVOA ARIAS

**1. Explica métodos para 'abrir' unha consola/shell a un contenedor en execución.**<br>
Podemos utilizar o comando `docker attach examen`.
Por último podemos utilizar o comando `docker exec -it examen bash` e podemos utilizar a consola do contenedor.

**2. No contenedor anterior (en execución), qué opciones tes que ter usado ó arrincalo para poder interactuar coas súas entradas e salidas**<br>
Podemos utilizar o comando `docker run -it image /bin/bash` -i para entrada estandar y -t para una terminal.

**3. Cómo sería un ficheiro docker-compose para que dous contenedores se comuniquen entre si nunha rede só deles?**<br>
O arquivo docker-compose.yml deberá conter a siguiente configuración:
```
version: "3.9"
services:
  examen1:
    image: alpine
    command: ["sh", "-c", "while true; do sleep 3600; done"]
    networks:
      examen:
        aliases:
          - examen1.local

  examen2:
    image: alpine
    command: ["sh", "-c", "while true; do sleep 3600; done"]
    networks:
      examen:
        aliases:
          - examen2.local

networks:
  examen:
    driver: bridge
```
Desta forma creamos os contenedores que estarán através da rede "examen" creada co comando `docker network create examen`.

**4. Qué tes que engadir ó ficheiro anterior para que un contenedor teña unha IP fixa?**
No apartado network debese definir a IP que queremos ter no noso contenedor.
```
  examen2:
    image: alpine
    command: ["sh", "-c", "while true; do sleep 3600; done"]
    networks:
      examen:
        ipv4_address: 172.17.0.2
```
Neste caso o apartado `ipv4_address: 172.17.0.2`es donde indicamos IP.

**5. Qué comando de consola podo usar para sabe las ips dos contenedores anteriores?**<br>
Co comando `docker inspect examen1 o examen2` podemos ver a configuración do contenedor neste caso podemos ver que a IP e (172.17.0.2):
```
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
```
**6. Cál é a funcionalidade do apartado "ports" en docker compose?**<br>
A opción ports permite mapear os portos internos dun contenedor e os portos da máquina anfitrioa e tamen serve para acceder ós servizos expostos polo contenedor desde o exterior.

**7. Para qué serve o rexistro CNAME? Pon un exemplo**<br>
O rexistro CNAME sirve para que poidan comunicarse entre as máquinas contindo o "alias". A sua finalidade e asociar un dominio con outro.

**8. Cómo podo facer para que a configuración dun contenedor DNS non se borre se creo outro contenedor?** <br>

Esta configuración fai que a confiración do DNS este vinculada a un arquivo `- dns_config:/etc/bind/`.

volumes:
  dns_config:
    driver: local

services:
  dns-server:
    image: my-dns-server
    volumes:
      - dns_config:/etc/bind/

**9. Engade unha zoa tendaelectronica.int no teu docker DNS que teña:**<br>
- www á IP 172.16.0.1
- owncloud sexa un CNAME de www
- un rexistro de texto có contido "1234ASDF"<br>

```
$TTL    38400
@       IN      SOA     ns.tendaelectronica.int. admin.tendaelectronica.int. (
                        2024111501 ; Serial
                        3600       ; Refresh
                        1800       ; Retry
                        604800     ; Expire
                        86400 )    ; Minimum TTL

        IN      NS      ns.tendaelectronica.int.
ns      IN      A       172.16.0.1
www     IN      A       172.16.0.1
owncloud IN     CNAME   www
@       IN      TXT     "1234ASDF"
```

No me funcinó y este ultimo esta basado en otro archivo que tenia.

Comproba que todo funciona có comando "dig"
Mostra nos logs que o servicio funciona ben usando a saída da terminal ó levantar o compose ou có comando "docker logs [nomeContenedorOuID]"
(o apartado 9 realízase na máquina virtual)
