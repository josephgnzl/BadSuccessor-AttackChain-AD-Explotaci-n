## Enumeración 
Como en todo pentest, el proceso comienza con la recopilación de información sobre el entorno que será evaluado. Antes de analizar los servicios o componentes de Active Directory, es necesario identificar qué sistemas se encuentran disponibles y qué servicios están expuestos.

Para esta primera etapa utilizaremos Nmap, una herramienta ampliamente utilizada para el reconocimiento de redes. A través de ella podremos identificar los puertos abiertos, los servicios disponibles y, en algunos casos, información adicional sobre los sistemas que estamos evaluando.

## Comando Inicial 
```nmap -Pn -sC -sV -p- --min-rate 2000 -oN nmap_initial.txt 10.0.0.46```

## Significado de Cada Parámetro 
| Parámetro              | Significado                                                                     |
| ---------------------- | ------------------------------------------------------------------------------- |
| `nmap`                 | Ejecuta Nmap.                                                                   |
| `-Pn`                  | Omite el descubrimiento mediante ping y considera el objetivo como activo.      |
| `-sC`                  | Ejecuta los scripts predeterminados de Nmap para obtener información adicional. |
| `-sV`                  | Intenta identificar el servicio y su versión en los puertos encontrados.        |
| `-p-`                  | Escanea todos los puertos TCP, del `1` al `65535`.                              |
| `--min-rate 2000`      | Establece una velocidad mínima de aproximadamente 2000 paquetes por segundo.    |
| `-oN nmap_initial.txt` | Guarda los resultados en formato de texto en `nmap_initial.txt`.                |
| `10.0.0.46`            | Dirección IP del objetivo.                                                      |
