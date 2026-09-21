# BadSuccessor Windows Active Directory

## Fecha: 21/09/2026

## Autor: Joseph González

Este repositorio nace como homenaje a la máquina `Checkpoint` de la plataforma `Hack The Box`, con un objetivo claro: reconstruir su cadena de ataque en un laboratorio controlado, comprender el funcionamiento de cada técnica involucrada y analizar cómo una serie de configuraciones y permisos aparentemente aislados pueden encadenarse hasta comprometer un dominio completo.

El objetivo no es limitarse a reproducir la máquina, sino entender y documentar la lógica detrás de cada salto de la cadena de explotación: partiendo de las credenciales iniciales proporcionadas, pasando por la enumeración de Active Directory, el abuso de permisos, la escalada de privilegios y el abuso de dMSA mediante BadSuccessor, hasta alcanzar el compromiso del dominio.

## Cadena de Ataque

La explotación se divide en cinco etapas principales. El acceso inicial parte de las credenciales proporcionadas para `ecabrera`, desde donde se identifican permisos delegados que permiten construir progresivamente la cadena de compromiso.

```text
┌──────────────────────────────────────────────────────────────┐
│  CREDENCIALES INICIALES                                      │
│  ecabrera @ pccn.local                                       │
└──────────────────────────────┬───────────────────────────────┘
                               │
                               ▼
┌──────────────────────────────────────────────────────────────┐
│  [01] RECONOCIMIENTO                                         │
│                                                              │
│  Enumeración SMB + análisis de permisos en Active Directory  │
│                                                              │
│  ├── WRITE → Deleted Objects                                 │
│  └── CREATE_CHILD → OU=Employees                             │
└──────────────────────────────┬───────────────────────────────┘
                               │
                               ▼
┌──────────────────────────────────────────────────────────────┐
│  [02] TOMBSTONE REANIMATION                                  │
│                                                              │
│  Crear → Mover → Eliminar → Restaurar                        │
│                                                              │
│  Abuso de objetos eliminados para recuperar un objeto        │
│  controlado dentro del dominio.                              │
└──────────────────────────────┬───────────────────────────────┘
                               │
                               ▼
┌──────────────────────────────────────────────────────────────┐
│  [03] BADSUCCESSOR / dMSA ABUSE                              │
│                                                              │
│  Creación de un `dMSA` controlado mediante `BadSuccessor`    │
│                                                              │
│  dMSA → S4U2Self → Kerberos                                  │
│          │                                                   │
│          └── RC4 / material de autenticación                  │
└──────────────────────────────┬───────────────────────────────┘
                               │
                               ▼
┌──────────────────────────────────────────────────────────────┐
│  [04] MOVIMIENTO LATERAL                                     │
│                                                              │
│  `DevDrop` → `VSIX` malicioso → ejecución                     │
│                                                              │
│  Abuso de un recurso compartido para introducir código       │
│  controlado y obtener ejecución bajo otro contexto.          │
└──────────────────────────────┬───────────────────────────────┘
                               │
                               ▼
┌──────────────────────────────────────────────────────────────┐
│  [05] ESCALADA FINAL                                         │
│                                                              │
│  Segundo abuso de `BadSuccessor`                             │
│                                                              │
│  dMSA → `svc_deploy` → `VMBackups` → credenciales             │
│                                                              │
│  VHDX / VMEM → extracción de hashes → Administrator           │
└──────────────────────────────┬───────────────────────────────┘
                               │
                               ▼
┌──────────────────────────────────────────────────────────────┐
│  DOMAIN COMPROMISE                                          │
│                                                              │
│  Administrator                                               │
│  NTLM Hash                                                   │
│  Evil-WinRM                                                  │
│                                                              │
│  → `Domain Admin`                                            │
└──────────────────────────────────────────────────────────────┘
```
