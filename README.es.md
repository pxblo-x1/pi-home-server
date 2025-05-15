# Raspberry Pi 5 Initial Config

Este repositorio contiene los pasos para realizar una configuración inicial de Raspberry Pi 5.

## Script de configuración automática 🚀

Para facilitar la configuración inicial, este repositorio incluye el script `setup-pi.sh` que automatiza gran parte del proceso.

### ¿Qué hace automáticamente el script?

- Actualiza el sistema operativo (`apt update && apt full-upgrade`)
- Instala dependencias necesarias para Docker (`ca-certificates`, `curl`)
- Agrega la clave GPG y el repositorio oficial de Docker
- Instala Docker y sus plugins
- Crea el grupo `docker` (si no existe)
- Agrega tu usuario al grupo `docker`
- Instala Neovim

### ¿Qué pasos debes hacer manualmente?

1. Ejecutar `sudo raspi-config` para:
   - Expandir el filesystem (Advanced Options > Expand Filesystem)
   - Activar autologin (System Options > Boot / Auto Login > Console Autologin)
   - Configurar localización (Locale, Timezone, Keyboard, WLAN Country)
2. Ejecutar `sudo nmtui` para configurar la IP estática si lo deseas.
3. Reiniciar la Raspberry Pi (`sudo reboot`).
4. Ejecutar `newgrp docker` o cerrar y abrir sesión para usar Docker sin sudo.

### Uso del script paso a paso

1. Copia el script a tu Raspberry Pi (o clona este repositorio).
2. Haz el script ejecutable:
   ```sh
   chmod +x setup-pi.sh
   ```
3. Ejecútalo:
   ```sh
   ./setup-pi.sh
   ```
4. Sigue las instrucciones que aparecerán al finalizar el script para completar la configuración manual.

---

## Primeros pasos 🤔

1. Luego de formatear la SD, colocarla en el Raspberry y encenderlo, vamos a comprobar que el equipo reciba direccional IP mediante DHCP.

    ```shell
    ping raspberrypi.local
    PING raspberrypi.local (192.168.50.180): 56 data bytes
    64 bytes from 192.168.50.180: icmp_seq=0 ttl=64 time=94.358 ms
    64 bytes from 192.168.50.180: icmp_seq=1 ttl=64 time=26.239 ms
    64 bytes from 192.168.50.180: icmp_seq=2 ttl=64 time=5.412 ms
    ```

    En este caso tenemos como IP `192.168.50.180`

2. Nos vamos a conectar a la raspberrypi utilizando su IP mediante ssh y utilizando las credenciales que se colocaron al dar formato la tarjeta SD:

    ```shell
    ssh piuser@192.168.50.180
    ```

3. El primer paso sera realizar la configuración basica de Raspberry mediante el comando:

    ```sh
    sudo raspi-config
    ```

    Esto nos mostrara un wizard con opciones para poder configurar algunos apartados.

    1. El primer paso seria incrementar el tamaño de la particion root: Advanced Options ➜ Expand Filesystem
    2. Luego debemos activar el auto-login: System Options ➜ Boot / Auto Login ➜ Console Autologin
    3. Ahora configuraremos las opciones de localización: 
        1. Localisation Options ➜ Locale ➜ es_EC.UTF-8 y en_US.UTF-8
        2. Timezone ➜ Americas ➜ Guayaquil
        3. Keyboard
        4.  WLAN Country ➜ Ecuador

4. Despues damos Finish y reiniciamos.

5. Finalmente podemos actualizar los paquetes de la distribución mediante:

    ```sh
    sudo apt update && sudo apt full-upgrade
    ```

## Configuración IP Estatica 📄

1. Dentro de la raspberry, vamos a ejecutar el siguiente comando para configurar la IP Estatica.

   ```shell
   sudo nmtui
   ```

2. En el wizard que se nos muestra vamos a acceder en las siguientes opciones:

   1. NetworkManager TUI > Edit a connection > Wired connection 1 > <Edit...>

   2. IPv4 CONFIGURATION > Show > 

   3. Agregaremos los siguientes datos:

      - Addresses

        ```
        192.168.50.180/24
        ```

      - Gateway

        ```
        192.168.50.1
        ```

      - DNS servers

        ```
        1.1.1.1
        ```

        ```
        8.8.8.8
        ```

   4. Para finalizar, debemos aplicar la configuracion con un reinicio:

      ```shell
      sudo reboot
      ```

## Instalacion de Docker :whale:

1. Set up Docker's `apt` repository.

```shell
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/debian \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

2. Install the Docker packages.

```shell
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

3. Create the `docker` group.

```shell
sudo groupadd docker
```

4. Add your user to the `docker` group.

```shell
sudo usermod -aG docker $USER
```

4. Log out and log back in so that your group membership is re-evaluated or apply the next command:

```shell
newgrp docker
```

5. Run `docker --version`
6. Install neo-vim `sudo apt-get install neovim`

## Authors and acknowledgment 🛡

Pablo Pin - devidence.dev ©

## License 🔒

Pablo Pin - Devidence.dev ©