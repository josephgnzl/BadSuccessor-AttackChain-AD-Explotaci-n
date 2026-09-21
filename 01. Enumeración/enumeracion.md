# Enumeración 
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

## Puertos y Servicios Identificados

Con este primer escaneo queremos construir una visión general del servidor: qué puertos están abiertos, qué servicios están disponibles y qué información básica podemos obtener de ellos.

| Evidencia encontrada                | Interpretación                                                                      |
| ----------------------------------- | ----------------------------------------------------------------------------------- |
| `53/tcp`                            | El servidor proporciona DNS.                                                        |
| `88/tcp`                            | Kerberos está disponible, utilizado por Active Directory para autenticación.        |
| `389/tcp`                           | LDAP permite comunicarse con Active Directory.                                      |
| `636/tcp`                           | LDAP también está disponible mediante TLS.                                          |
| `3268/tcp`                          | El servidor proporciona acceso al Global Catalog.                                   |
| `3269/tcp`                          | Global Catalog disponible mediante TLS.                                             |
| `445/tcp`                           | SMB está habilitado.                                                                |
| `135/tcp` + múltiples puertos altos | Existe infraestructura RPC de Windows.                                              |
| `3389/tcp`                          | RDP está habilitado.                                                                |
| `5985/tcp`                          | WinRM está disponible.                                                              |
| `9389/tcp`                          | Servicio .NET asociado a funciones de administración de Active Directory.           |
| `raynex.local`                      | El dominio de Active Directory fue identificado.                                    |
| `raynex.lab`                        | El nombre del servidor indica que estamos trabajando con el controlador de dominio. |
